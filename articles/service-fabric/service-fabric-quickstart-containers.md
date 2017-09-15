---
title: "Azure Service Fabric Windows 컨테이너 응용 프로그램 만들기 | Microsoft Docs"
description: "Azure Service Fabric에서 첫 번째 Windows 컨테이너 응용 프로그램을 만듭니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: c4f8c94e23a165b22533ffd74e04c9a7310f2d22
ms.contentlocale: ko-kr
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-a-service-fabric-windows-container-application-on-azure"></a>Azure에서 Service Fabric Windows 컨테이너 응용 프로그램 배포
Azure Service Fabric은 확장성 있고 안정성이 뛰어난 마이크로 서비스 및 컨테이너를 배포 및 관리하기 위한 분산 시스템 플랫폼입니다. 

Service Fabric 클러스터의 Windows 컨테이너에서 기존 응용 프로그램을 실행하더라도 응용 프로그램을 변경할 필요가 없습니다. 이 빠른 시작에서는 Service Fabric 응용 프로그램에서 미리 작성된 Docker 컨테이너 이미지를 배포하는 방법을 보여줍니다. 완료하면 Windows Server 2016 Nano 서버 및 IIS 컨테이너를 실행하게 됩니다. 이 빠른 시작에서는 Windows 컨테이너 배포에 대해 설명합니다. [이 빠른 시작](service-fabric-quickstart-containers-linux.md)을 참고하여 Linux 컨테이너를 배포합니다.

![IIS 기본 웹 페이지][iis-default]

이 빠른 시작을 사용하여 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> * Docker 이미지 컨테이너 패키징
> * 통신 구성
> * Service Fabric 응용 프로그램 빌드 및 패키징
> * Azure에 컨테이너 응용 프로그램 배포

## <a name="prerequisites"></a>필수 조건
* Azure 구속([체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만들 수 있음)
* 다음을 실행하는 개발 컴퓨터
  * Visual Studio 2015 또는 Visual Studio 2017.
  * [Service Fabric SDK 및 도구](service-fabric-get-started.md)

## <a name="package-a-docker-image-container-with-visual-studio"></a>Visual Studio에서 Docker 이미지 컨테이너 패키징
Service Fabric SDK 및 도구는 컨테이너를 Service Fabric 클러스터에 배포할 수 있는 서비스 템플릿을 제공합니다.

"관리자" 권한으로 Visual Studio를 시작합니다.  **파일** > **새로 만들기** > **프로젝트**를 선택합니다.

**Service Fabric 응용 프로그램**을 선택하고 "MyFirstContainer"라는 이름을 지정하고 **확인**을 클릭합니다.

**서비스 템플릿** 목록에서 **컨테이너**를 선택합니다.

**이미지 이름**에서 [Windows Server 2016 Nano Server 및 IIS 기본 이미지](https://hub.docker.com/r/nanoserver/iis/)인 "nanoserver/iis"를 입력합니다. 

"MyContainerService" 서비스 이름을 지정하고 **확인**을 클릭합니다.

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>통신 및 컨테이너 포트와 호스트 포트 간 매핑 구성
서비스에는 통신을 위한 끝점이 필요합니다.  이제 ServiceManifest.xml 파일에서 프로토콜, 포트 및 형식을 `Endpoint`에 추가할 수 있습니다. 이 빠른 시작에서 컨테이너화된 서비스는 포트 80에서 수신 대기합니다. 

```xml
<Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
```
`UriScheme`을 입력하면 이 컨테이너 끝점이 검색될 수 있도록 Service Fabric Naming 서비스에 자동으로 등록됩니다. 이 문서의 끝에서 전체 ServiceManifest.xml 예제 파일을 제공합니다. 

ApplicationManifest.xml 파일의 `ContainerHostPolicies`에서 `PortBinding` 정책을 지정하여 컨테이너 포트 및 호스트 간 포트 매핑을 구성합니다.  이 빠른 시작의 경우 `ContainerPort`는 80이고 `EndpointRef`는 "MyContainerServiceTypeEndpoint"(서비스 매니페스트에 정의된 끝점)입니다.  포트 80에서 서비스에 들어오는 요청은 컨테이너에 있는 포트 80에 매핑됩니다.  

```xml
<ServiceManifestImport>
...
  <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
    </ContainerHostPolicies>
  </Policies>
</ServiceManifestImport>
```

이 문서의 끝에서 전체 ApplicationManifest.xml 예제 파일을 제공합니다.

## <a name="create-a-cluster"></a>클러스터 만들기
응용 프로그램을 Azure의 클러스터에 배포하려면 사용자 고유의 클러스터를 만들거나 파티 클러스터를 만들도록 선택할 수 있습니다.

Party 클러스터는 평가판으로, Azure에서 호스트되고 Service Fabric 팀이 실행하는 제한 시간 Service Fabric 클러스터입니다. 여기서 누구나 응용 프로그램을 배포하고 플랫폼에 대해 알아볼 수 있습니다. 파티 클러스터에 대한 액세스 권한을 얻으려면 [지침에 따릅니다](http://aka.ms/tryservicefabric).  

사용자 고유의 클러스터를 만드는 방법은 [Azure에서 첫 번째 Service Fabric 클러스터 만들기](service-fabric-get-started-azure-cluster.md)를 참조하세요.

연결 끝점을 기록해 두고 다음 단계에서 사용합니다.  

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Visual Studio를 사용하여 Azure에 응용 프로그램 배포
응용 프로그램이 준비되면 Visual Studio에서 클러스터에 직접 배포할 수 있습니다.

솔루션 탐색기에서 **MyFirstContainer**를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. [게시] 대화 상자가 나타납니다.

![[게시] 대화 상자](./media/service-fabric-quickstart-dotnet/publish-app.png)

**연결 끝점** 필드에 클러스터의 연결 끝점을 입력하고 **게시**를 클릭합니다. 파티 클러스터에 등록하면 연결 끝점이 브라우저에 제공됩니다. 예: `winh1x87d1d.westus.cloudapp.azure.com:19000`

브라우저를 열고 http://winh1x87d1d.westus.cloudapp.azure.com:80으로 이동합니다. IIS 기본 웹 페이지가 표시됩니다. ![IIS 기본 웹 페이지][iis-default]

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric 응용 프로그램 및 서비스 매니페스트의 전체 예제
이 빠른 시작에서 사용되는 전체 서비스 및 응용 프로그램 매니페스트는 다음과 같습니다.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>nanoserver/iis</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="MyContainerService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="MyContainerService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="[MyContainerService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.
> [!div class="checklist"]
> * Docker 이미지 컨테이너 패키징
> * 통신 구성
> * Service Fabric 응용 프로그램 빌드 및 패키징
> * Azure에 컨테이너 응용 프로그램 배포

* [Service Fabric의 컨테이너](service-fabric-containers-overview.md)를 실행하는 방법에 대해 자세히 알아봅니다.
* [컨테이너에서 .NET 응용 프로그램 배포](service-fabric-host-app-in-a-container.md) 자습서를 참조합니다.
* Service Fabric [응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)에 대해 자세히 알아봅니다.
* GitHub에서 [Service Fabric 컨테이너 코드 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-containers)을 확인합니다.

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png

