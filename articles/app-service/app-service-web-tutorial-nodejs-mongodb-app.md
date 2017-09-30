---
title: "Azure에서 Node.js 및 MongoDB 웹앱 작성 | Microsoft Docs"
description: "Azure에서 작동하며 MongoDB 연결 문자열로 Cosmos 데이터베이스에 연결되는 Node.js 응용 프로그램을 만드는 방법을 알아봅니다."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: d5b41faa28d9181b957ce346e25804e16d9303d3
ms.contentlocale: ko-kr
ms.lasthandoff: 09/20/2017

---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Azure에서 Node.js 및 MongoDB 웹앱 작성

Azure Web Apps는 확장성 있는 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 Azure에서 Node.js 웹앱을 만들고 MongoDB 데이터베이스에 연결하는 방법을 보여 줍니다. 완료되면 MEAN 응용 프로그램(MongoDB, Express, AngularJS 및 Node.js)이 [Azure App Service](app-service-web-overview.md)에서 실행됩니다. 간편하게 하기 위해 샘플 응용 프로그램은 [MEAN.js 웹 프레임워크](http://meanjs.org/)를 사용합니다.

![Azure App Service에서 실행 중인 MEAN.js 응용 프로그램](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

학습할 내용:

> [!div class="checklist"]
> * Azure에서 MongoDB 데이터베이스 만들기
> * Node.js 앱을 MongoDB에 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

1. [Git 설치](https://git-scm.com/)
1. [Node.js 및 NPM 설치](https://nodejs.org/)
1. [Gulp.js 설치](http://gulpjs.com/)([MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started)에 필요)
1. [MongoDB Community Edition 설치 및 실행](https://docs.mongodb.com/manual/administration/install-community/) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb"></a>로컬 MongoDB 테스트

터미널 창을 열고 `cd`를 사용하여 MongoDB 설치 위치의 `bin` 디렉터리로 이동합니다. 이 터미널 창을 사용하여 이 자습서의 모든 명령을 실행할 수 있습니다.

터미널에서 `mongo`를 실행하여 로컬 MongoDB 서버에 연결합니다.

```bash
mongo
```

연결이 성공한다면 MongoDB 데이터베이스가 이미 실행 중입니다. 그렇지 않으면 [MongoDB Community Edition 설치](https://docs.mongodb.com/manual/administration/install-community/)의 단계에 따라 로컬 MongoDB 데이터베이스가 시작되었는지 확인합니다. MongoDB가 주로 설치되어 있지만 `mongod`를 실행하여 시작해야 합니다. 

MongoDB 데이터베이스를 테스트했으면 터미널에서 `Ctrl+C`를 입력합니다. 

## <a name="create-local-nodejs-app"></a>로컬 Node.js 앱 만들기

이 단계에서는 로컬 Node.js 프로젝트를 설정합니다.

### <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

터미널 창에서 `cd`를 사용하여 작업 디렉터리로 이동합니다.  

다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

이 샘플 리포지토리에는 [MEAN.js 리포지토리](https://github.com/meanjs/mean) 복사본이 들어 있습니다. App Service에서 실행하도록 수정되었습니다(자세한 내용은 MEAN.js 리포지토리의 [README(추가 정보) 파일](https://github.com/Azure-Samples/meanjs/blob/master/README.md) 참조).

### <a name="run-the-application"></a>응용 프로그램 실행

다음 명령을 실행하여 필요한 패키지를 설치하고 응용 프로그램을 시작합니다.

```bash
cd meanjs
npm install
npm start
```

앱이 완전히 로드되면 다음과 비슷한 메시지가 표시됩니다.

```
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

브라우저에서 `http://localhost:3000`으로 이동합니다. 위쪽 메뉴에서 **등록**을 클릭하고 테스트 사용자를 만듭니다. 

MEAN.js 샘플 응용 프로그램은 데이터베이스에 사용자 데이터를 저장합니다. 사용자 만들기와 로그인에 성공하면 앱에서 로컬 MongoDB 데이터베이스에 데이터를 쓰고 있습니다.

![MEAN.js가 MongoDB 연결에 성공](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

**관리자 > 문서 관리**를 선택하여 문서를 추가합니다.

언제든지 Node.js를 중지하려면 터미널에서 `Ctrl+C`를 입력합니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>프로덕션 MongoDB 만들기

이 단계에서는 Azure에 MongoDB 데이터베이스를 만듭니다. Azure에 앱을 배포하면 이 클라우드 데이터베이스가 사용됩니다.

MongoDB의 경우 이 자습서에서는 [Azure Cosmos DB](/azure/documentdb/)를 사용합니다. Cosmos DB는 MongoDB 클라이언트 연결을 지원합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다.

[!INCLUDE [Resource group intro](../../includes/resource-group.md)]

다음 예제에서는 유럽 서부 지역의 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

[az appservice list-locations](/cli/azure/appservice#list-locations) Azure CLI 명령을 사용하여 사용할 수 있는 위치를 나열합니다. 

### <a name="create-a-cosmos-db-account"></a>Cosmos DB 계정 만들기

Cloud Shell에서 [az cosmosdb create](/cli/azure/cosmosdb#create) 명령을 사용하여 Cosmos DB 계정을 만듭니다.

다음 명령에서 *\<cosmosdb_name>* 자리 표시자를 대신하여 고유한 Cosmos DB 이름으로 바꿉니다. 이 이름은 Cosmos DB 끝점(`https://<cosmosdb_name>.documents.azure.com/`)의 일부로 사용되므로 Azure의 모든 Cosmos DB 계정에서 고유해야 합니다. 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-50자 사이여야 합니다.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

*--kind MongoDB* 매개 변수는 MongoDB 클라이언트 연결을 사용하도록 설정합니다.

Cosmos DB 계정을 만든 경우 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>프로덕션 MongoDB에 앱 연결

이 단계에서는 MongoDB 연결 문자열을 사용하여 MEAN.js 샘플 응용 프로그램을 방금 만든 Cosmos DB 데이터베이스에 연결합니다. 

### <a name="retrieve-the-database-key"></a>데이터베이스 키 검색

Cosmos DB 데이터베이스에 연결하려면 데이터베이스 키가 필요합니다. Cloud Shell에서 [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys) 명령을 사용하여 기본 키를 검색합니다.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

`primaryMasterKey`의 값을 복사합니다. 이 정보는 다음 단계에서 필요합니다.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Node.js 응용 프로그램에 연결 문자열 구성

MEAN.js 리포지토리에서 _config/env/production.js_를 엽니다.

`db` 개체에서 `uri`의 값을 업데이트합니다.

* 두 개의 *\<cosmosdb_name>* 자리 표시자를 Cosmos DB 데이터베이스 이름으로 바꿉니다.
* *\<primary_master_key>* 자리 표시자를 이전 단계에서 복사한 키로 바꿉니다.

다음은 `db` 개체를 보여 주는 코드입니다.

```javascript
db: {
  uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
  ...
},
```

[Cosmos DB에서 SSL을 요구](../cosmos-db/connect-mongodb-account.md#connection-string-requirements)하므로 `ssl=true` 옵션이 필요합니다. 

변경 내용을 저장합니다.

### <a name="test-the-application-in-production-mode"></a>프로덕션 모드에서 응용 프로그램 테스트 

다음 명령을 실행하여 프로덕션 환경에 대한 스크립트를 축소하고 패키지합니다. 이 프로세스는 프로덕션 환경에 필요한 파일을 생성합니다.

```bash
gulp prod
```

다음 명령을 실행하여 _config/env/production.js_에서 구성한 연결 문자열을 사용합니다.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production`은 프로덕션 환경에서 실행되도록 Node.js에 지시하는 환경 변수를 설정합니다.  `node server.js`는 리포지토리 루트의 `server.js`로 Node.js 서버를 시작합니다. 이 방법으로 Node.js 응용 프로그램을 Azure에 로드합니다. 

앱이 로드되면 프로덕션 환경에서 실행 중인지 확인해야 합니다.

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

브라우저에서 `http://localhost:8443`으로 이동합니다. 위쪽 메뉴에서 **등록**을 클릭하고 테스트 사용자를 만듭니다. 사용자 만들기와 로그인에 성공하면 앱에서 Azure의 Cosmos DB 데이터베이스에 데이터를 쓰고 있습니다. 

터미널에서 `Ctrl+C`를 입력하여 Node.js를 중지합니다. 

## <a name="deploy-app-to-azure"></a>Azure에 앱 배포

이 단계에서는 MongoDB에 연결된 Node.js 응용 프로그램을 Azure App Service에 배포합니다.

### <a name="create-an-app-service-plan"></a>App Service 계획 만들기

Cloud Shell에서 [az appservice plan create](/cli/azure/appservice/plan#create) 명령으로 App Service 계획을 만듭니다. 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

다음 예제에서는 **무료** 가격 책정 계층을 사용하여 _myAppServicePlan_이라는 App Service 계획을 만듭니다.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

App Service 계획을 만들면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
```

### <a name="create-a-web-app"></a>웹앱 만들기

Cloud Shell에서 [az webapp create](/cli/azure/webapp#create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 웹앱을 만듭니다. 

웹앱은 코드를 배포할 호스팅 공간을 제공하고, 배포된 응용 프로그램을 확인할 수 있도록 URL도 제공합니다. 웹앱을 만드는 데 사용합니다. 

다음 명령에서 *\<app_name>* 자리 표시자를 고유한 앱 이름으로 바꿉니다. 이 이름은 웹앱에 대한 URL의 일부로 사용되므로 Azure App Service의 모든 앱에서 고유해야 합니다. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

웹앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다. 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-an-environment-variable"></a>환경 변수 구성

자습서의 앞부분에서는 _config/env/production.js_에 데이터베이스 연결 문자열을 하드코딩했습니다. 최상의 보안을 유지하려면 이 중요한 데이터를 Git 리포지토리 외부에 두는 것이 좋습니다. Azure에서 실행되는 응용 프로그램의 경우는 대신 환경 변수를 사용합니다.

Cloud Shell에서 [az webapp config appsettings update](/cli/azure/webapp/config/appsettings#update) 명령을 사용하여 환경 변수를 _앱 설정_으로 지정합니다. 

다음 예제에서는 Azure 웹앱에 `MONGODB_URI` 앱 설정을 구성합니다. *\<app_name>*, *\<cosmosdb_name>* 및 *\<primary_master_key>* 자리 표시자를 바꿉니다.

```azurecli-interactive
az webapp config appsettings update --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

Node.js 코드에서는 다른 환경 변수에 액세스할 때와 마찬가지로 `process.env.MONGODB_URI`를 사용하여 이 응용 프로그램 설정에 액세스합니다. 

이제 다음 명령을 사용하여 _config/env/production.js_의 변경 내용을 실행 취소합니다.

```bash
git checkout -- .
```

_config/env/production.js_를 다시 엽니다. 기본 MEAN.js 앱은 만든 `MONGODB_URI` 환경 변수를 사용하도록 이미 구성되어 있습니다.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="configure-local-git-deployment"></a>로컬 Git 배포 구성 

Cloud Shell에서 [az webapp deployment user set](/cli/azure/webapp/deployment/user#set) 명령을 사용하여 배포에 대한 자격 증명을 만듭니다.

FTP, 로컬 Git, GitHub, Visual Studio Team Services 및 BitBucket과 같은 다양한 방법으로 응용 프로그램을 Azure App Service에 배포할 수 있습니다. FTP 및 로컬 Git의 경우 배포에 인증하기 위해 배포 사용자를 서버에 구성할 필요가 있습니다. 이 배포 사용자는 계정 수준이며 Azure 구독 계정과 다릅니다. 이 배포 사용자는 한 번만 구성하면 됩니다.

다음 명령에서 *\<사용자 이름>* 및 *\<암호>*를 새 사용자 이름 및 암호로 바꿉니다. 사용자 이름은 고유해야 합니다. 암호는 글자, 숫자, 기호와 같은 세 가지 요소 중 두 가지를 포함하여 8자 이상이어야 합니다. ` 'Conflict'. Details: 409` 오류가 발생하면 사용자 이름을 변경합니다. ` 'Bad Request'. Details: 400` 오류가 발생하면 더 강력한 암호를 사용합니다.

```azurecli-interactive
az appservice web deployment user set --user-name <username> --password <password>
```

앱을 배포할 때 이후 단계에서 사용할 사용자 이름과 암호를 기록합니다.

[az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git) 명령을 사용하여 Azure 웹앱에 대한 로컬 Git 액세스를 구성합니다. 

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup
```

배포 사용자가 구성되면 Azure CLI에 다음과 같은 형식으로 Azure 웹앱의 배포 URL이 표시됩니다.

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

다음 단계에서 사용되므로 터미널의 출력을 복사합니다. 

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

로컬 터미널 창에서 로컬 Git 리포지토리에 Azure 원격을 추가합니다. 

```bash
git remote add azure <paste_copied_url_here> 
```

Azure에 원격으로 푸시하여 Node.js 응용 프로그램을 배포합니다. 배포 사용자를 만드는 작업의 일부로 이전에 입력한 암호를 묻는 메시지가 나타납니다. 

```bash
git push azure master
```

배포하는 동안 Azure App Service는 진행 상황을 Git에 전합니다.

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

배포 프로세스에서 `npm install` 후에 [Gulp](http://gulpjs.com/)를 실행하는 것을 알 수 있습니다. App Service는 배포 중에 Gulp 또는 Grunt 작업을 실행하지 않으므로 이 샘플 리포지토리는 사용 설정에 사용되는 추가 파일 두 개가 루트 디렉터리에 들어 있습니다. 

- _.deployment_ - 이 파일은 App Service에서 `bash deploy.sh`를 사용자 지정 배포 스크립트로 실행하게 만듭니다.
- _deploy.sh_ - 사용자 지정 배포 스크립트입니다. 파일을 검토 하는 경우 실행 되도록 표시 됩니다 `gulp prod` 후 `npm install` 및 `bower install`합니다. 

이 방식으로 Git 기반 배포에 어떤 단계든 추가할 수 있습니다. 언제든지 Azure Web App을 다시 시작하면 App Service에서 이 자동화 작업을 다시 실행하지 않습니다.

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기 

웹 브라우저를 사용하여 배포된 웹앱으로 이동합니다. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

위쪽 메뉴에서 **등록**을 클릭하고 더미 사용자를 만듭니다. 

앱에서 성공적으로 만든 사용자로 자동 로그인하면 Azure의 MEAN.js 앱이 MongoDB(Cosmos DB) 데이터베이스에 연결된 것입니다. 

![Azure App Service에서 실행 중인 MEAN.js 응용 프로그램](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

**관리자 > 문서 관리**를 선택하여 문서를 추가합니다. 

**축하합니다.** Azure App Service에서 데이터 기반 Node.js 응용 프로그램을 실행합니다.

## <a name="update-data-model-and-redeploy"></a>데이터 모델 업데이트 및 다시 배포

이 단계에서는 `article` 데이터 모델을 변경하고 변경 내용을 Azure에 게시합니다.

### <a name="update-the-data-model"></a>데이터 모델 업데이트

_modules/articles/server/models/article.server.model.js_를 엽니다.

`ArticleSchema`에서 `comment`라는 `String` 형식을 추가합니다. 완료된 후의 스키마 코드는 다음과 유사합니다.

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>문서 코드 업데이트

`comment`를 사용하도록 `articles` 코드의 나머지 부분을 업데이트합니다.

수정해야 하는 5개의 파일, 즉 서버 컨트롤러 하나와 클라이언트 보기 네 개가 있습니다. 

_modules/articles/server/controllers/articles.server.controller.js_를 엽니다.

`update` 함수에 `article.comment`의 할당을 추가합니다. 다음 코드에서는 완성된 `update` 함수를 보여 줍니다.

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

_modules/articles/client/views/view-article.client.view.html_을 엽니다.

닫는 `</section>` 태그 바로 위에 다음 줄을 추가하여 `comment`를 나머지 문서 데이터와 함께 표시합니다.

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

_modules/articles/client/views/list-articles.client.view.html_을 엽니다.

닫는 `</a>` 태그 바로 위에 다음 줄을 추가하여 `comment`를 나머지 문서 데이터와 함께 표시합니다.

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

_modules/articles/client/views/admin/list-articles.client.view.html_을 엽니다.

`<div class="list-group">` 요소 안과 닫는 `</a>` 태그 바로 위에 다음 줄을 추가하여 나머지 문서 데이터와 함께 `comment`를 표시합니다.

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

_modules/articles/client/views/admin/form-article.client.view.html_을 엽니다.

다음과 같이 제출 단추가 포함된 `<div class="form-group">` 요소를 찾습니다.

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

이 태그 바로 위에 사람들이 `comment` 필드를 편집할 수 있게 하는 또 다른 `<div class="form-group">` 요소를 추가합니다. 새 요소는 다음과 같습니다.

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>변경 내용을 로컬에서 테스트

변경 내용을 모두 저장합니다.

로컬 터미널 창에서 프로덕션 모드로 변경 내용을 다시 테스트합니다.

```bash
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> _config/env/production.js_이 원래대로 돌아갔으며 `MONGODB_URI` 환경 변수는 로컬 컴퓨터가 아닌 Azure 웹앱에만 설정되어 있다는 점에 유의해야 합니다. 구성 파일을 살펴보면 프로덕션 구성에서 기본적으로 로컬 MongoDB 데이터베이스를 사용하는 것을 알 수 있습니다. 이렇게 하면 로컬에서 코드 변경 내용을 테스트할 때 프로덕션 데이터를 건드리지 않을 수 있습니다.

브라우저에서 `http://localhost:8443`으로 이동하여 로그인했는지 확인합니다.

**관리자 > 문서 관리**를 선택한 다음 **+** 단추를 선택하여 문서를 추가합니다.

이제 새로운 `Comment` 텍스트 상자가 표시됩니다.

![문서에 추가된 주석 필드](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

터미널에서 `Ctrl+C`를 입력하여 Node.js를 중지합니다. 

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

로컬 터미널 창에서 Git의 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git commit -am "added article comment"
git push azure master
```

`git push`가 완료되면 Azure 웹앱으로 이동하여 새 기능을 테스트해 봅니다.

![Azure에 게시된 모델 및 데이터베이스 변경 내용](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

이전에 문서를 추가했으면 그 문서를 지금도 볼 수 있습니다. Cosmos DB의 기존 데이터는 손실되지 않습니다. 또한 데이터 스키마가 업데이트되고 기존 데이터는 그대로 유지됩니다.

## <a name="stream-diagnostic-logs"></a>진단 로그 스트림 

Node.js 응용 프로그램이 Azure App Service에서 실행되는 동안 콘솔 로그를 터미널에 파이프할 수 있습니다. 이 방법으로 응용 프로그램 오류를 디버깅하는 데 도움이 되는 진단 메시지를 동일하게 받을 수 있습니다.

로그 스트리밍을 시작하려면 Cloud Shell에서 [az webapp log tail](/cli/azure/webapp/log#tail) 명령을 사용합니다.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
``` 

로그 스트리밍이 시작되고 나면 브라우저에서 Azure 웹앱을 새로 고쳐 웹 트래픽을 만듭니다. 이제 터미널에 파이프된 콘솔 로그가 표시됩니다.

언제든지 `Ctrl+C`를 입력하여 로그 스트리밍을 중지합니다. 

## <a name="manage-your-azure-web-app"></a>Azure Web App 관리

[Azure Portal](https://portal.azure.com)로 이동하여 만든 웹앱을 확인합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

기본적으로 포털에는 웹앱의 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 페이지](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Azure에서 MongoDB 데이터베이스 만들기
> * Node.js 앱을 MongoDB에 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 터미널로 로그 스트림
> * Azure Portal에서 앱 관리

다음 자습서로 이동하여 사용자 지정 DNS 이름을 웹앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"] 
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)

