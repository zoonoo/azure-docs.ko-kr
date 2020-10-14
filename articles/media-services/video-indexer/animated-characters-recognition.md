---
title: Video Indexer를 사용한 애니메이션 문자 검색
titleSuffix: Azure Media Services
description: 이 항목에서는 Video Indexer에서 애니메이션 문자 검색을 사용 하는 방법을 보여 줍니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: 9d304767cf28c0e9230c93d5b9e7504b03ed25d4
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018885"
---
# <a name="animated-character-detection-preview"></a>애니메이션 캐릭터 검색(미리 보기)

Azure Media Services Video Indexer는 [Cognitive Services 사용자 지정 비전과](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)의 통합을 통해 애니메이션 내용에서 문자 검색, 그룹화 및 인식을 지원 합니다. 이 기능은 포털 및 API를 통해 사용할 수 있습니다.

특정 애니메이션 모델을 사용 하 여 애니메이션 비디오를 업로드 한 후에는 키 프레임을 추출 하 고, 이러한 프레임에서 애니메이션 문자를 검색 하 고, 비슷한 문자를 그룹화 하 고, 최상의 샘플을 선택 Video Indexer 그런 다음 그룹화 된 문자를 학습 된 모델을 기반으로 문자를 식별 하는 Custom Vision 보냅니다. 

모델에 대 한 학습을 시작 하기 전에 문자가 namelessly 검색 됩니다. 이름을 추가 하 고 모델을 학습 하면 Video Indexer는 문자를 인식 하 고 그에 따라 이름을 적절 하 게 합니다.

## <a name="flow-diagram"></a>흐름 다이어그램

다음 다이어그램에서는 애니메이션 문자 검색 프로세스의 흐름을 보여 줍니다.

