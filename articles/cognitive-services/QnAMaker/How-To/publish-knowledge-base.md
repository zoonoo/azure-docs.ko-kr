---
title: 기술 자료 게시
titleSuffix: QnA Maker API - Azure Cognitive Services
description: 기술 자료를 질문 응답 끝점으로 사용할 수 있도록 하는 마지막 단계는 QnA Maker API 서비스를 사용 하 여 기술 자료를 게시 합니다. 기술 자료를 게시하면 기술 자료의 QnA 콘텐츠가 테스트 인덱스에서 Azure Search의 프로덕션 인덱스로 이동합니다.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c65654e7d6b2e66a07116ea9555ed316ace88912
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091956"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>QnA Maker API 서비스 포털을 사용 하 여 기술 자료 게시

기술 자료를 질문 응답 끝점으로 사용할 수 있도록 하는 마지막 단계는 QnA Maker API 서비스를 사용 하 여 기술 자료를 게시 합니다. 

기술 자료를 게시하면 기술 자료의 QnA 콘텐츠가 테스트 인덱스에서 Azure Search의 프로덕션 인덱스로 이동합니다.

![게시 프로덕션 테스트 인덱스](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="publish-a-knowledge-base"></a>기술 자료 게시

1. KB 변경을 마쳤으면 맨 위 탐색 모음에서 **게시**를 선택합니다. Azure Search에 대해 할당된 기술 자료 수까지 게시할 수 있습니다. 

    ![기술 자료 게시](../media/qnamaker-how-to-publish-kb/publish.png)

2. 다시 **게시**를 선택하여 애플리케이션 또는 봇 코드에 사용할 수 있는 엔드포인트 세부 정보를 확인합니다.

    ![성공적으로 게시된 기술 자료](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>리소스 정리

기술 자료를 사용한 작업이 완료되면 QnA Maker 포털에서 제거합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료에 대한 분석 가져오기](./get-analytics-knowledge-base.md)
