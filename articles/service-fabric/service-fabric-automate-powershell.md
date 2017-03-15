---
title: "PowerShell을 사용하여 Service Fabric 응용 프로그램 관리 자동화 | Microsoft Docs"
description: "PowerShell을 사용하여 서비스 패브릭 응용 프로그램 배포, 업그레이드, 테스트 및 제거"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: f03f4294-571d-4262-8a77-cc8b481b959d
ms.service: service-fabric
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: d4a08f7eec5f57b06d87d13abe46942ddc24b482
ms.openlocfilehash: d1909225a950b961491d3fe426f1e0d6aa5bdd53
ms.lasthandoff: 02/24/2017


---
# <a name="automate-the-application-lifecycle-using-powershell"></a>PowerShell을 사용하여 응용 프로그램 수명 주기 자동화
[서비스 패브릭 응용 프로그램 수명 주기](service-fabric-application-lifecycle.md) 의 여러 측면을 자동화할 수 있습니다.  이 문서에서는 PowerShell을 사용해 Azure 서비스 패브릭 응용 프로그램을 배포, 업그레이드, 제거 및 테스트하는 일반적인 작업을 자동화하는 방법을 보여줍니다.  앱 관리를 위한 관리되는 API 및 HTTP API도 사용 가능합니다. 자세한 내용은 [앱 수명 주기](service-fabric-application-lifecycle.md) 를 참조하세요.  

## <a name="prerequisites"></a>필수 조건
다음은 문서의 작업으로 넘어가기 전에 해야 할 일입니다.

