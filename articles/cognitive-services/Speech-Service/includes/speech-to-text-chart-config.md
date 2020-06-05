---
title: 음성 컨테이너 설치
titleSuffix: Azure Cognitive Services
description: 음성 텍스트 투구 차트 구성 옵션에 대해 자세히 설명 합니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82875985"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>음성 텍스트 (하위 차트: 차트/speechToText)

"파라솔" 차트를 재정의 하려면 매개 변수에 접두사를 추가 하 여 `speechToText.` 더 구체적으로 만듭니다. 예를 들어이 매개 변수는 재정의와 같이 해당 매개 변수를 재정의 `speechToText.numberOfConcurrentRequest` `numberOfConcurrentRequest` 합니다.

|매개 변수|Description|기본값|
| -- | -- | -- |
| `enabled` | **음성 텍스트** 서비스의 사용 여부를 지정 합니다. | `false` |
| `numberOfConcurrentRequest` | **음성 텍스트** 서비스에 대 한 동시 요청 수입니다. 이 차트는이 값에 따라 CPU 및 메모리 리소스를 자동으로 계산 합니다. | `2` |
| `optimizeForAudioFile`| 서비스에서 오디오 파일을 통해 오디오 입력을 최적화 해야 하는지 여부입니다. 인 경우 `true` 이 차트는 서비스에 더 많은 CPU 리소스를 할당 합니다. | `false` |
| `image.registry`| **음성 텍스트** docker 이미지 레지스트리 | `containerpreview.azurecr.io` |
| `image.repository` | **음성 텍스트** docker 이미지 리포지토리입니다. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **음성 텍스트** docker 이미지 태그입니다. | `latest` |
| `image.pullSecrets` | **음성 텍스트** docker 이미지를 가져오기 위한 이미지 비밀입니다. | |
| `image.pullByHash`| Docker 이미지를 해시로 끌어올 지 여부를 나타냅니다. 인 `true` 경우 `image.hash` 가 필요 합니다. | `false` |
| `image.hash`| **음성 텍스트** docker 이미지 해시입니다. 인 경우에만 사용 `image.pullByHash: true` 됩니다.  | |
| `image.args.eula`(필수) | 라이선스를 수락 했음을 나타냅니다. 유일 하 게 유효한 값은`accept` | |
| `image.args.billing`(필수) | 청구 끝점 URI 값은 Azure Portal의 음성 개요 페이지에서 사용할 수 있습니다. | |
| `image.args.apikey`(필수) | 청구 정보를 추적하는 데 사용됩니다. ||
| `service.type` | **음성 텍스트** 서비스의 Kubernetes 서비스 유형입니다. 자세한 내용은 [Kubernetes service types 지침](https://kubernetes.io/docs/concepts/services-networking/service/) 및 클라우드 공급자 지원 확인을 참조 하세요. | `LoadBalancer` |
| `service.port`|  **음성 텍스트** 서비스의 포트입니다. | `80` |
| `service.annotations` | 서비스 메타 데이터에 대 한 **음성 텍스트** 주석입니다. 주석은 키 값 쌍입니다. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | [수평 Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) 사용 되는지 여부를 나타냅니다. 인 경우 `true` `speech-to-text-autoscaler` Kubernetes 클러스터에가 배포 됩니다. | `true` |
| `service.podDisruption.enabled` | [Pod 중단 예산이](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) 사용 되는지 여부를 나타냅니다. 인 경우 `true` `speech-to-text-poddisruptionbudget` Kubernetes 클러스터에가 배포 됩니다. | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>감정 분석 (하위 차트: 차트/speechToText)

2.2.0 컨테이너의 v 및 투구 차트의 v 0.2.0에서 시작 하 여 다음 매개 변수는 텍스트 분석 API을 사용 하 여 감정 분석에 사용 됩니다.

|매개 변수|Description|값|기본값|
| --- | --- | --- | --- |
|`textanalytics.enabled`| **텍스트 분석** 서비스의 설정 여부| true/false| `false`|
|`textanalytics.image.registry`| **텍스트 분석** docker 이미지 레지스트리| 유효한 docker 이미지 레지스트리| |
|`textanalytics.image.repository`| **텍스트 분석** docker 이미지 리포지토리| 유효한 docker 이미지 리포지토리| |
|`textanalytics.image.tag`| **텍스트-분석** docker 이미지 태그| 유효한 docker 이미지 태그| |
|`textanalytics.image.pullSecrets`| **텍스트 분석** docker 이미지를 가져오기 위한 이미지 암호| 유효한 비밀 이름| |
|`textanalytics.image.pullByHash`| 해시로 docker 이미지를 끌어오는 지 여부를 지정 합니다.  인 경우에 `yes` `image.hash` 도이 있어야 합니다. 인 경우 `no` ' f a l s e '로 설정 합니다. 기본값은 `false`입니다.| true/false| `false`|
|`textanalytics.image.hash`| **텍스트 분석** docker 이미지 해시입니다. 에서만 사용 `image.pullByHash:true` 합니다.| 유효한 docker 이미지 해시 | |
|`textanalytics.image.args.eula`| **텍스트 분석** 컨테이너의 필수 인수 중 하나로, 라이선스를 수락한 상태임을 나타냅니다. 이 옵션의 값은 이어야 `accept` 합니다.| `accept`컨테이너를 사용 하려는 경우 | |
|`textanalytics.image.args.billing`| 청구 끝점 URI를 지정 하는 **텍스트 분석** 컨테이너의 필수 인수 중 하나입니다. 청구 끝점 URI 값은 Azure Portal의 음성 개요 페이지에서 사용할 수 있습니다.|유효한 청구 끝점 URI||
|`textanalytics.image.args.apikey`| 청구 정보를 추적 하는 데 사용 되는 **텍스트 분석** 컨테이너의 필수 인수 중 하나입니다.| 올바른 apikey||
|`textanalytics.cpuRequest`| **텍스트 분석** 컨테이너에 대해 요청 된 CPU| int| `3000m`|
|`textanalytics.cpuLimit`| **텍스트 분석** 컨테이너에 대해 제한 된 CPU| | `8000m`|
|`textanalytics.memoryRequest`| **텍스트 분석** 컨테이너에 대해 요청 된 메모리| | `3Gi`|
|`textanalytics.memoryLimit`| **텍스트 분석** 컨테이너에 대해 제한 된 메모리| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| 감정 분석 URI 접미사, 전체 URI는 "http://:" 형식입니다 `<service>` `<port>` / `<sentimentURISuffix>` . | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Kubernetes의 **텍스트 분석** 서비스 유형입니다. [Kubernetes 서비스 형식](https://kubernetes.io/docs/concepts/services-networking/service/) 을 참조 하세요. | 유효한 Kubernetes 서비스 유형 | `LoadBalancer` |
|`textanalytics.service.port`| **텍스트 분석** 서비스의 포트입니다.| int| `50085`|
|`textanalytics.service.annotations`| 사용자가 **텍스트 분석** 서비스 메타 데이터에 추가할 수 있는 주석입니다. 예를 들면 다음과 같습니다.<br/> **달**<br/>`   `**some/annotation1: value1**<br/>`  `**일부/annotation2: value2** | 각 줄 마다 하나씩 주석| |
|`textanalytics.serivce.autoScaler.enabled`| [수평 Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) 사용 여부를 지정 합니다. 사용 하도록 설정 하면 `text-analytics-autoscaler` Kubernetes 클러스터에 배포 됩니다. | true/false| `true`|
|`textanalytics.service.podDisruption.enabled`| [Pod 중단 예산을](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) 사용 하는지 여부를 나타냅니다. 사용 하도록 설정 하면 `text-analytics-poddisruptionbudget` Kubernetes 클러스터에 배포 됩니다.| true/false| `true`|
