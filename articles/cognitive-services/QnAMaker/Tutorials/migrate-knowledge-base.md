---
title: 기술 자료 마이그레이션 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 기술 자료를 마이그레이션하려면 한 기술 자료를 내보낸 다음, 다른 기술 자료로 가져와야 합니다.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 8ff3c497372a761bd8a02ae81bc897c8ee297bd0
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794877"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>내보내기-가져오기를 사용하여 기술 자료 마이그레이션

기술 자료를 마이그레이션하려면 한 기술 자료를 내보낸 다음, 다른 기술 자료로 가져와야 합니다. 

## <a name="prerequisites"></a>필수 조건

* 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 새 [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md) 설정

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker에서 기술 자료 마이그레이션
1. [QnA Maker 포털](https://qnamaker.ai)에 로그인합니다.
1. 마이그레이션할 기술 자료를 선택합니다.

1. **설정** 페이지에서 **기술 자료 내보내기**를 선택하여 기술 자료의 콘텐츠(질문, 답변, 메타데이터 및 추출된 데이터 원본 이름)가 포함된 .tsv 파일을 다운로드합니다.

1. 위쪽 메뉴에서 **기술 자료 만들기**를 선택한 다음, 빈 기술 자료를 만듭니다. 

    ![데이터 원본 설정](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - 서비스의 **이름**을 지정합니다. 중복되는 이름과 특수 문자도 지원됩니다.

1. **만들기**를 선택합니다.

    ![기술 자료 만들기](../media/qnamaker-how-to-create-kb/create-kb.png)

1. 이 새 기술 자료에서 **설정** 탭을 열고, **기술 자료 가져오기**를 선택합니다. 이렇게 하면 질문, 답변 및 메타데이터를 가져오고 이러한 항목이 추출된 데이터 원본 이름이 유지됩니다.

   ![기술 자료 가져오기](../media/qnamaker-how-to-migrate-kb/Import.png)

1. 테스트 패널을 사용하여 새 기술 자료를 **테스트**합니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.
1. 기술 자료를 **게시**합니다. [기술 자료 게시](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 방법을 알아보세요.
1. 애플리케이션 또는 봇 코드에서 엔드포인트를 사용합니다. 여기에서 [QnA 봇을 만드는](../Tutorials/create-qna-bot.md) 방법을 참조하세요.

    ![QnA Maker 값](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    이때 모든 기술 자료 콘텐츠인 질문, 답변 및 메타데이터와 URL 및 원본 파일의 이름을 새 기술 자료로 가져옵니다. 

## <a name="chat-logs-and-alterations"></a>채팅 로그 및 변경 내용
대/소문자를 구분하지 않는 변경 내용(동의어)은 자동으로 가져오지 않습니다. 사용 된 [V4 Api](https://go.microsoft.com/fwlink/?linkid=2092179) 새 기술 자료에서 변경 작업을 이동 합니다.

새 기술 자료는 Application Insights를 사용하여 채팅 로그를 저장하기 때문에 채팅 로그를 마이그레이션할 방법이 없습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 편집](../How-To/edit-knowledge-base.md)
