<properties 
	pageTitle="모바일 서비스를 사용하여 Blob 저장소에 이미지 업로드(Windows Phone) | Microsoft Azure" 
	description="모바일 서비스를 사용하여 Azure Blob 저장소에 이미지를 업로드하는 방법에 대해 알아봅니다." 
	documentationCenter="windows" 
	authors="ggailey777" 
	services="mobile-services,storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# 모바일 서비스를 사용하여 Azure 저장소에 이미지 업로드

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##개요
이 항목에서는 Azure 모바일 서비스를 사용하여 사용자가 만든 이미지를 Azure 저장소에 업로드하고 저장하도록 Windows Phone 8 또는 Windows Phone 8.1 Silverlight 앱을 설정하는 방법에 대해 설명합니다. 모바일 서비스에서는 SQL 데이터베이스를 사용하여 데이터를 저장합니다. 그러나 Azure Blob 저장소 서비스에는 Blob(Binary Large Object) 데이터를 저장하는 것이 좀 더 효율적입니다.

클라이언트 앱에서는 Blob 저장소 서비스에 데이터를 안전하게 업로드하는 데 필요한 자격 증명을 안전하게 배포할 수 없습니다. 대신 이러한 자격 증명을 모바일 서비스에 저장하고, 이를 사용하여 새로운 이미지 업로드에 사용되는 SAS(공유 액세스 서명)를 생성해야 합니다. 만료 기간이 짧은(이 경우 5분) 자격 증명인 SAS는 모바일 서비스에 의해 클라이언트 앱으로 안전하게 반환됩니다. 그러면 앱은 이 임시 자격 증명을 사용하여 이미지를 업로드합니다. 이 예제에서 Blob 서비스의 다운로드 파일은 공개 파일입니다.

이 자습서에서는 모바일 서비스에서 생성한 SAS를 사용하여 사진을 찍고 이미지를 Azure에 업로드하는 기능을 [GetStartedWithData 샘플 앱 프로젝트](mobile-services-dotnet-backend-windows-phone-get-started-data.md)에 추가합니다.

##필수 조건 

이 자습서를 사용하려면 다음이 필요합니다.

+ Microsoft Visual Studio 2013 또는 이상 버전
+ [Windows Phone SDK 8.0] 이상
+ Microsoft Visual Studio용 Nuget 패키지 관리자 설치
+ [Azure 저장소 계정][How To Create a Storage Account]
+ [기존 앱에 모바일 서비스 추가](mobile-services-dotnet-backend-windows-phone-get-started-data.md) 자습서 완료  

>[AZURE.NOTE]이 자습서에서는 Windows Phone 8 및 Windows Phone 8.1 Silverlight 앱만 지원됩니다. Windows Phone Store 8.1 또는 유니버설 Windows 8.1 앱은 지원 하지 않습니다.

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Windows 스토어 앱용 저장소 클라이언트 설치

SAS를 사용하여 앱에서 Blob 저장소에 이미지를 업로드하려면 먼저 Windows 스토어 앱용 저장소 클라이언트 라이브러리를 설치하는 NuGet 패키지를 추가해야 합니다.

1. Visual Studio의 **솔루션 탐색기**에서 클라이언트 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2. 왼쪽 창에서 **온라인** 범주를 선택하고, **Include Prerelease**를 선택하고 **WindowsAzure.Storage-Preview**를 검색한 후 **Azure 저장소** 패키지에서 **설치**를 클릭한 다음, 사용권 계약에 동의합니다.

  	![][2]

  	Azure 저장소 서비스용 클라이언트 라이브러리가 프로젝트에 추가됩니다.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Install the storage client in the mobile service project]: #storage-client-server
[Update the TodoItem definition in the data model]: #update-data-model
[Update the table controller to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Schedule backend jobs in Mobile Services]: mobile-services-dotnet-backend-schedule-recurring-tasks.md
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Windows Phone SDK 8.0]: http://www.microsoft.com/download/details.aspx?id=35471


 

<!---HONumber=August15_HO8-->