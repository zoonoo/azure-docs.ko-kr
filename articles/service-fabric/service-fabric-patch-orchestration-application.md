---
title: "Azure Service Fabric 패치 오케스트레이션 응용 프로그램 | Microsoft Docs"
description: "Service Fabric 클러스터에 OS 패치를 자동화하는 응용 프로그램입니다."
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: nachandr
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: a3554881e7db894c602e73feb385b5b361837409
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017


---

# <a name="application-to-patch-windows-os-in-your-service-fabric-cluster"></a>Service Fabric 클러스터에서 Windows OS를 패치하는 응용 프로그램

패치 오케스트레이션 응용 프로그램은 Service Fabric 응용 프로그램이며 Azure 또는 온-프레미스에서 가동 중지 시간 없이 Service Fabric 클러스터에 대한 OS 패치를 자동화할 수 있습니다.

패치 오케스트레이션 응용 프로그램은 다음과 같은 중요한 기능을 포함합니다.

1. **자동 OS 업데이트 설치**: 패치 오케스트레이션 응용 프로그램을 통해 업데이트를 자동으로 다운로드하고 설치하며 적용 가능하도록 노드를 다시 부팅할 수 있습니다.
    이 작업은 가동 중지 시간 없이 모든 클러스터 노드에서 수행됩니다.

1. **클러스터 인식 패치 및 상태 통합**: 패치 오케스트레이션 응용 프로그램은 업데이트를 적용하는 동안 한 번에 하나의 노드 또는 한 번에 하나의 업그레이드 도메인을 업데이트하는 것에 앞서서 클러스터의 상태를 모니터링합니다. 
    언제든지 패치 프로세스로 인해 클러스터의 상태가 중지되는 것을 감지한 경우 문제를 방지하기 위해 프로세스를 중지합니다.

1. **모든 Service Fabric 클러스터 지원**: 응용 프로그램은 일반적으로 Azure Service Fabric 클러스터와 독립 실행형 클러스터 모두에서 작업하기에 충분합니다.
    > [!NOTE]
    > 독립 실행형 클러스터에 대한 지원이 곧 제공됩니다.

## <a name="link-to-download-the-application-package"></a>응용 프로그램 패키지를 다운로드하는 링크

