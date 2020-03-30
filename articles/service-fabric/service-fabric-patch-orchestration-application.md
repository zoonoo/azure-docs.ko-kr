---
title: Service Fabric 클러스터에서 Windows 운영 체제 패치
description: 이 문서에서는 패치 오케스트레이션 응용 프로그램을 사용하여 서비스 패브릭 클러스터의 운영 체제 패치를 자동화하는 방법에 대해 설명합니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: 857a4da0b24d600ecc572933af578e2e8faf501a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366320"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Service Fabric 클러스터에서 Windows 운영 체제 패치

> 
> [!IMPORTANT]
> 2019년 4월 30일부터 패치 오케스트레이션 응용 프로그램 버전 1.2.*는 더 이상 지원되지 않습니다. 최신 버전으로 업그레이드해야 합니다.

> [!NOTE]
> [가상 시스템 규모 집합에서 자동 OS 이미지 업그레이드를](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) 얻는 것은 운영 체제를 Azure에 패치하는 데 가장 좋은 방법입니다. 가상 머신 스케일 세트 기반 자동 OS 이미지 업그레이드를 수행하려면 스케일 세트에서 실버 또는 더 큰 내구성이 필요합니다.
>

 POA(패치 오케스트레이션 응용 프로그램)는 Azure 서비스 패브릭 복구 관리자 서비스의 래퍼로, Azure 호스팅되지 않은 클러스터에 대한 구성 기반 OS 패치 일정을 사용할 수 있습니다. POA는 Azure 호스팅되지 않는 클러스터에 필요하지 않지만 업데이트 도메인에 의한 패치 설치 를 예약하는 것은 가동 중지 시간을 발생하지 않고 Service Fabric 클러스터 호스트를 패치하는 데 필요합니다.

POA는 가동 중지 시간을 발생하지 않고 서비스 패브릭 클러스터의 운영 체제 패치를 자동화하는 서비스 패브릭 응용 프로그램입니다.

POA는 다음과 같은 기능을 제공합니다.

- **자동 운영 체제 업데이트 설치**. 운영 체제 업데이트가 자동으로 다운로드되고 설치됩니다. 클러스터 노드는 클러스터 가동 중지 시간을 발생하지 않고 필요에 따라 재부팅됩니다.

- **클러스터 인식 패치 및 상태 통합**. POA가 업데이트를 적용하는 동안 클러스터 노드의 상태를 모니터링합니다. 클러스터 노드는 한 번에 하나의 노드 또는 한 번에 하나의 업데이트 도메인으로 업데이트됩니다. 패치 프로세스로 인해 클러스터의 상태가 다운되면 패치가 중지되어 문제가 악화되지 않도록 합니다.

## <a name="internal-details-of-poa"></a>POA의 내부 세부 정보

POA는 다음과 같은 하위 구성 요소로 구성됩니다.

- **코디네이터 서비스**: 이 상태 저장 서비스는 다음과 같은 역할을 합니다.
    - 전체 클러스터에서 Windows Update 작업을 조정합니다.
    - 완료된 Windows 업데이트 작업의 결과를 저장합니다.

- **노드 에이전트 서비스**: 이 상태 비저장 서비스는 모든 Service Fabric 클러스터 노드에서 실행됩니다. 서비스는 다음과 같은 역할을 합니다.
    - 노드 에이전트 NTService의 부트스트랩
    - 노드 에이전트 NTService의 모니터링

- **노드 에이전트 NTService**: 이 Windows NT 서비스는 더 높은 수준의 권한(시스템)에서 실행됩니다. 반면, 노드 에이전트 서비스 및 코디네이터 서비스는 하위 수준의 권한(네트워크 서비스)에서 실행됩니다. 서비스는 모든 클러스터 노드에서 다음과 같은 Windows 업데이트 작업을 수행하는 일을 담당합니다.
    - 노드에서 자동 Windows 업데이트를 사용하지 않도록 설정합니다.
    - 사용자가 제공한 정책에 따라 Windows 업데이트를 다운로드하고 설치합니다.
    - Windows 이후 컴퓨터를 다시 시작하면 설치가 업데이트됩니다.
    - 코디네이터 서비스에 Windows 업데이트 결과 업로드
    - 모든 재시도를 모두 수행한 후 작업이 실패한 경우 상태 보고서를 보고합니다.

> [!NOTE]
> POA는 서비스 패브릭 복구 관리자 서비스를 사용하여 노드를 비활성화 하거나 활성화하고 상태 확인을 수행합니다. POA에서 만든 복구 작업은 각 노드의 Windows 업데이트 진행률을 추적합니다.

## <a name="prerequisites"></a>사전 요구 사항

> [!NOTE]
> 필요한 최소 .NET 프레임워크 버전은 4.6입니다.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>복구 관리자 서비스 활성화(아직 실행 중이 아닌 경우)

POA에서는 클러스터에서 복구 관리자 서비스를 사용하도록 설정해야 합니다.

#### <a name="azure-clusters"></a>Azure 클러스터

