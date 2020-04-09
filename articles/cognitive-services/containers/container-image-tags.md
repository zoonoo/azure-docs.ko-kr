---
title: Cognitive Services 컨테이너 이미지 태그
titleSuffix: Azure Cognitive Services
description: 모든 코그너티브 서비스 컨테이너 이미지 태그의 포괄적인 목록입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 786fde90e1bcf47e08857c26235a27862c5b3d75
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878768"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure 코그너티브 서비스 컨테이너 이미지 태그

Azure 코그너티브 서비스는 많은 컨테이너 이미지를 제공합니다. 컨테이너 레지스트리 및 해당 리포지토리는 컨테이너 이미지에 따라 다릅니다. 각 컨테이너 이미지 이름은 여러 태그를 제공합니다. 컨테이너 이미지 태그는 컨테이너 이미지를 버전 화하는 메커니즘입니다. 이 문서는 모든 코그너티브 서비스 컨테이너 이미지와 사용 가능한 태그를 나열하기 위한 포괄적인 참조로 사용됩니다.

> [!TIP]
> 을 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)사용하는 경우 **대소문자를**구분하므로 컨테이너 레지스트리, 저장소, 컨테이너 이미지 이름 및 해당 태그의 대/소문자에 주의를 기울이기.

## <a name="anomaly-detector"></a>Anomaly Detector

[변칙 검출기][ad-containers] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. `microsoft` 리포지토리 내에 상주하며 의 `cognitive-services-anomaly-detector`이름이 지정됩니다. 정규화된 컨테이너 이미지 이름은 `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

이 컨테이너 이미지에는 다음과 같은 태그를 사용할 수 있습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Computer Vision

[컴퓨터 비전][cv-containers] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. `microsoft` 리포지토리 내에 상주하며 의 `cognitive-services-read`이름이 지정됩니다. 정규화된 컨테이너 이미지 이름은 `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

이 컨테이너 이미지에는 다음과 같은 태그를 사용할 수 있습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Face

[Face][fa-containers] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. `microsoft` 리포지토리 내에 상주하며 의 `cognitive-services-face`이름이 지정됩니다. 정규화된 컨테이너 이미지 이름은 `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

이 컨테이너 이미지에는 다음과 같은 태그를 사용할 수 있습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Form Recognizer

[양식 인식기][fr-containers] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. `microsoft` 리포지토리 내에 상주하며 의 `cognitive-services-form-recognizer`이름이 지정됩니다. 정규화된 컨테이너 이미지 이름은 `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

이 컨테이너 이미지에는 다음과 같은 태그를 사용할 수 있습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>언어 이해(LUIS)

[LUIS][lu-containers] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services` 리포지토리 내에 상주하며 의 `luis`이름이 지정됩니다. 정규화된 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/luis`.

이 컨테이너 이미지에는 다음과 같은 태그를 사용할 수 있습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>사용자 지정 음성-텍스트

[사용자 지정 음성-텍스트][sp-cstt] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. `microsoft` 리포지토리 내에 상주하며 의 `cognitive-services-custom-speech-to-text`이름이 지정됩니다. 정규화된 컨테이너 이미지 이름은 `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

이 컨테이너 이미지에는 다음과 같은 태그를 사용할 수 있습니다.

| 이미지 태그            | 메모 |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환

[사용자 지정 텍스트 음성 변환][sp-ctts] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. `microsoft` 리포지토리 내에 상주하며 의 `cognitive-services-custom-text-to-speech`이름이 지정됩니다. 정규화된 컨테이너 이미지 이름은 `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

이 컨테이너 이미지에는 다음과 같은 태그를 사용할 수 있습니다.

| 이미지 태그            | 메모 |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>음성 텍스트 변환

