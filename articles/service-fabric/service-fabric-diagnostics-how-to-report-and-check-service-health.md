<properties
   pageTitle="Azure 서비스 패브릭을 사용하여 상태를 보고하고 확인 | Microsoft Azure"
   description="Azure 서비스 패브릭에서 제공하는 상태 모니터링 도구를 사용하여 서비스 코드에서 상태 보고서를 보내고 서비스의 상태를 확인하는 방법에 대해 알아보세요."
   services="service-fabric"
   documentationCenter=".net"
   authors="punewa"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/18/2016"
   ms.author="toddabel"/>

# 서비스 상태 보고 및 확인
서비스에 문제가 발생할 때 결과 인시던트 및 중단에 응답하고 수정하는 능력은 문제를 빠르게 검색할 수 있는 능력과 밀접한 관련이 있습니다. 서비스 코드에서 서비스 패브릭 상태 관리자로 문제 및 오류를 보고하면 서비스 패브릭이 제공하는 표준 상태 모니터링 도구를 사용하여 상태를 확인할 수 있습니다.

두 가지 방법으로 서비스에서 상태를 보고할 수 있습니다.

1. [Partition](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx) 또는 [CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx) 개체 사용. `Partition` 및 `CodePackageActivationContext` 개체를 사용하여 현재 컨텍스트의 일부인 요소에 대한 상태를 보고할 수 있습니다. 예를 들어 복제본의 일부로 실행되는 코드는 해당 복제본, 복제본이 속하는 파티션 및 응용 프로그램에 대해서만 상태를 보고할 수 있습니다.

2. `FabricClient` 사용. `FabricClient`를 사용하면 클러스터가 [보안](service-fabric-cluster-security.md) 상태가 아니거나 서비스가 관리자 권한으로 실행되는 경우에만 서비스 코드에서 상태를 보고할 수 있습니다. 대부분의 실제 시나리오에서는 이 경우가 해당되지 않습니다. FabricClient를 사용하면 클러스터의 일부인 모든 엔터티의 상태를 보고할 수 있습니다. 하지만 이상적으로 서비스 코드는 자체 상태와 관련된 보고서만 보내야 합니다.

이 문서에서는 서비스 코드에서 상태를 보고하는 예제를 안내하고 서비스 패브릭이 제공하는 도구를 사용하여 상태를 확인할 수 있는 방법을 보여 줍니다. 이 문서는 서비스 패브릭의 상태 모니터링 기능을 신속하게 소개할 목적으로 작성되었습니다. 보다 자세한 내용은 이 문서의 마지막 부분에 있는 링크를 누르면 시작되는 상태에 대한 심화 문서 시리즈를 통해 알아볼 수 있습니다.

## 필수 조건
다음이 설치되어 있어야 합니다.

   * Visual Studio 2015
   * 서비스 패브릭 SDK

## 로컬 보안 개발자 클러스터를 만들려면
관리자 권한으로 PowerShell을 시작하고 다음 명령을 실행합니다.

