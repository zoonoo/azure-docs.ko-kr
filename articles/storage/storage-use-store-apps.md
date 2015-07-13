<properties 
	pageTitle="Windows 스토어 앱에서 Azure 저장소 사용 | Azure" 
	description="Azure Blob, 큐 및 테이블을 사용하여 Windows 스토어 앱의 데이터를 저장하는 방법에 대해 알아봅니다." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="tamram"/>





# Windows 스토어 앱에서 Azure 저장소를 사용하는 방법

## 개요

이 가이드에서는 Azure 저장소를 사용하는 Windows 스토어 앱 개발을 시작하는 방법을 보여 줍니다.

## 필요한 도구 다운로드 ##

- [Visual Studio 2012](http://msdn.microsoft.com/library/windows/apps/br211384)를 사용하면 Windows 스토어 앱을 쉽게 빌드, 디버그, 지역화, 패키지 및 배포할 수 있습니다.
- [Windows Runtime용 Azure 저장소 클라이언트 라이브러리](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx)는 Azure 저장소와 함께 작업하기 위해 클래스 라이브러리를 제공합니다.
- [Windows 스토어 앱용 WCF 데이터 서비스 도구](http://www.microsoft.com/download/details.aspx?id=30714)는 Visual Studio 2012 이상에서 Windows 스토어 앱용 클라이언트 쪽 OData 지원으로 서비스 참조 추가까지 경험하게 해줍니다.

## 앱 개발 ##

### 준비

Visual Studio 2012 이상에서 새 Windows 스토어 앱 프로젝트를 만듭니다.

![store-apps-storage-vs-project][store-apps-storage-vs-project]

그런 다음, **참조**를 마우스 오른쪽 단추로 클릭한 후 **참조 추가**를 선택하고 다운로드한 Windows 런타임용 저장소 클라이언트 라이브러리를 찾아 Azure 저장소 클라이언트 라이브러리에 참조를 추가합니다.

![store-apps-storage-choose-library][store-apps-storage-choose-library]

### Blob 및 큐 서비스로 라이브러리 사용

이제 앱에서 Blob 및 큐 서비스를 호출할 준비가 되었습니다. Azure 저장소 형식을 직접 참조할 수 있도록 다음 **using** 문을 추가합니다.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    
그런 다음, 페이지에 단추를 추가합니다. 다음 코드를 단추의 **Click** 이벤트에 추가하고 [비동기 키워드](http://msdn.microsoft.com/library/vstudio/hh156513.aspx)로 이벤트 처리기 메서드를 수정합니다.
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();
    
이 코드는 두 개의 문자열 변수, 즉 저장소 계정 및 그 계정과 연결된 계정 키의 이름을 나타내는 *accountName*과 *accountKey*가 있다고 가정합니다.

응용 프로그램을 빌드 및 실행합니다. 단추를 클릭하면 먼저 *container1*이라는 컨테이너가 계정에 있는지 확인하고 없으면 만듭니다.

### 테이블 서비스로 라이브러리 사용

테이블 서비스와 통신하는 데 사용되는 형식은 Windows 스토어 앱 라이브러리용 WCF 데이터 서비스에 따라 다릅니다. 다음으로 패키지 관리자 콘솔을 사용하여 필요한 WCF 라이브러리에 참조를 추가합니다.

![store-apps-storage-package-manager][store-apps-storage-package-manager]

다음 명령을 사용하여 패키지 관리자가 컴퓨터의 위치를 가리키도록 합니다.
    
    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

이 명령은 필요한 모든 참조를 프로젝트에 자동으로 추가합니다. 패키지 관리자 콘솔을 사용하지 않는 경우에는 로컬 컴퓨터의 WCF 데이터 서비스 NuGet 폴더를 패키지 원본 목록에 추가한 다음 [대화 상자를 사용하여 NuGet 패키지 관리](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog)(영문)에 설명된 대로 UI를 통해 참조를 추가할 수도 있습니다.

WCF 데이터 서비스 NuGet 패키지를 참조한 경우 단추의 **Click** 이벤트에서 코드를 변경합니다.
    
    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();
    
이 코드는 *table1*이라는 테이블이 계정이 있는지 확인하고 없으면 만듭니다.

다운로드한 동일한 패키지에 있는 Microsoft.WindowsAzure.Storage.Table.dll에 참조를 추가할 수도 있습니다. 이 라이브러리에는 리플렉션 기반 직렬화 및 일반 쿼리와 같은 추가 기능이 포함되어 있습니다. 이 라이브러리는 JavaScript를 지원하지는 않습니다.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
 

<!---HONumber=July15_HO1-->