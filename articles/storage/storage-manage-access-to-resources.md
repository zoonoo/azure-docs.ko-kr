<properties 
	pageTitle="Azure 저장소 리소스에 대한 액세스 관리 | Microsoft Azure" 
	description="사용자가 Azure 저장소 리소스에 액세스하는 방식을 관리하는 방법에 대해 알아봅니다." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="jdial" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="micurd;tamram"/>

# Azure 저장소 리소스에 대한 액세스 관리

## 개요

기본적으로는 저장소 계정 소유자만 해당 계정 내의 저장소 리소스에 액세스할 수 있습니다. 액세스 키를 공유하지 않고 서비스 또는 응용 프로그램이 다른 클라이언트에 이러한 리소스를 제공해야 하는 경우에는 액세스를 허용하는 데 다음 옵션을 사용할 수 있습니다.

- 컨테이너와 해당 Blob에 대한 익명 읽기 권한을 허용하도록 컨테이너 권한을 설정할 수 있습니다. 테이블 또는 큐에 대해서는 이렇게 할 수 없습니다.

- 공유 액세스 서명을 통해 리소스를 표시할 수 있습니다. 그러면 리소스를 사용할 수 있는 간격과 클라이언트의 리소스에 대한 권한을 지정하여 컨테이너, Blob, 테이블 또는 큐 리소스에 대한 제한된 액세스 권한을 위임할 수 있습니다.

- 저장된 액세스 정책을 사용하여 컨테이너/해당 Blob, 큐 또는 테이블용 공유 액세스 서명을 관리할 수 있습니다. 저장된 액세스 정책을 통해 공유 액세스 서명을 추가로 제어할 수 있을 뿐 아니라 서명을 간편하게 해지할 수도 있습니다.

## 컨테이너 및 Blob에 대한 액세스 제한

기본적으로는 저장소 계정 소유자만 컨테이너 및 해당 컨테이너 내의 모든 Blob에 액세스할 수 있습니다. 익명 사용자에게 컨테이너와 해당 Blob에 대한 읽기 권한을 제공하려면 공용 액세스를 허용하도록 컨테이너 권한을 설정할 수 있습니다. 그러면 익명 사용자가 요청을 인증하지 않고도 공개적으로 액세스 가능한 컨테이너 내의 Blob을 읽을 수 있습니다.

컨테이너는 컨테이너 액세스를 관리하기 위한 다음 옵션을 제공합니다.

- **전체 공개 읽기 액세스:** 익명 요청을 통해 컨테이너와 Blob 데이터를 읽을 수 있습니다. 클라이언트는 익명 요청을 통해 컨테이너 내에서 Blob을 열거할 수 있지만 저장소 계정 내에서 컨테이너를 열거할 수는 없습니다.

- **Blob에 대해서만 공개 읽기 액세스:** 이 컨테이너 내의 Blob 데이터는 익명 요청을 통해 읽을 수 있으나 컨테이너 데이터는 읽을 수 없습니다. 클라이언트는 익명 요청을 통해 컨테이너 내의 Blob을 열거할 수 없습니다.

- **공개 읽기 액세스 권한 없음:** 계정 소유자만 컨테이너 및 Blob 데이터를 읽을 수 있습니다.

>[AZURE.NOTE]서비스에서 Blob 리소스를 보다 세부적으로 제어하도록 요구하는 경우 또는 읽기 작업 이외의 작업을 위한 권한을 제공하려는 경우에는 공유 액세스 서명을 사용하여 사용자가 리소스에 액세스할 수 있도록 지정할 수 있습니다.

### 익명 사용자에게 제공되는 기능
아래 표에는 컨테이너 ACL이 공용 액세스를 허용하도록 설정되어 있을 때 익명 사용자가 호출할 수 있는 작업이 나와 있습니다.