실버 내구성 계층의 Azure 클러스터에는 기본적으로 복구 관리자 서비스가 활성화되어 있습니다. 골드 내구성 계층의 Azure 클러스터는 해당 클러스터가 생성된 시기에 따라 복구 관리자 서비스를 사용하도록 설정하거나 사용하지 않을 수 있습니다. 기본적으로 브론즈 내구성 계층의 Azure 클러스터에는 복구 관리자 서비스가 활성화되어 있지 않습니다. 서비스가 이미 활성화되어 있는 경우 서비스 패브릭 탐색기의 시스템 서비스 섹션에서 서비스가 실행되고 있는 것을 볼 수 있습니다.

##### <a name="the-azure-portal"></a>Azure 포털
클러스터를 설정할 때 Azure 포털에서 복구 관리자를 활성화할 수 있습니다. 클러스터를 구성할 때 **추가 기능에서** **수리 관리자 포함** 옵션을 선택합니다.

![Azure 포털에서 복구 관리자를 사용하도록 설정하는 이미지](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Azure 리소스 관리자 배포 모델
또는 [Azure 리소스 관리자 배포 모델을](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) 사용하여 새 서비스 패브릭 클러스터및 기존 서비스 패브릭 클러스터에서 복구 관리자 서비스를 사용하도록 설정할 수 있습니다. 배포하려는 클러스터에 대한 템플릿을 가져옵니다. 예제 템플릿을 사용하거나 사용자 지정 Azure Resource Manager 배포 모델을 만들 수 있습니다. 

[Azure 리소스 관리자 배포 모델 템플릿을](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)사용하여 복구 관리자 서비스를 사용하려면 다음을 수행합니다.

1. `apiVersion` *Microsoft.ServiceFabric/클러스터* 리소스에 대한 *2017-07-01 미리 보기로* 설정되어 있는지 확인합니다. 다른 경우 `apiVersion` *2017-07-01-미리 보기* 또는 이후로 업데이트해야 합니다.

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. 다음 섹션 다음에 다음 `addonFeatures` 섹션을 추가하여 복구 관리자 서비스를 활성화합니다. `fabricSettings`

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 이러한 변경 내용으로 클러스터 템플릿을 업데이트한 후 적용하고 업데이트를 완료합니다. 이제 클러스터에서 실행 중인 복구 관리자 서비스를 볼 수 있습니다. 서비스 패브릭 탐색기의 시스템 서비스 섹션에서 *패브릭:/시스템/RepairManagerService라고* 합니다. 

### <a name="standalone-on-premises-clusters"></a>독립 실행형 온-프레미스 클러스터

새 또는 기존 서비스 패브릭 클러스터에서 복구 관리자 서비스를 사용하려면 [독립 실행형 Windows 클러스터에 대한 구성 설정을](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)사용할 수 있습니다.

복구 관리자 서비스를 사용하려면 다음을 수행하십시오.

1. 여기에 표시된 `apiVersion` 대로 [일반 클러스터 구성이](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) *04-2017* 이상으로 설정되어 있는지 확인합니다.

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. 여기에 표시된 대로 `addonFeatures` `fabricSettings` 섹션 다음에 다음 섹션을 추가하여 복구 관리자 서비스를 활성화합니다.

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. 업데이트된 클러스터 매니페스트를 사용하여 이러한 변경 내용으로 클러스터 [매니페스트를 업데이트하여 새 클러스터를 만들거나](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) [클러스터 구성을 업그레이드합니다.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server) 

   클러스터가 업데이트된 클러스터 매니페스트로 실행되면 클러스터에서 실행 중인 복구 관리자 서비스를 볼 수 있습니다. *패브릭:/시스템/RepairManagerService라고*하며 서비스 패브릭 탐색기의 시스템 서비스 섹션에 있습니다.

### <a name="configure-windows-updates-for-all-nodes"></a>모든 노드에 대한 Windows 업데이트 구성

여러 클러스터 노드를 동시에 다시 시작할 수 있으므로 자동 Windows 업데이트로 인해 가용성이 손실될 수 있습니다. POA는 기본적으로 각 클러스터 노드에서 자동 Windows 업데이트를 사용하지 않도록 설정하려고 시도합니다. 그러나 관리자 또는 그룹 정책에 의해 설정을 관리하는 경우 Windows 업데이트 정책을 명시적으로 "다운로드 하기 전에 알림"으로 설정하는 것이 좋습니다.

## <a name="download-the-application-package"></a>응용 프로그램 패키지 다운로드

응용 프로그램 패키지를 다운로드하려면 GitHub의 [패치 오케스트레이션 응용 프로그램 릴리스 페이지로](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) 이동하십시오.

## <a name="configure-poa-behavior"></a>POA 동작 구성

필요에 맞게 POA 동작을 구성할 수 있습니다. 응용 프로그램을 만들거나 업데이트하는 동안 응용 프로그램 매개 변수를 전달하여 기본값을 재정의합니다. 또는 cmdlet에 지정하여 `ApplicationParameter` 응용 프로그램 매개 변수를 제공할 수 있습니다. `New-ServiceFabricApplication` `Start-ServiceFabricApplicationUpgrade`

| 매개 변수        | Type                          | 세부 정보 |
|:-|-|-|
|MaxResultsToCache    |long                              | 캐시해야 하는 최대 Windows 업데이트 결과 수입니다. <br><br>기본값은 3000입니다. <br> &nbsp;&nbsp;- 노드 수는 20입니다. <br> &nbsp;&nbsp;- 매월 노드에 대한 업데이트 수는 5입니다. <br> &nbsp;&nbsp;- 작업당 결과 수는 10개일 수 있습니다. <br> &nbsp;&nbsp;- 지난 3 개월 동안의 결과를 저장해야합니다. |
|TaskApprovalPolicy   |열거형 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy는 Service Fabric 클러스터 노드에서 Windows 업데이트를 설치하기 위해 코디네이터 서비스에서 사용하는 정책을 나타냅니다.<br><br>허용되는 값은 다음과 같습니다. <br>*노드와이즈*: Windows 업데이트는 한 번에 하나의 노드를 설치합니다. <br> *UpgradeDomainWise*: Windows 업데이트는 한 번에 하나의 업데이트 도메인을 설치합니다. (대부분의 경우 업데이트 도메인에 속한 모든 노드는 Windows 업데이트를 위해 갈 수 있습니다.)<br><br> 클러스터에 가장 적합한 정책을 결정하는 데 도움이 되는 경우 [FAQ](#frequently-asked-questions) 섹션을 참조하세요.
|LogsDiskQuotaInMB   |long  <br> (기본값: *1024)*               | 노드에서 로컬로 유지될 수 있는 MB의 패치 오케스트레이션 앱 로그의 최대 크기입니다.
| WUQuery               | 문자열<br>(기본값: *설치없음=0)*                | Windows 업데이트를 가져올 쿼리입니다. 자세한 내용은 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)를 참조하세요.
| InstallWindowsOSOnlyUpdates | *부울* <br> (기본값: false)                 | 이 플래그를 사용하여 다운로드하고 설치해야 하는 업데이트를 제어합니다. 다음 값이 허용됩니다. <br>true - Windows 운영 체제 업데이트만 설치합니다.<br>false - 컴퓨터에서 사용 가능한 모든 업데이트를 설치합니다.          |
| WUOperationTimeOutInMinutes | Int <br>(기본값: *90)*                   | Windows 업데이트 작업에 대한 시간 제한을 지정합니다(검색, 다운로드 또는 설치). 지정된 시간 제한 내에 작업이 완료되지 않으면 중단됩니다.       |
| WURescheduleCount     | Int <br> (기본값: *5)*                  | 작업이 지속적으로 실패하는 경우 서비스가 Windows 업데이트를 다시 예약하는 최대 횟수입니다.          |
| WURescheduleTimeInMinutes | Int <br>(기본값: *30)* | 오류가 지속되면 서비스가 Windows 업데이트를 다시 예약하는 간격입니다. |
| WUFrequency           | 쉼표로 구분된 문자열(기본값: *주간, 수요일, 7:00:00:00)*     | Windows 업데이트를 설치하는 빈도입니다. 형식 및 가능한 값은 다음과 같습니다. <br>&nbsp;&nbsp;- 월간 : DD, HH : MM : SS (예 : *월별, 5,12:22:32)*<br>필드 DD(일)에 허용되는 값은 1부터 28까지의 숫자와 "마지막"입니다. <br> &nbsp;&nbsp;- 주간, 일, HH : MM : SS (예 : *주간, 화요일, 12:22:32)*  <br> &nbsp;&nbsp;- 매일, HH : MM : SS (예를 들어, *매일, 12:22:32)*  <br> &nbsp;&nbsp;-  *없음은* Windows 업데이트를 수행해서는 안 된다는 것을 나타냅니다.  <br><br> 시간은 UTC에 있습니다.|
| AcceptWindowsUpdateEula | 부울 <br>(기본값: *true)* | 이 플래그를 설정하면 애플리케이션이 컴퓨터의 소유자를 대신하여 Windows 업데이트에 대한 최종 사용자 사용권 계약에 동의합니다.              |

> [!TIP]
> Windows 업데이트를 즉시 발생하도록 하려면 `WUFrequency` 응용 프로그램 배포 시간을 기준으로 설정합니다. 예를 들어 5노드 테스트 클러스터가 있고 약 5PM UTC에 앱을 배포할 계획이라고 가정할 수 있습니다. 응용 프로그램 업그레이드 또는 배포에 최대 30분이 걸린다고 가정하는 경우 WUFrequency를 *17:30:00으로*설정합니다.

## <a name="deploy-poa"></a>POA 배포

1. 모든 필수 구성 요소 단계를 완료하여 클러스터를 준비합니다.
1. 다른 서비스 패브릭 앱과 마찬가지로 POA를 배포합니다. PowerShell을 사용하여 배포하려면 [PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)을 사용하여 응용 프로그램 배포 및 제거를 참조하십시오.
1. 배포 시 애플리케이션을 구성하려면 `ApplicationParameter`를 `New-ServiceFabricApplication` cmdlet에 전달합니다. 편의를 위해 Deploy.ps1 스크립트가 애플리케이션과 함께 제공됩니다. 스크립트를 사용하려면 다음을 수행합니다.

    - `Connect-ServiceFabricCluster`를 사용하여 Service Fabric 클러스터에 연결합니다.
    - 적절한 `ApplicationParameter` 값을 사용하여 PowerShell 스크립트 Deploy.ps1을 실행합니다.

> [!NOTE]
> 스크립트 와 응용 프로그램 *폴더를 유지PatchOrchestration응용* 동일한 디렉토리에 응용 프로그램입니다.

## <a name="upgrade-poa"></a>POA 업그레이드

PowerShell을 사용하여 POA 버전을 업그레이드하려면 PowerShell 을 [사용하여 서비스 패브릭 응용 프로그램 업그레이드의](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)지침을 따르십시오.

## <a name="remove-poa"></a>POA 제거

응용 프로그램을 제거하려면 PowerShell 을 [사용하여 응용 프로그램 배포 및 제거의](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)지침에 따라

사용자의 편의를 위해 응용 프로그램과 함께 Undeploy.ps1 스크립트를 제공했습니다. 스크립트를 사용하려면 다음을 수행합니다.

  - ```Connect-ServiceFabricCluster```를 사용하여 Service Fabric 클러스터에 연결합니다.
  - PowerShell 스크립트 Undeploy.ps1을 실행합니다.

> [!NOTE]
> 스크립트 와 응용 프로그램 *폴더를 유지PatchOrchestration응용* 동일한 디렉토리에 응용 프로그램입니다.

## <a name="view-the-windows-update-results"></a>Windows 업데이트 결과 보기

POA는 REST API를 노출하여 사용자에게 기록 결과를 표시합니다. JSON 결과의 예는 다음과 같습니다.

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

JSON 필드는 다음 표에 설명되어 있습니다.

필드 | 값 | 세부 정보
-- | -- | --
OperationResult | 0 - 성공<br> 1 - 성공하였으나 오류 발생<br> 2 - 실패<br> 3 - 중단<br> 4 - 시간 제한으로 중단 | 일반적으로 하나 이상의 업데이트를 설치하는 전체 작업의 결과를 나타냅니다.
ResultCode | OperationResult와 동일 | 이 필드는 개별 업데이트에 대한 설치 작업의 결과를 나타냅니다.
OperationType | 1 - 설치<br> 0 - 검색 및 다운로드| 기본적으로 설치는 결과에 표시되는 유일한 OperationType입니다.
WindowsUpdateQuery | 기본값은 “IsInstalled = 0”입니다. | 업데이트를 검색하는 데 사용된 Windows 업데이트 쿼리입니다. 자세한 내용은 [Wu쿼리](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)를 참조하십시오.
RebootRequired | true - 다시 부팅 필요<br> false - 다시 부팅 필요 없음 | 업데이트 설치를 완료하는 데 재부팅이 필요한지 여부를 나타냅니다.
작업 시작 시간 | DateTime | 작업(다운로드/설치)이 시작된 시간을 나타냅니다.
작업 시간 | DateTime | 작업(다운로드/설치)이 완료된 시간을 나타냅니다.
HResult | 0 - 성공<br> 기타 - 실패| updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6"와 Windows 업데이트의 실패에 대한 이유를 나타냅니다.

업데이트가 아직 예약되어 있지 않으면 결과 JSON은 비어 있습니다.

클러스터에 로그인하여 Windows 업데이트 결과를 쿼리합니다. 코디네이터 서비스의 기본 주소에 대 한 복제 IP 주소를 찾아 브라우저에서 다음&lt;URL을&gt;&lt;엽니다: http:// 복제-IP : 응용 프로그램 포트&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

코디네이터 서비스의 REST 엔드포인트에는 동적 포트가 있습니다. 정확한 URL을 확인하려면 서비스 패브릭 탐색기를 참조하십시오. 예를 들어 결과는 에서 *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults*사용할 수 있습니다.

![REST 끝점 이미지](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

클러스터에서 역방향 프록시를 사용하도록 설정하면 클러스터 외부에서도 URL에 액세스할 수 있습니다.

당신이 칠 필요가 끝점은 *http://&lt;SERVERURL입니다&gt;&lt;:&gt;REVERSEPROXYPORT / 패치 오케스트라 응용 프로그램 / 코디네이터 서비스 / v1 / GetWindowsUpdateResults*.

클러스터에서 역방향 프록시를 사용하려면 [Azure 서비스 패브릭의 역방향 프록시의](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)지침을 따릅니다. 

> 
> [!WARNING]
> 역방향 프록시가 구성된 후 HTTP 끝점을 노출하는 클러스터의 모든 마이크로 서비스는 클러스터 외부에서 주소를 지정할 수 있습니다.

## <a name="diagnostics-and-health-events"></a>진단 및 상태 이벤트

이 섹션에서는 서비스 패브릭 클러스터의 POA를 통해 패치 업데이트문제를 디버깅하거나 진단하는 방법에 대해 설명합니다.

> [!NOTE]
> 다음 호출 된 자체 진단 개선 사항 중 많은 부분을 얻으려면 POA 버전 1.4.0 이상에 설치되어 있어야 합니다.

노드 에이전트 NTService는 노드에 업데이트를 설치하기 위한 [복구 작업을](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) 만듭니다. 그런 다음 각 작업은 작업 승인 정책에 따라 코디네이터 서비스에서 준비합니다. 마지막으로 준비된 작업은 복구 관리자에 의해 승인되며 클러스터가 비정상 상태인 경우 작업을 승인하지 않습니다. 

노드에서 업데이트가 진행되는 방식을 이해하기 위해 단계별로 살펴보겠습니다.

1. 모든 노드에서 실행되는 NodeAgentNTService는 예약된 시간에 사용 가능한 Windows 업데이트를 찾습니다. 업데이트를 사용할 수 있는 경우 노드에서 다운로드합니다.

1. 업데이트를 다운로드한 후 노드 에이전트 NTService는 *>\< *unique_id POS___ 이름으로 노드에 대한 해당 복구 작업을 만듭니다. [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) cmdlet을 사용하거나 노드 세부 정보 섹션에서 SFX를 사용하여 이러한 복구 작업을 볼 수 있습니다. 복구 작업이 만들어지면 [ *클레임* 상태로](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet)빠르게 이동합니다.

1. 코디네이터 서비스는 주기적으로 *클레임* 상태의 복구 작업을 찾은 다음 TaskApprovalPolicy를 기반으로 *준비* 상태로 업데이트합니다. TaskApprovalPolicy가 NodeWise로 구성된 경우 노드에 해당하는 복구 작업은 현재 *준비,* *승인됨,* *실행*중 또는 복원 상태에 있는 다른 복구 작업이 없는 경우에만 *준비됩니다.* 

   마찬가지로 UpgradeWise 작업승인정책의 경우 동일한 업데이트 도메인에 속한 노드에 대해서만 이전 상태에 작업이 있습니다. 복구 작업을 *준비* 상태로 이동한 후 의도 집합을 *다시 시작하도록*설정된 해당 서비스 패브릭 노드가 [비활성화됩니다.](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps)

   POA 버전 1.4.0 이후 는 코디네이터 서비스에서 ClusterPatchingStatus 속성을 사용하여 이벤트를 게시하여 패치중인 노드를 표시합니다. 업데이트는 다음 이미지와 같이 _poanode_0 설치됩니다.

    [![클러스터 패치 상태 이미지](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. 노드를 사용하지 않도록 설정하면 복구 작업이 *실행* 상태로 이동됩니다. 
   
   > [!NOTE]
   > *비활성화* 된 상태에 갇혀 있는 노드는 새 복구 작업을 차단할 수 있으며, 이로 인해 클러스터의 패치 작업이 중단됩니다.

1. 복구 작업이 *실행* 상태인 경우 해당 노드의 패치 설치가 시작됩니다. 패치를 설치한 후 패치에 따라 노드가 다시 시작될 수도 있습니다. 다음으로 복구 작업이 *노드를* 다시 활성화하는 복원 상태로 이동합니다. 그러면 복구 작업이 완료된 것으로 표시됩니다.

   POA 버전 1.4.0 이상에서는 WUOperationStatus-\<NodeName> 속성을 사용하는 NodeAgentService의 상태 이벤트를 확인하여 업데이트 상태를 찾을 수 있습니다. 다음 이미지의 강조 표시된 섹션은 *노드 poanode_0* 및 *poanode_2*Windows 업데이트 상태를 보여 준다.

   [![Windows 업데이트 작업 상태 이미지](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Windows 업데이트 작업 상태 이미지](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   PowerShell을 사용하여 세부 정보를 얻을 수도 있습니다. 이렇게 하려면 클러스터에 연결 하 고 [Get-ServiceFabricRepairTask를](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps)사용 하 여 복구 작업의 상태를 가져옵니다. 
   
   다음 예제에서 "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" 작업은 *다운로드완료* 상태입니다. 즉, *poanode_2* 노드에서 업데이트가 다운로드되었으며 작업이 *실행* 상태로 이동할 때 설치가 시도됩니다.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   더 많은 문제가 남아 있는 경우 가상 컴퓨터(VM) 또는 VM에 로그인하고 Windows 이벤트 로그를 사용하여 해당 문제에 대해 알아봅니다. 앞에서 언급한 복구 작업은 다음 실행기 하위 상태에만 존재할 수 있습니다.

      실행기서브스테이트 | 설명
    -- | -- 
      없음=1 |  노드에 지속적인 작업이 없었음이 있음을 의미합니다. 상태가 전환 중일 수 있습니다.
      다운로드 완료=2 | 다운로드 작업이 성공, 부분 실패 또는 실패로 완료되었다는 것을 의미합니다.
      설치승인=3 | 다운로드 작업이 이전에 완료되었고 수리 관리자가 설치를 승인했다는 의미입니다.
      설치진행 중=4 | 복구 작업의 실행 상태에 해당합니다.
      설치 완료=5 | 설치가 성공, 부분 적 성공 또는 실패로 완료되었다는 것을 의미합니다.
      다시 시작 요청=6 | 즉, 패치 설치가 완료되었으며 노드에 보류 중인 다시 시작 작업이 있음을 의미합니다.
      다시 시작Not필요=7 |  패치 설치가 완료된 후 다시 시작할 필요가 없다는 의미입니다.
      다시 시작 완료=8 | 다시 시작이 성공적으로 완료되었다는 의미입니다.
      작업 완료=9 | Windows 업데이트 작업이 성공적으로 완료되었습니다.
      운영중단=10 | Windows 업데이트 작업이 중단되었다는 의미입니다.

1. POA 버전 1.4.0 이상에서 노드 업데이트 시도가 완료되면 "WUOperationStatus-[NodeName]" 속성이 있는 이벤트가 NodeAgentService에 게시되어 Windows 업데이트를 다운로드하고 설치하려는 다음 시도가 시작될 때 이를 알려줍니다. 이 이미지는 다음과 같은 이미지에 표시됩니다.

     [![Windows 업데이트 작업 상태 이미지](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>진단 로그

패치 오케스트레이션 응용 프로그램 로그는 서비스 패브릭 런타임 로그의 일부로 수집됩니다.

원하는 진단 도구 또는 파이프라인을 사용하여 로그를 캡처할 수 있습니다. POA는 다음과 같은 고정 공급자 ID를 사용하여 [이벤트 소스를](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)통해 이벤트를 기록합니다.

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>상태 보고서

POA는 또한 다음 시나리오에서 노드 에이전트 서비스 또는 코디네이터 서비스에 대한 상태 보고서를 게시합니다.

* 노드 에이전트 NTService 중단

   노드에서 노드 에이전트 NTService가 중단되면 노드 에이전트 서비스에 대한 경고 수준 상태 보고서가 생성됩니다.

* 복구 관리자 서비스를 사용할 수 없습니다.

   클러스터에서 복구 관리자 서비스를 찾을 수 없는 경우 코디네이터 서비스에 대한 경고 수준 상태 보고서가 생성됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: POA가 실행 중일 때 클러스터가 오류 상태로 표시되는 이유는 무엇입니까?**

A: 설치 프로세스 중에 POA는 노드를 비활성화하거나 다시 시작하므로 일시적으로 비정상 클러스터가 발생할 수 있습니다.

응용 프로그램의 정책에 따라 패치 작업 중에 하나의 노드가 *다운되거나* 전체 업데이트 도메인이 한 번에 모두 다운될 수 있습니다.

Windows 업데이트 설치가 끝나면 노드가 다시 시작후 다시 활성화됩니다.

다음 예제에서는 두 노드가 중단되고 MaxPercentageUnhealthyNodes 정책이 위반되었으므로 클러스터가 일시적으로 오류 상태가 되었습니다. 이 오류는 패치 작업이 시작될 때까지 일시적입니다.

![비정상 클러스터의 이미지](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

문제가 계속되는 경우 문제 해결 섹션을 참조하세요.

**Q: POA가 경고 상태에 있는 경우 어떻게 해야 합니까?**

A: 응용 프로그램에 대해 게시된 상태 보고서가 근본 원인을 나타내는지 확인합니다. 일반적으로 경고에는 문제에 대한 세부 정보가 포함되어 있습니다. 일시적인 경우 응용 프로그램이 자동으로 복구됩니다.

**Q: 클러스터가 비정상이고 긴급 운영 체제 업데이트를 수행해야 하는 경우 어떻게 해야 합니까?**

A: POA는 클러스터가 비정상인 동안 업데이트를 설치하지 않습니다. POA 워크플로의 차단을 해제하려면 클러스터를 정상 상태로 유지해 보십시오.

**Q: 내 클러스터에 대해 작업 승인 정책을 "NodeWise" 또는 "UpgradeDomainWise"로 설정해야 합니까?**

A: "UpgradeDomainWise" 설정은 업데이트 도메인에 속한 모든 노드를 병렬로 패치하여 전체 클러스터 복구 속도를 높입니다. 이 프로세스 중에 전체 업데이트 도메인에 속한 노드를 사용할 수 [ *없습니다(사용 안 함* 상태).](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)

반대로 "NodeWise" 설정은 한 번에 하나의 노드만 패치하므로 전체 클러스터 패치가 더 오래 걸릴 수 있음을 의미합니다. 그러나 패치 프로세스 중에 는 *비활성화된* 상태에서는 노드를 하나만 사용할 수 없습니다.

클러스터가 패치 주기 동안 N-1 업데이트 도메인에서 실행되는 것을 허용할 수 있는 경우(N이 클러스터의 총 업데이트 도메인 수인 경우) 정책을 "UpgradeDomainWise"로 설정할 수 있습니다. 그렇지 않으면 "NodeWise"로 설정합니다.

**Q: 노드를 패치하는 데 얼마나 많은 시간이 걸리나요?**

A: 노드를 패치하는 데는 몇 분(예: [Windows Defender 정의 업데이트)에서](https://www.microsoft.com/en-us/wdsi/definitions)시간(예: Windows [누적 업데이트)까지](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)걸릴 수 있습니다. 노드를 패치하는 데 필요한 시간은 주로 다음에 따라 다릅니다. 
 - 업데이트의 크기입니다.
 - 패치 창에 적용해야 하는 업데이트 수입니다.
 - 업데이트를 설치하고, 노드를 재부팅하고(필요한 경우) 재부팅 후 설치 단계를 완료하는 데 걸리는 시간입니다.
 - VM 또는 컴퓨터의 성능 및 네트워크 조건입니다.

**Q: 전체 클러스터를 패치하는 데 얼마나 걸습니까?**

A: 전체 클러스터를 패치하는 데 필요한 시간은 다음에 따라 다릅니다.

- 노드를 패치하는 데 필요한 시간입니다.

- 코디네이터 서비스 정책 기본 정책인 "NodeWise"는 "UpgradeDomainWise"를 사용하는 것보다 느린 접근 방식인 한 번에 하나의 노드만 패치합니다. 

   예를 들어 노드를 패치하는 데 ~1시간이 걸리는 경우 5개의 업데이트 도메인이 있는 20개 노드(동일한 유형의 노드) 클러스터를 패치하는 경우 각각 4개의 노드가 포함되어 있어야 합니다.
    - "노드 와이즈"의 경우 : ~ 20 시간.
    - "업그레이드도메인와이즈"의 경우: ~5시간.

- 클러스터 로드입니다. 각 패치 작업을 수행하려면 고객 워크로드를 클러스터의 사용 가능한 다른 노드로 재배치해야 합니다. 패치되는 노드는 이 시간 동안 [ *비활성화* 상태에](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) 있을 것입니다. 클러스터가 최대 부하 에 가깝게 실행중인 경우 비활성화 프로세스는 더 오래 걸립니다. 따라서 이러한 스트레스 조건에서는 전체 패치 프로세스가 느려질 수 있습니다.

- 패치 중 클러스터 상태 오류입니다. [클러스터의 상태](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) [저하로](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) 인한 패치 프로세스가 중단됩니다. 이 문제는 전체 클러스터를 패치하는 데 필요한 전체 시간에 추가됩니다.

**Q: REST API를 통해 가져오지만 컴퓨터의 Windows 업데이트 기록 아래에는 없는 Windows 업데이트 결과에 일부 업데이트가 표시되는 이유는 무엇입니까?**

A: 일부 제품 업데이트는 자체 업데이트 또는 패치 기록에만 표시됩니다. 예를 들어 Windows Defender 업데이트는 Windows Server 2016의 Windows 업데이트 기록에 표시되거나 표시되지 않을 수 있습니다.

**Q: POA를 사용하여 개발 클러스터(1노드 클러스터)를 패치할 수 있습니까?**

A: 아니요, POA는 1노드 클러스터를 패치하는 데 사용할 수 없습니다. [서비스 패브릭 시스템 서비스](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) 또는 기타 고객 앱에 가동 중지 시간이 발생하기 때문에 이러한 제한은 의도적으로 설계되었습니다. 따라서 복구 작업을 패치하는 것은 복구 관리자의 승인을 받지 않습니다.

**Q: Linux에서 클러스터 노드를 패치하려면 어떻게 해야 합니까?**

A: Linux에서 업데이트를 오케스트레이션하는 방법에 대해 알아보려면 [Azure 가상 시스템 규모 설정 자동 OS 이미지 업그레이드를](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)참조하십시오.

**Q: 업데이트 주기가 너무 오래 걸리는 이유는 무엇입니까?**

A: 결과 JSON에 대 한 쿼리, 모든 노드에 대 한 업데이트 주기를 입력 하 고 OperationStartTime 및 작업 시간 (작업 완료 시간)를 사용 하 여 모든 노드에 업데이트 설치에 의해 걸린 시간을 찾을 하려고 할 수 있습니다. 

업데이트가 수행되지 않는 큰 시간 기간이 있는 경우 클러스터가 오류 상태일 수 있으므로 복구 관리자는 POA 복구 작업을 승인할 수 없습니다. 업데이트 설치가 모든 노드에서 시간이 오래 걸리는 경우 해당 노드가 잠시 동안 업데이트되지 않았을 수 있습니다. 많은 업데이트가 설치 보류 중일 수 있으며 이로 인해 지연이 발생할 수 있습니다. 

노드 패치가 차단된 상태 *사용 설정이* 해제되어 차단될 수도 있습니다. 일반적으로 노드를 사용하지 않도록 설정하면 쿼럼 또는 데이터 손실 상황이 발생할 수 있기 때문에 이러한 상황이 발생합니다.

**Q: POA가 패치를 할 때 노드를 사용하지 않도록 설정해야 하는 이유는 무엇입니까?**

A: POA는 *노드에서* 실행 중인 모든 서비스 패브릭 서비스를 중지하거나 재할당하는 다시 시작 의도로 노드를 비활성화합니다. POA는 응용 프로그램이 새 DLL과 이전 DLL을 혼합하여 사용하지 않도록 하기 위해 이 작업을 수행하므로 노드를 사용하지 않도록 설정하지 않고 노드를 패치하지 않는 것이 좋습니다.

**Q: POA를 사용하여 업데이트할 수 있는 최대 노드 수는 몇 개입니까?**

A: POA는 서비스 패브릭 복구 관리자를 사용하여 업데이트에 대한 노드에 대한 복구 작업을 만듭니다. 그러나 동시에 250개 이하의 복구 작업을 수행할 수 없습니다. 현재 POA는 각 노드에 대한 복구 작업을 동시에 생성하므로 POA는 클러스터에서 250개 이하의 노드를 업데이트할 수 있습니다. 

## <a name="disclaimers"></a>고지 사항

- POA는 사용자를 대신하여 Windows 업데이트에 대한 최종 사용자 사용권 계약을 수락합니다. 필요에 따라 애플리케이션 구성에서 설정을 해제할 수 있습니다.

- POA는 사용 및 성능을 추적하기 위해 원격 분석을 수집합니다. 애플리케이션의 원격 분석은 Service Fabric 런타임의 원격 분석 설정을 따릅니다(기본적으로 설정됨).

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 패치 노드 문제에 대한 가능한 문제 해결 방법을 제공합니다.

### <a name="a-node-is-not-coming-back-to-up-state"></a>노드가 활성 상태로 전환되지 않음

* 다음과 같은 이유로 노드가 *비활성화* 상태에 갇혀 있을 수 있습니다.

  - 안전 검사가 보류 중입니다. 이 문제를 해결하려면 정상 상태에서 충분히 노드를 사용할 수 있는지 확인합니다.

* 다음과 같은 이유로 노드가 *비활성화* 된 상태로 고정 될 수 있습니다.

  - 수동으로 비활성화되었습니다.
  - 진행 중인 Azure 인프라 작업으로 인해 비활성화되었습니다.
  - 노드를 패치하기 위해 POA에 의해 일시적으로 비활성화되었습니다.

* 노드는 다음과 같은 이유로 중단 상태에 멈춰 있을 수 있습니다.

  - 수동으로 다운 상태에 배치했다.
  - POA에 의해 트리거될 수 있는 다시 시작됩니다.
  - VM 또는 컴퓨터에 결함이 있거나 네트워크 연결 문제가 있습니다.

### <a name="updates-were-skipped-on-some-nodes"></a>일부 노드에서 업데이트를 건너뛰었습니다.

POA는 일정 조정 정책에 따라 Windows 업데이트를 설치하려고 시도합니다. 이 서비스는 노드를 복구하고 애플리케이션 정책에 따라 업데이트를 건너뛰려고 합니다.

이러한 경우에 노드 에이전트 서비스에 대한 경고 수준 상태 보고서가 생성됩니다. Windows 업데이트 결과에는 오류의 가능한 원인도 포함되어 있습니다.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>업데이트를 설치하는 동안 클러스터의 상태 오류가 발생합니다.

Windows 에 결함이 있는 업데이트로 인해 특정 노드 또는 업데이트 도메인에서 응용 프로그램 또는 클러스터의 상태를 감소시킬 수 있습니다. POA는 클러스터가 다시 정상이 될 때까지 이후의 Windows 업데이트 작업을 중단합니다.

관리자는 Windows 업데이트로 인해 응용 프로그램 이나 클러스터가 비정상이 된 이유를 중재해야 합니다.

## <a name="poa-release-notes"></a>POA 릴리스 노트

>[!NOTE]
> POA 버전 1.4.0 이상에 대 한 릴리스 정보 및 릴리스를 찾을 수 있습니다 GitHub에서 [패치 오케스트레이션 응용 프로그램 릴리스 페이지에서.](https://github.com/microsoft/Service-Fabric-POA/releases/)

### <a name="version-110"></a>버전 1.1.0
- 공개 릴리스

### <a name="version-111"></a>버전 1.1.1
- NodeAgentNTService의 설치를 방지하는 NodeAgentService의 SetupEntryPoint에서 버그 수정

### <a name="version-120"></a>버전 1.2.0

- 시스템 다시 시작 워크플로 관련 버그 수정
- 복구 중 상태 확인이 예상 대로 작동하지 않는 문제로 인해 RM 작업 생성의 버그 수정
- Windows 서비스 POANodeSvc의 시작 모드를 자동에서 지연 자동으로 변경했습니다.

### <a name="version-121"></a>버전 1.2.1

- 클러스터 축소 워크플로의 버그 수정입니다. 존재하지 않는 노드에 속하는 POA 복구 태스크에 대해 가비지 수집 논리가 도입되었습니다.

### <a name="version-122"></a>버전 1.2.2

- 기타 버그가 수정되었습니다.
- 이제 이진 파일이 서명됩니다.
- 애플리케이션에 대한 sfpkg 링크가 추가되었습니다.

### <a name="version-130"></a>버전 1.3.0

- 이제 InstallWindowsOSOnlyUpdates를 False로 설정하면 사용 가능한 모든 업데이트가 설치됩니다.
- 자동 업데이트를 사용하지 않도록 설정하는 논리가 변경되었습니다. 이로 인해 Server 2016 이상에서 자동 업데이트가 사용하지 않도록 설정되지 않는 버그가 수정됩니다.
- 고급 사용 사례에 대한 POA의 마이크로 서비스에 대한 매개 변수화된 배치 제약 조건입니다.

### <a name="version-131"></a>버전 1.3.1
- POA 1.3.0이 Windows Server 2012 R2 또는 이전 에서 자동 업데이트를 사용하지 않도록 설정하지 못하여 작동하지 않는 회귀를 수정합니다. 
- InstallWindowsOSOnlyUpdates 구성이 항상 True로 선택되는 버그 수정
- InstallWindowsOSOnlyUpdates의 기본값을 False로 변경

### <a name="version-132"></a>버전 1.3.2
- 현재 노드 이름의 하위 집합인 이름의 노드가 있는 경우 노드의 패치 수명 주기에 영향을 주는 문제를 해결합니다. 이러한 노드의 경우 패치가 누락되었지만 재부팅이 보류 중일 수 있습니다.
