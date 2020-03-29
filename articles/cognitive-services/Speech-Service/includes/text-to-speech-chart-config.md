---
title: 음성 컨테이너 설치
titleSuffix: Azure Cognitive Services
description: 텍스트 음성 변환 헬름 차트 구성 옵션에 대해 자세히 설명합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: c87132c3ae41dec82e3493f9a0ee2397455ff881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971335"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>텍스트 음성 변환(하위 차트: 차트/텍스트음성)

"우산" 차트를 재정의하려면 매개 `textToSpeech.` 변수에 접두사를 추가하여 보다 구체적으로 만듭니다. 예를 들어, 해당 매개 변수를 재정의합니다. `textToSpeech.numberOfConcurrentRequest` `numberOfConcurrentRequest`

|매개 변수|설명|기본값|
| -- | -- | -- |
| `enabled` | 텍스트 **음성 변환** 서비스가 활성화되어 있는지 여부입니다. | `false` |
| `numberOfConcurrentRequest` | 텍스트 음성 변환 서비스에 대한 동시 요청 **수입니다.** 이 차트는 이 값을 기반으로 CPU 및 메모리 리소스를 자동으로 계산합니다. | `2` |
| `optimizeForTurboMode`| 서비스가 텍스트 파일을 통해 텍스트 입력을 최적화해야 하는지 여부입니다. 이 `true`차트에서 서비스에 더 많은 CPU 리소스를 할당합니다. | `false` |
| `image.registry`| **텍스트 음성 변환** 도커 이미지 레지스트리입니다. | `containerpreview.azurecr.io` |
| `image.repository` | **텍스트 음성 변환** 도커 이미지 리포지토리입니다. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | **텍스트 음성 변환** 도커 이미지 태그입니다. | `latest` |
| `image.pullSecrets` | **텍스트 음성 변환** 도커 이미지를 가져오는 이미지 의 비밀입니다. | |
| `image.pullByHash`| 도커 이미지가 해시에 의해 당겨지는지 여부입니다. 이 `true` `image.hash` 경우 . | `false` |
| `image.hash`| **텍스트 음성 변환** 도커 이미지 해시입니다. 할 때만 `image.pullByHash: true`사용됩니다.  | |
| `image.args.eula`(필수) | 라이선스를 수락했음을 나타냅니다. 유효한 유일한 값은`accept` | |
| `image.args.billing`(필수) | 청구 끝점 URI 값은 Azure 포털의 음성 개요 페이지에서 사용할 수 있습니다. | |
| `image.args.apikey`(필수) | 청구 정보를 추적하는 데 사용됩니다. ||
| `service.type` | **텍스트** 음성 변환 서비스의 Kubernetes 서비스 유형입니다. 자세한 내용은 [Kubernetes 서비스 유형 지침을](https://kubernetes.io/docs/concepts/services-networking/service/) 참조하고 클라우드 공급자 지원을 확인하십시오. | `LoadBalancer` |
| `service.port`|  텍스트 음성 **변환** 서비스의 포트입니다. | `80` |
| `service.annotations` | 서비스 메타데이터에 대한 **텍스트 음성 변환** 주석입니다. 주석은 키 값 쌍입니다. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | 가로 [포드 자동 크기 조정기가](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) 활성화되어 있는지 여부입니다. 이가 `true` `text-to-speech-autoscaler` Kubernetes 클러스터에 배포될 경우 | `true` |
| `service.podDisruption.enabled` | 포드 [중단 예산이](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) 활성화되어 있는지 여부입니다. 이가 `true` `text-to-speech-poddisruptionbudget` Kubernetes 클러스터에 배포될 경우 | `true` |