# Containers

Applications on Zimscape run in containerized environments. These are essentially Debian based micro 
operating systems that have server side programming languages and applications pre-installed. 
Each product is deployed in its own container with a set level of resources based on the plan chosen. 
Containerization as a convention has many benefits documented across the internet, however the ones we 
leverage on the most are:

- Can be quickly spawned or stripped down
- Consistent predictable way of managing data persistence
- Abstraction of infrastructure components (for one example, see [Data & Storage](../data.md))
- Simplified scaling
- Automated load balancing of instances

### Things to note about containerized environments are:

- They are ephemeral, that is they have a limited lifespan and, as a result, do not maintain state. This is 
important because your app must be designed to persist data in specific storage locations if the data is 
required for more than 24hrs. See [environments](environments.md).
- Each container exposes a maximum of **three** ports for use by services that run inside it. These ports
are primarily for the development, staging and live environments but can be re-purposed if a service needs
to expose more than one port.
- Each container has a private hostname accessible only by applications deployed in the same project. This 
is convenient and faster when app to app communication doesn't require going over the internet e.g. a java
application communicating with a mysql instance. For services running in that container the private hostname 
can be used interchangeably with localhost.

