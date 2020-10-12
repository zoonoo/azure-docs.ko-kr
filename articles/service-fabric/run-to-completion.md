---
title: Service Fabric의 RunToCompletion 의미 체계
description: Service Fabric의 RunToCompletion 의미 체계에 대해 설명 합니다.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261212"
---
# <a name="runtocompletion"></a>RunToCompletion

버전 7.1부터 Service Fabric는 [컨테이너][containers-introduction-link] 및 [게스트 실행 파일][guest-executables-introduction-link] 응용 프로그램에 대 한 **runtocompletion** 의미 체계를 지원 합니다. 이러한 의미 체계를 통해 응용 프로그램 및 서비스를 사용 하 여 작업을 완료 하 고 종료 하는 응용 프로그램 및 서비스를 실행할 수 있습니다.

이 문서를 진행 하기 전에 [Service Fabric 응용 프로그램 모델][application-model-link] 및 [Service Fabric 호스팅 모델][hosting-model-link]에 대해 잘 알고 있는 것이 좋습니다.

> [!NOTE]
> RunToCompletion 의미 체계는 현재 [Reliable Services][reliable-services-link] 프로그래밍 모델을 사용 하 여 작성 된 서비스에 대해 지원 되지 않습니다.
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion 의미 체계 및 사양
RunToCompletion 의미 체계 [는 servicemanifest.xml를 가져올][application-and-service-manifests-link]때 **set-executionpolicy** 으로 지정할 수 있습니다. 지정 된 정책은 Servicemanifest.xml를 구성 하는 모든 CodePackages 상속 됩니다. 다음 ApplicationManifest.xml 코드 조각에서 예제를 제공 합니다.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**Set-executionpolicy** 는 다음과 같은 두 가지 특성을 허용 합니다.
* **유형:** **runtocompletion** 는 현재이 특성에 허용 되는 유일한 값입니다.
* **다시 시작:** 이 특성은 오류 발생 시 ServicePackage를 구성 하는 CodePackages에 적용 되는 다시 시작 정책을 지정 합니다. **0이 아닌 종료 코드로** 종료 하는 CodePackage는 실패 한 것으로 간주 됩니다. 이 특성에 허용 되는 값은 **onfailure** 이며 기본값은 **onfailure** 가 **아닙니다** .

다시 시작 정책을 **Onfailure**로 설정 하면 CodePackage 실패 하는 경우 **(0이 아닌 종료 코드)** 다시 시작 되 고 반복 되는 오류 간의 백오프를 포함 합니다. 다시 시작 정책을 사용 **안 함**으로 설정 하면 CodePackage 실패 하는 경우 DeployedServicePackage의 배포 상태가 **Failed** 로 표시 되지만 다른 CodePackages는 실행을 계속할 수 있습니다. ServicePackage를 구성 하는 모든 CodePackages 성공적으로 완료 되 면 **(종료 코드 0)** DeployedServicePackage의 배포 상태는 **RanToCompletion**로 표시 됩니다. 

## <a name="complete-example-using-runtocompletion-semantics"></a>RunToCompletion 의미 체계를 사용 하는 전체 예제

RunToCompletion 의미 체계를 사용 하는 전체 예제를 살펴보겠습니다.

> [!IMPORTANT]
> 다음 예제에서는 [Service Fabric 및 Docker를 사용 하 여 Windows 컨테이너 응용 프로그램][containers-getting-started-link]을 만드는 방법을 알고 있다고 가정 합니다.
>
> 이 예제에서는 mcr.microsoft.com/windows/nanoserver:1809를 참조 합니다. Windows Server 컨테이너는 일부 버전의 호스트 OS에서 호환되지 않습니다. 자세한 내용은 [Windows 컨테이너 버전 호환성](/virtualization/windowscontainers/deploy-containers/version-compatibility)을 참조하세요.

다음 ServiceManifest.xml는 컨테이너를 나타내는 두 CodePackages로 구성 된 ServicePackage에 대해 설명 합니다. *RunToCompletionCodePackage1* 는 **stdout** 에 메시지를 기록 하 고 종료 합니다. *RunToCompletionCodePackage2* 는 잠시 동안 루프백 주소를 ping 한 후 종료 코드 **0**, **1** 또는 **2**를 사용 하 여 종료 합니다.

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

다음 ApplicationManifest.xml 위에서 설명한 ServiceManifest.xml 기반으로 하는 응용 프로그램을 설명 합니다. **Onfailure**의 다시 시작 정책으로 *WindowsRunToCompletionServicePackage* 에 대해 **runtocompletion** **set-executionpolicy** 를 지정 합니다. *WindowsRunToCompletionServicePackage*가 활성화 되 면 해당 구성 CodePackages 시작 됩니다. *RunToCompletionCodePackage1* 는 첫 번째 활성화에서 성공적으로 종료 됩니다. 그러나 *RunToCompletionCodePackage2* 가 실패할 수 있습니다 **(0이 아닌 종료 코드)**.이 경우 다시 시작 정책이 **onfailure**이므로 다시 시작 됩니다.

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
DeployedServicePackage의 배포 상태는 [ServiceFabricDeployedServicePackage][deployed-service-package-link] 를 사용 하 여 PowerShell에서 쿼리하거나 [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync (문자열, Uri, 문자열)][deployed-service-package-fabricclient-link] 를 사용 하 여 c #에서 쿼리할 수 있습니다.

## <a name="considerations-when-using-runtocompletion-semantics"></a>RunToCompletion 의미 체계 사용 시 고려 사항

현재 RunToCompletion 지원에 대 한 다음 사항을 유의 해야 합니다.
* 이러한 의미 체계는 [컨테이너][containers-introduction-link] 및 [게스트 실행 파일][guest-executables-introduction-link] 응용 프로그램에 대해서만 지원 됩니다.
* RunToCompletion 의미 체계를 사용 하는 응용 프로그램에 대 한 업그레이드 시나리오는 허용 되지 않습니다. 필요한 경우 사용자가 이러한 응용 프로그램을 삭제 하 고 다시 만들어야 합니다.
* 장애 조치 (Failover) 이벤트로 인해 성공적으로 완료 된 후, 동일한 노드 또는 클러스터의 다른 노드에서 CodePackages을 다시 실행할 수 있습니다. 장애 조치 (failover) 이벤트의 예로는 노드에서 노드를 다시 시작 하 고 런타임 업그레이드를 Service Fabric 합니다.

## <a name="next-steps"></a>다음 단계

관련 정보는 다음 문서를 참조 하세요.

* [Service Fabric 및 컨테이너][containers-introduction-link]
* [Service Fabric 및 게스트 실행 파일.][guest-executables-introduction-link]

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
