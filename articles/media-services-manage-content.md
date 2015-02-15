<properties 
	pageTitle="미디어 콘텐츠를 관리하는 방법 - Azure 미디어 서비스" 
	description="Azure 미디어 서비스에서 미디어 콘텐츠를 관리하는 방법에 대해 알아봅니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>





# 미디어 서비스에서 콘텐츠를 관리하는 방법 #

이 항목에서는 Azure 관리 포털을 사용하여 미디어 서비스 계정의 미디어 콘텐츠를 관리하는 방법을 보여 줍니다.

현재 포털에서 직접 수행할 수 있는 콘텐츠 작업은 다음과 같습니다.

- 게시 상태, 게시된 URL, 크기, 외신 업데이트 날짜/시간, 자산이 암호화되었는지 여부 등과 같은 콘텐츠 정보를 봅니다.
- 새 콘텐츠 업로드
- 콘텐츠 인덱싱
- 콘텐츠 인코딩
- 콘텐츠 재생
- 콘텐츠 게시/게시 취소


## 방법: 콘텐츠 업로드 


1. [관리 포털](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)에서 **미디어 서비스**를 클릭한 후 미디어 서비스 계정 이름을 클릭합니다.
2. 콘텐츠 페이지를 선택합니다. 
3. 페이지나 포털 맨 아래에 있는 **업로드** 단추를 클릭합니다. 
4. **콘텐츠 업로드** 대화 상자에서 원하는 자산 파일로 이동합니다. 파일을 클릭한 후 **열기**를 클릭하거나 **Enter** 키를 누릅니다.

	![UploadContentDialog][uploadcontent]

5. 콘텐츠 업로드 대화 상자에서 확인 단추를 클릭하여 파일 및 콘텐츠 이름을 적용합니다.
6. 업로드가 시작되며, 포털 맨 아래에서 진행 상태를 추적할 수 있습니다.  

	![JobStatus][status]

업로드가 완료되면 콘텐츠 목록에 새 자산이 나열됩니다. 편의상 이름의 끝에 "**-Source**"가 추가되어 새 콘텐츠를 인코딩 작업의 원본 콘텐츠로 추적할 수 있게 합니다.

![ContentPage][contentpage]

업로드 프로세스가 중지된 후 파일 크기 값이 업데이트되지 않는 경우 **메타데이터 동기화** 단추를 누릅니다. 그러면 자산 파일 크기가 저장소의 실제 파일 크기와 동기화되고 콘텐츠 페이지의 값이 새로 고쳐집니다.	

## 방법: 콘텐츠 인덱싱

Azure 미디어 인덱서를 사용하면 미디어 파일 콘텐츠를 검색 가능하게 만들고 선택 캡션 및 키워드용 전체 텍스트 기록을 생성할 수 있습니다. 아래에서 설명하는 단계에 따라 관리 포털을 사용하여 콘텐츠를 인덱싱할 수 있습니다. 그러나 파일 및 인덱싱 작업 수행 방법을 보다 세부적으로 제어하려는 경우에는 Media Services SDK for .NET이나 REST API를 사용할 수 있습니다. 자세한 내용은 [Azure 미디어 인덱서를 사용하여 미디어 파일 인덱싱](https://msdn.microsoft.com/ko-kr/library/azure/dn783455.aspx)을 참조하세요.

다음 단계는 관리 포털을 사용하여 콘텐츠를 인덱싱하는 방법을 보여 줍니다.

1. 인덱싱하려는 파일을 선택합니다.
	파일 형식에 대해 인덱싱이 지원되는 경우에는 콘텐츠 페이지 아래쪽에서 프로세스 단추를 사용할 수 있습니다.
1. 프로세스 단추를 누릅니다.
2. **프로세스** 대화 상자에서 **Azure 미디어 인덱서** 프로세서를 선택합니다.
3. 그런 다음 프로세스 대화 상자에서 입력 미디어 파일의 자세한 **제목** 및 **설명** 정보를 채웁니다.
	
	![Process][process]

## 방법: 콘텐츠 인코딩

인터넷을 통해 디지털 비디오를 배달하려면 미디어를 압축해야 합니다. 미디어 서비스는 콘텐츠를 인코딩할 방법(예: 사용할 코덱, 파일 형식, 해상도, 비트 전송률)을 지정할 수 있는 미디어 인코더를 제공합니다. 

Azure 미디어 서비스 작업 시 가장 일반적인 시나리오 중 하나는 클라이언트에 적응 비트 전송률 스트리밍을 제공하는 것입니다. 적응 비트 전송률 스트리밍을 사용하면 현재 네트워크 대역폭, CPU 사용률 및 기타 요인에 따라 비디오가 표시되므로 클라이언트는 더 높거나 낮은 비트 전송률 스트림으로 전환할 수 있습니다. 미디어 서비스가 지원하는 적응 비트 전송률 스트리밍 기술은 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 및 HDS(Adobe PrimeTime/Access 정식 사용자만 해당)입니다. 

미디어 서비스는 적응 비트 전송률 MP4 또는 부드러운 스트리밍 인코딩 콘텐츠를 미디어 서비스에서 지원되는 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍, HDS)으로 다시 패키지하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다. 

