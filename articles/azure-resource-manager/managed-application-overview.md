---
title: "Azure 관리되는 응용 프로그램 개요 | Microsoft Docs"
description: "Azure 관리되는 응용 프로그램에 대한 개념을 설명합니다."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 51732474e7983827988f950d344d36745564dfd2
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="azure-managed-applications-overview"></a>Azure 관리되는 응용 프로그램 개요

공급업체는 Azure를 통해 전 세계 고객에게 솔루션을 제공할 수 있습니다. Microsoft Azure Marketplace는 자사 및 타사 공급업체에서 제공하는 수백 가지의 복잡한 다중 리소스 템플릿으로 구성된 갤러리입니다. 고객은 수 분 내에 PaaS 및 SaaS 응용 프로그램을 배포하고 시작할 수 있습니다. 제품을 빠르게 배포할 수 있는 훌륭한 방법을 제공하지만 여전히 고객이 솔루션을 유지 관리하고 업데이트해야 합니다. 공급업체의 경우 응용 프로그램 사용에 대해 고객에게 가상 컴퓨터 이미지 청구 이외의 요금을 부과할 수 없습니다. 또한 공급업체는 고객이 중요한 응용 프로그램 리소스를 수정하지 못하도록 방지할 수 없으며, 응용 프로그램을 구성하는 지적 재산에 대한 액세스를 차단할 수 없습니다. Azure 관리되는 응용 프로그램은 이러한 문제를 해결할 수 있는 솔루션을 제공합니다. 

관리되는 응용 프로그램은 한 가지 주요 차이점이 있지만 Marketplace의 솔루션 템플릿과 유사합니다. 관리되는 응용 프로그램에서 리소스는 공급업체에서 관리하는 리소스 그룹으로 프로비전됩니다. 리소스 그룹은 고객의 구독에 있지만, 공급업체 테넌트의 ID는 해당 리소스 그룹에 액세스할 수 있습니다.

## <a name="advantages-of-managed-applications"></a>관리되는 응용 프로그램의 이점

MSP(관리 서비스 공급자), ISV(독립 소프트웨어 공급업체) 및 회사의 중앙 IT 팀은 관리되는 응용 프로그램으로 Marketplace 또는 서비스 카탈로그를 통해 솔루션을 제공할 수 있습니다. 고객은 자신의 구독에서 이러한 관리되는 응용 프로그램을 배포하지만 응용 프로그램을 유지 관리, 업데이트 또는 서비스하지 않아도 됩니다. 공급업체가 이러한 응용 프로그램을 관리 및 지원합니다. 따라서 고객은 이러한 응용 프로그램을 관리하기 위해 응용 프로그램에 관한 도메인 지식을 쌓을 필요가 없습니다. 그러므로 고객은 응용 프로그램 관련 문제 해결 및 문제 진단을 염려하지 않고 응용 프로그램 업데이트를 자동으로 얻을 수 있습니다.

공급업체 및 공급자의 경우 관리되는 응용 프로그램은 Marketplace를 통해 인프라 및 소프트웨어를 판매할 뿐만 아니라 Azure 고객에게 서비스 및 운영 지원을 연결하는 방법을 제공하는 채널을 만듭니다. 공급업체는 Azure의 청구 시스템을 사용하여 고객에게 청구할 수 있으며, 템플릿을 사용하여 배포된 응용 프로그램의 수명 주기를 관리할 수 있습니다. 이러한 솔루션은 자체 포함되어 고객에게 봉인되므로 공급업체는 고품질 서비스를 제공할 수 있습니다. 이러한 접근 방식은 PaaS 및 SaaS 공급업체뿐만 아니라 솔루션을 패키지하고 전매하려는 회사의 중앙 플랫폼 팀 및 시스템 통합업체에게도 이득이 됩니다.

## <a name="managed-application-types"></a>관리되는 응용 프로그램 종류
Azure 관리되는 응용 프로그램은 서비스 카탈로그와 Marketplace라는 두 가지 버전으로 제공됩니다.
 
### <a name="service-catalog"></a>서비스 카탈로그  

조직은 서비스 카탈로그를 통해 해당 조직의 사람들이 사용할 Azure에 대해 승인된 솔루션의 카탈로그를 만들 수 있습니다. 이러한 솔루션 카탈로그를 유지 관리하는 것은 기업의 중앙 IT 팀에 도움이 됩니다. 조직의 특정 표준을 준수하면서 조직에 뛰어난 솔루션을 제공할 수 있습니다. 이러한 응용 프로그램을 제어, 업데이트 및 유지 관리할 수 있습니다. 조직의 직원은 IT 부서에서 권장 및 승인한 풍부한 응용 프로그램 집합을 손쉽게 검색할 수 있습니다. 고객은 자신들이 만든 서비스 카탈로그 관리되는 응용 프로그램이나 조직의 다른 사람이 함께 공유하는 관리되는 응용 프로그램만 볼 수 있습니다.
 
서비스 카탈로그 관리되는 응용 프로그램을 게시하는 방법에 대한 자세한 내용은 [서비스 카탈로그 관리되는 응용 프로그램 만들기 및 게시](managed-application-publishing.md)를 참조하세요.
 
