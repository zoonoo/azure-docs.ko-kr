---
title: Media Services v3 샘플
description: 이 문서에는 메서드 및 SDK별로 구성된 Media Services v3에 사용할 수 있는 모든 샘플의 목록이 포함되어 있습니다.  샘플에는 .NET, Node.JS, Python, Java 외에도 Postman을 사용하는 REST가 포함됩니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: cd82da805538abcddf2f76a20c28cb0a74b5c89b
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109685606"
---
# <a name="media-services-v3-samples"></a>Media Services v3 샘플

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에는 메서드 및 SDK별로 구성된 Media Services에 사용할 수 있는 모든 샘플의 목록이 포함되어 있습니다.  샘플에는 .NET, Node.JS, Python, Java 외에도 Postman을 사용하는 REST가 포함됩니다.

## <a name="rest-postman-collection"></a>REST Postman Collection

[REST Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman) 샘플에는 Postman 클라이언트로 가져올 수 있는 Postman 환경 및 컬렉션이 포함되어 있습니다.

## <a name="samples-by-sdk"></a>SDK별 샘플

각 탭에서 찾고 있는 샘플에 대한 설명과 링크를 확인할 수 있습니다.

## <a name="net"></a>[.NET](#tab/net/)

| 폴더 | 설명 |
|-------------|-------------|
| [VideoEncoding/EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_PredefinedPreset)|기본 제공 사전 설정 및 HTTP URL 입력을 사용하여 작업을 제출하고, 스트리밍을 위해 출력 자산을 게시하고, 확인을 위해 결과를 다운로드하는 방법입니다.|
| [VideoEncoding/EncodingWithMESCustomPreset_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264)|사용자 지정 H.264 인코딩 사전 설정 및 HTTP URL 입력을 사용하여 작업을 제출하고, 스트리밍을 위해 출력 자산을 게시하고, 확인을 위해 결과를 다운로드하는 방법입니다.|
| [VideoEncoding/EncodingWithMESCustomPreset_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC)|사용자 지정 HEVC 인코딩 사전 설정 및 HTTP URL 입력을 사용하여 작업을 제출하고, 스트리밍을 위해 출력 자산을 게시하고, 확인을 위해 결과를 다운로드하는 방법입니다.|
| [VideoEncoding/EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_StitchTwoAssets)|JobInputSequence를 사용하여 작업을 제출하여 시작 또는 종료 시간에 따라 잘릴 수 있는 둘 이상의 자산을 연결하는 방법입니다. 인코딩된 결과 파일은 모든 자산이 함께 연결된 단일 비디오입니다.  이 샘플은 확인을 위해 스트리밍 및 다운로드 결과에 대한 출력 자산도 게시합니다.|
| [VideoEncoding/EncodingWithMESCustomPresetAndSprite](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_SpriteThumbnail)|미리 보기 스프라이트와 HTTP URL 입력이 포함된 사용자 지정 사전 설정을 사용하여 작업을 제출하고, 스트리밍을 위해 출력 자산을 게시하고, 확인을 위해 결과를 다운로드하는 방법입니다.|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|최대 25시간의 전체 보관을 사용하여 LiveEvent를 만들고 5분 DVR 기간을 사용하여 자산에 대한 필터를 만드는 방법입니다. 필터를 사용하여 스트리밍을 위한 로케이터를 만드는 방법입니다.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|비디오 분석기 변환을 만들고, 비디오 파일을 입력 자산에 업로드하고, 변환이 있는 작업을 제출하고, 확인을 위해 결과를 다운로드하는 방법입니다.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|오디오 분석기 변환을 만들고, 미디어 파일을 입력 자산에 업로드하고, 변환이 있는 작업을 제출하고, 확인을 위해 결과를 다운로드하는 방법입니다.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|기본 제공 AdaptiveStreaming 사전 설정을 사용하여 변환을 만들고, 작업을 제출하고, 비밀 키를 사용하여 ContentKeyPolicy를 만들고, ContentKeyPolicy를 StreamingLocator와 연결하고, 토큰을 가져오고, Azure Media Player에서 재생할 URL을 출력하는 방법입니다. 플레이어에서 스트림을 요청하면 Media Services는 지정된 키를 사용하여 AES-128을 사용하여 콘텐츠를 동적으로 암호화하고, Azure Media Player는 토큰을 사용하여 암호 해독합니다.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|기본 제공 AdaptiveStreaming 사전 설정을 사용하여 변환을 만들고, 작업을 제출하고, 비밀 키를 사용하여 Widevine 구성이 포함된 ContentKeyPolicy를 만들고, ContentKeyPolicy를 StreamingLocator와 연결하고, 토큰을 가져오고, Widevine Player에서 재생할 URL을 출력하는 방법입니다. 사용자가 Widevine으로 보호되는 콘텐츠를 요청하면 플레이어 애플리케이션에서 Media Services 라이선스 서비스로부터 라이선스를 요청합니다. 플레이어 애플리케이션에 권한이 있으면 Media Services 라이선스 서비스에서 플레이어에 라이선스를 발급합니다. Widevine 라이선스에는 클라이언트 플레이어에서 콘텐츠를 해독하고 스트림하는 데 사용할 수 있는 암호 해독 키가 포함되어 있습니다.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|기본 제공 AdaptiveStreaming 사전 설정을 사용하여 변환을 만들고, 작업을 제출하고, 비밀 키를 사용하여 PlayReady 구성이 포함된 ContentKeyPolicy를 만들고, ContentKeyPolicy를 StreamingLocator와 연결하고, 토큰을 가져오고, Azure Media Player에서 재생할 URL을 출력하는 방법입니다. 사용자가 PlayReady로 보호되는 콘텐츠를 요청하면 플레이어 애플리케이션에서 Media Services 라이선스 서비스로부터 라이선스를 요청합니다. 플레이어 애플리케이션에 권한이 있으면 Media Services 라이선스 서비스에서 플레이어에 라이선스를 발급합니다. PlayReady 라이선스에는 클라이언트 플레이어가 콘텐츠를 암호 해독하고 스트리밍하는 데 사용할 수 있는 암호 해독 키가 포함되어 있습니다.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|PlayReady 및 Widevine DRM을 사용하여 콘텐츠를 동적으로 암호화하고, 라이선스 서비스에서 라이선스를 요청하지 않고 콘텐츠를 재생하는 방법입니다. 기본 제공 AdaptiveStreaming 사전 설정을 사용하여 변환을 만들고, 작업을 제출하고, 개방 제한 및 PlayReady/Widevine 영구 구성을 사용하여 ContentKeyPolicy를 만들고, ContentKeyPolicy를 StreamingLocator와 연결하고, 재생할 URL을 출력하는 방법을 보여 줍니다.|
| [Streaming/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|기본 제공 AdaptiveStreaming 사전 설정을 사용하여 변환을 만들고, 작업을 제출하고, 자산 필터 및 계정 필터를 만들고, 필터를 스트리밍 로케이터와 연결하고, 재생할 URL을 출력하는 방법입니다.|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|기본 제공 AdaptiveStreaming 사전 설정을 사용하여 변환을 만들고, 작업을 제출하고, HLS 및 DASH 스트리밍에 대한 출력 자산을 게시하는 방법입니다.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | 주문형 인코딩 또는 분석을 사용하는 프로덕션 시스템에 대한 지침 및 모범 사례입니다. 읽기 권한자는 [Media Services 및 VOD를 통한 고가용성](./architecture-high-availability-encoding-concept.md) 관련 문서를 시작해야 합니다. [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/HighAvailabilityEncodingStreaming/README.md) 샘플에 제공되는 별도의 솔루션 파일이 있습니다. |

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|폴더|설명|
|---|---|
|[HelloWorld-ListAssets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/HelloWorld-ListAssets) |자산을 연결하고 나열하는 방법입니다. |
|[라이브](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)| 기본 라이브 스트리밍을 수행하는 방법입니다. **경고** 라이브를 사용하는 경우 모든 리소스가 정리되고 포털에서 더 이상 청구되지 않는지 확인해야 합니다.|
|[StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample)| 원본 URL에서 로컬 파일 또는 인코딩을 업로드하는 방법, 스토리지 SDK를 사용하여 콘텐츠를 다운로드하는 방법 및 플레이어로 스트림하는 방법입니다. |
|[StreamFilesWithDRMSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample)| Widevine 및 PlayReady DRM을 사용하여 인코딩하고 스트림하는 방법입니다. |
|[VideoIndexerSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample)| 비디오 및 오디오 분석기 사전 설정을 사용하여 비디오 또는 오디오 파일에서 메타데이터와 인사이트를 생성하는 방법입니다. |

## <a name="python"></a>[Python](#tab/python)

현재 [Python을 사용한 기본 인코딩](https://github.com/Azure-Samples/media-services-v3-python)이라는 하나의 Python 샘플이 있습니다.

## <a name="java"></a>[Java](#tab/java)

|폴더|설명|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|미디어 파일에서 오디오를 분석하는 방법입니다. |
|Content Protection|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|AES-128을 사용하여 콘텐츠를 동적으로 암호화하는 방법입니다.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|PlayReady DRM을 사용하여 콘텐츠를 동적으로 암호화하는 방법입니다.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Widevine DRM을 사용하여 콘텐츠를 동적으로 암호화하는 방법입니다.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|FairPlay DRM을 사용하여 콘텐츠를 동적으로 암호화하고, 라이선스 서비스에서 라이선스를 요청하지 않고 콘텐츠를 재생하는 방법입니다.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|PlayReady 및 Widevine DRM을 사용하여 콘텐츠를 동적으로 암호화하고, 라이선스 서비스에서 라이선스를 요청하지 않고 콘텐츠를 재생하는 방법입니다.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|자산 및 계정 필터를 사용하여 콘텐츠를 필터링하는 방법입니다.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|스트리밍을 위해 VOD 콘텐츠를 HLS/DASH로 동적으로 패키지하는 방법입니다.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|최대 25시간의 전체 보관을 사용하여 LiveEvent를 만들고 5분 DVR 기간을 사용하여 자산에 대한 필터를 만드는 방법 및 스트리밍을 위한 로케이터를 만들고 필터를 사용하는 방법입니다.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|최대 25시간의 전체 보관을 사용하여 LiveEvent를 만들고 5분 DVR 기간을 사용하여 자산에 대한 필터를 만드는 방법 및 스트리밍을 위한 로케이터를 만들고 필터를 사용하는 방법입니다.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|StandardEncoderPreset 설정을 사용하여 사용자 지정 인코딩 변환을 만드는 방법입니다.|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|기본 제공 사전 설정 및 HTTP URL 입력을 사용하여 작업을 제출하고, 스트리밍을 위해 출력 자산을 게시하고, 확인을 위해 결과를 다운로드하는 방법입니다.|

---
