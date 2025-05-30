# Agg-MongoDB
### 🔹 Easy Level
1. Filter Electronics Products
   db.products.aggregate([
{$match:{category:"Electronics"}},
{$project:{_id:1,name:1,category:1}}]).pretty();
# output:
{
  _id: ObjectId('68383a5832ea8954ddd0d8f9'),
  name: 'Laptop Pro',
  category: 'Electronics'
}
{
  _id: ObjectId('68383a5832ea8954ddd0d8fa'),
  name: 'Wireless Mouse',
  category: 'Electronics'
}
{
  _id: ObjectId('68383a5832ea8954ddd0d8fb'),
  name: 'Mechanical Keyboard',
  category: 'Electronics'
}
{
  _id: ObjectId('68383a5832ea8954ddd0d8ff'),
  name: 'Smartwatch',
  category: 'Electronics'
}

2. Count Products by Category
db.products.aggregate([
  {
    $group: {
      _id: "$category",
      count: { $sum: 1 }
    }
  }
])
{
  _id: 'Apparel',
  count: 2
}
{
  _id: 'Sports',
  count: 1
}
{
  _id: 'Electronics',
  count: 5
}
{
  _id: 'Home Goods',
  count: 1
}

3. List Products Sorted by Price (Descending)
db.products.aggregate([
  {$project: {
      _id: 0,
      name: 1,
      price: 1
    }
  },
  {$sort: {price: -1}
  }
])
# Output:
{
  name: 'Laptop Pro',
  price: 1200
}
{
  name: 'Espresso Machine',
  price: 250
}
{
  name: 'Smartwatch',
  price: 199
}
{
  name: 'Bluetooth Speaker',
  price: 80
}
{
  name: 'Mechanical Keyboard',
  price: 75
}
{
  name: 'Denim Jeans',
  price: 60
}
{
  name: 'Leather Wallet',
  price: 45
}
{
  name: 'Yoga Mat',
  price: 30

 # Medium
1. Total Quantity by Supplier
db.products.aggregate([
  {
    $group: {
      _id: "$supplier.name",
      totalQuantity: { $sum: "$quantity" }
    }
  }
]) 
# Output:
{
  _id: 'GadgetPro',
  totalQuantity: 125
}
{
  _id: 'HomeBest',
  totalQuantity: 30
}
{
  _id: 'ActiveLife',
  totalQuantity: 90
}
{
  _id: 'StyleCraft',
  totalQuantity: 120
}
{
  _id: 'FashionHub',
  totalQuantity: 280
}
{
  _id: 'SoundWave',
  totalQuantity: 60
}

2.Average Price by Tag
db.products.aggregate([
  {
    $unwind: "$tags"
  },
  {
    $group: {
      _id: "$tags",
      averagePrice: { $avg: "$price" }
    }
  },
  {
    $sort: {
      averagePrice: -1
    }
  }
])
# Output:
{
  _id: 'work',
  averagePrice: 1200
}
{
  _id: 'portable',
  averagePrice: 493
}
{
  _id: 'computer',
  averagePrice: 433.3333333333333
}
{
  _id: 'kitchen',
  averagePrice: 250
}
{
  _id: 'coffee',
  averagePrice: 250
}
{
  _id: 'appliance',
  averagePrice: 250
}
{
  _id: 'gadget',
  averagePrice: 199
}
{
  _id: 'wearable',
  averagePrice: 199
}
{
  _id: 'audio',
  averagePrice: 80
}
{
  _id: 'mechanical',
  averagePrice: 75
}
{
  _id: 'denim',
  averagePrice: 60
}
{
  _id: 'wireless',
  averagePrice: 52.5
}
{
  _id: 'peripheral',
  averagePrice: 50
}
{
  _id: 'leather',
  averagePrice: 45
}
{
  _id: 'fashion',
  averagePrice: 45
}
{
  _id: 'clothing',
  averagePrice: 40
}
{
  _id: 'fitness',
  averagePrice: 30
}
{
  _id: 'exercise',
  averagePrice: 30
}
{
  _id: 'casual',
  averagePrice: 20
}

3.Products Added in February 2023
db.products.aggregate([
  {
    $match: {
      date_added: {
        $gte: new Date("2023-02-01T00:00:00Z"),
        $lt: new Date("2023-03-01T00:00:00Z")
      }
    }
  },
  {
    $project: {
      _id: 0,
      name: 1,
      category: 1,
      formattedDateAdded: {
        $dateToString: {
          format: "%Y-%m-%d",
          date: "$date_added"
        }
      }
    }
  }
])
# Output:
{
  name: 'Wireless Mouse',
  category: 'Electronics',
  formattedDateAdded: '2023-02-01'
}
{
  name: 'Espresso Machine',
  category: 'Home Goods',
  formattedDateAdded: '2023-02-15'
}

 
}
{
  name: 'Wireless Mouse',
  price: 25
}

