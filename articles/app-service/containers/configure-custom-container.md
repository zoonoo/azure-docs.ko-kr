---
title: 사용자 지정 Linux 컨테이너 구성
description: Azure App Service에서 사용자 지정 Linux 컨테이너를 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 57281bedb34078dff6878d69be1bfe7f7300f545
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905802"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Azure App Service에 대 한 사용자 지정 Linux 컨테이너 구성

이 문서에서는 Azure App Service에서 실행 되도록 사용자 지정 Linux 컨테이너를 구성 하는 방법을 보여 줍니다.

이 가이드에서는 App Service의 Linux 앱 컨테이너 화에 대 한 주요 개념 및 지침을 제공 합니다. Azure App Service 사용한 적이 없는 경우 먼저 [사용자 지정 컨테이너 빠른](quickstart-docker-go.md) 시작 및 [자습서](tutorial-custom-docker-image.md) 를 따르세요. [다중 컨테이너 앱 빠른](quickstart-multi-container.md) 시작 및 [자습서](tutorial-multi-container-app.md)도 있습니다.

## <a name="configure-port-number"></a>포트 번호 구성

사용자 지정 이미지의 웹 서버는 80 이외의 포트를 사용할 수 있습니다. 앱 설정을 사용 하 여 사용자 지정 컨테이너에서 사용 하는 포트에 대 한 정보를 Azure에 알려 줍니다 `WEBSITES_PORT` . [이 자습서의 Python 샘플](https://github.com/Azure-Samples/docker-django-webapp-linux)에 대한 GitHub 페이지에서 `WEBSITES_PORT`를 _8000_으로 설정해야 함을 보여 줍니다. Cloud Shell에서 명령을 실행 하 여 설정할 수 있습니다 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) . 예를 들어:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>환경 변수 구성

사용자 지정 컨테이너는 외부에서 제공 해야 하는 환경 변수를 사용할 수 있습니다. Cloud Shell 명령을 실행 하 여에서 전달할 수 있습니다 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) . 예를 들어:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

이 메서드는 단일 컨테이너 앱 또는 다중 컨테이너 앱에 대해 모두 작동 하며 환경 변수는 *docker-compose.ci.build.yml* 파일에 지정 됩니다.

## <a name="use-persistent-shared-storage"></a>영구 공유 저장소 사용

앱의 파일 시스템에서 */home* 디렉터리를 사용 하 여 다시 시작할 때마다 파일을 유지 하 고 인스턴스 간에 공유할 수 있습니다. `/home`컨테이너 앱이 영구 저장소에 액세스할 수 있도록 앱의가 제공 됩니다.

영구 저장소를 사용 하지 않도록 설정 하면 디렉터리에 대 한 쓰기가 `/home` 앱을 다시 시작 하거나 여러 인스턴스에 걸쳐 지속 되지 않습니다. 유일한 예외는 `/home/LogFiles` Docker 및 컨테이너 로그를 저장 하는 데 사용 되는 디렉터리입니다. 영구 저장소를 사용 하도록 설정 하면 디렉터리에 대 한 모든 쓰기 `/home` 는 유지 되며 확장 된 앱의 모든 인스턴스에서 액세스할 수 있습니다.

기본적으로 영구 저장소는 *사용 하도록* 설정 되어 있으며 설정은 응용 프로그램 설정에 표시 되지 않습니다. 이 기능을 사용 하지 않도록 설정 하려면 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` Cloud Shell에서 명령을 실행 하 여 앱 설정을 설정 합니다 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) . 예를 들어:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> 사용자 [고유의 영구 저장소를 구성할](how-to-serve-content-from-azure-storage.md)수도 있습니다.

## <a name="enable-ssh"></a>SSH 사용

SSH를 사용하면 컨테이너와 클라이언트 간의 보안 통신을 설정할 수 있습니다. 사용자 지정 컨테이너가 SSH를 지원 하도록 하려면 Dockerfile 자체에 추가 해야 합니다.

> [!TIP]
> 모든 기본 제공 Linux 컨테이너는 해당 이미지 리포지토리에 SSH 명령을 추가 했습니다. [Node.js 10.14 리포지토리](https://github.com/Azure-App-Service/node/blob/master/10.14) 를 사용 하 여 다음 지침을 수행 하 여 어떻게 활성화 되어 있는지 확인할 수 있습니다.

- [실행](https://docs.docker.com/engine/reference/builder/#run) 명령을 사용 하 여 SSH 서버를 설치 하 고 루트 계정에 대 한 암호를로 설정 `"Docker!"` 합니다. 예를 들어 [알파인 Linux](https://hub.docker.com/_/alpine)를 기반으로 하는 이미지의 경우 다음 명령이 필요 합니다.

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    이 구성은 컨테이너에 대 한 외부 연결을 허용 하지 않습니다. SSH는를 통해서만 사용할 수 `https://<app-name>.scm.azurewebsites.net` 있으며 게시 자격 증명으로 인증 됩니다.

