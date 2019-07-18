---
title: 음성 컨테이너를 설치 합니다.
titleSuffix: Azure Cognitive Services
description: 음성-텍스트 helm 차트 구성 옵션을 자세히 설명합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717248"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>음성-텍스트 (하위 차트: 차트/speechToText)

추가 접두사 "포괄적인" 차트를 재정의 하려면 `speechToText.` 모든 매개 변수 보다 구체화 하기를 합니다. 예를 들어 덮어씁니다 해당 매개 변수의 예를 들어 `speechToText.numberOfConcurrentRequest` 재정의 `numberOfConcurrentRequest`합니다.

|매개 변수|Description|기본값|
| -- | -- | -- |
| `enabled` | 여부를 **음성-텍스트** 서비스를 사용 합니다. | `false` |
| `numberOfConcurrentRequest` | 에 대 한 동시 요청 수를 **음성-텍스트** 서비스입니다. 이 차트는 자동으로이 값을 기반으로 CPU 및 메모리 리소스를 계산 합니다. | `2` |
| `optimizeForAudioFile`| 여부 서비스 오디오 파일을 통해 오디오 입력에 대 한 최적화 해야 합니다. 경우 `true`,이 차트에 더 많은 CPU 리소스 할당 됩니다. | `false` |
| `image.registry`| 합니다 **음성-텍스트** docker 이미지 레지스트리입니다. | `containerpreview.azurecr.io` |
| `image.repository` | 합니다 **음성-텍스트** docker 이미지 리포지토리. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | 합니다 **음성-텍스트** docker 이미지 태그입니다. | `latest` |
| `image.pullSecrets` | 풀에 대 한 이미지 암호를 **음성-텍스트** docker 이미지입니다. | |
| `image.pullByHash`| 여부 docker 이미지를 해시 하 여 가져옵니다. 하는 경우 `true`, `image.hash` 필요 합니다. | `false` |
| `image.hash`| 합니다 **음성-텍스트** docker 이미지 해시 합니다. 경우에 사용 `image.pullByHash: true`합니다.  | |
| `image.args.eula` (필수) | 라이선스에 동의 하면 나타냅니다. 유효한 값은 `accept` | |
| `image.args.billing` (필수) | 청구 끝점 URI 값은 Azure portal의 음성 개요 페이지에서 사용할 수 있습니다. | |
| `image.args.apikey` (필수) | 청구 정보를 추적하는 데 사용됩니다. ||
| `service.type` | Kubernetes 서비스 유형의 합니다 **음성-텍스트** 서비스입니다. 참조를 [Kubernetes 서비스 형식 지침](https://kubernetes.io/docs/concepts/services-networking/service/) 대 한 자세한 내용은 클라우드 공급자 지원 확인 합니다. | `LoadBalancer` |
| `service.port`|  포트를 **음성-텍스트** 서비스입니다. | `80` |
| `service.autoScaler.enabled` | 여부를 합니다 [수평 Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) 사용 가능 합니다. 하는 경우 `true`, `speech-to-text-autoscaler` Kubernetes 클러스터에 배포 됩니다. | `true` |
| `service.podDisruption.enabled` | 여부를 합니다 [Pod 중단 예산](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) 사용 가능 합니다. 하는 경우 `true`, `speech-to-text-poddisruptionbudget` Kubernetes 클러스터에 배포 됩니다. | `true` |