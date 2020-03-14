---
title: Cognitive Services 컨테이너 이미지 태그
titleSuffix: Azure Cognitive Services
description: 모든 인식 서비스 컨테이너 이미지 태그의 포괄적인 목록입니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: eed2223dbfeee307b552cdd010530f27c379f5fe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221230"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure Cognitive Services 컨테이너 이미지 태그

Azure Cognitive Services는 많은 컨테이너 이미지를 제공 합니다. 컨테이너 레지스트리 및 해당 리포지토리는 컨테이너 이미지 마다 다릅니다. 각 컨테이너 이미지 이름은 여러 태그를 제공 합니다. 컨테이너 이미지 태그는 컨테이너 이미지의 버전을 관리 하는 메커니즘입니다. 이 문서는 모든 Cognitive Services 컨테이너 이미지와 사용 가능한 태그를 나열 하는 포괄적인 참조로 사용 하기 위한 것입니다.

> [!TIP]
> [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)를 사용 하는 경우 **대/소문자를 구분**하므로 컨테이너 레지스트리, 리포지토리, 컨테이너 이미지 이름 및 해당 태그의 대/소문자 구분에 주의 해야 합니다.

## <a name="anomaly-detector"></a>Anomaly Detector

[변칙 탐지기][ad-containers] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. 이 클래스는 `microsoft` 리포지토리 내에 있으며 `cognitive-services-anomaly-detector`이름이 지정 됩니다. 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Computer Vision

[Computer Vision][cv-containers] 컨테이너 이미지는 `containerpreview.azurecr.io` container registry에서 찾을 수 있습니다. 이 클래스는 `microsoft` 리포지토리 내에 있으며 `cognitive-services-read`이름이 지정 됩니다. 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-read`입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Face

[Face][fa-containers] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. 이 클래스는 `microsoft` 리포지토리 내에 있으며 `cognitive-services-face`이름이 지정 됩니다. 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-face`입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

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

[양식 인식기][fr-containers] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. 이 클래스는 `microsoft` 리포지토리 내에 있으며 `cognitive-services-form-recognizer`이름이 지정 됩니다. 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>언어 이해(LUIS)

