---
title: Service Fabric의 이니셜라이저 CodePackage
description: Service Fabric의 이니셜라이저 CodePackage에 대해 설명합니다.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 3be079b97c2660437344f88203fdda06cc6d6740
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86258968"
---
# <a name="initializer-codepackages"></a>이니셜라이저 CodePackage

버전 7.1부터는 Service Fabric에서 [컨테이너][containers-introduction-link] 및 [게스트 실행 파일][guest-executables-introduction-link] 애플리케이션에 **이니셜라이저 CodePackage** 를 지원합니다. 이니셜라이저 CodePackage는 다른 CodePackage가 실행을 시작하기 전에 ServicePackage 범위에서 초기화를 수행할 기회를 제공합니다. 이니셜라이저 CodePackage 및 ServicePackage의 관계는 [SetupEntryPoint][setup-entry-point-link] 및 CodePackage의 관계와 유사합니다.

이 문서를 계속 살펴보기 전에 먼저 [Service Fabric 애플리케이션 모델][application-model-link] 및 [Service Fabric 호스팅 모델][hosting-model-link]에 대해 잘 알아보는 것이 좋습니다.

> [!NOTE]
> 현재 [Reliable Services][reliable-services-link] 프로그래밍 모델로 작성된 서비스에는 이니셜라이저 CodePackage가 지원되지 않습니다.
 
## <a name="semantics"></a>의미 체계

이니셜라이저 CodePackage는 **성공적으로 완료(종료 코드 0)** 될 때까지 실행됩니다. 실패한 이니셜라이저 CodePackage는 성공적으로 완료될 때까지 다시 시작됩니다. 이니셜라이저 CodePackage는 여러 개를 사용할 수 있으며 ServicePackage의 다른 CodePackage가 실행을 시작하기 전에 **지정된 순서에 따라** **성공적으로 완료** 될 때까지 **순차적으로** 실행됩니다.

## <a name="specifying-initializer-codepackages"></a>이니셜라이저 CodePackage 지정
ServiceManifest에서 **이니셜라이저** 특성을 **true** 로 설정하여 CodePackage를 이니셜라이저로 표시할 수 있습니다. 이니셜라이저 CodePackage가 여러 개인 경우 **ExecOrder** 특성을 통해 실행 순서를 지정할 수 있습니다. **ExecOrder** 는 음수가 아닌 정수여야 하며 이니셜라이저 CodePackage에만 유효합니다. **ExecOrder** 값이 작으면 이니셜라이저 CodePackage가 먼저 실행됩니다. 이니셜라이저 CodePackage의 **ExecOrder** 가 지정되지 않은 경우 기본값인 0이라고 가정합니다. **ExecOrder** 값이 동일한 이니셜라이저 CodePackage의 상대적 실행 순서는 지정되지 않습니다.

다음 ServiceManifest 코드 조각에서는 세 개의 CodePackage에 대해 설명하며 이 CodePackage 중 두 개는 이니셜라이저로 표시되어 있습니다. 이 ServicePackage가 활성화되면 **ExecOrder** 값이 가장 낮은 *InitCodePackage0* 이 먼저 실행됩니다. *InitCodePackage0* 이 성공적으로 완료되면(종료 코드 0) *InitCodePackage1* 이 실행됩니다. 마지막으로 *InitCodePackage1* 이 성공적으로 완료되면 *WorkloadCodePackage* 가 실행됩니다.

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
## <a name="complete-example-using-initializer-codepackages"></a>이니셜라이저 CodePackage를 사용하는 전체 예제

이니셜라이저 CodePackage를 사용하는 전체 예제를 살펴보겠습니다.

> [!IMPORTANT]
> 다음 예제에서는 [Service Fabric 및 Docker를 사용하여 Windows 컨테이너 애플리케이션][containers-getting-started-link]을 만드는 방법을 알고 있다고 가정합니다.
>
> 이 예제에서는 mcr.microsoft.com/windows/nanoserver:1809를 참조합니다. Windows Server 컨테이너는 일부 버전의 호스트 OS에서 호환되지 않습니다. 자세한 내용은 [Windows 컨테이너 버전 호환성](/virtualization/windowscontainers/deploy-containers/version-compatibility)을 참조하세요.

다음 ServiceManifest.xml은 앞에서 설명한 ServiceManifest 코드 조각을 기반으로 빌드됩니다. *InitCodePackage0*, *InitCodePackage1* 및 *WorkloadCodePackage* 는 컨테이너를 나타내는 CodePackage입니다. 활성화되면 *InitCodePackage0* 이 제일 먼저 실행됩니다. 이 CodePackage는 파일에 메시지를 기록하고 종료됩니다. 다음에는 *InitCodePackage1* 이 실행되며, 이 CodePackage도 파일에 메시지를 기록하고 종료됩니다. 마지막으로 *WorkloadCodePackage* 가 실행을 시작합니다. 이 CodePackage는 파일에 메시지를 기록하고 파일의 콘텐츠를 **stdout** 로 출력한 다음 영구적으로 ping합니다.

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

다음 ApplicationManifest.xml에서는 위에 설명된 ServiceManifest.xml을 기반으로 하는 애플리케이션을 설명합니다. 모든 컨테이너에 동일한 **볼륨** 탑재가 지정됩니다. 즉, **C:\WorkspaceOnHost** 는 세 컨테이너 모두에서 **C:\WorkspaceOnContainer** 에 탑재됩니다. 결과적으로 모든 컨테이너는 활성화된 순서대로 동일한 로그 파일에 쓰게 됩니다.

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
ServicePackage가 성공적으로 활성화되면 **C:\WorkspaceOnHost\log.txt** 의 콘텐츠는 다음과 같습니다.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

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
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