[다운로드 링크](https://go.microsoft.com/fwlink/P/?linkid=849590)에서 응용 프로그램 다운로드

## <a name="internal-details-of-the-application"></a>응용 프로그램의 내부 세부 정보

패치 오케스트레이션 응용 프로그램은 다음 하위 구성 요소로 구성됩니다.

- **코디네이터 서비스**: 상태 저장 서비스입니다. 서비스는 다음과 같은 역할을 합니다.
    - 전체 클러스터에서 Windows Update 작업을 조정합니다.
    - 완료된 Windows Update 작업의 결과를 저장합니다.
- **노드 에이전트 서비스**: 상태 비저장 서비스이며 모든 Service Fabric 클러스터 노드에서 실행됩니다. 서비스는 다음과 같은 역할을 합니다.
    - 노드 에이전트 NTService의 부트스트랩
    - 노드 에이전트 NTService의 모니터링
- **노드 에이전트 NTService**: Windows NT 서비스입니다. 노드 에이전트 NTService는 더 높은 권한(시스템)으로 실행됩니다. 반면, 노드 에이전트 서비스 및 코디네이터 서비스는 하위 수준의 권한(네트워크 서비스)에서 실행됩니다. 서비스는 모든 클러스터 노드에서 다음과 같은 Windows Update 작업을 수행하는 일을 담당합니다.
    - 노드에서 자동 Windows Update 비활성화
    - 정책 사용자에 따라 제공된 Windows Updates 다운로드 및 설치
    - Windows Update 설치 이후 컴퓨터 다시 시작
    - 코디네이터 서비스에 Windows Update 결과 업로드
    - 모든 재시도가 끝난 다음 작업이 실패한 경우에 상태 보고서를 보고합니다.

> [!NOTE]
> 패치 오케스트레이션 응용 프로그램은 Service Fabric 시스템 서비스 **복구 관리자**를 사용하여 노드를 비활성화/활성화하고 상태 검사를 수행합니다. 패치 오케스트레이션 응용 프로그램에서 만든 복구 작업은 각 노드에 대한 Windows Update 진행률을 추적합니다.

## <a name="prerequisites-for-using-the-application"></a>응용 프로그램을 사용하기 위한 필수 조건

### <a name="ensure-service-fabric-version-is-55-or-above"></a>Service Fabric 버전 5.5 이상 확인

패치 오케스트레이션 응용 프로그램은 Service Fabric 런타임 버전 v5.5 이상이 설치된 클러스터에서 실행될 수 있습니다.

### <a name="enable-repair-manager-service-if-not-running-already"></a>복구 관리자 서비스 활성화(아직 실행되지 않은 경우)

패치 오케스트레이션 응용 프로그램은 복구 관리자 시스템 서비스를 클러스터에서 사용하도록 설정해야 합니다.

#### <a name="service-fabric-clusters-on-azure"></a>Azure에서 Service Fabric 클러스터 사용

실버 지속성 계층인 Azure 클러스터에서는 복구 관리자를 기본적으로 사용해야 합니다. 골드 지속성 계층인 Azure 클러스터에서는 해당 클러스터가 생성되어 경과한 시간에 따라 복구 관리자를 사용할 수 없게 됩니다. 반면 브론즈 지속성 계층인 Azure 클러스터에서는 기본적으로 복구 관리자 서비스를 사용할 수 없습니다. 

Service Fabric Explorer의 시스템 서비스 섹션에서 이미 복구 관리자 서비스를 실행한다고 표시되지 않는 경우 [Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)을 사용하여 새/기존 Service Fabric 클러스터에서 사용할 수 있도록 합니다.

먼저 배포하려는 클러스터에 대한 템플릿을 가져옵니다. 예제 템플릿을 사용하거나 사용자 지정 Resource Manager 템플릿을 만들 수 있습니다. 그런 후 다음 단계를 사용하여 복구 관리자를 사용하도록 설정할 수 있습니다.

1. 먼저 다음 코드 조각과 같이 `Microsoft.ServiceFabric/clusters` 리소스에 대해 `apiversion`이 `2017-07-01-preview`로 설정되었는지 확인합니다. 값이 다르면 `apiVersion`을 `2017-07-01-preview` 값으로 업데이트해야 합니다.

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 이제 아래와 같이 다음 `addonFeaturres` 섹션을 `fabricSettings` 섹션 뒤에 추가하여 복구 관리자를 사용하도록 설정합니다.

    ```json
    "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "RepairManager"
        ],
    ```

3. 이러한 변경 내용으로 클러스터 템플릿을 업데이트한 후 변경 내용을 적용하고 업그레이드가 완료되도록 합니다. 완료되면 클러스터에서 실행되는 `fabric:/System/RepairManagerService`라는 복구 관리자 시스템 서비스가 Service Fabric Explorer의 시스템 서비스 섹션 아래에 표시됩니다. 

#### <a name="standalone-on-premise-clusters"></a>독립 실행형 온-프레미스 클러스터

> [!NOTE]
> 독립 실행형 클러스터에 대한 지원이 곧 제공됩니다.

### <a name="disable-automatic-windows-update-on-all-nodes"></a>모든 노드에서 자동 Windows Update 비활성화

자동 Windows Update를 Service Fabric 클러스터에서 사용하는 경우 업데이트를 완료하는 동시에 여러 노드를 다시 시작할 수 있으므로 가용성 손실이 발생할 수 있습니다.

패치 오케스트레이션 응용 프로그램은 기본적으로 각 클러스터 노드에서 자동 Windows Update를 사용하지 않으려 합니다.

그러나 설정이 관리자/그룹 정책에서 관리되는 경우에 명시적으로 Windows Update 정책을 "다운로드하기 전에 알리도록" 설정하는 것이 좋습니다.


### <a name="optional-enable-windows-azure-diagnostics"></a>선택 사항: Windows Azure 진단 활성화

패치 오케스트레이션 응용 프로그램의 로그는 몇 일 이내에 Service Fabric 로그의 일부로 수집됩니다. 그러나 그 때까지 로그는 각 클러스터 노드에 로컬로 수집됩니다. 로그를 모든 노드에서 중앙 위치로 업로드하도록 WAD(WAD Azure 진단)를 구성하는 것이 좋습니다.

WAD를 활성화하는 단계는 [여기에서](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad) 자세하게 설명합니다.

다음과 같은 고정된 공급자 ID에서 패치 오케스트레이션 응용 프로그램의 로그가 생성됩니다.

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

Azure Resource Manager 템플릿의 "WadCfg" 섹션 내에 다음 섹션을 추가합니다. 

```json
"PatchOrchestrationApplication": \[
  {
    "provider": "e39b723c-590c-4090-abb0-11e3e6616346",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
      "eventDestination": "PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "fc0028ff-bfdc-499f-80dc-ed922c52c5e9",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "24afa313-0d3b-4c7c-b485-1047fd964b60",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "05dc046c-60e9-4ef7-965e-91660adffa68",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
\]
```

> [!NOTE]
> Service Fabric 클러스터를 여러 노드 형식으로 구성하는 경우 위의 섹션을 "WadCfg" 섹션에 추가해야 합니다.

## <a name="configuring-the-application"></a>응용 프로그램 구성

다음은 필요에 따라 패치 오케스트레이션 응용 프로그램의 동작을 조정하는 사용자가 설정할 수 있는 구성입니다.

응용 프로그램 만들기/업데이트 중에 응용 프로그램 매개 변수를 전달하여 기본값을 재정의할 수 있습니다. 응용 프로그램 매개 변수는 `Start-ServiceFabricApplicationUpgrade` 또는`New-ServiceFabricApplication` cmdlet에 `ApplicationParameter`을 지정하여 제공될 수 있습니다

|**매개 변수**        |**형식**                          | **세부 정보**|
|:-|-|-|
|MaxResultsToCache    |long                              | Windows Update의 최대 수는 기록으로 남으며 캐시되어야 합니다. <br>기본값은 3000입니다. <br> - 노드 수 20 <br> - 월별 노드에서 발생하는 업데이트의 수 5 <br> - 작업당 결과 수 10 <br> - 지난 3개월 동안 결과를 저장해야 함 |
|TaskApprovalPolicy   |열거형 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy는 Service Fabric 클러스터 노드를 통해 Windows Update를 설치하기 위해 CoordinatorService에서 사용할 정책을 나타냅니다.<br>                         허용되는 값은 다음과 같습니다. <br><br>                                                           <b>NodeWise</b> - Windows Update는 한 번에 한 노드를 설치합니다. <br>                                                           <b>UpgradeDomainWise</b> - Windows Update는 한 번에 하나의 업그레이드 도메인을 설치합니다(업그레이드 도메인에 속하는 모든 최대 노드는 Windows Update에서 사용할 수 있음).
|LogsDiskQuotaInMB   |long  <br> (기본값: 1024)               |패치 오케스트레이션 응용 프로그램 로그의 최대 크기(MB)는 노드에서 로컬로 유지될 수 있습니다.
| WUQuery               | string<br>(기본값: "IsInstalled = 0")                | Windows Update를 가져오는 쿼리입니다. 자세한 내용은 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)를 참조하세요.
| InstallWindowsOSOnlyUpdates | Bool <br> (기본값: True)                 | 이 플래그를 사용하면 Windows OS Update를 설치해야 합니다.            |
| WUOperationTimeOutInMinutes | int <br>(기본값: 90)                   | Windows Update 작업에 대한 제한 시간을 지정합니다(검색/다운로드/설치). 지정된 시간 제한 내에서 작업을 완료하지 않으면 중단됩니다.       |
| WURescheduleCount     | int <br> (기본값: 5)                  | 작업이 영구적으로 실패하는 경우 이 구성은 서비스가 Windows Update를 다시 예약하는 최대 수를 결정합니다.          |
| WURescheduleTimeInMinutes | int <br>(기본값: 30) | 오류가 계속 발생하는 경우 이 구성은 서비스가 Windows Update 일정을 다시 조정하는 간격을 결정합니다. |
| WUFrequency           | 쉼표로 구분된 문자열(기본값: "매주, 수요일, 7시")     | Windows Update를 설치하는 빈도입니다. 형식 및 가능한 값은 아래와 같습니다. <br>- 매월,DD,HH:MM:SS 예: 매월,5,12:22:32 <br> - 매주,DAY,HH:MM:SS 예: 매주, 화요일, 12:22:32  <br> - 매일, HH:MM:SS 예: 매일, 12:22:32  <br> - 없음 - Windows Update를 수행하지 않음을 나타냄  <br><br> 참고: 모든 시간은 UTC 형식입니다.|
| AcceptWindowsUpdateEula | Bool <br>(기본값: True) | 응용 프로그램은 이 플래그를 설정하여 컴퓨터의 소유자를 대신하여 Windows Update에 EULA를 허용합니다.              |


