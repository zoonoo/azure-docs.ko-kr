<properties urlDisplayName="How to manage media content" pageTitle="미디어 콘텐츠를 관리하는 방법 - Azure 미디어 서비스" metaKeywords="" description="Learn how to manage your media content in Azure Media Services." metaCanonical="" services="media-services" documentationCenter="" title="How to Manage Content in Media Services" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





# 미디어 서비스에서 콘텐츠를 관리하는 방법 #
Azure 미디어 서비스 콘텐츠 보기를 사용하여 미디어 서비스 계정의 미디어 콘텐츠를 관리할 수 있습니다.

현재 포털에서 직접 수행할 수 있는 콘텐츠 작업은 다음과 같습니다.

- 게시된 상태, 게시된 URL, 크기, 마지막 업데이트 날짜/시간 등의 콘텐츠 정보 보기
- 새 콘텐츠 업로드
- 콘텐츠 인코딩
- 콘텐츠 비디오 재생
- 콘텐츠 게시/게시 취소
- 콘텐츠 삭제


## 방법: 콘텐츠 업로드 ##


1. [관리 포털](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)에서 **미디어 서비스**를 클릭하고 미디어 서비스 계정 이름을 클릭합니다.
2. 콘텐츠 페이지를 선택합니다. 
3. 페이지나 포털 맨 아래에 있는 **업로드** 단추를 클릭합니다. 
4. **콘텐츠 업로드** 대화 상자에서 원하는 자산 파일로 이동합니다. 파일을 클릭한 후 **열기**를 클릭하거나 **Enter** 키를 누릅니다.

	![UploadContentDialog][uploadcontent]

5. 콘텐츠 업로드 대화 상자에서 확인 단추를 클릭하여 파일 및 콘텐츠 이름을 적용합니다.
6. 업로드가 시작되며, 포털 맨 아래에서 진행 상태를 추적할 수 있습니다.  

	![JobStatus][status]

업로드가 완료되면 콘텐츠 목록에 새 자산이 나열됩니다. 편의상 이름의 끝에 "**-Source**"가 추가되어 새 콘텐츠를 인코딩 작업의 원본 콘텐츠로 추적할 수 있게 합니다.

업로드 프로세스가 중지된 후 파일 크기 값이 업데이트되지 않는 경우 **메타데이터 동기화** 단추를 누릅니다. 그러면 자산 파일 크기가 저장소의 실제 파일 크기와 동기화되고 콘텐츠 페이지의 값이 새로 고쳐집니다.	


## 방법: 콘텐츠 인코딩

