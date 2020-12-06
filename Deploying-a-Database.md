Deploying a database can seem intimidating at first, partly because there are many options and partly because many of those options take your credit card details! Here are a few recommendations to investigate...

### [Heroku](https://www.heroku.com/)
As we've seen before, Heroku is a good option for freebie server hosting. They offer a [Postgres add-on](https://www.heroku.com/postgres).

### AWS
The cheapest way to handle a small database would be to **add one manually to your EC2 instance**. For small projects this should serve you just fine as proof of concept with no additional costs over your EC2 billing. If your home computer can handle it at the same time as other activity without panicking, so can your EC2 instance (check your AMI specs of course!). You could even use a docker-compose configuration quite easily without making changes. You may want to strongly consider adding validations in your code to check your database size regularly and delete old data to avoid hitting your storage limits.

When working at a company however there is a good chance that you'll be working with a managed database service. At time of writing, AWS has 15 managed database options covering 9 types of database! A few of the most popular are:

**[Amazon RDS](https://aws.amazon.com/rds/)**
offers 6 services that can be used to manage a variety of relational databases [including PostgreSQL](https://aws.amazon.com/rds/postgresql/) \
**[DynamoDB](https://aws.amazon.com/dynamodb/)**
is AWS' serverless NoSQL offering. [This video walkthrough](https://resources.awscloud.com/aws-builders-online-series/aws-amplify-donnie-prakoso?trk=em_builder_series20q3_od&trkcampaign=builders-online-series) is an excellent starting point for connecting to a DynamoDB using Amplify.

### [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
Once you've made a MongoDB Atlas cluster, you can [connect to it from nigh on anywhere](https://docs.atlas.mongodb.com/tutorial/connect-to-your-cluster/).

### [FaunaDB](https://fauna.com/)
FaunaDB is a very user friendly, serverless, collection-document database service. Fauna uses it's own query language, [FQL](https://docs.fauna.com/fauna/current/start/fql_for_sql_users.html). For a walkthrough on adding it to a Netlify-deployed app, [give this a go](https://medium.com/@bethmschofield/adding-faunadb-to-a-netlify-deployed-react-app-47753d6de1c9). Netlify Functions actually harnesses the same [AWS Lambda](https://aws.amazon.com/lambda/) service as Amplify just with some limitations.

### [Firebase Realtime Database](https://firebase.google.com/)
Google's Firebase has a popular and interesting real time database offering. For more info, see their own [introduction](https://firebase.google.com/docs/database).

***

### A note on AWS
AWS have lots of free training material. If you're not sure where to start I recommend [this quickstart overview](https://resources.awscloud.com/aws-builders-online-series/how-to-deploy-your-first-web-application-in-minutes-level-200?trk=em_builder_series20q3_od&trkcampaign=builders-online-series). If you are interested in more AWS training, their [Foundations course](https://www.aws.training/Details/Curriculum?id=27076) is free and a good primer for their huge array of other courses including [Database Offerings](https://www.aws.training/Details/Curriculum?id=38111).

The [Free Tier](https://aws.amazon.com/free/?all-free-tier&all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Categories=categories%23databases) can get you quite a long way so make sure to weigh up the free tier offerings when considering your options.

They have various options for deploying full stack web applications. The most appropriate to consider at this stage are:
#### [Lightsail](https://aws.amazon.com/lightsail/)
Amazon's lightest weight Virtual Private Server option is ideal for smaller scale applications. You may need to draw on other AWS services to complete your setup. For a PostgreSQL setup, check out their [documentation on connecting to a PostgreSQL database](https://lightsail.aws.amazon.com/ls/docs/en_us/articles/amazon-lightsail-connecting-to-your-postgres-database). There are costs involved but they are low and predictable. [AWS Budgets](https://aws.amazon.com/aws-cost-management/aws-budgets/) can help plan and ease your mind in that respect.

#### [Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/)
eb is a one-stop-shop that makes connecting to other AWS services smooth and contained. [Come this way](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.managing.db.html) for a guide on adding a relational database to your EB setup.

***

## Test vs Dev vs Production
It is highly likely that your development database will not be the same as your production one eg. using a local database just as we have used so far for dev. Your test environment is also likely to use a different database. Just make sure you are connecting to the right one in the right situation - meaning setting your connection string / credentials as appropriate. [Environment variables](https://www.npmjs.com/package/dotenv) are the standard way to handle this.