## <a name="steps-to-deploy-the-application"></a>응용 프로그램을 배포하는 단계

1. 모든 필수 구성 요소 단계를 완료하여 클러스터를 준비합니다.
1. 응용 프로그램 배포 - PowerShell에서 [여기에](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) 설명된 단계를 사용하여 수행합니다.
1. 배포 시 응용 프로그램을 구성하기 위해 cmdlet `New-ServiceFabricApplication`에 `ApplicationParamater`을 사용합니다.

    사용자의 편의상 응용 프로그램과 함께 Deploy.ps1 스크립트를 제공했습니다. 이를 사용하려면:

    - `Connect-ServiceFabricCluster`을 사용하여 Service Fabric 클러스터에 연결합니다.
    - 적절한 `ApplicationParameter` 값을 사용하여 Deploy.ps1 PowerShell 스크립트를 실행합니다.

> [!NOTE]
> 동일한 디렉터리에서 스크립트 및 응용 프로그램 폴더 PatchOrchestrationApplication을 유지합니다.

## <a name="steps-to-upgrade-the-application"></a>응용 프로그램을 업그레이드하는 단계

PowerShell을 사용하여 기존 패치 오케스트레이션 응용 프로그램을 업그레이드하려면 [여기](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)에 언급된 응용 프로그램 업그레이드 단계를 참조하세요.

