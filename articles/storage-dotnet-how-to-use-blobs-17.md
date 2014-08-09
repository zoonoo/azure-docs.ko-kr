<properties  linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob Service (2.0)" pageTitle="How to use blob storage in .NET | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob .NET, Azure blob C#, Azure blob C#" description="Learn how to use the Azure blob service to upload,,download, list, and delete blob content. Samples are written in C#." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Azure Blob Storage Service in .NET" authors="" solutions="" manager="paulettm" editor="cgronlun" />

# .NET에서 Azure Blob 저장소 서비스를 사용하는 방법

<div  class="dev-center-tutorial-selector">
<a href="/en-us/develop/net/how-to-guides/blob-storage-v17/" title="버전 1.7" class="current">버전 1.7</a>
<a href="/en-us/develop/net/how-to-guides/blob-storage/" title="버전 2.0">버전 2.0</a> 
</div>

이 가이드에서는 Azure Blob 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C#으로 작성되었으며 .NET API를 사용합니다. Blob **업로드**, **나열**, **다운로드** 및 **삭제** 시나리오를 다룹니다. Blob에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

<h2>목차</h2>


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

<h2><a name="create-account"></a><span  class="short-header">계정 만들기</span>Azure 저장소 계정 만들기</h2>


[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">연결 문자열 설정</span>저장소 연결 문자열 설정</h2>


Azure .NET 저장소 API는 저장소 연결 문자열을 사용하여 저장소 서비스에 액세스하기 위한 끝점 및 자격 증명을 구성하는 작업을 지원합니다. 코드에서 하드 코딩하는 대신 구성 파일에 저장소 연결 문자열을 배치할 수 있습니다.

* Azure 클라우드 서비스를 사용하는 경우 Azure 서비스 구성 시스템(`*.csdef` 및 `*.cscfg` 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.
* Azure 웹 사이트나 Azure 가상 컴퓨터를 사용하는 경우 .NET 구성 시스템(예: `web.config` 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.

두 경우 모두, 이 가이드의 뒷부분에 표시된 대로 `CloudConfigurationManager.GetSetting` 메서드를 사용하여 연결 문자열을 검색할 수 있습니다.

### 클라우드 서비스를 사용하는 경우 연결 문자열 구성

서비스 구성 메커니즘은 Azure 클라우드 서비스 프로젝트에 고유하며, 응용 프로그램을 다시 배포하지 않고도 Azure 관리 포털에서 구성 설정을 동적으로 변경할 수 있게 해 줍니다.

Azure 서비스 구성에서 연결 문자열을 구성하려면

1.  Visual Studio의 솔루션 탐색기 내에서 Azure 배포 프로젝트의 **역할** 폴더에 있는 해당 웹 역할이나 작업자 역할을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.  
     ![Visual Studio에서 클라우드 서비스 역할의 속성
    선택](./media/storage-dotnet-how-to-use-blobs-17/blob5.png)

2.  **설정** 탭을 클릭하고 **설정 추가** 단추를 누릅니다.  
     ![Visual Studio에서 클라우드 서비스 설정
    추가](./media/storage-dotnet-how-to-use-blobs-17/blob6.png)
    
    새로운 **Setting1** 항목이 설정 그리드에 표시됩니다.

3.  새로운 **Setting1** 항목의 **유형** 드롭다운에서 **연결 문자열**을 선택합니다.  
     ![Blob7](./media/storage-dotnet-how-to-use-blobs-20/blob7.png)

4.  **Setting1** 항목의 오른쪽 끝에 있는 **...** 단추를 클릭합니다. **저장소 계정 연결 문자열** 대화
    상자가 열립니다.

5.  저장소 에뮬레이터(로컬 컴퓨터에서 시뮬레이트된 Azure 저장소)를 대상으로 지정할지 또는 클라우드의 실제 저장소 계정을 대상으로 지정할지 선택합니다. 이 가이드의 코드는 두 옵션 중 하나로 작동합니다. 앞에서 Azure에 만든 저장소 계정에 Blob 데이터를 저장하려면 이 자습서의 이전 단계에서 복사한 **기본 액세스 키** 값을 입력합니다.   
     ![Blob8](./media/storage-dotnet-how-to-use-blobs-20/blob8.png)

6.  **이름** 항목을 **Setting1**에서 **StorageConnectionString** 등의 친근한 이름으로 변경합니다. 이 가이드의 코드 뒷부분에서 이 연결 문자열을 참조합니다.  
     ![Blob9](./media/storage-dotnet-how-to-use-blobs-20/blob9.png)

### 웹 사이트 또는 가상 컴퓨터를 사용하는 경우 연결 문자열 구성

웹 사이트나 가상 컴퓨터를 사용하는 경우 .NET 구성 시스템(예: `web.config`)을 사용하는 것이 좋습니다. `<a ppSettings>` 요소를 사용하여 연결 문자열을 저장합니다.

    <configuration>
	    <appSettings>
		    <add key="StorageConnectionString"
			     value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
		</appSettings>
	</configuration>

저장소 연결 문자열에 대한 자세한 내용은 [연결 문자열 구성][1]을 참조하십시오.

이제 이 가이드의 방법 작업을 수행할 준비가 되었습니다.

<h2> <a name="configure-access"> </a><span  class="short-header">프로그래밍 방식으로 액세스</span>방법: 프로그래밍 방식으로 Blob 저장소 액세스</h2>


프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

**CloudStorageAccount** 유형과 **CloudConfigurationManager** 유형을 사용하여 Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 검색할 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

**CloudBlobClient** 유형을 사용하면 Blob 저장소 서비스에 저장된 Blob과 컨테이너를 나타내는 개체를 검색할 수 있습니다. 다음 코드는 위에서 검색한 저장소 계정 개체를 사용하여 **CloudBlobClient** 개체를 만듭니다.

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

<h2> <a name="create-container"> </a><span  class="short-header">컨테이너 만들기</span>방법: 컨테이너 만들기</h2>


모든 저장소 Blob은 컨테이너에 있습니다. **CloudBlobClient** 개체를 사용하여 사용할 컨테이너에 대한 참조를 가져올 수 있습니다. 컨테이너가 없는 경우 새로 만들 수 있습니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve a reference to a container 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Create the container if it doesn't already exist
    container.CreateIfNotExist();

기본적으로 새 컨테이너는 전용이므로, 이 컨테이너에서 Blob을 다운로드하려면 위에서 수행한 방법으로 저장소 액세스 키를 지정해야 합니다. 컨테이너 내의 파일을 모든 사용자가 사용할 수 있게 하려는 경우 다음 코드를 사용하여 컨테이너를 공용으로 설정할 수 있습니다.

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
        BlobContainerPublicAccessType.Blob }); 

인터넷상의 누구든지 공용 컨테이너의 Blob을 볼 수 있지만 해당 액세스 키가 있는 경우에만 수정하거나 삭제할 수 있습니다.

<h2> <a name="upload-blob"> </a><span  class="short-header">컨테이너에 업로드</span>방법: 컨테이너에 Blob 업로드</h2>


Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 다음 이 참조를 사용하여 Blob 참조를 가져옵니다. Blob 참조가 있는 경우 Blob 참조에서 **UploadFromStream** 메서드를 호출하여 데이터 스트림을 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다. 아래 코드 샘플은 그 예시이며 컨테이너가 이미 만들어져 있다고 가정합니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Retrieve reference to a blob named "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");
    
    // Create or overwrite the "myblob" blob with contents from a local file
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blob.UploadFromStream(fileStream);
    } 

