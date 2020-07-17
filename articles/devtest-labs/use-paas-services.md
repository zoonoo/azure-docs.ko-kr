---
title: Azure DevTest Labs에서 PaaS (Platform as a Service) 서비스 사용
description: Azure DevTest Labs에서 Platform as a Service (Pass) 서비스를 사용 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: eec37527386098174906dc2737d7b763241da3f2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85478742"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Azure DevTest Labs에서 PaaS (Platform as a Service) 서비스 사용
PaaS는 환경 기능을 통해 DevTest Labs에서 지원 됩니다. DevTest Labs의 환경은 Git 리포지토리에서 미리 구성 된 Azure Resource Manager 템플릿으로 지원 됩니다. 환경에는 PaaS 및 IaaS 리소스가 모두 포함 될 수 있습니다. 이를 통해 가상 머신, 데이터베이스, 가상 네트워크 및 웹 앱과 같은 Azure 리소스를 포함할 수 있는 복잡 한 시스템을 만들 수 있습니다 .이는 함께 작동 하도록 사용자 지정 됩니다. 이러한 템플릿을 사용 하면 소스 코드 제어를 사용 하 여 일관 된 배포 및 환경 관리를 수행할 수 있습니다. 

제대로 설정 된 시스템에서는 다음과 같은 시나리오를 수행할 수 있습니다. 

- 독립적인 환경 및 여러 환경을 포함 하는 개발자
- 비동기 방식으로 다른 구성에서 테스트
- 템플릿 변경 없이 스테이징 및 프로덕션 파이프라인에 통합
- 동일한 랩 내에서 개발 컴퓨터와 환경을 둘 다 유지 하면 관리 및 비용 보고의 용이성도 향상 됩니다.  

DevTest Labs 리소스 공급자는 랩 사용자를 대신 하 여 리소스를 만들기 때문에, PaaS 리소스를 사용할 수 있도록 랩 사용자에 게 추가 권한을 부여할 필요가 없습니다. 다음 이미지는 랩에서 환경으로 Service Fabric 클러스터를 보여 줍니다.

![환경으로 클러스터 Service Fabric](./media/create-environment-service-fabric-cluster/cluster-created.png)

환경을 설정 하는 방법에 대 한 자세한 내용은 [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)를 참조 하세요. DevTest Labs에는 외부 GitHub 원본에 직접 연결할 필요 없이 환경을 만드는 데 사용할 수 있는 Azure Resource Manager 템플릿의 공용 리포지토리가 있습니다. 공용 환경에 대 한 자세한 내용은 [Azure DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)을 참조 하세요.

대부분의 조직에서 개발 팀은 일반적으로 사용자 지정/격리 된 테스트 환경과 같은 환경을 제공 합니다. 비즈니스 단위나 부서 내의 모든 팀에서 사용 하는 환경이 있을 수 있습니다. IT 그룹은 조직의 모든 팀에서 사용할 수 있는 환경을 제공 하고자 할 수 있습니다.  

## <a name="customizations"></a>사용자 지정

#### <a name="sandbox"></a>샌드박스 
랩 소유자는 랩 환경을 사용자 지정 하 여 리소스 그룹 내에서 사용자의 역할을 **판독기** 에서 **참가자로** 변경할 수 있습니다. 이 기능은 랩의 **구성 및 정책** 에 있는 **랩 설정** 페이지에 있습니다. 이러한 역할 변경으로 사용자는 해당 환경 내에서 리소스를 추가 하거나 제거할 수 있습니다. 액세스를 추가로 제한 하려면 Azure 정책을 사용 합니다. 이 기능을 사용 하면 구독 수준에서 액세스 하지 않고 리소스 또는 구성을 사용자 지정할 수 있습니다.

#### <a name="custom-tokens"></a>사용자 지정 토큰
리소스 그룹 외부에 있고 템플릿이 액세스할 수 있는 환경에만 적용 되는 몇 가지 사용자 지정 랩 정보가 있습니다. 다음은 몇 가지 예입니다. 

- 랩 네트워크 id
- 위치
- 리소스 관리자 템플릿 파일이 저장 되는 저장소 계정입니다. 
 
#### <a name="lab-virtual-network"></a>랩 가상 네트워크
[랩의 가상 네트워크에 환경 연결](connect-environment-lab-virtual-network.md) 문서에서는 토큰을 사용 하도록 리소스 관리자 템플릿을 수정 하는 방법을 설명 합니다 `$(LabSubnetId)` . 환경을 만들면 `$(LabSubnetId)` 토큰은 **가상 컴퓨터 만들기에서 사용** 옵션이 **true**로 설정 된 첫 번째 서브넷 표시로 바뀝니다. 환경을 통해 이전에 만든 네트워크를 사용할 수 있습니다. 테스트 환경에서 스테이징 및 프로덕션으로 동일한 리소스 관리자 템플릿을 사용 하려는 경우 `$(LabSubnetId)` 리소스 관리자 템플릿 매개 변수에서를 기본값으로 사용 합니다. 

