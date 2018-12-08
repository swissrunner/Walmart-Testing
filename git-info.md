### AVT Team Area

- [AVT Team Repo](https://gecgithub01.walmart.com/login)
- [Git/Hub How To](https://guides.github.com/)
- [Github Markdown](https://guides.github.com/features/mastering-markdown/)
- [Environment Setup](#setup-environment)
  - [Install Git](#git)
  - [Github Desktop](#github-desktop)
  - [Atom Text Editor](#atom-text-editor)
  - [Visual Studio Code](#visual-studio-code)
  - [Git Configuration](#configure_github)
    - [Global Settings](#global-settings)
    - [Local Settings](#local-settings)
  - [Add SSH Key](#add-ssh-key)
    - [Generate Keys](#generate-keys)
    - [Validate SSH](#validate-ssh)
- [Repositories](#repositories)
  - [Clone Github Repo](#clone-github-reppsitory)
  - [Create A Local Repository](#create-a-local-repository)
  - [Add Existing Repository To Github](#add-existing-repository-to-github)
- [Basic Commands](#basic-git-commands)
- Initialize local repo


#### Setup Environment
- Install Git
  - [Git](https://git-scm.com/download/win)

- Github Desktop
  - [Desktop](https://desktop.github.com/)

- Atom Text Editor
  - [Text Editor](https://atom.io/)
    - Enable platformio-ide-terminal package

- Visual Studio Code
  - [Visual Studio IDE](https://code.visualstudio.com/)

- Git Configurations
```
$ which git
$ git --version
$ git config --global user.email "you@example.com"
$ git config --global user.name "Your Name"
```

- Global Settings
```
$ git config --list --global
$ cat ~/.gitconfig
```

- Local Settings
```
$ git config --list --local
$ cat ~/path/to/repo/.git/config
```

- Add SSH Keys
  - [Generate Keys](https://help.github.com/articles/connecting-to-github-with-ssh/)
  - Validate ssh
    ```
    ssh git@gecgithub01.walmart.com
    ```

#### Repositories
- Clone A Github Repository
  - Login to github
    - Create Repository On Github
    - repo_name
  - Perform the following on the Command-Line
```
$ cd ~/path/to/dir
$ git clone git@gecgithub01.walmart.com:GDAP-AVTools/test_2.git
$ cd test_2.git
$ git log
```

- Create A Local Repository
  - Login to github
    - Create Repository On Github
    - repo_name
  - Execute the following on the Command-line
```
$ cd ~/path/to/dir
$ echo "# test_2" >> README.md
$ git init
$ git add README.md
$ git commit -m "first commit"
$ git remote add origin git@gecgithub01.walmart.com:GDAP-AVTools/test_2.git
$ git push -u origin master
```

- Add Existing Repository To Github
  - Login to github
    - Create Repository On Github
    - repo_name
    - Execute the following on the Command-line
```
$ cd ~/path/to/dir
$ git remote add origin git@gecgithub01.walmart.com:GDAP-AVTools/<repo-name.git
$ git remote -v
$ git push -u origin master
```

- List Remote Repo Url
```
$ cd ~/path/to/repo
$ git ls-remote --get-url
```

##### Git Commands
```
$ git status
$ git checkout -b testing_branch
$ touch test_1.txt
$ git status
$ git add test_1.txt
$ git status
$ git commit -m "Added File Info"
$ git status
$ ls -al
$ git branch
$ git checkout master
$ git log
$ git status
```
