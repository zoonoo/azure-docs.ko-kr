---
title: QnA Maker 기술 자료에 잡담 추가
titleSuffix: Azure Cognitive Services
description: KB를 만들 때 봇에 사적인 잡담을 추가하면 실제로 대화하는 것 같은 느낌이 들고 몰입도가 높아집니다. QnA Maker를 사용하면 미리 채워진 최고의 잡담 집합을 KB로 손쉽게 추가할 수 있습니다. 이는 봇 잡담의 시작점 역할을 하며, 잡담을 처음부터 작성하는 데 드는 비용과 시간을 많이 절약할 수 있습니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/24/2018
ms.author: tulasim
ms.openlocfilehash: ddd78cbfff2b1ee450a26306dce528cd89fea788
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50228927"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>기술 자료에 잡담 추가

봇에 잡담을 추가하면 실제로 대화하는 것 같은 느낌이 들고 몰입도가 높아집니다. QnA Maker의 잡담 기능을 사용하면 미리 채워진 최고의 잡담 집합을 KB(기술 자료)로 손쉽게 추가할 수 있습니다. 이는 봇 성격의 시작점 역할을 하며, 잡담을 처음부터 작성하는 데 드는 비용과 시간을 많이 절약할 수 있습니다.  

이 데이터 세트에는 The Professional, The Friend, The Comic이라는 세 가지 가상 사용자 목소리로 녹음된 약 100개의 잡담 시나리오가 있습니다. 봇의 음성과 가장 유사한 가상 사용자를 선택하세요. QnA Maker는 사용자 쿼리가 지정되면 알려진 잡담 QnA 중 가장 가까운 QnA와 이를 매칭합니다. 

여러 성격의 몇 가지 예: <!-- added quotes so acrolinx doesn't score these sentences -->
|사용자 쿼리|The Professional|친구|The Comic|
|--|--|--|--|
|`You are awesome`|`I aim to serve.`|`That's so nice of you!`|`Flattery. I like it.`|
|`Are you hungry?`|`I don't need to eat.`|`I only do food for thought.`|`Eating would require a lot of things I don't have. Like a digestive system. And silverware.`|
|`Sing a song`|`I'm afraid I'm not musically inclined.`|`La la la, tra la la. I'm awesome at this.`|`You can't handle my dulcet tones.`|
|`Will you marry me?`|`I think it's best if we stick to a professional relationship.`|`Aw, that's sweet.`|`Sure. Take me to city hall. See what happens.`|


> [!NOTE]
> 잡담 지원은 현재 영어로만 제공됩니다. 

## <a name="add-chit-chat-during-kb-creation"></a>KB 생성 중에 잡담 추가
기술 자료 생성 중에 원본 URL 및 파일을 추가하면 잡담을 추가하는 옵션이 생깁니다. 잡담 기반으로 사용하려는 성격을 선택합니다. 잡담을 추가하지 않으려는 경우 또는 이미 데이터 원본에 잡담 지원 기능이 있는 경우 **없음**을 선택합니다. 
   
![생성 중에 잡담 추가](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>기존 KB에 잡담 추가
KB를 선택하고 **설정** 페이지로 이동합니다. 적절한 **.tsv** 형식으로 된 모든 잡담 데이터 세트에 대한 링크가 있습니다. 원하는 성격을 다운로드한 다음, 파일 원본으로 업로드하세요. 파일을 다운로드하고 업로드할 때 형식 또는 메타데이터를 편집하지 않도록 하세요. 
  
![기존 KB에 잡담 추가](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>잡담의 질문과 답변 편집
KB를 편집할 때 선택된 성격에 따라 잡담의 새 원본이 표시됩니다. 이제 다른 모든 원본과 마찬가지로 변경된 질문을 추가하거나 응답을 편집할 수 있습니다. 

![잡담 QnA 편집](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

## <a name="add-additional-chit-chat-questions-and-answers"></a>잡담 질문과 답변 추가
미리 정의된 집합에 없는 새로운 잡담 QnA를 추가할 수 있습니다. 잡담 집합에 이미 포함된 QnA 쌍을 복제하지 않도록 하세요. 새로운 잡담 QnA를 추가하면 **Editorial** 원본에 추가됩니다. 평가자가 잡담이라고 이해할 수 있도록 다음 이미지에 보이는 것과 같이 메타데이터 키/값 쌍 "Editorial: 잡담"을 추가하세요.
   
![잡담 QnA 추가](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)

## <a name="delete-chit-chat-from-an-existing-kb"></a>기존 KB에서 잡담 삭제
KB를 선택하고 **설정** 페이지로 이동합니다. 특정 잠담 원본은 선택된 성격 이름과 함께 파일로 나열됩니다. 이 원본 파일을 삭제할 수 있습니다.

![KB에서 잡담 삭제](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 가져오기](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>참고 항목 

[QnA Maker 개요](../Overview/overview.md)
