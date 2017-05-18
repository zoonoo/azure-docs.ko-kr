---
title: "Azure에서 Java 및 MySQL 웹앱 빌드 | Microsoft Docs"
description: "Azure MySQL 데이터베이스 서비스에 연결되는 Java 앱이 Azure App Service에서 작동되도록 하는 방법을 알아봅니다."
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/06/2017
ms.author: bbenz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fab7759154b38b8043a17c933f896d7af9514c85
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Azure에서 Java 및 MySQL 웹앱 빌드
이 자습서에서는 Azure에서 Java 웹앱을 만들고 MySQL 데이터베이스에 연결하는 방법을 보여 줍니다. 첫 번째 단계는 로컬 컴퓨터에 응용 프로그램을 복제하고 로컬 MySQL 인스턴스에서 작동되도록 하는 것입니다.
다음 단계는 Java 앱 및 MySQL용 Azure 서비스를 설정한 후 응용 프로그램을 Azure App Service에 배포하는 것입니다.
완료되면 할 일 목록 응용 프로그램 Azure에서 실행하고 Azure MySQL 데이터베이스 서비스에 연결합니다.

![Azure App Service에서 실행 중인 Java 앱](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

## <a name="before-you-begin"></a>시작하기 전에

이 샘플을 실행하기 전에 다음 필수 조건을 로컬로 설치합니다.

1. [Git 다운로드 및 설치](https://git-scm.com/)
1. [Java 7 이상 다운로드 및 설치](http://Java.net/downloads.Java)
1. [Maven 다운로드 및 설치](https://maven.apache.org/download.cgi)
1. [MySQL 다운로드, 설치 및 시작](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [Azure CLI 2.0 다운로드 및 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql-database"></a>로컬 MySQL 데이터베이스 준비

이 단계에서는 이 자습서에서 사용할 데이터베이스를 로컬 MySQL 서버에 만듭니다.

### <a name="connect-to-mysql-server"></a>MySQL 서버에 연결
명령줄에서 로컬 MySQL 서버에 연결합니다.

```bash
mysql -u root -p
```

명령이 성공적으로 실행되면 MySQL 서버가 이미 실행되고 있는 것입니다. 그렇지 않은 경우 [MySQL 설치 후 단계](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)에 따라 로컬 MySQL 서버가 시작되었는지 확인합니다.

암호를 묻는 메시지가 표시되면 `root` 계정에 대한 암호를 입력합니다. 루트 계정 암호를 기억하지 못하는 경우 [MySQL: 루트 암호를 재설정하는 방법](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)을 참조하세요.


### <a name="create-a-database-and-table"></a>데이터베이스 및 테이블 만들기

`mysql` 프롬프트에서 할 일 항목에 대한 데이터베이스 및 테이블을 만듭니다.

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

`quit`을 입력하여 서버 연결을 종료합니다.

```sql
quit
```

## <a name="create-local-java-application"></a>로컬 Java 응용 프로그램 만들기
이 단계에서는 GitHub 리포지토리를 복제하고, MySQL 데이터베이스 연결을 구성하고, 앱을 로컬로 실행합니다. 

### <a name="clone-the-sample"></a>샘플 복제

명령 프롬프트에서 작업 디렉터리로 이동합니다.  

다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

```bash
git clone https://github.com/bbenz/azure-mysql-java-todo-app
```

다음으로 리포지토리의 추가 정보에 나와 있는 단계를 수행하여 lombok.jar을 설정합니다.


### <a name="configure-mysql-connection"></a>MySQL 연결 구성

이 응용 프로그램은 Maven Jetty 플러그 인을 사용하여 로컬로 응용 프로그램을 실행하고 MySQL 데이터베이스에 연결합니다.
로컬 MySQL 인스턴스에 대한 액세스를 사용하도록 설정하려면 WebContent/WEB-INF/jetty-env.xml에서 로컬 MySQL 사용자 ID 및 암호를 설정합니다.

사용자 및 암호 값을 로컬 MySQL 인스턴스의 사용자 ID 및 암호로 업데이트합니다.

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql://localhost:3306/itemdb</Set>
           <Set name="User">root</Set>
           <Set name="Password"></Set>
        </New>
     </Arg>
    </New>
</Configure>

```

&gt; [!NOTE]
&gt; Jetty에서 `jetty-env.xml` 파일을 사용하는 방법에 대한 자세한 내용은 [Jetty XML 참조](http://www.eclipse.org/jetty/documentation/9.4.x/jetty-env-xml.html)를 참조하세요.
&gt; &gt;

### <a name="run-the-sample"></a>샘플 실행

Maven 명령을 사용하여 샘플 실행 

```bash
mvn package jetty:run
```

다음으로 `http://localhost:8080` 브라우저로 이동합니다. 해당 페이지에서 몇 가지 작업을 추가합니다.

언제든지 응용 프로그램을 중지하려면 명령 프롬프트에 `Ctrl`+`C`를 입력합니다. 

## <a name="create-a-mysql-database-in-azure"></a>Azure에서 MySQL 데이터베이스 만들기

이 단계에서는 [MySQL용 Azure 데이터베이스(미리 보기)](/azure/mysql)에서 MySQL 데이터베이스를 만듭니다. 나중에 이 데이터베이스에 연결하도록 Java 응용 프로그램을 구성합니다.

### <a name="log-in-to-azure"></a>Azure에 로그인

터미널 창에서 Azure CLI 2.0을 사용하여 Azure App Service에서 Java 응용 프로그램을 호스트하는 데 필요한 리소스를 만들 예정입니다. [az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다. 

```azurecli 
az login 
``` 

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. Azure 리소스 그룹은 웹앱, 데이터베이스, 저장소 계정이 관리되었는지 등 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예wp에서는 북유럽 지역의 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup  --location "North Europe"
```

`--location`에 사용할 수 있는 값에 대해 [az appservice list-locations](/cli/azure/appservice#list-locations) 명령을 사용합니다.

### <a name="create-a-mysql-server"></a>MySQL 서버 만들기

[az mysql server create](/cli/azure/mysql/server#create) 명령을 사용하여 MySQL용 Azure 데이터베이스의 서버를 만듭니다.

`&lt;mysql_server_name&gt;` 자리 표시자를 고유한 MySQL 서버 이름으로 바꿉니다. 이 이름은 MySQL 서버의 호스트 이름인 `&lt;mysql_server_name&gt;.database.windows.net`에 속하므로 전역적으로 고유해야 합니다. 또한 `&lt;admin_user&gt;` 및 `&lt;admin_password&gt;`를 고유한 값으로 바꿉니다.

```azurecli
az mysql server create --name &lt;mysql_server_name&gt; --resource-group myResourceGroup --location "North Europe" --user &lt;admin_user&gt; --password &lt;admin_password&gt;
```

MySQL 서버를 만들면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "administratorLogin": "&lt;admin_user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql_server_name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql_server_name&gt;",
  "location": "northeurope",
  "name": "&lt;mysql_server_name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-a-server-firewall"></a>서버 방화벽 구성

[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create) 명령을 사용하여 클라이언트 연결을 허용하도록 MySQL 서버에 대한 방화벽 규칙을 만듭니다. 

```azurecli
az mysql server firewall-rule create --name allIPs --server &lt;mysql_server_name&gt; --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

&gt; [!NOTE]
&gt; MySQL용 Azure 데이터베이스(미리 보기)에서는 Azure 서비스의 연결만 사용하도록 설정하지 않습니다. Azure의 IP 주소는 동적으로 할당되므로 지금은 모든 IP 주소를 사용하도록 설정하는 것이 좋습니다. 이 서비스는 미리 보기로 제공되며, 데이터베이스를 보호하기 위한 더 나은 방법이 곧 제공될 예정입니다.
&gt; &gt;

### <a name="connect-to-the-mysql-server"></a>MySQL 서버에 연결

터미널 창에서 Azure의 MySQL 서버에 연결합니다. `&lt;admin_user&gt;` 및 `&lt;mysql_server_name&gt;`에 대해 이전에 지정한 값을 사용합니다.

```bash
mysql -u &lt;admin_user&gt;@&lt;mysql_server_name&gt; -h &lt;mysql_server_name&gt;.database.windows.net -P 3306 -p
```

### <a name="create-a-database-and-table-in-the-azure-mysql-service"></a>Azure MySQL 서비스에서 데이터베이스 및 테이블 만들기

`mysql` 프롬프트에서 할 일 항목에 대한 데이터베이스 및 테이블을 만듭니다.

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

### <a name="create-a-user-with-permissions"></a>사용 권한이 있는 사용자 만들기

데이터베이스 사용자를 만들고 `todoItemDb` 데이터베이스에서 모든 권한을 부여합니다. 자리 표시자 `&lt;Javaapp_user&gt;` 및 `&lt;Javaapp_password&gt;`를 고유한 응용 프로그램 이름으로 바꿉니다.

```sql
CREATE USER '&lt;Javaapp_user&gt;' IDENTIFIED BY '&lt;Javaapp_password&gt;'; 
GRANT ALL PRIVILEGES ON todoItemDb.* TO '&lt;Javaapp_user&gt;';
```

`quit`을 입력하여 서버 연결을 종료합니다.

```sql
quit
```

### <a name="configure-the-local-mysql-connection-with-the-new-azure-mysql-service"></a>새 Azure MySQL 서비스로 로컬 MySQL 연결 구성

이 단계에서는 Java 응용 프로그램을 MySQL용 Azure 데이터베이스(미리 보기)에서 만든 MySQL 데이터베이스에 연결합니다. 

로컬 응용 프로그램에서 Azure MySQL 서비스로의 액세스를 설정하려면 WebContent/WEB-INF/jetty-env.xml에서 새 MySQL 끝점, 사용자 ID 및 암호를 설정합니다.

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql:<paste the endpoint for the Azure MySQL Service>/itemdb</Set>
           <Set name="User">Azure MySQL userID</Set>
           <Set name="Password">Azure MySQL Password</Set>
        </New>
     </Arg>
    </New>
</Configure>

```

변경 내용을 저장합니다.

## <a name="test-the-application"></a>응용 프로그램 테스트

이전에 사용한 것과 동일한 maven 명령을 사용하여 샘플을 로컬로 다시 실행하지만 이번에는 Azure MySQL 서비스에 연결합니다. 

```bash
mvn package jetty:run
```

브라우저에서 `http://localhost:8080`으로 이동합니다. 오류 없이 페이지가 로드되면 Java 응용 프로그램이 Azure의 MySQL 데이터베이스에 연결됩니다. 

페이지에 일부 작업 추가 기능이 없을 것입니다.

언제든지 응용 프로그램을 중지하려면 터미널에 `Ctrl`+`C`을 입력합니다. 

### <a name="secure-sensitive-data"></a>중요 데이터 보안 유지

`WebContent/WEB-INF/jetty-env.xml`의 중요 데이터가 Git에 커밋되지 않았는지 확인합니다.

이렇게 하려면 리포지토리 루트에서 `.gitignore`를 열고 새 줄에 `WebContent/WEB-INF/jetty-env.xml`을 추가합니다. 변경 내용을 저장합니다.

변경 내용을 `.gitignore`에 커밋합니다.

```bash
git add .gitignore
git commit -m "keep sensitive data in WebContent/WEB-INF/jetty-env.xml out of git"
```

## <a name="deploy-the-java-application-to-azure"></a>Azure에 Java 응용 프로그램 배포
다음으로 Azure App Service에 Java 응용 프로그램을 배포합니다.

### <a name="create-an-appservice-plan"></a>App Service 계획 만들기

[az appservice plan create](/cli/azure/appservice/plan#create) 명령으로 App Service 계획을 만듭니다. 

&gt; [!NOTE] 
&gt; App Service 계획은 앱을 호스트하는 데 사용되는 실제 리소스의 컬렉션을 나타냅니다. App Service 계획에 할당된 모든 응용 프로그램은 정의된 리소스를 공유하므로 여러 앱을 호스팅할 때 비용을 절감할 수 있습니다. 
&gt;&gt; App Service 계획은 다음을 정의합니다. &gt; &gt; * 지역(북유럽, 미국 동부, 동남 아시아) &gt; * 인스턴스 크기(소형, 중간, 대형) &gt; * 확장 개수(1, 2, 3개 인스턴스 등) &gt; * SKU(무료, 공유, 기본, 표준, 프리미엄)&gt; 

다음 예제에서는 **무료** 가격 책정 계층을 사용하여 `myAppServicePlan`이라는 App Service 계획을 만듭니다.

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

App Service 계획을 만들면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
``` 

### <a name="create-an-azure-web-app"></a>Azure Web App 만들기

이제 App Service 계획을 만들었으므로 `myAppServicePlan` App Service 계획 내에서 Azure Web App을 만듭니다. 웹앱은 코드를 배포할 호스팅 공간을 제공하고, 배포된 응용 프로그램을 확인할 수 있도록 URL도 제공합니다. [az appservice web create](/cli/azure/appservice/web#create) 명령을 사용하여 웹앱을 만듭니다. 

다음 명령에서 `&lt;app_name&gt;` 자리 표시자를 고유한 응용 프로그램 이름으로 대체합니다. 이 고유한 이름은 웹앱에 대한 기본 도메인 이름의 일부로 사용되므로 이름은 Azure에 있는 모든 앱에서 고유해야 합니다. 나중에 사용자에게 노출하기 전에 웹앱에 사용자 지정 DNS 항목을 매핑할 수 있습니다. 

```azurecli
az appservice web create --name &lt;app_name&gt; --resource-group myResourceGroup --plan myAppServicePlan
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
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "&lt;app_name&gt;.azurewebsites.net",
    "&lt;app_name&gt;.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "&lt;app_name&gt;.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    &lt; JSON data removed for brevity. &gt;
}
```

### <a name="set-the-java-version-the-java-application-server-type-and-the-application-server-version"></a>Java 버전, Java 응용 프로그램 서버 형식 및 응용 프로그램 서버 버전 설정

[az appservice web config update](/cli/azure/appservice/web/config#update) 명령을 사용하여 Java 버전, Java 앱 서버(컨테이너) 및 컨테이너 버전을 설정합니다.

다음 명령은 Java 버전을 8로, Java 앱 서버를 Jetty로, Jetty 버전을 최신 Jetty 9.3으로 설정합니다.

```azurecli
az appservice web config update --name &lt;app_name&gt; --resource-group myResourceGroup --java-version 1.8 --java-container Jetty --java-container-version 9.3
```


### <a name="get-credentials-for-deployment-to-the-web-app-using-ftp"></a>FTP를 사용하여 웹앱에 배포하기 위한 자격 증명 가져오기 

FTP, 로컬 Git, GitHub, Visual Studio Team Services 및 BitBucket과 같은 다양한 방법으로 응용 프로그램을 Azure App Service에 배포할 수 있습니다. 이 예제에서는 Maven을 사용하여 .WAR 파일을 컴파일하고 FTP를 사용하여 .WAR 파일을 웹앱에 배포합니다.

ftp 명령에서 웹앱으로 전달할 자격 증명을 결정하려면 다음과 같이 [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#list-publishing-profiles) 명령을 사용합니다. 

```azurecli

az appservice web deployment list-publishing-profiles --name &lt;app_name&gt; --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --o table

```
### <a name="compile-the-local-application-to-deply-to-the-web-app"></a>웹앱에 배포할 로컬 응용 프로그램 컴파일 

Azure Web App에서 실행하도록 로컬 Java 응용 프로그램을 준비하려면 Java 응용 프로그램에 있는 모든 리소스를 배포할 수 있는 단일 .WAR 파일로 다시 컴파일합니다. 응용 프로그램 pom.xml이 있는 디렉터리로 이동한 후 다음을 입력합니다.

```bash 
mvn clean package
``` 
Maven 패키지 프로세스가 거의 다 끝나면 .WAR 파일의 위치가 표시됩니다.  출력은 다음과 비슷합니다.

```bash

[INFO] Processing war project
[INFO] Copying webapp resources [local-location\GitHub\mysql-java-todo-app\WebContent]
[INFO] Webapp assembled in [1519 msecs]
[INFO] Building war: C:\Users\your\localGitHub\mysql-java-todo-app\target\azure-appservice-mysql-java-sample-0.0.1-SNAPSHOT.war
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------

```

.War 파일의 위치를 적어두고 즐겨 찾는 FTP 메서드를 사용하여 .WAR 파일을 Jetty WebApps 폴더로 배포합니다.  Jetty WebApps 폴더는 Azure Web App의 /site/wwwroot/webapps에 있습니다. 

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기

`http://&lt;app_name&gt;.azurewebsites.net/&lt;app_name&gt;`으로 이동한 후 목록에 몇 가지 작업을 추가합니다. 

![Azure App Service에서 실행 중인 Java 앱](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)


**축하합니다.** Azure App Service에서 데이터 기반 Java 앱이 실행되고 있습니다.
이 앱을 업데이트하려면 maven clean package 명령을 반복하고 FTP를 통해 앱을 다시 배포합니다.

## <a name="manage-your-azure-web-app"></a>Azure Web App 관리

Azure Portal로 이동한 후 [https://portal.azure.com](https://portal.azure.com)에 로그인하여 만든 웹앱을 봅니다.

왼쪽 메뉴에서 **App Service**를 클릭한 다음 Azure Web App의 이름을 클릭합니다.

현재 웹앱의 _블레이드_(가로로 열리는 포털 페이지)에 있습니다.

기본적으로 웹앱의 블레이드는 **개요** 페이지를 표시합니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 블레이드의 왼쪽에 있는 탭에서는 열 수 있는 다른 구성 페이지를 보여 줍니다.

**응용 프로그램 설정** 페이지에서 

![Azure App Service Web App 응용 프로그램 설정](./media/app-service-web-tutorial-java-mysql/appservice-web-app-application-settings.png)



블레이드의 이러한 탭은 웹앱에 추가할 수 있는 유용한 많은 기능을 보여 줍니다. 다음은 몇 가지 가능성을 제공합니다.

* 사용자 지정 DNS 이름 매핑
* 사용자 지정 SSL 인증서 바인딩
* 지속적 배포 구성
* 수평 및 수직 확장
* 사용자 인증 추가

## <a name="more-resources"></a>추가 리소스

- [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)
- [Azure Web Apps에 기존 사용자 지정 SSL 인증서 바인딩](app-service-web-tutorial-custom-ssl.md)
- [웹앱 CLI 스크립트](app-service-cli-samples.md)</PRE>

