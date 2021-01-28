---
title: 인코딩 마이그레이션 지침
description: 이 문서에서는 Azure Media Services v 2에서 v3로 마이그레이션하는 데 도움이 되는 인코딩 시나리오 기반 지침을 제공 합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: ab819239572fd99fdf5ff3bf23f81eb3cdff3b9a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940102"
---
# <a name="encoding-scenario-based-migration-guidance"></a>인코딩 시나리오 기반 마이그레이션 지침

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2 단계](./media/migration-guide/steps-4.svg)

이 문서에서는 Azure Media Services v 2에서 v3로 마이그레이션하는 데 도움이 되는 인코딩 시나리오 기반 지침을 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

인코딩 워크플로를 변경 하려면 먼저 저장소를 관리 하는 방식에 대 한 차이점을 이해 하는 것이 좋습니다.  AMS V3에서 Azure Storage API는 Media Services 계정과 연결 된 저장소 계정을 관리 하는 데 사용 됩니다.

> [!NOTE]
> V 2에서 만든 작업 및 작업은 변환과 연결 되지 않으므로 v3에 표시 되지 않습니다. V3 변환 및 작업으로 전환 하는 것이 좋습니다.

## <a name="encoding-workflow-comparison"></a>인코딩 워크플로 비교

아래 순서도에서 V2 및 v 3에 대 한 인코딩 워크플로를 확인 하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="v2-encoding-workflow"></a>V2 encoding 워크플로

더 큰 버전을 보려면 아래 이미지를 클릭 하세요.

