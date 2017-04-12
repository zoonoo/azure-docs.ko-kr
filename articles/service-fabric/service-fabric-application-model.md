---
title: "Azure Service Fabric 응용 프로그램 모델 | Microsoft Docs"
description: "서비스 패브릭에서 응용 프로그램 및 서비스를 모델링 및 설명하는 방법"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/02/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 87db655d246dad90bf0afbc91ec507b0a86d90eb
ms.lasthandoff: 03/27/2017


---
# <a name="model-an-application-in-service-fabric"></a>서비스 패브릭에서 응용 프로그램 모델링
이 문서에서는 Azure Service Fabric 응용 프로그램 모델의 개요 및 매니페스트 파일을 통해 응용 프로그램과 서비스를 정의하는 방법에 대해 설명합니다.

## <a name="understand-the-application-model"></a>응용 프로그램 모델의 이해
응용 프로그램은 특정 기능을 수행하는 구성 서비스 컬렉션입니다. 서비스는 완전한 독립 실행형 기능을 수행하며(서비스는 다른 서비스와 독립적으로 시작 및 실행 가능) 코드, 구성 및 데이터로 이루어집니다. 각 서비스에 대해 코드는 실행 가능한 이진으로 구성되고, 구성은 런타임에 로드할 수 있는 서비스 설정으로 이루어지고, 데이터는 서비스에서 사용할 임의의 정적 데이터로 구성됩니다. 이 계층형 응용 프로그램 모델의 각 구성 요소를 독립적으로 버전 지정 및 업그레이드할 수 있습니다.

![서비스 패브릭 응용 프로그램 모델][appmodel-diagram]

응용 프로그램 유형은 응용 프로그램에 대한 분류이며 여러 서비스 유형으로 구성됩니다. 서비스 유형은 서비스에 대한 분류입니다. 분류에는 다양한 설정과 구성이 포함될 수 있지만 핵심 기능은 동일하게 유지됩니다 서비스 인스턴스는 같은 서비스 유형의 다른 서비스 구성 변형입니다.  

응용 프로그램 및 서비스의 클래스(또는 "유형")는 클러스터의 이미지 저장소에서 응용 프로그램을 인스턴스화할 수 있는 템플릿인 XML 파일(응용 프로그램 매니페스트 및 서비스 매니페스트)을 통해 설명됩니다. ServiceManifest.xml 및 ApplicationManifest.xml에 대한 스키마 정의는 Service Fabric SDK 및 도구와 함께 *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*에 설치됩니다.

서로 다른 응용 프로그램 인스턴스에 대한 코드는 동일한 서비스 패브릭 노드에 의해 호스팅되는 경우에도 별도의 프로세스로 실행됩니다. 뿐만 아니라 각 응용 프로그램 인스턴스의 수명 주기를 독립적으로 관리(예: 업그레이드)할 수 있습니다. 다음 다이어그램에서는 응용 프로그램 유형이 어떻게 서비스 유형으로 구성되고, 다시 서비스 유형이 코드, 구성 및 패키지로 구성되는지를 보여줍니다. 다이어그램을 간소화하려면 `ServiceType4` 에 대한 코드/구성/데이터 패키지만을 표시하지만 각 서비스 형식은 패키지 형식의 일부 또는 모두를 포함합니다.

![서비스 패브릭 응용 프로그램 유형 및 서비스 유형][cluster-imagestore-apptypes]

서로 다른 두 매니페스트 파일, 즉 서비스 매니페스트와 응용 프로그램 매니페스트를 사용하여 응용 프로그램 및 서비스를 설명합니다. 이에 대한 내용은 뒤에 나오는 섹션에서 자세히 다루겠습니다.

클러스터에 활성 상태의 서비스 유형 인스턴스가 하나 이상 있을 수 있습니다. 예를 들어 상태 저장 서비스 인스턴스 또는 복제본의 경우 클러스터의 서로 다른 노드에 있는 복제본 간에 상태를 복제하여 높은 안정성을 달성합니다. 복제는 클러스터의 한 노드에 장애가 발생해도 서비스가 제공되도록 중복성을 제공합니다. [분할된 서비스](service-fabric-concepts-partitioning.md) 는 클러스터의 노드에서 상태(및 해당 상태에 대한 액세스 패턴)를 더욱 세분화합니다.