| REST 작업 | 전체 공용 읽기가 가능한 권한 | Blob 전용 공용 읽기가 가능한 권한 |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| 컨테이너 나열 | 소유자만 | 소유자만 |
| 컨테이너 만들기 | 소유자만 | 소유자만 |
| 컨테이너 속성 가져오기 | 모두 | 소유자만 |
| 컨테이너 메타데이터 가져오기 | 모두 | 소유자만 |
| 컨테이너 메타데이터 설정 | 소유자만 | 소유자만 |
| 컨테이너 ACL 가져오기 | 소유자만 | 소유자만 |
| 컨테이너 ACL 설정 | 소유자만 | 소유자만 |
| 컨테이너 삭제 | 소유자만 | 소유자만 |
| Blob 나열 | 모두 | 소유자만 |
| Blob 배치 | 소유자만 | 소유자만 |
| Blob 가져오기 | 모두 | 모두 |
| Blob 속성 가져오기 | 모두 | 모두 |
| Blob 속성 설정 | 소유자만 | 소유자만 |
| Blob 메타데이터 가져오기 | 모두 | 모두 |
| Blob 메타데이터 설정 | 소유자만 | 소유자만 |
| 블록 배치 | 소유자만 | 소유자만 |
| 블록 목록 가져오기(커밋된 블록만) | 모두 | 모두 |
| 블록 목록 가져오기(커밋되지 않은 블록만 또는 모든 블록) | 소유자만 | 소유자만 |
| 블록 목록 배치 | 소유자만 | 소유자만 |
| Blob 삭제 | 소유자만 | 소유자만 |
| Blob 복사 | 소유자만 | 소유자만 |
| Blob 스냅숏 | 소유자만 | 소유자만 |
| Blob 임대 | 소유자만 | 소유자만 |
| 페이지 가져오기 | 소유자만 | 소유자만 |
| 페이지 범위 가져오기 | 모두 | 모두 |

## 공유 액세스 서명 만들기 및 사용
공유 액세스 서명은 특정 시간 간격 동안 컨테이너, Blob, 큐 및 테이블에 대한 제한적 액세스 권한을 부여하는 URI입니다. 클라이언트에 공유 액세스 서명을 제공하면 계정 키를 공유하지 않고도 클라이언트가 저장소 계정의 리소스에 액세스하도록 설정할 수 있습니다.

>[AZURE.NOTE]공유 액세스 서명에 대한 자세한 개념 개요 및 자습서는 [공유 액세스 서명](storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

공유 액세스 서명을 사용하여 수행할 수 있는 작업은 다음과 같습니다.

- 페이지 또는 블록 Blob 콘텐츠, 블록 목록, 속성 및 메타데이터 읽기/쓰기

- Blob 스냅숏 삭제, 임대, 만들기

- 컨테이너 내의 Blob 나열

- 저장소 클라이언트 라이브러리 2.0 이상 버전에서 큐 메시지 추가, 제거, 업데이트, 삭제

- 저장소 클라이언트 라이브러리 2.0 이상 버전에서 메시지 수를 포함한 큐 메타데이터 가져오기

- 저장소 클라이언트 라이브러리 2.0 이상 버전에서 테이블 엔터티 쿼리, 추가, 업데이트, 삭제, 분리

공유 액세스 서명 URI 쿼리 매개 변수는 저장소 리소스에 대한 제어된 액세스 권한을 부여하는 데 필요한 모든 정보를 통합합니다. URI 쿼리 매개 변수는 공유 액세스 서명이 유효한 시간 간격, 해당 서명이 부여하는 권한, 제공하려는 리소스, 그리고 저장소 서비스에서 요청을 인증하는 데 사용해야 하는 서명을 지정합니다.

또한 공유 액세스 서명 URI는 필요한 경우 리소스 액세스 권한을 수정하거나 해지하는 기능을 비롯하여 서명 집합에 대한 추가적인 제어 수준을 제공하는 저장된 액세스 정책을 참조할 수도 있습니다.

공유 액세스 서명의 URI 형식에 대한 자세한 내용은 [공유 액세스 서명을 사용하여 액세스 위임](https://msdn.microsoft.com/library/ee395415.aspx)을 참조하세요.

### 공유 액세스 서명의 안전한 사용
공유 액세스 서명은 URI가 부여하는 권한을 통해 지정되는 리소스에 대한 액세스 권한을 부여합니다. 항상 HTTPS를 사용하여 공유 액세스 서명 URI를 생성해야 합니다. 공유 액세스 서명에 HTTP를 사용하면 저장소 계정을 악의적인 용도로 사용하기가 쉬워집니다.

공유 액세스 서명이 일반 사용자용이 아닌 액세스 권한을 부여하는 경우에는 최대한 적은 권한을 포함하여 서명을 생성해야 합니다. 또한 공유 액세스 서명은 보안 연결을 통해 클라이언트에 안전하게 배포해야 하고 해지용으로 저장된 액세스 정책에 연결해야 하며 서명에 대해 최대한 짧은 수명을 지정해야 합니다.

>[AZURE.NOTE]공유 액세스 서명 URI는 서명을 만드는 데 사용된 계정 키 및 저장된 관련 액세스 정책(있는 경우)에 연결됩니다. 저장된 액세스 정책을 지정하지 않는 경우 공유 액세스 서명을 해지하는 방법은 계정 키를 변경하는 것뿐입니다.

### 공유 액세스 서명 만들기
다음 코드 예제에서는 컨테이너에 액세스 정책을 만든 다음 컨테이너에 대한 공유 액세스 서명을 생성합니다. 이 공유 액세스 서명을 클라이언트에 제공할 수 있습니다.

    // The connection string for the storage account.  Modify for your account.
    string storageConnectionString =
       "DefaultEndpointsProtocol=https;" +
       "AccountName=myaccount;" +
       "AccountKey=<account-key>";
    
    // As an alternative, you can retrieve storage account information from an app.config file. 
    // This is one way to store and retrieve a connection string if you are 
    // writing an application that will run locally, rather than in Microsoft Azure.
    
    // string storageConnectionString = ConfigurationManager.AppSettings["StorageAccountConnectionString"];
    
    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Create blob container permissions, consisting of a shared access policy 
    // and a public access setting. 
    BlobContainerPermissions blobPermissions = new BlobContainerPermissions();
    
    // The shared access policy provides 
    // read/write access to the container for 10 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don’t set start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
       Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
    });
    
    // The public access setting explicitly specifies that 
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Set the permission policy on the container.
    container.SetPermissions(blobPermissions);
    
    // Get the shared access signature to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

