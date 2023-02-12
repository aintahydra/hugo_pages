---
title: "Create Github Pages With Hugo"
date: 2023-02-13T05:37:53+09:00
draft: false
tag: hugo, github.io
---

# Create a static github page with Hugo

## Install Hugo on Ubuntu 22.04
```
apt install hugo
```

---
## Create github repositories

Login to github.com; Create two repositories: 
- [PAGE_SRC_REPO] contains sources; a regular github repo.
- [GITHUB_IO_REPO] contains rendered pages
    - Tts name should be`_USER_NAME_.github.io`, and needs to be `public`
    - Move to the newly created repository -> 'Settings' -> 'Pages', then set
        - Source: Deploy from a branch
        - Branch: master / root


---
## Create a hugo site
```
mkdir ~/webpages; cd ~/webpages
hugo new site hugo_pages
```

---
## Install a theme (as a submodule)
Visit https://themes.gohugo.io/ and pick a theme. Normally, theme authors provide how-to-install.  

```
cd ~/webpages/hugo_pages
git init
git submodule add https://github.com/_THEME_REPO_ themes/_THEME_NAME_
```

Set `config.toml` up as the theme authors guide. Some themes provide a sample `config.toml` file `theme/XXXX/exampleSite/`. So, in that case, copy the sample file. 

```
cd ~/webpages/hugo_pages
cp themes/_THEME_NAME_/exampleSite/config.toml .
```

Now, edit the copied `config.toml`, most importantly:
```
baseURL = "https://_USERNAME_.github.io/"
theme = _THEME_NAME_ 
```

---
## Check if the theme works
Execute the hugo server:
```
hugo server
```
Now, try access http://localhost:1313/

---
## Create a new post
```
    $ hugo new _PATH_/_FILE_NAME_
```
It creates a page under `content/`, for example, `hugo new post/etc/firstposting.md` creates `~/webpages/hugo_pages/content/post/etc/firstposting.md`. This directory structure needs to accord with the `config.toml` as the theme refer the file. In case of the theme(Binario) that I use, `config.toml` has to the following line:
```
[Params]
...
  mainSections = ["post"]
```
As the new post is created, the hugo server(localhost:1313) will display the post(If not, make sure the property, `draft: false` in the .md file.)

---
## Create static files
```
hugo -t _THEME_NAME_
```
then the static pages will be created under `public`.

---
## Link to [PAGE_SRC_REPO]
```
    $ cd ~/webpages/hugo_pages
    $ git remote add origin https://github.com/_USER_NAME_/hugo_pages.git
```
When need to add newly created pages:
```
git add .
git commit -m "COMMIT_MSG"
git push origin master
```

---
## Link to [GITHUB_IO_REPO]
```
    $ git submodule add -b master https://github.com/_USER_NAME_/_USER_NAME_.github.io.git public
```
When need to update the redered page via _USER_NAME_.github.io:
```
git add .
git commit -m "COMMIT MSG"
git push origin master
```
`*` if there’s a “refspec” error caused by that we haven’t had master branch, Solution:
```
$ git checkout -b 'master'
Switched to a new branch 'master'
$ git push -u origin master
```

---
## github auth token
```
remote: Support for password authentication was removed on August 13, 2021.
    remote: Please see https://docs.github.com/en/get-started/getting-started-with-git/about-remote-repositories#cloning-with-https-urls for information on currently recommended modes of authentication.
    fatal: Authentication failed for 'https://github.com/_USER_NAME_/hugo_pages.git/'
```

1. Login to the github account
2. “Settings” > “<> Developer settings” > “Personal access tokens” > “Tokens(Classic)” > “Generate new token”
3. Set and Fill up: “Note”, “Expiration”
4. Set scopes: repo, admin, delete, etc.
5. Save the generated token and use it as a password when committing codes

---
---

# Basic Git commands
![git_commands](https://user-images.githubusercontent.com/14274827/91470661-9d5a8780-e8b2-11ea-9ccb-0d813d2e35d1.png)


