---
title: Azure Container Registry의 리포지토리에 대 한 사용 권한
description: 레지스트리의 특정 리포지토리로 범위가 지정 된 토큰을 만들어 이미지를 끌어오거나 푸시 하거나 다른 작업을 수행 합니다.
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444310"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>리포지토리 범위 사용 권한이 있는 토큰 만들기

이 문서에서는 토큰 및 범위 맵을 만들어 컨테이너 레지스트리에서 리포지토리 범위 사용 권한을 관리 하는 방법을 설명 합니다. 레지스트리 소유자는 토큰을 만들어 사용자 또는 서비스에 리포지토리에 대해 제한 된 시간 제한 액세스를 제공 하 여 이미지를 끌어오거나 푸시 하거나 다른 작업을 수행할 수 있습니다. 토큰은 다른 레지스트리 [인증 옵션](container-registry-authentication.md)(전체 레지스트리에 대 한 사용 권한 범위) 보다 세분화 된 사용 권한을 제공 합니다. 

토큰을 만드는 시나리오는 다음과 같습니다.

* 개별 토큰이 있는 IoT 장치에서 리포지토리에서 이미지를 끌어올 수 있도록 허용
* 외부 조직을 특정 리포지토리에 대 한 사용 권한 제공 
* 조직의 다른 사용자 그룹에 대 한 리포지토리 액세스를 제한 합니다. 예를 들어 특정 리포지토리를 대상으로 하는 이미지를 작성 하는 개발자에 게 쓰기 및 읽기 액세스를 제공 하 고 해당 리포지토리에서 배포 하는 팀에 대 한 읽기 권한을 제공 합니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 이 기능은 **프리미엄** 컨테이너 레지스트리에서만 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대 한 자세한 내용은 [Azure Container Registry sku](container-registry-skus.md)를 참조 하세요.
* 현재 서비스 주체 또는 관리 id와 같은 Azure Active Directory id에 리포지토리 범위 사용 권한을 할당할 수 없습니다.

## <a name="concepts"></a>개념

리포지토리 범위 사용 권한을 구성 하려면 연결 된 *범위 맵을*사용 하 여 *토큰* 을 만듭니다. 

* 생성 된 암호와 함께 **토큰** 을 사용 하면 사용자가 레지스트리를 사용 하 여 인증할 수 있습니다. 토큰 암호에 대 한 만료 날짜를 설정 하거나 언제 든 지 토큰을 사용 하지 않도록 설정할 수 있습니다.  

  토큰을 사용 하 여 인증 한 후 사용자 또는 서비스는 하나 이상의 리포지토리로 범위가 지정 된 하나 이상의 *작업* 을 수행할 수 있습니다.

  |작업  |Description  | 예제 |
  |---------|---------|--------|
  |`content/delete`    | 리포지토리에서 데이터 제거  | 리포지토리 또는 매니페스트 삭제 |
  |`content/read`     |  리포지토리에서 데이터 읽기 |  아티팩트 풀 |
  |`content/write`     |  리포지토리에 데이터 쓰기     | `content/read`와 함께 사용 하 여 아티팩트 푸시 |
  |`metadata/read`    | 리포지토리에서 메타 데이터 읽기   | 태그 또는 매니페스트 나열 |
  |`metadata/write`     |  리포지토리에 메타 데이터 쓰기  | 읽기, 쓰기 또는 삭제 작업을 사용 하거나 사용 하지 않도록 설정 |

* **범위 맵은** 토큰에 적용 하는 리포지토리 권한을 그룹화 하 고 다른 토큰에 다시 적용할 수 있습니다. 모든 토큰은 단일 범위 맵에 연결 됩니다. 

   범위 맵 사용:

    * 저장소 집합에 대해 동일한 권한으로 여러 토큰 구성
    * 범위 맵에서 리포지토리 작업을 추가 또는 제거 하거나 다른 범위 맵을 적용 하는 경우 토큰 권한 업데이트 

  또한 Azure Container Registry은 모든 리포지토리에 대해 고정 된 권한으로 적용할 수 있는 여러 시스템 정의 범위 맵을 제공 합니다.

다음 이미지는 토큰과 범위 맵 간의 관계를 보여 줍니다. 