#### <a name="environment-storage-account"></a>환경 저장소 계정
DevTest Labs에서는 [중첩 된 리소스 관리자 템플릿](../azure-resource-manager/templates/linked-templates.md)사용을 지원 합니다. [[테스트 환경에 대해 중첩 Azure Resource Manager 템플릿 배포](deploy-nested-template-environments.md) 문서에서는 및 토큰을 사용 하 여 `_artifactsLocation` `_artifactsLocationSasToken` 주 템플릿의 중첩 된 폴더와 동일한 폴더에 있는 리소스 관리자 템플릿에 대 한 URI를 만드는 방법을 설명 합니다. 이러한 두 토큰에 대 한 자세한 내용은 [Azure Resource Manager-모범 사례 가이드](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)의 **배포 아티팩트** 섹션을 참조 하세요.

## <a name="user-experience"></a>사용자 환경

## <a name="developer"></a>개발자
개발자는 VM을 만들어 특정 환경을 만드는 데 동일한 워크플로를 사용 합니다. 환경 및 컴퓨터 이미지를 선택 하 고 템플릿에 필요한 정보를 입력 합니다. 환경을 보유 한 각 개발자는 변경 내용을 배포 하 고 내부 루프 디버깅을 향상 시킬 수 있습니다. 최신 템플릿을 사용 하 여 언제 든 지 환경을 만들 수 있습니다.  이 기능을 사용 하면 환경을 제거 하 고 다시 생성 하 여 가동 중지 시간을 수동으로 시스템을 만들거나 오류 테스트에서 복구할 필요가 없습니다.  

### <a name="testing"></a>테스트
DevTest Labs 환경에서는 특정 코드 및 구성을 개별적으로 테스트할 수 있습니다. 일반적인 방법은 개별 끌어오기 요청의 코드를 사용 하 여 환경을 설정 하 고 자동화 된 테스트를 시작 하는 것입니다. 자동화 된 테스트가 완료 될 때까지 모든 수동 테스트를 특정 환경에 대해 실행할 수 있습니다. 일반적으로이 프로세스는 CI/CD 파이프라인의 일부로 수행 됩니다. 

## <a name="management-experience"></a>관리 환경

### <a name="cost-tracking"></a>비용 추적
비용 추적 기능에는 전체 비용 추세의 일환으로 다양 한 환경 내에 Azure 리소스가 포함 되어 있습니다. 리소스 비용은 환경 내에서 다양 한 리소스를 분리 하지 않지만 환경을 단일 비용으로 표시 합니다.

### <a name="security"></a>보안
DevTest Labs를 사용 하 여 올바르게 구성 된 Azure 구독을 사용 하면 [랩을 통해 azure 리소스에 대 한 액세스를 제한할](devtest-lab-add-devtest-user.md)수 있습니다. 환경을 사용 하면 랩 소유자가 다른 Azure 리소스에 대 한 액세스를 허용 하지 않고 승인 된 구성을 사용 하 여 PaaS 리소스에 액세스할 수 있습니다. 랩 사용자가 환경을 사용자 지정 하는 시나리오에서 랩 소유자는 참가자 액세스를 허용할 수 있습니다. 참가자 액세스를 통해 랩 사용자는 관리 되는 리소스 그룹 내 에서만 Azure 리소스를 추가 하거나 제거할 수 있습니다. 이를 통해 보다 쉽게 추적 하 고 관리할 수 있으며 사용자 참가자가 구독에 액세스할 수 있습니다.

### <a name="automation"></a>Automation
Automation은 대규모의 효과적인 에코 시스템에 대 한 핵심 구성 요소입니다. Automation은 구독과 랩에서 여러 환경을 관리 하거나 추적 하는 작업을 처리 하는 데 필요 합니다.

### <a name="cicd-pipeline"></a>CI/CD 파이프라인
DevTest Labs의 PaaS 서비스는 랩에서 액세스를 제어 하는 집중 된 배포를 통해 CI/CD 파이프라인을 개선 하는 데 도움이 될 수 있습니다.

## <a name="next-steps"></a>다음 단계
환경에 대 한 세부 정보는 다음 문서를 참조 하세요. 

- 
- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
- [에서 자체 포함 된 Service Fabric 클러스터를 사용 하 여 환경 만들기 Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Azure DevTest Labs에서 랩의 가상 네트워크에 환경 연결](connect-environment-lab-virtual-network.md)
- [Azure DevOps CI/CD 파이프라인에 환경 통합](integrate-environments-devops-pipeline.md)
 





