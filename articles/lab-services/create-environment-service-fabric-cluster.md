---
title: Azure DevTest Labs에서 Service Fabric 클러스터 환경 만들기
description: 자체 포함 된 Service Fabric 클러스터로 환경을 만들고 일정을 사용 하 여 클러스터를 시작 및 중지 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170330"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>에서 자체 포함 된 Service Fabric 클러스터를 사용 하 여 환경 만들기 Azure DevTest Labs
이 문서에서는 Azure DevTest Labs에서 자체 포함 된 Service Fabric 클러스터로 환경을 만드는 방법에 대 한 정보를 제공 합니다. 

## <a name="overview"></a>개요
DevTest Labs는 Azure 리소스 관리 템플릿에 정의 된 대로 자체 포함 된 테스트 환경을 만들 수 있습니다. 이러한 환경에는 가상 머신과 같은 IaaS 리소스와 Service Fabric와 같은 PaaS 리소스가 모두 포함 됩니다. DevTest Labs를 사용 하면 가상 컴퓨터를 제어 하는 명령을 제공 하 여 환경에서 가상 컴퓨터를 관리할 수 있습니다. 이러한 명령은 일정에 따라 가상 컴퓨터를 시작 하거나 중지 하는 기능을 제공 합니다. 마찬가지로, DevTest Labs는 환경에서 Service Fabric 클러스터를 관리 하는 데도 도움이 될 수 있습니다. 환경에서 수동으로 또는 일정을 통해 Service Fabric 클러스터를 시작 하거나 중지할 수 있습니다.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric 클러스터 만들기
Service Fabric 클러스터는 DevTest Labs에서 환경을 사용 하 여 생성 됩니다. 각 환경은 Git 리포지토리의 Azure Resource Manager 템플릿에 의해 정의 됩니다. DevTest Labs에 대 한 [공용 Git 리포지토리에](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) 는 [ServiceFabric](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 폴더에 Service Fabric 클러스터를 만들기 위한 리소스 관리자 템플릿이 포함 되어 있습니다. 

1. 먼저 [랩 만들기](devtest-lab-create-lab.md)문서의 지침을 사용 하 여 Azure DevTest Labs에서 랩을 만듭니다. **공용 환경** 옵션은 기본적으로 **설정** 되어 있습니다. 
2. 다음 단계를 수행 하 여 Service Fabric 공급자가 구독에 등록 되어 있는지 확인 합니다.
    1. 왼쪽 탐색 메뉴에서 **구독** 을 선택 하 고 **구독** 을 선택 합니다.
    2. **구독** 페이지의 왼쪽 메뉴에 있는 **설정** 섹션에서 **리소스 공급자** 를 선택 합니다. 
    3. **ServiecFabric** 가 등록 되어 있지 않으면 **등록**을 선택 합니다. 
3. 랩의 **DevTest Labs** 페이지에서 도구 모음의 **+ 추가**를 선택합니다. 
    
    ![도구 모음에 단추 추가](./media/create-environment-service-fabric-cluster/add-button.png)
3. **기본 선택** 페이지의 목록에서 **랩 클러스터 Service Fabric** 를 선택 합니다. 

    ![목록에서 Service Fabric Lab Cluster를 선택 합니다.](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. **설정 구성** 페이지에서 다음 단계를 수행 합니다. 
    1. 클러스터 환경의 **이름을** 지정 합니다. Service Fabric 클러스터를 만들 Azure에서 리소스 그룹의 이름입니다. 
    2. 클러스터 가상 컴퓨터에 대 한 **os (운영 체제)** 를 선택 합니다. 기본값은 **Windows**입니다.
    3. 클러스터의 **관리자** 이름을 지정 합니다. 
    4. 관리자의 **암호** 를 지정 합니다. 
    5. **인증서**의 경우 Base64 인코딩된 문자열로 인증서 정보를 입력 합니다. 인증서를 만들려면 다음 단계를 수행 합니다.
        1. [Git 리포지토리에서](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) **Create-ClusterCertificate** 파일을 다운로드 합니다. 또는 컴퓨터에서 리포지토리를 복제 합니다. 
        2. **PowerShell**을 시작합니다. 
        3. 명령 `.\Create-ClusterCertificate.ps1`를 사용 하 여 **ps1** 파일을 실행 합니다. 이 페이지의 인증서 관련 필드에 입력 해야 하는 정보를 사용 하 여 메모장에서 열린 텍스트 파일이 표시 됩니다. . 
    6. **인증서에 대 한 암호**를 입력 합니다.
    7. 인증서의 **지문을** 지정 합니다.
    8. **설정 구성** 페이지에서 **추가** 를 선택 합니다. 

        ![클러스터 설정 구성](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. 클러스터를 만든 후 이전 단계에서 제공한 환경 이름이 포함 된 리소스 그룹이 표시 됩니다. 를 확장 하면 Service Fabric 클러스터가 표시 됩니다. 리소스 그룹의 상태가 **만드는 중**에 중지 되 면 도구 모음에서 **새로 고침** 을 선택 합니다. **Service Fabric 클러스터** 환경에서는 Linux 또는 Windows에서 5 노드 1-nodetype 클러스터를 만듭니다.

    다음 예제에서 **mysfabricclusterrg** 는 Service Fabric 클러스터에 대해 특별히 생성 된 리소스 그룹의 이름입니다. 랩 환경이 생성 되는 리소스 그룹 내에 자체 포함 된다는 점에 유의 해야 합니다. 이는 환경을 정의 하는 템플릿으로, 새로 만든 리소스 그룹이 나 [랩에서 사용 하도록 구성 된 가상 네트워크](devtest-lab-configure-vnet.md)내의 리소스에만 액세스할 수 있습니다. 위의 예제는 동일한 리소스 그룹에 필요한 모든 리소스를 만듭니다.

    ![클러스터를 만듦](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>클러스터 시작 또는 중지
DevTest Lab 페이지 자체 또는 DevTest Labs에서 제공 하는 Service Fabric 클러스터 페이지에서 클러스터를 시작 하거나 중지할 수 있습니다. 

### <a name="from-the-devtest-lab-page"></a>DevTest Lab 페이지에서
랩에 대 한 DevTest Lab 페이지에서 클러스터를 시작 하거나 중지할 수 있습니다. 

1. 다음 이미지에 표시 된 것 처럼 Service Fabric 클러스터에 대해 **세 개의 점 (...)** 을 선택 합니다. 

    ![클러스터에 대 한 시작 및 중지 명령](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. 상황에 맞는 메뉴에 두 개의 명령이 표시 되어 클러스터를 **시작** 하 고 **중지** 합니다. 시작 명령은 클러스터의 모든 노드를 시작 합니다. 중지 명령은 클러스터의 모든 노드를 중지 합니다. 클러스터가 중지 되 면 Service Fabric 클러스터 자체는 준비 상태로 유지 되지만 랩의 클러스터에서 시작 명령이 다시 실행 될 때까지 노드를 사용할 수 없습니다.

    테스트 환경에서 Service Fabric 클러스터를 사용할 때 유의 해야 할 몇 가지 사항이 있습니다. 노드가 다시 시작 된 후 Service Fabric 클러스터가 완전히 리하이드레이션 하는 데 약간의 시간이 걸릴 수 있습니다. 종료 시 데이터를 유지 하려면 가상 컴퓨터에 연결 된 관리 디스크에 데이터를 저장 해야 합니다. 연결 된 관리 디스크를 사용 하는 경우 성능에 영향을 미치므로 테스트 환경 에서만 사용 하는 것이 좋습니다. 데이터 저장소에 사용 되는 디스크를 백업 하지 않은 경우 클러스터에서 중지 명령이 실행 되 면 데이터가 손실 됩니다.

### <a name="from-the-service-fabric-cluster-page"></a>Service Fabric 클러스터 페이지에서 
클러스터를 시작 하거나 중지 하는 또 다른 방법이 있습니다. 

1. DevTest Lab 페이지의 트리 뷰에서 Service Fabric 클러스터를 선택 합니다. 

    ![클러스터 선택](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. 클러스터에 대 한 **Service Fabric 클러스터** 페이지에서 도구 모음에 클러스터를 시작 하거나 중지 하는 명령이 표시 됩니다. 

    ![클러스터 Service Fabric 페이지의 시작 또는 중지 명령](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>자동 시작 및 자동 종료 일정 구성
Service Fabric 클러스터는 일정에 따라 시작 되거나 중지 될 수도 있습니다. 이 환경은 랩의 가상 컴퓨터 환경과 비슷합니다. 비용을 절약 하기 위해 기본적으로 랩에서 만든 모든 클러스터는 랩 [종료 정책](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)에 정의 된 시간에 자동으로 종료 됩니다. 클러스터가 종료 되어야 하는지 여부를 지정 하거나 클러스터가 종료 되는 시간을 지정 하 여를 재정의할 수 있습니다. 

![자동 시작 및 자동 종료에 대 한 기존 일정](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>자동 시작 일정 옵트인 (Opt in)
시작 일정을 옵트인 하려면 다음 단계를 수행 합니다.

1. 왼쪽 메뉴에서 **자동 시작** 을 선택 합니다.
2. **이 service fabric 클러스터의 자동 시작 예약을 허용 하려면** **켜기** 를 선택 합니다. 이 페이지는 랩 소유자가 자신의 가상 컴퓨터 또는 Service Fabric 클러스터를 자동으로 시작 하도록 허용한 경우에만 사용할 수 있습니다.
3. 도구 모음에서 **저장**을 선택합니다. 

    ![자동 별 페이지](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>자동 종료 설정 구성 
종료에 대 한 설정을 변경 하려면 다음 단계를 수행 합니다.

1. 왼쪽 메뉴에서 **자동 종료** 를 선택 합니다. 
2. 이 페이지에서는 **사용**을 선택 하 여 자동 **종료를 옵트아웃** (opt out) 할 수 있습니다. 
3. **설정 됨**을 선택한 경우 다음 단계를 **수행 합니다.**
    1. 종료 **시간** 을 지정 합니다.
    2. 시간의 **표준 시간대** 를 지정 합니다. 
    3. 자동 종료 전에 DevTest Labs에서 **알림을** 보낼지 여부를 지정 합니다. 
    4. 알림 옵션에 대해 **예** 를 선택한 경우 알림을 보낼 **Webhook URL** 및/또는 **메일 주소** 를 지정 합니다. 
    5. 도구 모음에서 **저장**을 선택합니다.

        ![자동 종료 페이지](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Service Fabric 클러스터의 노드를 보려면
이전 단계에서 본 Service Fabric 클러스터 페이지는 DevTest Labs 페이지에만 적용 됩니다. 클러스터의 노드는 표시 되지 않습니다. 클러스터에 대 한 자세한 정보를 보려면 다음 단계를 수행 합니다.

1. 랩에 대 한 **DevTest lab** 페이지의 **내 가상 컴퓨터** 섹션에서 트리 보기의 **리소스 그룹** 을 선택 합니다.

    ![리소스 그룹 선택](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. 리소스 **그룹** 페이지에서 리소스 그룹의 모든 리소스를 목록으로 표시 합니다. 목록에서 **Service Fabric 클러스터** 를 선택 합니다. 

    ![목록에서 클러스터를 선택 합니다.](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. 클러스터에 대 한 **Service Fabric 클러스터** 페이지가 표시 됩니다. Service Fabric에서 제공 하는 페이지입니다. 노드, 노드 유형 등과 같은 클러스터에 대 한 모든 정보가 표시 됩니다.

    ![클러스터 홈 페이지 Service Fabric](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>다음 단계
환경에 대 한 세부 정보는 다음 문서를 참조 하세요. 

- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
