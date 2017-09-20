---
title: "Linux에서 Azure Service Fabric 컨테이너 응용 프로그램 만들기 | Microsoft Docs"
description: "Azure Service Fabric에서 첫 번째 Linux 컨테이너 응용 프로그램을 만듭니다.  응용 프로그램을 사용하여 Docker 이미지를 빌드하고, 이미지를 컨테이너 레지스트리로 푸시하고, Service Fabric 컨테이너 응용 프로그램을 빌드하고 배포합니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 78306672e812745fd1902ae264c2adea196ab721
ms.contentlocale: ko-kr
ms.lasthandoff: 09/20/2017

---

# <a name="deploy-a-service-fabric-linux-container-application-on-azure"></a>Azure에서 Service Fabric Linux 컨테이너 응용 프로그램 배포
Azure Service Fabric은 확장성 있고 안정성이 뛰어난 마이크로 서비스 및 컨테이너를 배포 및 관리하기 위한 분산 시스템 플랫폼입니다. 

Service Fabric 클러스터의 Linux 컨테이너에서 기존 응용 프로그램을 실행하더라도 응용 프로그램을 변경할 필요가 없습니다. 이 빠른 시작에서는 Service Fabric 응용 프로그램에서 미리 작성된 Docker 컨테이너 이미지를 배포하는 방법을 보여줍니다. 완료되면 nginx 컨테이너가 실행됩니다.  이 빠른 시작에서는 Linux 컨테이너 배포에 대해 설명합니다. [이 빠른 시작](service-fabric-quickstart-containers.md)을 참고하여 Windows 컨테이너를 배포합니다.

![Nginx][nginx]

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> * Docker 이미지 컨테이너 패키징
> * 통신 구성
> * Service Fabric 응용 프로그램 빌드 및 패키징
> * Azure에 컨테이너 응용 프로그램 배포

## <a name="prerequisites"></a>필수 조건
[Service Fabric SDK, Service Fabric CLI 및 Service Fabric Yeoman 템플릿 생성기](service-fabric-get-started-linux.md)를 설치합니다.
  
