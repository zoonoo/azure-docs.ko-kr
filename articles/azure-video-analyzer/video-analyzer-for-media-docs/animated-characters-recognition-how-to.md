---
title: Azure Video Analyzer for Media(이전의 Video Indexer)를 사용하여 애니메이션 문자 검색 방법
titleSuffix: Azure Video Analyzer
description: 이 방법은 Azure Video Analyzer for Media(이전의 Video Indexer)에서 애니메이션 문자 검색을 사용하는 방법을 보여 줍니다.
services: azure-video-analyzer
author: Juliako
manager: femila
ms.custom: references_regions
ms.topic: how-to
ms.subservice: azure-video-analyzer-media
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: a9807ac57130034b51c3188b56de32ade4db6844
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121581"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>포털 및 API에서 애니메이션 캐릭터 검색 사용(미리 보기) 

Azure Video Analyzer for Media(이전의 Video Indexer)는 애니메이션 콘텐츠에서 캐릭터를 검색, 그룹화 및 인식하는 기능을 지원하며, 이 기능은 Azure Portal 및 API를 통해 사용할 수 있습니다. [이 개요](animated-characters-recognition.md) 항목을 검토하세요.

이 문서에서는 Azure Portal 및 Video Analyzer for Media API에서 애니메이션 캐릭터 검색을 사용하는 방법을 보여 줍니다.

## <a name="use-the-animated-character-detection-with-portal"></a>포털에서 애니메이션 캐릭터 검색 사용 

Custom Vision 통합이 Video Analyzer for Media에 의해 관리되는 평가판 계정에서 애니메이션 캐릭터 모델 만들기 및 사용을 시작할 수 있습니다. 평가판 계정을 사용하는 경우 다음(“Custom Vision 계정 연결”) 섹션을 건너뛸 수 있습니다.

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Custom Vision 계정 연결(유료 계정에만 해당)

Video Analyzer for Media 유료 계정을 소유하고 있는 경우 먼저 Custom Vision 계정을 연결해야 합니다. Custom Vision 계정이 아직 없는 경우 새로 만드세요. 자세한 내용은 [Custom Vision](../../cognitive-services/custom-vision-service/overview.md)을 참조하세요.

> [!NOTE]
> 두 계정이 모두 동일한 지역에 있어야 합니다. Custom Vision 통합은 현재 일본 지역에서 지원되지 않습니다.

Custom Vision 계정에 대한 액세스 권한이 있는 유료 계정은 모델 및 태그가 지정된 이미지를 볼 수 있습니다.  [Custom Vision에서 분류자를 개선하는 방법](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)에 관해 자세히 알아보세요. 

모델 학습은 Video Analyzer for Media를 통해서만 수행되어야 하며 Custom Vision 웹 사이트를 통해서는 수행되지 않습니다. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Custom Vision 계정을 API와 연결 

다음 단계에 따라 Custom Vision 계정을 Video Analyzer for Media에 연결하거나 현재 Video Analyzer for Media에 연결된 Custom Vision 계정을 변경합니다.

