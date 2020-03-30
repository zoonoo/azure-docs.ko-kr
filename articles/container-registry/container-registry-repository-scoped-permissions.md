---
title: Azure 컨테이너 레지스트리의 리포지토리에 대한 사용 권한
description: 레지스트리의 특정 리포지토리로 범위가 조정된 권한이 있는 토큰을 만들어 이미지를 가져오거나 푸시하거나 다른 작업을 수행합니다.
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444310"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>리포지토리 범위 의 사용 권한이 있는 토큰 만들기

이 문서에서는 컨테이너 레지스트리에서 리포지토리 범위 권한을 관리하기 위해 토큰 및 범위 맵을 만드는 방법에 대해 설명합니다. 레지스트리 소유자는 토큰을 생성하여 사용자 또는 서비스에 이미지를 가져오거나 푸시하거나 다른 작업을 수행하기 위해 리포지토리에 대한 범위제한된 액세스를 제공할 수 있습니다. 토큰은 전체 레지스트리에 대한 범위를 포함하는 다른 레지스트리 [인증 옵션보다](container-registry-authentication.md)더 세분화된 권한을 제공합니다. 

토큰을 만드는 시나리오는 다음과 같습니다.

* 개별 토큰이 있는 IoT 장치가 리포지토리에서 이미지를 가져올 수 있도록 허용
* 외부 조직에 특정 리포지토리에 대한 사용 권한 제공 
* 조직의 다른 사용자 그룹에 대한 리포지토리 액세스를 제한합니다. 예를 들어 특정 리포지토리를 대상으로 하는 이미지를 빌드하는 개발자에게 쓰기 및 읽기 액세스를 제공하고 해당 리포지토리에서 배포하는 팀에 대한 액세스를 읽습니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 이 기능은 **프리미엄** 컨테이너 레지스트리에서만 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure 컨테이너 레지스트리 SCO를](container-registry-skus.md)참조하십시오.
* 현재 서비스 주체 또는 관리되는 ID와 같은 Azure Active Directory ID에 리포지토리 범위 의 사용 권한을 할당할 수 없습니다.

## <a name="concepts"></a>개념

리포지토리 범위 의 사용 권한을 구성하려면 연결된 *범위 맵이*있는 *토큰을* 만듭니다. 

* 생성된 암호와 함께 **토큰을** 사용하면 사용자가 레지스트리를 인증할 수 있습니다. 토큰 암호의 만료 날짜를 설정하거나 언제든지 토큰을 사용하지 않도록 설정할 수 있습니다.  

  토큰으로 인증한 후 사용자 또는 서비스는 하나 이상의 리포지토리로 범위가 조정된 하나 이상의 *작업을* 수행할 수 있습니다.

  |작업  |Description  | 예제 |
  |---------|---------|--------|
  |`content/delete`    | 리포지토리에서 데이터 제거  | 리포지토리 또는 매니페스트 삭제 |
  |`content/read`     |  리포지토리에서 데이터 읽기 |  아티팩트 당기기 |
  |`content/write`     |  리포지토리에 데이터 쓰기     | 아티팩트를 푸시하는 데 사용 `content/read` |
  |`metadata/read`    | 리포지토리에서 메타데이터 읽기   | 태그 또는 매니페스트 목록 |
  |`metadata/write`     |  리포지토리에 메타데이터 쓰기  | 읽기, 쓰기 또는 삭제 작업 사용 또는 비활성화 |

* **범위 맵은** 토큰에 적용하는 리포지토리 권한을 그룹하고 다른 토큰에 다시 적용할 수 있습니다. 모든 토큰은 단일 범위 맵과 연결됩니다. 

   범위 맵의 경우:

    * 리포지토리 집합에 동일한 사용 권한이 있는 여러 토큰 구성
    * 범위 맵에서 리포지토리 작업을 추가 하거나 제거하거나 다른 범위 맵을 적용할 때 토큰 사용 권한을 업데이트합니다. 

  또한 Azure 컨테이너 레지스트리는 모든 리포지토리에 대해 고정된 사용 권한을 통해 적용할 수 있는 여러 시스템 정의 범위 맵을 제공합니다.

