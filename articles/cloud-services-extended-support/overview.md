---
title: Azure Cloud Services 정보 (확장 지원)
description: Virtual Network 및 DNS 값을 지정 하는 서비스 구성 파일의 네트워크 구성 요소에 대 한 자식 요소에 대해 알아봅니다.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 8ed21d8689bf5340c1bde0a7f782bb8614f7cf11
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700204"
---
# <a name="about-azure-cloud-services-extended-support"></a>Azure Cloud Services 정보 (확장 지원)

> [!IMPORTANT]
> Cloud Services(추가 지원)는 현재 공개 미리 보기에 있습니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Cloud Services (확장 지원)은 [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) 제품에 대 한 새로운 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 기반 배포 모델로, 현재 공개 미리 보기로 제공 됩니다. Cloud Services (확장 지원)에는 Azure Service Manager를 사용 하 여 배포 된 Azure Cloud Services 기능 패리티와 함께 지역 복원 력을 제공 하는 주요 혜택이 있습니다. 또한 RBAC (역할 기반 액세스 및 제어), 태그, 정책 등의 ARM 기능을 제공 하 고 배포 템플릿을 지원 합니다.  

이와 같이 변경 하면 Cloud Services에 대 한 Azure Service Manager 기반 배포 모델이 [Cloud Services (클래식)](../cloud-services/cloud-services-choose-me.md)로 바뀝니다. 웹 및 클라우드 응용 프로그램 및 서비스를 빌드하고 신속 하 게 배포 하는 기능을 유지 합니다. 현재 수요를 기준으로 클라우드 서비스 인프라를 확장할 수 있으며, 응용 프로그램의 성능이 동시에 비용을 절감할 수 있습니다.  

## <a name="what-does-not-change"></a>변경 되지 않는 사항 
- 코드를 만들고, 구성을 정의 하 고, Azure에 배포 합니다. Azure에서 계산 환경을 설정 하 고 코드를 실행 한 다음 모니터링 하 고 유지 관리 합니다.
- Cloud Services (확장 지원)는 [웹 및 작업자](../cloud-services/cloud-services-choose-me.md)라는 두 가지 유형의 역할도 지원 합니다. 웹 및 작업자 역할의 디자인, 아키텍처 또는 구성 요소에는 변경 내용이 없습니다. 
- 클라우드 서비스의 세 가지 구성 요소, 서비스 정의 (.csdef), 서비스 구성 (.cscfg) 및 서비스 패키지 (.cspkg)는 앞으로 전달 되며 해당 [형식](cloud-services-model-and-package.md)에 변경 내용이 없습니다. 
- 데이터 평면이 동일 하 고 제어 평면이 변경 되기 때문에 런타임 코드를 변경 하지 않아도 됩니다. 
- Azure GuestOS 릴리스 및 관련 업데이트는 Cloud Services (클래식)에 정렬 됩니다.
- 업데이트 도메인과 관련 된 기본 업데이트 프로세스, 업그레이드 진행 방법, 롤백 및 허용 되는 서비스 변경 내용 변경 안 함

## <a name="changes-in-deployment-model"></a>배포 모델의 변경 내용

서비스 구성 (.cscfg) 및 서비스 정의 파일 (.csdef)을 배포 하는 데 필요한 최소한의 변경은 Cloud Services (확장 지원)를 수행 해야 합니다. 런타임 코드를 변경할 필요가 없습니다. 그러나 새 Azure Resource Manager 기반 Api를 호출 하려면 배포 스크립트를 업데이트 해야 합니다. 

:::image type="content" source="media/overview-image-1.png" alt-text="이미지는 템플릿 섹션이 추가 된 클래식 클라우드 서비스 구성을 보여 줍니다. ":::

배포와 관련 하 여 Cloud Services (클래식)과 Cloud Services (확장 지원) 간의 주요 차이점은 다음과 같습니다. 

- Azure Resource Manager 배포는 프로젝트에 대 한 인프라 및 구성을 정의 하는 JavaScript Object Notation (JSON) 파일인 [ARM 템플릿을](../azure-resource-manager/templates/overview.md) 사용 합니다. 이 템플릿은 대상을 만들기 위한 프로그래밍 명령 시퀀스를 작성하지 않고도 배포하려는 대상을 설명할 수 있는 선언적 구문입니다. Cloud Services (확장 지원)을 배포 하는 동안 서비스 구성 및 서비스 정의 파일은 [ARM 템플릿과](../azure-resource-manager/templates/overview.md) 일치 해야 합니다. [ARM 템플릿을 수동으로](deploy-template.md) 만들거나 [PowerShell](deploy-powershell.md), [포털](deploy-portal.md) 및 [Visual Studio](deploy-visual-studio.md)를 사용 하 여이를 달성할 수 있습니다.  

