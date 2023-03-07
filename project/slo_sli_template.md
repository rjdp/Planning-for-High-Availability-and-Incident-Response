# API Service

| Category     | SLI | SLO                                                                                                         |
|--------------|-----|-------------------------------------------------------------------------------------------------------------|
| Availability |  (number of requests with non 5xx status code) / (total number of requests)  | 99%                                                                                                         |
| Latency      |   ( number of requests with 200 status code that had response time < 100ms ) / (Total number of requests with 200 status code)  | 90% of requests below 100ms                                                                                 |
| Error Budget |  remaining % of errors that can be allowed  i.e  1-[(1-compliance)/(1-objective)] , where Objective is 80% and compliance is ratio of num. success requests to total num. requests  | Error budget is defined at 20%. This means that 20% of the requests can fail and still be within the budget |
| Throughput   |   Total number of success (2xx) responses generated per second, averaged over some time say last 5 mins  | 5 RPS indicates the application is functioning                                                              |