동적 패키징을 이용하려면 다음을 수행해야 합니다.

- mezzanine (원본) 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코딩하거나 트랜스코딩합니다(인코딩 단계는 이 자습서의 뒷부분에서 설명).  
- 콘텐츠를 배달하는 출발점이 될 스트리밍 끝점에 대해 하나 이상의 주문형 스트리밍 단위를 구성합니다. 자세한 내용은 [주문형 스트리밍 예약 단위를 확장하는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/media-services-how-to-scale/)(영문)을 참조하세요.

동적 패키징에서는 단일 저장소 형식으로 파일을 저장하고 비용을 지불하기만 하면 됩니다. 그러면 미디어 서비스가 클라이언트의 요청에 따라 적절한 응답을 빌드 및 제공합니다. 

동적 패키징 기능을 사용할 수 있을 뿐만 아니라, 주문형 스트리밍 예약 단위는 200Mbps 단위로 구입할 수 있는 전용 송신 용량을 제공합니다. 기본적으로 주문형 스트리밍은 다른 모든 사용자와 서버 리소스(예: 계산, 송신 기능 등)가 공유되는 공유 인스턴스 모델로 구성되어 있습니다. 주문형 스트리밍 처리량을 개선하려면 주문형 스트리밍 예약 단위를 구입하는 것이 좋습니다.


이 섹션에서는 관리 포털을 사용하여 Azure 미디어 인코더로 콘텐츠를 인코딩할 수 있는 단계를 설명합니다.

1.  인코딩하려는 파일을 선택합니다.
	이 파일 형식에 대해 이 지원되는 경우에는 콘텐츠 페이지 아래쪽에서 프로세스 단추를 사용할 수 있습니다.
