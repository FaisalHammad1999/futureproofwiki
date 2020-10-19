[Docker](https://www.docker.com/) is a tool which allows us to **_containerise_** our apps. Whilst there are many different reasons Docker might be used in a development flow, for us we will primarily be using it to solve the perennial issue of _'but it works on my machine!'_

As we go on, we will learn more about Docker and its features but a great way to get started quickly is with the Visual Studio Code Remote - Containers extension.

## Setup
To get started we will need to install only 2 things:
- [Docker Desktop](https://www.docker.com/products/docker-desktop) (download [here](https://www.docker.com/products/docker-desktop))
- the Remote - Containers extension (in VS Code, click the 4 blocks icon on the left hand side, search for 'Remote - Containers' and click 'Install')

That's it!

## The Problem
In your terminal, clone down one of our repos - try [this one](https://github.com/getfutureproof/fp_study_notes_hello_docker). `cd` into the folder and open in VS Code with `code .`

Let's open a terminal (in VS Code or otherwise) and try to run this app. It is designed to run with the command `npm run hello`.

Oh no! If you do not have node installed locally on your machine, it is likely that you received a message such as `npm: command not found`

## The Solution
We could install node locally and that's not a bad idea but as our projects grow and we collaborate more and more, even two devs with the same version of node installed may have troubles running the same code base thanks to their file system, OS, personal settings and more.

Now we have Docker and the Remote - Containers extension installed, you will see two caret arrows facing each other in the bottom left of your VS Code window (a bit like this `><`). Click on that and select **'Reopen in Container'**. The first time you do this it may take a little time. VS Code will refresh and will start up a Dev Container. If you'd like to see it working, click on 'Starting Dev Container (show log)' in the bottom right popup.

Now when you open up a terminal in VS Code (<kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>`</kbd> on all OS), you will get a new prompt showing that you are in a workspace for this folder.

Try `npm run hello` again and you should be greeted and asked for your name in the console.

To exit your container, you can click on the `><` and select **'Reopen Locally'**. Alternatively, type `exit` in the workspace terminal and hit enter.

## Behind the Scenes
What happened when you clicked **'Reopen in Container'** was that the Remote - Containers extension looked for a folder called `.devcontainer` - have a look and you will see that this repo comes with one already. In there, it found all the information it needed to spin up a Docker container ready to run this app. We can make these as simple or as complicated as we like, including loads of different programs and interpreters that we may need for any given project. 

## How we use Docker at futureproof
For the first two LAPS we will only really need a basic Node installation. It is up to you if you would like to install and run Node directly on your machine as well but most of our repos and all of our assignments will come with predefined `.devcontainer`s to make sure we are concentrating on code, not mismatched environment issues! 

This approach is increasingly common in dev teams to speed up onboarding and streamline the whole development process. You can also deploy in a Docker container although development in a container does not mean that you have to deploy in a container.


## Create Your Own
If you encounter a repo with no `.devcontainer` or you would like to add one to your own project, you can create files from scratch (have a look at some of the .devcontainer settings in our repos for inspiration in conjunction with the [official documentation](https://code.visualstudio.com/docs/remote/containers#_create-a-devcontainerjson-file).

Another good option is to auto-generate these files and optionally customise them. Even without pre-existing devcontainer settings, you can still click the bottom left `><` in VS Code and select 'Reopen in Container'. You will be presented with a selection of preset configurations. A simple Node.js configuration will do us for now! Once you've selected your preset, a container will be spun up and the relevant files created for you. Feel free to go into the generated files and update them to your taste/needs. If you make updates, remember to click the `><` again and select **'Rebuild Container'**. If you encounter an issue rebuilding, 'Retry' usually does the trick (assuming your settings are valid).