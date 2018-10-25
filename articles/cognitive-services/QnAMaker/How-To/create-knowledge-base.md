---
title: 기술 자료 만들기 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 봇에 잡담을 추가하면 실제로 대화하는 것 같은 느낌이 들고 몰입도가 높아집니다. QnA Maker를 사용하면 미리 채워진 최고의 잡담 집합을 KB로 손쉽게 추가할 수 있습니다. 이는 봇 잡담의 시작점 역할을 하며, 잡담을 처음부터 작성하는 데 드는 비용과 시간을 많이 절약할 수 있습니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: c9d36014bc364d8b016221e6b9ff380b0bd4b8ed
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854170"
---
# <a name="create-a-knowledge-base"></a>기술 자료 만들기

QnA Maker를 사용하면 기술 자료를 만들 때 간단하게 기존 데이터 원본을 추가할 수 있습니다. 다음 문서 형식에서 새 QnA Maker 기술 자료를 만들 수 있습니다.

<!-- added for scanability -->
* FAQ 페이지
* 제품 설명서
* 구조화된 문서

## <a name="steps"></a>단계

1. Azure 자격 증명을 사용하여 [QnA Maker 포털](https://qnamaker.ai)에 로그인하고 **기술 자료 문서 만들기**를 선택합니다.

2. 아직 QnA Maker 서비스를 만들지 않은 경우 **QnA 서비스 만들기**를 선택합니다. 

3. QnA Maker 포털의 **2단계**에서 목록의 QnA Maker 서비스와 연결된 Azure 테넌트, Azure 구독 이름 및 Azure 리소스 이름을 선택합니다. 기술 자료를 호스트할 Azure QnA Maker 서비스를 선택합니다.

    ![QnA 서비스 설정](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

4. 기술 자료의 이름 및 새 기술 자료에 대한 데이터 원본을 입력합니다.

    ![데이터 원본 설정](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - 서비스의 **이름**을 지정합니다. 중복 이름 및 특수 문자가 지원됩니다.
    - 추출하려는 데이터에 대한 URL을 추가합니다. 지원되는 원본 형식에 대한 자세한 내용은 [여기](../Concepts/data-sources-supported.md)서 확인할 수 있습니다.
    - 추출하려는 데이터에 대한 파일을 업로드합니다. 추가할 수 있는 문서 수는 [가격 책정 정보](https://aka.ms/qnamaker-pricing)를 참조하세요.
    - QnA를 수동으로 추가하려는 경우 이전 이미지에 표시된 **4단계**를 건너뜁니다.

5. KB에 **잡담**을 추가합니다. 3개의 미리 정의된 개성 중에서 하나를 선택하여 봇에 잡담 지원을 추가하도록 선택합니다. 

    <!-- TBD: add back in when chit chat how-to is merged
    ![Add chit-chat to KB ](../media/qnamaker-how-to-chitchat/create-kb-chit-chat.png)
    -->

6. **KB 만들기**를 선택합니다.

    ![기술 자료 만들기](../media/qnamaker-how-to-create-kb/create-kb.png)

7. 데이터를 추출할 때까지 몇 분 정도 걸립니다.

    ![추출](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

8. 기술 자료가 성공적으로 만들어지면 **기술 자료** 페이지로 리디렉션됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [잡담 개성 추가](./chit-chat-knowledge-base.md)
