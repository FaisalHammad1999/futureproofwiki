What comes to your mind when you think of the word 'hacking'?

Being hacked is a real possibility for most businesses and for a small company, a successful attack could result in their business being completely wiped out. The brand repercussions of a hack are huge. The truth is that unfortunatly hacks resulting in data breaches are not uncommon. Check out [Have I Been Pwned](https://haveibeenpwned.com/)...

## Types of Hacker
Not all hacking comes from a bad place! There are different types of hacker with different motivations:
### Black Hat
A Black Hat hacker is hacking for malicious intent, potentially for money or for information.

### Grey Hat
A Grey Hat hacker does violate laws or ethics but without the intent of a black hat hacker. This might be as a political statement.

### White Hat
A White Hat hacker is the hacker to hire: they are hacking for the purpose of finding faults so they can be fixed. Penetration testers, who try to penetrate and hack a system, giving companies a view of security risks so they can be fixed before hackers can get to them are a type of white-hat hacker. Sometimes we might also have people that complete white-hat hacker tasks and report them to organisations for the return of a bug bounty, which is a lump sum of money used to encourage those that find areas of weaknesses to report them, rather than attack them.

With the rise in Cybersecurity positions, Grey and Black Hat hackers have been popular recruits for White hat roles.

Consider [Alan Turing](https://en.wikipedia.org/wiki/Alan_Turing)... depending on whose side you were on, nowadays he might be considered either Grey or Black Hat. If he had been PenTesting the security of the UK government (with their permission), he would have been a White Hat. Either way, he is certainly one of the very first hackers!

***

## Attack Vectors
We can start to think through our vulnerabilities by considering attack vectors. These are the paths an attacker might take to do their work.


## Attacks on the User
A key area of vulnerability when building a website is the user itself. If we have a login to our website, which protects personal data often users themselves can leave themselves open to data loss:
- User leaves themselves logged in to a personal computer
- Reset password route is not secure e.g a hacker could Google the answer to our security questions
- User shares password/username with others. Either family/friends or when data is collected by hackers through illegal means e.g phishing

### Social Engineering
[Social engineering](https://en.wikipedia.org/wiki/Social_engineering_(security)) is a form of hacking that requires no actual coding but instead tricks the User into divulging information or giving access to secure locations of their own accord. There are numerous social engineering vectors to be worried about, yay, here are just a few:
- **Pretexting** is the start of it all. It is a trust building campaign in which the attacker lulls the victim into a state of trust. This could happen over an extended period in the run up to the information divulge itself.
- **Phishing** is where the victim receives an email impersonating a trusted person or company and requests their personal information. Smishing (via SMS) and Vishing (over voice call) are variations on this.
- **Baiting** is the reason you don't put strange external drives into your computer! An attacker may leave a device in a consipicuous place in the hope the victim will be curious to see the content. Perhaps you find a lost phone out of battery and in your good will you connect it to a USB port on your laptop to charge and send a message to the owner's family and friends. That's very kind of you but what if it actually contains malware that installs itself on your machine with access to all your private documents and connected devices eg. webcam...
- **Tailgating** is physically following someone through to a secured location. This may or may not require social engineering if the attacker is nimble enough but in most situations they will need to at least somehow convince the legitimate person as to why they just followed them in. In a large building where not everyone knows each other, door or elevator access might be gained with a "Oh no I forgot my card, what an idiot! Could you hold the door? Thanks!"
- **Quid Pro Quo** is an attack in which the victim feels they are the one receiving something. This may be help: "I see you have a problem! I can fix it, I just need your..." or a product eg. a free pen. Think we're exaggerating with the free pen example? [Think again](https://www.theregister.com/2003/04/18/office_workers_give_away_passwords/).

***

## Attacks on the Technology
Of course, our actual codebase could be attacked directly
- **Malware** could be spread around the network damaging our servers and taking down our service
- **Ransomware** - our servers could be taken over and hackers may request a large amount of money to unlock them
- **DDOS** (**D**istributed **D**enial **o**f **S**ervice) is where attackers flood a target with requests and overload it bringing it down

### Web specific attacks
Due to the web's connected nature, if we are not making decisions that protect ourselves when we code our website we could end up open to other attacks.
- **Cross Site Scripting Attack (XSS)** is where malicious code is uploaded to a website, this code is then automatically executed by the website and can lead to devastating consequences.

- **Network Attacks** - when we are sending data back and forth over the network it can possibly find itself in the wrong hands. The named techniques involved in network attacks include Man in the Middle and Eavesdropping.

- **SQL injection** - where we leave ourselves open for attackers to access our SQL database.

![SQL Injection](https://imgs.xkcd.com/comics/exploits_of_a_mom.png)

***

## Threat Modelling
In order to mitigate risks of attack we can go through a process called threat modelling.
1. Understand the system we are building
2. Research areas of weaknesses
3. Determine ways we can protect ourselves
4. Retrospectively consider the protection mechanisms we’ve put in place, determine if they did a good enough job or if we need to rethink
