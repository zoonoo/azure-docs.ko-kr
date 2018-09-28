---
title: API 버전 프로필을 사용 하 여 Azure Stack에서 Java를 사용 하 여 | Microsoft Docs
description: API 버전 프로필을 사용 하 여 Azure Stack에서 Java를 사용 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: b5a876ea8b5cc70ee0ca0dcac8628c12dc2b009b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414948"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Azure Stack에서 Java를 사용 하 여 API 버전 프로필 사용

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Java SDK는 Azure Stack Resource Manager에 대 한 빌드 및 인프라를 관리 하는 데 유용한 도구를 제공 합니다. 계산, 네트워킹, 저장소, app services 리소스 공급자 SDK에 포함 하 고 [KeyVault](../../key-vault/key-vault-whatis.md)합니다. Java SDK.java 파일의 올바른 모듈을 로드 하는 Pom.xml 파일에 종속성을 포함 하 여 API 프로필을 통합 합니다. 그러나 추가할 수 있습니다 여러 프로필이 종속성으로 같은 합니다 **2018-03-01-하이브리드**, 또는 **최신** Azure 프로필입니다. 이러한 종속성을 사용 하 여 사용자 리소스 종류를 만들 때는 사용 하려면 해당 프로필에서 API 버전을 선택할 수 있도록 올바른 모듈을 로드 합니다. 이 옵션을 사용 하면 Azure Stack에 대 한 최신 API 버전에 대해 개발 하는 동안 Azure에서 최신 버전을 사용할 수 있습니다. Java SDK를 사용 하 여 진정한 하이브리드 클라우드 개발자 환경 사용 하도록 설정 합니다. Java sdk에서 API 프로필 전역 Azure 리소스 및 Azure Stack에서 리소스 간에 전환할 수 있도록 하 여 하이브리드 클라우드 개발을 사용 합니다.

## <a name="java-and-api-version-profiles"></a>Java 및 API 버전 프로필

프로필을 API에는 리소스 공급자 및 API 버전의 조합입니다. 리소스 공급자 패키지의 각 리소스 종류의 최신, 가장 안정적 버전을 얻으려면 API 프로필을 사용할 수 있습니다.

- 모든 서비스의 최신 버전을 사용 하려면 사용 합니다 **최신** 종속성으로 프로필입니다.
    
   - 종속성이 최신 프로필을 사용 하려면 **com.microsoft.azure**합니다.

   - Azure Stack 호환 서비스를 사용 하려면 사용 합니다 **com.microsoft.azure.profile\_2018\_03\_01\_하이브리드** 프로필입니다.
    
      - 이.NET와 마찬가지로 오른쪽 클래스 드롭다운 목록에서 선택 하는 경우 모듈을 자동으로 로드 하는 종속성으로 Pom.xml 파일에 지정 되어야 합니다.
        
          - 각 모듈의 맨 위에 다음과 같이 나타납니다.         
           `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`
             

  - 종속성을 다음과 같이 나타납니다.
     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - 특정 리소스 공급자의 리소스 형식에 대 한 특정 API 버전을 사용 하려면 intellisense를 통해 정의 된 특정 API 버전을 사용 합니다.

참고 동일한 응용 프로그램 옵션을 모두 조합할 수 있습니다.

## <a name="install-the-azure-java-sdk"></a>설치 된 Azure Java SDK

Java SDK를 설치 하려면 다음 단계를 사용 합니다.

1.  Git을 설치 하는 공식 지침을 따릅니다. 자세한 내용은 [시작-Git 설치](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)합니다.

