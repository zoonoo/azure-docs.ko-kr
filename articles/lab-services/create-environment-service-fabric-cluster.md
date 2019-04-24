---
title: Azure DevTest Labs에서 Service Fabric 클러스터를 사용 하 여 환경 만들기 | Microsoft Docs
description: 자체 포함 된 Service Fabric 클러스터를 사용 하 여 환경 만들기를 시작 하 고 일정을 사용 하 여 클러스터를 중지 하는 방법을 알아봅니다.
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
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 9848f197800c391285c4065685b910685f0ac64b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312188"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Azure DevTest Labs에서 자체 포함 된 Service Fabric 클러스터를 사용 하 여 환경 만들기
이 문서에서는 Azure DevTest Labs에서 자체 포함 된 Service Fabric 클러스터를 사용 하 여 environment를 만드는 방법에 정보를 제공 합니다. 

## <a name="overview"></a>개요
DevTest Labs Azure Resource Management 템플릿 정의 된 대로 자체 포함 된 테스트 환경을 만들 수 있습니다. 이러한 환경에는 virtual machines 및 Service Fabric과 같은 PaaS 리소스와 같은 IaaS 리소스를 모두 포함 됩니다. DevTest Labs를 사용 하면 가상 컴퓨터를 제어 하는 명령을 제공 하 여 환경에서 가상 컴퓨터를 관리할 수 있습니다. 이러한 명령은 시작 하거나 일정에 따라 가상 컴퓨터를 중지 하는 기능을 제공 합니다. 마찬가지로, DevTest Labs도 도움이 될 수 있습니다 환경에서 Service Fabric 클러스터를 관리 합니다. 시작 하거나 수동으로 또는 일정을 통해 환경에서 Service Fabric 클러스터를 중지할 수 있습니다.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric 클러스터 만들기
Service Fabric 클러스터 환경을 사용 하 여 DevTest Labs에서 생성 됩니다. 각 환경 Git 리포지토리에서 Azure Resource Manager 템플릿에 의해 정의 됩니다. 합니다 [공용 Git 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) DevTest Labs에서 Service Fabric 클러스터를 만드는 Resource Manager 템플릿을 포함에 대 한 합니다 [ServiceFabric 클러스터](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 폴더입니다. 

1. 다음 문서의 지침을 사용 하 여 Azure DevTest Labs에서 랩을 먼저 만듭니다. [랩 만들기](devtest-lab-create-lab.md)합니다. 에 **공용 환경의** 옵션은 **에서** 기본적으로 합니다. 
2. 다음이 단계를 수행 하 여 Service Fabric 공급자가 구독에 대 한 등록을 확인 합니다.
    1. 선택 **구독** 왼쪽된 탐색 메뉴를 선택 하면 **구독**
    2. 에 **구독** 페이지에서 **리소스 공급자** 에 **설정** 왼쪽 메뉴의 섹션입니다. 
    3. 하는 경우 **Microsoft.ServiecFabric** 등록 된, 선택 되지 **등록**합니다. 
3. 랩의 **DevTest Labs** 페이지에서 도구 모음의 **+ 추가**를 선택합니다. 
    
    ![도구 모음에서 단추를 추가 합니다.](./media/create-environment-service-fabric-cluster/add-button.png)
3. 에 **기본 선택** 페이지에서 **Service Fabric 랩 클러스터** 목록의 합니다. 

    ![목록에서 Service Fabric 랩 클러스터를 선택 합니다.](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. 에 **설정을 구성** 페이지에서 다음 단계를 수행 합니다. 
    1. 지정 된 **이름을** 클러스터에 대 한 **환경**합니다. Azure는 Service Fabric 클러스터는 만들려는 리소스 그룹의 이름입니다. 
    2. 선택 된 **운영 체제 (OS)** 클러스터 가상 컴퓨터에 대 한 합니다. 기본값은 다음과 같습니다. **Windows**.
    3. 에 대 한 이름을 지정 합니다 **관리자** 클러스터에 대 한 합니다. 
    4. 지정 된 **암호** 관리자에 대 한 합니다. 
    5. 에 대 한 합니다 **인증서**, Base64로 인코딩된 문자열로 나타낸 인증서 정보를 입력 합니다. 인증서를 만들려면 다음 단계를 수행 합니다.
        1. 다운로드 합니다 **만들기 ClusterCertificate.ps1** 에서 파일을 [Git 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)합니다. 또는 컴퓨터에 리포지토리를 복제 합니다. 
        2. **PowerShell**을 시작합니다. 
        3. 실행 합니다 **ps1** 명령을 사용 하 여 파일 `.\Create-ClusterCertificate.ps1`합니다. 이 페이지에서 인증서 관련 필드를 입력 하는 데 필요한 정보를 사용 하 여 메모장에서 연 텍스트 파일을 표시 합니다. . 
    6. 입력 된 **인증서의 암호를**입니다.
    7. 지정 된 **지문을** 인증서에 대 한 합니다.
    8. 선택 **추가** 에 **Configure Settings** 페이지입니다. 

        ![클러스터 설정 구성](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. 클러스터를 만든 후 리소스 그룹 이름 사용 하 여 이전 단계에서 제공 하는 환경에 표시 됩니다. 확장 하면 Service Fabric 클러스터에 표시 됩니다. 리소스 그룹의 상태에서 중단 되 면 **만들기**를 선택 **새로 고침** 도구 모음에서 합니다. 합니다 **Service Fabric 클러스터** 환경은 Windows 또는 Linux에서 5 개 노드 1 nodetype 클러스터를 만듭니다.

    다음 예에서 **mysfabricclusterrg** Service Fabric 클러스터에 대해 특별히 만들어진 리소스 그룹의 이름입니다. 랩 환경은 자체 생성 된 리소스 그룹 내 포함 하는 것이 반드시 합니다. 즉, 새로 만든된 리소스 그룹 내 리소스에만 액세스할 수 있는 환경을 정의 하는 템플릿 또는 [랩에서 사용 하도록 구성 하는 가상 네트워크](devtest-lab-configure-vnet.md)합니다. 위의이 샘플에서는 동일한 리소스 그룹에 필요한 모든 리소스를 만듭니다.

    ![만든 클러스터](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>시작 하거나 클러스터를 중지 합니다.
시작 하거나 DevTest Lab 페이지 자체 또는 DevTest Labs에서 제공 하는 Service Fabric 클러스터 페이지에서 클러스터를 중지할 수 있습니다. 

### <a name="from-the-devtest-lab-page"></a>DevTest Lab 페이지에서
시작 하거나 랩에 대해 DevTest Lab 페이지에서 클러스터를 중지할 수 있습니다. 

1. 선택 **줄임표 (...)**  다음 그림에 나와 있는 것 처럼 Service Fabric 클러스터에 대 한 합니다. 

    ![시작 및 중지를 클러스터에 대 한 명령](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. 상황에 맞는 메뉴에서 두 명령의 표시 **시작** 하 고 **중지** 클러스터. Start 명령은 클러스터의 모든 노드를 시작 합니다. 중지 명령을 클러스터의 모든 노드를 중지합니다. 클러스터 중지 되 면 Service Fabric 클러스터 자체을 준비 상태로 남지만 랩의 클러스터에서 start 명령은 발급 될 때까지 사용할 수 있는 노드가 없습니다.

    테스트 환경에서 Service Fabric 클러스터를 사용할 때 알아야 할 몇 가지 고려 사항이 있습니다. Service Fabric 클러스터가 완벽 하 게 노드 다시 시작 된 후 리하이드레이션 하는 데 다소 시간이 걸릴 수 있습니다. 시작과 종료에서 데이터를 유지 하려면 가상 머신에 연결 된 관리 디스크에서 데이터를 저장 해야 합니다. 테스트 환경에만 것이 좋습니다. 연결 된 관리 디스크를 사용할 때 성능에 미치는 영향이 있습니다. 데이터 저장소에 사용 되는 디스크에 지원 되지 않습니다 후 데이터가 손실 됩니다 중지 명령을 클러스터에서 실행 되는 경우.

### <a name="from-the-service-fabric-cluster-page"></a>Service Fabric 클러스터 페이지에서 
시작 하거나 클러스터를 중지 하는 방법은 다른 방법이 있습니다. 

1. DevTest Lab 페이지의 트리 보기에서 Service Fabric 클러스터를 선택 합니다. 

    ![클러스터를 선택 합니다.](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. 에 **Service Fabric 클러스터** 페이지 클러스터에 대해 명령이 표시 시작 하거나 클러스터를 중지 하려면 도구 모음에서. 

    ![시작 또는 Service Fabric 클러스터 페이지에는 명령을 중지합니다](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>자동 시작 및 자동 종료 일정 구성
Service Fabric 클러스터를 시작 하거나 일정에 따라 중지 될 수도 있습니다. 이 환경은 랩에서 가상 머신에 대 한 환경과 유사 합니다. 기본적으로 비용을 저장 하려면 랩에서 자동으로 생성 하는 모든 클러스터 종료 lab에서 정의할 [종료 정책](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown)합니다. 클러스터를 종료 해야 하는지 여부를 지정 하거나 클러스터는 종료 시간을 지정 하 여 재정의할 수 있습니다. 

![자동 시작 및 자동 종료에 대 한 기존 일정](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>자동 시작 일정에 옵트인
Startup schedule을 옵트인 하려면 다음 단계를 수행 합니다.

1. 선택 **자동 시작** 왼쪽된 메뉴에서
2. 선택 **대** 에 대 한 **자동으로 시작 하도록 예약 하려면이 service fabric 클러스터를 허용**합니다. 이 페이지는 해당 가상 컴퓨터 또는 Service Fabric 클러스터 랩 소유자가 자동으로 시작 되도록 사용자를 허용할 경우에 사용 됩니다.
3. 도구 모음에서 **저장**을 선택합니다. 

    ![자동 별모양 페이지](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>자동 종료 설정을 구성 합니다. 
종료에 대 한 설정을 변경 하려면 다음 단계를 수행 합니다.

1. 선택 **자동 종료** 왼쪽된 메뉴에서. 
2. 이 페이지에서는 옵트아웃할 수 있습니다 자동 종료를 선택 하 여 **해제** 에 대 한 **Enabled**합니다. 
3. 선택한 경우 **온** 에 대 한 **Enabled**, 다음이 단계를 수행:
    1. 지정 된 **시간** 종료 합니다.
    2. 지정 된 **표준 시간대** 시간에 대 한 합니다. 
    3. DevTest Labs 보낼 것인지 여부를 지정 **알림을** 자동 종료 하기 전에 합니다. 
    4. 선택한 경우 **예** 알림 옵션을 지정 합니다 **웹 후크 URL** 및/또는 **전자 메일 주소** 알림을 보낼 수 있습니다. 
    5. 도구 모음에서 **저장**을 선택합니다.

        ![자동 페이지 종료](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Service Fabric 클러스터의 노드를 확인 하려면
이전 단계에서 살펴본 Service Fabric 클러스터 페이지 DevTest Labs 페이지와 관련이 있습니다. 클러스터의 노드 수 표시 되지는 않습니다. 클러스터에 대 한 자세한 정보를 보려면 다음을 수행 합니다.

1. 에 **DevTest Lab** lab에 선택에 대 한 페이지의 **리소스 그룹** 트리 뷰에 **내 가상 머신** 섹션.

    ![리소스 그룹 선택](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. 에 **리소스 그룹** 페이지 목록에 리소스 그룹에 있는 모든 리소스를 참조 하세요. 선택 하면 **Service Fabric 클러스터** 목록에서. 

    ![목록에서 클러스터를 선택 합니다.](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. 표시 된 **Service Fabric 클러스터** 클러스터에 대 한 페이지입니다. Service Fabric에서 제공 하는 페이지입니다. 노드, 노드 유형 등과 같은 클러스터에 대 한 모든 정보를 표시 합니다.

    ![Service Fabric 클러스터 홈 페이지](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>다음 단계
환경에 대 한 자세한 내용은 다음 문서를 참조 하세요. 

- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [구성 및 Azure DevTest Labs에서 공용 환경 사용](devtest-lab-configure-use-public-environments.md)
