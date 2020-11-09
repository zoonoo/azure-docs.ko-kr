---
title: 응답 범위 검색을 사용 하는 정확한 응답-QnA Maker
description: QnA Maker 관리에서 사용할 수 있는 정확한 응답 기능을 이해 합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 4f64bab698cb87e26fa4fd1587c4269acf99fa59
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94384107"
---
# <a name="precise-answering"></a>정확한 응답

정확한 응답 기능을 사용 하면 기술 자료에 있는 모든 사용자 쿼리에 대 한 최상의 후보 대답 통로에서 정확한 짧은 답을 얻을 수 있습니다. 이 기능은 런타임에 대 한 심층 학습 모델을 사용 합니다 .이 모델은 사용자 쿼리의 의도를 이해 하 고 답변 통로에 팩트로 표시 되는 짧은 답변이 있는 경우 대답 통로에서 정확한 짧은 답을 검색 합니다. 

이 기능은 테스트 창에 기본적으로 설정 되어 있으므로 시나리오와 관련 된 기능을 테스트할 수 있습니다. 이 기능은 콘텐츠 개발자와 최종 사용자 모두에 게 매우 유용 합니다. 이제 콘텐츠 개발자는 기술 자료에 있는 모든 팩트에 대해 특정 QnA 쌍을 수동으로 지정 하지 않아도 되며, 최종 사용자는 서비스에서 반환 되는 전체 대답 통로를 확인 하 여 사용자의 쿼리에 대답 하는 실제 팩트를 찾을 필요가 없습니다. 

## <a name="precise-answering-on-qna-maker-portal"></a>QnA Maker 포털에서 정확한 응답

QnA Maker 포털에서 테스트 창을 열면 위쪽에 **짧은 답을 표시** 하는 옵션이 표시 됩니다. 이 옵션은 기본적으로 선택 됩니다. 테스트 창에 쿼리를 입력 하면 대답 통로에 짧은 대답이 있는 경우 대답 통로와 함께 짧은 답이 표시 됩니다. 
 
![관리 되는 활성화 된 테스트 창](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

테스트 창에서 대답 통로만 표시 하려는 경우 **간단한 대답 표시** 옵션의 선택을 취소할 수 있습니다. 

또한이 서비스는 테스트 창에서 쿼리 바로 아래에 있는 **검사** 옵션을 선택 하 여 확인할 수 있는 **대답 범위 점수** 와 정확히 대답의 신뢰성 점수를 다시 반환 합니다.

![관리 되는 응답 범위 점수](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>QnA Maker bot 게시

Bot을 게시 하면 응용 프로그램에서 기본적으로 정확한 응답을 사용 하도록 설정 된 환경을 얻게 되며 여기에서 대답 통로와 함께 짧은 답을 확인할 수 있습니다. 사용자는 그 다음 eBot app service를 통해 템플릿을 업데이트 하 여 다른 환경을 유연 하 게 선택할 수 있습니다. 

## <a name="language-support"></a>언어 지원

현재 정확한 답변 기능은 영어 에서만 지원 됩니다.
