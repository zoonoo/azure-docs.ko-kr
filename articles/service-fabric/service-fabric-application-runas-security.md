---
title: 시스템 및 보안 로컬 계정에서 Azure Service Fabric 서비스 실행 | Microsoft Docs
description: 시스템 및 보안 로컬 계정에서 Service Fabric 응용 프로그램을 실행하는 방법에 대해 알아봅니다.  보안 주체를 만들고, 실행 정책을 적용하여 서비스를 안전하게 실행합니다.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 62917a1d342158ec2114a9204ee1ca9e447284fa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>로컬 사용자 계정 또는 시스템 계정으로 서비스 실행
Azure Service Fabric을 사용하여 다른 사용자 계정으로 클러스터에서 실행 중인 응용 프로그램을 보호할 수 있습니다. 기본적으로 서비스 패브릭 응용 프로그램은 Fabric.exe 프로세스가 실행하는 계정을 통해 실행됩니다. 또한 Service Fabric은 로컬 사용자 계정 또는 로컬 시스템 계정에서 응용 프로그램을 실행하는 기능을 제공합니다. 이 작업은 응용 프로그램 매니페스트 내에 실행 정책을 지정하여 수행됩니다. 지원되는 로컬 시스템 계정 유형은 **LocalUser**, **NetworkService**, **LocalService** 및 **LocalSystem**입니다.

사용자 그룹을 정의하고 만들었으므로 각 그룹에 사용자를 한 명 이상 추가하여 한꺼번에 관리할 수도 있습니다. 이 기능은 여러 서비스 진입점에 대한 사용자가 여러 명 있고 그 사용자들에게 그룹 수준에서 특정 공통 권한을 부여해야 하는 경우 유용합니다.

> [!NOTE] 
> 서비스에 RunAs 정책을 적용하고 서비스 매니페스트가 HTTP 프로토콜로 끝점 리소스를 선언하면 **SecurityAccessPolicy**를 지정해야 합니다.  자세한 내용은 [HTTP 및 HTTPS 엔드포인트에 보안 액세스 정책 할당](service-fabric-assign-policy-to-endpoint.md)을 참조하세요. 
>

## <a name="create-local-user-groups"></a>로컬 사용자 그룹 만들기
그룹에 사용자를 한 명 이상 추가하도록 사용자 그룹을 정의하고 만들 수 있습니다. 이 기능은 여러 서비스 진입점에 대한 사용자가 여러 명 있고 그 사용자들에게 그룹 수준에서 특정 공통 권한을 부여해야 하는 경우 유용합니다. 다음은 관리자 권한이 있는 **LocalAdminGroup**이라는 로컬 그룹을 보여 줍니다. 다음 응용 프로그램 매니페스트 예제에서 두 사용자 Customer1과 Customer2를 이 로컬 그룹 구성원으로 만듭니다.

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

## <a name="create-local-users"></a>로컬 사용자 만들기
응용 프로그램 내에서 서비스를 보호하는 데 사용할 수 있는 로컬 사용자를 만들 수 있습니다. 응용 프로그램 매니페스트의 주체 섹션에서 **LocalUser** 계정 형식이 지정되면 Service Fabric에서는 응용 프로그램이 배포되는 컴퓨터에 로컬 사용자 계정을 만듭니다. 기본적으로 이러한 계정은 응용 프로그램 매니페스트(예: 다음 응용 프로그램 매니페스트 예제의 Customer3)에 지정된 것과 동일한 이름을 갖지 않습니다. 대신 동적으로 생성되며 임의 암호를 포함합니다.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

응용 프로그램에서 사용자 계정과 암호가 모든 컴퓨터에서 같도록 요구할 경우(예: NTLM 인증을 사용하기 위해) 클러스터 매니페스트는 **NTLMAuthenticationEnabled**를 true로 설정해야 합니다. 클러스터 매니페스트는 모든 컴퓨터에서 동일한 암호를 생성하는 데 사용된 **NTLMAuthenticationPasswordSecret**도 지정해야 합니다.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>서비스 코드 패키지에 정책 할당
**ServiceManifestImport**에 대한 **RunAsPolicy** 섹션은 코드 패키지를 실행하는 데 사용되어야 하는 주체 섹션에서 계정을 지정합니다. 또한 서비스 매니페스트에서 코드 패키지를 주체 섹션에서 사용자 계정과 연결합니다. 설정 또는 기본 진입점에 대해 이 정책을 적용할 수도 있고 `All` 을 지정하여 양쪽에 적용할 수 있습니다. 다음은 여러 정책을 적용하는 방법을 보여주는 예입니다.

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

**EntryPointType**을 지정하지 않으면 기본값은 `EntryPointType=”Main”`으로 설정됩니다. 특히 시스템 계정에서 특정 고수준 권한 설정 작업을 실행하려면 **SetupEntryPoint**를 지정하는 것이 매우 유용합니다. 자세한 내용은 [로컬 사용자 또는 시스템 계정으로 서비스 시작 스크립트 실행](service-fabric-run-script-at-service-startup.md)을 참조하세요. 실제 서비스 코드는 낮은 권한 계정으로 실행할 수 있습니다.

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>모든 서비스 코드 패키지에 기본 정책 적용
**DefaultRunAsPolicy** 섹션은 특정 **RunAsPolicy**가 정의되지 않은 모든 코드 패키지에 대해 기본 사용자 계정을 지정하는 데 사용됩니다. 응용 프로그램에서 사용하는 서비스 매니페스트에 지정된 대부분의 코드 패키지가 동일한 사용자를 통해 실행되어야 하는 경우 응용 프로그램에서 해당 사용자 계정을 사용하여 기본 RunAs 정책을 정의할 수 있습니다. 다음은 코드 패키지의 **RunAsPolicy**가 지정되지 않으면 주체 섹션에 지정된 **MyDefaultAccount**에서 코드 패키지가 수행되는 예제입니다.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
* [응용 프로그램 모델의 이해](service-fabric-application-model.md)
* [서비스 매니페스트에서 리소스 지정](service-fabric-service-manifest-resources.md)
* [응용 프로그램 배포](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
