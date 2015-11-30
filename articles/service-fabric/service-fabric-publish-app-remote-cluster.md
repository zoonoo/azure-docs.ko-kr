<properties
    pageTitle="VS로 원격 클러스터에 앱 게시 | Microsoft Azure"
    description="Visual Studio를 사용하여 원격 서비스 패브릭 클러스터에 응용 프로그램을 게시하기 위해 필요한 단계를 알아봅니다."
    services="service-fabric"
    documentationCenter="na"
    authors="cawa"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# Visual Studio를 사용하여 원격 클러스터에 응용 프로그램 게시

Visual Studio 서비스 패브릭 도구는 서비스 패브릭 클러스터에 응용 프로그램을 게시하는 반복 및 스크립트 가능한 간편한 방법을 제공합니다. 게시 프로필을 통해 PowerShell 배포 스크립트를 사용하면 됩니다. 게시 프로필은 중요한 게시 정보를 저장하는 .XML 형식 파일입니다. 로컬 도는 원격 클러스터에 사용자가 응용 프로그램을 쉽게 게시할 수 있게 **서비스 패브릭 응용 프로그램 게시** 대화 상자를 추가할 수도 있습니다. 게시 대화 상자를 통한 설정 변경은 게시 프로필에 캡처됩니다. 이렇게 하면 나중에 자동화 프로세스에서 수동으로 게시 설정을 편집할 수 있습니다.

## 서비스 패브릭 클러스터에 응용 프로그램을 게시하는 데 필요한 아티팩트

### Deploy-FabricApplication.ps1

서비스 패브릭 응용 프로그램 게시를 위한 매개 변수로 게시 프로필 경로를 사용하는 PowerShell 스크립트입니다.

### 게시 프로필

이름이 **PublishProfiles**인 서비스 패브릭 응용 프로그램의 폴더에는 **Cloud.XML** 및 **Local.XML** 파일이 있습니다. 이것은 다음과 같이 응용 프로그램을 게시하기 위해 중요한 정보를 저장하는 "게시 프로필"입니다. - 서비스 패브릭 클러스터 연결 매개 변수 - 응용 프로그램 매개 변수 파일 경로 - 업그레이드 설정

### 응용 프로그램 매개 변수 파일

이름이 **ApplicationParameters**인 서비스 패브릭 응용 프로그램 프로젝트의 폴더에는 사용자 특정 응용 프로그램 매니페스트 매개 변수 값에 대한 XML 파일이 들어있습니다. 배포 설정에 다른 값을 사용할 수 잇게 응용 프로그램 매니페스트 파일을 매개 변수화할 수 있습니다.

예를 들어, 각 배포마다 서로 다른 환경에 맞게 파티션 수를 변경할 수 있습니다. 프로젝트를 만들 때 **TargetReplicaSetSize** 및 **PartitionCount** 등과 같은 응용 프로그램 매니페스트의 설정이 매개 변수로 변환됩니다. 이러한 배개 변수의 기본값은 응용 프로그램 매니페스트 파일(서비스 패브릭 응용 프로그램 프로젝트에 있는 ApplicationManifest.XML)의 **매개 변수** 섹션에서 지정합니다. 응용 프로그램 매개 변수 파일에 추가한 모든 값은 응용 프로그램 매니페스트 파일의 기본값보다 우선합니다.

>[AZURE.NOTE]행위자 서비스의 경우 먼저 프로젝트를 구축하여 매니페스트 파일의 매개 변수를 생성해야합니다.

다음은 응용 프로그램 매니페스트 파일의 예입니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application2Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Actor1ActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="Actor1ActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="Actor1ActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Actor1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Actor1ActorService">
         <StatefulService ServiceTypeName="Actor1ActorServiceType" TargetReplicaSetSize="[Actor1ActorService_TargetReplicaSetSize]" MinReplicaSetSize="[Actor1ActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[Actor1ActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## 서비스 패브릭 응용 프로그램 게시 대화 상자를 사용하여 서비스 패브릭 클러스터에 응용 프로그램을 게시합니다.

다음 단계에서는 Visual Studio 서비스 패브릭 도구에서 제공하는 **서비스 패브릭 응용 프로그램 게시** 대화 상자를 사용하여 응용 프로그램을 게시하는 방법을 보여줍니다.

1. 서비스 패브릭 응용 프로그램 프로젝트의 바로 가기 메뉴에서 **게시…**를 선택하여 **서비스 패브릭 응용 프로그램 게시** 대화 상자를 표시합니다.

    ![][0]

    **대상 프로필** 드롭다운 목록 상자에서 선택한 파일에 **매니페스트 버전**을 제외한 모든 설정이 저장됩니다. 기존 프로필을 재사용하거나, **대상 프로필** 드롭다운 목록 상자에서 **<프로필 관리…>**를 선택하여 새 프로필을 만듭니다. 게시 프로필을 선택하면 그 내용이 대화 상자의 해당 필드에 표시됩니다. 언제든 변경 사항을 저장하려면 **프로필 저장** 링크를 선택합니다.

