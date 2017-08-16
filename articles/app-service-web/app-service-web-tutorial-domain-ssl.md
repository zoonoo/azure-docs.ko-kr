---
title: "Azure 웹앱에 사용자 지정 도메인 및 SSL 추가 | Microsoft Docs"
description: "회사 브랜딩을 추가하여 프로덕션으로 전환하도록 Azure 웹앱을 준비하는 방법에 알아봅니다. 사용자 지정 도메인 이름(베니티 도메인)을 사용자 웹앱에 매핑하고 사용자 지정 SSL 인증서로 안전하게 유지합니다."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 03/29/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 535a7dbe24a1badc8539b61d34c09bdeda41ad40
ms.contentlocale: ko-kr
ms.lasthandoff: 05/25/2017

---
# <a name="add-custom-domain-and-ssl-to-an-azure-web-app"></a>Azure 웹앱에 사용자 지정 도메인 및 SSL 추가

이 자습서에서는 신속하게 사용자 지정 도메인 이름을 Azure 웹앱에 매핑한 다음 사용자 지정 SSL 인증서로 보호하는 방법을 설명합니다. 

## <a name="before-you-begin"></a>시작하기 전에

이 샘플을 실행하기 전에 [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)을 로컬로 설치합니다.

또한 각 도메인 공급자에 대한 DNS 구성 페이지의 관리자 액세스가 필요합니다. 예를 들어 `www.contoso.com`을 추가하려면 `contoso.com`에 대한 DNS 항목을 구성할 수 있어야 합니다.

마지막으로 업로드하고 바인딩하려는 SSL 인증서에 사용할 .PFX 파일 _및_ 해당 암호가 유효해야 합니다. 원하는 사용자 지정 도메인 이름을 보호하도록 이 SSL 인증서를 구성해야 합니다. 위의 예제에서 SSL 인증서는 `www.contoso.com`을 보호합니다. 

## <a name="step-1---create-an-azure-web-app"></a>1단계 - Azure 웹앱 만들기

### <a name="log-in-to-azure"></a>Azure에 로그인

