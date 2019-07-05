---
title: Docker 컨테이너에 대 한 레시피
titleSuffix: Azure Cognitive Services
description: 이러한 컨테이너 레시피를 사용 하 여 다시 사용할 수 있는 Cognitive Services 컨테이너를 만듭니다. 컨테이너는 컨테이너를 시작 하는 경우 필요 하지 않은 일부 또는 모든 구성 설정을 사용 하 여 빌드할 수 있습니다. 있는 컨테이너의이 새로운 계층 (설정)를 로컬로 테스트 하 고 컨테이너를 컨테이너 레지스트리에 저장할 수 있습니다. 컨테이너를 시작할 때 현재 컨테이너에 저장 되지 않은 해당 설정 하기만 됩니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 37fa972d52c564c4d61e5923c2b3dc48bde9d2ee
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445799"
---
# <a name="create-containers-for-reuse"></a>다시 사용할 수 있도록 컨테이너 만들기

이러한 컨테이너 레시피를 사용 하 여 다시 사용할 수 있는 Cognitive Services 컨테이너를 만듭니다. 수 있도록 일부 또는 모든 구성 설정을 사용 하 여 컨테이너를 빌드할 수 있습니다 _되지_ 컨테이너가 시작 될 때 필요 합니다.

있는 컨테이너의이 새로운 계층 (설정)를 로컬로 테스트 하 고 컨테이너를 컨테이너 레지스트리에 저장할 수 있습니다. 컨테이너를 시작할 때 현재 컨테이너에 저장 되지 않은 해당 설정 하기만 됩니다. 개인 레지스트리의 컨테이너에서 이러한 설정을 전달할 수 있는 구성 공간을 제공 합니다.

## <a name="docker-run-syntax"></a>Docker 구문을 실행 합니다.

모든 `docker run` 이 문서의 예제에서는 Windows 콘솔을 사용 하 여 가정 된 `^` 줄 연속 문자입니다. 직접 사용 하기 위해 다음을 고려 합니다.

* Docker 컨테이너에 대해 잘 알고 있지 않은 경우 인수 순서를 변경하지 마세요.
* Windows 이외의 운영 체제 또는 Windows 콘솔 이외의 콘솔을 사용 하는 경우 바 운 트 및 시스템 확인 하 고 콘솔에 대 한 줄 연속 문자에 대 한 올바른 콘솔/터미널을 폴더 구문을 사용 합니다.  Cognitive Services 컨테이너를 Linux 운영 체제 이기 때문에 대상 탑재는 Linux 스타일 폴더 구문을 사용 합니다.
* `docker run` 해제 디렉터리를 사용 하는 예제는 `c:` Windows에서 모든 권한 충돌을 방지 하는 드라이브. 입력 디렉터리로 특정 디렉터리를 사용해야 할 경우 Docker 서비스 권한을 받아야 할 수도 있습니다.

## <a name="store-no-configuration-settings-in-image"></a>구성 설정은 없습니다 이미지 저장

이 예제에서는 `docker run` 명령을 각 서비스에 대 한 컨테이너의 모든 구성 설정을 저장 하지 마십시오. 콘솔 또는 레지스트리 서비스에서 컨테이너를 시작 하는 경우 해당 구성 설정을 전달 해야 합니다. 개인 레지스트리의 컨테이너에서 이러한 설정을 전달할 수 있는 구성 공간을 제공 합니다.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>레시피를 다시 사용 합니다: 컨테이너를 사용 하 여 모든 구성 설정을 저장 합니다.

모든 구성 설정을 저장 하려면 만들기를 `Dockerfile` 이러한 설정을 사용 하 여 합니다.

이 방법 사용 하는 문제:

* 새 컨테이너에 별도 이름 및 원래 컨테이너의 태그입니다.
* 이러한 설정을 변경 하려면 Dockerfile의 값을 변경, 이미지를 다시 빌드하고 레지스트리로 다시 게시 해야 합니다.
* 컨테이너 레지스트리 또는 로컬 호스트에 대 한 액세스를 다른 사람이 컨테이너를 실행할 수 있으며 Cognitive Services 끝점을 사용.
* Cognitive 서비스 입력된 탑재 필요 하지 않은, 경우 추가 하지는 `COPY` 줄 Dockerfile 합니다.

Dockerfile을 사용 하려는 기존 Cognitive 서비스 컨테이너에서 끌어오기를 만든 다음을 설정 하거나 컨테이너 정보에서 끌어오기 Dockerfile에서 docker 명령을 사용 해야 합니다.

이 예제에서:

