---
title: 인코딩 마이그레이션 지침
description: 이 문서에서는 Azure Media Services v2에서 v3으로 마이그레이션하는 데 도움이 되는 인코딩 시나리오 기반 지침을 제공합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: a01571f4a1f852deb84b7f20d61b8048e8000790
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490100"
---
# <a name="encoding-scenario-based-migration-guidance"></a>인코딩 시나리오 기반 마이그레이션 지침

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2단계](./media/migration-guide/steps-4.svg)

이 문서에서는 Azure Media Services v2에서 v3으로 마이그레이션하는 데 도움이 되는 인코딩 시나리오 기반 지침을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

인코딩 워크플로를 변경하기 전에 스토리지 관리 방식의 차이점을 이해하는 것이 좋습니다.  AMS V3에서 Azure Storage API는 Media Services 계정과 연결된 스토리지 계정을 관리하는 데 사용됩니다.

> [!NOTE]
> v2에서 만든 작업(job)과 작업(task)은 변환과 연결되지 않으므로 v3에서 나타나지 않습니다. v3 변환 및 작업으로 전환하는 것이 좋습니다.

## <a name="encoding-workflow-comparison"></a>인코딩 워크플로 비교

잠시 시간을 내서 V2 및 V3에 대한 인코딩 워크플로의 시각적 비교를 위해 아래 순서도를 살펴보세요.

### <a name="v2-encoding-workflow"></a>V2 인코딩 워크플로

더 큰 버전을 보려면 아래 이미지를 클릭합니다.