* [서비스 패브릭의 기술 개요](service-fabric-technical-overview.md)에 설명된 서비스 패브릭 개념을 숙지합니다.
* [런타임, SDK 및 도구를 설치](service-fabric-get-started.md)합니다. **ServiceFabric** PowerShell 모듈도 함께 설치될 것입니다.
* [PowerShell 스크립트 실행을 활성화](service-fabric-get-started.md#enable-powershell-script-execution)합니다.
* 로컬 클러스터를 시작합니다.  관리자로 새 PowerShell 창을 시작한 다음 SDK 폴더에서 클러스터 설치 스크립트를 실행합니다. `& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
* 이 문서에서는 PowerShell 명령을 실행하기 전에 먼저 [Connect-ServiceFabricCluster](/powershell/servicefabric/vlatest/connect-servicefabriccluster)(`Connect-ServiceFabricCluster localhost:19000`)를 사용하여 로컬 Service Fabric 클러스터에 연결합니다.
* 다음 작업에서 배포에는 v1 응용 프로그램 패키지, 업그레이드에는 v2 응용 프로그램 패키지가 필요합니다. [**WordCount** 응용 프로그램 예제](http://aka.ms/servicefabricsamples)(시작 샘플에 있음)를 다운로드합니다. 솔루션 탐색기에서 **WordCount**를 마우스 오른쪽 단추로 클릭하고 **패키지**를 선택하여 Visual Studio에서 응용 프로그램을 빌드 및 패키지합니다. `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug`의 v1 패키지를 `C:\Temp\WordCount`에 복사합니다. `C:\Temp\WordCount`를 `C:\Temp\WordCountV2`에 복사하여 업그레이드에 사용할 v2 응용 프로그램 패키지를 만듭니다. 텍스트 편집기에서 `C:\Temp\WordCountV2\ApplicationManifest.xml` 파일을 엽니다. **ApplicationManifest** 요소에서 **ApplicationTypeVersion** 특성을 "1.0.0"에서 "2.0.0"으로 변경하여 앱 버전 번호를 업데이트합니다. 변경된 ApplicationManifest.xml 파일을 저장합니다.

## <a name="task-deploy-a-service-fabric-application"></a>작업: 서비스 패브릭 응용 프로그램 배포
응용 프로그램을 빌드 및 패키지(또는 응용 프로그램 패키지를 다운로드)한 후에는 로컬 서비스 패브릭 클러스터로 응용 프로그램을 배포할 수 있습니다. 배포에는 응용 프로그램 패키지를 업로드하고 응용 프로그램 형식을 등록하며 응용 프로그램 인스턴스를 만드는 작업이 포함됩니다. 이 섹션의 지침에 따라 새 응용 프로그램을 클러스터에 배포합니다.

### <a name="step-1-upload-the-application-package"></a>1단계: 응용 프로그램 패키지 업로드
응용 프로그램 패키지를 이미지 저장소에 업로드하면 내부 서비스 패브릭 구성 요소에 의해 액세스할 수 있는 위치에 배치됩니다.  응용 프로그램 패키지에는 필요한 응용 프로그램 매니페스트, 서비스 매니페스트, 그리고 응용 프로그램 및 서비스 인스턴스를 만드는 데 필요한 코드/구성/데이터 패키지가 포함됩니다. 앱 패키지를 로컬로 확인하려는 경우 [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage) cmdlet을 사용합니다.  [Copy-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/copy-servicefabricapplicationpackage) 명령으로 패키지를 업로드합니다. 예:

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>2단계: 응용 프로그램 형식 등록
응용 프로그램 패키지 등록은 응용 프로그램 매니페스트에서 사용하기 위해 응용 프로그램 형식과 버전을 사용할 수 있도록 합니다. 시스템은 1단계에서 업로드된 패키지를 읽은 다음 패키지를 확인하며(로컬에서 [Test-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/test-servicefabricapplicationpackage)를 실행하는 경우와 동일함), 패키지 콘텐츠를 처리하고 처리된 패키지를 내부 시스템 위치에 복사합니다.  [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) cmdlet을 실행합니다.

```powershell
Register-ServiceFabricApplicationType WordCount
```
클러스터에 등록된 모든 응용 프로그램 유형을 보려면 [Get-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/get-servicefabricapplicationtype) cmdlet을 실행합니다.

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>3단계: 응용 프로그램 인스턴스 만들기
응용 프로그램은 [New-ServiceFabricApplication](/powershell/servicefabric/vlatest/new-servicefabricapplication) 명령을 사용하여 성공적으로 등록된 모든 응용 프로그램 형식 버전을 사용하여 인스턴스화될 수 있습니다. 각 응용 프로그램의 이름은 배포 시에 선언되며 반드시 **fabric:** 체계로 시작하고 각 응용 프로그램 인스턴스에 대해 고유해야 합니다. 응용 프로그램 형식 이름 및 응용 프로그램 형식 버전은 응용 프로그램 패키지의 **ApplicationManifest.xml** 파일에 선언됩니다. 기본 서비스가 대상 응용 프로그램 형식의 응용 프로그램 매니페스트에 정의된 경우 이때 이러한 서비스도 생성됩니다.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

[Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) 명령은 해당 모든 상태에 따라 성공적으로 만들어진 모든 응용 프로그램 인스턴스를 나열합니다. [Get-ServiceFabricService](/powershell/servicefabric/vlatest/get-servicefabricservice) 명령은 주어진 응용 프로그램 인스턴스 내에서 성공적으로 만들어진 모든 서비스 인스턴스를 나열합니다. 기본 서비스(있는 경우)가 나열됩니다.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>작업: 서비스 패브릭 응용 프로그램 업그레이드
업데이트된 응용 프로그램 패키지를 사용하여 이전에 배포된 서비스 패브릭 응용 프로그램을 업그레이드할 수 있습니다. 이 작업은 "작업: 서비스 패브릭 응용 프로그램 배포"에서 배포했던 WordCount 응용 프로그램을 업그레이드합니다. 자세한 내용은 [서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md) 를 읽어보세요.

이 예와 관련된 모든 작업을 단순화하기 위해 필수 조건에서 만든 WordCountV2 응용 프로그램 패키지에서 응용 프로그램 버전 번호만 업데이트 되었습니다. 보다 현실적인 시나리오에서는 서비스 코드, 구성 또는 데이터 파일을 업데이트한 다음 업데이트된 버전 번호를 사용하여 응용 프로그램을 다시 빌드하고 패키지할 것입니다.  

### <a name="step-1-upload-the-updated-application-package"></a>1단계: 업데이트된 응용 프로그램 패키지 업로드
WordCount v1 응용 프로그램이 업그레이드 준비가 완료되었습니다. 관리자 권한으로 PowerShell 창을 열고 [**Get-ServiceFabricApplication**](/powershell/servicefabric/vlatest/get-servicefabricapplication)을 입력하면 WordCount 응용 프로그램 형식의 버전 1.0.0이 배포되었음이 표시됩니다.  

이제 업데이트된 응용 프로그램 패키지를 서비스 패브릭 이미지 저장소(서비스 패브릭에 의해 응용 프로그램 패키지가 저장된 곳)에 복사합니다. 매개 변수 **ApplicationPackagePathInImageStore** 는 서비스 패브릭에 응용 프로그램 패키지를 찾을 수 있는 위치를 알립니다. 다음 명령은 응용 프로그램 패키지를 이미지 저장소의 **WordCountV2** 에 복사합니다.  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>2단계: 업데이트된 응용 프로그램 형식 등록
다음 단계에서는 Service Fabric으로 새 버전의 응용 프로그램을 등록하는 것이며 이 작업은 [Register-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/register-servicefabricapplicationtype) cmdlet을 사용하여 수행할 수 있습니다.

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>3단계: 업그레이드 시작
응용 프로그램 업그레이드에는 다양한 업그레이드 매개 변수, 제한 시간 및 상태 기준을 적용할 수 있습니다. 자세히 알아보려면 [응용 프로그램 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md) 및 [업그레이드 프로세스](service-fabric-application-upgrade.md) 문서를 읽어보세요. 모든 서비스 및 인스턴스는 업그레이드 후 *정상* 상태여야 합니다.  **HealthCheckStableDuration** 을 60초로 설정하면 서비스는 다음 업그레이드 도메인으로 업그레이드를 진행하기 전에 적어도 20초간 정상 상태로 유지됩니다.  또한 **UpgradeDomainTimeout**을 1200초로, **UpgradeTimeout**을 3000초로 설정합니다. 마지막으로 업그레이드하는 동안 문제가 발생한 경우 Service Fabric이 응용 프로그램을 이전 버전으로 롤백하도록 요청하는 **롤백**으로 **UpgradeFailureAction**을 설정합니다.

이제 [Start-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/start-servicefabricapplicationupgrade) cmdlet을 사용하여 응용 프로그램 업그레이드를 시작할 수 있습니다.

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

응용 프로그램 이름은 이전에 배포한 v1.0.0 응용 프로그램 이름과 동일합니다(fabric:/WordCount). 서비스 패브릭은 이 이름을 사용하여 업그레이드할 응용 프로그램을 식별합니다. 제한 시간을 너무 짧게 설정하면 문제 발생을 알리는 오류 메시지가 발생할 수 있습니다. [응용 프로그램 업그레이드 문제 해결](service-fabric-application-upgrade-troubleshooting.md)을 참조하거나 시간 제한을 늘리세요.

### <a name="step-4-check-upgrade-progress"></a>4단계: 업그레이드 진행률 확인
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)를 사용하거나 [Get-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade) cmdlet을 사용하여 응용 프로그램 업그레이드 진행 상태를 모니터링할 수 있습니다.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

잠시 후에 [Get-ServiceFabricApplicationUpgrade](/powershell/servicefabric/vlatest/get-servicefabricapplicationupgrade) cmdlet을 실행하면 모든 업그레이드 도메인이 업그레이드되었으며 작업이 완료되었음이 표시됩니다.

## <a name="task-test-a-service-fabric-application"></a>작업: 서비스 패브릭 응용 프로그램 테스트
고품질 서비스를 작성하려면 개발자는 불안정한 인프라 결함을 유도하여 서비스의 안정성을 테스트할 수 있어야 합니다. 서비스 패브릭은 비정상 및 장애 조치(failover) 테스트 시나리오를 사용하여 개발자에게 오류가 있는 상황에서 서비스를 테스트할 수 있도록 오류 작업을 유도하는 기능을 제공합니다.  자세한 내용은 [테스트 용이성 개요](service-fabric-testability-overview.md) 를 읽어보세요.

### <a name="step-1-run-the-chaos-test-scenario"></a>1단계: 비정상 상황 테스트 시나리오 실행
비정상 상황 테스트 시나리오는 전체 서비스 패브릭 클러스터에서 오류를 생성합니다. 이 시나리오에서는 일반적으로 수개월 또는 수년 후에 발견되는 오류를 수 시간으로 압축합니다. 인터리브 오류를 높은 오류 비율과 결합하면 놓치기 쉬운 특이한 사례를 발견할 수 있습니다. 다음 예제에서는 60분 동안 비정상 상황 테스트 시나리오를 실행합니다.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>2단계: 비정상 상황 테스트 시나리오 실행
장애 조치(failover) 테스트 시나리오는 전체 클러스터가 아닌 특정 서비스 파티션을 대상으로 하고 기타 서비스는 영향을 받지 않은 상태로 유지합니다. 비즈니스 논리가 실행되는 동안 서비스 유효성 검사에서 일련의 시뮬레이션된 오류를 통해 시나리오가 반복됩니다. 서비스 유효성 검사에서 오류가 발생하면 추가 조사가 필요한 문제가 있다는 의미입니다. 비정상 상황 테스트 시나리오에서 다중 오류가 가능한 것과는 달리 장애 조치(failover) 테스트는 한 번에 하나씩 오류를 유도할 수 있습니다. 다음 예제는 fabric:/WordCount/WordCountService 서비스에 대해 60분 동안 장애 조치 테스트를 실행합니다.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>작업: 서비스 패브릭 응용 프로그램 제거
배포된 응용 프로그램의 인스턴스를 삭제하고 클러스터에서 프로비전된 응용 프로그램 형식을 제거하며 ImageStore에서 응용 프로그램 패키지를 제거할 수 있습니다.

### <a name="step-1-remove-an-application-instance"></a>1단계: 응용 프로그램 인스턴스 제거
응용 프로그램 인스턴스가 더 이상 필요하지 않은 경우, [Remove-ServiceFabricApplication](/powershell/servicefabric/vlatest/remove-servicefabricapplication) cmdlet을 사용하여 영구적으로 제거할 수 있습니다. 이렇게 하면 응용 프로그램에 속한 모든 서비스도 자동으로 제거되므로 모든 서비스 상태가 영구적으로 제거됩니다. 이 작업은 되돌릴 수 없으며 응용 프로그램 상태는 복구할 수 없습니다.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>2단계: 응용 프로그램 형식 등록 취소
응용 프로그램 형식의 특정 버전이 더 이상 필요하지 않은 경우, [Unregister-ServiceFabricApplicationType](/powershell/servicefabric/vlatest/unregister-servicefabricapplicationtype) cmdlet을 사용하여 등록 취소할 수 있습니다. 사용하지 않는 형식을 등록 취소하면 이미지 저장소에서 해당 응용 프로그램 패키지가 사용하는 저장소 공간이 해제됩니다. 응용 프로그램 형식은 이에 대해 인스턴스화된 응용 프로그램이나 이를 참조하는 보류 중인 응용 프로그램이 없는 한 등록 취소할 수 있습니다.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>3단계: 응용 프로그램 패키지 제거
응용 프로그램 형식이 등록 취소되면 [Remove-ServiceFabricApplicationPackage](/powershell/servicefabric/vlatest/remove-servicefabricapplicationpackage) cmdlet을 사용하여 이미지 저장소에서 응용 프로그램 패키지를 제거할 수 있습니다.

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>다음 단계
[서비스 패브릭 응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)

[응용 프로그램 배포](service-fabric-deploy-remove-applications.md)

[응용 프로그램 업그레이드](service-fabric-application-upgrade.md)

[Azure Service Fabric cmdlet](/powershell/servicefabric/vlatest/servicefabric)


