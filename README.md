### cURL

```
curl --location 'http://127.0.0.1:53887/products/bulk' \
--header 'Content-Type: application/json' \
--data '[
    {
        "code": "P001",
        "name": "Product 1",
        "quantity": 10,
        "availableDC": "DC1,DC2"
    },
    {
        "code": "P002",
        "name": "Product 2",
        "quantity": 20,
        "availableDC": "DC1,DC3"
    },
    {
        "code": "P003",
        "name": "Product 3",
        "quantity": 15,
        "availableDC": "DC2,DC3"
    },
    {
        "code": "P004",
        "name": "Product 4",
        "quantity": 25,
        "availableDC": "DC1,DC4"
    }
]'
```
