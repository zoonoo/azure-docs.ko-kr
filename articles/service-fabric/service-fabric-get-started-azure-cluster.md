---
title: "Azure Service Fabric 클러스터 설정 | Microsoft Docs"
description: "빠른 시작 - Azure에서 Windows 또는 Linux Service Fabric 클러스터를 만듭니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 813217567c7e1fa5fc9ac11492933ad0c34553d1
ms.lasthandoff: 04/18/2017


---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Azure에서 첫 번째 Service Fabric 클러스터 만들기
[Service Fabric 클러스터](service-fabric-deploy-anywhere.md): 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 컴퓨터 집합입니다. 이 빠른 시작을 사용하여 몇 분만에 [Azure Portal](http://portal.azure.com)을 통해 Windows 또는 Linux에서 실행하는 다섯 개의 노드 클러스터를 만들 수 있습니다.  

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인
Azure Portal[http://portal.azure.com](http://portal.azure.com)에 로그인합니다.

## <a name="create-the-cluster"></a>클러스터 만들기

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.
2. **새로 만들기** 블레이드에서 **계산**을 선택한 다음 **계산** 블레이드에서 **Service Fabric 클러스터**를 선택합니다.
3. Service Fabric **기본 사항** 양식을 입력합니다. **운영 체제**의 경우 클러스터 노드를 실행하려는 Windows 또는 Linux의 버전을 선택합니다. 여기에서 입력한 이사용자 이름과 암호는 가상 컴퓨터에 로그인하는 데 사용됩니다 **리소스 그룹**에 대해 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 만들어지고 전체적으로 관리되는 논리적 컨테이너입니다. 완료되면 **확인**을 클릭합니다.

    ![클러스터 설정 출력][cluster-setup-basics]

4. **클러스터 구성** 양식을 입력합니다.  **노드 유형 수**의 경우 "1"을 입력하고 [지속성 계층](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)을 "동"으로 입력합니다.

5. **각 노드 형식 구성**을 선택하고 **노드 유형 구성** 양식을 입력합니다. 노드 유형은 VM 크기, VM 수, 사용자 지정 끝점 및 해당 유형 VM의 기타 설정을 정의합니다. 정의된 각 노드 유형은 별도의 가상 컴퓨터 크기 집합으로 설정되고 가상 컴퓨터를 집합으로 배포하고 관리하는 데 사용됩니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다.  첫 번째 또는 주 노드 유형은 Service Fabric 시스템 서비스가 호스팅되는 위치이며 5개 이상의 VM이 있어야 합니다.

    프로덕션 배포의 경우 [용량 계획](service-fabric-cluster-capacity.md)은 중요한 단계입니다.  하지만 이 빠른 시작의 경우 응용 프로그램을 실행하지 않으므로 *DS1_v2 표준* VM 크기를 선택합니다.  [안정성 계층](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) 및 초기 가상 컴퓨터 크기 집합인 5에서 "은"을 선택합니다.  

    클러스터에서 실행 중인 응용 프로그램과 연결할 수 있도록 사용자 지정 끝점은 Azure Load Balancer에서 포트를 엽니다.  "80, 8172"를 입력하여 80 및 8172 포트를 엽니다.

    TCP/HTTP 관리 끝점, 응용 프로그램 포트 범위, [배치 제약 조건](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints) 및 [용량 속성](service-fabric-cluster-resource-manager-metrics.md)을 사용자 지정하는 데 사용하는 **고급 설정 구성** 상자를 확인하지 않습니다.    

    **확인**을 선택합니다.

6. **클러스터 구성** 양식에서 **진단**을 **켜기**로 설정합니다.  이 빠른 시작의 경우 [패브릭 설정](service-fabric-cluster-fabric-settings.md) 속성을 입력하지 않아도 됩니다.  Microsoft에서 클러스터를 실행하는 패브릭 코드의 버전을 자동으로 업데이트하도록 **패브릭 버전**에서 **자동** 업그레이드 모드를 선택합니다.  업그레이드할 [지원되는 버전을 선택](service-fabric-cluster-upgrade.md)하려는 경우 모드를 **수동**으로 설정합니다. 

    ![노드 유형 구성][node-type-config]

    **확인**을 선택합니다.

7. **보안** 양식을 입력합니다.  이 빠른 시작의 경우 **보안 해제**를 선택합니다.  하지만 누구든지 안전하지 않은 클러스터에 연결하고 관리 작업을 수행할 수 있게 되므로 프로덕션 워크로드에 대한 보안 클러스터를 만드는 것이 좋습니다.  

    인증서는 서비스 패브릭에서 클러스터 및 해당 응용 프로그램의 다양한 측면을 보호하기 위해 인증 및 암호화를 제공하는 데 사용됩니다. Service Fabric에서 인증서가 사용되는 방식에 대한 자세한 내용은 [Service Fabric 클러스터 보안 시나리오](service-fabric-cluster-security.md)를 참조하세요.  Azure Active Directory를 사용하여 사용자 인증을 사용하거나 응용 프로그램 보안에 대한 인증서를 설정하려면 [Resource Manager 템플릿에서 클러스터를 만듭니다](service-fabric-cluster-creation-via-arm.md).

    **확인**을 선택합니다.

8. 요약을 검토합니다.  입력한 설정에서 빌드된 Resource Manager 템플릿을 다운로드하려는 경우 **템플릿 및 매개 변수 다운로드**를 선택합니다.  **만들기**를 선택하여 클러스터를 만듭니다.

    알림에서 만들기 진행률을 볼 수 있습니다. (화면 오른쪽 위의 상태 표시줄 근처에 있는 "종" 모양 아이콘을 클릭합니다.) 클러스터를 만드는 동안 **시작 보드에 고정**을 클릭하면 **Service Fabric 클러스터 배포 중**이 **시작** 보드에 고정됩니다.

## <a name="view-cluster-status"></a>클러스터 상태 보기
클러스터를 만들면 포털의 **개요** 블레이드에서 클러스터를 검사할 수 있습니다. 이제 대시보드에서 클러스터의 공용 끝점 및 Service Fabric Explorer에 대한 링크를 포함한 클러스터 세부 정보를 볼 수 있습니다.

![클러스터 상태][cluster-status]

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Service Fabric Explorer를 사용하여 클러스터 시각화
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)는 클러스터를 시각화하고 응용 프로그램을 관리할 수 있는 좋은 도구입니다.  Service Fabric Explorer는 클러스터에서 실행되는 서비스입니다.  포털의 클러스터 **개요** 페이지의 **Service Fabric Explorer** 링크를 클릭하여 웹 브라우저를 사용하는 서비스에 액세스합니다.  브라우저에 직접 주소를 입력할 수도 있습니다. [http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer](http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer)

클러스터 대시보드는 응용 프로그램 및 노드 상태에 대한 요약을 포함하여 클러스터에 대한 개요를 제공합니다. 노드 보기는 클러스터의 물리적 레이아웃을 보여 줍니다. 지정된 노드의 경우 해당 노드에 배포된 코드를 가진 응용 프로그램을 검사할 수 있습니다.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="connect-to-the-cluster-using-powershell"></a>PowerShell을 사용하여 클러스터에 연결
PowerShell을 사용하도록 연결하여 클러스터가 실행되고 있는지 확인합니다.  ServiceFabric PowerShell 모듈은 [Service Fabric SDK](service-fabric-get-started.md)에서 설치됩니다.  [Connect-ServiceFabricCluster](/powershell/module/ServiceFabric/Connect-ServiceFabricCluster) cmdlet은 클러스터에 대한 연결을 설정합니다.   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
클러스터에 연결하는 다른 예제는 [보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md)을 참조하세요. 클러스터에 연결한 후에는 [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) cmdlet을 사용하여 클러스터의 노드 목록과 각 노드에 대한 상태 정보를 표시합니다. **HealthState**는 노드마다 *OK* 상태여야 합니다.

```powershell
PS C:\> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  ----------- ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
```

## <a name="remove-the-cluster"></a>클러스터 제거
Service Fabric 클러스터는 클러스터 리소스 외에도 다른 Azure 리소스로 이루어져 있습니다. 따라서 서비스 패브릭 클러스터를 완벽하게 삭제하려면 구성되어 있는 모든 리소스를 삭제해야 합니다. 클러스터 및 모든 리소스를 삭제하는 가장 간단한 방법은 리소스 그룹을 삭제하는 것입니다. 클러스터를 삭제하거나 리소스 그룹에서 리소스 일부(전부가 아님)를 삭제하는 다른 방법은 [클러스터 삭제](service-fabric-cluster-delete.md)를 참조하세요.

Azure Portal에서 리소스 그룹을 삭제합니다.
1. 삭제하려는 서비스 패브릭 클러스터로 이동합니다.
2. 클러스터 필수 페이지에서 **리소스 그룹** 이름을 클릭합니다.
3. **리소스 그룹 Essentials** 페이지에서 **삭제**를 클릭하고 해당 페이지의 지침에 따라 리소스 그룹 삭제를 완료합니다.
    ![리소스 그룹 삭제][cluster-delete]

## <a name="next-steps"></a>다음 단계
이제 독립 실행형 개발 클러스터를 설정했으며 다음을 시도하세요.
* [포털에서 보안 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* [템플릿에서 클러스터 만들기](service-fabric-cluster-creation-via-arm.md) 
* [PowerShell을 사용하여 앱 배포](service-fabric-deploy-remove-applications.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