다음 다이어그램에서는 응용 프로그램 및 서비스 인스턴스, 파티션, 복제본 간의 관계를 보여 줍니다.

![서비스 내의 파티션 및 복제본][cluster-application-instances]

> [!TIP]
> http://&lt;yourclusteraddress&gt;:19080/Explorer에서 제공되는 Service Fabric Explorer 도구를 사용하여 클러스터에서 응용 프로그램의 레이아웃을 볼 수 있습니다. 자세한 내용은 [Service Fabric Explorer를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)를 참조하세요.
> 
> 

## <a name="describe-a-service"></a>서비스 설명
서비스 매니페스트는 서비스 유형 및 버전을 선언적으로 정의합니다. 서비스 유형, 상태 속성, 부하 분산 메트릭, 서비스 바이너리, 구성 파일 등의 서비스 메타데이터를 지정합니다.  다시 말해서 하나 이상의 서비스 유형을 지원하는 서비스 패키지를 구성하는 코드, 구성 및 데이터 패키지를 설명합니다. 다음은 서비스 매니페스트의 간단한 예입니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```

**버전** 특성은 구조화되지 않은 문자열이며 시스템에서 구문 분석하지 않습니다. 업그레이드에 대한 각 구성 요소의 버전을 지정하는 데 사용됩니다.

**ServiceTypes**는 이 매니페스트의 **CodePackages**에서 지원하는 서비스 유형을 선언합니다. 이러한 서비스 유형 중 하나에 대해 서비스가 인스턴스화되면 코드 패키지의 진입점을 실행하여 이 매니페스트에 선언된 모든 코드 패키지가 활성화됩니다. 결과 프로세스는 런타임에 지원되는 서비스 유형을 등록합니다. 서비스 유형은 코드 패키지 수준이 아니라 매니페스트 수준에서 선언됩니다. 따라서 코드 패키지가 여러 개인 경우 시스템에서 선언된 서비스 유형 중 어떤 하나를 조회하면 모든 코드 패키지가 활성화됩니다.

**SetupEntryPoint** 는 서비스 패브릭과 같은 자격 증명(일반적으로 *LocalSystem* 계정)을 사용하여 다른 진입점보다 먼저 실행되는 권한 있는 진입점입니다. **EntryPoint** 를 통해 지정되는 실행 파일은 일반적으로 장기 실행 서비스 호스트입니다. 별도의 설정 진입점이 있으면 한동안은 높은 권한을 사용하여 서비스 호스트를 실행하지 않아도 됩니다. **EntryPoint**를 통해 지정된 실행 파일은 **SetupEntryPoint**가 성공적으로 종료된 후 실행됩니다. 종료되지 않거나 충돌하는 경우 결과 프로세스를 모니터링하여 다시 시작합니다( **SetupEntryPoint**를 사용하여 다시 시작). 

**SetupEntryPoint** 를 사용하는 일반적인 시나리오는 서비스를 시작하기 전에 실행 파일을 실행해야 하는 경우 또는 높은 권한을 사용하여 작업을 수행해야 하는 경우입니다. 예:

* 서비스 실행 파일에 필요한 환경 변수를 설정하고 초기화합니다. 이것은 서비스 패브릭 프로그래밍 모델을 통해 작성된 실행 파일에만 국한되지는 않습니다. 예를 들어 npm.exe 파일에는 node.js 응용 프로그램 배포를 위해 구성되는 환경 변수가 필요합니다.
* 보안 인증서를 설치하여 액세스 제어를 설정합니다.

**SetupEntryPoint**를 구성하는 방법에 대한 자세한 내용은 [서비스 설치 진입점에 대한 정책 구성](service-fabric-application-runas-security.md)을 참조하세요.

**EnvironmentVariables**는 이 코드 패키지에 대해 설정된 환경 변수 목록을 제공합니다. `ApplicationManifest.xml`에서 이를 재정의하여 다른 서비스 인스턴스의 다른 값을 제공할 수 있습니다. 

**DataPackage**는 **Name** 특성을 통해 이름이 지정되고 런타임에 프로세스에서 사용할 임의의 정적 데이터를 포함하는 폴더를 선언합니다.

**ConfigPackage**는 **Name** 특성을 통해 이름이 지정되고 *Settings.xml* 파일을 포함하는 폴더를 선언합니다. 이 파일은 런타임에 프로세스에서 다시 읽을 수 있는 사용자 정의 키-값 쌍 설정의 섹션을 포함합니다. 업그레이드하는 동안 **ConfigPackage** **버전**만 변경되면 실행 중인 프로세스가 다시 시작되지 않습니다. 대신, 콜백에서는 구성 설정이 변경되어 동적으로 다시 로드할 수 있음을 프로세스에 알립니다. 다음은 *Settings.xml* 파일의 예입니다.

```xml
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

