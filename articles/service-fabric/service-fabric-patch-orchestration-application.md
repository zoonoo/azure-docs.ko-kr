---
title: Azure Service Fabric 패치 오케스트레이션 애플리케이션 | Microsoft Docs
description: Service Fabric 클러스터에 운영 체제 패치를 자동화하는 애플리케이션입니다.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: ccc0399b6ac886ec8d9ef7d207c3539f1d078070
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65951949"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Service Fabric 클러스터에서 Windows 운영 체제 패치

> 
> [!IMPORTANT]
> 응용 프로그램 버전 1.2. * 30 년 4 월 2019에 지원 되지 않는 것입니다. 최신 버전으로 업그레이드 하십시오.


[Azure 가상 머신 확장 집합 자동 OS 이미지 업그레이드](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)는 Azure에서 패치된 운영 체제를 유지하는 모범 사례이며, POA(패치 오케스트레이션 애플리케이션)는 비 Azure 호스트 클러스터에 대한 OS 패치 일정을 기반으로 구성을 사용하는 Service Fabrics RepairManager Systems 서비스에 대한 래퍼입니다. 비 Azure 호스트 클러스터의 경우 POA가 필요하지 않지만, 가동 중지 시간 없이 Service Fabric 클러스터 호스트를 패치하려면 업그레이드 도메인에 의한 패치 설치를 예약해야 합니다.

POA는 Service Fabric 클러스터에서 가동 중지 시간 없이 운영 체제 패치를 자동화하는 Azure Service Fabric 애플리케이션입니다.

패치 오케스트레이션 앱은 다음과 같은 기능을 제공합니다.

- **자동 운영 체제 업데이트 설치**. 운영 체제 업데이트가 자동으로 다운로드되고 설치됩니다. 클러스터 노드는 필요에 따라 클러스터 가동 중지 시간 없이 다시 부팅됩니다.

- **클러스터 인식 패치 및 상태 통합**. 패치 오케스트레이션 앱은 업데이트를 적용하는 동안 클러스터 노드의 상태를 모니터링합니다. 클러스터 노드는 한 번에 하나의 노드씩 또는 한 번에 하나의 업그레이드 도메인씩 업그레이드됩니다. 패치 프로세스로 인해 클러스터의 상태가 다운되는 경우 문제가 악화되는 것을 방지하기 위해 프로세스가 중지됩니다.

## <a name="internal-details-of-the-app"></a>앱 내부 세부 정보

패치 오케스트레이션 앱은 다음 하위 구성 요소로 구성됩니다.

- **코디네이터 서비스**: 이 상태 저장 서비스는 다음과 같은 역할을 합니다.
    - 전체 클러스터에서 Windows Update 작업을 조정합니다.
    - 완료된 Windows 업데이트 작업의 결과를 저장합니다.
- **노드 에이전트 서비스**: 이 상태 비저장 서비스는 모든 Service Fabric 클러스터 노드에서 실행됩니다. 서비스는 다음과 같은 역할을 합니다.
    - 노드 에이전트 NTService의 부트스트랩
    - 노드 에이전트 NTService의 모니터링
- **노드 에이전트 NTService**: 이 Windows NT 서비스는 더 높은 수준의 권한(시스템)에서 실행됩니다. 반면, 노드 에이전트 서비스 및 코디네이터 서비스는 하위 수준의 권한(네트워크 서비스)에서 실행됩니다. 서비스는 모든 클러스터 노드에서 다음과 같은 Windows 업데이트 작업을 수행하는 일을 담당합니다.
    - 노드에서 자동 Windows Update 비활성화
    - 사용자가 제공한 정책에 따라 Windows 업데이트 다운로드 및 설치
    - Windows 업데이트 설치 이후 컴퓨터 다시 시작
    - 코디네이터 서비스에 Windows 업데이트 결과 업로드
    - 모든 재시도가 끝난 다음 작업이 실패한 경우 상태 보고서 보고

> [!NOTE]
> 패치 오케스트레이션 앱은 Service Fabric 복구 관리자 시스템 서비스를 사용하여 노드를 사용하도록 또는 사용하지 않도록 설정하고 상태 검사를 수행합니다. 패치 오케스트레이션 앱에서 만든 복구 작업은 각 노드에 대한 Windows 업데이트 진행률을 추적합니다.

## <a name="prerequisites"></a>필수 조건

> [!NOTE]
> 필요한 최소.NET framework 버전은 4.6입니다.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>복구 관리자 서비스 사용(아직 실행되지 않은 경우)

패치 오케스트레이션 앱은 복구 관리자 시스템 서비스를 클러스터에서 사용하도록 설정해야 합니다.

#### <a name="azure-clusters"></a>Azure 클러스터

실버 내구성 계층의 Azure 클러스터에는 복구 관리자 서비스가 기본적으로 사용하도록 설정되어 있습니다. 골드 내구성 계층의 Azure 클러스터에는 해당 클러스터가 만들어진 시기에 따라 복구 관리자 서비스가 사용하도록 설정되어 있을 수도 있고 그렇지 않을 수도 있습니다. 브론즈 내구성 계층의 Azure 클러스터에는 복구 관리자 서비스가 기본적으로 사용하도록 설정되어 있지 않습니다. 서비스가 이미 사용하도록 설정되어 있는 경우 Service Fabric Explorer의 시스템 서비스 섹션에서 서비스가 실행되고 있는 것을 볼 수 있습니다.

