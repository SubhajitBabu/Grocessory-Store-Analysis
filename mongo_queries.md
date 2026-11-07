# Grocery Store Data Quality Checks (MongoDB)

Queries written for `mongosh`. Replace `clean_data` with your collection name if it differs. For a clean dataset, each check should return `0` or an empty result.

**Fields:** `transaction_id`, `transaction_date`, `store_id`, `store_name`, `city`, `customer_id`, `gender`, `category`, `sub_category`, `brand`, `item_name`, `quantity`, `unit_price`, `total_amount`, `payment_method`

---

## 1. Basic Counts and Nulls

```js
// Total rows
db.clean_data.countDocuments()

// Null / missing / empty-string count for every column
const fields = ["transaction_id","transaction_date","store_id","store_name","city",
  "customer_id","gender","category","sub_category","brand","item_name",
  "quantity","unit_price","total_amount","payment_method"];

fields.forEach(f => {
  const c = db.clean_data.countDocuments({
    $or: [{ [f]: null }, { [f]: { $exists: false } }, { [f]: "" }]
  });
  print(f + " -> " + c);
});
```

---

## 2. Duplicates

```js
// Duplicate transaction_ids
db.clean_data.aggregate([
  { $group: { _id: "$transaction_id", cnt: { $sum: 1 } } },
  { $match: { cnt: { $gt: 1 } } }
])

// Fully duplicated rows (same content, ignoring _id)
db.clean_data.aggregate([
  { $group: { _id: { t: "$transaction_id", d: "$transaction_date", c: "$customer_id",
                     i: "$item_name", q: "$quantity" }, cnt: { $sum: 1 } } },
  { $match: { cnt: { $gt: 1 } } }
])
```

> **Note:** A transaction can legitimately have several items. If `transaction_id` repeats with different `item_name` values, that is probably fine.

---

## 3. Data Types

```js
db.clean_data.countDocuments({ quantity: { $not: { $type: "int" } } })
db.clean_data.countDocuments({ unit_price: { $not: { $type: "double" } } })
db.clean_data.countDocuments({ total_amount: { $not: { $type: "double" } } })
```

---

## 4. Date Checks (stored as String)

```js
// Wrong format (should be YYYY-MM-DD)
db.clean_data.countDocuments({
  transaction_date: { $not: /^\d{4}-\d{2}-\d{2}$/ }
})

// Format OK but not a real date (e.g. 2024-02-31)
db.clean_data.aggregate([
  { $addFields: { d: { $dateFromString: { dateString: "$transaction_date",
                                          format: "%Y-%m-%d", onError: null } } } },
  { $match: { d: null } },
  { $count: "invalid_dates" }
])

// Date range (min / max) to spot future or very old dates
db.clean_data.aggregate([
  { $group: { _id: null, min: { $min: "$transaction_date" }, max: { $max: "$transaction_date" } } }
])
```

---

## 5. Numeric Validity

```js
// quantity <= 0
db.clean_data.countDocuments({ quantity: { $lte: 0 } })

// unit_price or total_amount <= 0
db.clean_data.countDocuments({ $or: [{ unit_price: { $lte: 0 } }, { total_amount: { $lte: 0 } }] })

// total_amount != quantity * unit_price (tolerance 0.01)
db.clean_data.find({
  $expr: { $gt: [
    { $abs: { $subtract: ["$total_amount", { $multiply: ["$quantity", "$unit_price"] }] } },
    0.01
  ] }
})

// Outliers: min / max / avg
db.clean_data.aggregate([
  { $group: { _id: null,
      minQty: { $min: "$quantity" }, maxQty: { $max: "$quantity" },
      minPrice: { $min: "$unit_price" }, maxPrice: { $max: "$unit_price" },
      avgPrice: { $avg: "$unit_price" } } }
])
```

---

## 6. Categorical Values (typos, case, spaces)

```js
// Distinct values with counts: scan for typos like "male" / "Male" / "M"
["gender","payment_method","category","city","store_name"].forEach(f => {
  print("---- " + f);
  printjson(db.clean_data.aggregate([
    { $group: { _id: "$" + f, cnt: { $sum: 1 } } }, { $sort: { _id: 1 } }
  ]).toArray());
});

// Leading/trailing spaces in any text column
["store_id","store_name","city","customer_id","gender","category",
 "sub_category","brand","item_name","payment_method"].forEach(f => {
  print(f + " -> " + db.clean_data.countDocuments({ [f]: /^\s|\s$/ }));
});

// Case-insensitive duplicates (e.g. "Cash" vs "cash")
db.clean_data.aggregate([
  { $group: { _id: { $toLower: "$payment_method" }, variants: { $addToSet: "$payment_method" } } },
  { $match: { $expr: { $gt: [{ $size: "$variants" }, 1] } } }
])
```

---

## 7. ID Format Patterns

```js
db.clean_data.countDocuments({ transaction_id: { $not: /^TXN\d{7}$/ } })
db.clean_data.countDocuments({ store_id: { $not: /^ST\d{2}$/ } })
db.clean_data.countDocuments({ customer_id: { $not: /^CUS\d{5}$/ } })
```

---

## 8. Consistency Across Columns

```js
// One store_id should map to exactly one store_name
db.clean_data.aggregate([
  { $group: { _id: "$store_id", names: { $addToSet: "$store_name" } } },
  { $match: { $expr: { $gt: [{ $size: "$names" }, 1] } } }
])

// One store_id should map to one city (if each store is in one city)
db.clean_data.aggregate([
  { $group: { _id: "$store_id", cities: { $addToSet: "$city" } } },
  { $match: { $expr: { $gt: [{ $size: "$cities" }, 1] } } }
])

// One sub_category should belong to one category
db.clean_data.aggregate([
  { $group: { _id: "$sub_category", cats: { $addToSet: "$category" } } },
  { $match: { $expr: { $gt: [{ $size: "$cats" }, 1] } } }
])

// One customer_id should have one gender
db.clean_data.aggregate([
  { $group: { _id: "$customer_id", g: { $addToSet: "$gender" } } },
  { $match: { $expr: { $gt: [{ $size: "$g" }, 1] } } }
])

// Same item_name with widely different unit prices
db.clean_data.aggregate([
  { $group: { _id: "$item_name", minP: { $min: "$unit_price" }, maxP: { $max: "$unit_price" } } },
  { $addFields: { ratio: { $divide: ["$maxP", "$minP"] } } },
  { $match: { ratio: { $gt: 2 } } }
])

// item_name should contain its brand
db.clean_data.find({
  $expr: { $eq: [ { $indexOfCP: ["$item_name", "$brand"] }, -1 ] }
}).limit(20)
```

> **Note:** The brand check may flag legitimate rows, such as brand `"FamilyFoods Farm"` vs `"FamilyFoods"`, so review those manually.

---

## 9. Quick Summary of Issues

```js
printjson({
  total: db.clean_data.countDocuments(),
  badDates: db.clean_data.countDocuments({ transaction_date: { $not: /^\d{4}-\d{2}-\d{2}$/ } }),
  badQty: db.clean_data.countDocuments({ quantity: { $lte: 0 } }),
  badPrice: db.clean_data.countDocuments({ unit_price: { $lte: 0 } }),
  totalMismatch: db.clean_data.countDocuments({ $expr: { $gt: [
    { $abs: { $subtract: ["$total_amount", { $multiply: ["$quantity","$unit_price"] }] } }, 0.01 ] } }),
  nullPayment: db.clean_data.countDocuments({ payment_method: { $in: [null, ""] } })
})
```