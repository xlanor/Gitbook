# Bytedance SRE Interview (Offer)

I recently finished my interview loops at Bytedance Singapore for an SRE position and received an offer. I noticed that there's not much information avaliable, especially for SRE positions as compared to SWE. This role in particular is more of an SWE-SRE position and not an SRE-SE position.

In general, there's very little content online regarding SRE specific interviews.&#x20;

So far, in my experience (**this is for SRE interviews in general**, **not bytedance)**:

* You will be asked no harder than a leetcode medium. The questions almost always revolve around arrays, strings, sorting. This is most relevant for an SRE.
* I have been asked BFS/DFS once. I have also been asked dynamic programming once (what the fuck) but this was a round that was conducted by a SWE and not the SRE team.
* I also have been given an OA to write bash with no reference (booking.com). This was entirely unexpected and I failed the OA. I can write bash just fine, but I usually need to lookup syntax.
* I dont particularly enjoy those kind of OAs because what you can script in bash you can script in python in half the time. So I was kind of glad I failed that OA. It took place on a remote server that was automatically spun up... with 250ms latency. Can you imagine typing in vim with a 250ms latency?&#x20;
* You need to know your linux fundamentals. I was asked about proc tree, strace, top, as well as a bunch of other common linux interviewing questions. Some places got really deep into linux, whereas others were more platform-style and asked Kubernetes specific questions (thank you for looking at my background!). At yelp in particular, I had such a good time with an interviewer that we overshot the time allocated by 15 minutes, because we were discussing the state of SRE/DevOps in general from the time that I started working (and that my interviewer joined yelp) approximately 5 years ago, and now.&#x20;
* Every single place asked about inodes in some form or another.
* You also need to know networking. In over half of my interviews, I was asked to explain DNS, 3-way handshake, BGP, ARP, DHCP, just to name a few.
* Almost every interview had a troubleshooting round. It's usually something very generic and the same question really can vary depending on what the interviewer has in mind.
  * Troubleshoot an application with intermittent timeout\
    (For networking questions, my go-to answer is to work my way through the OSI layer, and also cover system issues that may cause it. Intermittent timeout could go down either path, your interviewer may want you to go down both, lean more towards the system side, or the networking side, so I usually start with networking - mentioning that this could be a system side issue too, but for the sake of structure I will first look at it from a networking perspective)
  * Troubleshoot an application that is not responding
    * (I usually start with doing a brief runthrough for network debugging)
  * When looking at graphs for an application, you notice a spike on a fixed interval (say, 9pm everyday). What are the possible causes and how would you investigate it?