##### <a name="azure-portal"></a>Azure portal
클러스터를 설정할 때 Azure Portal에서 복구 관리자를 설정할 수 있습니다. 클러스터를 구성할 때 **추가 기능**에서 **복구 관리자 포함** 옵션을 선택합니다.
![Azure Portal에서 복구 관리자 사용 이미지](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager 배포 모델
또는 [Azure Resource Manager 배포 모델](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)을 사용하여 신규 및 기존 Service Fabric 클러스터에서 복구 관리자 서비스를 사용하도록 설정할 수 있습니다. 배포하려는 클러스터에 대한 템플릿을 가져옵니다. 예제 템플릿을 사용하거나 사용자 지정 Azure Resource Manager 배포 모델을 만들 수 있습니다. 

[Azure Resource Manager 배포 모델 템플릿](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)을 사용하여 복구 관리자 서비스를 사용하도록 설정하려면:

1. 먼저 `Microsoft.ServiceFabric/clusters` 리소스에 대해 `apiversion`이 `2017-07-01-preview`로 설정되었는지 확인합니다. 값이 다르면 `apiVersion`을 `2017-07-01-preview` 값 이상으로 업데이트해야 합니다.

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 이제 아래와 같이 다음 `addonFeatures` 섹션을 `fabricSettings` 섹션 뒤에 추가하여 복구 관리자 서비스를 사용하도록 설정합니다.

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 이러한 변경 내용으로 클러스터 템플릿을 업데이트한 후 변경 내용을 적용하고 업그레이드가 완료되도록 합니다. 이제 클러스터에서 복구 관리자 시스템 서비스가 실행되고 있는 것을 볼 수 있습니다. Service Fabric Explorer의 시스템 서비스 섹션에 있는 `fabric:/System/RepairManagerService`입니다. 

### <a name="standalone-on-premises-clusters"></a>독립 실행형 온-프레미스 클러스터

[독립 실행형 Windows 클러스터에 대한 구성 설정](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)을 사용하여 신규 및 기존 Service Fabric 클러스터에서 복구 관리자 서비스를 사용하도록 설정할 수 있습니다.

복구 관리자 서비스를 사용하도록 설정하려면 다음을 수행합니다.

1. 먼저 아래와 같이 [일반 클러스터 구성](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations)의 `apiversion`이 `04-2017` 이상으로 설정되어 있는지 확인합니다.

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. 이제 아래와 같이 다음 `addonFeatures` 섹션을 `fabricSettings` 섹션 뒤에 추가하여 복구 관리자 서비스를 사용하도록 설정합니다.

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. 업데이트된 클러스터 매니페스트의 [새 클러스터 만들기](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) 또는 [클러스터 구성 업그레이드](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server)를 사용하여 이러한 변경 내용으로 클러스터 매니페스트를 업데이트합니다. 업데이트된 클러스터 매니페스트로 클러스터가 실행되면 Service Fabric Explorer의 시스템 서비스 섹션에서 `fabric:/System/RepairManagerService`라는, 클러스터에서 실행되고 있는 복구 관리자 시스템 서비스를 볼 수 있습니다.

### <a name="configure-windows-updates-for-all-nodes"></a>모든 노드에 Windows 업데이트 구성

자동 Windows 업데이트를 사용하면 여러 클러스터 노드가 동시에 다시 시작되어 가용성 손실이 발생할 수 있습니다. 패치 오케스트레이션 앱은 기본적으로 각 클러스터 노드에서 자동 Windows 업데이트를 사용하지 않으려 합니다. 그러나 설정이 관리자 또는 그룹 정책에 따라 관리되는 경우 명시적으로 Windows 업데이트 정책을 “다운로드하기 전에 알림”으로 설정하는 것이 좋습니다.

## <a name="download-the-app-package"></a>앱 패키지 다운로드

응용 프로그램 패키지를 다운로드 하려면 GitHub 릴리스를 참조 하세요 [페이지](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) 패치 오케스트레이션 응용 프로그램입니다.

## <a name="configure-the-app"></a>앱 구성

요구 사항에 맞게 패치 오케스트레이션 앱의 동작을 구성할 수 있습니다. 애플리케이션 만들기 또는 업데이트 중에 애플리케이션 매개 변수를 전달하여 기본값을 재정의합니다. 애플리케이션 매개 변수는 `Start-ServiceFabricApplicationUpgrade` 또는 `New-ServiceFabricApplication` cmdlet에 `ApplicationParameter`를 지정하여 제공될 수 있습니다.

|**매개 변수**        |**형식**                          | **세부 정보**|
|:-|-|-|
|MaxResultsToCache    |long                              | 캐시되어야 하는 Windows 업데이트 결과의 최대 수입니다. <br>기본값은 3000입니다. <br> - 노드 수는 20입니다. <br> - 매월 노드에서 발생하는 업데이트 수는 5입니다. <br> - 작업당 결과 수는 10일 수 있습니다. <br> - 지난 3개월 동안의 결과를 저장해야 합니다. |
|TaskApprovalPolicy   |열거형 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy는 Service Fabric 클러스터 노드에서 Windows 업데이트를 설치하기 위해 코디네이터 서비스에서 사용하는 정책을 나타냅니다.<br>                         허용되는 값은 다음과 같습니다. <br>                                                           <b>NodeWise</b>. Windows 업데이트가 한 번에 하나의 노드에 설치됩니다. <br>                                                           <b>UpgradeDomainWise</b>. Windows 업데이트가 한 번에 하나의 업그레이드 도메인에 설치됩니다. 최대, 한 업그레이드 도메인에 속하는 모든 노드가 Windows 업데이트에 해당될 수 있습니다.<br> 클러스터에 대한 가장 적합한 정책을 결정하는 방법은 [FAQ](#frequently-asked-questions) 섹션을 참조하세요.
|LogsDiskQuotaInMB   |long  <br> (기본값: 1024)               |패치 오케스트레이션 앱 로그의 최대 크기(MB)로, 노드에서 로컬로 유지될 수 있습니다.
| WUQuery               | 문자열<br>(기본값: "IsInstalled=0")                | Windows 업데이트를 가져올 쿼리입니다. 자세한 내용은 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)를 참조하세요.
| InstallWindowsOSOnlyUpdates | BOOLEAN <br> (기본값: false)                 | 이 플래그를 사용하여 다운로드하고 설치해야 하는 업데이트를 제어합니다. 다음 값이 허용됩니다. <br>true - Windows 운영 체제 업데이트만 설치합니다.<br>false - 컴퓨터에서 사용 가능한 모든 업데이트를 설치합니다.          |
| WUOperationTimeOutInMinutes | Int <br>(기본값: 90)                   | Windows 업데이트 작업에 대한 시간 제한을 지정합니다(검색, 다운로드 또는 설치). 지정된 시간 제한 내에 작업이 완료되지 않으면 중단됩니다.       |
| WURescheduleCount     | Int <br> (기본값: 5)                  | 작업이 영구적으로 실패하는 경우 서비스에서 Windows 업데이트를 다시 예약하는 최대 횟수입니다.          |
| WURescheduleTimeInMinutes | Int <br>(기본값: 30) | 오류가 계속 발생하는 경우 서비스에서 Windows 업데이트를 다시 예약하는 간격입니다. |
| WUFrequency           | 쉼표로 구분된 문자열(기본값: "Weekly, Wednesday, 7:00:00")     | Windows Update를 설치하는 빈도입니다. 형식 및 가능한 값은 다음과 같습니다. <br>-   매월, DD, HH:MM:SS(예: 매월, 5,12:22:32)<br>필드 DD(일)에서 허용되는 값은 1-28 범위의 숫자와 "last"입니다. <br> -   매주, DAY, HH:MM:SS(예: 매주, 화요일, 12:22:32)  <br> -   매일, HH:MM:SS(예: 매일, 12:22:32)  <br> -   [없음]은 Windows 업데이트를 수행하지 않음을 나타냅니다.  <br><br> 시간은 UTC 형식입니다.|
| AcceptWindowsUpdateEula | BOOLEAN <br>(기본값: True) | 이 플래그를 설정하면 애플리케이션이 컴퓨터의 소유자를 대신하여 Windows 업데이트에 대한 최종 사용자 사용권 계약에 동의합니다.              |

> [!TIP]
> Windows 업데이트가 즉시 처리되도록 하려면 애플리케이션 배포 시간에 관한 `WUFrequency`를 설정하세요. 예를 들어 5노드 테스트 클러스터가 있고 약 5PM UTC에 앱을 배포할 계획이라고 가정할 수 있습니다. 애플리케이션 업그레이드 또는 배포에 최대 30분이 소요된다고 가정하는 경우 WUFrequency를 "매일, 17:30:00"으로 설정하세요.

## <a name="deploy-the-app"></a>앱 배포

1. 모든 필수 구성 요소 단계를 완료하여 클러스터를 준비합니다.
2. 다른 Service Fabric 앱과 마찬가지로 패치 오케스트레이션 앱을 배포합니다. PowerShell을 사용하여 앱을 배포할 수 있습니다. [PowerShell을 사용하여 애플리케이션 배포 및 제거](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)의 단계를 따릅니다.
3. 배포 시 애플리케이션을 구성하려면 `ApplicationParameter`를 `New-ServiceFabricApplication` cmdlet에 전달합니다. 편의를 위해 Deploy.ps1 스크립트가 애플리케이션과 함께 제공됩니다. 스크립트를 사용하려면 다음을 수행합니다.

    - `Connect-ServiceFabricCluster`를 사용하여 Service Fabric 클러스터에 연결합니다.
    - 적절한 `ApplicationParameter` 값을 사용하여 PowerShell 스크립트 Deploy.ps1을 실행합니다.

> [!NOTE]
> 스크립트 및 애플리케이션 폴더 PatchOrchestrationApplication을 동일한 디렉터리에 유지합니다.

## <a name="upgrade-the-app"></a>앱 업그레이드

PowerShell을 사용하여 기존 패치 오케스트레이션 앱을 업그레이드하려면 [PowerShell을 사용하여 Service Fabric 애플리케이션 업그레이드](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)의 단계를 따릅니다.

## <a name="remove-the-app"></a>앱 제거

애플리케이션을 제거하려면 [PowerShell을 사용하여 애플리케이션 배포 및 제거](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)의 단계를 따릅니다.

편의를 위해 Undeploy.ps1 스크립트가 애플리케이션과 함께 제공됩니다. 스크립트를 사용하려면 다음을 수행합니다.

  - ```Connect-ServiceFabricCluster```를 사용하여 Service Fabric 클러스터에 연결합니다.

  - PowerShell 스크립트 Undeploy.ps1을 실행합니다.

> [!NOTE]
> 스크립트 및 애플리케이션 폴더 PatchOrchestrationApplication을 동일한 디렉터리에 유지합니다.

## <a name="view-the-windows-update-results"></a>Windows 업데이트 결과 보기

패치 오케스트레이션 앱은 사용자에게 기록 결과를 표시하는 REST API를 노출합니다. 결과 JSON의 예는 다음과 같습니다.
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

JSON의 필드가 아래에 설명되어 있습니다.

필드 | 값 | 세부 정보
-- | -- | --
OperationResult | 0 - 성공<br> 1 - 성공하였으나 오류 발생<br> 2 - 실패<br> 3 - 중단<br> 4 - 시간 제한으로 중단 | 전체 작업의 결과를 나타냅니다(일반적으로 하나 이상의 업데이트 설치 포함).
ResultCode | OperationResult와 동일 | 이 필드는 개별 업데이트에 대한 설치 작업의 결과를 나타냅니다.
OperationType | 1 - 설치<br> 0 - 검색 및 다운로드.| 설치는 기본적으로 결과에 표시되는 유일한 OperationType입니다.
WindowsUpdateQuery | 기본값은 “IsInstalled = 0”입니다. |업데이트 검색에 사용된 Windows 업데이트 쿼리입니다. 자세한 내용은 [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)를 참조하세요.
RebootRequired | true - 다시 부팅 필요<br> false - 다시 부팅 필요 없음 | 업데이트 설치를 완료하는 데 다시 부팅이 필요한지 여부를 표시합니다.
OperationStartTime | DateTime | 시작 하는 operation(Download/Installation) 시간을 나타냅니다.
OperationTime | DateTime | 완료는 operation(Download/Installation) 시간을 나타냅니다.
HResult | 0-성공<br> 기타-오류| UpdateID "7392acaf-6a85-427c-8a8d-058c25beb0d6"를 사용 하 여 windows update의 오류의 이유를 나타냅니다.

업데이트가 아직 예약되어 있지 않으면 결과 JSON은 비어 있습니다.

결과 쿼리하려면 클러스터에 Windows 업데이트에 로그인 합니다. 그런 다음 주 코디네이터 서비스의 복제본 주소를 찾아 브라우저에서 URL을 입력합니다(http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults).

코디네이터 서비스의 REST 엔드포인트에는 동적 포트가 있습니다. 정확한 URL을 확인하려면 Service Fabric Explorer를 참조하세요. 예를 들어 `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`에 결과가 제공됩니다.

![REST 엔드포인트의 이미지](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


클러스터에서 역방향 프록시를 사용하는 경우 사용자는 클러스터 외부에서도 URL에 액세스할 수 있습니다.
적중되어야 하는 엔드포인트는 http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults입니다.

클러스터에서 역방향 프록시를 사용하려면 [Azure Service Fabric의 역방향 프록시](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)에 있는 단계를 따릅니다. 

> 
> [!WARNING]
> 역방향 프록시가 구성된 후에는 HTTP 엔드포인트를 표시하는 클러스터에 있는 모든 마이크로 서비스의 주소를 클러스터 외부에서 지정할 수 있습니다.

## <a name="diagnosticshealth-events"></a>진단/상태 이벤트

다음 섹션에서는 Service Fabric 클러스터에 패치 오케스트레이션 응용 프로그램을 통해 패치 업데이트를 사용 하 여 문제를 디버그/진단 하는 방법을 설명 합니다.

> [!NOTE]
> 많은 가져오려는 설치 POA v1.4.0 버전이 있어야를 자체 진단 개선 사항에 아래 명시 합니다.

NodeAgentNTService를 만듭니다 [수리 작업](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) 노드에서 업데이트를 설치 합니다. 각 태스크는 다음 작업 승인 정책에 따라 CoordinatorService에서 준비 됩니다. 준비 작업에는 복구 관리자가 클러스터 비정상 상태 이면 모든 작업을 승인 하지 것입니다는 승인 마지막 됩니다. 수는 노드에서 업데이트 진행 하는 방법을 이해 하려면 과정을 단계별로 이동 합니다.

1. NodeAgentNTService, 모든 노드에서 실행 중인 예약된 된 시간에 사용 가능한 Windows 업데이트를 찾습니다. 업데이트를 사용할 수 있는 경우 진행 하 고 노드에 다운로드 합니다.
2. 업데이트가 다운로드 되 면 NodeAgentNTService, 이름을 POS___ < unique_id >를 사용 하 여 노드에 대 한 해당 복구 작업을 만듭니다. 이 볼 수 있습니다 cmdlet을 사용 하 여 작업을 복구할 [Get ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) 또는 SFX 노드 세부 정보 섹션에서. 복구 작업을 만든 후 신속 하 게 이동할 [상태를 요청](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet)합니다.
3. 코디네이터 서비스를 정기적으로 요청 된 상태에서 복구 작업을 찾습니다 진행 및 상태는 TaskApprovalPolicy 기반 준비를 위해이 업데이트 합니다. TaskApprovalPolicy는 NodeWise, 노드에 해당 하는 복구 작업으로 구성 된 경우는 경우에 다른 복구 작업이 현재 준비/승인 됨/실행/Restoring 상태로 준비 합니다. 마찬가지로, UpgradeWise TaskApprovalPolicy의 언제 든 지 확인 하는 경우 작업의에서 경우 동일한 업그레이드 도메인에 속하는 노드에 대해서만 위의 상태 복구 작업 상태를 준비를 위해 이동 되 면 해당 Service Fabric 노드인 [사용 하지 않도록 설정](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) "다시 시작" 의도 사용 하 여 합니다.

   POA(v1.4.0 and above)가 패치 되 고 있는 노드를 표시 하는 CoordinaterService에서 "ClusterPatchingStatus" 속성을 사용 하 여 이벤트를 게시 합니다. 아래 이미지를 업데이트 하는 표시 _poanode_0에서 설치 시작 됩니다.

    [![상태를 패치 하는 클러스터의 이미지](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

4. 노드 비활성화 되 면 복구 작업은 실행 상태로 이동 됩니다. 참고, 노드는 비활성화 상태로 멈출 때문에 상태 후 준비 중단 복구 작업을 차단 하는 새 복구 작업에서 발생 하 고 따라서 클러스터의 패치 적용을 중지 수 있습니다.
5. 복구 작업 상태를 실행 중이면 해당 노드에서 패치 설치를 시작 합니다. 여기에 패치가 설치 되 면 노드 수 또는 패치에 따라 다시 시작할 수 있습니다. Post는 복구 작업은 이동할 수 있도록 하는 다시 노드 다시 상태를 한 다음 복원 완료로 표시 됩니다.

   V1.4.0 이상 버전의 응용 프로그램에서 속성 "WUOperationStatus-[NodeName]"를 사용 하 여 NodeAgentService에 상태 이벤트를 확인 하 여 업데이트의 상태를 찾을 수 있습니다. 아래 그림에 강조 표시 된 섹션에는 노드 'poanode_0' 및 'poanode_2'에 대 한 windows 업데이트의 상태를 보여 줍니다.

   [![Windows 업데이트 작업 상태 이미지](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Windows 업데이트 작업 상태 이미지](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Powershell을 사용 하 여 클러스터에 연결 하 고 사용 하 여 복구 작업의 상태 가져오기 세부 정보를 가져올 수도 하나 [Get ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps)합니다. 마찬가지로 아래 예제는 "POS__poanode_2_125f2969 933 c-4774 85 d 1-ebdf85e79f15" 작업 DownloadComplete 상태입니다. 즉, "poanode_2" 노드에서 업데이트가 다운로드 된 작업 실행 중 상태로 전환 되 면 설치를 시도 합니다.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   있으면 다음 찾을 더 분 발해 로그인 특정 VM/Vm에 Windows 이벤트 로그를 사용 하 여 문제에 대 한 자세한 내용을 확인 하려면. 위에서 설명한 복구 작업 실행 기 하위 상태를 하나만 사용할 수 있습니다.

      ExecutorSubState | Detail
    -- | -- 
      None = 1 |  진행 중인 작업 노드에서 저렇게 의미 합니다. 가능한 상태 전환 됩니다.
      DownloadCompleted=2 | 다운로드 작업이 성공, 부분적으로 완료 된 것을 의미 오류 또는 실패 합니다.
      InstallationApproved=3 | 이전 다운로드 작업이 완료 되 고 복구 관리자가 설치를 승인한 의미 합니다.
      InstallationInProgress=4 | 복구 작업의 실행 상태에 해당합니다.
      InstallationCompleted = 5 | 설치가 성공, 부분적 성공 또는 실패를 사용 하 여 완료를 의미 합니다.
      RestartRequested=6 | 설치가 완료 된 패치 이며 노드에 대 한 보류 중인 다시 시작 동작을 의미 합니다.
      RestartNotNeeded=7 |  패치 설치가 완료 된 후 해당 다시 시작 필요 하지 않음 의미 합니다.
      RestartCompleted = 8 | 성공적으로 완료 하는 다시 시작을 의미 합니다.
      OperationCompleted=9 | Windows 업데이트 작업이 완료 되었습니다.
      OperationAborted=10 | Windows 업데이트 작업이 중단 될 것을 의미 합니다.

6. V1.4.0에서 위의 응용 프로그램 노드에서 업데이트 시도는 다음이 완료 되 면 속성 "WUOperationStatus-[NodeName]"를 사용 하 여 이벤트 게시 될 다음 시도 되는 경우에 알리기 위해, 다운로드 및 업데이트 설치를 시작 하는 NodeAgentService에 및 합니다. 아래 이미지를 참조 하세요.

     [![Windows 업데이트 작업 상태 이미지](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostic-logs"></a>진단 로그

Service Fabric 런타임 로그의 일부로 패치 오케스트레이션 앱 로그가 수집됩니다.

선택한 진단 도구/파이프라인을 통해 로그를 캡처하려고 합니다. 패치 오케스트레이션 응용 프로그램 사용 하 여 아래의 고정 된 공급자 Id 통해 이벤트를 기록 [이벤트 원본](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>상태 보고서

패치 오케스트레이션 앱은 다음과 같은 경우에도 코디네이터 서비스 또는 노드 에이전트 서비스에 대한 상태 보고서를 게시합니다.

#### <a name="the-node-agent-ntservice-is-down"></a>노드 에이전트 NTService 중단

노드에서 노드 에이전트 NTService가 중단되면 노드 에이전트 서비스에 대한 경고 수준 상태 보고서가 생성됩니다.

#### <a name="the-repair-manager-service-is-not-enabled"></a>복구 관리자 서비스가 사용하도록 설정되지 않음

복구 관리자 서비스가 클러스터에 없는 경우 코디네이터 서비스에 대해 경고 수준 상태 보고서가 생성됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

Q. **패치 오케스트레이션 앱이 실행 중인 경우 오류 상태인 클러스터가 표시되는 이유는 무엇인가요?**

a. 패치 오케스트레이션 앱은 설치 과정에서 노드를 사용하지 않도록 설정하거나 다시 시작하므로 클러스터의 상태가 일시적으로 중단될 수 있습니다.

애플리케이션의 정책에 따라 패치 작업 중에 하나의 노드가 중단되거나 *또는* 전체 업그레이드 도메인이 동시에 중단될 수 있습니다.

Windows 업데이트 설치가 끝날 때까지 노드는 다시 시작된 후 다시 사용하도록 설정됩니다.

다음 예제에서는 두 노드가 중단되고 MaxPercentageUnhealthyNodes 정책이 위반되었으므로 클러스터가 일시적으로 오류 상태가 되었습니다. 이 오류는 패치 작업이 진행될 때까지 일시적으로 지속됩니다.

![비정상 클러스터의 이미지](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

문제가 계속되는 경우 문제 해결 섹션을 참조하세요.

Q. **패치 오케스트레이션 앱이 경고 상태입니다.**

a. 애플리케이션에 대해 게시된 상태 보고서가 근본 원인인지 확인합니다. 일반적으로 경고에는 문제에 대한 세부 정보가 포함되어 있습니다. 문제가 일시적인 경우 애플리케이션은 이 상태에서 자동으로 복구됩니다.

Q. **클러스터가 비정상 상태이나 긴급한 운영 체제 업데이트를 수행해야 하는 경우 어떻게 해야 하나요?**

a. 클러스터 상태가 정상이 아닌 경우 패치 오케스트레이션 앱은 업데이트를 설치하지 않습니다. 패치 오케스트레이션 앱 워크플로의 차단을 해제하기 위해 클러스터를 정상 상태로 전환하려고 합니다.

Q. **설정 해야 하나요 TaskApprovalPolicy 'NodeWise' 또는 'UpgradeDomainWise' 내 클러스터에 대 한?**

a. 'UpgradeDomainWise'를 통해 병렬로 업그레이드 도메인에 속하는 모든 노드를 패치하여 전체 클러스터를 더 빠르게 패치할 수 있습니다. 즉, 전체 업그레이드 도메인에 속하는 노드가 패치 프로세스 중에 사용할 수 없게 됩니다([사용 안 함](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) 상태임).

반대로 'NodeWise' 정책은 한 번에 하나의 노드를 패치합니다. 즉, 전체 클러스터 패치에 더 많은 시간이 걸립니다. 하지만 최대 하나의 노드가 패치 프로세스 중에 사용할 수 없게 됩니다([사용 안 함](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) 상태임).

클러스터가 주기를 패치하는 동안 업그레이드 도메인의 N-1(N은 클러스터에 있는 업그레이드 도메인의 총합) 숫자에서 실행되도록 허용할 수 있는 경우 정책을 'UpgradeDomainWise'으로 설정할 수 있습니다. 그렇지 않으면 'NodeWise'로 설정합니다.

Q. **노드를 패치하는 데 시간이 얼마나 걸리나요?**

a. 노드를 패치하는 데 몇 분(예: [Windows Defender 정의 업데이트](https://www.microsoft.com/en-us/wdsi/definitions))에서 몇 시간(예: [Windows 누적 업데이트](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update))이 걸릴 수 있습니다. 노드를 패치하는 데 필요한 시간은 대부분 다음에 따라 달라집니다. 
 - 업데이트 크기
 - 패치하는 창에 적용해야 하는 업데이트 수
 - 업데이트를 설치하고, (필요한 경우) 노드를 다시 부팅하고, 다시 부팅 후 설치 단계를 완료하는 데 걸리는 시간
 - VM/머신 성능 및 네트워크 상태

Q. **전체 클러스터를 패치하는 데 시간이 얼마나 걸리나요?**

a. 전체 클러스터를 패치하는 데 필요한 시간은 다음과 같은 요인에 따라 달라집니다.

- 노드를 패치하는 데 필요한 시간
- 코디네이터 서비스 정책 기본 정책인 `NodeWise`에 따라, 한 번에 하나의 노드에만 패치를 적용합니다. 그러면 `UpgradeDomainWise`보다 느려집니다. 예를 들면 다음과 같습니다. 각각 4개의 노드가 포함된 5개의 업그레이드 도메인이 있는 20개의 노드(동일한 형식의 노드) 클러스터를 패치하기 위해 노드를 패치하는 데 최대 1시간이 걸리는 경우입니다.
    - 정책이 `NodeWise`인 경우 전체 클러스터를 패치하는 데 최대 20시간이 걸립니다.
    - 정책이 `UpgradeDomainWise`인 경우 최대 5시간이 걸립니다.
- 클러스터 로드 - 각 패치 작업에서는 고객 워크로드를 클러스터에서 사용 가능한 다른 노드로 재배치해야 합니다. 패치를 진행 중인 노드는 이 시간 동안 [사용 안 함](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) 상태입니다. 클러스터가 거의 최대 부하를 실행하는 경우 프로세스를 사용하지 않도록 설정하는 데 시간이 오래 걸립니다. 따라서 전체 패치 프로세스는 이러한 스트레스 조건에서 느려질 수 있습니다.
- 패치 중에 클러스터 상태 오류 - [클러스터의 상태](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction)의 [저하](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error)는 패치 프로세스를 중단합니다. 그러면 전체 클러스터를 패치하는 데 필요한 전체 시간에 추가됩니다.

Q. **REST API를 통해 가져온 Windows Update 결과에 표시된 일부 업데이트가 컴퓨터에서 Windows Update 기록에서 표시되지 않는 이유는 무엇인가요?**

a. 일부 제품 업데이트는 해당하는 업데이트/패치 기록에서만 나타납니다. 예를 들어 Windows Defender 업데이트는 Windows Server 2016의 Windows Update 기록에 표시되거나 되지 않을 수 있습니다.

Q. **패치 오케스트레이션 앱을 사용하여 개발자 클러스터(1노드 클러스터)에 패치를 적용할 수 있나요?**

a. 아니요, 패치 오케스트레이션 앱을 사용하여 1노드 클러스터에 패치를 적용할 수 없습니다. 이 제한은 기본적으로 [Service Fabric 시스템 서비스](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) 또는 고객 앱이 가동 중지되고 패치에 대한 모든 복구 작업이 복구 관리자에서 승인되지 않으므로 이 제한은 기본적으로 적용됩니다.

Q. **Linux에서 클러스터 노드를 패치 마십시오는 방법**

a. 참조 [Azure 가상 머신 확장 집합 자동 OS 이미지 업그레이드](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) linux에서 업데이트를 오케스트레이션 하는 것에 대 한 합니다.

Q.**이유는 업데이트 주기 시간이 많이 걸리는?**

a. 결과 json, 그런 다음 모든 노드 및 그런 다음 사용자에 대 한 업데이트 주기의 항목을 통해 이동에 대 한 쿼리 수 OperationStartTime 및 OperationTime(OperationCompletionTime)를 사용 하 여 모든 노드에서 업데이트 설치에 걸린 시간을 확인 하려고 합니다. 큰 시간 창에 있는 경우는 업데이트가 일어났던, 클러스터 된 오류 상태와 관리자는 복구로 인해 다른 POA 복구 태스크를 승인 하지 않았습니다 때문일 수 있습니다. 업데이트가 모든 노드에서 긴 경우, 그런 다음 때문일 노드 오랫동안에서 업데이트 되지 않았습니다 있으며 많은 업데이트 시간이 걸렸을 들어 설치가 보류 된 있습니다. 또한 있을 수 있는 노드는 노드를 사용 하지 않도록 설정 하기 때문에 일반적으로 발생 하는 상태를 사용 하지 않도록 설정 중 중지로 인해 차단 됩니다 노드에서 패치 하는 경우 쿼럼/데이터 손실 상황에 발생할 수 있습니다.

Q. **이유 필요 하지 않습니다 POA 패치 적용 되는 경우 노드를 사용 하지 않도록 설정 하 시겠습니까?**

a. 패치 오케스트레이션 응용 프로그램 노드는 노드에서 실행 중인 모든 서비스 패브릭 서비스를 중지/다시 할당. '다시 시작' 의도 사용 하 여 사용 하지 않도록 설정 합니다. 응용 프로그램 노드를 패치 하는 것을 사용 하지 않도록 설정 하지 않고는 권장 되지 않습니다 신규 버전과 기존 dll 혼합을 사용 하 여 끝나지 않는 되도록 수행 됩니다.

## <a name="disclaimers"></a>고지 사항

- 패치 오케스트레이션 앱은 사용자를 대신하여 Windows 업데이트에 대한 최종 사용자 사용권 계약에 동의합니다. 필요에 따라 애플리케이션 구성에서 설정을 해제할 수 있습니다.

- 패치 오케스트레이션 앱은 사용 및 성능을 추적하기 위해 원격 분석을 수집합니다. 애플리케이션의 원격 분석은 Service Fabric 런타임의 원격 분석 설정을 따릅니다(기본적으로 설정됨).

## <a name="troubleshooting"></a>문제 해결

### <a name="a-node-is-not-coming-back-to-up-state"></a>노드가 활성 상태로 전환되지 않음

**노드는 다음과 같은 이유로 사용 불가능 상태에 멈춰 있을 수 있습니다**.

안전 검사가 보류 중입니다. 이 문제를 해결하려면 정상 상태에서 충분히 노드를 사용할 수 있는지 확인합니다.

**노드는 다음과 같은 이유로 사용 안 함 상태에 멈춰 있을 수 있습니다**.

- 노드가 수동으로 사용하지 않도록 설정되었습니다.
- 노드가 진행 중인 Azure 인프라 작업으로 인해 사용하지 않도록 설정되었습니다.
- 노드가 노드를 패치하는 패치 오케스트레이션 앱에 의해 일시적으로 사용하지 않도록 설정되었습니다.

**노드는 다음과 같은 이유로 중단 상태에 멈춰 있을 수 있습니다**.

- 노드가 수동으로 중단되었습니다.
- 노드가 다시 시작하는 중입니다(패치 오케스트레이션 앱에 의해 트리거될 수 있음).
- 노드가 결함이 있는 VM, 컴퓨터 또는 네트워크 연결 문제로 인해 중단되었습니다.

### <a name="updates-were-skipped-on-some-nodes"></a>일부 노드에서 업데이트를 건너뛰었습니다.

패치 오케스트레이션 앱은 다시 예약 정책에 따라 Windows 업데이트를 설치하려고 합니다. 이 서비스는 노드를 복구하고 애플리케이션 정책에 따라 업데이트를 건너뛰려고 합니다.

이러한 경우에 노드 에이전트 서비스에 대한 경고 수준 상태 보고서가 생성됩니다. Windows 업데이트 결과에도 가능한 실패 원인이 포함됩니다.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>업데이트가 설치되는 동안 클러스터의 상태가 오류로 전환됨

잘못된 Windows 업데이트는 특정 노드 또는 업그레이드 도메인에 있는 애플리케이션 또는 클러스터의 상태를 중단시킬 수 있습니다. 패치 오케스트레이션 앱은 클러스터가 다시 정상 상태가 될 때까지 후속 Windows 업데이트 작업을 중단합니다.

관리자가 개입하여 Windows 업데이트로 인해 애플리케이션 또는 클러스터가 비정상 상태가 된 이유를 확인해야 합니다.

## <a name="release-notes"></a>릴리스 정보

>[!NOTE]
> 버전 1.4.0에서에서 시작 하며, 릴리스 정보 및 릴리스 있습니다 GitHub 릴리스에서 [페이지](https://github.com/microsoft/Service-Fabric-POA/releases/)합니다.

### <a name="version-110"></a>버전 1.1.0
- 공개 릴리스

### <a name="version-111"></a>버전 1.1.1
- NodeAgentNTService의 설치를 방지하는 NodeAgentService의 SetupEntryPoint에서 버그 수정

### <a name="version-120"></a>버전 1.2.0

- 시스템 다시 시작 워크플로 관련 버그 수정
- 복구 중 상태 확인이 예상 대로 작동하지 않는 문제로 인해 RM 작업 생성의 버그 수정
- Windows 서비스 POANodeSvc 시작 모드를 자동에서 지연 자동으로 변경

### <a name="version-121"></a>버전 1.2.1

- 클러스터 축소 워크플로의 버그 수정입니다. 존재하지 않는 노드에 속하는 POA 복구 태스크에 대해 가비지 수집 논리가 도입되었습니다.

### <a name="version-122"></a>버전 1.2.2

- 기타 버그가 수정되었습니다.
- 이제 이진 파일이 서명됩니다.
- 애플리케이션에 대한 sfpkg 링크가 추가되었습니다.

### <a name="version-130"></a>버전 1.3.0

- 이제 InstallWindowsOSOnlyUpdates를 False로 설정하면 사용 가능한 모든 업데이트가 설치됩니다.
- 자동 업데이트를 사용하지 않도록 설정하는 논리가 변경되었습니다. 이로 인해 Server 2016 이상에서 자동 업데이트가 사용하지 않도록 설정되지 않는 버그가 수정됩니다.
- 두 마이크로 서비스의 고급 사용 사례에 대 한 POA에 대 한 매개 변수가 있는 배치 제약 조건입니다.

### <a name="version-131"></a>버전 1.3.1
- 자동 업데이트 비활성화의 오류로 인해 Windows Server 2012 R2 이하에서 POA 1.3.0이 작동하지 않는 회귀 수정 
- InstallWindowsOSOnlyUpdates 구성이 항상 True로 선택되는 버그 수정
- InstallWindowsOSOnlyUpdates의 기본값을 False로 변경

### <a name="version-132"></a>버전 1.3.2
- 노드에서 패치 수명 주기에 영향을 현재 노드 이름의 하위 집합 이름 사용 하는 노드가 없는 경우에 문제를 수정 합니다. 그러한 노드의 경우 패치가 누락되거나 다시 부팅이 보류 중일 수 있습니다. 