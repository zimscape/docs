# Data Access & Storage

Storage on Zimscape is treated as a product. It's possible to provision storage independently from the number 
and type of products deployed. The benefits of this approach include:

- Flexibility when needing to scale storage.
- Cost effectiveness as an abstract storage layer. Can be used by all deployed products if necessary or by
none if not necessary.
- Can be deployed as a stand alone product for use by third party services.

### SSH Keys

Programmatic access to your Zimscape storage is primarily via SSH Keys. You can manage your SSH Keys via the 
[console](console.md). Zimscape does not currently allow shell connections.
 
### FTP 

Zimscape also exposes FTP access to your storage. Credentials for FTP access can be managed from the user
management page of your console. 
