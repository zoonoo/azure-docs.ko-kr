<properties
	pageTitle="Azure 포털을 사용하여 온-프레미스 인코더에서 다중 비트 전송률 라이브 스트림을 받는 채널 만들기 | Microsoft Azure"
	description="이 자습서에서는 채널이 온-프레미스 라이브 인코더에서 다중 비트 전송률 라이브 스트림을 받는 기본적인 미디어 서비스 라이브 스트리밍 응용 프로그램을 구현하는 단계를 안내합니다."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="ne"
	ms.topic="get-started-article"
	ms.date="09/07/2015"  
	ms.author="juliako"/>


# Azure 포털을 사용하여 온-프레미스 인코더에서 다중 비트 전송률 라이브 스트림을 받는 채널 만들기

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]


이 자습서에서는 채널이 온-프레미스 라이브 인코더에서 다중 비트 전송률 라이브 스트림을 받는 기본적인 미디어 서비스 라이브 스트리밍 응용 프로그램을 구현하는 단계를 안내합니다. 채널 및 관련 구성 요소 작업에 대한 자세한 내용은 [온-프레미스 인코더에서 다중 비트 전송률 라이브 스트림을 받는 채널 작업](media-services-manage-channels-overview.md)을 참조하세요.

이 자습서에서는 Azure 포털을 사용하여 다음 작업을 수행합니다.

2.  스트리밍 끝점을 구성합니다.
3.  채널을 만듭니다.
1.  라이브 인코더를 구성하고 라이브 스트림을 채널에 수집합니다(이 단계에서는 Wirecast가 사용됨).
1.  프로그램 및 자산을 만듭니다.
1.  자산을 게시하고 스트리밍 URL을 가져옵니다.  
1.  콘텐츠를 재생합니다.
2.  정리합니다.

## 필수 조건
자습서를 완료하는 데 필요한 조건은 다음과 같습니다.

- 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](azure.microsoft.com)을 참조하세요.
- 미디어 서비스 계정. 미디어 서비스 계정을 만들려면 [계정 만들기](media-services-create-account.md)를 참조하세요.
- 다중 비트 전송률 라이브 스트림을 보낼 수 있는 웹캠 및 인코더.


## Azure 포털을 사용하여 스트리밍 끝점 구성

Azure 미디어 서비스 작업 시 가장 일반적인 시나리오 중 하나는 클라이언트에 적응 비트 전송률 스트리밍을 제공하는 것입니다. 적응 비트 전송률 스트리밍을 사용하면 현재 네트워크 대역폭, CPU 사용률 및 기타 요인에 따라 비디오가 표시되므로 클라이언트는 더 높거나 낮은 비트 전송률 스트림으로 전환할 수 있습니다. 미디어 서비스에서 지원하는 적응 비트 전송률 스트리밍 기술은 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 및 HDS(Adobe PrimeTime/Access 정식 사용자만 해당)입니다.

라이브 스트리밍으로 작업하는 경우 온-프레미스 라이브 인코더(이 경우에는 Wirecast)는 다중 비트 전송률 라이브 스트림을 채널에 수집합니다. 사용자가 스트림을 요청하면 미디어 서비스는 동적 패키징을 사용하여 원본 스트림을 요청된 적응 비트 전송률 스트림(HLS, DASH 또는 부드러운 스트림)으로 다시 패키지합니다.

동적 패키징을 이용하려면 콘텐츠를 배달할 계획인 *스트리밍 끝점*에 대한 스트리밍 단위를 하나 이상 가져와야 합니다.

스트리밍 예약 단위의 수를 변경하려면 다음을 수행합니다.

