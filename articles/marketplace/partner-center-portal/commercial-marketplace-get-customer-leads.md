---
title: 고객 리드 구성 | Azure Marketplace
description: 상업적 marketplace에서 고객 리드를 구성 합니다.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 7ead8dee12d4376e6e1058b84a25b91c021a937c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812665"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Marketplace 제품의 고객 책임자

잠재 고객은 고객에 게 관심이 있거나 [Azure Marketplace](https://azuremarketplace.microsoft.com) 또는 [appsource](https://appsource.microsoft.com)에서 제품을 배포 하는 고객입니다. 제품이 marketplace에 게시 되 면 고객에 게 잠재 고객을 받게 됩니다. 이 문서에서는 다음에 대해 설명 합니다.

* Marketplace 제품이 고객 리드를 생성 하 여 비즈니스 기회를 놓치지 않도록 합니다. 
* CRM을 제품에 연결 하 여 하나의 중앙 위치에서 잠재 고객을 관리할 수 있습니다.
* 사용자에 게 연락 하는 잠재 고객에 대 한 후속 작업을 수행할 수 있도록 사용자에 게 보내는 잠재 고객 데이터를 이해 합니다.

## <a name="generate-customer-leads"></a>고객 리드 생성

잠재 고객이 생성 되는 위치는 다음과 같습니다.

1. 고객이 marketplace에서 "연락처"를 선택한 후 정보를 공유 하는 것을 동의 합니다. 이 리드는 제품을 얻기 위해 관심이 있는 고객에 대 한 정보를 공유 하는 **초기 관심사** 입니다. 해당 잠재 고객은 고객 유입 경로에서 최상위에 해당하는 고객입니다.

      ![Dynamics 365 연락처](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. 고객이 "지금 가져오기" 또는 "만들기" ( [Azure Portal](https://portal.azure.com/))를 선택 하 여 제안을 받을 때이 잠재 고객은 제품 배포를 시작한 고객에 대 한 정보를 공유 하는 **활성 잠재 고객**입니다.

    ![SQL 지금 가져오기](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server 만들기](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. 고객이 "시험 사용" 하거나 제품의 "무료 평가판"을 시작 합니다. 시험 사용 또는 무료 평가판은 진입 장벽 없이 잠재적 고객에 게 비즈니스를 즉시 공유할 수 있는 가속화 된 기회입니다.

    ![Dynamics 365 시험 드라이브](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 시험 드라이브](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>CRM 시스템에 연결

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>리드 데이터 이해

고객 확보 프로세스에서 수신되는 각 잠재 고객에게는 특정 필드에 데이터가 있습니다. 확인할 첫 번째 필드는 다음 형식을 따르는 `LeadSource` 필드입니다. **원본-동작** | **제품**입니다.

**원본**:이 필드의 값은 잠재 고객을 생성 한 marketplace를 기준으로 채워집니다. 가능한 값은 `"AzureMarketplace"`, `"AzurePortal"`및 `"AppSource (SPZA)"`입니다.

**작업**:이 필드의 값은 잠재 고객이 생성 한 marketplace에서 고객이 취한 작업을 기반으로 채워집니다. 

가능한 값은 다음과 같습니다.

- "INS" -- 설치. 고객이 제품을 구매할 때 Azure Marketplace 또는 AppSource에서 이 작업이 수행됩니다.
- "PLT" -- Partner Led Trial(파트너 진행 체험)의 약어. 고객이가 문의처 옵션을 사용할 때 AppSource에서 이 작업이 수행됩니다.
- "DNC" -- Do Not Contact(연락 안 함)의 약어. 앱 페이지에서 교차 목록에 올라 있는 파트너가 연락 요청을 받으면 AppSource에서 이 작업이 수행됩니다. 이 고객이 앱의 교차 목록에 올라 있다는 사실을 공유하지만 고객에게 연락할 필요는 없습니다.
- "만들기"-이 작업은 Azure Portal 내에만 포함 되며 고객이 자신의 계정에 제품을 구매할 때 생성 됩니다.
- “StartTestDrive” -– 이 작업은 시험 사용 시에만 수행되며, 고객이 시험 사용을 시작할 때 생성됩니다.

**제품**: marketplace에 여러 제품이 있을 수 있습니다. 이 필드의 값은 잠재 고객을 생성 한 제품을 기반으로 채워집니다. 게시자 ID 및 제품 ID는 모두이 필드에서 전송 되며 marketplace에 제품을 게시할 때 제공한 값입니다.

다음 예에서는 `publisherid.offerid`예상 형식의 예제 값을 보여 줍니다. 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>고객 정보

고객 정보는 여러 필드를 통해 전송 됩니다. 다음 예에서는 잠재 고객에 포함 된 고객 정보를 보여 줍니다.

- FirstName: John
- LastName: Smith
- Email: jsmith\@microsoft.com
- Phone: 1234567890
- Country: US
- Company: Microsoft
- Title: CTO

>[!Note]
>위 예제의 모든 데이터가 각 잠재 고객에 대해 항상 제공되는 것은 아닙니다. 고객 리드 섹션에 설명 된 대로 여러 단계를 수행 하기 위해 잠재 고객을 처리 하는 가장 좋은 방법은 레코드를 중복 제거 하 고 후속 작업을 개인 설정 하는 것입니다. 이렇게 하면 각 고객이 적절한 메시지를 받으며 모든 고객과 고유한 관계를 구축할 수 있습니다.

## <a name="best-practices-for-lead-management"></a>리드 관리에 대 한 모범 사례

1. *프로세스* -마일스 톤, kpi 및 명확한 팀 소유권을 사용 하 여 명확한 판매 프로세스를 정의 합니다.
2. *자격 증명* -선행의 정규화 여부를 나타내는 필수 구성 요소를 정의 합니다. 판매 또는 마케팅 담당자가 전체 판매 프로세스를 수행 하기 전에 신중 하 게 우량으로 선별 되도록 합니다.
3. *후속* 작업-후속 조치를 잊지 말고, 일반적인 트랜잭션이 5 ~ 12 번의 후속 호출을 요구 합니다.
4. *키우십시오* -수익을 더 높게 설정 하기 위해 잠재 고객을 키우십시오 합니다.

## <a name="leads-frequently-asked-questions"></a>자주 묻는 질문과 대답

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>내 리드 대상 설정에서 도움을 받을 수 있는 위치

[여기](#connect-to-your-crm-system) 에서 설명서를 찾거나 aka.ms/marketplacepublishersupport을 통해 지원 티켓을 제출 하 고 **' 제안 만들기 '** → **제안 유형** → **' 리드 관리 구성** '을 선택할 수 있습니다.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Marketplace에 제품을 게시 하기 위해 잠재 고객 대상을 구성 해야 하나요?

답변은 게시 하는 제품의 유형에 따라 달라 집니다. 고객 Engagement 제품에 대 한 SaaS 및 Dynamics 365은 ' 연락처 '로 나열 하 고, 모든 dynamics 365 for Operations 제품, 모든 Dynamics 365 Business Central 제품, 모든 컨설팅 서비스 제품에는 잠재 고객 대상에 대 한 연결이 필요 합니다. 제안 유형이 나열 되지 않은 경우에는 필요 하지 않습니다. 그러나 비즈니스 기회를 놓치지 않기 위해 잠재 고객 대상을 구성 하는 것이 좋습니다.

### <a name="how-can-i-find-the-test-lead"></a>테스트 리드는 어떻게 찾을 수 있나요?

잠재 고객 대상의 `"MSFT_TEST"`를 검색 합니다. Microsoft의 샘플 테스트 책임자는 다음과 같습니다.

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

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Live 제품이 있지만 잠재 고객을 볼 수 없나요?

잠재 고객 대상에 대 한 연결이 올바른지 확인 합니다. 파트너 센터에서 제품에 대 한 게시를 방문 하면 테스트 리드가 전송 됩니다. 테스트 리더가 표시 되 면 연결이 유효 합니다. Marketplace의 목록에서 "지금 가져오기", "연락처" 또는 "무료 평가판"을 클릭 하 여 미리 보기 단계에서 제품 미리 보기를 얻으려고 시도 하 여 리드 연결을 테스트할 수도 있습니다.

또한 올바른 데이터를 찾고 있는지 확인 합니다. 이 문서의 [리드 데이터 이해](#understand-lead-data) 섹션에 있는 내용은 잠재 고객 대상으로 보내는 리드 데이터를 설명 합니다.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Azure BLOB을 선행 대상으로 구성 했습니다. 리더가 표시 되지 않는 이유는 무엇 인가요?

Azure Blob 리드 대상은 더 이상 지원 되지 않으므로 제품에 의해 생성 된 고객 리더가 누락 됩니다. 다른 [리드 대상 옵션](./commercial-marketplace-get-customer-leads.md)으로 전환 합니다. 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Marketplace에서 전자 메일을 받았지만 CRM에서 잠재 고객을 찾을 수 없는 이유는 무엇입니까?

최종 사용자의 메일 도메인 원본이 .edu일 수 있습니다. 개인 정보 보호를 위해 .edu 도메인에서 PII 데이터를 전달 하지 않습니다. Aka.ms/marketplacepublishersupport를 통해 지원 티켓을 제출 합니다.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Azure 테이블을 잠재 고객 대상으로 구성 하 고 잠재 고객을 어떻게 볼 수 있나요?

Azure Portal에서 Azure 테이블에 저장 된 잠재 고객 데이터에 액세스 하거나 무료로 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 다운로드 하 여 설치 하 여 azure Storage 계정의 테이블 데이터를 볼 수 있습니다.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Azure 테이블을 잠재 고객 대상으로 구성 하 고 Marketplace에서 새 잠재 고객을 보낼 때마다 알림 메시지를 받을 수 있나요?

예, [여기](./commercial-marketplace-lead-management-instructions-azure-table.md)에서 설명서의 Azure 테이블에 잠재 고객이 추가 되 면 전자 메일을 보내는 Microsoft flow를 설정 하는 지침을 따르세요.

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Salesforce를 리드 대상으로 구성 하 고 잠재 고객을 찾을 수 없는 이유는 무엇입니까?

"웹에서 잠재 고객" 양식이 선택 목록을 기반으로 하는 필수 필드 인지 확인 합니다. 그런 경우 해당 필드를 비필수 텍스트 필드로 전환합니다.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>리드 대상에 문제가 발생 하 여 일부 잠재 고객이 누락 되었습니다. 전자 메일로 전자 메일을 보낼 수 있나요?

PII(프라이빗 식별 정보) 정책으로 인해 보안되지 않은 이메일을 통해 리드 정보를 공유할 수 없습니다.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Azure 테이블을 잠재 고객 대상으로 구성 하 고 비용은 얼마나 되나요?

리드 생성 데이터가 부족합니다(거의 모든 게시자에 대해 1GB 미만임). 비용은 수신된 리드 수에 따라 결정됩니다. 월 1,000개의 리드가 수신되면 약 50센트가 부과됩니다. 스토리지 가격에 대한 자세한 내용은 [스토리지 가격](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

그래도 답변이 없으면 aka.ms/marketplacepublishersupport을 통해 지원 담당자에 게 문의 한 후 **' 제안 만들기 '** → **제안 유형** → **' 리드 관리 구성** '을 선택 합니다. 

## <a name="next-steps"></a>다음 단계

기술 설정이 준비 되 면 이러한 잠재 고객을 현재 판매 & 마케팅 전략 및 운영 프로세스에 통합 해야 합니다. Microsoft는 전반적인 판매 프로세스를 보다 잘 이해하고 고품질 리드와 충분한 데이터를 제공하여 궁극적으로는 고객이 성공하도록 지원하기 위해 노력하고 있습니다. 이렇게 고객의 성공을 지원할 수 있도록, 추가 데이터를 제공하여 리드를 최적화하고 향상시킬 수 있는 방법에 대한 의견을 환영합니다. 판매 팀이 Marketplace 리드를 사용 하 여 더 성공적으로 사용할 수 있도록 [사용자 의견](mailto:AzureMarketOnboard@microsoft.com) 및 제안 사항을 제공 하는 데 관심이 있는 경우 알려주세요.
