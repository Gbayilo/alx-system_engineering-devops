## Issue Summary
**Duration:** The outage lasted for 4 hours and 15 minutes, from 07:30 AM to 11:45 AM UTC + 1 on August 12, 2024.

**Impact:**  The primary service affected was the company's e-commerce platform, where users experienced extremely slow loading times or complete inaccessibility. Approximately 45% of users were impacted globally, with the majority of affected users located in Europe. This led to a 35% drop in transactions during the outage window, resulting in significant revenue loss.

**Root Cause:** A recent deployment introduced a misconfiguration in the Nginx load balancer, causing uneven traffic distribution, which overwhelmed a single node in the web server cluster. This node eventually became unresponsive, leading to slow loading times and service disruptions for users attempting to access the platform.

## Timeline
- 07:30 AM UTC + 1: Initial detection by automated monitoring alert indicating a spike in 5xx errors and a drop in API response times.
- 07:40 AM UTC + 1: Engineering team notified via Slack; initial investigation starts by reviewing recent deployments and server logs.
- 08:00 AM UTC + 1: Misleading assumption that the issue was related to an external payment gateway due to error logs referencing API timeouts.
- 08:20 AM UTC: Escalated to the DevOps team after failing to identify the cause within the application layer.
- 08:45 AM UTC + 1: DevOps team identified that one web server node was handling an abnormally high amount of traffic.
- 09:15 AM UTC + 1: Issue traced back to the load balancer misconfiguration deployed earlier in the morning.
- 09:30 AM UTC + 1: Corrective action taken by reverting the load balancer configuration to its previous state.
- 10:00 AM UTC + 1: Full service restoration confirmed, though monitoring continued for another hour.
- 11:45 AM UTC + 1: Incident officially closed after ensuring system stability and normal traffic distribution across the cluster.

## Root Cause and Resolution

The root cause of the outage was a misconfigured Nginx load balancer, introduced during a routine deployment. The configuration file had a typo in the weight distribution settings for the web server nodes. As a result, traffic was not evenly distributed across the cluster, causing one node to handle the majority of the incoming requests. This led to the node becoming overloaded, leading to response delays and eventual failure.

Once the misconfiguration was identified, the solution involved rolling back to the previous configuration file, redistributing traffic evenly across all nodes. After the rollback, the overloaded node was restarted, and services returned to normal.

## Corrective and Preventative Measures
### Improvements and fixes:
1. Deployment Validation: Implement automated validation of configuration files before deployment to production to catch syntax errors and misconfigurations.
2. Load Balancer Monitoring: Introduce more granular monitoring on the load balancer, specifically focusing on traffic distribution metrics.
3. Failover Procedures: Establish better failover procedures to automatically reroute traffic if a node shows signs of overload.

### TODOs:
1. Add automated syntax checking for Nginx configuration files in the CI/CD pipeline.
2. Patch the Nginx servers with the latest update to include improved load balancing algorithms.
3. Implement additional monitoring for individual server nodes, ensuring early detection of traffic spikes or overloads.
4. Review and update failover strategies in the event of similar load distribution issues in the future.
5. Conduct a post-incident training session with the engineering and DevOps teams to improve incident response and communication strategies.