응용 프로그램의 응용 프로그램 매개 변수를 변경하려면 기존 응용 프로그램 버전을 사용하여 cmdlet `Start-ServiceFabricApplicationUpgrade`에 `ApplicationParamater`을 제공합니다.

## <a name="steps-to-remove-the-application"></a>응용 프로그램을 제거하는 단계

응용 프로그램을 제거하려면 [여기](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)에 언급한 단계를 참조하세요.

사용자의 편의상 응용 프로그램과 함께 Undeploy.ps1 스크립트를 제공했습니다. 이를 사용하려면:
    - ```Connect-ServiceFabricCluster```을 사용하여 Service Fabric 클러스터에 연결합니다.
    - Undeploy.ps1 PowerShell 스크립트를 실행합니다.

> [!NOTE]
> 동일한 디렉터리에서 스크립트 및 응용 프로그램 폴더 PatchOrchestrationApplication을 유지합니다.

## <a name="viewing-the-windows-update-results"></a>Windows Update 결과 보기

패치 오케스트레이션 응용 프로그램은 사용자에게 기록 결과를 표시하는 REST API를 노출합니다.

클러스터를 기록한 다음 코디네이터 서비스의 기본 데이터베이스에 대한 복제 주소를 파악하고 브라우저에서 URL에 도달하여 Windows Update 결과를 쿼리할 수 있습니다.
http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults CoordinatorService의 REST 끝점에는 Service Fabric 탐색기를 참조하는 정확한 URL을 확인하는 동적 포트가 있습니다.
예: 아래 예제에서 결과는 `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`
![REST 끝점의 이미지](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)에서 사용할 수 있습니다.


클러스터에서 역방향 프록시를 사용하는 경우 사용자는 클러스터 외부에서 URL에 액세스할 수 있습니다.
끝점은 http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults를 적중해야 합니다. 역방향 프록시는 [여기](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)에 있는 단계를 따라 클러스터에서 사용할 수 있습니다. 

> 
> [!WARNING]
> 역방향 프록시를 구성하면 HTTP 끝점을 표시하는 클러스터의 모든 마이크로 서비스의 주소를 클러스터 외부에서 지정할 수 있습니다.

