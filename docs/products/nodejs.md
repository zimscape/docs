# Node JS

Zimscape currently supports the following Node.js versions:

- 10.x
- 12.x

We are going to demonstrate how to deploy a Node.js application on Zimscape by deploying a basic Sails.js
'Hello World' application.

This tutorial assumes that you have the following:

- A Zimscape [account](https://zimscape.com/register) and a deployed Node.js subscription
- The Zimscape [CLI](../cli.md)
- A text editor
- A supported version of Node (10 / 12)
- Sails.js
- Git

Also required is a general appreciation of Git commands. [Here's](https://git-scm.com/book/en/v2) a tutorial
to get you up to speed if you need one.

If you have these in order then proceed.

### Get the source code

Clone the sample source code from the Zimscape tutorials repository.

```
git clone git@github.com:zimscape/sails-demo.git
```

### Generate an action

We are going to use `sails-generate` to generate an action. Use the following command.

```
sails generate action hello/index
```

Open up the `sails-demo` folder in your favorite text editor, open the file 
`api/controllers/hello/index.js` and update the file with the following code:

    :::node
    module.exports = {
      friendlyName: 'Index',
      description: 'Index hello.',
      inputs: {
    
      },
      exits: {
    
      },
      fn: async function (inputs) {
    
        return 'Hello World! Welcome digital explorer!';
    
      }
    };

To call the action, we need to map it to a URL. This is done in the `routes.js` file. 
Open the `config/routes.js` file and update it with this content:

    :::node
    module.exports.routes = {
    
      '/': 'hello/index',
    
    };
    
### Running the application

Once that's done, test the application by running

```
$ sails lift

 info: Starting app...

 info: 
 info:                .-..-.
 info: 
 info:    Sails              <|    .-..-.
 info:    v1.2.4              |\
 info:                       /|.\
 info:                      / || \
 info:                    ,'  |'  \
 info:                 .-'.-==|/_--'
 info:                 `--'-------' 
 info:    __---___--___---___--___---___--___
 info:  ____---___--___---___--___---___--___-__
 info: 
 info: Server lifted in `/sails-demo`
 info: To shut down Sails, press <CTRL> + C at any time.
 info: Read more at https://sailsjs.com/support.

debug: -------------------------------------------------------
debug: :: Mon Apr 27 2020 17:25:01 GMT+0200 (Central Africa Time)

debug: Environment : development
debug: Port        : 1337
debug: -------------------------------------------------------
```

Open a different terminal window and make a request to the service:

```
$ curl localhost:1337
Hello World! Welcome digital explorer!
```

Looks like everything is setup nicely. Hit Ctrl+C to close the server.


### Update exposed port

Containers on Zimscape expose specific ports which are reverse proxied before being exposed to end users. Your
application needs to update the port it listens on to match the exposed container port. To see which port
your container exposes for your chosen environment use the cli to list available environments.

```
$ zimscape ls
one moment please...
Product   Project                       Domain                        Type       Status    Ports       
NODEJS    zimscape.com                  nodejs.zimscape.com           staging    active    80  -> 9500 
```

The `Ports` column shows that requests on port 80 on the domain `nodejs.zimscape.com` get 
forwarded to the container port 9500. As such, the appropriate command to use to start up the 
node server would look something like this:

```
node app.js --prod --port 9500
```

We'll add this line to the `.runfile.yml`.

Finally, update the `sails.config.sockets.onlyAllowOrigins` array with the host we will be deploying the 
application on. Open `config/env/production.js` and update the array:

    :::javascript
    sockets: {
        ...
        onlyAllowOrigins: [
          'https://nodejs.zimscape.com',
        ],
        ...
    }


!!!warning
    The node server will expect to have the exposed port accessible. It is up to the developer to make sure
    there are no port conflicts. For example, this setup would not work if the Apache Add On is installed
    since it listens on the available environment ports.

### Add a runfile

Use a [runfile](../cd.md#runfile) to tell Zimscape servers how to handle your source code once you've pushed
it. Create a file `.runfile.yml` in the root of your project folder with the following content:

```yaml
package:
  - npm install
run: node app.js --prod --port 9500
```

Your project folder should now look something like this:

```
api
app.js
assets
config
.editorconfig
.eslintignore
.eslintrc
.git
.gitignore
Gruntfile.js
node_modules
.npmrc
package.json
package-lock.json
README.md
.runfile.yml
.sailsrc
tasks
views
```

Create your first commit:

```
git add . && git commit -m "initial commit"
```

This saves a snapshot of your code base.

### Push your source code

List the available environments using the [Zimscape cli](../cli.md):

```
$ zimscape ls
one moment please...
Product   Project                       Domain                        Type       Status    Ports       
NODEJS    zimscape.com                  nodejs.zimscape.com           staging    active    80  -> 9500 
```

Push to the environment domain's remote:

```
$ zimscape push nodejs.zimscape.com
one moment please...
error: src refspec staging does not match any.
error: failed to push some refs to 'zimscape@gitserver.zimscape.com:nodejs-project.git'
```

You should encounter an error. This error happens because the `staging` branch doesn't exist.
This is important to note. The cli looks for a branch name matching the environment type, in this case `staging`.
This is because Zimscape [takes the opinionated approach](../architecture/environments.md) of using specific 
branches to manage your environments. In order to 
utilize all available environments you'll ideally need to have a `master`, `staging` and `dev` branch in your
repo in addition to your other workflow branches.

To proceed, checkout current progress to a `staging` branch. There's a few ways to do this, the easiest being:

```
$ git checkout -b staging
Switched to a new branch 'staging'
```

Now try to push to the staging remote again.

```
$ zimscape push nodejs.zimscape.com
one moment please...
Counting objects: 90, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (81/81), done.
Writing objects: 100% (90/90), 285.82 KiB | 8.66 MiB/s, done.
Total 90 (delta 22), reused 0 (delta 0)
remote: Ref refs/heads/staging received. Deploying 'staging' branch...
remote: Switched to branch 'staging'
remote: Ref refs/heads/staging received and successfully deployed to staging
remote: Stopping service...
remote: Stopping daemon: nodejs-staging.
remote: Packaging: npm install...
remote: Running: node app.js --prod --port 9500...
remote: Restarting daemon: nodejs-staging.
To gitserver.zimscape.com:nodejs-project.git
 * [new branch]      staging -> staging
```

Now open a new terminal and send a request to the service.

```
$ curl --insecure https://nodejs.zimscape.com
Hello World! Welcome digital explorer!
```

If you get that response then congratulations you've successfully deployed a Sails.js application to Zimscape.
A few things to note there are that Zimscape automatically redirects `http` connections to `https`, however
since we haven't [generated an SSL certificate](../console.md) yet the connection will complain about
being insecure. As such, we add an `--insecure` tag to ignore the error for now.

The next step will likely be to [generate an SSL certificate](../console.md) for your environment's domain.

