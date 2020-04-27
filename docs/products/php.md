# PHP

Zimscape currently supports the following PHP versions:

- 7.2

We are going to demonstrate how to deploy a PHP application on Zimscape by deploying a basic Laravel
'Hello World' application.

This tutorial assumes that you have the following:

- A Zimscape [account](https://zimscape.com/register) and a deployed PHP subscription
- The Zimscape [CLI](../cli.md)
- A text editor
- PHP 7.2 or later and Composer installed
- Git

Also required is a general appreciation of Git commands. [Here's](https://git-scm.com/book/en/v2) a tutorial
to get you up to speed if you need one.

If you have these in order then proceed.

### Get the source code

Clone the sample source code from the Zimscape tutorials repository.

```
git clone git@github.com:zimscape/laravel-demo.git
```

### Update a Controller

Open up the `laravel-demo` folder in your favorite text editor, open the file 
`routes/web.php` and update the file with the following code:

    :::php
    <?php
    
    use Illuminate\Support\Facades\Route;
    
    Route::get('/', function () {
        return "Hello World! Welcome digital explorer\n";
    });

### Running the application

First, install composer dependencies using the command. This assumes that composer is installed 
[on your path](https://www.hostinger.com/tutorials/how-to-install-composer)

```
composer install
```

Once that's done, test the application by running

```
$ php artisan serve
Laravel development server started: http://127.0.0.1:8000
```

Open a different terminal window and make a request to the service:

```
$ curl localhost:8000
Hello World! Welcome digital explorer
```

Looks like everything is setup nicely. Hit Ctrl+C to close the server.

### Add a runfile

Use a [runfile](../cd.md#runfile) to tell Zimscape servers how to handle your source code once you've pushed
it. Create a file `.runfile.yml` in the root of your project folder with the following content:

```yaml
package:
  - composer install
  - mv .env.example .env
  - php artisan key:generate
root: public
```

Your project folder should now look something like this:

```
app
artisan
bootstrap
composer.json
composer.lock
config
database
.editorconfig
.env
.env.example
.git
.gitattributes
.gitignore
package.json
package-lock.json
phpunit.xml
public
README.md
resources
routes
.runfile.yml
server.php
storage
.styleci.yml
tests
vendor
webpack.mix.js
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
PHP       zimscape.com                  php.zimscape.com              staging    active    80  -> 8500 
```

Push to the environment domain's remote:

```
$ zimscape push php.zimscape.com
one moment please...
error: src refspec staging does not match any.
error: failed to push some refs to 'zimscape@gitserver.zimscape.com:php-project.git'
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
$ zimscape push php.zimscape.com
one moment please...
Counting objects: 127, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (107/107), done.
Writing objects: 100% (127/127), 157.76 KiB | 6.57 MiB/s, done.
Total 127 (delta 15), reused 0 (delta 0)
remote: Ref refs/heads/staging received. Deploying 'staging' branch...
remote: Switched to branch 'staging'
remote: Ref refs/heads/staging received and successfully deployed to staging
remote: Stopping service...
remote: Stopping daemon: php-staging.
remote: Packaging: composer install...
remote: Loading composer repositories with package information
remote: Installing dependencies (including require-dev) from lock file
remote: Package operations: 92 installs, 0 updates, 0 removals
remote:   - Installing doctrine/inflector (1.3.1): Downloading (100%)         
remote:   - Installing doctrine/lexer (1.2.0): Downloading (100%)         
remote:   - Installing dragonmantank/cron-expression (v2.3.0): Downloading (100%)         
...
remote: Packaging: mv .env.example .env...
remote: Packaging: php artisan key:generate...
remote: Updating root: public...
To gitserver.zimscape.com:php-project.git
 * [new branch]      staging -> staging
```

### Install the Apache Add On

PHP relies on a web server to execute and serve server side code. As such we need to have a web server
running in our environment. Got to the [Zimscape website](https://zimscape.com), click on 
Console > Manage Project > Manage PHP > Add Ons and install
the Apache add on. This gives your environment the ability to serve files that exist on the document root 
such as those we've just pushed.

Now open a new terminal and send a request to the service.

```
$ curl --insecure https://php.zimscape.com
Hello World! Welcome digital explorer
```

If you get that response then congratulations you've successfully deployed a Laravel application to Zimscape.
A few things to note there are that Zimscape automatically redirects `http` connections to `https`, however
since we haven't [generated an SSL certificate](../console.md) yet the connection will complain about
being insecure. As such, we add an `--insecure` tag to ignore the error for now.

The next step will likely be to [generate an SSL certificate](../console.md) for your environment's domain.