[음성-텍스트][sp-stt] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. `microsoft` 리포지토리 내에 상주하며 의 `cognitive-services-speech-to-text`이름이 지정됩니다. 정규화된 컨테이너 이미지 이름은 `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

이 컨테이너 이미지에는 다음과 같은 태그를 사용할 수 있습니다.

| 이미지 태그                  | 메모                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | 로캘이 `en-US` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ar-ae-preview` | 로캘이 `ar-AE` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ar-eg-preview` | 로캘이 `ar-EG` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ar-kw-preview` | 로캘이 `ar-KW` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ar-qa-preview` | 로캘이 `ar-QA` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ar-sa-preview` | 로캘이 `ar-SA` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ca-es-preview` | 로캘이 `ca-ES` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-da-dk-preview` | 로캘이 `da-DK` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-de-de-preview` | 로캘이 `de-DE` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-en-au-preview` | 로캘이 `en-AU` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-en-ca-preview` | 로캘이 `en-CA` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-en-gb-preview` | 로캘이 `en-GB` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-en-in-preview` | 로캘이 `en-IN` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-en-nz-preview` | 로캘이 `en-NZ` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-en-us-preview` | 로캘이 `en-US` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-es-es-preview` | 로캘이 `es-ES` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-es-mx-preview` | 로캘이 `es-MX` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-fi-fi-preview` | 로캘이 `fi-FI` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-fr-ca-preview` | 로캘이 `fr-CA` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-fr-fr-preview` | 로캘이 `fr-FR` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-gu-in-preview` | 로캘이 `gu-IN` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-hi-in-preview` | 로캘이 `hi-IN` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-it-it-preview` | 로캘이 `it-IT` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ja-jp-preview` | 로캘이 `ja-JP` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ko-kr-preview` | 로캘이 `ko-KR` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-mr-in-preview` | 로캘이 `mr-IN` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-nb-no-preview` | 로캘이 `nb-NO` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-nl-nl-preview` | 로캘이 `nl-NL` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-pl-pl-preview` | 로캘이 `pl-PL` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-pt-br-preview` | 로캘이 `pt-BR` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-pt-pt-preview` | 로캘이 `pt-PT` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ru-ru-preview` | 로캘이 `ru-RU` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-sv-se-preview` | 로캘이 `sv-SE` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ta-in-preview` | 로캘이 `ta-IN` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-te-in-preview` | 로캘이 `te-IN` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-th-th-preview` | 로캘이 `th-TH` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-tr-tr-preview` | 로캘이 `tr-TR` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-zh-cn-preview` | 로캘이 `zh-CN` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-zh-hk-preview` | 로캘이 `zh-HK` 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-zh-tw-preview` | 로캘이 `zh-TW` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ar-ae-preview` | 로캘이 `ar-AE` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ar-eg-preview` | 로캘이 `ar-EG` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ar-kw-preview` | 로캘이 `ar-KW` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ar-qa-preview` | 로캘이 `ar-QA` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ar-sa-preview` | 로캘이 `ar-SA` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ca-es-preview` | 로캘이 `ca-ES` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-da-dk-preview` | 로캘이 `da-DK` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-de-de-preview` | 로캘이 `de-DE` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-en-au-preview` | 로캘이 `en-AU` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-en-ca-preview` | 로캘이 `en-CA` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-en-gb-preview` | 로캘이 `en-GB` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-en-in-preview` | 로캘이 `en-IN` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-en-nz-preview` | 로캘이 `en-NZ` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-en-us-preview` | 로캘이 `en-US` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-es-es-preview` | 로캘이 `es-ES` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-es-mx-preview` | 로캘이 `es-MX` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-fi-fi-preview` | 로캘이 `fi-FI` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-fr-ca-preview` | 로캘이 `fr-CA` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-fr-fr-preview` | 로캘이 `fr-FR` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-gu-in-preview` | 로캘이 `gu-IN` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-hi-in-preview` | 로캘이 `hi-IN` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-it-it-preview` | 로캘이 `it-IT` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ja-jp-preview` | 로캘이 `ja-JP` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ko-kr-preview` | 로캘이 `ko-KR` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-mr-in-preview` | 로캘이 `mr-IN` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-nb-no-preview` | 로캘이 `nb-NO` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-nl-nl-preview` | 로캘이 `nl-NL` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-pl-pl-preview` | 로캘이 `pl-PL` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-pt-br-preview` | 로캘이 `pt-BR` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-pt-pt-preview` | 로캘이 `pt-PT` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ru-ru-preview` | 로캘이 `ru-RU` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-sv-se-preview` | 로캘이 `sv-SE` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ta-in-preview` | 로캘이 `ta-IN` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-te-in-preview` | 로캘이 `te-IN` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-th-th-preview` | 로캘이 `th-TH` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-tr-tr-preview` | 로캘이 `tr-TR` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-zh-cn-preview` | 로캘이 `zh-CN` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-zh-hk-preview` | 로캘이 `zh-HK` 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-zh-tw-preview` | 로캘이 `zh-TW` 있는 컨테이너 이미지입니다. |
| `2.0.3-amd64-ja-jp-preview` | 로캘이 `ja-JP` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ar-ae-preview` | 로캘이 `ar-AE` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ar-eg-preview` | 로캘이 `ar-EG` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ar-kw-preview` | 로캘이 `ar-KW` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ar-qa-preview` | 로캘이 `ar-QA` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ar-sa-preview` | 로캘이 `ar-SA` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ca-es-preview` | 로캘이 `ca-ES` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-da-dk-preview` | 로캘이 `da-DK` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-de-de-preview` | 로캘이 `de-DE` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-en-au-preview` | 로캘이 `en-AU` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-en-ca-preview` | 로캘이 `en-CA` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-en-gb-preview` | 로캘이 `en-GB` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-en-in-preview` | 로캘이 `en-IN` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-en-nz-preview` | 로캘이 `en-NZ` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-en-us-preview` | 로캘이 `en-US` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-es-es-preview` | 로캘이 `es-ES` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-es-mx-preview` | 로캘이 `es-MX` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-fi-fi-preview` | 로캘이 `fi-FI` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-fr-ca-preview` | 로캘이 `fr-CA` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-fr-fr-preview` | 로캘이 `fr-FR` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-gu-in-preview` | 로캘이 `gu-IN` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-hi-in-preview` | 로캘이 `hi-IN` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-it-it-preview` | 로캘이 `it-IT` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ja-jp-preview` | 로캘이 `ja-JP` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ko-kr-preview` | 로캘이 `ko-KR` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-mr-in-preview` | 로캘이 `mr-IN` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-nb-no-preview` | 로캘이 `nb-NO` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-nl-nl-preview` | 로캘이 `nl-NL` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-pl-pl-preview` | 로캘이 `pl-PL` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-pt-br-preview` | 로캘이 `pt-BR` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-pt-pt-preview` | 로캘이 `pt-PT` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ru-ru-preview` | 로캘이 `ru-RU` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-sv-se-preview` | 로캘이 `sv-SE` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ta-in-preview` | 로캘이 `ta-IN` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-te-in-preview` | 로캘이 `te-IN` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-th-th-preview` | 로캘이 `th-TH` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-tr-tr-preview` | 로캘이 `tr-TR` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-zh-cn-preview` | 로캘이 `zh-CN` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-zh-hk-preview` | 로캘이 `zh-HK` 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-zh-tw-preview` | 로캘이 `zh-TW` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ar-ae-preview` | 로캘이 `ar-AE` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ar-eg-preview` | 로캘이 `ar-EG` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ar-kw-preview` | 로캘이 `ar-KW` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ar-qa-preview` | 로캘이 `ar-QA` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ar-sa-preview` | 로캘이 `ar-SA` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ca-es-preview` | 로캘이 `ca-ES` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-da-dk-preview` | 로캘이 `da-DK` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-de-de-preview` | 로캘이 `de-DE` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-en-au-preview` | 로캘이 `en-AU` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-en-ca-preview` | 로캘이 `en-CA` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-en-gb-preview` | 로캘이 `en-GB` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-en-in-preview` | 로캘이 `en-IN` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-en-nz-preview` | 로캘이 `en-NZ` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-en-us-preview` | 로캘이 `en-US` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-es-es-preview` | 로캘이 `es-ES` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-es-mx-preview` | 로캘이 `es-MX` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-fi-fi-preview` | 로캘이 `fi-FI` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-fr-ca-preview` | 로캘이 `fr-CA` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-fr-fr-preview` | 로캘이 `fr-FR` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-gu-in-preview` | 로캘이 `gu-IN` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-hi-in-preview` | 로캘이 `hi-IN` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-it-it-preview` | 로캘이 `it-IT` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ja-jp-preview` | 로캘이 `ja-JP` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ko-kr-preview` | 로캘이 `ko-KR` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-mr-in-preview` | 로캘이 `mr-IN` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-nb-no-preview` | 로캘이 `nb-NO` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-nl-nl-preview` | 로캘이 `nl-NL` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-pl-pl-preview` | 로캘이 `pl-PL` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-pt-br-preview` | 로캘이 `pt-BR` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-pt-pt-preview` | 로캘이 `pt-PT` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ru-ru-preview` | 로캘이 `ru-RU` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-sv-se-preview` | 로캘이 `sv-SE` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ta-in-preview` | 로캘이 `ta-IN` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-te-in-preview` | 로캘이 `te-IN` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-th-th-preview` | 로캘이 `th-TH` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-tr-tr-preview` | 로캘이 `tr-TR` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-zh-cn-preview` | 로캘이 `zh-CN` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-zh-hk-preview` | 로캘이 `zh-HK` 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-zh-tw-preview` | 로캘이 `zh-TW` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-ar-eg-preview` | 로캘이 `ar-EG` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-ca-es-preview` | 로캘이 `ca-ES` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-da-dk-preview` | 로캘이 `da-DK` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-de-de-preview` | 로캘이 `de-DE` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-en-au-preview` | 로캘이 `en-AU` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-en-ca-preview` | 로캘이 `en-CA` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-en-gb-preview` | 로캘이 `en-GB` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-en-in-preview` | 로캘이 `en-IN` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-en-nz-preview` | 로캘이 `en-NZ` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-en-us-preview` | 로캘이 `en-US` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-es-es-preview` | 로캘이 `es-ES` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-es-mx-preview` | 로캘이 `es-MX` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-fi-fi-preview` | 로캘이 `fi-FI` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-fr-ca-preview` | 로캘이 `fr-CA` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-fr-fr-preview` | 로캘이 `fr-FR` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-hi-in-preview` | 로캘이 `hi-IN` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-it-it-preview` | 로캘이 `it-IT` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-ja-jp-preview` | 로캘이 `ja-JP` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-ko-kr-preview` | 로캘이 `ko-KR` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-nb-no-preview` | 로캘이 `nb-NO` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-nl-nl-preview` | 로캘이 `nl-NL` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-pl-pl-preview` | 로캘이 `pl-PL` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-pt-br-preview` | 로캘이 `pt-BR` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-pt-pt-preview` | 로캘이 `pt-PT` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-ru-ru-preview` | 로캘이 `ru-RU` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-sv-se-preview` | 로캘이 `sv-SE` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-th-th-preview` | 로캘이 `th-TH` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-tr-tr-preview` | 로캘이 `tr-TR` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-zh-cn-preview` | 로캘이 `zh-CN` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-zh-hk-preview` | 로캘이 `zh-HK` 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-zh-tw-preview` | 로캘이 `zh-TW` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-de-de-preview` | 로캘이 `de-DE` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-en-au-preview` | 로캘이 `en-AU` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-en-ca-preview` | 로캘이 `en-CA` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-en-gb-preview` | 로캘이 `en-GB` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-en-in-preview` | 로캘이 `en-IN` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-en-us-preview` | 로캘이 `en-US` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-es-es-preview` | 로캘이 `es-ES` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-es-mx-preview` | 로캘이 `es-MX` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-fr-ca-preview` | 로캘이 `fr-CA` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-fr-fr-preview` | 로캘이 `fr-FR` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-it-it-preview` | 로캘이 `it-IT` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-ja-jp-preview` | 로캘이 `ja-JP` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-pt-br-preview` | 로캘이 `pt-BR` 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-zh-cn-preview` | 로캘이 `zh-CN` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-de-de-preview` | 로캘이 `de-DE` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-en-au-preview` | 로캘이 `en-AU` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-en-ca-preview` | 로캘이 `en-CA` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-en-gb-preview` | 로캘이 `en-GB` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-en-in-preview` | 로캘이 `en-IN` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-en-us-preview` | 로캘이 `en-US` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-es-es-preview` | 로캘이 `es-ES` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-es-mx-preview` | 로캘이 `es-MX` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-fr-ca-preview` | 로캘이 `fr-CA` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-fr-fr-preview` | 로캘이 `fr-FR` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-it-it-preview` | 로캘이 `it-IT` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-ja-jp-preview` | 로캘이 `ja-JP` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-pt-br-preview` | 로캘이 `pt-BR` 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-zh-cn-preview` | 로캘이 `zh-CN` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-de-de-preview` | 로캘이 `de-DE` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-en-au-preview` | 로캘이 `en-AU` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-en-ca-preview` | 로캘이 `en-CA` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-en-gb-preview` | 로캘이 `en-GB` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-en-in-preview` | 로캘이 `en-IN` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-en-us-preview` | 로캘이 `en-US` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-es-es-preview` | 로캘이 `es-ES` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-es-mx-preview` | 로캘이 `es-MX` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-fr-ca-preview` | 로캘이 `fr-CA` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-fr-fr-preview` | 로캘이 `fr-FR` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-it-it-preview` | 로캘이 `it-IT` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-ja-jp-preview` | 로캘이 `ja-JP` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-pt-br-preview` | 로캘이 `pt-BR` 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-zh-cn-preview` | 로캘이 `zh-CN` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-de-de-preview` | 로캘이 `de-DE` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-en-au-preview` | 로캘이 `en-AU` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-en-ca-preview` | 로캘이 `en-CA` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-en-gb-preview` | 로캘이 `en-GB` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-en-in-preview` | 로캘이 `en-IN` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-en-us-preview` | 로캘이 `en-US` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-es-es-preview` | 로캘이 `es-ES` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-es-mx-preview` | 로캘이 `es-MX` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-fr-ca-preview` | 로캘이 `fr-CA` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-fr-fr-preview` | 로캘이 `fr-FR` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-it-it-preview` | 로캘이 `it-IT` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-ja-jp-preview` | 로캘이 `ja-JP` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-pt-br-preview` | 로캘이 `pt-BR` 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-zh-cn-preview` | 로캘이 `zh-CN` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-de-de-preview` | 로캘이 `de-DE` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-en-au-preview` | 로캘이 `en-AU` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-en-ca-preview` | 로캘이 `en-CA` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-en-gb-preview` | 로캘이 `en-GB` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-en-in-preview` | 로캘이 `en-IN` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-en-us-preview` | 로캘이 `en-US` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-es-es-preview` | 로캘이 `es-ES` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-es-mx-preview` | 로캘이 `es-MX` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-fr-ca-preview` | 로캘이 `fr-CA` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-fr-fr-preview` | 로캘이 `fr-FR` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-it-it-preview` | 로캘이 `it-IT` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-ja-jp-preview` | 로캘이 `ja-JP` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-pt-br-preview` | 로캘이 `pt-BR` 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-zh-cn-preview` | 로캘이 `zh-CN` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-de-de-preview` | 로캘이 `de-DE` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-en-au-preview` | 로캘이 `en-AU` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-en-ca-preview` | 로캘이 `en-CA` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-en-gb-preview` | 로캘이 `en-GB` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-en-in-preview` | 로캘이 `en-IN` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-en-us-preview` | 로캘이 `en-US` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-es-es-preview` | 로캘이 `es-ES` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-es-mx-preview` | 로캘이 `es-MX` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-fr-ca-preview` | 로캘이 `fr-CA` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-fr-fr-preview` | 로캘이 `fr-FR` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-it-it-preview` | 로캘이 `it-IT` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-ja-jp-preview` | 로캘이 `ja-JP` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-pt-br-preview` | 로캘이 `pt-BR` 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-zh-cn-preview` | 로캘이 `zh-CN` 있는 컨테이너 이미지입니다. |

