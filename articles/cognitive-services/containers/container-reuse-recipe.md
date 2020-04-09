---
title: Docker 컨테이너에 대한 레시피
titleSuffix: Azure Cognitive Services
description: 배포 및 재사용을 위해 구성 설정의 일부 또는 전부를 사용하여 컨테이너를 빌드, 테스트 및 저장하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7380ff58d033a68565de7e419ff318f7bdec121d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875081"
---
# <a name="create-containers-for-reuse"></a>재사용할 컨테이너 만들기

이러한 컨테이너 레시피를 사용하여 재사용할 수 있는 코그너티브 서비스 컨테이너를 만듭니다. 컨테이너를 시작할 때 필요하지 _않도록_ 일부 또는 모든 구성 설정으로 컨테이너를 빌드할 수 있습니다.

이 새 컨테이너 계층(설정 사용)이 있고 로컬로 테스트한 후에는 컨테이너를 컨테이너 레지스트리에 저장할 수 있습니다. 컨테이너가 시작되면 현재 컨테이너에 저장되지 않은 설정만 필요합니다. 개인 레지스트리 컨테이너는 이러한 설정을 전달할 수 있는 구성 공간을 제공합니다.

## <a name="docker-run-syntax"></a>도커 실행 구문

이 `docker run` 문서의 모든 예제에서는 줄 `^` 연속 문자가 있는 Windows 콘솔을 가정합니다. 직접 사용하기 위해 다음을 고려하십시오.

* Docker 컨테이너에 대해 잘 알고 있지 않은 경우 인수 순서를 변경하지 마세요.
* Windows 이외의 운영 체제 또는 Windows 콘솔 이외의 콘솔을 사용하는 경우 올바른 콘솔/터미널, 마운트에 대한 폴더 구문 및 콘솔 및 시스템의 줄 연속 문자를 사용합니다.  코그너티브 서비스 컨테이너는 Linux 운영 체제이므로 대상 마운트는 Linux 스타일 폴더 구문을 사용합니다.
* `docker run`예를 들어 `c:` Windows에서 권한 충돌을 피하기 위해 드라이브에서 디렉터리를 사용합니다. 입력 디렉터리로 특정 디렉터리를 사용해야 할 경우 Docker 서비스 권한을 받아야 할 수도 있습니다.

## <a name="store-no-configuration-settings-in-image"></a>이미지에 구성 설정 저장 없음

각 `docker run` 서비스에 대한 예제 명령은 컨테이너에 구성 설정을 저장하지 않습니다. 콘솔 또는 레지스트리 서비스에서 컨테이너를 시작할 때 이러한 구성 설정을 전달해야 합니다. 개인 레지스트리 컨테이너는 이러한 설정을 전달할 수 있는 구성 공간을 제공합니다.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>레시피 재사용: 모든 구성 설정을 컨테이너에 저장

모든 구성 설정을 저장하려면 해당 `Dockerfile` 설정을 사용하여 을 만듭니다.

이 접근 방식의 문제:

* 새 컨테이너에는 원래 컨테이너와 별도의 이름과 태그가 있습니다.
* 이러한 설정을 변경하려면 Dockerfile의 값을 변경하고 이미지를 다시 빌드한 다음 레지스트리에 다시 게시해야 합니다.
* 다른 사용자가 컨테이너 레지스트리 또는 로컬 호스트에 액세스하는 경우 컨테이너를 실행하고 코그너티브 서비스 끝점을 사용할 수 있습니다.
* 코그너티브 서비스에 입력 마운트가 필요하지 않은 `COPY` 경우 Dockerfile에 선을 추가하지 마십시오.

Dockerfile을 만들고 사용하려는 기존 코그너티브 서비스 컨테이너에서 끌어온 다음 Dockerfile의 docker 명령을 사용하여 컨테이너에 필요한 정보를 설정하거나 가져옵니다.

이 예제는 다음과 같이 작성되었습니다.

* 을 사용하여 `ENV`호스트의 `{BILLING_ENDPOINT}` 환경 키에서 청구 끝점을 설정합니다.
* 'ENV'를 사용하여 `{ENDPOINT_KEY}` 호스트의 환경 키에서 청구 API 키를 설정합니다.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>레시피 재사용: 컨테이너에 청구 설정 저장

