---
title: 컨테이너 리포지토리 및 이미지
services: cognitive-services
author: IEvangelist
manager: nitinme
description: 모든 인지 서비스 제공에 대 한 컨테이너 레지스트리, 리포지토리 및 이미지 이름을 나타내는 두 테이블
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 55a3bb5f894d3ab753cfec64687abc9c7cae53cb
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082387"
---
### <a name="container-repositories-and-images"></a>컨테이너 리포지토리 및 이미지

아래 표는 Azure Cognitive Services에서 제공 하는 사용 가능한 컨테이너 이미지의 목록입니다. 사용 가능한 모든 컨테이너 이미지 이름 및 해당 태그의 전체 목록은 [Cognitive Services 컨테이너 이미지 태그](../container-image-tags.md)를 참조 하세요. 현재 GA (일반 공급) 상태인 Cognitive Services 컨테이너가 없습니다. 시간이 걸리는 경우 추가 알림이 생성 될 때까지 컨테이너는 *공용으로 제어* 되지 않거나 공용으로 *제어 된 미리 보기로*제공 됩니다.

 - *공용 제어*되지 않음: 컨테이너는 제어 메커니즘 없이 공개적으로 사용할 수 있습니다.
 - *공용 제어 된 미리 보기*: 컨테이너는 공개적으로 사용할 수 있지만 먼저 컨테이너 레지스트리에 액세스 하기 위한 공식 요청이 필요 합니다.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>공용 "제어 되지 않음" (컨테이너 레지스트리: `mcr.microsoft.com`)

MCR (Microsoft Container Registry)은 Cognitive Services에 대해 공개적으로 사용할 수 있는 "제어 되지 않은" 컨테이너를 모두 syndicates 합니다. 컨테이너는 [Docker 허브](https://hub.docker.com/_/microsoft-azure-cognitive-services)에서 직접 사용할 수도 있습니다.

| 서비스 | 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 핵심 문구 추출 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 언어 검색 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 감정 분석 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>공용 "제어 된" 미리 보기 (컨테이너 레지스트리: `containerpreview.azurecr.io`)

Container Preview 레지스트리는 Cognitive Services에 대해 공개적으로 사용할 수 있는 "제어 된" 컨테이너를 모두 호스팅합니다. 이러한 컨테이너에는 해당 컨테이너 레지스트리를 통해 액세스 하기 위한 공식 요청이 필요 합니다.

| 서비스 | 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|--|
| [변칙 탐지기](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | 읽기 | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [폼 인식기](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | 음성 텍스트 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech 텍스트 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | 사용자 지정 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
