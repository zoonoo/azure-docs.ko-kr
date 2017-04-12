---
title: "Windows Server에서 독립 실행형 Azure Service Fabric 클러스터 업그레이드 | Microsoft 문서"
description: "클러스터 업데이트 모드 설정 등 독립 실행형 Service Fabric 클러스터를 실행하는 Azure Service Fabric 코드 및/또는 구성을 업그레이드합니다."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 6196cb7fa13cf664faa72b7f5f5e0645e4402739
ms.lasthandoff: 03/29/2017


---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Windows Server에서 독립 실행형 Azure Service Fabric 클러스터 업그레이드
> [!div class="op_single_selector"]
> * [Azure 클러스터](service-fabric-cluster-upgrade.md)
> * [독립 실행형 클러스터](service-fabric-cluster-upgrade-windows-server.md)
>
>

최신 시스템에서 업그레이드하는 기능은 제품의 장기적인 성공을 위해 핵심적입니다. Azure 서비스 패브릭 클러스터는 사용자가 소유하는 리소스입니다. 이 문서에서는 어떻게 클러스터가 Service Fabric 코드 및 구성의 지원되는 버전을 항상 실행하도록 하는지에 대해 설명합니다.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>클러스터에서 실행되는 Service Fabric 버전 제어
Microsoft에서 새 버전을 출시할 때 Service Fabric 업데이트를 다운로드하도록 클러스터를 설정하려면 **fabricClusterAutoupgradeEnabled** 클러스터 구성을 true로 설정합니다. 클러스터가 설정될 지원되는 Service Fabric 버전을 선택하려면 **fabricClusterAutoupgradeEnabled** 클러스터 구성을 false로 설정합니다.

> [!NOTE]
> 클러스터가 지원되는 Service Fabric 버전을 항상 실행하는지 확인합니다. Microsoft에서 새로운 버전의 Service Fabric 릴리스를 발표하면 이전 버전은 해당 발표일로부터 최소 60일 후 지원 종료되는 것으로 표시됩니다. 새로운 릴리스는 [Service Fabric 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/)에서 발표됩니다. 그러면 해당 시점에 새로운 릴리스를 선택할 수 있습니다.
>
>

각 Service Fabric 노드를 별도 물리적 컴퓨터 또는 가상 컴퓨터에 할당하는 프로덕션 스타일 노드 구성을 사용하는 경우에만 클러스터를 새 버전으로 업그레이드할 수 있습니다. 하나의 물리적 또는 가상 컴퓨터에 Service Fabric 노드가 여러 개 있는 개발 클러스터가 있는 경우 새 버전으로 클러스터를 다시 만들어야 합니다.