![보안 개발자 클러스터 만들기](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## 응용 프로그램을 배포하고 해당 상태를 확인하려면
응용 프로그램을 배포하고 해당 상태를 확인하려면 다음 단계를 수행합니다.

1. 관리자 권한으로 Visual Studio를 시작합니다.

2. 상태 저장 서비스에 대한 프로젝트를 만듭니다.

    ![상태 저장 서비스를 사용하여 서비스 패브릭 응용 프로그램 만들기](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. **F5** 키를 눌러 디버그 모드에서 응용 프로그램을 실행합니다. 응용 프로그램이 로컬 클러스터에 배포됩니다.

4. 응용 프로그램이 실행 중인 경우 로컬 클러스터 관리자 시스템 트레이 앱을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **로컬 클러스터 관리**를 선택하여 서비스 패브릭 탐색기를 시작합니다.

    ![시스템 트레이에서 서비스 패브릭 탐색기 시작](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. 응용 프로그램의 상태는 다음 이미지와 같이 표시되어야 합니다. 이때 응용 프로그램은 오류 없이 정상이어야 합니다.

    ![서비스 패브릭 탐색기에서 정상적인 응용 프로그램](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. PowerShell을 사용하여 상태를 확인할 수도 있습니다. ```Get-ServiceFabricApplicationHealth```를 사용하여 응용 프로그램의 상태를 확인하고 ```Get-ServiceFabricServiceHealth```를 사용하여 서비스 상태를 확인할 수 있습니다. 동일한 응용 프로그램에 대해 PowerShell에서 제공하는 상태 보고서는 다음과 같습니다.

    ![PowerShell에서 정상적인 응용 프로그램](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## 서비스 코드에 사용자 지정 상태 이벤트를 추가하려면
서버 패브릭 Visual Studio 프로젝트 템플릿에는 샘플 코드가 포함됩니다. 아래 단계는 서비스 코드에서 사용자 지정 상태 이벤트를 보고할 수 있는 방법을 보여 줍니다. 이런 보고서는 서비스 패브릭 탐색기, Azure 포털 상태 보기 및 PowerShell과 같이 상태 모니터링을 위해 서비스 패브릭이 제공하는 표준 도구에 자동으로 표시됩니다.

1. 위에서 Visual Studio로 만든 응용 프로그램을 다시 열거나 Visual Studio 템플릿의 상태 저장 서비스를 사용하여 새 응용 프로그램을 만듭니다.

2. 다음으로 **Stateful1.cs** 파일을 열고 *RunAsync* 메서드에서 `myDictionary.TryGetValueAsync` 호출을 조회합니다. 이 응용 프로그램의 핵심 논리는 카운트 실행을 계속 유지하는 것이므로 카운터의 현재 값을 보유하는 `result`를 반환하는 것을 볼 수 있습니다. 실제 응용 프로그램에서 결과 부족이 오류를 나타낸 경우 상태를 통해 플래그를 지정할 수 있습니다.

3. 오류를 나타내는 결과 부족에 대한 상태 이벤트를 보고하려면 `myDictionary.TryGetValueAsync` 호출 이후 아래 코드를 추가합니다. 이 복제본 상태는 상태 저장 서비스에서 보고되므로 해당 상태를 보고합니다. `HealthInformation` 매개 변수가 보고된 상태 문제에 대한 정보를 저장합니다. 이 네임스페이스를 **Stateful1.cs** 파일에 추가합니다.

    ```csharp
    using System.Fabric.Health;
    ```

    `myDictionary.TryGetValueAsync` 호출 뒤에 이 코드를 추가합니다.

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```

    상태 비저장 서비스의 경우 다음 코드를 사용합니다.

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

    서비스가 관리자 권한으로 실행되거나 클러스터가 [보안](service-fabric-cluster-security.md) 상태가 아닌 경우 아래와 같이 FabricClient를 사용하여 상태를 보고할 수도 있습니다.

    `var myDictionary` 선언 후 FabricClient 만들기

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    또한 `myDictionary.TryGetValueAsync` 호출 후에 이 코드를 추가합니다.

    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

4. 이 오류를 시뮬레이트하여 상태 모니터링 도구에서 오류가 표시되는지 확인합니다. 오류를 시뮬레이트하려면 위에서 추가한 상태 보고 코드의 첫 번째 줄을 주석 처리합니다. 첫 번째 줄을 주석 처리하고 나면 코드가 아래와 같이 표시됩니다. 그러면 이제 RunAsync가 실행될 때마다 이 상태 보고서를 발생시킵니다. 변경 작업을 수행한 후 **F5** 키를 사용하여 응용 프로그램을 실행합니다.

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```

5. 응용 프로그램이 실행 중이면 서비스 패브릭 탐색기를 열어 응용 프로그램의 상태를 확인합니다. 이번에는 서비스 패브릭 탐색기가 응용 프로그램이 비정상이라고 표시합니다. 이는 위에서 추가한 코드에서 보고된 오류 때문입니다.

    ![서비스 패브릭 탐색기에서 비정상적인 응용 프로그램](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

6. 서비스 패브릭 탐색기의 트리 뷰에서 주 복제본을 선택하면 상태가 역시 오류로 표시되고 코드에서 `HealthInformation` 매개 변수에 추가된 상태 보고서 세부 정보도 표시됩니다. PowerShell 및 Azure 포털에서도 동일한 상태 보고서를 볼 수 있습니다.

    ![서비스 패브릭 탐색기 내 복제본 상태](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

이 보고서는 다른 보고서로 대체되거나 이 복제본이 삭제될 때까지 상태 관리자에 그대로 남아 있습니다. HealthInformation 개체에서 이 상태 보고서에 대해 TimeToLive를 설정하지 않았기 때문에 보고서가 만료되지 않습니다.

가장 세분화된 수준(위 경우에서는 복제본)에서 상태를 보고하는 것이 좋습니다. `Partition`에 대해 상태를 보고할 수도 있습니다.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

`Application`, `DeployedApplication` 및 `DeployedServicePackage`에 대한 상태 보고를 위해 `CodePackageActivationContext` 사용

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## 다음 단계
[서비스 패브릭 상태 심층 분석](service-fabric-health-introduction.md)

<!---HONumber=AcomDC_0323_2016-->