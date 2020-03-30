---
title: 비디오 인덱서를 사용하는 애니메이션 캐릭터 감지
titleSuffix: Azure Media Services
description: 이 항목에서는 비디오 인덱서를 사용하여 애니메이션 된 문자 검색을 사용하는 방법을 보여 줍니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: af608dcfbb5d98cf3116de4e14dc12bf6facb97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989912"
---
# <a name="animated-character-detection-preview"></a>애니메이션 캐릭터 감지(미리 보기)

Azure 미디어 서비스 비디오 인덱서에서는 [Cognitive Services 사용자 지정 비전과의](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)통합을 통해 애니메이션 콘텐츠의 문자 검색, 그룹화 및 인식을 지원합니다. 이 기능은 포털과 API를 통해 모두 사용할 수 있습니다.

특정 애니메이션 모델로 애니메이션 비디오를 업로드한 후 Video Indexer는 키프레임을 추출하고, 이러한 프레임에서 애니메이션 된 캐릭터를 검색하고, 유사한 캐릭터를 그룹화하고, 최상의 샘플을 선택합니다. 그런 다음 그룹화 된 문자를 사용자 지정 비전으로 보내학습된 모델을 기반으로 문자를 식별합니다. 

모델 학습을 시작하기 전에 문자가 이름 없이 감지됩니다. 이름을 추가하고 모델을 학습할 때 비디오 인덱서가 문자를 인식하고 그에 따라 이름을 지정합니다.

## <a name="flow-diagram"></a>흐름 다이어그램

다음 다이어그램은 애니메이션된 캐릭터 검색 프로세스의 흐름을 보여 줍니다.