1. [www.customvision.ai](https://www.customvision.ai)로 이동하고 로그인합니다.
1. 학습 및 예측 리소스의 키를 복사합니다.

    > [!NOTE]
    > 모든 키를 제공하려면 학습 및 예측용으로 하나씩 두 개의 개별 리소스가 Custom Vision에 있어야 합니다.
1. 다른 정보를 제공합니다.

    * 엔드포인트 
    * 예측 리소스 ID
1. [Video Analyzer for Media](https://vi.microsoft.com/)를 찾아서 로그인합니다.
1. 페이지의 오른쪽 위에서 물음표를 클릭하고 **API 참조** 를 선택합니다.
1. **제품** 탭을 클릭하여 API Management를 구독하는지 확인합니다. API가 연결되어 있으면 다음 단계로 계속 진행하고 그러지 않으면 구독으로 이동합니다. 
1. 개발자 포털에서 **전체 API 참조** 를 클릭하고 **작업** 으로 이동합니다.  
1. **Custom Vision 계정 연결(미리 보기)** 을 선택하고 **체험해 보기** 를 클릭합니다.
1. 액세스 토큰 및 필수 필드를 입력하고 **보내기** 를 클릭합니다. 

    Video Indexer 액세스 토큰을 가져오는 방법에 관한 자세한 내용은 [개발자 포털](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token)로 이동하고 [관련 설명서](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)를 참조하세요.  
1. 호출이 200 OK 응답을 반환하면 계정이 연결된 것입니다.
1. [Video Analyzer for Media](https://vi.microsoft.com/) 포털로 이동하여 연결을 확인하려면:
1. 오른쪽 위에서 **콘텐츠 모델 사용자 지정** 단추를 클릭합니다.
1. **애니메이션 캐릭터** 탭으로 이동합니다.
1. Custom Vision에서 모델 관리를 클릭하면 방금 연결한 Custom Vision 계정으로 전송됩니다.

> [!NOTE]
> 현재는 Video Analyzer for Media를 통해 생성된 모델만 지원됩니다. Custom Vision을 통해 생성된 모델은 사용할 수 없습니다. 또한 Custom Vision을 통해 변경하면 의도하지 않은 결과가 발생할 수 있기 때문에 Video Analyzer for Media 플랫폼을 통해서만 Video Analyzer for Media를 사용하여 만든 모델을 편집하는 것이 가장 좋습니다.

### <a name="create-an-animated-characters-model"></a>애니메이션 캐릭터 모델 만들기

1. [Video Analyzer for Media](https://vi.microsoft.com/) 웹 사이트로 이동하여 로그인합니다.
1. 계정에서 모델을 사용자 지정하려면 페이지 왼쪽에서 **콘텐츠 모델 사용자 지정** 단추를 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Video Analyzer for Media에서 콘텐츠 모델 사용자 지정":::
1. 모델 사용자 지정 섹션에서 **애니메이션 캐릭터** 탭으로 이동합니다.
1. **모델 추가** 를 클릭합니다.
1. 모델 이름을 지정하고 Enter 키를 클릭하여 이름을 저장합니다.

> [!NOTE]
> 각 애니메이션 시리즈에 하나의 Custom Vision 모델을 포함하는 것이 좋습니다. 

### <a name="index-a-video-with-an-animated-model"></a>애니메이션 모델을 사용하여 비디오 인덱싱

초기 학습을 위해 두 개 이상의 비디오를 업로드합니다. 좋은 인식 모델을 예상하려면 먼저 각 비디오가 15분보다 긴 것이 좋습니다. 더 짧은 에피소드가 있는 경우에는 학습 전에 최소한 30분의 비디오 콘텐츠를 업로드하는 것이 좋습니다. 이렇게 하면 서로 다른 장면과 배경에서 동일한 캐릭터에 속한 그룹을 병합할 수 있으므로 인덱싱하는 다음 에피소드에서 캐릭터를 검색할 가능성이 높아집니다. 여러 비디오(에피소드)에서 모델을 학습시키려면 모두 동일한 애니메이션 모델을 사용하여 비디오를 인덱싱해야 합니다. 

1. **업로드** 단추를 클릭합니다.
1. 업로드할 비디오를 선택합니다(파일 또는 URL에서).
1. **고급 옵션** 을 클릭합니다.
1. **사람/애니메이션 캐릭터** 에서 **애니메이션 모델** 을 선택합니다.
1. 하나의 모델이 있으면 자동으로 선택되고 여러 모델이 있으면 드롭다운 메뉴에서 관련 모델을 선택할 수 있습니다.
1. 업로드를 클릭합니다.
1. 비디오가 인덱싱되면 **인사이트** 창의 **애니메이션 캐릭터** 섹션에 검색된 캐릭터가 표시됩니다.

모델에 태그를 지정하고 모델을 학습시키기 전에 모든 애니메이션 캐릭터 이름은 “Unknown #X”로 지정됩니다. 모델을 학습시킨 후 캐릭터도 인식됩니다.

### <a name="customize-the-animated-characters-models"></a>애니메이션 캐릭터 모델 사용자 지정

1. Video Analyzer for Media에서 문자의 이름을 지정합니다.

    1. 모델이 캐릭터 그룹을 만든 후 Custom Vision에서 해당 그룹을 검토하는 것이 좋습니다. 
    1. 비디오에서 애니메이션 캐릭터에 태그를 지정하려면  **인사이트** 탭으로 이동하고 창의 오른쪽 위에 있는  **편집** 단추를 클릭합니다. 
    1.  **인사이트** 창에서 검색된 애니메이션 캐릭터 중 하나를 클릭하고 이름을 “Unknown #X”에서 임시 이름(또는 이전에 캐릭터에 할당된 이름)으로 변경합니다. 
    1. 새 이름을 입력한 후 새 이름 옆에 있는 확인 아이콘을 클릭합니다. Video Analyzer for Media의 모델에 새 이름이 저장됩니다. 
1. 유료 계정에만 해당: Custom Vision에서 그룹을 검토합니다. 

    > [!NOTE]
    > Custom Vision 계정에 대한 액세스 권한이 있는 유료 계정은 모델 및 태그가 지정된 이미지를 볼 수 있습니다.  [Custom Vision에서 분류자를 개선하는 방법](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)에 관해 자세히 알아보세요. 모델 학습은 이 항목에 설명된 대로 Custom Vision 웹 사이트가 아니라 Video Analyzer for Media를 통해서만 수행해야 합니다. 

    1. Video Analyzer for Media의 **사용자 지정 모델** 페이지로 이동하고 **애니메이션 캐릭터** 탭을 선택합니다. 
    1. 작업 중인 모델의 편집 단추를 클릭하여 Custom Vision에서 모델을 관리합니다. 
    1. 각 캐릭터 그룹을 검토합니다. 

        * 그룹에 관련되지 않은 이미지가 포함된 경우 Custom Vision 웹 사이트에서 해당 이미지를 삭제하는 것이 좋습니다. 
        * 다른 캐릭터에 속하는 이미지가 있는 경우 이미지를 클릭하고, 오른쪽 태그를 추가하고, 잘못된 태그를 삭제하여 이 특정 이미지의 태그를 변경합니다. 
        * 주로 캐릭터가 아닌 이미지 또는 여러 캐릭터의 이미지가 포함되어 그룹이 올바르지 않은 경우 Custom Vision 웹 사이트 또는 Video Analyzer for Media 인사이트에서 그룹을 삭제할 수 있습니다. 
        * 그룹화 알고리즘은 경우에 따라 캐릭터를 서로 다른 그룹으로 분할합니다. 따라서 (Video Analyzer for Media 인사이트에서) 같은 캐릭터에 속한 모든 그룹에 동일한 이름을 지정하는 것이 좋습니다. 이렇게 하면 모든 관련 그룹이 즉시 Custom Vision 웹 사이트에 켜진 것으로 표시됩니다. 
    1. 그룹이 구체화된 후 태그를 지정한 초기 이름에 그룹의 캐릭터가 반영되는지 확인합니다. 
1. 모델 학습 

    1. 원하는 모든 이름의 편집을 마친 후 모델을 학습시켜야 합니다. 
    1. 캐릭터가 모델에 학습된 후 해당 모델을 사용하여 인덱싱된 다음 비디오에서 캐릭터가 인식됩니다. 
    1. 사용자 지정 페이지를 열고,  **애니메이션 캐릭터** 탭을 클릭한 다음,  **학습** 단추를 클릭하여 모델을 학습시킵니다. Video Indexer와 모델 간에 
    
연결을 유지하기 위해 Custom Vision 웹 사이트(유료 계정이 Custom Vision 웹 사이트에 액세스할 수 있음)에서는 모델을 학습시키지 않고 Video Analyzer for Media에서만 학습시킵니다. 학습된 후에는 해당 모델을 사용하여 인덱싱하거나 다시 인덱싱할 비디오가 학습된 캐릭터를 인식합니다. 

## <a name="delete-an-animated-character-and-the-model"></a>애니메이션 캐릭터 및 모델 삭제

1. 애니메이션 캐릭터를 삭제합니다.

    1. 비디오 인사이트에서 애니메이션 캐릭터를 삭제하려면 **인사이트** 탭으로 이동하고 창의 오른쪽 위에 있는 **편집** 단추를 클릭합니다.
    1. 애니메이션 캐릭터를 선택한 다음, 이름 아래에서 **삭제** 단추를 클릭합니다.

    > [!NOTE]
    > 이렇게 하면 이 비디오에서 인사이트가 삭제되지만 모델에는 영향을 주지 않습니다.
1. 모델을 삭제합니다.

    1. 위쪽 메뉴에서 **콘텐츠 모델 사용자 지정** 단추를 클릭하고 **애니메이션 캐릭터** 탭으로 이동합니다.
    1. 삭제하려는 모델의 오른쪽에 있는 줄임표 아이콘을 클릭한 다음, 삭제 단추를 클릭합니다.
    
    * 유료 계정: 모델의 Video Analyzer for Media에서 연결이 끊어지고 다시 연결할 수 없습니다.
    * 평가판 계정: 모델이 Custom Vision에서도 삭제됩니다. 
    
        > [!NOTE]
        > 평가판 계정에서는 하나의 모델만 사용할 수 있습니다. 삭제한 후에는 다른 모델을 학습시킬 수 없습니다.

## <a name="use-the-animated-character-detection-with-api"></a>API에서 애니메이션 캐릭터 검색 사용 

1. Custom Vision 계정을 연결합니다.

    Video Analyzer for Media 유료 계정을 소유하고 있는 경우 먼저 Custom Vision 계정을 연결해야 합니다. <br/>
    Custom Vision 계정이 아직 없는 경우 새로 만드세요. 자세한 내용은 [Custom Vision](../../cognitive-services/custom-vision-service/overview.md)을 참조하세요.

    [API를 사용하여 Custom Vision 계정을 연결합니다](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Connect-Custom-Vision-Account).
1. 애니메이션 캐릭터 모델을 만듭니다.

    [애니메이션 모델 만들기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Animation-Model) API를 사용합니다.
1. 비디오를 인덱싱하고 다시 인덱싱합니다.

    [다시 인덱싱](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) API를 사용합니다. 
1. 애니메이션 캐릭터 모델을 사용자 지정합니다.

    [애니메이션 모델 학습](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Animation-Model) API를 사용합니다.

### <a name="view-the-output"></a>출력 보기

생성된 JSON 파일에서 애니메이션 캐릭터를 확인합니다.

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

* 현재 “애니메이션 식별” 기능은 동아시아 지역에서 지원되지 않습니다.
* 비디오에서 작거나 멀리 보이는 캐릭터는 비디오 품질이 나쁜 경우 제대로 식별되지 않을 수 있습니다.
* 애니메이션 캐릭터 집합당(예: 애니메이션 시리즈당) 하나의 모델을 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[Video Analyzer for Media 개요](video-indexer-overview.md)
