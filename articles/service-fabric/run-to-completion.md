---
title: Service Fabric의 RunToCompletion 의미 체계
description: Service Fabric의 RunToCompletion 의미 체계에 대해 설명합니다.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000269"
---
# <a name="runtocompletion"></a>RunToCompletion

버전 7.1부터 Service Fabric은 **RunToCompletion** 의미 체계를 [컨테이너][containers-introduction-link] 및 [게스트 실행 파일][guest-executables-introduction-link] 애플리케이션에 지원합니다. 이러한 의미 체계는 항상 실행되는 애플리케이션 및 서비스와 달리 작업을 완료하고 종료하는 애플리케이션 및 서비스를 사용하도록 합니다.

이 문서를 계속 살펴보기 전에 먼저 [Service Fabric 애플리케이션 모델][application-model-link] 및 [Service Fabric 호스팅 모델][hosting-model-link]에 대해 잘 알아보는 것을 권장합니다.

> [!NOTE]
> RunToCompletion 의미 체계는 현재 [Reliable Services][reliable-services-link] 프로그래밍 모델을 사용하여 작성된 서비스에는 지원되지 않습니다.
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion 의미 체계 및 사양
RunToCompletion 의미 체계는 **ExecutionPolicy** 로 [ServiceManifest 가져오기][application-and-service-manifests-link]를 할 때 지정할 수 있습니다. 지정된 정책은 ServiceManifest로 구성된 모든 CodePackage에서 상속됩니다. 다음 ApplicationManifest.xml 조각은 예제를 제공합니다.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** 는 다음 두 가지 특성을 허용합니다.
* **형식:** **RunToCompletion** 은 현재 이 특성에 허용되는 유일한 값입니다.
* **다시 시작:** 이 특성은 실패 시 ServicePackage를 구성하는 CodePackage에 적용되는 다시 시작 정책을 지정합니다. **0이 아닌 종료 코드** 로 종료되는 CodePackage는 실패한 것으로 간주됩니다. 이 특성에 허용되는 값은 **OnFailure** 와 **Never** 에서 **OnFailure** 가 기본값인 경우입니다.

다시 시작 정책을 **OnFailure** 로 설정하고 CodePackage가 실패했을 경우 **(0이 아닌 종료 코드)** , 이는 다시 시작되며 반복되는 실패 사이에 백오프가 발생합니다. 다시 시작 정책을 **Never** 로 설정하면 CodePackage가 실패하는 경우 DeployedServicePackage의 배포 상태가 **Failed** 로 표시되지만 기타 CodePackage는 계속 실행될 수 있습니다. ServicePackage를 구성하는 모든 CodePackage가 성공적으로 완료 **(종료 코드 0)** 되도록 실행되면, DeployedServicePackage의 배포 상태가 **RanToCompletion** 으로 표시됩니다. 

## <a name="complete-example-using-runtocompletion-semantics"></a>RunToCompletion 의미 체계를 사용하는 전체 예제

RunToCompletion 의미 체계를 사용하는 전체 예제를 살펴보겠습니다.

> [!IMPORTANT]
> 다음 예제에서는 [Service Fabric 및 Docker를 사용하여 Windows 컨테이너 애플리케이션][containers-getting-started-link]을 만드는 방법을 알고 있다고 가정합니다.
>
> 이 예제에서는 mcr.microsoft.com/windows/nanoserver:1809를 참조합니다. Windows Server 컨테이너는 일부 버전의 호스트 OS에서 호환되지 않습니다. 자세한 내용은 [Windows 컨테이너 버전 호환성](/virtualization/windowscontainers/deploy-containers/version-compatibility)을 참조하세요.

다음 ServiceManifest.xml는 컨테이너를 나타내는 두 개의 CodePackage로 구성된 ServicePackage에 대해 설명합니다. *RunToCompletionCodePackage1* 은 **stdout** 에 메시지를 로그하고 종료합니다. *RunToCompletionCodePackage2* 는 잠시 동안 루프백 주소에 ping을 사용한 다음 종료 코드 **0**, **1** 또는 **2** 로 종료합니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

다음 ApplicationManifest.xml는 위에 설명된 ServiceManifest.xml을 기반으로 하는 애플리케이션을 설명합니다. 이는 **OnFailure** 의 다시 시작 정책을 통해 *WindowsRunToCompletionServicePackage* 에 대한 **RunToCompletion** **ExecutionPolicy** 를 지정합니다. *WindowsRunToCompletionServicePackage* 가 활성화되면 해당 구성 CodePackage가 시작됩니다. *RunToCompletionCodePackage1* 은 첫 번째 활성화에서 성공적으로 종료됩니다. 그러나 *RunToCompletionCodePackage2* 가 실패 **(0이 아닌 종료 코드)** 할 수 있습니다. 이 경우 다시 시작 정책이 **OnFailure** 이므로 다시 시작됩니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>DeployedServicePackage의 배포 상태 쿼리
DeployedServicePackage의 배포 상태는 [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link]를 사용하여 PowerShell에서 쿼리하거나 [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync(문자열, URI, 문자열)][deployed-service-package-fabricclient-link]를 사용하여 C#에서 쿼리할 수 있습니다.

## <a name="considerations-when-using-runtocompletion-semantics"></a>RunToCompletion 의미 체계 사용 시 고려 사항

현재 RunToCompletion 지원에 다음 사항을 유의해야 합니다.
* 이러한 의미 체계는 [컨테이너][containers-introduction-link] 및 [게스트 실행 파일][guest-executables-introduction-link] 애플리케이션에만 지원됩니다.
* RunToCompletion 의미 체계를 사용하는 애플리케이션에 대한 업그레이드 시나리오는 허용되지 않습니다. 필요한 경우 사용자가 이러한 애플리케이션을 삭제하고 다시 만들어야 합니다.
* 장애 조치(failover) 이벤트는 CodePackage가 같은 노드나 클러스터의 기타 노드에서 성공적으로 완료된 후 다시 실행되게 할 수 있습니다. 장애 조치(failover) 이벤트의 예시에는 노드 다시 시작과 노드에서의 Service Fabric 런타임 업그레이드가 있습니다.

## <a name="next-steps"></a>다음 단계

관련 정보는 다음 문서를 참조하세요.

* [Service Fabric 및 컨테이너][containers-introduction-link]
* [Service Fabric 및 게스트 실행 파일][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync
