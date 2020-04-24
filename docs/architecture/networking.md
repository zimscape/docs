# Networking

Applications on Zimscape run in containerized environments. Each project has a self 
contained isolated network that allows applications deployed in a project to communicate with each other. This
communication is achieved through an internal DNS system that links hostnames to container IPs.

### Things to note about networking on Zimscape are:

- Private hostnames are generated based on project domain names. 
- An application can be deployed in any of our server locations depending on capacity and provisioning
requirements. This means that you shouldn't rely on IP addresses advertised by our domains and should 
instead utilize DNS records for routing traffic to your services.
- Each application container has a private IP address assigned to it when it gets spawned, but since 
[containers are ephemeral](containers.md) these are likely to change over time and shouldn't be relied upon 
for application to application communication
