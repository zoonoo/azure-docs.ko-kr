---
title: Azure Media Services의 Transform 및 Job | Microsoft Docs
description: Media Services를 사용할 때 비디오를 처리하기 위한 규칙이나 사양을 설명하는 변환을 만들어야 합니다. 이 문서에서는 Transform의 개념과 사용법에 대해 간략히 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: e13afe26d06f5b5b2dcf7eddf00f9ee481312b2c
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024261"
---
# <a name="transforms-and-jobs"></a>Transform 및 Job
 
Azure Media Services v3에서는 비디오를 인코딩 및/또는 분석하는 데 사용할 작성법과 관련된 템플릿 워크플로 리소스인 [변환](https://docs.microsoft.com/rest/api/media/transforms)이 도입되었습니다. **변환**은 비디오 인코딩 또는 분석을 위한 일반적인 작업을 구성하는 데 사용할 수 있습니다. 각 **변환**은 비디오 또는 오디오 파일을 처리하는 작업의 작성법 또는 워크플로를 설명합니다. 

**작업**은 **변환**을 특정 입력 비디오 또는 오디오 콘텐츠에 적용하기 위한 Azure Media Services에 대한 실제 요청입니다. **작업**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다. 비디오의 위치는 HTTP, URL, SAS URL 또는 [Media Service 자산](https://docs.microsoft.com/rest/api/media/assets)을 사용하여 지정할 수 있습니다.  

## <a name="typical-workflow"></a>일반적인 워크플로

1. Transform 만들기 
2. Transform에 따라 Job 제출 
3. Transform 나열 
4. 나중에 이 "변환"을 사용할 계획이 아니면 삭제하세요. 

모든 비디오의 첫 번째 프레임을 썸네일 이미지로 추출하려는 경우 수행할 단계는 다음과 같습니다. 

1. 작성법 또는 비디오 처리 규칙을 정의합니다(예: "비디오의 첫 번째 프레임을 썸네일로 사용"). 
2. 각 비디오와 관련하여 서비스에 다음 정보를 제공합니다. 
    1. 해당 비디오를 찾을 위치  
    2. 출력 썸네일 이미지를 기록할 위치 

**변환**을 사용하면 작성법을 만들고(1단계), 해당 작성법을 사용하여 작업을 제출(2단계)할 수 있습니다.

## <a name="transforms"></a>변환

v3 API를 직접 사용하여 또는 게시된 SDK 중 하나를 사용하여 Media Services 계정에서 변환을 만들 수 있습니다. Media Services v3 API는 Azure Resource Manager에 의해 실행되기 때문에 Resource Manager 템플릿을 사용하여 Media Services 계정에 변환을 만들고 배포할 수 있습니다. 역할 기반 액세스 제어를 사용하여 변환에 대한 액세스를 잠글 수 있습니다.

### <a name="transform-definition"></a>변환 정의

다음 표는 변환의 속성을 보여주고 해당 정의를 제공합니다.

|이름|설명|
|---|---|
|Id|리소스에 대한 정규화된 리소스 ID입니다.|
|이름|리소스의 이름입니다.|
|properties.created |변환이 생성될 때 UTC 날짜 및 시간은 'YYYY-MM-DDThh:mm:ssZ' 형식입니다.|
|properties.description |변환에 대한 자세한 정보 표시 설명입니다(선택 사항).|
|properties.lastModified |변환이 마지막으로 업데이트될 때 UTC 날짜 및 시간은 'YYYY-MM-DDThh:mm:ssZ' 형식입니다.|
|properties.outputs |변환이 생성해야 하는 하나 이상의 TransformOutputs의 배열입니다.|
|형식|리소스 형식입니다.|

전체 정의는 [변환](https://docs.microsoft.com/rest/api/media/transforms)을 참조합니다.

위에서 설명한 것처럼, 변환을 통해 비디오를 처리하기 위한 작성법 또는 규칙을 만들 수 있습니다. 한 변환을 여러 규칙에 적용할 수 있습니다. 예를 들어 각 비디오를 지정된 비트 전송률에서 MP4 파일로 인코딩하고 비디오의 첫 번째 프레임으로 썸네일을 생성하도록 변환을 지정할 수 있습니다. 변환에 포함하려는 규칙마다 TransformOutput 항목 하나를 추가합니다.

> [!NOTE]
> 변환 정의에서 업데이트 작업을 지원하지만, 진행 중인 모든 작업이 완료된 후 수정해야 합니다. 일반적으로 설명을 변경하기 위해 기존 변환을 업데이트하거나, 기본 TransformOutputs의 우선 순위를 수정합니다. 작성법을 다시 쓰려면 새 변환을 만들어야 합니다.

## <a name="jobs"></a>교육

변환을 만든 후에는 Media Services API 또는 게시된 SDK를 사용하여 작업을 제출할 수 있습니다. Event Grid로 이벤트를 모니터링하여 작업의 진행 상황 및 상태를 가져올 수 있습니다. 자세한 내용은 [Event Grid를 사용하여 이벤트 모니터링](job-state-events-cli-how-to.md )을 참조하세요.

### <a name="jobs-definition"></a>작업 정의

다음 표는 작업의 속성을 보여주고 정의를 제공합니다.

|이름|설명|
|---|---|
|id|리소스에 대한 정규화된 리소스 ID입니다.|
|이름   |리소스의 이름입니다.|
|properties.correlationData |고객이 제공하는 상관 관계 데이터(변경 불가능)로, 작업 및 JobOutput 변경 알림의 일부로 반환됩니다. 자세한 내용은 [이벤트 스키마](media-services-event-schemas.md)를 참조하세요.<br/><br/>이 속성은 Media Services의 다중 테넌트 사용량에도 사용할 수 있습니다. 테넌트 ID를 작업에 넣을 수 있습니다. |
|properties.created |작업이 생성된 UTC 날짜 및 시간이며, 'YYYY-MM-DDThh:mm:ssZ' 형식입니다.|
|properties.description |고객이 선택 사항으로 제공하는 작업 설명입니다.|
|properties.input|작업에 대한 입력입니다.|
|properties.lastModified    |작업이 마지막으로 업데이트된 UTC 날짜 및 시간이며, 'YYYY-MM-DDThh:mm:ssZ' 형식입니다.|
|properties.outputs|작업에 대한 출력입니다.|
|properties.priority    |작업을 처리해야 하는 우선 순위입니다. 우선 순위가 높은 작업은 우선 순위가 낮은 작업보다 먼저 처리됩니다. 설정하지 않은 경우 기본값은 보통입니다.|
|properties.state   |작업의 현재 상태입니다.|
|형식   |리소스 형식입니다.|

전체 정의는 [작업](https://docs.microsoft.com/rest/api/media/jobs)을 참조합니다.

> [!NOTE]
> 작업 정의에서 업데이트 작업을 지원하지만, 작업이 제출된 후 수정할 수 있는 유일한 속성은 설명 및 우선 순위입니다. 또한 우선 순위 변경은 작업이 아직 큐에 대기 중인 상태에만 유효합니다. 작업 처리가 시작되었거나 완료된 경우 우선 순위를 변경해도 아무 효과가 없습니다.

### <a name="pagination"></a>페이지 매김

작업 페이지 매김은 Media Services v3에서 지원됩니다.

> [!TIP]
> 항상 다음 링크를 사용하여 컬렉션을 열거하고, 특정 페이지 크기에 따라 달라지지 않아야 합니다.

쿼리 응답에 많은 항목이 포함된 경우 서비스에서 "\@odata.nextLink" 속성을 반환하여 결과의 다음 페이지를 가져옵니다. 전체 결과 집합을 통해 페이지에 사용할 수 있습니다. 페이지 크기는 구성할 수 없습니다. 

컬렉션을 통해 페이징하는 동안 작업이 생성되거나 삭제되면 변경 내용이 반환된 결과에 반영됩니다(해당 변경 내용이 다운로드되지 않은 컬렉션의 일부인 경우). 

다음 C# 예제에서는 계정의 모든 작업을 통해 열거하는 방법을 보여줍니다.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

REST 예제는 [작업 - 목록](https://docs.microsoft.com/rest/api/media/jobs/list)을 참조하세요.


## <a name="next-steps"></a>다음 단계

[비디오 파일 스트리밍](stream-files-dotnet-quickstart.md)