## <a name="package-a-docker-image-container-with-yeoman"></a>Yeoman에서 Docker 이미지 컨테이너 패키징
Linux용 Service Fabric SDK는 쉽게 응용 프로그램을 만들고 컨테이너 이미지를 추가할 수 있는 [Yeoman](http://yeoman.io/) 생성기를 포함합니다. 

Service Fabric 컨테이너 응용 프로그램을 만들려면 터미널 창을 열고 `yo azuresfcontainer`을 실행합니다.  

"MyFirstContainer" 응용 프로그램 이름을 지정하고 "MyContainerService" 응용 프로그램 서비스 이름을 지정합니다.

"nginx:latest" 컨테이너 이미지 이름을 입력합니다(Docker 허브의 [nginx 컨테이너 이미지](https://hub.docker.com/r/_/nginx/)). 

이 이미지는 워크로드 진입점을 정의하므로 입력 명령을 명시적으로 지정해야 합니다. 

"1"이라는 인스턴스 수를 지정합니다.

![컨테이너용 Service Fabric Yeoman 생성기][sf-yeoman]

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>통신 및 컨테이너 포트와 호스트 포트 간 매핑 구성
클라이언트가 서비스와 통신할 수 있도록 HTTP 끝점을 구성합니다.  *./MyFirstContainer/MyContainerServicePkg/ServiceManifest.xml* 파일을 열고 **ServiceManifest** 요소에서 끝점 리소스를 선언합니다.  프로토콜, 포트 및 이름을 추가합니다. 이 빠른 시작에서 서비스는 포트 80에서 수신 대기합니다. 

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
    <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
`UriScheme`을 입력하면 이 컨테이너 끝점이 검색될 수 있도록 Service Fabric Naming 서비스에 자동으로 등록됩니다. 이 문서의 끝에서 전체 ServiceManifest.xml 예제 파일을 제공합니다. 

ApplicationManifest.xml 파일의 `ContainerHostPolicies`에서 `PortBinding` 정책을 사용하여 `Endpoint` 서비스에 컨테이너 포트를 매핑합니다.  이 빠른 시작에서 `ContainerPort`은 80(컨테이너가 포트 80을 노출함)이고 `EndpointRef`는 "myserviceTypeEndpoint"(서비스 매니페스트에서 이전에 정의된 끝점임)입니다.  포트 80에서 서비스에 들어오는 요청은 컨테이너에 있는 포트 80에 매핑됩니다.  

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Service Fabric 응용 프로그램 빌드 및 패키징
Service Fabric Yeoman 템플릿은 [Gradle](https://gradle.org/)에 대한 빌드 스크립트를 포함하며 이것을 사용하여 터미널에서 응용 프로그램을 빌드할 수 있습니다. 변경 내용을 모두 저장합니다.  응용 프로그램을 빌드하고 패키징하려면 다음을 실행합니다.

```bash
cd MyFirstContainer
gradle
```
## <a name="create-a-cluster"></a>클러스터 만들기
응용 프로그램을 Azure의 클러스터에 배포하려면 사용자 고유의 클러스터를 만들거나 파티 클러스터를 만들도록 선택할 수 있습니다.

Party 클러스터는 평가판으로, Azure에서 호스트되고 Service Fabric 팀이 실행하는 제한 시간 Service Fabric 클러스터입니다. 여기서 누구나 응용 프로그램을 배포하고 플랫폼에 대해 알아볼 수 있습니다. 파티 클러스터에 대한 액세스 권한을 얻으려면 [지침에 따릅니다](http://aka.ms/tryservicefabric).  

사용자 고유의 클러스터를 만드는 방법은 [Azure에서 첫 번째 Service Fabric 클러스터 만들기](service-fabric-get-started-azure-cluster.md)를 참조하세요.

연결 끝점을 기록해 두고 다음 단계에서 사용합니다.

## <a name="deploy-the-application-to-azure"></a>Azure에 응용 프로그램 배포
응용 프로그램이 빌드되면 Service Fabric CLI를 사용하여 Azure 클러스터에 배포할 수 있습니다.

Azure에서 Service Fabric 클러스터에 연결합니다.

```bash
sfctl cluster select --endpoint http://lnxt10vkfz6.westus.cloudapp.azure.com:19080
```

템플릿에 제공된 설치 스크립트를 사용하여 클러스터의 이미지 저장소에 응용 프로그램 패키지를 복사하고 응용 프로그램 유형을 등록하며 응용 프로그램의 인스턴스를 만듭니다.

```bash
./install.sh
```

브라우저를 열고 http://lnxt10vkfz6.westus.cloudapp.azure.com:19080/Explorer에서 Service Fabric Explorer로 이동합니다. 응용 프로그램 노드를 확장하면 응용 프로그램 유형에 대한 항목 및 해당 유형의 첫 번째 인스턴스에 대한 다른 항목이 만들어집니다.

![Service Fabric Explorer][sfx]

실행 중인 컨테이너에 연결합니다.  포트 80에서 반환되는 IP 주소(예: "lnxt10vkfz6.westus.cloudapp.azure.com:80")를 가리키는 웹 브라우저를 엽니다. nginx 홈페이지가 브라우저에 표시되어야 합니다.

![Nginx][nginx]

## <a name="clean-up"></a>정리
템플릿에 제공된 제거 스크립트를 사용하여 클러스터에서 응용 프로그램 인스턴스를 삭제하고 응용 프로그램 유형을 등록 해제합니다.

```bash
./uninstall.sh
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric 응용 프로그램 및 서비스 매니페스트의 전체 예제
이 빠른 시작에서 사용되는 전체 서비스 및 응용 프로그램 매니페스트는 다음과 같습니다.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>nginx:latest</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>

```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest  ApplicationTypeName="MyFirstContainerType" ApplicationTypeVersion="1.0.0"
                      xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
   <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
   
   <DefaultServices>
      <Service Name="MyContainerService">
        <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
        cluster, set InstanceCount to -1 for the container service to run on every node in 
        the cluster.
        -->
        <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="1">
            <SingletonPartition />
        </StatelessService>
      </Service>
   </DefaultServices>
   
</ApplicationManifest>

```

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> * Docker 이미지 컨테이너 패키징
> * 통신 구성
> * Service Fabric 응용 프로그램 빌드 및 패키징
> * Azure에 컨테이너 응용 프로그램 배포

* [Service Fabric의 컨테이너](service-fabric-containers-overview.md)를 실행하는 방법에 대해 자세히 알아봅니다.
* [컨테이너에서 .NET 응용 프로그램 배포](service-fabric-host-app-in-a-container.md) 자습서를 참조합니다.
* Service Fabric [응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)에 대해 자세히 알아봅니다.
* GitHub에서 [Service Fabric 컨테이너 코드 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-containers)을 확인합니다.

[sfx]: ./media/service-fabric-quickstart-containers-linux/SFX.png
[nginx]: ./media/service-fabric-quickstart-containers-linux/nginx.png
[sf-yeoman]: ./media/service-fabric-quickstart-containers-linux/YoSF.png

