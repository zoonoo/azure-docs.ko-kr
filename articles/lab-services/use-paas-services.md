---
title: Azure DevTest 랩에서 서비스로서의 플랫폼(PaaS) 서비스 사용
description: Azure DevTest 랩에서 서비스로서의 플랫폼(Pass) 서비스를 사용하는 방법에 대해 알아봅니다.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88bbf921fedae4bcdba2b6386ce6e08105206cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169199"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Azure DevTest 랩에서 서비스로서의 플랫폼(PaaS) 서비스 사용
PaaS는 환경 기능을 통해 DevTest 랩에서 지원됩니다. DevTest 랩의 환경은 Git 리포지토리에서 미리 구성된 Azure 리소스 관리자 템플릿에서 지원됩니다. 환경에는 PaaS 및 IaaS 리소스가 모두 포함될 수 있습니다. 이를 통해 함께 작동하도록 사용자 지정된 가상 컴퓨터, 데이터베이스, 가상 네트워크 및 웹 앱과 같은 Azure 리소스를 포함할 수 있는 복잡한 시스템을 만들 수 있습니다. 이러한 템플릿을 사용하면 소스 코드 제어를 사용하여 환경을 일관되게 배포하고 관리할 수 있습니다. 

제대로 설정된 시스템은 다음과 같은 시나리오를 허용합니다. 

- 독립적이고 다양한 환경을 갖도록 개발자
- 비동기적으로 다른 구성에 대한 테스트
- 템플릿 변경 없이 스테이징 및 프로덕션 파이프라인에 통합
- 동일한 랩 내에 개발 컴퓨터와 환경을 모두 보유함으로써 관리 및 비용 보고의 용이성을 향상시킵니다.  

DevTest Labs 리소스 공급자는 랩 사용자를 대신하여 리소스를 생성하므로 PaaS 리소스를 사용하도록 랩 사용자에게 추가 권한을 부여할 필요가 없습니다. 다음 이미지는 서비스 패브릭 클러스터를 랩의 환경으로 보여 주습니다.

![환경으로 패브릭 클러스터 서비스](./media/create-environment-service-fabric-cluster/cluster-created.png)

환경 설정에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 다중 VM 환경 및 PaaS 리소스 만들기를](devtest-lab-create-environment-from-arm.md)참조하십시오. DevTest Labs에는 외부 GitHub 원본에 직접 연결하지 않고도 환경을 만드는 데 사용할 수 있는 Azure 리소스 관리자 템플릿의 공용 리포지토리가 있습니다. 공용 환경에 대한 자세한 내용은 [Azure DevTest Labs의 공용 환경 구성 및 사용을](devtest-lab-configure-use-public-environments.md)참조하십시오.

대규모 조직에서 개발 팀은 일반적으로 사용자 지정/격리된 테스트 환경과 같은 환경을 제공합니다. 사업부 또는 부서 내의 모든 팀에서 사용해야 하는 환경이 있을 수 있습니다. IT 그룹은 조직의 모든 팀에서 사용할 수 있는 환경을 제공할 수 있습니다.  

## <a name="customizations"></a>사용자 지정

#### <a name="sandbox"></a>샌드박스 
랩 소유자는 랩 환경을 사용자 지정하여 리소스 그룹 내의 **판독기에서** **참여자로** 사용자의 역할을 변경할 수 있습니다. 이 기능은 **랩의** **구성 및 정책** 아래의 랩 설정 페이지에 있습니다. 이러한 역할 변경을 통해 사용자는 해당 환경 내에서 리소스를 추가하거나 제거할 수 있습니다. 액세스를 더 제한하려면 Azure 정책을 사용합니다. 이 기능을 사용하면 구독 수준에서 액세스하지 않고 리소스 또는 구성을 사용자 지정할 수 있습니다.

#### <a name="custom-tokens"></a>사용자 지정 토큰
리소스 그룹 외부에 있고 템플릿이 액세스할 수 있는 환경에만 적용되는 몇 가지 사용자 지정 랩 정보가 있습니다. 다음은 그 중 일부입니다. 

- 실험실 네트워크 식별
- 위치
- 리소스 관리자 템플릿 파일이 저장되는 저장소 계정입니다. 
 
#### <a name="lab-virtual-network"></a>랩 가상 네트워크
[환경을 랩의 가상 네트워크](connect-environment-lab-virtual-network.md) 문서에 연결하려면 `$(LabSubnetId)` 리소스 관리자 템플릿을 수정하여 토큰을 사용하는 방법을 설명합니다. 환경이 생성되면 `$(LabSubnetId)` 토큰은 가상 시스템 만들기 옵션에서 **사용이** **true로**설정된 첫 번째 서브넷 마크로 대체됩니다. 그것은 우리의 환경이 이전에 만든 네트워크를 사용할 수 있습니다. 테스트 환경에서 스테이징 및 프로덕션과 동일한 리소스 관리자 템플릿을 `$(LabSubnetId)` 사용하려면 리소스 관리자 템플릿 매개 변수에서 기본값으로 사용합니다. 