다음 이미지는 토큰과 범위 맵 간의 관계를 보여 주십습니다. 

![레지스트리 토큰 및 범위 맵](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>사전 요구 사항

* **Azure CLI** - 토큰을 만들고 관리하는 Azure CLI 명령은 Azure CLI 버전 2.0.76 이상에서 사용할 수 있습니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하십시오.
* **Docker** - 레지스트리를 사용하여 이미지를 가져오거나 푸시하려면 로컬 Docker 설치가 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에 대한 설치 지침을 제공합니다.
* **컨테이너 레지스트리** - 레지스트리가 없는 경우 Azure 구독에서 프리미엄 컨테이너 레지스트리를 만들거나 기존 레지스트리를 업그레이드합니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다. 

## <a name="create-token---cli"></a>토큰 만들기 - CLI

### <a name="create-token-and-specify-repositories"></a>토큰 만들기 및 리포지토리 지정

az acr 토큰 만들기 명령을 사용하여 [토큰을 만듭니다.][az-acr-token-create] 토큰을 만들 때 각 리포지토리에 하나 이상의 리포지토리 및 관련 작업을 지정할 수 있습니다. 리포지토리가 아직 레지스트리에 있을 필요는 없습니다. 기존 범위 맵을 지정하여 토큰을 만들려면 다음 섹션을 참조하세요.

다음 예제에서는 리포지토리에 `content/write` 대한 다음과 같은 권한이 있는 `samples/hello-world` 레지스트리 *myregistry에서* 토큰을 `content/read`만듭니다. 기본적으로 명령은 기본 토큰 상태를 `enabled`로 설정하지만 언제든지 상태를 `disabled` 업데이트할 수 있습니다.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

출력에는 생성된 두 개의 암호를 포함하여 토큰에 대한 세부 정보가 표시됩니다. 나중에 인증을 위해 사용할 수 있는 안전한 장소에 암호를 저장하는 것이 좋습니다. 암호를 다시 검색할 수는 없지만 새 암호를 생성할 수 있습니다.

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

출력에는 명령이 만든 범위 맵에 대한 세부 정보가 포함됩니다. 여기서 명명된 `MyToken-scope-map`범위 맵을 사용하여 동일한 리포지토리 작업을 다른 토큰에 적용할 수 있습니다. 또는 나중에 범위 맵을 업데이트하여 연결된 토큰의 사용 권한을 변경합니다.

### <a name="create-token-and-specify-scope-map"></a>토큰 만들기 및 범위 맵 지정

토큰을 만드는 또 다른 방법은 기존 범위 맵을 지정하는 것입니다. 아직 범위 맵이 없는 경우 먼저 리포지토리 및 관련 작업을 지정하여 맵맵을 만듭니다. 그런 다음 토큰을 만들 때 범위 맵을 지정합니다. 

범위 맵을 만들려면 [az acr 범위 맵 만들기][az-acr-scope-map-create] 명령을 사용합니다. 다음 명령은 이전에 사용한 리포지토리에 `samples/hello-world` 대한 동일한 사용 권한이 있는 범위 맵을 만듭니다. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

[az acr 토큰 만들기를][az-acr-token-create] 실행하여 *MyScopeMap* 범위 맵을 지정하여 토큰을 만듭니다. 이전 예제와 마찬가지로 명령은 기본 토큰 상태를 `enabled`로 설정합니다.

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

출력에는 생성된 두 개의 암호를 포함하여 토큰에 대한 세부 정보가 표시됩니다. 나중에 인증을 위해 사용할 수 있는 안전한 장소에 암호를 저장하는 것이 좋습니다. 암호를 다시 검색할 수는 없지만 새 암호를 생성할 수 있습니다.

## <a name="create-token---portal"></a>토큰 만들기 - 포털

Azure 포털을 사용하여 토큰 및 범위 맵을 만들 수 있습니다. `az acr token create` CLI 명령과 마찬가지로 하나 이상의 리포지토리 및 관련 작업을 지정하여 토큰을 만들 때 기존 범위 맵을 적용하거나 범위 맵을 만들 수 있습니다. 리포지토리가 아직 레지스트리에 있을 필요는 없습니다. 

다음 예제에서는 토큰을 만들고 `samples/hello-world` 리포지토리에 `content/write` 대한 다음 사용 권한이 있는 `content/read`범위 맵을 만듭니다.

1. 포털에서 컨테이너 레지스트리로 이동합니다.
1. **서비스에서** **토큰(미리 보기)> +추가 를 선택합니다.**
  ![포털에서 토큰 만들기](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. 토큰 이름을 입력합니다.
1. **범위 맵에서** **새 만들기를 선택합니다.**
1. 범위 맵 구성:
    1. 범위 맵에 대한 이름과 설명을 입력합니다. 
    1. **저장소에서**를 입력하고 `samples/hello-world`사용 권한 아래에서 `content/read` 를 `content/write`선택하고 을 **클릭합니다.** 그런 다음 **+Add**를 선택합니다.  
    ![포털에서 범위 맵 만들기](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. 리포지토리 및 사용 권한을 추가한 후 **추가를** 선택하여 범위 맵을 추가합니다.
1. 기본 토큰 **사용 현황을** 수락한 다음 **Enabled** **을**선택합니다.

토큰의 유효성을 검사하고 만든 후 토큰 세부 정보가 **토큰** 화면에 나타납니다.

### <a name="add-token-password"></a>토큰 암호 추가

토큰을 만든 후 암호를 생성합니다. 레지스트리를 사용하여 인증하려면 토큰을 사용하도록 설정하고 유효한 암호를 가져야 합니다.

하나 또는 두 개의 암호를 생성하고 각 암호에 대해 만료 날짜를 설정할 수 있습니다. 

1. 포털에서 컨테이너 레지스트리로 이동합니다.
1. **서비스에서** **토큰(미리 보기)을**선택하고 토큰을 선택합니다.
1. 토큰 세부 정보에서 **암호1** 또는 **암호2를**선택하고 생성 아이콘을 선택합니다.
1. 암호 화면에서 선택적으로 암호의 만료 날짜를 설정하고 **생성을**선택합니다.
1. 암호를 생성한 후 복사하여 안전한 위치에 저장합니다. 화면을 닫은 후에는 생성된 암호를 검색할 수 없지만 새 암호를 생성할 수는 있습니다.

    ![포털에서 토큰 암호 만들기](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>토큰으로 인증

사용자 또는 서비스가 토큰을 사용하여 대상 레지스트리를 인증하는 경우 토큰 이름을 사용자 이름과 생성된 암호 중 하나로 제공합니다. 인증 방법은 토큰과 연결된 구성된 작업 또는 작업에 따라 달라집니다.

|작업  |인증 방법  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`Azure CLI에서 |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`Azure CLI에서  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`Azure CLI에서     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`Azure CLI에서   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`Azure CLI에서 |

## <a name="examples-use-token"></a>예: 토큰 사용

다음 예제에서는 이 문서의 앞에서 만든 토큰을 사용하여 리포지토리에서 푸시 및 끌어오기, 이미지 삭제 및 리포지토리 태그 목록과 같은 일반적인 작업을 수행합니다. 토큰은 저장소에`content/write` `content/read` `samples/hello-world` 푸시 권한(및 작업)으로 처음에 설정되었습니다.

### <a name="pull-and-tag-test-images"></a>끌어오기 및 태그 테스트 이미지

다음 예제에서는 Docker `hello-world` Hub에서 `alpine` 및 이미지를 끌어와서 레지스트리 및 리포지토리에 태그를 붙입니다.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>토큰을 사용하여 인증

레지스트리를 사용하여 인증하고, 토큰 이름을 사용자 이름으로 제공하고, 암호 중 하나를 제공하기 위해 실행합니다. `docker login` 토큰에는 `Enabled` 상태가 있어야 합니다.

다음 예제는 bash 셸에 대해 서식이 지정되며 환경 변수를 사용하여 값을 제공합니다.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

출력에는 성공적인 인증이 표시되어야 합니다.

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>레지스트리에 이미지 푸시

로그인에 성공한 후 태그된 이미지를 레지스트리로 푸시합니다. 토큰에는 이미지를 리포지토리로 `samples/hello-world` 푸시할 수 있는 권한이 있으므로 다음 푸시가 성공합니다.

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

토큰에는 `samples/alpine` 리포지토리에 대한 권한이 없으므로 다음과 유사한 `requested access to the resource is denied`오류가 발생하므로 다음 푸시 시도가 실패합니다.

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>푸시/끌어오기 권한 변경

토큰의 권한을 업데이트하려면 연결된 범위 맵의 사용 권한을 업데이트합니다. 업데이트된 범위 맵은 연결된 모든 토큰에 즉시 적용됩니다. 

예를 들어 `MyToken-scope-map` `content/write` 리포지토리에 `samples/alpine` 대한 및 작업을 업데이트하고 `content/write` `samples/hello-world` `content/read` 리포지토리에서 작업을 제거합니다.  

Azure CLI를 사용하려면 [az acr 범위 맵 업데이트를][az-acr-scope-map-update] 실행하여 범위 맵을 업데이트합니다.

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

Azure Portal에서 다음을 수행합니다.

1. 컨테이너 레지스트리로 이동합니다.
1. **서비스에서**범위 **맵(미리 보기)을**선택하고 업데이트할 범위 맵을 선택합니다.
1. **저장소에서**를 입력하고 `samples/alpine`사용 권한 아래에서 `content/read` 를 `content/write`선택하고 을 **클릭합니다.** 그런 다음 **+Add**를 선택합니다.
1. **저장소에서**사용 권한 `samples/hello-world` 및 사용 권한 에서 `content/write`을 선택 **해제합니다.** 그런 다음 **저장을**선택합니다.

범위 맵을 업데이트한 후 다음 푸시가 성공합니다.

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

범위 맵에는 리포지토리에 대한 `content/read` `samples/hello-world` 권한만 있으므로 `samples/hello-world` 리포지토리에 대한 푸시 시도가 실패합니다.
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

범위 맵은 두 리포지토리에 대한 `content/read` 권한을 제공하기 때문에 두 리포지토리에서 이미지를 가져오는 데 성공합니다.

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>이미지 삭제

리포지토리에 작업을 `content/delete` 추가하여 `alpine` 범위 맵을 업데이트합니다. 이 작업을 수행하면 리포지토리에서 이미지를 삭제하거나 전체 리포지토리를 삭제할 수 있습니다.

간결하게 하기 위해 범위 맵을 업데이트하기 위해 [az acr 범위 맵 업데이트][az-acr-scope-map-update] 명령만 표시합니다.

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

포털을 사용하여 범위 맵을 업데이트하려면 이전 섹션을 참조하세요.

다음 [az acr 리포지토리 삭제][az-acr-repository-delete] 명령을 `samples/alpine` 사용하여 리포지토리를 삭제합니다. 이미지 또는 리포지토리를 삭제하려면 토큰은 을 `docker login`통해 인증되지 않습니다. 대신 토큰의 이름과 암호를 명령에 전달합니다. 다음 예제에서는 문서의 앞에 만든 환경 변수를 사용합니다.

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>리포지토리 태그 표시 

리포지토리에 작업을 `metadata/read` 추가하여 `hello-world` 범위 맵을 업데이트합니다. 이 작업을 수행하면 리포지토리에서 매니페스트 및 태그 데이터를 읽을 수 있습니다.

간결하게 하기 위해 범위 맵을 업데이트하기 위해 [az acr 범위 맵 업데이트][az-acr-scope-map-update] 명령만 표시합니다.

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

포털을 사용하여 범위 맵을 업데이트하려면 이전 섹션을 참조하세요.

`samples/hello-world` 리포지토리에서 메타데이터를 읽으려면 [az acr 저장소 표시 매니페스트][az-acr-repository-show-manifests] 또는 az acr 저장소 표시 태그 명령을 [실행합니다.][az-acr-repository-show-tags] 

메타데이터를 읽으려면 토큰은 을 통해 `docker login`인증되지 않습니다. 대신 토큰의 이름과 암호를 두 명령 중 하나에 전달합니다. 다음 예제에서는 문서의 앞에 만든 환경 변수를 사용합니다.

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

### <a name="list-scope-maps"></a>목록 범위 맵

[포털의 az acr 범위 맵 목록][az-acr-scope-map-list] 명령 또는 **범위 맵(미리 보기)** 화면을 사용하여 레지스트리에 구성된 모든 범위 맵을 나열합니다. 예를 들어:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

출력에는 정의한 범위 맵과 토큰을 구성하는 데 사용할 수 있는 여러 시스템 정의 범위 맵이 표시됩니다.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>토큰 세부 정보 표시

토큰의 상태 및 암호 만료 날짜와 같은 토큰의 세부 정보를 보려면 [az acr 토큰 표시][az-acr-token-show] 명령을 실행하거나 포털의 **토큰(미리 보기) 화면에서 토큰을 선택합니다.** 예를 들어:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

az [acr 토큰 목록][az-acr-token-list] 명령 또는 포털의 **토큰(미리 보기)** 화면을 사용하여 레지스트리에 구성된 모든 토큰을 나열합니다. 예를 들어:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>토큰에 대한 암호 생성

토큰 암호가 없거나 새 암호를 생성하려는 경우 [az acr 토큰 자격 증명 생성][az-acr-token-credential-generate] 명령을 실행합니다. 

다음 예제에서는 *MyToken* 토큰에 대 한 암호1에 대 한 새 값을 생성 하 고 만료 기간 30 일입니다. 환경 변수에 `TOKEN_PWD`암호를 저장합니다. 이 예제는 bash 셸에 대해 서식이 지정됩니다.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Azure 포털을 사용하여 토큰 암호를 생성하려면 이 문서의 앞에서 [토큰 만들기 - 포털의](#create-token---portal) 단계를 참조하세요.

### <a name="update-token-with-new-scope-map"></a>새 범위 맵으로 토큰 업데이트

다른 범위 맵으로 토큰을 업데이트하려면 [az acr 토큰 업데이트를][az-acr-token-update] 실행하고 새 범위 맵을 지정합니다. 예를 들어:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

포털에서 **토큰(미리 보기)** 화면에서 토큰을 선택하고 범위 **맵에서**다른 범위 맵을 선택합니다.

> [!TIP]
> 새 범위 맵으로 토큰을 업데이트한 후 새 토큰 암호를 생성할 수 있습니다. az [acr 토큰 자격 증명을][az-acr-token-credential-generate] 사용하여 명령을 생성하거나 Azure 포털에서 토큰 암호를 다시 생성합니다.

## <a name="disable-or-delete-token"></a>토큰 비활성화 또는 삭제

사용자 또는 서비스에 대한 토큰 자격 증명 사용을 일시적으로 비활성화해야 할 수 있습니다. 

Azure CLI를 사용하여 [az acr 토큰 업데이트][az-acr-token-update] `status` 명령을 `disabled`실행하여 다음을 설정합니다.

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

포털에서 **토큰(미리 보기)** 화면에서 토큰을 선택하고 **상태**에서 **비활성화를** 선택합니다.

자격 증명을 사용하는 모든 사용자가 영구적으로 액세스를 무효화하기 위해 토큰을 삭제하려면 [az acr 토큰 삭제][az-acr-token-delete] 명령을 실행합니다. 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

포털에서 **토큰(미리 보기)** 화면에서 토큰을 선택하고 **삭제를**선택합니다.

## <a name="next-steps"></a>다음 단계

* 범위 맵 및 토큰을 관리하려면 az [acr 범위 맵][az-acr-scope-map] 및 [az acr 토큰][az-acr-token] 명령 그룹에서 추가 명령을 사용합니다.
* Azure Active Directory ID, 서비스 주체 또는 관리자 계정을 사용하는 등 Azure 컨테이너 레지스트리를 사용하여 인증할 다른 옵션에 대한 [인증 개요를](container-registry-authentication.md) 참조하세요.


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