1. **연결 끝점** 섹션에서는 로컬 또는 원격 서비스 패브릭 클러스터의 게시 끝점을 지정할 수 있습니다. 연결 끝점을 추가하거나 변경하려면 **선택...** 단추를 선택합니다. **서비스 패브릭 클러스터 선택** 대화 상자는 게시할 수 있는 사용 가능한 서비스 패브릭 클러스터 연결 끝점을 표시합니다. Azure 구독에 아직 로그인하지 않은 경우 로그인하라는 메시지가 표시됩니다. 연결 끝점을 선택합니다.

    ![][1]

    끝점을 선택하면 Visual Studio가 선택한 서비스 패브릭 클러스터에 대해 연결 유효성을 확인합니다. 클러스터에 보안이 없으면 Visual Studio가 즉시 연결할 수 있습니다. 그러나 클러스터가 보안 상태이면 계속하기 전에 로컬 컴퓨터에 인증서를 설치해야 합니다. 자세한 내용은 [보안 연결을 구성하는 방법](service-fabric-visualstudio-configure-secure-connections.md)을 참조하세요. 완료되면 **확인** 단추를 선택합니다. 선택한 클러스터가 **서비스 패브릭 응용 프로그램 게시** 대화 상자에 표시됩니다.

1. **응용 프로그램 매개 변수 파일** 드롭 다운 목록에서는 응용 프로그램 매개 변수 파일로 이동할 수 있습니다. 응용 프로그램 매개 변수 파일은 응용 프로그램 매니페스트 파일의 매개 변수에 대한 사용자 특정 값을 담고 있습니다. 매개 변수를 추가 또는 변경하려면 **편집** 단추를 선택합니다. **매개 변수** 표에서 매개 변수의 값을 입력하거나 변경합니다. 완료되면 **저장** 단추를 선택합니다.

    ![][2]

1. **응용 프로그램을 업그레이드** 확인란을 사용하면 이 게시 작업이 업그레이드인지 여부를 지정할 수 있습니다. 업그레이드 게시 작업은 일반적인 게시 작업과 다릅니다. 차이점 목록은 [서비스 패브릭 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)를 참조하세요. 업그레이드 설정을 구성하려면 **업그레이드 설정 구성** 링크를 선택합니다. 업그레이드 매개 변수 편집기가 나타납니다. 업그레이드 매개 변수에 대한 자세한 내용은 [서비스 패브릭 응용 프로그램의 업그레이드 구성](service-fabric-visualstudio-configure-upgrade.md)을 참조하세요.

1. **편집 버전** 대화 상자를 보려면 **매니페스트 버전...** 단추를 선택합니다. 업그레이드를 수행하려면 응용 프로그램 및 서비스 버전을 업데이트해야 합니다. 응용 프로그램과 서비스 매니페스트 버전이 업그레이드 프로세스에 어떻게 영향을 미치는지 확인하려면 [서비스 패브릭 응용 프로그램 업그레이드 자습서](service-fabric-application-upgrade-tutorial.md)를 참조하세요.

    ![][3]

    응용 프로그램 및 서비스 버전이 1.0.0과 같은 의미 체계 버전 관리나 1.0.0.0 형식의 숫자 값을 사용할 경우 **응용 프로그램 및 서비스 버전을 자동으로 업데이트** 옵션을 선택합니다. 이 옵션을 선택하면 코드, 구성 또는 데이터 패키지 버전이 업데이트될 때마다 서비스 및 응용 프로그램 버전 번호가 자동으로 업데이트됩니다. 버전을 수동으로 편집하려면 확인란의 선택을 취소하여 이 기능을 사용하지 않습니다.

    >[AZURE.NOTE]행위자 프로젝트에 대해 모든 패키지 항목을 표시하려면 먼저 서비스 매니페스트 파일에 항목을 생성하는 프로젝트를 빌드합니다.

1. 모든 필요한 설정을 지정한 후에는 **게시** 단추를 선택하여 응용 프로그램을 선택한 서비스 패브릭 클러스터에 게시합니다. 지정한 설정이 게시 프로세스에 적용됩니다.

## 다음 단계

연속 통합 환경에서 게시 프로세스를 자동화하는 방법은 [서비스 패브릭 연속 통합 설정](service-fabric-set-up-continuous-integration.md)을 참조하세요.


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png

<!---HONumber=Nov15_HO4-->