1. [관리 포털](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)에서 **미디어 서비스**를 클릭하고 미디어 서비스 계정 이름을 클릭합니다. 
2. 페이지 맨 위에 있는 콘텐츠 페이지를 클릭합니다.
3. 인코딩 작업에 사용할 원본 비디오를 클릭한 후 페이지 맨 아래에 있는 **인코딩**을 클릭합니다.
4. Azure 미디어 인코더 대화 사자에서 일반 또는 고급 인코딩 기본 설정 중 하나를 선택합니다.

	**일반 미리 설정**

	+ **PC/Mac에서 재생(Flash/Silverlight를 통해)**. 이 기본 설정은 다음 특성을 가진 부드러운 스트리밍 자산을 생성합니다. 44.1kHz 16비트/샘플 스테레오 오디오 CBR은 AAC를 사용하여 96kbps로 인코딩되고 720p 비디오 CBR은 H.264 기본 프로필과 2초 GOP를 사용하여 3400kbps에서 400kbps 사이의 6 비트 전송률로 인코딩됩니다.
	+ **HTML5를 통해 재생(IE/Chrome/Safari)**. 이 기본 설정은 다음 특성을 가진 단일 MP4 파일을 생성합니다. 44.1kHz 16비트/샘플 스테레오 오디오 CBR은 AAC를 사용하여 128kbps로 인코딩되고 720p 비디오 CBR은 H.264 기본 프로필을 사용하여 4500kbps로 인코딩됩니다.
	+ **iOS 장치 및 PC/Mac에서 재생**. 이 기본 설정은 부드러운 스트리밍 자산과 동일한 특성(위 설명 참조)을 갖지만 Apple HLS 스트림을 iOS 장치에 제공하는 데 사용할 수 있는 형식으로 생성합니다. 
	+ **PlayReady 콘텐츠 보호 기능으로 인코드**. 이 사전 설정은 PlayReady 콘텐츠 보호 기능을 통해 인코딩된 자산을 생성합니다.  이 사전 설정이 작동하려면 PlayReady License Delivery 서비스를 사용하도록 설정해야 합니다. 이렇게 하려면 **콘텐츠 보호** 탭으로 이동하여 브랜딩 보고 테이블에 행을 추가합니다. 저장을 누르면 몇 분 후에 미디어 서비스 PlayReady 라이선스 서비스가 사용하도록 설정됩니다.   
	
	
		미디어 서비스 PlayReady 라이선스 서비스는 기본적으로 사용됩니다. 클라이언트가 PlayReady로 암호화된 콘텐츠를 재생하기 위한 라이선스를 가져올 수 있는 다른 서비스를 지정하려면 REST 또는 미디어 서비스 .NET SDK API를 사용합니다. 자세한 내용은 [정적 암호화를 사용하여 콘텐츠 보호]를 참조하여 Media Encryptor 미리 설정의 **licenseAcquisitionUrl** 속성을 설정합니다. 동적 암호화를 사용하여 [PlayReady 동적 암호화 및 License Delivery 서비스 사용]의 설명에 따라 **PlayReadyLicenseAcquisitionUrl**(http://go.microsoft.com/fwlink/?LinkId=507720 )속성을 설정할 수도 있습니다. 
		
		이 옵션은 PlayReady 콘텐츠 보호 미리 보기 기능을 등록한 경우에만 표시됩니다. 미리 보기 기능을 등록하려면 [Microsoft Azure 미리 보기 기능](http://azure.microsoft.com/ko-kr/services/preview/)페이지에서 설명하는 프로세스를 진행해야 합니다.  
	
		
	**고급 미리 설정**
	
	+ [Azure Media Encoder용 작업 미리 설정 문자열](http://go.microsoft.com/fwlink/?LinkId=270865) 항목에서는 고급 미리 설정 목록에 있는 각 미리 설정의 의미를 설명합니다. 


	![EncoderDialog][encoder]

	현재 포털에서는 미디어 인코더가 지원하는 일부 인코딩 형식을 지원하지 않습니다. 또한 미디어 자산 암호화/암호 해독을 지원하지 않습니다. 이러한 작업은 프로그래밍 방식으로 수행할 수 있습니다. 자세한 내용은 [Media Services SDK for .NET을 사용하여 응용 프로그램 빌드](http://go.microsoft.com/fwlink/?LinkId=270866) 및 [Azure 미디어 인코더에 대한 작업 미리 설정 문자열](http://go.microsoft.com/fwlink/?LinkId=270865)을 참조하세요.


5. Azure 미디어 인코더 대화 상자에서 원하는 출력 콘텐츠 이름을 입력하거나 기본값을 적용합니다. 확인 단추를 클릭하여 인코딩 작업을 시작하고 포털 맨 아래에서 진행 상태를 추적할 수 있습니다.

	인코딩이 완료되면 보기가 아래 스크린샷과 같이 표시되어야 합니다. 

	![PortalViewUploadCompleted][portaloverview]


	인코딩이 완료된 후 파일 크기 값이 업데이트되지 않는 경우 **메타데이터 동기화** 단추를 누릅니다. 그러면 출력 자산 파일 크기가 저장소의 실제 파일 크기와 동기화되고 콘텐츠 페이지의 값이 새로 고쳐집니다.	

## 방법: 콘텐츠 게시

1. [관리 포털](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)에서 **미디어 서비스**를 클릭하고 미디어 서비스 계정 이름을 클릭합니다.
2. 페이지 맨 위에 있는 콘텐츠 페이지를 클릭합니다.
3. 게시되지 않은 자산을 클릭합니다. 게시 단추를 클릭하여 공용 URL에 게시합니다. 콘텐츠가 URL에 게시되고 나면 인코딩된 콘텐츠를 렌더링할 수 있는 클라이언트 플레이어에서 URL을 열 수 있습니다.

 ![PublishedContent][publishedcontent]

## 방법: 포털에서 콘텐츠 재생


1. [관리 포털](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)에서 **미디어 서비스**를 클릭하고 미디어 서비스 계정 이름을 클릭합니다.
2. 페이지 맨 위에 있는 콘텐츠 페이지를 클릭합니다.
3. 원하는 비디오 콘텐츠를 클릭하고 포털 맨 아래에 있는 **재생** 단추를 클릭합니다. 게시된 콘텐츠만 포털에서 재생할 수 있습니다. 또한 브라우저에서 인코딩을 지원해야 합니다.


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png

<!--HONumber=35_1-->
