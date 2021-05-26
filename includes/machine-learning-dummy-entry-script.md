---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 04/21/2021
ms.author: gopalv
ms.openlocfilehash: 30237f94d5961d853509e303f50be1e8a42de9b0
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2021
ms.locfileid: "109846095"
---
항목 스크립트는 배포된 웹 서비스에 전송된 데이터를 받아서 모델에 전달합니다. 그런 다음, 모델 응답을 클라이언트로 반환합니다. *스크립트는 모델에 따라 다릅니다*. 항목 스크립트는 모델이 기대하고 반환하는 데이터를 해석해야 합니다.

항목 스크립트에서 수행해야 하는 두 가지 작업은 다음과 같습니다.

1. 모델 로드(`init()`라는 함수 사용)
1. 입력 데이터에서 모델 실행(`run()`이라는 함수 사용)

초기 배포의 경우 수신한 데이터를 인쇄하는 더미 항목 스크립트를 사용합니다.

<!-- ```python
import json

def init():
    print('This is init')

def run(data):
    test = json.loads(data)
    print(f'received data {test}')
    return(f'test is {test}')

``` -->

:::code language="python" source="~/azureml-examples-main/python-sdk/tutorials/deploy-local/source_dir/echo_score.py":::

이 파일을 `source_dir` 디렉터리 내부에 `echo_score.py`로 저장합니다.

예를 들어 사용자가 다음을 사용하여 모델을 호출하는 경우입니다.

```bash
curl -X POST -d '{"this":"is a test"}' -H "Content-Type: application/json" http://localhost:6789/score
```

다음 값이 반환됩니다.

```bash
"test is {'this': 'is a test'}"
```