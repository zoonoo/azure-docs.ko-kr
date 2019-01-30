---
title: Pivotal Cloud Foundry에서 애플리케이션과 함께 Open Service Broker for Azure 데이터베이스 사용
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/11/2019
ms.topic: tutorial
description: Open Service Broker for Azure 데이터베이스를 사용하도록 Pivotal Cloud Foundry 애플리케이션을 구성하는 방법 설명
keywords: Pivotal Cloud Foundry, Cloud Foundry, Open Service Broker, Open Service Broker for Azure
tags: Cloud-Foundry
ms.openlocfilehash: d553cd09ef42e47e3a10fb96039063b8aae665cb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258877"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>자습서: Pivotal Cloud Foundry에서 애플리케이션과 함께 Open Service Broker for Azure 데이터베이스 사용

Pivotal Cloud Foundry 인스턴스와 함께 Open Service Broker for Azure를 사용하면 Cloud Foundry CLI 및 Pivotal Cloud Foundry 인스턴스에서 직접 Azure의 데이터베이스와 같은 서비스를 프로비전할 수 있습니다. 또한 해당 서비스를 Pivotal Cloud Foundry 인스턴스에서 실행 중인 애플리케이션에 바인딩할 수 있습니다. 이 방식으로 서비스에 애플리케이션을 바인딩하는 경우 애플리케이션 내의 모든 코드 또는 구성을 업데이트할 필요가 없습니다. 이 문서에서는 Pivotal Cloud Foundry의 애플리케이션과 함께 데이터베이스에 대해 Open Service Broker for Azure 서비스를 사용하는 방법을 설명합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Pivotal Cloud Foundry에 애플리케이션 공간을 준비합니다.
> * GitHub에서 애플리케이션 예제 원본을 복제합니다.
> * 배포를 위한 애플리케이션을 준비합니다.
> * 애플리케이션을 배포합니다.
> * Open Service Broker for Azure를 사용하여 데이터베이스를 만듭니다.
> * 애플리케이션에 데이터베이스를 바인딩합니다.

## <a name="prerequisites"></a>필수 조건

진행하기 전에 다음을 수행해야 합니다.

