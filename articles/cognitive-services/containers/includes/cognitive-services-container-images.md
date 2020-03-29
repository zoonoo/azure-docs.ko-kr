---
title: 컨테이너 리포지토리 및 이미지
services: cognitive-services
author: IEvangelist
manager: nitinme
description: 모든 Cognitive Service 제품에 대한 컨테이너 레지스트리, 리포지토리 및 이미지 이름을 나타내는 두 테이블입니다.
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 55a3bb5f894d3ab753cfec64687abc9c7cae53cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082387"
---
### <a name="container-repositories-and-images"></a>컨테이너 리포지토리 및 이미지

아래 표는 Azure Cognitive 서비스에서 제공하는 사용 가능한 컨테이너 이미지의 목록입니다. 사용 가능한 모든 컨테이너 이미지 이름 및 사용 가능한 태그의 전체 목록은 [Cognitive Services 컨테이너 이미지 태그를](../container-image-tags.md)참조하십시오. 현재 일반적으로 사용할 수 있는 코그너티브 서비스 컨테이너(GA)는 없습니다. 당분간, 추가 공지가 이루어질 때까지 - 컨테이너는 *공용 없음* 또는 *공개 게이트 미리 보기로*사용할 수 있습니다.

 - *공용 Ungated*: 용기는 게이팅 메커니즘없이 공개적으로 사용할 수 있습니다.
 - *공개 게이트 미리 보기*: 컨테이너는 공개적으로 사용할 수 있지만 먼저 컨테이너 레지스트리에 액세스하려면 공식적인 요청이 필요합니다.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>공용 "둥글게" (컨테이너 `mcr.microsoft.com`레지스트리: )

Microsoft 컨테이너 레지스트리(MCR)는 코그너티브 서비스에 대해 공개적으로 사용 가능한 "폐기된" 컨테이너를 모두 신디케이트합니다. [컨테이너는 Docker 허브에서](https://hub.docker.com/_/microsoft-azure-cognitive-services)직접 사용할 수도 있습니다.

| 서비스 | 컨테이너 | 컨테이너 레지스트리 / 리포지토리 / 이미지 이름 |
|--|--|--|
| [루이스](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 핵심 문구 추출 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 언어 검색 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 감정 분석 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>공용 "게이트" 미리 보기(컨테이너 `containerpreview.azurecr.io`레지스트리: )

컨테이너 미리 보기 레지스트리는 코그너티브 서비스에 대해 공개적으로 사용할 수 있는 모든 "게이트" 컨테이너를 호스트합니다. 이러한 컨테이너는 컨테이너 레지스트리를 통해 액세스하기 위한 공식적인 요청이 필요합니다.

| 서비스 | 컨테이너 | 컨테이너 레지스트리 / 리포지토리 / 이미지 이름 |
|--|--|--|
| [변칙 검출기](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [컴퓨터 비전](../../Computer-vision/computer-vision-how-to-install-containers.md) | 읽기 | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [얼굴](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [양식 인식기](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | 음성 텍스트 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | 사용자 지정 음성-텍스트 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | 사용자 지정 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
