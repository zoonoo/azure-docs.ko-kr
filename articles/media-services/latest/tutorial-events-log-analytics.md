---
title: Azure Log Analytics에 Azure Media Services 이벤트 저장
titleSuffix: Azure Media Services
description: Azure Log Analytics에 Azure Media Services 이벤트를 저장하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: inhenkel
ms.openlocfilehash: dfd7b3dcc7e27dd1251c35c6272b5aa7f8036006
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88941794"
---
# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>자습서: Azure Log Analytics에 Azure Media Services 이벤트 저장

## <a name="azure-media-services-events"></a>Azure Media Services 이벤트

Azure Media Services v3은 [Azure Event Grid](media-services-event-schemas.md)에서 이벤트를 내보냅니다. 다양한 방법으로 이벤트를 구독하고 데이터 저장소에 저장할 수 있습니다. 이 자습서에서는 [논리 앱 흐름](https://azure.microsoft.com/services/logic-apps/)을 사용하여 Media Services 이벤트를 구독합니다. 논리 앱은 각 이벤트에 대해 트리거되고 Azure Log Analytics에 이벤트의 본문을 저장합니다. 이벤트가 Azure Log Analytics에 있으면 다른 Azure 서비스를 사용하여 해당 이벤트에 대한 대시보드, 모니터링 및 경고를 만들 수 있지만 이 자습서에서는 다루지 않습니다.

> [!NOTE]
> 온-프레미스 인코더로 FFmpeg 사용에 이미 익숙하면 유용할 수 있습니다.  그렇지 않아도 괜찮습니다. 비디오 스트리밍을 위한 명령줄 및 지침은 아래에 포함되어 있습니다.

다음 방법을 알게 됩니다.

> [!div class="checklist"]
> * 코드 없는 논리 앱 흐름 만들기
> * Azure Media Services 이벤트 항목 구독
> * 이벤트를 구문 분석하고 Azure Log Analytics에 저장
> * Azure Log Analytics의 이벤트 쿼리

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

> * [Azure 구독](how-to-set-azure-subscription.md)
> * [Media Services](create-account-howto.md) 계정 및 리소스 그룹
> * OS용 [FFmpeg](https://ffmpeg.org/download.html) 설치
> * [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 작업 영역

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>논리 앱을 사용하여 Media Services 이벤트 구독

1. Azure Portal에서 [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 작업 영역을 아직 만들지 않았으면 만듭니다. 작업 영역 ID와 키 중 하나가 필요하므로 브라우저 창을 열어 둡니다. 그런 다음, 다른 탭이나 창에서 포털을 엽니다.

1. Azure Media Services 계정으로 이동하여 **이벤트**를 선택합니다. 그러면 Azure Media Services 이벤트를 구독하는 모든 메서드가 표시됩니다.
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services 포털](media/tutorial-events-log-analytics/select-events-01a.png)

1. **Logic Apps 아이콘**을 선택하여 논리 앱을 만듭니다. 그러면 이벤트를 캡처하여 Log Analytics로 푸시하는 흐름을 만들 수 있는 논리 앱 디자이너가 열립니다. 
    > [!div class="mx-imgBorder"]
    > ![논리 앱 만들기](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. **+ 아이콘**을 선택하고 사용하려는 테넌트를 선택한 다음, 로그인을 선택합니다. Microsoft 로그인 프롬프트가 표시됩니다.
    > [!div class="mx-imgBorder"]
    > ![Azure Event Grid에 연결](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
![테넌트 선택](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. **계속**을 선택하여 Media Services 이벤트를 구독합니다.
    > [!div class="mx-imgBorder"]
    > ![Azure Event Grid에 연결됨](media/tutorial-events-log-analytics/select-continue-04.png)

1. **리소스 종류** 목록에서 "Microsoft.Media.MediaServices"를 찾습니다.
    > [!div class="mx-imgBorder"]
    >![Azure Media Services 리소스 이벤트](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. **이벤트 유형 항목**을 선택합니다. Azure Media Services가 내보내는 모든 이벤트 목록이 있습니다. 추적할 이벤트를 선택할 수 있습니다. 여러 이벤트 유형을 추가할 수 있습니다. (나중에, 각 이벤트 유형을 별도의 Log Analytics 로그에 저장하고 이벤트 유형 이름을 Log Analytics 로그 이름에 동적으로 전파하도록 논리 앱 흐름을 약간 변경합니다.)
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services 이벤트 유형](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. **다른 이름으로 저장**을 선택합니다.

1. 논리 앱에 이름을 지정합니다.  기본적으로 리소스 그룹이 선택됩니다. 다른 설정은 그대로 두고 **만들기**를 선택합니다.  Azure 홈 화면으로 돌아갑니다.
    > [!div class="mx-imgBorder"]
    > ![논리 앱 이름 지정 인터페이스](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>작업 만들기

이벤트를 구독했으면 작업을 만듭니다.

1. 포털에서 홈 화면으로 돌아간 경우, 모든 리소스에서 앱 이름을 검색하여 방금 만든 논리 앱으로 다시 이동합니다.

1. 앱을 선택한 다음, **논리 앱 디자이너**를 선택합니다. 디자이너 창이 열립니다.

1. **+ 새 단계**를 선택합니다.

1. 이벤트를 Azure Log Analytics 서비스에 푸시해야 하므로 "Azure Log Analytics"를 검색하고 "Azure Log Analytics Data Collector"를 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics Data Collector](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. Log Analytics 작업 영역에 연결하려면 작업 영역 ID와 에이전트 키가 필요합니다. 새 탭 또는 창에서 Azure Portal을 열고 이 자습서를 시작하기 전에 만든 Log Analytics 작업 영역으로 이동합니다.
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics 작업 영역 ID](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. 왼쪽 메뉴에서 **에이전트 관리**를 찾아서 선택합니다. 그러면 생성된 에이전트 키가 표시됩니다.
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics 에이전트 관리](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. *작업 영역 ID*를 복사합니다.
    > [!div class="mx-imgBorder"]
    > ![작업 영역 ID 복사](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. 다른 브라우저 탭 또는 창의 Azure Log Analytics Data Collector에서 **데이터 보내기**를 선택하고, 연결 이름을 지정한 다음, **작업 영역 ID** 필드에 *작업 영역 ID*를 붙여넣습니다.

1. 작업 영역 브라우저 탭 또는 창으로 돌아가서 *작업 영역 키*를 복사합니다.
    > [!div class="mx-imgBorder"]
    > ![에이전트 관리 기본 키](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. 다른 브라우저 탭 또는 창에서 **작업 영역 키** 필드에 *작업 영역 키*를 붙여넣습니다.

1. **만들기**를 선택합니다. 이제 JSON 요청 본문과 사용자 지정 로그 이름을 만듭니다.

1. **JSON 요청 본문** 필드를 선택합니다.  **동적 콘텐츠 추가** 링크가 표시됩니다.

1. **동적 콘텐츠 추가**를 선택한 다음, **토픽**을 선택합니다.

1. **사용자 지정 로그 이름**에 대해 동일한 작업을 수행합니다.
    > [!div class="mx-imgBorder"]
    > ![선택한 토픽](media/tutorial-events-log-analytics/topic-selected.png)

1. 논리 앱의 **코드 보기**를 선택합니다. Inputs 및 Log-Type 줄을 찾습니다.
    > [!div class="mx-imgBorder"]
    > ![두 줄의 코드 보기](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. `body` 값을 `"@triggerBody()?['topic']"`에서 `"@{triggerBody()}"`로 변경합니다. 전체 메시지를 Log Analytics로 구문 분석하기 위해서입니다.

1. `Log-Type`을 `"@triggerBody()?['topic']"`에서 `"@replace(triggerBody()?['eventType'],'.','')"`으로 변경합니다. ("."가 Log Analytics 로그 이름에서 허용되지 않으므로 대체됩니다.)
    > [!div class="mx-imgBorder"]
    > ![변경 후 논리 앱 json](media/tutorial-events-log-analytics/changed-lines.png)

1. **저장**을 선택합니다.

1. 확인하려면 **논리 앱 디자이너**를 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![본문 및 함수 단계 확인](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. 리소스 그룹의 모든 리소스를 검사하면 논리 앱과 두 개의 논리 앱 API 커넥터가 나열됩니다. 하나는 이벤트용이고 다른 하나는 Log Analytics용입니다. Event Grid 시스템 항목에 대한 자세한 내용은 [Event Grid 시스템 토픽](https://docs.microsoft.com/azure/event-grid/system-topics)을 참조하세요.
    > [!div class="mx-imgBorder"]
    > ![리소스 그룹의 모든 새 리소스 보기](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>테스트

실제로 작동하는 방식을 테스트하려면 Azure Media Services에서 라이브 이벤트를 만듭니다. RTMP 라이브 이벤트를 만들고 ffmpeg를 사용하여 .mp4 샘플 파일을 기반으로 "라이브" 스트림을 푸시합니다. 이벤트가 생성된 후 RTMP 수집 URL을 가져옵니다.

1. Media Services 계정에서 **라이브 스트리밍**을 선택합니다.
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services 라이브 이벤트 만들기](media/tutorial-events-log-analytics/live-event.png)

1. **라이브 이벤트 추가**를 선택합니다.

1. **라이브 이벤트 이름** 필드에 이름을 입력합니다. (**설명** 필드는 선택 사항입니다.)

1. **표준** 클라우드 인코딩을 선택합니다.

1. 인코딩 사전 설정으로 **기본 720p**를 선택합니다.

1. **RTMP** 입력 프로토콜을 선택합니다.

1. 영구 입력 URL에 대해 **예**를 선택합니다.

1. 이벤트가 만들어질 때 이벤트를 시작하려면 **예**를 선택합니다.

    > [!WARNING]
    > 예를 선택하면 청구가 시작됩니다.  FFmpeg를 사용하여 스트리밍을 시작하기 직전까지 스트림 시작을 기다리려면 **아니요**를 선택하고 라이브 이벤트를 시작해야 합니다.

    > [!div class="mx-imgBorder"]
    > ![라이브 이벤트 설정](media/tutorial-events-log-analytics/live-event-settings.png)

1. **I have all the rights to use the content/file...** (콘텐츠/파일을 사용할 수 있는 모든 권한이 있습니다.) 확인란을 선택합니다.

1. **검토 + 만들기**를 선택합니다.

1. 설정을 검토한 다음, **만들기**를 선택합니다.  라이브 이벤트 목록이 나타나고 라이브 이벤트 수집 URL이 표시됩니다.

1. **수집 URL**을 클립보드에 복사합니다.

1. 목록에서 **라이브 이벤트**를 선택하여 생산자 보기를 확인합니다.

### <a name="stream-with-ffmpeg-cli"></a>FFmpeg CLI를 사용하여 스트리밍

1. 다음 명령줄을 사용합니다.

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. `<ingestURL>`을 클립보드에 복사한 수집 URL로 변경합니다.
1. `<localpathtovideo>`를 FFmpeg에서 스트리밍하려는 파일의 로컬 경로로 변경합니다.
1. 고유한 이름(예: `mystream`)을 추가합니다.
1. 테스트 원본 파일 및 기타 시스템 변수를 반영하도록 명령줄을 조정합니다.
1. 명령을 실행합니다. 몇 초 후에 "생산자 보기" 플레이어가 스트리밍을 시작합니다. (비디오가 자동으로 표시되지 않으면 플레이어를 새로 고칩니다.)

    > [!div class="mx-imgBorder"]
    > ![생산자 미리 보기 플레이어에서 적절한 비디오 수집 확인](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>이벤트 확인

라이브 스트림을 통해 Azure Media Services는 논리 앱 흐름을 트리거하는 다양한 이벤트를 내보냅니다. 확인하려면 논리 앱으로 이동하여 Media Services의 이벤트에서 발생하는 트리거가 있는지 확인합니다.

1. 논리 앱 개요 페이지로 이동하면 성공적으로 완료된 작업을 나열하는 "실행 기록"이 표시됩니다.
    > [!div class="mx-imgBorder"]
    > ![논리 앱에서 성공적인 작업 실행 확인](media/tutorial-events-log-analytics/run-history.png)

1. 성공한 작업을 선택합니다. 런타임 중에 작업에 대한 세부 정보가 표시됩니다.
1. **데이터 보내기**를 선택하여 펼칩니다. 이 경우 `MicrosoftMediaLiveEventEncoderConnected` 이벤트는 구문 분석된 본문과 함께 캡처된 것을 보여줍니다. 이것이 Azure Log Analytics 작업 영역으로 푸시됩니다.
    > [!div class="mx-imgBorder"]
    > ![데이터 보내기 참조](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>로그 확인

1. 이전에 만든 Log Analytics 작업 영역으로 이동합니다.

1. **로그**를 선택합니다.
1. 예제 쿼리 팝업을 닫습니다.
1. 사용자 지정 로그가 있습니다. 아래쪽 화살표를 선택하여 펼칩니다. 이벤트 이름 `MicrosoftMediaLiveEventEncoderConnected`가 보입니다.
1. 이벤트 이름을 선택하여 펼칩니다.
1. "눈" 아이콘을 선택하면 쿼리 결과의 미리 보기가 표시됩니다.
1. **쿼리 편집기에서 확인**을 선택한 다음, **TimeGenerated UTC** 목록에서 항목을 선택하여 펼쳐서 원시 데이터를 살펴봅니다.

![Log Analytics에서 자세한 이벤트 출력 보기](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>리소스 삭제

이 자습서에서 만든 리소스를 계속 사용하지 않으려면 리소스 그룹의 모든 리소스를 삭제해야 합니다. 그렇지 않으면 요금이 계속 청구됩니다.

## <a name="next-steps"></a>다음 단계

다양한 쿼리를 만들고 저장할 수 있습니다. 이것을 [Azure 대시보드](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)에 추가할 수 있습니다.
