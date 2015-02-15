<properties 
	pageTitle="Azure 미디어 서비스와 함께 iOS 미디어 플레이어 프레임워크 사용" 
	description="미디어 서비스 iOS 미디어 플레이어 프레임워크 라이브러리를 사용하여 다양한 기능을 제공하는 동적 앱을 만드는 방법에 대해 알아봅니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>



# Azure 미디어 서비스 iOS 미디어 플레이어 프레임워크를 사용하는 방법

Azure 미디어 서비스 iOS 미디어 플레이어 프레임워크 라이브러리를 사용하면 iPod, iPhone 및 iPad 개발자가 즉석에서 비디오 및 오디오 스트림을 만들고 혼합하는 풍부한 동적 클라이언트 응용 프로그램을 쉽게 만들 수 있습니다. 예를 들어 스포츠 콘텐츠를 표시하는 응용 프로그램은 선택한 위치에 광고를 쉽게 삽입할 수 있으며, 주요 콘텐츠가 되감기는 경우에도 광고가 표시되는 빈도를 제어할 수 있습니다. 교육 응용 프로그램은 동일한 기능을 사용하여 주요 콘텐츠로 돌아가기 전에 주요 강의에 보충 내용이나 사이드바가 포함된 콘텐츠를 만들 수 있습니다.

일반적으로 응용 프로그램과 사용자 간의 상호 작용에서 발생하는 콘텐츠 스트림을 만들 수 있는 응용 프로그램을 빌드하는 것은 비교적 복잡한 작업입니다. 처음부터 전체 스트림을 만들고 미리 서버에 저장해야 합니다. iOS 미디어 플레이어 프레임워크를 사용하면 주요 콘텐츠 스트림을 제어하거나 수정하지 않고도 이러한 작업을 모두 수행할 수 있는 클라이언트 응용 프로그램을 빌드할 수 있습니다. 다음을 수행할 수 있습니다.

- 클라이언트 장치에서 미리 콘텐츠 스트림 예약
- 프리롤 광고 또는 삽입 예약
- 포스트롤 광고 또는 삽입 예약
- 중간롤 광고 또는 삽입 예약 및 광고 포드 만들기
- 콘텐츠 타임라인이 되감길 때마다 또는 한 번만 재생된 후 타임라인에서 제거되는지에 관계없이 중간롤 광고 또는 삽입을 재생할지 여부 제어
- 사용자가 단추를 눌렀는지 또는 응용 프로그램이 서비스에서 알림을 받았는지에 관계없이 임의 이벤트의 결과로 타임라인에 직접 콘텐츠를 동적으로 삽입. 예를 들어 뉴스 콘텐츠 프로그램이 속보 알림을 보내면 응용 프로그램이 주요 콘텐츠를 "일시 중지"하여 속보 스트림을 동적으로 로드할 수 있습니다. 

이러한 기능을 iOS 장치의 미디어 재생 기능과 결합하면 더 적은 리소스로 매우 짧은 시간에 풍부한 미디어 환경을 빌드할 수 있습니다.

SDK에는 이러한 기능을 사용하여 즉석에서 콘텐츠 스트림을 만드는 iOS 응용 프로그램을 빌드하고 사용자가 단추를 눌러 동적으로 삽입을 트리거할 수 있게 하는 방법을 보여 주는 SamplePlayer 응용 프로그램이 포함되어 있습니다. 이 자습서에서는 SamplePlayer 응용 프로그램의 주요 구성 요소 및 이 응용 프로그램을 사용자 응용 프로그램의 시작점으로 사용하는 방법을 보여 줍니다.

## SamplePlayer 응용 프로그램 시작
다음 단계에서는 응용 프로그램을 가져오고 프레임워크를 사용하는 응용 프로그램 영역 둘러보기를 제공하는 방법을 설명합니다. 

1. git 리포지토리를 복제합니다. 

    `git clone https://github.com/WindowsAzure/azure-media-player-framework`

