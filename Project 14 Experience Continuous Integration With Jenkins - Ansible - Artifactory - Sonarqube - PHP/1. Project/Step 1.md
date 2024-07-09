# WHY ARE WE DOING EVERYTHING WE ARE DOING? – 13 DEVOPS SUCCESS METRICS

After all, DevOps is all about continuous delivery or deployment, and being able to ship out quality code as fast as possible. This is
a very ambitious thing to desire; therefore, we must be careful not to break things as we are moving very fast. By tracking these
metrics, we can determine our delivery speed and bottlenecks before breaking things. Ultimately, the goals of DevOps are enhanced
Velocity, Quality, and Performance. But how do we track these parameters? Let us have a look at the 13 metrics to watch out for.

1. Deployment frequency: Tracking how often you do deployments is a good DevOps metric. Ultimately, the goal is to do more smaller
   deployments as often as possible. Reducing the size of deployments makes it easier to test and release. I would suggest counting
   both production and non-production deployments separately. How often you deploy to QA or pre-production environments is also important.
   You need to deploy early and often in QA to ensure enough time for testing.

2. Lead time: If the goal is to ship code quickly, this is a key DevOps metric. I would define lead time as the amount of time that
   occurs between starting on a work item until it is deployed. This helps you know that if you started on a new work item today, how
   long would it take on average until it gets to production.

3. Customer tickets: The best and worst indicator of application problems is customer support tickets and feedback. The last thing you
   want is your users reporting bugs or having problems with your software. Because of this, customer tickets also serve as a good
   indicator of application quality and performance problems.

4. Percentage of passed automated tests: To increase velocity, it is highly recommended that the development team makes extensive
   usage of unit and functional testing. Since DevOps relies heavily on automation, tracking how well automated tests work is a good
   DevOps metrics. It is good to know how often code changes break tests.

5. Defect escape rate: Do you know how many software defects are being found in production versus QA? If you want to ship code fast,
   you need to have confidence that you can find software defects before they get to production. Defect escape rate is a great DevOps
   metric to track how often those defects make it to production.

6. Availability: The last thing we ever want is for our application to be down. Depending on the type of application and how we
   deploy it, we may have a little downtime as part of scheduled maintenance. It is highly recommended to track this metric and all
   unplanned outages. Most software companies build status pages to track this. Such as this Google Products Status Page

7. Service level agreements: Most companies have some service level agreement (SLA) that they promise to the customers. It is also
   important to track compliance with SLAs. Even if there are no formally stated SLAs, there probably are application non-functional
   requirements or expectations to be met.

8. Failed deployments: We all hope this never happens, but how often do our deployments cause an outage or major issues for the
   users? Reversing a failed deployment is something we never want to do, but it is something you should always plan for. If you have
   issues with failed deployments, be sure to track this metric over time. This could also be seen as tracking
   \*Mean Time To Failure (MTTF).

9. Error rates: Tracking error rates within the application is super important. Not only they serve as an indicator of quality
   problems, but also ongoing performance and uptime related issues. In software development, errors are also known as exceptions,
   and proper exception handling is critical. If they are not handled nicely, we can figure it out while monitoring the rate of errors.

- Bugs – Identify new exceptions being thrown in the code after a deployment
- Production issues – Capture issues with database connections, query timeouts, and other related issues

Presenting error rate metrics like this simply gives greater insights into where to focus attention.

10. Application usage & traffic: After a deployment, we want to see if the number of transactions or users accessing our system
    looks normal. If we suddenly have no traffic or a giant spike in traffic, something could be wrong. An attacker may be routing
    traffic elsewhere, or initiating a DDOS attack

11. Application performance: Before we even perform a deployment, we should configure monitoring tools like Retrace, DataDog,
    New Relic, or AppDynamics to look for performance problems, hidden errors, and other issues. During and after the deployment, we
    should also look for any changes in overall application performance and establish some benchmarks to know when things deviate from
    the norm.

It might be common after a deployment to see major changes in the usage of specific SQL queries, web service or HTTP calls, and other
application dependencies. These monitoring tools can provide valuable visualizations like this one below that helps make it easy to
spot problems.

12. Mean time to detection (MTTD): When problems happen, it is important that we identify them quickly. The last thing we want is
    to have a major partial or complete system outage and not know about it. Having robust application monitoring and good observability
    tools in place will help us detect issues quickly. Once they are detected, we also must fix them quickly!

13. Mean time to recovery (MTTR): This metric helps us track how long it takes to recover from failures. A key metric for the
    business is keeping failures to a minimum and being able to recover from them quickly. It is typically measured in hours and
    may refer to business hours, not calendar hours.

These are the major metrics that any DevOps team should track and monitor to understand how well CI/CD process is established and
how it helps to deliver quality application to the users.