# Hard
1. Total Inventory Value and Classification by Category
db.products.aggregate([
  {
    $group: {
      _id: "$category",
      totalInventoryValue: {
        $sum: { $multiply: ["$price", "$quantity"] }
      }
    }
  },
  {
    $project: {
      _id: 0,
      categoryName: "$_id",
      totalInventoryValue: 1,
      valueClassification: {
        $switch: {
          branches: [
            {
              case: { $gt: ["$totalInventoryValue", 10000] },
              then: "High Value"
            },
            {
              case: { $gt: ["$totalInventoryValue", 5000] },
              then: "Medium Value"
            }
          ],
          default: "Standard Value"
        }
      }
    }
  }
])
# Output:
{
  totalInventoryValue: 8800,
  categoryName: 'Apparel',
  valueClassification: 'Medium Value'
}
{
  totalInventoryValue: 2700,
  categoryName: 'Sports',
  valueClassification: 'Standard Value'
}
{
  totalInventoryValue: 28025,
  categoryName: 'Electronics',
  valueClassification: 'High Value'
}
{
  totalInventoryValue: 7500,
  categoryName: 'Home Goods',
  valueClassification: 'Medium Value'
}
{
  totalInventoryValue: 5400,
  categoryName: 'Accessories',
  valueClassification: 'Medium Value'
}

2.Most Expensive Product by Supplier

db.products.aggregate([
  {
    $sort: {
      "supplier.name": 1,
      price: -1
    }
  },
  {
    $group: {
      _id: "$supplier.name",
      mostExpensiveProductName: { $first: "$name" },
      maxPrice: { $first: "$price" }
    }
  },
  {
    $project: {
      _id: 0,
      supplierName: "$_id",
      mostExpensiveProductName: 1,
      maxPrice: 1
    }
  }
])
# Output:
{
  mostExpensiveProductName: 'Laptop Pro',
  maxPrice: 1200,
  supplierName: 'TechGlobe'
}
{
  mostExpensiveProductName: 'Espresso Machine',
  maxPrice: 250,
  supplierName: 'HomeBest'
}
{
  mostExpensiveProductName: 'Leather Wallet',
  maxPrice: 45,
  supplierName: 'StyleCraft'
}
{
  mostExpensiveProductName: 'Yoga Mat',
  maxPrice: 30,
  supplierName: 'ActiveLife'
}
{
  mostExpensiveProductName: 'Bluetooth Speaker',
  maxPrice: 80,
  supplierName: 'SoundWave'
}
{
  mostExpensiveProductName: 'Denim Jeans',
  maxPrice: 60,
  supplierName: 'FashionHub'
}
{
  mostExpensiveProductName: 'Smartwatch',
  maxPrice: 199,
  supplierName: 'GadgetPro'
}

3.Products Tagged "portable" but Not "computer"
db.products.aggregate([
  {
    $match: {
      tags: {
        $all: ["portable"],
        $nin: ["computer"]
      }
    }
  },
  {
    $project: {
      _id: 0,
      name: 1,
      tags: 1
    }
  }
])
# Output
{
  name: 'Smartwatch',
  tags: [
    'wearable',
    'gadget',
    'portable'
  ]
}


