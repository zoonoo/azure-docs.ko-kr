---
title: 컨테이너 리포지토리 및 이미지
services: cognitive-services
author: IEvangelist
manager: nitinme
description: 모든 인지 서비스 제공에 대 한 컨테이너 레지스트리, 리포지토리 및 이미지 이름을 나타내는 두 테이블
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2019
ms.author: dapine
ms.openlocfilehash: c1593cb3dad7ee1370a66747fa3fe47e93c19957
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499190"
---
### <a name="container-repositories-and-images"></a>컨테이너 리포지토리 및 이미지

아래 표는 Azure Cognitive Services에서 제공 하는 사용 가능한 컨테이너 이미지의 포괄적인 목록입니다.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>공용 "제어 되지 않음" (컨테이너 레지스트리: `mcr.microsoft.com`)

Microsoft Container Registry는 Cognitive Services에 대해 공개적으로 사용할 수 있는 "제어 되지 않은" 컨테이너를 모두 호스팅합니다.

| 부여 | 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 핵심 문구 추출 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 언어 검색 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [텍스트 분석](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 감정 분석 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>공용 "제어 된" 미리 보기 (컨테이너 레지스트리: `containerpreview.azurecr.io`)

Container Preview 레지스트리는 Cognitive Services에 대해 공개적으로 사용할 수 있는 "제어 된" 컨테이너를 모두 호스팅합니다. 이러한 컨테이너는 이러한 컨테이너를 사용 하기 위해 액세스를 위한 공식 요청이 필요 합니다.

| 부여 | 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|--|--|--|
| [Anomaly Detector](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | 읽기 | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [폼 인식기](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
< < < < < < < 헤드 | [Speech SERVICE API](../../speech-service/speech-container-howto.md?tab=stt) | 음성 텍스트 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` | | [Speech SERVICE API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech-텍스트 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` | | [Speech SERVICE API](../../speech-service/speech-container-howto.md?tab=tts) | 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` | | [Speech SERVICE API](../../speech-service/speech-container-howto.md?tab=ctts) | 사용자 지정 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` | ======= | [Speech SERVICE API](../../speech-service/speech-container-howto.md) | 음성 텍스트 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` | | [Speech SERVICE API](../../speech-service/speech-container-howto.md) | 텍스트 음성 변환 | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` | | [Translator Text](../../translator/how-to-install-containers.md) | Translator Text | `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` |
>>>>>>> refs/원격/MicrosoftDocs/master