2. `azure-media-player-framework/src/iOS/HLSClient/`에 있는 **SamplePlayer.xcodeproj** 프로젝트를 엽니다.

3. 샘플 플레이어의 구조는 다음과 같습니다.

![HLS Sample Code structure](http://mingfeiy.com/wp-content/uploads/2013/01/HLS-Structure.png)

4. iPad 폴더 아래에는 두 개의 .xib 파일 **SeekbarViewController** 및 **SamplePlayerViewController**가 있습니다. 두 파일은 iPad 응용 프로그램 UI 레이아웃을 만듭니다. 마찬가지로, iPhone 폴더 아래에는 검색 표시줄과 컨트롤러를 정의하는 두 개의 .xib 파일이 있습니다. 

6. 주요 응용 프로그램 논리는 `Shared` 폴더 아래의 **SamplePlayerViewController.m**에 있습니다. 아래 설명된 코드 조각은 대부분 이 파일에 있습니다. 

## UI 레이아웃 이해
플레이어 인터페이스를 정의하는 두 개의 .xib 파일이 있습니다. 다음 논의에서는 iPad 레이아웃을 예로 사용하지만 iPhone 레이아웃도 유사하며 원칙은 동일합니다.
### SamplePlayerViewController_iPad.xib
![Sample Player Address Bar](http://mingfeiy.com/wp-content/uploads/2013/01/addressbar.png)

* **미디어 URL**은 미디어 스트림을 로드하는 데 사용되는 URL입니다. 응용 프로그램에는 URL 선택 단추를 통해 사용할 수 있는 미리 채워진 미디어 URL 목록이 있습니다. 또는 고유한 HLS(HTTP 라이브 스트리밍) 콘텐츠 URL을 입력할 수 있습니다. 이 미디어 콘텐츠는 첫 번째 주요 콘텐츠로 사용됩니다. 
**참고: 이 URL을 비워 두지 마세요.**

* **URL 선택** 단추를 통해 미디어 URL 목록에서 대체 URL을 선택할 수 있습니다.

### SeekbarViewController_iPad.xib
![Seek Bar Controller](http://mingfeiy.com/wp-content/uploads/2013/01/controller.png)
* **재생 단추**를 사용하여 미디어 재생을 재생 및 일시 중지합니다.

* **검색 표시줄**은 전체 재생 타임라인을 프로젝트합니다. 검색하는 경우 길게 누르고 원하는 위치로 끈 다음 검색 표시줄에서 검색 단추를 놓습니다. 

**참고**: 사용자가 광고를 검색하는 경우 광고 기간이 포함된 새 검색 표시줄이 나타납니다. 주요 검색 표시줄에는 주요 콘텐츠의 기간만 표시됩니다. 즉, 주요 검색 표시줄에서 광고 기간은 0입니다.

* **플레이어 시간** 컨트롤은 (`Label:playerTime`)을 두 번 보여 줍니다(예: 00:23/02:10). 이 경우 00:23은 현재 재생 시간이고 02:10은 미디어의 전체 기간입니다. 

* **SkipFroward 및 SkipBackward 단추**  는 현재 예상대로 작동하지 않습니다. 업데이트된 버전이 곧 릴리스될 예정입니다.

* 주요 콘텐츠가 재생되는 동안 **지금 예약 단추**를 누르면 광고가 삽입됩니다. 코드 숨김에서 광고 원본 URL을 정의할 수 있습니다. 참고: 현재 버전에서는 다른 광고가 재생되는 동안 광고를 예약할 수 없습니다. 

### 주요 콘텐츠를 예약하는 방법
0초부터 80초까지 콘텐츠 클립을 예약했습니다.

    //Schedule the main content
    MediaTime *mediaTime = [[[MediaTime alloc] init] autorelease];
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
        
    int clipId = 0;
    if (![framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

이전 샘플 코드에서 각 요소는 다음을 나타냅니다.

* **MediaTime** 개체는 주요 콘텐츠로 예약할 비디오 클립을 제어합니다. 이전 예제에서 비디오 클립은 80초 기간(0초부터 80초까지)으로 예약됩니다.
* **clipBeginMediaTime**은 비디오 재생이 시작되는 시작 시간을 나타냅니다. 예를 들어 **clipBeginMediaTime** = 5인 경우 이 비디오 클립은 비디오 클립 시작부터 5초 후에 시작됩니다.
* **clipEndMediaTime**은 비디오 재생 종료 시간을 나타냅니다. **clipEndMediaTime**=100인 경우 비디오 클립 시작부터 100초 후에 비디오 재생이 종료됩니다.
*프레임워크에 **appendContentClip**을 요청하여 **MediaTime**을 예약합니다. 이전 예제에서 주요 콘텐츠 URL은 `[NSURL URLWithString:url]`로 제공되며 **withMedia**를 사용하여 해당 미디어의 예약이 설정됩니다.
 `[framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])` .

**참고:** 항상 프리롤 광고를 비롯한 광고 일정보다 빠른 일정을 주요 콘텐츠에 지정합니다. 

### 변형: 두 개의 주요 콘텐츠 클립을 재생하는 경우 다음 코드를 사용하여 첫 번째 클립 후에 두 번째 클립이 재생되도록 예약할 수도 있습니다.

    //Schedule second content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 30;
    mediaTime.clipEndMediaTime = 80;
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

이전 코드 뒤에 이 작업을 수행하면 주요 콘텐츠 타임라인에 콘텐츠 스트림 두 개가 예약됩니다. 첫 번째 콘텐츠는 `URLWithString:url`을 기준으로 예약되고 두 번째 콘텐츠는 `URLWithString:secondContent`를 기준으로 예약됩니다. 두 번째 콘텐츠의 경우 미디어 스트림 시작부터 30초 지점에서 콘텐츠가 시작되고 80초에서 종료됩니다. 

## 광고 일정 
현재 릴리스에서는 **pauseTimeline=false** 광고만 지원되므로 광고가 종료된 후 플레이어에서 주요 콘텐츠가 중단된 위치부터 시작됩니다. 

다음은 몇 가지 주요 사항입니다.
<ul><li> 광고를 예약할 때 모든 **LinearTime.duration**이 0이어야 합니다.</li>
<li> **clipEndMediaTime**이 광고 기간보다 길면 완료 후에 광고가 종료되며 예외가 발생하지 않습니다. 광고 기회가 손실되지 않도록 광고 기본 기간이 렌더링 시간(**clipEndMediaTime**) 이내에 있는지 확인하는 것이 좋습니다.</li> 
<li> 프리롤, 중간롤 및 포스트롤 광고가 지원됩니다. 프리롤은 모든 콘텐츠의 시작 부분에만 예약됩니다. 예를 들어 RCE(가편집) 시나리오의 두 번째 콘텐츠에 대해서는 프리롤을 예약할 수 없습니다. </li>
<li> 고정 광고 및 한 번 재생 광고가 지원되며 프리롤, 중간롤 또는 포스트롤 광고와 함께 사용할 수 있습니다.</li>
<li> 광고 형식은 .Mp4 또는 HLS일 수 있습니다.</li>
</ul>
### 프리롤, 중간롤 및 포스트롤 광고와 광고 포드를 예약하는 방법

#### 프리롤 광고 예약

    LinearTime *adLinearTime = [[[LinearTime alloc] init] autorelease];
    NSString *adURLString = @"http://smoothstreamingdemo.blob.core.windows.net/videoasset/WA-BumpShort_120530-1.mp4";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.clipBeginMediaTime = 0;
    adInfo.mediaTime.clipEndMediaTime = 5;
    adInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

**AdInfo** 개체는 광고 클립에 대한 모든 정보를 나타냅니다.
* **ClipURL**은 클립 원본의 URL입니다.
* **mediaTime** 속성은 광고가 재생되는 기간을 나타냅니다. (**clipBeginMediaTime**은 광고 시작 시간이고 **clipEndMediaTime**은 광고 종료를 정의합니다.) 이전 샘플 코드는 0부터 시작하여 5초 광고 기간까지 5초 동안 광고를 예약합니다.
* **Policy** 개체는 현재 프레임워크에서 사용되지 않습니다.
* 광고 포드가 아닌 경우 **appendTo** 값을 -1로 설정해야 합니다. 
* **type** 값은 프리롤, 중간롤, 포스트롤 또는 광고 포드일 수 있습니다. 프리롤 또는 포스트롤의 경우 관련 타이밍이 없으므로 유형을 지정합니다. 

#### 중간롤 광고 예약

이전 코드 샘플에 `adLinearTime.startTime = 23;`을 추가하면 주요 콘텐츠 타임라인의 23초에서 광고 재생이 시작됩니다.

#### 포스트롤 광고 예약

    //Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    postAdInfo.mediaTime.clipEndMediaTime = 5;
    postAdInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

프리롤 광고 예약과의 유일한 차이점은 `postAdInfo.type = AdType_Postroll;`입니다. 이전 코드는 5초 광고를 포스트롤로 예약했습니다. 

#### 광고 포드 예약
광고 포드는 여러 광고가 연속 재생되는 광고 브레이크입니다. 다음은 하나의 광고 포드에 광고 두 개를 예약하기 위한 코드입니다. 

    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    adpodInfo1.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.mediaTime = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    int32_t adIndex = 0;
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
        
    NSString *adpodSt2=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-532531b8-fca4-4c15-86f6-45f9f45ec980/Windows%208_%20Sign%20in%20with%20a%20Smile.mp4?st=2012-11-28T16%3A35%3A26Z&se=2014-11-28T16%3A35%3A26Z&sr=c&si=c6ede35c-f212-4ccd-84da-805c4ebf64be&sig=zcWsj1JOHJB6TsiQL5ZbRmCSsEIsOJOcPDRvFVI0zwA%3D";
    AdInfo *adpodInfo2 = [[[AdInfo alloc] init] autorelease];
    adpodInfo2.clipURL = [NSURL URLWithString:adpodSt2];
    adpodInfo2.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo2.mediaTime.clipBeginMediaTime = 0;
    adpodInfo2.mediaTime.clipEndMediaTime = 17;
    adpodInfo2.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo2.type = AdType_Pod;
    adpodInfo2.appendTo = adIndex;
    if (![framework scheduleClip:adpodInfo2 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

여기서 확인할 몇 가지 사항은 다음과 같습니다.
* 첫 번째 클립에서 **appendTo**는 -1입니다. `[framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex]`를 호출할 때 `adIndex`는 광고 포드에서 이 첫 번째 클립의 끝을 나타내는 고유한 값을 받습니다. 광고 포드의 두 번째 클립에서 **appendTo**를 `adpodInfo2.appendTo = adIndex;`로 설정하여 두 번째 광고의 시작을 첫 번째 광고의 끝과 일치시킵니다. 이 경우 첫 번째 클립의 끝 위치가 두 번째 클립의 시작 위치로 지정됩니다. 
* 유형을 `AdType_Pod`로 설정하여 광고 포드임을 나타내야 합니다. 

### 한 번 재생 또는 "고정" 광고를 예약하는 방법
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    oneTimeInfo.deleteAfterPlay = YES;

이전 코드 예제와 같이 **deleteAfterPlay**를 **YES**로 설정하면 이 광고가 한 번만 재생됩니다. **deleteAfterPlay**를 **NO**로 설정하면 이 광고가 계속 재생되며 "고정 광고"라고 합니다.
### 자세한 내용은 [Azure 미디어 플레이어 프레임워크 위키](https://github.com/WindowsAzure/azure-media-player-framework/wiki)(영문)를 참조하세요.

<!--HONumber=42-->
