---
title: Github Blog 만들기
author: Chan Ho Han
date: 2021-06-25 00:00:00 +0900
categories: [Blogging]
tags: [first post]
comments : true
---

얼마 전부터 기술 블로그를 따로 만들어 보고 싶어졌는데, 예전부터 관심이 있었던 github 블로그를 만들어 보면 좋겠다는 막연한 생각에 시작하게 되었습니다.

테마를 다운받고 글만 작성하면 되겠다 싶었는데 생각보다 많은 오류를 내뿜어서 쉽지 않았습니다.

## 0.  환경 준비

Jekyll 사용을 위한 ruby 설치

[Installation](https://jekyllrb.com/docs/installation/)

- mac

```bash
brew install ruby
```

- ubuntu

```bash
sudo apt-get install ruby-full build-essential zlib1g-dev
```

## 1. 테마 고르기

[http://jekyllthemes.org/](http://jekyllthemes.org/])

위 링크에서 원하는 테마를 골라서 무료로 사용할 수 있습니다. 

저는 첫 페이지에 제 마음에 드는 테마가 바로 보였는데, 바로 [chripy](https://github.com/cotes2020/jekyll-theme-chirpy) 입니다. chripy theme를 기준으로 글을 작성해 보겠습니다.

## 2. GIT repo 만들기

![github_io_image](https://user-images.githubusercontent.com/46598292/123350024-1ee37a80-d595-11eb-8b23-2699a8b8c131.jpg){: width="60%" height="60%"}

Repository name에는 [username].github.io를 입력해줍니다.

username이 사용자 이름과 정확히 일치하지 않으면 작동하지 않을 수 있다고 합니다. (Repository의 이름에 대소문자는 실험해본 결과 구분하지 않는 것 같습니다)

## 3. 테마 다운로드

```bash
git clone https://github.com/cotes2020/jekyll-theme-chirpy.git
```

clone을 통해 테마를 다운로드 해준 후, [chanhohan.github.io](http://chanhohan.github.io) 저장소 안에 넣어줍니다.

## 4. _config.yml 수정

1. <span style="color:red">title</span> : 7ULY
2. <span style="color:red">tagline</span>: 찬호's 개발 블로그
3. <span style="color:red">github</span>: <span style="color:purple">username</span> : ChanHoHan
4. <span style="color:red">social</span>: <span style="color:purple">name</span> : Chan Ho Han
5. <span style="color:red">social</span>: <span style="color:purple">email</span> : chhan2759@gmail.com
6. <span style="color:red">avartar</span>

## 5. Deployment

![chripy-getting-start](https://user-images.githubusercontent.com/46598292/123353845-653cd780-d59d-11eb-8d17-557c6fbf5889.jpg){: width="60%" height="60%"}

안내 문서에 따르면, safe mode의 이유로 GitHub Actions를 사용해야하고, 그것을 위해 .github/workflows/pages-deploy.yml이 있어야 한다고 합니다.

링크된 문서의 내용을 그대로 복사하여 만든 후 커밋 및 푸쉬를 해줍니다.

![github-workflow](https://user-images.githubusercontent.com/46598292/123497198-296e4480-d667-11eb-993e-5df15cd3347d.jpg){: width="60%" height="60%"}


## 6. Troubleshooting

 -  "The jekyll-theme-chirpy theme is not currently supported on GitHub Pages." 
![image](https://user-images.githubusercontent.com/46598292/123497455-96360e80-d668-11eb-98aa-f020ce8e9b9a.png){: width="60%" height="60%"}

Settings > Pages 에서 Source 부분에 Branch가 gh-pages으로 되어있어야 했습니다.  

 -  "Your site is having problems building: Page build failed"

이 경우는 문법상의 오류 때문에 빌드를 실패해서 생기는 오류라고 합니다.
