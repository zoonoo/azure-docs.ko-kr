<properties pageTitle="모바일 서비스를 사용하여 Blob 저장소에 이미지 업로드(Windows 스토어) | 모바일 서비스" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# 모바일 서비스를 사용하여 Azure 저장소에 이미지 업로드

[WACOM.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여, 사용자가 만든 이미지를 앱에서 Azure 저장소에 업로드하고 저장하는 방법에 대해 설명합니다. 모바일 서비스에서는 SQL 데이터베이스를 사용하여 데이터를 저장합니다. 그러나 Azure Blob 저장소 서비스에는 BLOB(Binary Large Object) 데이터를 저장하는 것이 좀 더 효율적입니다. 

클라이언트 앱에서는 Blob 저장소 서비스에 데이터를 안전하게 업로드하는 데 필요한 자격 증명을 안전하게 배포할 수 없습니다. 대신 이러한 자격 증명을 모바일 서비스에 저장하고, 이를 사용하여 새로운 이미지 업로드에 사용되는 SAS(공유 액세스 서명)를 생성해야 합니다. 만료 기간이 짧은(이 경우 5분) 자격 증명인 SAS는 모바일 서비스에 의해 클라이언트 앱으로 안전하게 반환됩니다. 그러면 앱은 이 임시 자격 증명을 사용하여 이미지를 업로드합니다. 이 예제에서 Blob 서비스의 다운로드 파일은 공개 파일입니다.

이 자습서에서는 모바일 서비스에서 생성한 SAS를 사용하여 사진을 찍고 이미지를 Azure에 업로드하는 기능을 모바일 서비스 빠른 시작 앱에 추가합니다. 이를 위해 모바일 서비스 빠른 시작을 업데이트하여 Blob 저장소 서비스에 이미지를 업로드하는 다음과 같은 기본 단계를 안내합니다.

1. [저장소 클라이언트 라이브러리 설치]
2. [클라이언트 앱을 업데이트하여 이미지 캡처]
3. [모바일 서비스 프로젝트에 저장소 클라이언트 설치]
4. [데이터 모델에서 TodoItem 정의 업데이트]
5. [테이블 컨트롤러를 업데이트하여 SAS 생성]
6. [이미지를 업로드하여 앱 테스트]

이 자습서를 사용하려면 다음이 필요합니다.

+ Microsoft Visual Studio 2013 또는 이상 버전
+ Microsoft Visual Studio용 Nuget 패키지 관리자 설치
+ [Azure 저장소 계정][저장소 계정을 만드는 방법]

이 자습서는 모바일 서비스 빠른 시작을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작]을 완료해야 합니다. 

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Windows 스토어 앱용 저장소 클라이언트 설치

SAS를 사용하여 앱에서 Blob 저장소에 이미지를 업로드하려면 먼저 Windows 스토어 앱용 저장소 클라이언트 라이브러리를 설치하는 NuGet 패키지를 추가해야 합니다.

1. Visual Studio의 **솔루션 탐색기**에서 클라이언트 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2. 왼쪽 창에서 **온라인** 범주를 선택하고, **Include Prerelease**를 선택하고 **WindowsAzure.Storage-Preview**를 검색하고 **Azure 저장소** 패키지에서 **설치**를 클릭한 다음, 사용권 계약에 동의합니다. 

  	![][2]

  	Azure 저장소 서비스용 클라이언트 라이브러리가 프로젝트에 추가됩니다.

다음 단계에서는 이미지를 캡처하고 업로드하도록 빠른 시작 앱을 업데이트합니다.

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

 
<!-- Anchors. -->
[저장소 클라이언트 라이브러리 설치]: #install-storage-client
[클라이언트 앱을 업데이트하여 이미지 캡처]: #add-select-images
[모바일 서비스 프로젝트에 저장소 클라이언트 설치]: #storage-client-server
[데이터 모델에서 TodoItem 정의 업데이트]: #update-data-model
[테이블 컨트롤러를 업데이트하여 SAS 생성]: #update-scripts
[이미지를 업로드하여 앱 테스트]: #test
[다음 단계]:#next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[SendGrid로 모바일 서비스에서 메일 보내기]: /ko-kr/documentation/articles/store-sendgrid-mobile-services-send-email-scripts/
[모바일 서비스에서 백 엔드 작업 예약]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started

[Azure 관리 포털]: https://manage.windowsazure.com/
[저장소 계정을 만드는 방법]: /ko-kr/documentation/articles/storage-create-storage-account/
[스토어 앱용 Azure 저장소 클라이언트 라이브러리]: http://go.microsoft.com/fwlink/p/?LinkId=276866 
[모바일 서비스 .NET 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/ko-kr/download/details.aspx?id=35471


