---
title: Web App for Containers에서 다중 컨테이너 앱 만들기 - Azure App Service
description: Azure에서 WordPress 및 MySQL 앱을 통해 Docker Compose 및 Kubernetes 구성 파일이 포함된 다중 컨테이너를 사용하는 방법을 알아봅니다.
keywords: Azure App Service, 웹앱, Linux, Docker, Compose, 다중 컨테이너, 다중-컨테이너, 컨테이너용 웹앱, 다중 컨테이너, 컨테이너, Kubernetes, WordPress, Azure DB for MySQL, 컨테이너를 포함한 프로덕션 데이터베이스
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 1480032b7ff018081d9dc25038bf336740810079
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657580"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>자습서: Web App for Containers에서 다중 컨테이너(미리 보기) 앱 만들기

[Web App for Containers](app-service-linux-intro.md)는 Docker 이미지를 사용할 수 있는 유연한 방법을 제공합니다. 이 자습서에서는 WordPress 및 MySQL을 사용하여 다중 컨테이너 앱을 만드는 방법을 알아봅니다. Cloud Shell에서 이 자습서를 완료하겠지만 [Azure CLI](/cli/azure/install-azure-cli) 명령줄 도구(2.0.32 이상)를 사용하여 이러한 명령을 로컬로 실행할 수도 있습니다.