![레지스트리 토큰 및 범위 맵](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>사전 요구 사항

* 토큰을 만들고 관리 하는 **Azure CLI** Azure CLI 명령은 Azure CLI 버전 2.0.76 이상에서 사용할 수 있습니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
* **Docker** -레지스트리를 인증 하 여 이미지를 끌어오거나 푸시 하려면 로컬 Docker가 설치 되어 있어야 합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에 대한 설치 지침을 제공합니다.
* **Container registry** -없는 경우 Azure 구독에서 프리미엄 컨테이너 레지스트리를 만들거나 기존 레지스트리를 업그레이드 합니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다. 

## <a name="create-token---cli"></a>토큰 만들기-CLI

### <a name="create-token-and-specify-repositories"></a>토큰 만들기 및 리포지토리 지정

[Az acr token create][az-acr-token-create] 명령을 사용 하 여 토큰을 만듭니다. 토큰을 만들 때 각 리포지토리에서 하나 이상의 리포지토리 및 관련 작업을 지정할 수 있습니다. 리포지토리가 아직 레지스트리에 있을 필요는 없습니다. 기존 범위 맵을 지정 하 여 토큰을 만들려면 다음 섹션을 참조 하세요.

다음 예제에서는 `content/write` 및 `content/read``samples/hello-world` 리포지토리에 대해 다음 권한을 사용 하 여 레지스트리 *myregistry* 에 토큰을 만듭니다. 기본적으로이 명령은 기본 토큰 상태를 `enabled`으로 설정 하지만 언제 든 지 상태를 `disabled`으로 업데이트할 수 있습니다.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

출력은 두 개의 생성 된 암호를 포함 하 여 토큰에 대 한 세부 정보를 표시 합니다. 나중에 인증에 사용할 수 있도록 암호를 안전한 장소에 저장 하는 것이 좋습니다. 암호를 다시 검색할 수 없지만 새 암호를 생성할 수 있습니다.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

출력에는 생성 된 명령의 범위 맵에 대 한 세부 정보가 포함 됩니다. `MyToken-scope-map`이라는 범위 맵을 사용 하 여 다른 토큰에 동일한 리포지토리 작업을 적용할 수 있습니다. 또는 나중에 범위 맵을 업데이트 하 여 연결 된 토큰의 사용 권한을 변경 합니다.

### <a name="create-token-and-specify-scope-map"></a>토큰을 만들고 범위 맵을 지정 합니다.

토큰을 만드는 다른 방법은 기존 범위 맵을 지정 하는 것입니다. 범위 맵이 아직 없는 경우 리포지토리 및 관련 작업을 지정 하 여 먼저 범위 맵을 만듭니다. 그런 다음 토큰을 만들 때 범위 맵을 지정 합니다. 

범위 맵을 만들려면 [az acr scope-map create][az-acr-scope-map-create] 명령을 사용 합니다. 다음 명령을 사용 하 여 이전에 사용 된 `samples/hello-world` 리포지토리에 대해 동일한 권한으로 범위 맵을 만듭니다. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

[Az acr token create][az-acr-token-create] 를 실행 하 여 토큰을 만들고 *MyScopeMap* 범위 맵을 지정 합니다. 이전 예제와 같이 명령은 기본 토큰 상태를 `enabled`으로 설정 합니다.

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

출력은 두 개의 생성 된 암호를 포함 하 여 토큰에 대 한 세부 정보를 표시 합니다. 나중에 인증에 사용할 수 있도록 암호를 안전한 장소에 저장 하는 것이 좋습니다. 암호를 다시 검색할 수 없지만 새 암호를 생성할 수 있습니다.

## <a name="create-token---portal"></a>토큰 만들기-포털

Azure Portal를 사용 하 여 토큰 및 범위 맵을 만들 수 있습니다. `az acr token create` CLI 명령과 마찬가지로, 하나 이상의 리포지토리 및 관련 작업을 지정 하 여 토큰을 만들 때 기존 범위 맵을 적용 하거나 범위 맵을 만들 수 있습니다. 리포지토리가 아직 레지스트리에 있을 필요는 없습니다. 

다음 예에서는 토큰을 만들고 `samples/hello-world` 리포지토리에서 `content/write` 및 `content/read`에 대 한 다음 권한을 사용 하 여 범위 맵을 만듭니다.

1. 포털에서 컨테이너 레지스트리로 이동 합니다.
1. **서비스**에서 **토큰 (미리 보기) > + 추가**를 선택 합니다.
  포털에서 토큰 ![만들기](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. 토큰 이름을 입력 합니다.
1. **범위 지도**에서 **새로 만들기**를 선택 합니다.
1. 범위 맵을 구성 합니다.
    1. 범위 맵에 대 한 이름 및 설명을 입력 합니다. 
    1. **리포지토리**에서 `samples/hello-world`를 입력 하 고 **사용 권한**아래에서 `content/read` 및 `content/write`를 선택 합니다. 그런 다음 **+ 추가**를 선택 합니다.  
    ![포털에서 범위 맵을 만듭니다](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. 리포지토리 및 사용 권한을 추가한 후 **추가** 를 선택 하 여 범위 맵을 추가 합니다.
1. **사용** 의 기본 토큰 **상태** 를 적용 한 다음 **만들기**를 선택 합니다.

토큰의 유효성을 검사 하 고 만든 후 토큰 **정보는 토큰 화면에** 표시 됩니다.

### <a name="add-token-password"></a>토큰 암호 추가

토큰을 만든 후 암호를 생성 합니다. 레지스트리를 사용 하 여 인증 하려면 토큰을 사용 하도록 설정 하 고 유효한 암호를 사용 해야 합니다.

하나 또는 두 개의 암호를 생성 하 고 각 암호에 대 한 만료 날짜를 설정할 수 있습니다. 

1. 포털에서 컨테이너 레지스트리로 이동 합니다.
1. **서비스**에서 **토큰 (미리 보기)** 을 선택 하 고 토큰을 선택 합니다.
1. 토큰 세부 정보에서 **password1** 또는 **password2**를 선택 하 고 생성 아이콘을 선택 합니다.
1. 암호 화면에서 필요에 따라 암호에 대 한 만료 날짜를 설정 하 고 **생성**을 선택 합니다.
1. 암호를 생성 한 후에는 암호를 복사 하 여 안전한 위치에 저장 합니다. 화면을 닫은 후 생성 된 암호를 검색할 수 없지만 새 암호를 생성할 수 있습니다.

    ![포털에서 토큰 암호 만들기](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>토큰으로 인증

사용자 또는 서비스가 토큰을 사용 하 여 대상 레지스트리로 인증 하는 경우 토큰 이름을 사용자 이름으로 제공 하 고 생성 된 암호 중 하나를 제공 합니다. 인증 방법은 구성 된 작업 또는 토큰과 연결 된 작업에 따라 달라 집니다.

|작업  |인증 방법  |
  |---------|---------|
  |`content/delete`    | Azure CLI `az acr repository delete` |
  |`content/read`     |  `docker login`<br/><br/>Azure CLI `az acr login`  |
  |`content/write`     |  `docker login`<br/><br/>Azure CLI `az acr login`     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>Azure CLI `az acr repository show-manifests`   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>Azure CLI `az acr repository update` |

## <a name="examples-use-token"></a>예제: 토큰 사용

다음 예제에서는이 문서의 앞부분에서 만든 토큰을 사용 하 여 리포지토리에 대 한 일반적인 작업을 수행 합니다. 이미지 밀어넣기 및 끌어오기, 이미지 삭제 및 리포지토리 태그 나열. 토큰은 처음에 `samples/hello-world` 리포지토리의 푸시 권한 (`content/write` 및 `content/read` 작업)으로 설정 되었습니다.

### <a name="pull-and-tag-test-images"></a>테스트 이미지 끌어오기 및 태그

다음 예제에서는 `hello-world` 및 Docker 허브에서 이미지를 `alpine` 하 고 레지스트리 및 리포지토리에 대 한 태그를 합니다.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>토큰을 사용 하 여 인증

`docker login`를 실행 하 여 레지스트리를 인증 하 고, 토큰 이름을 사용자 이름으로 제공 하 고, 암호 중 하나를 제공 합니다. 토큰에 `Enabled` 상태가 있어야 합니다.

다음 예에서는 bash 셸에 대해 형식이 지정 되 고 환경 변수를 사용 하 여 값을 제공 합니다.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

출력은 성공적인 인증을 표시 해야 합니다.

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>레지스트리에 이미지 푸시

성공적으로 로그인 한 후 태그가 지정 된 이미지를 레지스트리에 푸시합니다. 토큰에 `samples/hello-world` 리포지토리로 이미지를 푸시할 수 있는 권한이 있기 때문에 다음 푸시가 성공 합니다.

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

토큰에 `samples/alpine` 리포지토리에 대 한 권한이 없으므로 다음과 같은 `requested access to the resource is denied`오류가 발생 하 여 다음과 같은 푸시 시도가 실패 합니다.

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>밀어넣기/끌어오기 권한 변경

토큰의 사용 권한을 업데이트 하려면 연결 된 범위 맵에서 사용 권한을 업데이트 합니다. 업데이트 된 범위 맵은 연결 된 모든 토큰에 즉시 적용 됩니다. 

예를 들어 `MyToken-scope-map`를 `content/write`로 업데이트 하 고 `samples/alpine` 리포지토리에서 작업을 `content/read` 하 고 `content/write` 리포지토리에서 `samples/hello-world` 동작을 제거 합니다.  

Azure CLI를 사용 하려면 [az acr scope-map update][az-acr-scope-map-update] 를 실행 하 여 범위 맵을 업데이트 합니다.

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

Azure Portal에서 다음을 수행합니다.

1. 컨테이너 레지스트리로 이동 합니다.
1. **서비스**에서 **범위 맵 (미리 보기)** 을 선택 하 고 업데이트할 범위 맵을 선택 합니다.
1. **리포지토리**에서 `samples/alpine`를 입력 하 고 **사용 권한**아래에서 `content/read` 및 `content/write`를 선택 합니다. 그런 다음 **+ 추가**를 선택 합니다.
1. **리포지토리**에서 `samples/hello-world`을 선택 하 고 **사용 권한**아래에서 `content/write`를 선택 취소 합니다. 그런 다음 **저장**을 선택합니다.

범위 맵을 업데이트 한 후 다음 푸시는 성공 합니다.

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

범위 맵에는 `samples/hello-world` 리포지토리에 대 한 `content/read` 권한만 있으므로 이제 `samples/hello-world` 리포지토리에 대 한 푸시 시도가 실패 합니다.
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

범위 맵은 두 리포지토리에 대 한 `content/read` 권한을 제공 하므로 두 리포지토리에서 이미지를 모두 성공적으로 끌어옵니다.

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>이미지 삭제

`alpine` 리포지토리에 `content/delete` 작업을 추가 하 여 범위 맵을 업데이트 합니다. 이 작업을 통해 리포지토리의 이미지를 삭제 하거나 전체 리포지토리를 삭제할 수 있습니다.

간단히 하기 위해 범위 맵을 업데이트 하려면 [az acr scope-map update][az-acr-scope-map-update] 명령만 표시 합니다.

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

포털을 사용 하 여 범위 맵을 업데이트 하려면 이전 섹션을 참조 하세요.

다음 [az acr repository delete][az-acr-repository-delete] 명령을 사용 하 여 `samples/alpine` 리포지토리를 삭제 합니다. 이미지 또는 리포지토리를 삭제 하기 위해 토큰은 `docker login`를 통해 인증 하지 않습니다. 대신 토큰의 이름과 암호를 명령에 전달 합니다. 다음 예제에서는이 문서의 앞부분에서 만든 환경 변수를 사용 합니다.

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>리포지토리 태그 표시 

`hello-world` 리포지토리에 `metadata/read` 작업을 추가 하 여 범위 맵을 업데이트 합니다. 이 작업을 통해 리포지토리의 매니페스트 및 태그 데이터를 읽을 수 있습니다.

간단히 하기 위해 범위 맵을 업데이트 하려면 [az acr scope-map update][az-acr-scope-map-update] 명령만 표시 합니다.

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

포털을 사용 하 여 범위 맵을 업데이트 하려면 이전 섹션을 참조 하세요.

`samples/hello-world` 리포지토리에서 메타 데이터를 읽으려면 [az acr repository show-manifest][az-acr-repository-show-manifests] 또는 [az acr repository show-tags][az-acr-repository-show-tags] 명령을 실행 합니다. 

메타 데이터를 읽으려면 토큰은 `docker login`를 통해 인증 하지 않습니다. 대신 토큰의 이름과 암호를 두 명령에 전달 합니다. 다음 예제에서는이 문서의 앞부분에서 만든 환경 변수를 사용 합니다.

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

샘플 출력:

```console
[
  "v1"
]
```
## <a name="manage-tokens-and-scope-maps"></a>토큰 및 범위 맵 관리

### <a name="list-scope-maps"></a>범위 맵 나열

[Az acr scope-map list][az-acr-scope-map-list] 명령 또는 포털의 **범위 맵 (미리 보기)** 화면을 사용 하 여 레지스트리에 구성 된 모든 범위 맵을 나열 합니다. 다음은 그 예입니다.

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

출력은 사용자가 정의한 범위 맵과 토큰을 구성 하는 데 사용할 수 있는 여러 시스템 정의 범위 맵을 보여 줍니다.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>토큰 세부 정보 표시

토큰에 대 한 세부 정보 (예: 상태 및 암호 만료 날짜)를 보려면 [az acr token show][az-acr-token-show] 명령을 실행 하거나 포털의 **토큰 (미리 보기)** 화면에서 토큰을 선택 합니다. 다음은 그 예입니다.

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

[Az acr token list][az-acr-token-list] 명령 또는 포털의 **토큰 (미리 보기)** 화면을 사용 하 여 레지스트리에 구성 된 모든 토큰을 나열할 수 있습니다. 다음은 그 예입니다.

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>토큰에 대 한 암호 생성

토큰 암호가 없거나 새 암호를 생성 하려는 경우 [az acr token credential generate][az-acr-token-credential-generate] 명령을 실행 합니다. 

다음 예에서는 만료 기간이 30 일인 *Mytoken* 토큰에 대해 password1의 새 값을 생성 합니다. `TOKEN_PWD`환경 변수에 암호를 저장 합니다. 이 예제는 bash 셸에 대해 형식이 지정 됩니다.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Azure Portal를 사용 하 여 토큰 암호를 생성 하려면이 문서의 앞부분에 있는 [토큰 만들기-포털](#create-token---portal) 의 단계를 참조 하세요.

### <a name="update-token-with-new-scope-map"></a>새 범위 맵으로 토큰 업데이트

다른 범위 맵으로 토큰을 업데이트 하려면 [az acr token update][az-acr-token-update] 를 실행 하 고 새 범위 맵을 지정 합니다. 다음은 그 예입니다.

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

포털의 **토큰 (미리 보기)** 화면에서 토큰을 선택 하 고 **범위 지도**에서 다른 범위 맵을 선택 합니다.

> [!TIP]
> 새 범위 맵으로 토큰을 업데이트 한 후 새 토큰 암호를 생성할 수 있습니다. [Az acr token credential generate][az-acr-token-credential-generate] 명령을 사용 하거나 Azure Portal에서 토큰 암호를 다시 생성 합니다.

## <a name="disable-or-delete-token"></a>토큰 비활성화 또는 삭제

사용자 또는 서비스에 대 한 토큰 자격 증명을 일시적으로 사용 하지 않도록 설정 해야 할 수도 있습니다. 

Azure CLI를 사용 하 여 [az acr token update][az-acr-token-update] 명령을 실행 하 여 `status`을 `disabled`로 설정 합니다.

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

포털의 토큰 **(미리 보기)** 화면에서 토큰을 선택 하 고 **상태**아래에서 **사용 안 함** 을 선택 합니다.

자격 증명을 사용 하는 모든 사용자가 영구적으로 액세스를 무효화 하기 위해 토큰을 삭제 하려면 [az acr token delete][az-acr-token-delete] 명령을 실행 합니다. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

포털의 토큰 **(미리 보기)** 화면에서 토큰을 선택 하 고 **취소**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

* 범위 맵과 토큰을 관리 하려면 [az acr scope-map][az-acr-scope-map] 및 [az acr token][az-acr-token] 명령 그룹에서 추가 명령을 사용 합니다.
* Azure Active Directory id, 서비스 주체 또는 관리자 계정을 사용 하 여 Azure container registry를 사용 하 여 인증 하는 다른 옵션은 [인증 개요](container-registry-authentication.md) 를 참조 하세요.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