서비스 카탈로그 관리되는 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [서비스 카탈로그 관리되는 응용 프로그램 사용](managed-application-consumption.md)을 참조하세요.
 
### <a name="marketplace"></a>마켓플레이스

Azure Portal에서 Marketplace를 통해 사용할 수 있는 관리되는 응용 프로그램입니다. 공급업체에서 게시한 응용 프로그램은 사용할 조직 내부 또는 외부 모든 사람에게 제공됩니다. 이 접근 방법을 통해 MSP, ISV 및 SI(시스템 통합자)는 해당 솔루션을 모든 Azure 고객에게 제공할 수 있습니다. 고객은 솔루션을 이해하고 유지 관리하는 데 투자하지 않고도 이러한 복잡한 솔루션을 활용하는 이점을 얻게 됩니다. 현재는 게시자가 관리되는 응용 프로그램 또는 관리되지 않는 솔루션 템플릿으로 제품을 사용할 수 있도록 합니다. 관리되는 응용 프로그램을 게시하는 주요 구성 요소에는 프로비전된 리소스를 설명하는 템플릿 파일, 이러한 리소스를 프로비전하는 데 필요한 입력이 포털에 표시되는 방식을 설명하는 UI 정의 파일이 포함됩니다. 필요한 파일은 .zip 파일로 패키지화되고 게시 포털을 통해 업로드됩니다.
 
Marketplace에 관리되는 응용 프로그램을 게시하는 방법에 대한 자세한 내용은 [Marketplace의 Azure 관리되는 응용 프로그램](managed-application-author-marketplace.md)을 참조하세요.

Marketplace의 관리되는 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [Marketplace의 Azure 관리되는 응용 프로그램 사용](managed-application-consume-marketplace.md)을 참조하세요.

## <a name="key-concepts"></a>주요 개념

### <a name="managed-resource-group"></a>관리되는 리소스 그룹
템플릿에서 프로비전되는 모든 Azure 리소스를 만드는 리소스 그룹입니다. 예를 들어 어플라이언스에서 저장소 계정을 만드는 경우 이 리소스 그룹에는 저장소 계정 리소스가 포함됩니다. 어플라이언스 리소스는 포함되지 않습니다.

### <a name="appliance-package"></a>어플라이언스 패키지
게시자는 템플릿 파일과 createUIDefinition 파일이 포함된 패키지를 만듭니다. 포함되는 파일은 구체적으로 다음과 같습니다.

- **applianceMainTemplate.json** - 어플라이언스에서 프로비전하는 모든 리소스를 정의하는 템플릿 파일입니다. 이 파일은 리소스를 만드는 데 사용되는 일반 템플릿 파일입니다.

- **MainTemplate.json** - 어플라이언스 리소스를 정의하는 템플릿 파일입니다(Microsoft.Solutions/appliances). 이 리소스에 정의되는 하나의 주요 속성은 ManagedResourceGroupId입니다. 이 속성은 applianceMainTemplate.json에 정의된 실제 리소스를 호스팅하는 데 사용되는 리소스 그룹을 나타냅니다.

- **applianceCreateUIDefinition.json** - 이 파일은 템플릿에 정의된 매개 변수에 필요한 UI가 렌더링되는 방법을 설명합니다.

### <a name="authorization"></a>권한 부여
게시자는 공급업체에서 고객을 대신하여 리소스를 관리하는 데 필요한 권한을 지정해야 합니다. 이 권한은 관리되는 리소스 그룹에 적용됩니다. 다음 값을 설정합니다.

- **PrincipalID** - 관리되는 리소스 그룹에 대한 액세스 권한을 부여하는 데 사용되는 사용자, 그룹 또는 응용 프로그램의 Azure AD 식별자입니다. 이 식별자는 게시자의 테넌트에 속합니다.

- **RoleDefinitionID** - 앞서의 보안 주체 ID에 할당된 역할의 Azure AD 식별자입니다. 게시자의 테넌트에 기본 제공된 RBAC 역할 중 하나일 수 있습니다. 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* Marketplace에 관리되는 응용 프로그램을 게시하는 방법에 대한 자세한 내용은 [Marketplace의 Azure 관리되는 응용 프로그램](managed-application-author-marketplace.md)을 참조하세요.
* Marketplace의 관리되는 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [Marketplace의 Azure 관리되는 응용 프로그램 사용](managed-application-consume-marketplace.md)을 참조하세요.
* 서비스 카탈로그 관리되는 응용 프로그램을 게시하는 방법에 대한 자세한 내용은 [서비스 카탈로그 관리되는 응용 프로그램 만들기 및 게시](managed-application-publishing.md)를 참조하세요.
* 서비스 카탈로그 관리되는 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [서비스 카탈로그 관리되는 응용 프로그램 사용](managed-application-consumption.md)을 참조하세요.
* UI 정의 파일을 만들려면 [CreateUiDefinition 시작](managed-application-createuidefinition-overview.md)을 참조하세요.
