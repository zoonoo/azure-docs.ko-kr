---
title: 감정 분석 컨테이너 인스턴스 확인
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너 인스턴스를 확인하는 방법을 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 2e201b4ec0d1364ea99b376171efabad65af0a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70968595"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>감정 분석 컨테이너 인스턴스 확인

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

    컨테이너에 패키지된 모델은 0에서 1까지의 점수를 생성하며, 여기서 0은 음수 감정이고 1은 양수 감정입니다.

    반환된 JSON 응답에는 업데이트된 텍스트 입력에 대한 감정이 포함됩니다.

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
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

이제 응답 페이로드의 `id` JSON 데이터의 문서를 원래 요청 페이로드 문서와 `id`상관 관계를 지정할 수 있습니다. 그 이상의 `0.98` 점수는 매우 긍정적인 감정을 나타냅니다.