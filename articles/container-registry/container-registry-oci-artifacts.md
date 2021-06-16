---
title: OCI 아티팩트 밀어넣기 및 끌어오기
description: Azure에서 개인 컨테이너 레지스트리를 사용하여 OCI(Open Container Initiative) 아티팩트 밀어넣기 및 끌어오기
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 02/03/2021
ms.author: stevelas
ms.openlocfilehash: dbd64d939f823e8dc7b0cd6d76233b07fce462d9
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110537405"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Azure 컨테이너 레지스트리를 사용하여 OCI 아티팩트 밀어넣기 및 끌어오기

Azure 컨테이너 레지스트리를 사용하여 Docker 및 Docker 호환 컨테이너 이미지뿐 아니라 [OCI(Open Container 이니셔티브) 아티팩트](container-registry-image-formats.md#oci-artifacts)도 저장하고 관리할 수 있습니다.

이 기능을 설명하기 위해 이 문서에서는 [ORAS(OCI Registry as Storage)](https://github.com/deislabs/oras) 도구를 사용하여 샘플 아티팩트(텍스트 파일)를 Azure 컨테이너 레지스트리로 밀어넣는 방법을 보여 줍니다. 그런 다음 레지스트리에서 아티팩트를 끌어옵니다. 각 아티팩트에 적합한 다양한 명령줄 도구를 사용하여 Azure 컨테이너 레지스트리에서 다양한 OCI 아티팩트를 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure Container Registry** - Azure 구독 내에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다.
* **ORAS 도구** - [GitHub 리포지토리](https://github.com/deislabs/oras/releases)에서 운영 체제에 맞는 최신 ORAS 릴리스를 다운로드하여 설치합니다. 이 도구는 압축된 tarball(`.tar.gz` 파일)로 릴리스됩니다. 운영 체제의 표준 절차를 사용하여 파일 압축을 풀고 설치합니다.
* **Azure Active Directory 서비스 주체(선택 사항)** - ORAS를 사용하여 직접 인증하려면 레지스트리에 액세스하는 [서비스 주체](container-registry-auth-service-principal.md)를 만듭니다. 아티팩트를 밀어넣고 끌어올 수 있는 권한을 갖도록 서비스 주체에게 AcrPush와 같은 역할을 할당해야 합니다.
* **Azure CLI(선택 사항)** - 개별 ID를 사용하려면 Azure CLI의 로컬 설치가 필요합니다. 버전 2.0.71 이상을 사용하는 것이 좋습니다. `az --version `을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
* **Docker(선택 사항)** - 개별 ID를 사용하려면 로컬로 Docker를 설치하여 레지스트리로 인증해야 합니다. Docker는 모든 [macOS][docker-mac], [Windows][docker-windows] 또는 [Linux][docker-linux] 시스템에서 Docker를 쉽게 구성할 수 있는 패키지를 제공합니다.


## <a name="sign-in-to-a-registry"></a>레지스트리에 로그인

이 섹션에서는 사용되는 ID에 따라 레지스트리에 로그인하기 위한 두 가지 권장 워크플로를 보여 줍니다. 사용자 환경에 적합한 방법을 선택하세요.

### <a name="sign-in-with-oras"></a>ORAS로 로그인

끌어오기 권한이 있는 [서비스 주체](container-registry-auth-service-principal.md)를 사용하여 서비스 주체 애플리케이션 ID 및 암호로 `oras login` 명령을 실행하여 레지스트리에 로그인합니다. 정규화된 레지스트리 이름(모두 소문자)을 지정합니다. 이 경우 *myregistry.azurecr.io* 입니다. 서비스 주체 애플리케이션 ID는 환경 변수 `$SP_APP_ID`로 전달되고 암호는 변수 `$SP_PASSWD`로 전달됩니다.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Stdin에서 암호를 읽으려면 `--password-stdin`을 사용합니다.

### <a name="sign-in-with-azure-cli"></a>Azure CLI로 로그인

ID로 Azure CLI에 [로그인](/cli/azure/authenticate-azure-cli)하여 컨테이너 레지스트리에서 아티팩트를 밀어넣고 끌어옵니다.

그런 다음 Azure CLI 명령 [az acr login](/cli/azure/acr#az_acr_login)을 사용하여 레지스트리에 액세스합니다. 예를 들어 이름이 *myregistry* 인 레지스트리에 로그인하는 방법은 다음과 같습니다.

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`은 Docker 클라이언트를 사용하여 `docker.config` 파일에 Azure Active Directory 토큰을 설정합니다. 개별 인증 흐름을 완료하려면 Docker 클라이언트가 설치되고 실행 중이어야 합니다.

## <a name="push-an-artifact"></a>아티팩트 밀어넣기

일부 샘플 텍스트를 사용하여 로컬 작업 디렉터리에 텍스트 파일을 만듭니다. 예를 들어 bash 셸에서는 다음과 같습니다.

```bash
echo "Here is an artifact" > artifact.txt
```

`oras push` 명령을 사용하여 이 텍스트 파일을 레지스트리에 밀어넣습니다. 다음 예제는 `samples/artifact` 리포지토리에 샘플 텍스트 파일을 밀어넣습니다. 레지스트리는 정규화된 레지스트리 이름 *myregistry.azurecr.io*(모두 소문자)로 식별됩니다. 아티팩트에 `1.0` 태그가 지정됩니다. 아티팩트에는 기본적으로 파일 이름 `artifact.txt` 다음에 오는 *media type* 문자열로 식별되는 정의되지 않은 형식이 있습니다. 추가 형식은 [OCI 아티팩트](https://github.com/opencontainers/artifacts)를 참조하세요. 

**Linux 또는 macOS**

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

밀어넣기가 성공하면 다음과 비슷한 출력이 표시됩니다.

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

레지스트리에서 아티팩트를 관리하려면 Azure CLI를 사용하는 경우 이미지 관리를 위한 표준 `az acr` 명령을 실행합니다. 예를 들어 [az acr repository show][az-acr-repository-show] 명령을 사용하여 아티팩트의 특성을 가져옵니다.

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

## <a name="pull-an-artifact"></a>아티팩트 끌어오기

`oras pull` 명령을 사용하여 레지스트리에서 아티팩트를 끌어옵니다.

먼저 로컬 작업 디렉터리에서 텍스트 파일을 제거합니다.

```bash
rm artifact.txt
```

`oras pull`을 실행하여 아티팩트를 끌어오고, 아티팩트를 밀어넣는 데 사용되는 미디어 유형을 지정합니다.

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

끌어오기가 성공했는지 확인합니다.

```bash
$ cat artifact.txt
Here is an artifact
```

## <a name="remove-the-artifact-optional"></a>아티팩트 제거(선택 사항)

Azure 컨테이너 레지스트리에서 아티팩트를 제거하려면 [az acr repository delete][az-acr-repository-delete] 명령을 사용합니다. 다음 예제는 Azure 컨테이너 레지스트리에 저장한 아티팩트를 제거합니다.

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="example-build-docker-image-from-oci-artifact"></a>예제: OCI 아티팩트에서 Docker 이미지 빌드

컨테이너 이미지를 빌드하기 위한 소스 코드 및 이진 파일을 Azure 컨테이너 레지스트리에 OCI 아티팩트로 저장할 수 있습니다. [ACR 작업](container-registry-tasks-overview.md)의 빌드 컨텍스트로 소스 아티팩트를 참조할 수 있습니다. 이 예제는 Dockerfile을 OCI 아티팩트로 저장한 다음 아티팩트를 참조하여 컨테이너 이미지를 빌드하는 방법을 보여 줍니다.

예를 들어 한 줄 Dockerfile을 만듭니다.

```bash
echo "FROM mcr.microsoft.com/hello-world" > hello-world.dockerfile
```

대상 컨테이너 레지스트리에 로그인합니다.

```azurecli
az login
az acr login --name myregistry
```

`oras push` 명령을 사용하여 새 OCI 아티팩트를 만들고 대상 레지스트리에 밀어넣습니다. 이 예제는 아티팩트의 기본 미디어 유형을 설정합니다.

```bash
oras push myregistry.azurecr.io/dockerfile:1.0 hello-world.dockerfile
```

[az acr build](/cli/azure/acr#az_acr_build) 명령을 실행하고 새 아티팩트를 빌드 컨텍스트로 사용하여 hello-world 이미지를 빌드합니다.

```azurecli
az acr build --registry myregistry --image builds/hello-world:v1 \
  --file hello-world.dockerfile \
  oci://myregistry.azurecr.io/dockerfile:1.0
```

## <a name="next-steps"></a>다음 단계

* 아티팩트의 매니페스트를 구성하는 방법을 포함하여 [ORAS 라이브러리](https://github.com/deislabs/oras)에 대해 자세히 알아보세요.
* 새 아티팩트 형식에 대한 참조 정보를 보려면 [OCI 아티팩트](https://github.com/opencontainers/artifacts) 리포지토리를 방문하세요.



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
