---
title: "Azure Portal을 사용한 VoD 제공 시작 | Microsoft Docs"
description: "이 자습서에서는 Azure 포털을 사용한 Azure 미디어 서비스(AMS) 응용 프로그램으로 기본 VoD(주문형 비디오) 콘텐츠 배달 서비스를 구현하는 단계를 안내합니다."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/23/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 555e0b6340d09517bfd87efe209f0304f3266788
ms.openlocfilehash: 76fd245f91e1bfab3df68120859c69e459283e5b


---
# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Azure 포털을 사용한 주문형 콘텐츠 제공 시작
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

이 자습서에서는 Azure 포털을 사용한 Azure 미디어 서비스(AMS) 응용 프로그램으로 기본 VoD(주문형 비디오) 콘텐츠 배달 서비스를 구현하는 단계를 안내합니다.

## <a name="prerequisites"></a>필수 조건
자습서를 완료하는 데 필요한 조건은 다음과 같습니다.

* Azure 계정. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
* 미디어 서비스 계정. Media Services 계정을 만들려면 [Media Services 계정을 만드는 방법](media-services-portal-create-account.md)을 참조하세요.

이 자습서에는 다음 작업이 포함되어 있습니다.

1. 스트리밍 끝점을 시작합니다.
2. 비디오 파일을 업로드합니다.
3. 원본 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다.
4. 자산을 게시하고, 스트리밍 기능을 사용하고, URL을 점진적으로 다운로드합니다.  
5. 콘텐츠를 재생합니다.

## <a name="start-streaming-endpoints"></a>스트리밍 끝점 시작 

Azure Media Services 작업 시 가장 일반적인 시나리오 중 하나는 적응 비트 전송률 스트리밍을 통해 비디오를 제공하는 것입니다. Media Services는 적응 비트 전송률 MP4 인코딩 콘텐츠를 Media Services에서 적시에 지원되는 각 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍)의 사전 패키징된 버전을 저장하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다.

>[!NOTE]
>AMS 계정이 만들어질 때 **기본** 스트리밍 끝점은 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 끝점은 **실행** 상태에 있어야 합니다. 

스트리밍 끝점을 시작하려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 설정 창에서 스트리밍 끝점을 클릭합니다. 
3. 기본 스트리밍 끝점을 클릭합니다. 

    기본 스트리밍 끝점 세부 정보 창이 나타납니다.

4. 시작 아이콘을 클릭합니다.
5. 저장 단추를 클릭하여 변경 내용을 저장합니다.

## <a name="upload-files"></a>파일 업로드
Azure 미디어 서비스를 사용하여 비디오를 스트림하려면 원본 비디오를 업로드하고 다중 비트 전송률로 인코딩하고 결과를 게시해야 합니다. 첫 번째 단계는 이 섹션에서 다룹니다. 

1. **설정** 창에서 **자산**을 클릭합니다.
   
    ![파일 업로드](./media/media-services-portal-vod-get-started/media-services-upload.png)
2. **업로드** 단추를 클릭합니다.
   
    **비디오 자산 업로드** 창이 나타납니다.
   
   > [!NOTE]
   > 파일 크기에는 제한이 없습니다.
   > 
   > 
3. 컴퓨터에 원하는 비디오를 찾아 선택하고 확인을 누릅니다.  
   
    업로드가 시작되고 파일 이름 아래에 진행 상태가 표시됩니다.  

업로드가 완료되면 **자산** 창에 새 자산이 나열됩니다. 

## <a name="encode-assets"></a>자산 인코딩
Azure 미디어 서비스 작업 시 가장 일반적인 시나리오 중 하나는 클라이언트에 적응 비트 전송률 스트리밍을 제공하는 것입니다. Media Services에서 지원하는 적응 비트 전송률 스트리밍은 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG-DASH입니다. 적응 비트 전송률 스트리밍을 위한 비디오를 준비하려면 소스 비디오를 다중 비트 전송률 파일로 인코딩해야 합니다. 비디오를 인코딩하는 데는 **미디어 인코더 표준** 인코더를 사용해야 합니다.  

Media Services는 다중 비트 전송률 MP4를 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍)으로 다시 패키지하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다. 동적 패키징에서는 단일 저장소 형식으로 파일을 저장하고 비용을 지불하기만 하면 됩니다. 그러면 미디어 서비스가 클라이언트의 요청에 따라 적절한 응답을 빌드 및 제공합니다.

동적 패키징을 활용하려면 원본 파일을 다중 비트 전송률 MP4 파일 집합으로 인코딩해야 합니다(인코딩 단계는 이 섹션의 뒷부분에서 설명).

