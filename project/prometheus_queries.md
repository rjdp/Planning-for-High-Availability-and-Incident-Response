## Availability SLI
### The percentage of successful requests over the last 5m
```
sum(rate(flask_http_request_total{job="ec2",instance="18.218.154.2:80", status!~"5.."}[5m]))/sum(rate(flask_http_request_total{job="ec2",instance="18.218.154.2:80"}[5m]))
```

## Latency SLI
### 90% of requests finish in these times

```
histogram_quantile(0.90,
sum(rate(flask_http_request_duration_seconds_bucket{job="ec2",instance="18.218.154.2:80"}[5m])) by (le, method))
```

## Throughput
### Successful requests per second
```
sum(rate(flask_http_request_total{job="ec2",instance="18.218.154.2:80", status=~"2.."}[5m]))
```


## Error Budget - Remaining Error Budget
### The error budget is 20%

```
1 - ((1 - (sum(increase(flask_http_request_total{job="ec2",instance="18.218.154.2:80", status=~"2.."}[7d])) by (method)) / sum(increase(flask_http_request_total{job="ec2",instance="18.218.154.2:80"}[7d])) by (method)) / (1 - .80))
```