## <a name="diagnostics--health-events"></a>진단/상태 이벤트

### <a name="collecting-patch-orchestration-application-logs"></a>패치 오케스트레이션 응용 프로그램 로그 수집

패치 오케스트레이션 응용 프로그램 로그는 몇 일 이내에 Service Fabric 로그의 일부로 수집됩니다.
다음 방법 중 하나를 사용하여 시간 로그를 수집할 수 있을 때까지

#### <a name="locally-on-each-node"></a>각 노드에 로컬로

로그는 각 Service Fabric 클러스터 노드에 로컬로 수집됩니다. 로그에 액세스하는 위치는 \[Service Fabric\_Installation\_Drive\]:\\PatchOrchestrationApplication\\logs입니다.

예: Service Fabric이 "D" 드라이브에 설치된 경우 경로는 D:\\PatchOrchestrationApplication\\logs입니다.

#### <a name="central-location"></a>중앙 위치

WAD가 필수 구성 요소 단계의 일부로 구성된 경우 Azure Storage에서 사용할 수 있는 패치 오케스트레이션 응용 프로그램의 로그를 사용할 수 있습니다.

### <a name="health-reports"></a>상태 보고서

패치 오케스트레이션 응용 프로그램은 다음과 같은 경우에도 CoordinatorService 또는 NodeAgentService에 대한 상태 보고서를 게시합니다.

#### <a name="windows-update-operation-failed"></a>Windows Update 작업이 실패했습니다

노드에서 Windows Update 작업이 실패하면 NodeAgentService에 대한 상태 보고서가 생성됩니다.
상태 보고서의 세부 정보에는 문제가 있는 노드 이름이 포함됩니다.

문제가 있는 노드에서 패치 작업이 성공적으로 완료되면 보고서가 자동으로 지워집니다.

#### <a name="node-agent-ntservice-is-down"></a>노드 에이전트 NTService가 중단되었습니다.

NodeAgentNTService가 노드에서 중단된 경우 NodeAgentService에 대한 경고 수준 상태 보고서가 생성됩니다.

#### <a name="repair-manager-is-not-enabled"></a>복구 관리자가 설정되지 않습니다.

복구 관리자 서비스가 클러스터에 없는 경우 CoordinatorService에 대한 경고 수준 상태 보고서가 생성됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답:

Q. **패치 오케스트레이션 응용 프로그램을 실행하는 경우 오류 상태인 클러스터가 표시됩니다**.

A. 패치 오케스트레이션 응용 프로그램은 설치 과정에서 노드를 비활성화하거나 다시 시작하여 클러스터의 상태가 일시적으로 중단될 수 있습니다.

응용 프로그램의 정책에 따라 패치 작업 중에 하나의 노드가 중단되거나 동시에 전체 업그레이드 도메인이 중단될 수 있습니다.

Windows Update 설치가 끝날 때까지 노드는 다시 사용 이후 다시 활성화됩니다.

예: 아래의 경우에 클러스터는 2개의 노드 중단으로 인해 일시적으로 오류 상태가 되고 MaxPercentageUnhealthNodes 정책이 위반됩니다. 패치 작업이 진행될 때까지 임시 오류입니다.

