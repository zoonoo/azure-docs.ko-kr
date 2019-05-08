---
title: 컨테이너 설치 및 실행 - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer용 컨테이너를 다운로드, 설치 및 실행하는 방법입니다.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 2a62fd288f9118882e5cd9899ab572d4b247fc5a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156933"
---
# <a name="install-and-run-personalizer-containers"></a>Personalizer 컨테이너 설치 및 실행

Personalizer 서비스에는 다음과 같은 컨테이너가 있습니다. 

|함수|기능|
|-|-|
|Personalizer|콘텐츠 및 사용자의 현재 컨텍스트에서 최상의 작업을 결정합니다.|

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Personalizer 서비스 컨테이너를 사용하려면 먼저 다음 필수 조건을 충족해야 합니다.

|필수|목적|
|--|--|
|Docker 엔진| [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에서 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br>|
|Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다.| 
|Personalizer 서비스 리소스 |이러한 컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>연결된 청구 키 및 청구 엔드포인트 URI를 가져오는 _Personalizer Service_ Azure 리소스. 두 값은 모두 Azure Portal의 Personalizer 서비스 개요 및 키 페이지에 제공되며 컨테이너를 시작하는 데 필요합니다.<br><br>**{BILLING_KEY}**: 리소스 키<br><br>**{BILLING_ENDPOINT_URI}**: 엔드포인트 URI 예제: `https://westus.api.cognitive.microsoft.com/`|

### <a name="the-host-computer"></a>호스트 컴퓨터

**호스트**는 Docker 컨테이너를 실행하는 컴퓨터입니다. 다음을 포함하여 Azure에서 컴퓨터 온-프레미스 또Docker 호스팅 서비스일 수 있습니다.

* [Azure Kubernetes Service](https://docs.microsoft.com/aks/index.yml)
* [Azure Container Instances](https://docs.microsoft.com/container-instances/index.yml)
* [Kubernetes](https://kubernetes.io/) 클러스터는 [Azure Stack](https://docs.microsoft.com/azure-stack/index.yml)에 배포됩니다. 자세한 내용은 [Azure Stack에 Kubernetes 배포](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md)를 참조하세요.

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표에서는 각 Personalizer 서비스 컨테이너에 할당할 최소 및 권장 CPU 코어 수 및 메모리에 대해 설명합니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
|Personalizer | 1개 코어, 4GB 메모리 | 2개 코어, 8GB 메모리 |

각 코어는 속도가 2.6GHz 이상이어야 합니다.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

컨테이너는 Azure의 Personalizer 서버에 대한 순위 지정 및 보상 호출의 정보를 중계하기 위해 Azure EventHub에 연결할 수 있어야 하며, 필요한 구성 및 최신 기계 학습 모델을 로드하기 위해 Personalizer API에 연결할 수 있어야 합니다.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

Personalizer 서비스에 대한 컨테이너 이미지를 사용할 수 있습니다. 

| 컨테이너 | 리포지토리 |
|-----------|------------|
| Personalizer | `mcr.microsoft.com/azure-cognitive-services/personalizer:latest` |

> [!TIP]
> [docker images](https://docs.docker.com/engine/reference/commandline/images/) 명령을 사용하여 다운로드한 컨테이너 이미지를 나열할 수 있습니다. 예를 들어 다음 명령은 다운로드한 각 컨테이너 이미지의 ID, 리포지토리 및 태그를 테이블 형식으로 나열합니다.
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/personalizer                 latest
>  ``` 

### <a name="docker-pull-for-the-personalizer-service-container"></a>Personalizer 서비스 컨테이너에 대한 docker pull

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/personalizer:latest
```

## <a name="how-the-container-works"></a>컨테이너의 작동 원리

Personalizer 컨테이너는 Personalizer 서비스의 클라이언트 부분을 나타냅니다. 컨테이너를 실행하면 Azure 클라우드의 Personalizer 서비스에서 모델 및 구성 설정을 가져옵니다. 컨테이너 서비스는 이 정보를 사용하여 **순위 지정** 및 **보상** 요청에 응답합니다. 컨테이너는 이러한 요청을 Azure 클라우드의 Personalizer 리소스에도 보냅니다. 이러한 정보는 모델 업데이트 빈도에 대한 현재 설정을 기반으로 Personalizer 루프 모델을 교육하는 데 사용됩니다. 업데이트된 모델은 컨테이너로 다시 전송됩니다. 

![Personalizer 컨테이너의 클라이언트에 대한 온-프레미스, 로컬 아키텍처](./media/personalizer-container-howto/personalizer-container-architecture.png)

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](personalizer-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다. 
1. [컨테이너의 예측 엔드포인트를 쿼리합니다](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 세 컨테이너 중 하나를 실행합니다. 명령은 다음 매개 변수를 사용합니다.

| Placeholder | 값 |
|-------------|-------|
|{BILLING_KEY} | 이 키는 컨테이너를 시작하는 데 사용되며, Azure Portal의 Personalizer 서비스 키 페이지에 제공됩니다.  |
|{BILLING_ENDPOINT_URI} | 청구 엔드포인트 URI 값은 Azure Portal의 Personalizer 서비스 개요 페이지에 제공됩니다.|

다음 예`docker run`에서 매개 변수를 사용자 고유값으로 바꿉니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/personalizer\
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 Personalizer 서비스 컨테이너 실행
* 1개 CPU 코어 및 4GB 메모리 할당
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다. 

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

### <a name="run-multiple-containers-on-the-same-host"></a>동일한 호스트에서 여러 컨테이너 실행

노출된 포트로 여러 컨테이너를 실행하려는 경우, 각 컨테이너를 다른 포트로 실행해야 합니다. 예를 들어 첫 번째 컨테이너는 포트 5000에서 실행하고 두 번째 컨테이너는 포트 5001에서 실행합니다.

`<container-registry>`와 `<container-name>`을 사용하는 컨테이너의 값으로 대체합니다. 동일한 컨테이너일 필요는 없습니다. Personalizer 컨테이너와 LUIS 컨테이너를 HOST에서 함께 실행하거나 여러 개의 Personalizer 컨테이너를 실행할 수 있습니다. 

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

## <a name="stop-the-container"></a>컨테이너 중지

컨테이너를 종료하려면 컨테이너를 실행하는 명령줄 환경에서 **Ctrl + C**를 누릅니다.

## <a name="troubleshoot"></a>문제 해결

출력 [탑재](personalizer-container-configuration.md#mount-settings) 및 활성화된 로깅을 사용하여 컨테이너를 실행하는 경우 컨테이너는 컨테이너를 시작 또는 실행하는 동안 발생하는 문제를 해결하는 데 도움이 되는 로그 파일을 생성합니다. 

## <a name="container-api-documentation"></a>컨테이너 API 설명서

컨테이너는 `Try it now` 기능뿐만 아니라 엔드포인트에 대한 모든 설명서를 제공합니다. 이 기능을 사용하면 웹 기반 HTML 형식으로 설정을 입력할 수 있고 코드 작성 없이 쿼리를 만들 수 있습니다. 쿼리가 반환되면 예로 CURL 명령은 필요한 HTTP 헤더 및 본문 포맷을 데모하는 데 제공됩니다. 

> [!TIP]
> `/swagger` 상대 URI에서 컨테이너의 API 작업 지원을 설명하는 [OpenAPI 사양](https://swagger.io/docs/specification/about/)을 읽으세요. 예: 
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>결제

Personalizer 서비스 컨테이너는 Azure 계정의 _Personalizer 서비스_ 리소스를 사용하여 청구 정보를 Azure에 보냅니다. 

Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터를 Microsoft로 보내지 않습니다. 

`docker run` 명령은 청구 목적으로 다음 인수를 사용합니다.

| 옵션 | 설명 |
|--------|-------------|
| `ApiKey` | 청구 정보를 추적하는 데 사용되는 _Personalizer 서비스_ 리소스의 API 키입니다. |
| `Billing` | 청구 정보를 추적하는 데 사용되는 _Personalizer 서비스_ 리소스의 엔드포인트입니다.|
| `Eula` | 컨테이너에 대한 라이선스에 동의했음을 나타냅니다.<br/>이 옵션의 값은 `accept`로 설정해야 합니다. |

> [!IMPORTANT]
> 세 가지 옵션을 모두 유효한 값으로 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](personalizer-container-configuration.md)을 참조하세요.

## <a name="summary"></a>요약

이 문서에서는 Personalizer 서비스 컨테이너를 다운로드, 설치 및 실행하기 위한 개념과 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Personalizer 서비스는 개인 설정을 캡슐화하여 Docker용 Linux 컨테이너를 제공합니다.
* 컨테이너 이미지는 Azure의 MCR(Microsoft Container Registry)에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API 또는 SDK를 사용하여 컨테이너의 호스트 URI를 지정함으로써 Personalizer 서비스 컨테이너에서 작업을 호출할 수 있습니다.
* 컨테이너를 인스턴스화할 때 청구 정보를 지정해야 합니다.

> [!IMPORTANT]
> Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Personalizer 컨테이너는 온라인 교육을 목적으로 Azure의 해당 서비스에 요청 데이터를 보내고, Azure에서 업데이트된 모델을 정기적으로 가져옵니다.

## <a name="next-steps"></a>다음 단계

[`Docker Run` 명령에 대해 Docker 컨테이너를 구성하는 방법](personalizer-container-configuration.md)