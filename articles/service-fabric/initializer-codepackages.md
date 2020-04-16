---
title: 서비스 패브릭의 이니시저 코드 패키지
description: 서비스 패브릭의 이니셜 라이저 코드 패키지에 대해 설명합니다.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430631"
---
# <a name="initializer-codepackages"></a>이시더 코드 패키지

버전 7.1부터 서비스 패브릭은 [컨테이너][containers-introduction-link] 및 게스트 [실행 응용][guest-executables-introduction-link] 프로그램에 대한 **Initializer 코드 패키지를** 지원합니다. Initializer 코드 패키지는 다른 코드 패키지 실행을 시작하기 전에 ServicePackage 범위에서 초기화를 수행할 수 있는 기회를 제공합니다. 서비스 패키지와의 관계는 [SetupEntryPoint가][setup-entry-point-link] 코드 패키지에 대한 것과 유사합니다.

이 문서를 진행하기 전에 [서비스 패브릭 응용 프로그램 모델][application-model-link] 및 서비스 패브릭 호스팅 [모델에][hosting-model-link]익숙해지는 것이 좋습니다.

> [!NOTE]
> Initializer CodePackage는 현재 [신뢰할 수 있는 서비스][reliable-services-link] 프로그래밍 모델을 사용 하 여 작성 된 서비스에 대 한 지원 되지 않습니다.
 
## <a name="semantics"></a>의미 체계

Initializer 코드 패키지는 성공적으로 **완료될 것으로 예상됩니다(종료 코드 0).** 실패한 초기화자 코드 패키지가 성공적으로 완료될 때까지 다시 시작됩니다. 여러 Initializer 코드 패키지가 허용되며 ServicePackage의 다른 CodePackage가 실행을 시작하기 전에 **지정된 순서로** **순차적으로**성공적으로 **완료될**수 있습니다.

## <a name="specifying-initializer-codepackages"></a>초기화자 코드 패키지 지정
ServiceManifest에서 **Initializer** 특성을 **true로** 설정하여 CodePackage를 초기화자로 표시할 수 있습니다. 여러 Initializer 코드 패키지가 있는 경우 **ExecOrder** 특성을 통해 실행 순서를 지정할 수 있습니다. **ExecOrder는** 음수가 아닌 정수여야 하며 초기화자 코드 패키지에만 유효합니다. **ExecOrder의** 값이 낮은 초기화 자 코드 패키지가 먼저 실행됩니다. 초기화자 코드패키지에 **대해 ExecOrder를** 지정하지 않으면 기본값 0이 가정됩니다. **ExecOrder의** 동일한 값을 가진 Initializer 코드 패키지의 상대 실행 순서는 지정되지 않습니다.

다음 ServiceManifest 코드 조각은 세 개의 CodePackage 두 가지를 설명하며 그 중 두 개는 초기화자로 표시됩니다. 이 서비스 패키지가 활성화되면 *InitCodePackage0는* **ExecOrder**의 값이 가장 낮기 때문에 먼저 실행됩니다. *InitCodePackage0의*성공적인 완료(종료 코드 0)에 *InitCodePackage1이* 실행됩니다. 마지막으로 *InitCodePackage1을*성공적으로 완료하면 *워크로드CodePackage가* 실행됩니다.

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>초기화자 코드 패키지를 사용한 완전한 예제

Initializer 코드 패키지를 사용하여 전체 예제를 살펴보겠습니다.

> [!IMPORTANT]
> 다음 예제는 서비스 패브릭 [및 Docker를 사용하여 Windows 컨테이너 응용 프로그램을][containers-getting-started-link]만드는 데 익숙하다고 가정합니다.
>
> 이 예제는 mcr.microsoft.com/windows/nanoserver:1809 참조합니다. Windows Server 컨테이너는 일부 버전의 호스트 OS에서 호환되지 않습니다. 자세한 내용은 [Windows 컨테이너 버전 호환성](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)을 참조하세요.

다음 ServiceManifest.xml은 앞서 설명한 ServiceManifest 스니펫을 기반으로 합니다. *InitCodePackage0*, *InitCodePackage1* 및 *워크로드코드패키지는* 컨테이너를 나타내는 코드 패키지입니다. 활성화시 *InitCodePackage0이* 먼저 실행됩니다. 파일에 메시지를 기록하고 종료합니다. 다음으로 *InitCodePackage1이* 실행되어 파일에 메시지를 기록하고 종료합니다. 마지막으로 *워크로드코드패키지가* 실행을 시작합니다. 또한 메시지를 파일에 기록하고 파일 내용을 **stdout에** 출력한 다음 영원히 ping합니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

다음 ApplicationManifest.xml은 위에서 설명한 ServiceManifest.xml을 기반으로 하는 응용 프로그램에 대해 설명합니다. 모든 컨테이너에 대해 동일한 **볼륨** 마운트(예: **C:\WorkspaceOnHost)가** 세 컨테이너 모두에 **C:\WorkspaceOnContainer에** 탑재되어 있음을 명시합니다. 그 결과 모든 컨테이너가 활성화된 순서대로 동일한 로그 파일에 기록됩니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
ServicePackage가 성공적으로 활성화되면 **C:\WorkspaceOnHost\log.txt의** 내용은 다음과 같은 내용이어야 합니다.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

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
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

