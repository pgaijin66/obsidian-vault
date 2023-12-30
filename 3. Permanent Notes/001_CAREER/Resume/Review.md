

As someone who has been a hiring manager in the past, I'm wary of resumes that are just lists of keywords and technologies. I like to hear more about depth and breadth of scope, responsibility, and achievement.

What business problems have you solved using those technologies?

Compare and contrast your 2017 bullet point "created data pipelines using AWS Kinesis and S3" with your 2022 list of technologies.

```nomnoml
[<start>start] -> [<state>plunder]
```

The former tells more about your actual experience and what sorts of problems you've solved. The latter might be someone who once used a prebuilt terraform module to deploy an AKS cluster, but even that is just guesswork.

It sounds like you are a freelance contractor in the latter role. So how many clients did you work with? Generally, what business problems did you solve for them and at what scale? Can you attach broad metrics to that?

I'd much rather read:

> -   Helped plan and carry out migration of X workloads from VMs to containers over Y months. Resulting in reduced cycle time and MTTR, plus cost savings of appx $Z/mo
>     
> -   Tech: Azure, Kubernetes, Gitlab, etc...
>     

To just a flat list of technologies where I need to guess if you one time ran apply on a pre-existing terraform project as an intern or if you helped design, implement, and support a set of reusable terraform modules as part of a platform engineering team.

You might consider creating an 'achievement log', this can help provide a good pool of work to pull from and help tailor your resume for the roles you're going for.

I try to use the SBI model - situation, behavior, impact. What was going on, what did _you_ do, what was the impact?

Rather than a list of keywords, something like:

“Existing log management system was slow and too expensive. I learned Elk, implemented it in our k8s environment. Developer cycle time was reduced by N, and we saved $Y”

So just my opinion and hope it helps (current SW Engineering Manager):

-   Do you have a summary at the start of your resume indicating what you are looking for in a role (i.e. seeking Senior DevOps Developer role etc.)? While you list your experience, I wouldn't want to waste my time (or yours!) in an interview where the role itself may not be a fit up-front. Its important as you would be shocked with resumes that end up getting routed to me as the hiring manager. My favorite are when recruiters send me Electrical Engineers with outstanding certs and experience in cable work... For my software development role.
    
-   As someone else mentioned, what type of projects were you working on with your skills? What type of systems were you providing DevOps for? I would have a technical summary for each position (with the tech used) but really provide what you did while in each position. As a hiring manager you're only giving me a title and some technologies - What did you DO? Having said that, I would only do it for your more recent roles to avoid your resume turning into a small novel!
    
-   With your resume I'm going to make an assumption and I believe you've left out soft skills / tech here: Have you worked on Agile / Scrum / SaFE teams and understand the methodologies? Have you worked with JIRA, Rally, Trello, etc.? Its quite possible your resume is getting cut for not having that experience listed before it even gets to the manager.
    

I'd also recommend trying to reach out to a few of the companies where you haven't heard back or were rejected and ask for feedback. Worst case they don't respond, better case they may provide you feedback that will help in future submissions. Best case it was some form of automated process that kicked your resume out, and now they may look at it!

Others have basically said this, but I'll +1. As a hiring manager.. this looks to me like someone just threw together lots of key/buzzwords. I have literally no idea what you did in your last 2 roles and only an inkling in the ones before that. People want to see significant projects/impacts/outcomes. I get that every job isn't "I cured cancer", or "I saved the company 8 trillion dollars", but you gotta give people _something_.


Resume review

Use STAR or SBI ( Situation, Behavior, Impact ) model while describing your responsibilities.  Something like this “Helped plan and carry out development of X app to address Y. Resulted in Z”. I can see you have done in couple of those, very good but it is still to vague. Eg in optimized web pages, you could add what did you actually optimize, was it SEO, page load time, accessibility and how did you achieve that, and then the result. Even better if you could provide some metrics or numbers. HR and interviewers love numbers.
Avoid using a number of technical jargon to create the impression that you have expertise in the field. Instead, provide tangible examples that people can examine. 

A good interviewer will immediately stop if you used the tech in a side project and never touched it again, or actually know about the stuff. While it may not always be possible to showcase accomplishments as significant as curing cancer or saving a company millions of dollars, it is still important to offer something of value for others to observe and examine.
Consider shortening your objective statement as recruiters often receive a high volume of applications and have limited time to review each one thoroughly.
Remove HTML and CSS from the list of programming languages as they are not technically considered languages. Given your proficiency in TypeScript, JavaScript, and Node, it can be inferred that you also have knowledge of HTML and CSS.
Consider removing non-technical skills such as Word, Excel, and PowerPoint from your resume as they may not be relevant to the position and could potentially affect your credibility.
Remove the “Basic Knowledge” section and instead follow the format of “Type: Name” for your skills, such as “Automation: Jenkins” and “Containerization: Docker, Kubernetes”.
Move your education section below your experience and projects. Highlight your experience and projects first, as they are likely to be more relevant to the position you are applying for.
Optimize the use of screen real-estate and aim to keep your resume to one page.
Ensure consistency in the project descriptions, using a brief two-word description with a Git link and deployment URL.
Consider leaving projects with duplicate tech stacks to your portfolio website. Three projects with similar stacks should be sufficient.
Remove descriptions from the education section to free up space in your resume.
Consider learning TDD and adding it to your skills section. It is a valuable skill to have. ( Bonus )
Would be good to see if candidate displays some knowledge of security displayed. At least have a look into OWASP top 10 and see if you application has addressed those. ( Bonus )