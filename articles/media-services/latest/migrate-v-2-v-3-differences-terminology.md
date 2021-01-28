---
title: Media Services v3 용어 및 엔터티 변경
description: 이 문서에서는 Azure Media Services v 2와 v3 간의 용어 차이점을 설명 합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: a2348e0578b60c59fd7205037bd42d7bb1e84fae
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953702"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Media Services V2와 V3 간의 용어 및 엔터티 변경

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2 단계](./media/migration-guide/steps-2.svg)

이 문서에서는 Azure Media Services v 2와 v3 간의 용어 차이점을 설명 합니다.

## <a name="naming-conventions"></a>명명 규칙

Media Services V3 리소스에 적용 되는 명명 규칙을 검토 합니다. [Blob 명명](assets-concept.md#naming) 도 검토 합니다.

## <a name="terminology-changes"></a>용어 변경

- 이제 *로케이터* 를 *스트리밍 로케이터* 라고 합니다.
- 이제 *채널* 을 *라이브 이벤트* 라고 합니다.
- 이제 *프로그램* 을 *라이브 출력* 이라고 합니다.
- *작업* 은 이제 작업의 일부인 *joboutput* 이라고 합니다.

## <a name="entity-changes"></a>엔터티 변경
| **V2 엔터티**<!-- row --> | **V3 엔터티** | **지침** | **V3에서 액세스할 수 있음** | **V3에서 업데이트 됨** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  이 엔터티는 `AccessPolicies` V3에 존재 하지 않습니다. | 아니요 | 아니요 |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | 예 | 예 |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | 예 | 아니요 |
| `AssetFile`<!-- row --> | <!-- empty --> |이 엔터티는 `AssetFiles` V3에 존재 하지 않습니다. 업로드 하는 파일 (저장소 blob)은 여전히 파일로 간주 됩니다.<br/><br/> Azure Storage Api를 사용 하 여 컨테이너의 blob을 대신 열거 합니다. 작업을 사용 하 여 파일에 변환을 적용 하는 방법에는 두 가지가 있습니다.<br/><br/>저장소에 이미 업로드 된 파일: URI에는 저장소 계정 내의 자산에 대해 수행 되는 작업의 자산 ID가 포함 됩니다.<br/><br/>변환 및 작업 프로세스 중에 업로드할 파일: 자산은 저장소에서 만들어지고, SAS URL이 반환 되 고, 파일이 저장소에 업로드 된 후 파일에 변환이 적용 됩니다. | 아니요 | 아니요 |
| `Channel`<!-- row --> | `LiveEvent` | 라이브 이벤트는 v2 API에서 채널을 대체 합니다. 이러한 기능은 대부분의 기능을 제공 하며, 라이브 온라인 모드와 RTMPS 수집에 대 한 지원과 같은 새로운 기능을 제공 합니다. <br/><br/>[시나리오 기반 라이브 스트리밍의 라이브 이벤트를](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) 참조 하세요. | 아니요 | 아니요 |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` 는 더 이상 엔터티가 아니므로 이제 스트리밍 로케이터의 속성입니다.<br/><br/>  V3에서 콘텐츠 키 데이터는 `StreamingLocator` (출력 암호화의 경우) 또는 자산 자체 (클라이언트 쪽 저장소 암호화의 경우)와 연결 되어 있습니다. | 예 | 아니요 |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | 예 | 아니요 |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` 는에 포함 되어 `ContentKeyPolicy` 있습니다. | 예 | 아니요 |
| `IngestManifest`<!-- row --> | <!-- empty --> | 이 엔터티는 `IngestManifests` V3에 존재 하지 않습니다. V3에서 파일 업로드는 Azure storage API를 포함 합니다. 먼저 자산이 생성 된 다음 연결 된 저장소 컨테이너에 파일이 업로드 됩니다. 대신 사용할 수 있는 Azure Storage 컨테이너에 데이터를 가져오는 여러 가지 방법이 있습니다. `JobInputHttp` 또한 원하는 경우 지정 된 url에서 작업 입력을 다운로드 하는 방법을 제공 합니다. | 아니요 | 아니요 |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | 대신 사용할 수 있는 Azure Storage 컨테이너에 데이터를 가져오는 여러 가지 방법이 있습니다. `JobInputHttp` 또한 원하는 경우 지정 된 url에서 작업 입력을 다운로드 하는 방법을 제공 합니다. | 아니요 | 아니요 |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | 대신 사용할 수 있는 Azure Storage 컨테이너에 데이터를 가져오는 여러 가지 방법이 있습니다. `JobInputHttp` 또한 원하는 경우 지정 된 url에서 작업 입력을 다운로드 하는 방법을 제공 합니다. | 아니요 | 아니요 |
| `Job`<!-- row --> | `Job` | 를 만들기 `Transform` 전에를 만듭니다 `Job` . | 아니요 | 아니요 |
| `JobTemplate`<!-- row --> | `Transform` | 사용 된 `Transform` 대신 합니다. 변환은 작업과는 별개의 엔터티입니다. reuseable입니다. | 아니요 | 아니요 |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | 예 | 아니요 |
| `MediaProcessor`<!-- row --> | <!-- empty --> | 이름으로 사용할를 조회 하는 대신 `MediaProcessor` 변환을 정의할 때 원하는 미리 설정을 사용 합니다. 사용 되는 기본 설정에 따라 작업 시스템에서 사용 하는 미디어 프로세서가 결정 됩니다. [시나리오 기반 인코딩의](migrate-v-2-v-3-migration-scenario-based-encoding.md)인코딩 항목을 참조 하세요. <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | 아니요 | NA (V2에서 readonly) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | V3의 알림은 Azure Event Grid를 통해 처리 됩니다. 는 `NotificationEndpoint` 받을 알림 유형에 대 한 구성 (작업의에서 처리 한 `JobNotificationSubscription` , `TaskNotificationSubscription` 작업의 및 원격 분석)도 캡슐화 하는 Event Grid 구독 등록으로 대체 됩니다 `ComponentMonitoringSetting` . V2 원격 분석은 대규모 Azure 에코 시스템의 향상 된 기능에 맞게 Azure Event Grid와 Azure Monitor 간에 분할 되었습니다. | 아니요 | 아니요 |
| `Program`<!-- row --> | `LiveOutput` | 이제 라이브 출력이 v3 API의 프로그램을 대체 합니다.  | 아니요 | 아니요 |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | 스트리밍 끝점은 주로 동일 하 게 유지 됩니다. 원본에서 직접 또는 CDN을 통해 라이브 및 주문형 스트리밍 모두에 대 한 HLS 및 대시 콘텐츠를 동적 패키징, 암호화 및 제공 하는 데 사용 됩니다. 새로운 기능에는 통합 및 차트 Azure Monitor 향상을 위한 지원이 포함 됩니다. |  예 | 예 |
| `Task`<!-- row --> | `JobOutput` | `JobOutput`(더 이상 API에서 별도의 엔터티가 아님)로 대체 되었습니다.  [시나리오 기반 인코딩의](migrate-v-2-v-3-migration-scenario-based-encoding.md)인코딩 항목을 참조 하세요. | 아니요 | 아니요 |
| `TaskTemplate`<!-- row --> | `TransformOutput` | `TransformOutput`(더 이상 API에서 별도의 엔터티가 아님)로 대체 되었습니다. [시나리오 기반 인코딩의](migrate-v-2-v-3-migration-scenario-based-encoding.md)인코딩 항목을 참조 하세요. | 아니요 | 아니요 |
| `Inputs`<!-- row --> | `Inputs` | 현재 입력 및 출력은 작업 수준에 있습니다. [시나리오 기반 인코딩의](migrate-v-2-v-3-migration-scenario-based-encoding.md) 인코딩 항목을 참조 하세요. | 아니요 | 아니요 |
| `Outputs`<!-- row --> | `Outputs` | 현재 입력 및 출력은 작업 수준에 있습니다.  V3에서 메타 데이터 형식이 XML에서 JSON으로 변경 되었습니다.  라이브 출력은 생성과 동시에 시작되고 삭제되면 중지됩니다. [시나리오 기반 인코딩의](migrate-v-2-v-3-migration-scenario-based-encoding.md) 인코딩 항목을 참조 하세요. | 아니요 | 아니요 |


| **기타 변경 내용** | **[V2**  | **V3** |
|---|---|---|
| **스토리지** <!--new row --> |||
| 스토리지 <!--new row --> | | 이제 V3 Sdk가 저장소 SDK에서 분리 되어 사용 하려는 저장소 SDK 버전을 보다 강력 하 게 제어할 수 있으며 버전 관리 문제를 방지할 수 있습니다.                      |
| **인코딩** <!--new row --> |||
| 인코딩 비트 전송률 <!--new row --> | 비트 전송률 (kbps): 128 (kbps)| 초당 비트 예: 128000 (비트/초)|
| 인코딩 DRM FairPlay <!--new row --> | Media Services v 2에서는 IV (initialization vector)를 지정할 수 있습니다. | Media Services V3에서는 FairPlay IV를 지정할 수 없습니다.|
| 프리미엄 인코더 <!--new row --> | 프리미엄 인코더 및 레거시 인덱서| [프리미엄 인코더](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset) 및 레거시 [미디어 분석 프로세서](https://docs.microsoft.com/azure/media-services/previous/legacy-components) (Azure Media Services 인덱서 2 Preview, Face Redactor 등)는 V3을 통해 액세스할 수 없습니다. 표준 인코더에 대 한 오디오 채널 매핑에 대 한 지원이 추가 되었습니다.  [Media Services Encoding Swagger 설명서에서 오디오를](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)참조 하세요.  | [시나리오 기반 인코딩의](migrate-v-2-v-3-migration-scenario-based-encoding.md) 인코딩 항목을 참조 하세요. |
| **변환 및 작업** <!--new row -->|||
| 작업 기반 처리 HTTPS <!--new row --> |<!-- empty -->| 파일 기반 작업 처리의 경우 HTTPS URL을 입력으로 사용할 수 있습니다. 콘텐츠를 Azure에 이미 저장 하거나 자산을 만들 필요가 없습니다. |
| 작업용 ARM 템플릿 <!--new row --> | ARM 템플릿이 V2에 없습니다. | 변환을 사용 하 여 재사용 가능한 구성을 빌드하고, Azure Resource Manager 템플릿을 만들고, 여러 고객 또는 테 넌 트 간에 처리 설정을 격리할 수 있습니다. |
| **라이브 이벤트** <!--new row --> |||
| 스트리밍 엔드포인트 <!--new row --> | 스트리밍 끝점은 클라이언트 플레이어 응용 프로그램에 직접 콘텐츠를 배달할 수 있는 스트리밍 서비스 또는 추가 배포를 위해 CDN (Content Delivery Network)을 나타냅니다. | 스트리밍 끝점은 주로 동일 하 게 유지 됩니다. 원본에서 직접 또는 CDN을 통해 라이브 및 주문형 스트리밍 모두에 대 한 HLS 및 대시 콘텐츠를 동적 패키징, 암호화 및 제공 하는 데 사용 됩니다.  새로운 기능에는 통합 및 차트 Azure Monitor 향상을 위한 지원이 포함 됩니다. |
| 라이브 이벤트 채널 <!--new row --> | 채널은 라이브 스트리밍 콘텐츠 처리를 담당 합니다. 채널은 라이브 트랜스코더에 제공될 입력 엔드포인트(수집 URL)를 제공합니다. 채널은 라이브 코드 변환기에서 라이브 입력 스트림을 수신 하 고 하나 이상의 스트리밍 끝점을 통해 스트리밍할 수 있도록 합니다. 또한 채널은 스트림을 추가로 처리하고 배달하기 전에 미리 보고 유효성을 검색하는 데 사용되는 미리 보기 엔드포인트(미리 보기 URL)를 제공합니다.| 라이브 이벤트는 v2 API에서 채널을 대체 합니다. 이러한 기능은 대부분의 기능을 제공 하며, 라이브 온라인 모드와 RTMPS 수집에 대 한 지원과 같은 새로운 기능을 제공 합니다. |
| 라이브 이벤트 프로그램 <!--new row --> | 프로그램은 라이브 스트림의 세그먼트 게시 및 스토리지을 제어합니다. 채널은 프로그램을 관리합니다. 채널 및 프로그램 관계는 기존 미디어와 유사하여 채널에는 일정한 콘텐츠 스트림이 있고 프로그램 범위는 해당 채널에 있는 일부 시간 제한 이벤트로 지정됩니다. 속성을 설정 하 여 프로그램에 대해 기록 된 콘텐츠를 유지할 시간을 지정할 수 있습니다 `ArchiveWindowLength` . 이 값은 최소 5분에서 최대 25시간 사이로 설정할 수 있습니다.| 이제 라이브 출력이 v3 API의 프로그램을 대체 합니다. |
| 라이브 이벤트 길이 <!--new row --> |<!-- empty -->| Media Services를 사용 하 여 단일 비트 전송률 기여 피드를 여러 비트 전송률이 있는 출력 스트림으로 트랜스 코딩 하는 경우 라이브 이벤트 24/7을 스트리밍할 수 있습니다.|
| 라이브 이벤트 대기 시간 <!--new row --> |<!-- empty -->| 라이브 이벤트에서 새로운 짧은 대기 시간 라이브 스트리밍 지원 |
| 라이브 이벤트 미리 보기 <!--new row --> |<!-- empty -->| 라이브 이벤트 미리 보기는 동적 패키징 및 동적 암호화를 지원합니다. 이렇게 하면 DASH 및 HLS 패키징뿐만 아니라 미리 보기에서도 콘텐츠 보호가 가능합니다. |
| 라이브 이벤트 RTMPS <!--new row --> |<!-- empty-->| 안정성을 높이고 더 많은 소스 인코더를 지 원하는 RTMPS 지원이 향상 되었습니다. |
| 라이브 이벤트 RTMPS 보안 수집 <!--new row --> | | 라이브 이벤트를 만들 때 4 개의 수집 Url을 얻게 됩니다. 4 개의 수집 Url은 거의 동일 하 고, 동일한 스트리밍 토큰이 있으며 `AppId` , 포트 번호 부분만 다릅니다. URL 중 두 개는 RTMPS에 대한 기본 및 백업용입니다.| 
| 라이브 이벤트 기록 <!--new row --> |<!-- empty--> | Azure 미디어 서비스는 다양 한 프로토콜에서 비디오, 오디오 및 텍스트를 제공 합니다. MPEG-2 또는 HLS/CMAF를 사용 하 여 라이브 스트림을 게시 하면 비디오 및 오디오와 함께 서비스가 IMSC 1.1 호환 TTML에 transcribed 텍스트를 제공 합니다.|
| 라이브 이벤트 대기 모드 <!--new row --> | V2 용 대기 모드가 없습니다. | 독립 모드는 라이브 이벤트의 핫 풀을 관리 하는 데 도움이 되는 새로운 v3 기능입니다. 이제 고객은 실행 상태로 전환 하기 전에 더 낮은 비용으로 라이브 이벤트를 시작할 수 있습니다. 이는 채널 시작 시간을 개선 하 고 더 빠른 시작을 위해 운영 중인 핫 풀의 비용을 줄입니다. |
| 라이브 이벤트 청구 <!--new row --> | <!-- empty-->| 라이브 이벤트 요금은 라이브 채널 측정기를 기준으로 합니다. |
| 라이브 출력 <!--new row --> | 프로그램을 만든 후 시작 해야 합니다. | 라이브 출력은 생성과 동시에 시작되고 삭제되면 중지됩니다. |

## <a name="next-steps"></a>다음 단계

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
