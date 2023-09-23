---
title: "[Jekyll] `require': cannot load such file -- 2.6/ffi_c (LoadError)"
author:
    name: 박 찬영
    author: https://github.com/univdev
date: 2022-01-25 10:39:00 +0900
categories: [Tech, Jekyll]
tags: [Jekyll, Blog]
---
# 사건
제 맥북이 어느 순간부터 Jekyll 기반 블로그들을 로컬에서 실행할 수가 없게 되었습니다.  
짐작가는 원인이랄게 전혀 없는 상황에서
```text
/Library/Ruby/Gems/2.6.0/gems/ffi-1.15.5/lib/ffi.rb:3:in `require': cannot load such file -- 2.6/ffi_c (LoadError)
        from /Library/Ruby/Gems/2.6.0/gems/ffi-1.15.5/lib/ffi.rb:3:in `<top (required)>'
        from /Library/Ruby/Gems/2.6.0/gems/sassc-2.4.0/lib/sassc/native.rb:3:in `require'
        from /Library/Ruby/Gems/2.6.0/gems/sassc-2.4.0/lib/sassc/native.rb:3:in `<top (required)>'
        from /Library/Ruby/Gems/2.6.0/gems/sassc-2.4.0/lib/sassc.rb:31:in `require_relative'
        from /Library/Ruby/Gems/2.6.0/gems/sassc-2.4.0/lib/sassc.rb:31:in `<top (required)>'
        from /Library/Ruby/Gems/2.6.0/gems/jekyll-sass-converter-2.1.0/lib/jekyll/converters/scss.rb:3:in `require'
        from /Library/Ruby/Gems/2.6.0/gems/jekyll-sass-converter-2.1.0/lib/jekyll/converters/scss.rb:3:in `<top (required)>'
        from /Library/Ruby/Gems/2.6.0/gems/jekyll-sass-converter-2.1.0/lib/jekyll-sass-converter.rb:4:in `require'
        from /Library/Ruby/Gems/2.6.0/gems/jekyll-sass-converter-2.1.0/lib/jekyll-sass-converter.rb:4:in `<top (required)>'
```
```bundle exec jekyll serve```를 입력해도 이런 에러만 나오고 있는 상황이라 로컬에서 글을 확인하고 올릴 수가 없는 상황에 빠졌습니다.  
## 첫 번째 시도
위 에러문에서 유독 눈에 띄는 부분이 있는데, ```cannot load such file -- 2.6/ffi_c``` 라고 적힌 부분입니다.  
의존성 패키지 중 ```ffi```가 삭제라도 됐나 싶어서 ```gem install ffi```를 입력해서 설치를 하고 실행해봐도 결과는 매한가지였습니다.
## 두 번째 시도
```rm -rf``` 명령어로 프로젝트 폴더를 전부 지우고 Github에서 다시 Clone을 떠오는 방법을 사용해봤습니다.  
Github의 Workflow는 정상 동작을 하길래 이렇게 하면 되지 않을까 싶어 시도해 본 결과 같은 에러를 표시했습니다.
# 해결 방법
이게 직접적인 원인을 끼친건지, 아니면 여러 방법을 시도해보면서 간접적으로 영향을 끼친건지 정확히 판단은 되지 않습니다만 아래와 같은 방법을 통해 해결했습니다.  
1. ```gem update --system``` 명령어 실행
   1. 실행이 잘 되는 것 같다가 권한이 없다고 나옴.
2. ```sudo gem update --system``` 명령어 실행
   1. sudo를 앞에 붙여도 권한이 없다고 뜨긴 매한가지
3. 위 에러문 무시하고 ```bundle exec jekyll serve``` 명령어 실행
   1. ```bundle```이 설치 되지 않았다고 나옴
4. ```gem install bundle``` 명령어 실행
5. ```bundle exec jekyll serve``` 명령어 다시 실행
6. 정상 동작

# 참고
[Error in jekyll installation][참고]

[참고]: https://talk.jekyllrb.com/t/error-in-jekyll-installation/5868/2