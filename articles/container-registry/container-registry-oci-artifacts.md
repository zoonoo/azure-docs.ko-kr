---
title: OCI 아티팩트를 밀고 당깁니다.
description: Azure의 개인 컨테이너 레지스트리를 사용하여 OCI(개방형 컨테이너 이니셔티브) 아티팩트를 푸시하고 가져옵니다.
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371055"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Azure 컨테이너 레지스트리를 사용하여 OCI 아티팩트를 푸시하고 가져옵니다.

Azure 컨테이너 레지스트리를 사용하여 [OCI(개방형 컨테이너 이니셔티브) 아티팩트와](container-registry-image-formats.md#oci-artifacts) Docker 및 Docker 호환 컨테이너 이미지를 저장하고 관리할 수 있습니다.

이 기능을 설명하기 위해 이 문서에서는 [OCI 레지스트리를 ORAS(저장소)로](https://github.com/deislabs/oras) 사용하여 샘플 아티팩트(텍스트 파일)를 Azure 컨테이너 레지스트리로 푸시하는 방법을 보여 줍니다. 그런 다음 레지스트리에서 아티팩트를 가져옵니다. 각 아티팩트에 적합한 다양한 명령줄 도구를 사용하여 Azure 컨테이너 레지스트리에서 다양한 OCI 아티팩트를 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 컨테이너 레지스트리** - Azure 구독에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다.
* **ORAS 도구** - [GitHub 리포지토리에서](https://github.com/deislabs/oras/releases)운영 체제에 대한 현재 ORAS 릴리스를 다운로드하여 설치합니다. 이 도구는 압축 된 타볼`.tar.gz` (파일)로 해제됩니다. 운영 체제에 대한 표준 절차를 사용하여 파일을 추출하고 설치합니다.
* **Azure Active Directory 서비스 주체(선택 사항)** - ORAS로 직접 인증하려면 레지스트리에 액세스하는 [서비스 주체를](container-registry-auth-service-principal.md) 만듭니다. 아티팩트를 푸시하고 끌어올 수 있는 권한이 있도록 서비스 주체가 AcrPush와 같은 역할을 할당해야 합니다.
* **Azure CLI(선택 사항)** - 개별 ID를 사용하려면 Azure CLI의 로컬 설치가 필요합니다. 버전 2.0.71 이상은 권장됩니다. 버전을 `az --version `찾기 위해 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하십시오.
* **Docker(선택 사항)** - 개별 ID를 사용하려면 레지스트리를 인증하기 위해 Docker가 로컬로 설치되어 있어야 합니다. Docker는 모든 [macOS][docker-mac], [Windows][docker-windows] 또는 [Linux][docker-linux] 시스템에서 Docker를 쉽게 구성할 수 있는 패키지를 제공합니다.


## <a name="sign-in-to-a-registry"></a>레지스트리에 로그인

이 섹션에서는 사용된 ID에 따라 레지스트리에 로그인할 두 가지 권장 워크플로를 보여 주십습니다. 사용자 환경에 적합한 방법을 선택합니다.

### <a name="sign-in-with-oras"></a>ORAS로 로그인

푸시 권한이 있는 [서비스 주체를](container-registry-auth-service-principal.md) 사용하여 `oras login` 명령을 실행하여 서비스 주체 응용 프로그램 ID 및 암호를 사용하여 레지스트리에 로그인합니다. 이 *경우*myregistry.azurecr.io 정규화된 레지스트리 이름(모든 소문자)을 지정합니다. 서비스 주체 응용 프로그램 ID는 `$SP_APP_ID`환경 변수및 변수의 `$SP_PASSWD`암호에 전달됩니다.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Stdin에서 암호를 읽으려면 `--password-stdin`을 사용합니다.

### <a name="sign-in-with-azure-cli"></a>Azure CLI로 로그인

ID를 사용하여 Azure CLI에 [로그인하여](/cli/azure/authenticate-azure-cli) 컨테이너 레지스트리에서 아티팩트를 푸시하고 가져옵니다.

그런 다음 Azure CLI 명령 [az acr 로그인을](/cli/azure/acr?view=azure-cli-latest#az-acr-login) 사용하여 레지스트리에 액세스합니다. 예를 들어 *myregistry라는*레지스트리를 인증하려면 다음과 같은

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`Docker 클라이언트를 사용하여 파일에서 Azure Active `docker.config` Directory 토큰을 설정합니다. 개별 인증 흐름을 완료하려면 Docker 클라이언트를 설치하고 실행해야 합니다.

## <a name="push-an-artifact"></a>아티팩트 푸시

일부 샘플 텍스트가 있는 로컬 작업 디렉토리에 텍스트 파일을 만듭니다. 예를 들어 bash 셸에서:

```bash
echo "Here is an artifact!" > artifact.txt
```

`oras push` 명령을 사용하여 이 텍스트 파일을 레지스트리로 푸시합니다. 다음 예제에서는 샘플 텍스트 파일을 `samples/artifact` 리포지토리로 푸시합니다. 레지스트리는 정규화된 레지스트리 이름 *myregistry.azurecr.io(모든* 소문자)로 식별됩니다. 아티팩트에 태그가 지정됩니다. `1.0` 아티팩트에는 기본적으로 파일 이름 `artifact.txt`다음에 미디어 형식 문자열로 식별되는 정의되지 않은 *형식이* 있습니다. 추가 [유형은 OCI 아티팩트를](https://github.com/opencontainers/artifacts) 참조하십시오. 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

성공적인 푸시에 대한 출력은 다음과 유사합니다.

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

레지스트리에서 아티팩트를 관리하려면 Azure CLI를 사용하는 경우 `az acr` 이미지 관리를 위한 표준 명령을 실행합니다. 예를 들어 [az acr 저장소 표시][az-acr-repository-show] 명령을 사용하여 아티팩트의 특성을 가져옵니다.

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

다음과 유사하게 출력됩니다.

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>아티팩트 당기기

`oras pull` 명령을 실행하여 레지스트리에서 아티팩트를 가져옵니다.

먼저 로컬 작업 디렉토리에서 텍스트 파일을 제거합니다.

```bash
rm artifact.txt
```

아티팩트를 당기기 위해 실행하고 `oras pull` 아티팩트를 푸시하는 데 사용되는 미디어 유형을 지정합니다.

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

끌어오기가 성공했는지 확인합니다.

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>아티팩트 제거(선택 사항)

Azure 컨테이너 레지스트리에서 아티팩트를 제거하려면 [az acr 저장소 삭제][az-acr-repository-delete] 명령을 사용합니다. 다음 예제에서는 저장한 아티팩트를 제거합니다.

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>다음 단계

* 아티팩트에 대한 매니페스트를 구성하는 방법을 포함하여 [ORAS 라이브러리에](https://github.com/deislabs/oras/tree/master/docs)대해 자세히 알아보기
* 새로운 아티팩트 유형에 대한 참조 정보는 [OCI 아티팩트](https://github.com/opencontainers/artifacts) 리포지토리를 방문하십시오.



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
