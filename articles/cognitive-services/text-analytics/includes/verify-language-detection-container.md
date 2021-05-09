---
title: 언어 감지 컨테이너 인스턴스 확인
titleSuffix: Azure Cognitive Services
description: 언어 감지 컨테이너 인스턴스를 확인하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 543a4d85982adadc86435819679351c8ffaa9814
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96009892"
---
### <a name="verify-the-language-detection-container-instance"></a>언어 감지 컨테이너 인스턴스 확인

1. **개요** 탭을 선택하고 IP 주소를 복사합니다.
1. 새 브라우저 탭을 열고 IP 주소를 입력합니다. 예를 들어 `http://<IP-address>:5000 (http://55.55.55.55:5000`을 입력합니다. 컨테이너가 실행 중임을 알려주는 컨테이너의 홈페이지가 표시됩니다.

    ![컨테이너 홈페이지를 확인하여 실행 중인지 확인합니다.](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. **서비스 API 설명** 링크를 선택하여 컨테이너의 Swagger 페이지로 이동합니다.

1. **POST** API 중 하나를 선택하고 **사용해 보기** 를 선택합니다. 이 예제 입력을 포함하는 매개 변수가 표시됩니다.

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

1. **showStats** 를 `true`로 설정합니다.

1. **실행** 을 선택하여 텍스트의 감정을 확인합니다.

    컨테이너에 패키징된 모델은 0에서 1 사이의 점수를 생성합니다. 여기서 0은 부정적인 감정이고 1은 긍정적인 감정입니다.

    반환되는 JSON 응답에는 업데이트된 텍스트 입력에 대한 감정이 포함됩니다.

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

이제 응답 페이로드의 JSON 데이터 문서를 해당 `id`로 원래 요청 페이로드 문서와 연관시킬 수 있습니다. 각 문서는 `characterCount` 및 `transactionCount`와 같은 다양한 통계를 포함하여 독립적으로 처리됩니다. 또한 각 결과 문서에는 검색된 각 언어에 대해 `name`, `iso6391Name` 및 `score`가 포함된 `detectedLanguages` 배열이 있습니다. 여러 언어가 검색되면 `score`를 사용하여 가장 가능성이 높은 언어를 결정합니다.