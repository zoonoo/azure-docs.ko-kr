---
title: Azure DevTest 랩에서 서비스 패브릭 클러스터 환경 만들기
description: 자체 포함된 Service Fabric 클러스터를 사용하여 환경을 만들고 일정을 사용하여 클러스터를 시작하고 중지하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: enewman
ms.openlocfilehash: 71793b81d8735c80881fc25a9b7ec31bc4fc6762
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170330"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Azure DevTest 랩에서 독립적인 서비스 패브릭 클러스터를 사용하여 환경 만들기
이 문서에서는 Azure DevTest Labs에서 독립적인 서비스 패브릭 클러스터를 사용하여 환경을 만드는 방법에 대한 정보를 제공합니다. 

## <a name="overview"></a>개요
DevTest Labs는 Azure 리소스 관리 템플릿에 정의된 대로 자체 포함된 테스트 환경을 만들 수 있습니다. 이러한 환경에는 가상 컴퓨터와 같은 IaaS 리소스와 서비스 패브릭과 같은 PaaS 리소스가 모두 포함됩니다. DevTest Labs를 사용하면 가상 컴퓨터를 제어하는 명령을 제공하여 환경에서 가상 컴퓨터를 관리할 수 있습니다. 이러한 명령은 일정에 따라 가상 컴퓨터를 시작하거나 중지할 수 있는 기능을 제공합니다. 마찬가지로 DevTest 랩도 환경에서 서비스 패브릭 클러스터를 관리하는 데 도움을 줄 수 있습니다. 수동으로 또는 일정을 통해 환경에서 서비스 패브릭 클러스터를 시작하거나 중지할 수 있습니다.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric 클러스터 만들기
서비스 패브릭 클러스터는 DevTest 랩의 환경을 사용하여 만들어집니다. 각 환경은 Git 리포지토리의 Azure 리소스 관리자 템플릿에 의해 정의됩니다. DevTest Labs의 [공용 Git 리포지토리에는](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) [ServiceFabric-클러스터](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 폴더에 서비스 패브릭 클러스터를 만드는 리소스 관리자 템플릿이 포함되어 있습니다. 

1. 먼저 다음 문서의 지침을 사용하여 Azure DevTest Labs에서 [랩을 만듭니다.](devtest-lab-create-lab.md) **공용 환경** 옵션은 기본적으로 **켜두기** 입니다. 
2. 다음 단계에 따라 서비스 패브릭 공급자가 구독에 등록되어 있는지 확인합니다.
    1. 왼쪽 탐색 메뉴에서 **구독을** 선택하고 **구독을** 선택합니다.
    2. **구독** 페이지에서 왼쪽 메뉴의 **설정** 섹션에서 **리소스 공급자를** 선택합니다. 
    3. **Microsoft.ServiecFabric이** 등록되지 않은 경우 **등록을**선택합니다. 
3. 랩의 **DevTest Labs** 페이지에서 도구 모음의 **+ 추가**를 선택합니다. 
    
    ![도구 모음에 버튼 추가](./media/create-environment-service-fabric-cluster/add-button.png)
3. **기본** 선택 페이지에서 목록에서 **서비스 패브릭 랩 클러스터를** 선택합니다. 

    ![목록에서 서비스 패브릭 랩 클러스터 선택](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. 설정 **구성** 페이지에서 다음 단계를 수행합니다. 
    1. 클러스터 **환경에**대한 **이름을** 지정합니다. 서비스 패브릭 클러스터를 만들 Azure의 리소스 그룹의 이름입니다. 
    2. 클러스터 가상 시스템의 **운영 체제(OS)를** 선택합니다. 기본값은 다음과 같은 **값입니다.**
    3. 클러스터의 **관리자** 이름을 지정합니다. 
    4. 관리자의 **암호를** 지정합니다. 
    5. **인증서의**경우 인증서 정보를 Base64 인코딩된 문자열로 입력합니다. 인증서를 만들려면 다음 단계를 수행합니다.
        1. [Git 리포지토리에서](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) **클러스터 만들기인증서.ps1** 파일을 다운로드합니다. 또는 컴퓨터에서 리포지토리를 복제합니다. 
        2. **PowerShell**을 시작합니다. 
        3. 명령을 `.\Create-ClusterCertificate.ps1`사용하여 **ps1** 파일을 실행합니다. 이 페이지의 인증서 관련 필드를 채우는 데 필요한 정보가 있는 메모장에서 열린 텍스트 파일이 표시됩니다. . 
    6. **인증서의 암호를 입력합니다.**
    7. 인증서의 **지문을** 지정합니다.
    8. **설정 구성** 페이지에서 **추가를** 선택합니다. 

        ![클러스터 설정 구성](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. 클러스터를 만든 후 이전 단계에서 제공한 환경의 이름이 있는 리소스 그룹이 표시됩니다. 확장하면 서비스 패브릭 클러스터가 표시됩니다. [만들기]에서 리소스 그룹의 **Creating**상태가 막혀 있는 경우 도구 모음에서 **새로 고침을** 선택합니다. **서비스 패브릭 클러스터** 환경은 Linux 또는 Windows에서 5노드 1 노드 유형 클러스터를 만듭니다.

    다음 예제에서 **mysfabricclusterrg는** 서비스 패브릭 클러스터를 위해 특별히 만든 리소스 그룹의 이름입니다. 랩 환경은 만들어지는 리소스 그룹 내에 자체 포함되어 있습니다. 즉, 새로 생성된 리소스 그룹 내의 리소스또는 [랩에서 사용하도록 구성된 가상 네트워크](devtest-lab-configure-vnet.md)내에서만 리소스에 액세스할 수 있는 환경을 정의하는 템플릿을 의미합니다. 위의 이 샘플에서는 동일한 리소스 그룹에 필요한 모든 리소스를 만듭니다.

    ![생성된 클러스터](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>클러스터 시작 또는 중지
DevTest 랩 페이지 자체 또는 DevTest 연구소에서 제공하는 서비스 패브릭 클러스터 페이지에서 클러스터를 시작하거나 중지할 수 있습니다. 

### <a name="from-the-devtest-lab-page"></a>개발자 테스트 랩 페이지에서
랩의 DevTest 랩 페이지에서 클러스터를 시작하거나 중지할 수 있습니다. 

1. 다음 이미지와 같이 서비스 패브릭 클러스터에 대해 **세 개의 점(...)을** 선택합니다. 

    ![클러스터에 대한 명령 시작 및 중지](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. 클러스터를 **시작하고** **중지하는** 컨텍스트 메뉴에 두 개의 명령이 표시됩니다. 시작 명령은 클러스터의 모든 노드를 시작합니다. 중지 명령은 클러스터의 모든 노드를 중지합니다. 클러스터가 중지되면 Service Fabric 클러스터 자체는 준비 상태로 유지되지만 랩의 클러스터에서 시작 명령을 다시 발행할 때까지 노드를 사용할 수 없습니다.

    테스트 환경에서 서비스 패브릭 클러스터를 사용할 때 주의해야 할 몇 가지 사항이 있습니다. 노드를 다시 시작한 후 서비스 패브릭 클러스터가 완전히 수화되는 데 다소 시간이 걸릴 수 있습니다. 종료에서 시작까지 데이터를 유지하려면 가상 시스템에 연결된 관리 디스크에 데이터를 저장해야 합니다. 연결된 관리 디스크를 사용할 때 성능에 영향을 미치므로 테스트 환경에만 권장됩니다. 데이터 저장소에 사용된 디스크가 백업되지 않으면 클러스터에서 중지 명령을 발급할 때 데이터가 손실됩니다.

### <a name="from-the-service-fabric-cluster-page"></a>서비스 패브릭 클러스터 페이지에서 
클러스터를 시작하거나 중지하는 다른 방법이 있습니다. 

1. DevTest 랩 페이지의 트리 보기에서 서비스 패브릭 클러스터를 선택합니다. 

    ![클러스터 선택](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. 클러스터의 **서비스 패브릭 클러스터** 페이지에는 클러스터를 시작하거나 중지하는 명령이 도구 모음에 표시됩니다. 

    ![서비스 패브릭 클러스터 페이지에서 명령 시작 또는 중지](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>자동 시작 및 자동 종료 일정 구성
서비스 패브릭 클러스터를 일정에 따라 시작하거나 중지할 수도 있습니다. 이 환경은 랩의 가상 컴퓨터에 대한 환경과 유사합니다. 기본적으로 비용을 절감하기 위해 랩에서 만든 모든 클러스터는 랩 [종료 정책에](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)의해 정의된 시간에 자동으로 종료됩니다. 클러스터를 종료할지 여부를 지정하거나 클러스터를 종료할 시간을 지정하여 재정의할 수 있습니다. 

![자동 시작 및 자동 종료를 위한 기존 일정](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>자동 시작 일정에 옵트인
시작 일정을 선택하려면 다음 단계를 수행합니다.

1. 왼쪽 메뉴에서 **자동 시작을** 선택합니다.
2. **이 서비스 패브릭 클러스터를 자동 시작하도록 예약하려면** **켜기를** 선택합니다. 이 페이지는 랩 소유자가 사용자가 가상 컴퓨터 또는 서비스 패브릭 클러스터를 자동으로 시작할 수 있도록 허용한 경우에만 사용할 수 있습니다.
3. 도구 모음에서 **저장**을 선택합니다. 

    ![자동 별 페이지](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>자동 종료 설정 구성 
종료 설정을 변경하려면 다음 단계를 수행합니다.

1. 왼쪽 메뉴에서 **자동 종료를** 선택합니다. 
2. 이 페이지에서 **사용**중 **시작** 을 선택하여 자동 종료를 옵트아웃할 수 있습니다. 
3. 사용 **가능** **을** 선택한 경우 다음 단계를 따르십시오.
    1. 종료 **시간을** 지정합니다.
    2. 시간의 **시간대를** 지정합니다. 
    3. 자동 종료 전에 DevTest Labs에서 **알림을** 보낼지 여부를 지정합니다. 
    4. 알림 옵션에 **대해 예를** 선택한 경우 **Webhook URL** 및/또는 전자 메일 **주소를** 지정하여 알림을 보낼 수 있습니다. 
    5. 도구 모음에서 **저장**을 선택합니다.

        ![자동 종료 페이지](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>서비스 패브릭 클러스터에서 노드를 보려면
이전 단계에서 보았던 서비스 패브릭 클러스터 페이지는 DevTest Labs 페이지에만 해당됩니다. 클러스터의 노드는 표시되지 않습니다. 클러스터에 대한 자세한 정보를 보려면 다음 단계를 따르십시오.

1. 랩의 **DevTest Lab** 페이지에서 **내 가상 시스템** 섹션의 트리 보기에서 리소스 **그룹을** 선택합니다.

    ![리소스 그룹 선택](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. 리소스 **그룹** 페이지에서 리소스 그룹의 모든 리소스가 목록에 표시됩니다. 목록에서 **서비스 패브릭 클러스터를** 선택합니다. 

    ![목록에서 클러스터 선택](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. 클러스터의 **서비스 패브릭 클러스터** 페이지가 표시됩니다. 서비스 패브릭이 제공하는 페이지입니다. 노드, 노드 유형 등과 같은 클러스터에 대한 모든 정보가 표시됩니다.

    ![서비스 패브릭 클러스터 홈페이지](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>다음 단계
환경에 대한 자세한 내용은 다음 문서를 참조하십시오. 

- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