![흐름 다이어그램](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>계정

비디오 인덱서 계정 유형에 따라 다양한 기능 집합을 사용할 수 있습니다. 계정을 Azure에 연결하는 방법에 대한 자세한 내용은 [Azure에 연결된 비디오 인덱서 계정 만들기를](connect-to-azure.md)참조하십시오.

* 평가판 계정: 비디오 인덱서에서는 내부 사용자 지정 비전 계정을 사용하여 모델을 만들고 비디오 인덱서 계정에 연결합니다. 
* 유료 계정: 사용자 지정 비전 계정을 비디오 인덱서 계정에 연결합니다(아직 계정이 없는 경우 먼저 계정을 만들어야 합니다).

### <a name="trial-vs-paid"></a>평가판 대 유료

|기능|평가판|유료|
|---|---|---|
|사용자 지정 비전 계정|비디오 인덱서에 의해 무대 뒤에서 관리. |사용자 지정 비전 계정이 비디오 인덱서에 연결되어 있습니다.|
|애니메이션 모델 수|1|계정당 최대 100개의 모델(사용자 지정 비전 제한).|
|모델 학습|비디오 인덱서에서 기존 캐릭터의 새 문자 추가 예제에 대한 모델을 훈련합니다.|계정 소유자는 변경할 준비가 되면 모델을 훈련합니다.|
|사용자 지정 비전의 고급 옵션|사용자 지정 비전 포털에 액세스할 수 없습니다.|사용자 지정 비전 포털에서 모델을 직접 조정할 수 있습니다.|

## <a name="use-the-animated-character-detection-with-portal"></a>포털에서 애니메이션 캐릭터 감지 사용 

이 섹션에서는 애니메이션된 캐릭터 검색 모델을 사용하기 위해 수행해야 하는 단계에 대해 설명합니다. 

평가판 계정에서 사용자 지정 비전 통합은 비디오 인덱서에서 관리하므로 애니메이션 된 캐릭터 모델을 만들고 사용하고 다음 섹션("사용자 지정 비전 계정 연결")을 건너뛸 수 있습니다.

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>사용자 지정 비전 계정 연결(유료 계정만 해당)

비디오 인덱서 유료 계정을 소유하고 있는 경우 먼저 사용자 지정 비전 계정을 연결해야 합니다. 사용자 지정 비전 계정이 아직 없는 경우 계정을 만드세요. 자세한 내용은 [사용자 지정 비전을](../../cognitive-services/custom-vision-service/home.md)참조하십시오.

> [!NOTE]
> 두 계정이 모두 동일한 지역에 있어야 합니다. 사용자 지정 비전 통합은 현재 일본 지역에서 지원되지 않습니다.

#### <a name="connect-a-custom-vision-account-with-api"></a>사용자 지정 비전 계정을 API와 연결 

다음 단계에 따라 사용자 지정 비전 계정을 비디오 인덱서에 연결하거나 현재 비디오 인덱서에 연결된 사용자 지정 비전 계정을 변경합니다.

1. [www.customvision.ai](https://www.customvision.ai) 및 로그인을 찾아봅습니다.
1. 다음 키를 복사합니다. 

    * 교육 키(교육 리소스용)
    * 예측 키(예측 리소스의 경우)
    * 엔드포인트 
    * 예측 리소스 ID
    
    > [!NOTE]
    > 모든 키를 제공하려면 사용자 지정 비전에 교육용 과 예측을 위한 두 개의 별도 리소스가 있어야 합니다.
1. [비디오 인덱서를 찾아 로그인합니다.](https://vi.microsoft.com/)
1. 페이지의 오른쪽 상단에 있는 물음표를 클릭하고 **API 참조를**선택합니다.
1. **제품** 탭을 클릭하여 API 관리에 가입되어 있는지 확인합니다. API가 연결되어 있는 경우 다음 단계로 계속 진행할 수 있습니다. 
1. 개발자 포털에서 전체 **API 참조를** 클릭하고 **운영.**  
1. **사용자 지정 비전 계정 연결(미리 보기)을** 선택하고 **시도를 클릭합니다.**
1. 필요한 필드와 액세스 토큰을 입력하고 **보내기**를 클릭합니다. 

    Video Indexer 액세스 토큰을 얻는 방법에 대한 자세한 내용은 [개발자 포털로](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)이동하여 [관련 설명서를](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)참조하십시오.  
1. 통화가 200 OK 응답을 반환하면 계정이 연결됩니다.
1. [비디오 인덱서)](https://vi.microsoft.com/)포털을 찾아연결을 확인하려면 다음을 수행하십시오.
1. 오른쪽 상단 모서리에 있는 **콘텐츠 모델 사용자 지정** 버튼을 클릭합니다.
1. 애니메이션 캐릭터 탭으로 **이동합니다.**
1. 사용자 지정 비전에서 모델 관리를 클릭하면"**, 방금 연결한 사용자 지정 비전 계정으로 전송됩니다.

> [!NOTE]
> 현재 비디오 인덱서를 통해 생성된 모델만 지원됩니다. 사용자 지정 비전을 통해 생성된 모델은 사용할 수 없습니다. 또한 사용자 지정 비전을 통해 변경하면 의도하지 않은 결과가 발생할 수 있으므로 비디오 인덱서 플랫폼을 통해서만 비디오 인덱서를 통해 만든 모델을 편집하는 것이 가장 좋습니다.

### <a name="create-an-animated-characters-model"></a>애니메이션 캐릭터 모델 만들기

1. [Video Indexer](https://vi.microsoft.com/) 웹 사이트로 이동하고 로그인합니다.
1. 페이지의 오른쪽 상단 모서리에 있는 콘텐츠 모델 사용자 지정 버튼을 클릭합니다.

    ![콘텐츠 모델 사용자 지정](./media/animated-characters-recognition/content-model-customization.png)
1. 모델 사용자 지정 섹션의 **애니메이션 문자** 탭으로 이동합니다.
1. 모델 **추가를**클릭합니다.
1. 이름을 모델링하고 Enter를 클릭하여 이름을 저장합니다.

> [!NOTE]
> 가장 좋은 방법은 애니메이션각 시리즈에 대해 하나의 사용자 지정 비전 모델을 두는 것입니다. 

### <a name="index-a-video-with-an-animated-model"></a>애니메이션 된 모델로 비디오 색인

1. 상단 메뉴에서 **업로드** 버튼을 클릭합니다.
1. 파일 또는 URL에서 업로드할 동영상을 선택합니다.
1. 고급 **옵션을 클릭합니다.**
1. **사람 / 애니메이션 캐릭터에서** 애니메이션 **모델을**선택합니다.
1. 하나의 모델이 있는 경우 자동으로 선택되며 여러 모델이 있는 경우 드롭다운 메뉴에서 관련 모델을 선택할 수 있습니다.
1. 업로드를 클릭합니다.
1. 비디오가 인덱싱되면 **인사이트** 창의 애니메이션 문자 섹션에 감지된 **문자가** 표시됩니다.

> [!NOTE] 
> 모델에 태그를 지정하고 학습하기 전에 모든 애니메이션 캐릭터의 이름이 "알 수 없는 #X"입니다. 모델을 학습한 후에는 모델도 인식됩니다.

### <a name="customize-the-animated-characters-models"></a>애니메이션 캐릭터 모델 사용자 지정

1. 모델에 태그를 붙이고 학습합니다.

    1. 이름을 편집하여 검색된 문자에 태그를 지정합니다. 캐릭터가 모델에 대해 학습되면 해당 모델로 인덱싱된 다음 비디오로 인식됩니다. 
    1. 동영상에 애니메이션 된 캐릭터에 태그를 하려면 **인사이트** 탭으로 이동하여 창의 오른쪽 상단 모서리에있는 **편집** 버튼을 클릭하십시오.
    1. **Insights** 창에서 검색된 애니메이션 된 문자를 클릭 하 고 "알 수 없는 #X"(또는 이전에 캐릭터에 할당 된 이름)에서 이름을 변경 합니다.
    1. 새 이름을 입력한 후 새 이름 옆에 있는 확인 아이콘을 클릭합니다. 이렇게 하면 비디오 인덱서의 모델에서 새 이름이 저장됩니다.
    1. 원하는 모든 이름 편집을 완료한 후에는 모델을 학습해야 합니다.

        사용자 지정 페이지를 열고 **애니메이션 캐릭터** 탭을 클릭한 다음 **기차** 버튼을 클릭하여 모델을 학습합니다.
         
        유료 계정이 있는 경우 **고객 비전 링크에서 모델 관리** 링크를 클릭할 수 있습니다(아래 그림 참조). 그런 다음 **사용자 지정 비전의**모델 페이지로 전달됩니다.
 
        ![콘텐츠 모델 사용자 지정](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. 학습이 완료되면 해당 모델로 인덱싱되거나 다시 인덱싱되는 모든 비디오는 학습된 문자를 인식합니다. 
    사용자 지정 비전 계정에 액세스할 수 있는 유료 계정은 모델과 태그가 지정된 이미지를 볼 수 있습니다. [사용자 지정 비전에서 분류기 개선에](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier)대해 자세히 알아보십시오.

1. 애니메이션된 캐릭터를 삭제합니다.

    1. 동영상 인사이트에 애니메이션된 캐릭터를 삭제하려면 **인사이트** 탭으로 이동하여 창의 오른쪽 상단 모서리에 있는 **편집** 버튼을 클릭합니다.
    1. 애니메이션된 캐릭터를 선택한 다음 이름 아래에 있는 **삭제** 단추를 클릭합니다.

    > [!NOTE]
    > 이렇게 하면 이 비디오에서 인사이트가 삭제되지만 모델에는 영향을 미치지 않습니다.

1. 모델을 삭제합니다.

    1. 상단 메뉴에서 **콘텐츠 모델 사용자 지정** 버튼을 클릭하고 애니메이션 **캐릭터** 탭으로 이동합니다.
    1. 삭제하려는 모델의 오른쪽에 있는 타원 아이콘을 클릭한 다음 삭제 버튼을 클릭합니다.
    
    * 유료 계정: 모델의 연결이 비디오 인덱서에서 연결이 끊어지며 다시 연결할 수 없습니다.
    * 평가판 계정: 모델도 세관 비전에서 삭제됩니다. 
    
        > [!NOTE]
        > 평가판 계정에서사용할 수 있는 모델은 하나만 있습니다. 삭제한 후에는 다른 모델을 학습할 수 없습니다.

## <a name="use-the-animated-character-detection-with-api"></a>API를 사용하여 애니메이션 된 문자 감지 사용 

1. 사용자 지정 비전 계정을 연결합니다.

    비디오 인덱서 유료 계정을 소유하고 있는 경우 먼저 사용자 지정 비전 계정을 연결해야 합니다. <br/>
    사용자 지정 비전 계정이 아직 없는 경우 계정을 만드세요. 자세한 내용은 [사용자 지정 비전을](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)참조하십시오.

    [API를 사용하여 사용자 지정 비전 계정을 연결합니다.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)
1. 애니메이션된 캐릭터 모델을 만듭니다.

    애니메이션 모델 API [만들기를](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) 사용합니다.
1. 비디오 인덱싱 또는 재인덱싱합니다.

    다시 [인덱싱](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) API를 사용합니다. 
1. 애니메이션 된 캐릭터 모델을 사용자 정의할 수 있습니다.

    학습 [애니메이션 모델](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) API를 사용합니다.

### <a name="view-the-output"></a>출력 보기

생성된 JSON 파일에서 애니메이션된 문자를 참조하십시오.

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

* 현재 동아시아 지역에서는 "애니메이션 식별" 기능이 지원되지 않습니다.
* 동영상의 품질이 좋지 않은 경우 동영상에서 작거나 멀리 있는 것으로 보이는 문자가 제대로 식별되지 않을 수 있습니다.
* 애니메이션 된 캐릭터 집합당 모델을 사용하는 것이 좋습니다(예: 애니메이션 시리즈당).

## <a name="next-steps"></a>다음 단계

[Video Indexer 개요](video-indexer-overview.md)