[![V2 ](./media/migration-guide/V2-pretty.svg) 용 Encoding 워크플로](./media/migration-guide/V2-pretty.svg#lightbox)

1. 설정
    1. 자산을 만들거나 및 기존 자산을 사용 합니다. 새 자산을 사용 하는 경우 해당 자산에 콘텐츠를 업로드 합니다. 기존 자산을 사용 하는 경우 자산에 이미 있는 파일을 인코딩 해야 합니다.
    2. 다음 항목의 값을 가져옵니다.
        - 미디어 프로세서 ID 또는 개체
        - 사용 하려는 인코더의 인코더 문자열 (이름)
        - 새 자산의 자산 ID 또는 기존 자산의 자산 ID
    3. 모니터링을 위해 작업 또는 태스크 수준 알림 구독 또는 SDK 이벤트 처리기를 만듭니다.
2. 태스크가 포함 된 작업을 만듭니다. 각 작업에는 위의 항목과 다음 항목이 포함 되어야 합니다.
    - 출력 자산을 만들어야 하는 지시문입니다.  시스템에서 출력 자산을 만듭니다.
    - 출력 자산의 선택적 이름입니다.
3. 작업을 제출합니다.
4. 작업을 모니터링 합니다.

### <a name="v3-encoding-workflow"></a>V3 인코딩 워크플로

[![V3 ](./media/migration-guide/V3-pretty.svg) 용 Encoding 워크플로](./media/migration-guide/V3-pretty.svg#lightbox)

1. 설정
    1. 자산을 만들거나 및 기존 자산을 사용 합니다. 새 자산을 사용 하는 경우 해당 자산에 콘텐츠를 업로드 합니다. 기존 자산을 사용 하는 경우 자산에 이미 있는 파일을 인코딩 해야 합니다. *해당 자산에 더 이상 콘텐츠를 업로드 해서는 안 됩니다.*
    1. 출력 자산을 만듭니다.  출력 자산은 인코딩된 파일 및 입력 및 출력 메타 데이터를 저장 하는 위치입니다.
    1. 변환 값을 가져옵니다.
        - 표준 인코더 사전 설정
        - AMS 리소스 그룹
        - AMS 계정 이름
    1. 변환을 만들거나 기존 변환을 사용 합니다.  변환은 다시 사용할 수 있습니다. 작업을 제출 하려면 매번 새 변환을 만들 필요가 없습니다.
1. 작업 만들기
    1. 작업의 경우 다음 항목에 대 한 값을 가져옵니다.
        - 변환 이름
        - 기본-자산의 SAS URL에 대 한 기본 URI, 파일 공유의 HTTPs 원본 경로 또는 파일의 로컬 경로입니다. 는 `JobInputAsset` 자산 이름을 입력으로 사용할 수도 있습니다.
        - 파일 이름
        - 출력 자산
        - 리소스 그룹
        - AMS 계정 이름  
1. 작업 모니터링에 [Event Grid](monitor-events-portal-how-to.md) 를 사용 합니다.
1. 작업을 제출합니다.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>V2에서 V3 인코딩으로 사용자 지정 사전 설정

V2 코드가 사용자 지정 사전 설정으로 표준 인코더를 호출한 경우 먼저 작업을 제출 하기 전에 사용자 지정 표준 인코더 사전 설정을 사용 하 여 새 변환을 만들어야 합니다.

사용자 지정 사전 설정은 이제 JSON 이며 더 이상 XML을 기반으로 하지 않습니다. [Swagger (Transform OPEN API)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) 설명서에 정의 된 대로 사용자 지정 미리 설정 된 스키마에 따라 JSON에 미리 설정을 다시 만듭니다.


<!-- removed because this is covered in the tutorials
Common custom [encoding](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json) scenarios:
        1. Create a custom Single Bitrate MP4 encode
        1. Create a custom [Adaptive Bitrate Encoding Ladder](autogen-bitrate-ladder.md)
        1. Creating Sprite Thumbnails
        1. Creating Thumbnails (see below for your preferred method)
        1. [Sub Clipping](subclip-video-rest-howto.md)
        1. Cropping
-->

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>인코딩 작업에서 입력 및 출력 메타 데이터 파일

V2에서 XML 입력 및 출력 메타 데이터 파일은 인코딩 작업의 결과로 생성 됩니다. V3에서 메타 데이터 형식이 XML에서 JSON으로 변경 되었습니다. 메타 데이터에 대 한 자세한 내용은 [입력 메타 데이터](input-metadata-schema.md) 및 [출력 메타 데이터](output-metadata-schema.md)를 참조 하세요.

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>V3 표준 인코더 또는 파트너 기반 솔루션에 대 한 프리미엄 인코더

V2 API는 더 이상 프리미엄 인코더를 지원 하지 않습니다. 이전에 HEVC 인코딩에 워크플로 기반 프리미엄 인코더를 사용한 경우 HEVC encoding을 지 원하는 새 v3 [표준 인코더](media-encoder-standard-formats.md) 로 마이그레이션해야 합니다.

프리미엄 인코더의 고급 워크플로 기능을 사용 해야 하는 경우 Azure advanced encoding 파트너 솔루션을 사용 하 여 [통신](https://imaginecommunications.com), [Telestream](https://www.telestream.net)또는 [bitmovin](https://bitmovin.com)사용 하는 것이 좋습니다.

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>HTTPS 호스트 Url에 있는 입력을 포함 하는 작업

이제 Azure storage에 저장 된 파일, 로컬로 저장 된 파일 또는 [HTTP (S) 작업 입력 지원을](job-input-from-http-how-to.md)사용 하는 외부 웹 서버에서 V3의 작업을 제출할 수 있습니다.

이전에 워크플로를 사용 하 여 작업을 제출 하기 전에 Azure blob 파일에서 빈 자산으로 파일을 복사 하는 경우 Azure blob storage의 파일에 대 한 SAS URL을 작업에 직접 전달 하 여 워크플로를 단순화할 수 있습니다.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>새 오디오 분석기 "기본 모드"에 대 한 인덱서 v1 오디오 기록

V2 API에서 인덱서 v1 프로세서를 사용 하는 고객의 경우 `AudioAnalyzer` 작업을 제출 하기 전에 [기본 모드](how-to-create-basic-audio-transform.md) 에서 새를 호출 하는 변환을 만들어야 합니다.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>인코딩, 변환 및 작업 개념, 자습서 및 방법 가이드

### <a name="concepts"></a>개념

- [Media Services를 사용 하 여 비디오 및 오디오 인코딩](encoding-concept.md)
- [표준 인코더 형식 및 코덱](media-encoder-standard-formats.md)
- [자동 생성 된 비트 전송률 사다리를 사용 하 여 인코딩](autogen-bitrate-ladder.md)
- [콘텐츠 인식 인코딩 미리 설정을 사용 하 여 지정 된 해상도에 대 한 최적의 비트 전송률 값을 찾습니다.](content-aware-encoding.md)
- [미디어 예약 단위](concept-media-reserved-units.md)
- [입력 메타데이터](input-metadata-schema.md)
- [출력 메타데이터](output-metadata-schema.md)
- [Media Services v3의 동적 패키징: 오디오 코덱](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>자습서

- [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - .NET](stream-files-dotnet-quickstart.md)
- [자습서: Media Services v3를 사용하여 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>방법 가이드

- [HTTPS URL에서 작업 입력 만들기](job-input-from-http-how-to.md)
- [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)
- [기본 오디오 변환 만들기](how-to-create-basic-audio-transform.md)
- .NET 사용
  - [사용자 지정 변환-.NET을 사용 하 여 인코딩하는 방법](customize-encoder-presets-how-to.md)
  - [Media Encoder Standard를 사용 하 여 오버레이를 만드는 방법](how-to-create-overlay.md)
  - [.NET에서 인코더 표준을 사용 하 여 미리 보기를 생성 하는 방법](media-services-generate-thumbnails-dotnet.md)
- Azure CLI 사용
  - [사용자 지정 변환-Azure CLI를 사용 하 여 인코딩하는 방법](custom-preset-cli-howto.md)
- REST 사용
  - [사용자 지정 변환을 사용 하 여 인코딩하는 방법-REST](custom-preset-rest-howto.md)
  - [REST를 사용 하 여 인코더 표준을 사용 하 여 미리 보기를 생성 하는 방법](media-services-generate-thumbnails-rest.md)
- [Media Services-.NET을 사용 하 여 인코딩할 경우 비디오 하위 클립](subclip-video-dotnet-howto.md)
- [Media Services를 사용 하 여 인코딩할 때 비디오를 하위 클립-REST](subclip-video-rest-howto.md)

## <a name="samples"></a>샘플

[코드 샘플에서 V2 및 V3 코드도 비교할](migrate-v-2-v-3-migration-samples.md)수 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