* [Pivotal Cloud Foundry 설치 및 구성](create-cloud-foundry-on-azure.md)
* Cloud Foundry 인스턴스와 함께 [Open Service Broker for Azure 설치 및 구성](https://network.pivotal.io/products/azure-open-service-broker-pcf)

Open Service Broker for Azure 타일이 설치 및 구성된 Pivotal Cloud Foundry Ops Manager 화면의 예제는 다음과 같습니다.

![Open Service Broker for Azure가 설치된 Pivotal Cloud Foundry](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>Pivotal Cloud Foundry에 애플리케이션 공간 준비

Pivotal Cloud Foundry 인스턴스에 애플리케이션을 배포하기 위해 `cf` 명령줄 도구를 사용하여 로그인해야 합니다. 원하는 조직 및 대상이 지정된 공간도 있어야 합니다.

로그인되었는지 확인하고 대상으로 하는 공간을 표시하려면 `cf target`을 사용합니다. 아래 예제는 *myorg* 조직을 사용하고, *dev* 공간을 대상으로 하는 *관리자*로 이미 로그인한 사용자를 보여줍니다.

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

로그인하려면 `cf login`을 사용합니다.

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

새 조직 및 공간을 만들려면 `cf create-org` 및 `cf create-space`를 사용합니다.

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

공간을 대상으로 지정하려면 `cf target`을 사용합니다.

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>애플리케이션 코드 가져오기

애플리케이션에서 Open Service Broker for Azure를 사용하려면 애플리케이션에서 데이터베이스와 같은 데이터 원본으로 사용해야 합니다. 이 문서에서는 [Cloud Foundry의 봄 음악 애플리케이션 예제](https://github.com/cloudfoundry-samples/spring-music)를 사용하여 데이터 원본을 사용하는 애플리케이션을 보여줍니다.

GitHub에서 애플리케이션을 복제하고 해당 디렉터리로 이동합니다.

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> 이 애플리케이션에 대한 데이터 원본을 보려면 [src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml) 파일을 엽니다.


## <a name="prepare-the-application-for-deployment"></a>배포를 위한 애플리케이션 준비

Pivotal Cloud Foundry 인스턴스에 애플리케이션을 배포하려면 먼저 구축해야 합니다. 시연을 위해서 데이터 원본 연결 정보를 기록하는 일부 *디버그* 로깅을 활성화합니다.

데이터베이스 연결 세부 정보에 대한 *디버그* 로깅을 활성화하려면 *application.yml*의 끝에 아래 yaml 속성을 추가합니다.

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

애플리케이션 예제는 gradle을 사용하여 Spring Boot 실행 가능한 jar로 애플리케이션을 빌드합니다. 실행 가능한 jar은 Pivotal Cloud Foundry 인스턴스에 배포됩니다. 애플리케이션을 빌드하려면:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>애플리케이션 배포

`cf push` 명령을 사용하여 Pivotal Cloud Foundry 인스턴스에 애플리케이션을 배포합니다.

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

`cf push`의 밖에 표시된 *경로*의 값을 복사합니다. 경로는 실행 중인 애플리케이션에 액세스하는 데 사용할 URL입니다. 예: 

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


애플리케이션을 배포한 후 애플리케이션에서 사용하는 연결 URL을 확인하기 위해 애플리케이션의 로그를 볼 수 있습니다. 아래 명령은 애플리케이션의 로그를 표시하고 `grep`을 사용하여 *jdbcUrl* 구성을 검색합니다.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

애플리케이션은 메모리 내 데이터베이스인 *h2:mem:testdb*를 사용합니다. Spring 애플리케이션은 메모리 내 데이터베이스 종속성이 클래스 경로에 있고 [자동 구성](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html)이 활성화된 경우 메모리 내 데이터베이스를 사용하도록 자동으로 구성됩니다. 애플리케이션 예제는 [h2 메모리 내 데이터베이스 종속성이 구성](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49)되어 있으며 [src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8)에서 자동 구성이 활성화되어 있습니다.

애플리케이션의 경로를 사용하여 브라우저에서 이동합니다. 경로 또는 URL은 `cf push` 명령의 출력에 표시됩니다.

> [!TIP]
> `cf apps`를 실행하여 애플리케이션의 URL을 표시할 수도 있습니다.

브라우저를 사용하여 애플리케이션으로 이동하면 기존 앨범을 삭제하고 새 항목을 만들어 상호 작용합니다. 애플리케이션 예제는 메모리 내 데이터베이스를 사용하여 변경 내용을 저장합니다. 애플리케이션이 일부 [기본 데이터](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json)를 사용하여 채워진 것을 확인할 수 있습니다. 

![기본 데이터가 있는 봄 음악 앱](media/music-app.png)

애플리케이션에서 메모리 내 데이터베이스를 사용하므로 애플리케이션이 다시 시작되거나 다시 배포되는 경우 변경 내용이 유지되지 않습니다. 변경 내용이 유지되지 않는 것을 표시하려면 변경 내용을 만든 후 `cf restage`를 사용하여 애플리케이션을 다시 스테이징합니다.

```cmd
cf restage spring-music
```

애플리케이션이 다시 스테이징된 후 동일한 URL을 사용하여 브라우저에서 이동합니다. 수행한 변경 내용이 사라지고 기본 데이터가 표시됩니다.

![기본 데이터가 있는 봄 음악 앱](media/music-app.png)

## <a name="create-a-database"></a>데이터베이스 만들기

Open Service Broker를 사용하여 Azure에 영구 데이터베이스를 만들려면 `cf create-service` 명령을 사용합니다. 아래 명령은 *eastus* 지역의 리소스 그룹 *MyResourceGroup*에서 Azure의 PostgreSQL 데이터베이스를 프로비전합니다. *resourceGroup*, *위치* 및 다른 Azure 관련 JSON 매개 변수에 대한 자세한 내용은 [PostgreSQL 모듈 참조 설명서](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision)에서 사용할 수 있습니다.

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

데이터베이스는 *mypgsql*이라는 서비스로 Pivotal Cloud Foundry 인스턴스에서 노출됩니다. 데이터베이스에서 몇 분 정도 소요되는 프로비전을 완료하면 애플리케이션에 바인딩할 수 있습니다. 데이터베이스에서 프로비전을 완료했는지 확인하려면 `cf services` 명령을 사용합니다.

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

서비스에 대한 *마지막 작업* 값은 프로비전이 완료되면 *생성 성공*이 됩니다.

## <a name="bind-the-database-to-your-application"></a>애플리케이션에 데이터베이스 바인딩

`bind-service` 명령을 사용하여 애플리케이션에 서비스를 바인딩합니다.

```cmd
cf bind-service spring-music mypgsql
```

애플리케이션에 서비스를 바인딩한 후 변경 내용을 적용하려면 애플리케이션을 다시 스테이징해야 합니다.

```cmd
cf restage spring-music
```

애플리케이션이 [Spring 클라우드 커넥터를 사용하도록 구성](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46)됩니다. 이를 통해 Pivotal Cloud Foundry 인스턴스가 애플리케이션의 데이터 원본에서 [자동 재구성](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto)을 사용할 수 있습니다. 이 경우 Pivotal Cloud Foundry 인스턴스는 애플리케이션이 다시 스테이징될 때 데이터 원본에 대해 바인딩된 서비스를 사용하도록 애플리케이션을 자동으로 다시 구성합니다.

애플리케이션이 다시 스테이징되면 데이터를 저장하기 위해 메모리 내 데이터베이스 대신 *mypgsql*을 사용합니다.

이제 수행한 모든 변경 내용이 다시 시작 및 다시 배포 시에도 유지됩니다. 애플리케이션의 로그를 다시 확인하여 애플리케이션에서 사용하는 연결 URL을 확인할 수도 있습니다.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

두 개의 항목이 있습니다.

* *h2:mem:testdb*의 원래 값
* 애플리케이션이 다시 스테이징된 경우 PostgreSQL 데이터베이스에 대한 업데이트된 값

데이터가 PostgreSQL 데이터베이스로 유지되는지 확인하려면 브라우저에서 애플리케이션으로 이동하고, 변경한 다음, 애플리케이션을 다시 스테이징합니다.

```cmd
cf restage spring-music
```

애플리케이션이 다시 스테이징된 후 동일한 URL을 사용하여 브라우저에서 이동합니다. 변경 내용이 여전히 유지됩니다.

## <a name="cleanup"></a>정리

데이터베이스에서 애플리케이션을 분리하려는 경우 `cf unbind-service` 명령을 사용하여 바인딩 해제할 수 있습니다.

```cmd
cf unbind-service spring-music mypgsql
```

서비스에 애플리케이션을 바인딩하는 것과 비슷하게 이러한 변경 내용을 적용하려면 애플리케이션을 다시 스테이징해야 합니다. 이 작업은 *mypgsql* 및 애플리케이션을 그대로 유지하지만 애플리케이션은 *mypgsql* 대신 메모리 내 데이터베이스를 사용하기 시작합니다.

데이터베이스를 삭제하기 위해 `cf delete-service` 명령을 사용할 수 있습니다. *이 작업을 실행 취소할 수 없으므로 계속하기 전에 데이터베이스 삭제를 원하는지 확인해야 합니다.*

```cmd
cf delete-service mypgsql
```

Pivotal Cloud Foundry 인스턴스에서 애플리케이션을 제거하려면:
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Pivotal Cloud Foundry에 애플리케이션 배포뿐만 아니라 Open Service Broker for Azure를 사용하여 데이터베이스 만들기를 설명했습니다. 또한 Pivotal Cloud Foundry 인스턴스 내에서 애플리케이션에 데이터베이스 바인딩을 설명했습니다. Azure의 Cloud Foundry에 애플리케이션을 배포하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Azure의 Cloud Foundry](../virtual-machines/linux/cloudfoundry-get-started.md)
* [Microsoft Azure의 Cloud Foundry에 첫 번째 앱 배포](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)