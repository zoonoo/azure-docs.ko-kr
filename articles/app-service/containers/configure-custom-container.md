---
title: 사용자 지정 Linux 컨테이너 구성
description: Azure 앱 서비스에서 사용자 지정 Linux 컨테이너를 구성하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280145"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Azure 앱 서비스에 대한 사용자 지정 Linux 컨테이너 구성

이 문서에서는 Azure App Service에서 실행되도록 사용자 지정 Linux 컨테이너를 구성하는 방법을 보여 주며 있습니다.

이 가이드는 앱 서비스에서 Linux 앱의 컨테이너화에 대한 주요 개념 및 지침을 제공합니다. Azure 앱 서비스를 사용한 적이 없는 경우 [먼저 사용자 지정 컨테이너 빠른 시작](quickstart-docker-go.md) 및 [자습서를](tutorial-custom-docker-image.md) 따릅니다. [멀티 컨테이너 응용 프로그램 빠른 시작](quickstart-multi-container.md) 및 [자습서도](tutorial-multi-container-app.md)있습니다.

## <a name="configure-port-number"></a>포트 번호 구성

사용자 지정 이미지의 웹 서버는 80이 아닌 다른 포트를 사용할 수 있습니다. 앱 설정을 사용하여 사용자 지정 컨테이너가 사용하는 `WEBSITES_PORT` 포트에 대해 Azure에 알릴 수 있습니다. [이 자습서의 Python 샘플](https://github.com/Azure-Samples/docker-django-webapp-linux)에 대한 GitHub 페이지에서 `WEBSITES_PORT`를 _8000_으로 설정해야 함을 보여 줍니다. 클라우드 셸에서 명령을 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 실행하여 설정할 수 있습니다. 예를 들어:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>환경 변수 구성

사용자 지정 컨테이너는 외부에서 제공해야 하는 환경 변수를 사용할 수 있습니다. 클라우드 셸에서 명령을 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 실행하여 전달할 수 있습니다. 예를 들어:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

이 메서드는 단일 컨테이너 앱 또는 환경 변수가 *docker-compose.yml* 파일에 지정된 다중 컨테이너 앱모두에서 작동합니다.

## <a name="use-persistent-shared-storage"></a>영구 공유 저장소 사용

앱의 파일 시스템에서 */home* directory를 사용하여 다시 시작간에 파일을 유지시키고 인스턴스 간에 공유할 수 있습니다. 컨테이너 `/home` 앱이 영구 저장소에 액세스할 수 있도록 앱이 제공됩니다.

영구 저장소를 사용하지 않도록 설정하면 앱 다시 시작하거나 여러 인스턴스에서 `/home` 디렉터리에 대한 쓰기가 유지되지 않습니다. 유일한 예외는 `/home/LogFiles` Docker 및 컨테이너 로그를 저장하는 데 사용되는 디렉터리입니다. 영구 저장소를 사용하도록 설정하면 `/home` 디렉터리에 대한 모든 쓰기가 유지되며 확장된 앱의 모든 인스턴스에서 액세스할 수 있습니다.

기본적으로 영구 저장소가 *활성화되고* 설정이 응용 프로그램 설정에 노출되지 않습니다. 비활성화하려면 Cloud Shell에서 명령을 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 실행하여 앱 설정을 설정합니다. 예를 들어:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> [사용자 고유의 영구 저장소를 구성할](how-to-serve-content-from-azure-storage.md)수도 있습니다.

## <a name="enable-ssh"></a>SSH 사용

SSH를 사용하면 컨테이너와 클라이언트 간의 보안 통신을 설정할 수 있습니다. 사용자 지정 컨테이너가 SSH를 지원하려면 Dockerfile 자체에 추가해야 합니다.

> [!TIP]
> 모든 기본 제공 Linux 컨테이너는 이미지 리포지토리에 SSH 지침을 추가했습니다. [Node.js 10.14 리포지토리를](https://github.com/Azure-App-Service/node/blob/master/10.14) 사용하여 다음 지침을 통해 활성화 방법을 확인할 수 있습니다.

- [RUN](https://docs.docker.com/engine/reference/builder/#run) 명령을 사용하여 SSH 서버를 설치하고 루트 계정의 `"Docker!"`암호를 로 설정합니다. 예를 들어 [알파인 Linux를](https://hub.docker.com/_/alpine)기반으로 하는 이미지의 경우 다음 명령이 필요합니다.

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    이 구성은 컨테이너에 대한 외부 연결을 허용하지 않습니다. SSH는 게시 `https://<app-name>.scm.azurewebsites.net` 자격 증명을 통해서만 사용할 수 있으며 인증됩니다.

- [이 sshd_config 파일을](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) 이미지 리포지토리에 추가하고 [COPY](https://docs.docker.com/engine/reference/builder/#copy) 명령을 사용하여 */etc/ssh/디렉터리에* 파일을 복사합니다. *sshd_config* 파일에 대한 자세한 내용은 [OpenBSD 문서를](https://man.openbsd.org/sshd_config)참조하십시오.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > *sshd_config* 파일에 다음이 항목되어야 합니다.
    > - `Ciphers`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`aes128-cbc,3des-cbc,aes256-cbc`
    > - `MACs`는 이 목록에 적어도 하나의 항목이 있어야 합니다.`hmac-sha1,hmac-sha1-96`

- [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 명령을 사용하여 컨테이너에서 포트 2222를 엽니다. 루트 암호는 알려져 있지만 포트 2222는 인터넷에서 액세스할 수 없습니다. 사설 가상 네트워크의 브리지 네트워크 내의 컨테이너에서만 액세스할 수 있습니다.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 컨테이너에 대한 시작 스크립트에서 SSH 서버를 시작합니다.

    ```bash
    /usr/sbin/sshd
    ```

    예를 들어 기본 [Node.js 10.14 컨테이너가](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) SSH 서버를 시작하는 방법을 참조하세요.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>다중 컨테이너 앱 구성

- [Docker 컴포지션에서 영구 저장소 사용](#use-persistent-storage-in-docker-compose)
- [미리 보기 제한 사항](#preview-limitations)
- [도커 컴포지션 옵션](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Docker 컴포지션에서 영구 저장소 사용

워드 프레스와 같은 멀티 컨테이너 애플 리 케이 션 제대로 작동 하기 위해 영구 스토리지필요. 이를 사용하려면 Docker 구성이 컨테이너 *외부의* 저장소 위치를 가리커해야 합니다. 컨테이너 내부의 저장소 위치는 앱을 다시 시작하는 것 이상으로 변경 사항이 지속되지 않습니다.

클라우드 셸에서 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` [az webapp config 앱 설정 설정](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용하여 앱 설정을 설정하여 영구 저장소를 활성화합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

*docker-compose.yml* 파일에서 `volumes` 옵션을 에 `${WEBAPP_STORAGE_HOME}`매핑합니다. 

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

다중 컨테이너는 현재 미리 보기 상태입니다. 다음 앱 서비스 플랫폼 기능은 지원되지 않습니다.

- 인증/권한 부여
- 관리 ID

### <a name="docker-compose-options"></a>도커 컴포지션 옵션

다음 목록은 지원되고 지원되지 않는 Docker 구성 구성 옵션을 보여 준다.

#### <a name="supported-options"></a>지원되는 옵션

- command
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
> 명시적으로 호출되지 않은 다른 옵션은 공개 미리 보기에서 무시됩니다.

## <a name="configure-vnet-integration"></a>VNet 통합 구성

VNet 통합이 있는 사용자 지정 컨테이너를 사용하려면 추가 컨테이너 구성이 필요할 수 있습니다. [Azure Virtual Network에 앱 통합](../web-sites-integrate-with-vnet.md)을 참조하세요.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 개인 컨테이너 리포지토리에서 배포](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [튜토리얼 : 멀티 컨테이너 워드 프레스 응용 프로그램](tutorial-multi-container-app.md)
