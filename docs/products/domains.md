# Domain Names

### Changing Nameservers

If you already own a domain name then your domain name is pointed to your registrars `nameservers`. 
Upon successful checkout you will receive an email saying that you need to change those nameservers 
to point to our nameservers. This is necessary because in order for your emails to be formatted 
correctly when sending, the nameserver needs to sign them appropriately otherwise they will be 
detected as spam.

**Zimscape Nameservers**
```
ns1.zimscape.com
ns2.zimscape.com
```

### DNS Records

DNS Records are the configuration records that tell traffic that's trying to access your domain name 
where the domain name's respective servers are. DNS records basically link the domain name to server IPs. 

In the case where:

- You have a domain name not registered with us but have changed your domain name's nameservers to ours.
- You have other services that rely on records on the old service provider's nameservers.

Then you will need to update the relevant records on the DNS portal to match the records that existed 
in the old nameserver. 

A common scenario for this would be if you already have a website but have purchased email hosting 
with us. You will need to update your `A records` on our DNS portal to point to your website server's 
IP to avoid service disruption to your website.
