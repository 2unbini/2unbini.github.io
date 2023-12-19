---
title: "하나의 컴퓨터에서 복수의 Github 계정 cli로 사용하기"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - sapjil
tags:
    - 깃허브
    - 삽질
last_modified_at: 2023-12-19
---

## 터미널에서 두 개의 계정을 스위치해서 쓰고 싶었는데 하나를 쓰고 나면 다른걸 쓸 수 없었다..!

[이전 글](https://2unbini.github.io/📂%20all/sapjil/github-ssh-key/#ssh-키-ssh-agent-에-등록하기)에서 ssh키를 등록해 쓰면서 광명을 찾고 github CLI를 즐겁게 쓰고 있었다.

그런데 한가지 문제가 있었다. 2unbini라는 메인 계정과 seri라는 부계정을 스위치해 사용하고 싶었는데 그게 잘 안됐다. 내가 잘못 쓴 걸수도 있으나 일단 구글링을 통해 방법을 알아내어 포스팅을 하게 됐다.

## SSH 키 확인하기

Github에 대한 clone, pull, push 등의 작업을 CLI로 하고 있었다면 ssh키를 발급하고 등록하는 과정을 다 거쳤을 것이다.

그 과정에서 ssh 폴더에 config 파일이 생성됐을수도 있고, 안 됐을수도 있다.

<img width="680" alt="Screenshot 2023-12-19 at 11 25 11 AM" src="https://github.com/2unbini/2unbini.github.io/assets/75126613/00a7fb86-b77c-4bec-b522-39f2fd5b4351">



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
