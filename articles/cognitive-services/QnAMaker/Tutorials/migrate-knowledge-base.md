---
title: 기술 자료 마이그레이션 - QnA Maker
description: 기술 자료를 마이그레이션하려면 한 기술 자료를 내보낸 다음, 다른 기술 자료로 가져와야 합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: 78e22a9ec150435c1bc83873a2fcf0a00560c0c1
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94375961"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>내보내기-가져오기를 사용하여 기술 자료 마이그레이션

마이그레이션은 기존 기술 자료를 기반으로 새 기술 자료를 만드는 프로세스입니다. 다음과 같은 여러 가지 이유로이 작업을 수행할 수 있습니다.

* 백업 및 복원 프로세스
* CI/CD 파이프라인
* 영역 이동

기술 자료를 마이그레이션하려면 기존 기술 자료에서 내보낸 다음 다른 기술 자료로 가져와야 합니다.

> [!NOTE]
> 아래 지침에 따라 기존 기술 자료를 관리 되는 새 QnA Maker (미리 보기)로 마이그레이션합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.
* 새 [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md) 설정

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker에서 기술 자료 마이그레이션
1. [QnA Maker 포털](https://qnamaker.ai)에 로그인 합니다.
1. 마이그레이션할 원본 기술 자료를 선택 합니다.

1. **설정** 페이지에서 **기술 자료 내보내기** 를 선택 하 여 원래 기술 자료의 내용, 질문, 답변, 메타 데이터, 추가 작업 프롬프트 및 추출 된 데이터 원본 이름을 포함 하는 tsv 파일을 다운로드 합니다. 질문 및 답변을 사용 하 여 내보낸 QnA Id는 [업데이트 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)를 사용 하 여 특정 QnA 쌍을 업데이트 하는 데 사용할 수 있습니다. 특정 QnA 쌍의 QnA ID는 여러 내보내기 작업에서 변경 되지 않은 상태로 유지 됩니다.

1. 상단 메뉴에서 **기술 자료 만들기** 를 선택 하 고 _빈_ 기술 자료를 만듭니다. 이 파일은 만들 때 Url 또는 파일을 추가 하지 않기 때문에 비어 있습니다. 이러한 작업은 생성 후 가져오기 단계에서 추가 됩니다.

    기술 자료를 구성 합니다. 새 기술 자료 이름만 설정 합니다. 중복되는 이름과 특수 문자도 지원됩니다.

    파일을 가져올 때 해당 값을 덮어쓰기 때문에 4 단계에서 아무것도 선택 하지 마십시오.

1. 5 단계에서 **만들기** 를 선택 합니다.

1. 이 새 기술 자료에서 **설정** 탭을 열고, **기술 자료 가져오기** 를 선택합니다. 그러면 질문, 답변, 메타 데이터, 추가 작업 프롬프트가 가져오며 추출 된 데이터 원본 이름이 유지 됩니다. **새 기술 자료에서 만든 QnA 쌍은 내보낸 파일에 있는 것과 동일한 QNA ID를 가져야** 합니다. 이렇게 하면 기술 자료의 정확한 복제본을 만들 수 있습니다.

   > [!div class="mx-imgBorder"]
   > [![기술 자료 가져오기](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. 테스트 패널을 사용하여 새 기술 자료를 **테스트** 합니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.

1. 기술 자료를 **게시** 하 고 채팅 봇을 만듭니다. [기술 자료 게시](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 방법을 알아보세요.

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker에서 기술 자료를 프로그래밍 방식으로 마이그레이션

마이그레이션 프로세스는 다음 REST Api를 사용 하 여 프로그래밍 방식으로 사용할 수 있습니다.

**내보내기**

* [기술 자료 API 다운로드](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**가져오기**

* [API 바꾸기 (동일한 기술 자료 ID로 다시 로드)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [API 만들기 (새 기술 자료 ID로 로드)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>채팅 로그 및 변경 내용
대/소문자를 구분하지 않는 변경 내용(동의어)은 자동으로 가져오지 않습니다. [V4 api](https://go.microsoft.com/fwlink/?linkid=2092179) 를 사용 하 여 새 기술 자료의 변경 사항을 이동 합니다.

새 기술 자료는 Application Insights를 사용하여 채팅 로그를 저장하기 때문에 채팅 로그를 마이그레이션할 방법이 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 편집](../How-To/edit-knowledge-base.md)
