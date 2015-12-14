<properties
    pageTitle="iOS에서 Azure Blob 저장소를 사용하는 방법 | Microsoft Azure"
    description="Azure Blob 저장소를 사용하여 Blob 콘텐츠를 업로드, 다운로드, 나열 및 삭제하는 방법을 알아봅니다. 샘플은 Objective-C로 작성되었습니다."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Objective-C"
    ms.topic="article"
    ms.date="10/07/2015"
    ms.author="micurd"/>

# iOS에서 Blob 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 개요

이 문서에서는 Microsoft Azure Blob 저장소를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Objective-C로 작성되었으며 [Azure 저장소 iOS 라이브러리](https://github.com/Azure/azure-storage-ios)를 사용합니다. Blob **업로드**, **나열**, **다운로드** 및 **삭제** 시나리오를 다룹니다. Blob에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오. 또한 [샘플 앱](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample)을 다운로드하여 iOS 응용 프로그램에서 Azure 저장소의 사용을 신속하게 볼 수 있습니다.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 프레임워크 파일 빌드
Azure 저장소 iOS 라이브러리를 사용하려면 먼저 프레임워크 파일을 빌드해야 합니다.

1. 먼저, [azure-storage-ios repo](https://github.com/azure/azure-storage-ios)를 다운로드하거나 복제합니다.

2. *azure-storage-ios* -> *Lib* -> *Azure 저장소 클라이언트 라이브러리*로 이동하고 X 코드에서 `Azure Storage Client Library.xcodeproj`를 엽니다.

3. Xcode의 왼쪽 위에서 "Azure 저장소 클라이언트 라이브러리"의 활성 구성표를 "프레임워크"로 변경합니다.

4. 프로젝트를 빌드합니다(⌘+B). 그러면 바탕 화면에 `Azure Storage Client Library.framework` 파일이 만들어집니다.

## Azure 저장소 iOS 라이브러리를 응용 프로그램으로 가져오기

다음을 수행하여 Azure 저장소 iOS 라이브러리를 응용 프로그램으로 가져올 수 있습니다.

1. 새 프로젝트를 만들거나 Xcode에서 기존 프로젝트를 엽니다.

2. 왼쪽의 탐색에서 프로젝트를 클릭하고 프로젝트 편집기의 위에서 *일반* 탭을 클릭합니다.

3. *연결된 프레임워크 및 라이브러리* 섹션 아래에서 추가 단추 (+)를 클릭합니다.

4. *기타 추가...*를 클릭합니다. 방금 만든 `Azure Storage Client Library.framework` 파일을 찾아 추가합니다.

5. *연결된 프레임워크 및 라이브러리* 섹션 아래에서 추가 단추 (+)를 다시 클릭합니다.

6. 이미 제공된 라이브러리 목록에서 `libxml2.2.dylib`를 검색하고 프로젝트에 추가합니다.

##Import 문
Azure 저장소 API를 호출하려는 파일에 다음 import 문을 포함해야 합니다.

    // Include the following import statement to use blob APIs.
    #import <Azure Storage Client Library/Azure_Storage_Client_Library.h>

## Blob 저장소에 액세스하도록 응용 프로그램 구성

저장소 서비스에 액세스하려면 응용 프로그램을 인증하는 두 가지 방법이 있습니다.

- 공유 키: 테스트 목적으로만 공유 키 사용
- 공유 액세스 서명(SAS): 프로덕션 응용 프로그램에 대해 SAS 사용

###공유 키
공유 키 인증은 응용 프로그램이 계정 이름과 계정 키를 사용하여 저장소 서비스에 액세스하는 것을 의미합니다. 이 시작 설명서에서는 iOS에서 Blob 저장소를 사용하는 방법을 신속하게 표시하는 용도로 공유 키 인증을 사용합니다.

> [AZURE.WARNING (Only use Shared Key authentication for testing purposes!) ]연결된 저장소 계정에 대한 전체 읽기/쓰기 액세스를 부여하는 계정 이름 및 계정 키가 앱을 다운로드한 모든 사람에게 배포됩니다. 이 방법은 신뢰할 수 없는 클라이언트가 키를 손상시킬 수 있는 위험이 있으므로 좋은 방법은 **아닙니다**.

공유 키 인증을 사용하면 연결 문자열이 만들어집니다. 연결 문자열은 다음과 같이 구성됩니다.

- **DefaultEndpointsProtocol** - HTTP 또는 HTTPS를 선택할 수 있습니다. 그러나 HTTPS를 사용하는 것이 좋습니다.
- **계정 이름** - 저장소 계정의 이름입니다.
- **계정 키** - [Azure 포털](portal.azure.com)을 사용 중인 경우 저장소 계정으로 이동하고 **키** 아이콘을 클릭하여 이 정보를 찾습니다. [Azure 클래식 포털](manage.windowsazure.com)을 사용하는 경우 포털에서 저장소 계정으로 이동하고 **액세스 키 관리**를 클릭합니다. 

응용 프로그램에서의 모양은 다음과 같습니다.

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=account_name;AccountKey=account_key"];

###공유 액세스 서명(SAS)
iOS 응용 프로그램에서 Blob 저장소에 대해 클라이언트의 요청을 인증하는 데 권장되는 방법은 공유 액세스 서명(SAS)을 사용하는 것입니다. SAS를 사용하면 지정된 사용 권한 집합과 함께 지정된 기간 동안 리소스에 대한 클라이언트 액세스를 부여할 수 있습니다. 저장소 계정 소유자는 iOS 클라이언트에서 사용할 SAS를 생성해야 합니다. SAS를 생성하려면 클라이언트로 배포되는 SAS를 생성하는 별도의 서비스를 작성할 수 있습니다. 테스트를 위해 Azure CLI를 사용하여 SAS를 생성할 수도 있습니다. 공유 키 자격 증명이 SAS를 생성하는 데 사용되지만 클라이언트는 SAS URL에 캡슐화된 인증 정보를 사용하여 생성된 SAS 사용할 수 있습니다. SAS를 만들 때, SAS가 유효한 시간 간격 및 SAS가 클라이언트에 부여하는 사용 권한을 지정할 수 있습니다. 예를 들어 Blob 컨테이너에 대해, SAS는 컨테이너에서 Blob에 대한 읽기, 쓰기 또는 삭제 권한을 부여하고 컨테이너에서 Blob를 나열하는 나열 권한을 부여할 수 있습니다.

다음 예제에서는 Azure CLI를 사용하여 2015년 9월 5일 오전 12:00시(UTC)까지, 컨테이너 *sascontainer*에 대한 읽기 및 쓰기 권한을 부여하는 SAS 토큰을 생성하는 방법을 보여 줍니다.

1. 먼저 이 [가이드](../xplat-cli/#how-to-install-the-azure-cli)를 따라 Azure CLI를 설치하고 Azure 구독에 연결하는 방법을 알아봅니다.

2. 그런 다음 Azure CLI에서 다음 명령을 입력하여 사용자 계정에 대한 연결 문자열을 가져옵니다.

		azure storage account connectionstring show youraccountname

3. 방금 생성된 연결 문자열로 환경 변수를 만듭니다.

		export AZURE_STORAGE_CONNECTION_STRING=”your connection string”

4. SAS URL을 생성합니다.

		azure storage container sas create --container sascontainer --permissions rw --expiry 2015-09-05T00:00:00

5. SAS URL은 다음과 유사하게 나타납니다.

		https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

6. iOS 응용 프로그램에서 다음과 같은 방식으로 SAS URL을 사용하여 이제 컨테이너에 대한 참조를 가져올 수 있습니다.

		// Get a reference to a container in your Storage account
    	AZSCloudBlobContainer *blobContainer = [[AZSCloudBlobContainer alloc] initWithUrl:[NSURL URLWithString:@" your SAS URL"]];

보시는 바와 같이 SAS 토큰을 사용하는 경우, iOS 응용 프로그램에서 계정 이름 및 계정 키가 노출되지 않습니다. [공유 액세스 서명 자습서](../storage-dotnet-shared-access-signature-part-1)를 확인하여 SAS에 대해 자세히 알아볼 수 있습니다.

##비동기 작업
> [AZURE.NOTE]서비스에 대한 요청을 수행하는 모든 메서드는 비동기 작업입니다. 코드 샘플에서 이러한 메서드에는 완료 처리기가 있음을 확인할 수 있습니다. 완료 처리기 내에 있는 코드는 요청이 완료된 **후** 실행됩니다. 완료 처리기 이후 코드는 요청이 이루어지는 **동안** 실행됩니다.

## 컨테이너 만들기
Azure 저장소의 모든 Blob는 컨테이너에 있어야 합니다. 다음 예제에서는 *newcontainer*라는 컨테이너를 저장소 계정에 만드는 방법을 보여 줍니다(아직 없는 경우). 컨테이너에 대한 이름을 선택할 때 위에서 언급한 명명 규칙을 따릅니다.

     -(void)createContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

[Azure 포털](portal.azure.com) 또는 [저장소 탐색기](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)를 확인하고 저장소 계정에 대한 컨테이너 목록에 *newcontainer*가 있는지 확인하여 이 작업을 확인할 수 있습니다.

## 컨테이너 사용 권한 설정
기본적으로 컨테이너의 사용 권한은 **개인** 액세스용으로 구성됩니다. 그러나 컨테이너는 컨테이너 액세스에 대한 몇 가지 다른 옵션을 제공합니다.

- **개인**: 계정 소유자만 컨테이너 및 Blob 데이터를 읽을 수 있습니다.

- **Blob**: 이 컨테이너 내의 Blob 데이터는 익명 요청을 통해 읽을 수 있으나 컨테이너 데이터는 읽을 수 없습니다. 클라이언트는 익명 요청을 통해 컨테이너 내의 Blob을 열거할 수 없습니다.

- **컨테이너**: 익명 요청을 통해 컨테이너와 Blob 데이터를 읽을 수 있습니다. 클라이언트는 익명 요청을 통해 컨테이너 내에서 Blob을 열거할 수 있지만 저장소 계정 내에서 컨테이너를 열거할 수는 없습니다.

다음 예제에서는 인터넷에서 모든 사용자의 공용, 읽기 전용 액세스를 허용할 **컨테이너** 액세스 권한으로 컨테이너를 만드는 방법을 보여 줍니다.

     -(void)createContainerWithPublicAccess{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## 컨테이너에 Blob 업로드
[Blob 서비스 개념](#blob-service-concepts) 섹션에서 설명한 것처럼 Blob 저장소는 블록 Blob, 추가 Blob, 페이지 Blob의 서로 다른 Blob 유형을 제공합니다. 현재 Azure 저장소 iOS 라이브러리는 블록 Blob만 지원합니다. 대부분의 경우 블록 Blob을 사용하는 것이 좋습니다.

다음 예제에서는 NSString에서 블록 Blob를 업로드하는 방법을 보여줍니다. 같은 이름의 Blob가 이 컨테이너에 이미 있는 경우 이 Blob의 내용을 덮어씁니다.

     -(void)uploadBlobToContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
         	 else{
				 // Create a local blob object
             	 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

             	 // Upload blob to Storage
             	 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                 	if (error){
						NSLog(@"Error in creating blob.");
                 	}             
             	 }];
			 }
         }];
     }

