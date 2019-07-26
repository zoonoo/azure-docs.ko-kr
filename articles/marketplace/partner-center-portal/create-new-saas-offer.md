---
title: 상업적 Marketplace에서 새 SaaS 제품 만들기
description: Microsoft 파트너 센터에서 상용 Marketplace 포털을 사용 하 여 Azure Marketplace, AppSource 또는 CSP (클라우드 솔루션 공급자) 프로그램을 통해 나열 하거나 판매 하기 위해 새 SaaS (Software as a Service) 제품을 만드는 방법입니다.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a4947349e64d5f9bf95a9213701dc62a0e018b8f
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501315"
---
# <a name="create-a-new-saas-offer"></a>새 SaaS 제품 만들기

SaaS (Software as a Service) 제품을 만들기 시작 하려면 먼저 [파트너 센터 계정을 만들고](./create-account.md) **개요** 탭을 선택 하 여 [상용 마켓플레이스 대시보드](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)를 열어야 합니다.

![파트너 센터의 상용 마켓플레이스 대시보드](./media/new-offer-overview.png)

\+ **새 제품** 을 선택 합니다. 단추를 클릭 한 다음, 서비스 메뉴 항목 **으로 소프트웨어** 를 선택 합니다. 

다른 제안 유형 중 하나를 선택 하는 경우 이전 [Cloud 파트너 포털](https://cloudpartner.azure.com/)리디렉션됩니다.  이번에는 파트너 센터의 상용 Marketplace 포털에서 SaaS 제품을 사용할 수 있습니다. 

![파트너 센터에서 제안 창 만들기](./media/new-offer-click.png)

**새 제품** 대화 상자가 표시 됩니다. 

![새 제품 대화 상자](./media/new-offer-popup.png)


## <a name="offer-id-and-alias"></a>제품 ID 및 별칭

- **제품 ID**: 계정의 각 제품에 대 한 고유 식별자입니다. 이 ID는 marketplace 제품 및 Azure Resource Manager 템플릿 (해당 하는 경우)에 대 한 URL 주소의 고객에 게 표시 됩니다. 제품 ID는 소문자, 영숫자 (하이픈 및 밑줄을 포함 하 고 공백 없음) 이어야 합니다. 이는 50 자로 제한 되며, *만들기*를 선택한 후에는 변경할 수 없습니다.  
예: 테스트-제품-1
<br>결과 URL은 다음과 같습니다.`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **제품 별칭**: 파트너 센터 포털에서 제품을 참조 하는 데 사용 되는 이름입니다. 이 이름은 marketplace에서 사용 되지 않으며 고객에 게 표시 되는 *제품 이름* 및 기타 값과 다릅니다. *만들기*를 선택한 후에는이 값을 변경할 수 없습니다.

<br>예제: 테스트 제안 1&#8482;

**만들기**를 선택합니다.  이 제품에 대 한 **제품 개요** 페이지가 생성 됩니다.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>제품 개요

**제품 개요** 페이지에는 다음이 포함 됩니다. 

- **게시 상태** 는이 제품을 게시 하는 데 필요한 단계를 시각적으로 표시 하 고 각 단계를 완료 하는 데 걸리는 시간을 표시 합니다. 불완전 한 게시 단계 아이콘은 회색으로 표시 됩니다. 

- **제안 개요** 메뉴에는이 제품에 대 한 작업을 수행 하기 위한 링크 목록이 포함 되어 있습니다. 이 작업 목록은 제품에 대해 선택한 내용에 따라 변경 됩니다.  
    - 제안이 초안이 면 초안을 삭제 합니다. 
    - 제품이 라이브 인 경우 – 제품 판매 중지 
    - 제품이 미리 보기 상태 이면 라이브로 전환 합니다. 
    - 게시자 로그 아웃을 완료 하지 않은 경우-게시 취소

## <a name="offer-setup"></a>제품 설정

**제품 설정** 탭에서 다음 정보를 요청 합니다. 이러한 필드를 완료 한 후 **저장** 을 선택 합니다.

- **Microsoft를 통해 판매 하 시겠습니까?** (예/아니요)
    - **예**, microsoft를 대신 하 여 microsoft 호스팅 marketplace 트랜잭션을 사용 하 여 제품을 판매 하 고 싶습니다. 디스크나 
    - **아니요**. 마켓플레이스을 통해 제품을 나열 하 고 Microsoft와는 독립적으로 통화 트랜잭션을 처리 하는 것이 좋습니다.    

### <a name="sell-through-microsoft"></a>Microsoft를 통해 판매

Microsoft를 통해 판매 되는 고객 검색 및 취득은 Microsoft에서 사용자를 대신 하 여 marketplace 트랜잭션을 호스트 하 고 Microsoft의 전 세계적으로 사용 가능한 상거래 기능을 활용할 수 있도록 하는 것입니다.

#### <a name="saas-offer-requirements"></a>SaaS 제품 요구 사항

파트너 센터에서 상업적 Marketplace를 사용 하 여 SaaS (Software as a Service) 제품을 나열 하려면 다음 조건을 충족 해야 합니다.

- 사용자 제품은 id 관리 및 인증을 위해 [Azure Active Directory (AZURE AD)](https://azure.microsoft.com/services/active-directory/) 를 사용 해야 합니다.
- 제품에서 SaaS 처리 [api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) 를 사용 하 여 Azure Marketplace와 통합 해야 합니다.
- 더 광범위 한 요구 사항은 [SaaS 제품 게시 가이드](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)를 참조 하세요.

#### <a name="saas-on-azure-billing-infrastructure-costs"></a>Azure 청구 인프라의 SaaS 비용
SaaS 제품이 Azure에서 호스트 되는 경우 게시자는 Azure 인프라 사용 요금 및 소프트웨어 라이선스 요금을 단일 비용 항목으로 고려 해야 합니다. 이 비용은 고객에 게 고정 된 월간 요금으로 표현 됩니다. Azure 인프라 사용은 사용자, 파트너, 직접 관리 및 청구 됩니다. 실제 인프라 사용 요금은 고객에게 표시되지 않습니다. 일반적으로 게시자는 Azure 인프라 사용 요금을 소프트웨어 라이선스 가격 책정에 추가하는 것을 선택합니다. 

소프트웨어 라이선스 요금은 월별 반복 되는 사이트 기반 구독 정액 요금으로 제공 되며 요금제 또는 소비 기반이 아닙니다.

|**라이선스 비용**|**$100/월**|
|:---|:---|
|Azure 사용량 비용(D1/1개 코어)|고객이 아닌 게시자에게 직접 청구됩니다.|
|Microsoft에서 청구 하는 고객|월 $100.00 (게시자는 라이선스 요금으로 발생 하거나 통과 한 인프라 비용을 고려해 야 함)|

- 이 시나리오에서 Microsoft는 소프트웨어 라이선스에 대해 $100.00를 청구하고, 게시자에게 $80.00를 지급합니다.
- **줄어든 Marketplace 서비스 요금** 으로 한정 된 파트너는 2019 년 5 월 2020 일까 야 년 5 월부터 SaaS 제품에 대 한 트랜잭션 요금이 감소 하는 것을 볼 수 있습니다. 이 시나리오에서 Microsoft는 귀하의 소프트웨어 라이선스에 대해 $100.00를 청구 하 고 게시자에 게 $90.00을 지불 합니다.

> [!NOTE]
> **Marketplace 서비스 요금 감소**: 상업적 Marketplace에 게시 한 특정 SaaS 제품의 경우 Microsoft는 Microsoft 게시자 계약에 설명 된 대로 20%에서 Marketplace 서비스 요금을 10%까지 줄일 수 있습니다. 제안을 받으려면 Microsoft에서 하나 이상의 제품을 IP 공동 판매 준비 또는 IP 공동 판매 우선 순위로 지정 해야 합니다.  해당 월에 대해이 감소 한 Marketplace 서비스 요금을 받으려면 각 월이 끝날 때까지 영업일 (5) 이상 이어야 합니다.  낮은 Marketplace 서비스 요금은 Vm, 관리 되는 앱 또는 상용 Marketplace를 통해 제공 되는 기타 제품에는 적용 되지 않습니다.  줄어든 Marketplace 서비스 요금은 2020 년 5 월 1 일부 터 2019 년 6 월 30 일 사이 Microsoft에서 수집한 라이선스 요금에 대 한 한정 된 제품에만 제공 됩니다.  이 시간 후에 Marketplace 서비스 요금은 일반적인 금액으로 반환 됩니다. 

|**Microsoft에 청구되는 요금**|**$100/월**|
|:---|:---|
|Microsoft는 라이선스 비용의 80%를 지불합니다. <br>**Microsoft는 정규화 된 SaaS 앱에 대해 90%의 라이선스 비용을 지불 합니다.*|매월 $80.00 <br>*$* 월 90.00 *|


#### <a name="csp-program-opt-in"></a>CSP 프로그램 옵트인
[CSP (클라우드 솔루션 공급자)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) 프로그램을 사용 하면 소프트웨어 제품이 최소의 마케팅 및 판매 투자로 수많은 Microsoft 고객에 게 도달할 수 있습니다.

- **채널이 CSP 프로그램** (확인란)에서 제품을 사용할 수 있도록 설정

CSP 프로그램에서 제품을 사용할 수 있도록 리더로 선택 클라우드 솔루션 공급자는 고객에 게 번들로 제공 되는 솔루션의 일부로 제품을 판매할 수 있습니다. 

### <a name="list-through-microsoft"></a>Microsoft의 목록

Marketplace 목록을 만들어 Microsoft로 비즈니스를 홍보 하세요. Microsoft가 아닌 제품을 나열 하도록 선택 하는 것은 Microsoft가 소프트웨어 라이선스 트랜잭션에 직접 참여 하지 않는다는 것을 의미 합니다. 연결 된 트랜잭션 요금은 없으며 게시자는 고객 으로부터 수집 된 소프트웨어 라이선스 수수료의 100%를 유지 합니다. 그러나 게시자는 주문 처리, 계량, 청구, 청구, 지불 및 수집을 비롯 하 여 소프트웨어 라이선스 트랜잭션의 모든 측면을 지원 해야 합니다. 

- **잠재 고객이이 목록 제공 서비스와 상호 작용 하 게 하려면 어떻게 해야 하나요?**

##### <a name="get-it-now-free"></a>지금 받기 (무료)
앱에 액세스할 수 있는 올바른 URL ( *http* 또는 *https*로 시작)을 제공 하 여 고객에 게 제품을 무료로 제공 합니다.  예: `https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>무료 평가판 (목록)
[AZURE AD (Azure Active Directory)를 사용 하 여 한 번의 클릭으로 인증](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)을 통해 평가판을 얻을 수 있는 올바른 URL ( *http* 또는 *https*로 시작)을 제공 하 여 고객에 게 제품을 나열 합니다.  예를 들어 `https://contoso.com/trial/saas-app`을 참조하십시오. 무료 평가판을 나열 하는 제품은 서비스에서 생성, 관리 및 구성 되며 Microsoft에서 관리 하는 구독을 포함 하지 않습니다.

> [!NOTE]
> 평가판 링크를 통해 응용 프로그램에서 수신 하는 토큰은 Azure AD를 통해 사용자 정보를 가져와서 앱에서 계정 만들기를 자동화 하는 데만 사용할 수 있습니다. 이 토큰을 사용 하는 인증에는 MSA (Microsoft 계정)가 지원 되지 않습니다.

##### <a name="contact-me"></a>나에게 연락
CRM (고객 관계 관리) 시스템을 연결 하 여 고객 연락처 정보를 수집 합니다. 고객에 게 정보를 공유할 수 있는 권한을 요청 하는 메시지가 표시 됩니다. 제품을 찾은 제품 이름, ID 및 marketplace 원본과 함께 이러한 고객 세부 정보는 구성 된 CRM 시스템으로 전송 됩니다. CRM을 구성 하는 방법에 대 한 자세한 내용은 [연결 리드 management](#connect-lead-management)를 참조 하세요. 

## <a name="example-marketplace-offer-listing"></a>Marketplace 제품 목록 예

![노트를 사용 하는 예제 marketplace 제품 목록](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>시험 사용을 사용하도록 설정

시험 사용한 고객에 게 제품을 고객에 게 제공 하는 좋은 방법은 ' 구매 전 시도 '를 선택 하 여 변환 및 높은 우량 잠재 고객을 생성 하는 옵션을 제공 하는 것입니다. [테스트 드라이브에 대해 자세히 알아보세요.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **테스트 드라이브 사용** 상자 

테스트 드라이브를 사용 하도록 설정 하면 고객이 고정 기간 동안 제품을 사용해 볼 수 있도록 시연 환경을 구성 하 라는 메시지가 표시 됩니다. 

### <a name="type-of-test-drive"></a>테스트 드라이브의 유형입니다.

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : 솔루션을 구성 하는 모든 Azure 리소스를 포함 하는 배포 템플릿입니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용 합니다.
- **[Dynamics 365 For Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft는 비즈니스 중심 엔터프라이즈 리소스 계획 시스템 (재무, 운영, 공급망, CRM 등)에 대해 테스트 드라이브 서비스 (프로 비전 및 배포 포함)를 호스팅하고 유지 관리 합니다.  
- **[고객 Engagement의 Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft는 고객 참여 시스템 (판매, 서비스, 프로젝트 서비스, 현장 서비스 등)에 대해 테스트 드라이브 서비스 (프로 비전 및 배포 포함)를 호스팅하고 유지 관리 합니다.  
- **[운영을 위한 Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft는 재무 및 운영 enterprise 리소스 계획 시스템 (재무, 운영, 제조, 공급망 등)을 위해 테스트 드라이브 서비스 (프로 비전 및 배포 포함)를 호스팅하고 유지 관리 합니다. 
- **[논리 앱](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : 모든 복잡 한 솔루션 아키텍처를 포괄 하는 배포 템플릿입니다. 모든 사용자 지정 제품은이 유형의 테스트 드라이브를 사용 해야 합니다.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : 사용자가 작성 한 대시보드에 대 한 포함 된 링크입니다. 대화형 Power BI 시각적 개체를 시연 하려는 제품은이 유형의 테스트 드라이브를 사용 해야 합니다. 포함된 Power BI URL만 업로드하면 됩니다.

#### <a name="additional-test-drive-resources"></a>추가 테스트 드라이브 리소스
- [테스트 드라이브 기술 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [테스트 드라이브 마케팅 모범 사례](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [시험 드라이브 개요 1 호출기](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>리드 관리 연결

마켓플레이스에 제품을 나열 하 고 CRM (고객 관계 관리) 시스템을 연결 하 여 고객에 게 직접 연결 하 여 고객에 게 관심이 나 배포한 후 즉시 고객 연락처 정보를 받을 수 있도록 합니다. 제품은.

- **리드 대상 선택** (드롭다운 메뉴): 고객 리드를 보내려는 CRM 시스템에 대 한 연결 세부 정보를 제공 합니다. 

파트너 센터는 리드 관리를 위해 다음과 같은 CRM 시스템을 지원 합니다. 설치 지침에 대 한 링크를 선택 합니다.

- Azure Blob – 연락처 전자 메일, 컨테이너 이름 및 저장소 계정 연결 문자열을 제공 합니다. 
- [Azure 테이블](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) – 연락처 전자 메일 및 저장소 계정 연결 문자열을 제공 합니다. 
- [DYNAMICS CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – 연락처 전자 메일, URL 및 인증 모드 (Office 365 또는 Azure Active Directory)를 제공 합니다.
- [Https 끝점](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) – 연락처 전자 메일 및 HTTPS 끝점 URL을 제공 합니다. 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – 연락처 전자 메일, 양식 Id, MUNCHKIN 계정 id 및 서버 id를 제공 합니다.
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) -연락처 전자 메일 및 조직 ID를 제공 합니다. 

#### <a name="additional-lead-management-resources"></a>추가 리드 관리 리소스
- [리드 관리 Faq](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [일반적인 리드 구성 오류](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [리드 관리 개요 1 호출기](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.

## <a name="properties"></a>속성
**속성** 탭에서는 마켓플레이스에서 제품을 그룹화 하는 데 사용 되는 범주와 산업, 제품을 지 원하는 법적 계약 및 앱 버전을 정의 하 라는 메시지가 표시 됩니다. 

이러한 필드를 완료 한 후 **저장** 을 선택 합니다. 

### <a name="category"></a>범주
제품을 적절 한 마켓플레이스 검색 영역으로 그룹화 하는 데 사용 되는 최소 1 개 (1) 및 최대 3 개 범주를 선택 합니다. 제품 설명에서 제품이 이러한 범주를 어떻게 지원 하는지 확인 하세요. 

### <a name="industry"></a>업종
제품을 적절 한 마켓플레이스 검색 영역으로 그룹화 하는 데 사용 되는 최대 2 개의 산업을 선택 합니다. 제품이 특정 업계에 국한 되지 않은 경우 하나를 선택 하지 마세요. 제품 설명에서 선택 된 산업을 지 원하는 방법을 확인 하세요. 

### <a name="app-version"></a>앱 버전
제품의 버전 번호를 식별 하기 위해 AppSource marketplace에서 사용 되는 선택적 필드입니다. 

### <a name="standard-contract"></a>표준 계약

- **표준 계약을 사용 하나요?**

고객을 위한 조달 프로세스를 간소화 하 고 소프트웨어 공급 업체에 대 한 법적 복잡성을 줄이기 위해 Microsoft에서는 marketplace에서 트랜잭션을 용이 하 게 하기 위해 표준 계약 템플릿을 제공 합니다. 

Azure Marketplace 게시자는 사용자 지정 약관을 만드는 대신 표준 계약에 따라 소프트웨어를 제공 하도록 선택할 수 있으며, 고객은 한 번만 심사 하 고 수락 하면 됩니다. 

표준 계약은에서 찾을 https://go.microsoft.com/fwlink/?linkid=2041178 수 있습니다.

#### <a name="terms-of-use"></a>서비스 계약

사용 조건이 표준 계약과 다른 경우 귀하의 법적 사용 약관을 여기에 입력 하도록 선택할 수 있습니다. 이 필드에는 최대 1만 자의 텍스트를 입력할 수도 있습니다. 사용 약관에 더 긴 설명이 필요한 경우 추가 사용 조건을 찾을 수 있는이 필드에 단일 URL 링크를 입력 합니다. 고객이 활성 링크로 표시 됩니다.

고객은 이러한 사용 약관에 동의해야 앱을 사용할 수 있습니다. 

다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.

## <a name="offer-listing"></a>제품 목록

제품 목록 탭에는 제품을 사용할 수 있는 언어 (및 시장)가 표시 되 고 현재 영어 (미국)만 사용할 수 있는 위치입니다. 또한이 페이지에는 언어별 목록과 추가 된 날짜/시간의 상태가 표시 됩니다. 각 언어/시장에 대해 마켓플레이스 세부 정보 (제품 이름, 설명, 검색어 등)를 정의 해야 합니다.

> [!NOTE]
> 제품 설명, 문서, 스크린샷, 사용 약관 및 개인 정보 취급 방침 등 제품 목록 콘텐츠는 영어로 시작 하지 않아도 됩니다. "이 응용 프로그램은 [영어가 아닌 언어] 에서만 사용할 수 있습니다. 또한 제품 목록 콘텐츠에 사용 되는 것 이외의 언어로 콘텐츠를 제공 하는 *데 유용한 링크 URL* 을 제공 하는 것도 허용 됩니다.

### <a name="offer-listings"></a>제품 목록

제품 및 마케팅 자산에 대 한 설명을 포함 하 여 marketplace에 표시 되는 세부 정보를 제공 합니다.

- **이름** (필수): 여기서 정의 된 이름은 사용자가 선택한 marketplace의 제품 목록으로 표시 됩니다. 이전 **새 제안** 항목에 따라 이름이 미리 채워집니다.  이는 상표 수 있습니다.  이는 영숫자와 저작권 기호를 제외 하 고는 공백,이 모 지를 포함 해서는 안 되며 50 자로 제한 되어야 합니다.
- **요약** (필수): Marketplace 목록 검색 결과에서 사용할 제품에 대 한 간단한 설명을 제공 합니다. 이 필드에는 최대 100 자의 텍스트를 입력할 수 있습니다.
- **설명** (필수): Marketplace 목록에 표시 되는 제품에 대 한 설명을 제공 합니다. 가치 제안, 주요 이점, 범주 또는 업계 연결, 앱 내 구매 기회, 필요한 공개 및 자세한 내용을 볼 수 있는 링크를 포함 하는 것이 좋습니다.
이 필드에는 최대 3000 자의 텍스트를 입력할 수 있습니다. 추가 팁은 [유용한 앱 설명 작성](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)문서를 참조 하세요.
- **검색 키워드**: 고객이 marketplace에서 제품을 찾는 데 사용할 수 있는 검색 키워드를 최대 3 개까지 입력 합니다.
- **시작 지침** (필수): 잠재적 고객에 대해 앱 사용을 구성 하 고 시작 하는 방법을 설명 합니다.  이 빠른 시작에는 보다 자세한 온라인 설명서에 대 한 링크가 포함 될 수 있습니다. 이 필드에는 최대 3000 자의 텍스트를 입력할 수 있습니다. 

#### <a name="description"></a>**설명**

이 이름은 반드시 입력해야 합니다. 설명에 포함할 항목: 

* 설명의 처음 몇 문장에서 제품의 가치 제안을 명확하게 설명합니다.  
* 처음 몇 문장은 검색 엔진 결과에 표시될 수 있다는 점에 유의하세요.  
* 제품을 판매하는 특징과 기능에 의존하지 않습니다. 대신, 전달하는 가치에 중점을 둡니다.  
* 산업별 어휘 또는 혜택 기반 단어를 최대한 많이 사용합니다. 

가치 제안의 핵심 구성 요소에 포함되어야 하는 정보는 다음과 같습니다. 

* 제품에 대한 설명입니다. 
* 제품의 혜택을 받는 사용자 유형 
* 고객이 요구 하거나 제품의 문제를 해결 합니다. 

제품 설명을 더 많이 활용 하려면 HTML 태그를 사용 하 여 설명의 서식을 지정할 수 있습니다. 

1. 단락을 만들려면 텍스트의 begging를 추가 `<p>` 하 고 끝에를 추가 `</p>` 합니다.

    **예제**: 

    `<p>`첫 번째 단락입니다. `</p>` <br>
    `<p>`이는 두 번째 단락입니다. `</p>` <br>

    위는 다음과 같습니다.

    <p> 첫 번째 단락입니다. </p>
    <p> 이는 두 번째 단락입니다. </p>

1. **항목의 글머리 기호 목록을**추가 하려면 아래 `<li>` 태그 안에 텍스트를 입력 합니다. `<li>` `</li>` 및 태그`</ul>` 내에서 더 많은 글머리 기호 항목 (및 태그 사이에 있는 항목)을 복사 하 여 붙여 넣을 수 있습니다. `<ul>` 을 `<ul></ul>`추가 했는지 확인 합니다. 

    **예제**:

    ```
    <ul> 
        <li>add text here</li> 
        <li> add text here </li> 
        <li> add text here </li> 
    </ul> 
    ```

    위는 다음과 같습니다.
    <ul> 
        <li>여기에 텍스트 추가</li> 
        <li> 여기에 텍스트 추가 </li> 
        <li> 여기에 텍스트 추가 </li> 
    </ul> 

1. 내용을 **굵게** 표시 하려면 굵게 `<b>` 표시할 텍스트의 시작 부분에를 추가 하 고 굵게 표시할 `</b>` 텍스트의 끝에를 추가 합니다. 

    **예제**: `<b>`무료 평가판`</b>`
    
    위의 방법으로 인해 storefront의 제품 설명에서 무료 평가판 이라는 단어가 굵게 표시 됩니다. 

    **무료 평가판**

1. 콘텐츠 사이에 **줄 바꿈을** 추가 하려면 새 줄 `<br>` 에서 시작 하려는 콘텐츠 앞에를 추가 합니다. 공백을 두고 콘텐츠가 새 줄에서 시작 되도록 하려면 콘텐츠 앞에를 추가 `<br><br>` 합니다. 

    **예제**:

    텍스트 줄입니다. `<br>`새 줄에서 시작 하는 텍스트 줄입니다. `<br><br>`아래 두 줄을 시작 하는 줄입니다. 

    위는 다음과 같습니다.

    텍스트 줄입니다. <br> 새 줄에서 시작 하는 텍스트 줄입니다. <br><br> 아래 두 줄을 시작 하는 줄입니다. 

1. **텍스트의 크기를 늘리려면**먼저 텍스트를 표시할 크기를 선택 합니다. 아래 예제를 사용 합니다. 텍스트의 크기를 선택한 후에는 텍스트의 시작과 끝에 `<H*></H*>` 해당 태그를 추가 합니다. 

    **예제**:

    `<h1>`제목 1입니다.`</h1>` <br>
    `<h2>`제목 2입니다.`</h2>` <br>
    `<h3>`제목 3입니다.`</h3>` <br>
    `<h4>`제목 4입니다.`</h4>` <br>
    `<h5>`제목 5입니다.`</h5>` <br>
    `<h6>`제목 6입니다.`</h6>` 

    위는 다음과 같습니다.

    <h1>제목 1입니다.</h1> 
    <h2>제목 2입니다.</h2> 
    <h3>제목 3입니다.</h3> 
    <h4>제목 4입니다.</h4> 
    <h5>제목 5입니다.</h5> 
    <h6>제목 6입니다.</h6> 

#### <a name="links"></a>링크

- **개인 정보 취급 방침** (필수): 조직의 개인 정보 취급 방침에 대 한 링크입니다. 앱이 개인 정보 법률 및 규정을 준수 하는지 확인 하 고 유효한 개인 정보 취급 방침을 제공 해야 합니다.
- **CSP 프로그램 마케팅 자료** (선택 사항): 제품을 [CSP (클라우드 솔루션 공급자)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) 프로그램으로 확장 하도록 선택 하는 경우 마케팅 자료에 대 한 링크를 제공 해야 합니다. CSP는 CSP 파트너가 제품을 번들, 시장 및 재판매 수 있도록 하 여 제품을 더 광범위 한 범위의 고객으로 확장 합니다. 이러한 대리점은 제품 마케팅에 대 한 자료에 액세스 해야 합니다. 자세한 내용은 [출시 전 서비스](https://partner.microsoft.com/reach-customers/gtm)를 참조 하세요.
- **유용한 링크** (선택 사항): **제목** 및 **URL**을 제공 하 여 나열 된 앱 또는 관련 서비스에 대 한 선택적 보조 온라인 문서입니다. **+ URL 추가**를 클릭 하 여 유용한 링크를 더 추가 합니다.

#### <a name="contact-information"></a>연락처 정보

- **연락처**: 각 고객 연락처에 대해 직원 **이름** , **전화 번호**및 **전자 메일** 주소를 제공 합니다.  이는 공개적으로 표시 *되지* 않습니다. 지원 **URL** 은 **지원 담당자** 그룹에도 필요 합니다.  이 정보 *는* 공개적으로 표시 됩니다.

**지원 연락처** (필수): 일반 지원에 대 한 질문입니다.

**엔지니어링 연락처** (필수): 기술 관련 질문.

**채널 관리자 연락처** (필수): CSP 프로그램과 관련 된 재판매인 질문의 경우

#### <a name="files-and-images"></a>파일 및 이미지

- **문서** (필수): 제품에 대 한 관련 마케팅 문서를 PDF 형식으로 추가 하 여 제품에 대 한 최소 1 개 (1 개)와 최대 3 개의 문서를 제공 합니다.
- **이미지** (선택 사항): 제품 로고 이미지가 marketplace 전체에 나타날 수 있으며, 다음과 같은 크기를 요구 하는 여러 위치가 있습니다. 48 x 48 픽셀 _(필수),_ 보통: 90 x 90 픽셀, 큼: 216 x 216 픽셀 _(필수),_ 전체: 255 x 115 픽셀 및 주인공: 815 x 290 픽셀 모든 이미지는에 있어야 합니다. PNG 형식입니다.
- **스크린샷** (필수): 제품을 보여 주는 스크린샷 추가 최대 5 개 (5)의 스크린샷에 추가 될 수 있으며 1280 x 720 픽셀에 크기를 지정 해야 합니다. 모든 이미지는에 있어야 합니다. PNG 형식입니다.
- **비디오** (선택 사항): 제품을 보여 주는 비디오에 대 한 링크를 추가 합니다. 고객에게 제품과 함께 표시되는 YouTube 및/또는 Vimeo 동영상의 링크를 사용할 수 있습니다. 또한 비디오의 미리 보기 이미지를 입력 해야 합니다 .이 이미지는 PNG 형식의 1280 x 720 픽셀로 크기가 지정 됩니다. 제품 당 최대 4 개의 비디오를 표시할 수 있습니다.

다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.

#### <a name="additional-marketplace-listing-resources"></a>리소스를 나열 하는 추가 마켓플레이스

- [Marketplace 제품 목록에 대 한 모범 사례](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Preview

**미리 보기** 탭에서는 제품을 광범위 한 marketplace 대상에 게시 하기 전에 제품을 릴리스할 수 있는 제한 된 **미리 보기 대상을** 정의할 수 있습니다.

> [!IMPORTANT]
> 미리 보기에서 제품을 확인 한 후 제품이 marketplace 공용 대상 그룹에 live 게시 되기 전에 **라이브 전환** 을 선택 해야 합니다.

- **미리 보기 대상 정의: 선택적 설명과 함께 단일 AAD/MSA 계정 전자 메일을 줄 단위로 추가 합니다.**

Live 게시 전에 제품의 유효성을 검사 하는 데 도움이 되도록 기존 Microsoft 계정 (MSA) 또는 Azure Active Directory 계정에 대해 수동으로 최대 10 개의 전자 메일 주소를 추가 하거나 20 (20)을 추가 합니다. 이러한 계정을 추가 하 여 marketplace에 게시 하기 전에 제품에 대 한 미리 보기 액세스를 허용 하는 대상 그룹을 정의 합니다. 제품이 이미 라이브 상태인 경우 제품에 대 한 변경 내용 또는 업데이트를 테스트 하기 위한 미리 보기 대상을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 사용자는 개인 대상과 다릅니다. 미리 보기 대상 그룹은 마켓플레이스에 라이브 게시 _되기 전에_ 제품에 대 한 액세스를 허용 합니다. 계획을 만들어 개인 사용자만 사용할 수 있도록 설정할 수도 있습니다. **계획 목록** 탭에서 **비공개 요금제** 인 개인 사용자를 정의할 수 있습니다. 그런 다음 Azure 테 넌 트 Id를 사용 하 여 최대 2만 고객의 개인 사용자를 정의할 수 있습니다.

## <a name="technical-configuration"></a>기술 구성

**기술 구성** 탭에서는 제품에 연결 하는 데 사용 되는 기술 세부 정보 (URL 경로, webhook, 테 넌 트 id 및 앱 id)를 정의 합니다. 이 연결을 통해 최종 고객을 위해 제품을 프로 비전 할 수 있습니다. 수집 된 필드의 사용을 설명 하는 다이어그램은 SaaS 처리 [api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)에 대 한 설명서에서 확인할 수 있습니다.

- **방문 페이지 URL** (필수): Marketplace에서 제품을 확보 한 후 고객이 게 될 사이트 URL을 정의 합니다. 이 URL은 고객이 페이지로 라우팅되는 경우 토큰을 수신 하는 끝점입니다. 이 토큰은 처리 Api에서 해결을 사용 하 여 프로 비전 세부 정보를 교환할 수 있습니다. 이러한 세부 정보 및 수집 하는 다른 항목은 등록을 완료 하 고 구매를 활성화 하는 사용자 환경에서 빌드된 고객 대화형 웹 페이지의 일부로 사용할 수 있습니다.

- **연결 webhook** (필수): Microsoft에서 고객을 대신해서 보내야 하는 모든 비동기 이벤트(예: SaaS 구독이 잘못 되었습니다.) 연결 webhook를 제공 해야 합니다. 웹 후크 시스템이 아직 없는 경우 가장 간단한 구성은 게시 되는 모든 이벤트를 수신 대기 하 고 적절 하 게 처리 하는 HTTP 끝점 논리 앱을 보유 하는 것입니다 (예: https:\//prod-1westus.logic.azure.com:443/work). 자세한 내용은 [Logic Apps의 HTTP 엔드포인트를 통해 워크플로 호출, 트리거 또는 중첩](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)을 참조하세요.

- **AZURE AD 테 넌 트 ID** (필수): Azure Portal 내에서는 두 서비스 간의 연결에 대 한 유효성을 검사할 수 있도록 [AD (Azure Active Directory) 앱을 만들어야](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 합니다. [테 넌 트 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 찾으려면 Azure Active Directory로 이동 하 여 **속성**을 선택한 다음 나열 된 **디렉터리 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 확인 합니다.

- **AZURE AD 앱 ID** (필수): [응용 프로그램 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) 와 인증 키도 필요 합니다. 이러한 값을 얻으려면 Azure Active Directory로 이동 하 여 **앱 등록**을 선택한 다음 나열 된 **응용 프로그램 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 확인 합니다. 인증 키를 찾으려면 **설정** 으로 이동 하 고 **키**를 선택 합니다. 설명 및 기간을 제공 하 고 숫자 값이 제공 됩니다.

 Azure 응용 프로그램 ID가 게시자 ID와 연결 되어 있으므로 모든 제품에서 동일한 응용 프로그램 ID를 사용 해야 합니다.

## <a name="plan-overview"></a>계획 개요

**계획 개요** 탭에서는 동일한 제품 내에서 다양 한 계획 옵션을 제공할 수 있습니다. 이러한 계획 (Sku 라고도 함)은 버전, 수익 화 또는 서비스 계층의 측면에서 다를 수 있습니다. Marketplace에서 제품을 판매 하려면 계획을 하나 이상 설정 해야 합니다.

일단 만들어지면 계획 이름, Id, 가격 책정 모델, 가용성 (공용 또는 개인), 현재 게시 상태 및 사용 가능한 모든 동작이 표시 됩니다.

**계획 개요** 에서 사용할 수 있는 **작업** 은 계획의 현재 상태에 따라 달라 지 며 다음을 포함할 수 있습니다.

- 계획 상태가 **초안** -초안 삭제 인 경우
- 계획 상태가 **라이브** 인 경우 – 계획 판매 중지 또는 개인 대상 사용자 동기화

**새 계획 만들기** (Microsoft에서 판매 하도록 선택 하는 사용자에 대 한 계획 중 하나)

- **계획 ID:** 이 제안의 각 계획에 대 한 고유한 계획 ID를 만듭니다. 이 ID는 제품 URL 및 Azure Resource Manager 템플릿 (해당 하는 경우)의 고객에 게 표시 됩니다. 소문자, 영숫자, 대시 또는 밑줄만 사용 합니다. 이 계획 ID에는 최대 50 자까지 사용할 수 있습니다. 만들기를 선택한 후에는 ID를 수정할 수 없습니다.
- **계획 이름:** 제품 내에서 선택할 계획을 결정할 때 고객에 게이 이름이 표시 됩니다. 이 제안의 각 계획에 대 한 고유한 제안 이름을 만듭니다. 계획 이름은 동일한 제품에 포함 될 수 있는 소프트웨어 요금제를 구분 하는 데 사용 됩니다 (예: 제품 이름: Windows Server 서 Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>계획 목록

**계획 목록** 탭에는 요금제를 사용할 수 있는 언어 (및 시장)가 표시 됩니다. 현재 영어 (미국)는 사용할 수 있는 유일한 위치입니다. 또한이 페이지에는 언어별 목록과 추가 된 날짜/시간의 상태가 표시 됩니다. 각 언어/시장에 대해 마켓플레이스 세부 정보 (제품 이름, 설명, 검색어 등)를 정의 해야 합니다.

#### <a name="plan-listing-details"></a>계획 목록 세부 정보

계획 언어 중 하나를 선택 하면 **이름** 및 설명 등의 **계획 목록** 정보가 표시 됩니다 **.**

- **이름**: 미리 보기 **새 요금제** 항목을 기준으로 미리 채워져 있으며 marketplace에 표시 되는 제품의 "소프트웨어 계획" 제목으로 표시 됩니다.
- **설명:** 이 설명에서는이 소프트웨어 계획의 고유한 정의와 제품 내 다른 소프트웨어 요금제의 차이점을 설명할 수 있는 기회를 제공 합니다. 최대 500 자까지 포함할 수 있습니다.

이러한 필드를 완료 한 후 **저장** 을 선택 합니다.

#### <a name="plan-pricing-and-availability"></a>가격 책정 및 가용성 계획

**가격 책정 및 가용성** 탭에서는이 요금제를 사용할 수 있는 시장, 원하는 수익 화 모델, 가격 및 청구 기간을 구성할 수 있습니다. 또한 계획이 모든 사용자에 게 표시 되도록 할지 아니면 특정 고객 에게만 표시 되도록 할지를 지정할 수 있습니다 (개인 사용자).

##### <a name="enabling-free-trials"></a>무료 평가판 사용

상업적 marketplace를 통한 SaaS 제품을 사용 하면 Microsoft를 통해 판매 하는 경우 1 개월 무료 평가판을 제공할 수 있습니다. 요금제 요금제를 제외한 모든 청구 모델 및 용어의 경우 무료 평가판이 지원 됩니다. 이 옵션을 사용 하면 고객은 한 달의 무료 액세스를 통해 낮은 장벽을 입력할 수 있습니다.  제품 내에서 요금제에 대 한 무료 평가판을 사용 하도록 선택 하는 경우 고객은 초기 1 개월 기간이 끝나기 전에 유료 구독으로 변환할 수 없습니다.  이 기간 동안 제품을 구매 하는 고객은 무료 평가판을 사용할 수 있는 지원 되는 요금제를 사용해 보고 둘 사이를 변환할 수 있습니다.  유료 구독으로의 변환은 해당 기간이 끝날 때 자동으로 수행 됩니다.

>[!Note]
>고객이 무료 평가판을 사용 하지 않고 요금제로 전환 하도록 선택 하는 경우에는 변환이 수행 되지만 무료 평가판은 즉시 손실 됩니다.  또한 고객이 요금제에 대 한 요금을 지불 하 고 나면 무료 평가판을 지 원하는 SKU로 변환 되는 경우에도 더 이상 동일한 구독에서 더 이상 무료 평가판을 얻을 수 없습니다.

제품의 각 요금제에 대해 무료 평가판을 구성 하는 기능을 사용할 수 있습니다. 각 제품에 대 한 가격 책정 및 가용성으로 이동 하 여 1 개월 평가판을 허용 하는 확인란을 선택 하면 됩니다.

![1 개월 무료 평가판 확인란](./media/free-trial-enable.png)

현재 무료 평가판에 참여 하는 고객 구독에 대 한 정보를 얻으려면 true 또는 false `isFreeTrial`로 표시 되는 새 API 속성을 사용 합니다. 자세한 내용은 [SaaS Get SUBSCRIPTION API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription) 를 참조 하세요.

>[!Note]
>Marketplace 계량 서비스를 활용 하는 요금제에 대 한 무료 평가판은 지원 되지 않습니다.

#### <a name="markets"></a>시장

- **시장 편집** 필드

모든 요금제는 하나 이상의 시장에서 사용할 수 있어야 합니다. 이 요금제를 사용할 수 있도록 하려는 시장 위치에 대 한 확인란을 선택 합니다. "세금 송금" 국가를 선택 하는 데 사용 되는 검색 상자와 단추를 사용 하 여 Microsoft 송금 판매 및 세금을 대신 사용 합니다. 


요금제에 대 한 가격을 미국 달러 (USD)로 설정 하 고 다른 시장 위치를 추가 하는 경우 새 시장의 가격은 현재 환율이 따라 계산 됩니다. 게시 하기 전에 항상 각 시장의 가격을 검토 해야 합니다. 변경 내용을 저장 한 후 "가격 (.xlsx) 내보내기" 링크를 사용 하 여 가격 책정을 검토할 수 있습니다.

#### <a name="pricing"></a>가격 책정

- **가격 책정 모델**: 균등형 (Flat rate or) 기반

**기본 요율:** 월별 또는 연간 가격의 단일 가격 가격으로 제품에 대 한 액세스를 사용 하도록 설정 합니다. 이를 사이트 기반 가격 책정이 라고도 합니다. 이 가격 책정 모델을 사용 하는 경우 필요에 따라 표준 단위에 따라 고객에 게 요금을 청구 하기 위해 marketplace 계량 서비스 API를 사용 하는 요금제 요금제를 정의할 수 있습니다.  요금제 요금 청구에 대 한 자세한 내용은 [marketplace 계량 서비스를 사용 하 여 요금제 요금제](./saas-metered-billing.md)를 참조 하세요.

**사용자 기반:** 제품에 액세스 하는 사용자 수 또는 사용자 수를 기준으로 가격으로 제품에 대 한 액세스를 사용 하도록 설정 *합니다.* 이 사용자 기반 모델을 사용 하면 가격에 따라 허용 되는 최소 및 최대 사용자 수를 설정할 수 있습니다. 이러한 방식으로 여러 계획을 구성 하 여 사용자 수를 기준으로 다양 한 가격을 구성할 수 있습니다.  이러한 필드는 선택 사항입니다. 비워 두면 사용자 수는 제한이 없는 것으로 해석 됩니다 (최소 1, 시스템에서 지원할 수 있는 최대 수). 이러한 필드는 계획에 대 한 업데이트의 일부로 편집할 수 있습니다.

게시 된 후에는 청구 가격 책정 모델을 변경할 수 없습니다. 또한 동일한 제품에 대 한 모든 요금제는 동일한 가격 책정 모델을 공유 해야 합니다.

- **청구 기간**: 월별 또는 연간

고객이 나열 된 가격을 지불 해야 하는 빈도를 선택 합니다. 하나 이상의 월별 또는 연간 가격을 제공 해야 하며, 그렇지 않으면 두 옵션을 모두 고객이 사용할 수 있습니다.

- **가격**: 월 당 USD 또는 연간 USD

현지 통화 (USD = 미국 달러)로 설정 된 가격은 설치 중 사용 가능한 현재 환율을 사용 하 여 선택한 모든 시장의 현지 통화로 변환 됩니다. 가격 책정 스프레드시트를 내보내고 각 시장에서 가격을 검토 하 여 게시 하기 전에 이러한 가격의 유효성을 검사 합니다. 개별 시장에서 사용자 지정 가격을 설정 하려면 가격 책정 스프레드시트를 수정 하 고 가져오세요. 이 가격을 확인 하 고 이러한 설정을 소유할 책임이 있습니다.
**가격 책정 데이터를 내보낼 수 있도록 가격 책정 변경 내용을 먼저 저장 해야 합니다.*

계획이 게시 된 후 변경 될 수 있는 사항에 대 한 몇 가지 제한 사항이 있으므로 게시 하기 전에 가격을 신중 하 게 검토 합니다.

- 계획이 게시 된 후에는 가격 책정 모델을 변경할 수 없습니다.
- 요금제에 대 한 청구 기간이 게시 된 후에는 나중에 제거할 수 없습니다.
- 요금제의 시장 가격은 게시 된 후에는 나중에 변경할 수 없습니다.

### <a name="plan-audience"></a>대상 계획

모든 사용자 또는 선택한 특정 대상에 게 표시 되도록 각 계획을 구성 하는 옵션이 있습니다. Azure AD 테 넌 트 Id를 사용 하 여이 제한 된 대상 그룹의 멤버 자격을 할당할 수 있습니다.

#### <a name="privacy"></a>개인 정보 취급 방침

- **비공개 계획입니다** . (옵션 확인란)

계획을 비공개로 설정 하 고 선택한 제한 된 대상 그룹에만 표시 하려면이 확인란을 선택 합니다. 비공개 계획으로 게시 한 후에는 대상 그룹을 업데이트 하거나 계획을 모든 사용자가 사용할 수 있도록 선택할 수 있습니다. 모든 사용자에 게 표시 되는 계획을 게시 한 후에는 모든 사용자에 게 계속 표시 되어야 합니다. (요금제를 개인 계획으로 다시 구성할 수 없습니다.)

- **제한 된 대상 그룹 (테 넌 트 Id)**

이 비공개 요금제에 대 한 액세스 권한이 있는 대상 그룹을 할당 합니다. 지정 된 각 테 넌 트 ID에 대 한 설명을 포함 하는 옵션과 함께 테 넌 트 Id를 사용 하 여 액세스 권한을 할당 합니다. .Csv 스프레드시트 파일을 가져오는 경우 최대 10 개의 테 넌 트 Id를 추가 하거나 2만 고객 테 넌 트 Id를 추가할 수 있습니다.

테 넌 트는 ID가 GUID (GUID (Globally Unique Identifier)로 표시 되 고, 리소스를 식별 하는 데 사용 되는 128 비트 정수) 인 조직의 표현입니다. 조직 또는 앱 개발자가 Microsoft와의 관계를 만들 때(예: Azure, Microsoft Intune 또는 Microsoft 365에 등록) 조직 또는 앱 개발자가 받는 Azure AD의 전용 인스턴스입니다. 각 Azure AD 테넌트는 고유하며 다른 Azure AD 테넌트와 별개입니다. 테 넌 트를 확인 하려면 응용 프로그램을 관리 하는 데 사용 하려는 계정으로 Azure Portal에 로그인 합니다. 테넌트가 있으면 자동으로 로그인되고 계정 이름 바로 아래에 테넌트 이름이 표시됩니다. Azure Portal의 오른쪽 위에 있는 사용자의 계정 이름을 가리켜 사용자의 이름, 이메일, 디렉터리/테넌트 ID(GUID) 및 사용자의 도메인을 표시합니다. 계정이 여러 테넌트와 연결되어 있으면, 계정 이름을 선택하여 테넌트 간에 전환할 수 있는 메뉴를 열 수 있습니다. 각 테넌트가 자기만의 ID를 가지고 있습니다. 에서 도메인 이름 URL을 사용 하 여 조직의 테 넌 트 ID를 조회할 수도 있습니다 [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

SaaS 제공이 테 넌 트 Id를 사용 하 여 개인 사용자를 정의 하는 동안 다른 제품 유형은 Azure 구독 Id (Guid로도 표시 됨)를 사용할 수 있습니다.

> [!NOTE]
> 비공개 사용자 (또는 제한 된 대상)는 미리 보기 대상과 다릅니다. **[미리 보기](#preview)** 탭에서 미리 보기 대상 그룹을 정의할 수 있습니다. 제품을 marketplace에 게시 하기 *전에* 미리 보기 대상 사용자가 제품에 대 한 액세스를 허용 합니다. 개인 대상 지정은 특정 계획에만 적용 되지만 미리 보기 대상은 모든 요금제를 볼 수 있지만 (비공개 또는 아님) 계획을 테스트 하 고 유효성을 검사 하는 동안에는 제한 된 미리 보기 기간 동안만 볼 수 있습니다.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Marketplace 제품 내 계획의 예제 목록

![노트와 함께 나열 된 마켓플레이스 계획 예제](./media/marketplace-plan.svg)

## <a name="test-drive"></a>시험 사용

**시험** 사용 탭에서는 고객이 제품을 구입 하기 전에 제품을 사용해 볼 수 있도록 데모 (또는 "시험 사용")를 설정할 수 있습니다. [테스트 드라이브인 이란?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)문서에서 자세히 알아보세요. 제품에 대 한 시험 사용을 더 이상 제공 하지 않으려는 경우 **[제품 설치](#offer-setup)** 페이지로 돌아가 **테스트 드라이브 사용**을 선택 취소 합니다.

### <a name="technical-configuration"></a>기술 구성
각각의 기술 구성 요구 사항을 포함 하는 다음과 같은 종류의 테스트 드라이브를 사용할 수 있습니다.

- [Azure 리소스 관리자](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [논리 앱](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (기술 구성 필요 없음)

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager 테스트 드라이브에 대 한 기술 구성

솔루션을 구성 하는 모든 Azure 리소스를 포함 하는 배포 템플릿입니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용 합니다. [Azure Resource Manager 테스트 드라이브](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)를 설정 하는 방법에 대해 자세히 알아보세요.

- **지역** (필수): 현재 테스트 드라이브를 사용 하도록 설정할 수 있는 26 개의 Azure 지원 지역이 있습니다. 일반적으로 가장 큰 수의 고객을 예상 하는 지역에서 테스트 드라이브를 사용할 수 있도록 하 여 최상의 성능을 위해 가장 가까운 지역을 선택할 수 있게 합니다. 구독에서 선택 하는 각 지역에 필요한 모든 리소스를 배포할 수 있는지 확인 해야 합니다.

- **인스턴스**: 제품을 사용할 수 있는 지역 수로 곱할 유형 (핫 또는 콜드) 및 사용할 수 있는 인스턴스 수를 선택 합니다.

**핫**: 이 유형의 인스턴스는 선택한 지역에 따라 배포 되 고 액세스를 대기 합니다. 고객은 배포를 기다릴 필요 없이 테스트 드라이브의 *핫* 인스턴스에 즉시 액세스할 수 있습니다. 단점은 이러한 인스턴스가 Azure 구독에서 항상 실행되고 있으므로 더 큰 가동 시간 비용이 발생한다는 것입니다. 대부분의 고객이 전체 배포를 기다리지 않고 *핫* 인스턴스를 사용할 수 없는 경우 고객 사용에 대 한 삭제를 원하지 않으므로 *핫* 인스턴스를 하나 이상 사용 하는 것이 좋습니다.

**콜드**: 이 유형의 인스턴스는 지역별 배포할 수 있는 총 인스턴스 수를 나타냅니다. 콜드 인스턴스는 고객이 시험 드라이브를 요청할 때 배포 하기 위해 전체 테스트 드라이브 리소스 관리자 템플릿이 필요 하므로 *콜드* 인스턴스는 *핫* 인스턴스 보다 로드 속도가 훨씬 느립니다. 이는 테스트 드라이브의 기간에 대해서만 비용을 지불 하면 되므로 항상 *핫* 인스턴스와 마찬가지로 Azure 구독에서 실행 *되지 않습니다* .

- **테스트 드라이브 Azure Resource Manager 템플릿**: Azure Resource Manager 템플릿을 포함 하는 .zip을 업로드 합니다.  빠른 시작 문서 [Azure Portal를 사용 하 여 Azure Resource Manager 템플릿을 만들고 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)하는 방법에 대 한 자세한 정보를 Azure Resource Manager.

- **테스트 드라이브 기간** (필수): 테스트 드라이브가 활성 상태로 유지 되는 기간 (시간)을 입력 합니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다. 이 기간에는 전체 시간 (예: "2" 시간, "1.5"은 유효 하지 않습니다.

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 시험 드라이브에 대 한 기술 구성

Microsoft는이 유형의 테스트 드라이브를 사용 하 여 서비스 프로 비전 및 배포를 호스팅하고 유지 관리 하 여 테스트 드라이브를 설정 하는 복잡성을 제거할 수 있습니다. 이 유형의 호스트 된 테스트 드라이브에 대 한 구성은 테스트 드라이브가 비즈니스 중부, 고객 참여 또는 작업 대상을 대상으로 하는지 여부에 관계 없이 동일 합니다.

- **최대 동시 테스트 드라이브** (필수): 테스트 드라이브를 한 번에 사용할 수 있는 최대 고객 수를 설정 합니다. 테스트 드라이브가 활성화 된 상태에서 각 동시 사용자는 Dynamics 365 라이선스를 사용 하므로 최대 제한 집합을 지 원하는 데 사용할 수 있는 충분 한 라이선스가 있는지 확인 해야 합니다. 권장 값은 3~5입니다.

- **테스트 드라이브 기간** (필수): 테스트 드라이브가 활성 상태로 유지 되는 시간을 시간 수를 정의 하 여 입력 합니다. 이 시간이 지난 후에는 세션이 종료 되 고 더 이상 라이선스 중 하나를 사용 하지 않습니다. 제품의 복잡도에 따라 2-24 시간 값을 설정 하는 것이 좋습니다. 이 기간에는 전체 시간 (예: "2" 시간, "1.5"은 유효 하지 않습니다.  사용자는 시간이 부족 하 고 테스트 드라이브에 다시 액세스 하려는 경우 새 세션을 요청할 수 있습니다.

- **인스턴스 URL** (필수): 고객이 시험 드라이브를 시작 하는 URL입니다. 일반적으로 샘플 데이터가 설치 된 앱을 실행 하는 Dynamics 365 인스턴스의 URL입니다 (예 https://testdrive.crm.dynamics.com):).

- **인스턴스 웹 API URL** (필수): Microsoft 365 계정에 로그인 하 고 **설정** \&gt;로 이동 하 여 Dynamics 365 인스턴스의 Web API URL을 검색 합니다. **사용자 지정** \&gt; **개발자 리소스** \&gt; **인스턴스 웹 API (서비스 루트 URL)** 에서 여기에 있는 url (예: https://testdrive.crm.dynamics.com/api/data/v9.0) )을 복사 합니다.

- **역할 이름** (필수): 사용자 지정 Dynamics 365 테스트 드라이브에서 정의한 보안 역할 이름을 제공 합니다. 이는 테스트 드라이브 (예: 테스트-드라이브-역할) 중에 사용자에 게 할당 됩니다.

#### <a name="technical-configuration-for-logic-app-test-drive"></a>논리 앱 테스트 드라이브에 대 한 기술 구성

모든 사용자 지정 제품은 다양 한 복잡 한 솔루션 아키텍처를 포함 하는이 유형의 테스트 드라이브 배포 템플릿을 사용 해야 합니다. 논리 앱 테스트 드라이브를 설정 하는 방법에 대 한 자세한 내용은 GitHub의 [작업](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) 및 [고객 참여](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) 를 참조 하세요.

- **영역** (필수, 단일 선택 드롭다운 목록): 현재 테스트 드라이브를 사용 하도록 설정할 수 있는 26 개의 Azure 지원 지역이 있습니다. 논리 앱에 대 한 리소스는 선택한 지역에 배포 됩니다. 논리 앱에 특정 지역에 저장 된 사용자 지정 리소스가 있는 경우 여기에서 해당 지역이 선택 되어 있는지 확인 합니다. 이 작업을 수행 하는 가장 좋은 방법은 포털의 Azure 구독에서 논리 앱을 로컬로 완전히 배포 하 고이를 선택 하기 전에 제대로 작동 하는지 확인 하는 것입니다.

- **최대 동시 테스트 드라이브** (필수): 테스트 드라이브를 한 번에 사용할 수 있는 최대 고객 수를 설정 합니다. 이러한 테스트 드라이브는 이미 배포 되어 있으므로 고객이 배포를 기다리지 않고 즉시 액세스할 수 있습니다.

- **테스트 드라이브 기간** (필수): 테스트 드라이브가 활성 상태로 유지 되는 기간 (시간)을 입력 합니다. 이 기간이 종료 되 면 테스트 드라이브가 자동으로 종료 됩니다.

- **Azure 리소스 그룹 이름** (필수): 논리 앱 테스트 드라이브가 저장 된 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) 이름을 입력 합니다.

- **Azure 논리 앱 이름** (필수): 사용자에 게 테스트 드라이브를 할당 하는 논리 앱의 이름을 입력 합니다. 이 논리 앱은 위의 Azure 리소스 그룹에 저장 해야 합니다.

- **논리 앱 이름 프로 비전** 해제 (필수): 고객이 완료 되 면 테스트 드라이브를 프로 비전 해제 하는 논리 앱의 이름을 입력 합니다. 이 논리 앱은 위의 Azure 리소스 그룹에 저장 해야 합니다.

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 테스트 드라이브에는 기술 구성이 필요 하지 않습니다.

대화형 Power BI 시각적 개체를 시연 하려는 제품은 포함 된 링크를 사용 하 여 사용자 지정 된 대시보드를 테스트 드라이브로 공유할 수 있으며 추가 기술 구성은 필요 하지 않습니다. [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) 템플릿 앱 설정에 대해 자세히 알아보세요.

### <a name="deployment-subscription-details"></a>배포 구독 정보

사용자를 대신 하 여 테스트 드라이브를 배포 하기 위해 별도의 고유한 Azure 구독을 만들어 제공 하세요. (Power BI 테스트 드라이브에는 필요 하지 않음)

- **Azure 구독 ID** (Azure Resource Manager 및 논리 앱에 필요): 리소스 사용량 보고 및 청구를 위해 Azure 계정 서비스에 대 한 액세스 권한을 부여 하려면 구독 ID를 입력 합니다. 아직 없는 경우 테스트 드라이브에 사용할 [별도의 Azure 구독을 만드는](https://docs.microsoft.com/azure/billing/billing-create-subscription) 것이 좋습니다. [Azure Portal](https://portal.azure.com/) 에 로그인 하 고 왼쪽 메뉴의 **구독** 탭으로 이동 하 여 Azure 구독 ID를 찾을 수 있습니다. 탭을 선택 하면 구독 ID (예: "a83645ac-1234-5ab6-6789-1h234g764

- **AZURE AD 테 넌 트 ID** (필수): AD (Azure Active Directory) [테 넌 트 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 입력 합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고, 왼쪽 메뉴에서 Active Directory 탭을 선택 하 고, **속성** 을 선택한 다음, 나열 된 **디렉터리 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 확인 합니다. 에서 도메인 이름 URL을 사용 하 여 조직의 테 넌 트 ID를 조회할 수도 있습니다 [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **AZURE AD 테 넌 트 이름** (동적 365에 필요): Azure Active Directory (AD) 이름을 입력 합니다. 이 이름을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고, 오른쪽 위 모서리에 있는 테 넌 트 이름이 계정 이름 아래에 나열 됩니다.

- **AZURE AD 앱 ID** (필수): Azure Active Directory (AD) [응용 프로그램 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 입력 합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고 왼쪽 메뉴에서 Active Directory 탭을 선택한 다음 **앱 등록**를 선택 하 고 나열 된 **응용 프로그램 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 확인 합니다.

- **AZURE AD 앱 클라이언트 암호** (필수): Azure AD 응용 프로그램 [클라이언트 암호](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)를 입력 합니다. 이 값을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 합니다. 왼쪽 메뉴에서 **Azure Active Directory** 탭을 선택 하 고 **앱 등록**을 선택한 다음 테스트 드라이브 앱을 선택 합니다. 그런 다음 **인증서 및 비밀**을 선택 하 고, **새 클라이언트 암호**를 선택 하 고, 설명을 입력 하 고, **만료**됨 **을 선택 하** 고, **추가**를 선택 합니다. 값을 복사 해야 합니다. 이 작업을 수행 하기 전에 페이지에서 다른 곳으로 이동 하지 마세요. 그렇지 않으면 값에 액세스할 수 없습니다.

다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.

### <a name="test-drive-listings-optional"></a>드라이브 목록 테스트 (선택 사항)

테스트 **드라이브 탭 아래** 에 있는 **테스트 드라이브 목록** 옵션은 테스트 드라이브를 사용할 수 있는 언어 (및 시장)를 표시 하며 현재 영어 (미국)만 사용할 수 있는 위치입니다. 또한이 페이지에는 언어별 목록과 추가 된 날짜/시간의 상태가 표시 됩니다. 각 언어/시장에 대해 테스트 드라이브 세부 정보 (설명, 사용자 설명서, 비디오 등)를 정의 해야 합니다.

- **설명** (필수): 사용자가 제품을 확보할 지 여부를 결정 하는 데 도움이 되는 테스트 드라이브, 표시 되는 내용, 탐색할 기능 및 관련 정보를 설명 합니다. 이 필드에는 최대 3000 자의 텍스트를 입력할 수 있습니다. 

- **액세스 정보** (Azure Resource Manager 및 논리 테스트 드라이브에 필요 함): 이 시험 사용에 액세스 하 고 사용 하기 위해 고객이 알아야 할 사항을 설명 합니다. 제품 사용에 대 한 시나리오와 고객이 테스트 드라이브를 통해 기능에 액세스 하기 위해 알아야 하는 사항을 정확히 살펴봅니다. 이 필드에는 최대 1만 자의 텍스트를 입력할 수 있습니다.

- **사용자 설명서** (필수): 테스트 드라이브 환경을 자세히 연습 합니다. 사용자 설명서는 시험 사용에 대 한 고객의 의견을 정확 하 게 파악 하 고 있을 수 있는 질문에 대 한 참조로 사용할 수 있습니다. 업로드 후 파일은 PDF 형식 이어야 하며 (최대 255 자) 이름이 지정 되어야 합니다.

- **비디오 비디오** 추가 (선택 사항): 비디오는 YouTube 또는 Vimeo에 업로드 하 고 링크 및 미리 보기 이미지 (533 x 324 픽셀)를 사용 하 여 여기에서 참조할 수 있으므로 고객은의 기능을 사용 하는 방법을 비롯 하 여 테스트 드라이브를 보다 잘 이해할 수 있도록 정보를 단계별로 볼 수 있습니다. 혜택을 강조 하는 시나리오를 제공 하 고 이해 합니다.
  - **이름** 하다
  - **URL (YouTube 또는 Vimeo만 해당)** 하다
  - **축소판 그림 (533 x 324px)** : 이미지 파일은 PNG 형식 이어야 합니다.

이러한 필드를 완료 한 후 **저장** 을 선택 합니다.

## <a name="publish"></a>게시

#### <a name="submit-offer-to-preview"></a>미리 보기로 제품 제출

제품의 필수 섹션을 모두 완료 한 후에는 포털의 오른쪽 위 모서리에서 **게시** 를 선택 합니다. **검토 및 게시** 페이지로 다시 이동 됩니다. 

이 제품을 처음으로 게시 하는 경우 다음을 수행할 수 있습니다.

- 제품의 각 섹션에 대 한 완료 상태를 확인 합니다.
    - *시작 되지 않음* -섹션이 작업 되지 않아 완료 해야 함을 의미 합니다.
    - *불완전* -섹션에 수정 해야 하는 오류가 있거나 추가 정보를 제공 해야 함을 의미 합니다. 섹션으로 돌아가서 업데이트 하세요.
    - *Complete* – 섹션이 완료 되었고 필요한 모든 데이터가 제공 되었으며 오류가 없음을 의미 합니다. 제품의 모든 섹션은 제품을 제출 하기 전에 완전 한 상태 여야 합니다.
- 앱을 이해 하는 데 도움이 되는 보조 노트 뿐만 아니라 앱이 올바르게 테스트 되었는지 확인 하기 위해 인증 팀에 테스트 지침을 제공 합니다.
- **제출**을 선택 하 여 게시에 대 한 제품을 제출 합니다. 제품의 미리 보기 버전을 검토 하 고 승인할 수 있는 시기를 알려 주는 전자 메일을 보내 드리겠습니다. 파트너 센터로 돌아가서 제품을 공개 (또는 개인 제품의 경우 비공개 사용자에 게 공개)에 게시 하려면 [ **라이브]** 를 선택 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [상업적 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