#### <a name="environment-storage-account"></a>환경 저장소 계정
DevTest 연구소는 [중첩된 리소스 관리자 템플릿의](../azure-resource-manager/templates/linked-templates.md)사용을 지원합니다. [[테스트 환경용 중첩된 Azure Resource Manager](deploy-nested-template-environments.md) 템플릿 `_artifactsLocation` `_artifactsLocationSasToken` 배포 문서에서는 기본 템플릿의 중첩 된 폴더와 동일한 폴더또는 중첩 된 폴더에 리소스 관리자 템플릿에 URI를 만드는 방법 및 토큰을 사용 하는 방법을 설명 합니다. 이러한 두 토큰에 대한 자세한 내용은 Azure 리소스 관리자의 **배포 아티팩트** 섹션 [- 모범 사례 가이드를](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)참조하십시오.

## <a name="user-experience"></a>설치 환경

## <a name="developer"></a>Developer
개발자는 VM을 만드는 데 동일한 워크플로를 사용하여 특정 환경을 만듭니다. 컴퓨터 이미지와 환경을 선택하고 템플릿에 필요한 정보를 입력합니다. 환경을 갖는 각 개발자는 변경 내용의 배포 및 향상된 내부 루프 디버깅을 허용합니다. 환경은 언제든지 최신 템플릿을 사용하여 만들 수 있습니다.  이 기능을 사용하면 환경을 삭제하고 다시 만들 수 있으므로 시스템을 수동으로 만들거나 오류 테스트에서 복구할 필요가 없는 가동 중지 시간을 줄일 수 있습니다.  

### <a name="testing"></a>테스트
DevTest Labs 환경을 사용하면 특정 코드 및 구성을 비동기적으로 독립적으로 테스트할 수 있습니다. 일반적인 방법은 개별 끌어오기 요청의 코드로 환경을 설정하고 자동화된 테스트를 시작하는 것입니다. 자동화된 테스트가 완료되면 특정 환경에 대해 모든 수동 테스트를 실행할 수 있습니다. 일반적으로 이 프로세스는 CI/CD 파이프라인의 일부로 수행됩니다. 

## <a name="management-experience"></a>관리 경험

### <a name="cost-tracking"></a>비용 추적
비용 추적 기능에는 전체 비용 추세의 일부로 서로 다른 환경 내의 Azure 리소스가 포함됩니다. 리소스별 비용은 환경 내의 다른 리소스를 나누지 않고 환경을 단일 비용으로 표시합니다.

### <a name="security"></a>보안
DevTest Labs를 사용하여 적절하게 구성된 Azure 구독은 [랩을 통해서만 Azure 리소스에 대한 액세스를 제한할](devtest-lab-add-devtest-user.md)수 있습니다. 환경을 사용하면 랩 소유자는 사용자가 다른 Azure 리소스에 대한 액세스를 허용하지 않고 승인된 구성으로 PaaS 리소스에 액세스할 수 있도록 허용할 수 있습니다. 랩 사용자가 환경을 사용자 지정하는 시나리오에서는 랩 소유자가 참여자 액세스를 허용할 수 있습니다. 기여자 액세스를 사용하면 랩 사용자가 관리되는 리소스 그룹 내에서만 Azure 리소스를 추가하거나 제거할 수 있습니다. 이를 통해 사용자 기여자가 구독에 쉽게 액세스할 수 있는 것과 비교하여 더 쉽게 추적 및 관리가 가능합니다.

### <a name="automation"></a>Automation
자동화는 대규모의 효과적인 생태계를 위한 핵심 구성 요소입니다. 자동화는 구독 및 랩 전반에서 여러 환경을 관리하거나 추적하는 데 필요합니다.

### <a name="cicd-pipeline"></a>CI/CD 파이프라인
DevTest Labs의 PaaS 서비스는 랩에서 액세스가 제어되는 집중 배포를 통해 CI/CD 파이프라인을 개선하는 데 도움이 될 수 있습니다.

## <a name="next-steps"></a>다음 단계
환경에 대한 자세한 내용은 다음 문서를 참조하십시오. 

- 
- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
- [Azure DevTest 랩에서 독립적인 서비스 패브릭 클러스터를 사용하여 환경 만들기](create-environment-service-fabric-cluster.md)
- [Azure DevTest 랩에서 환경을 랩의 가상 네트워크에 연결합니다.](connect-environment-lab-virtual-network.md)
- [Azure DevOps CI/CD 파이프라인에 환경 통합](integrate-environments-devops-pipeline.md)
 





