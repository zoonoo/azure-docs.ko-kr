---
title: 문제 해결 - QnA 메이커
description: QnA Maker 서비스와 관련하여 가장 자주 묻는 질문의 선별된 목록은 더 빠르고 더 나은 결과를 통해 서비스를 채택하는 데 도움이 됩니다.
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: diberry
ms.openlocfilehash: 7847e21dbcf07f669d6802fffdd1e43623a72340
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804353"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA 제조업체의 문제 해결

QnA Maker 서비스와 관련하여 가장 자주 묻는 질문의 선별된 목록은 더 빠르고 더 나은 결과를 통해 서비스를 채택하는 데 도움이 됩니다.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>예측 관리

<details>
<summary><b>쿼리 예측의 처리량 성능을 개선하면 어떻게 해야 합니까?</b></summary>

**답변**: 처리량 성능 문제는 앱 서비스와 인지 검색 모두에 대해 확장해야 함을 나타냅니다. 성능을 향상시키기 위해 인지 검색에 복제본을 추가하는 것이 좋습니다.

[가격 책정 계층에](Concepts/azure-resources.md)대해 자세히 알아보십시오.
</details>

<details>
<summary><b>QnAMaker 서비스 엔드포인트를 얻는 방법</b></summary>

**답변**: QnAMaker 서비스 끝점은 QnAMaker 지원 또는 UserVoice에 문의 할 때 디버깅 목적으로 유용합니다. 끝점은 이 양식의 `https://your-resource-name.azurewebsites.net`URL입니다.

