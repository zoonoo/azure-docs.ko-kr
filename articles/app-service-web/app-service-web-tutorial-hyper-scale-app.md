---
title: "Azure에서 초대형 앱 빌드 | Microsoft Docs"
description: "Azure에서 ASP.NET 앱의 성능을 최대화하기 위해 서로 다른 Azure 서비스를 사용하는 방법에 알아봅니다."
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: erikre
editor: 
ms.assetid: a4d49ac7-0f97-4997-84c5-cdb9c4465757
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/23/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: eac9c5b0d8d0f7802d88e6f4f27d9d23c406e025
ms.contentlocale: ko-kr
ms.lasthandoff: 04/28/2017


---
# <a name="build-a-hyper-scale-web-app-in-azure"></a>Azure에서 초대형 웹앱 빌드

이 자습서에서는 사용자 요청을 최대화하기 위해 Azure에서 ASP.NET 웹앱의 규모를 확장하는 방법을 보여 줍니다.

이 자습서를 시작하기 전에 컴퓨터에 [Azure CLI가 설치되었는지](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) 확인합니다. 또한 샘플 응용 프로그램을 실행하려면 로컬 컴퓨터에 [Visual Studio](https://www.visualstudio.com/vs/)가 있어야 합니다.

## <a name="step-1---get-sample-application"></a>1단계 - 샘플 응용 프로그램 가져오기
이 단계에서 로컬 ASP.NET 프로젝트를 설정합니다.

### <a name="clone-the-application-repository"></a>응용 프로그램 리포지토리 복제

선택한 명령줄 터미널을 열고 작업 디렉터리로 `CD` 합니다. 그런 다음 샘플 응용 프로그램을 복제하기 위해 다음 명령을 실행합니다. 

```powershell
git clone https://github.com/cephalin/HighScaleApp.git
```

### <a name="run-the-sample-application-in-visual-studio"></a>Visual Studio에서 샘플 응용 프로그램 실행

Visual Studio에서 솔루션을 엽니다.

```powershell
cd HighScaleApp
.\HighScaleApp.sln
```

`F5`를 입력하여 응용 프로그램을 실행합니다.

이 샘플 ASP.NET 웹 응용 프로그램은 기본 템플릿에서 제공되며 사용자 세션을 유지하고 출력 캐시를 사용합니다. `HighScaleApp\Controllers\HomeController.cs`에 대해 살펴보겠습니다. `Index()` 메서드는 데이터 일부를 세션에 추가합니다.

```csharp
Session.Add("visited", "true"); 
```

또한 `About()` 및 `Contact()` 메서드는 해당 출력을 캐시합니다.

```csharp
[OutputCache(Duration = 60)]
```

## <a name="step-2---deploy-to-azure"></a>2단계 - Azure에 배포
이 단계에서는 Azure 웹앱 만들고 샘플 ASP.NET 응용 프로그램을 배포합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기   
[az 그룹 만들기](https://docs.microsoft.com/cli/azure/group#create)를 사용하여 유럽 서부 지역에 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 리소스 그룹은 웹앱 및 해당 SQL Database 백 엔드와 같이 함께 관리하고자 하는 모든 Azure 리소스를 배치하는 위치입니다.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

`---location`에 사용할 수 있는 가능한 값을 보려면 [az appservice list-locations](https://docs.microsoft.com/en-us/cli/azure/appservice#list-locations) 명령을 사용합니다.

### <a name="create-an-app-service-plan"></a>앱 서비스 계획 만들기
[az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create)를 사용하여 “B1” [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)을 만듭니다. 

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1
```

App Service 계획은 동일한 App Service 인프라를 통해 함께 강화 또는 규모 확장하고자 하는 많은 앱을 포함할 수 있는 배율 단위입니다. 또한 각 계획은 [가격 책정 계층](https://azure.microsoft.com/en-us/pricing/details/app-service/)에 할당되어 있습니다. 더 높은 계층은 더 많은 규모 확장 인스턴스와 같이 더 향상된 하드웨어와 더 많은 기능을 포함합니다.

이 자습서에서 B1은 세 개의 인스턴스로 규모 확장이 가능한 최소 계층입니다. 나중에 [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update)를 실행하여 앱을 가격 책정 계층 위아래로 이동할 수 있습니다. 

### <a name="create-a-web-app"></a>웹앱 만들기
[az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#create)를 사용하여 `$appName`에서 고유한 이름을 갖는 웹앱을 만듭니다.

```azurecli
$appName = "<replace-with-a-unique-name>"
az appservice web create --name $appName --resource-group myResourceGroup --plan myAppServicePlan
```

### <a name="set-deployment-credentials"></a>배포 자격 증명 설정
[az appservice web deployment user set](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/user#set)을 사용하여 App Service에 대한 계정 수준 배포 자격 증명을 설정합니다.

```azurecli
az appservice web deployment user set --user-name <letters-numbers> --password <mininum-8-char-captital-lowercase-letters-numbers>
```

### <a name="configure-git-deployment"></a>Git 배포 구성
[az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git)을 사용하여 로컬 Git 배포를 구성합니다.

```azurecli
az appservice web source-control config-local-git --name $appName --resource-group myResourceGroup
```

이 명령은 다음과 같은 출력을 제공합니다.

```json
{
  "url": "https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git"
}
```

반환된 URL을 사용하여 Git 원격을 구성합니다. 다음 명령은 앞의 출력 예제를 사용합니다.

```powershell
git remote add azure https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-the-sample-application"></a>샘플 응용 프로그램 배포
이제 샘플 응용 프로그램을 배포할 준비가 되었습니다. `git push`을 실행합니다.

```powershell
git push azure master
```

암호를 입력하라는 메시지가 표시되면 `az appservice web deployment user set`을 실행할 때 지정한 암호를 사용합니다.

### <a name="browse-to-azure-web-app"></a>Azure 웹앱 찾아보기
[az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse)를 사용하여 Azure에서 실시간으로 실행 중인 앱을 확인하려면 이 명령을 실행합니다.

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-3---connect-to-redis"></a>3단계 - Redis에 연결
이 단계에서는 Azure Redis Cache를 Azure 웹앱에 공동 배치된 외부 캐시로 설정합니다. Redis를 신속하게 활용하여 페이지 출력을 캐시할 수 있습니다. 또한 나중에 웹앱의 규모를 확장할 때 Redis를 사용하면 사용자 세션을 여러 인스턴스에 걸쳐 안정적으로 유지할 수 있습니다.

### <a name="create-an-azure-redis-cache"></a>Azure Redis Cache 만들기
[az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create)를 사용하여 Azure Redis Cache를 만들고 JSON 출력을 저장합니다. `$cacheName`에 고유한 이름을 사용합니다.

```powershell
$cacheName = "<replace-with-a-unique-cache-name>"
$redis = (az redis create --name $cacheName --resource-group myResourceGroup --location "West Europe" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

### <a name="configure-the-application-to-use-redis"></a>Redis를 사용하도록 응용 프로그램 구성
사용자 캐시에 연결 문자열의 서식을 적용합니다.

```powershell
$connstring = "$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False"
$connstring 
```

두 번째 줄은 다음과 같은 출력을 제공해야 합니다.

```
mycachename.redis.cache.windows.net:6380,password=/rQP/TLz1mrEPpmh9b/gnfns/t9vBRXqXn3i1RwBjGA=,ssl=True,abortConnect=False
```

Visual Studio에서 `redis.config`라는 프로젝트 루트에 웹 구성 파일을 만들고 다음 코드를 붙여넣습니다. `value`에서 PowerShell 출력의 연결 문자열을 사용합니다.

```xml
<appSettings>
  <add key="RedisConnection" value="your-azure-redis-cache-connection-string"/>
</appSettings>
```

Git 리포지토리에 `.gitignore` 파일이 있으면 이 파일이 원본 제어에서 제외되었음을 확인할 수 있습니다. 이런 방식으로 중요한 정보가 안전하게 유지됩니다. 

`Web.config`를 엽니다. `redis.config`에서 만든 설정을 가져오는 `<appSettings file="redis.config">` 요소에 주의하세요. 

`<sessionState>` 및 `<caching>`을 포함하는 주석 처리된 섹션을 찾습니다. 이 섹션의 주석 처리를 제거합니다.

![](./media/app-service-web-tutorial-hyper-scale-app/redisproviders.png)

이 코드는 `RedisConnection`에서 정의한 Redis 연결 문자열을 찾습니다. 

이제 응용 프로그램이 Redis를 사용하여 세션 및 캐싱을 관리합니다. `F5`를 입력하여 응용 프로그램을 실행합니다. 원하는 경우 Redis 관리 클라이언트를 다운로드하면 이제 캐시에 저장된 데이터를 시각화할 수 있습니다.

### <a name="configure-the-connection-string-in-azure"></a>Azure에서 연결 문자열 구성

Azure에서 작동하는 응용 프로그램의 경우 Azure 웹앱에서 동일한 Redis 연결 문자열을 구성해야 합니다. `redis.config`가 원본 제어에서 유지 관리되지 않으므로 Git 배포를 실행하는 경우 Azure에 배포되지 않습니다.

[az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update)를 사용하여 동일한 이름(`RedisConnection`)의 연결 문자열을 추가합니다.

az appservice web config appsettings update --settings "RedisConnection=$connstring" --name $appName --resource-group myResourceGroup

`$connstring`에는 서식이 지정된 연결 문자열이 포함되어 있음에 유의하세요.

### <a name="redeploy-the-application-to-azure"></a>Azure에 응용 프로그램을 다시 배포
Git 명령을 사용하여 변경 내용을 Azure에 푸시합니다.

```bash
git add .
git commit -m "now use Redis providers"
git push azure master
```

암호를 입력하라는 메시지가 표시되면 `az appservice web deployment user set`을 실행할 때 지정한 암호를 사용합니다.

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기
[az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse)를 사용하여 Azure에서 변경 내용을 실시간으로 확인합니다.

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-4---scale-to-multiple-instances"></a>4단계 - 여러 인스턴스로 크기 조정
App Service 계획은 Azure 웹앱에 대한 배율 단위입니다. 웹앱 규모를 확장하려면 App Service 계획의 크기를 조정합니다.

[az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update)를 사용하여 App Service 계획의 규모를 B1 가격 책정 계층에서 허용하는 최대 수인 세 개의 인스턴스로 확장합니다. B1은 이전에 App Service 계획을 만들 때 선택한 가격 책정 계층입니다. 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --number-of-workers 3 
```

## <a name="step-5---scale-geographically"></a>5단계 - 지리적으로 크기 조정
지리적으로 크기를 조정할 때 Azure 클라우드의 여러 하위 지역에서 앱을 실행합니다. 이 설정은 지리에 기반하여 추가로 앱 부하를 분산하며, 앱을 클라이언트 브라우저에 가까이 배치함으로써 반응 시간을 줄입니다.

이 단계에서 [Azure Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/)를 사용하여 ASP.NET 웹앱을 두 번째 하위 지역으로 확장합니다. 단계 마지막에는 유럽 서부에서 실행되는 웹앱(이미 생성함) 및 동남 아시아에서 실행되는 웹앱(아직 생성하지 않음)이 생기게 됩니다. 두 앱은 동일한 Traffic Manager URL에서 처리됩니다.

### <a name="scale-up-the-europe-app-to-standard-tier"></a>유럽 앱을 표준 계층으로 강화
App Service에서 Azure Traffic Manager와 통합하려면 표준 가격 책정 계층이 필요합니다. [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update)를 사용하여 App Service 계획을 S1로 강화합니다. 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --sku S1
```
### <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기 
[az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#create)를 사용하여 Traffic Manager 프로필을 만들고 리소스 그룹에 추가합니다. $dnsName에 고유한 DNS 이름을 사용합니다.

```azurecli
$dnsName = "<replace-with-unique-dns-name>"
az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name $dnsName
```

> [!NOTE]
> `--routing-method Performance`는 이 프로필이 [사용자 트래픽을 가장 가까운 끝점으로 라우팅](../traffic-manager/traffic-manager-routing-methods.md)하도록 지정합니다.

### <a name="get-the-resource-id-of-the-europe-app"></a>유럽 앱의 리소스 ID 가져오기
[az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#show)를 사용하여 웹앱의 리소스 ID를 가져옵니다.

```azurecli
$appId = az appservice web show --name $appName --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-europe-app"></a>유럽 앱에 대한 Traffic Manager 끝점 추가
[az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#create)를 사용하여 끝점을 Traffic Manager 프로필에 추가하고 웹앱의 리소스 ID를 대상으로 사용합니다.

```azurecli
az network traffic-manager endpoint create --name myWestEuropeEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appId
```

### <a name="get-the-traffic-manager-endpoint-url"></a>Traffic Manager 끝점 URL 가져오기
이제 Traffic Manager 프로필에 기존 웹앱을 가리키는 끝점이 있습니다. [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#show)를 사용하여 해당 URL을 가져옵니다. 

```azurecli
az network traffic-manager profile show --name myTrafficManagerProfile --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
```

브라우저에 출력을 복사합니다. 웹앱을 다시 확인해야 합니다.

### <a name="create-an-azure-redis-cache-in-asia"></a>아시아에 Azure Redis Cache 만들기
이제 Azure 웹앱을 동남 아시아 지역으로 복제합니다. 시작하려면 [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create)를 사용하여 동남 아시아에 두 번째는 Azure Redis Cache를 만듭니다. 이 캐시는 아시아의 앱과 공동 배치해야 합니다.

```powershell
$redis = (az redis create --name $cacheName-asia --resource-group myResourceGroup --location "Southeast Asia" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

`--name $cacheName-asia`는 캐시에 `-asia` 접미사가 있는 유럽 서부 캐시의 이름을 부여합니다.

### <a name="create-an-app-service-plan-in-asia"></a>아시아에 App Service 계획 만들기
[az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create)를 사용하여 동남 아시아 지역에 유럽 서부 계획과 동일한 S1 계층을 사용하는 두 번째 App Service 계획을 만듭니다.

```azurecli
az appservice plan create --name myAppServicePlanAsia --resource-group myResourceGroup --location "Southeast Asia" --sku S1
```

### <a name="create-a-web-app-in-asia"></a>아시아에 웹앱 만들기
[az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#create)를 사용하여 두 번째 웹앱을 만듭니다.

```azurecli
az appservice web create --name $appName-asia --resource-group myResourceGroup --plan myAppServicePlanAsia
```

`--name $appName-asia`는 앱에 `-asia` 접미사가 있는 유럽 서부 앱의 이름을 부여합니다.

### <a name="configure-the-connection-string-for-redis"></a>Redis에 대한 연결 문자열 구성
[az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update)를 사용하여 웹앱에 동남 아시아 캐시에 대한 연결 문자열을 추가합니다.

az appservice web config appsettings update --settings "RedisConnection=$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False" --name $appName-asia --resource-group myResourceGroup

### <a name="configure-git-deployment-for-the-asia-app"></a>아시아 앱에 대한 Git 배포를 구성합니다.
[az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git)을 사용하여 두 번째 웹앱에 대한 로컬 Git 배포를 구성합니다.

```azurecli
az appservice web source-control config-local-git --name $appName-asia --resource-group myResourceGroup
```

이 명령은 다음과 같은 출력을 제공합니다.

```json
{
  "url": "https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git"
}
```

반환된 URL을 사용하여 로컬 리포지토리에 대한 두 번째 Git 원격을 구성합니다. 다음 명령은 앞의 출력 예제를 사용합니다.

```bash
git remote add azure-asia https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-your-sample-application"></a>샘플 응용 프로그램 배포
`git push`를 실행하여 샘플 응용 프로그램을 두 번째 Git 원격에 배포합니다. 

```bash
git push azure-asia master
```

암호를 입력하라는 메시지가 표시되면 `az appservice web deployment user set`을 실행할 때 지정한 암호를 사용합니다.

### <a name="browse-to-the-asia-app"></a>아시아 앱 찾아보기
[az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse)를 사용하여 Azure에서 실시간으로 실행 중인 앱을 확인합니다.

```azurecli
az appservice web browse --name $appName-asia --resource-group myResourceGroup
```

### <a name="get-the-resource-id-of-the-asia-app"></a>아시아 앱의 리소스 ID 가져오기
[az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#show)를 사용하여 동남 아시아의 웹앱 리소스 ID를 가져옵니다.

```azurecli
$appIdAsia = az appservice web show --name $appName-asia --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-asia-app"></a>아시아 앱에 대한 Traffic Manager 끝점 추가
[az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#create)를 사용하여 두 번째 끝점을 Traffic Manager 프로필에 추가합니다.

```azurecli
az network traffic-manager endpoint create --name myAsiaEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appIdAsia
```

### <a name="add-region-identifier-to-web-apps"></a>웹앱에 하위 지역 식별자 추가
[az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update)를 사용하여 지역별 환경 변수를 추가합니다.

```azurecli
az appservice web config appsettings update --settings "Region=West Europe" --name $appName --resource-group myResourceGroup
az appservice web config appsettings update --settings "Region=Southeast Asia" --name $appName-asia --resource-group myResourceGroup
```

응용 프로그램 코드는 이미 이 응용 프로그램 설정을 사용합니다. `HighScaleApp\Views\Home\Index.cshtml`에 대해 살펴보겠습니다.

### <a name="complete"></a>완료!

이제 다른 지리적 하위 지역의 브라우저에서 Traffic Manager 프로필의 URL에 액세스를 시도합니다. 유럽의 클라이언트 브라우저는 “ASP.NET West Europe”, 아시아의 클라이언트 브라우저는 “ASP.NET Southeast Asia”가 표시되어야 합니다.

## <a name="more-resources"></a>추가 리소스