2.  공식 지침에 따라 설치 합니다 [Java SDK](http://zulu.org/download/)) 및 [Maven](https://maven.apache.org/)합니다. 올바른 버전은 버전 8 Java Developer Kit입니다. 올바른 Apache Maven은 버전 3.0 이상. 빠른 시작을 완료 하려면 JAVA_HOME 환경 변수가 Java Development Kit의 설치 위치에 설정 되어야 합니다. 자세한 내용은 [Java 및 Maven을 사용 하 여 첫 번째 함수 만들기](../../azure-functions/functions-create-first-java-maven.md)합니다.

3.  올바른 종속성 패키지를 설치 하려면 Java 응용 프로그램에서 Pom.xml 파일을 엽니다. 다음 코드와 같이 종속성을 추가 합니다.

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4.  패키지를 설치 해야 하는 사용 하려는 프로필 버전에 따라 달라 집니다. 프로필 버전에 대 한 패키지 이름은 다음과 같습니다.
    
   - **com.microsoft.azure.profile\_2018\_03\_01\_하이브리드**
   - **com.microsoft.azure**
      - **latest**

5.  사용할 수 없는 경우 구독을 만들고 나중에 사용할 구독 ID를 저장 합니다. 구독을 만드는 방법에 지침은 [Azure Stack에서 제품에 구독을 만들려면](../azure-stack-subscribe-plan-provision-vm.md)합니다.

6.  서비스 주체를 만들고 클라이언트 ID 및 클라이언트 암호를 저장 합니다. Azure Stack에 대 한 서비스 주체를 만드는 방법에 지침은 [Azure Stack에 대 한 응용 프로그램 액세스할](../azure-stack-create-service-principals.md)합니다. 클라이언트 ID 라고도 응용 프로그램 ID 서비스 주체를 만들 때 note 합니다.

7.  구독에서 서비스 주체에 참가자/소유자 역할이 있는지 확인 합니다. 서비스 주체에 역할을 할당 하는 방법에 지침은 [Azure Stack에 대 한 응용 프로그램 액세스할](../azure-stack-create-service-principals.md)합니다.

## <a name="prerequisites"></a>필수 조건

Azure Stack을 사용 하 여 Azure.NET SDK를 사용 하려면 다음 값을 제공 하며 다음 환경 변수를 사용 하 여 값을 설정 합니다. 환경 변수를 설정 하려면 표 다음에 운영 체제에 대 한 지침을 참조 하세요.

| 값                     | 환경 변수 | 설명                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 테넌트 ID                 | TENANT_ID            | Azure Stack에 값 [ <span class="underline">테 넌 트 ID</span>](../azure-stack-identity-overview.md)합니다.                                                          |
| 클라이언트 ID                 | CLIENT_ID             | 주 응용 프로그램 ID 저장이 문서의 이전 섹션에서 서비스 주체를 만들 때 서비스입니다.                                                                                              |
| 구독 ID           | SUBSCRIPTION_ID      | 합니다 [ <span class="underline">구독 ID</span> ](../azure-stack-plan-offer-quota-overview.md#subscriptions) 제품을 액세스 하는 방법에 Azure Stack에서.                |
| 클라이언트 암호             | CLIENT_SECRET        | 서비스 주체 응용 프로그램 서비스 주체를 만들 때 암호 저장 합니다.                                                                                                                                   |
| Resource Manager 끝점 | 끝점              | 참조 [ <span class="underline">Azure Stack 리소스 관리자 끝점</span>](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint)합니다. |
| 위치                  | RESOURCE_LOCATION    | Azure Stack에 대 한 로컬                                                                                                                                                                                                |

Azure Stack에 대 한 테 넌 트 ID를 찾으려면 지침을 따릅니다 하세요 [여기](../azure-stack-csp-ref-operations.md)합니다. 환경 변수를 설정 하려면 다음을 수행 합니다.

### <a name="microsoft-windows"></a>Microsoft Windows

Windows 명령 프롬프트에서 환경 변수를 설정 하려면 다음 형식을 사용 합니다.

```shell
Set Azure_Tenant_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux 및 Unix 기반 시스템

Unix 기반 시스템에서 다음 명령을 사용할 수 있습니다.

```shell
Export Azure_Tenant_ID=<Your_Tenant_ID>
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack resource manager 끝점

Microsoft Azure 리소스 관리자는 관리자가 배포, 관리 및 Azure 리소스를 모니터링할 수 있도록 관리 합니다. Azure Resource Manager 그룹으로 대신 개별적으로 단일 작업에서 이러한 작업을 처리할 수 있습니다.

Resource Manager 끝점에서 메타 데이터 정보를 가져올 수 있습니다. 코드를 실행 하는 데 필요한 정보를 사용 하 여 JSON 파일을 반환 하는 끝점입니다.

다음 고려 사항을 note 합니다.

- 합니다 **ResourceManagerUrl** 에 Azure Stack 개발 키트 ASDK ()는: https://management.local.azurestack.external/

- **ResourceManagerUrl** 통합된 시스템의: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` 필요한 메타 데이터를 검색 합니다. `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

샘플 JSON 파일:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>기존 API 프로필

1.  **com.microsoft.azure.profile\_2018\_03\_01\_하이브리드**: Azure Stack 용으로 작성 된 최신 프로필입니다. 가장 1808 스탬프에는 Azure Stack을 사용 하 여 호환 또는 추가 서비스에 대 한이 프로필을 사용 합니다.

2.  **com.microsoft.azure.profile\_2017\_03\_09\_프로필**: 1808 빌드 보다 낮은 스탬프를 사용 하는 경우이 프로필을 사용 합니다.

3.  **com.microsoft.azure**: 모든 서비스의 최신 버전으로 구성 된 프로필입니다. 모든 서비스의 최신 버전을 사용 합니다.

API 및 Azure Stack 프로필에 대 한 자세한 내용은 참조는 [API의 요약 프로필](../user/azure-stack-version-profiles.md#summary-of-api-profiles)합니다.

## <a name="azure-java-sdk-api-profile-usage"></a>Azure Java SDK API 프로필 사용

다음 코드는 Azure Stack에서 서비스 주체를 인증합니다. ID 및 Azure Stack에만 해당 되는 인증 자료에는 테 넌 트에서 토큰을 만듭니다.

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionId(subscriptionId);
            Azure azureStack = Azure.configure().withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionId());
```

이 옵션을 사용 하면 API 프로필 종속성을 사용 하 여 Azure Stack에 성공적으로 응용 프로그램을 배포할 수 있습니다.

## <a name="define-azure-stack-environment-setting-functions"></a>Azure Stack 환경 설정 함수를 정의 합니다.

Azure Stack 클라우드에 올바른 끝점을 등록 하려면 다음 코드를 사용 합니다.

```java
AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceId", settings.get("audience"));
                    put("activeDirectoryGraphResourceId", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".adminvault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

`getActiveDirectorySettings` 다음 코드에서 호출 된 메타 데이터 끝점에서 끝점을 검색 합니다. 이 제안에 따르면 되는 호출에서 환경 변수:

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml
data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>API 프로필을 사용 하는 샘플

.NET 및 Azure Stack API 프로필을 사용 하 여 솔루션을 만들기 위한 참조로 다음 GitHub 샘플을 사용할 수 있습니다.

  - [리소스 그룹 관리](https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid)

  - [저장소 계정 관리](https://github.com/viananth/storage-java-manage-storage-accounts/tree/stack/Hybrid)

  - [가상 컴퓨터를 관리 합니다.](https://github.com/viananth/compute-java-manage-vm/tree/stack/Hybrid)

### <a name="sample-unit-test-project"></a>샘플 단위 테스트 프로젝트 

1.  다음 명령을 사용 하 여 리포지토리를 복제 합니다.
    
    `git clone https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid`

2.  Azure 서비스 주체 만들기 및 구독에 액세스 하는 역할을 할당 합니다. 서비스 주체를 만드는 방법에 지침은 [인증서를 사용 하 여 서비스 주체를 만들려면 Azure PowerShell을 사용 하 여](../azure-stack-create-service-principals.md)입니다.

3.  다음 값을 검색 합니다.
    
   1.  테넌트 ID
   2.  클라이언트 ID
   3.  클라이언트 암호
   4.  구독 ID
   5.  Resource Manager 끝점
   6.  리소스 위치

4.  명령 프롬프트를 사용 하 여 만든 서비스 주체에서를 검색 한 정보를 사용 하 여 다음 환경 변수를 설정 합니다.
    
   1. TENANT_ID 내보내기 = {테 넌 트 id}
   2. CLIENT_ID 내보내기 = {client id}
   3. CLIENT_SECRET 내보내기 = {클라이언트 암호}
   4. SUBSCRIPTION_ID 내보내기 = {구독 id}
   5. ARM_ENDPOINT 내보내기 = {에 Azure Stack Resource manager URL}
   6. RESOURCE_LOCATION 내보내기 = {location Azure Stack의}

   Windows를 사용 하 여 **설정할** of **내보내기**합니다.

5.  사용 된 `getactivedirectorysettings` 끝점 정보를 설정 하려면 HTTP 클라이언트를 사용 하 여 arm 메타 데이터 끝점을 검색 하는 코드입니다.

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

7.  Pom.xml 파일에 Azure Stack에 대 한 2018-03-01-하이브리드 프로필을 사용 하려면 다음 종속성을 추가 합니다. 이 종속성이이 프로필과 연결 된 계산, 네트워킹, 저장소, KeyVault 및 App Services 리소스 공급자에 대 한 모듈을 설치 합니다.
    
   최신 프로필의 대상 Azure 사용할 수 있습니다 note:
        
   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

8.  환경 변수를 설정 하려면 열려 있는 명령 프롬프트에서 다음 줄을 입력 합니다.
    
   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>다음 단계

API 프로필에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Stack에서 API 버전 프로필 관리](azure-stack-version-profiles.md)
- [프로필에서 지 원하는 리소스 공급자 API 버전](azure-stack-profiles-azure-resource-manager-versions.md)