* The above are all generic questions that can be found online, but the answers that I gave in each interview varied greatly. Make sure to look at your interviewer's face. Verbal Cues are super important as to whether you are currently on the right track or not. These are usually very generic questions that could go in many different directions, so at every stage, list out the possibe causes, explain why they could be possible causes, and prompt your interviewer as to which path he would like you to explore further.
* I cannot emphasis the above enough. Troubleshooting interviews and system design interviews are a bi-directional street. You need to communicate with your interviewer to make sure you're on the right track.
* The same is also true for coding interviews, but its less impactful than it is in troubleshooting/sysdesign
* You need to demonstrate passion and interest in the sector. Seriously, it makes the interview process so much better than just a bunch of rehearsed questions and answers for both the interviewer and the candidate. I've traded stories with interviewers about the horrors of shipping old servers from taobao for our homelabs (How I ended up with dead RAM after over a month of waiting on shipping  for some no-name DDR3 ECC sticks from there). I've also had a ton of fun discussing the various kind of filesystems and their advantages/disadvantages during the introduction in the coding round, to the point where the interviewer almost forgot to give me a coding question and I got away with a leetcode super easy (valid palindrome).
* Some places want more bare-metal knowledge (I was ever asked to design a on-premise database setup, so including hardware, things like UPS, splitting the nodes across racks, then moving on to speccing the servers, what kind of FS, CPU/RAM/Disk types and speeds, etc, before moving on to the virtualization layer, talking about things like PXE Boot for easy remote setup , using Proxmox as a hypervisor, and preparing a base image in Packer, before using terraform to provision the image and packer to deploy the vm, and subsequently kubespray for preparing a kubernetes cluster, then installing the database). Be sure to talk about tradeoffs in speed here, since for a database access speed is pretty much everything, as well as redundancy (you dont want to lose your data, do you?)
* Other places want more platform-based knowledge (assume that everything is in AWS/cloud-native, talk about the provisioning steps and which part can be automated using what tool, design the entire system, including things like deployment (watchtower/argo), etc etc.
* My advice will be to do more research on the firm as well as the role you're applying to. It's a pity they don't put this in the JD up front for most of them, because a ton of SREs I know today have more platform-based knowledge (most roles in Singapore are cloud-native and not many places run on-prem anymore outside of the big firms). My on-prem knowledge was mainly gained by dabbling in my homelab, so my answers may not be spot on.
* System Design is probably going to be the **most** important for your interviews. As an SRE you need to know how to design systems. Some places will give you entirely custom questions specific to their business domain. Others will use your cookie cutter SD questions.
  * Irregardless, even if you've received the same question before, **ALWAYS** communicate with your interviewer. When I've received the same question before, usually the interviewer wanted me to place priority on different parts of the system and go into a deeper dive than High-Level Design at that point.
* Last but not least, don't bother applying for an SRE position fresh out of school. In every single interview I've attended, it's usually mentioned at some point that they are looking for people with background experience in SWE/SE positions. If you manage to slip through the recruiter and land an OA, you may be subject to a cooldown period if you fail the phone screen/onsites.

The above is mostly true for either:

* tech firms
* Overseas interviews

Singapore specific interviews at firms who do not deal with tech are likely to be much easier than the above.

Ok, so on to the Bytedance experience itself.&#x20;

I interviewed at two orgs in bytedance: Infra Org, ML Org.

I initially interviewed at Infra Org in November. I don't recall the first round question for coding, but it was a strings-based question. During this round, there's about 20 minutes devoted to your background experience, and you can expect a deep dive here. It's going to be very specific to your background, so don't fluff up anything you don't know.

My background is primarily working with kubernetes, so I was asked alot of kubernetes specific questions. I dont really recall them, but I was asked alot about the service mesh layer (in my case, I picked and defended istio against its competitors like linkerd, although to be fair it would be fairly easy to do the same for linkerd against istio). I was also asked about security concepts here, so things like mTLS to secure pod-to-pod communication came into play, as well as secret management with Vault.

My second round, I was asked about my entire current infra setup at work. I did not enjoy this round, at all.

First of all, There were some glaringly obvious flaws in the current infra setup that was technical debt that I inherited and that we could not replace because we are currently overwhelmed with projects. I was not given a chance to explain this.

Secondly, the entire interview was conducted in Mandarin. I'm comfortable with speaking and listening to mandarin, but I'm really not used to using it for work (there are many technical terms that I'm not familiar with). Having specifically requested for an English Speaking interviewer with HR and told that it would be granted, I was not prepared for this at all.

I was not asked a coding question in this round. The interviewer probably decided during the initial phase that I was not a good fit, and the feedback from the recruiter was that I did not have the sufficient background to debug the infrastructure setup at bytedance.

Less than a week later, the ML Org reached out to me immediately.&#x20;

The recruiter in this case (thank you, fangzhou!) told me that most people did not even make it past the first round for the Infra org, and that as such, the ML Org was more than happy to conduct an immediate reinterview without a cooldown, as they are looking for a different set of skills from infra org. I happily accepted the reinterview offer and was slotted in for an interview the following week.

For the first round, it went pretty much the same as the previous round. I won't specifically mention the coding questions that was asked, because I don't believe that they reuse the same questions. (My second round interviewer actually verbalised it in hmm let me find a question in the question bank...). What I think actually goes on is that every interviewer probably has a set of preferred questions that they know like the back of their hand, which helps them in their evaluation process.&#x20;

However, safe to say: I think you are expected to come up with the most Optimised solution. I managed to do that for this round, as well as the next.

The next round, in addition to coding, also had a bunch of linux trivia questions and minor troubleshooting questions. This was probably the longest interview and I managed to squeeze in 7 questions in 45 minutes! I think that for this round, you will probably get as many questions as they can fit into the timeframe. I had a ton of fun with those questions. It was a fun mix of troubleshooting, leetcode, shell scripting, and log parsing. (you see this, what tool will you use, how will you use it, a few simple bash scripting/log parsing questions,  a leetcode question (any language), and some other questions that I cant remember. Almost every single one was testing technical skills, and I was allowed to use a linux server that I had on hand to run the scripts that I wrote, since lark doesn't run bash).

I would like to thank the interviewer at this stage for not insisting that you cannot refer for bash. You were allowed to refer to man pages for this. I think that while bash is still fairly important for system portability, for speed and ease we use python most of the time at work for scripting since our servers come pre-configured with python anyway. This was probably just to gauge at least some basic knowledge of bash.

I had a linux environment on standby and was allowed to share my screen to execute the commands and discuss accordingly.

The third round, was a HM round. This is supposed to be an SD round, but it differed very heavily from traditional SD rounds. We spent almost 40 minutes talking specifically about sharding and designing a custom partitioning algorithm that was related to the business domain that the team was currently involved in. (No, the answer is not consistent hashing. LOL. It's a Discrete Math answer).

The HR call is a standard behavioural call. I didn't prep for this, because the kind of questions that I got, I've gotten at other firms too. I was also asked for my salary at this point.

Less than 30 minutes after the call, I received a message that the HR team was ready to move ahead with my offer and to submit my payslips for the last six month.





