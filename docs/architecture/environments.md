# Environments

Environments give you the flexibility to expose different versions of your application whenever there is need
to. Not every product on Zimscape exposes the ability to manage environments however. Some projects deploy with a base
environment and will only offer that base environment throughout the product's lifespan, for example database 
products like MySQL.

### Uploading your code base

Zimscape takes the opinionated approach of using Git source control to manage your separate environments. This
decision came easily because Git is already widely used and is an excellent platform for managing and 
transporting code bases across servers. 

In the event that you cannot use Git for some reason there are [other ways](../data.md)
to upload your code to your respective environment.

### Document Roots

Each environment has a document root. This is where files related to that environment are stored. This is true 
for both prepackaged applications and applications deployed to development environments. Document roots are 
important because only files in these locations persist across container restarts. This means that applications 
deployed on Zimscape should save data meant for persistence on the applications root path.

### Ports

Each environment exposes a single port. This means that each container exposes a maximum of three ports for 
use by services that run inside it. These ports are primarily for the development, staging and live 
environments but can be re-purposed if a service needs to expose more than one port. 
The application should therefore be designed to expose a service
on the port available on the environment to be deployed. The developer should coordinate versions deployed
in order to avoid port conflicts as an application's environments are deployed on the same port range.
