---
title: "Github에서 원격으로 Clone, Push 하기 (feat. SSH Key)"
toc: true
toc_sticky: true
categories:
    - sapjil
tags:
    - 깃허브
    - 블로그
    - 삽질
last_modified_at: 2021-08-26
---

## Github 에서 원격으로 Clone, Push 하기

<br/>

![drag and drop](/assets/images/sap-3/1.png)

<br/>

'프로그래머는 깃허브를 쓴댄다' 라는 말만 듣고 무작정 깃허브 계정을 만들고 드래그 앤 드롭으로 레포 관리를 해왔던 나는, 매번 새로운 내용을 업데이트 할 때마다 폴더를 열고, 드래그 앤 드롭을 해서, 모든 파일들이 다 업로드되길 기다려야 했다. 42서울에서 과제를 제출할 때 처음으로 `git clone` , `git push` 명령어를 쓰게 되었는데, 이를 위해서는 내가 사용하는 로컬 환경에서 SSH 키를 받아 42서울 서버에 저장해야 했다.


무지성으로 따라한 뒤로 의식 없이 쓰다가, 코로나 4단계로 집에 감금(?)되면서 내 맥북에서 작업하던 과제나 프로젝트 파일들을 Github에 정리해야 하는 상황이 발생했다. 귀찮아서 그냥 또다시 깃허브에서 레포 만들고 드래그 앤 드롭으로 올리다가 폴더가 꼬이는 바람에... 이번엔 유지성 유의식으로 해보자 하여 글을 정리하게 되었다.

## SSH

