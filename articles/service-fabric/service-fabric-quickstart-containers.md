---
title: Azure Service Fabric Windows 컨테이너 응용 프로그램 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 Azure Service Fabric에서 첫 번째 Windows 컨테이너 응용 프로그램을 만듭니다.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/03/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 47a4e75699e024dae367524f16eb23fb72043ef5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-deploy-a-service-fabric-windows-container-application-on-azure"></a>빠른 시작: Azure에서 Service Fabric Windows 컨테이너 응용 프로그램 배포
Azure Service Fabric은 확장성 있고 안정성이 뛰어난 마이크로 서비스 및 컨테이너를 배포 및 관리하기 위한 분산 시스템 플랫폼입니다. 

Service Fabric 클러스터의 Windows 컨테이너에서 기존 응용 프로그램을 실행하더라도 응용 프로그램을 변경할 필요가 없습니다. 이 빠른 시작에서는 Service Fabric 응용 프로그램에서 미리 작성된 Docker 컨테이너 이미지를 배포하는 방법을 보여줍니다. 완료하면 Windows Server 2016 Nano 서버 및 IIS 컨테이너를 실행하게 됩니다. 이 빠른 시작에서는 Windows 컨테이너 배포에 대해 설명합니다. [이 빠른 시작](service-fabric-quickstart-containers-linux.md)을 참고하여 Linux 컨테이너를 배포합니다.

![IIS default web page][iis-default]

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

* Docker 이미지 컨테이너 패키징
* 통신 구성
* Service Fabric 응용 프로그램 빌드 및 패키징
* Azure에 컨테이너 응용 프로그램 배포

