# Branching and working with remotes

In the Introduction article, we looked at how to set up a local git repository, how to configure git and create aliases, and how to add and commit files on the example of creating an encyclopedia with articles and a list of topics to write about.

Now we will look at how to save your work remotely so that you won't only have access to it locally on your machine but also on other machines and you can work with collaborators. Your encyclopedia is going places and at any rate, you wouldn't want to do it all by yourself. It's time to give this thing called balance a go and let other people join in on your project.

## Working with remotes

All you have for now is your changes on your own machine. Anything that you commit will not be lost as long as your machine is working, but you can't share it with anyone else easily nor will it be safe if your device decides to stop working.

Firstly, you need to settle on a place to host your repository. This could be github or gitlab, for example.
After creating an account, you can create your repository.
On how to set up a repository, please see these articles in their respective documentation: [Github](https://docs.github.com/en/get-started/quickstart/create-a-repo), [Gitlab](https://docs.gitlab.com/ee/user/project/repository/)

After I created a new repository on github, it already showed me some instructions on how to push an existing git repository:

![](github-push-to-remote.png)

The first line is adding a so-called `origin` to our local repository. This is referencing our new repo on github. In this example you can see that I have chosen to use SSH but this can be switched with a button further on top if you want to use HTTP. Using SSH will require you to set up a private and public SSH key pair whereas you will only have to authenticate with your username and password or access key if you use HTTP, so it will be easier if you have never done this before.
Since I had previously connected to github on my machine, I was not prompted for my login credentials here. If you are, enter the credentials you are prompted for and then you are able to push to your remote repository.

![Pushing to a remote repository](pushing-to-remote.png)

After pushing, you can now see your changes on the site you have chosen as a host after refreshing in the browser. It will also show the last commit message, "Add a house cat" in this example, and your files.

![](github-remote-repository-overview.png)