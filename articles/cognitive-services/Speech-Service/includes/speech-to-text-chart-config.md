---
title: 음성 컨테이너 설치
titleSuffix: Azure Cognitive Services
description: 음성-텍스트 투구 차트 구성 옵션에 대해 자세히 설명합니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: trbye
ms.openlocfilehash: f7ca8fdaddab9757292939c4f7e658179d6e517c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421795"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>음성-텍스트(하위 차트: 차트/speechToText)

"우산" 차트를 재정의하려면 매개 `speechToText.` 변수에 접두사를 추가하여 보다 구체적으로 만듭니다. 예를 들어, 해당 매개 변수를 재정의합니다. `speechToText.numberOfConcurrentRequest` `numberOfConcurrentRequest`

|매개 변수|Description|기본값|
| -- | -- | -- |
| `enabled` | **음성-텍스트** 서비스가 활성화되어 있는지 여부입니다. | `false` |
| `numberOfConcurrentRequest` | 음성-텍스트 서비스에 대한 동시 요청 **수입니다.** 이 차트는 이 값을 기반으로 CPU 및 메모리 리소스를 자동으로 계산합니다. | `2` |
| `optimizeForAudioFile`| 서비스가 오디오 파일을 통해 오디오 입력을 최적화해야 하는지 여부입니다. 이 `true`차트에서 서비스에 더 많은 CPU 리소스를 할당합니다. | `false` |
| `image.registry`| 음성-텍스트 도커 이미지 **레지스트리입니다.** | `containerpreview.azurecr.io` |
| `image.repository` | **음성-텍스트** 도커 이미지 리포지토리입니다. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **음성-텍스트** 도커 이미지 태그입니다. | `latest` |
| `image.pullSecrets` | **음성-텍스트** 도커 이미지를 가져오는 이미지 의 비밀입니다. | |
| `image.pullByHash`| 도커 이미지가 해시에 의해 당겨지는지 여부입니다. 이 `true` `image.hash` 경우 . | `false` |
| `image.hash`| **음성-텍스트** 도커 이미지 해시입니다. 할 때만 `image.pullByHash: true`사용됩니다.  | |
| `image.args.eula`(필수) | 라이선스를 수락했음을 나타냅니다. 유효한 유일한 값은`accept` | |
| `image.args.billing`(필수) | 청구 끝점 URI 값은 Azure 포털의 음성 개요 페이지에서 사용할 수 있습니다. | |
| `image.args.apikey`(필수) | 청구 정보를 추적하는 데 사용됩니다. ||
| `service.type` | **음성-텍스트** 서비스의 Kubernetes 서비스 유형입니다. 자세한 내용은 [Kubernetes 서비스 유형 지침을](https://kubernetes.io/docs/concepts/services-networking/service/) 참조하고 클라우드 공급자 지원을 확인하십시오. | `LoadBalancer` |
| `service.port`|  **음성-텍스트** 서비스의 포트입니다. | `80` |
| `service.annotations` | 서비스 메타데이터에 대한 **음성-텍스트** 주석입니다. 주석은 키 값 쌍입니다. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | 가로 [포드 자동 크기 조정기가](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) 활성화되어 있는지 여부입니다. 이가 `true` `speech-to-text-autoscaler` Kubernetes 클러스터에 배포될 경우 | `true` |
| `service.podDisruption.enabled` | 포드 [중단 예산이](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) 활성화되어 있는지 여부입니다. 이가 `true` `speech-to-text-poddisruptionbudget` Kubernetes 클러스터에 배포될 경우 | `true` |