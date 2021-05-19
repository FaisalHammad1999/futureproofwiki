For a deployed MongoDB solution, [Atlas](https://www.mongodb.com/cloud/atlas) provides a free tier which is perfect for demo and small app usage.

### Sign up to MongoDB Atlas
- [Sign up](https://www.mongodb.com/cloud/atlas) for free (no credit card required), verify email
- Name your org and a project (call them anything you like but make note!)
- Select the free Shared Clusters option
- Choose your provider & region - you can choose any but for region consider your proximity to users (consider researching carbon neutraility too! Save the planet!)
- Stick with free defaults for provisions
- Name your cluster (note this is not the name of your database, you can have multiple databases within a cluster)


**From this point, Atlas provide a thorough `Follow the Get Started` walkthrough. Each step is detailed below with some tips.**

---

### Create a Database User
This is important! You won't be able to connect to your database without it!
- On the left hand side under `SECURITY`, click `Database Access`
- Click `Add New Database User` button
- Choose an auth method - we'll go with Password for this walkthrough
- Enter a username and a password (recommended to use the Autogenerate Secure Password) and make note of them, you'll need them soon!
- For user privileges, go for Read and write to any database (you can set up an Atlas admin if you like but for your app to access, read and write is sufficient)
- Keep restrictions and temporary user settings switched off
- Click `Add User`
  
---

### Add IP Address to your Access List
This is also important! You won't be able to connect to your database without it!
- On the left hand side under `SECURITY`, click `Network Access`
- For a quick start, click `ALLOW ACCESS FROM ANYWHERE` - later on you can get more specific and add eg. just your host server IP / your dev machine IP
- Click `Confirm` 
  
---

### Load Sample Data (Optional)
This optional sample data set can be useful for testing out your new cluster
- On the left hand side under `DATA STORAGE` click `Clusters`
- In the sandbox, click the `...` button in the info box
- Click `Load Sample Dataset` and again in the modal
- You'll see the cluster updating with the message 'Loading your sample dataset...'

---

### Connect to your cluster
- On the left hand side under `DATA STORAGE` click `Clusters`
- Click the `CONNECT` button
- There are 3 options here - start with `Connect with the mongo shell` to make sure you can connect remotely without worrying about your driver setup

**Connect from command line**
- If you have the mongo shell installed on your machine you don't need to do anything else
- If you don't have the mongo shell installed and don't want to install it, use a docker container eg.:
  - :arrow_right: `docker run -it --name <new-container-name> -p 27017:27017 mongo bash` (the port map is essential)
- To connect, copy the provided link and switch out the MyFirstDatabase part with what you would like to call your database
  - :arrow_right: `mongo "mongodb+srv://<cluster-name>.fra7q.mongodb.net/<db-name>" --username <username>`
- You may get a startup warning if connecting from a non-admin account but it's okay!
- Once your prompt changes to something like `MongoDB Enterprise atlas-13bcv8-shard-0:PRIMARY>`, you're in!

**Connect from your own app**
Once you've confirmed you have access, try connecting from your own application.
- Whereever your connection string belongs (check your driver docs for this!) use the following format:
    - :arrow_right: `mongodb+srv://<username>:<password>@futureproof.fra7q.mongodb.net/<db-name>?retryWrites=true&w=majority`