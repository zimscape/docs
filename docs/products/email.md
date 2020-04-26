# Email Hosting

#### Deploying File Storage

Zimscape offers storage as a separate product from the services that use the storage. This gives you the 
flexibility of expanding or shrinking your storage without affecting your product. Another benefit is 
the ability to reuse excess storage for other deployments which further reduces costs.

#### Connecting your emails to your phone, tablet or PC

Once you've successfully deployed your email hosting you can receive or send emails straight from 
your device. To do this use the settings below:

#### General

    :::yaml
    type: IMAP

#### Incoming Server

    :::yaml
    server: imap.zimscape.com
    port: 143
    security: None
    username: your email address
    password: your email password

#### Outgoing / SMTP Server

    :::yaml
    server: smtp.zimscape.com
    port: 587
    security: STARTTLS
    username: your email address
    password: your email password
