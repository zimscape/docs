# Environments

Environments give you the flexibility to expose different versions of your application whenever there is need
to. Not every product on Zimscape exposes the ability to manage environments however. Some projects deploy with a base
environment and will only offer that base environment throughout the product's lifespan, for example database 
products like MySQL.

### Document Roots

Each environment has a document root. This is where files related to that environment are stored. This is true 
for both prepackaged applications and applications deployed to development environments. Document roots are 
important because only files in these locations persist across container restarts. This means that applications 
deployed on Zimscape should save data meant for persistence to the applications root path.

### Ports

