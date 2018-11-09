---
title: FAQ - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 서비스에 대한 FAQ(질문과 대답) 목록입니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 10/25/2018
ms.author: tulasim
ms.openlocfilehash: 9597b878eb3d92727b352ba42a9e5557bb1cc799
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211437"
---
# <a name="frequently-asked-questions"></a>질문과 대답

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>URL/파일이 질문-답변 쌍을 추출하지 않는 이유는 무엇인가요?

QnA Maker가 유효한 FAQ URL에서 일부 QnA(질문-답변) 콘텐츠를 자동으로 추출하지 못할 수 있습니다. 이러한 경우 QnA 콘텐츠를 .txt 파일에 붙여넣고 도구에서 수집할 수 있는지 확인할 수 있습니다. 또는 편집자로서 [QnA Maker 포털](https://qnamaker.ai)을 통해 기술 자료에 콘텐츠를 추가할 수 있습니다.

## <a name="how-large-a-knowledge-base-can-i-create"></a>만들 수 있는 기술 자료 크기는 얼마인가요?

기술 자료 크기는 QnA Maker 서비스를 만들 때 선택한 Azure Search의 SKU에 따라 다릅니다. 자세한 내용은 [여기](./Tutorials/choosing-capacity-qnamaker-deployment.md)를 참조하세요.

## <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>새 기술 자료를 만들려고 할 때 드롭다운에 아무것도 표시될 수 없는 이유는 무엇인가요?

Azure에서 QnA Maker 서비스를 아직 만들지 않았습니다. 만드는 방법에 대해 자세히 알아보려면 [여기](./How-To/set-up-qnamaker-service-azure.md)를 참조하세요.

## <a name="how-do-i-share-a-knowledge-base-with-others"></a>기술 자료를 다른 사용자와 공유하려면 어떻게 하나요?

공유는 QnA Maker 서비스 수준에서 작동합니다. 즉, 서비스의 모든 기술 자료가 공유됩니다. 기술 자료에 대한 공동 작업 방법은 [여기](./How-To/collaborate-knowledge-base.md)를 참조하세요.

## <a name="can-you-share-a-kb-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-kb"></a>동일한 AAD 테넌트에 있지 않은 기여자와 KB를 공유하여 KB를 수정할 수 있나요? 

공유는 Azure RBAC(역할 기반 Access Control)에 따릅니다. 다른 사용자와 Azure의 _모든_ 리소스를 공유할 수 있는 경우 QnA Maker도 공유할 수 있습니다.

## <a name="if-you-have-an-app-service-plan-with-5-qnamaker-kbs-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-kb"></a>5개 QnAMaker KB가 포함된 App Service 계획이 있는 경우. 5명의 각각 다른 사용자가 하나의 QnAMaker KB에만 액세스할 수 있도록 해당 사용자에게 읽기/쓰기 권한을 할당할 수 있나요?

개별 KB가 아닌 전체 QnAMaker 서비스를 공유할 수 있습니다.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>양호한 일치 항목이 없는 경우 기본 메시지를 변경하려면 어떻게 하나요?

기본 메시지는 App Service 설정의 일부입니다.
- Azure Portal의 App Service 리소스로 이동

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- **설정** 옵션 클릭

![qnamaker appservice 설정](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- **DefaultAnswer** 설정 값 변경
- App Service 다시 시작

![qnamaker appservice 다시 시작](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>SharePoint 링크가 추출되지 않는 이유는 무엇인가요?

이 도구는 공용 URL만 구문 분석하며, 지금은 인증된 데이터 원본을 지원하지 않습니다. 또는 파일을 다운로드하고 파일 업로드 옵션을 사용하여 질문과 답변을 추출할 수 있습니다.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>기술 자료에 수행한 업데이트가 게시에 반영되지 않습니다. 왜 그런가요?

테이블 업데이트, 테스트 또는 설정이든 상관없이 모든 편집 작업은 먼저 저장해야 게시할 수 있습니다. 모든 편집 작업 후에는  **저장 후 학습**  단추를 클릭해야 합니다.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>엔드포인트 키를 새로 고쳐야 하는 경우는 언제인가요?

엔드포인트 키가 손상되었다고 의심되는 경우 엔드포인트 키를 새로 고칩니다.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>기술 자료는 풍부한 데이터 또는 멀티미디어를 지원하나요?

기술 자료는 Markdown을 지원합니다. 그러나 URL에서 자동 추출하는 경우 HTML에서 Markdown으로 변환 기능이 제한됩니다. 모든 기능이 갖춰진 Markdown을 사용하려는 경우 표에서 바로 콘텐츠를 수정하거나, 서식 있는 콘텐츠가 포함된 기술 자료를 업로드할 수 있습니다.

이미지 및 비디오와 같은 멀티미디어는 현재 지원되지 않습니다.

## <a name="does-qna-maker-support-non-english-languages"></a>QnA Maker에서 영어 이외의 언어를 지원하나요?

[지원되는 언어](./Overview/languages-supported.md)에 대한 자세한 내용을 참조하세요.

여러 언어의 콘텐츠가 있는 경우 각 언어마다 별도의 서비스를 만들어야 합니다.

## <a name="can-i-use-the-same-azure-search-resource-for-kbs-using-multiple-languages"></a>여러 언어를 사용하여 KB에 대한 동일한 Azure Search 리소스를 사용할 수 있나요?

여러 언어 및 여러 KB를 사용하려면 사용자는 각 언어에 대한 QnA Maker 리소스를 만들어야 합니다. 이렇게 하면 언어당 별도 Azure Search 서비스를 만들게 됩니다. 단일 Azure Search 서비스에서 다른 언어로 된 KB를 혼합하면 결국 성능이 저하된 결과의 관련성이 발생합니다.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>QnA Maker를 사용하기 위해 Bot Framework를 사용해야 하나요?

아니요, QnA Maker와 함께 Bot Framework를 사용하지 않아도 됩니다. 그러나 QnA Maker는 Azure Bot Service에서 여러 템플릿 중 하나로 제공됩니다. Bot Service는 Microsoft Bot Framework를 통해 지능형 봇의 신속한 개발을 사용하도록 설정하고, 서버리스 환경에서 실행됩니다.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>QnA Maker를 사용하여 봇을 만들려면 어떻게 하나요?

[이](./Tutorials/create-qna-bot.md) 문서의 지침에 따라 Azure Bot Service를 사용하여 봇을 만듭니다.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>웹 사이트에 QnA Maker 서비스를 포함하려면 어떻게 하나요?

다음 단계를 수행하여 QnA Maker 서비스를 웹 사이트에 웹 채팅 컨트롤로 포함합니다.

1. [여기](./Tutorials/create-qna-bot.md)에 제공된 지침을 따라 FAQ 봇을 만듭니다.
2. [여기](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)에 제공된 단계를 따라 웹 채팅을 사용하도록 설정합니다.