![흐름 다이어그램](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>계정

Video Indexer 계정의 유형에 따라 다양 한 기능 집합을 사용할 수 있습니다. Azure에 계정을 연결 하는 방법에 대 한 자세한 내용은 [azure에 연결 된 Video Indexer 계정 만들기](connect-to-azure.md)를 참조 하세요.

* 평가판 계정: Video Indexer 내부 Custom Vision 계정을 사용 하 여 모델을 만들고 Video Indexer 계정에 연결 합니다. 
* 유료 계정: Video Indexer 계정에 Custom Vision 계정을 연결 합니다 (아직 없는 경우 계정을 먼저 만들어야 함).

### <a name="trial-vs-paid"></a>평가판 및 유료

|기능|평가판|유료|
|---|---|---|
|Custom Vision 계정|Video Indexer 하 여 백그라운드에서 관리 됩니다. |Custom Vision 계정이 Video Indexer에 연결 되어 있습니다.|
|애니메이션 모델 수|하나|계정 당 최대 100 모델 (Custom Vision 제한).|
|모델 학습|Video Indexer 새 문자에 대 한 모델을 학습 하 여 기존 문자의 추가 예를 보여 줍니다.|계정 소유자는 변경을 수행할 준비가 되 면 모델을 학습 합니다.|
|Custom Vision 고급 옵션|Custom Vision 포털에 대 한 액세스 권한이 없습니다.|모델은 Custom Vision 포털에서 직접 조정할 수 있습니다.|

## <a name="use-the-animated-character-detection-with-portal"></a>포털에서 애니메이션 문자 검색 사용 

이 섹션에서는 애니메이션 문자 검색 모델 사용을 시작 하기 위해 수행 해야 하는 단계에 대해 설명 합니다. 

평가판 계정에서 Custom Vision 통합은 Video Indexer에서 관리 되므로, 애니메이션 문자 모델 만들기 및 사용을 시작 하 고 다음 섹션 ("Custom Vision 계정 연결")을 건너뛸 수 있습니다.

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Custom Vision 계정 연결 (유료 계정에만 해당)

Video Indexer 유료 계정을 소유 하 고 있는 경우 먼저 Custom Vision 계정을 연결 해야 합니다. Custom Vision 계정이 아직 없는 경우 새로 만드세요. 자세한 내용은 [Custom Vision](../../cognitive-services/custom-vision-service/overview.md)를 참조 하세요.

> [!NOTE]
> 두 계정 모두 동일한 지역에 있어야 합니다. Custom Vision 통합은 현재 일본 지역에서 지원 되지 않습니다.

#### <a name="connect-a-custom-vision-account-with-api"></a>API를 사용 하 여 Custom Vision 계정 연결 

Custom Vision 계정을 Video Indexer에 연결 하거나 현재 Video Indexer에 연결 된 Custom Vision 계정을 변경 하려면 다음 단계를 수행 합니다.

1. [Www.customvision.ai](https://www.customvision.ai) 으로 이동 하 여 로그인 합니다.
1. 다음 키를 복사 합니다. 

    * 학습 키 (학습 리소스의 경우)
    * 예측 키 (예측 리소스의 경우)
    * 엔드포인트 
    * 예측 리소스 ID
    
    > [!NOTE]
    > 모든 키를 제공 하려면 두 개의 개별 Custom Vision 리소스를 제공 해야 합니다. 하나는 학습을 위한 것이 고 다른 하나는 예측 용입니다.
1. [Video Indexer](https://vi.microsoft.com/)로 이동 하 여 로그인 합니다.
1. 페이지의 오른쪽 위 모서리에서 물음표를 클릭 하 고 **API 참조**를 선택 합니다.
1. **제품** 탭을 클릭 하 여 API Management를 구독 하는지 확인 합니다. API가 연결 되어 있으면 다음 단계를 계속 진행할 수 있습니다. 그렇지 않으면를 구독 합니다. 
1. 개발자 포털에서 **전체 API 참조** 를 클릭 하 고 **작업**으로 이동 합니다.  
1. **연결 Custom Vision 계정 (미리 보기)** 을 선택 하 고 **사용해**보기를 클릭 합니다.
1. 액세스 토큰 뿐만 아니라 필수 필드를 입력 하 고 **보내기**를 클릭 합니다. 

    Video Indexer 액세스 토큰을 가져오는 방법에 대 한 자세한 내용은 [개발자 포털로](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)이동 하 여 [관련 설명서](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)를 참조 하세요.  
1. 호출이 200 OK 응답을 반환 하면 계정이 연결 됩니다.
1. [Video Indexer](https://vi.microsoft.com/)) 포털을 검색 하 여 연결을 확인 하려면 다음을 수행 합니다.
1. 오른쪽 위 모서리에서 **콘텐츠 모델 사용자 지정** 단추를 클릭 합니다.
1. **애니메이션 문자** 탭으로 이동 합니다.
1. Custom Vision "* *에서 모델 관리를 클릭 하면 방금 연결한 Custom Vision 계정으로 전송 됩니다.

> [!NOTE]
> 현재 Video Indexer를 통해 생성 된 모델만 지원 됩니다. Custom Vision를 통해 생성 된 모델은 사용할 수 없습니다. 또한 Video Indexer 플랫폼을 통해서만 Video Indexer를 통해 생성 된 모델을 편집 하는 것이 가장 좋습니다. Custom Vision를 통해 변경 하면 의도 하지 않은 결과가 발생할 수 있기 때문입니다.

### <a name="create-an-animated-characters-model"></a>애니메이션 문자 모델 만들기

1. [Video Indexer](https://vi.microsoft.com/) 웹 사이트로 이동하고 로그인합니다.
1. 페이지의 오른쪽 위 모퉁이에 있는 콘텐츠 모델 사용자 지정 단추를 클릭 합니다.

    ![콘텐츠 모델 사용자 지정](./media/animated-characters-recognition/content-model-customization.png)
1. 모델 사용자 지정 섹션에서 **애니메이션 문자** 탭으로 이동 합니다.
1. **모델 추가**를 클릭 합니다.
1. 모델 이름을 입력 하 고 enter 키를 클릭 하 여 이름을 저장 합니다.

> [!NOTE]
> 가장 좋은 방법은 애니메이션 효과를 주는 각 계열에 대해 하나의 사용자 지정 비전 모델을 유지 하는 것입니다. 

### <a name="index-a-video-with-an-animated-model"></a>애니메이션 모델을 사용 하 여 비디오 인덱싱

1. 상단 메뉴에서 **업로드** 단추를 클릭 합니다.
1. 업로드할 비디오 (파일 또는 URL에서)를 선택 합니다.
1. **고급 옵션**을 클릭 합니다.
1. **사람/애니메이션 문자** 아래에서 **애니메이션 모델**을 선택 합니다.
1. 모델 하나가 있는 경우 자동으로 선택 되 고 여러 모델이 있는 경우 드롭다운 메뉴에서 관련 항목을 선택할 수 있습니다.
1. 업로드를 클릭 합니다.
1. 비디오가 인덱싱되 면 **정보** 창의 **애니메이션 문자** 섹션에 검색 된 문자가 표시 됩니다.

> [!NOTE] 
> 모델에 태그를 지정 하 고 학습 하기 전에 모든 애니메이션 문자 이름이 "알 수 없는 #X"로 지정 됩니다. 모델을 학습 한 후에도 인식 됩니다.

### <a name="customize-the-animated-characters-models"></a>애니메이션 문자 모델 사용자 지정

1. 모델에 태그를 만들고 학습 합니다.

    1. 해당 이름을 편집 하 여 검색 된 문자에 태그를 합니다. 모델에 문자를 학습 한 후에는 해당 모델을 사용 하 여 인덱싱된 다음 비디오가 인식 됩니다. 
    1. 비디오에서 애니메이션 문자를 태그 하려면 **Insights** 탭으로 이동 하 고 창의 오른쪽 위 모퉁이에 있는 **편집** 단추를 클릭 합니다.
    1. **정보** 창에서 검색 된 애니메이션 문자 중 하나를 클릭 하 고 "알 수 없는 #X" (또는 이전에 문자에 할당 된 이름)에서 이름을 변경 합니다.
    1. 새 이름을 입력한 후 새 이름 옆에 있는 확인 아이콘을 클릭합니다. 그러면 새 이름이 Video Indexer 모델에 저장 됩니다.
    1. 원하는 모든 이름 편집을 마친 후 모델을 학습 해야 합니다.

        사용자 지정 페이지를 열고 **애니메이션 문자** 탭을 클릭 한 다음 **학습** 단추를 클릭 하 여 모델을 학습 합니다.
         
        유료 계정이 있는 경우 아래와 같이 **고객 비전에서 모델 관리** 링크를 클릭할 수 있습니다. 그런 다음 **Custom Vision**의 모델 페이지로 전달 됩니다.
 
        ![콘텐츠 모델 사용자 지정](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. 학습 된 후에는 해당 모델을 사용 하 여 인덱싱하거나 인덱싱해야는 학습 된 문자를 인식 합니다. 
    Custom Vision 계정에 대 한 액세스 권한이 있는 유료 계정은 모델 및 태그가 지정 된 이미지를 볼 수 있습니다. [Custom Vision에서 분류자를 개선 하](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)는 방법에 대해 자세히 알아보세요.

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