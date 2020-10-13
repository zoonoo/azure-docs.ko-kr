---
title: 여러 날 녹화본 재생 - Azure
description: 이 자습서에서는 Azure Media Service API를 사용하여 여러 날 연속 비디오 녹화본을 재생하는 방법을 설명합니다.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 1ec9260be7241057478b06446ac2aa53c14bcb47
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803435"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>자습서: 여러 날 녹화본 재생  

이 자습서는 [연속 비디오 녹화](continuous-video-recording-concept.md)(CVR) 자습서를 기반으로 합니다. 이 자습서에서는 Azure Media Service API를 사용하여 클라우드에서 여러 날 연속 비디오 녹화본을 재생하는 방법을 설명합니다. 

이는 여러 날(또는 몇 주) 동안 카메라의 장면 녹화본을 유지 관리해야 하고 해당 장면의 특정 부분을 감시해야 하는 경우와 같은 치안 시나리오 등에 유용합니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 여러 날 녹화본의 콘텐츠를 탐색하는 방법을 보여 주는 샘플 앱 실행
> * 해당 녹화본의 선택된 부분 보기

## <a name="suggested-pre-reading"></a>추천 참고 자료  

다음 설명서 페이지를 읽어 보는 것이 좋습니다.

* [Live Video Analytics on IoT Edge 개요](overview.md)
* [Live Video Analytics on IoT Edge 용어](terminology.md)
* [미디어 그래프 개념](media-graph-concept.md)
* [연속 비디오 녹화](continuous-video-recording-concept.md) 
* [방법 가이드: 녹화본 재생](playback-recordings-how-to.md)
* [자습서: 연속 비디오 녹화](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>필수 구성 요소

* [CVR 자습서](continuous-video-recording-tutorial.md)를 완료합니다. 이 자습서와 [자습서: 연속 비디오 녹화](continuous-video-recording-tutorial.md)에서 설명하는 관련 API는 5분 이상의 녹화본에 적용되지만 5시간 정도의 비디오를 녹화하는 것이 좋습니다. 녹화본을 검색하는 데 사용되는 API는 긴 녹화본으로 가장 잘 설명할 수 있습니다.
* [자습서: 연속 비디오 녹화](continuous-video-recording-tutorial.md)가 계속 실행 중인 상태에서 이 자습서를 실행하는 것이 좋습니다. 즉, 여전히 클라우드에 비디오를 녹화하고 있는 것입니다.

## <a name="run-the-sample"></a>샘플 실행 

[CVR 자습서](continuous-video-recording-tutorial.md)를 진행하면서 Media Service 계정을 만들었을 것입니다. 이 자습서를 진행하려면 해당 계정에 대한 전체 API 액세스 권한이 필요합니다. [Media Services API에 액세스하기 위한 자격 증명 가져오기](../latest/access-api-howto.md?tabs=portal)의 단계에 따라 서비스 주체를 만들 수 있습니다. Azure Portal에서 다음과 같은 JSON 블록을 가져올 수 있어야 합니다.

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

다음으로, Visual Studio Code에서 src/ams-asset-player를 엽니다. 이 폴더에는 이 자습서를 진행하는 데 필요한 파일이 포함되어 있습니다. appsettings.json 파일을 열고 해당 파일의 내용을 새 파일인 appsettings.development.json에 복사합니다. 후자 파일을 다음과 같이 편집합니다.

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누름) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정**을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="확장 설정":::
1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="확장 설정" 단추를 선택하는 방법을 확인합니다.

![STREAM](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
플레이어의 오른쪽에서 보관 파일을 검색하는 컨트롤을 볼 수 있습니다. 이 컨트롤의 연도, 월 및 날짜는 [방법 가이드: 녹화본 재생](playback-recordings-how-to.md)에 설명된 availableMedia API를 사용하여 채워집니다.
특정 일을 확장한 후 CVR 자습서를 몇 시간 동안 실행하면 다음과 같은 결과가 표시됩니다.

![보관 파일 찾아보기](./media/playback-multi-day-recordings-tutorial/results.png)

자습서에서 비디오 피드의 원본은 MKV 파일입니다. RSTP 시뮬레이터([Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) 참조)가 파일의 끝에 도달하면 스트림이 종료됩니다. 미디어 그래프의 RTSP 원본 노드가 이를 검색하고 연결을 다시 설정하면 비디오가 다시 시작됩니다. 녹화된 보관 파일에는 이러한 파일의 종료 시점과 재연결 시점 사이에 간격이 있습니다. 이는 availableMedia 결과에 새 항목으로 표시됩니다.

![결과](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
목록에서 한 항목을 클릭하면 애플리케이션에서 적절한 필터를 사용하여 스트리밍 URL을 만듭니다(예: https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS)). 플레이어가 이 URL을 로드하면 원하는 startTime부터 재생이 시작됩니다.

또는 페이지 하단에 있는 컨트롤을 통해 특정 시작 및 종료 시간을 사용할 수 있습니다. 오른쪽에 나열된 availableMedia 호출의 결과를 허용되는 시작 및 종료 시간 값에 대한 가이드로 사용할 수 있습니다. startTime 및 endTime 필터에 대한 자세한 제약 조건은 [방법 가이드: 녹화본 재생](playback-recordings-how-to.md)에 설명되어 있습니다. 시간 값을 선택한 후 제출을 클릭하면 애플리케이션이 스트리밍 URL(예: https://{hostname}/{locatorId}/content.ism/manifest(format=mpd-time-csf,startTime=YYYY-MM-DDTHH:MM:SS,endTime= YYYY-MM-DDTHH:MM:SS))을 사용하여 플레이어를 로드합니다. 원하는 startTime부터 재생이 시작됩니다.

## <a name="next-steps"></a>다음 단계

[이벤트 기반 비디오를 클라우드에 녹화하고 클라우드에서 재생](event-based-video-recording-tutorial.md)
