# Continuous Deployment

There has been a lot of talk in the tech community about Continuous Integration and Continuous Deployment (CI/CD)
over the last few years. We won't go much into that conversation beyond describing these concepts and pointing
out their relevance to Zimscape. 

Continuous Integration refers to the frequent merging of different streams of work by developers to better 
represent the state of a project over time. Continuous Deployment has to do with frequently generating 
deployment packs to be deployed to customer facing environments in order to continuously ship software
changes as they become available.

Considering that at Zimscape we are more inclined towards solving deployment problems as opposed to
integration problems here are a few differences between Zimscape and other CI/CD platforms:

- Zimscape does not support integration pipelines out of the box
- Zimscape recommends that tests and packaging be run client side or in CI/CD pipeline programs like
Jenkins or Gitlab CI and then output meant for deployment deployed on Zimscape
- For edge cases Zimscape does offer the ability to run commands before deploying a service via a 
`.runfile.yml`

### Runfile

The runfile is a yaml document that post deployment hooks on Zimscape servers use to know how to run an 
application and whether or not there are commands that should precede the service run command. 

The run file supports **three** keys; `package`, `run` and `root`. The `.runfile.yml` file itself and 
all keys are optional.

### `package` 
Type: `List<String>`

Commands that should run before the service starts.

### `run`
Type: `String`

Command to start the service.

### `root`
Type `String`

This key indicates which folder the web server should serve from.

!!! warning
    This key only applies to services that have installed a web server [add-on](architecture/addons.md). 

#### Examples
Here are a few basic examples:
#### Java Application
    :::yaml
    package:
      - mvn clean package
    run: java -jar target/demo-app.jar

#### Mkdocs (Python)
    :::yaml
    package:
      - pip install mkdocs
      - pip install mkdocs-material
      - mkdocs build
    root: site

#### PHP Using Composer
    :::yaml
    package:
      - composer install


