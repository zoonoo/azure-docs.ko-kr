---
title: Azure 애플리케이션 제품 | Microsoft Docs
description: Azure 애플리케이션 제품을 Azure Marketplace에 게시하는 프로세스에 대한 개요입니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: pbutlerm
ms.openlocfilehash: 63b7ee4e0d9cb9d0d1f26119fe73573b124d04e8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195944"
---
# <a name="azure-application-offer"></a>Azure 애플리케이션 제품

이 섹션에서는 새 Azure 애플리케이션 제품을 <a href="https://azuremarketplace.microsoft.com">Microsoft Azure Marketplace</a>에 게시하는 방법을 설명합니다.
|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| 각 Azure 애플리케이션에는 일반적으로 하나 이상의 가상 머신 및 기타 지원 Azure 기반 서비스 또는 웹 기반 서비스를 포함하는 애플리케이션에서 사용하는 모든 기술 자산을 정의하는 Azure Resource Manager 템플릿이 포함됩니다. | ![Azure 앱 아이콘](./media/azureapp-icon1.png)  |

## <a name="benefits"></a>이점

Microsoft Marketplace에 애플리케이션을 나열하는 이점 중 일부는 다음과 같습니다.

* Office 365 및 Dynamics 365에서 1억 명의 Azure Active Directory 사용자를 연결합니다.

* 영업 팀 확장: 전 세계의 비즈니스 사용자를 연결하여 최종 사용자의 참여를 이끌어내고, 잠재 고객을 창출하는 데 도움이 되고, 업계의 새로운 고객과 대화를 시작하는 영업 채널을 확보합니다.

* 실행 가능한 인사이트 획득: AppSource에서 앱이 수행되는 방법, 효율적으로 작동하는 기능 및 영업 절차를 더욱 향상시키는 방법에 대한 인사이트를 공유합니다.

## <a name="types-of-azure-applications"></a>Azure 애플리케이션 유형

Azure 애플리케이션에는 관리 애플리케이션과 솔루션 템플릿의 두 가지 종류가 있습니다. 두 애플리케이션이 비슷하지만 몇 가지 주목할 차이점이 있습니다.

### <a name="solution-template"></a>솔루션 템플릿

솔루션 템플릿은 Marketplace에서 솔루션을 게시하는 주요 방법 중 하나입니다. 이 제품 유형은 솔루션에 단일 VM(가상 머신) 이외의 추가 배포 및 구성 자동화가 필요할 때 사용됩니다. 솔루션 템플릿을 사용하면 둘 이상의 VM 제공을 자동화할 수 있습니다. 여기에는 복잡한 IaaS 솔루션을 제공하기 위한 네트워킹 및 스토리지 리소스 프로비저닝이 포함됩니다. 솔루션 템플릿 요구 사항 및 청구 모델의 개요를 보려면 [Azure 애플리케이션: 솔루션 템플릿](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates)을 참조하세요.

### <a name="managed-application"></a>관리되는 애플리케이션

관리형 애플리케이션은 한 가지 주요 차이점이 있지만 Marketplace의 솔루션 템플릿과 유사합니다. 관리되는 애플리케이션에서 리소스는 앱 게시자가 관리하는 리소스 그룹에 배포됩니다. 리소스 그룹은 소비자의 구독에 있지만, 게시자 테넌트의 ID는 해당 리소스 그룹에 액세스할 수 있습니다. 게시자는 지속적인 솔루션 지원을 위한 비용을 지정합니다. Azure Managed applications를 사용하여 완전 관리형 턴키 애플리케이션을 손쉽게 빌드하고 고객에게 제공합니다.

Marketplace 외에, 서비스 카탈로그에 관리되는 애플리케이션을 제공할 수 있습니다. 서비스 카탈로그는 조직 내 사용자에 대해 승인된 솔루션의 내부 카탈로그입니다. 조직에 그룹을 위한 솔루션을 제공하면서, 카탈로그를 사용하여 조직의 표준을 준수합니다. 직원은 카탈로그를 사용하여 IT 부서에서 권장하고 승인한 애플리케이션을 손쉽게 찾습니다.

관리되는 애플리케이션의 장점 및 유형에 대한 자세한 내용은 [Azure Managed Applications 개요](https://docs.microsoft.com/azure/managed-applications/overview)를 참조하세요.

## <a name="publishing-overview"></a>게시 개요

[솔루션 템플릿 및 Azure Marketplace에 대한 관리되는 애플리케이션 빌드](https://channel9.msdn.com/Events/Build/2018/BRK3603) 비디오는 Azure Resource Manager 템플릿을 작성하여 Azure 애플리케이션 솔루션을 정의하는 방법 및 이후에 앱 제품을 Azure Marketplace에 게시하는 방법을 간략하게 보여줍니다.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]

## <a name="publishing-process-workflow"></a>게시 프로세스 워크플로

다음 다이어그램에서는 Azure 애플리케이션 제안을 게시하는 대략적인 프로세스를 보여 줍니다.

![제품 게시 워크플로](./media/new-offer-process.png)

## <a name="offer-components"></a>제안 구성 요소

이 섹션에서는 관리되는 애플리케이션 제품을 게시하는 요소에 대해 간략히 설명하고, Azure Marketplace의 게시자를 위한 지침으로 사용됩니다. 게시는 다음과 같은 주요 부분으로 나뉩니다. 

* [필수 조건](./cpp-prerequisites.md) - 관리되는 애플리케이션 제품을 만들거나 게시하기 전에 기술 및 비즈니스 요구 사항을 나열합니다. 
* [제품 만들기](./cpp-create-offer.md) - Cloud 파트너 포털을 사용하여 관리되는 애플리케이션 제품 항목을 만드는 데 필요한 단계를 나열합니다. 
* [제품 게시](./cpp-publish-offer.md) - Azure Marketplace에 게시할 제품을 제출하는 방법을 설명합니다.

## <a name="steps-in-the-publishing-process"></a>게시 프로세스의 단계

Azure 애플리케이션 제품을 게시하기 위한 대략적인 단계는 다음과 같습니다.

1. 제안 만들기 - 제안에 대한 자세한 정보를 제공합니다. 이 정보는 제안 설명, 마케팅 자료, 지원 정보 및 자산 사양을 포함합니다.

2. 비즈니스 및 기술 자산 만들기 - 관련 솔루션에 대한 비즈니스 자산(법률 문서 및 마케팅 자료) 및 기술 자산을 만듭니다.

3. SKU 만들기 - 제안과 연결된 SKU를 만듭니다. 게시하려는 각 이미지마다 고유한 SKU가 필요합니다.

4. 제안 인증 및 게시 - 제안 및 기술 자산이 완성되면 해당 제안을 제출할 수 있습니다. 이 제출은 게시 프로세스를 시작합니다. 이 프로세스 중 솔루션을 테스트하고, 유효성을 검사한 다음, Azure Marketplace에서 "라이브 상태가 됩니다".

## <a name="next-steps"></a>다음 단계

이러한 단계를 고려하기 전에 관리되는 애플리케이션을 Microsoft Azure Marketplace에 게시하기 위한 [기술 및 비즈니스 요구 사항](./cpp-prerequisites.md)이 충족되어야 합니다.