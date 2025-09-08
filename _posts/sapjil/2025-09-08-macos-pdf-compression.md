---
title: "macOS에서 pdf 용량 줄이기(feat. ghostscript, 터미널)"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - sapjil
tags:
    - 삽질
    - 블로그
    - 꿀팁
last_modified_at: 2025-09-08
---

# 맥에서 Ghostscript로 PDF 용량 줄이는 방법

PDF 파일, 특히 이미지가 많은 문서는 용량이 커져서 불편할 때가 많다.
맥 기본 앱인 미리보기(Preview)로도 압축이 되긴 하지만, 실질적으로 줄질 않음;;

13.6MB짜리 파일이 Preview로 마법처럼 16MB가 됐다. 어이가 없어서 다른 방식을 찾아봄.
챗 지피티한테 물어본 결과 **Ghostscript**라는 툴을 쓰면 훨씬 유연하게 용량을 줄일 수 있음.
이거로 화질 깨짐 없이 5.7MB로 줄여버림.

> 애플은 preview를 개선하라!!

---

## Ghostscript가 머냐

[Ghostscript](https://www.ghostscript.com)는 오픈소스 프로그램인데, PDF나 PostScript 파일을 다루는 데 특화돼 있음.
간단히 말해서 **PDF 최적화·압축·변환** 같은 걸 터미널에서 쉽게 할 수 있게 도와주는 도구.

---

## 설치하는 방법

갓브루로 편하게 인스톨.

```bash
brew install ghostscript
```

설치가 끝났으면 버전 확인 후 사용을 합니다.

```bash
gs --version
```

숫자가 뜨면 준비 완료!

---

## PDF 압축 명령어

가장 기본적인 압축 명령어임돠.

```bash
gs -sDEVICE=pdfwrite \
   -dCompatibilityLevel=1.4 \
   -dPDFSETTINGS=/ebook \
   -dNOPAUSE -dQUIET -dBATCH \
   -sOutputFile=output.pdf input.pdf
```

보면 알겠지만 핵심은 pdf 세팅을 선택하고, input, output 파일의 이름을 대치하는 것.

---

## 옵션 하나씩 뜯어보기

* `-sDEVICE=pdfwrite` → 출력 파일을 PDF로 만들겠다는 의미
* `-dCompatibilityLevel=1.4` → PDF 버전 지정 (1.4가 가장 무난)
* `-dPDFSETTINGS=/ebook` → 압축 수준 선택
  * `/screen`: 용량 최소, 화질 낮음 (화면 전용)
  * `/ebook`: 용량 적당, 화질 준수 (추천)
  * `/printer`: 인쇄용, 용량 좀 큼
  * `/prepress`: 고급 인쇄용, 화질 최고, 용량 최대
* `-dNOPAUSE` → 페이지마다 멈추지 않고 계속 진행
* `-dQUIET` → 불필요한 메시지 안 뜨게
* `-dBATCH` → 작업 끝나면 자동 종료
* `-sOutputFile=output.pdf` → 결과 파일 이름
* `input.pdf` → 원본 PDF 파일 이름

---

## 실제 예시

예를 들어 `report.pdf`라는 파일을 전자책 수준으로 압축하려면 이렇게 하면 된다:

```bash
gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 \
   -dPDFSETTINGS=/ebook -dNOPAUSE -dQUIET -dBATCH \
   -sOutputFile=report_small.pdf report.pdf
```

그러면 `report_small.pdf`라는 더 가벼운 파일이 생긴다. 당연히 이 작업은 report.pdf가 있는 파일에서 진행해야 함. 예를 들어, 해당 파일이 /Desktop에 있다면, `cd /Desktop`을 먼저 해야한다는 말씀,,

---

## 마무리

Ghostscript를 쓰면 단순히 용량 줄이는 걸 넘어서, **사용 목적에 맞춰 화질·용량을 조절**할 수 있다는 게 진짜 장점임.

Preview보다 훨씬 세밀하게 컨트롤할 수 있으니까, PDF 자주 다루는 사람이라면 꼭 써보길 추천함.
