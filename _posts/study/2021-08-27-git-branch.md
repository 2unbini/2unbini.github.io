---
title: "Git Branch, 깃 브랜치 만들고 운용하기"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - study
tags:
    - 깃
    - 기초
    - 공부
last_modified_at: 2021-09-01
---

# Branch

브랜치는 마지막 커밋 위치를 가리키는 역할을 할 뿐 실제 브랜치는 아니다. 실제 브랜치는 커밋이 이루어지는 시점에 생성된다.

## 생성

```
git branch 브랜치이름 커밋ID
```

커밋 ID 에 따라 새롭게 생성되는 브랜치의 상태가 결정된다.

커밋 ID 를 기입하지 않으면, 자동으로 마지막 커밋으로 브랜치가 생성된다.

## 확인

```
git branch -v    // 브랜치 상태
git branch -vv   // 더 구체적인 상태
```

## 이동

```
git checkout 브랜치이름
```

브랜치 이름으로 브랜치를 이동한다.

깃은 하나의 워킹 디렉터리만 가지고 있으므로, 한 브랜치에서는 하나의 작업과 커밋을 할 수 있다. 즉, 다른 브랜치에서 작업하고자 한다면 브랜치를 변경하여 워킹 디렉터리를 재설정해야 한다.

여기서, 워킹 디렉터리에서 커밋하지 않은 내용이 있다면 브랜치를 변경할 수 없다. 반드시 커밋을 모두 완료한 뒤 브랜치를 이동하자.

## 원격 저장소에 브랜치 생성

```
git push 원격저장소별칭 브랜치이름

git push -u origin master
// '선택한 브랜치(master)를 원격 서버(origin)로 전달한다'는 의미.
```

## commit & push

```
git commit -m "Message"
git push
```

커밋을 함과 동시에 브랜치가 진짜_최종 생성된다.

푸시를 통해 원격 저장소에 변경 내용을 저장한다.

## 삭제

```
// 로컬 브랜치 삭제
git branch -d 브랜치이름

// 원격 저장소 브랜치 삭제
git push origin --delete 리모트브랜치이름
```

-d 옵션은 스테이지 상태가 깨끗할 때만 삭제를 허용한다.

현재 자신이 있는 브랜치, 병합되지 않은 브랜치는 삭제할 수 없다.

강제로 삭제하고자 한다면 -D 옵션을 사용하면 된다.

원격 브랜치를 삭제하려면 삭제 명령을 푸시해야 하며, 이 작업은 리모트 브랜치에 기록된 커밋도 모두 삭제한다.