4. **프로세스** 대화 상자에서** Azure 미디어 인코더 **프로세서를 선택합니다.
5. **인코딩 구성** 중에서 하나를 선택합니다.

	![Process2][process2]

	[Azure 미디어 인코더용 태스크 기본 설정 문자열](https://msdn.microsoft.com/ko-kr/library/azure/dn619392.aspx) 항목에서는 **적응 스트리밍 기본 설정(동적 패키징)**, **점진적 다운로드 기본 설정**, **적응 스트리밍 레거시 기본 설정** 범주에 있는 각 기본 설정에 대해  설명합니다.  

	**다른** 구성에 대해서는 다음에서 설명합니다.

	+ **PlayReady 콘텐츠 보호 기능으로 인코딩**. 이 사전 설정은 PlayReady 콘텐츠 보호 기능을 통해 인코딩된 자산을 생성합니다.  
	
		미디어 서비스 PlayReady 라이선스 서비스는 기본적으로 사용됩니다. 클라이언트가 PlayReady로 암호화된 콘텐츠를 재생하기 위한 라이선스를 가져올 수 있는 다른 서비스를 지정하려면 REST 또는 미디어 서비스 .NET SDK API를 사용합니다. 자세한 내용은 [정적 암호화를 사용하여 콘텐츠 보호]()를 참조하여 Media Encryptor 기본 설정의 **licenseAcquisitionUrl** 속성을 설정하세요. 동적 암호화를 사용하여 [PlayReady 동적 암호화 및 License Delivery 서비스 사용](http://go.microsoft.com/fwlink/?LinkId=507720 )의 설명에 따라 **PlayReadyLicenseAcquisitionUrl** 속성을 설정할 수도 있습니다. 
	+ **PC/Mac에서 재생(Flash/Silverlight를 통해)**. 이 기본 설정은 다음 특성을 가진 부드러운 스트리밍 자산을 생성합니다. 44.1kHz 16비트/샘플 스테레오 오디오 CBR은 AAC를 사용하여 96kbps로 인코딩되고 720p 비디오 CBR은 H.264 기본 프로필과 2초 GOP를 사용하여 3400kbps에서 400kbps 사이의 6 비트 전송률로 인코딩됩니다.
	+ **HTML5를 통해 재생(IE/Chrome/Safari)**. 이 기본 설정은 다음 특성을 가진 단일 MP4 파일을 생성합니다. 44.1kHz 16비트/샘플 스테레오 오디오 CBR은 AAC를 사용하여 128kbps로 인코딩되고 720p 비디오 CBR은 H.264 기본 프로필을 사용하여 4500kbps로 인코딩됩니다.
	+ **iOS 장치 및 PC/Mac에서 재생**. 이 기본 설정은 부드러운 스트리밍 자산과 동일한 특성(위 설명 참조)을 갖지만 Apple HLS 스트림을 iOS 장치에 제공하는 데 사용할 수 있는 형식으로 생성합니다. 

5. 그런 다음, 원하는 출력 콘텐츠 이름을 입력하거나 기본값을 적용합니다. 확인 단추를 클릭하여 인코딩 작업을 시작하고 포털 맨 아래에서 진행 상태를 추적할 수 있습니다.

6. 확인을 누릅니다.

	인코딩이 완료되고 나면 콘텐츠 페이지에 인코딩된 파일이 포함됩니다. 

	인코딩 작업의 진행률을 보려면 **작업** 페이지로 전환합니다.  

	인코딩이 완료된 후에 파일 크기 값이 업데이트되지 않는 경우에는 **메타데이터 동기화** 단추를 누릅니다. 그러면 출력 자산 파일 크기가 저장소의 실제 파일 크기와 동기화되고 콘텐츠 페이지의 값이 새로 고쳐집니다.	

## 방법: 콘텐츠 게시

콘텐츠를 게시하면 스트리밍 또는 점진적 다운로드 URL이 제공됩니다. 클라이언트가 이 URL을 사용하여 비디오를 재생할 수 있게 됩니다.

1. 게시하려는 자산을 클릭한 
2. 다음 게시 단추를 클릭합니다. 
	
	콘텐츠가 URL에 게시되고 나면 인코딩된 콘텐츠를 렌더링할 수 있는 클라이언트 플레이어에서 URL을 열 수 있습니다.

 ![PublishedContent][publishedcontent]

## 방법: 포털에서 콘텐츠 재생

관리 포털에서는 비디오를 테스트하는 데 사용할 수 있는 미디어 서비스 콘텐츠 플레이어를 제공합니다.

원하는 비디오 콘텐츠를 클릭하고 포털 맨 아래에 있는 **재생** 단추를 클릭합니다. 
 
게시된 콘텐츠만 포털에서 재생할 수 있습니다. 또한 브라우저에서 인코딩을 지원해야 합니다.


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-manage-content/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-manage-content/media-services-process-video2.png

<!--HONumber=42-->
