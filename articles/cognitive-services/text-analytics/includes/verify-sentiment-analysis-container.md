---
title: 감정 분석 컨테이너 인스턴스 확인
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너 인스턴스를 확인 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c9b5411c044bb45d284cac0d30705c2b3d40ccd0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876441"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>감정 분석 컨테이너 인스턴스 확인

1. **개요** 탭을 선택 하 고 IP 주소를 복사 합니다.
1. 새 브라우저 탭을 열고 IP 주소를 입력 합니다. 예를 들어를 `http://<IP-address>:5000 (http://55.55.55.55:5000`입력 합니다. 컨테이너의 홈 페이지가 표시 됩니다. 그러면 컨테이너가 실행 중임을 알 수 있습니다.

    ![컨테이너 홈 페이지를 확인 하 여 실행 중인지 확인 합니다.](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. **서비스 API 설명** 링크를 선택 하 여 컨테이너의 Swagger 페이지로 이동 합니다.

1. **POST** api 중 하나를 선택 하 고 **사용해 보기**를 선택 합니다. 이 예제 입력을 포함 하는 매개 변수가 표시 됩니다.

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

1. **Showstats** 를로 `true`설정 합니다.

1. **실행** 을 선택 하 여 텍스트의 감정 확인 합니다.

    컨테이너에 패키지 된 모델은 0에서 1 사이의 점수를 생성 합니다. 여기서 0은 음수 감정이 고 1은 양의 감정입니다.

    반환 되는 JSON 응답에는 업데이트 된 텍스트 입력에 대 한 감정 포함 됩니다.

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

이제 응답 페이로드의 JSON 데이터 문서 `id` 를 원래 요청 페이로드 문서 `id`와의 상관 관계를 지정할 수 있습니다. 이상 점수는 매우 긍정적인 `0.98` 감정 나타냅니다.