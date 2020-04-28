# Python

Zimscape currently supports the following Python versions:

- 3.6

We are going to demonstrate how to deploy a Python application on Zimscape by deploying a basic Django
'Hello World' application.

This tutorial assumes that you have the following:

- A Zimscape [account](https://zimscape.com/register) and a deployed Python subscription
- The Zimscape [CLI](../cli.md)
- A text editor
- Python 3.6 or later
- The latest Django version and Gunicorn
- Git

Also required is a general appreciation of Git commands. [Here's](https://git-scm.com/book/en/v2) a tutorial
to get you up to speed if you need one.

If you have these in order then proceed.

### Get the source code

Clone the sample source code from the Zimscape tutorials repository.

```
git clone git@github.com:zimscape/django_demo.git
```

### Add a View

Open up the `django_demo` folder in your favorite text editor, open the file 
`hello/views.py` and update the file with the following code:

    :::Python
    from django.http import HttpResponse
    
    def index(request):
        return HttpResponse("Hello, world. Welcome digital explorer\n")

To call the view, we need to map it to a URL. This is done in the `urls.py` file. Create a `hello/urls.py`
file and update it with this content:

    :::python
    from django.urls import path
    
    from . import views
    
    urlpatterns = [
        path('', views.index, name='index'),
    ]
    
Now update the root url configuration to point to the django app we just created. Open 
`django_demo/django_demo/urls.py` and update the file with the following code:

    :::diff
    from django.contrib import admin
    from django.urls import include, path
    
    urlpatterns = [
    +   path('', include('hello.urls')),
        path('admin/', admin.site.urls),
    ]

### Running the application

Once that's done, test the application by running

```
$ python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

April 26, 2020 - 23:49:59
Django version 3.0.5, using settings 'django_demo.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Open a different terminal window and make a request to the service:

```
$ curl localhost:8000
Hello, world. Welcome digital explorer
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
PYTHON    zimscape.com                  python.zimscape.com           staging    active    80  -> 8000 
```

The `Ports` column shows that requests on port 80 on the domain `python.zimscape.com` get 
forwarded to the container port 8000. 

The Python dev environment comes pre-installed with Gunicorn as the wsgi server. 
As such, the appropriate command to use to run our application would look something like this:

```
gunicorn -w 3 -b python-zimscape-com-0:8000 django_demo.wsgi
```

Here we're binding to the hostname of the container. See [networking](../architecture/networking.md) 
for more on networking and containerization. You can find the hostname of your environment on the 
Console > Manage Project > Manage Python > Access page of the Zimscape website.

We'll add this line to the `.runfile.yml`.

Finally, update the `ALLOWED_HOSTS` array with the host we will be deploying the application on. Open
`django_demo/django_demo/settings.py` and update the array:

```
ALLOWED_HOSTS = [ 'python.zimscape.com' ]
```

!!!warning
    Gunicorn will expect to have the exposed port accessible. It is up to the developer to make sure
    there are no port conflicts. For example, this setup would not work if the Apache Add On is installed
    since it listens on the available environment ports.

### Add a runfile

Use a [runfile](../cd.md#runfile) to tell Zimscape servers how to handle your source code once you've pushed
it. Create a file `.runfile.yml` in the root of your project folder with the following content:

```yaml
package:
  - pip install django
run: gunicorn -w 3 -b python-zimscape-com-0:8000 django_demo.wsgi
```

Your project folder should now look something like this:

```
db.sqlite3
django_demo
.git
.gitignore
hello
manage.py
README.md
.runfile.yml
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
PYTHON    zimscape.com                  python.zimscape.com           staging    active    80  -> 8000 
```

Push to the environment domain's remote:

```
$ zimscape push python.zimscape.com
one moment please...
error: src refspec staging does not match any.
error: failed to push some refs to 'zimscape@gitserver.zimscape.com:python-project.git'
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
$ zimscape push python.zimscape.com
one moment please...
Counting objects: 27, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (27/27), 102.10 KiB | 17.02 MiB/s, done.
Total 27 (delta 0), reused 0 (delta 0)
remote: Ref refs/heads/staging received. Deploying 'staging' branch...
remote: Switched to branch 'staging'
remote: Ref refs/heads/staging received and successfully deployed to staging
remote: Stopping service...
remote: Packaging: pip install django...
remote: Running: gunicorn -w 3 -b python-zimscape-com-0:8000 django_demo.wsgi...
remote: Restarting daemon: python-staging.
To gitserver.zimscape.com:python-project.git
 * [new branch]      staging -> staging
```

Now open a new terminal and send a request to the service.

```
$ curl --insecure https://python.zimscape.com
Hello, world. Welcome digital explorer
```

If you get that response then congratulations you've successfully deployed a Django application to Zimscape.
A few things to note there are that Zimscape automatically redirects `http` connections to `https`, however
since we haven't [generated an SSL certificate](../console.md) yet the connection will complain about
being insecure. As such, we add an `--insecure` tag to ignore the error for now.

The next step will likely be to [generate an SSL certificate](../console.md) for your environment's domain.
