---
title: 미디어 서비스의 혁신 및 작업
titleSuffix: Azure Media Services
description: Azure Media 서비스에서 비디오를 처리하는 규칙을 설명하는 변환을 만드는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: ab99b974aed6f8cd5e1da2ee9b427f593b405889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571231"
---
# <a name="transforms-and-jobs-in-media-services"></a>미디어 서비스의 혁신 및 작업

이 항목에서는 [변환](https://docs.microsoft.com/rest/api/media/transforms) 및 [작업에](https://docs.microsoft.com/rest/api/media/jobs) 대한 세부 정보를 제공하고 이러한 엔터티 간의 관계를 설명합니다.

## <a name="overview"></a>개요

### <a name="transformsjobs-workflow"></a>변환/작업 워크플로우

다음 다이어그램은 변환/작업 워크플로를 보여 주며 다음과 같은 작업을 보여 주며 다음과 같은 작업을 보여 주며 다음과 같은 작업을 보여 주며

![Azure 미디어 서비스의 변환 및 작업 워크플로](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>일반적인 워크플로

1. 변환을 만듭니다.
2. 해당 변환 아래에 작업을 제출합니다.
3. 변환을 나열합니다.
4. 나중에 사용할 계획이 없는 경우 변환을 삭제합니다.

#### <a name="example"></a>예제

모든 동영상의 첫 번째 프레임을 축소판 이미지로 추출하려고 한다고 가정해 보세요.

1. 레시피 또는 동영상 처리 규칙을 정의합니다.
2. 각 비디오에 대해 서비스에 대해 다음과 같은 것을 알려줍니다.
    1. 어디에서 해당 비디오를 찾을 수 있습니다.
    2. 출력 썸네일 이미지를 기록할 위치

**변환**을 사용하면 작성법을 만들고(1단계), 해당 작성법을 사용하여 작업을 제출(2단계)할 수 있습니다.

> [!NOTE]
> **변환** 및 **Datetime** 형식의 작업 속성은 항상 UTC 형식으로 되어 있습니다.

## <a name="transforms"></a>변환

**Transforms**는 비디오 인코딩 또는 분석에 대한 일반적인 작업을 구성하는 데 사용할 수 있습니다. 각 **Transform은** 비디오 또는 오디오 파일을 처리하기 위한 레시피 또는 작업 워크플로를 설명합니다. 한 변환을 여러 규칙에 적용할 수 있습니다. 예를 들어 각 비디오를 지정된 비트 전송률에서 MP4 파일로 인코딩하고 비디오의 첫 번째 프레임으로 썸네일을 생성하도록 변환을 지정할 수 있습니다. 변환에 포함하려는 규칙마다 TransformOutput 항목 하나를 추가합니다. 사전 설정을 사용하여 입력 미디어 파일을 처리하는 방법을 변환에 알릴 수 있습니다.

### <a name="viewing-schema"></a>스키마 보기

Media Services v3에서 사전 설정은 API 자체에서 강력하게 입력된 엔터티입니다. 이러한 개체에 대한 "스키마" 정의는 [개방형 API 사양(또는 Swagger)에서](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)찾을 수 있습니다. [또한 REST API,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)또는 기타 미디어 서비스 v3 SDK 참조 설명서에서 사전 설정 된 정의 (예 : **StandardEncoderPreset)를**볼 수 있습니다.

### <a name="creating-transforms"></a>변환 만들기

REST, CLI 또는 게시된 SDK를 사용하여 변환을 만들 수 있습니다. Media Services v3 API는 Azure Resource Manager에 의해 실행되기 때문에 Resource Manager 템플릿을 사용하여 Media Services 계정에 변환을 만들고 배포할 수 있습니다. 역할 기반 액세스 제어를 사용하여 변환에 대한 액세스를 잠글 수 있습니다.

### <a name="updating-transforms"></a>변환 업데이트

[변환을](https://docs.microsoft.com/rest/api/media/transforms)업데이트해야 하는 경우 **업데이트** 작업을 사용합니다. 설명 또는 기본 TransformOutputs의 우선 순위를 변경하기 위한 것입니다. 진행 중인 모든 작업이 완료되면 이러한 업데이트를 수행하는 것이 좋습니다. 레시피를 다시 작성하려면 새 변환을 만들어야 합니다.

### <a name="transform-object-diagram"></a>개체 다이어그램 변환

다음 다이어그램은 **변형** 객체와 파생 관계를 포함하여 참조하는 객체를 보여 주며, 이 다이어그램은 회색 화살표는 Job 참조 유형과 녹색 화살표가 클래스 파생 관계를 표시하는 형식을 표시합니다.

전체 크기를 보려면 이미지를 선택합니다.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>작업

**작업은** 지정된 입력 비디오 또는 오디오 콘텐츠에 **변환을** 적용하기 위해 미디어 서비스에 대한 실제 요청입니다. 변환을 만든 후에는 Media Services API 또는 게시된 SDK를 사용하여 작업을 제출할 수 있습니다. **Job**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다. HTTPS URL, SAS URL 또는 [자산](https://docs.microsoft.com/rest/api/media/assets)을 사용하여 입력 비디오의 위치를 지정할 수 있습니다.  

### <a name="job-input-from-https"></a>HTTPS에서 작업 입력

콘텐츠가 URL을 통해 이미 액세스할 수 있고 Azure에 원본 파일을 저장할 필요가 없는 경우 [HTTPS에서 작업 입력을](job-input-from-http-how-to.md) 사용합니다(예: S3에서 가져오기). 이 메서드는 Azure Blob 저장소에 콘텐츠가 있지만 파일이 자산에 있을 필요가 없는 경우에도 적합합니다. 현재 이 메서드는 입력에 대한 단일 파일만 지원합니다.

### <a name="asset-as-job-input"></a>작업 입력으로 자산

입력 콘텐츠가 이미 자산에 있거나 콘텐츠가 로컬 파일에 저장된 경우 자산을 [작업 입력으로](job-input-from-local-file-how-to.md) 사용합니다. 스트리밍 또는 다운로드를 위해 입력 자산을 게시하려는 경우(다운로드를 위해 mp4를 게시하고 텍스트 또는 얼굴 감지음성을 수행하려는 경우)도 적합합니다. 이 메서드는 다중 파일 자산(예: 로컬로 인코딩된 MBR 스트리밍 집합)을 지원합니다.

### <a name="checking-job-progress"></a>작업 진행률 확인

Event Grid로 이벤트를 모니터링하여 작업의 진행 상황 및 상태를 가져올 수 있습니다. 자세한 내용은 [Event Grid를 사용하여 이벤트 모니터링](job-state-events-cli-how-to.md)을 참조하세요.

### <a name="updating-jobs"></a>작업 업데이트

[작업](https://docs.microsoft.com/rest/api/media/jobs) 엔터티의 업데이트 작업을 사용하여 작업을 제출한 후 *설명* 및 *우선 순위* 속성을 수정할 수 있습니다. *우선 순위* 속성에 대한 변경은 작업이 아직 큐에 대기 중인 상태에만 유효합니다. 작업 처리가 시작되었거나 완료된 경우 우선 순위를 변경해도 아무 효과가 없습니다.

### <a name="job-object-diagram"></a>작업 개체 다이어그램

다음 다이어그램은 **Job** 개체와 파생 관계를 포함하여 참조하는 객체를 보여 주며 있습니다.

전체 크기로 보려면 이미지를 클릭합니다.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>미디어 예약 단위 구성

미디어 서비스 v3 또는 비디오 인덱서에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 10개의 S3 미디어 예약 단위(MRUs)로 계정을 프로비전하는 것이 좋습니다. 10개가 넘는 S3 MRU가 필요한 경우 [Azure Portal](https://portal.azure.com/)을 사용하여 지원 티켓을 엽니다.

자세한 내용은 [CLI를 사용하여 미디어 처리 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="see-also"></a>참조

* [오류 코드](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)

## <a name="next-steps"></a>다음 단계

- 개발을 시작하기 전에 [미디어 서비스 v3 API 개발을](media-services-apis-overview.md) 검토합니다(API 액세스, 명명 규칙 등에 대한 정보 포함)
- 다음 자습서를 확인하십시오.

    - [자습서: URL을 기반으로 원격 파일을 인코딩하고 비디오를 스트리밍합니다.](stream-files-tutorial-with-rest.md)
    - [튜토리얼: 동영상 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
    - [자습서: 미디어 서비스 v3로 동영상 분석](analyze-videos-tutorial-with-api.md)
