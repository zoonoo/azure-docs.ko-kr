---
title: "Azure Service Fabric 컨테이너 앱 만들기 | Microsoft Docs"
description: "Azure Service Fabric에서 첫 번째 컨테이너 앱을 만듭니다.  앱을 사용하여 Docker 이미지를 빌드하고, 이미지를 컨테이너 레지스트리로 푸시하고, Service Fabric 컨테이너 앱을 빌드하고 배포합니다."
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
ms.date: 05/08/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: acb68b274228aa647dc7be5d36b2b077bd213c1b
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---

# <a name="create-your-first-service-fabric-container-app"></a>첫 번째 Service Fabric 컨테이너 앱 만들기
Service Fabric 클러스터의 Windows 컨테이너에서 기존 응용 프로그램을 실행하기 위해 앱을 변경하지 않아도 됩니다. 이 빠른 시작에서는 웹앱에 포함된 Docker 이미지를 생성하고, Azure Container Registry에 새 이미지를 푸시하고, Service Fabric 컨테이너 앱을 만들고, 컨테이너 앱을 Service Fabric 클러스터에 배포하는 과정을 설명합니다.  이 문서에서는 Docker에 대한 기본적으로 이해하고 있다고 가정합니다. [Docker 개요](https://docs.docker.com/engine/understanding-docker/)를 참고하여 Docker에 대해 알아볼 수 있습니다.

## <a name="prerequisites"></a>필수 조건
다음을 실행하는 개발 컴퓨터
* Visual Studio 2015 또는 Visual Studio 2017.
* [Service Fabric SDK 및 도구](service-fabric-get-started.md)
*  Windows용 Docker  [Windows용 Docker CE 가져오기(안정화)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) Docker를 설치하고 시작한 후에 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 **Windows 컨테이너로 전환**을 선택합니다. 그러려면 Windows를 기반으로 하는 Docker 이미지를 실행해야 합니다.

컨테이너를 사용하여 Windows Server 2016에서 실행되는 3개 이상의 노드가 있는 Windows 클러스터 - [클러스터를 만들](service-fabric-get-started-azure-cluster.md)거나 [체험판으로 Service Fabric을 사용](http://tryazureservicefabric.westus.cloudapp.azure.com/)하세요. 

Azure Container Registry의 레지스트리 - Azure 구독 내에서 [컨테이너 레지스트리를 만듭니다](../container-registry/container-registry-get-started-portal.md). 

## <a name="create-a-simple-web-app"></a>간단한 웹앱 만들기
동일한 위치에서 Docker 이미지에 로드해야 하는 모든 자산을 수집합니다. 이 빠른 시작에서 개발 컴퓨터에 "헬로 월드" 웹앱을 만듭니다.

1. *c:\temp\helloworldapp*과 같은 디렉터리를 만듭니다.
2. *c:\temp\helloworldapp\content*와 같은 하위 디렉터리를 만듭니다.
3. *c:\temp\helloworldapp\content*에서 *index.html* 파일을 만듭니다.
4. *index.html*을 편집하고 다음 줄을 추가합니다.
    ```
    <h1>Hello World!</h1>
    ```
5. *index.html*의 변경 내용을 저장합니다.

## <a name="build-the-docker-image"></a>Docker 이미지 빌드
Docker Hub에 있는 [microsft/iis 이미지](https://hub.docker.com/r/microsoft/iis/)를 기반으로 이미지를 빌드합니다. microsoft/iis 이미지는 Windows Server Core 기본 OS 이미지에서 파생되고 IIS(인터넷 정보 서비스)를 포함합니다.  컨테이너에서 이 이미지를 실행하면 자동으로 IIS를 시작하고 웹 사이트를 설치합니다.

Dockerfile에서 Docker 이미지를 정의합니다. Dockerfile은 이미지를 빌드하고 실행하려는 앱을 로드하기 위한 지침을 포함합니다. Dockerfile는 ```docker build``` 명령에 대한 입력이며 이미지를 만듭니다. 

1. *c:\temp\helloworldapp*에서 *Dockerfile*(파일 확장명이 없음) 파일을 만들고 다음을 추가합니다.

    ```
    # The `FROM` instruction specifies the base image. You are
    # extending the `microsoft/iis` image.
    FROM microsoft/iis

    # Create a directory to hold the web app in the container.
    RUN mkdir C:\site

    # Create a new IIS site.
    RUN powershell -NoProfile -Command \
        Import-module IISAdministration; \
        New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:8000:"

    # Opens port 8000 on the container.
    EXPOSE 8000

    # The final instruction copies the web app you created earlier into the container.
    ADD content/ /site
    ```

    이 Dockerfile에는 ```ENTRYPOINT``` 명령이 없습니다. 해당 명령이 필요하지 않습니다. IIS에서 Windows Server를 실행하는 경우 IIS 프로세스는 진입점이며 기본 이미지에서 시작하도록 구성됩니다.

    자세한 내용은 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/)를 참고하세요.

2. ```docker build``` 명령을 실행하여 웹앱을 실행하는 이미지를 만듭니다. PowerShell 창을 열고 *c:\temp\helloworldapp*으로 이동합니다. 다음 명령을 실행합니다.

    ```
    docker build -t helloworldapp .
    ```
    이 명령은 Dockerfile에 있는 지침을 사용하여 새 이미지를 빌드하며 이미지의 이름을 "helloworldapp"으로 지정(-t 태그 지정)합니다. 이미지를 빌드하면 Docker Hub에서 기본 이미지를 가져오고 기본 이미지를 기반으로 앱을 추가하는 새 이미지를 만듭니다.  [microsft/iis 이미지](https://hub.docker.com/r/microsoft/iis/) 및 OS 기본 이미지는 10.5GB이며 다운로드하여 개발 컴퓨터에 추출하는 데 시간이 걸립니다.  잠시 다른 작업을 하는 것이 좋습니다.  이전에 개발 컴퓨터에서 기본 OS 이미지를 가져왔던 경우 다운로드 시간이 줄어듭니다.

3. 빌드 명령이 완료되면 `docker images` 명령을 실행하여 새 이미지에 대한 정보를 확인합니다.

    ```
    docker images
    
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    helloworldapp              latest              86838648aab6        2 minutes ago       10.1 GB
    ```

## <a name="verify-the-image-runs-locally"></a>이미지를 로컬로 실행하는지 확인
컨테이너 레지스트리를 푸시하기 전에 먼저 로컬에서 이미지를 확인합니다.  

1. ```docker run```을 사용하여 컨테이너를 시작합니다.

    ```
    docker run -d -p 8000:8000 --name my-web-site helloworldapp
    ```

    *name*은 (컨테이너 ID가 아닌) 실행 중인 컨테이너에 이름을 지정합니다.

2. 컨테이너가 시작되면 브라우저에서 실행 중인 컨테이너에 연결할 수 있도록 해당 IP 주소를 찾습니다.
    ```
    docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
    ```

3. 실행 중인 컨테이너에 연결합니다.  포트 8000에서 반환되는 IP 주소(예: " http://172.31.194.61:8000 ")를 가리키는 웹 브라우저를 엽니다. 제목인 "Hello World!"가 브라우저에 표시됩니다.

4. 컨테이너를 중지하려면 다음을 실행합니다.

    ```
    docker stop my-web-site
    ```

5. 개발 컴퓨터에서 컨테이너를 삭제합니다.

    ```
    docker rm my-web-site
    ```

## <a name="push-the-image-to-the-container-registry"></a>컨테이너 레지스트리에 이미지를 푸시합니다.
컨테이너가 개발 컴퓨터에서 실행되었는지 확인한 후에 Azure Container Registry에서 이미지를 레지스트리에 푸시합니다.

1. [레지스트리 자격 증명](../container-registry/container-registry-authentication.md)을 사용하여 컨테이너 레지스트리에 로그인하려면 ``docker login``을 실행합니다.

    다음 예제는 Azure Active Directory [서비스 주체](../active-directory/active-directory-application-objects.md)의 ID와 암호를 전달합니다. 예를 들어 자동화 시나리오를 위해 레지스트리에 서비스 주체를 할당할 수 있습니다.

    ```
    docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
    ```

2. 다음 명령은 레지스트리에 대한 정규화된 경로를 사용하여 이미지의 태그 또는 별칭을 만듭니다. 이 예제는 레지스트리의 루트에서 혼잡을 방지하기 위해 ```samples``` 네임스페이스에 이미지를 배치합니다.

    ```
    docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
    ```

3.  이미지를 컨테이너 레지스트리에 푸시합니다.

    ```
    docker push myregistry.azurecr.io/samples/helloworldapp
    ```

## <a name="create-and-package-the-containerized-service-in-visual-studio"></a>Visual Studio에서 컨테이너화된 서비스를 만들고 패키징합니다.
Service Fabric SDK 및 도구는 컨테이너를 Service Fabric 클러스터에 배포할 수 있는 서비스 템플릿을 제공합니다.

1. Visual Studio를 시작합니다.  **파일** > **새로 만들기** > **프로젝트**를 선택합니다.
2. **Service Fabric 응용 프로그램**을 선택하고 "MyFirstContainer"라는 이름을 지정하고 **확인**을 클릭합니다.
3. **서비스 템플릿** 목록에서 **게스트 컨테이너**를 선택합니다.
4. **이미지 이름**에서 컨테이너 리포지토리에 푸시된 이미지인 "myregistry.azurecr.io/samples/helloworldapp"을 입력합니다. 
5. 서비스에 이름을 지정하고 **확인**을 클릭합니다.
6. 컨테이너화된 서비스에서 통신에 끝점이 필요한 경우 이제 프로토콜, 포트, 형식을 ServiceManifest.xml 파일의 ```Endpoint```에 추가할 수 있습니다. 이 빠른 시작에서 컨테이너화된 서비스는 포트 80에서 수신 대기합니다. 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    ```
    ```UriScheme```을 입력하면 이 컨테이너 끝점이 검색될 수 있도록 Service Fabric Naming 서비스에 자동으로 등록됩니다. 이 문서의 끝에서 전체 ServiceManifest.xml 예제 파일을 제공합니다. 
7. ApplicationManifest.xml 파일의 ```ContainerHostPolicies```에서 ```PortBinding``` 정책을 지정하여 컨테이너 포트 및 호스트 간 포트 매핑을 구성합니다.  이 빠른 시작에서 ```ContainerPort```은 8000(컨테이너가 Dockerfile에서 지정된 대로 포트 8000을 노출함)이고 ```EndpointRef```는 "Guest1TypeEndpoint"(서비스 매니페스트에서 정의된 끝점임)입니다.  포트 80에서 서비스에 들어오는 요청은 컨테이너에 있는 포트 8000에 매핑됩니다.  컨테이너가 개인 리포지토리에 인증해야 하는 경우 ```RepositoryCredentials```를 추가합니다.  이 빠른 시작에서 myregistry.azurecr.io 컨테이너 레지스트리의 계정 이름 및 암호를 추가합니다. 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    이 문서의 끝에서 전체 ApplicationManifest.xml 예제 파일을 제공합니다.
8. 클러스터에 앱을 게시할 수 있는 클러스터 연결 끝점을 구성합니다.  [Azure Portal](https://portal.azure.com)에 있는 클러스터의 개요 블레이드에서 클라이언트 연결 끝점을 찾을 수 있습니다. 솔루션 탐색기의 **MyFirstContainer**->**PublishProfiles** 아래에서 *Cloud.xml*을 엽니다.  **ClusterConnectionParameters**에 클러스터 이름 및 연결 포트를 추가합니다.  예:
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. 모든 파일을 저장하고 프로젝트를 빌드합니다.  

10. 앱을 패키징하려면 솔루션 탐색기에서 **MyFirstContainer**를 마우스 오른쪽 단추로 클릭하고 **패키지**를 선택합니다. 

## <a name="deploy-the-container-app"></a>컨테이너 앱 배포
1. 앱을 게시하려면 솔루션 탐색기에서 **MyFirstContainer**를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

2. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)는 Service Fabric 클러스터에서 응용 프로그램 및 노드를 검사 및 관리하기 위한 웹 기반 도구입니다. 브라우저를 열고 http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/로 이동하고 앱 배포를 수행합니다.  앱이 배포되지만 이미지가 클러스터 노드에서 다운로드될 때까지 오류 상태입니다(이미지 크기에 따라 시간이 걸릴 수 있음).  ![오류][1]

3. 앱이 ```Ready``` 상태이면 사용할 준비가 되었습니다.  ![준비][2]

4. 브라우저를 열고 http://containercluster.westus2.cloudapp.azure.com으로 이동합니다. 제목인 "Hello World!"가 브라우저에 표시됩니다.

## <a name="clean-up"></a>정리
클러스터가 실행되는 동안 요금이 계속 청구되므로 [클러스터를 삭제](service-fabric-get-started-azure-cluster.md#remove-the-cluster)하는 것이 좋습니다.  [파티 클러스터](http://tryazureservicefabric.westus.cloudapp.azure.com/)는 몇 시간 후 자동으로 삭제됩니다.

이미지를 컨테이너 레지스트리에 푸시한 후에 개발 컴퓨터에서 로컬 이미지를 삭제할 수 있습니다.

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric 응용 프로그램 및 서비스 매니페스트의 전체 예제
이 빠른 시작에서 사용되는 전체 서비스 및 응용 프로그램 매니페스트는 다음과 같습니다.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
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
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
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
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>다음 단계
* [Service Fabric의 컨테이너](service-fabric-containers-overview.md)를 실행하는 방법에 대해 자세히 알아봅니다.
* Service Fabric [응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)에 대해 자세히 알아봅니다.
* GitHub에서 [Service Fabric 컨테이너 코드 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-containers)을 확인합니다.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