[![V2의 인코딩 워크플로](./media/migration-guide/V2-pretty.svg) ](./media/migration-guide/V2-pretty.svg#lightbox)

1. 설정
    1. 자산을 만들거나 기존 자산을 사용합니다. 새 자산을 사용하는 경우 해당 자산에 콘텐츠를 업로드합니다. 기존 자산을 사용하는 경우 자산에 이미 있는 파일을 인코딩해야 합니다.
    2. 다음 항목의 값을 가져옵니다.
        - 미디어 프로세서 ID 또는 개체
        - 사용하려는 인코더의 인코더 문자열(이름)
        - 새 자산의 자산 ID 또는 기존 자산의 자산 ID
    3. 모니터링을 위해 작업(job)이나 작업(task) 수준 알림 구독 또는 SDK 이벤트 처리기를 만듭니다.
2. 작업(job)이나 작업(task)이 포함된 작업(job)을 만듭니다. 각 작업에는 위 항목과 다음 항목이 포함되어야 합니다.
    - 출력 자산을 만들어야 하는 지시문.  출력 자산은 시스템에서 생성됩니다.
    - 출력 자산의 선택적 이름
3. 작업을 제출합니다.
4. 작업을 모니터링합니다.

### <a name="v3-encoding-workflow"></a>V3 인코딩 워크플로

[![V3의 인코딩 워크플로](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. 설정
    1. 자산을 만들거나 기존 자산을 사용합니다. 새 자산을 사용하는 경우 해당 자산에 콘텐츠를 업로드합니다. 기존 자산을 사용하는 경우 자산에 이미 있는 파일을 인코딩해야 합니다. ‘해당 자산에 추가 콘텐츠를 업로드하면 안 됩니다’.
    1. 출력 자산을 만듭니다.  출력 자산은 인코딩된 파일과 입력 및 출력 메타데이터가 저장되는 위치입니다.
    1. 변환을 위해 값을 가져옵니다.
        - 표준 인코더 사전 설정
        - AMS 리소스 그룹
        - AMS 계정 이름
    1. 변환을 만들거나 기존 변환을 사용합니다.  변환은 다시 사용할 수 있습니다. 작업을 제출할 때마다 새 변환을 만들 필요가 없습니다.
1. 작업 만들기
    1. 작업의 경우 다음 항목의 값을 가져옵니다.
        - 변환 이름
        - 자산에 대한 SAS URL의 기본 URI, 파일 공유의 HTTPs 원본 경로 또는 파일의 로컬 경로. `JobInputAsset`은 자산 이름을 입력으로 사용할 수도 있습니다.
        - 파일 이름
        - 출력 자산
        - 리소스 그룹
        - AMS 계정 이름  
1. 작업 모니터링에 [Event Grid](monitoring/monitor-events-portal-how-to.md) 사용합니다.
1. 작업을 제출합니다.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>V2에서 V3으로 인코딩의 사용자 지정 사전 설정

V2 코드가 사용자 지정 사전 설정을 사용하여 표준 인코더를 호출한 경우 작업을 제출하기 전에 먼저 사용자 지정 표준 인코더 사전 설정을 사용하여 새 변환을 만들어야 합니다.

사용자 지정 사전 설정은 이제 JSON이며 더 이상 XML을 기반으로 하지 않습니다. [변환 오픈 API(Swagger)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) 설명서에 정의된 대로 사용자 지정 사전 설정 스키마에 따라 JSON으로 사전 설정을 다시 만듭니다.

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>인코딩 작업의 입력 및 출력 메타데이터 파일

v2에서 XML 입력 및 출력 메타데이터 파일은 인코딩 작업의 결과로 생성됩니다. v3에서 메타데이터 형식이 XML에서 JSON으로 변경되었습니다. 메타데이터에 관한 자세한 내용은 [입력 메타데이터](input-metadata-schema.md) 및 [출력 메타데이터](output-metadata-schema.md)를 참조하세요.

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>v3 표준 인코더 또는 파트너 기반 솔루션에 대한 프리미엄 인코더

v2 API는 더 이상 프리미엄 인코더를 지원하지 않습니다. 이전에 HEVC 인코딩에 워크플로 기반 프리미엄 인코더를 사용한 경우 HEVC 인코딩 지원을 통해 새 v3 [표준 인코더](encode-media-encoder-standard-formats-reference.md)로 마이그레이션해야 합니다.

프리미엄 인코더의 고급 워크플로 기능이 필요한 경우 [Imagine Communications](https://imaginecommunications.com), [Telestream](https://www.telestream.net) 또는 [Bitmovin](https://bitmovin.com)에서 제공하는 Azure 고급 인코딩 파트너 솔루션의 사용을 시작하는 것이 좋습니다.

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>HTTPS 호스트된 URL에 있는 입력을 사용하는 작업

이제 [HTTP(S) 작업 입력 지원](job-input-from-http-how-to.md)을 사용하여 Azure Storage에 저장된 파일, 로컬로 저장된 파일 또는 외부 웹 서버에서 V3의 작업을 제출할 수 있습니다.

이전에 워크플로를 사용하여 작업을 제출하기 전에 Azure Blob 파일에서 빈 자산으로 파일을 복사한 경우 Azure Blob Storage의 파일에 대한 SAS URL을 작업에 직접 전달하여 워크플로를 간소화할 수 있습니다.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>새 AudioAnalyzer “기본 모드”에 대한 Indexer v1 오디오 대화 내용 기록

v2 API에서 Indexer v1 프로세서를 사용하는 고객의 경우 작업을 제출하기 전에 [기본 모드](transform-create-basic-audio-how-to.md)에서 새 `AudioAnalyzer`를 호출하는 변환을 만들어야 합니다.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>인코딩, 변환 및 작업 개념, 자습서, 방법 가이드

### <a name="concepts"></a>개념

- [Media Services를 사용하여 비디오 및 오디오 인코딩](encode-concept.md)
- [표준 인코더 형식 및 코덱](encode-media-encoder-standard-formats-reference.md)
- [자동 생성된 비트 전송률 사다리로 인코딩](encode-autogen-bitrate-ladder.md)
- [콘텐츠 인식 인코딩 사전 설정을 사용하여 지정된 해상도에 대한 최적의 비트 전송률 값 찾기](encode-content-aware-concept.md)
- [미디어 예약 단위](concept-media-reserved-units.md)
- [입력 메타데이터](input-metadata-schema.md)
- [출력 메타데이터](output-metadata-schema.md)
- [Media Services v3의 동적 패키징: 오디오 코덱](encode-dynamic-packaging-concept.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>자습서

- [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - .NET](stream-files-dotnet-quickstart.md)
- [자습서: Media Services v3를 사용하여 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>방법 가이드

- [HTTPS URL에서 작업 입력 만들기](job-input-from-http-how-to.md)
- [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)
- [기본 오디오 변환 만들기](transform-create-basic-audio-how-to.md)
- .NET 사용
  - [사용자 지정 변환을 사용하여 인코딩하는 방법 - .NET](transform-custom-presets-how-to.md)
  - [Media Encoder Standard를 사용하여 오버레이를 만드는 방법](transform-create-overlay-how-to.md)
  - [.NET과 함께 Encoder Standard를 사용하여 썸네일을 생성하는 방법](transform-generate-thumbnails-dotnet-how-to.md)
- Azure CLI 사용
  - [사용자 지정 변환을 사용하여 인코딩하는 방법 - Azure CLI](transform-custom-preset-cli-how-to.md)
- REST 사용
  - [사용자 지정 변환을 사용하여 인코딩하는 방법 - REST](transform-custom-preset-rest-how-to.md)
  - [REST와 함께 Encoder Standard를 사용하여 썸네일을 생성하는 방법](transform-generate-thumbnails-rest-how-to.md)
- [Media Services로 인코딩할 때 비디오 서브클리핑 - .NET](transform-subclip-video-dotnet-how-to.md)
- [Media Services로 인코딩할 때 비디오 서브클리핑 - REST](transform-subclip-video-rest-how-to.md)

## <a name="samples"></a>샘플

[코드 샘플에서 V2와 V3 코드를 비교](migrate-v-2-v-3-migration-samples.md)할 수도 있습니다.
