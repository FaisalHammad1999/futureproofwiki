Signing up for AWS is a [simple registration form](https://portal.aws.amazon.com/billing/signup#/start) but they do need to take your card details! Don't panic but do use it as a reminder to be aware of exactly what services you decide to use! Many of the services have a [Free Tier](https://aws.amazon.com/free/) allowance. Some of these are only valid for the first year after sign up but some are lifetime quotas eg. the Lambda service gives 1 million free requests per month before any charges are applied. If you wake up to an unexpected bill, contact their support team ASAP as they have a good reputation for waiving large bills that were clearly newbie mistakes.

Once you have signed up, why not start off on the right foot in terms of basic security and best practices. After initial sign up you will now have a 'root user' account. Whilst you can do all your activity in here, it is recommended to set up an 'IAM user'. IAM is one of the services and it stands for **I**dentity and **A**ccess **M**anagement. You can create as many IAM users as you desire and they can each have their own permissions. We could go a lot deeper into security and access management across AWS but this is a great start and if you would like to find out more, check out [this documentation](https://aws.amazon.com/iam/).

## Creating an IAM user
1. Log in to your root user account
2. In the top search bar, search and select `IAM` (you should now be at [the IAM console](https://console.aws.amazon.com/iam/home#/home)
3. From the sidebar, select `Users`
4. Click the `Add User` button
5. Follow the instructions creating a username and selecting access (select both for access both in-browser and from CLI)
6. On the Permission page, select what you want for this user. If it will be your primary account you may consider Administrator Access
7. For now you don't need to worry about groups or tags but it is good practice to use them. Essentially with groups you can assign the same permission sets to everyone in a given group. Tags are simply human-readable data to store for your info.
8. Once you've gone through all the pages and clicked `Create User`, copy/download the secret access key, password and console login link. Optionally you may also send the login instructions via email.

## Logging in as an IAM user in the browser
The browser interface offers a great graphical interaction with most of the services. It is a great place to start and you can certainly achieve everything you'll initially want within the browser. Initially it can be intimidating but essentially each **AWS*ervice has it's own 'console' (landing page). They also each have excellent [documentation](https://docs.aws.amazon.com/).
1. Visit the console login link you received when creating your IAM user (consider bookmarking this)
2. Login with your details
3. Use as usual!

## Logging in as an IAM user in the [AWS CLI](https://aws.amazon.com/cli/)
This is optional but you may well want to start using this earlier rather than later as there are a few actions that can only be taken from the CLI *(although it is unlikely that you will require these too soon)*
1. Download and install the latest version](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) (v2 at time of writing) in your preferred way
2. Open a terminal shell and run `aws` - if it is installed, you will see some help options
3. [Configure](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) by running `aws configure`
    - The Access Key ID and Secret Access Key were given when you created the IAM user
    - When giving your default region, the format is as seen on the region dropdown in the browser console eg. `eu-west-2` for London
    - Output format is up to you, my personal preference is `json` or `table` but you can change this at any time
4. Test to see if you are successfully logged in by trying out a service!
    - `aws translate translate-text --text "Hello friends\!" --source-language-code=en --target-language=sr`
    - this should give you back the Serbian translation of "Hello friends!"


You can do even more from the CLI than you can in the browser console so we won't go into it here but as a rough guide, you can start an aws command with:
- `aws <service-name> <action> <options>` eg. `aws ec2 describe-instances --region eu-west-2 --output table`
- add `help` after any command to see what you can do!

***

### Resources
[AWS Signup](https://portal.aws.amazon.com/billing/signup#/start) \
[AWS Free Tier allowances](https://aws.amazon.com/free/) \
[AWS IAM Documentation](https://docs.aws.amazon.com/iam/index.html) \
[AWS CLI Documentation](https://docs.aws.amazon.com/cli/index.html)