### <a name="to-use-the-portal-to-encode"></a>인코딩하는 데 포털을 사용하려면
이 섹션에서는 미디어 인코더 표준을 사용하여 콘텐츠를 인코딩할 수 있는 단계를 설명합니다.

1. **설정** 창에서 **자산**을 선택합니다.  
2. **자산** 창에서 인코딩할 자산을 선택합니다.
3. **인코딩** 단추를 누릅니다.
4. **자산 인코딩** 창에서 "Media Encoder Standard" 프로세서 및 사전 설정을 선택합니다. 예를 들어 입력 비디오가 1920x1080픽셀 해상도를 포함하는 것을 알고 있는 경우 "H264 다중 비트 전송률 1080p" 사전 설정을 사용할 수 있습니다. 사전 설정에 대한 자세한 내용은 [이](media-services-mes-presets-overview.md) 문서를 참조하세요. 입력 비디오에 가장 적합한 사전 설정을 선택하는 것이 중요합니다. 낮은 해상도(640x360) 비디오가 있는 경우 기본 "H264 다중 비트 전송률 1080p" 사전 설정을 사용하지 말아야 합니다.
   
   관리를 간소화하기 위해 출력 자산의 이름과 작업 이름을 편집하는 옵션이 있습니다.
   
   ![자산 인코딩](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. **만들기**를 누릅니다.

### <a name="monitor-encoding-job-progress"></a>인코딩 작업의 진행 상태 모니터링
인코딩 작업의 진행 상태를 모니터링하려면 페이지 맨 위에 있는 **설정**을 클릭한 후 **작업**을 선택합니다.

![교육](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>콘텐츠 게시
콘텐츠를 스트리밍 또는 다운로드하는 데 사용할 수 있는 URL을 사용자에게 제공하려면 먼저 로케이터를 만들어 자산을 "게시"해야 합니다. 로케이터는 자산에 포함된 파일에 대한 액세스를 제공합니다. 미디어 서비스는 두 가지 유형의 로케이터를 지원합니다. 

* 스트리밍(OnDemandOrigin) 로케이터로, 적응 스트리밍(예: MPEG DASH, HLS 또는 부드러운 스트리밍을 스트림)에 사용합니다. 스트리밍 로케이터를 만들려면 자산에 .ism 파일이 있어야 합니다. 
* 점진적(SAS) 로케이터로, 점진적 다운로드를 통해 비디오를 배달하는 데 사용합니다.

스트리밍 URL에는 다음 형식이 있으며 부드러운 스트리밍 자산을 재생하는 데 사용할 수 있습니다.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS 스트리밍 URL을 작성하려면 URL에 (format=m3u8-aapl)을 추가합니다.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH 스트리밍 URL을 작성하려면 URL에 (format=mpd-time-csf)를 추가합니다.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


SAS URL의 형식은 다음과 같습니다.

    {blob container name}/{asset name}/{file name}/{SAS signature}

> [!NOTE]
> 2015년 3월 이전에 로케이터를 만드는 데 포털을 사용한 경우에는 만료 날짜가 2년인 로케이터가 생성되었습니다.  
> 
> 

로케이터의 만료 날짜를 업데이트하려면 [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) 또는 [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API를 사용합니다. SAS 로케이터의 만료 날짜를 업데이트할 때 해당 URL도 변경됩니다.

### <a name="to-use-the-portal-to-publish-an-asset"></a>자산을 게시하기 위해 포털을 사용하려면
자산을 게시하기 위해 포털을 사용하려면 다음을 수행합니다.

1. 계정 배포 진행 상태를 보려면 **설정** > **자산**을 참조하세요.
2. 게시하려는 자산을 선택합니다.
3. **게시** 단추를 클릭합니다.
4. 로케이터 유형을 선택합니다.
5. **추가**를 누릅니다.
   
    ![게시](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL이 **게시된 URL**목록에 추가됩니다.

## <a name="play-content-from-the-portal"></a>포털에서 콘텐츠 재생
Azure 포털에서는 비디오를 테스트하는 데 사용할 수 있는 콘텐츠 플레이어를 제공합니다.

원하는 비디오를 클릭하고 **재생** 단추를 클릭합니다.

![게시](./media/media-services-portal-vod-get-started/media-services-play.png)

다음과 같은 몇 가지 고려 사항이 적용됩니다.

* 비디오가 게시된 것을 확인합니다.
* 이 **미디어 플레이어** 가 기본 스트리밍 끝점에서 재생됩니다. 기본이 아닌 스트리밍 끝점에서 재생하려면 URL 복사를 클릭하고 다른 플레이어를 사용합니다. 예를 들어 [Azure 미디어 서비스 플레이어](http://amsplayer.azurewebsites.net/azuremediaplayer.html)를 사용합니다.

## <a name="next-steps"></a>다음 단계
미디어 서비스 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO4-->


