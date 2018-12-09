### **Github AVT Team Area**

- [AVT Team Repo](https://gecgithub01.walmart.com/login)
- [Git/Hub How To](https://guides.github.com/)
- [Github Markdown](https://guides.github.com/features/mastering-markdown/)
- [Environment Setup](#setup-environment)
  - Install Git
  - Github Desktop
- [Text Editors](#editors)
  - Atom
  - Visual Studio Code
- [Git Configuration](#git-settings)
  - Global Settings
  - Local Settings
- [Add SSH Key](#ssh-keys)
  - Generate Keys
  - Validate SSH
- [Github Repositories](#repositories)
  - Clone A Github Repository
  - Create A Local Repository
  - Add An Existing Repository To Github
  - List Remote Repository Url
- [Git Basic Commands](#git-commands)


#### Setup Environment
- Git Install
  - [Git](https://git-scm.com/download/win)

- Desktop
  - [Desktop](https://desktop.github.com/)

##### Editors
  - [Atom](https://atom.io/)
    - Enable **platformio-ide-terminal** package
  - [Visual Studio IDE](https://code.visualstudio.com/)

##### Git Settings
```
$ which git
$ git --version
$ git config --global user.email "you@example.com"
$ git config --global user.name "Your Name"
```

- Global
```
$ git config --list --global
$ cat ~/.gitconfig
```

- Local
```
$ git config --list --local
$ cat ~/path/to/repo/.git/config
```

##### SSH Keys
  - [Generate](https://help.github.com/articles/connecting-to-github-with-ssh/)
  - Validate
    ```
    ssh git@gecgithub01.walmart.com
    ```

#### Repositories
- Clone Repository
  - Login to github
    - Create Repository On Github
    - *<repo_name>*
  - Perform the following on the Command-Line
```
$ cd ~/path/to/dir
$ git clone git@gecgithub01.walmart.com:GDAP-AVTools/repo-name.git
$ cd repo-name
$ git log
```

- Create Local Repository
  - Login to github
    - Create Repository On Github
    - *<repo_name>*
  - Execute the following on the Command-line
```
$ cd ~/path/to/dir
$ echo "# Info" >> File-name
$ git init
$ git add README.md
$ git commit -m "first commit"
$ git remote add origin git@gecgithub01.walmart.com:GDAP-AVTools/repo_name.git
$ git push -u origin master
```

- Add Existing Repository
  - Login to github
    - Create Repository On Github
    - *<repo_name>*
    - Execute the following on the Command-line
```
$ cd ~/path/to/dir
$ git remote add origin git@gecgithub01.walmart.com:GDAP-AVTools/<repo-name.git
$ git remote -v
$ git push -u origin master
```

- Remote Repository Url
```
$ cd ~/path/to/repo
$ git ls-remote --get-url
```

##### Git Commands
```
$ git status
$ git checkout -b testing_branch
$ touch file-name
$ git status
$ git add file-name
$ git status
$ git commit -m "Added File Info"
$ git status
$ ls -al
$ git branch
$ git checkout master
$ git log
$ git status
```
