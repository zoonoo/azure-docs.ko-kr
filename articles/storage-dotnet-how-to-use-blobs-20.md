<properties  linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage from .NET | Microsoft Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use the Windows Azure blob service to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use the Windows Azure Blob Storage Service in .NET" authors="tamram" />

# .NET에서 Blob 저장소를 사용하는 방법

이 가이드에서는 Azure Blob 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C#으로 작성되었으며 Azure Storage Client Library for .NET을 사용합니다. Blob **업로드**, **나열**, **다운로드** 및 **삭제** 시나리오를 다룹니다. Blob에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

## 목차

* [Blob 저장소 정의](#what-is)
* [개념](#concepts)
* [Azure 저장소 계정 만들기](#create-account)
* [저장소 연결 문자열 설정](#setup-connection-string)
* [방법: 프로그래밍 방식으로 Blob 저장소 액세스](#configure-access)
* [방법: 컨테이너 만들기](#create-container)
* [방법: 컨테이너에 Blob 업로드](#upload-blob)
* [방법: 컨테이너의 Blob 나열](#list-blob)
* [방법: Blob 다운로드](#download-blobs)
* [방법: Blob 삭제](#delete-blobs)
* [다음 단계](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"></a><span  class="short-header">계정 만들기</span>Azure 저장소 계정 만들기

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="setup-connection-string"></a><span  class="short-header">연결 문자열 설정</span>저장소 연결 문자열 설정

Azure Storage Client Library for .NET은 저장소 연결 문자열을 사용하여 저장소 서비스에 액세스하기 위한 끝점 및 자격 증명을 구성하는 작업을 지원합니다. 코드에서 하드 코딩하는 대신 구성 파일에 저장소 연결 문자열을 배치할 수 있습니다.

* Azure 클라우드 서비스를 사용하는 경우 Azure 서비스 구성 시스템(`*.csdef` 및 `*.cscfg` 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.
* Azure 웹 사이트 또는 Azure 가상 컴퓨터를 사용하거나 Azure 외부에서 실행하려는 .NET 응용 프로그램을 빌드하는 경우 .NET 구성 시스템(예: `web.config` 또는 `app.config` 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.

연결 문자열 검색은 이 가이드의 뒷부분에 나와 있습니다.

### 클라우드 서비스를 사용할 때 연결 문자열 구성

서비스 구성 메커니즘은 Azure 클라우드 서비스 프로젝트에 고유하며, 응용 프로그램을 다시 배포하지 않고도 Azure 관리 포털에서 구성 설정을 동적으로 변경할 수 있게 해 줍니다.

Azure 서비스 구성에서 연결 문자열을 구성하려면

1.  Visual Studio의 솔루션 탐색기 내에서 Azure 배포 프로젝트의 **역할** 폴더에 있는 해당 웹 역할이나 작업자 역할을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.  
     ![Visual Studio에서 클라우드 서비스 역할의 속성 선택](./media/storage-dotnet-how-to-use-blobs-20/blob5.png)

2.  **설정** 탭을 클릭하고 **설정 추가** 단추를 누릅니다.  
     ![Visual Studio에서 클라우드 서비스 설정 추가](./media/storage-dotnet-how-to-use-blobs-20/blob6.png)
    
    새로운 **Setting1** 항목이 설정 그리드에 표시됩니다.

3.  새로운 **Setting1** 항목의 **유형** 드롭다운에서 **연결 문자열**을 선택합니다.  
     ![Blob7](./media/storage-dotnet-how-to-use-blobs-20/blob7.png)

4.  **Setting1** 항목의 오른쪽 끝에 있는 **...** 단추를 클릭합니다. **저장소 계정 연결 문자열** 대화
    상자가 열립니다.

5.  저장소 에뮬레이터(로컬 컴퓨터에서 시뮬레이트된 Windows Azure 저장소)를 대상으로 지정할지 또는 클라우드의 저장소 계정을 대상으로 지정할지 선택합니다. 이 가이드의 코드는 두 옵션 중 하나로 작동합니다. 앞에서 Azure에 만든 저장소 계정에 Blob 데이터를 저장하려면 이 자습서의 이전 단계에서 복사한 **기본 액세스 키** 값을 입력합니다.   
     ![Blob8](./media/storage-dotnet-how-to-use-blobs-20/blob8.png)

6.  **이름** 항목을 **Setting1**에서 **StorageConnectionString** 등의 친근한 이름으로 변경합니다. 나중에 이 가이드의 코드에서 이 연결 문자열을 참조합니다.  
     ![Blob9](./media/storage-dotnet-how-to-use-blobs-20/blob9.png)

### .NET 구성을 사용하여 연결 문자열 구성

Azure 클라우드 서비스가 아닌 응용 프로그램을 작성하는 경우(이전 섹션 참조) .NET 구성 시스템(예: `web.config` 또는 `app.config`)을 사용하는 것이 좋습니다. 여기에는 Azure 웹 사이트 또는 Azure 가상 컴퓨터와 Azure 외부에서 실행되도록 설계된 응용 프로그램이 포함됩니다. 다음과 같이 `<a ppSettings>` 요소를 사용하여 연결 문자열을 저장합니다.

    <configuration>
  		<a ppSettings>
    		<a dd key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

 저장소 연결 문자열에 대한 자세한 내용은 [연결 문자열 구성][1]을 참조하십시오.

 이제 이 가이드의 방법 작업을 수행할 준비가 되었습니다.

## <a name="configure-access"> </a><span  class="short-header">프로그래밍 방식으로 액세스</span>방법: 프로그래밍 방식으로 Blob 저장소 액세스

### 어셈블리 가져오기

 NuGet을 사용하여 `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 가져올 수 있습니다. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 온라인에서 "WindowsAzure.Storage"를 검색하고 **설치**를 클릭하여 Azure 저장소 패키지와 종속성을 설치합니다.

 `Microsoft.WindowsAzure.Storage.dll`은 [.NET 개발자 센터][2](영문)에서 다운로드할 수 있는 Azure SDK for .NET에도 포함되어 있습니다. 어셈블리는 `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk>\ref\` 디렉터리에 설치됩니다.

### 네임스페이스 선언

 프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 네임스페이스 선언을 추가합니다.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

 `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 참조해야 합니다.

### 연결 문자열 검색

 **CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. Windows Azure 프로젝트 템플릿을 사용 중이며 Microsoft.WindowsAzure.CloudConfigurationManager에 대한 참조가 있는 경우 **CloudConfigurationManager** 유형을 사용하여 Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 검색할 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

 Microsoft.WindowsAzure.CloudConfigurationManager에 대한 참조 없이 응용 프로그램을 만드는 중이며 위에 표시된 대로 연결 문자열이 `web.config` 또는 `app.config`에 있는 경우 **ConfigurationManager**를 사용하여 연결 문자열을 검색할 수 있습니다. System.Configuration.dll에 대한 참조를 프로젝트에 추가하고 다른 네임스페이스 선언을 추가해야 합니다.

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

 **CloudBlobClient** 유형을 사용하면 Blob 저장소 서비스에 저장된 Blob과 컨테이너를 나타내는 개체를 검색할 수 있습니다. 다음 코드는 위에서 검색한 저장소 계정 개체를 사용하여 **CloudBlobClient** 개체를 만듭니다.

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### ODataLib 종속성

 Storage Client Library for .NET의 ODataLib 종속성은 WCF 데이터 서비스가 아니라 NuGet을 통해 사용 가능한 ODataLib(버전 5.0.2) 패키지를 통해 확인됩니다. ODataLib 라이브러리를 직접 다운로드하거나 NuGet을 통해 코드 프로젝트에서 참조할 수 있습니다. 특정 ODataLib 패키지는 [OData][3], [Edm][4] 및 [Spatial][5]입니다.

## <a name="create-container"> </a><span  class="short-header">컨테이너 만들기</span>방법: 컨테이너 만들기

 모든 저장소 Blob은 컨테이너에 있습니다. **CloudBlobClient** 개체를 사용하여 사용할 컨테이너에 대한 참조를 가져올 수 있습니다. 컨테이너가 없는 경우 새로 만들 수 있습니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Blob 클라이언트를 만듭니다.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // 컨테이너에 대한 참조를 검색합니다. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // 컨테이너가 없는 경우 새로 만듭니다.
    container.CreateIfNotExists();

 기본적으로 새 컨테이너는 전용이며, 이 컨테이너에서 Blob을 다운로드하려면 저장소 액세스 키를 지정해야 합니다. 컨테이너 내의 파일을 모든 사용자가 사용할 수 있게 하려는 경우 다음 코드를 사용하여 컨테이너를 공용으로 설정할 수 있습니다.

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 

 BlobContainerPublicAccessType.Blob }); 인터넷상의 누구든지 공용 컨테이너의 Blob을 볼 수 있지만 해당 액세스 키가 있는 경우에만 수정하거나 삭제할 수 있습니다.

 <h2> <a name="upload-blob"> </a><span  class="short-header">컨테이너에 업로드</span>방법: 컨테이너에 Blob 업로드</h2>


 Azure Blob 저장소는 블록 Blob 및 페이지 Blob을 지원합니다. 대부분의 경우 블록 Blob을 사용하는 것이 좋습니다.

 블록 Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 다음 이 참조를 사용하여 블록 Blob 참조를 가져옵니다. Blob 참조가 있는 경우 **UploadFromStream** 메서드를 호출하여 데이터 스트림을 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다. 다음 예제에서는 컨테이너에 Blob을 업로드하는 방법을 보여 주며, 컨테이너가 이미 만들어져 있다고 가정합니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Blob 클라이언트를 만듭니다.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // 이전에 만든 컨테이너에 대한 참조를 검색합니다.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // "myblob"이라는 Blob에 대한 참조를 검색합니다.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");
    
    // 로컬 파일의 내용을 사용하여 "myblob" Blob을 만들거나 덮어씁니다.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

## <a name="list-blob"> </a><span  class="short-header">컨테이너의 Blob 나열</span>방법: 컨테이너의 Blob 나열

 컨테이너의 Blob을 나열하려면 먼저 컨테이너 참조를 가져옵니다. 컨테이너의 **ListBlobs** 메서드를 사용하여 컨테이너 내의 Blob 및/또는 디렉터리를 검색할 수 있습니다. 반환된 **IListBlobItem**에 대한 풍부한 속성 및 메서드 집합에 액세스하려면 **CloudBlockBlob**, **CloudPageBlob** 또는 **CloudBlobDirectory** 개체로 캐스트해야 합니다. 형식을 알 수 없는 경우 형식 검사를 사용하여 캐스트할 형식을 확인할 수 있습니다. 다음 코드에서는 `photos` 컨테이너에 있는 각 항목의 URI를 검색하고 출력하는 방법을 보여 줍니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Blob 클라이언트를 만듭니다. 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // 이전에 만든 컨테이너에 대한 참조를 검색합니다.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");
    
    // 컨테이너 내의 항목을 반복하고 길이와 URI를 출력합니다.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
    	if (item.GetType() == typeof(CloudBlockBlob))
    	{
    		CloudBlockBlob blob = (CloudBlockBlob)item;
    
    		Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
    	}
    	else if (item.GetType() == typeof(CloudPageBlob))
    	{
    		CloudPageBlob pageBlob = (CloudPageBlob)item;
    
    		Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
    
    	}
    	else if (item.GetType() == typeof(CloudBlobDirectory))
    	{
    		CloudBlobDirectory directory = (CloudBlobDirectory)item;
    		
    		Console.WriteLine("Directory: {0}", directory.Uri);
    	}
    }

 위에 표시된 것처럼 Blob 서비스에도 컨테이너 내의 디렉터리 개념이 있습니다. 따라서 폴더와 유사한 구조로 Blob을 구성할 수 있습니다. 예를 들어 `photos`라는 컨테이너에 있는 다음 블록 Blob 집합을 고려합니다.

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

 위 샘플과 같이 'photos' 컨테이너에서 **ListBlobs**를 호출하면 반환된 컬렉션에 디렉터리를 나타내는 **CloudBlobDirectory** 및 **CloudBlockBlob** 개체와 최상위 수준의 Blob이 포함됩니다. 결과 출력은 다음과 같습니다.

    Directory: https://<a ccountname>.blob.core.windows.net/photos/2010/
    Directory: https://<a ccountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<a ccountname>.blob.core.windows.net/photos/photo1.jpg

 선택적으로, **ListBlobs** 메서드의 **UseFlatBlobListing** 매개 변수를 **true**로 설정할 수 있습니다. 이 경우 디렉터리에 관계없이 모든 Blob이 **CloudBlockBlob**으로 반환됩니다. **ListBlobs** 호출은 다음과 같습니다.

    // 컨테이너 내의 항목을 반복하고 길이와 URI를 출력합니다.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

 결과는 다음과 같습니다.

    Block blob of length 4: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<a ccountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<a ccountname>.blob.core.windows.net/photos/photo1.jpg

 자세한 내용은 [CloudBlobContainer.ListBlobs][]를 참조하십시오.

## <a name="download-blobs"> </a><span  class="short-header">Blob 다운로드</span>방법: Blob 다운로드

 Blob을 다운로드하려면 먼저 Blob 참조를 검색한 다음 **DownloadToStream** 메서드를 호출합니다. 다음 예제에서는 **DownloadToStream** 메서드를 사용하여 Blob 콘텐츠를 스트림 개체로 전송한 다음 이 개체를 로컬 파일에 저장할 수 있습니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Blob 클라이언트를 만듭니다.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // 이전에 만든 컨테이너에 대한 참조를 검색합니다.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // "photo1.jpg"라는 Blob에 대한 참조를 검색합니다.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");
    
    // Blob 콘텐츠를 파일에 저장합니다.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

 **DownloadToStream** 메서드를 사용하여 Blob 콘텐츠를 텍스트 문자열로 다운로드할 수도 있습니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Blob 클라이언트를 만듭니다.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // 이전에 만든 컨테이너에 대한 참조를 검색합니다.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // "myblob.txt"라는 Blob에 대한 참조를 검색합니다.
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");
    
    string text;
    using (var memoryStream = new MemoryStream())
    {
    	blockBlob2.DownloadToStream(memoryStream);
    	text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"> </a><span  class="short-header">Blob 삭제</span>방법: Blob 삭제

 Blob을 삭제하려면 먼저 Blob 참조를 가져온 다음 **Delete** 메서드를 호출합니다.

    // 연결 문자열에서 저장소 계정을 검색합니다.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Blob 클라이언트를 만듭니다.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // 이전에 만든 컨테이너에 대한 참조를 검색합니다.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // "myblob.txt"라는 Blob에 대한 참조를 검색합니다.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");
    
    // Blob을 삭제합니다.
    blockBlob.Delete(); 

## <a name="next-steps"></a><span  class="short-header">다음 단계</span>다음 단계

 이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

*  사용 가능한 API에 대한 자세한 내용은 Blob 서비스 참조 설명서를 참조하십시오.
   *  [Storage Client Library for .NET 참조][6]
   *  [REST API 참조][7]
*  Azure 저장소를 사용하여 수행할 수 있는 고급 작업에 대한 자세한 내용은 [Azure에 데이터 저장 및 액세스][8]를 참조하십시오.
*  Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하십시오.
   *  [테이블 저장소](/en-us/develop/net/how-to-guides/table-services/)를 사용하여 구조화된 데이터를 저장합니다.
   *  [SQL 데이터베이스](/en-us/develop/net/how-to-guides/sql-database/)를 사용하여 관계형 데이터를 저장합니다.
 </appsettings>



[1]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/ko-kr/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dn495001(v=azure.10).aspx
[7]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dd179355
[8]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx