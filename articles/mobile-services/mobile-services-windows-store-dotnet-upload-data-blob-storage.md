<properties 
	pageTitle="모바일 서비스를 사용하여 Blob 저장소에 이미지 업로드(Windows 스토어) | 모바일 서비스" 
	description="모바일 서비스를 사용하여 Azure Blob 저장소에 이미지를 업로드하고 Windows 스토어 앱에서 이미지에 액세스하는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="glenga"/>

# 모바일 서비스를 사용하여 Azure 저장소에 이미지 업로드

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여, 사용자가 만든 이미지를 앱에서 Azure 저장소에 업로드하고 저장하는 방법에 대해 설명합니다. 모바일 서비스에서는 SQL 데이터베이스를 사용하여 데이터를 저장합니다. 그러나 Azure Blob 저장소 서비스에는 Blob(Binary Large Object) 데이터를 저장하는 것이 좀 더 효율적입니다.

클라이언트 앱에서는 Blob 저장소 서비스에 데이터를 안전하게 업로드하는 데 필요한 자격 증명을 안전하게 배포할 수 없습니다. 대신 이러한 자격 증명을 모바일 서비스에 저장하고, 이를 통해 새로운 이미지 업로드에 사용되는 SAS(공유 액세스 서명)를 생성해야 합니다. 만료 기간이 짧은(이 경우 5분) 자격 증명인 SAS는 모바일 서비스에 의해 클라이언트 앱으로 안전하게 반환됩니다. 그러면 앱은 이 임시 자격 증명을 사용하여 이미지를 업로드합니다. 이 예제에서 Blob 서비스의 다운로드 파일은 공개 파일입니다.

이 자습서에서는 모바일 서비스에서 생성한 SAS를 사용하여 사진을 찍고 이미지를 Azure에 업로드하는 기능을 모바일 서비스 퀵 스타트 앱에 추가합니다.

##필수 조건

이 자습서를 사용하려면 다음이 필요합니다.

+ Windows 8용 Microsoft Visual Studio 2012 Express 이상
+ [Azure 저장소 계정](../storage-create-storage-account.md)
+ 컴퓨터에 연결되는 카메라 또는 기타 이미지 캡처 장치

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기]를 완료해야 합니다.

##<a name="install-storage-client"></a>Windows 스토어 앱용 저장소 클라이언트 설치

SAS를 사용하여 Blob 저장소에 이미지를 업로드하려면 먼저 Windows 스토어 앱용 저장소 클라이언트 라이브러리를 설치하는 NuGet 패키지를 추가해야 합니다.

1. Visual Studio의 **솔루션 탐색기**에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2. 왼쪽 창에서 **온라인** 범주를 선택하고 `WindowsAzure.Storage`을(를) 검색하고 **Azure 저장소** 패키지에서 **설치**를 클릭한 다음 사용권 계약에 동의합니다.

  	![][2]

  	Azure 저장소 서비스용 클라이언트 라이브러리가 프로젝트에 추가됩니다.

다음 단계에서는 이미지를 캡처하고 업로드하도록 quickstart 앱을 업데이트합니다.

##<a name="update-scripts"></a>관리 포털에서 등록된 삽입 스크립트 업데이트

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

## <a name="next-steps"> </a>다음 단계

이제 모바일 서비스를 Blob 서비스와 통합하여 이미지를 안전하게 업로드할 수 있게 되었으므로 몇 가지 기타 백엔드 서비스 및 통합 항목을 확인해보십시오.

+ [SendGrid로 모바일 서비스에서 메일 보내기]
 
  SendGrid 전자 메일 서비스를 사용해 모바일 서비스에 전자 메일 기능을 추가하는 방법에 대해 알아보십시오. 이 항목에서는 SendGrid를 사용해 전자 메일을 보내기 위해 서버 쪽 스크립트를 추가하는 방법에 대해 설명합니다.

+ [모바일 서비스에서 백 엔드 작업 예약]

  모바일 서비스 작업 스케줄러 기능을 사용하여, 예약된 시간에 실행되는 서버 스크립트 코드를 정의하는 방법에 대해 알아보십시오.

+ [모바일 서비스 서버 스크립트 참조]

  서버 스크립트를 사용하여 서버 쪽 작업을 수행하고 기타 Azure 구성 요소 및 외부 리소스와 통합하는 방법을 안내하는 참조 항목입니다.
 
+ [모바일 서비스 .NET 방법 개념 참조]

  모바일 서비스를 .NET과 함께 사용하는 방법에 대해 알아보십시오.
  
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[SendGrid로 모바일 서비스에서 메일 보내기]: store-sendgrid-mobile-services-send-email-scripts.md
[모바일 서비스에서 백 엔드 작업 예약]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[모바일 서비스 서버 스크립트 참조]: mobile-services-how-to-use-server-scripts.md
[모바일 서비스 시작하기]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[모바일 서비스 .NET 방법 개념 참조]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

<!---HONumber=August15_HO6-->