SSH는 [Secure Shell](https://ko.wikipedia.org/wiki/시큐어_셸)로, 네트워크 상의 다른 컴퓨터에 접속하기 위해 사용되는 인터넷 프로토콜이다. Github 가 SSH 프로토콜을 지원하기 때문에 우리는 SSH 키를 통해 원격으로 Git Clone, Push와 같은 코드 관리를 안전하게 수행할 수 있는 것이다.

## 공개키 인증 방식

SSH는 다양한 인증 방법을 제공하는데, 그 중 우리는 공개키 인증 방식을 사용하게 된다.

[공개키 인증 방식](https://ko.wikipedia.org/wiki/공개_키_암호_방식)을 사용하려면 공개키와 개인키로 이루어진 키 쌍을 발급받아야 한다. 이렇게 발급받은 키는 각각 접속하고자 하는 서버에 올리고, 접속할 때 사용하게 된다.

공개키는 말 그대로 공개되어도 무방하지만, 개인키는 비밀번호의 역할을 하기 때문에 외부에 보여져선 안 된다. 공개키를 Github 서버에 등록해놓은 상태에서 개인키가 외부에 유출되면, 개인키를 가진 모두가 내 Git 서버에 접근할 수 있기 때문이다.

간단하게 SSH와 키에 대해 알아봤으면, 이제 내 로컬 환경에서 키를 발급받고, 이를 Github 서버에 올리면 된다. 이 과정에 대해서는 Github에서 상세하게 설명 해 주고 있다.

[Generating a new SSH key and adding it to the ssh-agent](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

## SSH 키 확인하기

로컬 환경에서 SSH 키를 발급받기 전에, 내가 이미 발급받은 키가 있는지 확인하는 것이 좋다.

터미널을 켠 다음, 

```
ls -al ~/.ssh
```

명령어를 입력한다.

디렉토리나 파일이 없습니다 라는 메시지가 뜨면 없는거고, 리스트가 나오면 이미 있는 것이다.

만약 있으면, 아래와 같은 형식의 공개키가 뜰 것이고, 원하는 키 쌍을 사용하면 된다.

- id_rsa.pub
- id_ecdsa.pub
- id_ed25519.pub

없다면, 다음의 과정을 밟아 키를 만들어주면 된다.

## SSH 키 생성하기

다시 터미널을 켠 다음, 

```
ssh-keygen -t ed25519 -C "your_email@example.com"
```

을 입력하되, "" 안의 이메일 형식을 내 Github 이메일 주소로 바꿔주면 된다.

만약, Ed25519 알고리즘을 지원하지 않는 시스템이라면 아래 명령어를 사용하면 된다.

```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

마찬가지로 "" 안의 이메일은 내 Github 이메일 주소로 채워넣는다.

이 명령어를 통해 새로운 SSH 키 쌍이 만들어진다. 정상적으로 만들어졌다면,

```
Generating public/private ed25519 key pair.
Enter a file in which to save the key (/Users/you/.ssh/id_ed25519): [Press enter]
```

와 같은 메시지가 뜰 것이다.

두 번째 문장은 키를 저장할 위치를 지정해주는 것인데, 그냥 엔터를 누르면 괄호 안에 쓰여진 기본 위치에 저장된다.

저장 위치까지 설정됐으면, password 와 유사한 passphrase 를 입력하라는 문구가 뜬다. 암호의 역할을 하는 문장인 듯 싶다.

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

안 해 줘도 상관 없지만, 나는 그냥 해 줬다.

여기까지 성공적으로 마치면, 공개키와 개인키가 저장된 위치와 fingerpring, 그리고 randomart image가 뜬다.

## SSH 키 확인하기

생성된 키를 확인하고자 한다면, `ls` 혹은 `cat` 명령어를 사용하면 된다.

```
// ssh 키들의 리스트 출력
ls -al ~/.ssh

// 특정 ssh 키 출력
cat ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```

위에서 설정했던(혹은 기본으로 설정된) 키 저장 위치를 적어 넣으면 확인이 가능하다.

Github 서버에 등록해야 할 것은 **공개키** 이므로, `cat id_ed25519.pub` 을 해서 나온 내용을 복사해 붙여넣으면 된다.

여태껏 `cat` 해서 그냥 드래그 하고 복사 붙여넣기를 했는데, 새로운 명령어를 알아냈다. [[참고 블로그]](https://www.lainyzine.com/ko/article/creating-ssh-key-for-github/)

```
pbcopy < ~/.ssh/id_ed25519.pub
```

을 하면, `cat` 을 통해 나온 내용이 그대로 복사된다.

## SSH 키 ssh-agent 에 등록하기

ssh-agent 에 등록하기에 앞서, SSH 키가 존재하는지를 먼저 확인한 뒤, 등록을 진행한다.

ssh-agent 는 왜 쓰는가..! 를 찾아봤는데, 앞서 지정한 passphrase 를 기억해서 최초 한 번만 비밀번호를 입력하면 그 다음부턴 쭉 알아서 인증을 해 주는 기능이라고 한다. 이 말은, passphrase 를 설정하지 않은 사람이거나, SSH 키 비밀번호를 내가 직접 입력하고 싶은 사람이라면 이 단계를 굳이 하지 않아도 된다는 것.

SSH 키를 등록하기 위해선, 기본적으로 제공하는 `ssh-add` 명령어를 쓰거나, 이 명령어를 찾을 수 없다고 하면 macports, homebrew 혹은 외부 소스를 통해 설치해 사용하면 된다.

먼저, ssh-agent 를 백그라운드에서 실행한다.

```
eval "$(ssh-agent -s)"
```

환경에 따라서 위의 명령어가 아닌 다른 형태의 명령어가 필요할 수도 있다.

- root 접근 : ssh-agent 시작 전 `sudo -s -H` 입력
- `exec ssh-agent bash` 혹은 `exec ssh-agent zsh` 을 통해 ssh-agent 실행


다음으로, `~/.ssh/config` 를 수정해야 한다.

이 파일은 있을 수도 있고, 없을 수도 있는데, 이를 확인하려면 앞서 계속 사용한 `ls` 혹은 `cat` 명령어를 사용하면 된다. 해당 파일을 수정함으로써 ssh-agent 가 passphrase 를 내 키체인에 저장하고 자동으로 불러올 수 있게 한다.

```
// 파일이 없을 때
touch ~/.ssh/config

// 파일이 있을 때 open vi vim 아무거나...
open ~/.ssh/config
vi ~/.ssh/config
```

`config` 파일에 아래와 같은 내용을 넣어준다.

```
Host *
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

내가 발급받은 개인키의 위치는 앞서 내가 설정한대로 (혹은 기본값으로) `IdentityFile` 의 내용을 수정해주면 된다.

만약, 내가 해당 키를 Github 에서만 쓰고 싶고, ssh-agent 를 쓸 필요가 없다면 아래와 같이 수정해주면 된다.

```
Host github.com
	IdentityFile ~/.ssh/id_ed25519
	User git
```

마지막으로, ssh-agent 가 내 passphrase 를 키체인에 저장할 수 있게 한다.

```
ssh-add -K ~/.ssh/id_ed25519
```

경로와 키 이름을 발급받은 상태로 바꿔서 입력해주면 끝이다.

## Github 서버에 공개키 저장하기

Github 에 로그인한 뒤, Setting > SSH and GPG keys 항목에 들어가면 SSH 키를 입력할 수 있다.

![sshkey window](/assets/images/sap-3/3.png)

아까 `pbcopy` 명령어로 복사했던 **공개키**를 아래의 Key 란에 붙여넣기 해 준다.

제목은 그냥 내가 알아보기 쉽게 적어주면 된다. 나는 `Seri SSH key` 뭐 이렇게 적었다.

이렇게 Key를 입력하고 추가하면, SSH 키가 정상적으로 등록된다.

![sshkey](/assets/images/sap-3/2.png)

## Drag and Drop 안녕...

![clone](/assets/images/sap-3/4.png)

더 이상 드래그 앤 드롭 말고 `git clone` 하세요~...~!
