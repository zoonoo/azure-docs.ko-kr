---
title: Face API용 Docker 컨테이너 설치 및 실행
titleSuffix: Azure Cognitive Services
description: Face API용 Docker 컨테이너를 사용하여 이미지에서 사람 얼굴을 감지하고 식별하세요.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/28/2021
ms.author: aahi
keywords: 온-프레미스, Docker, 컨테이너, 식별
ms.openlocfilehash: 3af013000b49690a4edd71c16e607ed57b3ca163
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108291031"
---
# <a name="install-and-run-face-containers-retiring"></a>Face 컨테이너 설치 및 실행(사용 중지)

> [!IMPORTANT]
> Face 컨테이너 미리 보기는 더 이상 애플리케이션을 허용하지 않으며, 2021년 4월 29일부터 컨테이너는 사용이 중단되었습니다. Face 컨테이너는 2021년 7월 26일에 완전히 사용 중지됩니다.

Azure Cognitive Services Face API는 이미지에서 사람 얼굴을 감지하고 분석하는 Linux Docker 컨테이너를 제공합니다. 또한 얼굴 랜드마크(예: 코, 눈), 성별, 연령, 기타 머신 예측 얼굴 특징 등을 포함한 특성을 식별합니다. 감지 외에도 Face는 신뢰도 점수를 사용하여 동일한 이미지 또는 다른 이미지의 두 얼굴이 동일한지 확인할 수 있습니다. Face는 데이터베이스에 대해 얼굴을 비교하여 비슷하거나 동일한 얼굴이 이미 있는지 여부도 확인할 수 있습니다. 공유된 시각적 특성을 사용하여 비슷한 얼굴을 그룹으로 구성할 수도 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Face 서비스 컨테이너를 사용하려면 먼저 다음 사전 요구 사항을 충족해야 합니다.

|필수|용도|
|--|--|
|Docker 엔진| Docker 엔진은 [호스트 컴퓨터](#the-host-computer)에 설치해야 합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> Windows에서 Docker는 Linux 컨테이너도 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념을 기본적으로 이해하고 있어야 합니다. 또한 기본 `docker` 명령에 대한 지식이 필요합니다.| 
|Face 리소스 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>Azure **Face** 리소스 및 연결된 API 키와 엔드포인트 URI. 두 값 모두 리소스의 **개요** 페이지와 **키** 페이지에 제공됩니다. 이는 컨테이너를 시작하는 데 필요합니다.<br><br>**{API_KEY}** : **키** 페이지에 제공된 두 개의 리소스 키 중 하나<br><br>**{ENDPOINT_URI}** : **개요** 페이지에 제공된 엔드포인트

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 각 Face 서비스 컨테이너에 할당해야 하는 최소/권장 CPU 코어 수와 메모리에 대해 설명합니다.

| 컨테이너 | 최소 | 권장 | 초당 트랜잭션 수<br>(최소, 최대)|
|-----------|---------|-------------|--|
|Face | 1개 코어, 2GB 메모리 | 1개 코어, 4GB 메모리 |10, 20|

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* 초당 트랜잭션 수(TPS)입니다.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>docker pull로 컨테이너 이미지 가져오기

Face 서비스의 컨테이너 이미지를 사용할 수 있습니다. 

| 컨테이너 | 리포지토리 |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Face 컨테이너용 docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>컨테이너 사용

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](./face-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 
1. [컨테이너의 예측 엔드포인트를 쿼리합니다](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>docker run을 사용하여 컨테이너 실행

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

* 컨테이너 이미지에서 Face 컨테이너를 실행합니다.
* 1개 CPU 코어 및 4GB 메모리를 할당합니다.
* 5000 TCP 포트를 표시하고 컨테이너에 의사 TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다. 

`docker run` 명령의 자세한 [예제](./face-resource-container-config.md#example-docker-run-commands)를 사용할 수 있습니다. 

> [!IMPORTANT]
> 컨테이너를 실행하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다. 자세한 내용은 [Billing](#billing)를 참조하세요.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다. 

컨테이너 API에 대한 호스트 `http://localhost:5000`을 사용합니다.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](./face-resource-container-config.md#mount-settings)를 사용하여 컨테이너를 실행하고 로깅이 활성화된 경우 컨테이너는 컨테이너를 시작하거나 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

Face 서비스 컨테이너는 Azure 계정의 Face 리소스를 사용하여 청구 정보를 Azure로 보냅니다. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](./face-resource-container-config.md)을 참조하세요.

## <a name="summary"></a>요약

이 문서에서는 Face 서비스 컨테이너를 다운로드, 설치 및 실행하는 방법에 대한 개념과 워크플로에 대해 알아봤습니다. 요약하면 다음과 같습니다.

* 컨테이너 이미지는 Azure Container Registry에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하면 컨테이너의 호스트 URI를 지정하여 Face 서비스 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 계량을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 구성 설정은 [컨테이너 구성](face-resource-container-config.md)을 참조하세요.
* 얼굴을 감지하고 식별하는 방법에 대해 자세히 알아보려면 [Face 개요](Overview.md)를 참조하세요.
* 컨테이너에서 지원하는 메서드에 대한 자세한 내용은 [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 참조하세요.
* 더 많은 Cognitive Services 컨테이너를 사용하려면 [Cognitive Services 컨테이너](../cognitive-services-container-support.md)를 참조하세요.
