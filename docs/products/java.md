# Java

Zimscape currently supports the following Java versions:

- 8
- 11

!!!warning
    It's worth noting unfortunately that Zimscape does not currently support fringe cases to do with 
    Application Servers such as Jboss and WebSphere. Deploying services that depend or bundle these 
    might not work out of the box. 

We are going to demonstrate how to deploy a Java application on Zimscape by deploying a basic Spring Boot
'Hello World' application.

This tutorial assumes that you have the following:

- A Zimscape [account](https://zimscape.com/register) and a deployed Java subscription
- The Zimscape [CLI](../cli.md)
- A text editor
- JDK 1.8 or later
- Git

Also required is a general appreciation of Git commands. [Here's](https://git-scm.com/book/en/v2) a tutorial
to get you up to speed if you need one.

If you have these in order then proceed.

### Get the source code

Clone the sample source code from the Zimscape tutorials repository.

```
git clone git@github.com:zimscape/springboot-demo.git
```

### Add a Controller

Open up the `springboot-demo` folder in your favorite text editor, create the file 
`src/main/java/com/example/demo/HelloController.java` and update the file
with the following code:

    :::java
    package com.example.demo;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Hello World! Welcome digital explorer\n";
        }
    
    }

### Running the application

Once that's done, test the application by running

```
./mvnw spring-boot:run
```

You should see output similar to:

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.2.6.RELEASE)

2020-04-25 19:00:34.697  INFO 22093 --- [           main] com.example.demo.DemoApplication         : No active profile set, falling back to default profiles: default
2020-04-25 19:00:35.351  INFO 22093 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2020-04-25 19:00:35.358  INFO 22093 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2020-04-25 19:00:35.358  INFO 22093 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.33]
2020-04-25 19:00:35.402  INFO 22093 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2020-04-25 19:00:35.402  INFO 22093 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 655 ms
2020-04-25 19:00:35.516  INFO 22093 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2020-04-25 19:00:35.614  INFO 22093 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2020-04-25 19:00:35.616  INFO 22093 --- [           main] com.example.demo.DemoApplication         : Started DemoApplication in 1.192 seconds (JVM running for 1.484)
```

Open a different terminal window and make a request to the service:

```
$ curl localhost:8080
Hello World! Welcome digital explorer
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
JAVA      zimscape.com                  java.zimscape.com             staging    active    80  -> 9000 
```

The `Ports` column shows that requests on port 80 on the domain `java.zimscape.com` get 
forwarded to the container port 9000. As such, update the port that the test application listens on
to port 9000.

Add the following line to the file `src/main/resources/application.properties`:

```
server.port=9000
``` 

Your application will now listen on port `9000`.

### Add a runfile

Use a [runfile](../cd.md#runfile) to tell Zimscape servers how to handle your source code once you've pushed
it. Create a file `.runfile.yml` in the root of your project folder with the following content:

```yaml
package:
  - mvn clean package
run: java -jar target/demo-0.0.1-SNAPSHOT.jar
```

Your project folder should now look something like this:

```
.git  
.gitignore
.mvn  
mvnw  
mvnw.cmd  
pom.xml  
README.md
.runfile.yml  
src  
target
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
JAVA      zimscape.com                  java.zimscape.com             staging    active    80  -> 9000 
```

Push to the environment domain's remote:

```
$ zimscape push java.zimscape.com
one moment please...
error: src refspec staging does not match any.
error: failed to push some refs to 'zimscape@gitserver.zimscape.com:java-project.git'
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
$ zimscape push java.zimscape.com
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
remote: Stopping daemon: java-staging.
remote: Packaging: mvn clean package...
remote: Running: java -jar target/spring-boot-0.0.1-SNAPSHOT.jar...
remote: Restarting daemon: java-staging.
To gitserver.zimscape.com:java-project.git
 * [new branch]      staging -> staging
```

Now open a new terminal and send a request to the service.

```
$ curl --insecure https://java.zimscape.com
Hello World! Welcome digital explorer
```

If you get that response then congratulations you've successfully deployed a Spring Boot application to Zimscape.
A few things to note there are that Zimscape automatically redirects `http` connections to `https`, however
since we haven't [generated an SSL certificate](../console.md) yet the connection will complain about
being insecure. As such, we add an `--insecure` tag to ignore the error for now.

The next step will likely be to [generate an SSL certificate](../console.md) for your environment's domain.
