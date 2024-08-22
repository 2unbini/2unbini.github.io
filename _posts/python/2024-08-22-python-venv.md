---
title: "Python 가상환경, venv"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - study
    - python
tags:
    - python
last_modified_at: 2024-08-22
---

## 파이썬 가상환경 by venv

파이썬으로 프로젝트를 할 때 각 프로젝트에 필요한 라이브러리, 혹은 파이썬 그 자체의 버전이 각기 다 다를 수 있다.

하나의 파이썬에서 모든 프로젝트에 사용된 라이브러리를 관리하는 것은 생각만 해도 충돌의 연속이 예상된다.

그래서 프로젝트별로 가상환경을 만들어 python을 설치하고 필요한 라이브러리를 버전별로 인스톨해 쓴다.

이 때 가상환경을 만드는 방법은 다양한데, 표준 라이브러리인 `venv`를 사용하는 방법을 써보려고 한다.

### venv
[venv 웹사이트](https://docs.python.org/ko/3/library/venv.html)

해당 웹사이트에 친절히 가상환경을 만들고 사용하고 사용해제하는 방법이 다 나와있다.

### 만들기

```zsh
python -m venv {venv_path}
```

{venv_path}에 해당하는 필드에 내가 가상환경을 만들고자 하는 위치를 적어주면 된다.

나는 venv들을 하나의 디렉토리에 묶어두고 싶어서 `venvs/project_name` 이런 형식으로 만들어둔다.

### 활성화

가상환경을 만들었으면 실행시켜야 한다.

> A virtual environment may be “activated” using a script in its binary directory (bin on POSIX; Scripts on Windows). This will prepend that directory to your PATH, so that running python will invoke the environment’s Python interpreter and you can run installed scripts without having to use their full path.

사이트에 위와 같이 설명돼있다.

가상환경은 위에 만들어둔 가상환경의 bin 디렉토리에 있는 어떤 스크립트를 통해 "활성화"된다. 이게 환경변수를 설정해줘서 파이썬이 위의 "활성화"된 가상환경에 파이썬이 동작할 수 있도록 해주는 것.

이에 대한 명령어는 다음과 같다.

```zsh
source {venv_path}/bin/activate
```

나는 zsh을 쓰고 있어서 위와 같은데, 각 쉘에 따라 명령어와 실행 스크립트는 달라질 수 있다. 어쨌든 `activate`인건 동일~

### 비활성화
해당 가상환경에서 작업이 끝났으면 활성화된 가상환경을 비활성화해준다.

만약 특정 가상환경이 활성화되어 있으면 쉘 스크립트에 다음과 같이 가상환경 이름이 붙어있을것이다.

```zsh
(venv_name) ~/venv_path $
```

이 상태에서 `deactivate`만 쳐 주면 된다.

### 키워드화

매번 액티베이트 하는 것도 좋은데..

`.zshrc`에 다음과 같은 스크립트를 추가하면 지정된 키워드로 바로 활성화할 수 있다.

```zsh
alias project_shortcut='cd {venv_path};source {venv_path}/bin/activate;'
```

요래 하면 그냥 쉘에 project_shortcut 치면 해당 프로젝트 가상환경이 활성화된다. 꿀꿀~~~