<properties
   pageTitle="Service Fabric 및 컨테이너 배포 | Microsoft Azure"
   description="Service Fabric 및 마이크로 서비스 응용 프로그램 배포를 위한 컨테이너 사용. 이 문서는 Service Fabric이 컨테이너에 대해 제공하는 기능과 클러스터에 컨테이너 이미지를 배포하는 방법을 알려줍니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="msfussell"/>


# <a name="preview:-deploy-a-container-to-service-fabric"></a>미리 보기: 컨테이너를 Service Fabric에 배포

>[AZURE.NOTE] 이 기능은 Linux용 미리 보기 상태이며 Windows Server에서는 현재 사용할 수 없습니다. Windows Server 2016 GA 후 Service Fabric의 다음 릴리스에서 Windows Server용 미리 보기로 제공된 후에 후속 릴리스에서 지원될 예정입니다.

Service Fabric에는 컨테이너화된 마이크로 서비스로 구성된 응용 프로그램을 빌드하는 데 도움을 주는 몇 가지 컨테이너 기능이 있습니다. 이것을 컨테이너화된 서비스라고 합니다. 기능은 다음과 같습니다.

- 컨테이너 이미지 배포 및 활성화
- 리소스 관리
- 리포지토리 인증
- 포트 매핑을 호스트하는 컨테이너 포트
- 컨테이너 간 검색 및 통신
- 환경 변수를 구성하고 설정할 수 있는 기능

컨테이너화된 서비스가 응용 프로그램에 포함되도록 패키징 하는 경우의 각 기능을 차례차례 살펴보겠습니다.

## <a name="packaging-a-container"></a>컨테이너 패키징