이 자습서에서 학습할 방법은 다음과 같습니다.
> [!div class="checklist"]
> * Web App for Containers에서 작동하도록 Docker Compose 구성 변환
> * Web App for Containers에서 작동하도록 Kubernetes 구성 변환
> * Azure에 다중 컨테이너 앱 배포
> * 애플리케이션 설정 추가
> * 컨테이너용 영구 저장소 사용
> * Azure Database for MySQL에 연결
> * 오류 문제 해결

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="preview-feature-limitations"></a>미리 보기 기능 제한 사항
다중 컨테이너는 현재 미리 보기로 제공되며, 다음 App Service 플랫폼 기능은 지원되지 않습니다. GA(일반 공급) 전에 다중 컨테이너 웹앱을 위해 이러한 기능을 활성화할 예정입니다.
* 인증/권한 부여
* 관리 ID

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 [Docker Compose](https://docs.docker.com/compose/) 또는 [Kubernetes](https://kubernetes.io/)를 사용해야 합니다.

## <a name="download-the-sample"></a>샘플 다운로드

이 자습서에서는 [Docker](https://docs.docker.com/compose/wordpress/#define-the-project)의 작성 파일을 사용하지만 Azure Database for MySQL, 영구 저장소 및 Redis를 포함하도록 수정합니다. 구성 파일은 [Azure 샘플](https://github.com/Azure-Samples/multicontainerwordpress)에 있습니다.

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Cloud Shell에서 자습서 디렉터리를 만든 다음, 변경합니다.

```bash
mkdir tutorial

cd tutorial
```

다음으로 다음 명령을 실행하여 자습서 디렉터리에 샘플 앱 리포지토리를 복제합니다. 그런 다음, `multicontainerwordpress` 디렉터리로 변경합니다.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Cloud Shell에서 [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *미국 중남부* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. **표준** 계층에서 Linux의 App Service에 지원되는 모든 위치를 확인하려면 [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) 명령을 실행합니다.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

일반적으로 사용자와 가까운 지역에서 리소스 그룹 및 리소스를 만듭니다.

명령이 완료되면 JSON 출력이 리소스 그룹 속성을 보여줍니다.

## <a name="create-an-azure-app-service-plan"></a>Azure App Service 계획 만들기

Cloud Shell에서 [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) 명령을 사용하여 리소스 그룹에 App Service 계획을 만듭니다.

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

다음 예제에서는 **표준** 가격 책정 계층(`--sku S1`) 및 Linux 컨테이너(`--is-linux`)에서 `myAppServicePlan`이라는 App Service 계획을 만듭니다.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

App Service 계획을 만든 경우 Cloud Shell은 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="docker-compose-configuration-options"></a>Docker Compose 구성 옵션

이 자습서에서는 [Docker](https://docs.docker.com/compose/wordpress/#define-the-project)의 작성 파일을 사용하지만 Azure Database for MySQL, 영구 저장소 및 Redis를 포함하도록 수정합니다. 또는 [Kubernetes 구성](#use-a-kubernetes-configuration-optional)을 사용할 수 있습니다. 구성 파일은 [Azure 샘플](https://github.com/Azure-Samples/multicontainerwordpress)에 있습니다.

다음 목록에서는 Web App for Containers에서 지원되거나 지원되지 않는 Docker Compose 구성 옵션을 보여 줍니다.

### <a name="supported-options"></a>지원되는 옵션

* command
* entrypoint
* 환경
* 이미지
* ports
* restart
* services
* volumes

### <a name="unsupported-options"></a>지원되지 않는 옵션

* build(허용 안 함)
* depends_on(무시됨)
* networks(무시됨)
* secrets(무시됨)

> [!NOTE]
> 명시적으로 호출되지 않는 다른 모든 옵션은 공개 미리 보기에서도 무시됩니다.

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>WordPress 및 MySQL 컨테이너가 포함된 Docker Compose

## <a name="create-a-docker-compose-app"></a>Docker Compose 앱 만들기

Cloud Shell에서 [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 다중 컨테이너 [웹앱](app-service-linux-intro.md)을 만듭니다. _\<app_name>_ 을 고유한 앱 이름으로 바꿔야 합니다.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

웹앱이 만들어지면 Cloud Shell에서는 다음 예제와 비슷한 출력을 표시합니다.

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>앱으로 이동

(`http://<app_name>.azurewebsites.net`)에 배포된 앱으로 이동합니다. 앱을 로드하는 데 몇 분 정도 걸릴 수 있습니다. 오류가 발생하면 몇 분 후에 브라우저를 새로 고칩니다. 문제가 발생하여 이를 해결하려면 [컨테이너 로그](#find-docker-container-logs)를 검토합니다.

![Web App for Containers의 다중 컨테이너 앱 샘플][1]

**축하합니다!** Web App for Containers에 다중 컨테이너 앱을 만들었습니다. 다음으로, Azure Database for MySQL을 사용하도록 앱을 구성합니다. 지금은 WordPress를 설치하지 마세요.

## <a name="connect-to-production-database"></a>프로덕션 데이터베이스에 연결

프로덕션 환경에서 데이터베이스 컨테이너를 사용하지 않는 것이 좋습니다. 로컬 컨테이너는 확장할 수 없습니다. 대신 Azure Database for MySQL을 사용하면 확장할 수 있습니다.

### <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버 만들기

[`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) 명령을 사용하여 Azure Database for MySQL 서버를 만듭니다.

다음 명령에서 _&lt;mysql_server_name>_ 자리 표시자를 고유한 MySQL 서버 이름으로 바꿉니다(유효한 문자: `a-z`, `0-9` 및 `-`). 이 이름은 MySQL 서버의 호스트 이름(`<mysql_server_name>.database.windows.net`)의 일부이며, 전역적으로 고유해야 합니다.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

서버를 만드는 작업이 완료될 때까지 몇 분 정도 걸릴 수 있습니다. MySQL 서버를 만들면 Cloud Shell은 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "southcentralus",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>서버 방화벽 구성

[`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) 명령을 사용하여 클라이언트 연결을 허용하도록 MySQL 서버에 대한 방화벽 규칙을 만듭니다. 시작 IP 및 끝 IP가 0.0.0.0으로 설정되면 방화벽이 다른 Azure 리소스에 대해서만 열립니다.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> [앱이 사용하는 아웃바운드 IP 주소만 사용](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)으로 방화벽 규칙을 훨씬 더 엄격하게 제한할 수 있습니다.
>

### <a name="create-the-wordpress-database"></a>WordPress 데이터베이스 만들기

```bash
az mysql db create --resource-group myResourceGroup --server-name <mysql_server_name> --name wordpress
```

데이터베이스를 만들면 Cloud Shell은 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>WordPress의 데이터베이스 변수 구성

WordPress 앱을 이 새 MySQL 서버에 연결하려면 `MYSQL_SSL_CA`에서 정의된 SSL CA 경로를 포함하여 몇 가지 WordPress 관련 환경 변수를 구성합니다. [DigiCert](https://www.digicert.com/)의 [Baltimore CyberTrust Root](https://www.digicert.com/digicert-root-certificates.htm)는 아래의 [사용자 지정 이미지](https://docs.microsoft.com/azure/app-service/containers/tutorial-multi-container-app#use-a-custom-image-for-mysql-ssl-and-other-configurations)에서 제공됩니다.

이렇게 변경하려면 Cloud Shell에서 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용합니다. 앱 설정은 대/소문자를 구분하고 공백으로 구분합니다.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WORDPRESS_DB_HOST="<mysql_server_name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql_server_name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

앱 설정을 만든 경우 Cloud Shell은 다음 예제와 비슷한 정보를 표시합니다.

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql_server_name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
```

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>MySQL SSL 및 기타 구성에 사용자 지정 이미지 사용

SSL은 기본적으로 Azure Database for MySQL에서 사용됩니다. WordPress에는 MySQL에서 SSL을 사용하기 위한 추가 구성이 필요합니다. WordPress '공식 이미지'에서 추가 구성을 제공하지는 않지만, 사용자의 편의를 위해 [사용자 지정 이미지](https://hub.docker.com/r/microsoft/multicontainerwordpress/builds/)가 준비되어 있습니다. 실제로 원하는 변경 내용을 자신의 이미지에 추가합니다.

사용자 지정 이미지는 [Docker 허브의 WordPress](https://hub.docker.com/_/wordpress/) '공식 이미지'를 기반으로 합니다. Azure Database for MySQL에 대한 이 사용자 지정 이미지는 다음과 같이 변경되었습니다.

* [SSL에 대한 Baltimore Cyber Trust Root 인증서 파일을 MySQL에 추가](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61)
* [WordPress wp-config.php에서 MySQL SSL 인증 기관 인증서에 대한 앱 설정 사용](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163)
* [MySQL SSL에 필요한 MYSQL_CLIENT_FLAGS에 대한 WordPress 정의 추가](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164)

Redis에 대해 다음과 같이 변경되었습니다(다음 섹션에서 사용됨).

* [Redis v4.0.2용 PHP 확장 추가](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [파일 추출에 필요한 압축 해제 추가](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Redis Object Cache 1.3.8 WordPress 플러그 인 추가](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [WordPress wp-config.php에서 Redis 호스트 이름에 대한 앱 설정 사용](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

사용자 지정 이미지를 사용하려면 docker-compose-wordpress.yml 파일을 업데이트합니다. Cloud Shell에서 `nano docker-compose-wordpress.yml`을 입력하여 Nano 텍스트 편집기를 엽니다. `image: microsoft/multicontainerwordpress`를 사용하도록 `image: wordpress`를 변경합니다. 데이터베이스 컨테이너가 더 이상 필요하지 않습니다. 따라서 구성 파일에서 `db`, `environment`, `depends_on` 및 `volumes` 섹션을 제거합니다. 파일은 다음 코드와 같습니다.

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

변경 내용을 저장하고 Nano를 종료합니다. `^O` 명령을 사용하여 저장하고 `^X` 명령을 사용하여 종료합니다.

### <a name="update-app-with-new-configuration"></a>새 구성으로 앱 업데이트

Cloud Shell에서 [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) 명령을 사용하여 다중 컨테이너 [웹앱](app-service-linux-intro.md)을 다시 구성합니다. _\<app_name>_ 을 앞에서 만든 웹앱의 이름으로 바꿔야 합니다.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

앱이 다시 구성되면 Cloud Shell에서는 다음 예제와 비슷한 정보를 표시합니다.

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
```

### <a name="browse-to-the-app"></a>앱으로 이동

(`http://<app_name>.azurewebsites.net`)에 배포된 앱으로 이동합니다. 이제 앱에서 Azure Database for MySQL을 사용합니다.

![Web App for Containers의 샘플 다중 컨테이너 앱][1]

## <a name="add-persistent-storage"></a>영구 저장소 추가

이제 다중 컨테이너가 Web App for Containers에서 실행됩니다. 그러나 지금 WordPress를 설치하고 나중에 앱을 다시 시작하면 WordPress 설치가 없어진 것을 알게 됩니다. 이는 Docker Compose 구성이 현재 컨테이너 내의 저장소 위치를 가리키기 때문에 발생합니다. 컨테이너에 설치된 파일은 앱을 다시 시작할 때까지 유지되지 않습니다. 이 섹션에서는 영구 저장소를 WordPress 컨테이너에 추가합니다.

### <a name="configure-environment-variables"></a>환경 변수 구성

영구 저장소를 사용하려면 App Service 내에서 이 설정을 사용하도록 설정합니다. 이렇게 변경하려면 Cloud Shell에서 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용합니다. 앱 설정은 대/소문자를 구분하고 공백으로 구분합니다.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

앱 설정을 만든 경우 Cloud Shell은 다음 예제와 비슷한 정보를 표시합니다.

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="modify-configuration-file"></a>구성 파일 수정

Cloud Shell에서 `nano docker-compose-wordpress.yml`을 입력하여 Nano 텍스트 편집기를 엽니다.

`volumes` 옵션은 파일 시스템을 컨테이너 내의 디렉터리에 매핑합니다. `${WEBAPP_STORAGE_HOME}`은 앱의 영구 저장소에 매핑되는 App Service의 환경 변수입니다. WordPress 파일이 컨테이너 대신 영구 저장소에 설치되도록 이 환경 변수를 volumes 옵션에 사용합니다. 파일을 다음과 같이 수정합니다.

`wordpress` 섹션에서 다음 코드와 같이 `volumes` 옵션을 추가합니다.

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>새 구성으로 앱 업데이트

Cloud Shell에서 [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) 명령을 사용하여 다중 컨테이너 [웹앱](app-service-linux-intro.md)을 다시 구성합니다. _\<app_name>_ 을 고유한 앱 이름으로 바꿔야 합니다.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

명령이 실행되면 다음 예제와 비슷한 출력이 표시됩니다.

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>앱으로 이동

(`http://<app_name>.azurewebsites.net`)에 배포된 앱으로 이동합니다.

이제 WordPress 컨테이너에서 Azure Database for MySQL과 영구 저장소를 사용합니다.

## <a name="add-redis-container"></a>Redis 컨테이너 추가

 WordPress '공식 이미지'에는 Redis에 대한 종속성이 포함되어 있지 않습니다. 이러한 종속성 및 WordPress에서 Redis를 사용하는 데 필요한 추가 구성은 이 [사용자 지정 이미지](https://github.com/Azure-Samples/multicontainerwordpress)에 준비되어 있습니다. 실제로 원하는 변경 내용을 자신의 이미지에 추가합니다.

사용자 지정 이미지는 [Docker 허브의 WordPress](https://hub.docker.com/_/wordpress/) '공식 이미지'를 기반으로 합니다. Redis에 대한 이 사용자 지정 이미지는 다음과 같이 변경되었습니다.

* [Redis v4.0.2용 PHP 확장 추가](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [파일 추출에 필요한 압축 해제 추가](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Redis Object Cache 1.3.8 WordPress 플러그 인 추가](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [WordPress wp-config.php에서 Redis 호스트 이름에 대한 앱 설정 사용](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

다음 예제와 같이 구성 파일의 맨 아래에 redis 컨테이너를 추가합니다.

[!code-yml[Main](../../../azure-app-service-multi-container/compose-wordpress.yml)]

### <a name="configure-environment-variables"></a>환경 변수 구성

Redis를 사용하려면 App Service 내에서 `WP_REDIS_HOST` 설정을 사용하도록 설정합니다. 이는 WordPress에서 Redis 호스트와 통신하는 데 *필요한 설정*입니다. 이렇게 변경하려면 Cloud Shell에서 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용합니다. 앱 설정은 대/소문자를 구분하고 공백으로 구분합니다.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WP_REDIS_HOST="redis"
```

앱 설정을 만든 경우 Cloud Shell은 다음 예제와 비슷한 정보를 표시합니다.

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
```

### <a name="update-app-with-new-configuration"></a>새 구성으로 앱 업데이트

Cloud Shell에서 [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) 명령을 사용하여 다중 컨테이너 [웹앱](app-service-linux-intro.md)을 다시 구성합니다. _\<app_name>_ 을 고유한 앱 이름으로 바꿔야 합니다.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

명령이 실행되면 다음 예제와 비슷한 출력이 표시됩니다.

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>앱으로 이동

(`http://<app_name>.azurewebsites.net`)에 배포된 앱으로 이동합니다.

단계를 완료하고 WordPress를 설치합니다.

### <a name="connect-wordpress-to-redis"></a>Redis에 WordPress 연결

WordPress 관리자에 로그인합니다. 왼쪽 탐색 영역에서 **플러그 인**을 선택한 다음, **설치된 플러그 인**을 선택합니다.

![WordPress 플러그 인 선택][2]

여기서는 모든 플러그 인이 표시됩니다.

플러그 인 페이지에서 **Redis Object Cache**를 찾은 다음, **활성화**를 클릭합니다.

![Redis 활성화][3]

**설정**을 클릭합니다.

![설정 클릭][4]

**개체 캐시 사용** 단추를 클릭합니다.

!['개체 캐시 사용' 단추 클릭][5]

WordPress가 Redis 서버에 연결됩니다. 연결 **상태**가 동일한 페이지에 표시됩니다.

![WordPress가 Redis 서버에 연결됩니다. 연결 ** 상태 **가 동일한 페이지에 표시됩니다.][6]

**축하합니다!** WordPress가 Redis에 연결되었습니다. 이제 프로덕션 준비가 완료된 앱에서 **Azure Database for MySQL, 영구 저장소 및 Redis**를 사용합니다. 그리고 App Service 계획을 여러 인스턴스로 확장할 수 있습니다.

## <a name="use-a-kubernetes-configuration-optional"></a>Kubernetes 구성 사용(선택 사항)

이 섹션에서는 Kubernetes 구성을 사용하여 여러 컨테이너를 배포하는 방법에 대해 알아봅니다. 이전 단계를 수행하여 [리소스 그룹](#create-a-resource-group) 및 [App Service 계획](#create-an-azure-app-service-plan)을 만듭니다. 대부분의 단계가 작성 섹션 단계와 비슷하므로 구성 파일이 결합되었습니다.

### <a name="supported-kubernetes-options-for-multi-container"></a>다중 컨테이너에 지원되는 Kubernetes 옵션

* args
* command
* 컨테이너
* 이미지
* 이름
* ports
* spec

> [!NOTE]
>명시적으로 호출되지 않는 다른 모든 Kubernetes 옵션은 공개 미리 보기에서 지원되지 않습니다.
>

### <a name="kubernetes-configuration-file"></a>Kubernetes 구성 파일

자습서의 이 부분에서 *kubernetes wordpress.yml*을 사용합니다. 여기에 참조용으로 표시됩니다.

[!code-yml[Main](../../../azure-app-service-multi-container/kubernetes-wordpress.yml)]

### <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버 만들기

[`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) 명령을 사용하여 Azure Database for MySQL의 서버를 만듭니다.

다음 명령에서 _&lt;mysql_server_name>_ 자리 표시자를 고유한 MySQL 서버 이름으로 바꿉니다(유효한 문자: `a-z`, `0-9` 및 `-`). 이 이름은 MySQL 서버의 호스트 이름(`<mysql_server_name>.database.windows.net`)의 일부이며, 전역적으로 고유해야 합니다.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

MySQL 서버를 만들면 Cloud Shell은 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "southcentralus",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>서버 방화벽 구성

[`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) 명령을 사용하여 클라이언트 연결을 허용하도록 MySQL 서버에 대한 방화벽 규칙을 만듭니다. 시작 IP 및 끝 IP가 0.0.0.0으로 설정되면 방화벽이 다른 Azure 리소스에 대해서만 열립니다.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> [앱이 사용하는 아웃바운드 IP 주소만 사용](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)으로 방화벽 규칙을 훨씬 더 엄격하게 제한할 수 있습니다.
>

### <a name="create-the-wordpress-database"></a>WordPress 데이터베이스 만들기

아직 [Azure Database for MySQL 서버](#create-an-azure-database-for-mysql-server)를 만들지 않은 경우 이 서버를 만듭니다.

```bash
az mysql db create --resource-group myResourceGroup --server-name <mysql_server_name> --name wordpress
```

데이터베이스를 만들면 Cloud Shell은 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="create-a-multi-container-app-kubernetes"></a>다중 컨테이너 앱 만들기(Kubernetes)

Cloud Shell에서 [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 명령을 사용하여 `myResourceGroup` 리소스 그룹 및 `myAppServicePlan` App Service 계획에 다중 컨테이너 [웹앱](app-service-linux-intro.md)을 만듭니다. _\<app_name>_ 을 고유한 앱 이름으로 바꿔야 합니다.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type kube --multicontainer-config-file kubernetes-wordpress.yml
```

웹앱이 만들어지면 Cloud Shell에서는 다음 예제와 비슷한 출력을 표시합니다.

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
  < JSON data removed for brevity. >
}
```

### <a name="configure-database-variables-in-wordpress"></a>WordPress의 데이터베이스 변수 구성

WordPress 앱을 이 새 MySQL 서버에 연결하려면 몇 가지 WordPress 관련 환경 변수를 구성합니다. 이렇게 변경하려면 Cloud Shell에서 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용합니다. 앱 설정은 대/소문자를 구분하고 공백으로 구분합니다.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WORDPRESS_DB_HOST="<mysql_server_name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql_server_name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

앱 설정을 만든 경우 Cloud Shell은 다음 예제와 비슷한 정보를 표시합니다.

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql_server_name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  }
]
```

### <a name="add-persistent-storage"></a>영구 저장소 추가

이제 다중 컨테이너가 Web App for Containers에서 실행됩니다. 파일이 유지되지 않으므로 다시 시작하면 데이터가 지워집니다. 이 섹션에서는 영구 저장소를 WordPress 컨테이너에 추가합니다.

### <a name="configure-environment-variables"></a>환경 변수 구성

영구 저장소를 사용하려면 App Service 내에서 이 설정을 사용하도록 설정합니다. 이렇게 변경하려면 Cloud Shell에서 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용합니다. 앱 설정은 대/소문자를 구분하고 공백으로 구분합니다.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

앱 설정을 만든 경우 Cloud Shell은 다음 예제와 비슷한 정보를 표시합니다.

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="browse-to-the-app"></a>앱으로 이동

(`http://<app_name>.azurewebsites.net`)에 배포된 앱으로 이동합니다.

이제 앱에서 Web App for Containers에 있는 여러 컨테이너를 실행합니다.

![Web App for Containers의 샘플 다중 컨테이너 앱][1]

**축하합니다!** Web App for Containers에 다중 컨테이너 앱을 만들었습니다.

Redis를 사용하려면 [Redis에 WordPress 연결](#connect-wordpress-to-redis)의 단계를 따릅니다.

## <a name="find-docker-container-logs"></a>Docker 컨테이너 로그 찾기

여러 컨테이너를 사용하는 데 문제가 발생하면 `https://<app_name>.scm.azurewebsites.net/api/logs/docker`로 이동하여 컨테이너 로그에 액세스할 수 있습니다.

다음 예제와 비슷한 출력이 표시됩니다.

```json
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
```

각 컨테이너에 대한 로그와 부모 프로세스에 대한 추가 로그가 표시됩니다. 해당 `href` 값을 브라우저에 복사하여 로그를 살펴봅니다.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

이 자습서에서는 다음 방법에 대해 알아보았습니다.
> [!div class="checklist"]
> * Web App for Containers에서 작동하도록 Docker Compose 구성 변환
> * Web App for Containers에서 작동하도록 Kubernetes 구성 변환
> * Azure에 다중 컨테이너 앱 배포
> * 애플리케이션 설정 추가
> * 컨테이너용 영구 저장소 사용
> * Azure Database for MySQL에 연결
> * 오류 문제 해결

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Web App for Containers에 사용자 지정 Docker 이미지 사용](tutorial-custom-docker-image.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
