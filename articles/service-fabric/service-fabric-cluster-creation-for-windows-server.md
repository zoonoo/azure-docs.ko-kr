---
title: 독립 실행형 Azure Service Fabric 클러스터 만들기 | Microsoft Docs
description: 온-프레미스 또는 클라우드에서 Windows Server 컴퓨터(실제 또는 가상)를 사용하여 Azure Service Fabric 클러스터를 만듭니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 9c59da35f5dafcdcf62901689814e696dce3cd20
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674220"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Windows Server에서 실행되는 독립 실행형 클러스터 만들기
Azure Service Fabric을 사용하면 Windows Server를 실행 중인 가상 머신 또는 컴퓨터에서 Service Fabric 클러스터를 만들 수 있습니다. 즉, 온-프레미스 또는 클라우드 공급자에 서로 연결된 일련의 Windows Server 컴퓨터가 있는 환경에서 Service Fabric 애플리케이션을 배포하고 실행할 수 있습니다. 서비스 패브릭은 독립 실행형 Windows Server 패키지라는 서비스 패브릭 클러스터를 만들 수 있는 설치 패키지를 제공합니다.

이 문서에서는 Service Fabric 독립 실행형 클러스터를 만들기 위한 단계를 안내합니다.

> [!NOTE]
> 이 독립 실행형 Windows Server 패키지는 상업적으로 제공되며 프로덕션 배포에 사용할 수 있습니다. 이 패키지는 "Preview"에 있는 새 Service Fabric 기능을 포함할 수 있습니다. "[이 패키지에 포함된 미리 보기 기능](#previewfeatures_anchor)"까지 아래로 스크롤합니다. 미리 보기 기능 목록 섹션입니다. 현재 [EULA 사본을 다운로드](https://go.microsoft.com/fwlink/?LinkID=733084)할 수 있습니다.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Windows Server용 Service Fabric 패키지 지원
* [Azure Service Fabric 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?)에서 Windows Server용 Service Fabric 독립 실행형 패키지에 대해 커뮤니티에 문의합니다.
* [Service Fabric에 대한 전문 지원](https://support.microsoft.com/oas/default.aspx?prid=16146)에 대한 티켓을 엽니다.  [여기](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)에서 Microsoft의 전문 지원에 대해 자세히 알아봅니다.
* 또한 [Microsoft 프리미어 지원](https://support.microsoft.com/en-us/premier)의 일부로 이 패키지에 대해 지원을 받을 수도 있습니다.
* 자세한 내용은 [Azure Service Fabric 지원 옵션](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)을 참조하세요.
* 지원을 위해 로그를 수집하려면 [Service Fabric 독립 실행형 로그 수집기](service-fabric-cluster-standalone-package-contents.md)를 실행합니다.

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Windows Server용 Service Fabric 패키지 다운로드
클러스터를 만들려면 다음에서 찾을 수 있는 Windows Server(Windows Server 2012 R2 이상)용 Service Fabric 패키지를 사용합니다. <br>
[다운로드 링크 - Service Fabric 독립 실행형 패키지 - Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

[여기](service-fabric-cluster-standalone-package-contents.md)에서 패키지의 자세한 내용을 찾습니다.

Service Fabric 런타임 패키지는 클러스터 생성 시 자동으로 다운로드됩니다. 인터넷에 연결되지 않은 컴퓨터에서 배포하는 경우 여기에서 대역 외 런타임 패키지를 다운로드하세요. <br>
[다운로드 링크 - Service Fabric 런타임 - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

다음에서 독립 실행형 클러스터 구성 샘플을 찾습니다. <br>
[독립 실행형 클러스터 구성 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>클러스터 만들기
몇 가지 샘플 클러스터 구성 파일은 설치 패키지와 함께 설치됩니다. *ClusterConfig.Unsecure.DevCluster.json*은 가장 간단한 클러스터 구성: 단일 컴퓨터에서 실행되는 비보안 3개 노드 클러스터입니다.  다른 구성 파일에서는 X.509 인증서 또는 Windows 보안을 사용하여 보호되는 단일 또는 다중 컴퓨터 클러스터를 설명합니다.  이 자습서에 대한 기본 구성 설정을 수정할 필요는 없지만 구성 파일을 확인하고 설정에 익숙해지도록 합니다.  **노드** 섹션에서는 클러스터에 있는 세 개의 노드(이름, IP 주소, [노드 유형, 장애 도메인 및 업그레이드 도메인](service-fabric-cluster-manifest.md#nodes-on-the-cluster))에 대해 설명합니다.  **속성** 섹션에서는 클러스터의 [보안, 안정성 수준, 진단 컬렉션 수준 및 노드의 형식](service-fabric-cluster-manifest.md#cluster-properties)을 정의합니다.

이 문서에서 만든 클러스터는 안전하지 않습니다.  누구든지 익명으로 연결하고 관리 작업을 수행할 수 있으므로 프로덕션 클러스터가 항상 X.509 인증서 또는 Windows 보안을 사용하여 보호되어야 합니다.  클러스터 생성 시에만 보안을 구성하므로 클러스터를 만든 후에 보안을 사용하도록 설정할 수 없습니다. 구성 파일을 업데이트하여 [인증서 보안](service-fabric-windows-cluster-x509-security.md) 또는 [Windows 보안](service-fabric-windows-cluster-windows-security.md)을 사용하도록 설정합니다. Service Fabric 클러스터 보안에 대한 자세한 내용은 [클러스터에 보안 적용](service-fabric-cluster-security.md)을 읽어 보세요.

### <a name="step-1-create-the-cluster"></a>1단계: 클러스터 만들기

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>시나리오 A: 비보안 로컬 개발 클러스터 만들기
[샘플](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)에 포함된 *ClusterConfig.Unsecure.DevCluster.json* 파일을 사용하여 하나의 컴퓨터 개발 클러스터에 Service Fabric을 배포할 수 있습니다.

머신에 독립 실행형 패키지의 압축을 풀고 로컬 머신에 샘플 구성 파일을 복사한 다음, 독립 실행형 패키지 폴더에서 관리자 PowerShell 세션을 통해 *CreateServiceFabricCluster.ps1* 스크립트를 실행합니다.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

문제 해결 세부 정보는 [클러스터 배포를 위한 계획 및 준비](service-fabric-cluster-standalone-deployment-preparation.md)에서 환경 설정 섹션을 참조하세요.

실행 중인 개발 시나리오를 마친 경우 "[클러스터 제거](#removecluster_anchor)" 섹션의 단계를 참조하여 컴퓨터에서 Service Fabric 클러스터를 제거할 수 있습니다. 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>시나리오 B: 다중 머신 클러스터 만들기
[클러스터 배포를 위한 계획 및 준비](service-fabric-cluster-standalone-deployment-preparation.md)에서 자세히 설명된 계획 및 준비 단계를 완료한 후 클러스터 구성 파일을 사용하여 프로덕션 클러스터를 만들 준비가 되었습니다.

클러스터를 배포하고 구성하는 클러스터 관리자는 컴퓨터에서 관리자 권한이 있어야 합니다. 도메인 컨트롤러에 Service Fabric을 설치할 수 없습니다.

1. 독립 실행형 패키지의 *TestConfiguration.ps1* 스크립트는 지정된 환경에 클러스터를 배포할 수 있는지 여부를 확인하는 모범 사례 분석기로 사용됩니다. [배포 준비](service-fabric-cluster-standalone-deployment-preparation.md)는 필수 구성 요소 및 환경 요구 사항을 나열합니다. 개발 클러스터를 만들 수 있는지 확인하는 스크립트를 실행합니다.  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    다음과 유사한 결과가 표시됩니다. 아래쪽 필드 "Passed"가 "True"로 반환되는 경우 온전성 검사를 통과했고 클러스터는 입력 구성에 따라 배포 가능한 것으로 보입니다.

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. 클러스터 만들기:  *CreateServiceFabricCluster.ps1* 스크립트를 실행하여 구성의 각 머신에 Service Fabric 클러스터를 배포합니다. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> CreateServiceFabricCluster.ps1 PowerShell 스크립트를 실행한 VM/컴퓨터에 배포 추적이 기록됩니다. 스크립트가 실행된 디렉터리에 따라 하위 폴더 DeploymentTraces에서 찾을 수 있습니다. Service Fabric이 컴퓨터에 올바르게 배포되었는지 확인하려면 클러스터 구성 파일 FabricSettings 섹션에서 설명된 대로 FabricDataRoot 디렉터리에 설치된 파일을 찾습니다(기본적으로 c:\ProgramData\SF). 또한 작업 관리자에서 실행 중인 FabricHost.exe 및 Fabric.exe 프로세스를 볼 수 있습니다.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>시나리오: c: 오프라인(인터넷 끊김) 클러스터 만들기
Service Fabric 런타임 패키지는 클러스터 생성 시 자동으로 다운로드됩니다. 클러스터를 인터넷에 연결되지 않은 컴퓨터에 배포할 때 Service Fabric 런타임 패키지를 별도로 다운로드하고 클러스터 생성 시 경로를 제공해야 합니다.
런타임 패키지는 [다운로드 링크 - Service Fabric 런타임 - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)에서 인터넷에 연결된 다른 컴퓨터에서 개별적으로 다운로드할 수 있습니다. 오프라인 클러스터를 배포하는 위치에 런타임 패키지를 복사하고 다음 예제와 같이 포함된 `-FabricRuntimePackagePath` 매개 변수를 사용하여 `CreateServiceFabricCluster.ps1`을 실행하여 클러스터를 만듭니다. 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.json* 및 *.\MicrosoftAzureServiceFabric.cab*는 각각 클러스터 구성 및 런타임 .cab 파일에 대한 경로입니다.

### <a name="step-2-connect-to-the-cluster"></a>2단계: 클러스터에 연결
클러스터가 실행 중이고 사용 가능한지 확인하려면 클러스터에 연결합니다. ServiceFabric PowerShell 모듈은 런타임에 설치됩니다.  Service Fabric 런타임을 사용하여 클러스터 노드 중 하나 또는 원격 컴퓨터에서 클러스터에 연결할 수 있습니다.  [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet은 클러스터에 대한 연결을 설정합니다.

비보안 클러스터에 연결하려면 다음 PowerShell 명령을 실행합니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

예: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```

클러스터에 연결하는 다른 예제는 [보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md)을 참조하세요. 클러스터에 연결한 후에는 [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) cmdlet을 사용하여 클러스터의 노드 목록과 각 노드에 대한 상태 정보를 표시합니다. **HealthState**는 노드마다 *OK* 상태여야 합니다.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>3단계: Service Fabric Explorer를 사용하여 클러스터 시각화
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)는 클러스터를 시각화하고 애플리케이션을 관리할 수 있는 좋은 도구입니다.  Service Fabric Explorer는 브라우저를 사용하여 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)로 이동하여 액세스할 수 있는 클러스터에서 실행되는 서비스입니다.

클러스터 대시보드는 애플리케이션 및 노드 상태에 대한 요약을 포함하여 클러스터에 대한 개요를 제공합니다. 노드 보기는 클러스터의 물리적 레이아웃을 보여 줍니다. 지정된 노드의 경우 해당 노드에 배포된 코드를 가진 애플리케이션을 검사할 수 있습니다.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>노드 추가 및 제거
비즈니스 요구가 변경됨에 따라 독립 실행형 서비스 패브릭 클러스터에 노드를 추가 또는 제거할 수 있습니다. 자세한 단계는 [서비스 패브릭 독립 실행형 클러스터에 노드 추가 또는 제거](service-fabric-cluster-windows-server-add-remove-nodes.md)를 참조하세요.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>클러스터 제거
클러스터를 제거하려면 패키지 폴더에서 *RemoveServiceFabricCluster.ps1* Powershell 스크립트를 실행하고 JSON 구성 파일에 대한 경로를 전달합니다. 경우에 따라 삭제 로그 위치를 지정할 수도 있습니다.

이 스크립트는 클러스터 구성 파일에서 노드로 나열된 모든 컴퓨터에 대한 관리자 액세스 권한이 있는 모든 컴퓨터에서 실행될 수 있습니다. 이 스크립트가 실행되는 컴퓨터가 클러스터의 일부일 필요는 없습니다.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>수집된 원격 분석 데이터 및 옵트아웃(opt out)
기본적으로 이 제품은 제품을 개선하기 위해 Service Fabric 사용 현황에 대한 원격 분석을 수집합니다. 설치의 일부로 실행되는 모범 사례 분석기는 [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)에 대한 연결을 확인하고 연결할 수 없는 경우 원격 분석을 옵트아웃하지 않으면 설치가 실패합니다. 연결할 수 없는 경우 원격 분석을 옵트아웃하지 않으면 설치가 실패합니다.

1. 원격 분석 파이프라인은 하루에 한 번 다음 데이터를 [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)에 업로드하려고 시도합니다. 최상의 업로드이며 클러스터 기능에는 영향이 없습니다. 원격 분석은 장애 조치(Failover) 관리자 기본이 실행되는 노드에서만 전송됩니다. 다른 노드에서는 원격 분석이 전송되지 않습니다.
2. 원격 분석은 다음으로 구성됩니다.

* 서비스 수
* ServiceTypes 수
* 애플리케이션 수
* ApplicationUpgrades 수
* FailoverUnits 수
* InBuildFailoverUnits 수
* UnhealthyFailoverUnits 수
* 복제본 수
* InBuildReplicas 수
* StandByReplicas 수
* OfflineReplicas 수
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* 노드 수
* IsContextComplete: True/False
* ClusterId: 각 클러스터에 대해 임의로 생성된 GUID
* ServiceFabricVersion
* 원격 분석을 업로드한 가상 컴퓨터 또는 컴퓨터의 IP 주소

원격 분석을 사용하지 않도록 설정하려면 클러스터 config: *enableTelemetry: false*의 *properties*에 다음을 추가합니다.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>이 패키지에 포함된 미리 보기 기능
없음.


> [!NOTE]
> 새로운 [GA 버전의 Windows Server용 독립 실행형 클러스터(버전 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)로 시작하는 경우 수동 또는 자동으로 클러스터를 후속 릴리스로 업그레이드할 수 있습니다. 자세한 내용은 [독립 실행형 Service Fabric 클러스터 버전 업그레이드](service-fabric-cluster-upgrade-windows-server.md)를 참조하세요.
> 
> 

## <a name="next-steps"></a>다음 단계
* [PowerShell을 사용하여 애플리케이션 배포 및 제거](service-fabric-deploy-remove-applications.md)
* [독립 실행형 Windows 클러스터에 대한 구성 설정](service-fabric-cluster-manifest.md)
* [독립 실행형 서비스 패브릭 클러스터에 노드 추가 또는 제거](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [독립 실행형 Service Fabric 클러스터 버전 업그레이드](service-fabric-cluster-upgrade-windows-server.md)
* [Windows를 실행하는 Azure VM에서 독립 실행형 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Windows 보안을 사용하여 독립 실행형 클러스터 보호](service-fabric-windows-cluster-windows-security.md)
* [X509 인증서를 사용하여 Windows에서 독립 실행형 클러스터 보호](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png