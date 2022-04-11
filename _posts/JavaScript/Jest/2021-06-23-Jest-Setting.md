---
layout: post
title: "vscode에서 Jest 자동 완성이 안되는 경우"
date: 2021-06-23 00:17:00 +0900
categories: [javascript]
tags: [javascript]
---

1. `npm i @types/jest` 설치

- jest 관련 type definiton을 가지고 있는 패키지

2. add jsconfig.json를 프로젝트 루트 디렉토리에 추가

- `jsconfig.json` : vscode에서 사용하는 자바스크립트 랭귀지 서비스와 관련된 설정을 지정할 수 있다.

```
{
    "typeAcquisition": {
        "include": [
            "jest"
        ]
    }
}
```

```note
[자바스크립트 랭귀지 서비스](https://github.com/microsoft/TypeScript/wiki/JavaScript-Language-Service-in-Visual-Studio) : vs code에서 자바스크립트 편집을 지원하는 모듈
```