클러스터를 최신 버전 또는 지원되는 Service Fabric 버전으로 업그레이드하는 데는 두 가지 워크플로가 있습니다. 한 가지 워크플로는 최신 버전을 자동으로 다운로드하도록 연결된 클러스터에 사용됩니다. 또 다른 워크플로는 최신 Service Fabric 버전을 다운로드하기 위한 연결이 없는 클러스터에 사용됩니다.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>최신 코드와 구성을 다운로드하도록 연결된 클러스터 업그레이드
클러스터 노드가 [http://download.microsoft.com](http://download.microsoft.com)에 인터넷으로 연결되어 있는 경우 클러스터를 지원되는 버전으로 업그레이드하려면 이 단계를 사용합니다.

[http://download.microsoft.com](http://download.microsoft.com)에 연결된 클러스터의 경우, Microsoft는 새 Service Fabric 버전의 가용성을 정기적으로 확인합니다.

새 Service Fabric 버전을 사용할 수 있는 경우 패키지를 클러스터에 로컬로 다운로드하고 업그레이드를 위해 프로비전됩니다. 또한 이 새로운 버전을 고객에게 알리기 위해 시스템은 다음과 같이 명시적인 클러스터 상태 경고를 표시합니다.

"현재 클러스터 버전[버전 #] 지원은 [날짜]로 종료됩니다."

클러스터가 최신 버전을 실행한 후 경고는 사라집니다.

#### <a name="cluster-upgrade-workflow"></a>클러스터 업그레이드 워크플로
클러스터 상태 경고가 표시된 후 다음을 수행합니다.

1. 클러스터에서 노드로 나열된 모든 컴퓨터에 대한 관리자 액세스 권한이 있는 모든 컴퓨터에서 클러스터에 연결합니다. 이 스크립트가 실행되는 컴퓨터가 클러스터의 일부일 필요는 없습니다.

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. 업그레이드할 수 있는 Service Fabric 버전의 목록을 가져옵니다.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    다음과 유사한 결과가 표시됩니다.

    ![패브릭 버전 가져오기][getfabversions]
3. [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx) PowerShell cmd 명령을 사용하여 사용 가능한 버전으로 클러스터 업그레이드를 시작합니다.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   업그레이드 진행률을 모니터링하려면 Service Fabric Explorer를 사용하거나 다음 Windows PowerShell 명령을 실행합니다.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. **Start-ServiceFabricClusterUpgrade** 명령에 대한 사용자 지정 상태 정책을 지정하려면 [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx)에 대한 설명서를 참조하세요.

롤백을 일으킨 문제를 수정한 후 이전과 동일한 단계에 따라 업그레이드를 다시 시작합니다.

### <a name="upgrade-clusters-that-have-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>최신 코드와 구성을 다운로드하도록 <U>연결되지 않은</u> 클러스터 업그레이드
클러스터 노드가 [http://download.microsoft.com](http://download.microsoft.com)에 인터넷으로 연결되어 있지 않은 경우 클러스터를 지원되는 버전으로 업그레이드하려면 이 단계를 사용합니다.

> [!NOTE]
> 인터넷에 연결되지 않은 클러스터를 실행하는 경우 Service Fabric 팀 블로그를 모니터링하여 새 릴리스를 확인해야 합니다. 시스템은 새 릴리스를 알리는 클러스터 상태 경고를 표시하지 않습니다.  
>
>

구성 업그레이드를 시작하기 전에 클러스터 구성을 수정하여 다음 속성을 false로 설정합니다.

        "fabricClusterAutoupgradeEnabled": false,

자세한 사용 방법은 [Start-ServiceFabricClusterConfigurationUpgrade PS cmd ](https://msdn.microsoft.com/en-us/library/mt788302.aspx)를 참조하세요. 구성 업그레이드를 시작하기 전에 JSON에서 'clusterConfigurationVersion'을 업데이트해야 합니다.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>클러스터 업그레이드 워크플로

1. 클러스터의 노드 중 하나에서 Get-ServiceFabricClusterUpgrade를 실행하고 TargetCodeVersion을 기록해 둡니다.
2. 인터넷에 연결된 컴퓨터에서 다음을 실행하여 현재 버전과 호환 가능한 모든 업그레이드 버전을 나열하고 연결된 다운로드 링크에서 해당 패키지를 다운로드합니다.

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. 클러스터에서 노드로 나열된 모든 컴퓨터에 대한 관리자 액세스 권한이 있는 모든 컴퓨터에서 클러스터에 연결합니다. 이 스크립트가 실행되는 컴퓨터가 클러스터의 일부일 필요는 없습니다.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. 다운로드 한 패키지를 클러스터 이미지 저장소에 복사합니다.

5. 복사된 패키지를 등록합니다.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. 사용 가능한 버전으로의 클러스터 업그레이드를 시작합니다.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Service Fabric Explorer에서 업그레이드 진행률을 모니터링하거나 다음 PowerShell 명령을 실행할 수 있습니다.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. **Start-ServiceFabricClusterUpgrade** 명령에 대한 사용자 지정 상태 정책을 지정하려면 [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx)에 대한 설명서를 참조하세요.

롤백을 일으킨 문제를 수정한 후 이전과 동일한 단계에 따라 업그레이드를 다시 시작합니다.


## <a name="upgrade-the-cluster-configuration"></a>클러스터 구성 업그레이드
클러스터 구성을 업그레이드하려면 **Start-ServiceFabricClusterConfigurationUpgrade**를 실행합니다. 업그레이드 도메인으로 구성 업그레이드가 처리됩니다.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>클러스터 인증서 구성 업그레이드  
오류는 클러스터 노드 간의 통신을 차단하므로 주의해서 인증서 롤오버가 실행될 수 있도록 클러스터 노드 간 인증에 클러스터 인증서가 사용됩니다.  
기술적으로 두 가지 옵션이 지원됩니다.  

1. 단일 인증서 업그레이드: 업그레이드 경로는 '인증서 A(기본) -> 인증서 B(기본) -> 인증서 C(기본) ->...'입니다.   
2. 이중 인증서 업그레이드: 업그레이드 경로는 '인증서 A(기본) -> 인증서 A(기본) 및 B(보조) -> 인증서 B(기본) -> 인증서 B(기본) 및 C(보조) -> 인증서 C(기본) ->...'입니다.


## <a name="next-steps"></a>다음 단계
* [Service Fabric 클러스터 설정](service-fabric-cluster-fabric-settings.md) 중 일부를 사용자 지정하는 방법 알아보기
* [클러스터를 확장 및 축소](service-fabric-cluster-scale-up-down.md)하는 방법 알아보기
* [응용 프로그램 업그레이드](service-fabric-application-upgrade.md)에 대해 알아보기

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