이제 터미널 창에서 Azure CLI 2.0을 사용하여 Azure에서 Node.js 앱을 호스팅하는 데 필요한 리소스를 만들 예정입니다.  [az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>리소스 그룹 만들기   
[az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 웹앱, 데이터베이스, 저장소 계정이 관리되었는지 등 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

```azurecli
az group create --name myResourceGroup --location westeurope 
```

`---location`에 사용할 수 있는 가능한 값을 보려면 `az appservice list-locations` Azure CLI 명령을 사용합니다.

## <a name="create-an-app-service-plan"></a>App Service 계획 만들기

[az appservice plan create](/cli/azure/appservice/plan#create) 명령으로 App Service 계획을 만듭니다. 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

다음 예제에서는 **기본** 가격 책정 계층을 사용하는 `myAppServicePlan`이라는 App Service 계획을 만듭니다.

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1

App Service 계획을 만들었으면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. 

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 1, 
    "family": "B", 
    "name": "B1", 
    "tier": "Basic" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

## <a name="create-a-web-app"></a>웹앱 만들기

이제 App Service 계획을 만들었으므로 `myAppServicePlan` App Service 계획 내에서 웹앱을 만듭니다. 웹앱은 코드를 배포할 호스팅 공간을 제공할 뿐만 아니라 배포된 응용 프로그램을 확인할 수 있도록 URL도 제공합니다. [az appservice web create](/cli/azure/appservice/web#create) 명령을 사용하여 웹앱을 만듭니다. 

아래 명령에서 `<app_name>` 자리 표시자를 확인한 고유한 앱 이름을 대체하세요. 이 고유한 이름은 웹앱에 대한 기본 도메인 이름의 일부로 사용되므로 이름은 Azure에 있는 모든 앱에서 고유해야 합니다. 나중에 사용자에게 노출하기 전에 웹앱에 사용자 지정 DNS 항목을 매핑할 수 있습니다. 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan 
```

웹앱을 만들었으면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

JSON 출력에서 `defaultHostName`은 웹앱의 기본 도메인 이름을 표시합니다. 브라우저에서 이 주소로 이동합니다.

```
http://<app_name>.azurewebsites.net 
``` 
 
![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-created.png)  

## <a name="step-2---configure-dns-mapping"></a>2단계 - DNS 매핑 구성

이 단계에서는 사용자 지정 도메인의 매핑을 사용자 웹앱의 기본 도메인 이름인 `<app_name>.azurewebsites.net`에 추가합니다. 일반적으로 도메인 공급자의 웹 사이트에서 이 단계를 수행 합니다. 각 도메인 등록 기관의 웹 사이트는 약간씩 다르므로 공급자의 설명서를 참조해야 합니다. 하지만 다음은 몇 가지 일반적인 지침입니다. 

### <a name="navigate-to-to-dns-management-page"></a>DNS 관리 페이지로 이동

먼저 도메인 등록 기관의 웹 사이트에 로그인합니다.  

그런 다음 DNS 레코드를 관리하기 위한 페이지를 찾습니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리** 레이블이 지정된 사이트의 링크 또는 영역을 찾습니다. 계정 정보를 확인한 다음 **내 도메인**과 같은 링크를 검색하여 링크를 찾을 수 있습니다.

이 페이지를 찾았으면 DNS 레코드를 추가하거나 편집할 수 있는 링크를 찾습니다. 이는 **영역 파일** 또는 **DNS 레코드** 링크이거나 **고급 구성** 링크일 수 있습니다.

### <a name="create-a-cname-record"></a>CNAME 레코드 만들기

원하는 하위 도메인 이름을 웹앱의 기본 도메인 이름(`<app_name>.azurewebsites.net`, 여기서 `<app_name>`은 앱의 고유 이름)에 매핑하는 CNAME 레코드를 추가합니다.

`www.contoso.com` 예제의 경우 `www` 호스트 이름을 `<app_name>.azurewebsites.net`에 매핑하는 CNAME을 만듭니다.

## <a name="step-3---configure-the-custom-domain-on-your-web-app"></a>3단계 - 웹앱에서 사용자 지정 도메인 구성

도메인 공급자의 웹 사이트에서 호스트 이름 매핑의 구성을 마쳤다면 웹앱에서 사용자 지정 도메인을 구성할 준비가 된 것입니다. [az appservice web config hostname add](/cli/azure/appservice/web/config/hostname#add) 명령을 사용하여 이 구성을 추가합니다. 

아래 명령에서 `<app_name>`을 고유한 앱 이름으로, <your_custom_domain>을 정규화된 사용자 지정 도메인 이름(예: `www.contoso.com`)으로 대체합니다. 

```azurecli
az appservice web config hostname add --webapp <app_name> --resource-group myResourceGroup --name <your_custom_domain>
```

이제 사용자 지정 도메인은 완전하게 사용자 웹앱에 매핑되었습니다. 브라우저에서 사용자 지정 도메인 이름으로 이동합니다. 예:

```
http://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-custom-domain.png)  

## <a name="step-4---bind-a-custom-ssl-certificate-to-your-web-app"></a>4단계 - 웹앱에 사용자 지정 SSL 인증서 바인딩

이제 브라우저의 주소 표시줄에 원하는 도메인 이름을 사용하는 Azure 웹앱이 있습니다. 하지만 `https://<your_custom_domain>`으로 이동하는 경우 인증서 오류가 발생합니다. 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-cert-error.png)  

이 오류는 웹앱에 사용자 지정 도메인 이름과 일치하는 SSL 인증서 바인딩 아직 없기 때문에 발생합니다. 하지만 `https://<app_name>.azurewebsites.net`으로 이동하면 오류가 발생하지 않습니다. 즉, 사용자의 앱 뿐 아니라 모든 Azure App Service 앱은 기본적으로 `*.azurewebsites.net` 와일드카드 도메인에 대한 SSL 인증서로 보호되기 때문입니다. 

사용자 지정 도메인 이름으로 웹앱에 액세스하려면 웹앱에 사용자 지정 도메인에 대한 SSL 인증서를 바인딩해야 합니다. 바로 이 단계에서 수행합니다. 

### <a name="upload-the-ssl-certificate"></a>SSL 인증서 업로드

[az appservice web config ssl upload](/cli/azure/appservice/web/config/ssl#upload) 명령을 사용하여 사용자 지정 도메인에 대한 SSL 인증서를 웹앱에 업로드합니다.

아래 명령에서 `<app_name>`을 고유한 앱 이름으로, `<path_to_ptx_file>`을 .PFX 파일에 대한 경로로, `<password>`를 인증서의 암호로 대체합니다. 

```azurecli
az appservice web config ssl upload --name <app_name> --resource-group myResourceGroup --certificate-file <path_to_pfx_file> --certificate-password <password> 
```

인증서를 업로드하면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "cerBlob": null,
  "expirationDate": "2018-03-29T14:12:57+00:00",
  "friendlyName": "",
  "hostNames": [
    "www.contoso.com"
  ],
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/cert
ificates/9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "issueDate": "2017-03-29T14:12:57+00:00",
  "issuer": "www.contoso.com",
  "keyVaultId": null,
  "keyVaultSecretName": null,
  "keyVaultSecretStatus": "Initialized",
  "kind": null,
  "location": "West Europe",
  "name": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "password": null,
 "pfxBlob": null,
  "publicKeyHash": null,
  "resourceGroup": "myResourceGroup",
  "selfLink": null,
  "serverFarmId": null,
  "siteName": null,
  "subjectName": "www.contoso.com",
  "tags": null,
  "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00",
  "type": "Microsoft.Web/certificates",
  "valid": null
}
```

JSON 출력에서 `thumbprint`는 업로드된 인증서의 지문을 표시합니다. 다음 단계를 위해 해당 값을 복사합니다.

### <a name="bind-the-uploaded-ssl-certificate-to-the-web-app"></a>업로드한 SSL 인증서를 웹앱에 바인딩

이제 웹앱에 원하는 사용자 지정 도메인 이름과 해당 사용자 지정 도메인을 보호하는 SSL 인증서가 있습니다. 이제 남은 것은 업로드된 인증서를 웹앱에 바인딩하는 것입니다. [az appservice web config ssl bind](/cli/azure/appservice/web/config/ssl#bind) 명령을 사용하여 이를 수행합니다.

아래 명령에서 `<app_name>`을 고유한 앱 이름으로, `<thumbprint-from-previous-output>`을 이전 명령에서 가져온 인증서 지문으로 대체합니다. 

az appservice web config ssl bind --name <app_name> --resource-group myResourceGroup --certificate-thumbprint <thumbprint-from-previous-output> --ssl-type SNI

인증서를 웹앱에 바인딩하면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

{ "availabilityState": "Normal", "clientAffinityEnabled": true, "clientCertEnabled": false, "cloningInfo": null, "containerSize": 0, "dailyMemoryTimeQuota": 0, "defaultHostName": "<app_name>.azurewebsites.net", "enabled": true, "enabledHostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net", "<app_name>.scm.azurewebsites.net" ], "gatewaySiteName": null, "hostNameSslStates": [ { "hostType": "Standard", "name": "<app_name>.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Repository", "name": "<app_name>.scm.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Standard", "name": "www.contoso.com", "sslState": "SniEnabled", "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00", "toUpdate": null, "virtualIp": null } ], "hostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net" ], "hostNamesDisabled": false, "hostingEnvironmentProfile": null, "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/site s/<app_name>", "isDefaultContainer": null, "kind": "WebApp", "lastModifiedTimeUtc": "2017-03-29T14:36:18.803333", "location": "West Europe", "maxNumberOfWorkers": null, "microService": "false", "name": "<app_name>", "outboundIpAddresses": "13.94.143.57,13.94.136.57,40.68.199.146,13.94.138.55,13.94.140.1", "premiumAppDeployed": null, "repositorySiteName": "<app_name>", "reserved": false, "resourceGroup": "myResourceGroup", "scmSiteAlsoStopped": false, "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsof t.Web/serverfarms/myAppServicePlan", "siteConfig": null, "slotSwapStatus": null, "state": "Running", "suspendedTill": null, "tags": null, "targetSwapSlot": null, "trafficManagerHostNames": null, "type": "Microsoft.Web/sites", "usageState": "Normal" }

브라우저에서 사용자 지정 도메인 이름의 HTTPS 끝점으로 이동합니다. 예:

```
https://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-ssl-success.png)  

## <a name="more-resources"></a>추가 리소스

[Azure App Service에서 사용자 지정 도메인 이름 구입 및 구성](custom-dns-web-site-buydomains-web-app.md)
[Azure App Service에 대한 SSL 인증서 구입 및 구성](web-sites-purchase-ssl-web-site.md)

