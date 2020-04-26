# Networking

Each application on Zimscape runs in a containerized environment. Each project can have multiple
applications deployed under it. In order to facilitate communication applications under the same project 
run in a project isolated network that allows application to application communication. 
This communication is achieved through an internal DNS system that links hostnames to container IPs.

### Things to note about networking on Zimscape are:

- Private container hostnames are generated based on project domain names. 
- An application can be deployed in any of our server locations depending on capacity and provisioning
requirements. This means that you shouldn't rely on public IP addresses advertised by our domains and should 
instead utilize DNS records for routing traffic to your services.
- Each application container has a private IP address assigned to it when it gets spawned, however since 
[containers are ephemeral](containers.md) these are likely to change over time and shouldn't be relied upon 
for application to application communication