이 예제에서는 Dockerfile에서 텍스트 분석의 감정 컨테이너를 빌드하는 방법을 보여 주었습니다.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

필요에 따라 [로컬](#how-to-use-container-on-your-local-host) 또는 [개인 레지스트리 컨테이너에서](#how-to-add-container-to-private-registry) 컨테이너를 빌드하고 실행합니다.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>레시피 재사용: 컨테이너로 결제 및 마운트 설정 저장

이 예제에서는 언어 이해를 사용하여 Dockerfile에서 청구 및 모델을 저장하는 방법을 보여 주며 있습니다.

* 을 사용하여 `COPY`호스트의 파일 시스템에서 언어 이해(LUIS) 모델 파일을 복사합니다.
* LUIS 컨테이너는 두 개 이상의 모델을 지원합니다. 모든 모델이 동일한 폴더에 저장되어 있는 `COPY` 경우 모두 하나의 명령문이 필요합니다.
* 모델 입력 디렉터리의 상대 부모에서 docker 파일을 실행합니다. 다음 예제에서는 `docker build` `docker run` `/input`의 상대 부모로부터 및 명령을 실행합니다. 명령의 `/input` `COPY` 첫 번째는 호스트 컴퓨터의 디렉터리입니다. 두 `/input` 번째는 컨테이너의 디렉터리입니다.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

필요에 따라 [로컬](#how-to-use-container-on-your-local-host) 또는 [개인 레지스트리 컨테이너에서](#how-to-add-container-to-private-registry) 컨테이너를 빌드하고 실행합니다.

## <a name="how-to-use-container-on-your-local-host"></a>로컬 호스트에서 컨테이너를 사용하는 방법

Docker 파일을 빌드하려면 `<your-image-name>` 이미지의 새 이름으로 바꾼 다음 다음을 사용합니다.

```console
docker build -t <your-image-name> .
```

이미지를 실행하고 컨테이너가 중지될 때 이미지를`--rm`제거하려면 (:

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>개인 레지스트리에 컨테이너를 추가 하는 방법

다음 단계에 따라 Dockerfile을 사용하고 개인 컨테이너 레지스트리에 새 이미지를 배치합니다.  

1. 재사용 `Dockerfile` 레시피의 텍스트로 를 만듭니다. A에는 `Dockerfile` 확장이 없습니다.

1. 각도 대괄호의 모든 값을 고유한 값으로 바꿉습니다.

1. 다음 명령을 사용하여 명령줄 또는 터미널의 이미지로 파일을 빌드합니다. 각도 괄호의 값을 고유한 `<>`컨테이너 이름 및 태그로 바꿉니다.  

    태그 옵션은 `-t`컨테이너에 대해 변경한 내용에 대한 정보를 추가하는 방법입니다. 예를 들어 원래 컨테이너가 계층화되었음을 `modified-LUIS` 나타내는 컨테이너 이름입니다. 태그 이름은 `with-billing-and-model` 언어 이해(LUIS) 컨테이너가 수정된 방법을 나타냅니다.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. 콘솔에서 Azure CLI에 로그인합니다. 이 명령은 브라우저를 열고 인증이 필요합니다. 인증되면 브라우저를 닫고 콘솔에서 작업을 계속할 수 있습니다.

    ```azurecli
    az login
    ```

1. 콘솔에서 Azure CLI를 사용하여 개인 레지스트리에 로그인합니다.

    각도 괄호에 있는 값을 `<my-registry>`고유한 레지스트리 이름으로 바꿉니다.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    서비스 주체가 할당된 경우 docker 로그인을 통해 로그인할 수도 있습니다.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. 개인 레지스트리 위치로 컨테이너에 태그를 붙입니다. 각도 괄호에 있는 값을 `<my-registry>`고유한 레지스트리 이름으로 바꿉니다. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    태그 이름을 `latest` 사용하지 않는 경우 암시됩니다.

1. 새 이미지를 개인 컨테이너 레지스트리로 푸시합니다. 개인 컨테이너 레지스트리를 볼 때 다음 CLI 명령에 사용된 컨테이너 이름은 리포지토리의 이름입니다.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 컨테이너 인스턴스 만들기 및 사용](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->