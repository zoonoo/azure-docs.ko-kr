---
title: 미리 보기 기술 자료 마이그레이션 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 무료 미리 보기로 만들어진 기술 자료는 QnA Maker GA로 마이그레이션해야 합니다. QnA Maker 미리 보기는 2019년 1월에 지원 중단될 예정입니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 261d42a6c21681ee8015edfe73ad78b4458eb0ee
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078816"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>내보내기-가져오기를 사용하여 기술 자료 마이그레이션
QnA Maker는 2018년 5월 7일에 \\\build\ 컨퍼런스에서 일반 공급을 발표했습니다. QnA Maker GA에는 Azure에 빌드된 새로운 아키텍처가 있습니다. QnA Maker 무료 미리 보기로 만들어진 기술 자료는 QnA Maker GA로 마이그레이션해야 합니다. QnA Maker 미리 보기는 2019년 1월에 지원 중단될 예정입니다. QnA Maker GA의 변경 내용에 대한 자세한 내용은 QnA Maker GA 알림 [블로그 게시물](https://aka.ms/qnamakerga-blog)을 참조하세요.

이제 QnA Maker에는 [가격 모델](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)이 있습니다.

필수 조건
> [!div class="checklist"]
> * Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
> * 새 [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md) 설정

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>QnA Maker 미리 보기 포털에서 기술 자료 마이그레이션
1. [QnA Maker 미리 보기 포털](https://aka.ms/qnamaker-old-portal
)로 이동하여 **내 서비스**를 클릭합니다.
2. 편집 아이콘을 클릭하여 마이그레이션할 기술 자료를 선택합니다.

    ![기술 자료 편집](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. **기술 자료 다운로드**를 클릭하여 기술 자료의 콘텐츠(질문, 답변, 메타데이터 및 추출된 데이터 원본 이름)가 포함된 .tsv 파일을 다운로드합니다.

    ![기술 자료 다운로드](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Azure 자격 증명을 사용하여 [QnA Maker 포털](https://qnamaker.ai)에 로그인하고 **기술 자료 문서 만들기**를 클릭합니다.
    
5. 아직 QnA Maker 서비스를 만들지 않은 경우 **QnA 서비스 만들기**를 선택합니다. 이미 만들었으면 2단계의 드롭다운 목록에서 QnA Maker 서비스를 선택합니다. 기술 자료를 호스트할 QnA Maker 서비스를 선택합니다.

    ![QnA 서비스 설정](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. 빈 기술 자료 만들기 

    ![데이터 원본 설정](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - 서비스의 **이름**을 지정합니다. 중복되는 이름과 특수 문자도 지원됩니다.
    - 미리 보기 기술 자료의 데이터를 사용하려고 하므로 파일 또는 URL 업로드를 건너뜁니다. 이제 빈 기술 자료를 만듭니다.

7. **만들기**를 선택합니다.

    ![기술 자료 만들기](../media/qnamaker-how-to-create-kb/create-kb.png)

8. 이 새 기술 자료에서 **설정** 탭을 열고 **기술 자료 가져오기**를 클릭합니다. 이렇게 하면 질문, 답변 및 메타데이터를 가져오고 이러한 항목이 추출된 데이터 원본 이름이 유지됩니다.

   ![기술 자료 가져오기](../media/qnamaker-how-to-migrate-kb/Import.png)

9. 테스트 패널을 사용하여 새 기술 자료를 **테스트**합니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.
10. 기술 자료를 **게시**합니다. [기술 자료 게시](../How-To/publish-knowledge-base.md) 방법을 알아보세요.
11. 응용 프로그램 또는 봇 코드에서 아래 엔드포인트를 사용합니다. 여기에서 [QnA 봇을 만드는](../Tutorials/create-qna-bot.md) 방법을 참조하세요.

    ![QnA Maker 값](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

이때 모든 기술 자료 콘텐츠인 질문, 답변 및 메타데이터와 URL 및 원본 파일의 이름을 새 기술 자료로 가져옵니다. 

## <a name="chatlogs-and-alterations"></a>채팅 로그 및 변경 내용
변경 내용(동의어)은 자동으로 가져오지 않습니다. [V2 API](https://aka.ms/qnamaker-v2-apis)를 사용하여 미리 보기 스택의 변경 내용을 내보내고 [V4 API](https://aka.ms/qnamaker-v4-apis)를 사용하여 새 스택에서 바꿉니다.

새 스택은 채팅 로그를 저장하는 데 Application Insights를 사용하므로 채팅 로그를 마이그레이션할 방법이 없습니다. 그러나 [미리 보기 포털](https://aka.ms/qnamaker-old-portal)에서 채팅 로그를 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 편집](../How-To/edit-knowledge-base.md)