* 청구는 끝점을 설정 `{BILLING_ENDPOINT}` 호스트에서 환경 키를 사용 하 여 `ENV`입니다.
* 청구 API 키 집합 `{ENDPOINT_KEY}` 호스트에서 환경 키를 사용 하 여 ' ENV 합니다.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>레시피를 다시 사용 합니다: 컨테이너를 사용 하 여 청구 설정 저장

이 예제는 Dockerfile에서 텍스트 분석 감정 컨테이너를 빌드하는 방법을 보여 줍니다.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

빌드 및 컨테이너를 실행 [로컬로](#how-to-use-container-on-your-local-host) 또는 사용자 [개인 레지스트리의 컨테이너](#how-to-add-container-to-private-registry) 필요에 따라 합니다.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>레시피를 다시 사용: 대금 청구를 저장 하 고 컨테이너를 사용 하 여 설정을 탑재

이 예에서는 Language Understanding, Dockerfile에서 대금 청구 및 모델 저장을 사용 하는 방법을 보여 줍니다.

* LUIS (Language Understanding) 모델에서 호스트의 파일 복사 사용 하 여 시스템 파일 `COPY`합니다.
* LUIS 컨테이너 둘 이상의 모델을 지원합니다. 모든 모델은 같은 폴더에 저장 되 면 모든 필요한 `COPY` 문입니다.
* 모델 입력된 디렉터리의 상대 부모에서 docker 파일을 실행 합니다. 다음 예제를 실행 합니다 `docker build` 및 `docker run` 의 상대 부모에서 명령을 `/input`합니다. 첫 번째 `/input` 에 `COPY` 명령에는 호스트 컴퓨터의 디렉터리입니다. 두 번째 `/input` 컨테이너의 디렉터리입니다.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

빌드 및 컨테이너를 실행 [로컬로](#how-to-use-container-on-your-local-host) 또는 사용자 [개인 레지스트리의 컨테이너](#how-to-add-container-to-private-registry) 필요에 따라 합니다.

## <a name="how-to-use-container-on-your-local-host"></a>로컬 호스트에서 컨테이너를 사용 하는 방법

Docker 파일을 빌드하려면 대체 `<your-image-name>` 이미지의 새 이름으로 사용 합니다.

```console
docker build -t <your-image-name> .
```

이미지를 실행 하 여 컨테이너를 중지 하는 경우 제거 (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>에 개인 레지스트리의 컨테이너를 추가 하는 방법

다음 단계를 Dockerfile을 사용 하 여 개인 컨테이너 레지스트리에 새 이미지를 배치 합니다.  

1. 만들기는 `Dockerfile` 재사용 작성법에서 텍스트를 사용 하 여 합니다. `Dockerfile` 확장명이 없습니다.

1. 꺾쇠 괄호 안의 값을 고유한 값으로 바꿉니다.

1. 명령줄 또는 터미널에서 다음 명령을 사용 하 여 이미지에 파일을 빌드하십시오. 꺾쇠 괄호 안의 값을 대체 `<>`, 사용자 고유의 컨테이너 이름 및 태그를 사용 하 여 합니다.  

    태그 옵션을 `-t`, 컨테이너에 대해 변경 된 내용을 대 한 정보를 추가 하는 방법입니다. 예를 들어, 컨테이너 이름을 `modified-LUIS` 원래 컨테이너를 레이어를 나타냅니다. 태그 이름을 `with-billing-and-model` LUIS (Language Understanding) 컨테이너를 어떻게 수정 되었음을 나타냅니다.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. 콘솔에서 Azure CLI에 로그인 합니다. 이 명령은 브라우저를 열고 인증이 필요 합니다. 인증 되 면 브라우저를 닫고 콘솔의 작업을 계속할 수 있습니다.

    ```azure-cli
    az login
    ```

1. 콘솔에서 Azure CLI를 사용 하 여 개인 레지스트리에 로그인 합니다.

    꺾쇠 괄호 안의 값을 대체 `<my-registry>`, 사용자 레지스트리 이름입니다.  

    ```azure-cli
    az acr login --name <my-registry>
    ```

    서비스 주체를 할당 하는 경우에 docker 로그인으로 서명할 수 있습니다.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. 개인 레지스트리 위치를 사용 하 여 컨테이너를 태그 합니다. 꺾쇠 괄호 안의 값을 대체 `<my-registry>`, 사용자 레지스트리 이름입니다. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    태그 이름을 사용 하지 않는 경우 `latest` 인 것으로 간주 합니다.

1. 새 이미지를 개인 컨테이너 레지스트리에 푸시하십시오. 개인 컨테이너 레지스트리를 볼 때 다음 CLI 명령을 사용 하는 컨테이너 이름을 리포지토리의 이름이 됩니다.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [만들기 및 Azure Container Instance를 사용 합니다.](azure-container-instance-recipe.md)

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