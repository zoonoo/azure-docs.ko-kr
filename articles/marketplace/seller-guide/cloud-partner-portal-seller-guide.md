---
title: "Azure Marketplace 판매자 가이드 | Microsoft Docs"
description: "이 가이드는 Azure Certified Virtual Machine 이미지를 IT 전문가 및 개발자에게 판매하려는 ISV(독립 소프트웨어 공급업체)의 비즈니스 사용자 및 제품 관리자를 위해 작성되었습니다."
documentationcenter: 
author: rupeshazure
manager: hamidm
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: rupesk@microsoft.com
ms.robots: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 59a0f0ec1b7b576339a45be26be8543ac0650a8a
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017



---
# <a name="azure-marketplace-seller-guide"></a>Azure Marketplace 판매자 가이드

Azure Marketplace 판매자 가이드를 시작합니다. 이 가이드는 Azure Certified Virtual Machine 이미지를 IT 전문가 및 개발자에게 판매하려는 ISV(독립 소프트웨어 공급업체)의 비즈니스 사용자 및 제품 관리자를 위해 작성되었습니다. 전 세계에 있는 Azure 고객을 통해 [Marketplace](https://azuremarketplace.microsoft.com/)는 제품에 대한 풍부한 도달률 및 노출을 제공할 수 있습니다.


> [!NOTE]
> 완성된 SaaS(Software as a Service) 제품을 비즈니스 사용자에게 판매하려는 경우 [AppSource](https://appsource.microsoft.com)에 나열하는 옵션을 조사할 수 있습니다.

이 가이드의 끝부분에서는 다음 항목에 대한 자세한 정보를 어디에서 확인할 수 있는지 알게 됩니다.

- Azure 마켓플레이스란 무엇인가요?
- 내 제품이 Marketplace에 맞는지 어떻게 확인하나요?
- Marketplace에서 판매하면 어떤 이점이 있나요?
- Marketplace에서 판매하기 위한 필수 조건(기술 및 비기술)은 무엇인가요?
- Azure 호환 가상 하드 디스크(VHD)는 어떻게 빌드하나요?
- 판매자로 어떻게 적용 및 등록하나요?
- 내 제품을 만들고 게시하려면 어떻게 하나요?
- 시장으로 이동하여 사용 가능한 리소스는 어떻게 찾나요?
- Marketplace에서 어떤 보고 및 통찰력을 제공하나요?
- 도움말 및 지원은 어디에서 받을 수 있나요?

이제 시작하겠습니다.

## <a name="whats-the-azure-marketplace"></a>Azure Marketplace란 무엇인가요?

Azure Marketplace는 신생 기업에서 기존 기업까지 ISV가 전 세계 Azure 고객에게 자사의 솔루션을 제공하는 온라인 응용 프로그램 및 서비스 Marketplace입니다. Azure 게시자는 Marketplace를 통해 Azure에서 해당 클라우드 기반 응용 프로그램 및 모바일 솔루션을 신속하게 개발하려는 다른 전문가에게 가상 컴퓨터 이미지를 배포 및 판매할 수 있습니다. Marketplace는 데이터 처리, 데이터 저장소 및 분석 계층을 포함한 종단 간 데이터 분석 응용 프로그램에서 계층화된 전자 상거래 앱(데이터, 서비스 및 인터넷)까지 다양한 제품을 지원합니다.

클라우드 고객은 고객의 고유한 요구에 맞는 솔루션을 검색할 때 여러 가지 문제에 직면합니다. Marketplace는 이러한 문제를 해결하고 고객과 혁신적인 ISV 솔루션을 연결하는 방법을 제공하며 이에 대한 내용은 다음 표에 설명되어 있습니다.

| 고객 요구 | Azure Marketplace 솔루션 |
| --- | --- |
| 비즈니스 및 기술적 요구에 맞게 추가적인 클라우드 플랫폼 기능 요구 | Azure에서 보완적인 응용 프로그램 및 서비스의 확장 포트폴리오 제공 |
| 적절한 응용 프로그램 또는 서비스를 찾기가 어려움 | 응용 프로그램 및 서비스를 찾고 검색하며 구입할 수 있는 원스톱 상점 제공 |
| 타사 응용 프로그램 및 서비스를 위한 확장성 있는 배포 메커니즘 필요 | 타사 응용 프로그램 및 서비스를 위한 확장성 있는 배포 생성 및 구성 가능 |
| 기존 솔루션과 통합 및 연동하기 위한 새로운 응용 프로그램 및 서비스 필요 | 타사 응용 프로그램 및 서비스를 Azure에서 기존 솔루션과 손쉽게 통합 |

Marketplace는 Azure 파트너 에코시스템의 옵션 및 장점을 전 세계 고객에게 제공합니다. 주요 이점은 다음과 같습니다.

- Microsoft 및 파트너로부터 Azure 기반 제품에 대한 단일화된 위치 제공
- 5,000가지 이상의 제품 제공
- 통합된 플랫폼 환경
- 간소화된 구성, 배포 및 관리

## <a name="is-the-marketplace-right-for-my-business"></a>내 비즈니스에 Marketplace가 적합한가요?

이제 Azure Marketplace가 비즈니스에 적합한지 궁금할 수 있습니다. 그렇다면 얻고자 하는 것은 무엇인가요? Marketplace는 새로운 매출 기회를 만듭니다.

- **Azure에서 확장된 솔루션 포트폴리오로 신규 고객 시장에 진출합니다.** MOSP(Microsoft Online Subscription Program) 및 Microsoft 기업계약 고객에게 제공되는 Azure 서비스로 Marketplace 제품을 상향 판매 및 교차 판매합니다. Marketplace 제품을 고객 솔루션 및 다양한 Azure 시나리오에 손쉽게 통합할 수 있습니다.
- **기존 및 신규 고객 계정으로 비즈니스 가치를 개선하고 거래 규모를 늘립니다.** Marketplace는 거래 규모를 늘리고 워크로드를 클라우드로 옮길 때 고객의 고충을 해결하며 거래 수익성을 높이는 데 도움이 됩니다. 완벽한 솔루션을 판매하여 비즈니스 가치를 높이고 고객의 요구 사항을 충족시키도록 Azure 클라우드 플랫폼 격차를 해결할 수 있습니다.
- **Marketplace 응용 프로그램 및 서비스를 판매하여 광범위한 잠재 고객의 관심을 끕니다.** Marketplace는 신규 고객을 쉽게 찾고 보유할 수 있습니다. 오늘날 많은 비즈니스는 워크로드를 클라우드로 전환하고 끊임없이 변화하는 인프라 환경에 적응해야 합니다. 이러한 차이를 메워줄 적합한 응용 프로그램 및 서비스를 제공할 수 있습니다.
- **Azure 서비스와 함께 Marketplace 제품을 묶어서 Azure 기능을 보완하고 확장합니다.** Marketplace는 고객과의 시나리오 기반 대화를 구성하는 데 도움을 줄 수 있습니다. 또한 중요한 종단 간 솔루션에 대해 이야기함으로써 특정 플랫폼 격차 및 고객 요구 사항도 해결할 수 있습니다. 마지막으로, 솔루션 번들을 판매함으로써 Azure 플랫폼 에코 시스템을 활용하여 다양한 고객 문제를 해결하고 매출을 높일 수 있습니다.

## <a name="what39s-the-customer-base-for-the-marketplace"></a>Marketplace에 대한 고객층은 어떻게 되나요?

Marketplace 고객은 다양합니다. 또한 Azure는 모든 클라우드 공급자 중에서 급성장하는 고객층 중 하나입니다. 신생 업체에서 기업, 여러 산업, 공공 및 민간 부문에 이르기까지 다양한 회사에서 일하는 IT 전문가 및 개발자와 접촉할 수 있습니다.

## <a name="how-does-the-marketplace-work"></a>Marketplace는 어떻게 작동하나요?

아주 쉽습니다. 승인되면 Azure Certified Virtual Machine 이미지를 만들어 Marketplace에 게시합니다. Azure 고객은 몇 분만에 제품을 찾아서 구입하고 배포할 수 있습니다. 더 나아가 고객은 자신있게 솔루션을 배포합니다. 고객은 환경이 Azure에서 성공적으로 구성되었고 인프라가 몇 분 안에 사용할 준비가 된다는 것을 알고 있습니다.

클라우드 파트너 포털은 Marketplace에서 제품을 만들기 위한 허브입니다. 가상 컴퓨터 이미지는 완전히 설치된 운영 체제 및 하나 이상의 응용 프로그램과 함께 미리 구성됩니다. 게시 준비가 완료되도록 이미지를 인증하려면 특정 필수 조건을 충족해야 합니다. 다음 섹션에서 설명합니다.


## <a name="whats-next"></a>다음 작업

Azure Marketplace가 실제로 제품에 적합하다고 생각할 수도 있습니다. 그렇다면 어떻게 시작할까요? 이 섹션은 다음을 통해 Marketplace에서 시작하고 실행하는 모든 사항을 보여 줍니다(그림 1). 
* Azure Certified 되기
* 제품 판매 승인 받기
* 클라우드 파트너 포털에서 제품 만들기 

![Azure Marketplace에서 판매 프로세스](./media/cloud-partner-portal-seller-guide/processforselling.png)

그림 1: Azure Marketplace에서 판매 프로세스

먼저 기술적 및 비기술적 필수 조건을 충족하고 가상 컴퓨터 이미지를 준비합니다. 그런 다음 제품을 지정하고 판매자로 등록합니다. 마지막으로 마케팅 콘텐츠를 추가하고 게시를 위해 제출합니다. Marketplace에 라이브 상태로 만들기 전에 미리 보기/스테이징 환경에서 제품을 검토할 수 있습니다.

처음으로 Azure Marketplace에 대한 제품을 만들면 기본 등록을 위해 약 4주를 계획해야 합니다. 가능하다면 미디어 및 게시 작업을 위한 시간을 할애하기 위해 제품 출시 6주 전에 빌드합니다.

## <a name="how-do-i-become-azure-certified"></a>Azure 인증은 어떻게 받나요?

Azure Marketplace에 대한 제품을 만드는 첫 번째 단계는 Azure Certified가 되는 것입니다. 이는 회사 정보를 수집하고 참여 정책에 동의하며 필요한 도구를 다운로드하고 기술 구성 요소를 구축하는 것을 의미합니다(그림 2).

![Azure 인증을 받기 위한 요구 사항](./media/cloud-partner-portal-seller-guide/azurecertified.png)

그림 2: Azure 인증을 받기 위한 요구 사항

### <a name="technical-prerequisites"></a>기술적 필수 조건

시작하기 전에 주의 깊게 검토하고 모든 기술적인 필수 조건을 충족시킵니다. Windows 또는 Linux뿐 아니라 테스트 도구에 연결된 Azure 호환 VHD에 액세스해야 합니다.

원격 데스크톱 프로토콜을 사용하여 클라우드에서 직접 Azure VHD를 개발하는 것이 좋습니다. 그러나 이 방법이 유일한 옵션인 경우 VHD를 다운로드하고 [온-프레미스 인프라](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-on-premise)를 사용하여 개발할 수 있습니다.

자세한 내용은 [기술적 필수 조건](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites) 페이지를 참조하세요.

### <a name="nontechnical-prerequisites"></a>비기술적 필수 조건

Marketplace에 속하려면 몇 가지 비기술적 필수 조건을 충족해야 합니다. 먼저, [Azure Marketplace 참가 정책](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) 약관을 검토하고 동의합니다. Marketplace에 제공되는 소프트웨어 및 서비스는 다음 요구 사항 중 하나 이상을 충족해야 합니다.

- **Azure에서 실행**. 소프트웨어 또는 서비스의 기본 기능이 Microsoft Azure에서 실행되어야 합니다.
- **Azure에 배포**. 목록 정보에서 소프트웨어 또는 서비스가 Azure에서 배포되는 방법을 설명해야 합니다.
- **Azure 서비스와 통합 또는 확장**. 목록 정보를 지정해야 합니다.
  - 소프트웨어 또는 서비스와 통합 또는 확장되는 Azure 서비스
  - 소프트웨어 또는 서비스가 Azure 서비스와 통합 또는 확장되는 방법

Azure Marketplace 참가 정책에 설명된 대로 다음과 같은 비즈니스 요구에 부합해야 합니다.

- 회사(또는 해당 자회사)는 Marketplace에서 지원하는 판매처 국가에 위치해야 합니다.
- 제품은 Marketplace에서 지원하는 청구 모델과 호환되는 방식으로 허가를 받아야 합니다.
- 무료, 유료 또는 커뮤니티 지원을 통해 상업적으로 합리적인 방식으로 고객이 이용할 수 있는 기술 지원을 담당합니다.
- 소프트웨어 및 타사 소프트웨어 종속성에 대해 사용 허가를 받아야 합니다.
- 콘텐츠는 [azure.microsoft.com](../../C:/Users/Lisa.Rosenberger/Desktop/azure.microsoft.com) 및 Azure Portal에서 나열된 제품에 대한 특정 조건을 충족해야 합니다.

마지막으로 [사용 약관](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft 개인정보처리방침](http://www.microsoft.com/privacystatement/default.aspx) 및 [Microsoft Azure Certified 프로그램 계약](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)에 동의해야 합니다. 

일반적인 질문 목록은 [Azure Marketplace FAQ](https://azure.microsoft.com/marketplace/faq/)를 참조하세요.


### <a name="azure-certification"></a>Azure 인증

_Azure 인증_ 상태를 받았다는 것은 등록 프로세스가 성공적으로 완료되었음을 나타냅니다. 이 상태는 고객이, IT 전문가 및 개발자가 신뢰할 수 있는 파트너의 Azure 기술로 실행되거나 Azure 기술을 토대로 구축된 고품질 솔루션을 보유하고 있다는 확신을 갖게 해줍니다. Azure 인증 솔루션은 다음과 같습니다.

- 전역 심사
- Azure 플랫폼과의 호환성 확인
- 온라인 이미지 안전 규정 준수
- 바이러스 또는 맬웨어 없음
- 지원되는 청구 모델

## <a name="how-do-i-nominate-my-product-and-get-approved"></a>내 제품을 어떻게 지정하고 승인을 받나요?

이제는 Marketplace에서 제품을 판매하도록 승인을 받을 차례입니다(그림 3). Microsoft에서는 제품을 쉽게 지정하고 게시 프로세스를 완료하며 판매자로 등록하도록 지원합니다

![Azure Marketplace에서 판매하도록 승인](./media/cloud-partner-portal-seller-guide/gettingapprovedsteps.png)

그림 3: Azure Marketplace에서 판매하도록 승인을 받는 단계

승인을 받는 첫 번째 단계는 제품을 [지정](https://createopportunity.azurewebsites.net/)하는 것이며 그런 다음 등록 및 게시합니다. 승인에는 _최대 3영업일_이 소요될 수 있습니다.

승인되면 다음 내용을 받게 됩니다.

- 개발 센터 및 클라우드 파트너 포털의 프로필에 대한 $99 신청 수수료를 면제하는 프로모션 코드가 포함된 메일 영수증
- 제품을 만들고 VHD를 인증하는 옵션과 함께 Azure 인증 상태에 대한 기술적 사전 승인 (제품을 만들려면 개발 센터 응용 프로그램을 먼저 승인해야 합니다.)
- 클라우드 파트너 포털에 액세스하기 위한 지침 및 게시 프로세스에 대한 개요입니다.
- Microsoft 온보딩 팀과 통화하여 프로세스를 진행하고 질문할 수 있는 자격
- 두 번째 제품을 게시하는 기능. 두 번째 제품은 승인을 거칠 필요가 없습니다. 클라우드 파트너 포털로 직접 이동할 수 있지만 Virtual Machines는 게시 프로세스를 통해 여전히 인증되어야 합니다.
- 게시를 통해 도움을 요청하는 지침. (질문은 Marketplace 게시자 [지원 링크](https://support.microsoft.com/en-us/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfName=productselection&prid=16230&ccsid=636282352448485256)로 리디렉션되어야 합니다.)

마지막으로 Microsoft 판매자로 [계정을 등록](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration)합니다. 승인 및 심사는 _최대 2주_ 소요될 수 있으므로 이때 클라우드 파트너 포털에서 Azure Marketplace 제품을 만듭니다.

## <a name="how-do-i-publish-my-offer-on-the-azure-marketplace"></a>Azure Marketplace에서 제품을 어떻게 게시하나요?

이제 가상 컴퓨터 이미지를 인증하고 제품을 게시할 준비가 되었습니다. 이렇게 하려면 클라우드 파트너 포털을 사용합니다. 클라우드 파트너 포털을 솔루션을 게시하고 관리하기 위한 허브로 생각할 수 있습니다. 기본적으로 VHD를 업로드하고, 마케팅 콘텐츠 및 SKU 세부 정보를 추가하고, 인증 및 검토를 위해 제품을 제출하면 됩니다. Marketplace에서 사용할 수 있기 전에 제품 미리 보기 및 모양을 확인할 수 있습니다.

## <a name="what-about-best-practices"></a>모범 사례 정보

다음은 Marketplace에서 판매자가 되는 데 최대한 활용할 수 있는 몇 가지 도구와 모범 사례입니다.

### <a name="azure-test-drives"></a>Azure 시험 사용

[Azure 시험 사용](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/test-drives?page=1)는 잠재 고객에게 제품을 소개하고 구매하기 전에 사용해 볼 수 있는 옵션을 제공하는 훌륭한 방법입니다. 시험 사용을 통해 전환을 늘리고 업계를 선도할 수 있습니다.

연락처 정보를 제공한 후 고객은 미리 빌드된 시험 사용에 액세스할 수 있습니다. 실제 시나리오에서 제품의 주요 기능 및 장점에 대한 실습, 자기 주도형 체험을 경험합니다.

현재는 제품에 대한 시험 사용 게시가 클래식 게시 포털에서만 제공됩니다. [새 시험 사용을 게시하는 방법](https://github.com/Azure/AzureTestDrive/wiki)에 대한 문서로 이동하여 자세히 알아봅니다.

[Azure 시험 사용](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)에 대해 자세히 알아봅니다.

### <a name="go-to-market-checklist"></a>Go-to-market 검사 목록

조직의 글로벌 접근성을 확장하는 데 도움이 될 수 있는 [go-to-market 프로그램](https://partner.microsoft.com/go-to-market/)에 대해 자세히 알아보세요. [Partner Marketing Center](http://smartpartnermarketing.microsoft.com/isv)의 리소스도 활용할 수 있습니다.

시작하기 전에 Marketplace 제품을 신속하게 구동할 수 있는 몇 가지 단계를 수행하는 것이 좋습니다. 이 검사 목록을 사용하여 준비가 되었는지 확인합니다.

- 블로그를 게시하거나, 메일을 보내거나, 보도 자료를 발행하여 **Marketplace에서 내 제품을 사용할 수 있다고 발표했습니다.**
- **내 웹 사이트에서 제품 홍보**, Marketplace에서 고객을 내 제품으로 안내합니다.
- **시험 사용을 게시**하여 고객이 휴식 시간에 Azure에서 실시간으로 내 제품을 경험할 수 있습니다.
- **주문형 리드 생성을 활성화**하여 고객이 내 응용 프로그램을 배포하기 위해 클릭할 때마다 해당 이름과 연락처 정보를 받습니다.
- Microsoft에서 **내 파트너 관리자와 연락**(있는 경우)하여 추가 기회를 탐색했습니다.

## <a name="what-about-reports"></a>보고서 정보

Marketplace는 Marketplace [게시자 보고 포털](https://reports.azure.com)을 통해 액세스할 수 있는 주문, 사용량 및 고객에 대한 보고서를 제공합니다. 유용한 통찰력 및 분석 외에도 원시 데이터는 탐색 가능한 테이블로 제공되고 CSV 또는 XLS 파일로 다운로드할 수 있습니다.

이 [비디오](https://player.vimeo.com/video/200859918)는 다음과 같은 보고서 기능 및 혜택의 미리 보기를 제공합니다.

- 보고서 유형: 홈 페이지에서 주문, 사용량 및 고객 동향의 요약 스냅숏
- 주문, 사용량 및 고객 데이터의 세부 정보
- 월간 요약 또는 6개월 추세 보기로 표시된 주문 및 사용량
- 표준으로 표시된 다양한 통찰력
- 다음을 기준으로 한 사용량/주문:
  - 시장
  - 채널
  - 유행 제품
  - Marketplace 라이선스 유형

상세 보고서에는 회사 이름 및 우편 번호와 같은 지리적 위치 등의 고객 정보가 표시되어 판매자를 지원할 수 있습니다. 다음 목록은 고객에 대해 제공하는 구체적인 특성입니다.

- Reseller
- FirstName
- LastName
- Email
- CompanyName
- TransactionDate
- SubscriptionName
- AzureSubscriptionId
- CloudInstanceName
- OrderCount
- CustomerCountryRegion
- CustomerCity
- CustomerCommunicationCulture
- CustomerZipCode

도움말 문서, 용어집 및 녹화된 데모를 통한 학습도 제공합니다. 보고서에 대한 도움 또는 지원이 필요한 경우 [지원 티켓](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=636233723471685249)을 열면 됩니다.

ISV 판매자 커뮤니티에 오신 것을 환영하며 제품을 게시하기를 기대합니다.

---