<h2> <a name="list-blob"> </a><span  class="short-header">컨테이너의 Blob 나열</span>방법: 컨테이너의 Blob 나열</h2>


컨테이너의 Blob을 나열하려면 먼저 컨테이너 참조를 가져옵니다. 컨테이너의 **ListBlobs** 메서드를 사용하여 컨테이너 내의 Blob을 검색할 수 있습니다. 다음 코드에서는 컨테이너에 있는 각 Blob의 **Uri**를 검색하고 출력하는 방법을 보여 줍니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Loop over blobs within the container and output the URI to each of them
    foreach (var blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    } 

Blob 서비스에도 컨테이너 내의 디렉터리 개념이 있습니다. 따라서 폴더와 유사한 구조로 Blob을 구성할 수 있습니다. 예를 들어 'photos'라는 컨테이너가 있고 이 컨테이너에서 'rootphoto1', '2010/photo1', '2010/photo2' 및 '2011/photo1' Blob을 업로드할 수 있습니다. 그러면 'photos' 컨테이너 내에 '2010' 및 '2011' 디렉터리가 실제로 만들어집니다. 'photos' 컨테이너에서 **ListBlobs**를 호출하면 반환된 컬렉션에 디렉터리를 나타내는 **CloudBlobDirectory** 및 **CloudBlob** 개체와 최상위 수준의 Blob이 포함됩니다. 이 경우 '2010' 및 '2011' 디렉터리와 'rootphoto1' 사진이 반환됩니다. 선택적으로 **UseFlatBlobListing**을 **true**로 설정하여 새 **BlobRequestOptions** 클래스에서 전달할 수 있습니다. 이 경우 디렉터리에 관계없이 모든 Blob이 반환됩니다. 자세한 내용은 [CloudBlobContainer.ListBlobs][]를 참조하십시오.

<h2> <a name="download-blobs"> </a><span  class="short-header">Blob 다운로드</span>방법: Blob 다운로드</h2>


Blob을 다운로드하려면 먼저 Blob 참조를 검색합니다. 다음 예제에서는 **DownloadToStream** 메서드를 사용하여 Blob 콘텐츠를 스트림 개체로 전송한 다음 이 개체를 로컬 파일에 저장할 수 있습니다. Blob의 **DownloadToFile**, **DownloadByteArray** 또는 **DownloadText** 메서드를 호출할 수도 있습니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Retrieve reference to a blob named "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");
    
    // Save blob contents to disk
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blob.DownloadToStream(fileStream);
    } 

<h2> <a name="delete-blobs"> </a><span  class="short-header">Blob 삭제</span>방법: Blob 삭제</h2>


마지막으로 Blob을 삭제하려면 Blob 참조를 가져온 다음 **Delete** 메서드를 호출합니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Retrieve reference to a blob named "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");
    
    // Delete the blob
    blob.Delete(); 

<h2> <a name="next-steps"></a><span  class="short-header">다음 단계</span>다음 단계</h2>


이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

*  사용 가능한 API에 대한 자세한 내용은 Blob 서비스 참조 설명서를 참조하십시오.
  *  [.NET 클라이언트 라이브러리 참조][2]
  *  [REST API 참조][3]
  

*  Azure 저장소를 사용하여 수행할 수 있는 고급 작업에 대한 자세한 내용은 [Azure에 데이터 저장 및 액세스][4]를 참조하십시오.
*  Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하십시오.
  *  [테이블 저장소](/en-us/develop/net/how-to-guides/table-services/)를 사용하여 구조화된 데이터를 저장합니다.
  *  [SQL 데이터베이스](/en-us/develop/net/how-to-guides/sql-database/)를 사용하여 관계형 데이터를 저장합니다.
  




[1]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee758697.aspx
[2]: http://msdn.microsoft.com/ko-kr/library/windowsazure/wl_svchosting_mref_reference_home
[3]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dd179355
[4]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx