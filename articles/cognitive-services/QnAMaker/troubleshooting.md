---
title: 문제 해결-QnA Maker
description: QnA Maker 서비스와 관련 하 여 가장 자주 묻는 질문의 큐 레이트 목록은 서비스를 더 빠르게 채택 하 고 더 나은 결과를 생성 하는 데 도움이 됩니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: 6bf9eb5fbb8604bca67279f5f41c8aed19a510c0
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376879"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA Maker에 대 한 문제 해결

QnA Maker 서비스와 관련 하 여 가장 자주 묻는 질문의 큐 레이트 목록은 서비스를 더 빠르게 채택 하 고 더 나은 결과를 생성 하는 데 도움이 됩니다.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>예측 관리

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)

<details>
<summary><b>쿼리 예측의 처리량 성능을 향상하려면 어떻게 해야 하나요?</b></summary>

**답변** : 처리량 성능 문제는 App service와 Cognitive Search에 대해 모두 확장 해야 함을 의미 합니다. 성능을 향상 시키려면 Cognitive Search에 복제본을 추가 하는 것이 좋습니다.

[가격 책정 계층](Concepts/azure-resources.md)에 대해 자세히 알아보세요.
</details>

<details>
<summary><b>QnAMaker 서비스 끝점을 가져오는 방법</b></summary>

**답변** : QnAMaker service 끝점은 QnAMaker 지원 또는 UserVoice에 문의할 때 디버깅 목적으로 유용 합니다. 끝점은 형식으로 된 URL입니다 `https://your-resource-name.azurewebsites.net` .

