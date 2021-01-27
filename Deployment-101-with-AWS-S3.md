AWS is a bit of a buzz-acronym in modern web development. **A**mazon **W**eb **S**ervices offers an extensive range of resources and services over the internet. This is great news for anyone, whether an individual student, or a massive enterprise (at time of writing, AWS customers include Netflix, Twitch, LinkedIn and many more big names), as with a service such as AWS, we can 'rent' hardware as well as take advantage of managed services. The best part is that we can pick-and-mix the exact hardware and services that we require and pay only for what we need.

If you've ever built your own computer, you'll know the feeling of agonising over exactly what spec you need. Imagine doing that at a business-critical level. Now imagine if you could try something out and change it out with no hassle as soon as you realise you need more or less than initially anticipated. Fantastic!

At time of writing there are reportedly over 175 services available on AWS. They all serve a specific purpose and the one we will look at here is [**AWS S3**](https://aws.amazon.com/s3/).

## What is S3?
S3 is a storage service. Up until now, it is likely that most of your storage options have been file-based. S3 is different in that it is object-based. More on that in a moment.

### S3 Buckets
What do we put in buckets? Usually a collection of things. Maybe you have a bucket that you fill with grains of sand, perhaps your bucket is full of flowers. In S3 we can fill our buckets with objects. You can have multiple buckets that each contain multiple related items. Perhaps I have a bucket called 'my-portfolio' which contains all the data I need for my portfolio website. I may have another bucket called 'my-best-cat-edits' to which I regularly upload only my best edited images of my cats.

### S3 Objects
Anything that exists within a bucket is an object. For user-friendliness, on the AWS GUI it may appear to be eg. a file within a folder but in reality, every single item uploaded to S3 has an individual ID that is related to its bucket but no further. Each object has an address at which, if we give the public access, anyone can access. It also has an S3 address at which we can access it within AWS.

### S3 Pricing
When using any AWS service it is important to bear in mind costing. AWS Free Tier is very generous in many ways with various offers across the different services. For S3, in your first year, you get 5GB of storage free every month. After that, S3 pricing is inexpensive, especially for static, proof-of-concept sites such as we may make whilst expanding a portfolio.

### S3 Usages
You can use S3 to store nigh on anything you like. The storage per bucket is 'unlimited' - 8 exabytes - good luck filling that! Whether it's images, text, audio files, S3 doesn't care, it just stores it as a new object. In this walkthrough we will use its feature of static site hosting which allows us to define an index page, relate to other content within the bucket and give an elegant(ish) url with which to access it.

## Hosting a Static Site with S3
1. Create files
- Given S3s agnostic approach to file types, we can easily upload our entire project folder and it will handle the rest, so go ahead and create a project as normal! If you like, you can use [this sample code](https://github.com/getfutureproof/fp_demo_aws_s3_static_hosting) as an example.
- ***NB**: If you are using a bundler eg. webpack, ensure that you build the project eg. `yarn build` / `npm build` and upload only the build/dist folder.*

2. Upload files to S3 and configure for static site hosting
- If you've not already got an AWS account and an IAM user, then **[follow this guide](https://github.com/getfutureproof/fp_guides_wiki/wiki/Basic-AWS-Setup) to get set up and then come back here**.
You can do the next steps either from the in-browser AWS Management Console or using the AWS CLI.

**NB: These instructions look long as they have explanations and detail but once you've done it once, it is a very quick process, so give it a go and get started with this extremely popular platform that clients love to hear that you have experience with!!**

### Option 1: Management Console
- Select `S3` from the search bar or services menu
- Create bucket
    + Click `Create Bucket` on the top right
    + Name your bucket. _NB: this must be completely unique - you cannot share a name with any other bucket in the world!_
    + Choose your region. _NB: you can choose any but you may consider choosing the location physically closest to you or your main user base. If you don't care about latency, the cheapest regions (once you are past free tier quota) tend to be North Virginia, Ohio and Ireland)_
    + Grant public access by unchecking 'Block _all_ public access' and checking the acknowledgement. _NB: this should only be done when you explicitly wish the public to access these files. In this case we do but bear in mind that in other contexts, this would not be desirable.on
    + Versioning, tags, encryption and Advanced Settings can be left as default for now, although Versioning is well worth looking into and enabling!
    + Click 'Create Bucket'
    + On next page, click on the name of your new bucket
- Add bucket policy to allow access to all objects
    + Go to your bucket management page
    + Click on `Permissions` tab
    + Scroll to `Bucket policy` and click `Edit`
    + Copy the given Bucket ARN (**A**mazon **R**esource **N**ame) and click `Policy generator` (should open in new tab)
        - Policy Type: S3
        - Effect: Allow
        - Principal: *
        - Actions: GetObject
        - ARN: `<paste in ARN>`
        - Click `Add Statement`
        - Click `Generate Policy`, copy the given JSON and click `Close`
    + In original tab, paste in the copied JSON to the Policy input
    + In the "Resource" key value pair, add "/*" to the value eg. `"Resource": "arn:aws:s3:::s3-static-demo-futureproof"` becomes `"Resource": "arn:aws:s3:::s3-static-demo-futureproof/*"`
    + Click `Save Changes`
- Add objects
    + Click 'Upload'
    + Click 'Add files' and drag or select files and folders from within project folder
    + Check preview of object names and click 'Upload'
- Enable static site hosting
    + Go to your bucket management page
    + Click on `Properties` tab
    + Scroll to bottom and click `Edit` on `Static website hosting`
    + Click `Enable`
    + At a minimum, add the path to your index.html and an error.html page
    + Click `Save Changes`
    + Visit the link now given under `Static website hosting`

--- 

### Option 2: AWS CLI
- At any point, you can see all existing S3 buckets with:
    + `aws s3api list-buckets`
- Create a new bucket
    + In console run the following, updating to use your own bucket name:
    + `aws s3api create-bucket --bucket my-awsome-bucket-name --region eu-west-1  --create-bucket-configuration LocationConstraint=eu-west-1`
    + Adjust region as you prefer - I've gone for the generally cheapest EU location for balance of cost and distance
- Add bucket policy to allow access to all objects
    + create file eg. policy.json
    + in your new file, paste the following and update to use your own bucket name
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::my-awsome-bucket-name/*"
        }
    ]
}
```
   + In console run the following, updating to use your own bucket name and policy file name:
   + `aws s3api put-bucket-policy --bucket my-awsome-bucket-name --policy file://my-policy-file.json`
   + You can optionally delete the policy file at this point
- Add objects
    + From within project folder, in console, paste the following and update to use your own bucket name:
    + `aws s3 sync ./ s3://my-awsome-bucket-name`
    + _NB: Just run this again when you want to update your files!_
- Enable static site hosting
    + In console, paste the following and update to use your own bucket name and, if needed, index & error pages:
    + aws s3 website s3://my-awsome-bucket-name/ --index-document index.html --error-document error.html
- Profit!
    + Visit your new site at `<bucket-name>.s3-website.<AWS-region>.amazonaws.com` eg. `s3-static-demo-futureproof-cli.s3-website.eu-west-1.amazonaws.com`

---

**As mentioned above, these instructions seem long but it is really just a few short steps so give it a go and get started with this extremely popular platform that clients love to hear that you have experience with!**