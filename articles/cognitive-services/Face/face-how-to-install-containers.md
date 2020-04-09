---
title: 컨테이너 설치 및 실행 - 얼굴
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 이 연습 자습서에서 Face용 컨테이너를 다운로드, 설치 및 실행하는 방법을 보여 주며 이 자습서를 통해 사용할 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 74465bddb57c14af4d02c1d3bfdc46f3ac25bef3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878547"
---
# <a name="install-and-run-face-containers-preview"></a>얼굴 컨테이너 설치 및 실행(미리 보기)

Azure Cognitive Services Face는 이미지에서 사람의 얼굴을 감지하는 Docker용 표준화된 Linux 컨테이너를 제공합니다. 또한 코와 눈, 성별, 나이 및 기타 기계 예측 얼굴 특징과 같은 얼굴 랜드마크를 포함하는 속성을 식별합니다. Face는 감지 외에도 신뢰도 점수를 사용하여 동일한 이미지 또는 다른 이미지의 두 얼굴이 동일한지 확인할 수 있습니다. 얼굴은 또한 데이터베이스와 면을 비교하여 비슷한 모습이거나 동일한 면이 이미 존재하는지 확인할 수 있습니다. 또한 공유 시각적 특성을 사용하여 유사한 얼굴을 그룹으로 구성할 수도 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Face 서비스 컨테이너를 사용하기 전에 다음 필수 구성 조건을 충족해야 합니다.

|필수|목적|
|--|--|
|Docker 엔진| Docker 엔진을 [호스트 컴퓨터에](#the-host-computer)설치해야 합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> Windows에서 도커도 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대한 기본적인 이해가 필요합니다. 또한 기본 `docker` 명령에 대한 지식도 필요합니다.| 
|얼굴 리소스 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>Azure **Face** 리소스 및 연결된 API 키 및 끝점 URI입니다. 두 값 모두 **리소스의 개요** 및 **키** 페이지에서 사용할 수 있습니다. 컨테이너를 시작해야 합니다.<br><br>**{API_KEY}**: **키** 페이지에서 사용 가능한 두 리소스 키 중 하나<br><br>**{ENDPOINT_URI}**: **개요** 페이지에 제공된 끝점

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>프라이빗 컨테이너 레지스트리에 대한 액세스 요청

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표는 각 Face 서비스 컨테이너에 할당할 최소 및 권장 CPU 코어 및 메모리에 대해 설명합니다.

| 컨테이너 | 최소 | 권장 | 초당 트랜잭션 수<br>(최소, 최대)|
|-----------|---------|-------------|--|
|Face | 1 코어, 2GB 메모리 | 1 코어, 4GB 메모리 |10, 20|

* 각 코어는 2.6GHz 이상이어야 합니다.
* 초당 트랜잭션(TPS).

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>도커 풀로 컨테이너 이미지 받기

Face 서비스에 대한 컨테이너 이미지를 사용할 수 있습니다. 

| 컨테이너 | 리포지토리 |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Face 컨테이너용 docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>컨테이너 사용

컨테이너가 [호스트 컴퓨터에](#the-host-computer)있는 후에는 다음 프로세스를 사용하여 컨테이너를 사용합니다.

1. 필요한 청구 설정으로 [컨테이너를 실행합니다.](#run-the-container-with-docker-run) `docker run` 명령의 자세한 [예제](./face-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 
1. [컨테이너의 예측 끝점을 쿼리합니다.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>도커 실행으로 컨테이너 실행

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. `{ENDPOINT_URI}` 및 `{API_KEY}` 값을 가져오는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요.

`docker run`명령의 [예](face-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 얼굴 컨테이너를 실행합니다.
* CPU 코어 1개와 메모리 4GB를 할당합니다.
* TCP 포트(5000)를 노출하고 컨테이너에 의사 TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다. 

`docker run` 명령의 자세한 [예제](./face-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 

> [!IMPORTANT]
> 컨테이너를 실행하려면 `Eula`및 `Billing` `ApiKey` 옵션을 지정해야 하거나 컨테이너가 시작되지 않습니다. 자세한 내용은 [Billing](#billing)를 참조하세요.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다. 

컨테이너 API에 대한 호스트, `http://localhost:5000`을 사용합니다.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [마운트를](./face-resource-container-config.md#mount-settings) 사용하여 컨테이너를 실행하고 로깅이 활성화된 경우 컨테이너는 컨테이너를 시작하거나 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

Face 서비스 컨테이너는 Azure 계정의 Face 리소스를 사용하여 Azure에 청구 정보를 보냅니다. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](./face-resource-container-config.md)을 참조하세요.

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 Face 서비스 컨테이너를 다운로드, 설치 및 실행하는 방법에 대한 개념 및 워크플로를 배웠습니다. 요약하면 다음과 같습니다.

* 컨테이너 이미지는 Azure 컨테이너 레지스트리에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하여 컨테이너의 호스트 URI를 지정하여 Face 서비스 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> 코그너티브 서비스 컨테이너는 계량을 위해 Azure에 연결하지 않고 실행할 수 있도록 라이선스가 부여되지 않습니다. 고객은 컨테이너가 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 구성 설정의 경우 [컨테이너 구성](face-resource-container-config.md)을 참조하십시오.
* 얼굴을 감지하고 식별하는 방법에 대한 자세한 내용은 [얼굴 개요를](Overview.md)참조하십시오.
* 컨테이너에서 지원하는 메서드에 대한 자세한 내용은 [Face API를](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)참조하십시오.
* 더 많은 코그너티브 서비스 컨테이너를 사용하려면 [코그너티브 서비스 컨테이너를](../cognitive-services-container-support.md)참조하십시오.
