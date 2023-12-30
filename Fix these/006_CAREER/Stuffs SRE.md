```
STUFF 


https://www.brendangregg.com/USEmethod/use-rosetta.html https://github.com/mxssl/sre-interview-prep-guide https://github.com/michaelkkehoe/sre-interview https://syedali.net/engineer-interview-questions/ https://github.com/trimstray/test-your-sysadmin-skills https://roadmap.sh/devops https://blog.balthazar-rouberol.com/preparing-the-sre-interview https://catonmat.net/my-job-interview-at-google 


From danrl.com on troubleshooting: 

* Julia Evans publishes short comics about useful system administration tools. The comics are now also available as books. 
* Brendan Gregg’s Linux performance page was a good starting point to learn more about operating system telemetry and 
* https://www.brendangregg.com/linuxperf.html 
* analysis tools. He delivered plenty of talks on system performance such as Linux Performance Tools (Part 1) and Linux Performance Tools (Part 2) which I enjoyed. 

* Occasionally interesting posts related to troubleshooting would pop up on company blogs. I particularly remember Linux Kernel Bug Hunting by booking.com.

* The free Advanced Operating Systems self-learning course by the Georgia Institute of Technology is something I briefly looked into. 

* I made sure I am not missing an important piece of the puzzle by watching videos from Brian Will’s Hardware Basics YouTube playlist and skipping through this video about Operating System Basics. 

* I loved the Computer Systems Engineering lectures by the Massachusetts Institute of Technology. I watched the whole course three times and every time I learned something new. The value is not only in the lecture content, but in pausing the lectures every now and then and start thinking about the underlying problems. 

* There’s been some research on How complex systems fail. 

* My recruiter recommended that I take a look at Life in App Engine production to learn more about troubleshooting real life systems. 

http://sreweekly.com/ https://jvns.ca/blog/2019/06/23/a-few-debugging-resources/

Recommended from engineer from YouTube video

https://techdevguide.withgoogle.com/paths/interview/?no-filter=true

Reccomended from blind 
https://leetcode.com/discuss/interview-question/352460/Google-Online-Assessment-Questions/320460 

GitHub repo with are resources ( some come from google) -from discord https://github.com/rishiloyola/SRE-Interviews Discord https://discord.com/channels/839106968425201664/839106968425201667 Resource from discord https://sre.google/resources/book-update/ 


How to design a distributed System (google) https://www.youtube.com/watch?v=ohtqI3AHR0k Non-Abstract system Design (recommended from discord) https://sre.google/classroom/imageserver/ 

* design part is simple but they are more interested in the total no of machines required for the setup and the optimization steps we take. Reading whitepapers will help or the Advanced system design course in educative.io Troubleshooting, for me it was a multi-tier application with LB, application, database etc. Most time was spend on isolating the issue to a single component rather than running troubleshooting commands everywhere if others are interested: 
* 
* https://sre.google/classroom/distributed-pubsub/ and https://sre.google/classroom/imageserver/ 

- you can find recordings of google doing these workshops in conference 
- 

*******Recommended guide from discord**** 

-main prep and build around https://underpaid.medium.com/i-received-sre-offers-from-facebook-and-google-without-a-university-degree-here-is-how-224f06b49e7d SRE podcast https://sre.google/prodcast/?utm_campaign=603f978448788b0001425700&utm_content=6255a800070f050001955517&utm_medium=smarpshare&utm_source=twitter 

Problem Examples: 

* Reverse Polish notation, also known as reverse Łukasiewicz notation, Polish postfix notation or simply postfix notation 


* Phone Screen: A command line tool implementation, One lc easy and a log parsing problem. 


* Practical Coding: Another command line tool implementation, little difficult compared to Phone screen. Linux/Unix Internals 1: Starts from a basic question and goes deep into OS concepts based on my understanding. Linux/Unix Internals 2: Similar to round 1, mostly around shell, http, security etc. 

* Googliness: Usual behavioral round. Troubleshooting: Debugging a disk related issue. 

* For Google SE-SRE they will grind you on System calls , they also might ask you to implement a linux utility using python or something like this https://stackoverflow.com/questions/16395207/create-a-process-tree-like-pstree-command-with-python-in-linux * There were also some basic UNIX questions, like how you can delete a file starting with a dash as well as basic networking - how does a switch work * You will need to know how the malloc() works and how the memory allocator is implemented in, say, glibc, how the processes are started and pass the data between themselves on the low level. * why strace output is full of sbrk() and mmap() calls. * what's wrong with def foo(data=[]): *  finding the reason of hanging terminal during the SSH connection. Locales, environment, libraries, networking MTU * asked to concretely design a largescale system, such as a system to join different types of log entries written in multiple datacenters for analysis. * we’re looking for candidates to be able to provide realistic estimates of throughput, storage, and so on for each component—while considering various tradeoffs for reliability, cost, and difficulty of building the system. The ideal candidate can not only reason about how each high-level component fits together, but work through each layer in the design, right down to the hardware underpinning it. - Given integer convert to binary, then replace all '0' with '1' and vice versa and return int again * - Given string of words without delimiter you should return all possible words based on some dictionary (let say 'happydogcat' should return ['happy', 'dog', 'cat']) * https://www.careercup.com/question?id=5675828586217472 * # Google: thankfully I skipped all steps and went directly to onsite: - 2 Coding interviews - veeery different as they were both way "slower" paced compared to FB, only 1 question each and spent a LOT of time talking about trade-offs and "what if" scenarios and follow ups - System Design: very similar to FB, again focus on infra/automation and not on externally facing products/features - NALSD: This was a nightmare, it is surely the most fun interview but it is just so hard, it was like a focused system design in which the architecture being design is way simpler but you need to get very deep into estimating and calculation all your resources/hosts - I should have devoted at least 5x more prep time into this I think as I was really not ready - Behavioural: Same as FB, maybe a bit more focus on "what did you do when X went wrong" * LC/Coding: I got LC Premium and did 80 LC focusing on easy/medium questions for FB based on frequency. I like to write notes to come back to study so as I was coding I created this https://turquoise-sneezeweed-ac9.notion.site/LeetCode-Sep-2021-346c69db2a4045fbb7037912fd92c492 and used it all the time to come back and spot patterns and stuff on questions/solution. I also got https://technicalinterviews.dev/ to brush up on algorithms and stuff but overall I dont recommend it much as it is very high level (but well organised in case you are in a rush I guess) (edited)  Linux/Systems: I used https://github.com/mxssl/sre-interview-prep-guide as my main guide and just kept reading all articles and googling about anything that I wanted to learn deeper about it. I also read Systems Performance by Brendan Gregg and really recommend it as gives you that applied knowledge of a lot of Linux concepts and that is great for learning. Again I was always taking my own notes for it all and created for it https://turquoise-sneezeweed-ac9.notion.site/Linux-d02f62a8d69a4137829ad9e8791a4153 System Design: I started by reading Designing Data-Intensive Applications and 1000% recommend it as a must read, this book is just so good and fun and amazing of setting the ground on loads of aspects of design. This was a long read and taking your time for it is super valuable. After that I went through Grokking the System Design (FB gave me a code to get it for free, that was cool) and later Grokking the Advanced System Design - I do recommend both for sure althought I didnt get questions very similar to any of their scenarios in my interview. In the end the useful bit was the patterns and mindset of system design and stuff. For NALSD specifically I did all the cases of https://sre.google/classroom/ and watched all nalsd workshops in youtube that I found. As you might have guessed I was writing my own notes and created https://turquoise-sneezeweed-ac9.notion.site/System-Design-b4739c9747c44c49b6ec5bc30647db81 for it Soft Skills/Project Management: I didn't do anything special for it, just listed scenarios from my experience and collected sample questions from the recruiter that I should prepare for  Final remarks: I was not aware of how the recruiters were so helpful so please be aware of that and use it! Ask for more help on resources to prepare, or if there are any free books/courses you can get and stuff. For Google I bombed my 1st NALSD and got offered a redo and before that I had the chance to get tips from another interviewer which was great. Another thing I did was align the processes timelines which was great to make studying more efficient but it got dramatically tiring as I was going over onsites as it was just SO MANY interviews all the time - still would do it all again as now it makes offer negotiation easier System Design -serverless fraud https://www.youtube.com/watch?v=qQnxq3COr9Q https://cloud.google.com/blog/topics/developers-practitioners/your-google-cloud-database-options-explained 

* Coding: Assess simple algorithm/data structure implementation. 


* Operating Systems: Understand processes, threads, concurrency issues, locks, mutexes, semaphores, monitors and how they work. Understand deadlock, livelock and how to avoid them. Know what resources a process and a thread needs. Understand how context switching works, how it's initiated by the operating system and underlying hardware. Know a little about scheduling and the fundamentals of "modern" concurrency constructs. * Unix/Linux: Know what’s happening under the hood. Understand kernels, libraries, system calls, memory management, permissions, file systems, client-server protocols and the shell. Suggested reading: The Art of Unix Programming (https://books.google.ie/books/about/The_Art_of_UNIX_Programming.html?id=H4q1t-jAcBIC&hl=en) and Advanced Programming in the Unix Environment(https://books.google.ie/books/about/Advanced_Programming_in_the_Unix_Environ.html?id=D_VQAAAAMAAJ&redir_esc=y) to review system fundamentals. 


* Troubleshooting: are looking for a logical and structured approach to problem solving through network or distributed systems scenarios. Some troubleshooting examples include an App Engine outage(https://groups.google.com/g/google-appengine/c/6SN_x7CqffU/m/ecHIgNnelboJ) and the Life in App Engine Production(https://www.youtube.com/watch?v=rgQm1KEIIuc) 


* Non-Abstract Large-scale Systems Design - These questions are used to assess a candidate's ability to combine knowledge, theory, experience and judgement toward solving areal-world engineering problem---where you will be expected to translate an abstract problem statement into a non-abstract system design which would work at large scale. Areas you could brush up include distributed systems, designing a system under certain constraints, gathering requirements (e.g. time, cost, throughput, users), simplicity, limitations, robustness and tradeoffs. Another tip would be that estimating large numbers(https://ed.ted.com/lessons/michael-mitchell-a-clever-way-to-estimate-enormous-numbers) is extremely beneficial when thinking at Google scale. Consider brushing up on your powers of 2 and 10. Also commit to memory “Back-Of-The-Envelope Calculations” (http://highscalability.com/blog/2011/1/26/google-pro-tip-use-back-of-the-envelope-calculations-to-choo.html) that will help you assess system design trade-offs. Lastly, make sure you also have an understanding of how the internet works and be familiar with the various pieces (routers, domain name servers, load balancers, firewalls, etc.). For information on system design, check out our research(https://research.google/pubs/) on distributed systems and parallel computing.“Back-Of-The-Envelope Calculations” that will help you assess system design trade-offs. (Additional resources for System Design: “Scalable web architecture & distributed systems”,(http://www.aosabook.org/en/distsys.html) “How complex systems fail”(http://web.mit.edu/2.75/resources/random/How%20Complex%20Systems%20Fail.pdf), “Building software systems at Google and lessons learned”)(https://www.youtube.com/watch?v=modXC5IWTJI) * Networking (optional) - Show off your depth of knowledge and understanding of network theory, like different protocols (TCP/IP, UDP, ICMP, etc), MAC addresses, IP packets, DNS, OSI layers, and load balancing. Check out Computer Networking: A Top-Down Approach(https://docs.google.com/file/d/0B_ZlPoR-N0ldM3Bzc0Fhc2trVW8/edit?resourcekey=0-mRhEr3eotyV_ii3RL4aSDA) From a medium post: https://fabrizio2210.medium.com/how-i-get-a-job-at-google-as-sre-83d44aef7859 >>>>> NALSD * http://www.aosabook.org/en/distsys.html * http://highscalability.com/numbers-everyone-should-know * https://danrl.com/blog/2019/path-to-srm-nalsd/ * https://danrl.com/blog/2018/srecon18asia-day-3/ * https://storage.googleapis.com/pub-tools-public-publication-data/pdf/9b0aa90de33d2a5f6a5575f71e772f74c0f4b945.pdf * http://highscalability.com/blog/2011/1/26/google-pro-tip-use-back-of-the-envelope-calculations-to-choo.html * https://landing.google.com/sre/workbook/chapters/non-abstract-design/ * https://www.youtube.com/watch?v=modXC5IWTJI * https://youtu.be/Gg318hR5JY0 Troubleshooting ... (39 lines left)
```