---
title: Media Services v3 용어 및 엔터티 변경
description: 이 문서에서는 Azure Media Services v2와 v3 간의 용어 차이를 설명합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 42c4c91b6715ffec4c734632c69623206bc6dbae
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076005"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Media Services V2와 V3 간의 용어 및 엔터티 변경 사항

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 단계 2](./media/migration-guide/steps-2.svg)

이 문서에서는 Azure Media Services v2와 v3 간의 용어 차이를 설명합니다.

## <a name="naming-conventions"></a>명명 규칙

Media Services V3 리소스에 적용되는 명명 규칙을 검토합니다. [Blob 명명](assets-concept.md#naming)도 검토합니다.

## <a name="terminology-changes"></a>용어 변경

- 이제 *로케이터* 를 *스트리밍 로케이터* 라고 합니다.
- 이제 *채널* 을 *라이브 이벤트* 라고 합니다.
- 이제 *프로그램* 을 *라이브 출력* 이라고 합니다.
- *작업* 은 이제 작업의 일부인 *JobOutput* 이라고 합니다.

## <a name="entity-changes"></a>엔터티 변경
| **V2 엔터티**<!-- row --> | **V3 엔터티** | **지침** | **V3에서 액세스할 수 있음** | **V3에서 업데이트 됨** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  이 엔터티 `AccessPolicies`는 V3에 존재하지 않습니다. | 예 | 예 |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | 예 | 예 |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | 예 | 예 |
| `AssetFile`<!-- row --> | <!-- empty --> |이 엔터티 `AssetFiles`는 V3에 존재하지 않습니다. 업로드하는 파일(스토리지 BLOB)은 여전히 파일로 간주됩니다.<br/><br/> 대신 Azure Storage API를 사용하여 컨테이너의 BLOB을 열거합니다. 작업을 사용하여 파일에 변환을 적용하는 방법에는 두 가지가 있습니다.<br/><br/>스토리지에 이미 업로드된 파일: URI에는 스토리지 계정 내의 자산에 수행되는 작업의 자산 ID가 포함됩니다.<br/><br/>변환 및 작업 프로세스 중에 업로드할 파일: 자산은 스토리지에서 만들어지고, SAS URL이 반환되고, 파일이 스토리지에 업로드된 후 파일에 변환이 적용됩니다. | 예 | 예 |
| `Channel`<!-- row --> | `LiveEvent` | 라이브 이벤트는 v2 API에서 채널을 대체합니다. 라이브 이벤트는 대부분의 기능을 계속해서 제공하며 라이브 대화 내용 기록, 대기 모드, RTMPS 수집 지원과 같은 새로운 기능을 제공합니다. <br/><br/>[시나리오 기반 라이브 스트리밍의 라이브 이벤트](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)를 참조하세요. | 예 | 예 |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys`는 더 이상 엔터티가 아니므로 이제 스트리밍 로케이터의 속성입니다.<br/><br/>  V3에서 콘텐츠 키 데이터는 `StreamingLocator`(출력 암호화의 경우) 또는 자산 자체(클라이언트 쪽 스토리지 암호화의 경우)와 연결되어 있습니다. | 예 | 예 |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | 예 | 예 |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions`는 `ContentKeyPolicy`에 포함됩니다. | 예 | 예 |
| `IngestManifest`<!-- row --> | <!-- empty --> | 이 엔터티 `IngestManifests`는 V3에 존재하지 않습니다. V3에서 파일 업로드는 Azure 스토리지 API를 포함합니다. 먼저 자산이 생성된 다음 연결된 스토리지 컨테이너에 파일이 업로드됩니다. 대신 사용할 수 있는 Azure Storage 컨테이너에 데이터를 가져오는 방법은 여러 가지가 있습니다. `JobInputHttp` 또한 원하는 경우 지정된 URL에서 작업 입력을 다운로드하는 방법을 제공합니다. | 예 | 예 |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | 대신 사용할 수 있는 Azure Storage 컨테이너에 데이터를 가져오는 방법은 여러 가지가 있습니다. `JobInputHttp` 또한 원하는 경우 지정된 URL에서 작업 입력을 다운로드하는 방법을 제공합니다. | 예 | 예 |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | 대신 사용할 수 있는 Azure Storage 컨테이너에 데이터를 가져오는 방법은 여러 가지가 있습니다. `JobInputHttp` 또한 원하는 경우 지정된 URL에서 작업 입력을 다운로드하는 방법을 제공합니다. | 예 | 예 |
| `Job`<!-- row --> | `Job` | `Job`을 만들기 전에 `Transform`을. | 예 | 예 |
| `JobTemplate`<!-- row --> | `Transform` | 사용 된 `Transform` 대신 합니다. 변환은 작업과는 별개의 엔터티이며 재사용할 수 있습니다. | 예 | 예 |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | 예 | 예 |
| `MediaProcessor`<!-- row --> | <!-- empty --> | 사용할 `MediaProcessor`를 이름으로 조회하는 대신, 변환을 정의할 때 원하는 사전 설정을 사용합니다. 사용되는 사전 설정에 따라 작업 시스템에서 사용하는 미디어 프로세서가 결정됩니다. [시나리오 기반 인코딩](migrate-v-2-v-3-migration-scenario-based-encoding.md)의 인코딩 토픽을 참조하세요. <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | 예 | NA(V2에서 readonly) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | V3에서 알림은 Azure Event Grid를 통해 처리됩니다. `NotificationEndpoint`는 받을 알림 형식에 관한 구성(v2에서는 작업의 `JobNotificationSubscription`, 작업의 `TaskNotificationSubscription`, 원격 분석 `ComponentMonitoringSetting`로 처리)도 캡슐화하는 Event GrID 구독 등록으로 대체됩니다. V2 원격 분석은 대규모 Azure 에코시스템의 향상된 기능에 맞게 Azure Event GrID와 Azure Monitor 간에 분할되었습니다. | 예 | 예 |
| `Program`<!-- row --> | `LiveOutput` | 이제 라이브 출력이 v3 API의 프로그램을 대체합니다.  | 예 | 예 |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | 스트리밍 엔드포인트는 주로 동일하게 유지됩니다. 동적 패키징, 암호화, 원본에서 직접 또는 CDN을 통해 라이브 및 주문형 스트리밍 모두에 관한 HLS 및 DASH 콘텐츠를 제공하는 데 사용됩니다. 새로운 기능에는 Azure Monitor 통합 및 차트 작성 개선을 위한 지원이 포함됩니다. |  예 | 예 |
| `Task`<!-- row --> | `JobOutput` | `JobOutput`(더 이상 API에서 별도의 엔터티가 아님)으로 대체되었습니다.  [시나리오 기반 인코딩](migrate-v-2-v-3-migration-scenario-based-encoding.md)의 인코딩 토픽을 참조하세요. | 예 | 예 |
| `TaskTemplate`<!-- row --> | `TransformOutput` | `TransformOutput`(더 이상 API에서 별도의 엔터티가 아님)으로 대체되었습니다. [시나리오 기반 인코딩](migrate-v-2-v-3-migration-scenario-based-encoding.md)의 인코딩 토픽을 참조하세요. | 예 | 예 |
| `Inputs`<!-- row --> | `Inputs` | 현재 입력 및 출력은 작업 수준에 있습니다. [시나리오 기반 인코딩](migrate-v-2-v-3-migration-scenario-based-encoding.md)의 인코딩 항목을 참조하세요. | 예 | 예 |
| `Outputs`<!-- row --> | `Outputs` | 현재 입력 및 출력은 작업 수준에 있습니다.  V3에서 메타데이터 형식이 XML에서 JSON으로 변경되었습니다.  라이브 출력은 생성과 동시에 시작되고 삭제되면 중지됩니다. [시나리오 기반 인코딩](migrate-v-2-v-3-migration-scenario-based-encoding.md)의 인코딩 항목을 참조하세요. | 예 | 예 |


| **기타 변경 내용** | **V2**  | **V3** |
|---|---|---|
| **스토리지** <!--new row --> |||
| 스토리지 <!--new row --> | | V3 SDK가 Storage SDK에서 분리되었으며, 따라서 사용하고 싶은 Storage SDK를 더욱 철저하게 제어하고 버전 문제를 피할 수 있게 되었습니다.                      |
| **인코딩** <!--new row --> |||
| 인코딩 비트 전송률 <!--new row --> | 비트 전송률(kbps) 예: 128(kbps)| 초당 비트 예: 128000(비트/초)|
| 인코딩 DRM FairPlay <!--new row --> | Media Services V2에서는 IV(초기화 벡터)를 지정할 수 있습니다. | Media Services V3에서는 FairPlay IV를 지정할 수 없습니다.|
| 프리미엄 인코더 <!--new row --> | 프리미엄 인코더 및 레거시 인덱서| V3를 통해 [프리미엄 인코더](../previous/media-services-encode-asset.md) 및 레거시 [미디어 분석 프로세서](../previous/legacy-components.md)(Azure Media Services Indexer 2 미리 보기, Face Redactor 등)에 액세스할 수 없습니다. 표준 인코더에 관한 오디오 채널 매핑을 위한 지원이 추가되었습니다.  [Media Services Encoding Swagger 설명서에서 오디오](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)를 참조하세요.  <br/> [시나리오 기반 인코딩](migrate-v-2-v-3-migration-scenario-based-encoding.md)의 인코딩 항목을 참조하세요. |
| **변환 및 작업** <!--new row -->|||
| 작업 기반 처리 HTTPS <!--new row --> |<!-- empty -->| 파일 기반 작업 처리의 경우 HTTPS URL을 입력으로 사용할 수 있습니다. 아직 콘텐츠를 Azure에 저장하거나 자산을 만들 필요가 없습니다. |
| 작업용 ARM 템플릿 <!--new row --> | ARM 템플릿이 V2에 없습니다. | 변환을 사용하여 재사용 가능한 구성을 빌드하고, Azure Resource Manager 템플릿을 만들고, 여러 고객 또는 테넌트 간에 처리 설정을 격리할 수 있습니다. |
| **라이브 이벤트** <!--new row --> |||
| 스트리밍 엔드포인트 <!--new row --> | 스트리밍 엔드포인트는 추가 배포를 위해 CDN(Content Delivery Network) 또는 클라이언트 플레이어 애플리케이션에 직접 콘텐츠를 배달할 수 있는 스트리밍 서비스를 나타냅니다. | 스트리밍 엔드포인트는 주로 동일하게 유지됩니다. 동적 패키징, 암호화, 원본에서 직접 또는 CDN을 통해 라이브 및 주문형 스트리밍 모두에 관한 HLS 및 DASH 콘텐츠를 제공하는 데 사용됩니다.  새로운 기능에는 Azure Monitor 통합 및 차트 작성 개선을 위한 지원이 포함됩니다. |
| 라이브 이벤트 채널 <!--new row --> | 채널은 라이브 스트리밍 콘텐츠 처리를 담당합니다. 채널은 라이브 트랜스코더에 제공될 입력 엔드포인트(수집 URL)를 제공합니다. 채널은 라이브 트랜스코더에서 라이브 입력 스트림을 수신하여 하나 이상의 StreamingEndpoints를 통해 스트리밍하는 데 사용할 수 있도록 합니다. 또한 채널은 스트림을 추가로 처리하고 배달하기 전에 미리 보고 유효성을 검색하는 데 사용되는 미리 보기 엔드포인트(미리 보기 URL)를 제공합니다.| 라이브 이벤트는 v2 API에서 채널을 대체합니다. 라이브 이벤트는 대부분의 기능을 계속해서 제공하며 라이브 대화 내용 기록, 대기 모드, RTMPS 수집 지원과 같은 새로운 기능을 제공합니다. |
| 라이브 이벤트 프로그램 <!--new row --> | 프로그램은 라이브 스트림의 세그먼트 게시 및 스토리지을 제어합니다. 채널은 프로그램을 관리합니다. 채널 및 프로그램 관계는 기존 미디어와 유사하여 채널에는 일정한 콘텐츠 스트림이 있고 프로그램 범위는 해당 채널에 있는 일부 시간 제한 이벤트로 지정됩니다. `ArchiveWindowLength` 속성을 설정하여 프로그램을 위해 녹음/녹화된 콘텐츠를 유지할 시간을 지정할 수 있습니다. 이 값은 최소 5분에서 최대 25시간 사이로 설정할 수 있습니다.| 이제 라이브 출력이 v3 API의 프로그램을 대체합니다. |
| 라이브 이벤트 길이 <!--new row --> |<!-- empty -->| 단일 비트 전송률 기여 피드를 다중 비트 전송률이 있는 출력 스트림으로 코드 변환하기 위해 Media Services를 사용할 때 라이브 이벤트를 연중무휴 스트리밍할 수 있습니다.|
| 라이브 이벤트 대기 시간 <!--new row --> |<!-- empty -->| 대기 시간이 짧은 새 라이브 스트리밍이 라이브 이벤트에서 지원됩니다. |
| 라이브 이벤트 미리 보기 <!--new row --> |<!-- empty -->| 라이브 이벤트 미리 보기는 동적 패키징 및 동적 암호화를 지원합니다. 이렇게 하면 DASH 및 HLS 패키징뿐만 아니라 미리 보기에서도 콘텐츠 보호가 가능합니다. |
| 라이브 이벤트 RTMPS <!--new row --> |<!-- empty-->| RTMPS 지원 개선(향상된 안정성 및 더 많은 소스 인코더 지원). |
| 라이브 이벤트 RTMPS 보안 수집 <!--new row --> | | 라이브 이벤트를 만들면 수집 URL이 4개 생성됩니다. 4개의 수집 URL은 거의 동일하며 스트리밍 토큰 `AppId`가 동일하고 포트 번호 부분만 다릅니다. URL 중 두 개는 RTMPS에 대한 기본 및 백업용입니다.| 
| 라이브 이벤트 대화 내용 기록 <!--new row --> |<!-- empty--> | Azure 미디어 서비스는 다양한 프로토콜에서 비디오, 오디오, 텍스트를 제공합니다. MPEG-DASH 또는 HLS/CMAF를 사용하여 라이브 스트림을 게시하면 비디오 및 오디오와 함께 서비스가 IMSC 1.1 호환 TTML로 기록된 텍스트를 제공합니다.|
| 라이브 이벤트 대기 모드 <!--new row --> | V2용 대기 모드가 없습니다. | 대기 모드는 라이브 이벤트의 핫 풀을 관리하는 데 도움이 되는 새로운 v3 기능입니다. 이제 고객은 실행 상태로 전환하기 전에 대기 모드에서 라이브 이벤트를 시작하는 비용을 절감할 수 있습니다. 이는 채널 시작 시각을 개선하고 보다 빠른 스타트업을 위해 운영 중인 핫 풀의 비용을 줄입니다. |
| 라이브 이벤트 청구 <!--new row --> | <!-- empty-->| 라이브 이벤트 요금은 라이브 채널 미터를 기반으로 청구됩니다. |
| 라이브 출력 <!--new row --> | 프로그램은 만든 후에 시작해야 합니다. | 라이브 출력은 생성과 동시에 시작되고 삭제되면 중지됩니다. |
