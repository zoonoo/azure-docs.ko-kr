---
title: 음성 컨테이너를 설치 합니다.
titleSuffix: Azure Cognitive Services
description: 음성 포괄적인 helm 차트 구성 옵션을 자세히 설명합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717239"
---
### <a name="speech-umbrella-chart"></a>음성 (포괄적인 차트)

최상위 "포괄적인" 차트에서 값에는 해당 하위 차트 값을 재정의합니다. 따라서 모든 온-프레미스 여기에 사용자 지정 된 값을 추가 해야 합니다.

|매개 변수|Description|기본값|
| -- | -- | -- | -- |
| `speechToText.enabled` | 여부를 **음성-텍스트** 서비스를 사용 합니다. | `true` |
| `speechToText.verification.enabled` | 여부를 합니다 `helm test` 기능에 대 한 **음성-텍스트** 서비스를 사용할 수 있습니다. | `true` |
| `speechToText.verification.image.registry` | Docker 이미지 리포지토리에 있는 `helm test` 테스트를 사용 하 여 **음성-텍스트** 서비스입니다. Helm 테스트를 위해 클러스터 내에서 별도 pod를 만듭니다 및 가져옵니다 합니다 *테스트 용도의* 이 레지스트리에서 이미지입니다. | `docker.io` |
| `speechToText.verification.image.repository` | Docker 이미지 리포지토리에 있는 `helm test` 테스트를 사용 하 여 **음성-텍스트** 서비스입니다. Helm 테스트 pod이이 리포지토리를 사용 하 여 가져오려고 *테스트 용도의* 이미지입니다. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | 사용 하 여 docker 이미지 태그 `helm test` 에 대 한 **음성-텍스트** 서비스입니다. Helm 테스트 pod이이 태그를 사용 하 여 가져오려고 *테스트 용도의* 이미지입니다. | `latest` |
| `speechToText.verification.image.pullByHash` | 여부는 *테스트 용도의* 해시 하 여 docker 이미지를 끌어온. 하는 경우 `true`, `speechToText.verification.image.hash` 올바른 이미지 해시 값으로 추가 해야 합니다. | `false` |
| `speechToText.verification.image.arguments` | 실행 하는 데 사용 되는 인수를 *테스트 용도의* docker 이미지입니다. Helm 테스트 pod를 실행 하는 경우 컨테이너에 이러한 인수를 전달 `helm test`합니다. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | 여부를 합니다 **text to speech** 서비스를 사용할 수 있습니다. | `true` |
| `textToSpeech.verification.enabled` | 여부를 합니다 `helm test` 기능에 대 한 **음성-텍스트** 서비스를 사용할 수 있습니다. | `true` |
| `textToSpeech.verification.image.registry` | Docker 이미지 리포지토리에 있는 `helm test` 테스트를 사용 하 여 **음성-텍스트** 서비스입니다. Helm 테스트를 위해 클러스터 내에서 별도 pod를 만듭니다 및 가져옵니다 합니다 *테스트 용도의* 이 레지스트리에서 이미지입니다. | `docker.io` |
| `textToSpeech.verification.image.repository` | Docker 이미지 리포지토리에 있는 `helm test` 테스트를 사용 하 여 **음성-텍스트** 서비스입니다. Helm 테스트 pod이이 리포지토리를 사용 하 여 가져오려고 *테스트 용도의* 이미지입니다. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | 사용 하 여 docker 이미지 태그 `helm test` 에 대 한 **음성-텍스트** 서비스입니다. Helm 테스트 pod이이 태그를 사용 하 여 가져오려고 *테스트 용도의* 이미지입니다. | `latest` |
| `textToSpeech.verification.image.pullByHash` | 여부는 *테스트 용도의* 해시 하 여 docker 이미지를 끌어온. 하는 경우 `true`, `textToSpeech.verification.image.hash` 올바른 이미지 해시 값으로 추가 해야 합니다. | `false` |
| `textToSpeech.verification.image.arguments` | 인수를 사용 하 여 실행 합니다 *테스트 용도의* docker 이미지입니다. Helm 테스트 pod를 실행 하는 경우 컨테이너에 이러한 인수를 전달 `helm test`합니다. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |