DevOps is a portmanteau (two words smushed together) of Development and Operations. Let's look at them in their traditional separate forms first.

### Development
This covers the actual writing of a new feature, app, website etc. Job titles in this field may include 'Web Developer', 'Software Developer', 'Front-end Developer', 'Software Engineer'

### Operations
This covers the needs of getting code out to release, database admin, QA testing and more. Job titles in this field may include 'Operations Engineer', 'QA Tester', 'System Administrator', 'Security Engineer'

***

## DevOps
DevOps is not easy to define as it is as much a mindset as anything else. The idea to bring these two departments together aims to bring a smoother end-to-end experience for all people involved, including the end user (usually without their knowledge). Having a DevOps approach should mean less miscommunication between departments, less duplication of work, more efficient processes, quicker release schedules and in the end, happier clients.

In a DevOps approach, all of the roles mentioned above would have more insight and access to the work of the other department. There is also an increasing demand for the role of 'DevOps Engineer' which really does bridge the gap. Increasingly we are expected to understand more and more about the entire cycle since we have more opportunity to communicate with people in roles we may not have previously had much time with.

***

## DevOps cycle
Whilst difficult to define, there is a generally accepted DevOps 'cycle':

#### Plan
As it sounds, before any code is written, a plan is made! This can involve many people with insight about various relevant parts. Some sort of overarching plan or roadmap is crafted.

#### Code/Create
Coding with DevOps in mind can also mean making use of some tools that will smooth out the process such as good code hygiene, bearing security issues in mind, TDD approach etc.

#### Build
This is the part where we actually take our pieces of code and put it together in a package. In a DevOps mindset, this might be an automated action so the dev team can spot build errors early.

#### Test/Verify
This testing stage is beyond the tests done in the Code stage. As part of Continuous Integration, we want to know that when put together, our product works as expected when anything new is added.

#### Release
We're nearly ready to start sharing our product! This stage is where we do final checks to make sure everything is ready to go. It all works, but what should a user have access to? Do we include all our new features in this release or not? We can check everything in a 'staging' build.

#### Deploy/Configure
Now it's ready to be deployed! This should be using the same environment as test > staging so no nasty surprises await. This is also when we make sure our deployment hosting is working and users can access our product.

#### Operate
Your product is now out in the world and in use - but we need to make sure we are on top of things. Gathering feedback from users, making sure our servers can handle any new influx of traffic and generally keeping things ticking over, handling any server outages. This is a 24/7 job.

#### Monitor
This stage is similar to a retrospective. Continuous monitoring of feedback and usage can help flag current and potential issues. At this stage, we start feeding back up to the planning and the project team can start thinking about the next new update / feature to be coded!

***

![DevOps Cycle](https://d1.awsstatic.com/Marketplace/solutions-center/icons/AWS-MP-DevOps-Infographic-Light.9cc594ee04ab14e33066daff892a49e5329ed47e.png)

***

## Some key phrases and concepts
### CALMS
An increasingly popular model to measure the 'health' of a company's devops flow based on 5 key areas: \
**C**ollaboration, **A**utomation, **L**ean, **M**easurement and **S**haring

### Collaboration
It is tempting to get caught up in the technical side of DevOps but none of it will fit together without tight collaboration across all the teams. Collaboration and communication between all parties needs to run both ways, with all involved taking responsibility.

### Automation
"The technology by which a process or procedure is performed without human assistance.". Successful DevOps uses a *lot* of it! Consistency, reliability and speed are the key benefits.

### Infrastructure as Code (IaC)
The usage of code to define the automation of infrastructure, rather than relying on physical hardware configuration and/or manual configuration.

### Configuration Management
The automation of preparing and managing host-specific configuration for different environments eg. test, staging, production.

### Continuous Integration
The practice of frequent commits and merges to a version control system, which then triggers an automated build.

### Continuous Testing
The automation of test runs as an integrated part of the delivery flow. This can offer instant feedback, vastly reducing risks of releasing a product that does not work as expected.

### Continuous Delivery
The on-demand ability to deliver updates in a fast, safe, and sustainable manner. This relies heavily on the successful implementation of configuration management, continuous integration & continuous testing. This can take you from delivering updates once a month to updates once a day or even hour! The process is automated except the final push to production which may need manual approval.

### Continuous Deployment
An extension of Continuous Delivery which automates the entire process, including the final push to production. Feedback loops are even more important than ever when implementing continuous deployment.

### Continuous Monitoring
The implementation of various feedback loops to monitor across the entire life cycle to catch failures and required improvements.
