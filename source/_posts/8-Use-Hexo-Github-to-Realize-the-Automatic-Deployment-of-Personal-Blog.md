---
title: 8-Use-Hexo+Github-to-Realize-the-Automatic-Deployment-of-Personal-Blog
date: 2019-12-09 20:35:22
tags:
---

Recently the computer was broken, so all study resources were lost, and before I didn't like backup files, I had to learn to build a personal blog with Hexo and Github. Write this file, so last time will not waste too much time to redo the same things as today.

<!--more-->

#### Important 
(How to use GitHub redeploy blog):
1. Download the source file from the <b>gh-pages</b> branch from [github repository.](https://github.com/wang-yuhao/wang-yuhao.github.io/tree/gh-pages)

2. Generate a new Post with the command: 
```
hexo n post <Here-is-post-name>
```
then a .md file and a folder with the same name \<Here-is-post-name\> will be automatically generated.

3. Add relative pdfs or images in \<Here-is-post-name\> folder
and edit the \<Here-is-post-name\>.md file.
+ Insert pdf files to post:
```
<object data="./Poster_ADL-AI_final.pdf" type="application/pdf" width="100%" height="877px">
</object>
```
+ Insert images to post:
```
![comment.png](comments.png)
```

4. Git push local <b>gh-pages</b>repository to remote repository

5. Finally, run: 
```
$ hexo clean && hexo g -d
```

6. Reset the domain in your repository `Setting` on GitHub:


#### Warning:
If you met (Your Connection is Not Private error) or (The custom domain for your GitHub Pages site is pointed at an outdated IP address. You must update your site's DNS records if you'd like it to be available via your custom domain. ), maybe everything are right, you just need to wait a few hours:

### On Ubuntu
#### 1. Learn Hexo Blog Framework:
1. install hexo: `npm install -g hexo`
2. Initlize hexo: `hexo init`
3. Install required packages: `npm install`
4. Deploy Hexo:`hexo s`
5. Open `localhost:4000` in browser.
[Hexo](https://hexo.io/)

#### 2. Use NEXT Thema in Hexo: 
[Next Thema Website](https://theme-next.iissnan.com/)
ps: This blog is using Next-Gemini.
1. Install Thema:
```
$ cd hexo
$ git clone https://github.com/theme-next/hexo-theme-next themes/next
```
2. Set up the theme, in _config.yml change:
```
theme: next
```
3. Set up the scheme, in themes/next/_config.yml change:
```
scheme: Gemini
```
4. Set up menu: user can directly use Font Awesome icon in `themes/next/_config.yml`

5. Dynamic background, modify `themes/next/_config.yml`: 
```
canvas_nest:
enable: true
onmobile: true # Display on mobile or not
color: "100,100,100" # RGB values, use `,` to separate
opacity: 0.5 # The opacity of line: 0~1
zIndex: -1 # z-index property of the background
count: 99 # The number of lines
```
6. Add visitor comment, modify `themes/next/_config.yml`, first register a count in Disqus: 
```
# Multiple Comment System Support
comments:
  # Available values: tabs | buttons
  style: tabs
  # Choose a comment system to be displayed by default.
  # Available values: changyan | disqus | disqusjs | gitalk | livere | valine
  active: disqus 
  # Setting `true` means remembering the comment system selected by the visitor.
  storage: true
  # Lazyload all comment systems.
  lazyload: true
  # Modify texts or order for any navs, here are some examples.
  nav:
    disqus:

# Disqus
disqus:
  enable: true
  shortname: <your-disqus-id> #
  count: true
```

#### 3. Delete a post in hexo
1. Delete the post under source/_post folder 

2. Run hexo clean to delete the database (db.json) and assets folder 

3. Run hexo generateto generate the new blog without your deleted post 

4. Run hexo deploy to deploy your blog


#### 4. Connect Hexo with Github
1. deploy hexo on GitHub pages: build a new repository with the name: <username.github.io>, create a new branch <hexo>

2. git clone this repository and copy hexo file into this folder

3. modify the `_config.yml`
```
deploy:
  type: 'git'
  repo: git@github.com:wang-yuhao/wang-yuhao.github.io.git
  branch: master
```
when there is `Deploy error`:
```
npm install hexo-deployer-git --save
```

4. generate and deploy
```
hexo clean && hexo generate
hexo deploy
```

5. Important: Use <hexo> store and update files, <master> store static pages, so always run command in hexo branch

#### 5. Achieve Automated Deployment:
1. comment `repo` in _config.yml:
```
deploy:
  type: 'git'
#  repo: git@github.com:wang-yuhao/wang-yuhao.github.io.git
  branch: gh-pages
```

2. In local blog directory create a folder with name `.travis.yml`, it exists in the same directory with `_config.yml`.

3. Input this command:
```
sudo: false
language: node_js
node_js:
  - 12
cache: npm
branches:
  only:
    - master # build master branch only
script:
  - hexo generate
deploy:
  provider: pages
  skip-cleanup: true
  github-token: $GH_TOKEN
  keep-history: true
  on:
    branch: master
  local-dir: public
```

Don't need to change anything.

4. Get access token, have to write down this token and put it in `travis ci` environment variables. 

5. Finally, run:
```
git checkout hexo
hexo new "my first blog"
git add .
git commit -am"add a new blog"
git push
```
`Travis ci` will be called automatically and automatically finish the deployment.


Reference: 
[#1 HEXO](https://hexo.io/)
[#2](https://www.cnblogs.com/zyh1020/p/11129565.html)
[#3](https://ouuan.github.io/post/hexo%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E6%8C%87%E5%8C%97/)