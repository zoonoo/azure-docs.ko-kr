---
title: 키 구 추출 컨테이너 인스턴스 확인
titleSuffix: Azure Cognitive Services
description: Key Phrase 추출 컨테이너 인스턴스를 확인하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c177517ec18d7526b1cc09da74e35cb5434766d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876442"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>키 구 추출 컨테이너 인스턴스 확인

1. **개요** 탭을 선택하고 IP 주소를 복사합니다.
1. 새 브라우저 탭을 열고 IP 주소를 입력합니다. 예를 들어 `http://<IP-address>:5000 (http://55.55.55.55:5000`enter)를 입력합니다. 컨테이너의 홈 페이지가 표시되어 컨테이너가 실행 중임을 알 수 있습니다.

    ![컨테이너 홈 페이지를 보고 실행 중인지 확인합니다.](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 서비스 **API 설명** 링크를 선택하여 컨테이너의 Swagger 페이지로 이동합니다.

1. **POST** API 중 하나를 선택하고 **시도해 선택합니다.** 이 예제 입력을 포함하는 매개 변수가 표시됩니다.

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

1. 입력을 다음 JSON 콘텐츠로 바꿉꿉입니다.

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

1. **showStats를** `true`로 설정합니다.

1. 실행을 **선택하여** 텍스트의 감정을 결정합니다.

    컨테이너에 패키징된 모델은 0에서 1까지의 점수를 생성하고 0은 음수이고 1은 양수입니다.

    반환된 JSON 응답에는 업데이트된 텍스트 입력에 대한 감정이 포함됩니다.

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

이제 응답 페이로드의 `id` JSON 데이터의 문서를 원래 요청 페이로드 문서와 `id`상관 관계를 지정할 수 있습니다. 결과 문서에는 `keyPhrases` 해당 입력 문서에서 추출된 주요 구 목록이 포함된 배열이 있습니다. 또한 결과 문서마다 다양한 `characterCount` `transactionCount` 통계가 있습니다.