컨테이너를 패키징할 경우 Visual Studio 프로젝트 템플릿을 사용하거나 [응용 프로그램 패키지를 수동으로 만들도록](#manually)선택할 수 있습니다. Visual Studio를 사용하면 새 프로젝트 마법사에서 응용 프로그램 패키지 구조 및 매니페스트 파일을 생성합니다.

## <a name="using-visual-studio-to-package-an-existing-executable"></a>Visual Studio를 사용하여 기존 실행 파일 패키징

>[AZURE.NOTE] Visual Studio 도구 SDK 향후 릴리스에서는 현재 게스트 실행 파일을 추가하는 것과 비슷한 방식으로 컨테이너를 응용 프로그램에 추가할 수 있게 됩니다. [Service Fabric에 게스트 실행 파일 배포](service-fabric-deploy-existing-app.md) 항목을 참조하세요. 현재는 아래 설명에 따라 수동으로 패키징해야 합니다.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-container"></a>컨테이너 수동 패키징 및 배포
컨테이너화된 서비스를 수동으로 패키징하는 프로세스는 다음 단계를 기반으로 합니다.

1. 컨테이너를 리포지토리에 게시합니다.
2. 패키지 디렉터리 구조를 만듭니다.
3. 서비스 매니페스트 파일을 편집합니다.
4. 응용 프로그램 매니페스트 파일을 편집합니다.

## <a name="container-image-deployment-and-activation."></a>컨테이너 이미지 배포 및 활성화
Service Fabric [응용 프로그램 모델](service-fabric-application-model.md)에서 컨테이너는 다수의 서비스 복제본이 배치되는 응용 프로그램 호스트를 나타냅니다. 컨테이너를 배포하고 활성화하려면 컨테이너 이미지의 이름을 서비스 매니페스트의 `ContainerHost` 요소에 넣습니다.

서비스 매니페스트에서 진입점에 `ContainerHost`를 추가하고 `ImageName`이 컨테이너 리포지토리 및 이미지의 이름이 되도록 설정합니다. 다음 부분적인 매니페스트는 *myrepo*라는 리포지토리에서 *myimage:v1*라는 컨테이너를 배포하는 예제를 보여줍니다.

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

컨테이너 내에서 실행할 쉼표로 구분된 명령 집합과 함께 선택적인 `Commands` 요소를 지정하여 컨테이너 이미지에 입력 명령을 제공할 수 있습니다. 

## <a name="resource-governance"></a>리소스 관리
리소스 관리는 컨테이너의 기능이며 컨테이너가 호스트에서 사용할 수 있는 리소스를 제한합니다. 응용 프로그램 매니페스트에 지정된 `ResourceGovernancePolicy`는 서비스 코드 패키지에 대한 리소스 제한을 선언할 수 있는 기능을 제공합니다. 리소스 제한은 다음에 항목에 대해 설정할 수 있습니다.

- 메모리
- MemorySwap
- CpuShares(CPU 상대적 가중치)
- MemoryReservationInMB  
- BlkioWeight(BlockIO 상대적 가중치) 

>[AZURE.NOTE] 향후 릴리스에서는 IOP, 읽기/쓰기 BPS 등과 같은 특정 블록 IO 제한 지정에 대한 지원이 가능해질 예정입니다.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>리포지토리 인증
컨테이너를 다운로드하려면 컨테이너 리포지토리에 대한 로그인 자격 증명을 제공해야 할 수 있습니다. *application* 매니페스트에 지정된 로그인 자격 증명이 로그인 정보 또는 이미지 리포지토리에서 컨테이너 이미지를 다운로드하기 위해 SSH 키를 지정하는 데 사용됩니다.  다음 예제는 *TestUser* 라는 계정과 암호를 일반 텍스트로 보여줍니다. 이것은 권장되지 **않습니다** .


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

암호는 컴퓨터에 배포된 인증서를 사용하여 암호화되어야 합니다.

다음 예제는 *TestUser*라는 계정과 *MyCert*라는 인증서를 사용하여 암호화된 암호를 보여줍니다. `Invoke-ServiceFabricEncryptText` Powershell 명령을 사용하여 암호에 대한 암호 텍스트를 만들 수 있습니다. 자세한 방법은 [Service Fabric 응용 프로그램의 암호 관리](service-fabric-application-secret-management.md) 를 참조하세요. 암호 해독을 위한 인증서의 개인 키는 대역외 메서드로(Azure에서는 Resource Manager를 통해) 로컬 컴퓨터에 배포되어야 합니다. 그런 다음 Service Fabric이 컴퓨터에 서비스 패키지를 배포할 때 계정 이름과 함께 암호를 해독하고 이 자격 증명을 사용하여 컨테이너 리포지토리로 인증할 수 있습니다.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
            <SecurityAccessPolicies>
                <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
            </SecurityAccessPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>포트 매핑을 호스트하는 컨테이너 포트
응용 프로그램 매니페스트에 `PortBinding` 을 지정하여 컨테이너와의 통신에 사용되는 호스트 포트를 구성할 수 있습니다. 포트 바인딩은 컨테이너 내에서 서비스가 수신 대기 중인 포트를 호스트의 포트로 매핑합니다.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>컨테이너 간 검색 및 통신
다음 예제와 같이 `PortBinding` 정책을 사용하여 컨테이너 포트를 서비스 매니페스트의 `Endpoint`로 매핑할 수 있습니다. 끝점 `Endpoint1`은 고정된 포트를 지정할 수 있는 데, 예를 들면 포트 80을 지정하거나 포트를 전혀 지정하지 않을 수 있습니다. 이런 경우 클러스터의 응용 프로그램 포트 범위에서 무작위 포트가 선택됩니다.

게스트 컨테이너의 경우, 서비스 매니페스트에서 이와 같이 `Endpoint` 를 지정하면 Service Fabric에서 이 끝점을 명명 서비스에 자동으로 게시할 수 있기 때문에 클러스터 내에서 실행 중인 다른 서비스가 검색 서비스 REST 쿼리를 사용하여 이 컨테이너를 검색할 수 있습니다. 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

명명 서비스에 등록하면 [역방향 프록시](service-fabric-reverseproxy.md)를 사용하여 컨테이너 내 코드로 쉽게 컨테이너 간 통신이 가능합니다. 역방향 프록시 http 수신 대기 포트와 통신하려는 서비스 이름을 환경 변수로 설정하여 제공하기만 하면 됩니다. 이 작업을 수행하는 방법은 다음 섹션을 참조하세요.  

## <a name="configure-and-set-environment-variables"></a>환경 변수 구성 및 설정
환경 변수는 컨테이너에 배포된 서비스나 프로세스/게스트 실행 파일의 서비스 매니페스트에 각 코드 패키지에 대해 지정될 수 있습니다. 이러한 환경 변수 값은 응용 프로그램 매니페스트에서 명확하게 재정의되거나 응용 프로그램 매개 변수로 배포하는 동안 지정될 수 있습니다.

다음 서비스 매니페스트 XML 코드 조각은 코드 패키지에 대한 환경 변수를 지정하는 방법의 예제를 보여줍니다. 

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

이러한 환경 변수는 응용 프로그램 매니페스트 수준에서 재정의될 수 있습니다.

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

위의 예제에서 `HttpGateway` 환경 변수(19000)에는 명확한 값을 지정한 반면에 `BackendServiceName` 매개 변수의 값은 `[BackendSvc]` 응용 프로그램 매개 변수를 통해 설정됩니다. 이렇게 하면 매니페스트에 고정된 값을 두지 않고 응용 프로그램 배포 시에 `BackendServiceName`에 대한 값을 지정할 수 있습니다. 

## <a name="complete-examples-for-application-and-service-manifest"></a>응용 프로그램 및 서비스 매니페스트에 대한 전체 예제
다음은 컨테이너 기능을 보여주는 예제 응용 프로그램 매니페스트입니다.


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


다음은 컨테이너 기능을 보여주는 예제 서비스 매니페스트(이전 응용 프로그램 매니페스트에 지정됨)입니다.

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
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
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>



<!--HONumber=Oct16_HO2-->