## <a name="prerequisites"></a>필수 조건
* Azure 구속([체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만들 수 있음)
* 다음을 실행하는 개발 컴퓨터
  * Visual Studio 2015 또는 Visual Studio 2017.
  * [Service Fabric SDK 및 도구](service-fabric-get-started.md)

## <a name="package-a-docker-image-container-with-visual-studio"></a>Visual Studio에서 Docker 이미지 컨테이너 패키징
Service Fabric SDK 및 도구는 컨테이너를 Service Fabric 클러스터에 배포할 수 있는 서비스 템플릿을 제공합니다.

"관리자" 권한으로 Visual Studio를 시작합니다.  **파일** > **새로 만들기** > **프로젝트**를 선택합니다.

**Service Fabric 응용 프로그램**을 선택하고 "MyFirstContainer"라는 이름을 지정하고 **확인**을 클릭합니다.

**호스트된 컨테이너 및 응용 프로그램** 템플릿에서 **컨테이너**를 선택합니다.

**이미지 이름**에 [Windows Server Nano Server 및 IIS 기본 이미지](https://hub.docker.com/r/microsoft/iis/)인 "microsoft/iis:nanoserver"를 입력합니다. 

"MyContainerService" 서비스 이름을 지정하고 **확인**을 클릭합니다.

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>통신 및 컨테이너 포트와 호스트 포트 간 매핑 구성
서비스에는 통신을 위한 끝점이 필요합니다.  이 빠른 시작에서 컨테이너화된 서비스는 포트 80에서 수신 대기합니다.  솔루션 탐색기에서 *MyFirstContainer/ApplicationPackageRoot/MyContainerServicePkg/ServiceManifest.xml*을 엽니다.  ServiceManifest.xml 파일에서 기존 `Endpoint`를 업데이트하고 프로토콜, 포트 및 URI 체계를 추가합니다. 

```xml
<Resources>
    <Endpoints>
        <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
   </Endpoints>
</Resources>
```
`UriScheme`을 입력하면 이 컨테이너 끝점이 검색될 수 있도록 Service Fabric Naming 서비스에 자동으로 등록됩니다. 이 문서의 끝에서 전체 ServiceManifest.xml 예제 파일을 제공합니다. 

포트 80의 서비스로 들어오는 요청이 컨테이너의 포트 80에 매핑되도록 컨테이너 포트와 호스트 포트 간 매핑을 구성합니다.  솔루션 탐색기에서 *MyFirstContainer/ApplicationPackageRoot/ApplicationManifest.xml*을 열고 `ContainerHostPolicies`에 `PortBinding` 정책을 지정합니다.  이 빠른 시작의 경우 `ContainerPort`는 80이고 `EndpointRef`는 "MyContainerServiceTypeEndpoint"(서비스 매니페스트에 정의된 끝점)입니다.    

```xml
<ServiceManifestImport>
...
  <ConfigOverrides />
  <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
    </ContainerHostPolicies>
  </Policies>
</ServiceManifestImport>
```

이 문서의 끝에서 전체 ApplicationManifest.xml 예제 파일을 제공합니다.

## <a name="create-a-cluster"></a>클러스터 만들기
응용 프로그램을 Azure의 클러스터에 배포하려면 Party 클러스터에 조인할 수 있습니다. Party 클러스터는 평가판으로, Azure에서 호스트되고 Service Fabric 팀이 실행하는 제한 시간 Service Fabric 클러스터입니다. 여기서 누구나 응용 프로그램을 배포하고 플랫폼에 대해 알아볼 수 있습니다.  클러스터는 노드-노드뿐만 아니라 클라이언트-노드 보안에도 단일 자체 서명 인증서를 사용합니다. 파티 클러스터가 컨테이너를 지원합니다. 자체 클러스터를 설정하고 사용하려는 경우 컨테이너를 지원하는 SKU에서 클러스터가 실행되어야 합니다(예: 컨테이너가 있는 Windows Server 2016 데이터 센터).

[Windows 클러스터에 로그인하고 조인](http://aka.ms/tryservicefabric)합니다. **PFX** 링크를 클릭하여 PFX 인증서를 컴퓨터에 다운로드합니다. **보안 Party 클러스터에 연결하는 방법** 링크를 클릭하고 인증서 암호를 복사합니다. 인증서, 인증서 암호 및 **연결 엔드포인트** 값은 다음 단계에서 사용됩니다.

![PFX 및 연결 엔드포인트](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> 시간당 사용 가능한 Party 클러스터의 수가 제한되어 있습니다. Party 클러스터에 등록하려고 할 때 오류가 발생하면, 일정 기간 동안 기다린 후 다시 시도하거나, [.NET 응용 프로그램 배포](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) 자습서에서 이러한 단계를 수행하여 Azure 구독에 Service Fabric 클러스터를 만들고 이 클러스터에 응용 프로그램을 배포할 수 있습니다. Visual Studio를 통해 만들어진 클러스터는 컨테이너를 지원합니다. 클러스터에 응용 프로그램을 배포하고 유효성을 확인한 후에는 이 빠른 시작의 [Service Fabric 응용 프로그램 및 서비스 매니페스트의 전체 예제](#complete-example-service-fabric-application-and-service-manifests)로 건너뛸 수 있습니다. 
>

Windows 컴퓨터에서 *CurrentUser\My* 인증서 저장소에 PFX를 설치합니다.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

다음 단계를 위해 지문을 기억합니다.  

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Visual Studio를 사용하여 Azure에 응용 프로그램 배포
응용 프로그램이 준비되면 Visual Studio에서 클러스터에 직접 배포할 수 있습니다.

솔루션 탐색기에서 **MyFirstContainer**를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. [게시] 대화 상자가 나타납니다.

파티 클러스터 페이지의 **연결 끝점**을 **연결 끝점** 필드에 복사합니다. 예: `zwin7fh14scd.westus.cloudapp.azure.com:19000` **고급 연결 매개 변수**를 클릭하고 연결 매개 변수 정보를 확인합니다.  *FindValue* 및 *ServerCertThumbprint* 값은 이전 단계에서 설치한 인증서의 지문과 일치해야 합니다. 

![[게시] 대화 상자](./media/service-fabric-quickstart-containers/publish-app.png)

**게시**를 클릭합니다.

클러스터의 각 응용 프로그램에는 고유한 이름이 있어야 합니다.  Party 클러스터가 공용 공유 환경이지만 기존 응용 프로그램과 충돌이 발생할 수 있습니다.  이름이 충돌하는 경우 Visual Studio 프로젝트의 이름을 바꾸고 다시 배포합니다.

브라우저를 열고 Party 클러스터 페이지에 지정된 **연결 엔드포인트**로 이동합니다. 필요에 따라 URL에 `http://` 스키마 식별자를 앞쪽에 추가하고 `:80` 포트를 추가할 수 있습니다. 예: http://zwin7fh14scd.westus.cloudapp.azure.com:80 IIS 기본 웹 페이지가 표시됩니다. ![IIS 기본 웹 페이지][iis-default]

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
        <ImageName>microsoft/iis:nanoserver</ImageName>
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

* Docker 이미지 컨테이너 패키징
* 통신 구성
* Service Fabric 응용 프로그램 빌드 및 패키징
* Azure에 컨테이너 응용 프로그램 배포

Service Fabric에서 Windows 컨테이너 작업에 대해 자세히 알아보려면 Windows 컨테이너 앱에 대한 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Windows 컨테이너 앱 만들기](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
