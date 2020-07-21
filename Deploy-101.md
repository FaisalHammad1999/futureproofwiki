### Netlify is a great hosting tool for many (not all) projects and it's super easy to get going with!

## Pre-requisites for a super-easy HTML/CSS/JS site deploy
- A project folder containing all the files needed to run your code. 
- An `index.html` file on the top level (if you run `ls` and see an `index.html`, you're in the right place!) 
- The `netlify-cli` tool. Install it with `npm install netlify-cli -g` (if you get npm errors, check our local env setup guides) 

# TL:DR
```bash
netlify init
netlify deploy
netlify deploy --prod
```

# Full Steps
- From your project folder run `netlify init`
- If you see `Do you want to create a Netlify site without a git repository?` and you're not sure about `git` yet, select `Yes, create and deploy site manually`.
- Select your team (usually you'll only have the one option here)
- Give your site a name. Bear in mind these are unique so you might have to get a bit creative.
- If the name is available you should get something similar to:
```bash
Site Created

Admin URL: https://app.netlify.com/sites/amazing-site-name
URL:       https://amazing-site-name.netlify.app
Site ID:   ce665ba5-21b3-4b40-a601-0554b9079c1a
"amazing-site-name" site was created
```
- For a basic HTML/CSS/JS site, no build stage is required so you can go ahead and run `netlify deploy`
- For a basic HTML/CSS/JS site, as long as you ran these commands from the same folder that contains your `index.html`, you can go ahead and just hit [enter]
- If all went well you will get a return that looks something like:
```bash
Deploy path: /my/project/path
Deploying to draft URL...
✔ Finished hashing ? files
✔ CDN requesting ? files
✔ Finished uploading ? assets
✔ Draft deploy is live!

Logs:              https://app.netlify.com/sites/amazing-site-name/deploys/5f05a1c3b760ea3cc4b3fcd3
Website Draft URL: https://5f05a1c3b760ea3cc4b3fcd3--amazing-site-name.netlify.app
```
- Visit the draft URL and make sure it looks as expected. Make any changes as necessary and run `netlify deploy` again, checking the draft URL.
Once it looks good in draft, ship it! \
`netlify deploy --prod`
- The steps are now the same as above but this time you'll get feedback like:
```bash
Deploy path: /my/project/path
Deploying to main site URL...
✔ Finished hashing ? files
✔ CDN requesting ? files
✔ Finished uploading ? assets
✔ Deploy is live!

Logs:              https://app.netlify.com/sites/amazing-site-name/deploys/5f05a2b3f57c0962fabd375c
Unique Deploy URL: https://5f05a2b3f57c0962fabd375c--amazing-site-name.netlify.app
Website URL:       https://amazing-site-name.netlify.app
```
### Visit your Website URL and enjoy!
