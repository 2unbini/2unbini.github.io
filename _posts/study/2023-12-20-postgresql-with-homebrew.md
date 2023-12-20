---
title: "Homebrew홈브루로 PostgreSQL 설치 및 시작하기"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - Database
tags:
    - Database
    - PostgreSQL
last_modified_at: 2023-12-20
---

## 홈브루로 PostgreSQL 설치 및 시작하기

### 설치

[PostgreSQL 홈페이지에 있는 macOS 다운로드 페이지](https://www.postgresql.org/download/macosx/)에 들어가면 Homebrew로 인스톨하는 명령어가 적혀 있다.

@15로 major version을 설정해 줬는데, 10부터 16까지 있으므로 원하는 버전을 설정해주면 된다.

```
brew install postgresql@15
```

### 시작하기

이대로 그냥 냅다 createdb db를 치면 당연히 안된다. 서버 연결을 확인하라고 하는 에러가 나온다.

[PostgreSQL Homebrew Wiki](https://wiki.postgresql.org/wiki/Homebrew)에 적힌 대로 서버를 시작하는 명령어를 친다.

```
brew services start postgresql@15
```

만약 서버 실행이 잘 됐다면 다음 리스트에 postgresql@15가 started로 되어 있을 것이다.

```
brew services list

# 다음과 같이 되어 있으면 잘 실행되는 중~
Name          Status  User File
postgresql@15 started name ~/Library/LaunchAgents/homebrew.mxcl.postgresql@15.plist
```

### 끝내기

```
brew services stop postgresql@15
```

반대로 해주면 끝난다.

![postgre-1](/assets/images/all/postgre-1.png)