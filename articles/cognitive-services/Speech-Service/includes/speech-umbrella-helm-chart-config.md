---
title: 음성 컨테이너 설치
titleSuffix: Azure Cognitive Services
description: 음성 우산 투구 차트 구성 옵션에 대해 자세히 설명합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73523411"
---
### <a name="speech-umbrella-chart"></a>스피치(우산 도표)

최상위 "우산" 차트의 값은 해당 하위 차트 값을 재정의합니다. 따라서 모든 온-프레미스 사용자 지정 값은 여기에 추가되어야 합니다.

|매개 변수|설명|기본값|
| -- | -- | -- | -- |
| `speechToText.enabled` | **음성-텍스트** 서비스가 활성화되어 있는지 여부입니다. | `true` |
| `speechToText.verification.enabled` | `helm test` **음성-텍스트** 서비스 기능이 활성화되어 있는지 여부입니다. | `true` |
| `speechToText.verification.image.registry` | **음성-텍스트** 서비스를 테스트하는 `helm test` 데 사용하는 docker 이미지 리포지토리입니다. Helm은 테스트를 위해 클러스터 내부에 별도의 포드를 만들고 이 레지스트리에서 *테스트 사용* 이미지를 가져옵니다. | `docker.io` |
| `speechToText.verification.image.repository` | **음성-텍스트** 서비스를 테스트하는 `helm test` 데 사용하는 docker 이미지 리포지토리입니다. Helm 테스트 포드는 이 리포지토리를 사용하여 *테스트 사용* 이미지를 가져옵니다. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | **음성-텍스트** 서비스에 `helm test` 사용되는 도커 이미지 태그입니다. Helm 테스트 포드는 이 태그를 사용하여 *테스트 사용* 이미지를 가져옵니다. | `latest` |
| `speechToText.verification.image.pullByHash` | 테스트 *사용* 도커 이미지가 해시로 당겨지는지 여부입니다. 을 `true` `speechToText.verification.image.hash` 추가해야 하는 경우 유효한 이미지 해시 값입니다. | `false` |
| `speechToText.verification.image.arguments` | *테스트 사용* docker 이미지를 실행하는 데 사용되는 인수입니다. Helm 테스트 포드는 실행 `helm test`시 이러한 인수를 컨테이너에 전달합니다. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | 텍스트 **음성 변환** 서비스가 활성화되어 있는지 여부입니다. | `true` |
| `textToSpeech.verification.enabled` | `helm test` **음성-텍스트** 서비스 기능이 활성화되어 있는지 여부입니다. | `true` |
| `textToSpeech.verification.image.registry` | **음성-텍스트** 서비스를 테스트하는 `helm test` 데 사용하는 docker 이미지 리포지토리입니다. Helm은 테스트를 위해 클러스터 내부에 별도의 포드를 만들고 이 레지스트리에서 *테스트 사용* 이미지를 가져옵니다. | `docker.io` |
| `textToSpeech.verification.image.repository` | **음성-텍스트** 서비스를 테스트하는 `helm test` 데 사용하는 docker 이미지 리포지토리입니다. Helm 테스트 포드는 이 리포지토리를 사용하여 *테스트 사용* 이미지를 가져옵니다. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | **음성-텍스트** 서비스에 `helm test` 사용되는 도커 이미지 태그입니다. Helm 테스트 포드는 이 태그를 사용하여 *테스트 사용* 이미지를 가져옵니다. | `latest` |
| `textToSpeech.verification.image.pullByHash` | 테스트 *사용* 도커 이미지가 해시로 당겨지는지 여부입니다. 을 `true` `textToSpeech.verification.image.hash` 추가해야 하는 경우 유효한 이미지 해시 값입니다. | `false` |
| `textToSpeech.verification.image.arguments` | *테스트 사용* docker 이미지로 실행할 인수입니다. helm 테스트 포드는 실행 `helm test`시 이러한 인수를 컨테이너에 전달합니다. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |