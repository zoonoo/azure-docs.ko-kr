---
title: Microsoft 상업적 marketplace 제품의 잠재 고객 구성
description: Microsoft 상업적 marketplace에서 고객 리드를 구성 합니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 54c67656d7847b44c8fc83b33a4e03be3838cf76
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131112"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>마켓플레이스 제품의 잠재 고객

고객은 Microsoft [Appsource](https://appsource.microsoft.com) 및 [Azure Marketplace](https://azuremarketplace.microsoft.com)에서 귀하의 제품에 관심이 있거나 배포 하는 고객입니다. 제품이 marketplace에 게시 된 후 고객 리드를 받게 됩니다. 이 문서는 다음 사항을 설명합니다.

* Marketplace 제품이 고객 리드를 생성 하 여 비즈니스 기회가 누락 되지 않도록 합니다. 
* 단일 중앙 위치에서 잠재 고객을 관리할 수 있도록 제품에 CRM (고객 관계 관리) 시스템을 연결 하는 방법을 설명 합니다.
* 사용자에 게 도달 하는 고객에 대 한 후속 작업을 수행할 수 있도록 사용자가 전송 하는 잠재 고객 데이터입니다.

## <a name="generate-customer-leads"></a>고객 리드 생성

잠재 고객이 생성 되는 위치는 다음과 같습니다.

- 고객은 상업적 marketplace에서 **연락처** 를 선택한 후 정보를 공유 하는 것을 동의. 이 리드는 *초기 관심사* 입니다. Microsoft는 제품 사용에 관심이 있는 고객에 대 한 정보와 정보를 공유 합니다. 해당 잠재 고객은 고객 유입 경로에서 최상위에 해당하는 고객입니다.

    ![Dynamics 365 연락처](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- 고객은 **지금 가져오기** 를 선택 하거나, [Azure Portal](https://portal.azure.com/)에서 **만들기** 를 선택 하 여 제품을 가져옵니다. 이 리드는 *활성* 리드입니다. 제품 배포를 시작한 고객에 대 한 정보와 정보를 공유 합니다.

    ![SQL Get Now 단추](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server 만들기 단추](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- 고객은 **시험** **사용 또는 무료 평가판** 을 선택 하 여 제품을 사용해 보세요. 시험 사용 또는 무료 평가판은 진입 장벽 없이 잠재적 고객에 게 비즈니스를 즉시 공유할 수 있는 가속화 된 기회입니다.

    ![Dynamics 365 테스트 드라이브 단추](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 무료 평가판 단추](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>CRM 시스템에 연결

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>리드 데이터 이해

고객 확보 프로세스에서 수신되는 각 잠재 고객에게는 특정 필드에 데이터가 있습니다. 확인할 첫 번째 필드는 다음 `LeadSource` 형식을 따르는 필드입니다. **원본-동작** | **제안**.

**원본**:이 필드의 값은 잠재 고객을 생성 한 marketplace를 기준으로 채워집니다. 가능한 값은 `"AzureMarketplace"`, `"AzurePortal"` 및 `"AppSource (SPZA)"`입니다.

**작업**:이 필드의 값은 잠재 고객을 생성 한 marketplace에서 고객이 취한 작업을 기준으로 채워집니다.

가능한 값은 다음과 같습니다.

- **"INS"**: *설치*를 나타냅니다. 고객이 제품을 구매 하는 경우이 작업은 Azure Marketplace 또는 AppSource에 있습니다.
- **"PLT"**: *파트너-led 평가판*을 나타냅니다. 이 작업은 고객이 **연락처** 옵션을 선택할 때 appsource에 있습니다.
- **"Dnc"**: 연결 안 *함*을 의미 합니다. 앱 페이지에 교차 나열 된 파트너에 게 연락 요청이 요청 될 때이 작업은 AppSource에 있습니다. 이 고객이 앱에 교차 나열 되었다는 알림을 공유 하지만 사용자에 게 연락할 필요가 없습니다.
- **"만들기"**:이 작업은 Azure Portal 내에만 있으며 고객이 자신의 계정에 제품을 구매할 때 생성 됩니다.
- **"Starttestdrive"**:이 작업은 **테스트 드라이브** 옵션에만 사용할 수 있으며 고객이 테스트 드라이브를 시작할 때 생성 됩니다.

**제품**: 상업적 marketplace에서 여러 제품을 사용할 수 있습니다. 이 필드의 값은 잠재 고객을 생성 한 제품을 기반으로 채워집니다. 게시자 ID 및 제품 ID는 모두이 필드에서 전송 되며 marketplace에 제품을 게시할 때 제공한 값입니다.

다음 예에서는 예상 되는 형식의 `publisherid.offerid`값을 보여 줍니다. 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>고객 정보

고객 정보는 여러 필드를 통해 전송 됩니다. 다음 예에서는 잠재 고객에 포함 된 고객 정보를 보여 줍니다.

- FirstName: John
- LastName: Smith
- Email: jsmith\@microsoft.com
- Phone: 1234567890
- Country: US
- Company: Microsoft
- Title: CTO

>[!NOTE]
>위 예제의 모든 데이터가 각 잠재 고객에 대해 항상 제공되는 것은 아닙니다. "고객 리드 생성" 섹션에 설명 된 대로 여러 단계를 진행 하기 위해 잠재 고객을 처리 하는 가장 좋은 방법은 레코드를 중복 제거 하 고 후속 작업을 개인 설정 하는 것입니다. 이러한 방식으로 각 고객이 적절 한 메시지를 가져오고 고유한 관계를 만들 수 있습니다.

## <a name="best-practices-for-lead-management"></a>리드 관리에 대 한 모범 사례

- **프로세스**: 중요 시점, 분석 및 명확한 팀 소유권이 있는 명확한 판매 프로세스를 정의 합니다.
- **한정**: 선행 작업이 정규화 되었는지 여부를 나타내는 필수 조건을 정의 합니다. 판매 또는 마케팅 담당자가 전체 판매 프로세스를 수행 하기 전에 신중 하 게 우량으로 선별 되도록 합니다.
- **후속**작업: 후속 작업을 잊지 마십시오. 일반적인 트랜잭션은 5 ~ 12 개의 후속 호출이 필요 합니다.
- **키우십시오**: 잠재 고객을 키우십시오 하 여 더 높은 수익 이윤으로 이동 하세요.

## <a name="leads-frequently-asked-questions"></a>자주 묻는 질문과 대답

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>리드 대상을 설정하는 데 필요한 도움은 어디에서 받을 수 있나요?

[CRM 시스템에 연결](#connect-to-your-crm-system)섹션의 단계에 따라 [파트너 센터 도움말 및 지원을](https://partner.microsoft.com/support/v2/?stage=1)통해 지원 티켓을 제출 합니다. 그런 다음 제품 **생성** > **을** > 선택 하 여**리드 관리 구성**유형을 선택 합니다.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>상업적 marketplace에서 제품을 게시 하기 위해 리드 대상을 구성 해야 하나요?

답변은 게시 하는 제품의 유형에 따라 달라 집니다. SaaS (Software as a service) 및 Dynamics 365 Customer Engagement는 **Me Me** 를 사용 하 여 재무 및 운영 제품, 모든 Dynamics 365 Business Central 제품 및 모든 컨설팅 서비스 제품에 대 한 모든 dynamics 365을 나열 합니다. 따라서 이러한 사용자에 게는 선행 대상에 대 한 연결이 필요 합니다. 제안 형식이 나열 되지 않은 경우에는 잠재 고객 대상에 대 한 연결이 필요 하지 않습니다. 비즈니스 기회가 누락 되지 않도록 리드 대상을 구성 하는 것이 좋습니다.

### <a name="how-can-i-find-the-test-lead"></a>테스트 리드를 찾으려면 어떻게 해야 하나요?

잠재 고객 `"MSFT_TEST"` 대상에서을 검색 합니다. Microsoft의 샘플 테스트 리드는 다음과 같습니다.

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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Live 제품이 있지만 잠재 고객이 보이지 않는 이유는 무엇 인가요?

잠재 고객 대상에 대 한 연결이 올바른지 확인 합니다. 파트너 센터에서 제품에 대 한 **게시** 를 선택한 후에 테스트 리드를 보내 드리겠습니다. 테스트 리더가 표시 되 면 연결이 유효 합니다. 미리 보기 단계에서 제품 미리 보기를 얻으려고 시도 하 여 리드 연결을 테스트할 수도 있습니다. 상용 marketplace의 목록에서 **지금 가져오기**, 나 **에 게 문의**또는 **무료 평가판** 을 선택 합니다.

또한 올바른 데이터를 검색 하 고 있는지 확인 합니다. 이 문서에 있는 리드 [데이터 이해](#understand-lead-data) 섹션의 내용은 잠재 고객 대상으로 보내는 리드 데이터를 설명 합니다.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Azure Blob storage를 잠재 고객 대상으로 구성 했는데 리더가 표시 되지 않는 이유는 무엇 인가요?

Azure Blob storage는 더 이상 잠재 고객으로 지원 되지 않으므로 제품에 의해 생성 된 고객 리더가 누락 됩니다. 다른 [리드 대상 옵션](./commercial-marketplace-get-customer-leads.md)으로 전환 합니다. 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>상업적 marketplace에서 전자 메일을 받았지만 CRM에서 잠재 고객을 찾을 수 없는 이유는 무엇입니까?

최종 사용자의 메일 도메인 원본이 .edu일 수 있습니다. 개인 정보 보호를 위해 .edu 도메인에서 개인 식별이 가능한 정보를 전달 하지 않습니다. [파트너 센터 도움말 및 지원을](https://partner.microsoft.com/support/v2/?stage=1)통해 지원 티켓을 제출 합니다.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Azure 테이블을 선행 대상으로 구성 했습니다. 잠재 고객을 어떻게 볼 수 있나요?

Azure Portal에서 Azure 테이블에 저장 된 잠재 고객 데이터에 액세스할 수 있습니다. 또한 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 를 다운로드 하 고 설치 하 여 Azure Storage 계정의 테이블 데이터를 볼 수 있습니다.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Azure 테이블을 선행 대상으로 구성 했습니다. 새 상업적 마켓플레이스 리더가 전송 될 때마다 알림 메시지를 받을 수 있나요?

예. Azure 테이블을 [사용 하 여 잠재 고객 관리 구성](./commercial-marketplace-lead-management-instructions-azure-table.md) 의 지침에 따라 잠재 고객이 azure 테이블에 추가 되는 경우 전자 메일을 보내는 Microsoft flow를 설정 합니다.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Salesforce를 리드 대상으로 구성 했는데 리드를 찾을 수 없는 이유는 무엇입니까?

웹-리드 양식이 선택 목록을 기반으로 하는 필수 필드 인지 확인 합니다. 인 경우 필드를 nonmandatory 텍스트 필드로 전환 합니다.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>리드 대상에 문제가 있습니다. 일부 리드가 누락되었습니다. 메일로 보내줄 수 있나요?

개인적으로 식별할 수 있는 정보 정책으로 인해 보안 되지 않은 전자 메일을 통해 잠재 고객 정보를 공유할 수 없습니다.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Azure 테이블을 선행 대상으로 구성 했습니다. 비용은 얼마 인가요?

리드 생성 데이터가 부족 합니다. 거의 모든 게시자의 경우 1gb 미만입니다. 비용은 받은 잠재 고객 수에 따라 달라 집니다. 예를 들어 한 달에 1000 잠재 고객이 수신 되 면 비용은 50 센트입니다. 저장소 가격 책정에 대 한 자세한 내용은 [Azure Storage 개요 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조 하세요.

질문에 대 한 답변이 없으면 [파트너 센터 도움말과 지원을](https://aka.ms/marketplacepublishersupport)통해 Microsoft 지원에 문의 하세요. 그런 다음 제품 **생성** > **을** > 선택 하 여**리드 관리 구성**유형을 선택 합니다.

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>새 고객 잠재 고객이 수신 되 면 전자 메일 알림을 받습니다. 다른 사람이 이러한 전자 메일을 받도록 구성 하려면 어떻게 해야 하나요?

파트너 센터에서 제품에 액세스 하 고, **잠재 고객 관리** > **편집**> **제품 설정** 페이지로 이동 합니다. **연락처 전자 메일** 필드에서 전자 메일 주소를 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

기술 설정이 적용 되 면 이러한 잠재 고객을 현재 판매 및 마케팅 전략 및 운영 프로세스에 통합 합니다. 전반적인 판매 프로세스를 보다 잘 이해 하 고 있으며 사용자와 긴밀 하 게 협력 하 여 높은 품질의 잠재 고객과 성공 하는 데 필요한 데이터를 제공 하는 데 관심이 있습니다. 이렇게 고객의 성공을 지원할 수 있도록, 추가 데이터를 제공하여 리드를 최적화하고 향상시킬 수 있는 방법에 대한 의견을 환영합니다. [사용자 의견을 제공](mailto:AzureMarketOnboard@microsoft.com) 하는 데 관심이 있는 경우 영업 팀이 상업적 marketplace 리드를 사용 하 여 더 성공적으로 사용할 수 있도록 합니다.
