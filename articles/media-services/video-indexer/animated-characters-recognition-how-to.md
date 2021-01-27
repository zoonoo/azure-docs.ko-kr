---
title: Video Indexer 하는 방법에 대 한 애니메이션 문자 검색
titleSuffix: Azure Media Services
description: 이 방법에서는 Video Indexer에서 애니메이션 문자 검색을 사용 하는 방법을 보여 줍니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: 29dbdb896d1a6063fec277afa33327c84cb2f0cd
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880930"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>포털 및 API와 함께 애니메이션 문자 검색 (미리 보기) 사용 

Azure Media Services Video Indexer는 애니메이션 된 콘텐츠에서 문자 검색, 그룹화 및 인식을 지원 합니다 .이 기능은 Azure Portal 및 API를 통해 사용할 수 있습니다. [이 개요](animated-characters-recognition.md) 항목을 검토 하세요.

이 문서에서는 Azure Portal 및 Video Indexer API와 함께 애니메이션 문자 검색을 사용 하는 방법을 보여 줍니다.

## <a name="use-the-animated-character-detection-with-portal"></a>포털에서 애니메이션 문자 검색 사용 

평가판 계정에서 Custom Vision 통합은 Video Indexer에 의해 관리 되며, 애니메이션 문자 모델 만들기 및 사용을 시작할 수 있습니다. 평가판 계정을 사용 하는 경우 다음을 건너뛸 수 있습니다 ("연결 Custom Vision 계정").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Custom Vision 계정 연결 (유료 계정에만 해당)

Video Indexer 유료 계정을 소유 하 고 있는 경우 먼저 Custom Vision 계정을 연결 해야 합니다. Custom Vision 계정이 아직 없는 경우 새로 만드세요. 자세한 내용은 [Custom Vision](../../cognitive-services/custom-vision-service/overview.md)를 참조 하세요.

> [!NOTE]
> 두 계정 모두 동일한 지역에 있어야 합니다. Custom Vision 통합은 현재 일본 지역에서 지원 되지 않습니다.

Custom Vision 계정에 대 한 액세스 권한이 있는 유료 계정은 모델 및 태그가 지정 된 이미지를 볼 수 있습니다.  [Custom Vision에서 분류자를 개선 하](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)는 방법에 대해 자세히 알아보세요. 

모델의 교육은 Custom Vision 웹 사이트를 통해가 아니라 Video Indexer을 통해서만 수행 되어야 합니다. 

#### <a name="connect-a-custom-vision-account-with-api"></a>API를 사용 하 여 Custom Vision 계정 연결 

Custom Vision 계정을 Video Indexer에 연결 하거나 현재 Video Indexer에 연결 된 Custom Vision 계정을 변경 하려면 다음 단계를 수행 합니다.

