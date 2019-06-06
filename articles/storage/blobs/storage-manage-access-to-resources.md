---
title: 컨테이너 및 Azure Blob Storage의 Blob에 대한 공용 읽기 권한을 사용하도록 설정 | Microsoft Docs
description: 컨테이너와 Blob에서 익명 액세스를 사용하도록 설정하는 방법 및 프로그래밍 방식으로 액세스하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: e0f93b0a95a228b26fae266129aea4b595b05e0f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148373"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>컨테이너 및 Blob에 대한 익명 읽기 권한 관리

컨테이너 및 Azure Blob Storage의 해당 Blob에 대한 익명의 공용 읽기 권한을 사용하도록 설정할 수 있습니다. 이렇게 하면 계정 키를 공유하지 않고 공유 액세스 서명(SAS)을 요구하지 않고도 이러한 리소스에 대해 읽기 전용 권한을 부여할 수 있습니다.

공용 읽기 권한은 특정 Blob을 항상 익명 읽기 권한에 사용할 수 있게 하려는 경우에 가장 적합합니다. 보다 세부적인 제어를 위해 공유 액세스 서명을 만들 수 있습니다. 공유 액세스 서명을 사용하면 특정 기간 동안 다양한 권한을 사용하여 제한된 액세스를 제공할 수 있습니다. 공유 액세스 서명 만들기에 대한 자세한 내용은 [Azure Storage에서 SAS(공유 액세스 서명) 사용](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>컨테이너 및 Blob에 익명의 사용자 권한 부여

기본적으로 컨테이너와 그 blob에 적절 한 권한이 부여 된 사용자에 의해서만 액세스할 수 있습니다. 컨테이너와 해당 blob에 대 한 익명 사용자에 게 읽기 액세스에 권한을 부여 하려면 컨테이너 공용 액세스 수준을 설정할 수 있습니다. 컨테이너에 대 한 공용 액세스를 부여 하는 경우 다음 익명 사용자가 읽을 수 공개적으로 액세스 가능한 컨테이너 내의 blob 요청을 승인 없이 합니다.

다음 권한으로 컨테이너를 구성할 수 있습니다.

* **공용 읽기 액세스 없음:** 컨테이너 및 blob 저장소 계정 소유자에 의해서만 액세스할 수 있습니다. 이것이 새로운 모든 컨테이너에 대한 기본 설정입니다.
* **Blob 전용 공용 읽기 권한:** 익명 요청을 통해 컨테이너 내의 blob을 읽을 수는 있지만 컨테이너 데이터를 사용할 수 없습니다. 익명 클라이언트는 컨테이너 내의 Blob을 열거할 수 없습니다.
* **공용은 컨테이너 및 해당 blob에 대 한 액세스 읽기:** 익명 요청으로 모든 컨테이너 및 blob 데이터를 읽을 수 있습니다. 클라이언트는 익명 요청을 통해 컨테이너 내에서 Blob을 열거할 수 있지만 저장소 계정 내에서 컨테이너를 열거할 수는 없습니다.

다음을 사용하여 컨테이너 권한을 설정할 수 있습니다.

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* 프로그래밍 방식으로, 저장소 클라이언트 라이브러리 중 하나 또는 REST API 사용

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Azure portal에서 컨테이너 공용 액세스 수준 설정

[Azure portal](https://portal.azure.com), 하나 이상의 컨테이너에 대 한 공용 액세스 수준을 업데이트할 수 있습니다.

1. Azure Portal의 저장소 계정으로 이동합니다.
1. 아래 **Blob service** 메뉴 블레이드에서 선택 **Blob**합니다.
1. 공용 액세스 수준을 설정 하려면 컨테이너를 선택 합니다.
1. 사용 된 **액세스 수준 변경** 공용 액세스 설정을 표시 하려면 단추입니다.
1. 적절된 한 공용 액세스 수준을 선택 합니다 **공용 액세스 수준을** 드롭다운 선택한 컨테이너 변경 내용을 적용 하려면 확인 단추를 클릭 합니다.

다음 스크린샷에서 선택한 컨테이너에 대 한 공용 액세스 수준을 변경 하는 방법을 보여 줍니다.

![포털에서 공용 액세스 수준을 설정 하는 방법을 보여 주는 스크린샷](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> 개별 blob에 대 한 공용 액세스 수준을 변경할 수 없습니다. 공용 액세스 수준은 컨테이너 수준 에서만 설정 됩니다.

### <a name="set-container-public-access-level-with-net"></a>.NET을 사용 하 여 컨테이너 공용 액세스 수준 설정

C# 및 .NET용 Storage 클라이언트 라이브러리를 사용하여 컨테이너에 대한 권한을 설정하려면 먼저 **GetPermissions** 메서드를 호출하여 컨테이너의 기존 권한을 검색합니다. 그런 다음 **GetPermissions** 메서드에 의해 반환된 **BlobContainerPermissions** 개체에 대해 **PublicAccess** 속성을 설정합니다. 마지막으로 업데이트된 권한으로 **SetPermissions** 메서드를 호출합니다.

다음 예제에서는 컨테이너의 권한을 전체 공용 읽기 권한으로 설정합니다. 권한을 Blob에 대해서만 공용 읽기 권한으로 설정하려면 **PublicAccess** 속성을 **BlobContainerPublicAccessType.Blob**으로 설정합니다. 익명 사용자에 대한 모든 권한을 제거하려면 속성을 **BlobContainerPublicAccessType.Off**로 설정합니다.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>컨테이너 및 Blob에 익명으로 액세스

컨테이너 및 Blob에 익명으로 액세스하는 클라이언트는 자격 증명을 필요로 하지 않는 생성자를 사용할 수 있습니다. 다음 예제에서는 컨테이너 및 blob을 익명으로 참조 하는 몇 가지를 보여 줍니다.

### <a name="create-an-anonymous-client-object"></a>익명 클라이언트 개체 만들기

계정에 대해 Blob storage 끝점을 제공 하 여 익명 액세스에 대 한 새 서비스 클라이언트 개체를 만들 수 있습니다. 그러나 익명 액세스에 사용할 수 있는 해당 계정의 컨테이너 이름도 알아야 합니다.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>컨테이너를 익명으로 참조

익명으로 사용할 수 있는 컨테이너에 대한 URL이 있는 경우 해당 URL을 사용하여 컨테이너를 직접 참조할 수 있습니다.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Blob을 익명으로 참조

익명 액세스에 사용할 수 있는 Blob에 대한 URL이 있는 경우 해당 URL을 사용하여 Blob을 직접 참조할 수 있습니다.

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>익명 사용자에게 제공되는 기능

다음 표에서 어떤 작업을 호출할 수 있습니다 익명으로 컨테이너를 구성할 때 공용 액세스용 보여 줍니다.

| REST 작업 | 컨테이너에 대 한 공용 읽기 액세스 | Blob에 대한 공용 읽기 액세스만 |
| --- | --- | --- |
| 컨테이너 나열 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| 컨테이너 만들기 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| 컨테이너 속성 가져오기 | 익명 요청 허용 | 권한 있는 요청에만 |
| 컨테이너 메타데이터 가져오기 | 익명 요청 허용 | 권한 있는 요청에만 |
| 컨테이너 메타데이터 설정 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| 컨테이너 ACL 가져오기 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| 컨테이너 ACL 설정 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| 컨테이너 삭제 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| Blob 나열 | 익명 요청 허용 | 권한 있는 요청에만 |
| Blob 배치 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| Blob 가져오기 | 익명 요청 허용 | 익명 요청 허용 |
| Blob 속성 가져오기 | 익명 요청 허용 | 익명 요청 허용 |
| Blob 속성 설정 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| Blob 메타데이터 가져오기 | 익명 요청 허용 | 익명 요청 허용 |
| Blob 메타데이터 설정 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| 블록 배치 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| 블록 목록 가져오기(커밋된 블록만) | 익명 요청 허용 | 익명 요청 허용 |
| 블록 목록 가져오기(커밋되지 않은 블록만 또는 모든 블록) | 권한 있는 요청에만 | 권한 있는 요청에만 |
| 블록 목록 배치 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| Blob 삭제 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| Blob 복사 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| Blob 스냅샷 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| Blob 임대 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| 페이지 가져오기 | 권한 있는 요청에만 | 권한 있는 요청에만 |
| 페이지 범위 가져오기 | 익명 요청 허용 | 익명 요청 허용 |
| Blob 추가 | 권한 있는 요청에만 | 권한 있는 요청에만 |

## <a name="next-steps"></a>다음 단계

* [Azure Storage 서비스에 대 한 권한 부여](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services)
* [SAS(공유 액세스 서명) 사용](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)