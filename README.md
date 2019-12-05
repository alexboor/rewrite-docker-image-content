# How to rewrite content in a container?

A small example of how we still let dev-squads build their 
containers, and at the same time ops-team can prepare 
containers for production without actual build whole 
application without all necessary tools and dependencies. 

To develop this way can clearly split the delivery process to 
different environments without rebuild containers, control 
configurations for each environment and automate all stages 
if CD process. 

If Develop it even more then can implement auto-rotating 
secrets/certificates in production environment in automaticall 
basis.

### DEV part

Small example based on Nginx application, which returns index.html
for any request. 

Let assume that this is our application. All necessary steps 
performs from `Dockerfile.dev`. Basically there we copy configs 
and `index.dev.html` to `index.html` to detect container version. 

To build use:

`docker build -f Dockerfile.dev -t nginx:dev .`

then we get local docker image tagged as `nginx:dev`

If run it:

`docker run -p 80:80 nginx:dev`

and open browser at http://localhost/ we can see *DEVELOPMENT ENVIRONMENT* 
caption on the screen - the content from `index.dev.html` file.

Could say that dev version of our application built successfully. 

Go next?

### QA part

Assume that our image ready for QA environment. On QA we need different 
databases with seeded data, most probably we need different certificates 
to interact with other systems in QA contour etc. In other words, we need 
to update some files and configuration parameters for application inside 
the container. Let's skip configurations as it should come from ENV and 
it easy to implement. But how to deal with files we need put inside the 
container? Actually it also pretty easy - check `Dockerfile.qa`.

`docker build -f Dockerfile.qa -t nginx:qa .`

`docker run -p 80:80 nginx:qa`

Open it in a browser and get `QA` caption on the screen. 

We had to update the index.html file in the container but we didn't touch 
anything else. Not necessary to install all toolset and bunch of libraries 
to build application container as developers do. Only operation manipulations. 
Easy? 

### Production part

Do the same approach. But here at this stage we already have tested not 
only application together with configuration set, but also the approach. 
So if it comes from dev to QA with change only necessary configuration, 
then we could say that this configuration set work and updating automatically.

`docker build -f Dockerfile.prod -t nginx:prod .`

`docker run -p 80:80 nginx:prod` 

and get 'production' caption on the page. 

Developers doing their job, ops doing their job, everybody happy. 