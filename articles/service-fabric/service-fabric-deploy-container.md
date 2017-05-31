---
title: "Service Fabric 및 컨테이너 배포 | Microsoft Docs"
description: "Service Fabric 및 마이크로 서비스 응용 프로그램 배포를 위한 컨테이너 사용. 이 문서는 Service Fabric이 컨테이너에 대해 제공하는 기능과 클러스터에 Windows 컨테이너 이미지를 배포하는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 799cc9ad-32fd-486e-a6b6-efff6b13622d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/16/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 17e9f4f81c60d86f804d1d9e6df2014dd4568d75
ms.contentlocale: ko-kr
ms.lasthandoff: 05/17/2017


---
# <a name="deploy-a-windows-container-to-service-fabric"></a>Windows 컨테이너를 Service Fabric에 배포
> [!div class="op_single_selector"]
> * [Windows 컨테이너 배포](service-fabric-deploy-container.md)
> * [Docker 컨테이너 배포](service-fabric-deploy-container-linux.md)
> 
> 

이 문서에서는 Windows 컨테이너에서 컨테이너화된 서비스를 빌드하는 과정을 안내합니다.

Service Fabric에는 컨테이너화된 마이크로 서비스로 구성된 응용 프로그램을 빌드하는 데 도움을 주는 몇 가지 컨테이너 기능이 있습니다. 

기능은 다음과 같습니다.

* 컨테이너 이미지 배포 및 활성화
* 리소스 관리
* 리포지토리 인증
* 컨테이너 포트와 호스트 포트 간 매핑
* 컨테이너 간 검색 및 통신
* 환경 변수를 구성하고 설정할 수 있는 기능

응용 프로그램에 포함할 컨테이너화된 서비스를 패키징하는 경우 각 기능이 어떻게 작동하는지 살펴보겠습니다.

