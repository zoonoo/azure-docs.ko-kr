---
title: "Azure Blob 저장소 및 Visual Studio 연결된 서비스 시작(ASP.NET) | Microsoft Docs"
description: "Visual Studio 연결된 서비스를 사용하여 저장소 계정에 연결한 후 Visual Studio의 ASP.NET 프로젝트에서 Azure Blob 저장소를 사용하는 방법에 대해 설명합니다."
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: c9b16bd5cd3dc2429a30fd30f9ec942c568abe42
ms.openlocfilehash: 802249883c6adf3fedc9873e35fb64a9085d905b


---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Azure Blob 저장소 및 Visual Studio 연결된 서비스 시작(ASP.NET)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>개요

Azure Blob 저장소는 구조화되지 않은 데이터를 개체/Blob으로 클라우드에 저장하는 서비스입니다. Blob 저장소는 문서, 미디어 파일 또는 응용 프로그램 설치 프로그램과 같은 모든 종류의 텍스트 또는 이진 데이터를 저장할 수 있습니다. 또한 Blob 저장소를 개체 저장소라고 합니다.

이 자습서에서는 Azure Blob 저장소를 사용하여 몇 가지 일반적인 시나리오를 위한 ASP.NET 코드를 작성하는 방법을 보여 줍니다. 시나리오에는 Blob 컨테이너 만들기, Blob 업로드, 나열, 다운로드 및 삭제가 포함됩니다.

##<a name="prerequisites"></a>필수 조건

