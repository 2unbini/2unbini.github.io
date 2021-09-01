---
title: "깃헙 블로그 삽질 1"
categories:
    - sapjil
tags:
    - 깃허브
    - 블로그
    - 삽질
last_modified_at: 2021-09-01
---

# 깃허브 페이지로 블로그 만들기

> 사실 이 글은 시작에 불과하다 . . .

<br />

나만의 웹사이트를 만들어 사람들이 언제든 방문할 수 있으려면 서버가, 쉽게 접근할 수 있으려면 도메인이 필요하다.

그럼 이 서버와 도메인은 아무나 무료로 사용할 수 있을까? 아니다.

도메인의 경우 일정한 수수료를 내고 인터넷 진흥원이나 카페 24와 같은 등록대행자 홈페이지에서 도메인을 등록해야 그 도메인을 일정 기간 동안 가질 수 있게 된다.

서버의 경우 서버용 컴퓨터를 구축해 24시간 365일 켜 놓고 에러 처리를 다 해준다면 무료로 가능하지만 쉽지 않은 일이다. 역시 도메인처럼 웹 호스팅 업체들에 돈을 내고 서버를 운용할 수도 있다.

<br/>

그럼 서버와 도메인만 있으면 될까? 그렇지 않다.

우리가 일상적으로 봐서 몰랐던 웹 페이지들은 개발자도구를 통해 코드로 보면 어마무시하게 많은 html 코드로 이루어져 있다. 메인 화면부터 내가 원하는대로 분류한 카테고리, 각각의 글들, 이 모든 걸 담을 틀, 디자인...

단순히 내 공부, 프로젝트 기록용 블로그를 위해서 도메인을 구입하고, 웹 페이지를 손수 만드는 것은 부담스러울 수 있다.

<br/>

깃허브에서 제공하는 `Github Pages` 호스팅 서비스, `.github.io` 도메인, 그리고 정적 웹사이트 생성 도구 `jekyll` 을 이용해 웹사이트를 구성하면 우리는 수수료와 웹페이지 개발에 드는 노력을 들일 필요 없이 나만의 블로그를 가질 수 있다.