## <a name="package-a-windows-container"></a>Windows 컨테이너 패키징
컨테이너를 패키징할 경우 Visual Studio 프로젝트 템플릿을 사용하거나 [응용 프로그램 패키지를 수동으로 만들도록](#manually)선택할 수 있습니다.  Visual Studio를 사용하면 새 프로젝트 템플릿에 의해 응용 프로그램 패키지 구조 및 매니페스트 파일이 생성됩니다.

> [!TIP]
> 기존 컨테이너 이미지를 서비스로 패키징하는 가장 쉬운 방법은 Visual Studio를 사용하는 것입니다.

## <a name="use-visual-studio-to-package-an-existing-container-image"></a>Visual Studio를 사용하여 기존 컨테이너 이미지 패키징
Visual Studio는 컨테이너를 Service Fabric 클러스터에 배포할 수 있도록 Service Fabric 서비스 템플릿을 제공합니다.

1. **파일** > **새 프로젝트**를 선택하여 Service Fabric 응용 프로그램을 만듭니다.
2. **게스트 컨테이너**를 서비스 템플릿으로 선택합니다.
3. **이미지 이름**을 선택하고 컨테이너 리포지토리(예: https://hub.docker.com/)의 이미지 경로를 입력합니다(예: myrepo/myimage:v1). 
4. 서비스에 이름을 지정하고 **확인**을 클릭합니다.
5. 컨테이너화된 서비스에서 통신에 끝점이 필요한 경우 이제 프로토콜, 포트, 형식을 ServiceManifest.xml 파일에 추가할 수 있습니다. 예: 
     
    `<Endpoint Name="MyContainerServiceEndpoint" Protocol="http" Port="80" UriScheme="http" PathSuffix="myapp/" Type="Input" />`
    
    `UriScheme`을 입력하면 이 컨테이너 끝점이 검색될 수 있도록 Service Fabric Naming 서비스에 자동으로 등록됩니다. 포트는 모든 서비스와 동일한 방식으로 고정(이전 예제에 표시)하거나 동적으로 할당(비워 두면 지정된 응용 프로그램 포트 범위에서 포트가 할당됨)할 수 있습니다.
    또한 아래에 설명된 대로 응용 프로그램 매니페스트에 `PortBinding` 정책을 지정하여 컨테이너 포트-호스트 포트 매핑을 구성해야 합니다.
6. 컨테이너에 리소스 관리가 필요할 경우 `ResourceGovernancePolicy`를 추가합니다.
8. 컨테이너가 개인 리포지토리에 인증해야 하는 경우 `RepositoryCredentials`를 추가합니다.
7. 컨테이너 지원이 활성화된 Windows Server 2016일 경우 이제 패키지를 사용하고 로컬 클러스터에 대한 작업을 게시할 수 있습니다. 
8. 준비가 되면 원격 클러스터로 응용 프로그램을 게시하거나 원본 제어에 대한 솔루션을 체크 인합니다. 

예제 응용 프로그램에 대해서는 [GitHub에서 Service Fabric 컨테이너 코드 샘플을 확인하세요](https://github.com/Azure-Samples/service-fabric-dotnet-containers).

## <a name="creating-a-windows-server-2016-cluster"></a>Windows Server 2016 클러스터 만들기
컨테이너화된 응용 프로그램을 배포하려면 컨테이너 지원이 설정된 Windows Server 2016이 실행되는 클러스터를 만들어야 합니다. 이러한 클러스터는 로컬 개발 컴퓨터에 있거나 Azuredml ARM(Azure Resource Manager)를 통해 배포할 수 있습니다. 

ARM을 사용하여 클러스터를 배포하려면 Azure에서 **Windows Server 2016(컨테이너 포함)** 이미지 옵션을 선택합니다. 문서 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)를 참조하세요. 다음 ARM 설정을 사용하는지 확인합니다.

```xml
"vmImageOffer": { "type": "string","defaultValue": "WindowsServer"     },
"vmImageSku": { "defaultValue": "2016-Datacenter-with-Containers","type": "string"     },
"vmImageVersion": { "defaultValue": "latest","type": "string"     },  
```
[5 Node ARM 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)을 사용하여 클러스터를 만들 수도 있습니다. Service Fabric 및 Windows 컨테이너 사용에 대한 [Leok의 블로그 게시물](https://loekd.blogspot.com/2017/01/running-windows-containers-on-azure.html)을 읽어볼 수도 있습니다.

<a id="manually"></a>

## <a name="manually-package-and-deploy-a-container-image"></a>수동으로 패키징하고 컨테이너 이미지 배포
컨테이너화된 서비스를 수동으로 패키징하는 프로세스는 다음 단계를 기반으로 합니다.

1. 컨테이너를 리포지토리에 게시합니다.
2. 패키지 디렉터리 구조를 만듭니다.
3. 서비스 매니페스트 파일을 편집합니다.
4. 응용 프로그램 매니페스트 파일을 편집합니다.

## <a name="deploy-and-activate-a-container-image"></a>컨테이너 이미지 배포 및 활성화
Service Fabric [응용 프로그램 모델](service-fabric-application-model.md)에서 컨테이너는 다수의 서비스 복제본이 배치되는 응용 프로그램 호스트를 나타냅니다. 컨테이너를 배포하고 활성화하려면 컨테이너 이미지의 이름을 서비스 매니페스트의 `ContainerHost` 요소에 넣습니다.

서비스 매니페스트에서 진입점용 `ContainerHost`를 추가합니다. 그런 다음 `ImageName`을 컨테이너 리포지토리 및 이미지의 이름이 되도록 설정합니다. 다음의 부분 매니페스트는 `myrepo`라는 리포지토리에서 `myimage:v1`라는 컨테이너를 배포하는 방법의 예를 보여줍니다.

```xml
    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimage:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>
```

컨테이너 내에서 실행할 쉼표로 구분된 명령 집합과 함께 선택적인 `Commands` 요소를 지정하여 입력 명령을 제공할 수 있습니다.

## <a name="understand-resource-governance"></a>리소스 관리 이해
리소스 관리는 호스트에서 컨테이너가 사용할 수 있는 리소스를 제한하는 컨테이너의 기능입니다. 응용 프로그램 매니페스트에서 지정된 `ResourceGovernancePolicy`는 서비스 코드 패키지에 대한 리소스 제한을 선언하는 데 사용됩니다. 다음 리소스에 대한 리소스 제한을 설정할 수 있습니다.

* 메모리
* MemorySwap
* CpuShares(CPU 상대적 가중치)
* MemoryReservationInMB  
* BlkioWeight(BlockIO 상대적 가중치)

> [!NOTE]
> 향후 릴리스에서는 IOP, 읽기/쓰기 BPS 등과 같은 특정 블록 IO 제한 지정에 대한 지원이 포함될 예정입니다.
> 
> 

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500"
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="authenticate-a-repository"></a>리포지토리 인증
컨테이너를 다운로드하려면 컨테이너 리포지토리에 대한 로그인 자격 증명을 제공해야 할 수 있습니다. 애플리케이션 매니페스트에 지정된 로그인 자격 증명은 로그인 정보 또는 이미지 리포지토리에서 컨테이너 이미지를 다운로드하기 위한 SSH 키를 지정하는 데 사용됩니다. 다음 예제는 *TestUser*라는 계정과 암호를 일반 텍스트로 보여줍니다(권장되지 *않음*).

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

컴퓨터에 배포된 인증서를 사용하여 암호를 암호화하는 것이 좋습니다.

다음 예제는 *MyCert*라는 인증서를 사용하여 암호가 암호화된 *TestUser*라는 계정을 보여줍니다. `Invoke-ServiceFabricEncryptText` PowerShell 명령을 사용하여 암호에 대한 암호 텍스트를 만들 수 있습니다. 자세한 내용은 [Service Fabric 응용 프로그램의 암호 관리](service-fabric-application-secret-management.md) 문서를 참조하세요.

암호 해독에 사용되는 인증서의 개인 키는 대역외 메서드를 통해 로컬 컴퓨터에 배포되어야 합니다. (Azure에서 이 메서드는 Azure Resource Manager입니다.) 그런 다음 Service Fabric이 서비스 패키지를 컴퓨터에 배포하면 암호를 해독할 수 있습니다. 계정 이름과 암호를 사용하면 컨테이너 리포지토리를 통해 인증할 수 있습니다.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping"></a>컨테이너 포트와 호스트 포트 간 매핑 구성
응용 프로그램 매니페스트에 `PortBinding`을 지정하여 컨테이너와의 통신에 사용되는 호스트 포트를 구성할 수 있습니다. 포트 바인딩은 컨테이너 내에서 서비스가 수신 대기 중인 포트를 호스트의 포트로 매핑합니다.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-to-container-discovery-and-communication"></a>컨테이너 간 검색 및 통신 구성
다음 예제와 같이 `PortBinding` 정책을 사용하여 컨테이너 포트를 서비스 매니페스트의 `Endpoint`에 매핑할 수 있습니다. 끝점 `Endpoint1`(예: 포트 80)은 고정 포트를 지정할 수 있습니다. 어떤 포트도 지정하지 않을 수 있는데, 그런 경우 클러스터의 응용 프로그램 포트 범위에서 포트가 무작위로 선택됩니다.

게스트 컨테이너의 서비스 매니페스트에서 `Endpoint` 태그를 사용하여 끝점을 지정하는 경우 Service Fabric은 이 끝점을 명명 서비스에 자동으로 게시할 수 있습니다. 클러스터에서 실행되는 기타 서비스는 해결용 REST 쿼리를 사용하여 이 컨테이너를 검색할 수 있습니다.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

명명 서비스에 등록하면 [역방향 프록시](service-fabric-reverseproxy.md)를 사용하여 컨테이너 내 코드를 통해 쉽게 컨테이너 간 통신을 할 수 있습니다. 역방향 프록시 http 수신 대기 포트와 통신하려는 서비스 이름을 환경 변수로서 제공하면 통신이 이루어집니다. 자세한 내용은 다음 섹션을 참조하세요. 

## <a name="configure-and-set-environment-variables"></a>환경 변수 구성 및 설정
환경 변수는 컨테이너에 배포된 서비스나 프로세스/게스트 실행 파일로서 배포된 서비스를 위해 서비스 매니페스트의 각 코드 패키지에 지정될 수 있습니다. 이러한 환경 변수 값은 응용 프로그램 매니페스트에서 명확하게 재정의되거나 응용 프로그램 매개 변수로 배포하는 동안 지정될 수 있습니다.

다음 서비스 매니페스트 XML 코드 조각은 코드 패키지에 대한 환경 변수를 지정하는 방법의 예제를 보여줍니다.

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>
```

이러한 환경 변수는 응용 프로그램 매니페스트 수준에서 재정의될 수 있습니다.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>
```

이전 예제에서 `HttpGateway` 환경 변수(19000)에는 명확한 값을 지정했던 반면에 `BackendServiceName` 매개 변수의 값은 `[BackendSvc]` 응용 프로그램 매개 변수를 통해 설정했습니다. 이렇게 설정하면 응용 프로그램을 배포하고 매니페스트에 고정된 값을 지정하지 않은 경우 `BackendServiceName`에 값을 지정할 수 있습니다.

## <a name="complete-examples-for-application-and-service-manifest"></a>응용 프로그램 및 서비스 매니페스트에 대한 전체 예제

예제 응용 프로그램 매니페스트는 다음을 따릅니다.

```xml
    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>
```

예제 서비스 매니페스트(이전 응용 프로그램 매니페스트에 지정됨)는 다음을 따릅니다.

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless front end in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Endpoints>
                <Endpoint Name="Endpoint1" UriScheme="http" Port="80" Protocol="http"/>
            </Endpoints>
        </Resources>
    </ServiceManifest>
```

## <a name="next-steps"></a>다음 단계
컨테이너화된 서비스를 배포했으므로 [Service Fabric 응용 프로그램 수명 주기](service-fabric-application-lifecycle.md)를 읽고 수명 주기를 관리하는 방법에 대해 알아보세요.

* [Service Fabric 및 컨테이너 개요](service-fabric-containers-overview.md)
* 예제 응용 프로그램에 대해서는 [GitHub에서 Service Fabric 컨테이너 코드 샘플을 확인하세요](https://github.com/Azure-Samples/service-fabric-dotnet-containers).

