---
title: 플랫폼-as a Service (PaaS) 서비스를 사용 하 여 Azure DevTest Labs에서 | Microsoft Docs
description: Azure DevTest Labs에서 플랫폼-as a Service (통과) 서비스를 사용 하는 방법에 알아봅니다.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 7cdd185cddbd2403b72ff0e06530913af0b031de
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233131"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>플랫폼-as a Service (PaaS) 서비스를 사용 하 여 Azure DevTest Labs에서
PaaS 환경 기능을 통해 DevTest Labs에서 지원 됩니다. DevTest Labs에서 환경은 Git 리포지토리에서 미리 구성 된 Azure Resource Manager 템플릿에서 지원 됩니다. 환경에는 PaaS 및 IaaS 리소스를 모두 포함할 수 있습니다. 그러면 가상 머신, 데이터베이스, 가상 네트워크와 같은 Azure 리소스를 포함할 수 있는 복잡 한 시스템 및 공동 작업 사용자 지정 된 웹 앱을 만들 수 있습니다. 이러한 템플릿은 일관 된 배포 및 소스 코드 제어를 사용 하 여 환경의 향상 된 관리를 허용 합니다. 

시스템을 제대로 설정는 다음 시나리오에서 허용: 

- 개발자가 여러 환경과 관계 없이
- 비동기적으로 다른 구성에서 테스트
- 스테이징 및 프로덕션 파이프라인 템플릿 변경 하지 않고 통합
- 개발 컴퓨터와 같은 랩 내에서 환경 둘 다 있으면 손쉬운 관리 및 비용 보고 향상 됩니다.  

DevTest Labs 리소스 공급자는 PaaS 리소스를 사용할 수 있도록 랩 사용자에 게 제공 될 추가 권한이 필요 하므로 랩 사용자 대신 리소스를 만듭니다. 다음 이미지에서 랩 환경으로 Service Fabric 클러스터를 보여 줍니다.

![환경으로 Service Fabric 클러스터](./media/create-environment-service-fabric-cluster/cluster-created.png)

환경 설정에 대 한 자세한 내용은 참조 [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)합니다. DevTest Labs에 직접 외부 GitHub 원본에 연결 하지 않고도 환경을 만드는 데 사용할 수 있는 Azure Resource Manager 템플릿 공용 리포지토리가 있습니다. 공용 환경에 대 한 자세한 내용은 참조 하세요. [Azure DevTest Labs의 구성 및 사용 하 여 공용 환경](devtest-lab-configure-use-public-environments.md)합니다.

개발 팀은 대규모 조직에서는 일반적으로 테스트 환경 사용자 지정 하 고 격리와 같은 환경 제공합니다. 사업부 또는 부서 내에서 모든 팀에서 사용할 수 있는 환경에 있을 수 있습니다. IT 그룹은 조직에서 모든 팀에서 사용할 수 있는 해당 환경을 제공 하려고 합니다.  

## <a name="customizations"></a>사용자 지정

#### <a name="sandbox"></a>샌드박스 
랩 소유자는 랩 환경에서 사용자의 역할을 변경 하려면 사용자 지정할 수 있습니다 **판독기** 하 **참가자** 리소스 그룹 내에서. 이 기능은 합니다 **랩 설정** 페이지를 **구성 및 정책** 랩의 합니다. 역할에서이 변경 내용을 사용자를를 추가 하거나 해당 환경 내에서 리소스를 제거할 수 있습니다. 추가 액세스를 제한 하려는 경우에 Azure 정책을 사용 합니다. 이 기능을 사용 하면 리소스 또는 구독 수준에서 액세스를 사용 하지 않는 구성을 사용자 지정할 수 있습니다.

#### <a name="custom-tokens"></a>사용자 지정 토큰
리소스 그룹 외부와 템플릿에 액세스할 수 있는 환경에 관련 있는 몇 가지 사용자 지정 랩 정보가 있습니다. 일부는 다음과 같습니다. 

- 랩 네트워크 id입니다.
- Location
- Resource Manager 템플릿 파일이 저장 되는 저장소 계정입니다. 
 
#### <a name="lab-virtual-network"></a>랩 가상 네트워크
[랩의 가상 네트워크에 연결 하는 환경](connect-environment-lab-virtual-network.md) 문서에 사용 하 여 Resource Manager 템플릿을 수정 하는 방법에 설명 합니다 `$(LabSubnetId)` 토큰입니다. Environment를 만들면를 `$(LabSubnetId)` 토큰 첫 번째 서브넷 표시로 바뀝니다. 여기서는 **가상 머신에서 사용 하도록 만들** 옵션을 설정 **true**합니다. 이전에 네트워크를 만든 환경을 사용할 수 있습니다. 스테이징 및 프로덕션으로 테스트에서 환경에서 동일한 Resource Manager 템플릿을 사용 하려는 경우 사용 하 여 `$(LabSubnetId)` Resource Manager 템플릿 매개 변수의 기본값으로 합니다. 

