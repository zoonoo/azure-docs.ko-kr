---
title: 핵심 구 추출 컨테이너 인스턴스 확인
titleSuffix: Azure Cognitive Services
description: 핵심 구 추출 컨테이너 인스턴스를 확인하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5dca4828a5c1127133461ddf9fc06099fc176b68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96009910"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>핵심 구 추출 컨테이너 인스턴스 확인

1. **개요** 탭을 선택하고 IP 주소를 복사합니다.
1. 새 브라우저 탭을 열고 IP 주소를 입력합니다. 예를 들어 `http://<IP-address>:5000 (http://55.55.55.55:5000`을 입력합니다. 컨테이너가 실행 중임을 알려주는 컨테이너의 홈페이지가 표시됩니다.

    ![컨테이너 홈페이지를 확인하여 실행 중인지 확인합니다.](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. **서비스 API 설명** 링크를 선택하여 컨테이너의 Swagger 페이지로 이동합니다.

1. **POST** API 중 하나를 선택하고 **사용해 보기** 를 선택합니다. 이 예제 입력을 포함하는 매개 변수가 표시됩니다.

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. 입력을 다음 JSON 콘텐츠로 바꿉니다.

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. **showStats** 를 `true`로 설정합니다.

1. **실행** 을 선택하여 텍스트의 감정을 확인합니다.

    컨테이너에 패키징된 모델은 0에서 1 사이의 점수를 생성합니다. 여기서 0은 부정적이고 1은 긍정적입니다.

    반환되는 JSON 응답에는 업데이트된 텍스트 입력에 대한 감정이 포함됩니다.

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

이제 응답 페이로드의 JSON 데이터 문서 `id`를 원래 요청 페이로드 문서 `id`와 연관시킬 수 있습니다. 결과 문서에는 해당 입력 문서에서 추출된 핵심 구 목록이 포함된 `keyPhrases` 배열이 있습니다. 또한 결과 문서마다 `characterCount`, `transactionCount` 등의 다양한 통계가 있습니다.