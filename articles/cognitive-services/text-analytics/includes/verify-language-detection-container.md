---
title: 언어 검색 컨테이너 인스턴스 확인
titleSuffix: Azure Cognitive Services
description: 언어 검색 컨테이너 인스턴스를 확인하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc52586550f89ddae147d79458584331ed984eea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876444"
---
### <a name="verify-the-language-detection-container-instance"></a>언어 검색 컨테이너 인스턴스 확인

1. **개요** 탭을 선택하고 IP 주소를 복사합니다.
1. 새 브라우저 탭을 열고 IP 주소를 입력합니다. 예를 들어 `http://<IP-address>:5000 (http://55.55.55.55:5000`enter)를 입력합니다. 컨테이너의 홈 페이지가 표시되어 컨테이너가 실행 중임을 알 수 있습니다.

    ![컨테이너 홈 페이지를 보고 실행 중인지 확인합니다.](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 서비스 **API 설명** 링크를 선택하여 컨테이너의 Swagger 페이지로 이동합니다.

1. **POST** API 중 하나를 선택하고 **시도해 선택합니다.** 이 예제 입력을 포함하는 매개 변수가 표시됩니다.

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. **showStats를** `true`로 설정합니다.

1. 실행을 **선택하여** 텍스트의 감정을 결정합니다.

    컨테이너에 패키지된 모델은 0에서 1까지의 점수를 생성하며, 여기서 0은 음수 감정이고 1은 양수 감정입니다.

    반환된 JSON 응답에는 업데이트된 텍스트 입력에 대한 감정이 포함됩니다.

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

이제 응답 페이로드의 JSON 데이터의 문서를 해당 `id`로 원래 요청 페이로드 문서와 상호 연관시킬 수 있습니다. 각 문서는 `characterCount` 및 와 `transactionCount`같은 다양한 통계를 포함하는 독립적으로 처리됩니다. 또한 각 결과 `detectedLanguages` 문서에는 `name`에서 `iso6391Name`와 `score` 가 와 각 언어에 대해 검색된 배열이 있습니다. 여러 언어가 검색되면 가장 `score` 가능성이 높은 언어를 결정하는 데 사용됩니다.