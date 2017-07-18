---
title: "Azure Managed Application 개요 | Microsoft Docs"
description: "Azure Managed Application에 대한 개념을 설명합니다."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/24/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: c4e0508e243bd4f0cd57ea1619f7812433329423
ms.contentlocale: ko-kr
ms.lasthandoff: 05/26/2017


---
# <a name="azure-managed-applications-overview"></a>Azure Managed Applications 개요

오늘날 Azure에서는 ISV 및 신생업체가 전 세계 고객에게 솔루션을 제공할 수 있는 강력한 Marketplace를 제공합니다. Azure Marketplace는 자사 및 타사 공급업체에서 제공하는 수백 가지의 복잡한 다중 리소스 템플릿으로 구성된 갤러리입니다. 고객은 수 분 내에 PaaS 및 SaaS 응용 프로그램을 배포하고 시작할 수 있습니다. 제품을 빠르게 배포할 수 있는 훌륭한 방법을 제공하지만 여전히 고객이 솔루션을 유지 관리하고 업데이트해야 합니다. 공급업체의 경우 응용 프로그램 사용에 대해 고객에게 가상 컴퓨터 이미지 청구 이외의 요금을 부과할 수 없습니다. 또한 공급업체는 고객이 중요한 응용 프로그램 리소스를 수정하지 못하도록 방지할 수 없으며, 응용 프로그램을 구성하는 지적 재산에 대한 액세스를 차단할 수 없습니다. Azure Managed Applications는 이러한 문제를 해결할 수 있는 솔루션을 제공합니다. 

## <a name="advantages-of-managed-applications"></a>관리되는 응용 프로그램의 이점

Azure Managed Applications는 공급업체에서 자체 포함된 응용 프로그램으로 PaaS 또는 SaaS 서비스를 사용할 수 있게 하는 에코시스템을 제공합니다. 고객은 자신의 구독에서 관리되는 응용 프로그램을 배포하지만, 공급업체는 이를 관리할 수 있습니다. 공급업체는 Azure의 청구 시스템을 사용하여 고객에게 청구할 수 있으며, 템플릿을 사용하여 배포된 응용 프로그램의 수명 주기를 관리할 수 있습니다. 다른 측면에서 고객은 업데이트를 자동으로 획득하고, 지원 및 유지 관리에 대한 비용을 지불할 수 있습니다. 응용 프로그램을 유지 관리하거나 업데이트하거나 응용 프로그램에 오류가 발생하면 문제를 진단하고 수정할 필요가 없습니다.

이러한 Azure 에코시스템은 PaaS 및 SaaS 공급업체뿐만 아니라 솔루션을 패키지하고 전매하려는 회사의 중앙 플랫폼 팀 및 시스템 통합업체에게도 이득이 됩니다.

## <a name="how-managed-applications-work"></a>관리되는 응용 프로그램의 작동 방법
관리되는 응용 프로그램을 사용할 때는 다음 두 가지 환경이 있습니다.

1. 관리되는 응용 프로그램을 만들고 광범위하게 사용할 수 있게 하는 공급업체 또는 ISV(독립적 소프트웨어 공급업체) 
2. 게시된 응용 프로그램을 만들고 사용하려는 고객 또는 소비자 

이 문서에서는 두 가지 환경에 대한 개요를 제공합니다. 먼저 관리되는 응용 프로그램이 작동하는 방식을 살펴보겠습니다. 

관리되는 응용 프로그램은 하나의 주요 차이점이 있지만 Marketplace 솔루션 템플릿과 유사합니다. 관리되는 응용 프로그램에서 리소스는 ISV/공급업체에서 관리하는 리소스 그룹으로 프로비전됩니다. 리소스 그룹은 고객의 구독에 있지만, ISV의 테넌트에 있는 사용자, 사용자 그룹 또는 응용 프로그램은 해당 리소스 그룹에 액세스할 수 있습니다. 응용 프로그램을 관리하고 서비스를 제공하기 위해 공급업체의 ID가 Active Directory 소유자, 참가자, 읽기 권한자 또는 다른 모든 기본 제공 역할에 추가됩니다. 

공급업체 환경에 대한 자세한 내용은 [Azure Managed Application 만들기 및 게시](managed-application-publishing.md)를 참조하세요.

소비자 환경에 대한 자세한 내용은 [Azure Managed Application 사용](managed-application-consumption.md)을 참조하세요.

## <a name="key-concepts"></a>주요 개념

### <a name="managed-resource-group"></a>관리되는 리소스 그룹
템플릿에서 프로비전되는 모든 Azure 리소스를 만드는 리소스 그룹입니다. 예를 들어 어플라이언스에서 저장소 계정을 만드는 경우 이 리소스 그룹에는 저장소 계정 리소스가 포함됩니다. 어플라이언스 리소스는 포함되지 않습니다.

### <a name="appliance-package"></a>어플라이언스 패키지
게시자는 템플릿 파일과 createUIDefinition 파일이 포함된 패키지를 만듭니다. 포함되는 파일은 구체적으로 다음과 같습니다.

- **applianceMainTemplate.json** - 어플라이언스에서 프로비전하는 모든 리소스를 정의하는 템플릿 파일입니다. 이 파일은 리소스를 만드는 데 사용되는 일반 템플릿 파일입니다.

- **MainTemplate.json** - 어플라이언스 리소스를 정의하는 템플릿 파일입니다(Microsoft.Solutions/appliances). 이 리소스에 정의되는 하나의 주요 속성은 ManagedResourceGroupId입니다. 이 속성은 applianceMainTemplate.json에 정의된 실제 리소스를 호스팅하는 데 사용되는 리소스 그룹을 나타냅니다.

- **createUIDefinition.json** - 이 파일은 템플릿에 정의된 매개 변수에 필요한 UI가 렌더링되는 방법을 설명합니다.

### <a name="authorization"></a>권한 부여
게시자는 공급업체에서 고객을 대신하여 리소스를 관리하는 데 필요한 권한을 지정해야 합니다. 이 권한은 관리되는 리소스 그룹에 적용됩니다. 다음 값을 설정합니다.

- **PrincipalID** - 관리되는 리소스 그룹에 대한 액세스 권한을 부여하는 데 사용되는 사용자, 그룹 또는 응용 프로그램의 Azure AD 식별자입니다. 이 식별자는 게시자의 테넌트에 속합니다.

- **RoleDefinitionID** - 앞서의 보안 주체 ID에 할당된 역할의 Azure AD 식별자입니다. 게시자의 테넌트에 기본 제공된 RBAC 역할 중 하나일 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 공급업체 환경을 이해하려면 [Azure Managed Application 만들기 및 게시](managed-application-publishing.md)를 참조하세요.
* 소비자 환경을 이해하려면 [Azure Managed Application 사용](managed-application-consumption.md)을 참조하세요.
* UI 정의 파일을 만들려면 [CreateUiDefinition 시작](managed-application-createuidefinition-overview.md)을 참조하세요.
