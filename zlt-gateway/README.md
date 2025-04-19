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



## 分布式链路追踪

```java
/**
 * 生成日志链路追踪id，并传入header中
 *
 * @author zlt
 * @date 2019/10/7
 * <p>
 * Blog: http://zlt2000.gitee.io
 * Github: https://github.com/zlt2000
 */
@Component
public class TraceFilter implements GlobalFilter, Ordered {
    @Autowired
    private TraceProperties traceProperties;

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        if (traceProperties.getEnable()) {
            //链路追踪id
            MDCTraceUtils.addTrace();

            ServerHttpRequest serverHttpRequest = exchange.getRequest().mutate()
                    .headers(h -> {
                        h.add(MDCTraceUtils.TRACE_ID_HEADER, MDCTraceUtils.getTraceId());
                        h.add(MDCTraceUtils.SPAN_ID_HEADER, MDCTraceUtils.getNextSpanId());
                    })
                    .build();

            ServerWebExchange build = exchange.mutate().request(serverHttpRequest).build();
            return chain.filter(build);
        }
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return Ordered.HIGHEST_PRECEDENCE;
    }
}
```

