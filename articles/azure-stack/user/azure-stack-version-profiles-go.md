---
title: Azure Stack에서 GO를 사용한 API 버전 프로필을 사용 하 여 | Microsoft Docs
description: Azure Stack에서 GO를 사용한 API 버전 프로필을 사용 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 9ad4402098e938f72cf4b8c61cce8d0d46b5a147
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139565"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Azure Stack에서 Go를 사용한 API 버전 프로필 사용

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

## <a name="go-and-version-profiles"></a>Go 및 버전 프로필

프로필에는 다른 서비스에서 다른 버전을 사용 하 여 다양 한 리소스 유형 조합입니다. 프로필을 사용 하 여 혼합 및 다른 리소스 유형과 일치 하는 데 도움이 됩니다. 프로필을 제공할 수 있습니다.

 - 특정 API 버전을 잠그는 방식으로 응용 프로그램에 대 한 안정성.
 - Azure Stack 및 Azure 지역 데이터 센터를 사용 하 여 응용 프로그램에 대 한 호환성.

프로필은 프로필에서 사용할 수 있는 Go sdk에서 / 해당 버전을 사용 하 여 경로 **YYYY-월-일** 형식입니다. 지금 최신 Azure Stack 프로필 버전이 **2017-03-09**합니다. 프로필에서 지정된 된 서비스를 가져오려는 모듈을 가져오려면 해당 해당 프로필에서 지정 해야 합니다. 예를 들어 가져오려는 **계산** 서비스 **2017-03-09** 프로필:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Azure SDK for Go 설치

  1. Git을 설치 합니다. 자세한 내용은 [시작-Git 설치](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)합니다.
  2. 설치 합니다 [Go 프로그래밍 언어](https://golang.org/dl)합니다.  
  Azure에 대 한 프로필 API는 Go 버전 1.9 이상 버전이 필요 합니다.
  3. 다음 bash 명령을 실행 하 여 Go Azure SDK 및 해당 종속성을 설치 합니다.
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>GO SDK

자세한 내용은 Azure GO SDK에서 찾을 수 있습니다.
- Azure SDK에 이동 [Azure SDK for Go 설치](https://docs.microsoft.com/go/azure/azure-sdk-go-install)합니다.
- Azure Go SDK가 있는 GitHub에서 공개적으로 사용할 [go에 대 한 azure sdk](https://github.com/Azure/azure-sdk-for-go)합니다.

### <a name="go-autorest-dependencies"></a>Go AutoRest 종속성

GO SDK는 Azure Resource Manager 끝점에 REST 요청을 보내는 Azure Go AutoRest 모듈에 따라 달라 집니다. Azure Go AutoRest 모듈 종속성을 가져오려면 해야 [GitHub의 Azure Go AutoRest](https://github.com/Azure/go-autorest)합니다. 설치 bash 명령을 찾을 수 있습니다 합니다 **설치** 섹션입니다.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Azure Stack에서 GO SDK 프로필을 사용 하는 방법

Azure Stack에서 Go 코드 샘플을 실행 합니다 하:
  1. Go 및 해당 종속성에 대 한 Azure SDK를 설치 합니다. 명령에 대 한 이전 섹션을 참조 하세요 [Azure SDK for Go 설치](#install-azure-sdk-for-go)합니다.
  2. Resource Manager 끝점에서 메타 데이터 정보를 가져옵니다. Go 코드를 실행 하는 데 필요한 정보를 사용 하 여 JSON 파일을 반환 하는 끝점입니다.

  > [!Note]  
  > 합니다 **ResourceManagerUrl** 에 Azure Stack 개발 키트 ASDK ()는: `https://management.local.azurestack.external/`  
  > 합니다 **ResourceManagerUrl** 통합된 시스템의: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > 에 필요한 메타 데이터를 검색 합니다. `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  샘플 JSON 파일:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

  3. 사용할 수 없는 경우 구독을 만들고 나중에 사용할 구독 ID를 저장 합니다. 구독을 만드는 방법은 [Azure Stack에서 제품에 구독을 만들려면](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)합니다. 
  4. "구독" 범위를 사용 하 여 서비스 주체 만들기 및 **소유자** 역할입니다. ID 및 암호에는 서비스 주체를 저장 합니다. Azure Stack에 대 한 서비스 주체 만들기에 대 한 내용은 참조 하세요 [서비스 주체 만들기](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad)합니다. Azure Stack 환경과 설정 됩니다.
  5. 코드에서 Go SDK 프로필에서 서비스 모듈을 가져옵니다. Azure Stack 프로필의 현재 버전이 **2017-03-09**합니다. 예를 들어 네트워크 모듈에서 가져오려는 **2017-03-09** 프로필 유형: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. 함수에서 만들고 사용 하 여 클라이언트 인증을 **새로 만들기** 클라이언트 함수 호출 합니다. 가상 네트워크 클라이언트를 만들려면 다음 코드를 사용할 수 있습니다.  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  설정할 `<baseURI>` 에 **ResourceManagerUrl** 2 단계에서 사용 되는 값입니다.
  설정할 `<subscriptionID>` 에 **SubscriptionID** 3 단계에서 저장 된 값입니다.
  참조 인증 토큰을 만들려면 아래 섹션입니다.  

  7. 이전 단계에서 만든 클라이언트를 사용 하 여 API 메서드를 호출 합니다. 예를 들어, 이전 단계에서 클라이언트를 사용 하 여 가상 네트워크를 만들려면: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Azure Stack에서 Go SDK 프로필을 사용 하 여 가상 네트워크를 만드는 전체 예제를 참조 하세요 [예제에서는](#example)합니다.

## <a name="authentication"></a>인증

Go SDK를 사용 하 여 Azure Active Directory의 권한 부여자 속성을 가져오려면 Go AutoRest 모듈을 설치 합니다. 이러한 모듈은 이미 설치 된 "Go SDK" 설치; 그렇지 않은 경우 설치 합니다 [GitHub에서 인증 패키지가](https://github.com/Azure/go-autorest/tree/master/autorest/adal)합니다.

권한 부여 자의 리소스 클라이언트에 대 한 권한 부여자로 설정 되어야 합니다. 권한 부여자;를 가져오려면 여러 가지가 있습니다. 자세한 전체 목록은 참조에 여기 해당 합니다.

이 섹션에는 클라이언트 자격 증명을 사용 하 여 Azure Stack의 권한 부여자 토큰을 가져오도록 하는 일반적인 방법은 제공 합니다.

  1. 구독에 소유자 역할을 사용 하 여 서비스 주체를 사용할 수 있는 경우이 단계를 건너뜁니다. 그렇지 않으면 서비스 주체를 만듭니다 [지침]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) 구독에 범위가 지정 된 "소유자" 역할을 할당 하 고 [지침]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal)합니다. 서비스 주체 응용 프로그램 ID 및 암호를 저장 합니다. 

  2. 가져오기 **adal** 코드에서 Go AutoRest 패키지 있습니다. 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. oauthConfig NewOAuthConfig 메서드를 사용 하 여 만드는 **adal** 모듈입니다. 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  설정 `<activeDirectoryEndpoint>` "loginEndpoint"의 값으로 속성 ResourceManagerUrl 메타 데이터에서이 문서의 이전 섹션에서 검색 합니다.
  설정 `<tenantID>` Azure Stack 테 넌 트 ID 값 

  4. 마지막으로 adal 모듈에서 NewServicePrincipalToken 메서드를 사용 하 여 서비스 주체 토큰을 만듭니다. 

  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/adal" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  ````
  
  설정 `<activeDirectoryResourceID>` "대상"에 있는 값 중 하나에이 문서의 이전 섹션에서 ResourceManagerUrl 메타 데이터에서 목록을 검색 합니다.  
  설정 `<clientID>` 서비스 주체 응용 프로그램 ID이 문서의 이전 섹션에서 서비스 주체를 만들 때을 저장 합니다.  
  설정 `<clientSecret>` 서비스 주체 응용 프로그램 비밀 저장이 문서의 이전 섹션에서 서비스 주체를 만들 때.  

## <a name="example"></a>예

이 섹션에서는 가상 네트워크를 만들려면 Azure Stack에서 Go 코드 예제를 보여 줍니다. Go SDK의 전체 예제를 보려면 [Azure Go SDk 샘플 리포지토리](https://github.com/Azure-Samples/azure-sdk-for-go-samples)합니다. Azure Stack 샘플은 하이브리드에서 사용할 수 있는 / 저장소의 서비스 폴더 내의 경로입니다.

> [!Note]  
> 이 예제의 코드를 실행 하려면 사용 된 구독에 있는지 확인 하십시오 **네트워크** 로 나열 하는 리소스 공급자 **Registered**합니다. 이 확인 하려면 Azure Stack 포털에 구독에 대 한 확인을 클릭할 **리소스 공급자입니다.**

1. 코드에서 필요한 패키지를 가져옵니다. 네트워크 모듈을 가져오려면 Azure Stack에서 최신 사용 가능한 프로필을 사용 해야 합니다. 
  
  ````go
  package main

  import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
  )
  ````

2. 환경 변수를 정의 합니다. 가상 네트워크를 만들려는 리소스 그룹을 지정 해야 합니다. 

  ````go
  var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
      subscriptionID = "yourSubscriptionID"
      baseURI = "yourResourceManagerURL"
      resourceGroupName = "existingResourceGroupName"
  )
  ````

3. 환경 변수를 정의한 했으므로 사용 하 여 인증 토큰을 만드는 메서드를 추가 **adal** 패키지 있습니다. 이전 섹션에서 인증에 대 한 세부 정보를 참조 하세요.
  
  ````go
  //CreateToken creates a service principal token
  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  }
  ````

4. Main 메서드에 추가 합니다. Main 메서드는 먼저 이전 단계에서 정의 된 메서드를 사용 하 여 토큰을 가져옵니다. 그런 다음 프로필에서 네트워크 모듈을 사용 하 여 클라이언트를 만듭니다. 마지막으로, 가상 네트워크를 만듭니다. 
  
````go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
    "github.com/Azure/go-autorest/autorest"
    "github.com/Azure/go-autorest/autorest/adal"
    "github.com/Azure/go-autorest/autorest/to"
)

var (
    activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
    tenantID = "yourAzureStackTenantID"
    clientID = "yourServicePrincipalApplicationID"
    clientSecret = "yourServicePrincipalSecret"
    activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
    subscriptionID = "yourSubscriptionID"
    baseURI = "yourResourceManagerURL"
    resourceGroupName = "existingResourceGroupName"
)

//CreateToken creates a service principal token
func CreateToken() (adal.OAuthTokenProvider, error) {
    var token adal.OAuthTokenProvider
    oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        clientID,
        clientSecret,
        activeDirectoryResourceID)
    return token, err
}

func main() {
    token, _ := CreateToken()
    vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
    vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
    future, _ := vnetClient.CreateOrUpdate(
        context.Background(),
        resourceGroupName,
        "sampleVnetName",
        network.VirtualNetwork{
            Location: to.StringPtr("local"),
            VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                AddressSpace: &network.AddressSpace{
                    AddressPrefixes: &[]string{"10.0.0.0/8"},
                },
                Subnets: &[]network.Subnet{
                    {
                        Name: to.StringPtr("subnetName"),
                        SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                            AddressPrefix: to.StringPtr("10.0.0.0/16"),
                        },
                    },
                },
            },
        })
    err := future.WaitForCompletion(context.Background(), vnetClient.Client)
    if err != nil {
        fmt.Printf(err.Error())
        return
    }
}

````

## <a name="next-steps"></a>다음 단계
* [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)
* [Azure Stack 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)  