* [Microsoft Visual Studio](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)
* [Azure 저장소 계정](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>MVC 컨트롤러 만들기 

1. **솔루션 탐색기**에서 **컨트롤러**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->컨트롤러**를 차례로 선택합니다.

    ![ASP.NET MVC 앱에 컨트롤러 추가](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. **스캐폴드 추가** 대화 상자에서 **MVC 5 컨트롤러 - 비어 있음**을 선택하고 **추가**를 선택합니다.

    ![MVC 컨트롤러 유형 지정](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. **컨트롤러 추가** 대화 상자에서 컨트롤러 이름을*BlobsController*로 설정하고 **추가**를 선택합니다.

    ![MVC 컨트롤러 이름 지정](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. 다음 *using* 지시문을 `BlobsController.cs` 파일에 추가합니다.

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="create-a-blob-container"></a>Blob 컨테이너 만들기

Blob 컨테이너는 Blob 및 폴더의 중첩된 계층 구조입니다. 다음 단계에서는 Blob 컨테이너를 만드는 방법을 보여 줍니다.

1. `BlobsController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **CreateBlobContainer**라는 메서드를 추가합니다.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **CreateBlobContainer** 메서드 내에서 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. *&lt;storage-account-name>*을 액세스 중인 Azure 저장소 계정의 이름으로 변경합니다.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Blob service 클라이언트를 나타내는 **CloudBlobClient** 개체를 가져옵니다.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. 원하는 Blob 컨테이너 이름에 대한 참조를 나타내는 **CloudBlobContainer** 개체를 가져옵니다. **CloudBlobClient.GetContainerReference** 메서드는 Blob 저장소에 대한 요청을 만들지 않습니다. Blob 컨테이너가 있는지 여부에 관계없이 참조가 반환됩니다. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. 컨테이너가 아직 없으면 **CloudBlobContainer.CreateIfNotExists** 메서드를 호출하여 해당 컨테이너를 만듭니다. 컨테이너가 없고 성공적으로 만들어지면 **CloudBlobContainer.CreateIfNotExists** 메서드에서 **true**를 반환합니다. 그렇지 않으면 **false**를 반환합니다.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. **ViewBag**을 Blob 컨테이너의 이름으로 업데이트합니다.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```

1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **Blobs**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **CreateBlobContainer**를 입력하고 **추가**를 선택합니다.

1. `CreateBlobContainer.cshtml`을 열고 다음과 같이 수정합니다.

    ```csharp
    @{
        ViewBag.Title = "Create blob container";
    }
    
    <h2>Create blob container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. 응용 프로그램을 실행하고 **Blob 컨테이너 만들기**를 선택합니다. 다음 스크린샷과 비슷한 결과가 표시됩니다. 
  
    ![Blob 컨테이너 만들기](./media/vs-storage-aspnet-getting-started-blobs/results.png)

    앞에서 언급했듯이 컨테이너가 없고 만들어진 경우에만 **CloudBlobContainer.CreateIfNotExists** 메서드에서 **true**를 반환합니다. 따라서 컨테이너가 있을 때 앱을 실행하면 메서드에서 **false**를 반환합니다. 앱을 여러 번 실행하려면 앱을 다시 실행하기 전에 컨테이너를 삭제해야 합니다. 컨테이너 삭제는 **CloudBlobContainer.Delete** 메서드를 통해 수행할 수 있습니다. 또한 [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) 또는 [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 컨테이너를 삭제할 수도 있습니다.  

## <a name="upload-a-blob-into-a-blob-container"></a>Blob 컨테이너에 Blob 업로드

[Blob 컨테이너를 만들면](#create-a-blob-container) 해당 컨테이너에 파일을 업로드할 수 있습니다. 이 섹션에서는 Blob 컨테이너에 로컬 파일을 업로드하는 방법을 안내합니다. 이 단계에서는 *test-blob-container*라는 이름의 Blob 컨테이너를 만들었다고 가정합니다. 이 이름을 Blob 컨테이너의 이름으로 변경해야 합니다. 

1. `BlobsController.cs` 파일을 엽니다.

1. **EmptyResult**를 반환하는 **UploadBlob**라는 메서드를 추가합니다.

    ```csharp
    public EmptyResult UploadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. **UploadBlob** 메서드 내에서 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. *&lt;storage-account-name>*을 액세스 중인 Azure 저장소 계정의 이름으로 변경합니다.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Blob service 클라이언트를 나타내는 **CloudBlobClient** 개체를 가져옵니다.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Blob 컨테이너 이름에 대한 참조를 나타내는 **CloudBlobContainer** 개체를 가져옵니다. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. 앞에서 설명한 대로 Azure 저장소는 다양한 Blob 유형을 지원합니다. 페이지 Blob에 대한 참조를 검색하려면 **CloudBlobContainer.GetPageBlobReference** 메서드를 호출합니다. 블록 Blob에 대한 참조를 검색하려면 **CloudBlobContainer.GetBlockBlobReference** 메서드를 호출합니다. 일반적으로 블록 Blob을 사용하는 것이 좋습니다. 업로드하면 *<blob-name>*을 Blob에 부여할 이름으로 변경합니다.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Blob 참조가 있으면 Blob 참조 개체의 **UploadFromStream** 메서드를 호출하여 데이터 스트림을 업로드할 수 있습니다. **UploadFromStream** 메서드는 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다. *&lt;file-to-upload>*를 업로드하려는 파일의 정규화된 경로로 변경합니다.

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(<file-to-upload>))
    {
        blob.UploadFromStream(fileStream);
    }
    ```

1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **Blobs**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. 응용 프로그램을 실행하고 **Blob 업로드**를 선택합니다.  
  
다음 섹션에서는 Blob 컨테이너에 Blob을 나열하는 방법에 대해 설명합니다.     

## <a name="list-the-blobs-in-a-blob-container"></a>Blob 컨테이너에 Blob 나열

이 섹션에서는 Blob 컨테이너에 Blob을 나열하는 방법을 보여 줍니다. 샘플 코드는 [Blob 컨테이너 만들기](#create-a-blob-container) 섹션에서 만든 *test-blob-container*를 참조합니다.

1. `BlobsController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **ListBlobs**라는 메서드를 추가합니다.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **ListBlobs** 메서드 내에서 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. *&lt;storage-account-name>*을 액세스 중인 Azure 저장소 계정의 이름으로 변경합니다.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Blob service 클라이언트를 나타내는 **CloudBlobClient** 개체를 가져옵니다.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Blob 컨테이너 이름에 대한 참조를 나타내는 **CloudBlobContainer** 개체를 가져옵니다. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Blob 컨테이너에 Blob을 나열하려면 **CloudBlobContainer.ListBlobs** 메서드를 사용합니다. **CloudBlobContainer.ListBlobs** 메서드는 **CloudBlockBlob**, **CloudPageBlob** 또는 **CloudBlobDirectory** 개체에 캐스팅하는 **IListBlobItem** 개체를 반환합니다. 다음 코드 조각에서는 Blob 컨테이너에 모든 Blob을 열거하고, 해당 유형을 기반으로 하여 반환된 개체를 적절한 개체로 캐스팅하며, 표시될 수 있는 목록에 이름(또는 **CloudBlobDirectory**의 경우 URI)을 추가합니다.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    Blob 컨테이너는 Blob 외에도 디렉터리를 포함할 수 있습니다. 다음 계층 구조를 가진 *test-blob-container*라는 Blob 컨테이너가 있다고 가정해 보겠습니다.

        foo.png
        dir1/bar.png
        dir2/baz.png

    앞의 코드 예제를 사용하면 **Blob** 문자열 목록에 다음과 비슷한 값이 포함됩니다.

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    여기서 볼 수 있듯이 목록에는 중첩된 엔터티가 아닌 최상위 엔터티만 포함됩니다(*bar.png* 및 *baz.png*). Blob 컨테이너 내의 모든 엔터티를 나열하려면 **CloudBlobContainer.ListBlobs** 메서드를 호출하고 **useFlatBlobListing** 매개 변수에 대해 **true**를 전달해야 합니다.    

    ```csharp
    ...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    ...
    ```

    **useFlatBlobListing** 매개 변수를 **true**로 설정하면 Blob 컨테이너의 모든 항목에 대한 플랫 목록이 반환되고 다음 결과가 생성됩니다.

        foo.png
        dir1/bar.png
        dir2/baz.png

1. **솔루션 탐색기**에서 **보기** 폴더를 확장한 다음 **Blobs**를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **추가->보기**를 차례로 선택합니다.

1. **보기 추가** 대화 상자에서 보기 이름으로 **ListBlobs**를 입력하고 **추가**를 선택합니다.

1. `ListBlobs.cshtml`을 열고 다음과 같이 수정합니다.

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. 응용 프로그램을 실행하고 **Blob 나열**을 선택합니다. 다음 스크린샷과 비슷한 결과가 표시됩니다. 
  
    ![Blob 나열](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Blob 다운로드

다음 단계에서는 Blob을 다운로드하고 로컬 저장소에 저장하거나 내용을 문자열로 읽는 방법을 보여 줍니다. 샘플 코드는 [Blob 컨테이너 만들기](#create-a-blob-container) 섹션에서 만든 *test-blob-container*를 참조합니다.

1. `BlobsController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **DownloadBlob**이라는 메서드를 추가합니다.

    ```csharp
    public EmptyResult DownloadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. **DownloadBlob** 메서드 내에서 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. *&lt;storage-account-name>*을 액세스 중인 Azure 저장소 계정의 이름으로 변경합니다.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Blob service 클라이언트를 나타내는 **CloudBlobClient** 개체를 가져옵니다.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Blob 컨테이너 이름에 대한 참조를 나타내는 **CloudBlobContainer** 개체를 가져옵니다. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. **CloudBlobContainer.GetBlockBlobReference** 또는 **CloudBlobContainer.GetPageBlobReference** 메서드를 호출하여 Blob 참조 개체를 가져옵니다. *&lt;blob-name>*을 다운로드하는 Blob의 이름으로 변경합니다.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Blob을 다운로드하려면 Blob 유형에 따라 **CloudBlockBlob.DownloadToStream** 또는 **CloudPageBlob.DownloadToStream** 메서드를 사용합니다. 다음 코드 조각에서는 **CloudBlockBlob.DownloadToStream** 메서드를 사용하여 Blob의 내용을 스트림 개체로 전송하고 로컬 파일에 저장합니다. Blob을 다운로드하는 위치를 나타내는 정규화된 파일 이름으로 *&lt;local-file-name>*을 변경합니다. 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. 응용 프로그램을 실행하고 **Blob 다운로드**를 선택하여 Blob을 다운로드합니다. **CloudBlobContainer.GetBlockBlobReference** 메서드 호출에서 지정한 Blob은 **File.OpenWrite** 메서드 호출에서 지정한 위치로 다운로드됩니다. 

## <a name="delete-blobs"></a>Blob 삭제

다음 단계에서는 Blob을 삭제하는 방법을 보여 줍니다. 

1. `BlobsController.cs` 파일을 엽니다.

1. **ActionResult**를 반환하는 **DeleteBlob**이라는 메서드를 추가합니다.

    ```csharp
    public EmptyResult DeleteBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```

1. 저장소 계정 정보를 나타내는 **CloudStorageAccount** 개체를 가져옵니다. Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 가져오려면 다음 코드를 사용합니다. *&lt;storage-account-name>*을 액세스 중인 Azure 저장소 계정의 이름으로 변경합니다.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Blob service 클라이언트를 나타내는 **CloudBlobClient** 개체를 가져옵니다.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Blob 컨테이너 이름에 대한 참조를 나타내는 **CloudBlobContainer** 개체를 가져옵니다. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. **CloudBlobContainer.GetBlockBlobReference** 또는 **CloudBlobContainer.GetPageBlobReference** 메서드를 호출하여 Blob 참조 개체를 가져옵니다. *&lt;blob-name>*을 삭제하는 Blob의 이름으로 변경합니다.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
        ```

1. To delete a blob, use the **Delete** method.

    ```csharp
    blob.Delete();
    ```

1. **솔루션 탐색기**에서 **보기->공유됨** 폴더를 차례로 확장하고 `_Layout.cshtml`을 엽니다.

1. 마지막 **Html.ActionLink** 뒤에 다음 **Html.ActionLink**를 추가합니다.

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. 응용 프로그램을 실행하고 **Blob 삭제**를 선택하여 **CloudBlobContainer.GetBlockBlobReference** 메서드 호출에서 지정한 Blob을 삭제합니다. 

## <a name="next-steps"></a>다음 단계
Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하십시오.

  * [Azure 테이블 저장소 및 Visual Studio 연결된 서비스 시작(ASP.NET)](./vs-storage-aspnet-getting-started-tables.md)
  * [Azure 큐 저장소 및 Visual Studio 연결된 서비스 시작(ASP.NET)](./vs-storage-aspnet-getting-started-queues.md)


<!--HONumber=Dec16_HO3-->


