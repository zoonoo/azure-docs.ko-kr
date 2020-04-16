---
title: RunTo서비스 패브릭의 완료 의미론
description: 서비스 패브릭에서 RunToCompletion 의미체계에 대해 설명합니다.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431294"
---
# <a name="runtocompletion"></a>실행 완료

버전 7.1부터 서비스 패브릭은 [컨테이너][containers-introduction-link] 및 [게스트 실행 응용][guest-executables-introduction-link] 프로그램에 대한 **RunToCompletion** 시맨틱을 지원합니다. 이러한 의미 체계는 항상 응용 프로그램 및 서비스를 실행하는 것과 는 달리 작업을 완료하고 종료하는 응용 프로그램 및 서비스를 활성화합니다.

이 문서를 진행하기 전에 [서비스 패브릭 응용 프로그램 모델][application-model-link] 및 서비스 패브릭 호스팅 [모델에][hosting-model-link]익숙해지는 것이 좋습니다.

> [!NOTE]
> RunToCompletion 의미 체계는 현재 신뢰할 수 있는 [서비스][reliable-services-link] 프로그래밍 모델을 사용 하 여 작성 된 서비스에 대 한 지원 되지 않습니다.
 
## <a name="runtocompletion-semantics-and-specification"></a>RunTo완성 시맨틱 및 사양
RunToCompletion 의미 체계는 [ServiceManifest][application-and-service-manifests-link]를 가져올 때 **실행 정책으로** 지정할 수 있습니다. 지정된 정책은 ServiceManifest를 포함하는 모든 CodePackage에 의해 상속됩니다. 다음 ApplicationManifest.xml 스니펫은 예제를 제공합니다.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy는** 다음과 같은 두 가지 특성을 허용합니다.
* **유형:** **RunToCompletion은** 현재 이 특성에 대해 허용되는 유일한 값입니다.
* **다시 시작:** 이 특성은 서비스 패키지를 포함하는 CodePackage에 적용되는 다시 시작 정책을 실패시 지정합니다. **0이 아닌 종료 코드로** 종료되는 CodePackage는 실패한 것으로 간주됩니다. 이 특성에 대해 허용된 값은 **OnFailure이며** **OnFailure이** 기본값인 **경우 는 안** 됩니다.

다시 시작 정책이 **OnFailure로**설정되면 **CodePackage가 실패(0이 아닌 종료 코드)가**실패하면 반복된 실패 간에 백오프가 있는 다시 시작됩니다. 다시 시작 정책이 **Never로**설정된 경우 CodePackage가 실패하면 DeployServicePackage의 배포 상태가 **실패한** 것으로 표시되지만 다른 CodePackage는 실행을 계속할 수 있습니다. ServicePackage를 포함하는 모든 코드 패키지가 성공적으로 **완료(종료 코드 0)로**실행되면 DeployServicePackage의 배포 상태가 **RanToCompletion로**표시됩니다. 

## <a name="complete-example-using-runtocompletion-semantics"></a>RunToCompletion 시맨틱을 사용한 예제 완성

RunToCompletion 시맨틱을 사용하여 전체 예제를 살펴보겠습니다.

> [!IMPORTANT]
> 다음 예제는 서비스 패브릭 [및 Docker를 사용하여 Windows 컨테이너 응용 프로그램을][containers-getting-started-link]만드는 데 익숙하다고 가정합니다.
>
> 이 예제는 mcr.microsoft.com/windows/nanoserver:1809 참조합니다. Windows Server 컨테이너는 일부 버전의 호스트 OS에서 호환되지 않습니다. 자세한 내용은 [Windows 컨테이너 버전 호환성](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)을 참조하세요.

다음 ServiceManifest.xml은 컨테이너를 나타내는 두 개의 코드 패키지로 구성된 ServicePackage를 설명합니다. *RunToCompletionCodePackage1은* **stdout** 및 종료에 메시지를 기록합니다. *RunToCompletionCodePackage2는* 루프백 주소를 잠시 동안 ping한 다음 **0,** **1** 또는 **2의**종료 코드로 종료합니다.

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

다음 ApplicationManifest.xml은 위에서 설명한 ServiceManifest.xml을 기반으로 하는 응용 프로그램에 대해 설명합니다. *WindowsRunToCompletionServicePackage에* 대 한 **RunToCompletion** **실행 정책을** 지정 합니다 **.실패의**다시 시작 정책 . *WindowsRunTo완료서비스패키지의*활성화시, 그 구성 코드 패키지가 시작됩니다. *RunToCompleteCodePackage1은* 첫 번째 정품 인증에서 성공적으로 종료되어야 합니다. 그러나 *RunToCompletionCodePackage2는* **실패(0이 아닌 종료 코드)가**발생할 수 있으며, 이 경우 다시 시작 정책이 **OnFailure이므로**다시 시작됩니다.

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
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>배포된 서비스 패키지의 배포 상태 쿼리
배포된 ServicePackage의 배포 상태는 [Get-ServiceFabricDeployServicePackage를][deployed-service-package-link] 사용하여 PowerShell에서 쿼리하거나 [FabricClient][fabric-client-link] API [GetDeployServiceListAsync(문자열, Uri, 문자열)를][deployed-service-package-fabricclient-link] 사용하여 C#에서 쿼리할 수 있습니다.

## <a name="considerations-when-using-runtocompletion-semantics"></a>RunToCompletion 시맨틱을 사용할 때고려해야 사항

현재 RunToCompletion 지원에 대해 다음 사항을 기록해야 합니다.
* 이러한 의미 체계는 [컨테이너][containers-introduction-link] 및 [게스트 실행 응용][guest-executables-introduction-link] 프로그램에대해서만 지원됩니다.
* RunToCompletion 의미 체계를 사용 하 고 응용 프로그램에 대 한 업그레이드 시나리오는 허용 되지 않습니다. 필요한 경우 사용자는 이러한 응용 프로그램을 삭제하고 다시 만들어야 합니다.
* 장애 조치 이벤트로 인해 CodePackage가 성공적으로 완료된 후 동일한 노드 또는 클러스터의 다른 노드에서 다시 실행될 수 있습니다. 장애 조치 이벤트의 예로는 노드에서 노드가 다시 시작되고 노드에서 서비스 패브릭 런타임 업그레이드가 있습니다.

## <a name="next-steps"></a>다음 단계

관련 정보는 다음 문서를 참조하십시오.

* [서비스 패브릭 및 컨테이너.][containers-introduction-link]
* [서비스 패브릭 및 게스트 실행.][guest-executables-introduction-link]

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
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync

