---
title: 기술 자료 마이그레이션 - QnA Maker
description: 기술 자료를 마이그레이션하려면 한 기술 자료를 내보낸 다음, 다른 기술 자료로 가져와야 합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: 8e1cff5a82870c5491b262f5ced95f688d5ee4ac
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110369513"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>내보내기-가져오기를 사용하여 기술 자료 마이그레이션

마이그레이션은 기존 기술 자료에서 새 기술 자료 기반을 만드는 프로세스입니다. 다음과 같은 이유로 이 작업을 수행할 수 있습니다.

* 백업 및 복원 프로세스
* CI/CD 파이프라인
* 지역 이동

기술 자료를 마이그레이션하려면 기존 기술 자료를 내보냈다가 다른 기술 자료로 가져와야 합니다.

> [!NOTE]
> 아래 지침에 따라 기존 기술 자료를 관리되는 새 QnA Maker(미리 보기)로 마이그레이션합니다.

## <a name="prerequisites"></a>필수 구성 요소

> * Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/cognitive-services/) 계정을 만듭니다.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

> * Azure Portal에서 만든 [QnA Maker 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)입니다. 리소스를 만들 때 선택한 Azure Active Directory ID, 구독, QnA 리소스 이름을 기억하세요.
> * 새 [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md) 설정

# <a name="custom-question-answering-preview-release"></a>[사용자 지정 질문 답변(미리 보기 릴리스)](#tab/v2)

> * Azure Portal에서 사용자 지정 질문 답변 기능이 활성화된 [Text Analytics 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)입니다. 리소스를 만들 때 선택한 Azure Active Directory ID, 구독 및 Text Analytics 리소스 이름을 기억하세요.
> * 새 [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md) 설정

---

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker에서 기술 자료 마이그레이션
1. [QnA Maker 포털](https://qnamaker.ai)에 로그인합니다.
1. 마이그레이션할 기술 자료를 선택합니다.

1. **설정** 페이지에서 **기술 자료 내보내기** 를 선택하여 원본 기술 자료의 콘텐츠(질문, 답변, 메타데이터, 후속 프롬프트 및 추출된 데이터 원본 이름)가 포함된 .tsv 파일을 다운로드합니다. 질문 및 답변과 함께 내보낸 QnA ID는 [업데이트 API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update)를 사용하여 특정 QnA 쌍을 업데이트하는 데 사용할 수 있습니다. 특정 QnA 쌍의 QnA ID는 여러 내보내기 작업에서 변경되지 않고 유지됩니다.

1. 위쪽 메뉴에서 **기술 자료 만들기** 를 선택한 다음 빈 기술 자료를 만듭니다. 이 기술 자료는 만들 때 URL 또는 파일을 추가하지 않기 때문에 비어 있습니다. 이러한 항목은 만든 후 가져오기 단계에서 추가됩니다.

    기술 자료를 구성합니다. 새 기술 자료 이름만 설정합니다. 중복되는 이름과 특수 문자도 지원됩니다.

    파일을 가져올 때 해당 값을 덮어쓰므로 4단계에서는 아무것도 선택하지 마세요.

1. 5단계에서 **만들기** 를 선택합니다.

1. 이 새 기술 자료에서 **설정** 탭을 열고, **기술 자료 가져오기** 를 선택합니다. 이렇게 하면 질문, 답변, 메타데이터 및 후속 프롬프트를 가져오고 추출된 데이터 원본 이름이 유지됩니다. **새 기술 자료에서 만든 QnA 쌍은 내보낸 파일에 있는 것과 동일한 QnA ID를 가져야 합니다.** 그러면 기술 자료의 정확한 복제본을 만들 수 있습니다.

   > [!div class="mx-imgBorder"]
   > [![기술 자료 가져오기](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. 테스트 패널을 사용하여 새 기술 자료를 **테스트** 합니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.

1. 기술 자료를 **게시** 하고 채팅 봇을 만듭니다. [기술 자료 게시](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 방법을 알아보세요.

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker에서 기술 자료를 프로그래밍 방식으로 마이그레이션

마이그레이션 프로세스는 다음 REST API를 사용하여 프로그래밍 방식으로 실행할 수 있습니다.

**내보내기**

* [기술 자료 다운로드 API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**가져오기**

* [API 바꾸기(동일한 기술 자료 ID로 다시 로드)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [API 만들기(새 기술 자료 ID로 로드)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>채팅 로그 및 변경 내용
대/소문자를 구분하지 않는 변경 내용(동의어)은 자동으로 가져오지 않습니다. [V4 API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)를 사용하여 새 기술 자료에서 변경 내용을 이동합니다.

새 기술 자료는 Application Insights를 사용하여 채팅 로그를 저장하기 때문에 채팅 로그를 마이그레이션할 방법이 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 편집](../How-To/edit-knowledge-base.md)