[LUIS][lu-containers] 컨테이너 이미지는 `mcr.microsoft.com` 컨테이너 레지스트리 게시에서 찾을 수 있습니다. 이 클래스는 `azure-cognitive-services` 리포지토리 내에 있으며 `luis`이름이 지정 됩니다. 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/luis`입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

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

## <a name="custom-speech-to-text"></a>Custom Speech 텍스트

[Custom Speech 텍스트][sp-cstt] 컨테이너 이미지는 `containerpreview.azurecr.io` container registry에서 찾을 수 있습니다. 이 클래스는 `microsoft` 리포지토리 내에 있으며 `cognitive-services-custom-speech-to-text`이름이 지정 됩니다. 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그            | 메모 |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>사용자 지정 텍스트 음성 변환

[사용자 지정 텍스트 음성 변환][sp-ctts] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. 이 클래스는 `microsoft` 리포지토리 내에 있으며 `cognitive-services-custom-text-to-speech`이름이 지정 됩니다. 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그            | 메모 |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>음성 텍스트 변환

[음성 텍스트][sp-stt] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. 이 클래스는 `microsoft` 리포지토리 내에 있으며 `cognitive-services-speech-to-text`이름이 지정 됩니다. 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                  | 메모                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ar-ae-preview` | `ar-AE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ar-eg-preview` | `ar-EG` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ar-kw-preview` | `ar-KW` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ar-qa-preview` | `ar-QA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ar-sa-preview` | `ar-SA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ca-es-preview` | `ca-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-da-dk-preview` | `da-DK` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-de-de-preview` | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-en-au-preview` | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-en-ca-preview` | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-en-gb-preview` | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-en-in-preview` | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-en-nz-preview` | `en-NZ` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-en-us-preview` | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-es-es-preview` | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-es-mx-preview` | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-fi-fi-preview` | `fi-FI` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-fr-ca-preview` | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-fr-fr-preview` | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-gu-in-preview` | `gu-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-hi-in-preview` | `hi-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-it-it-preview` | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ja-jp-preview` | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ko-kr-preview` | `ko-KR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-mr-in-preview` | `mr-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-nb-no-preview` | `nb-NO` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-nl-nl-preview` | `nl-NL` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-pl-pl-preview` | `pl-PL` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-pt-br-preview` | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-pt-pt-preview` | `pt-PT` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ru-ru-preview` | `ru-RU` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-sv-se-preview` | `sv-SE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-ta-in-preview` | `ta-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-te-in-preview` | `te-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-th-th-preview` | `th-TH` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-tr-tr-preview` | `tr-TR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-zh-cn-preview` | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-zh-hk-preview` | `zh-HK` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.1-amd64-zh-tw-preview` | `zh-TW` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ar-ae-preview` | `ar-AE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ar-eg-preview` | `ar-EG` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ar-kw-preview` | `ar-KW` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ar-qa-preview` | `ar-QA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ar-sa-preview` | `ar-SA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ca-es-preview` | `ca-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-da-dk-preview` | `da-DK` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-de-de-preview` | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-en-au-preview` | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-en-ca-preview` | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-en-gb-preview` | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-en-in-preview` | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-en-nz-preview` | `en-NZ` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-en-us-preview` | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-es-es-preview` | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-es-mx-preview` | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-fi-fi-preview` | `fi-FI` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-fr-ca-preview` | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-fr-fr-preview` | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-gu-in-preview` | `gu-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-hi-in-preview` | `hi-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-it-it-preview` | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ja-jp-preview` | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ko-kr-preview` | `ko-KR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-mr-in-preview` | `mr-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-nb-no-preview` | `nb-NO` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-nl-nl-preview` | `nl-NL` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-pl-pl-preview` | `pl-PL` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-pt-br-preview` | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-pt-pt-preview` | `pt-PT` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ru-ru-preview` | `ru-RU` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-sv-se-preview` | `sv-SE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-ta-in-preview` | `ta-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-te-in-preview` | `te-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-th-th-preview` | `th-TH` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-tr-tr-preview` | `tr-TR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-zh-cn-preview` | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-zh-hk-preview` | `zh-HK` 로캘이 있는 컨테이너 이미지입니다. |
| `2.1.0-amd64-zh-tw-preview` | `zh-TW` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.3-amd64-ja-jp-preview` | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ar-ae-preview` | `ar-AE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ar-eg-preview` | `ar-EG` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ar-kw-preview` | `ar-KW` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ar-qa-preview` | `ar-QA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ar-sa-preview` | `ar-SA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ca-es-preview` | `ca-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-da-dk-preview` | `da-DK` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-de-de-preview` | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-en-au-preview` | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-en-ca-preview` | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-en-gb-preview` | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-en-in-preview` | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-en-nz-preview` | `en-NZ` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-en-us-preview` | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-es-es-preview` | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-es-mx-preview` | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-fi-fi-preview` | `fi-FI` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-fr-ca-preview` | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-fr-fr-preview` | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-gu-in-preview` | `gu-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-hi-in-preview` | `hi-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-it-it-preview` | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ja-jp-preview` | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ko-kr-preview` | `ko-KR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-mr-in-preview` | `mr-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-nb-no-preview` | `nb-NO` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-nl-nl-preview` | `nl-NL` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-pl-pl-preview` | `pl-PL` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-pt-br-preview` | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-pt-pt-preview` | `pt-PT` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ru-ru-preview` | `ru-RU` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-sv-se-preview` | `sv-SE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-ta-in-preview` | `ta-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-te-in-preview` | `te-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-th-th-preview` | `th-TH` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-tr-tr-preview` | `tr-TR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-zh-cn-preview` | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-zh-hk-preview` | `zh-HK` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.2-amd64-zh-tw-preview` | `zh-TW` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ar-ae-preview` | `ar-AE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ar-eg-preview` | `ar-EG` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ar-kw-preview` | `ar-KW` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ar-qa-preview` | `ar-QA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ar-sa-preview` | `ar-SA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ca-es-preview` | `ca-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-da-dk-preview` | `da-DK` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-de-de-preview` | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-en-au-preview` | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-en-ca-preview` | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-en-gb-preview` | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-en-in-preview` | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-en-nz-preview` | `en-NZ` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-en-us-preview` | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-es-es-preview` | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-es-mx-preview` | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-fi-fi-preview` | `fi-FI` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-fr-ca-preview` | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-fr-fr-preview` | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-gu-in-preview` | `gu-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-hi-in-preview` | `hi-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-it-it-preview` | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ja-jp-preview` | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ko-kr-preview` | `ko-KR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-mr-in-preview` | `mr-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-nb-no-preview` | `nb-NO` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-nl-nl-preview` | `nl-NL` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-pl-pl-preview` | `pl-PL` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-pt-br-preview` | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-pt-pt-preview` | `pt-PT` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ru-ru-preview` | `ru-RU` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-sv-se-preview` | `sv-SE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-ta-in-preview` | `ta-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-te-in-preview` | `te-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-th-th-preview` | `th-TH` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-tr-tr-preview` | `tr-TR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-zh-cn-preview` | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-zh-hk-preview` | `zh-HK` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.1-amd64-zh-tw-preview` | `zh-TW` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-ar-eg-preview` | `ar-EG` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-ca-es-preview` | `ca-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-da-dk-preview` | `da-DK` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-de-de-preview` | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-en-au-preview` | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-en-ca-preview` | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-en-gb-preview` | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-en-in-preview` | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-en-nz-preview` | `en-NZ` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-en-us-preview` | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-es-es-preview` | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-es-mx-preview` | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-fi-fi-preview` | `fi-FI` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-fr-ca-preview` | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-fr-fr-preview` | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-hi-in-preview` | `hi-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-it-it-preview` | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-ja-jp-preview` | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-ko-kr-preview` | `ko-KR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-nb-no-preview` | `nb-NO` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-nl-nl-preview` | `nl-NL` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-pl-pl-preview` | `pl-PL` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-pt-br-preview` | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-pt-pt-preview` | `pt-PT` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-ru-ru-preview` | `ru-RU` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-sv-se-preview` | `sv-SE` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-th-th-preview` | `th-TH` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-tr-tr-preview` | `tr-TR` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-zh-cn-preview` | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-zh-hk-preview` | `zh-HK` 로캘이 있는 컨테이너 이미지입니다. |
| `2.0.0-amd64-zh-tw-preview` | `zh-TW` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-de-de-preview` | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-en-au-preview` | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-en-ca-preview` | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-en-gb-preview` | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-en-in-preview` | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-en-us-preview` | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-es-es-preview` | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-es-mx-preview` | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-fr-ca-preview` | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-fr-fr-preview` | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-it-it-preview` | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-ja-jp-preview` | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-pt-br-preview` | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `1.2.0-amd64-zh-cn-preview` | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-de-de-preview` | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-en-au-preview` | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-en-ca-preview` | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-en-gb-preview` | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-en-in-preview` | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-en-us-preview` | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-es-es-preview` | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-es-mx-preview` | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-fr-ca-preview` | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-fr-fr-preview` | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-it-it-preview` | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-ja-jp-preview` | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-pt-br-preview` | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.3-amd64-zh-cn-preview` | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-de-de-preview` | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-en-au-preview` | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-en-ca-preview` | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-en-gb-preview` | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-en-in-preview` | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-en-us-preview` | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-es-es-preview` | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-es-mx-preview` | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-fr-ca-preview` | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-fr-fr-preview` | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-it-it-preview` | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-ja-jp-preview` | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-pt-br-preview` | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.2-amd64-zh-cn-preview` | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-de-de-preview` | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-en-au-preview` | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-en-ca-preview` | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-en-gb-preview` | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-en-in-preview` | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-en-us-preview` | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-es-es-preview` | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-es-mx-preview` | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-fr-ca-preview` | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-fr-fr-preview` | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-it-it-preview` | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-ja-jp-preview` | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-pt-br-preview` | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.1-amd64-zh-cn-preview` | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-de-de-preview` | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-en-au-preview` | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-en-ca-preview` | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-en-gb-preview` | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-en-in-preview` | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-en-us-preview` | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-es-es-preview` | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-es-mx-preview` | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-fr-ca-preview` | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-fr-fr-preview` | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-it-it-preview` | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-ja-jp-preview` | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-pt-br-preview` | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `1.1.0-amd64-zh-cn-preview` | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-de-de-preview` | `de-DE` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-en-au-preview` | `en-AU` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-en-ca-preview` | `en-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-en-gb-preview` | `en-GB` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-en-in-preview` | `en-IN` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-en-us-preview` | `en-US` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-es-es-preview` | `es-ES` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-es-mx-preview` | `es-MX` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-fr-ca-preview` | `fr-CA` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-fr-fr-preview` | `fr-FR` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-it-it-preview` | `it-IT` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-ja-jp-preview` | `ja-JP` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-pt-br-preview` | `pt-BR` 로캘이 있는 컨테이너 이미지입니다. |
| `1.0.0-amd64-zh-cn-preview` | `zh-CN` 로캘이 있는 컨테이너 이미지입니다. |

