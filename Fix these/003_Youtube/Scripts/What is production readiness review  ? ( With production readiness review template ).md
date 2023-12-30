
You application is not "production ready" ?

I am sure you might have heard this phrase from various people working in Tech. 

So what does it actually means ? 

In this video we will look into what it means when someone from your team says the phrase "its not ready for production" 

and we will also discuss what are the requirements for an application to be called production ready. 

The word "Production ready" means differently to different people. 

You will hear different views based on their line of work, whether they are developers, management people or from ops team.

For instance, for a developer it might mean that,  a code can be considered production ready if it satisfies the feature requirements, if it runs, if it passes all the test cases and  it is scalable and maintainable

For project owner, it might mean that a application can be considered production ready if the requested feature is present, it works and it brings some revenue into the company.

So all of this can be summarized into a single definition which is an application can be considered production ready if it is ready to run in production environment. 

You might be thinking, that definition does not even makes sense right. 

So hear me out,  

the reason i say this is because when deploying any application to production, we would need to consider a lot of things such as

Does it runs, 
Does it has tests present and all of them has passed, 
Is code scalable and maintainable, 
Is it well documented, 
Does it have a proper release and rollback plan, 
Did it went through some level of security audit, 
Does it have acceptable performance, 
Is it automated, 
Does it have well tested backups and DR plans
Does it have observability in place (metrics, logs and traces) along with key indicators which shows service is performing well.
Does it have proper ownership defined.
Does it have a proper change management process in place


so these are some of the things that you might consider when thinking about application being production ready.

And If any application satisfies these conditions, then,

it is ready to run in production. 

So here is a quick pop quiz, do all the service needs to be reviewed for "production readiness" at such depth ?

Well, the answer is "it depends"

It depends on various factors like project timeline, company and team size, budget, scope of the project etc. 

The next thing we will discuss is how does a company determine whether an application is production ready or not ?

Well, this is done using a process called Production readiness review process or PRR in short

A PRR process produces a document which might be a small checklist or a fully comprehensive document.

==> PRR consists of Authors or PRR leads who are responsible for creating and formulating the PRR checklist

It also consists of PRR reviewers who are responsible to discussing the checklist answers, find shortcomings and propose different fixes. Generally PRR reviewers are experienced engineers  . <==

where everything is defined in as detail as it can get on how to determine if a service is ready for production.

It all depends on the company and their definition of "production readiness standards"

PRR process also differs from company to company for example Googles PRR process looks something like this

1. First of all authors are selected to carry out the PRR process
2. Authors then communicate with different teams including the dev teams to come up with a process, end goal, outcomes. 
3. This includes consulting to other teams which have more expertise in that subject matter to see if it follows production best practices. 
4. All of these are then compiled into a checklist
5. Once PRR authors create a draft checklist , it undergoes improvements and refactoring. 
6. Not everything listed in the draft will be addressed. 
8. Once refactoring is done, then SRE team who will eventually take ownership of the service will undergo knowledge transfer, trainings sessions and exercises from dev teams.
9. This training is led generally by PRR authors.
10. Once training phase is completed, progressive transfer of responsibilities and ownership is taken by the SRE team. 
11. This includes parts like operations  work, release management, access controls etc.


Now let's talk about why do we need a production readiness review document ?

The main reasons why companies should be creating a PRR document is

1. It Verifies that that service meets production requirements some of which are ( ACL, security, release management process etc)
2.  it Improve reliability
3. it Helps to Minimise incidents
4. and also helps to promote Continuous improvements of the service in production.

In general a productions readiness review is a must have process if a company wants to have a flawless release of any service and wants to maintain service in production with less incidents.

Let's have a look at a template PRR document. This is what a PRR template document looks like. 

Here we can see the checklist being separated into different parts, like architecture, operational risk, database etc.

Each part has checklist of actionable items which needs to be addressed as part of the readiness review.


In a PRR document we add almost all of the things that has to go right 

along with things that might go wrong well 

and determine if there is a process to address that.

I will leave a link to this document in the description below. 

Please feel free to reach out tif you have any question

So, I hope you enjoyed this video, 

if you still have not, don't forget to smash the like button and subscribe to the channel if you haven't already. 

I will see you in the next video.