> [!NOTE]
> 서비스 매니페스트는 여러 코드, 구성 및 데이터 패키지를 포함할 수 있습니다. 이들 항목에 대해 각각 독립적으로 버전을 지정할 수 있습니다.
> 
> 

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>응용 프로그램 설명
응용 프로그램 매니페스트는 응용 프로그램 유형 및 버전을 선언적으로 설명합니다. 안정적인 이름, 파티션 구성표, 인스턴스 수/복제 요소, 보안/격리 정책, 배치 제약 조건, 구성 재정의, 구성 서비스 유형 등의 서비스 구성 메타데이터를 지정합니다. 또한 응용 프로그램이 배치되는 부하 분산 도메인을 설명합니다.

따라서 응용 프로그램 매니페스트는 응용 프로그램 수준에서 요소를 설명하고 응용 프로그램 유형을 구성하는 하나 이상의 서비스 매니페스트를 참조합니다. 다음은 응용 프로그램 매니페스트의 간단한 예입니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
    <ConfigOverrides/>
    <EnvironmentOverrides CodePackageRef="MyCode"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
```

서비스 매니페스트와 마찬가지로 **Version** 특성은 구조화되지 않은 문자열이며 시스템에서 구문을 분석하지 않습니다. 또한 업그레이드에 대한 각 구성 요소의 버전을 지정하는 데 사용됩니다.

**ServiceManifestImport** 는 이 응용 프로그램 유형을 구성하는 서비스 매니페스트에 대한 참조를 포함합니다. 가져온 서비스 매니페스트는 이 응용 프로그램 유형 내에서 유효한 있는 서비스 유형을 결정합니다. ServiceManifestImport 내에서 Settings.xml의 구성 값과 ServiceManifest.xml 파일의 환경 변수를 재정의할 수 있습니다. 


**DefaultServices** 는 이 응용 프로그램 유형에 대해 응용 프로그램이 인스턴스화할 때마다 자동으로 생성되는 서비스 인스턴스를 선언합니다. 기본 서비스는 편리하기는 하지만 생성된 후 모든 면에서 일반 서비스처럼 동작합니다. 응용 프로그램 인스턴스의 다른 서비스와 함께 업그레이드되며 제거할 수도 있습니다.

> [!NOTE]
> 응용 프로그램 매니페스트는 여러 서비스 매니페스트 가져오기 및 기본 서비스를 포함할 수 있습니다. 각 서비스 매니페스트 가져오기를 독립적으로 버전 지정할 수 있습니다.
> 
> 

개별 환경에 대해 서로 다른 응용 프로그램 및 서비스 매개 변수를 유지 관리하는 방법을 알아보려면 [여러 환경에 대한 응용 프로그램 매개 변수 관리](service-fabric-manage-multiple-environment-app-configuration.md)를 참조하세요.

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>다음 단계
[응용 프로그램을 패키지](service-fabric-package-apps.md)하고 배포를 준비합니다.

[응용 프로그램 배포 및 제거][10]에서는 PowerShell을 사용하여 응용 프로그램 인스턴스를 관리하는 방법을 설명합니다.

[여러 환경에 대한 응용 프로그램 매개 변수 관리][11]에서는 여러 응용 프로그램 인스턴스의 매개 변수 및 환경 변수를 구성하는 방법을 설명합니다.

[응용 프로그램에 대한 보안 정책 구성][12]에서는 액세스를 제한하는 보안 정책에 따라 서비스를 실행하는 방법을 설명합니다.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md

