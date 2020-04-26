# Console

The console is the web based graphical interface that helps users manage their deployments and 
Zimscape accounts.

## Managing Users

Users belong to a Zimscape project, as such, a project can have many users but cannot share the user properties
with another project. Each project must have it's own set of users. User credentials are effective across
all products in a project. Not all products require users however for example, Email Hosting and Storage rely on users for
access control however Dev environments and Databases do not.

## Managing SSH Keys

SSH Keys belong to a project. A project can have many SSH Keys regardless of the number of users the project has.
SSH Keys mainly facilitate the use of the [Zimscape CLI](cli.md). Their use will likely be expanded in the 
future. [Here's](https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) 
a straight forward tutorial on generating SSH keys.

## Managing Products
### SSL Certificates

SSL certificates allow secure connections from a web server to a browser. Zimscape offers free SSL certificates
thanks to the great work from [Let's Encrypt](https://letsencrypt.org/). Certificates on Zimscape are generated
based on available `A Records` on the project's domain name. As such if you need your product to be available on
a specific sub-domain you will have to create that sub-domain first in the DNS management page of
your domain name product then generate the appropriate SSL certificate. 
