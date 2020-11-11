---
title: 시험 사용이란? Microsoft 상업적 marketplace
description: Marketplace 시험 구동 기능 설명
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: 304e1ab475213bde2644c8241905408ade5672ed
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489967"
---
# <a name="what-is-a-test-drive"></a>시험 사용이란?

시험 (시험) 드라이브는 구매 하기 전에 시도 하는 옵션을 제공 하 고, 우량으로 선별 된 잠재 고객을 생성 하 고, 향상 된 변환을 제공 하 여 잠재 고객에 게 제품을 소개 하는 좋은 방법입니다. 테스트 드라이브는 실제 구현 시나리오에서 제품을 최신 상태로 만듭니다. 제품을 시험 하는 고객은 비슷한 솔루션을 구입 하기 위한 명확한 의도를 보여 줍니다. 이를 통해 더 많은 고급 잠재 고객을 사용 하 여 혜택을 얻을 수 있습니다.

고객은 테스트 드라이브를 활용 하는 것도 좋습니다. 제품을 먼저 사용해 볼 수 있도록 허용 하면 구매 프로세스의 마찰을 줄일 수 있습니다. 또한 테스트 드라이브가 미리 프로 비전 됩니다. 즉, 고객이 제품을 다운로드 하거나 설정 하거나 구성할 필요가 없습니다.

## <a name="how-does-it-work"></a>작동 원리

테스트 드라이브는 솔루션을 요청 하는 고객에 대해 주문형 응용 프로그램 또는 응용 프로그램을 실행 하는 관리 되는 인스턴스입니다. 테스트 드라이브 인스턴스가 할당 되 면 해당 고객이 설정 된 기간 동안 사용할 수 있습니다. 기간이 종료 된 후에는 다른 고객을 위한 공간을 확보 하기 위해 삭제 됩니다.

게시자는 파트너 센터에서 테스트 드라이브 설정을 관리 하 고 구성 합니다. 기술 구성 세부 정보는 제품 유형에 따라 달라 집니다. 자세한 지침은 [테스트 드라이브 기술 구성](https://docs.microsoft.com/azure/marketplace/test-drive-technical-configuration)을 참조 하세요.

잠재적 고객은 [Appsource](https://appsource.microsoft.com/en-US/)에서 제품의 cta로 테스트 드라이브를 검색할 수 있습니다. 연락처 정보를 제공 하 고 제품의 사용 약관 및 개인 정보 취급 방침에 동의한 다음, 미리 구성 된 환경에 대 한 액세스 권한을 획득 하 여 고정 된 기간 동안 시험해 보세요. 고객은 제품의 주요 기능 및 혜택에 대 한 실습 자체 기반 평가판을 받고 귀중 한 잠재 고객을 받게 됩니다.

## <a name="types-of-test-drives"></a>테스트 드라이브 유형

사용 중인 제품, 시나리오 및 marketplace의 유형에 따라 상업용 marketplace에서 선택할 수 있는 다양 한 테스트 드라이브가 있습니다.

- Azure Resource Manager
    - Azure 응용 프로그램
    - SaaS
    - Virtual Machines
- 호스트 된 테스트 드라이브
    - Dynamics 365 for Business Central (현재 지원 되지 않음)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- 논리 앱 (지원 모드 에서만)
- Power BI

이러한 테스트 드라이브 중 하나를 구성 하는 방법에 대 한 자세한 내용은 [테스트 드라이브 기술 구성](https://docs.microsoft.com/azure/marketplace/test-drive-technical-configuration)을 참조 하세요. 

### <a name="azure-resource-manager-test-drive"></a>시험 드라이브 Azure Resource Manager

이 배포 템플릿에는 솔루션을 구성 하는 모든 Azure 리소스가 포함 되어 있습니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용합니다. 이러한 제안 유형에는 Azure Resource Manager 테스트 드라이브를 사용할 수 있습니다. 

- Azure 애플리케이션
- SaaS
- 가상 머신

>[!NOTE]
>가상 컴퓨터 및 Azure 응용 프로그램 제품에 대 한 유일한 테스트 드라이브 옵션입니다.

### <a name="hosted-test-drive-recommended"></a>호스트 된 테스트 드라이브 (권장)

호스트 된 테스트 드라이브를 사용 하면 Microsoft 호스트에서 테스트 드라이브 사용자 프로 비전 및 프로 비전 해제를 수행 하는 서비스를 관리할 수 있으므로 설치의 복잡성이 제거 됩니다. Microsoft AppSource에 대 한 제안이 있는 경우 Dynamics AX/CRM 인스턴스를 사용 하 여 연결 하는 테스트 드라이브를 빌드합니다. 다음 AppSource 제품 유형을 사용할 수 있습니다.

- 판매, 서비스, 프로젝트 서비스 및 현장 서비스와 같은 고객 engagement 시스템에 대해 [고객 engagement에 Dynamics 365](partner-center-portal/create-new-customer-engagement-offer.md) 을 사용 합니다.
- 재무, 운영, 제조, 공급망 등의 재무 및 운영 enterprise 리소스 계획 시스템에 대해 [Dynamics 365](partner-center-portal/create-new-operations-offer.md) 을 사용 합니다.

### <a name="logic-app-test-drive"></a>논리 앱 테스트 드라이브

이 유형의 테스트 드라이브는 Microsoft에서 호스팅되지 않으며 Dynamics AX/CRM 제품 유형에 대 한 ARM (Azure Resource Manager) 템플릿을 사용 합니다. Azure 구독에 필요한 리소스를 만들려면 ARM 템플릿을 실행 해야 합니다. 논리 앱 테스트 드라이브는 현재 지원 모드 전용 이며 Microsoft에서 논리 앱 테스트 드라이브를 구성 하는 방법에 대 한 자세한 내용은 [테스트 드라이브 기술 구성](https://docs.microsoft.com/azure/marketplace/test-drive-technical-configuration)을 참조 하세요.

### <a name="power-bi-test-drive"></a>Power BI 시험 사용

이는 단순히 사용자가 작성 한 대시보드에 대 한 포함 된 링크입니다. 대화형 Power BI 시각적 개체를 보여 주는 모든 제품은이 유형의 테스트 드라이브를 사용 해야 합니다.

## <a name="transforming-examples"></a>변환 예

리소스의 아키텍처를 테스트 드라이브로 전환 하는 프로세스는 어려울 수 있습니다. 현재 아키텍처를 가장 잘 변환 하는 방법에 대 한 예제를 확인 하세요.

[웹 사이트 템플릿을 시험 드라이브로 변환](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[가상 컴퓨터 템플릿을 시험 드라이브로 변환](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[기존 리소스 관리자 템플릿을 시험 드라이브로 변환](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>테스트 드라이브에서 리드 생성

상용 marketplace 테스트 드라이브는 마케터에 적합 한 도구입니다. 비즈니스에 더 많은 잠재 고객을 생성 하기 위해를 시작할 때 출시 간 노력에 통합 하는 것이 좋습니다. 자세한 지침은 [상용 marketplace 제품의 고객 리드](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)를 참조 하세요.

시험 사용 리드를 사용 하 여 처리를 종료 하는 경우 [Microsoft 파트너 판매 연결](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect)에 등록 해야 합니다. 또한 테스트 드라이브가 역할을 수행 하는 고객의 wins에 대해 알아보겠습니다.

## <a name="other-resources"></a>기타 리소스

추가 시험 사용 리소스:

- [시험 드라이브 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [개요](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)(PDF. 팝업 차단이 해제되어 있는지 확인)

## <a name="next-step"></a>다음 단계

- [시험 사용 기술 구성](test-drive-technical-configuration.md)
