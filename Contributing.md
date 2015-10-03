## Contributing to LLILUM
While the LLILUM project is sponsored by the [.NET Foundation](http://www.dotnetfoundation.org/) it is an Open Source project and therefore _**we encourage and welcome contributions from the development community**_. As with any Open Source project - in order to avoid complete chaos and mass fragmentation, there are a few rules to follow.

### Pre-requisites for Contributing
1. Sign a [Contribution License Agreement (CLA)](Contribution License Agreement) (One time activity)
2. Fork the repository into your own GitHub account  
Forking the repository is a simple click on the "Fork" button on the repositories page in GitHub.
3. Clone your Forked GitHub repository to your local development machine  
`git clone https://github.com/<your GitHub ID>/llilum.git`
4. In your local repository, configure a remote to "upstream" NETMF repository  
`git remote add upstream https://github.com/NETMF/llilum.git`

### Basic Workflow for Contributions
1. Update your local dev branch with dev branch of "upstream" NETMF repository   
`git checkout dev`  
`git pull upstream dev`
2. Create a topic branch for your changes in your local repository  
`git checkout -b <topic name>`
2. Make your changes and commit them to your local topic branch
3. Ensure that the your changes can be merged into "upstream" NETMF repository without any conflicts  
`git checkout dev`  
`git pull upstream dev`  
`git checkout <topic name>`  
`git rebase dev`  
4. Push your local topic branch to your GitHub repository  
`git push origin <topic name>`
5. Create a Pull Request from the topic branch of your GitHub repository to dev branch of "upstream" NETMF repository  
This can be easily done through the "Pull Request" tab of your GitHub repository page
6. Respond to review comments on your changes  
This may entail additional changes to your code with an update to the Pull Request in order to fix issues, meet guidelines, etc.
7. Core team commits your changes to the "upstream" NETMF repository.  
Once the Pull Request is approved, a core member of the team will apply the changes to the NETMF repository so they are available for everyone to include. 
