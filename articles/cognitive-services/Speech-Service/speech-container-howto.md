---
title: 음성 컨테이너 설치 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 컨테이너를 설치하고 실행합니다. 음성 텍스트 변환은 오디오 스트림을 애플리케이션, 도구 또는 디바이스가 사용하거나 표시할 수 있는 텍스트로 실시간으로 기록합니다. 텍스트 음성 변환은 입력 텍스트를 인간과 유사한 합성 음성으로 변환합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 2beee81bc365d00e59a62cacabacc5f5d6b62a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474784"
---
# <a name="install-and-run-speech-service-containers-preview"></a>음성 서비스 컨테이너 설치 및 실행(미리 보기)

컨테이너를 사용하면 사용자 고유의 환경에서 일부 음성 서비스 API를 실행할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다. 이 문서에서는 음성 컨테이너를 다운로드, 설치 및 실행하는 방법을 배웁니다.

스피치 컨테이너를 사용하면 고객은 강력한 클라우드 기능과 에지 로컬에 최적화된 음성 응용 프로그램 아키텍처를 구축할 수 있습니다. 사용할 수 있는 네 가지 컨테이너가 있습니다. 두 개의 표준 컨테이너는 **음성 변환** 및 텍스트 **음성 변환입니다.** 두 개의 사용자 지정 컨테이너는 **사용자 지정 음성 변환** 및 사용자 지정 텍스트 음성 **변환입니다.**

> [!IMPORTANT]
> 모든 음성 컨테이너는 현재 [공용 "게이트" 미리 보기의](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio)일부로 제공됩니다. 음성 컨테이너가 GA(일반 가용성)로 진행되면 공지가 표시됩니다.