![비정상 클러스터의 이미지](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

문제가 계속되는 경우 문제 해결 섹션을 참조하세요.

Q. **독립 실행형 클러스터에 패치 오케스트레이션 응용 프로그램을 사용할 수 있나요?**

A. 아직 아니요. 독립 실행형 클러스터에 대한 지원이 곧 제공됩니다.

Q. **패치 오케스트레이션 응용 프로그램이 경고 상태입니다.**

A. 응용 프로그램에 대해 게시된 상태 보고서가 근본 원인인지 확인합니다. 일반적으로 경고는 문제의 세부 정보를 포함합니다.
응용 프로그램은 문제가 일시적인 경우에 이 상태에서 자동으로 복구됩니다.

Q. **클러스터가 정상이 아닙니다. 급하게 OS 업데이트를 수행해야 합니다.**

A. 클러스터 상태가 정상이 아닌 경우 패치 오케스트레이션 응용 프로그램은 업데이트를 설치하지 않습니다.
패치 오케스트레이션 응용 프로그램 워크플로의 차단을 해제하기 위해 클러스터를 정상 상태로 전환하려고 합니다.

Q. **클러스터에 패치를 실행하는 데 시간이 너무 오래 걸리는 이유는 무엇인가요?**

A. 패치 오케스트레이션 응용 프로그램에서 소요된 시간은 대개 다음과 같은 요인에 종속됩니다.

- CoordinatorService의 정책 - `NodeWise`의 기본 정책은 특히 더 큰 클러스터의 경우에 한 번에 하나의 노드만을 패치합니다. 따라서 클러스터에 걸쳐 빠르게 패치를 수행하기 위해 `UpgradeDomainWise` 정책을 사용하는 것이 좋습니다.
- 다운로드 및 설치에 사용할 수 있는 업데이트 수 - 평균 업데이트를 다운로드하고 설치하는 데 걸리는 시간은 몇 시간을 넘지 않아야 합니다.
- VM 및 네트워크 대역폭의 성능입니다.

## <a name="disclaimers"></a>고지 사항

- 패치 오케스트레이션 응용 프로그램은 사용자를 대신하여 Windows Update의 EULA를 수락합니다. 응용 프로그램의 구성에서 설정은 필요에 따라 해제될 수 있습니다.

- 패치 오케스트레이션 응용 프로그램은 사용 및 성능을 추적하기 위해 원격 분석을 수집합니다.
    응용 프로그램의 원격 분석은 Service Fabric 런타임의 원격 분석 설정을 따릅니다(기본적으로 설정됨).

## <a name="troubleshooting-help"></a>문제 해결 도움말

### <a name="node-not-coming-back-to-up-state"></a>노드가 활성 상태로 전환되지 않음

**노드는 비활성화 상태로 멈출 수 있습니다** 안전 검사 보류로 인해 작업에 예약된 노드를 비활성화할 수 없을 때 발생할 수 있습니다. 이 문제를 해결하려면 정상 상태에서 충분히 노드를 사용할 수 있는지 확인합니다.

**노드는 다음과 같은 이유로 인해 비활성화 상태로 멈출 수 있습니다.**

- 노드가 수동으로 비활성화되었습니다.
- 노드가 진행 중인 Azure 인프라 작업으로 인해 비활성화되었습니다.
- 노드가 노드를 패치하는 패치 오케스트레이션 응용 프로그램에 의해 일시적으로 비활성화되었습니다.

**노드는 다음과 같은 이유로 인해 중단 상태로 멈출 수 있습니다.**

- 노드가 수동으로 중단되었습니다.
- 노드가 다시 시작하는 중입니다(패치 오케스트레이션 응용 프로그램에 의해 트리거될 수 있음).
- 노드가 결함이 있는 VM/컴퓨터 또는 네트워크 연결 문제로 인해 중단되었습니다.

### <a name="updates-were-skipped-on-some-nodes"></a>일부 노드에서 업데이트를 건너뛰었습니다.

패치 오케스트레이션 응용 프로그램은 다시 예약 정책에 따라 Windows Update를 설치하려고 합니다. 서비스는 노드를 복구하고 응용 프로그램 정책에 따라 업데이트를 건너뛰려고 합니다.

이러한 경우에 노드 에이전트 서비스에 대한 경고 수준 상태 보고서가 생성됩니다.
또한 Windows Update에 대한 결과가 가능한 실패 원인을 포함합니다.

### <a name="health-of-the-cluster-goes-to-error-while-update-was-getting-installed"></a>업데이트가 설치되는 동안 클러스터의 상태는 오류로 전환됩니다.

이 경우에 잘못된 Windows Update로 인해 특정 노드 또는 업그레이드 도메인에서 응용 프로그램/클러스터의 상태가 중단되고 클러스터가 다시 정상이 될 때까지 패치 오케스트레이션 응용 프로그램은 후속 Windows Update 작업을 진행하지 않습니다.

관리자가 개입하여 Windows Update가 응용 프로그램/클러스터의 상태에 문제를 일으키는 이유를 이해해야 합니다.

