---
title: "Figma Dev mode & VSCode"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - design
tags:
    - figma
    - vscode
last_modified_at: 2023-11-14
---

# Figma Dev Mode

<aside>
📖 Table of Contents

</aside>

<aside>
🗣 Dev Mode는 23년까진 open beta, 24년부터 charge

</aside>

# Figma Dev Mode

- Figma Web - App - VSCode 연동 가능
- VSCode Extension으로 Figma 프로젝트 View, Export, Code generate 가능
- Figma App에서는 Flutter Code까지 생성해주는데, VSCode 안에서는 아직 noop

## Generate Code in Figma App

Figma APP(아마 웹도 될 것 같습니다)에서 Dev Mode를 켠 후, 원하는 컴포넌트와 프레임워크/OS를 선택.

컴포넌트에 따라 코드를 자동으로 생성됨.

## Export SVG file in VSCode

VSCode에서 Figma Extension 추가한 뒤, Setting을 통해 SVG 파일 추출 경로를 지정.

Figma Extension에서 Asset에 대해 다운로드 버튼을 누르면 path가 뜨고, enter를 누르면 해당 경로에 svg 파일 생성.

<aside>
❓ fontweight, size, color등 다른 asset에 대한 추출은 어떻게 가능할지 서치가 필요합니다.

</aside>

## Ref.

- [Figma Dev Mode에 대한 소개](https://help.figma.com/hc/en-us/articles/15023124644247-Guide-to-Dev-Mode)
- [VSCode에서 Figma Dev Mode 사용하기](https://help.figma.com/hc/en-us/articles/15023121296151)