1. [Www.customvision.ai](https://www.customvision.ai) 으로 이동 하 여 로그인 합니다.
1. 학습 및 예측 리소스에 대 한 키를 복사 합니다.

    > [!NOTE]
    > 모든 키를 제공 하려면 두 개의 개별 Custom Vision 리소스를 제공 해야 합니다. 하나는 학습을 위한 것이 고 다른 하나는 예측 용입니다.
1. 다른 정보를 제공 합니다.

    * 엔드포인트 
    * 예측 리소스 ID
1. [Video Indexer](https://vi.microsoft.com/)로 이동 하 여 로그인 합니다.
1. 페이지의 오른쪽 위 모서리에서 물음표를 클릭 하 고 **API 참조** 를 선택 합니다.
1. **제품** 탭을 클릭 하 여 API Management를 구독 하는지 확인 합니다. API가 연결 되어 있으면 다음 단계를 계속 진행할 수 있습니다. 그렇지 않으면를 구독 합니다. 
1. 개발자 포털에서 **전체 API 참조** 를 클릭 하 고 **작업** 으로 이동 합니다.  
1. **연결 Custom Vision 계정 (미리 보기)** 을 선택 하 고 **사용해** 보기를 클릭 합니다.
1. 액세스 토큰 뿐만 아니라 필수 필드를 입력 하 고 **보내기** 를 클릭 합니다. 

    Video Indexer 액세스 토큰을 가져오는 방법에 대 한 자세한 내용은 [개발자 포털로](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)이동 하 여 [관련 설명서](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)를 참조 하세요.  
1. 호출이 200 OK 응답을 반환 하면 계정이 연결 됩니다.
1. [Video Indexer](https://vi.microsoft.com/)) 포털을 검색 하 여 연결을 확인 하려면 다음을 수행 합니다.
1. 오른쪽 위 모서리에서 **콘텐츠 모델 사용자 지정** 단추를 클릭 합니다.
1. **애니메이션 문자** 탭으로 이동 합니다.
1. Custom Vision에서 모델 관리를 클릭 하면 방금 연결한 Custom Vision 계정으로 전송 됩니다.

> [!NOTE]
> 현재 Video Indexer를 통해 생성 된 모델만 지원 됩니다. Custom Vision를 통해 생성 된 모델은 사용할 수 없습니다. 또한 Video Indexer 플랫폼을 통해서만 Video Indexer를 통해 생성 된 모델을 편집 하는 것이 가장 좋습니다. Custom Vision를 통해 변경 하면 의도 하지 않은 결과가 발생할 수 있기 때문입니다.

### <a name="create-an-animated-characters-model"></a>애니메이션 문자 모델 만들기

1. [Video Indexer](https://vi.microsoft.com/) 웹 사이트로 이동하고 로그인합니다.
1. 계정에서 모델을 사용자 지정 하려면 페이지 왼쪽에 있는 **콘텐츠 모델 사용자 지정** 단추를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Video Indexer에서 콘텐츠 모델 사용자 지정":::
1. 모델 사용자 지정 섹션에서 **애니메이션 문자** 탭으로 이동 합니다.
1. **모델 추가** 를 클릭 합니다.
1. 모델 이름을 입력 하 고 enter 키를 클릭 하 여 이름을 저장 합니다.

> [!NOTE]
> 가장 좋은 방법은 애니메이션 효과를 주는 각 계열에 대해 하나의 사용자 지정 비전 모델을 유지 하는 것입니다. 

### <a name="index-a-video-with-an-animated-model"></a>애니메이션 모델을 사용 하 여 비디오 인덱싱

초기 교육을 위해 두 개 이상의 비디오를 업로드 합니다. 정상적인 인식 모델이 예상 되기 전에는 각각 15 분 보다 길 수 있습니다. 에피소드가 짧으면 학습 전에 최소한 30 분 이상의 비디오 콘텐츠를 업로드 하는 것이 좋습니다. 이렇게 하면 서로 다른 장면 및 배경의 동일한 문자에 속한 그룹을 병합할 수 있으므로 인덱스를 만든 다음 에피소드에서 문자를 검색할 가능성이 높아집니다. 여러 비디오 (에피소드)에서 모델을 학습 하려면 동일한 애니메이션 모델을 사용 하 여 모델을 모두 인덱싱합니다. 

1. **업로드** 단추를 클릭 합니다.
1. 업로드할 비디오 (파일 또는 URL에서)를 선택 합니다.
1. **고급 옵션** 을 클릭 합니다.
1. **사람/애니메이션 문자** 아래에서 **애니메이션 모델** 을 선택 합니다.
1. 모델 하나가 있는 경우 자동으로 선택 되 고 여러 모델이 있는 경우 드롭다운 메뉴에서 관련 항목을 선택할 수 있습니다.
1. 업로드를 클릭 합니다.
1. 비디오가 인덱싱되 면 **정보** 창의 **애니메이션 문자** 섹션에 검색 된 문자가 표시 됩니다.

모델에 태그를 지정 하 고 학습 하기 전에 모든 애니메이션 문자 이름이 "알 수 없는 #X"로 지정 됩니다. 모델을 학습 한 후에도 인식 됩니다.

### <a name="customize-the-animated-characters-models"></a>애니메이션 문자 모델 사용자 지정

1. Video Indexer의 문자 이름을로 합니다.

    1. 모델에서 문자 그룹을 만든 후 Custom Vision에서 이러한 그룹을 검토 하는 것이 좋습니다. 
    1. 비디오에서 애니메이션 문자를 태그 하려면 Insights 탭으로 이동 하 ****   고 ****   창의 오른쪽 위 모퉁이에 있는 편집 단추를 클릭 합니다. 
    1.  **정보**   창에서 검색 된 애니메이션 문자 중 하나를 클릭 하 고 이름을 "Unknown #X"에서 임시 이름 또는 이전에 문자에 할당 된 이름으로 변경 합니다. 
    1. 새 이름을 입력한 후 새 이름 옆에 있는 확인 아이콘을 클릭합니다. 그러면 새 이름이 Video Indexer 모델에 저장 됩니다. 
1. 유료 계정만: Custom Vision에서 그룹을 검토 합니다. 

    > [!NOTE]
    > Custom Vision 계정에 대 한 액세스 권한이 있는 유료 계정은 모델 및 태그가 지정 된 이미지를 볼 수 있습니다.  [Custom Vision에서 분류자를 개선 하](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)는 방법에 대해 자세히 알아보세요. 모델에 대 한 교육은 Custom Vision 웹 사이트를 통해가 아니라 Video Indexer (이 topid에 설명 된 대로)를 통해서만 수행 되어야 합니다. 

    1. Video Indexer의 **사용자 지정 모델** 페이지로 이동 하 여 [ **애니메이션 문자** ] 탭을 선택 합니다. 
    1. 작업 중인 모델에 대 한 편집 단추를 클릭 하 여 Custom Vision에서 관리 합니다. 
    1. 각 문자 그룹을 검토 합니다. 

        * 그룹이 관련 되지 않은 이미지를 포함 하는 경우 Custom Vision 웹 사이트에서 해당 이미지를 삭제 하는 것이 좋습니다. 
        * 다른 문자에 속하는 이미지가 있는 경우 이미지를 클릭 하 고 오른쪽 태그를 추가 하 고 잘못 된 태그를 삭제 하 여 이러한 특정 이미지의 태그를 변경 합니다. 
        * 그룹이 잘못 된 경우 (예를 들어, 주로 문자가 아닌 이미지 또는 여러 문자를 포함 하는 이미지를 포함 하는 경우) Custom Vision 웹 사이트 또는 Video Indexer insights에서을 (를) 삭제할 수 있습니다. 
        * 그룹화 알고리즘에서 문자를 다른 그룹으로 분할 하는 경우도 있습니다. 따라서 같은 문자에 속한 모든 그룹을 동일한 Video Indexer 이름으로 지정 하는 것이 좋습니다 .이 경우에는이 모든 그룹이 Custom Vision 웹 사이트에서 설정 된 것으로 즉시 표시 됩니다. 
    1. 그룹을 구체화 한 후에는 태그가 지정 된 초기 이름이 그룹의 문자를 반영 하는지 확인 합니다. 
1. 모델 학습 

    1. 원하는 모든 이름 편집을 마친 후 모델을 학습 해야 합니다. 
    1. 모델에 문자를 학습 한 후에는 해당 모델을 사용 하 여 인덱싱된 다음 비디오가 인식 됩니다. 
    1. 사용자 지정 페이지를 열고 **애니메이션 문자** 탭을 클릭 한   다음 **학습** 단추를 클릭 하 여 모델을 학습 합니다. 비디오 간 연결을 유지 하기 위해 
    
인덱서 및 모델에서는 Custom Vision 웹 사이트 (유료 계정에 Custom Vision 웹 사이트에 대 한 액세스 권한이 있습니다)에서 모델을 학습 하지 않고 Video Indexer 에서만 학습 합니다. 학습 된 후에는 해당 모델을 사용 하 여 인덱싱하거나 인덱싱해야는 학습 된 문자를 인식 합니다. 

## <a name="delete-an-animated-character-and-the-model"></a>애니메이션 문자 및 모델 삭제

1. 애니메이션 문자를 삭제 합니다.

    1. Video insights에서 애니메이션 문자를 삭제 하려면 **insights** 탭으로 이동 하 고 창의 오른쪽 위 모퉁이에 있는 **편집** 단추를 클릭 합니다.
    1. 애니메이션 문자를 선택한 다음 이름 아래에서 **삭제** 단추를 클릭 합니다.

    > [!NOTE]
    > 이렇게 하면이 비디오에서 정보를 삭제 하지만 모델에는 영향을 주지 않습니다.
1. 모델을 삭제 합니다.

    1. 상단 메뉴에서 **콘텐츠 모델 사용자 지정** 단추를 클릭 하 고 [ **애니메이션 문자** ] 탭으로 이동 합니다.
    1. 삭제 하려는 모델의 오른쪽에 있는 줄임표 아이콘을 클릭 한 다음 삭제 단추를 클릭 합니다.
    
    * 유료 계정: Video Indexer에서 모델의 연결이 끊어지고 다시 연결할 수 없게 됩니다.
    * 평가판 계정:이 모델은 세관 비전과 함께 삭제 됩니다. 
    
        > [!NOTE]
        > 평가판 계정에는 사용할 수 있는 모델은 하나 뿐입니다. 삭제 한 후에는 다른 모델을 학습 시킬 수 없습니다.

## <a name="use-the-animated-character-detection-with-api"></a>API에서 애니메이션 문자 검색 사용 

1. Custom Vision 계정을 연결 합니다.

    Video Indexer 유료 계정을 소유 하 고 있는 경우 먼저 Custom Vision 계정을 연결 해야 합니다. <br/>
    Custom Vision 계정이 아직 없는 경우 새로 만드세요. 자세한 내용은 [Custom Vision](../../cognitive-services/custom-vision-service/overview.md)를 참조 하세요.

    [API를 사용 하 여 Custom Vision 계정에 연결](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)합니다.
1. 애니메이션 문자 모델을 만듭니다.

    [애니메이션 모델 만들기](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) API를 사용 합니다.
1. 비디오를 인덱싱하고 인덱스를 다시 만듭니다.

    [다시 인덱싱](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) API를 사용 합니다. 
1. 애니메이션 문자 모델을 사용자 지정 합니다.

    [애니메이션 학습 모델](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) API를 사용 합니다.

### <a name="view-the-output"></a>출력 보기

생성 된 JSON 파일에서 애니메이션 문자를 확인 합니다.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>제한 사항

* 현재 "애니메이션 식별" 기능은 East-Asia 지역에서 지원 되지 않습니다.
* 비디오에서 작거나 멀리 보이는 문자는 비디오 품질이 나쁜 경우 제대로 식별 되지 않을 수 있습니다.
* 애니메이션 효과가 적용 되는 일련의 문자 집합에 따라 모델을 사용 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[Video Indexer 개요](video-indexer-overview.md)