### 공유 액세스 서명 사용
공유 액세스 서명을 받은 클라이언트는 코드에서 해당 서명을 사용하여 [StorageCredentials](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.auth.storagecredentials.aspx) 형식의 개체를 생성할 수 있습니다. 이러한 자격 증명을 사용하여 아래 예제와 같이 리소스에 사용할 수 있는 [CloudStorageAccount](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.cloudstorageaccount.aspx) 또는 [CloudBlobClient](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx) 개체를 생성할 수 있습니다.

    Uri blobUri = new Uri("https://myaccount.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Upload the blob. 
    // If the blob does not yet exist, it will be created. 
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Test\myblob.txt"))
    {
    blob.UploadFromStream(fileStream);
    }

## 저장된 액세스 정책 사용
저장된 액세스 정책은 서버 쪽의 공유 액세스 서명에 대해 추가적인 제어 수준을 제공합니다. 저장된 액세스 정책을 설정하면 공유 액세스 서명을 그룹화하고 해당 정책이 적용되는 서명을 추가로 제한할 수 있습니다. 저장된 액세스 정책을 사용하여 서명의 시작 시간/만료 시간/권한을 변경하거나 발급된 서명을 해지할 수 있습니다.

저장된 액세스 정책을 통해 해제한 공유 액세스 서명을 보다 세부적으로 제어할 수 있습니다. URL에서 서명의 수명과 권한을 지정하는 대신 공유 중인 Blob, 컨테이너, 큐 또는 테이블에 저장한 저장된 액세스 정책 내에서 이러한 매개 변수를 지정할 수 있습니다. 서명 하나 이상에 대해 이러한 매개 변수를 변경하려는 경우 서명을 다시 발급하는 대신 저장된 액세스 정책을 수정하면 됩니다. 저장된 액세스 정책을 수정하여 서명을 빠르게 해지할 수도 있습니다.

저장된 액세스 정책과 연결된 공유 액세스 서명을 발급한 경우를 예로 들어 보겠습니다. 저장된 액세스 정책 내에서 만료 시간을 지정한 경우에는 새 서명을 다시 발급하지 않고 서명의 수명을 연장하도록 액세스 정책을 수정할 수 있습니다.

모범 사례에 따르면, 공유 액세스 서명을 발급하는 서명된 리소스에 대해 저장된 액세스 정책을 지정하는 것이 좋습니다. 저장된 정책을 사용하면 서명을 발급한 후 수정하거나 해지할 수 있기 때문입니다. 저장된 정책을 지정하지 않는 경우에는 저장소 계정 리소스에 대한 위험을 최소화할 수 있도록 서명의 수명을 제한하는 것이 좋습니다.

### 공유 액세스 서명을 저장된 액세스 정책에 연결
저장된 액세스 정책에는 컨테이너, 큐 또는 테이블 내에서 고유한 최대 64자의 이름이 포함됩니다. 공유 액세스 서명을 저장된 액세스 정책에 연결하려면 공유 액세스 서명을 만들 때 이 식별자를 지정합니다. 공유 액세스 서명 URI에서 *signedidentifier* 필드는 저장된 액세스 정책의 식별자를 지정합니다.

컨테이너, 큐 또는 테이블에는 최대 5개의 저장된 액세스 정책이 포함될 수 있습니다. 숫자에 상관없이 여러 공유 액세스 서명이 각 정책을 사용할 수 있습니다.

>[AZURE.NOTE]컨테이너, 큐 또는 테이블에 대해 저장된 액세스 정책을 설정할 때는 정책이 적용될 때까지 최대 30초가 걸릴 수 있습니다. 이 간격 동안에는 액세스 정책이 활성화될 때까지 저장된 액세스 정책과 연결된 공유 액세스 서명에서 오류가 발생하고 상태 코드 403(사용 권한 없음)이 반환됩니다.

### 공유 액세스 정책에 대한 액세스 정책 매개 변수 지정
저장된 액세스 정책은 연결된 서명에 대해 다음과 같은 액세스 정책 매개 변수를 지정할 수 있습니다.

- 시작 시간

- 만료 시간

- 권한

저장소 리소스 액세스를 제어하려는 방법에 따라 저장된 액세스 정책 내에서 이러한 매개 변수를 모두 지정하고 공유 액세스 서명의 URL에서는 생략할 수 있습니다. 이렇게 하면 언제든지 연결된 서명의 동작을 수정하고 서명을 해지할 수 있습니다. 저장된 액세스 정책 내에서 액세스 정책 매개 변수를 하나 이상 지정하고 나머지 매개 변수는 URL에서 지정할 수도 있습니다. 마지막으로 모든 매개 변수를 URL에서 지정할 수 있습니다. 이 경우 저장된 액세스 정책을 사용하여 서명을 해지할 수는 있지만 서명의 동작을 수정할 수는 없습니다.

서명을 인증하는 데 필요한 모든 필드를 공유 액세스 서명과 저장된 액세스 정책에 포함해야 합니다. 필수 필드가 누락된 경우 요청은 실패하며 상태 코드 403(사용 권한 없음)이 반환됩니다. 마찬가지로 공유 액세스 서명 URL과 저장된 액세스 정책에서 모두 하나의 필드를 지정하면 요청이 실패하며 상태 코드 403(잘못된 요청)이 반환됩니다. 서명을 구성하는 필드에 대한 자세한 내용은 공유 액세스 서명 만들기 및 사용을 참조하세요.

### 저장된 액세스 정책 수정 또는 해지

동일한 저장된 액세스 정책을 사용하는 공유 액세스 서명에 대한 액세스를 해지하려면 저장된 정책 목록을 정책 이름이 들어 있지 않은 새 목록으로 덮어써서 저장소 리소스에서 저장된 정책을 제거합니다. 저장된 액세스 정책의 액세스 설정을 변경하려면 저장된 정책 목록을 새 액세스 제어 세부 정보가 포함된 같은 이름의 정책이 들은 새 목록으로 덮어씁니다.

## 참고 항목

- [Azure 저장소 서비스에 대한 인증](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [공유 액세스 서명: SAS 모델 이해하기](storage-dotnet-shared-access-signature-part-1.md)
- [공유 액세스 서명을 사용하여 액세스 위임](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=July15_HO4-->