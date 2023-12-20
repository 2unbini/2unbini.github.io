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

```
ls -al ~/.ssh
```

여기에 키 쌍이 나온다면 등록이 되어 있는 상태이다.

### 각 계정에 대한 키 생성하기

나는 2unbini라는 메인 계정과 seri라는 부계정을 모두 사용하고 싶었기 때문에 우선 각 계정에 대한 키를 생성했다.

2unbini에는 id_ed25519키를, seri에는 id_rsa키를 발급했다.

두개 다 동일한 알고리즘의 키를 생성해도 되는데, 이름을 바꾸고 관리하기가 어려울 것 같아서 각기 다른 알고리즘의 키를 생성했다.

### 계정에 대한 Host 매핑하기

이전에 ssh키를 발급하고 등록하는 과정에서 특정 호스트에 대한 키를 매핑해 정의해두어야 한다.

그 과정에서 ssh 폴더에 config 파일이 생성됐을수도 있고, 안 됐을수도 있다.

<figure>
  <img width="400" alt="Screenshot 2023-12-19 at 11 25 11 AM" src="https://github.com/2unbini/2unbini.github.io/assets/75126613/00a7fb86-b77c-4bec-b522-39f2fd5b4351" />
  <figcaption>
    <- 나는 예전에 이런 방식으로 뭔가를 끄적여놨다
  </figcaption>
</figure>


```
vim ~/.ssh/config
```

vim 등의 편집기로 config 파일을 아래와 같이 생성/수정한다.

```
# Default github account: 2unbini
Host github.com
  HostName github.com
  IdentityFile ~/.ssh/id_ed25519
  IdentitiesOnly yes

# Other github account: seri
Host github-sub
  HostName github.com
  IdentityFile ~/.ssh/id_rsa
  IdentitiesOnly yes
```

## 끝!

깃헙에서 ssh로 clone받을 때 이런 방식으로 터미널에 명령어를 입력한다.

```
git clone git@github.com:2unbini/hello-world.git
```

- 원하는 레포에 대한 커맨드라인 문장을 복붙하고,
- @와 :사이에 원하는 Host로 수정해주면

끝!

### 참고
- [using-multiple-github-accounts-with-ssh-keys](https://gist.github.com/oanhnn/80a89405ab9023894df7)
- [Adding your SSH key to the ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#adding-your-ssh-key-to-the-ssh-agent)

