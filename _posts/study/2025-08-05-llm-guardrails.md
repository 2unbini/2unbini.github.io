---
title: "LLM Guardrail 이해하기"
toc: true
toc_sticky: true
categories:
  - 📂 all
  - study
tags:
  - LLM
  - AI
last_modified_at: 2025-08-25
---

# LLM 가드레일 이해하기

대규모 언어 모델(LLM, Large Language Model)은 자연어 생성, 문서 요약, 코드 작성, 질문 응답 등 다양한 작업에 활용할 수 있어요. 하지만 확률적으로 작동하기 때문에, 때로는 신뢰하기 어려운 결과를 내놓기도 해요.

예를 들어, 그럴듯하지만 사실이 아닌 정보를 출력하거나, 편향되거나 민감한 내용을 그대로 드러낼 수 있어요. 이럴 때 **가드레일(Guardrails)**을 통해 입력과 출력을 다듬으면, 결과를 보다 안전하고 서비스 목적에 맞게 조정할 수 있어요.

## 가드레일이 필요한 이유 이해하기

LLM은 다음에 올 단어를 예측하는 방식으로 동작해요. 그대로 두면 다음과 같은 문제가 생길 수 있어요:

* 거짓 정보를 사실처럼 표현함
* 혐오 표현이나 편향된 의견 포함
* 민감한 정보 그대로 노출
* 형식이나 정책에 맞지 않는 출력 발생

가드레일을 걸어두면, 이런 출력이 사용자에게 전달되기 전에 실시간으로 점검하거나 수정할 수 있어요.

## 가드레일 적용 위치 살펴보기

가드레일은 보통 두 지점에서 적용돼요.

* **입력 가드레일**: 프롬프트를 모델에 전달하기 전에 확인하고 정제
* **출력 가드레일**: 모델이 생성한 응답을 보여주기 전에 검토하고 수정

<img width="2016" height="696" alt="image" src="https://github.com/user-attachments/assets/0544fe69-edce-4cdc-bfa8-caa579a666ca" />


상황에 따라 이 두 단계를 조합해 유연하게 구성할 수 있어요.

## 주요 가드레일 방법 살펴보기

요즘 LLM 시스템은 여러 단계의 가드레일을 겹쳐서 사용해요. 아래는 쉽게 적용할 수 있는 대표적인 방법들이에요.

### 1. 규칙 기반 필터 적용하기

금칙어나 특정 패턴을 기준으로 민감한 내용을 차단하거나 가려줘요.

```python
import re

text = "How to hack a server?"
filtered = re.sub(r"(?i)hack|exploit|malicious", "[차단됨]", text)
```

### 2. 분류기 기반 필터 활용하기

OpenAI Moderation API 같은 도구를 사용하면, 출력이 부적절한지 자동으로 판단하고 차단할 수 있어요.

```python
import openai

flagged = openai.OpenAI(api_key="...")\
    .moderations.create(input=text).results[0].flagged
```

### 3. LLM으로 출력 검토하기

응답을 두 번째 LLM에 보내서 정책에 어긋나는 부분이 있는지 확인하고, 필요하다면 다시 작성하게 만들 수 있어요.

```python
import openai

openai.api_key = "YOUR_OPENAI_API_KEY"
text = "출력된 텍스트를 여기에 넣으세요."

response = openai.ChatCompletion.create(
    model="gpt-4",
    messages=[
        {
            "role": "user",
            "content": f"다음 텍스트의 안전성을 검토해줘. 정책 위반이나 민감한 표현이 있다면 알려줘:\n\n{text}"
        }
    ],
    temperature=0
)

review = response.choices[0].message["content"]
```

### 4. 구조화된 출력 강제하기

JSON처럼 정해진 형식으로만 응답하도록 제한하고, `Pydantic` 같은 도구로 형식이 맞는지 검증할 수 있어요.

```python
from pydantic import BaseModel
import json

class Weather(BaseModel):
    city: str
    temperature: int

raw = '{ "city": "Seoul", "temperature": 27 }'
data = Weather(**json.loads(raw))
```

## 가드레일로 AI 도우미 설계하기

가드레일은 단순히 위험한 출력을 막는 도구가 아니에요. 원하는 방식으로 AI가 작동하도록 만드는 **기능 구성 요소**로도 쓸 수 있어요.

잘 설계된 가드레일을 조합하면, 다음과 같은 일을 할 수 있어요:

* 정해진 구조대로 출력해 외부 도구와 안전하게 연동
* 검색 기반 문맥(RAG)을 활용해 사실에 근거한 응답 생성
* 대화 흐름 중간에도 응답의 안전성 점검
* 의료, 금융 등 민감한 영역에서도 규정을 자동으로 지키게 구성

이렇게 구성하면, 단순히 텍스트만 생성하던 LLM이 **정확하고 책임감 있게 작동하는 똑똑한 AI 도우미**로 바뀔 수 있어요.
