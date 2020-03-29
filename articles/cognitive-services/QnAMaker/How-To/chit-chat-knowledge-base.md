---
title: QnA Maker 기술 자료에 잡담 추가
titleSuffix: Azure Cognitive Services
description: KB를 만들 때 봇에 사적인 잡담을 추가하면 실제로 대화하는 것 같은 느낌이 들고 몰입도가 높아집니다. QnA Maker를 사용하면 미리 채워진 최고의 잡담 집합을 KB로 손쉽게 추가할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a9a14056e6be62fc1c1b5e542c1a3acceb738eac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221460"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>기술 자료에 잡담 추가

봇에 잡담을 추가하면 실제로 대화하는 것 같은 느낌이 들고 몰입도가 높아집니다. QnA Maker의 잡담 기능을 사용하면 미리 채워진 최고의 잡담 집합을 KB(기술 자료)로 손쉽게 추가할 수 있습니다. 이는 봇 성격의 시작점 역할을 하며, 잡담을 처음부터 작성하는 데 드는 비용과 시간을 많이 절약할 수 있습니다.  

이 데이터 집합에는 전문가, 친절 및 위트와 같은 여러 가상 사용자의 목소리로 약 100 개의 chit-chat 시나리오가 있습니다. 봇의 음성과 가장 유사한 가상 사용자를 선택하세요. QnA Maker는 사용자 쿼리가 지정되면 알려진 잡담 QnA 중 가장 가까운 QnA와 이를 매칭합니다.  

다른 성격의 몇 가지 예는 다음과 같습니다. 당신은 성격의 세부 사항과 함께 모든 성격 [데이터 세트를](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) 볼 수 있습니다.

의 사용자 쿼리의 `When is your birthday?`경우 각 성격에는 스타일이 있는 응답이 있습니다.

<!-- added quotes so acrolinx doesn't score these sentences -->
|성격|예제|
|--|--|
|Professional|나이는 나에게 실제로 적용되지 않습니다.|
|친숙한|난 정말 나이가 없어.|
|재치|나는 나이가 없어.|
|돌보는|나이도 없어요.|
|열정|저는 봇이기 때문에 나이가 없어요.|
||


## <a name="language-support"></a>언어 지원

Chit-chat 데이터 세트는 다음 언어로 지원됩니다.

|언어|
|--|
|중국어|
|영어|
|프랑스어|
|독일|
|이탈리아어|
|일본어|
|한국어|
|포르투갈어|
|스페인어|


## <a name="add-chit-chat-during-kb-creation"></a>KB 생성 중에 잡담 추가
기술 자료 생성 중에 원본 URL 및 파일을 추가하면 잡담을 추가하는 옵션이 생깁니다. 잡담 기반으로 사용하려는 성격을 선택합니다. 잡담을 추가하지 않으려는 경우 또는 이미 데이터 원본에 잡담 지원 기능이 있는 경우 **없음**을 선택합니다. 

## <a name="add-chit-chat-to-an-existing-kb"></a>기존 KB에 잡담 추가
KB를 선택하고 **설정** 페이지로 이동합니다. 적절한 **.tsv** 형식으로 된 모든 잡담 데이터 세트에 대한 링크가 있습니다. 원하는 성격을 다운로드한 다음, 파일 원본으로 업로드하세요. 파일을 다운로드하고 업로드할 때 형식 또는 메타데이터를 편집하지 않도록 하세요. 
  
![기존 KB에 잡담 추가](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>잡담의 질문과 답변 편집
KB를 편집할 때 선택된 성격에 따라 잡담의 새 원본이 표시됩니다. 이제 다른 모든 원본과 마찬가지로 변경된 질문을 추가하거나 응답을 편집할 수 있습니다. 

![잡담 QnA 편집](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

메타데이터를 보려면 도구 모음에서 **옵션 보기를** 선택한 다음 **메타데이터 표시를**선택합니다.

## <a name="add-additional-chit-chat-questions-and-answers"></a>잡담 질문과 답변 추가
미리 정의된 집합에 없는 새로운 잡담 QnA를 추가할 수 있습니다. 잡담 집합에 이미 포함된 QnA 쌍을 복제하지 않도록 하세요. 새로운 잡담 QnA를 추가하면 **Editorial** 원본에 추가됩니다. 랭커가 chit-chat임을 이해하려면 다음 이미지와 같이 메타데이터 키/값 쌍 "에디토리얼: chitchat"을 추가합니다.
   
![! [치트 채팅 QnA 추가] (.. //qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>기존 KB에서 잡담 삭제
KB를 선택하고 **설정** 페이지로 이동합니다. 특정 잠담 원본은 선택된 성격 이름과 함께 파일로 나열됩니다. 이 원본 파일을 삭제할 수 있습니다.

![KB에서 잡담 삭제](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 가져오기](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>참조 

[QnA Maker 개요](../Overview/overview.md)
