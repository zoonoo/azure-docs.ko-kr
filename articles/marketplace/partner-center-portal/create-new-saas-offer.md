---
title: 새 SaaS 제품을 상업적 Marketplace에서 만들기
description: 목록 또는 Azure Marketplace, AppSource 또는 상용 Marketplace 포털을 사용 하 여 Microsoft 파트너 센터에서 클라우드 솔루션 공급자 (CSP) 프로그램을 통해 판매에 대 한 서비스 (SaaS) 제품으로 새 소프트웨어를 만드는 방법입니다.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 897634ac0c30b4a2cc496c225965f465a94fe4e9
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66751887"
---
# <a name="create-a-new-saas-offer"></a>새 SaaS 제품을 만드는

서비스 (SaaS)에서 제공 하는 대로 소프트웨어를 만들기를 시작 하려면는 첫 번째 [파트너 센터 계정 만들기](./create-account.md) 연 합니다 [상용 Marketplace 대시보드](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)를 사용 하 여는 **개요** 탭을 선택 합니다.

![파트너 센터에서 상용 Marketplace 대시보드](./media/new-offer-overview.png)

선택 된 + **새...** 단추를 클릭 합니다 **Software-as-a-service** 메뉴 항목입니다. 

다른 제품 유형 중 하나를 선택 하면 리디렉션됩니다 이전의 [클라우드 파트너 포털](https://cloudpartner.azure.com/)합니다.  지금은 SaaS 제공 파트너 센터에 있는 상용 Marketplace 포털에서 제공 됩니다. 

![파트너 센터에서 제품 창 만들기](./media/new-offer-click.png)

합니다 **새 제품** 대화 상자가 표시 됩니다. 

![새 제품 대화 상자](./media/new-offer-popup.png)


## <a name="offer-id-and-name"></a>제품 ID 및 이름

- **제품 ID**: 계정에 각 제품에 대 한 고유 식별자를 만듭니다. 이 ID는 marketplace 제품 및 Azure Resource Manager 템플릿 (있는 경우)에 대 한 URL 주소에는 고객에 게 표시 됩니다. 제품 ID는 소문자, 영숫자 (밑줄, 하이픈 하며 공백이 없어야 포함) 여야 합니다. 이 50 자로 제한 됩니다 및 선택한 후에 업데이트할 수 없습니다 만듭니다.  
예: 테스트-제품-1
<br>URL을 생성 합니다. `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **제품 이름**: SaaS 응용 프로그램 제품을 게시, 광고 및 웹 사이트 전체에서 일관성의 공식 이름입니다.  이 이름은 상표 될 수 있습니다.  이름을 공백,이 모 지 있어야 (아닌 상표 또는 저작권 기호)을 제공 하며 50 자로 제한 해야 합니다.
<br>예제: 테스트 제품 1&#8482;

**만들기**를 선택합니다.  **개요를 제공** 이 제품에 대 한 페이지가 생성 됩니다.  

![파트너 센터에 대 한 개요를 제공 합니다.](./media/commercial-marketplace-offer-overview.png)

## <a name="offer-overview"></a>제품 개요

합니다 **개요를 제공** 페이지에 포함 됩니다. 

- 합니다 **게시 상태** 시각적으로이 제품 및 각 단계를 완료에 소요 됩니다을 게시 하는 데 필요한 단계를 표시 합니다. 불완전 한 게시 단계 아이콘이 회색으로 나타납니다. 

- 합니다 **개요를 제공** 메뉴에는이 제품에 대 한 작업을 수행 하는 것에 대 한 링크의 목록을 포함 합니다. 작업의이 목록은 제품에 대 한 선택에 따라 변경 됩니다.  
    - 제품은 초안이 – 초안 삭제 하는 경우 
    - 제품을 라이브 – 중지 하는 경우 제품을 판매 합니다. 
    - 제품 미리 보기 상태인 – 라이브 하는 경우 
    - 게시 취소 게시자 로그 – 아웃을 완료 하지 않은 경우

## <a name="offer-setup"></a>제품 설치

합니다 **설치 프로그램을 제공** 탭에서 다음 정보를 요청 합니다. 선택 **저장할** 이러한 필드를 완료 합니다.

- **Microsoft를 통해 판매 하 시겠습니까?** (예/아니요)
    - **예**를 통해 사용자를 대신해; marketplace 트랜잭션을 호스팅하는 Microsoft와 Microsoft 제품을 판매 하려는 또는 
    - **이상**를 선호 하 방금 마켓플레이스를 통해 제품을 나열 하려면 Microsoft와 독립적으로 금전 거래를 처리 합니다.    

### <a name="sell-through-microsoft"></a>Microsoft를 통해 판매

Microsoft 사용자 대신 호스트 marketplace 트랜잭션 수는 더 나은 고객 검색 및 데이터 획득 제공 Microsoft를 통해 판매 및 Microsoft의 전역적으로 사용 가능한 전자 상거래 기능을 활용 합니다.

#### <a name="saas-offer-requirements"></a>SaaS 제품 요구 사항

소프트웨어 목록 상용 Marketplace 파트너 센터를 사용 하 여 서비스 (SaaS)을 제공 하기 위해 다음 기준은 충족 되어야 합니다.

- 제품을 Azure 클라이언트와 호환 되어야 합니다. (종종 SaaS 앱은 또한 최상의 성능 및 호환성을 위해 Azure에서 호스트 됩니다 있지만 이것이 요구 사항은 없습니다.) 
- 제품을 사용 해야 합니다 [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) id 관리 및 인증에 대 한 합니다.
- 제품을 사용 해야 합니다 [SaaS Fulfillment Api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) Azure Marketplace에 통합할 수 있습니다.

#### <a name="billing-infrastructure-costs"></a>인프라 비용 청구
SaaS 제품의 경우, 게시자로 고려해 야 사용량 요금 Azure 인프라 및 소프트웨어 라이선스 요금에 대 한 단일 비용 항목으로 합니다. 이 비용은 고객에 게 월별 고정 요금으로 표시 됩니다. Azure 인프라 사용 관리 되 고의 파트너에 게 직접 청구 됩니다. 실제 인프라 사용 요금은 고객에게 표시되지 않습니다. 일반적으로 게시자는 Azure 인프라 사용 요금을 소프트웨어 라이선스 가격 책정에 추가하는 것을 선택합니다. 

소비 기반 하거나 소프트웨어 라이선스 요금을 월간, 되풀이 사이트 기반 구독 요금이 요금으로 표시 됩니다 및 측정 되지 됩니다.

|**라이선스 비용**|**월 100 달러**|
|:---|:---|
|Azure 사용량 비용(D1/1개 코어)|고객이 아닌 게시자에게 직접 청구됩니다.|
|고객은 Microsoft에서 청구 되는|$ 100.00 / 월 (게시자 라이선스 요금에 모든 발생 또는 통과 인프라 비용을 고려해 야 합니다.)|

- 이 시나리오에서 Microsoft는 소프트웨어 라이선스에 대해 $100.00를 청구하고, 게시자에게 $80.00를 지급합니다.
- 한 파트너는 **Marketplace 서비스 요금 감소** SaaS에 감소 거래 수수료는 월 2019 년 6 월 2020까지에서 제공 하는 데 나타납니다. 이 시나리오에서는 Microsoft $100.00 소프트웨어 라이선스에 대 한 청구 및 게시자에 매출 $90.00 초과 비용을 지불 합니다.

> [!NOTE]
> **Marketplace 서비스 요금을 감소**: 특정 SaaS는 상용 Marketplace에 게시에 Microsoft 줄어듭니다 해당 Marketplace 서비스 요금에서 20% (Microsoft 판매자 계약에 설명)으로 10%를 제공 합니다. 되려면 제품에 대 한 순서로 제품 중 하나 이상 지정 되어 있어야 microsoft IP 공동 판매 준비 또는 우선 순위를 지정 하는 IP 공동 판매도 합니다.  자격 달에이 감소 하는 Marketplace 서비스 요금을 받기 위해 각 월의 종료 되기 전에 영업일 최소 5 (5) 충족 되어야 합니다.  감소 Marketplace 서비스 요금 Vm, 관리 되는 앱 또는 상용 Marketplace를 통해 사용할 수 있는 다른 제품에 적용 되지 않습니다.  감소 Marketplace 서비스 요금 2019 년 5 월 1 일 사이의 2020 년 6 월 30 년 Microsoft에서 수집 하는 라이선스 비용에 대해 정규화 된 제품으로 제공 됩니다.  이 시간이 지난 후 Marketplace 서비스 요금 고 일반 크기와 돌아갈 것입니다. 

|**Microsoft에 청구되는 요금**|**월 100 달러**|
|:---|:---|
|Microsoft는 라이선스 비용의 80%를 지불합니다. <br>**정규화 된 SaaS 앱에 대 한 Microsoft 라이선스 비용의 90%를 지불*|매월 $80.00 <br>*$* 월 * 별 90.00|


#### <a name="csp-program-opt-in"></a>CSP 프로그램 옵트인
[클라우드 솔루션 공급자 (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) 프로그램에서는 수백만 개의 최소 정규화 된 Microsoft 고객에 게 도달 하기 위해 소프트웨어 제품 마케팅 및 판매 투자 합니다.

- **채널: 내 제품을 CSP 프로그램에서 사용할 수 있도록** (확인란)

CSP 프로그램에서 사용할 수 있는 제품 수 있도록 선택 번들된 솔루션의 일부로 서 고객에 게 제품을 판매 하도록 클라우드 솔루션 공급자 수 있습니다. 

### <a name="list-through-microsoft"></a>Microsoft 통해 목록

Marketplace 명단을 만들어 Microsoft 사용 하 여 비즈니스를 홍보 합니다. 선택 하 고만 제품을 나열 하지 Microsoft를 통해 Microsoft 소프트웨어 라이선스 트랜잭션을 직접 참여 하지 않습니다 하는 것을 의미 합니다. 연결 된 거래 수수료 없음 되며 게시자는 고객 으로부터 수집 라이선스 소프트웨어의 100%를 유지 합니다. 그러나 게시자에 대 한 책임이 포함 하지만에 제한 되지 않는 소프트웨어 라이선스 트랜잭션의 모든 측면을 지 원하는: 주문이 행, 계량, 청구, 송장, 지불 및 컬렉션입니다. 

- **잠재 고객을이 목록 제품과 상호 작용 하 고 시겠습니까?**

##### <a name="get-it-now-free"></a>지금 가져오기 (무료)
무료로 앱을 액세스할 수 있는 올바른 URL (http 또는 https로 시작)를 제공 하 여 고객에 게 제품을 나열 합니다.  예: `https://contoso.com/saas-app`

##### <a name="free-trial"></a>평가판
앱을 액세스할 수 있는 올바른 URL (http 또는 https로 시작)를 제공 하 여 무료 평가판으로 고객에 게 제품을 나열 합니다.  예: `https://contoso.com/trial/saas-app`

##### <a name="contact-me"></a>연락처
고객 관계 관리 (CRM) 시스템을 연결 하 여 고객 연락처 정보를 수집 합니다. 고객은 해당 정보를 공유 하는 권한에 대 한 라는 메시지가 나타납니다. 제품 이름, ID 및 제품을 찾을 수 있는 marketplace 소스와 함께 이러한 고객 세부 정보를 구성한 경우 CRM 시스템에 전송 됩니다. CRM을 구성 하는 방법에 대 한 자세한 내용은 참조 하세요. [Connect 잠재 고객 관리](#connect-lead-management)합니다. 

## <a name="example-marketplace-offer-listing"></a>예제에서는 marketplace 제품 목록

![예제에서는 marketplace 제품 정보를 사용 하 여 나열 합니다.](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>시험 사용을 사용하도록 설정

테스트 드라이브가 '구입 전에 사용해 볼' 수 있도록, 향상 된 변환 되 고 항상 정규화 된 잠재 고객을 생성 하 여 잠재적인 고객에 게 제품을 소개 하는 좋은 방법입니다. [시험 사용에 대해 알아봅니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **시험 사용** (확인란) 

테스트 드라이브를 사용 하 여 고정 된 기간에 대 한 제품을 고객에 대 한 데모 환경을 구성 하 라는 메시지가 표시 됩니다. 

### <a name="type-of-test-drive"></a>테스트 드라이브의 유형

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : 솔루션을 구성 하는 모든 Azure 리소스를 포함 하는 배포 템플릿. 이 시나리오에 맞는 제품에는 Azure 리소스에만 사용 합니다.
- **[Dynamics 365 Business Central에 대 한](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft 호스트 하 고 시스템을 계획 하는 Business Central 엔터프라이즈 리소스에 대 한 프로 비전 및 배포 등 테스트 구동 서비스를 유지 관리 (재무, 작업, 공급망, CRM, 등.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft를 호스트 하 고 고객 시스템 (sales, 서비스, 프로젝트 서비스, 현장 서비스 등)에 대 한 테스트 구동 서비스 프로 비전 및 배포 등을 유지 합니다.  
- **[작업에 대 한 Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft를 호스트 하 고 시스템 (finance, operations, 제조, 공급망 등) 계획 Finance and Operations enterprise 리소스에 대 한 테스트 구동 서비스 프로 비전 및 배포 등을 유지 합니다. 
- **[Logic app](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : 모든 복잡 한 솔루션 아키텍처를 포함 하는 배포 템플릿. 사용자 지정 제품이이 유형의 테스트 드라이브를 사용 해야 합니다.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : 사용자 지정 빌드 대시보드에 포함 된 링크입니다. 제품은 대화형 Power BI 시각적 개체는이 유형의 테스트 드라이브를 사용 해야 하는 방식을 보여 주기 위해입니다. 포함된 Power BI URL만 업로드하면 됩니다.

#### <a name="additional-test-drive-resources"></a>추가 테스트 드라이브 리소스
- [테스트 기술 모범 사례를 이끄는](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Test Drive 마케팅 모범 사례](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [드라이브 개요 하나 호출기 테스트](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>잠재 고객 관리를 연결 합니다.

고객은 marketplace에서 제품을 나열 하 고 고객이 관심을 표 또는 배포 후에 즉시 고객 연락처 정보를 받을 수 있도록 고객 관계 관리 (CRM) 시스템을 연결 하 여 직접 연결 프로그램 제품입니다.

- **리드 대상을 선택** (드롭다운 메뉴): 하려는 잠재 고객을 전송할 CRM 시스템에 연결 세부 정보를 제공 합니다. 

파트너 센터는 잠재 고객 관리에 대 한 다음 CRM 시스템을 지원합니다. 설치 지침을 보려면 링크를 선택 합니다.

- Azure Blob – 제공 연락처 메일, 컨테이너 이름 및 저장소 계정 연결 문자열입니다. 
- [Azure 테이블](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) – 연락처 전자 메일 및 저장소 계정 연결 문자열을 제공 합니다. 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – 연락처 전자 메일, URL 및 인증 모드 (Office 365 또는 Azure Active Directory)를 제공 합니다.
- [Https 끝점](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) – 연락처 전자 메일 및 HTTPS 끝점 URL을 제공 합니다. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – 연락처 전자 메일, 형식 ID, Munchkin 계정 ID 및 서버 ID를 제공 합니다.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) -연락처 전자 메일 및 조직 ID를 제공 합니다. 

#### <a name="additional-lead-management-resources"></a>추가 잠재 고객 관리 리소스
- [잠재 고객 관리 Faq](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [일반적인 잠재 고객 구성 오류](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [관리 개요 하나 호출기 잠재 고객](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

해야 **저장할** 다음 섹션으로 이동 하기 전에!

## <a name="properties"></a>properties
합니다 **속성** 탭 요청 범주 및 마켓플레이스를, 제품 및 앱 버전을 지 원하는 법적 계약에 대 한 제품을 그룹화 하는 데 사용 되는 산업을 정의할 수 있습니다. 

선택 **저장할** 이러한 필드를 완료 합니다. 

### <a name="category"></a>Category
최소의 1 개 및 최대 3 개의 적절 한 marketplace 검색 영역으로 제품을 그룹화 하는 데 사용 되는 범주를 선택 합니다. 제품 제품 설명에서 이러한 범주를 지원 하는 방법을 살펴봅니다. 

### <a name="industry"></a>산업
적절 한 marketplace 검색 영역으로 제품을 그룹화 하는 데 최대 2 개의 산업을 선택 합니다. 제품 업계와 관련이 없는 경우 하나 선택 하지 마십시오. 제품이 제품 설명에서 선택한 산업을 지 원하는 방법을 호출 하세요. 

### <a name="app-version"></a>앱 버전
AppSource marketplace에서 제품의 버전 번호를 식별 하는 데 사용 하는 선택적 필드입니다. 

### <a name="standard-contract"></a>표준 계약

- **표준 계약 사용**

고객에 대 한 조달 프로세스를 간소화 하 고 소프트웨어 공급 업체에 대 한 법적 복잡성을 줄이기, Microsoft는 marketplace에서 트랜잭션을 용이 하 게 하기 위해 표준 계약 템플릿을 제공 합니다. 

사용자 지정 사용 약관을 작성 하는 대신 Azure Marketplace 게시자 고객에 게 심사 하 고 동의 한 번만 해야 하는 표준 계약에 따라 해당 소프트웨어를 제공할 수 있습니다. 

표준 계약을 확인할 수 있습니다: https://go.microsoft.com/fwlink/?linkid=2041178합니다.

#### <a name="terms-of-use"></a>사용 약관

라이선스 사용 약관이 표준 계약에서 다른 경우에 고유한 사용 약관의 여기 입력 선택할 수 있습니다. 또한이 필드에 텍스트의 최대 10,000 개의 문자를 입력할 수 있습니다. 사용 약관 보다 자세한 설명에 필요한 경우 추가 사용 조건을 찾을 수 있는이 필드에 단일 URL 링크를 입력 합니다. 활성 링크도 고객에 게 표시 됩니다.

고객은 이러한 사용 약관에 동의해야 앱을 사용할 수 있습니다. 

해야 **저장할** 다음 섹션으로 이동 하기 전에!

## <a name="offer-listing"></a>제품 목록

현재 나열 탭 표시 언어 (시장) 제품을 사용할 수 있는 제품을 영어 (미국) 사용할 수 있는 유일한 위치를입니다. 또한이 페이지는 언어별 목록 및 추가 된 날짜/시간의 상태를 표시 합니다. 각 언어에 대 한 marketplace 세부 정보 (이름, 설명, 검색어, 등 제공)를 정의 해야 시장 /입니다.

### <a name="offer-listings"></a>제품 목록

Marketplace에 제품 설명을 포함 하 여 및 마케팅 자산에 표시할 세부 정보를 제공 합니다.

- **이름** (필수): 여기에 정의 된 이름 목록에 선택한 marketplace(s) 제품의 제목으로 표시 됩니다. 이름을 기준으로 이전에 미리 채워져 **새 제품** 항목입니다.  이 상표 될 수 있습니다.  이 없어야 합니다. 공백,이 모 지 (상표 및 저작권 기호 아니면) 및 50 자로 제한 되어야 합니다.
- **요약** (필수): Marketplace 등록 항목 검색 결과에 사용할 제품에 대 한 간단한 설명을 제공 합니다. 이 필드에는 최대 100 자 텍스트를 입력할 수 있습니다.
- **설명** (필수): Marketplace 등록 항목 개요에서 표시할 제품에 대 한 설명을 제공 합니다. 가치를 주요 이점, 범주 또는 산업별 연결을 포함 하는 것이 좋습니다. 공개, 및 자세한 내용을 보려면 링크 필요한 인 앱 구매 기회입니다.
이 필드에는 최대 3,000 자 텍스트를 입력할 수 있습니다. 추가 팁에 대 한 문서를 참조 [멋진 앱 설명을 작성](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)합니다.
- **키워드 검색**: 고객에 게는 marketplace(s)에서 제품을 찾는 데 사용할 수 있는 최대 3 개의 검색 키워드를 입력 합니다.
- **지침 시작** (필수): 구성 및 잠재 고객에 대 한 앱을 사용 하 여 시작 하는 방법을 설명 합니다.  이 빠른 시작에 자세한 온라인 설명서의 링크를 포함할 수 있습니다. 이 필드에는 최대 3,000 자 텍스트를 입력할 수 있습니다. 

#### <a name="links"></a>링크

- **개인 정보 취급 방침** (필수): 조직의 개인 정보 취급 방침에 연결 합니다. 앱이 개인 정보 보호 법률 및 규정 준수를 보장 하 고 유효한 개인 정보 취급 방침을 제공 하는 데는
- **CSP 프로그램에 대 한 마케팅 자료** (선택 사항): 에 제품을 확장 하려는 경우 마케팅 자료에 대 한 링크를 제공 해야 합니다 [클라우드 솔루션 공급자 (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) 프로그램입니다. CSP는 번들을 시장에 제품을 재판매 하는 CSP 파트너를 사용 하 여 광범위 한 정규화 된 고객에 제품을 확장 합니다. 이들이 대리점에 제품을 마케팅 자료에 액세스를 해야 합니다. 자세한 내용은 [Go 시장 진출 서비스](https://partner.microsoft.com/reach-customers/gtm)합니다.
- **유용한 링크** (선택 사항): 앱 또는 관련된 서비스를 제공 하 여 나열 하는 방법에 대 한 선택적 추가 온라인 문서를 **제목** 하 고 **URL**합니다. 클릭 하 여 추가 유용한 링크를 추가할 **+ 추가 URL**합니다.

#### <a name="contact-information"></a>연락처 정보

- **연락처**: 각 고객 연락처에 대 한 직원을 제공 **이름을** 를 **전화 번호**, 및 **전자 메일** 주소입니다.  (이러한 *것입니다* 공개적으로 표시). A **지원 URL** 에 필요 합니다 **지원 연락처** 그룹입니다.  (이 정보 *는* 공개적으로 표시).

**지원 담당자** (필수): 일반적인 지원 질문 합니다.

**엔지니어링 연락처** (필수): 기술 관련 질문 합니다.

**관리자 연락처 채널** (필수): CSP 프로그램에 관련 된 reseller 질문 합니다.

#### <a name="files-and-images"></a>파일 및 이미지

- **문서** (필수): 최소의 1 개 및 최대 3 개의 제품당 문서를 제공 하는 PDF 형식으로 제품을 마케팅 관련된 문서를 추가 합니다.
- **이미지** (선택 사항): 제품 로고 이미지 다음 크기-작은 요구는 marketplace(s) 전체 나타날 수 있는 여러 위치 가지가 있습니다. 48x48 픽셀 _(필수),_ 보통: 큰 90 x 90 픽셀: 216x216 픽셀 _(필수),_ 다양 합니다. 255 x 115 픽셀 및 대표 합니다. 815 x 290 픽셀입니다. 모든 이미지 여야 합니다. PNG 형식입니다.
- **스크린 샷을** (필수): 제품을 보여 주는 스크린 샷을 추가 합니다. 최대 5 개의 스크린샷 추가할 수 있습니다 및 1280 x 720 픽셀 크기를 조정 해야 합니다. 모든 이미지 여야 합니다. PNG 형식입니다.
- **비디오** (선택 사항): 제품을 보여 주는 비디오에 대 한 링크를 추가 합니다. 고객에게 제품과 함께 표시되는 YouTube 및/또는 Vimeo 동영상의 링크를 사용할 수 있습니다. 비디오의 썸네일 이미지를 입력 해야 1280 x 720 픽셀 PNG 형식에서으로 조정 합니다. 제품 당 4 개의 비디오 최대를 표시할 수 있습니다.

해야 **저장할** 다음 섹션으로 이동 하기 전에!

#### <a name="additional-marketplace-listing-resources"></a>추가 marketplace 목록 리소스

- [Marketplace에 대 한 모범 사례 목록은 제공](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>미리 보기

합니다 **미리 보기** 탭을 사용 하면 제한 된 정의할 수 있습니다 **미리 보기 대상** marketplace 사람이 라이브 제품을 게시 하기 전에 제품을 릴리스 합니다.

> [!IMPORTANT]
> 선택 해야 합니다 **라이브로 전환** 제품 미리 보기에서 제품을 확인 한 후 라이브에 marketplace 공개 청중에 게 게시할 수 됩니다.

- **대상 그룹을 미리 보기를 정의 합니다. 단일 AAD/MSA 계정 전자 메일에 대 한 선택적 설명 함께 줄을 추가 합니다.**

최대 10 개로 전자 메일 주소를 수동으로 추가 하거나 20 (20) 기존 Microsoft 계정 (MSA)에 대 한 CSV 파일 업로드 또는 게시 하기 전에 제품을 유효성 검사에 도움이 되도록 Azure Active Directory (AAD) 계정을 live 합니다. 이러한 계정에 추가 하면 허용 되는 미리 보기 제품에 대 한 액세스는 marketplace(s)를 게시 하기 전에 대상을 정의 됩니다. 제품 라이브 이미 상태인 경우 모든 변경 내용이 나 업데이트 제품을 테스트 하는 것에 대 한 미리 보기 대상을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상을 개인 대상에서 다릅니다. 대상 그룹을 미리 보기 제품에 대 한 액세스를 허용 _이전_ 는 marketplace에서 라이브 게시 되 고 있습니다. 계획을 만들고 개인 대상에만 사용할 수 있도록 하려면 수도 있습니다. 에 **목록 계획** 탭을 사용 하 여 개인 대상을 정의할 수 있습니다 합니다 **비공개 계획입니다** 확인란을 선택 합니다. Azure 테 넌 트 Id를 사용 하 여 최대 20,000 명의 고객의 개인 대상을 정의할 수 있습니다.

## <a name="technical-configuration"></a>기술 구성

합니다 **기술 구성** 탭 기술 세부 정보를 정의 (예: URL 경로, webhook, 테 넌 트 ID 및 앱 ID) 제품에 연결 하는 데 사용 합니다. 이 연결을 통해 가져오는 선택 하는 경우 제품으로 고객의 Azure 구독에서 리소스를 프로 비전 할 수 있습니다.

- **방문 페이지 URL** (필수): Marketplace에서 제품을 가져오면를 고객에 게 될 URL 전달 하는 사이트를 정의 합니다. 이 URL은 Microsoft와의 거래를 용이하게 하기 위한 연결 API를 수신할 엔드포인트로도 사용됩니다.

- **연결 웹 후크** (필수): Microsoft에서 고객을 대신해서 보내야 하는 모든 비동기 이벤트(예: Azure 구독에 잘못 된)를 완료 해야 연결 웹 후크를 제공할 수 있습니다. 가장 단순한 구성에 게시 되는 모든 이벤트에 대 한 수신 대기 하 고 다음 적절 하 게 처리 하는 HTTP 끝점 논리 앱은 현재 위치에서 웹 후크 시스템 없는 경우 (예: https:\//prod-1westus.logic.azure.com:443/work). 자세한 내용은 [Logic Apps의 HTTP 엔드포인트를 통해 워크플로 호출, 트리거 또는 중첩](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)을 참조하세요.

- **Azure AD 테 넌 트 ID** (필수): Azure portal 내에서는 위해서는 있습니다 [Azure AD (Active Directory) 앱 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 뒤에 인증 된 통신은 두 서비스 간의 연결 검증할 수 있도록 합니다. 찾을 수는 [테 넌 트 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id), Azure Active Directory로 이동 및 선택 **속성**, 찾은 후에 대 한 합니다 **디렉터리 ID** 번호 (예: 나열 된 50c464d3-4930-494c-963c-1e951d15360e)입니다.

- **Azure AD 앱 ID** (필수): 해야 하면 [응용 프로그램 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) 및 인증 키입니다. 이러한 값을 얻으려면 Azure Active Directory로 이동 및 선택 **앱 등록**을 찾습니다 합니다 **응용 프로그램 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 나열 합니다. 인증 키를 찾으려면에서로 이동 **설정을** 선택한 **키**합니다. 설명 및 기간과 다음에 숫자 값을 지정할 수 있도록 해야 합니다.

 Azure 응용 프로그램 ID는 게시자 ID에 연결 하 고 있으므로 응용 프로그램 ID가 동일한 모든 제품에서 사용 되는 해야 합니다.

## <a name="plan-overview"></a>계획 개요

합니다 **계획 개요** 탭을 사용 하면 여러 가지 동일한 제품 내 계획 옵션을 제공할 수 있습니다. 이러한 계획 (Sku 라고도 함) 버전, 수익 또는 서비스 계층의 측면에서 다를 수 있습니다. Marketplace에서 제품을 판매 하려면 하나 이상의 계획을 설정 해야 합니다.

를 만든 후 표시 됩니다에 계획 이름, Id, 가격 책정 모델, 가용성 (공용 또는 개인), 현재 상태 및 사용 가능한 모든 작업을 게시 합니다.

**작업** 에서 사용할 수 있는 합니다 **계획 개요** 계획의 현재 상태에 따라 다르며 포함 될 수 있습니다.

- 계획 상태가 **초안** – 초안 삭제
- 계획 상태가 **Live** – 계획 또는 동기화 개인 대상 중지 판매

**새 계획을 만들** (Microsoft를 통해 판매를 선택 하는 사람에 게 하나의 계획 최소)

- **계획 ID:** 이 제품에 각 계획에 대 한 고유한 계획 ID를 만듭니다. 이 ID는 제품 URL 및 Azure Resource Manager 템플릿 해당 하는 경우에 고객에 게 표시 됩니다. 소문자 영숫자 문자, 대시 또는 밑줄만 사용 합니다. 이 계획 ID에 대 한 최대 50 자의 수 Note ID 선택 하면 생성 후 수정할 수 없습니다.
- **계획 이름:** 고객은 제품 내에서 선택 하는 계획을 결정할 때이 이름이 표시 됩니다. 이 제품에 각 계획에 대 한 고유한 이름을 만듭니다. 계획 이름 (예: 동일한 제품의 일부가 될 수 있는 소프트웨어 계획을 구별 하는 데는 제품 이름: Windows Server; 계획: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>목록 계획

합니다 **계획 목록** 계획할 수 언어 (및 시장) 탭에 표시 됩니다 현재 영어 (미국)은 사용할 수 있는 유일한 위치입니다. 또한이 페이지는 언어별 목록 및 추가 된 날짜/시간의 상태를 표시 합니다. 각 언어에 대 한 marketplace 세부 정보 (이름, 설명, 검색어, 등 제공)를 정의 해야 시장 /입니다.

#### <a name="plan-listing-details"></a>계획 세부 정보를 나열 합니다.

계획 언어 중 하나를 선택 하면 표시 됩니다는 **계획 목록** 정보를 포함 하 여 **이름** 및 **설명 합니다.**

- **이름**: 미리 채워진 미리 보기에 따라 **새 계획** 항목 제품의 "소프트웨어 계획의" marketplace에 표시 된 제목으로 표시 됩니다.
- **설명:** 이 설명은 점에서이 소프트웨어 계획과 고유 설명 하는 기회와 제품 내에서 다른 소프트웨어 계획에서 차이점입니다. 최대 500 개의 문자를 포함할 수 있습니다.

선택 **저장할** 이러한 필드를 완료 합니다.

#### <a name="plan-pricing-and-availability"></a>계획 가격 책정 및 가용성

합니다 **가격 책정 및 가용성** 탭을 사용 하면이 계획에서 사용할 수 있는 시장을 구성할 수 있습니다 원하는 수익 모델, 가격 및 청구 기간입니다. 또한 모든 사용자 또는 특정 고객 (개인 대상)에 계획을 표시할 것인지 지정할 수 있습니다.

#### <a name="markets"></a>시장

- **시장 편집** (선택 사항)

모든 계획에 하나 이상의 시장에서 사용할 수 있어야 합니다. 이 계획을 사용할 수 있도록 하려는 모든 시장 위치에 대 한 확인란을 선택 합니다. 검색 상자 및 단추 선택 "세금 송금" 국가 Microsoft remits 판매 및 사용자를 대신해를 사용 하 여 세금에 대 한 포함 되어 있습니다. 


미국 달러 (USD)의 계획에 대 한 가격을 이미 설정 하 고 다른 시장 위치를 추가 하는 경우 새로운 시장에 대 한 가격은 현재 환율에 따라 계산 됩니다. 게시 하기 전에 각 시장에 대 한 가격을 항상 확인 해야 합니다. 가격 책정 변경 내용을 저장 한 후 "가격 (xlsx) 내보내기" 링크를 사용 하 여 검토할 수 있습니다.

#### <a name="pricing"></a>가격

- **가격 책정 모델**: 요금이 또는 기반 사용자

**정액 요금:** 단일 월간 또는 연간 가격 정액 요금 가격을 사용 하 여 제품에 대 한 액세스를 사용 하도록 설정 합니다. 이 경우에 따라 라고 가격 책정 사이트 기반으로 합니다.

**사용자 기반:** 제품에 액세스 하거나 차지 하는 사용자의 수에 따라 가격을 사용 하 여 제품에 대 한 액세스를 사용 하도록 설정 *좌석*합니다. 이 사용자 기반 모델을 사용 하면 최소를 설정할 수 있습니다 및 가격을 기준으로 최대 허용 사용자 수입니다. 이 이렇게 하면 다른 4백 6십만 구성할 수 있습니다 여러 계획을 구성 하 여 사용자의 수에 따라.  이러한 필드는 선택 사항입니다. 비워두는 경우 실제 사용자 수 (최소 1) 및 시스템에서 지원할 수 만큼의 최대 제한 없는 것으로 해석 됩니다. 계획에 대 한 업데이트의 일부로 이러한 필드를 편집할 수 있습니다.

게시 한 후에 청구 가격 책정 모델 선택을 변경할 수 없습니다. 또한 동일한 제품에 대 한 모든 계획과 동일한 가격 책정 모델을 공유 해야 합니다.

- **청구 기간**: 월간 또는 연간

고객에 게 표시 된 가격을 지불 해야 하는 빈도 선택 합니다. 하나 이상의 월간 또는 연간 가격을 제공 해야 합니다 또는 옵션을 모두 사용할 수 있습니다 고객에 게 합니다.

- **가격**: 월 당 USD 또는 연도 당 USD

현지 통화에서 집합 가격 (USD = 미국 달러)을 설치 하는 동안 사용할 수 있는 현재 환율을 사용 하 여 선택한 모든 시장 로컬 통화로 변환 됩니다. 내보낼 스프레드시트 가격 및 각 시장에서 가격을 검토 하 여 게시 하기 전에 이러한 가격을 확인 합니다. 개별 시장에서 사용자 지정 가격을 설정 하려는 경우 수정 하 고 가격 책정 스프레드시트를 가져옵니다. 이러한 설정은 소유 한 가격 책정이 유효성 검사에 대 한 책임이 있습니다.
**가격 데이터 내보내기 사용 하도록 설정 하려면 가격 책정 변경 내용을 먼저 저장 해야 합니다.*

계획을 게시 된 후 변경 내용에 대 한 제한으로 게시 하기 전에 신중 하 게 가격을 검토 합니다.

- 계획을 게시 한 후에 가격 책정 모델을 변경할 수 없습니다.
- 청구 기간을 계획에 대 한 게시 되 면 나중에 제거할 수 없습니다.
- 계획의 약 세 시장 가격에 게시 한 후 나중에 변경할 수 없습니다.

### <a name="plan-audience"></a>대상 계획

선택한 대상에 특정 또는 모든 사용자에 게 표시 되도록 각 계획을 구성 하는 옵션이 있습니다. Azure AD 테 넌 트 Id를 사용 하 여이 제한 된 대상 그룹의 멤버 자격을 할당할 수 있습니다.

#### <a name="privacy"></a>개인 정보 보호

- **이 비공개 계획** (확인란 선택 사항)

계획에 사설 및 원하는 제한 된 대상에만 표시 되도록이 확인란을 선택 합니다. 개인 계획으로 게시 한 후에 대상 그룹 업데이트 하거나 계획을 사용할 수 있도록 모든 사용자가 선택할 수 있습니다. 모든 사용자에 게 표시 되는 계획 게시 되 면 모든 사람이 볼 수 있어야 합니다. (계획을 구성할 수 없습니다 개인 계획으로 다시).

- **제한 된 사용자 (테 넌 트 Id)**

이 비공개 계획에 대 한 액세스를 갖게 되는 대상 그룹을 할당 합니다. 액세스 옵션을 사용 하 여 테 넌 트 Id를 사용 하 여 할당 된 각 테 넌 트 ID에 대 한 설명을 포함 하도록 할당 됩니다. 최대 10 명의 테 넌 트 Id 추가할 수 있습니다 또는 높은 고객 20,000.csv 스프레드시트 파일을 가져오는 경우 Id 테 넌 트입니다.

테 넌 트에는 GUID (Globally Unique Identifier, 128 비트 정수 숫자 리소스를 식별 하는 데 사용)로 표현 하는 ID 사용 하 여 조직의 표현입니다. 조직 또는 앱 개발자가 Microsoft와의 관계를 만들 때(예: Azure, Microsoft Intune 또는 Microsoft 365에 등록) 조직 또는 앱 개발자가 받는 Azure AD의 전용 인스턴스입니다. 각 Azure AD 테넌트는 서로 전혀 다르고 다른 Azure AD 테넌트와 별개입니다. 테 넌 트를 확인 하려면 응용 프로그램을 관리 하는 데 사용할 계정 사용 하 여 Azure portal에 로그인 합니다. 테넌트가 있으면 자동으로 로그인되고 계정 이름 바로 아래에 테넌트 이름이 표시됩니다. Azure Portal의 오른쪽 위에 있는 사용자의 계정 이름을 가리켜 사용자의 이름, 이메일, 디렉터리/테넌트 ID(GUID) 및 사용자의 도메인을 표시합니다. 계정이 여러 테넌트와 연결되어 있으면, 계정 이름을 선택하여 테넌트 간에 전환할 수 있는 메뉴를 열 수 있습니다. 각 테넌트가 자기만의 ID를 가지고 있습니다. 도메인 이름 URL을 사용 하 여 조직의 테 넌 트 ID를 조회할 수도 있습니다: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com)합니다.

개인 사용자를 정의 하려면 테 넌 트 Id를 사용 하는 SaaS 제품을 다른 제품 유형 (또한 Guid로 표현 됨)는 Azure 구독 Id를 사용할 수 있습니다.

> [!NOTE]
> 개인 사용자 (또는 제한 된 대상) 미리 보기 대상에서 다릅니다. 에 **[미리 보기](#preview)** 탭 미리 보기 대상을 정의할 수 있습니다. 대상 그룹을 미리 보기 제품에 대 한 액세스를 허용 *이전* 라이브 marketplace에 게시 되는 제품입니다. 개인 사용자를 지정 하는 특정 계획에만 적용 됩니다, 미리 보기 대상을 모든 계획을 볼 수 (개인 여부), 계획을 테스트 하 고 유효성을 검사 하는 동안 제한 된 미리 보기 기간 중에 합니다.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Marketplace 제품 내에서 계획의 예제 목록

![예제에서는 marketplace 계획 정보를 사용 하 여 나열 합니다.](./media/marketplace-plan.svg)

## <a name="test-drive"></a>시험 사용

**시험** 탭을 사용 하면 데모 (또는 "시험")를 설정 하면 고객 구매를 커밋하기 전에 제품을 사용 합니다. 이 문서에서 자세히 알아보세요 [시험 사용 이란?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)합니다. 을 더 이상 제품에 대 한 시험 사용을 제공 하려는 경우에 반환 합니다 **[설치 프로그램을 제공](#offer-setup)** 페이지 및 선택 취소 **테스트 드라이브 사용**합니다.

### <a name="technical-configuration"></a>기술 구성
다음 종류의 테스트 드라이브를 사용할 수는 각각 자체 기술 구성 요구 사항입니다.

- [Azure 리소스 관리자](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [논리 앱](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (없어도 기술 구성)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager에 대 한 기술 구성 시험 사용

솔루션을 구성 하는 모든 Azure 리소스를 포함 하는 배포 템플릿. 이 시나리오에 맞는 제품에는 Azure 리소스에만 사용 합니다. 설정에 대 한 자세한 정보는 [Azure Resource Manager 시험](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)합니다.

- **지역** (필수): 현재는 26 개 Azure 지원 지역 위치 시험 사용할 수 있습니다. 일반적으로 시험 사용할 수 있도록 가장 많은 고객의 경우 예상 되는 지역에서 최상의 성능을 위해 가장 가까운 지역을 선택할 수 있도록 해야 합니다. 구독의 모든 선택 하는 지역 각각에 필요한 리소스를 배포 하려면 허용 되어 있는지 확인 해야 합니다.

- **인스턴스**: (핫 또는 콜드) 유형을 선택 하 고 숫자의 사용 가능한 인스턴스를 제품을 사용할 수 있는 지역 수에 곱합니다.

**핫**: 이 유형의 인스턴스는 선택한 지역 마다 배포 되 고 대기 중 액세스가 있습니다. 고객에 게 즉시 액세스할 수 있습니다 *핫* 인스턴스의 시험 사용 하는 것에 배포 될 때까지 기다립니다. 단점은 이러한 인스턴스가 Azure 구독에서 항상 실행되고 있으므로 더 큰 가동 시간 비용이 발생한다는 것입니다. 하나 이상 있어야 하는 좋습니다 *핫* 대부분의 고객을 기다리지 않으려고 전체 배포의 경우 고객 사용량에서 차 없는 경우 발생 하는 대로 인스턴스 *핫* 인스턴스를 사용할 수 있습니다.

**콜드**: 이 유형의 인스턴스 지역당 가능한 배포할 수 있는 인스턴스의 총 수를 나타냅니다. 콜드 인스턴스에 필요한 고객 이므로 테스트 드라이브를 요청할 때 배포 전체 테스트 드라이브 Resource Manager 템플릿을 *콜드* 인스턴스가 로드 보다 훨씬 느립니다 *핫* 인스턴스. 시험 기간에 대 한 요금을 지불 하기만 하면을 것이 *되지* 항상 사용 하 여 Azure 구독에서 실행 되는 *핫* 인스턴스.

- **테스트 드라이브 Azure Resource Manager 템플릿을**: Azure Resource Manager 템플릿에 포함 된.zip을 업로드 합니다.  빠른 시작 문서에서는 Azure Resource Manager 템플릿을 만드는 방법을 자세히 알아보세요 [만들기 및 Azure portal을 사용 하 여 Azure Resource Manager 템플릿 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)합니다.

- **시험 사용 기간** (필수): 시험 시간 #에서 활성화 상태로 있는 시간의 길이 입력 합니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다. 이 기간 수만 bet 설정할 시간 정수 (예: "2" 시간 "1.5" 유효 하지 않습니다).

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 대 한 기술 구성 시험 사용

Microsoft 호스팅 및이 유형의 테스트 드라이브를 사용 하 여 배포 하 고 프로 비전 서비스를 유지 관리 하 여 시험 사용을 설정 하는 복잡성을 제거할 수 있습니다. 호스 티 드 시험이이 유형에 대 한 구성이 시험 Business Central, 고객, 또는 작업 대상 그룹을 대상으로 하는지 여부에 관계 없이 동일 합니다.

- **최대 동시 시험** (필수): 한 번에 시험 사용할 수 있는 고객의 최대 수를 설정 합니다. 각 동시 사용자 최대 한도 세트를 지원 하기 위해 사용할 수 있는 충분 한 라이선스가 있는지 확인 해야 하므로 시험 활성화 된 동안 Dynamics 365 라이선스를 사용 합니다. 권장 값은 3~5입니다.

- **시험 사용 기간** (필수): 시간을 정의 하 여 Test Drive 활성화 상태로 있는 시간의 길이 입력 합니다. 이 많은 시간 후에도 세션 종료 되며 더 이상 라이선스 중 하나를 사용 합니다. 제품의 복잡성에 따라 2 ~ 24 시간을 사용 하는 것이 좋습니다. 이 기간 수만 bet 설정할 시간 정수 (예: "2" 시간 "1.5" 유효 하지 않습니다).  사용자는 시간이 부족 하며 시험에 다시 액세스 하려고 하는 경우 새 세션을 요청할 수 있습니다.

- **인스턴스 URL** (필수): 고객은 고객이 시험에 주소를 시작할 URL입니다. 일반적으로 설치 하는 샘플 데이터를 사용 하 여 앱을 실행 하 여 Dynamics 365 인스턴스 URL (예: https://testdrive.crm.dynamics.com)합니다.

- **Web API URL 인스턴스** (필수): Microsoft 365 계정에 로그인 하 고 이동 하 여 Dynamics 365 인스턴스에 대 한 Web API URL 검색 **설정을** \&gt; **사용자 지정** \&gt; **개발자 리소스** \&gt; **인스턴스 웹 API (서비스 루트 URL)** 를 확인할 URL을 복사 (예: https://testdrive.crm.dynamics.com/api/data/v9.0)합니다.

- **역할 이름** (필수): 사용자 지정 Dynamics 365 시험에서 정의한 보안 역할 이름을 제공 합니다. 해당 테스트 드라이브 (예: 테스트-드라이브-역할) 하는 동안 사용자에 게 지정 됩니다.

#### <a name="technical-configuration-for-logic-app-test-drive"></a>논리 앱 시험 사용에 대 한 기술 구성

사용자 지정 제품에는 다양 한 복잡 한 솔루션 아키텍처를 포함 하는 테스트 드라이브 배포 템플릿의이 형식을 사용 해야 합니다. 논리 앱을 설정 하는 방법에 대 한 자세한 내용은 시험, 방문 [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) 및 [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) GitHub에서.

- **지역** (필요한 경우 단일 선택 드롭다운 목록): 현재는 26 개 Azure 지원 지역 위치 시험 사용할 수 있습니다. 논리 앱에 대 한 리소스를 선택 하면 지역에 배포 됩니다. 논리 앱에 특정 지역에 저장 된 모든 사용자 지정 리소스 하는 경우 해당 지역 여기 확인란이 선택 되어 있는지 확인 합니다. 이 작업을 수행 하는 가장 좋은 방법은 완벽 하 게 배포 하는 포털에서 Azure 구독에 로컬로 논리 앱이이 선택 하기 전에 올바르게 작동 하는지 확인 됩니다.

- **최대 동시 시험** (필수): 한 번에 시험 사용할 수 있는 고객의 최대 수를 설정 합니다. 이러한 테스트 드라이브 이미 배포 된, 고객이 배포 될 때까지 기다리지 않고 즉시 액세스할 수 있도록 합니다.

- **시험 사용 기간** (필수): 시험 시간 #에서 활성화 상태로 있는 시간의 길이 입력 합니다. 시험이 시간 기간이 끝난 후 자동으로 종료 합니다.

- **Azure 리소스 그룹 이름** (필수): 입력 된 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) 논리 앱 테스트 드라이브에 저장 된 이름입니다.

- **Azure 논리 앱 이름** (필수): 사용자에 게 테스트 드라이브를 할당 하는 논리 앱의 이름을 입력 합니다. 이 논리 앱은 위의 Azure 리소스 그룹에 저장 해야 합니다.

- **프로 비전 해제 논리 앱 이름** (필수): 고객이 완료 되 면 테스트 드라이브를 프로 비전 취소는 논리 앱의 이름을 입력 합니다. 이 논리 앱은 위의 Azure 리소스 그룹에 저장 해야 합니다.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 테스트 드라이브에 대 한 필요가 기술 구성

대화형 Power BI 시각적 개체는 포함 된 링크를 사용 하 여 고객이 시험에 필요한 기술 구성 더 이상으로 사용자 지정 대시보드를 공유 하려면 수를 보여 주기 위해 원하는 제품입니다. 설정에 대해 자세히 알아보려면[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) 템플릿 앱.

### <a name="deployment-subscription-details"></a>배포 구독 세부 정보

사용자를 대신해 테스트 드라이브를 배포 하려면 만든 후 별도, 고유한 Azure 구독을 제공 하십시오. (Power BI 테스트 드라이브에 대 한 필수 아님).

- **Azure 구독 ID** (Azure Resource Manager 및 논리 앱에 대 한 필수): 리소스 사용량 보고 및 청구에 대 한 Azure 계정 서비스에 대 한 액세스 권한을 부여 하려면 구독 ID를 입력 합니다. 고려 하는 것이 좋습니다 [별도 Azure 구독을 만드는](https://docs.microsoft.com/azure/billing/billing-create-subscription) 있습니다 아직 없는 경우 테스트 드라이브 사용 하도록 합니다. 에 로그인 하 여 Azure 구독 ID를 찾을 수 있습니다 합니다 [Azure portal](https://portal.azure.com/) 로 이동 하는 **구독** 왼쪽 메뉴의 탭 합니다. 탭을 선택 하면 구독 ID (예: "a83645ac-1234-5ab6-6789-1h234g764ghty")이 표시 됩니다.

- **Azure AD 테 넌 트 ID** (필수): Azure Active Directory (AD)를 입력 [테 넌 트 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id)합니다. 이 ID를 찾으려면에 로그인 하는 [Azure portal](https://portal.azure.com/), 왼쪽 메뉴에서 Active Directory 탭을 선택, 선택 **속성** 를 찾은 후에 대 한를 **디렉터리 ID** 번호 (예: 나열 된 50c464d3-4930-494c-963c-1e951d15360e)입니다. 도메인 이름 URL을 사용 하 여 조직의 테 넌 트 ID를 조회할 수도 있습니다: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com)합니다.

- **Azure AD 테 넌 트 이름** (동적 365에 대 한 필수): Azure Active Directory (AD) 이름을 입력 합니다. 이 이름을 찾으려면에 로그인 합니다 [Azure portal](https://portal.azure.com/), 오른쪽 위 모서리에서 테 넌 트 이름 계정 이름에 나열 됩니다.

- **Azure AD 앱 ID** (필수): Azure Active Directory (AD)를 입력 [응용 프로그램 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)합니다. 이 ID를 찾으려면에 로그인 하는 [Azure portal](https://portal.azure.com/)고 왼쪽 메뉴에서 Active Directory 탭을 선택, 선택 **앱 등록**, 찾아보십시오를 **응용 프로그램 ID** 수 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 나열 합니다.

- **Azure AD 앱 키** (필수): Azure Active Directory (AD)를 입력 [응용 프로그램 키](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)합니다. 이 ID를 찾으려면에 로그인 합니다 [Azure portal](https://portal.azure.com/)고 왼쪽 메뉴에서 Active Directory 탭을 선택, 선택 **앱 등록** 을 선택한 후 **설정**  >  **키**합니다.

해야 **저장할** 다음 섹션으로 이동 하기 전에!

### <a name="test-drive-listings-optional"></a>(선택 사항) 테스트 드라이브 목록

**시험 목록** 옵션 아래 합니다 **시험** 시험을를 사용할 수 있는 언어 (및 시장) 탭에 표시 됩니다 현재 영어 (미국)은 사용할 수 있는 유일한 위치 . 또한이 페이지는 언어별 목록 및 추가 된 날짜/시간의 상태를 표시 합니다. 각 언어/시장에 대 한 테스트 드라이브 정보 (예: 설명, 사용자 설명서, 비디오, 등)를 정의 해야 합니다.

- **설명** (필수): 새로운이 데모에서는, 시험, 설명 실험 사용자, 탐색, 기능 및 사용자를 제품을 가져올지 여부를 결정 하는 데 관련 정보에 대 한 목표입니다. 이 필드에는 최대 3,000 자 텍스트를 입력할 수 있습니다. 

- **정보에 액세스할** (Azure Resource Manager 및 논리 드라이브를 테스트에 대 한 필수): 액세스 하 고이 test drive를 사용 하기 위해 알고 해야 하는 고객에 설명 합니다. 제품 및 정확 하 게 고객 알아야 할 테스트 드라이브에서 기능에 액세스를 사용 하는 시나리오를 안내 합니다. 이 필드에는 최대 10,000 자의 텍스트를 입력할 수 있습니다.

- **사용자 설명서** (필수): 자세한 연습 테스트 드라이브 환경입니다. 사용자 설명서는 정확 하 게 원하는 시험 발생에서 얻을 수 있는 질문에 대 한 참조로 사용할를 고객을 다루어야 합니다. 파일을 PDF 형식으로 이어야 하며 (최대 255 자) 업로드을 따서 지어야 합니다.

- **비디오: 비디오 추가** (선택 사항): 비디오를 YouTube 또는 Vimeo에 업로드 하 고 고객 설명은 성공적으로의 기능을 사용 하는 방법을 포함 하 여 테스트 드라이브를 더 잘 이해 하는 데 유용한 정보를 볼 수 있도록 링크 및 썸네일 이미지 (533 x 324 픽셀)를 사용 하 여 여기 참조 수에 제공 하 고 혜택을 강조 하는 시나리오를 이해 합니다.
  - **이름** (필수)
  - **URL (YouTube 또는 Vimeo만)** (필수)
  - **미리 보기 (533 x 324px)** : 이미지 파일은 PNG 형식에서 이어야 합니다.

선택 **저장할** 이러한 필드를 완료 합니다.

## <a name="publish"></a>게시

#### <a name="submit-offer-to-preview"></a>제출 미리 보기를 제공 합니다.

제품의 모든 필요한 섹션을 완료 한 후 선택 **게시** 포털의 오른쪽 위 모퉁이에서. 위치를 변경 해야 합니다 **검토 및 게시** 페이지입니다. 

처음으로이 제품을 게시 인 경우에 다음을 수행할 수 있습니다.

- 제품의 각 섹션에 대 한 완료 상태를 참조 하세요.
    - *시작 되지 않은* – 섹션 수행 되지 않은 의미 하 고 완료 해야 합니다.
    - *불완전 한* – 섹션 가지게 수정 해야 하는 오류 또는 자세한 정보를 제공 해야 합니다. 섹션이로 다시 이동 하 고 업데이트 하세요.
    - *전체* – 방법 섹션을 완료 되, 필요한 모든 데이터를 제공한 및 오류가 없는 합니다. 제품의 모든 섹션은 제품을 제출할 수 있습니다 완료 상태에서 여야 합니다.
- 앱을 이해 하는 데 유용한 모든 보조 정보 외에도 앱 올바르게 테스트 하도록 인증 팀으로 테스트 지침을 제공 합니다.
- 선택 하 여 게시에 대 한 제품을 제출 **제출**합니다. 때 제품의 미리 보기 버전을 알 수 있도록 전자 메일 사용자가 검토 하 고 승인 하면 전송 됩니다. 파트너 센터를 반환 하 고 선택 해야 합니다 **go-live** public (또는 개인 제공, 개인 사용자에 맞게) 제품을 게시 하는 제품에 대 한 합니다.

## <a name="next-steps"></a>다음 단계

- [상용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