- 고객은 [Azure Key Vault](../key-vault/general/overview.md) 를 사용 하 여 [Cloud Services (확장 지원)에서 인증서를 관리](certificates-and-key-vault.md)해야 합니다. Azure Key Vault를 사용 하면 중앙 및 보안 클라우드 리포지토리에서 비밀, 키 및 인증서와 같은 응용 프로그램 자격 증명을 안전 하 게 저장 하 고 관리할 수 있습니다. 응용 프로그램은 런타임에 Key Vault에 인증 하 여 자격 증명을 검색할 수 있습니다. 

- [Azure Resource Manager](../azure-resource-manager/templates/overview.md) 를 통해 배포 된 모든 리소스는 가상 네트워크 내에 있어야 합니다. 가상 네트워크 및 서브넷은 기존 Azure Resource Manager Api를 사용 하 여 Azure Resource Manager에서 만들어지며, Cloud Services를 배포할 때 .cscfg의 NetworkConfiguration 섹션 (확장 지원)에서 참조 해야 합니다.   

- 각 클라우드 서비스 (확장 지원)는 단일 독립적인 배포입니다. 클라우드 서비스 (연장 지원)는 단일 클라우드 서비스 내의 여러 슬롯을 지원 하지 않습니다.  
    - VIP 교환 <sup>*</sup> 기능을 사용 하 여 두 클라우드 서비스 (확장 지원) 간을 교환할 수 있습니다. 클라우드 서비스의 새 릴리스를 테스트 하 고 준비 하려면 클라우드 서비스 (확장 된 지원)를 배포 하 고 다른 클라우드 서비스 (확장 지원)를 사용 하 여 VIP로 스왑로 태그를 추가 합니다.  

- 클라우드 서비스에 대 한 DNS (Domain Name Service) 레이블은 선택 사항입니다 (확장 지원). Azure Resource Manager에서 DNS 레이블은 클라우드 서비스와 연결 된 공용 IP 리소스의 속성입니다. 


<sup>*</sup> 공개 미리 보기 동안에는 Cloud Services에 대 한 VIP 교환 (확장 지원)을 사용할 수 없습니다.  

## <a name="migration-to-azure-resource-manager"></a>Azure Resource Manager로 마이그레이션

Cloud Services (확장 지원)에서는 [Azure Service Manager](/powershell/azure/servicemanagement/overview?preserve-view=true&view=azuresmps-4.0.0) 에서 [Azure Resource Manager](../azure-resource-manager/management/overview.md)로 마이그레이션할 수 있는 두 가지 경로를 제공 합니다. 
1) 고객은 Azure Resource Manager에서 직접 클라우드 서비스를 배포한 후 Azure Service Manager에서 기존 클라우드 서비스를 삭제 합니다. 
2) 내부 마이그레이션은 Cloud Services (연장 된 지원)의 가동 중지 시간을 최소화 하면서 Cloud Services (클래식)를 마이그레이션할 수 있는 기능을 지원 합니다. 

### <a name="additional-migration-options"></a>추가 마이그레이션 옵션

Cloud Services (클래식)에서 Cloud Services (연장 지원)로 마이그레이션 계획을 평가할 때 [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md), [App Service](../app-service/overview.md), [azure Kubernetes Service](../aks/intro-kubernetes.md)및 [azure Service Fabric](../service-fabric/service-fabric-overview.md)등의 추가 azure 서비스를 조사할 수 있습니다. 이러한 서비스는 추가 기능을 계속 제공 하지만 Cloud Services (확장 지원)는 주로 Cloud Services (클래식)를 사용 하 여 기능 패리티를 유지 관리 합니다. 

응용 프로그램에 따라 Cloud Services (확장 된 지원)을 통해 다른 옵션과 비교 하 여 Azure Resource Manager으로 이동 하는 데 상당한 노력이 소요 될 수 있습니다. 응용 프로그램이 진화 하지 않는 경우 빠른 마이그레이션 경로를 제공 하므로 Cloud Services (확장 지원)를 고려 하는 것이 좋습니다. 반대로 응용 프로그램을 지속적으로 진화 하 고 최신 기능 집합을 필요로 하는 경우에는 다른 Azure 서비스를 탐색 하 여 현재 및 미래의 요구 사항을 더 효과적으로 해결 합니다. 

## <a name="next-steps"></a>다음 단계
- Cloud Services (확장 지원)에 대 한 [배포 필수 구성 요소](deploy-prerequisite.md) 를 검토 합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.md)을 검토합니다.