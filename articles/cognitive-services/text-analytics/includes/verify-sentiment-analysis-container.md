---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너 인스턴스를 확인 하는 방법에 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f68d9c7098f2b1ca782e2522c632c2e267b35336
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455103"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>감정 분석 컨테이너 인스턴스를 확인 합니다.

1. 선택 된 **개요** 탭 및 IP 주소를 복사 합니다.
1. 새 브라우저 탭을 열고 예를 들어, IP 주소를 사용 하 여 `http://<IP-address>:5000 (http://55.55.55.55:5000`). 컨테이너의 홈 페이지가 표시 됩니다, 그리고 실행 중인 컨테이너를 알려 줍니다.

    ![실행 중인지 확인 하려면 컨테이너 홈 페이지 보기](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 선택 된 **서비스 API 설명을** 컨테이너 swagger 페이지로 이동 하는 링크입니다.

1. 중 하나를 선택 합니다 **게시물** Api 및 선택 **사용해**합니다.  매개 변수 예제 입력을 포함 하 여 표시 됩니다.

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

1. 다음 JSON을 사용 하 여 입력을 바꿉니다.

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

1. 설정할 **showStats** true로 합니다.

1. 선택 **Execute** 텍스트의 감정을 결정을 합니다.

    모델 컨테이너에 패키지 0에서 1, 여기서 0은 부정, 1은 양의 까지의 점수를 생성 합니다.

    반환 된 JSON 응답은 다음과 같습니다. 업데이트 된 텍스트 입력에 대 한 정서를

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

문서를 이제 상호 연결할 수 있습니다 `id` 원래 요청 페이로드 문서에 대 한 응답 페이로드로 JSON `id`, 점수는 되었는지 확인을 통해 `.98` 매우 긍정적인 감정이 나타내는입니다.