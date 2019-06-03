---
title: 설치 하 고 컨테이너-이상 감지기를 실행
titleSuffix: Azure Cognitive Services
description: 시계열 데이터에서 변칙을 식별 하는 비정상 탐지기 API의 고급 알고리즘을 사용 합니다.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/28/2019
ms.author: aahi
ms.openlocfilehash: ec32d5de9e3af14abbf14f79f37ab00f3346b1c1
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306987"
---
# <a name="install-and-run-anomaly-detector-containers"></a>설치 하 고 이상 감지기 컨테이너 실행

변칙 검색 기가 다음 컨테이너에 있습니다. 

|함수|기능|
|-|-|
|Anomaly Detector| <li> 실시간으로 변칙을 검색 합니다. <li> 일괄 처리로 데이터 집합 전체에서 변칙을 검색합니다. <li> 데이터의 예상된 범위를 유추합니다. <li> 데이터에 맞게 더 잘 지원 변칙 검색 민감도 조정 합니다. |

Api에 대 한 자세한 내용은 다음을 참조 하세요.
* [비정상 탐지기 API 서비스에 자세히 알아보기](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

비정상 탐지기 컨테이너를 사용 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

|필수|목적|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.| 
|비정상 탐지기 리소스 |이러한 컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>_이상 감지기_ Azure 리소스 연결 된 청구 키 및 청구 끝점 URI 가져올 수 있습니다. 값이 모두 Azure portal의 비정상 감지기 개요 및 키 페이지에서 사용할 수 있는 및 컨테이너를 시작 하는 데 필요한 합니다.<br><br>**{BILLING_KEY}** : 리소스 키<br><br>**{BILLING_ENDPOINT_URI}** : 끝점 URI 예제: `https://westus2.api.cognitive.microsoft.com`|

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

먼저 완료 하 고 제출 합니다 [비정상 탐지기 컨테이너 요청 양식](https://aka.ms/adcontainer) 컨테이너에 액세스를 요청 합니다.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서 최소 및 권장 되는 CPU 코어 및 이상 감지기 컨테이너에 할당할 메모리를 설명 합니다.

| QPS (초당 쿼리 수) | 최소 | 권장 |
|-----------|---------|-------------|
| 10 QPS | 4 코어 및 1GB 메모리 | 8 코어 2GB 메모리 |
| 20 QPS | 8 코어, 2GB 메모리 | 16 코어 4GB 메모리 |

각 코어는 속도가 2.6GHz 이상이어야 합니다.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 컨테이너 이미지를 다운로드합니다.

| 컨테이너 | 리포지토리 |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>비정상 탐지기 컨테이너에 대 한 docker 가져오기

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>컨테이너 사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](anomaly-detector-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다. 
1. [컨테이너의 예측 엔드포인트를 쿼리합니다](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 세 컨테이너 중 하나를 실행합니다. 명령은 다음 매개 변수를 사용합니다.

| Placeholder | 값 |
|-------------|-------|
|{BILLING_KEY} | 이 키 컨테이너를 시작 하는 데 사용 되 고 Azure portal의 비정상 감지기 키 페이지에서 확인할 수 있습니다.  |
|{BILLING_ENDPOINT_URI} | 청구 끝점 URI 값은 Azure portal의 비정상 감지기 개요 페이지에서 사용할 수 있습니다.|

다음 예제 `docker run` 명령에서 해당 매개 변수를 사용자 고유의 값으로 바꿉니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

이 명령은 다음을 수행합니다.

* 이상 감지기 컨테이너를 컨테이너 이미지에서 실행 됩니다.
* 1개 CPU 코어 및 4GB 메모리를 할당합니다.
* TCP 5000 포트를 공개하고 컨테이너에 의사-TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다. 

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

### <a name="running-multiple-containers-on-the-same-host"></a>동일한 호스트에서 실행 중인 여러 컨테이너

노출된 포트로 여러 컨테이너를 실행하려는 경우, 각 컨테이너를 다른 포트로 실행해야 합니다. 예를 들어 첫 번째 컨테이너는 포트 5000에서 실행하고 두 번째 컨테이너는 포트 5001에서 실행합니다.

`<container-registry>`와 `<container-name>`을 사용하는 컨테이너의 값으로 대체합니다. 동일한 컨테이너일 필요는 없습니다. 비정상 탐지기 컨테이너와 함께 호스트에서 실행 중인 LUIS 컨테이너 할 수 있습니다 또는 실행 하는 여러 이상 감지기 컨테이너 할 수 있습니다. 

포트 5000에서 첫 번째 컨테이너를 실행합니다. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

포트 5001에서 두 번째 컨테이너를 실행합니다.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

이후의 각 컨테이너는 다른 포트에 있어야 합니다. 

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

컨테이너는 REST 기반 쿼리 예측 엔드포인트 API를 제공합니다. 

컨테이너 API에 대한 호스트, https://localhost:5000을 사용합니다.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

출력 [탑재](anomaly-detector-container-configuration.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다. 

## <a name="billing"></a>결제

청구 Azure에 대 한 정보를 사용 하 여 이상 감지기 컨테이너 송신을 _이상 감지기_ Azure 계정에는 리소스입니다. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](anomaly-detector-container-configuration.md)을 참조하세요.

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 개념 및 다운로드, 설치 및 실행 중인 컨테이너 이상 감지기에 대 한 워크플로 알아보았습니다. 요약하면 다음과 같습니다.

* 이상 감지기 제공 하나의 Linux 컨테이너 Docker 스트리밍 예상된 범위 유추 및 민감도 튜닝 하는 일괄 처리 vs를 사용 하 여 변칙 검색을 캡슐화 합니다.
* 컨테이너 이미지를 개인 Azure Container Registry 미리 보기 컨테이너에 대 한 전용된에서 다운로드 됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* 호스트 컨테이너의 URI를 지정 하 여 이상 감지기 컨테이너에서 작업을 호출 하려면 REST API 또는 SDK 중 하나를 사용할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 계량을 위한 청구 끝점에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너를 항상 청구 끝점과 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너 고객 데이터 (예: 시계열 데이터 분석 되는) Microsoft로 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* [컨테이너 구성](anomaly-detector-container-configuration.md)에서 구성 설정을 검토합니다.
* [비정상 탐지기 API 서비스에 자세히 알아보기](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