1. [Azure 포털](https://manage.windowsazure.com/)에서 **미디어 서비스**를 클릭합니다. 그런 다음 미디어 서비스의 이름을 클릭합니다.

2. **스트리밍 끝점** 페이지를 선택합니다. 그리고 수정할 스트리밍 끝점을 클릭합니다.

3. 스트리밍 단위 수를 지정하려면 **크기 조정** 탭을 선택하고 예약된 용량 슬라이더를 움직입니다.

	![크기 조정 페이지](./media/media-services-portal-get-started-with-live/media-services-origin-scale.png)

4. **저장** 단추를 클릭하여 변경 내용을 저장합니다.

	새 단위를 할당하는 작업은 완료하는 데 20분 정도 걸립니다.

	>[AZURE.NOTE]현재, 스트리밍 단위의 양수 값을 0으로 변경하면 최대 1시간 동안 스트리밍을 사용하지 않을 수 있습니다.
	>
	> 24시간 동안 가장 많은 단위 수가 비용 계산에 사용됩니다. 가격 정보에 대한 자세한 내용은 [미디어 서비스 가격 정보](http://go.microsoft.com/fwlink/?LinkId=275107)를 참조하세요.


## 채널 만들기

Azure 포털에서 **채널** 페이지를 선택합니다. 그런 다음 **새로 만들기**를 클릭합니다. **새 라이브 채널 만들기** 페이지에서 채널의 이름을 입력합니다.

![createchannel](./media/media-services-portal-get-started-with-live/media-services-create-channel.png)

페이지의 오른쪽 아래에서 확인 표시를 클릭하여 업데이트 내용을 저장합니다.

몇 분 후 채널이 만들어지고 시작됩니다.

## 수집 URL 가져오기

채널을 만든 후 라이브 인코더에 제공할 수집 URL을 가져올 수 있습니다. 인코더는 이러한 URL을 사용하여 라이브 스트림을 입력합니다.

![readychannel](./media/media-services-portal-get-started-with-live/media-services-ready-channel.png)


![ingesturls](./media/media-services-portal-get-started-with-live/media-services-ingest-urls.png)

수집 URL에 대한 자세한 내용은 [온-프레미스 인코더를 사용하여 다중 비트 전송률 라이브 스트림을 채널에 보내기](../media-services-channels-overview.md)를 참조하세요.

## 라이브 인코더를 구성하고 라이브 스트림 수집

>[AZURE.NOTE]이 단계를 수행하려면 이전 단계에서 언급된 채널의 수집 URL이 필요합니다.

Wirecast를 구성하고 스트림 수집을 시작하는 방법에 대한 자세한 내용은 [Wirecast 구성](http://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)을 참조하세요.

>[AZURE.NOTE]인코더를 중지한 후 다시 시작해야 하는 경우 먼저 Azure 포털에서 **다시 설정** 명령을 클릭하여 채널을 다시 설정해야 합니다.


## 프로그램 만들기 및 관리

### 개요

채널은 라이브 스트림에서 세그먼트의 게시 및 저장소를 제어할 수 있는 프로그램과 연결되어 있습니다. 채널은 프로그램을 관리합니다. 채널 및 프로그램 관계는 기존 미디어와 매우 유사하여 채널에는 일정한 콘텐츠 스트림이 있고 프로그램 범위는 해당 채널에 있는 일부 시간 제한 이벤트로 지정됩니다.

**보관 창** 길이를 설정하여 프로그램에 대해 기록된 콘텐츠를 유지할 시간을 지정할 수 있습니다. 이 값은 최소 5분에서 최대 25시간 사이로 설정할 수 있습니다. 또한 보관 창 길이는 클라이언트가 현재 라이브 위치에서 이전 시간을 검색할 수 있는 최대 시간을 나타냅니다. 프로그램은 지정된 시간 동안 실행되지만 기간 길이보다 늦는 콘텐츠는 계속 삭제됩니다. 또한 이 속성의 값은 클라이언트 매니페스트가 증가할 수 있는 길이를 결정합니다.

각 프로그램은 자산에 연결됩니다. 프로그램을 게시하려면 연결된 자산에 대한 주문형 로케이터를 만들어야 합니다. 이 로케이터가 있으면 클라이언트에 제공할 수 있는 스트리밍 URL을 작성할 수 있습니다.

채널은 동시 실행 프로그램을 최대 세 개까지 지원하므로 동일한 들어오는 스트림의 보관 파일을 여러 개 만들 수 있습니다. 따라서 이벤트의 여러 부분을 필요에 따라 게시하고 보관할 수 있습니다. 예를 들어 비즈니스 요구 사항에 따라 6시간의 프로그램을 보관하고 마지막 10분만 브로드캐스트해야 할 수 있습니다. 이렇게 하려면 두 개의 동시 실행 프로그램을 만들어야 합니다. 한 프로그램은 6시간의 이벤트를 보관하도록 설정하고 프로그램은 게시하지 않습니다. 다른 프로그램은 10분 동안을 보관하도록 설정하고 프로그램을 게시합니다.

새 이벤트에 기존 프로그램을 다시 사용할 수 없습니다. 대신, 각 이벤트에 대해 새 프로그램을 만들고 시작합니다.

스트리밍 및 보관을 시작할 준비가 되었으면 프로그램을 시작합니다. 이벤트 스트리밍 및 보관을 중지할 때마다 프로그램을 중지 합니다.

보관된 콘텐츠를 삭제하려면 프로그램을 중단 및 삭제한 다음 연결된 자산을 삭제합니다. 프로그램이 자산을 사용하는 경우 삭제할 수 없습니다. 프로그램을 먼저 삭제해야 합니다.

프로그램을 중단 및 삭제한 다음에도 자산을 삭제하지 않는 한 사용자는 주문형 비디오로 보관된 콘텐츠를 스트림할 수 있어야 합니다.

보관된 콘텐츠를 보관하려는데 스트리밍에 사용할 수 있는 콘텐츠가 없는 경우 스트리밍 로케이터를 삭제합니다.

### 프로그램 만들기, 시작 및 중지

채널로 들어오는 스트림이 있으면 자산, 프로그램 및 스트리밍 로케이터를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 이렇게 하면 스트림이 보관되고 스트리밍 끝점을 통해 시청자가 스트림을 사용할 수 있게 됩니다.

이벤트를 시작하는 방법에는 다음 두 가지가 있습니다.

1. **채널** 페이지에서 **추가**를 클릭하여 새 프로그램을 추가합니다.

	**새 프로그램 만들기** 페이지에서 프로그램 이름, 자산 이름, 보관 창 및 암호화 옵션을 지정합니다.

	![createprogram](./media/media-services-portal-get-started-with-live/media-services-create-program.png)

	**이 프로그램을 지금 게시**를 선택하면 게시 URL이 생성됩니다.

	프로그램을 스트리밍할 준비가 되면 언제든지 **시작**을 클릭할 수 있습니다.

	프로그램을 시작한 후 **재생**을 클릭하여 콘텐츠 재생을 시작할 수 있습니다.

	![createdprogram](./media/media-services-portal-get-started-with-live/media-services-created-program.png)

2. 또는 바로 가기를 사용하고 **채널** 페이지에서 **스트리밍 시작** 단추를 클릭할 수 있습니다. 그러면 자산, 프로그램 및 스트리밍 로케이터가 만들어집니다.

	프로그램 이름이 DefaultProgram으로 지정되고 보관 창은 1시간으로 설정됩니다.

	**채널** 페이지에서 게시된 프로그램을 재생할 수 있습니다.

	![channelpublish](./media/media-services-portal-get-started-with-live/media-services-channel-play.png)


**채널** 페이지에서 **스트리밍 중지**를 클릭하는 경우 기본 프로그램이 중지되고 삭제됩니다. 자산은 계속 그곳에 있으며 **콘텐츠** 페이지에서 자산을 게시하거나 게시 취소할 수 있습니다.

**콘텐츠** 페이지로 전환하는 경우 프로그램에 대해 만들어진 자산이 표시됩니다.

![contentasset](./media/media-services-portal-get-started-with-live/media-services-content-assets.png)


## 콘텐츠 재생

콘텐츠를 스트리밍하는 데 사용할 수 있는 URL을 사용자에게 제공하려면 먼저 로케이터를 만들어(Azure 포털을 사용하여 자산을 게시하는 경우에는 로케이터가 자동으로 만들어짐) 자산을 *게시*해야 합니다(이전 섹션에 설명되어 있음). 로케이터는 자산에 포함된 파일에 대한 액세스를 제공합니다.

콘텐츠를 재생하는 데 사용하려는 스트리밍 프로토콜에 따라 채널\\프로그램의 **게시 URL** 링크에서 가져오는 URL을 수정해야 할 수 있습니다.

동적 패키징은 라이브 스트림을 지정된 프로토콜로 패키지하는 작업을 처리합니다.

기본적으로 스트리밍 URL의 형식은 다음과 같으며 스트리밍 URL을 사용하여 부드러운 스트리밍 자산을 재생할 수 있습니다.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS 스트리밍 URL을 작성하려면 URL에 (format=m3u8-aapl)을 추가합니다.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH 스트리밍 URL을 작성하려면 URL에 (format=mpd-time-csf)를 추가합니다.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

콘텐츠 배달에 대한 자세한 내용은 [콘텐츠 배달](media-services-deliver-content-overview.md)을 참조하세요.

[AMS 플레이어](http://amsplayer.azurewebsites.net/azuremediaplayer.html)를 사용하여 부드러운 스트림을 재생하거나 iOS 및 Android 장치를 사용하여 HLS 버전 3을 재생할 수 있습니다.

## 정리

스트리밍 이벤트를 완료하고 이전에 프로비전된 리소스를 정리하려면 다음 절차를 따릅니다.

- 인코더에서 스트림의 푸시를 중지합니다.
- 채널을 중지합니다. 채널이 중지되면 채널에 대한 요금이 발생하지 않습니다. 채널을 다시 시작해야 하는 경우 채널의 수집 URL은 동일하므로 인코더를 다시 구성하지 않아도 됩니다.
- 라이브 이벤트의 보관 파일을 주문형 스트림으로 계속 제공하지 않으려면 스트리밍 끝점을 중지할 수 있습니다. 채널이 중지된 상태이면 채널에 대한 요금이 발생하지 않습니다.


##미디어 서비스 학습 경로

여기서 AMS 학습 경로를 볼 수 있습니다.

- [AMS 라이브 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS 주문형 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## 추가 리소스
- [Azure 관리 포털을 사용하여 라이브 스트리밍 시작](http://azure.microsoft.com/blog/getting-started-with-live-streaming-using-the-azure-management-portal/)

<!-- URLs. -->
[Azure portal]: http://manage.windowsazure.com/

<!-- Images -->

<!---HONumber=Sept15_HO2-->