깃허브에서 제공하는 [`Github Pages`](https://pages.github.com) 의 내용을 쭉 따라가다보면 깃허브 페이지가 생성된다.

<br/>

## 시작하기

### Repo 생성

우선, 새 레파지토리를 생성한다.

튜토리얼의 내용대로, `username.github.io` 라는 이름의 Public Repo 를 생성하자.

나는 이미 해당 이름으로 된 레파지토리가 있어 저렇게 빨간 줄이 드는데, 처음 만들면 그냥 초록색으로 뜰 것이다.


![new repo](/assets/images/sap-1/repo.png)

레포는 Public으로 해 주고, README 파일은 어차피 나중에 테마 적용하면서 사라질 녀석이기 때문에 그냥 안 만들어 준다.

> **Note** : Private으로 Page를 이용하려면 돈을 내야 한다.

<br/>

### 깃 클론

앞서 만든 레포를 내 로컬 환경에 클론 해 준다.

```zsh
git clone https://github.com/username/username.github.io
```

> 클론이 안 된다면? [SSH key 등록, 원격에서 Clone, Push 하기.]()

<br />

### index.html 만들기

클론한 username.github.io 폴더에 들어가 화면에 띄울 웹 페이지를 생성한다.

```zsh
cd username.github.io

echo "Hello World" > index.html
```

`echo` 명령어를 써도 되고, `vi` 나 `vim`, `touch` 등 원하는 대로 만들어준다.

<br />

### 내 깃헙에 푸시하기

지금까지의 수정사항은 내 로컬에서만 이루어졌기 때문에 이를 깃허브에 반영시켜줘야 한다.

```zsh
git add .
git commit -m "Initial commit"
git push -u origin main
```

첫 번째 문장은 내가 반영하고자 하는 수정사항을 스테이지에 올리는 과정이다.

`.` 을 써도 되고, `--all` 과 같은 옵션을 써도 되고, 위의 예시에선 `index.html` 을 써줘도 되는데, 앞의 두 옵션은 수정사항이 생긴 모든 파일(혹은 폴더)을 스테이지에 올리겠다는 뜻이다.

두 번째 문장은 스테이지에 올라간 수정사항들을 기록하는 단계다.

여기서 `""` 안에 들어가는 문장은 커밋 메시지로, 주로 어떤 수정사항이 생겼는지를 간단하게 서술한다.

세 번째 문장은 원격 저장소에 저장하는 단계다.

선택한 브랜치 `main`을 원격 서버 `origin` 으로 전달하겠다는 의미다. 브랜치를 딱히 명시해줄 필요 없이 짧게 쓰고 싶다면 그냥 `git push` 라고 쓰면 된다.

<br/>

### 확인하기

`https://username.github.io` 로 접속하면, 정상적인 경우 Hello World가 뜰 것이다.

<br/>
<br/>

## Jekyll

한 고비를 넘었다. 사실 고비도 아니다.

다음은 큰 고비인 `jekyll` 이다. jekyll은 루비로 만들어졌기 때문에 이를 설치하고 사용하려면 `ruby` 가 깔려 있어야 한다.

<br/>

### Ruby 버전 확인

Mac OS 에는 기본적으로 `ruby` 가 깔려 있다. 하지만 내가 사용하고 싶은 jekyll 테마 최소 요구사항 등에 맞지 않을 수 있으니 한 번 확인 해 주도록 하자.

```zsh
ruby -v
```

명령어를 통해 루비 버전을 확인한다.

버전이 출력되면 깔려 있는 것이고, 'No such file or Directory' 와 같은 문구가 뜨면 깔려 있지 않은 것이다.

있다면 아래로 넘어가고, 없다면 [루비 설치하기 사이트](https://www.ruby-lang.org/ko/documentation/installation/)를 통해 차근차근 설치 해 보자.

<br/>

### 로컬에 jekyll 설치

아래 명령어를 실행시켜 `jekyll`을 설치하자.

```zsh
gem install jekyll bundler
```

나는 여기서 권한 에러가 떴다.

![error](/assets/images/sap-1/error1.png)

흑흑... 대충 해당 디렉토리에 대한 권한을 주면 되는 것 같아서 `sudo` 명령어를 써 봤지만, 이미 많은 사람이 썼다가 문제가 생겼는지 잘 먹히지 않았다.

그래서 구글링(a.k.a 삽질)을 통해 `Ruby` 디렉토리에 권한을 주는 방식으로 에러를 수정했다.

```zsh
sudo chown -R 권한줄유저이름 /Library/Ruby/Gems/2.6.0
```

`권한줄유저이름`에 권한을 줄 유저 이름을 넣고, 뒤의 경로엔 내가 권한을 주고 싶은 디렉토리 경로를 넣어 주면 된다.

권한을 주고 나니 잘 설치가 됐다.

<br/>

### github.io 폴더에 jekyll 심기

아까 깃 클론한 username.github.io 폴더로 들어간다.

```zsh
cd username.github.io
```

해당 폴더 안에서 아래 명령어를 쳐 jekyll 을 설치한다.

```zsh
jekyll new ./
```

이렇게 치면 에러 메시지가 뜰 텐데(안 뜰 수도 있음), 에러 메시지가 뜨면 그냥 걔가 하라는 대로 하면 된다.

![error](/assets/images/sap-1/error2.png)

대충 '니가 지금 설치하려는 폴더 안 비어있음' 인데, 어차피 index.html 뿐이니 그냥 `--force` 명령어를 사용해 설치 해 준다.

`jekyll` 을 통해 새로운 웹 페이지를 만들었고, 이를 로컬 환경에서 실행시켜보자.

```zsh
bundle exec jekyll serve
```

이렇게 되면 jekyll 서버가 내 컴퓨터에서 실행된다.

정상적으로 실행됐으면, 터미널에 서버 상태와 url 이 적힌 메시지가 떠 있을 것이다.

프로세스를 종료하지 않은 채로 브라우저에 `localhost:4000` 를 입력하면 기본 웹사이트가 열린다.

<br/>

![index](/assets/images/sap-1/index.png)

<br/>

### 근데 난 bundle 이 안 됐음

<br/>

> Gem::Ext::BuildError: ERROR: Failed to build gem native extension

<br/>

`bundle` 명령어로 빌드에 필요한 패키지들이 싹 설치가 돼야 하는데 그게 잘 안 됐다.

가장 문제인 녀석은 `nokogiri`와 `nokogumbo` 였고, 아키텍처 에러가 계속 뜬 걸 보아하니 아마 내 맥북이 M1 이라 컴퓨터가 혼돈의 도가니였던 것 같다. 아닐 수도 있음.

솔직히 왜 됐는 지 잘 모르겠고, 또 안될 가능성이 농후하지만 일단 해결책은

```zsh
gem uninstall nokogiri
gem install nokogiri
gem install nokogumbo
bundle
```

를 통해 삭제했다가, 따로 설치하는 방식이다.

이렇게 했더니 일단 잘 됐다.

<br />

## 다음은?!

밋밋한 블로그가 아닌 조금 더 체계적이고 깔끔한 블로그를 원한다면 여기서 더 나아가 `테마` 를 설정하면 된다.

내 깃허브 블로그 만들기 삽질 24시간의 가장 큰 부분은 테마에 있다고 해도 과언이 아니다. 어려운 게 아닌 것 같으면서도 어렵다 ㅜㅜ

여튼 나는 `minimal mistakes` 테마를 사용했고, 이를 적용하는 방법은 다음 글에 써 놓았다.

[해당 링크](http://jekyllthemes.org)에 들어가면 많은 무료 테마를 볼 수 있다.