- [이 sshd_config 파일](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) 을 이미지 리포지토리에 추가 하 고 [복사](https://docs.docker.com/engine/reference/builder/#copy) 명령을 사용 하 여 파일을 */etc/ssh/* 디렉터리에 복사 합니다. *Sshd_config* 파일에 대 한 자세한 내용은 [openbsd 설명서](https://man.openbsd.org/sshd_config)를 참조 하세요.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > *sshd_config* 파일에 다음이 항목되어야 합니다.
    > - `Ciphers`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`aes128-cbc,3des-cbc,aes256-cbc`
    > - `MACs`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`hmac-sha1,hmac-sha1-96`

- 표시 명령을 [사용 하 여 컨테이너](https://docs.docker.com/engine/reference/builder/#expose) 에서 포트 2222를 엽니다. 루트 암호는 알려져 있지만 포트 2222은 인터넷에서 액세스할 수 없습니다. 개인 가상 네트워크의 브리지 네트워크 내 컨테이너 에서만 액세스할 수 있습니다.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 컨테이너의 시작 스크립트에서 SSH 서버를 시작 합니다.

    ```bash
    /usr/sbin/sshd
    ```

    예제는 기본 [Node.js 10.14 컨테이너가](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) SSH 서버를 시작 하는 방법을 참조 하세요.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>다중 컨테이너 앱 구성

- [Docker Compose에서 영구적 저장소 사용](#use-persistent-storage-in-docker-compose)
- [미리 보기 제한 사항](#preview-limitations)
- [Docker Compose 옵션](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Docker Compose에서 영구적 저장소 사용

WordPress 같은 다중 컨테이너 앱은 영구적 저장소가 제대로 작동 해야 합니다. 이 기능을 사용 하도록 설정 하려면 Docker Compose 구성이 컨테이너 *외부* 의 저장소 위치를 가리켜야 합니다. 컨테이너 내부의 저장소 위치는 앱을 다시 시작 하는 이후 변경 내용을 유지 하지 않습니다.

`WEBSITES_ENABLE_APP_SERVICE_STORAGE`Cloud Shell에서 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용 하 여 앱 설정을 설정 하 여 영구 저장소를 사용 하도록 설정 합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

*Docker-compose.ci.build.yml* 파일에서 `volumes` 옵션을에 매핑합니다 `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME`은 앱의 영구 스토리지에 매핑되는 App Service의 환경 변수입니다. 예를 들어:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>미리 보기 제한 사항

다중 컨테이너는 현재 미리 보기로 제공 됩니다. 다음 App Service 플랫폼 기능은 지원 되지 않습니다.

- 인증/권한 부여
- 관리 ID

### <a name="docker-compose-options"></a>Docker Compose 옵션

다음 목록에서는 지원 되거나 지원 되지 않는 Docker Compose 구성 옵션을 보여 줍니다.

#### <a name="supported-options"></a>지원되는 옵션

- 명령을 사용합니다.
- entrypoint
- 환경
- 이미지
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>지원되지 않는 옵션

- build(허용 안 함)
- depends_on(무시됨)
- networks(무시됨)
- secrets(무시됨)
- 포트 80 및 8080 이외의 포트(무시됨)

> [!NOTE]
> 명시적으로 호출 되지 않는 다른 모든 옵션은 공개 미리 보기에서 무시 됩니다.

## <a name="configure-vnet-integration"></a>VNet 통합 구성

VNet 통합에 사용자 지정 컨테이너를 사용 하려면 컨테이너를 추가로 구성 해야 할 수 있습니다. [Azure Virtual Network에 앱 통합](../web-sites-integrate-with-vnet.md)을 참조하세요.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 프라이빗 컨테이너 리포지토리에서 배포](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [자습서: 다중 컨테이너 WordPress 앱](tutorial-multi-container-app.md)
