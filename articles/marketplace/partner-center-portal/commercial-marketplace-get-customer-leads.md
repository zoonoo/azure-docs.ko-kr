---
title: 고객 잠재 고객 구성 | Azure 마켓플레이스
description: 상용 시장에서 고객 잠재 고객을 구성합니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: a1ec89dfd2dda91a10f2cc00b6ca4d9d7abbf032
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731151"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>마켓플레이스 제품의 잠재 고객

잠재 고객은 [Azure 마켓플레이스](https://azuremarketplace.microsoft.com) 또는 [AppSource](https://appsource.microsoft.com)에서 오퍼를 관심 있거나 배포하는 고객입니다. 오퍼가 마켓플레이스에 게시되면 고객 잠재 고객을 받게 됩니다. 이 문서에서는 다음을 설명합니다.

* 마켓플레이스 오퍼가 고객 잠재 고객을 창출하는 방법을 통해 비즈니스 기회를 놓치지 마세요. 
* CRM을 오퍼에 연결하여 하나의 중앙 위치에서 잠재 고객을 관리할 수 있습니다.
* 당사가 귀하에게 보내는 잠재 고객 데이터를 이해하므로 연락한 고객에 대한 후속 조치를 취할 수 있습니다.

## <a name="generate-customer-leads"></a>고객 잠재 고객 생성

잠재 고객도 생성되는 장소는 다음과 같습니다.

1. 고객이 마켓플레이스에서 "나에게 연락하기"를 선택한 후 자신의 정보를 공유하는 데 동의하는 경우. 이 잠재 고객은 제품을 가져오는 데 관심을 표명한 고객에 대한 정보를 공유하는 **초기 관심** 리드입니다. 해당 잠재 고객은 고객 유입 경로에서 최상위에 해당하는 고객입니다.

      ![역학 365 저에게 연락](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. 고객이 제품을 받기 위해 "Get It Now" 또는 [Azure portal](https://portal.azure.com/)"Create"(Azure Portal에서)를 선택하면 이 잠재 고객은 **활성 잠재 고객으로,** 제품을 배포하기 시작한 고객에 대한 정보를 공유합니다.

    ![SQL 지금 받기](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![윈도우 서버 만들기](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. 고객은 "시험 주행"을 하거나 오퍼의 "무료 평가판"을 시작합니다. 테스트 드라이브 또는 무료 평가판은 진입 장벽 없이 잠재 고객과 즉시 비즈니스를 공유할 수 있는 가속화된 기회입니다.

    ![다이내믹스 365 테스트 드라이브](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![다이내믹스 365 테스트 드라이브](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>CRM 시스템에 연결

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>잠재 고객 데이터 이해

고객 확보 프로세스에서 수신되는 각 잠재 고객에게는 특정 필드에 데이터가 있습니다. 가장 먼저 살펴볼 필드는 이 `LeadSource` 형식인 **소스-작업** | **제공을**따르는 필드입니다.

**출처**: 이 필드의 값은 잠재 리드를 생성한 마켓플레이스에 따라 채워집니다. 가능한 값은 `"AzureMarketplace"`, `"AzurePortal"` 및 `"AppSource (SPZA)"`입니다.

**작업**: 이 필드의 값은 고객이 잠재 고객을 생성한 마켓플레이스에서 수행한 작업에 따라 채워집니다. 

가능한 값은 다음과 같습니다.

- "INS" -- 설치. 고객이 제품을 구매할 때 Azure Marketplace 또는 AppSource에서 이 작업이 수행됩니다.
- "PLT" -- Partner Led Trial(파트너 진행 체험)의 약어. 고객이가 문의처 옵션을 사용할 때 AppSource에서 이 작업이 수행됩니다.
- "DNC" -- Do Not Contact(연락 안 함)의 약어. 앱 페이지에서 교차 목록에 올라 있는 파트너가 연락 요청을 받으면 AppSource에서 이 작업이 수행됩니다. 이 고객이 앱의 교차 목록에 올라 있다는 사실을 공유하지만 고객에게 연락할 필요는 없습니다.
- "만들기" - 이 작업은 Azure 포털 내에서만 수행되며 고객이 자신의 계정에 대한 제안을 구매할 때 생성됩니다.
- “StartTestDrive” -– 이 작업은 시험 사용 시에만 수행되며, 고객이 시험 사용을 시작할 때 생성됩니다.

**오퍼**: 시장에서 여러 오퍼가 있을 수 있습니다. 이 필드의 값은 잠재 리드를 생성한 오퍼에 따라 채워집니다. 게시자 ID와 쿠폰 ID는 모두 이 필드에 전송되며 마켓플레이스에 쿠폰을 게시할 때 제공한 값입니다.

다음 예제에서는 예상된 형식으로 `publisherid.offerid`예제 값을 보여 주다. 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>고객 정보

고객의 정보는 여러 필드를 통해 전송됩니다. 다음 예제에서는 잠재 고객에 포함된 고객 정보를 보여 주어집니다.

- FirstName: John
- LastName: Smith
- Email: jsmith\@microsoft.com
- Phone: 1234567890
- Country: US
- Company: Microsoft
- Title: CTO

>[!Note]
>위 예제의 모든 데이터가 각 잠재 고객에 대해 항상 제공되는 것은 아닙니다. 고객 잠재 고객 섹션에 언급된 여러 단계에서 잠재 고객을 얻을 수 있으므로 잠재 고객을 처리하는 가장 좋은 방법은 레코드를 중복 해제하고 후속 조치를 개인 설정하는 것입니다. 이렇게 하면 각 고객이 적절한 메시지를 받으며 모든 고객과 고유한 관계를 구축할 수 있습니다.

## <a name="best-practices-for-lead-management"></a>잠재 고객 관리를 위한 모범 사례

1. *프로세스* - 이정표, KPI 및 명확한 팀 소유권을 통해 명확한 영업 프로세스를 정의합니다.
2. *검증* - 잠재 고객의 자격을 충분히 갖추었는지 여부를 나타내는 전제 조건을 정의합니다. 영업 또는 마케팅 담당자가 전체 영업 프로세스를 진행하기 전에 잠재 고객을 신중하게 선별하도록 합니다.
3. *후속 조치* - 후속 조치를 잊지 말고 일반적인 트랜잭션에 5~12개의 후속 호출이 필요할 것으로 예상합니다.
4. *육성* - 더 높은 이익 마진으로 가는 길에 당신을 얻기 위해, 당신의 리드를 육성.

## <a name="leads-frequently-asked-questions"></a>자주 묻는 질문 리드

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>리드 대상을 설정하는 데 필요한 도움은 어디에서 받을 수 있나요?

[여기에서](#connect-to-your-crm-system) 문서를 찾거나 https://partner.microsoft.com/support/v2/?stage=1 **'오퍼 생성'을** 선택한 다음 지원 티켓을 **'lead management configuration.'** 제출할 **수 있습니다.**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>마켓플레이스에 오퍼를 게시하려면 잠재 고객 대상을 구성해야 하나요?

대답은 게시하는 오퍼의 유형에 따라 다릅니다. 고객 참여를 위한 SaaS 및 Dynamics 365는 '연락처', 운영에 대한 모든 Dynamics 365, 모든 Dynamics 365 비즈니스 센트럴 오퍼, 모든 컨설팅 서비스 제공을 통해 잠재 고객 대상에 연결해야 합니다. 오퍼 유형이 나열되지 않은 경우 필요하지 않습니다. 그러나 비즈니스 기회를 놓치지 않도록 잠재 고객 대상을 구성하는 것이 좋습니다.

### <a name="how-can-i-find-the-test-lead"></a>테스트 리드를 찾으려면 어떻게 해야 하나요?

잠재 `"MSFT_TEST"` 고객 대상에서 검색하는 경우 Microsoft의 샘플 테스트 리드는 다음과 같습니다.

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>라이브 오퍼가 있지만 잠재 고객을 볼 수 있습니까?

잠재 고객 대상에 대한 연결이 유효한지 확인합니다. 파트너 센터에서 오퍼에 게시를 입력한 후 테스트 리드를 보내드립니다. 테스트 잠재 고객표시가 표시되면 연결이 유효합니다. 또한 마켓플레이스의 리스팅에서 "지금 받기", "연락처" 또는 "무료 평가판"을 클릭하여 미리 보기 단계에서 쿠폰 미리 보기를 획득하려고 시도하여 잠재 고객 연결을 테스트할 수도 있습니다.

또한 올바른 데이터를 찾고 있는지 확인하십시오. 이 문서의 [잠재 고객 데이터 이해](#understand-lead-data) 섹션의 내용은 잠재 고객 대상으로 보내는 잠재 고객 데이터를 설명합니다.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Azure Blob을 리드 대상으로 구성했는데 리드가 표시되지 않는 이유는 무엇인가요?

Azure Blob 잠재 고객 대상은 더 이상 지원되지 않으므로 오퍼에서 생성된 고객 잠재 고객을 놓치고 있습니다. 다른 잠재 고객 [대상 옵션으로](./commercial-marketplace-get-customer-leads.md)전환합니다. 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Marketplace에서 메일을 받았으나 CRM에서 리드를 찾을 수 없는 이유는 무엇인가요?

최종 사용자의 메일 도메인 원본이 .edu일 수 있습니다. 개인 정보 보호를 위해 .edu 도메인에서 개인 식별 정보를 전달하지 않습니다. 을 통해 지원 https://partner.microsoft.com/support/v2/?stage=1티켓을 제출합니다.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Azure 테이블을 잠재 고객 대상으로 구성한 경우 잠재 고객을 어떻게 볼 수 있습니까?

Azure 포털에서 Azure 테이블에 저장된 잠재 고객 데이터에 액세스하거나 Azure [저장소 탐색기를](https://azure.microsoft.com/features/storage-explorer/) 무료로 다운로드하여 설치하여 Azure 저장소 계정의 테이블 데이터를 볼 수 있습니다.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Azure 테이블을 리드 대상으로 구성했습니다. Marketplace에서 새 리드를 보낼 때마다 알림을 받을 수 있나요?

예. 여기에 설명서의 Azure 테이블에 잠재 고객이 추가되면 전자 메일을 보내는 Microsoft 흐름을 설정하는 방법에 따라 [설명서를](./commercial-marketplace-lead-management-instructions-azure-table.md)따릅니다.

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Salesforce를 내 리드 대상으로 구성했으나 리드를 찾을 수 없는 이유는 무엇인가요?

"리드할 웹" 양식이 선택 목록에 따라 필수 필드인지 확인합니다. 그런 경우 해당 필드를 비필수 텍스트 필드로 전환합니다.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>리드 대상에 문제가 있습니다. 일부 리드가 누락되었습니다. 메일로 보내줄 수 있나요?

개인 식별 정보 정책으로 인해 보안되지 않은 이메일을 통해 잠재 고객 정보를 공유할 수 없습니다.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Azure 테이블을 잠재 고객 대상으로 구성한 경우 비용이 얼마나 들까요?

리드 생성 데이터가 부족합니다(거의 모든 게시자에 대해 1GB 미만임). 비용은 수신된 리드 수에 따라 결정됩니다. 월 1,000개의 리드가 수신되면 약 50센트가 부과됩니다. 스토리지 가격에 대한 자세한 내용은 [스토리지 가격](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

질문에 여전히 답변이 없는 경우 aka.ms/marketplacepublishersupport 통해 지원팀에 문의한 다음 **'오퍼 생성'→** '잠재 고객 **관리 구성'을** **선택합니다.** 

### <a name="i-am-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-who-to-receive-these-emails"></a>새 고객 잠재 고객을 받으면 이메일 알림을 받습니다. 이러한 이메일을 받을 사람을 어떻게 구성할 수 있습니까?

파트너 센터에서 오퍼에 액세스하고 **잠재 고객** -> 관리**편집을**> **제안 설정** 페이지로 이동합니다. 연락처 전자 메일 필드 에서 전자 **메일** 주소를 업데이트합니다.

## <a name="next-steps"></a>다음 단계

기술 설정이 완료되면 이러한 잠재 고객을 현재 영업 & 마케팅 전략 및 운영 프로세스에 통합해야 합니다. Microsoft는 전반적인 판매 프로세스를 보다 잘 이해하고 고품질 리드와 충분한 데이터를 제공하여 궁극적으로는 고객이 성공하도록 지원하기 위해 노력하고 있습니다. 이렇게 고객의 성공을 지원할 수 있도록, 추가 데이터를 제공하여 리드를 최적화하고 향상시킬 수 있는 방법에 대한 의견을 환영합니다. 영업 팀이 마켓플레이스 잠재 고객을 보다 성공적으로 수행할 수 있도록 [피드백과](mailto:AzureMarketOnboard@microsoft.com) 제안을 제공하는 데 관심이 있다면 저희에게 알려주세요.
