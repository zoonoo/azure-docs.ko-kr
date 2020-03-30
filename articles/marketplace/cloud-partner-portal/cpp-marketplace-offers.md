---
title: Azure 및 AppSource Marketplace 제품
description: Azure 및 AppSource Marketplace의 제품 만들기 및 관리
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278487"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Azure 및 AppSource Marketplace 제품

이 섹션의 첫 번째 부분에서는 Azure 및 AppSource Marketplace의 제품을 만들고 관리하는 데 사용되는 일반적인 작업을 소개합니다.  이 부분은 모든 제품 유형에 공통적인 기술 정보뿐만 아니라 특정 제품 유형을 관리하기 위해 알아 두어야 하는 배경 정보를 제공합니다.  이 섹션에는 대부분 특정 제품 유형을 만들고 관리하는 방법에 대한 자세한 지침이 포함되어 있습니다.  

다음 비디오에서는 Azure Marketplace 또는 AppSource에서 사용할 수 있는 다양한 기능과 다양한 제품 유형을 소개합니다.  또한 이러한 마켓플레이스에 애플리케이션 또는 서비스 게시와 관련된 중요한 기술 및 비즈니스 측면도 다룹니다.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Azure Marketplace 및 AppSource에 대한 앱 및 서비스 빌드 - 빌드 2018**

이러한 Marketplace에 대한 자세한 내용은 [Azure Marketplace 및 AppSource 게시 가이드](../marketplace-publishers-guide.md)를 참조하세요.


## <a name="common-offer-operations"></a>일반적인 제품 작업

새 제품을 만드는 과정은 예를 들어 [Azure 애플리케이션 제품](./azure-applications/cpp-azure-app-offer.md)과 [컨설팅 서비스 제품](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) 등 제품 유형 간에 크게 다릅니다.  반면, [Cloud 파트너 포털](https://cloudpartner.azure.com)의 제품에 대해 수행하는 다른 많은 작업은 제품 유형 간에 비교적 표준화되었습니다.  게시, 상태 보기, 업데이트 및 삭제와 같은 일반적인 작업은 [제품 관리](./manage-offers/cpp-manage-offers.md) 섹션에서 다루고 있습니다.


## <a name="test-drive"></a>시험 사용

*시험 사용*은 고객에게 설정된 각 제품에 대해 “구입 전 시험 사용” 데모 옵션을 제공하는 마켓플레이스 기능입니다.  테스트 드라이브 기능은 [Azure 응용 프로그램,](./azure-applications/cpp-azure-app-offer.md) [Dynamics 365 비즈니스 센트럴,](../cloud-partner-portal-orig/cpp-business-central-offer.md)고객 [참여를 위한 Dynamics 365,](./dyn365ce/cpp-customer-engagement-offer.md) [재무 및 운영을 위한 Dynamics 365,](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) [SaaS 응용 프로그램](./saas-app/cpp-saas-offer.md)및 가상 [컴퓨터의](./virtual-machine/cpp-virtual-machine-offer.md)다음 하위 집합으로 제한됩니다.  이 기능을 사용하려면 게시자가 해당 제품에 맞게 사용자 지정된 시험 사용 템플릿을 만들어야 합니다.  자세한 내용은 [시험 사용](./test-drive/what-is-test-drive.md) 섹션을 참조하세요.

[시험 사용 필터](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive)를 적용하여 시험 사용 데모가 있는 기존 마켓플레이스 제품을 찾아볼 수 있습니다. 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure Marketplace 및 AppSource 제품 유형

다음 표에는 [Cloud 파트너 포털](https://cloudpartner.azure.com)에서 지원되는 현재 제품 유형이 나와 있습니다.  각 제품 유형에 대해 제품을 목록에 추가할 수 있는 마켓플레이스와 제품 솔루션 기술에 대한 일반적인 설명이 나열됩니다.

|                제품 유형                |  Marketplace  |   설명                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Azure 애플리케이션](./azure-applications/cpp-azure-app-offer.md) | Azure | 솔루션이 Azure Resource Manger 템플릿을 통해 배포된 하나 이상의 VM(가상 머신) 및 선택적 사용자 지정 Azure 코드로 구성됩니다.  배포는 고객이 솔루션 템플릿을 통해 수행하거나 게시자가 관리할 수 있습니다. 이 유형은 제공된 가상 머신 제품 유형보다 많은 유연성을 제공하는 데 사용됩니다.  |
| [컨설팅 서비스](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | 둘 다 | Microsoft 인증 컨설턴트는 Azure Marketplace 또는 AppSource의 목록에 도메인 특정 서비스를 추가할 수 있습니다.  컨설턴트의 전문 지식은 고객이 문제를 평가하고 비즈니스 목표 달성을 위한 올바른 솔루션을 만들고 배포하는 데 도움이 됩니다.  |
| [컨테이너](./containers/cpp-containers-offer.md)  | Azure | 솔루션이 Kubernetes 기반 서비스 또는 Azure Container Instances로 프로비전된 Docker 컨테이너 이미지입니다. |
| [다이내믹스 365 비즈니스 센트럴](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | 이 ERP(전사적 자원 관리) 및 비즈니스 관리 시스템을 확장하는 패키지입니다. |
| [고객 참여를 위한 Dynamics 365](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | 영업, 서비스, 프로젝트 서비스 및 현장 서비스 모듈을 통해 CRM(고객 리소스 관리) 시스템을 확장하는 패키지  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | 고급 재무, 운영, 제조 및 공급망 관리를 지원하는 ERP(전사적 자원 관리) 서비스를 확장하는 패키지 |
| [IoT Edge 모듈](./iot-edge-module/cpp-offer-process-parts.md) | Azure | IoT Edge 디바이스에서 실행되는 Docker 호환 컨테이너입니다.  사용자 지정 코드, 다른 Azure 서비스 및 타사 서비스의 조합을 사용하는 소규모 계산 모듈로 구성됩니다. |
| [Power BI 앱](./power-bi/cpp-power-bi-offer.md) | AppSource | 데이터 집합, 보고서 및 대시보드를 포함하여 사용자 지정 가능한 Power BI 콘텐츠를 패키지화하는 Power BI 앱 |
| [SaaS 앱](./saas-app/cpp-saas-offer.md) | Azure | 솔루션은 사용자가 Azure Active Directory를 사용하는 사용자 지정된 인터페이스를 통해 로그인하는 게시자가 관리하는 서비스형 소프트웨어 구독입니다. |
| [가상 머신](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | 솔루션이 고객의 구독에 배포된 단일 가상 머신 내에 포함됩니다.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

자세한 내용은 [제품 유형별 게시 가이드](../publisher-guide-by-offer-type.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

오퍼 [관리](./manage-offers/cpp-manage-offers.md)문서에서 마켓플레이스 오퍼에서 수행할 수 있는 일반적인 작업과 일반적인 기술적 특성 및 자산에 대해 알아봅니다.