## <a name="text-to-speech"></a>텍스트 음성 변환

[텍스트 음성 변환][sp-tts] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. `microsoft` 리포지토리 내에 상주하며 의 `cognitive-services-text-to-speech`이름이 지정됩니다. 정규화된 컨테이너 이미지 이름은 `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

이 컨테이너 이미지에는 다음과 같은 태그를 사용할 수 있습니다.

| 이미지 태그                                  | 메모                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | 로캘과 `en-US-JessaRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | 로캘과 `ar-EG-Hoda` `ar-EG` 음성이 있는 컨테이너 이미지입니다.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | 로캘과 `ar-SA-Naayf` `ar-SA` 음성이 있는 컨테이너 이미지입니다.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | 로캘과 `bg-BG-Ivan` `bg-BG` 음성이 있는 컨테이너 이미지입니다.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | 로캘과 `ca-ES-HerenaRUS` `ca-ES` 음성이 있는 컨테이너 이미지입니다.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | 로캘과 `cs-CZ-Jakub` `cs-CZ` 음성이 있는 컨테이너 이미지입니다.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | 로캘과 `da-DK-HelleRUS` `da-DK` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-de-at-michael-preview`         | 로캘과 `de-AT-Michael` `de-AT` 음성이 있는 컨테이너 이미지입니다.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | 로캘과 `de-CH-Karsten` `de-CH` 음성이 있는 컨테이너 이미지입니다.         |
| `1.3.0-amd64-de-de-hedda-preview`           | 로캘과 `de-DE-Hedda` `de-DE` 음성이 있는 컨테이너 이미지입니다.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | 로캘과 `de-DE-Hedda` `de-DE` 음성이 있는 컨테이너 이미지입니다.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | 로캘과 `de-DE-HeddaRUS` `de-DE` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | 로캘과 `de-DE-Stefan-Apollo` `de-DE` 음성이 있는 컨테이너 이미지입니다.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | 로캘과 `el-GR-Stefanos` `el-GR` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-en-au-catherine-preview`       | 로캘과 `en-AU-Catherine` `en-AU` 음성이 있는 컨테이너 이미지입니다.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | 로캘과 `en-AU-HayleyRUS` `en-AU` 음성이 있는 컨테이너 이미지입니다.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | 로캘과 `en-CA-HeatherRUS` `en-CA` 음성이 있는 컨테이너 이미지입니다.      |
| `1.3.0-amd64-en-ca-linda-preview`           | 로캘과 `en-CA-Linda` `en-CA` 음성이 있는 컨테이너 이미지입니다.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | 로캘과 `en-GB-George-Apollo` `en-GB` 음성이 있는 컨테이너 이미지입니다.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | 로캘과 `en-GB-HazelRUS` `en-GB` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | 로캘과 `en-GB-Susan-Apollo` `en-GB` 음성이 있는 컨테이너 이미지입니다.    |
| `1.3.0-amd64-en-ie-sean-preview`            | 로캘과 `en-IE-Sean` `en-IE` 음성이 있는 컨테이너 이미지입니다.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | 로캘과 `en-IN-Heera-Apollo` `en-IN` 음성이 있는 컨테이너 이미지입니다.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | 로캘과 `en-IN-PriyaRUS` `en-IN` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | 로캘과 `en-IN-Ravi-Apollo` `en-IN` 음성이 있는 컨테이너 이미지입니다.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | 로캘과 `en-US-BenjaminRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | 로캘과 `en-US-Guy24kRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | 로캘과 `en-US-Jessa24kRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | 로캘과 `en-US-JessaRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | 로캘과 `en-US-ZiraRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | 로캘과 `es-ES-HelenaRUS` `es-ES` 음성이 있는 컨테이너 이미지입니다.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | 로캘과 `es-ES-Laura-Apollo` `es-ES` 음성이 있는 컨테이너 이미지입니다.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | 로캘과 `es-ES-Pablo-Apollo` `es-ES` 음성이 있는 컨테이너 이미지입니다.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | 로캘과 `es-MX-HildaRUS` `es-MX` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | 로캘과 `es-MX-Raul-Apollo` `es-MX` 음성이 있는 컨테이너 이미지입니다.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | 로캘과 `fi-FI-HeidiRUS` `fi-FI` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | 로캘과 `fr-CA-Caroline` `fr-CA` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | 로캘과 `fr-CA-HarmonieRUS` `fr-CA` 음성이 있는 컨테이너 이미지입니다.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | 로캘과 `fr-CH-Guillaume` `fr-CH` 음성이 있는 컨테이너 이미지입니다.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | 로캘과 `fr-FR-HortenseRUS` `fr-FR` 음성이 있는 컨테이너 이미지입니다.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | 로캘과 `fr-FR-Julie-Apollo` `fr-FR` 음성이 있는 컨테이너 이미지입니다.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | 로캘과 `fr-FR-Paul-Apollo` `fr-FR` 음성이 있는 컨테이너 이미지입니다.     |
| `1.3.0-amd64-he-il-asaf-preview`            | 로캘과 `he-IL-Asaf` `he-IL` 음성이 있는 컨테이너 이미지입니다.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | 로캘과 `hi-IN-Hemant` `hi-IN` 음성이 있는 컨테이너 이미지입니다.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | 로캘과 `hi-IN-Kalpana-Apollo` `hi-IN` 음성이 있는 컨테이너 이미지입니다.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | 로캘과 `hi-IN-Kalpana` `hi-IN` 음성이 있는 컨테이너 이미지입니다.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | 로캘과 `hi-IN-Kalpana` `hi-IN` 음성이 있는 컨테이너 이미지입니다.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | 로캘과 `hr-HR-Matej` `hr-HR` 음성이 있는 컨테이너 이미지입니다.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | 로캘과 `hu-HU-Szabolcs` `hu-HU` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-id-id-andika-preview`          | 로캘과 `id-ID-Andika` `id-ID` 음성이 있는 컨테이너 이미지입니다.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | 로캘과 `it-IT-Cosimo-Apollo` `it-IT` 음성이 있는 컨테이너 이미지입니다.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | 로캘과 `it-IT-LuciaRUS` `it-IT` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | 로캘과 `ja-JP-Ayumi-Apollo` `ja-JP` 음성이 있는 컨테이너 이미지입니다.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | 로캘과 `ja-JP-HarukaRUS` `ja-JP` 음성이 있는 컨테이너 이미지입니다.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | 로캘과 `ja-JP-Ichiro-Apollo` `ja-JP` 음성이 있는 컨테이너 이미지입니다.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | 로캘과 `ko-KR-HeamiRUS` `ko-KR` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | 로캘과 `ms-MY-Rizwan` `ms-MY` 음성이 있는 컨테이너 이미지입니다.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | 로캘과 `nb-NO-HuldaRUS` `nb-NO` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | 로캘과 `nl-NL-HannaRUS` `nl-NL` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | 로캘과 `pl-PL-PaulinaRUS` `pl-PL` 음성이 있는 컨테이너 이미지입니다.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | 로캘과 `pt-BR-Daniel-Apollo` `pt-BR` 음성이 있는 컨테이너 이미지입니다.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | 로캘과 `pt-BR-HeloisaRUS` `pt-BR` 음성이 있는 컨테이너 이미지입니다.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | 로캘과 `pt-PT-HeliaRUS` `pt-PT` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | 로캘과 `ro-RO-Andrei` `ro-RO` 음성이 있는 컨테이너 이미지입니다.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | 로캘과 `ru-RU-EkaterinaRUS` `ru-RU` 음성이 있는 컨테이너 이미지입니다.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | 로캘과 `ru-RU-Irina-Apollo` `ru-RU` 음성이 있는 컨테이너 이미지입니다.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | 로캘과 `ru-RU-Pavel-Apollo` `ru-RU` 음성이 있는 컨테이너 이미지입니다.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | 로캘과 `sk-SK-Filip` `sk-SK` 음성이 있는 컨테이너 이미지입니다.           |
| `1.3.0-amd64-sl-si-lado-preview`            | 로캘과 `sl-SI-Lado` `sl-SI` 음성이 있는 컨테이너 이미지입니다.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | 로캘과 `sv-SE-HedvigRUS` `sv-SE` 음성이 있는 컨테이너 이미지입니다.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | 로캘과 `ta-IN-Valluvar` `ta-IN` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-te-in-chitra-preview`          | 로캘과 `te-IN-Chitra` `te-IN` 음성이 있는 컨테이너 이미지입니다.          |
| `1.3.0-amd64-th-th-pattara-preview`         | 로캘과 `th-TH-Pattara` `th-TH` 음성이 있는 컨테이너 이미지입니다.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | 로캘과 `tr-TR-SedaRUS` `tr-TR` 음성이 있는 컨테이너 이미지입니다.         |
| `1.3.0-amd64-vi-vn-an-preview`              | 로캘과 `vi-VN-An` `vi-VN` 음성이 있는 컨테이너 이미지입니다.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | 로캘과 `zh-CN-HuihuiRUS` `zh-CN` 음성이 있는 컨테이너 이미지입니다.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | 로캘과 `zh-CN-Kangkang-Apollo` `zh-CN` 음성이 있는 컨테이너 이미지입니다. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | 로캘과 `zh-CN-Yaoyao-Apollo` `zh-CN` 음성이 있는 컨테이너 이미지입니다.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | 로캘과 `zh-HK-Danny-Apollo` `zh-HK` 음성이 있는 컨테이너 이미지입니다.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | 로캘과 `zh-HK-Tracy-Apollo` `zh-HK` 음성이 있는 컨테이너 이미지입니다.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | 로캘과 `zh-HK-TracyRUS` `zh-HK` 음성이 있는 컨테이너 이미지입니다.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | 로캘과 `zh-TW-HanHanRUS` `zh-TW` 음성이 있는 컨테이너 이미지입니다.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | 로캘과 `zh-TW-Yating-Apollo` `zh-TW` 음성이 있는 컨테이너 이미지입니다.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | 로캘과 `zh-TW-Zhiwei-Apollo` `zh-TW` 음성이 있는 컨테이너 이미지입니다.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | 로캘과 `de-DE-Hedda` `de-DE` 음성이 있는 컨테이너 이미지입니다.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | 로캘과 `de-DE-HeddaRUS` `de-DE` 음성이 있는 컨테이너 이미지입니다.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | 로캘과 `de-DE-Stefan-Apollo` `de-DE` 음성이 있는 컨테이너 이미지입니다.   |
| `1.2.0-amd64-en-au-catherine-preview`       | 로캘과 `en-AU-Catherine` `en-AU` 음성이 있는 컨테이너 이미지입니다.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | 로캘과 `en-AU-HayleyRUS` `en-AU` 음성이 있는 컨테이너 이미지입니다.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | 로캘과 `en-GB-George-Apollo` `en-GB` 음성이 있는 컨테이너 이미지입니다.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | 로캘과 `en-GB-HazelRUS` `en-GB` 음성이 있는 컨테이너 이미지입니다.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | 로캘과 `en-GB-Susan-Apollo` `en-GB` 음성이 있는 컨테이너 이미지입니다.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | 로캘과 `en-IN-Heera-Apollo` `en-IN` 음성이 있는 컨테이너 이미지입니다.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | 로캘과 `en-IN-PriyaRUS` `en-IN` 음성이 있는 컨테이너 이미지입니다.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | 로캘과 `en-IN-Ravi-Apollo` `en-IN` 음성이 있는 컨테이너 이미지입니다.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | 로캘과 `en-US-BenjaminRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | 로캘과 `en-US-Guy24kRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | 로캘과 `en-US-Jessa24kRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | 로캘과 `en-US-JessaRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | 로캘과 `en-US-ZiraRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | 로캘과 `es-ES-HelenaRUS` `es-ES` 음성이 있는 컨테이너 이미지입니다.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | 로캘과 `es-ES-Laura-Apollo` `es-ES` 음성이 있는 컨테이너 이미지입니다.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | 로캘과 `es-ES-Pablo-Apollo` `es-ES` 음성이 있는 컨테이너 이미지입니다.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | 로캘과 `es-MX-HildaRUS` `es-MX` 음성이 있는 컨테이너 이미지입니다.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | 로캘과 `es-MX-Raul-Apollo` `es-MX` 음성이 있는 컨테이너 이미지입니다.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | 로캘과 `fr-CA-Caroline` `fr-CA` 음성이 있는 컨테이너 이미지입니다.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | 로캘과 `fr-CA-HarmonieRUS` `fr-CA` 음성이 있는 컨테이너 이미지입니다.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | 로캘과 `fr-FR-HortenseRUS` `fr-FR` 음성이 있는 컨테이너 이미지입니다.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | 로캘과 `fr-FR-Julie-Apollo` `fr-FR` 음성이 있는 컨테이너 이미지입니다.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | 로캘과 `fr-FR-Paul-Apollo` `fr-FR` 음성이 있는 컨테이너 이미지입니다.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | 로캘과 `it-IT-Cosimo-Apollo` `it-IT` 음성이 있는 컨테이너 이미지입니다.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | 로캘과 `it-IT-LuciaRUS` `it-IT` 음성이 있는 컨테이너 이미지입니다.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | 로캘과 `ja-JP-Ayumi-Apollo` `ja-JP` 음성이 있는 컨테이너 이미지입니다.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | 로캘과 `ja-JP-HarukaRUS` `ja-JP` 음성이 있는 컨테이너 이미지입니다.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | 로캘과 `ja-JP-Ichiro-Apollo` `ja-JP` 음성이 있는 컨테이너 이미지입니다.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | 로캘과 `ko-KR-HeamiRUS` `ko-KR` 음성이 있는 컨테이너 이미지입니다.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | 로캘과 `pt-BR-Daniel-Apollo` `pt-BR` 음성이 있는 컨테이너 이미지입니다.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | 로캘과 `pt-BR-HeloisaRUS` `pt-BR` 음성이 있는 컨테이너 이미지입니다.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | 로캘과 `zh-CN-HuihuiRUS` `zh-CN` 음성이 있는 컨테이너 이미지입니다.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | 로캘과 `zh-CN-Kangkang-Apollo` `zh-CN` 음성이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | 로캘과 `zh-CN-Yaoyao-Apollo` `zh-CN` 음성이 있는 컨테이너 이미지입니다.   |
| `1.1.0-amd64-de-de-hedda-preview`           | 로캘과 `de-DE-Hedda` `de-DE` 음성이 있는 컨테이너 이미지입니다.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | 로캘과 `de-DE-Hedda` `de-DE` 음성이 있는 컨테이너 이미지입니다.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | 로캘과 `de-DE-HeddaRUS` `de-DE` 음성이 있는 컨테이너 이미지입니다.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | 로캘과 `de-DE-Stefan-Apollo` `de-DE` 음성이 있는 컨테이너 이미지입니다.   |
| `1.1.0-amd64-en-au-catherine-preview`       | 로캘과 `en-AU-Catherine` `en-AU` 음성이 있는 컨테이너 이미지입니다.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | 로캘과 `en-AU-HayleyRUS` `en-AU` 음성이 있는 컨테이너 이미지입니다.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | 로캘과 `en-GB-George-Apollo` `en-GB` 음성이 있는 컨테이너 이미지입니다.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | 로캘과 `en-GB-HazelRUS` `en-GB` 음성이 있는 컨테이너 이미지입니다.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | 로캘과 `en-GB-Susan-Apollo` `en-GB` 음성이 있는 컨테이너 이미지입니다.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | 로캘과 `en-IN-Heera-Apollo` `en-IN` 음성이 있는 컨테이너 이미지입니다.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | 로캘과 `en-IN-PriyaRUS` `en-IN` 음성이 있는 컨테이너 이미지입니다.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | 로캘과 `en-IN-Ravi-Apollo` `en-IN` 음성이 있는 컨테이너 이미지입니다.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | 로캘과 `en-US-BenjaminRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | 로캘과 `en-US-Guy24kRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | 로캘과 `en-US-Jessa24kRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | 로캘과 `en-US-JessaRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | 로캘과 `en-US-ZiraRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | 로캘과 `es-ES-HelenaRUS` `es-ES` 음성이 있는 컨테이너 이미지입니다.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | 로캘과 `es-ES-Laura-Apollo` `es-ES` 음성이 있는 컨테이너 이미지입니다.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | 로캘과 `es-ES-Pablo-Apollo` `es-ES` 음성이 있는 컨테이너 이미지입니다.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | 로캘과 `es-MX-HildaRUS` `es-MX` 음성이 있는 컨테이너 이미지입니다.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | 로캘과 `es-MX-Raul-Apollo` `es-MX` 음성이 있는 컨테이너 이미지입니다.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | 로캘과 `fr-CA-Caroline` `fr-CA` 음성이 있는 컨테이너 이미지입니다.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | 로캘과 `fr-CA-HarmonieRUS` `fr-CA` 음성이 있는 컨테이너 이미지입니다.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | 로캘과 `fr-FR-HortenseRUS` `fr-FR` 음성이 있는 컨테이너 이미지입니다.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | 로캘과 `fr-FR-Julie-Apollo` `fr-FR` 음성이 있는 컨테이너 이미지입니다.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | 로캘과 `fr-FR-Paul-Apollo` `fr-FR` 음성이 있는 컨테이너 이미지입니다.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | 로캘과 `it-IT-Cosimo-Apollo` `it-IT` 음성이 있는 컨테이너 이미지입니다.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | 로캘과 `it-IT-LuciaRUS` `it-IT` 음성이 있는 컨테이너 이미지입니다.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | 로캘과 `ja-JP-Ayumi-Apollo` `ja-JP` 음성이 있는 컨테이너 이미지입니다.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | 로캘과 `ja-JP-HarukaRUS` `ja-JP` 음성이 있는 컨테이너 이미지입니다.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | 로캘과 `ja-JP-Ichiro-Apollo` `ja-JP` 음성이 있는 컨테이너 이미지입니다.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | 로캘과 `ko-KR-HeamiRUS` `ko-KR` 음성이 있는 컨테이너 이미지입니다.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | 로캘과 `pt-BR-Daniel-Apollo` `pt-BR` 음성이 있는 컨테이너 이미지입니다.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | 로캘과 `pt-BR-HeloisaRUS` `pt-BR` 음성이 있는 컨테이너 이미지입니다.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | 로캘과 `zh-CN-HuihuiRUS` `zh-CN` 음성이 있는 컨테이너 이미지입니다.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | 로캘과 `zh-CN-Kangkang-Apollo` `zh-CN` 음성이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | 로캘과 `zh-CN-Yaoyao-Apollo` `zh-CN` 음성이 있는 컨테이너 이미지입니다.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | 로캘과 `en-US-BenjaminRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | 로캘과 `en-US-Guy24kRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | 로캘과 `en-US-Jessa24kRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | 로캘과 `en-US-JessaRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | 로캘과 `en-US-ZiraRUS` `en-US` 음성이 있는 컨테이너 이미지입니다.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | 로캘과 `zh-CN-HuihuiRUS` `zh-CN` 음성이 있는 컨테이너 이미지입니다.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | 로캘과 `zh-CN-Kangkang-Apollo` `zh-CN` 음성이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | 로캘과 `zh-CN-Yaoyao-Apollo` `zh-CN` 음성이 있는 컨테이너 이미지입니다.   |

## <a name="key-phrase-extraction"></a>핵심 문구 추출

[키 구 추출][ta-kp] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services` 리포지토리 내에 상주하며 의 `keyphrase`이름이 지정됩니다. 정규화된 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

이 컨테이너 이미지에는 다음과 같은 태그를 사용할 수 있습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>언어 검색

[언어 검색][ta-la] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services` 리포지토리 내에 상주하며 의 `language`이름이 지정됩니다. 정규화된 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/language`.

이 컨테이너 이미지에는 다음과 같은 태그를 사용할 수 있습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>감정 분석

[감정 분석][ta-se] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 신디케이트에서 찾을 수 있습니다. `azure-cognitive-services` 리포지토리 내에 상주하며 의 `sentiment`이름이 지정됩니다. 정규화된 컨테이너 이미지 이름은 `mcr.microsoft.com/azure-cognitive-services/sentiment`.

이 컨테이너 이미지에는 다음과 같은 태그를 사용할 수 있습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
