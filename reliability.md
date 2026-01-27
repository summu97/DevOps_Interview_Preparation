### Reliability
* Reliability is the ability of a system or application to consistently work correctly and respond as expected over time, even when there is high load, failures, or changes.

---

### Scenario: 
> Users are experiencing increased response times, which indicates a reliability issue affecting application availability and performance. 
> or
> Users complain about slow load / slow response of the application how do you use reliability in solving the issue.

## How you approach it as a DevOps Engineer
## Step-by-step: What you do when users complain 🚨


1. Using Grafana dashoards i check for the following
> Check **request rate** and then the **response time** which will say about the latency, any error rates like 404, 403 500 etc etc errors

2. Next check on metrics like cpu usage, memory usage any application or pod restarts etc
3. check for disk pressure, memory pressure network level issues
```
👉 If resources are maxed → scaling / resource issue
👉 If resources are fine → move to logs
```

4. In case of Kubernetes check for pod status, any probe failures, service endpoints missing pods 

5. Now using loki i'll check for any errors, exceptions, timeouts or db service failures

---

### What is Upstream service failures in loki?
* An upstream service is any external service or internal microservice that your application depends on to fulfill a request.
* An upstream service failure occurs when that service is unavailable, slow, or returning errors, causing your application to fail or respond slowly.

### How it shows in Loki logs: 

> Look for keywords like:
* timeout
* connection refused
* 503 Service Unavailable
* failed to fetch from <service-name>
