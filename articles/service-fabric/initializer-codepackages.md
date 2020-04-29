---
title: Service Fabric 이니셜라이저 CodePackages
description: Service Fabric 이니셜라이저 CodePackages 설명 합니다.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430631"
---
# <a name="initializer-codepackages"></a>이니셜라이저 CodePackage

버전 7.1부터 Service Fabric는 [컨테이너][containers-introduction-link] 및 [게스트 실행 파일][guest-executables-introduction-link] 응용 프로그램에 대 한 **이니셜라이저 CodePackages** 지원 합니다. 이니셜라이저 CodePackages 다른 CodePackages 실행을 시작 하기 전에 ServicePackage 범위에서 초기화를 수행할 수 있는 기회를 제공 합니다. ServicePackage와의 관계는 CodePackage에 대 한 [Setupentrypoint][setup-entry-point-link] 와 유사 합니다.

이 문서를 진행 하기 전에 [Service Fabric 응용 프로그램 모델][application-model-link] 및 [Service Fabric 호스팅 모델][hosting-model-link]에 대해 잘 알고 있는 것이 좋습니다.

> [!NOTE]
> 이니셜라이저 CodePackages는 현재 [Reliable Services][reliable-services-link] 프로그래밍 모델을 사용 하 여 작성 된 서비스에 대해 지원 되지 않습니다.
 
## <a name="semantics"></a>의미 체계

이니셜라이저 CodePackage가 성공적으로 완료 될 때까지 실행 되어야 합니다 **(종료 코드 0)**. 실패 한 이니셜라이저 CodePackage 성공적으로 완료 될 때까지 다시 시작 됩니다. 여러 이니셜라이저 CodePackages 허용 되며, ServicePackage begin 실행의 다른 CodePackages 전에 **지정 된 순서에 따라** 순차적으로 **성공적으로 완료**될 때까지 **순차적**으로 실행 됩니다.

## <a name="specifying-initializer-codepackages"></a>이니셜라이저 CodePackages 지정
Servicemanifest.xml에서 **이니셜라이저** 특성을 **true** 로 설정 하 여 CodePackage을 이니셜라이저로 표시할 수 있습니다. 이니셜라이저가 여러 개 있는 경우에는 **execorder** 특성을 통해 실행 순서를 지정할 수 있습니다. **Execorder** 는 음수가 아닌 정수 여야 하며 이니셜라이저 CodePackages 유효 합니다. **Execorder** 값이 낮은 이니셜라이저 CodePackages 먼저 실행 됩니다. 이니셜라이저 CodePackage에 대해 **Execorder** 가 지정 되지 않은 경우 기본값 0이 가정 됩니다. **Execorder** 값이 같은 이니셜라이저 CodePackages의 상대적 실행 순서는 지정 되지 않습니다.

다음 Servicemanifest.xml 코드 조각에서는 이니셜라이저로 표시 되는 세 가지 CodePackages를 설명 합니다. 이 ServicePackage가 활성화 되 면 **order order**의 가장 낮은 값을 가지 므로 *InitCodePackage0* 이 먼저 실행 됩니다. *InitCodePackage0*의 성공적으로 완료 되 면 (종료 코드 0) *InitCodePackage1* 이 실행 됩니다. 마지막으로 *InitCodePackage1*가 성공적으로 완료 되 면 *WorkloadCodePackage* 이 실행 됩니다.

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
## <a name="complete-example-using-initializer-codepackages"></a>이니셜라이저 CodePackages를 사용 하는 전체 예제

이니셜라이저 CodePackages를 사용 하는 전체 예제를 살펴보겠습니다.

> [!IMPORTANT]
> 다음 예제에서는 [Service Fabric 및 Docker를 사용 하 여 Windows 컨테이너 응용 프로그램][containers-getting-started-link]을 만드는 방법을 알고 있다고 가정 합니다.
>
> 이 예제에서는 mcr.microsoft.com/windows/nanoserver:1809를 참조 합니다. Windows Server 컨테이너는 일부 버전의 호스트 OS에서 호환되지 않습니다. 자세한 내용은 [Windows 컨테이너 버전 호환성](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)을 참조하세요.

다음 Servicemanifest.xml는 앞에서 설명한 Servicemanifest.xml 코드 조각을 기반으로 합니다. *InitCodePackage0*, *InitCodePackage1* 및 *WorkloadCodePackage* 는 컨테이너를 나타내는 CodePackages입니다. 활성화 되 면 *InitCodePackage0* 가 먼저 실행 됩니다. 메시지를 파일에 기록 하 고 종료 합니다. 그런 다음 *InitCodePackage1* 를 실행 하 여 메시지를 파일에 기록 하 고 종료 합니다. 마지막으로 *WorkloadCodePackage* 실행을 시작 합니다. 또한 파일에 메시지를 기록 하 고, 파일의 내용을 **stdout** 으로 출력 한 다음, 영원히 ping 합니다.

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

다음 ApplicationManifest은 위에서 설명한 Servicemanifest.xml을 기반으로 하는 응용 프로그램을 설명 합니다. 모든 컨테이너에 대해 동일한 **볼륨** 탑재를 지정 합니다. 즉, **C:\WorkspaceOnHost** 는 세 컨테이너 모두에서 **C:\WorkspaceOnContainer** 에 탑재 됩니다. 결과적으로 모든 컨테이너는 활성화 된 순서 대로 동일한 로그 파일에 기록 됩니다.

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
ServicePackage 성공적으로 활성화 되 면 **C:\WorkspaceOnHost\log.txt** 의 내용은 다음과 같아야 합니다.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

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
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