[Azure 포털](portal.azure.com) 또는 [저장소 탐색기](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)를 확인하고 컨테이너 *containerpublic*이 Blob *sampleblob*을 포함하는지 확인하여 이 작업을 확인할 수 있습니다. 이 샘플에서는 공용 컨테이너를 사용했으므로 Blob URI로 이동하여 작업을 확인할 수도 있습니다.

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

NSString에서 블록 Blob를 업로드하는 것 외에도 이와 유사한 메서드가 NSData, NSInputStream 또는 로컬 파일에 존재합니다.

## 컨테이너의 Blob 나열
다음 예제에서는 컨테이너의 모든 Blob를 나열하는 방법을 보여줍니다. 이 작업을 수행할 때는 다음 매개 변수를 염두에 두어야 합니다.

- **continuationToken** - 연속 토큰은 목록 작업을 시작할 위치를 나타냅니다. 토큰이 제공되지 않는 경우 처음부터 Blob를 나열합니다. 0에서 최대 설정까지 개수에 관계 없이 Blob를 나열할 수 있습니다. 이 메서드가 0개의 결과를 반환하더라도 `results.continuationToken`이 nil이 아니면 서비스에 나열되지 않은 더 많은 Blob이 있을 수 있습니다.
- **prefix** - Blob 목록에 사용할 접두사를 지정할 수 있습니다. 이 접두사로 시작하는 Blob만 나열됩니다.
- **useFlatBlobListing** - [컨테이너 및 Blob 이름 명명 및 참조](#naming-and-referencing-containers-and-blobs) 섹션에서 설명한 것처럼 Blob 서비스가 플랫 저장소 스키마인 경우에도 경로 정보로 Blob 이름을 지정하여 가상 계층 구조를 만들 수 있습니다. 그러나 현재는 플랫이 아닌 목록은 지원되지 않으며 곧 제공될 예정입니다. 지금은 이 값이 `YES`여야 합니다.
- **blobListingDetails** - Blob을 나열할 때 포함할 항목을 지정할 수 있습니다.
	- `AZSBlobListingDetailsNone`: 커밋된 Blob만 나열하고 Blob 메타데이터는 반환하지 않습니다.
	- `AZSBlobListingDetailsSnapshots`: 커밋된 Blob 및 Blob 스냅숏을 나열합니다.
	- `AZSBlobListingDetailsMetadata`: 목록에 반환된 각 Blob에 대한 Blob 메타데이터를 검색합니다.
	- `AZSBlobListingDetailsUncommittedBlobs`: 커밋 및 커밋되지 않은 Blob을 나열합니다.
	- `AZSBlobListingDetailsCopy`: 목록에 복사 속성을 포함합니다.
	- `AZSBlobListingDetailsAll`: 사용 가능한 모든 커밋된 Blob, 커밋되지 않은 Blob 및 스냅숏을 나열하고 모든 메타데이터와 해당 Blob에 대한 복사 상태를 반환합니다.
- **maxResults** - 이 작업에 대해 반환할 결과의 최대 수입니다. 제한을 설정하지 않으려면 -1을 사용합니다.
- **completionHandler** - 나열 작업의 결과와 함께 실행할 코드 블록입니다.

이 예제에서는 연속 토큰이 반환될 때마다 나열 Blob 메서드를 재귀적으로 호출하는 데 도우미 메서드가 사용됩니다.

    -(void)listBlobsInContainer{
      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      //List all blobs in container
      [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
         if (error != nil){
             NSLog(@"Error in creating container.");
         }
      }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
      [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
         if (error)
         {
             completionHandler(error);
         }
         else
         {
             for (int i = 0; i < results.blobs.count; i++) {
                 NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
             }
             if (results.continuationToken)
             {
                 [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
             }
             else
             {
                 completionHandler(nil);
             }
         }
      }];
    }

## Blob 다운로드

다음 예제에서는 NSString 개체로 Blob를 다운로드하는 방법을 보여줍니다.

     -(void)downloadBlobToString{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob    
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
			else{
            	NSLog(@"%@",text);
			}
        }];
    }

## Blob 삭제

다음 예제에서는 Blob를 삭제하는 방법을 보여줍니다.

     -(void)deleteBlob{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
          if (error) {
            NSLog(@"Error in deleting blob.");
          }
        }];
     }

## Blob 컨테이너 삭제

다음 예제에서는 컨테이너를 삭제하는 방법을 보여줍니다.

     -(void)deleteContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Delete container
        [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
            if(error){
                NSLog(@"Error in deleting container");
            }
        }];
    }

## 다음 단계

이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀더 복잡한 저장소 작업에 대해 알아보세요.

- [Azure 저장소 iOS 라이브러리]
- [Azure 저장소 REST API]
- [Azure 저장소 팀 블로그]

이 라이브러리에 대한 문의 사항이 있는 경우 [MSDN Azure 포럼](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) 또는 [스택 오버플로](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files)에 자유롭게 게시해 주세요. Azure 저장소에 대한 기능 제안 사항이 있는 경우 [Azure 저장소 피드백](http://feedback.azure.com/forums/217298-storage)에 게시해 주세요.

[Azure 저장소 iOS 라이브러리]: https://github.com/azure/azure-storage-ios
[Azure 저장소 REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage

<!---HONumber=AcomDC_1203_2015-->