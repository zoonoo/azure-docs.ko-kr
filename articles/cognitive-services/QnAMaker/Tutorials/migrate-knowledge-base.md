---
title: 기술 자료 마이그레이션 - QnA Maker
description: 기술 자료를 마이그레이션하려면 한 기술 자료를 내보낸 다음, 다른 기술 자료로 가져와야 합니다.
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 13e5e79bf4eaf6ec59e41b3e12aa1bb23f2c1578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258093"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>내보내기-가져오기를 사용하여 기술 자료 마이그레이션

마이그레이션은 기존 기술 자료에서 새 기술 기반을 만드는 프로세스입니다. 다음과 같은 여러 가지 이유로 이 작업을 수행할 수 있습니다.

* 백업 및 복원 프로세스
* CI/CD 파이프라인
* 영역 이동

기술 자료를 마이그레이션하려면 기존 기술 자료에서 내보낸 다음 다른 기술 자료로 가져와야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 새 [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md) 설정

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker에서 기술 자료 마이그레이션
1. [QnA Maker 포털](https://qnamaker.ai)에 로그인합니다.
1. 마이그레이션할 원본 기술 기반을 선택합니다.

1. **설정** 페이지에서 **내보내기 기술 자료를** 선택하여 질문, 답변, 메타데이터, 후속 프롬프트 및 추출된 데이터 원본 이름등 원본 기술 자료의 내용을 포함하는 .tsv 파일을 다운로드합니다.

1. 상단 메뉴에서 **기술 자료 만들기를** 선택한 다음 _빈_ 기술 자료 만들기를 선택합니다. URL이나 파일을 추가하지 않으므로 비어 있습니다. 이러한 것은 생성 후 가져오기 단계 중에 추가됩니다.

    기술 자료 구성. 새 기술 자료 이름만 설정합니다. 중복되는 이름과 특수 문자도 지원됩니다.

    파일을 가져올 때 해당 값이 덮어쓰므로 4단계에서 아무 것도 선택하지 마십시오.

1. 5단계에서 는 **만들기를**선택합니다.

1. 이 새 기술 자료에서 **설정** 탭을 열고, **기술 자료 가져오기**를 선택합니다. 이렇게 하면 질문, 답변, 메타데이터, 후속 프롬프트가 가져오고 추출된 데이터 원본 이름이 유지됩니다.

   > [!div class="mx-imgBorder"]
   > [![기술 자료 가져오기](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. 테스트 패널을 사용하여 새 기술 자료를 **테스트**합니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.

1. 기술 기반을 **게시하고** 채팅 봇을 만듭니다. [기술 자료 게시](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 방법을 알아보세요.

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker에서 프로그래밍 방식으로 기술 자료 마이그레이션

마이그레이션 프로세스는 다음 REST API를 사용하여 프로그래밍 방식으로 사용할 수 있습니다.

**내보내기**

* [기술 자료 API 다운로드](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**가져오기**

* [API 바꾸기(동일한 기술 자료 ID로 다시 로드)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [API 만들기(새 기술 자료 ID로 로드)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>채팅 로그 및 변경 내용
대/소문자를 구분하지 않는 변경 내용(동의어)은 자동으로 가져오지 않습니다. [V4 API를](https://go.microsoft.com/fwlink/?linkid=2092179) 사용하여 새 기술 자료의 변경 내용을 이동합니다.

새 기술 자료는 Application Insights를 사용하여 채팅 로그를 저장하기 때문에 채팅 로그를 마이그레이션할 방법이 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 편집](../How-To/edit-knowledge-base.md)