1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스(리소스 그룹)로 이동

    ![Azure Portal의 QnAMaker Azure 리소스 그룹](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. QnA Maker 리소스와 연결 된 App Service를 선택 합니다. 일반적으로 이름은 동일 합니다.

     ![QnAMaker App Service 선택](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 끝점 URL은 개요 섹션에서 사용할 수 있습니다.

    ![QnAMaker 끝점](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리 (미리 보기 릴리스)](#tab/v2)

<details>
<summary><b>쿼리 예측의 처리량 성능을 향상하려면 어떻게 해야 하나요?</b></summary>

**답변** : 처리량 성능 문제는 Cognitive Search을 확장 해야 함을 의미 합니다. 성능을 향상 시키려면 Cognitive Search에 복제본을 추가 하는 것이 좋습니다.

[가격 책정 계층](Concepts/azure-resources.md)에 대해 자세히 알아보세요.
</details>

---

## <a name="manage-the-knowledge-base"></a>기술 자료 관리

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)

<details>
<summary><b>내 QnA Maker의 일부를 실수로 삭제했습니다. 어떻게 해야 하나요?</b></summary>

**답변** : 검색 또는 웹 앱과 같은 QnA Maker 리소스와 함께 생성 된 Azure 서비스를 삭제 하지 마세요. QnA Maker 작동 하기 위해 필요 합니다. 하나를 삭제 하면 QnA Maker 제대로 작동 하지 않습니다.

질문 및 답변 쌍, 파일, URL, 사용자 지정 질문 및 답변, 기술 자료 또는 Azure 리소스를 포함한 모든 삭제는 영구적입니다. 기술 자료를 삭제하기 전에 **설정** 페이지에서 기술 자료를 내보냈는지 확인합니다.

</details>

<details>
<summary><b>URL/파일이 질문-답변 쌍을 추출하지 않는 이유는 무엇인가요?</b></summary>

**대답** : QnA Maker는 유효한 FAQ url에서 일부 질문 및 답변 (QnA) 콘텐츠를 자동으로 추출할 수 없습니다. 이러한 경우 QnA 콘텐츠를 .txt 파일에 붙여넣고 도구에서 수집할 수 있는지 확인할 수 있습니다. 또는 편집자로서 [QnA Maker 포털](https://qnamaker.ai)을 통해 기술 자료에 콘텐츠를 추가할 수 있습니다.

</details>

<details>
<summary><b>만들 수 있는 기술 자료 크기는 얼마인가요?</b></summary>

**답변** : 기술 자료의 크기는 QnA Maker 서비스를 만들 때 선택 하는 Azure SEARCH의 SKU에 따라 다릅니다. 자세한 내용은 [여기](./Tutorials/choosing-capacity-qnamaker-deployment.md)를 참조하세요.

</details>

<details>
<summary><b>새 기술 자료를 만들려고 할 때 드롭다운에 아무것도 표시될 수 없는 이유는 무엇인가요?</b></summary>

**답변** : Azure에서 아직 QnA Maker 서비스를 만들지 않았습니다. 만드는 방법에 대해 자세히 알아보려면 [여기](./How-To/set-up-qnamaker-service-azure.md)를 참조하세요.

</details>

<details>
<summary><b>기술 자료를 다른 사용자와 공유하려면 어떻게 하나요?</b></summary>

**답변** : 공유는 QnA Maker 서비스 수준에서 작동 합니다. 즉, 서비스의 모든 기술 자료가 공유 됩니다. 기술 자료에 대한 공동 작업 방법은 [여기](./How-To/collaborate-knowledge-base.md)를 참조하세요.

</details>

<details>
<summary><b>동일한 AAD 테넌트에 있지 않은 contributor와 기술 자료를 공유하여 기술 자료를 수정할 수 있나요?</b></summary>

**답변** : 공유는 azure 역할 기반 access Control (azure RBAC)을 기반으로 합니다. 다른 사용자와 Azure의 _모든_ 리소스를 공유할 수 있는 경우 QnA Maker도 공유할 수 있습니다.

</details>

<details>
<summary><b>5 QnAMaker 기술 자료를 포함 하는 App Service 요금제가 있는 경우 서로 다른 5 명의 사용자에 게 읽기/쓰기 권한을 할당 하 여 각 사용자가 QnAMaker 기술 자료 1 개만 액세스할 수 있나요?</b></summary>

**답변** : 개별 기술 자료가 아니라 전체 QnAMaker 서비스를 공유할 수 있습니다.

</details>

<details>
<summary><b>양호한 일치 항목이 없는 경우 기본 메시지를 변경하려면 어떻게 하나요?</b></summary>

**응답** : 기본 메시지는 App service의 설정에 포함 되어 있습니다.
- Azure Portal의 App Service 리소스로 이동

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- **설정** 옵션 클릭

![qnamaker appservice 설정](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- **DefaultAnswer** 설정 값 변경
- App Service 다시 시작

![qnamaker appservice 다시 시작](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>SharePoint 링크가 추출되지 않는 이유는 무엇인가요?</b></summary>

**응답** : 자세한 내용은 [데이터 원본 위치](./Concepts/knowledge-base.md#data-source-locations) 를 참조 하세요.

</details>

<details>
<summary><b>기술 자료에 대 한 업데이트는 게시에 반영 되지 않습니다. 왜 안 돼요?</b></summary>

**답변** : 테이블 업데이트, 테스트 또는 설정에 관계 없이 모든 편집 작업을 게시 하려면 먼저 저장 해야 합니다. 모든 편집 작업 후에는 **저장 및 학습** 단추를 클릭 해야 합니다.

</details>

<details>
<summary><b>기술 자료는 풍부한 데이터 또는 멀티미디어를 지원하나요?</b></summary>

**응답** :

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>파일 및 Url에 대 한 멀티미디어 자동 추출

* URL-HTML-Markdown 변환 기능을 제한 합니다.
* 파일-지원 되지 않음

#### <a name="answer-text-in-markdown"></a>Markdown의 대답 텍스트
QnA 쌍이 기술 자료에 있으면 대답의 markdown 텍스트를 편집 하 여 공용 Url에서 사용할 수 있는 미디어에 대 한 링크를 포함할 수 있습니다.


</details>

<details>
<summary><b>QnA Maker에서 영어 이외의 언어를 지원하나요?</b></summary>

**답변** : [지원 되는 언어](./Overview/languages-supported.md)에 대 한 자세한 내용을 참조 하세요.

여러 언어의 콘텐츠가 있는 경우 각 언어마다 별도의 서비스를 만들어야 합니다.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리 (미리 보기 릴리스)](#tab/v2)

<details>
<summary><b>URL/파일이 질문-답변 쌍을 추출하지 않는 이유는 무엇인가요?</b></summary>

**대답** : QnA Maker는 유효한 FAQ url에서 일부 질문 및 답변 (QnA) 콘텐츠를 자동으로 추출할 수 없습니다. 이러한 경우 QnA 콘텐츠를 .txt 파일에 붙여넣고 도구에서 수집할 수 있는지 확인할 수 있습니다. 또는 편집자로서 [QnA Maker 포털](https://qnamaker.ai)을 통해 기술 자료에 콘텐츠를 추가할 수 있습니다.

</details>

<details>
<summary><b>만들 수 있는 기술 자료 크기는 얼마인가요?</b></summary>

**답변** : 기술 자료의 크기는 QnA Maker 서비스를 만들 때 선택 하는 Azure SEARCH의 SKU에 따라 다릅니다. 자세한 내용은 [여기](./Tutorials/choosing-capacity-qnamaker-deployment.md)를 참조하세요.

</details>

<details>
<summary><b>새 기술 자료를 만들려고 할 때 드롭다운에 아무것도 표시될 수 없는 이유는 무엇인가요?</b></summary>

**답변** : Azure에서 아직 QnA Maker 서비스를 만들지 않았습니다. 만드는 방법에 대해 자세히 알아보려면 [여기](./How-To/set-up-qnamaker-service-azure.md)를 참조하세요.

</details>

<details>
<summary><b>기술 자료를 다른 사용자와 공유하려면 어떻게 하나요?</b></summary>

**답변** : 공유는 QnA Maker 서비스 수준에서 작동 합니다. 즉, 서비스의 모든 기술 자료가 공유 됩니다. 기술 자료에 대한 공동 작업 방법은 [여기](./How-To/collaborate-knowledge-base.md)를 참조하세요.

</details>

<details>
<summary><b>동일한 Azure Active Directory 테 넌 트에 없는 참가자와 기술 자료를 공유 하 여 기술 자료를 수정할 수 있습니까?</b></summary>

**답변** : 공유는 azure 역할 기반 access Control (azure RBAC)을 기반으로 합니다. 다른 사용자와 Azure의 _모든_ 리소스를 공유할 수 있는 경우 QnA Maker도 공유할 수 있습니다.

</details>

<details>
<summary><b>5명의 각각 다른 사용자가 하나의 QnAMaker 기술 자료에만 액세스할 수 있도록 해당 사용자에게 읽기/쓰기 권한을 할당할 수 있나요?</b></summary>

**답변** : 개별 기술 자료가 아니라 전체 QnAMaker 서비스를 공유할 수 있습니다.

</details>

<details>
<summary><b>SharePoint 링크가 추출되지 않는 이유는 무엇인가요?</b></summary>

**응답** : 자세한 내용은 [데이터 원본 위치](./Concepts/knowledge-base.md#data-source-locations) 를 참조 하세요.

</details>

<details>
<summary><b>기술 자료에 대 한 업데이트는 게시에 반영 되지 않습니다. 왜 안 돼요?</b></summary>

**답변** : 테이블 업데이트, 테스트 또는 설정에 관계 없이 모든 편집 작업을 게시 하려면 먼저 저장 해야 합니다. 모든 편집 작업 후에는 **저장 및 학습** 단추를 클릭 해야 합니다.

</details>

<details>
<summary><b>기술 자료는 풍부한 데이터 또는 멀티미디어를 지원하나요?</b></summary>

**응답** :

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>파일 및 Url에 대 한 멀티미디어 자동 추출

* URL-HTML-Markdown 변환 기능을 제한 합니다.
* 파일-지원 되지 않음

#### <a name="answer-text-in-markdown"></a>Markdown의 대답 텍스트
QnA 쌍이 기술 자료에 있으면 대답의 markdown 텍스트를 편집 하 여 공용 Url에서 사용할 수 있는 미디어에 대 한 링크를 포함할 수 있습니다.


</details>

<details>
<summary><b>QnA Maker에서 영어 이외의 언어를 지원하나요?</b></summary>

**답변** : [지원 되는 언어](./Overview/languages-supported.md)에 대 한 자세한 내용을 참조 하세요.

여러 언어의 콘텐츠가 있는 경우 각 언어마다 별도의 서비스를 만들어야 합니다.

</details>

---

## <a name="manage-service"></a>서비스 관리

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)

<details>
<summary><b>내 App Service를 다시 시작해야 하는 경우는 언제인가요?</b></summary>

**답변** : **사용자 설정** [페이지](https://www.qnamaker.ai/UserSettings)의 **끝점 키** 테이블에서 기술 자료에 대 한 버전 값 옆에 주의 아이콘이 있으면 app service를 새로 고칩니다.

</details>

<details>
<summary><b>기존 검색 서비스를 삭제 했습니다. 이 문제를 해결 하려면 어떻게 해야 하나요?</b></summary>

**답변** : Azure Cognitive Search 인덱스를 삭제 하는 경우 작업이 마지막 이며 인덱스를 복구할 수 없습니다.

</details>

<details>
<summary><b>`testkb`검색 서비스에서 인덱스를 삭제 했습니다. 이 문제를 해결 하려면 어떻게 해야 하나요?</b></summary>

**답변** : 이전 데이터를 복구할 수 없습니다. 새 QnA Maker 리소스를 만들고 기술 자료를 다시 만듭니다.

</details>

<details>
<summary><b>엔드포인트 키를 새로 고쳐야 하는 경우는 언제인가요?</b></summary>

**응답** : 끝점 키가 손상 된 것으로 의심 되는 경우 새로 고칩니다.

</details>

<details>
<summary><b>여러 언어를 사용 하는 기술 자료에 대해 동일한 Azure Cognitive Search 리소스를 사용할 수 있나요?</b></summary>

**답변** : 여러 언어와 여러 기술 자료를 사용 하려면 사용자는 각 언어에 대 한 QnA Maker 리소스를 만들어야 합니다. 이렇게 하면 언어 마다 별도의 Azure search 서비스를 만듭니다. 단일 Azure Search 서비스에서 다른 언어로 된 기술 자료를 혼합하면 결국 성능이 저하된 결과의 관련성이 발생합니다.

</details>

<details>
<summary><b>QnA Maker에서 사용 하는 Azure Cognitive Search 리소스의 이름을 변경 하려면 어떻게 해야 하나요?</b></summary>

**답변** : Azure Cognitive Search 리소스의 이름은 끝에 임의의 문자가 추가 된 QnA Maker 리소스 이름입니다. 이 이름으로는 QnA Maker의 여러 Search 리소스를 구별하기 어렵습니다. 별도의 검색 서비스 (원하는 방식으로 이름 지정)를 만들고 QnA 서비스에 연결 합니다. 이러한 단계는 [Azure search를 업그레이드](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)하기 위해 수행 해야 하는 단계와 비슷합니다.

</details>

<details>
<summary><b>QnA Maker에서 반환 `Runtime core is not initialized,` 하는 경우 어떻게 해결할 수 있나요?</b></summary>

**답변** : app service의 디스크 공간이 가득 찼을 수 있습니다. 디스크 공간을 수정 하는 단계:

1. [Azure Portal](https://portal.azure.com)에서 QnA Maker의 App service를 선택한 다음 서비스를 중지 합니다.
1. App service에서 **개발 도구** , **고급 도구** , **이동** 을 차례로 선택 합니다. 그러면 새 브라우저 창이 열립니다.
1. **디버그 콘솔** , **CMD** 를 차례로 선택 하 여 명령줄 도구를 엽니다.
1. _Site/wwwroot/Data/QnAMaker/_ directory로 이동 합니다.
1. 이름이로 시작 하는 모든 폴더를 제거 `rd` 합니다.

    다음을 **삭제 하지 마십시오** .

    * KbIdToRankerMappings.txt 파일
    * 파일에 EndpointSettings.js
    * EndpointKeys 폴더

1. App service를 시작 합니다.
1. 기술 자료에 액세스 하 여 지금 작동 하는지 확인 합니다.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리 (미리 보기 릴리스)](#tab/v2)


<details>
<summary><b>기존 검색 서비스를 삭제 했습니다. 이 문제를 해결 하려면 어떻게 해야 하나요?</b></summary>

**답변** : Azure Cognitive Search 인덱스를 삭제 하는 경우 작업이 마지막 이며 인덱스를 복구할 수 없습니다.

</details>

<details>
<summary><b>`testkb`검색 서비스에서 인덱스를 삭제 했습니다. 이 문제를 해결 하려면 어떻게 해야 하나요?</b></summary>

**답변** : 이전 데이터를 복구할 수 없습니다. 새 QnA Maker 리소스를 만들고 기술 자료를 다시 만듭니다.

</details>

<details>
<summary><b>여러 언어를 사용 하는 기술 자료에 대해 동일한 Azure Cognitive Search 리소스를 사용할 수 있나요?</b></summary>

**답변** : 여러 언어와 여러 기술 자료를 사용 하려면 사용자는 각 언어에 대 한 QnA Maker 리소스를 만들어야 합니다. 이렇게 하면 언어 마다 별도의 Azure search 서비스를 만듭니다. 단일 Azure Search 서비스에서 다른 언어로 된 기술 자료를 혼합하면 결국 성능이 저하된 결과의 관련성이 발생합니다.

</details>

<details>
<summary><b>QnA Maker에서 사용 하는 Azure Cognitive Search 리소스의 이름을 변경 하려면 어떻게 해야 하나요?</b></summary>

**답변** : Azure Cognitive Search 리소스의 이름은 끝에 임의의 문자가 추가 된 QnA Maker 리소스 이름입니다. 이 이름으로는 QnA Maker의 여러 Search 리소스를 구별하기 어렵습니다. 별도의 검색 서비스 (원하는 방식으로 이름 지정)를 만들고 QnA 서비스에 연결 합니다. 이러한 단계는 [Azure search를 업그레이드](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)하기 위해 수행 해야 하는 단계와 비슷합니다.

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>Bot을 비롯한 다른 서비스와의 통합

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)

<details>
<summary><b>QnA Maker를 사용하기 위해 Bot Framework를 사용해야 하나요?</b></summary>

**답변** : 아니요, QnA Maker에서 [봇 Framework](https://github.com/Microsoft/botbuilder-dotnet) 를 사용할 필요가 없습니다. 그러나 QnA Maker은 [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0&preserve-view=true)의 여러 템플릿 중 하나로 제공 됩니다. Bot Service는 Microsoft Bot Framework를 통해 지능형 봇의 신속한 개발을 사용하도록 설정하고, 서버리스 환경에서 실행됩니다.

</details>

<details>
<summary><b>QnA Maker를 사용 하 여 새 봇을 만들려면 어떻게 해야 하나요?</b></summary>

**답변** : [이](./Quickstarts/create-publish-knowledge-base.md) 설명서의 지침에 따라 Azure Bot Service를 사용 하 여 봇을 만듭니다.

</details>

<details>
<summary><b>기존 Azure bot service에서 다른 기술 자료를 사용할 어떻게 할까요? 있나요?</b></summary>

**답변** : 기술 자료에 대 한 다음 정보가 필요 합니다.

* 기술 자료 ID입니다.
* 기술 자료의 게시 된 끝점 사용자 지정 하위 도메인 이름으로 `host` , 게시 후 **설정** 페이지에서 찾을 수 있습니다.
* 기술 자료의 게시 된 끝점 키-게시 후 **설정** 페이지에서 찾을 수 있습니다.

이 정보를 사용 하 여 Azure Portal에서 봇의 app service로 이동 합니다. **설정-> 구성-> 응용 프로그램 설정** 에서 해당 값을 변경 합니다.

기술 자료의 끝점 키는 `QnAAuthkey` ABS 서비스에서 레이블이 지정 됩니다.

</details>

<details>
<summary><b>두 개 이상의 클라이언트 응용 프로그램에서 기술 자료를 공유할 수 있나요?</b></summary>

**답변** : 예, 기술 자료는 원하는 수의 클라이언트에서 쿼리할 수 있습니다. 기술 자료의 응답이 느리거나 시간이 초과 되는 것으로 나타나는 경우 기술 자료와 연결 된 app service에 대 한 서비스 계층을 업그레이드 하는 것이 좋습니다.

</details>

<details>
<summary><b>웹 사이트에 QnA Maker 서비스를 포함하려면 어떻게 하나요?</b></summary>

**대답** : 다음 단계에 따라 QnA Maker 서비스를 웹 사이트에 웹 채팅 컨트롤로 포함 합니다.

1. [여기](./Quickstarts/create-publish-knowledge-base.md)에 제공된 지침을 따라 FAQ 봇을 만듭니다.
2. [여기](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)에 제공된 단계를 따라 웹 채팅을 사용하도록 설정합니다.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리 (미리 보기 릴리스)](#tab/v2)


<details>
<summary><b>QnA Maker를 사용하기 위해 Bot Framework를 사용해야 하나요?</b></summary>

**답변** : 아니요, QnA Maker에서 [봇 Framework](https://github.com/Microsoft/botbuilder-dotnet) 를 사용할 필요가 없습니다. 그러나 QnA Maker은 [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0&preserve-view=true)의 여러 템플릿 중 하나로 제공 됩니다. Bot Service는 Microsoft Bot Framework를 통해 지능형 봇의 신속한 개발을 사용하도록 설정하고, 서버리스 환경에서 실행됩니다.

</details>

<details>
<summary><b>QnA Maker를 사용 하 여 새 봇을 만들려면 어떻게 해야 하나요?</b></summary>

**답변** : [이](./Quickstarts/create-publish-knowledge-base.md) 설명서의 지침에 따라 Azure Bot Service를 사용 하 여 봇을 만듭니다.

</details>

<details>
<summary><b>기존 Azure bot service에서 다른 기술 자료를 사용할 어떻게 할까요? 있나요?</b></summary>

**답변** : 기술 자료에 대 한 다음 정보가 필요 합니다.

* 기술 자료 ID입니다.
* 기술 자료의 게시 된 끝점 사용자 지정 하위 도메인 이름으로 `host` , 게시 후 **설정** 페이지에서 찾을 수 있습니다.
* 기술 자료의 게시 된 끝점 키-게시 후 **설정** 페이지에서 찾을 수 있습니다.

이 정보를 사용 하 여 Azure Portal에서 봇의 app service로 이동 합니다. **설정-> 구성-> 응용 프로그램 설정** 에서 해당 값을 변경 합니다.

기술 자료의 끝점 키는 `QnAAuthkey` ABS 서비스에서 레이블이 지정 됩니다.

</details>

<details>
<summary><b>두 개 이상의 클라이언트 응용 프로그램에서 기술 자료를 공유할 수 있나요?</b></summary>

**답변** : 예, 기술 자료는 원하는 수의 클라이언트에서 쿼리할 수 있습니다. 기술 자료의 응답이 느리거나 시간이 초과 되는 것으로 나타나는 경우 기술 자료와 연결 된 app service에 대 한 서비스 계층을 업그레이드 하는 것이 좋습니다.

</details>

<details>
<summary><b>웹 사이트에 QnA Maker 서비스를 포함하려면 어떻게 하나요?</b></summary>

**대답** : 다음 단계에 따라 QnA Maker 서비스를 웹 사이트에 웹 채팅 컨트롤로 포함 합니다.

1. [여기](./Quickstarts/create-publish-knowledge-base.md)에 제공된 지침을 따라 FAQ 봇을 만듭니다.
2. [여기](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)에 제공된 단계를 따라 웹 채팅을 사용하도록 설정합니다.

---

## <a name="data-storage"></a>데이터 스토리지

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (안정적인 릴리스)](#tab/v1)

<details>
<summary><b>어떤 데이터가 저장되고 어디에 저장되나요?</b></summary>

**응답** :

QnA Maker 서비스를 만들 때 Azure 지역을 선택했습니다. 기술 자료 및 로그 파일에는 이 지역에 저장됩니다.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리 (미리 보기 릴리스)](#tab/v2)

<details>
<summary><b>어떤 데이터가 저장되고 어디에 저장되나요?</b></summary>

**응답** :

QnA Maker 서비스를 만들 때 Azure 지역을 선택했습니다. 기술 자료 및 로그 파일에는 이 지역에 저장됩니다.

</details>

---
