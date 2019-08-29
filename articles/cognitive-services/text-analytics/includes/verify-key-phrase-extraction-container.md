---
title: 핵심 구 추출 컨테이너 인스턴스 확인
titleSuffix: Azure Cognitive Services
description: 핵심 구 추출 컨테이너 인스턴스를 확인 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: 1a14ea186b3e1127928a36600d1047a633aea568
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70052117"
---
## <a name="verify-the-key-phrase-extraction-container-instance"></a>핵심 구 추출 컨테이너 인스턴스 확인

1. **개요** 탭을 선택 하 고 IP 주소를 복사 합니다.
1. 새 브라우저 탭을 열고 IP 주소를 입력 합니다. 예를 들어를 `http://<IP-address>:5000 (http://55.55.55.55:5000`입력 합니다. 컨테이너의 홈 페이지가 표시 됩니다. 그러면 컨테이너가 실행 중임을 알 수 있습니다.

    ![컨테이너 홈 페이지를 확인 하 여 실행 중인지 확인 합니다.](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. **서비스 API 설명** 링크를 선택 하 여 컨테이너의 Swagger 페이지로 이동 합니다.

1. **POST** api 중 하나를 선택 하 고 **사용해 보기**를 선택 합니다. 이 예제 입력을 포함 하는 매개 변수가 표시 됩니다.

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

1. **Showstats** 를로 `true`설정 합니다.

1. **실행** 을 선택 하 여 텍스트의 감정 확인 합니다.

    컨테이너에 패키지 된 모델은 0에서 1 사이의 점수를 생성 합니다. 여기서 0은 음수이 고 1은 양수입니다.

    반환 되는 JSON 응답에는 업데이트 된 텍스트 입력에 대 한 감정 포함 됩니다.

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

이제 응답 페이로드의 JSON 데이터 문서 `id` 를 원래 요청 페이로드 문서 `id`와의 상관 관계를 지정할 수 있습니다. 결과 문서 `keyPhrases` 에는 해당 입력 문서에서 추출 된 키 구 목록이 포함 된 배열이 있습니다. 또한 각 결과 문서에 대해 및 `characterCount` `transactionCount` 와 같은 다양 한 통계가 있습니다.