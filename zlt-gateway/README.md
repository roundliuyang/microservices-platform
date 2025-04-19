# README



## Nacos 动态路由

- 命名空间 : public
- Group: SCG_GATEWAY
- Data Id:  scg-routes

```json
[
  {
    "id": "order",
    "uri": "lb://order-service",
    "predicates": [
      {
        "name": "Path",
        "args": {
          "pattern": "/order/**"
        }
      }
    ]
  }
]
```

