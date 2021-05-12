---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>필수 필드. 자세한 내용은 aka.ms/skyeye/meta를 참조하세요.
제목: Media Services 변환 및 작업: Azure Media Services 설명: 변환은 Azure Media Services에서 비디오를 처리하는 규칙을 설명합니다.
서비스: media-services 작성자: IngridAtMicrosoft 관리자: femila ms.service: media-services ms.topic: conceptual ms.topic: 2021년 3월 22일 ms.author: inhenkel
---

# <a name="transforms-and-jobs-in-media-services"></a>Media Services 변환 및 작업

이 토픽에서는 [변환](/rest/api/media/transforms) 및 [작업](/rest/api/media/jobs)에 관한 세부 정보를 제공하고 해당 엔터티 간의 관계를 설명합니다.

## <a name="overview"></a>개요

### <a name="transformsjobs-workflow"></a>변환/작업 워크플로

다음 다이어그램은 변환/작업 워크플로를 보여 줍니다.

![Azure Media Services의 변환 및 작업 워크플로](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow&quot;></a>일반적인 워크플로

1. 변환 만들기.
2. 변환에서 Job 제출.
3. 변환 나열.
4. 나중에 변환을 사용할 계획이 아니면 삭제하세요.

#### <a name=&quot;example&quot;></a>예제

모든 비디오의 첫 번째 프레임을 썸네일 이미지로 추출하려는 경우 수행할 단계는 다음과 같습니다.

1. 작성법 또는 비디오 처리 규칙을 정의합니다(예: &quot;비디오의 첫 번째 프레임을 썸네일로 사용").
2. 각 비디오와 관련하여 서비스에 다음 정보를 제공합니다.
    1. 해당 비디오를 찾을 위치.
    2. 출력 썸네일 이미지를 기록할 위치

**변환** 을 사용하면 작성법을 만들고(1단계), 해당 작성법을 사용하여 작업을 제출(2단계)할 수 있습니다.

> [!NOTE]
> 날짜/시간 형식의 **변환** 및 **작업** 속성의 우선 순위는 언제나 UTC 형식입니다.

## <a name="transforms"></a>변형

**Transforms** 는 비디오 인코딩 또는 분석에 대한 일반적인 작업을 구성하는 데 사용할 수 있습니다. 각 **변환** 은 비디오 또는 오디오 파일을 처리하는 작업의 작성법 또는 워크플로를 설명합니다. 한 변환을 여러 규칙에 적용할 수 있습니다. 예를 들어 각 비디오를 지정된 비트 전송률에서 MP4 파일로 인코딩하고 비디오의 첫 번째 프레임으로 썸네일을 생성하도록 변환을 지정할 수 있습니다. 변환에 포함하려는 규칙마다 TransformOutput 항목 하나를 추가합니다. 사전 설정을 사용하여 입력 미디어 파일의 처리 방법을 변환에 알려줍니다.

### <a name="viewing-schema"></a>스키마 보기

Media Services v3에서 사전 설정은 API 자체에 있는 강력한 형식의 엔터티입니다. [Open API 사양(또는 Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)에서 이런 개체에 관한 “스키마” 정의를 찾아볼 수 있습니다. 또한 [REST API](/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset)(또는 다른 Media Services v3 SDK 참조 설명서)에서 사전 설정 정의(예: **StandardEncoderPreset**)를 확인할 수 있습니다.

### <a name="creating-transforms"></a>변환 만들기

REST, CLI 또는 게시된 SDK를 사용하여 변환을 만들 수 있습니다. Media Services v3 API는 Azure Resource Manager에 의해 실행되기 때문에 Resource Manager 템플릿을 사용하여 Media Services 계정에 변환을 만들고 배포할 수 있습니다. Azure 역할 기반 액세스 제어를 사용하여 변환에 대한 액세스를 잠글 수 있습니다.

### <a name="updating-transforms"></a>변환 업데이트

[변환](/rest/api/media/transforms)을 업데이트해야 하는 경우 **업데이트** 작업을 사용합니다. 이는 기반 TransformOutputs의 설명 또는 우선 순위를 변경하기 위한 것입니다. 해당 업데이트는 모든 진행 중인 작업이 완료되었을 때 수행하는 것이 좋습니다. 레시피를 다시 작성하려면 새 변환을 만들어야 합니다.

### <a name="transform-object-diagram"></a>변환 개체 다이어그램

다음 다이어그램에서는 **변환** 개체와 해당 개체가 참조하는 개체(파생 관계 포함)를 보여 줍니다. 회색 화살표에는 작업에서 참조하는 형식을 표시하고, 녹색 화살표는 클래스 파생 관계를 표시합니다.

이미지를 선택하여 전체 크기를 확인합니다.  

[![개체 간의 클래스 파생 관계를 포함하여 변환 개체와 해당 개체가 참조하는 개체를 보여 주는 다이어그램.](./media/api-diagrams/transform-small.png)](./media/api-diagrams/transform-large.png#lightbox)

## <a name="jobs"></a>작업

**작업** 은 지정된 입력 비디오 또는 오디오 콘텐츠에 **변환** 을 적용하도록 요구하는 Media Services에 대한 실제 요청입니다. 변환을 만든 후에는 Media Services API 또는 게시된 SDK를 사용하여 작업을 제출할 수 있습니다. **Job** 은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다. 입력 비디오의 위치는 HTTP URL, SAS URL 또는 [자산](/rest/api/media/assets)을 사용하여 지정할 수 있습니다.  

### <a name="job-input-from-https"></a>HTTPS의 작업 입력

이미 URL을 통해 콘텐츠를 액세스할 수 있고 원본 파일을 Azure에 저장하지 않아도 되는 경우(예: S3에서 가져오기) [HTTPS의 작업 입력](job-input-from-http-how-to.md)을 사용합니다. 이 방법은 Azure Blob 스토리지에 콘텐츠가 있지만, 파일이 자산에 포함될 필요가 없는 경우에도 적합합니다. 현재이 메서드는 입력 시 단일 파일만 지원합니다.

### <a name="asset-as-job-input"></a>작업 입력으로서의 자산

입력 콘텐츠가 이미 자산에 있거나 콘텐츠가 로컬 파일에 저장된 경우 [자산을 작업 입력으로](job-input-from-local-file-how-to.md) 사용합니다. 스트리밍 또는 다운로드를 위해 입력 자산을 게시하려는 경우(예: 다운로드를 위해 mp4를 게시하고 음성 텍스트 변환하거나 얼굴 감지도 수행하려는 경우)에도 좋은 옵션입니다. 이 메서드는 다중 파일 자산(예: 로컬로 인코딩된 MBR 스트리밍 집합)을 지원합니다.

### <a name="checking-job-progress"></a>작업 진행 상황 확인

Event Grid로 이벤트를 모니터링하여 작업의 진행 상황 및 상태를 가져올 수 있습니다. 자세한 내용은 [Event Grid를 사용하여 이벤트 모니터링](monitoring/job-state-events-cli-how-to.md)을 참조하세요.

### <a name="updating-jobs"></a>작업 업데이트

작업을 제출한 후 [작업](/rest/api/media/jobs) 엔터티를 위한 업데이트 작업을 사용하여 *설명* 및 *우선 순위* 속성을 수정할 수 있습니다. *우선 순위* 속성에 대한 변경은 작업이 아직 큐에 대기 중인 상태에만 유효합니다. 작업 처리가 시작되었거나 완료된 경우 우선 순위를 변경해도 아무 효과가 없습니다.

### <a name="job-object-diagram"></a>작업 개체 다이어그램

다음 다이어그램에서는 **작업** 개체와 해당 개체가 참조하는 개체(파생 관계 포함)를 보여 줍니다.

전체 크기로 보려면 이미지를 클릭합니다.  

[![개체 간의 클래스 파생 관계를 포함하여 작업 개체와 해당 개체가 참조하는 개체를 보여 주는 다이어그램.](./media/api-diagrams/job-small.png)](./media/api-diagrams/job-large.png#lightbox)

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="see-also"></a>참고 항목

* [오류 코드](/rest/api/media/jobs/get#joberrorcode)
* [Media Services 엔터티 필터링, 순서 지정, 페이징](filter-order-page-entitites-how-to.md)

## <a name="next-steps"></a>다음 단계

- 개발을 시작하기 전에 [Media Services v3 API로 개발](media-services-apis-overview.md)(API 액세스, 명명 규칙 등에 관한 정보 포함)을 검토하세요.
- 다음 자습서를 확인하세요.

    - [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림](stream-files-tutorial-with-rest.md)
    - [자습서: 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
    - [자습서: Media Services v3으로 비디오 분석](analyze-videos-tutorial.md)