## <a name="text-to-speech"></a>텍스트 음성 변환

[텍스트 음성 변환][sp-tts] 컨테이너 이미지는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 찾을 수 있습니다. 이 클래스는 `microsoft` 리포지토리 내에 있으며 `cognitive-services-text-to-speech`이름이 지정 됩니다. 정규화 된 컨테이너 이미지 이름은, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                                  | 메모                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | `en-US` 로캘 및 `en-US-JessaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | `ar-EG` 로캘 및 `ar-EG-Hoda` 음성이 포함 된 컨테이너 이미지입니다.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | `ar-SA` 로캘 및 `ar-SA-Naayf` 음성이 포함 된 컨테이너 이미지입니다.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | `bg-BG` 로캘 및 `bg-BG-Ivan` 음성이 포함 된 컨테이너 이미지입니다.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | `ca-ES` 로캘 및 `ca-ES-HerenaRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | `cs-CZ` 로캘 및 `cs-CZ-Jakub` 음성이 포함 된 컨테이너 이미지입니다.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | `da-DK` 로캘 및 `da-DK-HelleRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-de-at-michael-preview`         | `de-AT` 로캘 및 `de-AT-Michael` 음성이 포함 된 컨테이너 이미지입니다.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | `de-CH` 로캘 및 `de-CH-Karsten` 음성이 포함 된 컨테이너 이미지입니다.         |
| `1.3.0-amd64-de-de-hedda-preview`           | `de-DE` 로캘 및 `de-DE-Hedda` 음성이 포함 된 컨테이너 이미지입니다.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | `de-DE` 로캘 및 `de-DE-Hedda` 음성이 포함 된 컨테이너 이미지입니다.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | `de-DE` 로캘 및 `de-DE-HeddaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | `de-DE` 로캘 및 `de-DE-Stefan-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | `el-GR` 로캘 및 `el-GR-Stefanos` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-en-au-catherine-preview`       | `en-AU` 로캘 및 `en-AU-Catherine` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | `en-AU` 로캘 및 `en-AU-HayleyRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | `en-CA` 로캘 및 `en-CA-HeatherRUS` 음성이 포함 된 컨테이너 이미지입니다.      |
| `1.3.0-amd64-en-ca-linda-preview`           | `en-CA` 로캘 및 `en-CA-Linda` 음성이 포함 된 컨테이너 이미지입니다.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | `en-GB` 로캘 및 `en-GB-George-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | `en-GB` 로캘 및 `en-GB-HazelRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | `en-GB` 로캘 및 `en-GB-Susan-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.3.0-amd64-en-ie-sean-preview`            | `en-IE` 로캘 및 `en-IE-Sean` 음성이 포함 된 컨테이너 이미지입니다.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | `en-IN` 로캘 및 `en-IN-Heera-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | `en-IN` 로캘 및 `en-IN-PriyaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | `en-IN` 로캘 및 `en-IN-Ravi-Apollo` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | `en-US` 로캘 및 `en-US-BenjaminRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | `en-US` 로캘 및 `en-US-Guy24kRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | `en-US` 로캘 및 `en-US-Jessa24kRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | `en-US` 로캘 및 `en-US-JessaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | `en-US` 로캘 및 `en-US-ZiraRUS` 음성이 포함 된 컨테이너 이미지입니다.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | `es-ES` 로캘 및 `es-ES-HelenaRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | `es-ES` 로캘 및 `es-ES-Laura-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | `es-ES` 로캘 및 `es-ES-Pablo-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | `es-MX` 로캘 및 `es-MX-HildaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | `es-MX` 로캘 및 `es-MX-Raul-Apollo` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | `fi-FI` 로캘 및 `fi-FI-HeidiRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | `fr-CA` 로캘 및 `fr-CA-Caroline` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | `fr-CA` 로캘 및 `fr-CA-HarmonieRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | `fr-CH` 로캘 및 `fr-CH-Guillaume` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | `fr-FR` 로캘 및 `fr-FR-HortenseRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | `fr-FR` 로캘 및 `fr-FR-Julie-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | `fr-FR` 로캘 및 `fr-FR-Paul-Apollo` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.3.0-amd64-he-il-asaf-preview`            | `he-IL` 로캘 및 `he-IL-Asaf` 음성이 포함 된 컨테이너 이미지입니다.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | `hi-IN` 로캘 및 `hi-IN-Hemant` 음성이 포함 된 컨테이너 이미지입니다.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | `hi-IN` 로캘 및 `hi-IN-Kalpana-Apollo` 음성이 포함 된 컨테이너 이미지입니다.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | `hi-IN` 로캘 및 `hi-IN-Kalpana` 음성이 포함 된 컨테이너 이미지입니다.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | `hi-IN` 로캘 및 `hi-IN-Kalpana` 음성이 포함 된 컨테이너 이미지입니다.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | `hr-HR` 로캘 및 `hr-HR-Matej` 음성이 포함 된 컨테이너 이미지입니다.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | `hu-HU` 로캘 및 `hu-HU-Szabolcs` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-id-id-andika-preview`          | `id-ID` 로캘 및 `id-ID-Andika` 음성이 포함 된 컨테이너 이미지입니다.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | `it-IT` 로캘 및 `it-IT-Cosimo-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | `it-IT` 로캘 및 `it-IT-LuciaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | `ja-JP` 로캘 및 `ja-JP-Ayumi-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | `ja-JP` 로캘 및 `ja-JP-HarukaRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | `ja-JP` 로캘 및 `ja-JP-Ichiro-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | `ko-KR` 로캘 및 `ko-KR-HeamiRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | `ms-MY` 로캘 및 `ms-MY-Rizwan` 음성이 포함 된 컨테이너 이미지입니다.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | `nb-NO` 로캘 및 `nb-NO-HuldaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | `nl-NL` 로캘 및 `nl-NL-HannaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | `pl-PL` 로캘 및 `pl-PL-PaulinaRUS` 음성이 포함 된 컨테이너 이미지입니다.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | `pt-BR` 로캘 및 `pt-BR-Daniel-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | `pt-BR` 로캘 및 `pt-BR-HeloisaRUS` 음성이 포함 된 컨테이너 이미지입니다.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | `pt-PT` 로캘 및 `pt-PT-HeliaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | `ro-RO` 로캘 및 `ro-RO-Andrei` 음성이 포함 된 컨테이너 이미지입니다.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | `ru-RU` 로캘 및 `ru-RU-EkaterinaRUS` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | `ru-RU` 로캘 및 `ru-RU-Irina-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | `ru-RU` 로캘 및 `ru-RU-Pavel-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | `sk-SK` 로캘 및 `sk-SK-Filip` 음성이 포함 된 컨테이너 이미지입니다.           |
| `1.3.0-amd64-sl-si-lado-preview`            | `sl-SI` 로캘 및 `sl-SI-Lado` 음성이 포함 된 컨테이너 이미지입니다.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | `sv-SE` 로캘 및 `sv-SE-HedvigRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | `ta-IN` 로캘 및 `ta-IN-Valluvar` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-te-in-chitra-preview`          | `te-IN` 로캘 및 `te-IN-Chitra` 음성이 포함 된 컨테이너 이미지입니다.          |
| `1.3.0-amd64-th-th-pattara-preview`         | `th-TH` 로캘 및 `th-TH-Pattara` 음성이 포함 된 컨테이너 이미지입니다.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | `tr-TR` 로캘 및 `tr-TR-SedaRUS` 음성이 포함 된 컨테이너 이미지입니다.         |
| `1.3.0-amd64-vi-vn-an-preview`              | `vi-VN` 로캘 및 `vi-VN-An` 음성이 포함 된 컨테이너 이미지입니다.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | `zh-CN` 로캘 및 `zh-CN-HuihuiRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | `zh-CN` 로캘 및 `zh-CN-Kangkang-Apollo` 음성이 포함 된 컨테이너 이미지입니다. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | `zh-CN` 로캘 및 `zh-CN-Yaoyao-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | `zh-HK` 로캘 및 `zh-HK-Danny-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | `zh-HK` 로캘 및 `zh-HK-Tracy-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | `zh-HK` 로캘 및 `zh-HK-TracyRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | `zh-TW` 로캘 및 `zh-TW-HanHanRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | `zh-TW` 로캘 및 `zh-TW-Yating-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | `zh-TW` 로캘 및 `zh-TW-Zhiwei-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | `de-DE` 로캘 및 `de-DE-Hedda` 음성이 포함 된 컨테이너 이미지입니다.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | `de-DE` 로캘 및 `de-DE-HeddaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | `de-DE` 로캘 및 `de-DE-Stefan-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.2.0-amd64-en-au-catherine-preview`       | `en-AU` 로캘 및 `en-AU-Catherine` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | `en-AU` 로캘 및 `en-AU-HayleyRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | `en-GB` 로캘 및 `en-GB-George-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | `en-GB` 로캘 및 `en-GB-HazelRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | `en-GB` 로캘 및 `en-GB-Susan-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | `en-IN` 로캘 및 `en-IN-Heera-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | `en-IN` 로캘 및 `en-IN-PriyaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | `en-IN` 로캘 및 `en-IN-Ravi-Apollo` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | `en-US` 로캘 및 `en-US-BenjaminRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | `en-US` 로캘 및 `en-US-Guy24kRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | `en-US` 로캘 및 `en-US-Jessa24kRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | `en-US` 로캘 및 `en-US-JessaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | `en-US` 로캘 및 `en-US-ZiraRUS` 음성이 포함 된 컨테이너 이미지입니다.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | `es-ES` 로캘 및 `es-ES-HelenaRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | `es-ES` 로캘 및 `es-ES-Laura-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | `es-ES` 로캘 및 `es-ES-Pablo-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | `es-MX` 로캘 및 `es-MX-HildaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | `es-MX` 로캘 및 `es-MX-Raul-Apollo` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | `fr-CA` 로캘 및 `fr-CA-Caroline` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | `fr-CA` 로캘 및 `fr-CA-HarmonieRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | `fr-FR` 로캘 및 `fr-FR-HortenseRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | `fr-FR` 로캘 및 `fr-FR-Julie-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | `fr-FR` 로캘 및 `fr-FR-Paul-Apollo` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | `it-IT` 로캘 및 `it-IT-Cosimo-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | `it-IT` 로캘 및 `it-IT-LuciaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | `ja-JP` 로캘 및 `ja-JP-Ayumi-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | `ja-JP` 로캘 및 `ja-JP-HarukaRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | `ja-JP` 로캘 및 `ja-JP-Ichiro-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | `ko-KR` 로캘 및 `ko-KR-HeamiRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | `pt-BR` 로캘 및 `pt-BR-Daniel-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | `pt-BR` 로캘 및 `pt-BR-HeloisaRUS` 음성이 포함 된 컨테이너 이미지입니다.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | `zh-CN` 로캘 및 `zh-CN-HuihuiRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | `zh-CN` 로캘 및 `zh-CN-Kangkang-Apollo` 음성이 포함 된 컨테이너 이미지입니다. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | `zh-CN` 로캘 및 `zh-CN-Yaoyao-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.1.0-amd64-de-de-hedda-preview`           | `de-DE` 로캘 및 `de-DE-Hedda` 음성이 포함 된 컨테이너 이미지입니다.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | `de-DE` 로캘 및 `de-DE-Hedda` 음성이 포함 된 컨테이너 이미지입니다.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | `de-DE` 로캘 및 `de-DE-HeddaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | `de-DE` 로캘 및 `de-DE-Stefan-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.1.0-amd64-en-au-catherine-preview`       | `en-AU` 로캘 및 `en-AU-Catherine` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | `en-AU` 로캘 및 `en-AU-HayleyRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | `en-GB` 로캘 및 `en-GB-George-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | `en-GB` 로캘 및 `en-GB-HazelRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | `en-GB` 로캘 및 `en-GB-Susan-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | `en-IN` 로캘 및 `en-IN-Heera-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | `en-IN` 로캘 및 `en-IN-PriyaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | `en-IN` 로캘 및 `en-IN-Ravi-Apollo` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | `en-US` 로캘 및 `en-US-BenjaminRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | `en-US` 로캘 및 `en-US-Guy24kRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | `en-US` 로캘 및 `en-US-Jessa24kRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | `en-US` 로캘 및 `en-US-JessaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | `en-US` 로캘 및 `en-US-ZiraRUS` 음성이 포함 된 컨테이너 이미지입니다.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | `es-ES` 로캘 및 `es-ES-HelenaRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | `es-ES` 로캘 및 `es-ES-Laura-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | `es-ES` 로캘 및 `es-ES-Pablo-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | `es-MX` 로캘 및 `es-MX-HildaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | `es-MX` 로캘 및 `es-MX-Raul-Apollo` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | `fr-CA` 로캘 및 `fr-CA-Caroline` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | `fr-CA` 로캘 및 `fr-CA-HarmonieRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | `fr-FR` 로캘 및 `fr-FR-HortenseRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | `fr-FR` 로캘 및 `fr-FR-Julie-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | `fr-FR` 로캘 및 `fr-FR-Paul-Apollo` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | `it-IT` 로캘 및 `it-IT-Cosimo-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | `it-IT` 로캘 및 `it-IT-LuciaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | `ja-JP` 로캘 및 `ja-JP-Ayumi-Apollo` 음성이 포함 된 컨테이너 이미지입니다.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | `ja-JP` 로캘 및 `ja-JP-HarukaRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | `ja-JP` 로캘 및 `ja-JP-Ichiro-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | `ko-KR` 로캘 및 `ko-KR-HeamiRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | `pt-BR` 로캘 및 `pt-BR-Daniel-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | `pt-BR` 로캘 및 `pt-BR-HeloisaRUS` 음성이 포함 된 컨테이너 이미지입니다.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | `zh-CN` 로캘 및 `zh-CN-HuihuiRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | `zh-CN` 로캘 및 `zh-CN-Kangkang-Apollo` 음성이 포함 된 컨테이너 이미지입니다. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | `zh-CN` 로캘 및 `zh-CN-Yaoyao-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | `en-US` 로캘 및 `en-US-BenjaminRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | `en-US` 로캘 및 `en-US-Guy24kRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | `en-US` 로캘 및 `en-US-Jessa24kRUS` 음성이 포함 된 컨테이너 이미지입니다.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | `en-US` 로캘 및 `en-US-JessaRUS` 음성이 포함 된 컨테이너 이미지입니다.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | `en-US` 로캘 및 `en-US-ZiraRUS` 음성이 포함 된 컨테이너 이미지입니다.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | `zh-CN` 로캘 및 `zh-CN-HuihuiRUS` 음성이 포함 된 컨테이너 이미지입니다.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | `zh-CN` 로캘 및 `zh-CN-Kangkang-Apollo` 음성이 포함 된 컨테이너 이미지입니다. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | `zh-CN` 로캘 및 `zh-CN-Yaoyao-Apollo` 음성이 포함 된 컨테이너 이미지입니다.   |

## <a name="key-phrase-extraction"></a>핵심 문구 추출

[핵심 구 추출][ta-kp] 컨테이너 이미지는 `mcr.microsoft.com` container registry 게시에서 찾을 수 있습니다. 이 클래스는 `azure-cognitive-services` 리포지토리 내에 있으며 `keyphrase`이름이 지정 됩니다. 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/keyphrase`입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>언어 검색

[언어 감지][ta-la] 컨테이너 이미지는 `mcr.microsoft.com` container registry 게시에서 찾을 수 있습니다. 이 클래스는 `azure-cognitive-services` 리포지토리 내에 있으며 `language`이름이 지정 됩니다. 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/language`입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

| 이미지 태그                    | 메모 |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>감정 분석

[감정 분석][ta-se] 컨테이너 이미지는 `mcr.microsoft.com` container registry 게시에서 찾을 수 있습니다. 이 클래스는 `azure-cognitive-services` 리포지토리 내에 있으며 `sentiment`이름이 지정 됩니다. 정규화 된 컨테이너 이미지 이름은, `mcr.microsoft.com/azure-cognitive-services/sentiment`입니다.

이 컨테이너 이미지에 사용할 수 있는 태그는 다음과 같습니다.

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
