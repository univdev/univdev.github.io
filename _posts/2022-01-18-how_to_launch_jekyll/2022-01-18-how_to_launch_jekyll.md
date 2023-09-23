---
title: "[jekyll] 프로젝트 구동하는법"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-01-18 14:07:00 +0900
categories: [Tech, Jekyll]
tags: [Jekyll, Markdown, Notice]
---
# 개요
마크다운 문서를 배포하기 전에 미리 보기 위해서, 블로그 레이아웃을 커스터마이징 하기 위해서 등등 여러가지 이유로 프로젝트를 로컬에서 구동 할 필요가 있습니다.  
본 문서는 **Mac OS**를 기준으로 작성 되었습니다.
# 설치
## XCode
Jekyll은 Ruby언어 기반으로 만들어진 SSG Framework입니다.  
그렇기 때문에 Ruby언어를 해석할 수 있는 컴파일러가 필요한데, 이를 설치하기 위해서 [XCode][XCode]를 먼저 설치합니다.
## Ruby
XCode를 설치하셨다면 Ruby언어를 읽을 수 있는 컴파일러를 설치할 수 있습니다. 터미널에서 아래의 명령어를 입력해주세요.
```shell
# Install Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Ruby
brew install ruby
```
## Ruby 환경변수 등록
터미널에서 아래 명령어를 입력해주세요.
```shell
# If you're using Zsh
echo 'export PATH="/usr/local/opt/ruby/bin:/usr/local/lib/ruby/gems/3.0.0/bin:$PATH"' >> ~/.zshrc

# If you're using Bash
echo 'export PATH="/usr/local/opt/ruby/bin:/usr/local/lib/ruby/gems/3.0.0/bin:$PATH"' >> ~/.bash_profile

# Unsure which shell you are using? Type
echo $SHELL
```
### Ruby 설치 및 환경 변수 등록 여부 확인
터미널에 ```which ruby```와 ```ruby -v``` 명령어를 입력했을 때 아래와 같이 표시되면 설치가 되었다는 의미입니다.
```shell
which ruby
# /usr/local/opt/ruby/bin/ruby
```
```shell
ruby -v
ruby 3.0.0p0 (2020-12-25 revision 95aff21468)
```
## Jekyll 설치
Ruby 패키지를 설치했다면 ```gem``` 명령어를 사용하실 수 있습니다. ```jekyll```를 설치하기 위해 아래 명령어를 입력해주세요.
```shell
gem install --user-install bundler jekyll
```
## Jekyll 환경 변수 설정
환경 변수를 설정하기 위해 아래 명령어를 입력합니다.
```shell
# If you're using Zsh
echo 'export PATH="$HOME/.gem/ruby/X.X.0/bin:$PATH"' >> ~/.zshrc

# If you're using Bash
echo 'export PATH="$HOME/.gem/ruby/X.X.0/bin:$PATH"' >> ~/.bash_profile

# Unsure which shell you are using? Type
echo $SHELL
```
환경 변수 설정이 완료 되었다면 아래 명령어를 통해 체크 해보실 수 있습니다.
```shell
gem env
```
## 프로젝트 실행
### 의존성 패키지 설치
이미 jekyll 프로젝트가 있다고 가정하고 작성합니다.  
jekyll 프로젝트 폴더에 들어가서 아래 명령어를 실행합니다. 블로그를 실행하기 위한 의존성 패키지를 설치하기 위한 과정입니다.
```shell
bundle install
```
### 실행
아래 명령어를 통해 실행이 가능합니다.
```
bundle exec jekyll serve
```
### 접속
위 명령어를 입력하셨고, 아무 에러도 나오지 않았다면 ```http://127.0.0.1:4000``` 주소로 웹 브라우저에 접속하실 수 있습니다!
# 참고
[Jekyll 공식 가이드 문서][공식 가이드 문서]

[공식 가이드 문서]: https://jekyllrb.com/docs/installation/macos/
[XCode]: https://apps.apple.com/kr/app/xcode/id497799835?mt=12