1. [Azure Portal](https://portal.azure.com)에서 QnAMaker 서비스(리소스 그룹)로 이동

    ![Azure Portal의 QnAMaker Azure 리소스 그룹](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. QnA 메이커 리소스와 연결된 앱 서비스를 선택합니다. 일반적으로 이름은 동일합니다.

     ![QnAMaker App Service 선택](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 끝점 URL은 개요 섹션에서 사용할 수 있습니다.

    ![QnAMaker 끝점](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>기술 자료 관리

<details>
<summary><b>내 QnA Maker의 일부를 실수로 삭제했습니다. 어떻게 해야 하나요?</b></summary>

**답변**: 검색 또는 웹 앱과 같은 QnA Maker 리소스와 함께 만든 Azure 서비스를 삭제하지 마십시오. QnA 메이커가 작동하려면 이 것들이 필요하며, 하나를 삭제하면 QnA Maker가 올바르게 작동하지 않습니다.

질문 및 답변 쌍, 파일, URL, 사용자 지정 질문 및 답변, 기술 자료 또는 Azure 리소스를 포함한 모든 삭제는 영구적입니다. 기술 자료를 삭제하기 전에 **설정** 페이지에서 기술 자료를 내보냈는지 확인합니다.

</details>

<details>
<summary><b>URL/파일이 질문-답변 쌍을 추출하지 않는 이유는 무엇인가요?</b></summary>

**답변**: QnA Maker가 유효한 FAQ URL에서 QnA(질문 및 답변) 콘텐츠를 자동으로 추출할 수 없습니다. 이러한 경우 QnA 콘텐츠를 .txt 파일에 붙여넣고 도구에서 수집할 수 있는지 확인할 수 있습니다. 또는 편집자로서 [QnA Maker 포털](https://qnamaker.ai)을 통해 기술 자료에 콘텐츠를 추가할 수 있습니다.

</details>

<details>
<summary><b>만들 수 있는 기술 자료 크기는 얼마인가요?</b></summary>

**답변**: 기술 자료의 크기는 QnA Maker 서비스를 만들 때 선택하는 Azure 검색의 SKU에 따라 다릅니다. 자세한 내용은 [여기](./Tutorials/choosing-capacity-qnamaker-deployment.md)를 참조하세요.

</details>

<details>
<summary><b>새 기술 자료를 만들려고 할 때 드롭다운에 아무것도 표시될 수 없는 이유는 무엇인가요?</b></summary>

**답변**: 아직 Azure에서 QnA Maker 서비스를 만들지 않았습니다. 만드는 방법에 대해 자세히 알아보려면 [여기](./How-To/set-up-qnamaker-service-azure.md)를 참조하세요.

</details>

<details>
<summary><b>기술 자료를 다른 사용자와 공유하려면 어떻게 하나요?</b></summary>

**답변**: 공유는 QnA Maker 서비스의 수준에서 작동, 즉, 서비스의 모든 지식 기반이 공유됩니다. 기술 자료에 대한 공동 작업 방법은 [여기](./How-To/collaborate-knowledge-base.md)를 참조하세요.

</details>

<details>
<summary><b>동일한 AAD 테넌트에 있지 않은 contributor와 기술 자료를 공유하여 기술 자료를 수정할 수 있나요?</b></summary>

**답변**: 공유는 AZURE 역할 기반 액세스 제어(RBAC)를 기반으로 합니다. 다른 사용자와 Azure의 _모든_ 리소스를 공유할 수 있는 경우 QnA Maker도 공유할 수 있습니다.

</details>

<details>
<summary><b>5개의 QnAMaker 기술 자료가 있는 앱 서비스 계획이 있는 경우. 5명의 다른 사용자에게 읽기/쓰기 권한을 할당하여 각 사용자가 1개의 QnAMaker 기술 자료에만 액세스할 수 있도록 할 수 있습니까?</b></summary>

**답변**: 개별 기술 자료가 아닌 전체 QnAMaker 서비스를 공유할 수 있습니다.

</details>

<details>
<summary><b>양호한 일치 항목이 없는 경우 기본 메시지를 변경하려면 어떻게 하나요?</b></summary>

**답변**: 기본 메시지는 앱 서비스의 설정의 일부입니다.
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

**답변**: 자세한 내용은 [데이터 원본 위치를](./Concepts/knowledge-base.md#data-source-locations) 참조하십시오.

</details>

<details>
<summary><b>기술 자료에 대한 업데이트는 게시에 반영되지 않습니다. 왜 안 돼요?</b></summary>

**답변**: 테이블 업데이트, 테스트 또는 설정에서 모든 편집 작업을 게시하려면 저장해야 합니다. 모든 편집 작업 후 **저장 및 학습** 단추를 클릭해야 합니다.

</details>

<details>
<summary><b>기술 자료는 풍부한 데이터 또는 멀티미디어를 지원하나요?</b></summary>

**대답**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>파일 및 URL에 대한 멀티미디어 자동 추출

* URL - 제한된 HTML-마크다운 변환 기능입니다.
* 파일 - 지원되지 않음

#### <a name="answer-text-in-markdown"></a>마크다운에서 텍스트 에 대한 답변
QnA 쌍이 기술 자료에 있으면 응답의 마크다운 텍스트를 편집하여 공개 URL에서 사용할 수 있는 미디어에 대한 링크를 포함할 수 있습니다.


</details>

<details>
<summary><b>QnA Maker에서 영어 이외의 언어를 지원하나요?</b></summary>

**답변**: [지원되는 언어에](./Overview/languages-supported.md)대한 자세한 내용을 참조하십시오.

여러 언어의 콘텐츠가 있는 경우 각 언어마다 별도의 서비스를 만들어야 합니다.

</details>

## <a name="manage-service"></a>서비스 관리

<details>
<summary><b>내 App Service를 다시 시작해야 하는 경우는 언제인가요?</b></summary>

**답변**: 주의 아이콘이 **사용자 설정** [페이지의](https://www.qnamaker.ai/UserSettings) **Endpoint 키** 테이블의 기술 자료에 대한 버전 값 옆에 있을 때 앱 서비스를 새로 고칩니다.

</details>

<details>
<summary><b>기존 검색 서비스를 삭제했습니다. 이 문제를 어떻게 해결할 수 있습니까?</b></summary>

**답변**: Azure 인지 검색 인덱스를 삭제하면 작업이 최종적이며 인덱스를 복구 할 수 없습니다.

</details>

<details>
<summary><b>검색 서비스에서 `testkb` 색인을 삭제했습니다. 이 문제를 어떻게 해결할 수 있습니까?</b></summary>

**답변**: 이전 데이터를 복구 할 수 없습니다. 새 QnA Maker 리소스를 만들고 기술 기반을 다시 만듭니다.

</details>

<details>
<summary><b>엔드포인트 키를 새로 고쳐야 하는 경우는 언제인가요?</b></summary>

**답변**: 엔드포인트 키가 손상되었다고 의심되는 경우 끝점 키를 새로 고칩니다.

</details>

<details>
<summary><b>여러 언어를 사용하는 기술 자료에 동일한 Azure 인지 검색 리소스를 사용할 수 있습니까?</b></summary>

**답변**: 다중 언어 및 여러 기술 기반을 사용하려면 사용자는 각 언어에 대한 QnA Maker 리소스를 만들어야 합니다. 이렇게 하면 언어당 별도의 Azure 검색 서비스가 생성됩니다. 단일 Azure Search 서비스에서 다른 언어로 된 기술 자료를 혼합하면 결국 성능이 저하된 결과의 관련성이 발생합니다.

</details>

<details>
<summary><b>QnA Maker에서 사용하는 Azure 인지 검색 리소스의 이름을 변경하려면 어떻게 해야 합니까?</b></summary>

**답변**: Azure 인지 검색 리소스의 이름은 끝에 임의의 문자가 추가된 QnA Maker 리소스 이름입니다. 이 이름으로는 QnA Maker의 여러 Search 리소스를 구별하기 어렵습니다. 별도의 검색 서비스를 만들고(원하는 대로 이름을 지정) QnA 서비스에 연결합니다. 단계는 [Azure 검색을 업그레이드하는](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)데 필요한 단계와 유사합니다.

</details>

<details>
<summary><b>QnA 메이커가 `Runtime core is not initialized,` 반환할 때 어떻게 고칠 수 있습니까?</b></summary>

**답변**: 앱 서비스의 디스크 공간이 가득 찼을 수 있습니다. 디스크 공간을 수정하는 단계:

1. Azure [포털에서](https://portal.azure.com)QnA Maker의 앱 서비스를 선택한 다음 서비스를 중지합니다.
1. 앱 서비스에 있는 동안 **개발 도구,** **고급 도구를**선택한 다음 **이동합니다.** 그러면 새 브라우저 창이 열립니다.
1. **디버그 콘솔을**선택한 다음 **CMD를** 선택하여 명령줄 도구를 엽니다.
1. _사이트/wwwroot/데이터/QnAMaker/디렉토리로_ 이동합니다.
1. 이름으로 시작하는 모든 폴더를 `rd`제거합니다.

    다음을 **삭제하지 마십시오.**

    * KbIdToRanker매핑스.txt 파일
    * 엔드포인트설정.json 파일
    * 끝점키 폴더

1. 앱 서비스를 시작합니다.
1. 기술 자료에 액세스하여 지금 작동하는지 확인합니다.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Bot을 비롯한 다른 서비스와의 통합

<details>
<summary><b>QnA Maker를 사용하기 위해 Bot Framework를 사용해야 하나요?</b></summary>

**답변**: 아니요, QnA Maker와 함께 [봇 프레임워크를](https://github.com/Microsoft/botbuilder-dotnet) 사용할 필요가 없습니다. 그러나 QnA Maker는 [Azure 봇 서비스의](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)여러 템플릿 중 하나로 제공됩니다. Bot Service는 Microsoft Bot Framework를 통해 지능형 봇의 신속한 개발을 사용하도록 설정하고, 서버리스 환경에서 실행됩니다.

</details>

<details>
<summary><b>QnA 메이커로 새 봇을 만들려면 어떻게 해야 하나요?</b></summary>

**답변**: [이](./Quickstarts/create-publish-knowledge-base.md) 설명서의 지침에 따라 Azure Bot 서비스를 사용하여 봇을 만듭니다.

</details>

<details>
<summary><b>기존 Azure 봇 서비스에서 다른 기술 기반을 사용하려면 어떻게 해야 합니까?</b></summary>

**답변**: 기술 자료에 대한 다음 정보가 있어야합니다.

* 기술 자료 ID.
* 게시 한 후 **설정** 페이지에서 찾을 수 `host`있는 것으로 알려진 기술 자료의 게시된 끝점 사용자 지정 하위 도메인 이름입니다.
* 기술 자료의 게시된 끝점 키 - 게시 한 후 **설정** 페이지에서 찾을 수 있습니다.

이 정보를 사용하여 Azure 포털에서 봇의 앱 서비스로 이동합니다. **설정 -> 구성 -> 응용 프로그램 설정에서**해당 값을 변경합니다.

기술 자료의 끝점 키는 ABS `QnAAuthkey` 서비스에 레이블이 지정됩니다.

</details>

<details>
<summary><b>둘 이상의 클라이언트 응용 프로그램이 기술 자료와 공유할 수 있습니까?</b></summary>

**답변**: 예, 기술 자료는 클라이언트의 수에 관계없이 쿼리 할 수 있습니다. 기술 자료의 응답이 느리거나 시간이 지난 것으로 나타나면 기술 자료와 연결된 앱 서비스의 서비스 계층을 업그레이드하는 것이 좋습니다.

</details>

<details>
<summary><b>웹 사이트에 QnA Maker 서비스를 포함하려면 어떻게 하나요?</b></summary>

**답변**: 다음 단계를 수행하여 QnA Maker 서비스를 웹 사이트에 웹 채팅 컨트롤로 포함합니다.

1. [여기](./Quickstarts/create-publish-knowledge-base.md)에 제공된 지침을 따라 FAQ 봇을 만듭니다.
2. [여기](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)에 제공된 단계를 따라 웹 채팅을 사용하도록 설정합니다.

</details>

## <a name="data-storage"></a>데이터 스토리지

<details>
<summary><b>어떤 데이터가 저장되고 어디에 저장되나요?</b></summary>

**대답**:

QnA Maker 서비스를 만들 때 Azure 지역을 선택했습니다. 기술 자료 및 로그 파일에는 이 지역에 저장됩니다.

</details>