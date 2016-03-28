<properties
   pageTitle="Java용 리소스 관리자 SDK | Microsoft Azure"
   description="리소스 관리자 Java SDK 인증 및 사용 예제 개요"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="navale;tomfitz;"/>
   
# Java용 Azure Resource Manager SDK
   
ARM(Azure Resource Manager) 미리 보기 SDK는 여러 언어 및 플랫폼에 사용할 수 있습니다. 이러한 언어 구현은 각각 해당 에코 시스템 패키지 관리자 및 GitHub를 통해 사용할 수 있습니다.

이러한 SDK의 각 코드는 [Azure RESTful API 사양](https://github.com/azure/azure-rest-api-specs)에서 생성됩니다. 이러한 사양은 오픈 소스이며 Swagger v2 사양을 기반으로 합니다. SDK 코드는 [AutoRest](https://github.com/azure/autorest)라는 오픈 소스 프로젝트를 통해 생성된 코드입니다. AutoRest는 이러한 RESTful API 사양을 여러 언어의 클라이언트 라이브러리로 변환합니다. SDK에서 생성된 코드의 어떤 측면을 개선하려면 SDK를 만드는 전체 도구는 오픈되어 자유롭게 사용 가능하고 광범위하게 채택된 API 사양 형식에 기반으로 해야 합니다.

Azure Resource Manager Java SDK는 GitHub [Azure Java SDK 리포지토리](https://github.com/azure/azure-sdk-for-java)에서 호스팅됨니다. 작성할 때 이 SDK는 미리 보기 상태입니다. 다음과 같은 패키지를 사용할 수 있습니다.

* 계산 관리: (azure-mgmt-compute)
* DNS 관리: (azure-mgmt-dns)
* 네트워크 관리: (azure-mgmt-network)
* 리소스 관리: (azure-mgmt-resources)
* SQL 관리: (azure-mgmt-sql)
* 저장소 관리: (azure-mgmt-storage)
* 트래픽 관리자 관리: (azure-mgmt-traffic-manager)
* 유틸리티 및 도우미: (azure-mgmt-utility)
* 웹 사이트 / 웹 앱 관리: (azure-mgmt-websites)

최신 목록은 [Java 기능 Wiki 페이지](https://github.com/Azure/azure-sdk-for-java/wiki/Azure-SDK-for-Java-Features)용 Azure SDK로 이동합니다.

## 필수 조건
1. Java v1.6+
2. [maven](https://maven.apache.org/) SDK에서 개발하려는 경우

## SDK 가져오기
[Maven](https://maven.apache.org/) 분산된 jar은 Azure Java SDK를 시작하기에 좋은 방법입니다. 대부분의 Java 종속성 관리 도구(Maven, Gradle, Ivy...)에 이러한 종속성을 추가할 수 있습니다. maven에서 사용할 수 있는 라이브러리의 목록은 이 [링크](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.azure%22)로 이동합니다.

또는 Git를 사용하여 원본에서 직접 SDK를 가져옵니다. Git를 통해 SDK 원본 코드를 가져오려면:

    git clone https://github.com/Azure/azure-sdk-for-java.git
    cd ./azure-sdk-for-java/

(이 개요의 샘플은 Maven을 SDK 패키지에 대한 원본으로 사용합니다)

SDK는 인증, VM 프로비전 등 일부 시나리오에 대한 샘플을 포함합니다. 모두 [azure-mgmt-samples](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples) GitHub 리포지토리에서 찾을 수 있습니다.

## 도우미 클래스

SDK는 주 패키지에 대한 도우미 클래스를 포함합니다. 도우미 클래스는 auzre-mgmt-utility 패키지에 구현됩니다.

* AuthHelper - 인증 도우미 클래스
* ComputeHelper - 계산 도우미 클래스
* NetworkHelper - 네트워크 도우미 클래스
* ResourceHelper - 배포 도우미 클래스
* StorageHelper - 저장소 도우미 클래스

**Maven 종속성 정보**

    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-utility</artifactId>
      <version>0.9.1</version>
    </dependency>

유틸리티 패키지의 0.9.1 버전을 사용합니다.

## 인증

ARM에 대한 인증은 Azure AD(Active Directory)에 의해 처리됩니다. API에 연결하기 위해 먼저 모든 요청에 전달할 수 있는 인증 토큰을 수신하도록 Azure AD로 인증해야 합니다. 이 토큰을 가져오려면 먼저 로그인에 사용되는 Azure AD 응용 프로그램 및 서비스 주체를 만들어야 합니다. 단계별 지침은 [Azure AD 응용 프로그램 및 서비스 주체 만들기](./resource-group-create-service-principal-portal.md)를 따릅니다.

서비스 주체를 만든 후에 다음을 수행해야 합니다.
* 클라이언트 ID(GUID)
* 클라이언트 암호(문자열)
* 테넌트 ID(GUID) 또는 도메인 이름(문자열) 이 값이 있는 경우 한 시간 동안 유효한 Active Directory 액세스 토큰을 얻을 수 있습니다.

클라이언트 ID, 암호 및 테넌트 ID로 제공되면 Java SDK에는 액세스 토큰을 만드는 도우미 클래스 AuthHelper가 포함됩니다. 다음 예제는 [ServicePrincipalExample](https://github.com/Azure/azure-sdk-for-java/blob/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/authentication/ServicePrincipalExample.java) 클래스에서 AuthHelper *getAccessTokenFromServicePrincipalCredentials* 메서드를 사용하여 액세스 토큰을 가져옵니다.

```java
public static Configuration createConfiguration() throws Exception {
   String baseUri = System.getenv("arm.url");

   return ManagementConfiguration.configure(
         null,
         baseUri != null ? new URI(baseUri) : null,
         System.getenv(ManagementConfiguration.SUBSCRIPTION_ID),
         AuthHelper.getAccessTokenFromServicePrincipalCredentials(
                  System.getenv(ManagementConfiguration.URI), System.getenv("arm.aad.url"),
                  System.getenv("arm.tenant"), System.getenv("arm.clientid"),
                  System.getenv("arm.clientkey"))
                  .getAccessToken());
}
```

## 가상 컴퓨터 만들기 
유틸리티 패키지는 도우미 클래스 [ComputeHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ComputeHelper.java)를 포함하여 가상 컴퓨터를 만듭니다. 가상 컴퓨터로 사용하기 위한 몇 가지 샘플은 [계산](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/compute)의 azure-mgmt-samples 패키지에서 찾을 수 있습니다.

다음은 가상 컴퓨터를 만들기 위한 간단한 흐름입니다. 이 예제에서는 도우미 클래스가 VM을 만드는 일부로 저장소 및 네트워크를 만듭니다.

```java
public static void main(String[] args) throws Exception {
        Configuration config = createConfiguration();
        ResourceManagementClient resourceManagementClient = ResourceManagementService.create(config);
        StorageManagementClient storageManagementClient = StorageManagementService.create(config);
        ComputeManagementClient computeManagementClient = ComputeManagementService.create(config);
        NetworkResourceProviderClient networkResourceProviderClient = NetworkResourceProviderService.create(config);

        String resourceGroupName = "javasampleresourcegroup";
        String region = "EastAsia";

        ResourceContext context = new ResourceContext(
                region, resourceGroupName, System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);

        System.out.println("Start create vm...");

        try {
            VirtualMachine vm = ComputeHelper.createVM(
                    resourceManagementClient, computeManagementClient, networkResourceProviderClient, 
                    storageManagementClient,
                    context, vnName, vmAdminUser, vmAdminPassword)
              .getVirtualMachine();

            System.out.println(vm.getName() + " is created");
        } catch (Exception ex) {
            System.out.println(ex.toString());
        }
}
```

## 템플릿 배포
Java SDK가 포함된 ARM 템플릿을 배포하는 프로세스가 용이하도록 [ResouceHelper](https://github.com/Azure/azure-sdk-for-java/blob/master/resource-management/azure-mgmt-utility/src/main/java/com/microsoft/azure/utility/ResourceHelper.java) 클래스를 만들었습니다.

```java
// create a new resource group
ResourceManagementClient resourceManagementClient = createResourceManagementClient();
ResourceContext resourceContext = new ResourceContext(
        resourceGroupLocation, resourceGroupName,
        System.getenv(ManagementConfiguration.SUBSCRIPTION_ID), false);
ComputeHelper.createOrUpdateResourceGroup(resourceManagementClient, resourceContext);

// create the template deployment
DeploymentExtended deployment = ResourceHelper.createTemplateDeploymentFromURI(
        resourceManagementClient,
        resourceGroupName,
        DeploymentMode.Incremental,
        deploymentName,
        TEMPLATE_URI,
        "1.0.0.0",
        parameters);
```

[templatedeployments](https://github.com/Azure/azure-sdk-for-java/tree/master/azure-mgmt-samples/src/main/java/com/microsoft/azure/samples/templatedeployments)의 샘플 패키지에서 더 많은 샘플을 찾을 수 있습니다.

## 추가 자료 및 도움말
Java용 Azure SDK 설명서: [Java 문서](http://azure.github.io/azure-sdk-for-java/) SDK를 사용할 때 버그가 발생하는 경우 [문제](https://github.com/Azure/azure-sdk-for-java/issues) 또는 체크 아웃 [Azure Java SDK용 StackOverflow](http://stackoverflow.com/questions/tagged/azure-java-sdk)를 통해 문제를 제출해주세요.

<!---HONumber=AcomDC_0316_2016-->