#### <a name="environment-storage-account"></a>Storage 계정 환경
DevTest Labs 사용을 지원 [중첩 된 Resource Manager 템플릿](../azure-resource-manager/resource-group-linked-templates.md)합니다. 합니다 [어떻게 Azure DevTest Labs에서 중첩 된 Resource Manager 템플릿 배포 테스트 환경 쉽게](https://azure.microsoft.com/updates/azure-devtest-labs-streamlined-nested-arm-template-deployment-support-for-arm-template-based-environments) 문서를 사용 하는 방법에 설명 `_artifactsLocation` 고 `_artifactsLocationSasToken` 토큰에서 Resource Manager 템플릿에 URI를 만듭니다 주 템플릿의 중첩 된 폴더 또는으로 동일한 폴더입니다. 이러한 두 토큰에 대 한 자세한 내용은 참조는 **배포 아티팩트** 부분 [모범 사례 가이드-Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)합니다.

## <a name="user-experience"></a>사용자 환경

## <a name="developer"></a>Developer
개발자는 특정 환경을 만들려면 VM을 만들기 위한 동일한 워크플로 사용 합니다. 이러한 컴퓨터 이미지 및 환경을 선택 하 고 템플릿에 필요한 필요한 정보를 입력 합니다. 각 개발자 environment가 배포 변경 내용 및 향상 된 내부 루프 디버깅 허용 합니다. 최신 템플릿을 사용 하 여 언제 든 환경을 만들 수 있습니다.  이 기능을 사용 하면 환경을 제거 하 고 수동으로 시스템 작성 하거나 테스트 오류에서 복구 하는 것과 가동 중지 시간을 줄이는 데 다시 만들어야 합니다.  

### <a name="testing"></a>테스트하는 중
DevTest Labs 환경 독립적인 테스트 특정 코드 및 구성의 비동기적으로 허용 합니다. 가장 일반적인 방법은 개별 끌어오기 요청에서 코드를 사용 하 여 환경을 설정 하 고 자동화 된 테스트를 시작 하는 것입니다. 완료 될 때까지 자동화 된 테스트에 실행 되 면 모든 수동 테스트 실행할 수 있습니다 특정 환경에 대 한 합니다. 일반적으로이 프로세스는 CI/CD 파이프라인의 일부로 수행 됩니다. 

## <a name="management-experience"></a>관리 환경

### <a name="cost-tracking"></a>비용 추적
전체의 일부로 다양 한 환경 내에서 Azure 리소스를 포함 하는 비용 추적 기능에 대 한 비용 추세입니다. 리소스별 비용 환경 내에서 여러 리소스를 중단 하지 않습니다 하지만 단일 비용으로 환경을 표시 합니다.

### <a name="security"></a>보안
DevTest Labs를 사용 하 여 올바르게 구성 된 Azure 구독을 수 있습니다 [랩만을 통해 Azure 리소스에 대 한 액세스를 제한](devtest-lab-add-devtest-user.md)합니다. 환경에서 사용 하 여 랩 소유자는 사용자가 다른 Azure 리소스에 대 한 액세스를 허용 하지 않고 승인 된 구성 사용 하 여 PaaS 리소스에 액세스할 수 있습니다. 랩 사용자 환경을 사용자 지정할이 있는 시나리오에서 랩 소유자는 참가자 액세스를 허용할 수 있습니다. 참가자 액세스를 랩 사용자를를 추가 하거나 관리 되는 리소스 그룹 내 에서만 Azure 리소스를 제거할 수 있습니다. 훨씬 쉽게 추적할 수 있도록 하 고 관리 및 구독에 대 한 사용자 참여자 액세스를 허용 합니다.

### <a name="automation"></a>Automation
자동화는 대규모 효과적인 에코 시스템에 대 한 핵심 구성 요소입니다. Automation은 관리 또는 여러 환경 구독 및 실습에서 추적을 처리 하는 데 필요한입니다.

### <a name="cicd-pipeline"></a>CI/CD 파이프라인
PaaS 서비스 DevTest Labs에서 랩으로 액세스가 제어 됩니다 여기서 배포 초점을 맞춘 것에서 CI/CD 파이프라인을 개선할 수 있습니다.

## <a name="next-steps"></a>다음 단계
환경에 대 한 자세한 내용은 다음 문서를 참조 하세요. 

- 
- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [구성 및 Azure DevTest Labs에서 공용 환경 사용](devtest-lab-configure-use-public-environments.md)
- [Azure DevTest Labs에서 자체 포함 된 Service Fabric 클러스터를 사용 하 여 환경 만들기](create-environment-service-fabric-cluster.md)
- [Azure DevTest Labs에서 랩의 가상 네트워크에는 환경 연결](connect-environment-lab-virtual-network.md)
- [환경에 Azure DevOps CI/CD 파이프라인 통합](integrate-environments-devops-pipeline.md)
 