| 함수 | 기능 | 최신 버전 |
|--|--|--|
| 음성 텍스트 변환 | 연속 실시간 음성 또는 일괄 처리 오디오 녹음/녹화를 중간 결과 텍스트로 기록합니다. | 2.1.1 |
| 사용자 지정 음성-텍스트 | 사용자 정의 음성 [포털에서](https://speech.microsoft.com/customspeech)사용자 지정 모델을 사용하여 연속 실시간 음성 또는 배치 오디오 녹음을 중간 결과와 함께 텍스트로 전사합니다. | 2.1.1 |
| 텍스트 음성 변환 | 일반 텍스트 입력 또는 음성 합성 마크업 언어(SSML)를 사용하여 텍스트를 자연스러운 음성으로 변환합니다. | 1.3.0 |
| 사용자 지정 텍스트 음성 변환 | 사용자 정의 음성 [포털에서](https://aka.ms/custom-voice-portal)사용자 지정 모델을 사용 하 여 일반 텍스트 입력 또는 음성 합성 태그 언어 (SSML)와 자연 소리 음성으로 텍스트를 변환 합니다. | 1.3.0 |

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

음성 컨테이너를 사용하기 전에 다음 필수 구성 조건은 다음과 같은 것입니다.

| 필수 | 목적 |
|--|--|
| Docker 엔진 | [호스트 컴퓨터](#the-host-computer)에 설치된 Docker 엔진이 필요합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms)에 Docker 환경을 구성하는 패키지를 제공합니다. Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.<br><br> Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다. <br><br> **Windows**에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다.<br><br> |
| Docker 사용 경험 | 기본 `docker`명령에 대한 지식뿐만 아니라 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지와 같은 Docker 개념에 대해 기본적으로 이해해야 합니다. |
| 음성 리소스 | 이러한 컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>연결된 API 키 및 끝점 URI를 얻을 수 있는 Azure _음성_ 리소스입니다. 두 값 모두 Azure 포털의 **음성** 개요 및 키 페이지에서 사용할 수 있습니다. 둘 다 컨테이너를 시작 해야 합니다.<br><br>**{API_KEY}**: **키** 페이지에서 사용 가능한 두 리소스 키 중 하나<br><br>**{ENDPOINT_URI}**: **개요** 페이지에 제공된 끝점 |

## <a name="request-access-to-the-container-registry"></a>컨테이너 레지스트리에 대한 액세스 요청

채우기 및 [컨테이너에](https://aka.ms/speechcontainerspreview/) 대한 액세스를 요청하는 인지 서비스 음성 컨테이너 요청 양식을 제출합니다. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>고급 벡터 확장 지원

**호스트**는 Docker 컨테이너를 실행하는 컴퓨터입니다. 호스트는 [고급 벡터 확장(AVX2)을](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) *지원해야 합니다.* 다음 명령을 통해 Linux 호스트에서 AVX2 지원을 확인할 수 있습니다.

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> AVX2를 지원하려면 호스트 컴퓨터가 *필요합니다.* AVX2 지원 없이 컨테이너가 제대로 작동하지 *않습니다.*

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

다음 표는 각 Speech 컨테이너에 대한 최소 및 권장 리소스 할당에 대해 설명합니다.

# <a name="speech-to-text"></a>[음성-텍스트](#tab/stt)

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| 음성 텍스트 변환 | 2코어, 2GB 메모리 | 4코어, 4GB 메모리 |

# <a name="custom-speech-to-text"></a>[사용자 지정 음성-텍스트](#tab/cstt)

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| 사용자 지정 음성-텍스트 | 2코어, 2GB 메모리 | 4코어, 4GB 메모리 |

# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| 텍스트 음성 변환 | 1 코어, 2GB 메모리 | 2 코어, 3GB 메모리 |

# <a name="custom-text-to-speech"></a>[사용자 지정 텍스트 음성 변환](#tab/ctts)

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| 사용자 지정 텍스트 음성 변환 | 1 코어, 2GB 메모리 | 2 코어, 3GB 메모리 |

***

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.

> [!NOTE]
> 최소 및 권장 사항은 호스트 컴퓨터 리소스가 *아닌* Docker 제한을 기반으로 합니다. 예를 들어, 음성-텍스트 컨테이너는 큰 언어 모델의 맵 부분을 기억하며 추가로 4 ~ 6GB의 메모리에 전체 파일을 두는 것을 *권장* 합니다. 또한 모델은 메모리로 페이징되므로 두 컨테이너의 첫 번째 실행은 더 오래 걸릴 수 있습니다.

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

음성용 컨테이너 이미지는 다음 컨테이너 레지스트리에서 사용할 수 있습니다.

# <a name="speech-to-text"></a>[음성-텍스트](#tab/stt)

| 컨테이너 | 리포지토리 |
|-----------|------------|
| 음성 텍스트 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[사용자 지정 음성-텍스트](#tab/cstt)

| 컨테이너 | 리포지토리 |
|-----------|------------|
| 사용자 지정 음성-텍스트 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

| 컨테이너 | 리포지토리 |
|-----------|------------|
| 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[사용자 지정 텍스트 음성 변환](#tab/ctts)

| 컨테이너 | 리포지토리 |
|-----------|------------|
| 사용자 지정 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>음성 컨테이너에 대한 도커 풀

# <a name="speech-to-text"></a>[음성-텍스트](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>음성-텍스트 컨테이너에 대한 도커 풀

[선커 끌어오기](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 컨테이너 미리 보기 레지스트리에서 컨테이너 이미지를 다운로드합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> 태그가 `latest` 로캘을 `en-US` 가져옵니다. 추가 로캘은 [음성-텍스트 로캘을](#speech-to-text-locales)참조하십시오.

#### <a name="speech-to-text-locales"></a>음성-텍스트 로캘

다음 `latest` 형식을 제외한 모든 태그는 다음과 같은 형식이며 대/소문자를 구분합니다.

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

다음 태그는 해당 형식의 예입니다.

```
2.1.1-amd64-en-us-preview
```

**음성-텍스트** 컨테이너의 지원되는 모든 로캘에 대해서는 [음성-텍스트 이미지 태그를 참조하세요.](../containers/container-image-tags.md#speech-to-text)

# <a name="custom-speech-to-text"></a>[사용자 지정 음성-텍스트](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>사용자 지정 음성-텍스트 컨테이너에 대한 도커 풀

[선커 끌어오기](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 컨테이너 미리 보기 레지스트리에서 컨테이너 이미지를 다운로드합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> `locale` 및 `voice` 사용자 지정 음성 컨테이너의 경우 컨테이너에서 인더스트링한 사용자 지정 모델에 의해 결정됩니다.

# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>텍스트 음성 변환 컨테이너에 대한 도커 당기기

[선커 끌어오기](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 컨테이너 미리 보기 레지스트리에서 컨테이너 이미지를 다운로드합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> `latest` 태그는 `en-US` 로캘 및 `jessarus` 음성을 끌어오기 합니다. 추가 로캘은 [텍스트 음성 변환 로캘을](#text-to-speech-locales)참조하십시오.

#### <a name="text-to-speech-locales"></a>텍스트 음성 변환 로캘

다음 `latest` 형식을 제외한 모든 태그는 다음과 같은 형식이며 대/소문자를 구분합니다.

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

다음 태그는 해당 형식의 예입니다.

```
1.3.0-amd64-en-us-jessarus-preview
```

지원되는 모든 로캘 및 텍스트 **음성 변환** 컨테이너의 해당 음성에 대한 [텍스트 음성 변환 이미지 태그를](../containers/container-image-tags.md#text-to-speech)참조하세요.

> [!IMPORTANT]
> 표준 텍스트 *음성 변환* HTTP POST를 구성할 때 [SSML(음성 합성 태그 언어)](speech-synthesis-markup.md) `voice` `name` 메시지에는 특성이 있는 요소가 필요합니다. 값은 ["짧은 이름"이라고도](language-support.md#standard-voices)하는 해당 컨테이너 로캘 및 음성입니다. 예를 들어 `latest` 태그에는 의 `en-US-JessaRUS`음성 이름이 있습니다.

# <a name="custom-text-to-speech"></a>[사용자 지정 텍스트 음성 변환](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>사용자 지정 텍스트 음성 변환 컨테이너에 대한 도커 끌어오기

[선커 끌어오기](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 컨테이너 미리 보기 레지스트리에서 컨테이너 이미지를 다운로드합니다.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> `locale` 및 `voice` 사용자 지정 음성 컨테이너의 경우 컨테이너에서 인더스트링한 사용자 지정 모델에 의해 결정됩니다.

***

## <a name="how-to-use-the-container"></a>컨테이너사용 방법

컨테이너가 [호스트 컴퓨터](#the-host-computer)에 있으면 다음 프로세스를 사용하여 컨테이너 작업을 수행합니다.

1. 필수 청구 설정을 사용하여 [컨테이너를 실행](#run-the-container-with-docker-run)합니다. `docker run` 명령의 자세한 [예제](speech-container-configuration.md#example-docker-run-commands)를 사용할 수 있습니다.
1. [컨테이너의 예측 끝점을 쿼리합니다.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>`docker run`을 사용하여 컨테이너 실행

[Docker 실행](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 컨테이너를 실행합니다. `{Endpoint_URI}` 및 `{API_Key}` 값을 가져오는 방법에 대한 자세한 내용은 [필수 매개 변수 수집](#gathering-required-parameters)을 참조 하세요. `docker run` 명령의 추가 [예제도](speech-container-configuration.md#example-docker-run-commands) 사용할 수 있습니다.

# <a name="speech-to-text"></a>[음성-텍스트](#tab/stt)

*음성-텍스트* 컨테이너를 실행하려면 다음 `docker run` 명령을 실행합니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *음성-텍스트* 컨테이너를 실행합니다.
* 4개의 CPU 코어와 4기가바이트(GB)의 메모리를 할당합니다.
* TCP 포트(5000)를 노출하고 컨테이너에 의사 TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

# <a name="custom-speech-to-text"></a>[사용자 지정 음성-텍스트](#tab/cstt)

*사용자 지정 음성-텍스트* 컨테이너는 사용자 지정 음성 모델을 따릅니다. 사용자 지정 모델은 사용자 지정 [음성 포털을](https://speech.microsoft.com/customspeech)사용하여 [학습해야](how-to-custom-speech-train-model.md) 합니다.

> [!IMPORTANT]
> 사용자 지정 음성 모델은 다음 모델 버전 중 하나에서 학습해야 합니다.
> * **20181201 (v3.3 통합)**
> * **20190520 (v4.14 통합)**
> * **20190701 (v4.17 통합)**<br>
> ![사용자 지정 음성 학습 컨테이너 모델](media/custom-speech/custom-speech-train-model-container-scoped.png)

컨테이너를 실행하려면 사용자 지정 음성 **모델 ID가** 필요합니다. 사용자 지정 음성 포털의 **교육** 페이지에서 찾을 수 있습니다. 사용자 지정 음성 포털에서 **교육** 페이지로 이동하여 모델을 선택합니다.
<br>

![사용자 지정 음성 학습 페이지](media/custom-speech/custom-speech-model-training.png)

명령의 매개 변수에 대한 인수로 사용할 **모델 ID를** `ModelId` 가져옵니다. `docker run`
<br>

![사용자 지정 음성 모델 세부 정보](media/custom-speech/custom-speech-model-details.png)

다음 표는 다양한 `docker run` 매개 변수와 해당 설명을 나타냅니다.

| 매개 변수 | 설명 |
|---------|---------|
| `{VOLUME_MOUNT}` | docker가 사용자 지정 모델을 유지하는 데 사용하는 호스트 컴퓨터 [볼륨 마운트입니다.](https://docs.docker.com/storage/volumes/) 예를 들어 C *드라이브가* 호스트 컴퓨터에 있는 *C:\CustomSpeech입니다.* |
| `{MODEL_ID}` | 사용자 지정 음성 포털의 **학습** 페이지에서 사용자 지정 음성 **모델 ID입니다.** |
| `{ENDPOINT_URI}` | 측량 및 청구에는 끝점이 필요합니다. 자세한 내용은 [필수 매개변수 수집을](#gathering-required-parameters)참조하십시오. |
| `{API_KEY}` | API 키가 필요합니다. 자세한 내용은 [필수 매개변수 수집을](#gathering-required-parameters)참조하십시오. |

*사용자 지정 음성-텍스트* 컨테이너를 실행하려면 `docker run` 다음 명령을 실행합니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 사용자 지정 음성 변환 컨테이너를 *실행합니다.*
* 4개의 CPU 코어와 4기가바이트(GB)의 메모리를 할당합니다.
* 볼륨 입력 마운트에서 *사용자 지정 음성 변환* 변환 모델을 로드합니다(예: *C:\CustomSpeech.*
* TCP 포트(5000)를 노출하고 컨테이너에 의사 TTY를 할당합니다.
* 주어진 모델을 `ModelId` 다운로드합니다(볼륨 마운트에서 찾을 수 없는 경우).
* 사용자 지정 모델이 이전에 다운로드된 경우 는 `ModelId` 무시됩니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

# <a name="text-to-speech"></a>[텍스트 음성 변환](#tab/tts)

텍스트 음성 변환 컨테이너를 실행하려면 다음 `docker run` 명령을 *실행합니다.*

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *텍스트 음성 변환* 컨테이너를 실행합니다.
* 2개의 CPU 코어와 1기가바이트(GB)의 메모리를 할당합니다.
* TCP 포트(5000)를 노출하고 컨테이너에 의사 TTY를 할당합니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

# <a name="custom-text-to-speech"></a>[사용자 지정 텍스트 음성 변환](#tab/ctts)

*사용자 지정 텍스트 음성 변환* 컨테이너는 사용자 지정 음성 모델을 따릅니다. 사용자 지정 모델은 사용자 지정 [음성 포털을](https://aka.ms/custom-voice-portal)사용하여 [학습된](how-to-custom-voice-create-voice.md) 사용자 지정 모델입니다. 컨테이너를 실행하려면 사용자 지정 음성 **모델 ID가** 필요합니다. 사용자 지정 음성 포털의 **교육** 페이지에서 찾을 수 있습니다. 사용자 지정 음성 포털에서 **교육** 페이지로 이동하여 모델을 선택합니다.
<br>

![사용자 지정 음성 교육 페이지](media/custom-voice/custom-voice-model-training.png)

docker run 명령의 `ModelId` 매개 변수에 대한 인수로 사용할 모델 **ID를** 가져옵니다.
<br>

![사용자 정의 음성 모델 세부 정보](media/custom-voice/custom-voice-model-details.png)

다음 표는 다양한 `docker run` 매개 변수와 해당 설명을 나타냅니다.

| 매개 변수 | 설명 |
|---------|---------|
| `{VOLUME_MOUNT}` | docker가 사용자 지정 모델을 유지하는 데 사용하는 호스트 컴퓨터 [볼륨 마운트입니다.](https://docs.docker.com/storage/volumes/) 예를 들어 C *드라이브가* 호스트 컴퓨터에 있는 *C:\CustomSpeech입니다.* |
| `{MODEL_ID}` | 사용자 지정 음성 포털의 **학습** 페이지에서 사용자 지정 음성 **모델 ID입니다.** |
| `{ENDPOINT_URI}` | 측량 및 청구에는 끝점이 필요합니다. 자세한 내용은 [필수 매개변수 수집을](#gathering-required-parameters)참조하십시오. |
| `{API_KEY}` | API 키가 필요합니다. 자세한 내용은 [필수 매개변수 수집을](#gathering-required-parameters)참조하십시오. |

사용자 지정 텍스트 음성 변환 컨테이너를 실행하려면 다음 `docker run` 명령을 *실행합니다.*

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *사용자 지정 텍스트 음성 변환* 컨테이너를 실행합니다.
* 2개의 CPU 코어와 1기가바이트(GB)의 메모리를 할당합니다.
* 볼륨 입력 마운트에서 *사용자 지정 텍스트 음성 변환* 모델을 로드합니다(예: *C:\CustomVoice.*
* TCP 포트(5000)를 노출하고 컨테이너에 의사 TTY를 할당합니다.
* 주어진 모델을 `ModelId` 다운로드합니다(볼륨 마운트에서 찾을 수 없는 경우).
* 사용자 지정 모델이 이전에 다운로드된 경우 는 `ModelId` 무시됩니다.
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.

***

> [!IMPORTANT]
> 컨테이너를 인스턴스화하려면 `Eula`, `Billing` 및 `ApiKey` 옵션을 지정해야 합니다. 그렇지 않으면 컨테이너가 시작되지 않습니다.  자세한 내용은 [Billing](#billing)를 참조하세요.

## <a name="query-the-containers-prediction-endpoint"></a>컨테이너의 예측 엔드포인트 쿼리

| 컨테이너 | SDK 호스트 URL | 프로토콜 |
|--|--|--|
| 음성-텍스트 및 사용자 지정 음성-텍스트 | `ws://localhost:5000` | WS |
| 텍스트 음성 변환 및 사용자 지정 텍스트 음성 변환 | `http://localhost:5000` | HTTP |

WSS 및 HTTPS 프로토콜 사용에 대한 자세한 내용은 [컨테이너 보안](../cognitive-services-container-support.md#azure-cognitive-services-container-security)을 참조하십시오.

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>텍스트 음성 변환 또는 사용자 지정 텍스트 음성 변환

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>동일한 호스트에서 여러 컨테이너 실행

노출된 포트로 여러 컨테이너를 실행하려는 경우, 각 컨테이너를 다른 노출된 포트로 실행해야 합니다. 예를 들어 첫 번째 컨테이너는 포트 5000에서 실행하고 두 번째 컨테이너는 포트 5001에서 실행합니다.

이 컨테이너와 다른 Azure Cognitive Services 컨테이너를 HOST에서 함께 실행할 수 있습니다. 또한 동일한 Cognitive Services 컨테이너의 여러 컨테이너를 실행할 수 있습니다.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>컨테이너 중지

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>문제 해결

컨테이너를 시작하거나 실행할 때 문제가 발생할 수 있습니다. 출력 [마운트를](speech-container-configuration.md#mount-settings) 사용하고 로깅을 사용하도록 설정합니다. 이렇게 하면 컨테이너에서 문제를 해결할 때 유용한 로그 파일을 생성할 수 있습니다.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>결제

음성 컨테이너는 Azure 계정의 *음성* 리소스를 사용하여 청구 정보를 Azure로 보냅니다.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

이러한 옵션에 대한 자세한 내용은 [컨테이너 구성](speech-container-configuration.md)을 참조하세요.

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>요약

이 문서에서는 음성 컨테이너 다운로드, 설치 및 실행에 대한 개념 및 워크플로를 알아보았습니다. 요약하면 다음과 같습니다.

* Speech는 Docker용 4개의 Linux 컨테이너를 제공하여 다양한 기능을 캡슐화합니다.
  * *음성-텍스트*
  * *사용자 지정 음성-텍스트*
  * *텍스트 음성 변환*
  * *사용자 지정 텍스트 음성 변환*
* 컨테이너 이미지는 Azure의 컨테이너 레지스트리에서 다운로드됩니다.
* 컨테이너 이미지는 Docker에서 실행됩니다.
* REST API(텍스트 음성 변환 전용) 또는 SDK(음성-텍스트 또는 텍스트 음성 변환)를 사용하든 컨테이너의 호스트 URI를 지정합니다. 
* 컨테이너를 인스턴스화할 때 청구 정보를 제공해야 합니다.

> [!IMPORTANT]
>  Cognitive Services 컨테이너는 측광을 위해 Azure에 연결되지 않은 상태에서 실행할 수 있는 권한이 없습니다. 고객은 컨테이너에서 항상 계량 서비스와 청구 정보를 통신할 수 있도록 설정해야 합니다. Cognitive Services 컨테이너는 고객 데이터(예: 분석 중인 이미지 또는 텍스트)를 Microsoft에 보내지 않습니다.

## <a name="next-steps"></a>다음 단계

* 구성 설정에 대한 [컨테이너 구성](speech-container-configuration.md) 검토
* [Kubernetes 및 Helm에서 음성 서비스 컨테이너를 사용하는](speech-container-howto-on-premises.md) 방법 알아보기
* 더 많은 [코그너티브 서비스 컨테이너](../cognitive-services-container-support.md) 사용
