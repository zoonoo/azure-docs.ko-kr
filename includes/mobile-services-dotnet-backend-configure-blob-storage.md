
##<a name="storage-client-server"></a>모바일 서비스 프로젝트에 저장소 클라이언트 설치

SAS를 생성하여 Blob 저장소에 이미지를 업로드하려면 먼저 모바일 서비스 프로젝트에 저장소 클라이언트 라이브러리를 설치하는 NuGet 패키지를 추가해야 합니다.

1. Visual Studio의 **솔루션 탐색기**에서 모바일 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

2. 왼쪽 창에서 **온라인** 범주를 선택하고, **Stabile Only**를 선택하고 **WindowsAzure.Storage**를 검색한 후 **Azure 저장소** 패키지에서 **설치**를 클릭한 다음, 사용권 계약에 동의합니다.

  	![](./media/mobile-services-configure-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Azure 저장소 서비스용 클라이언트 라이브러리가 모바일 서비스 프로젝트에 추가됩니다.

##<a name="update-data-model"></a>데이터 모델에서 TodoItem 정의 업데이트

TodoItem 클래스는 데이터 개체를 정의하고, 클라이언트에서 추가한 것과 동일한 속성을 이 클래스에 추가해야 합니다.

1. Visual Studio 2013에서 모바일 서비스 프로젝트를 열고 DataObjects 폴더를 확장한 후 TodoItem.cs 프로젝트 파일을 엽니다.
	
2. 다음과 같은 새 속성을 **TodoItem** 클래스에 추가합니다.

        public string containerName { get; set; }
		public string resourceName { get; set; }
		public string sasQueryString { get; set; }
		public string imageUri { get; set; } 

	이러한 속성은 SAS를 생성하고 이미지 정보를 저장하는 데 사용됩니다. 이러한 속성에 대한 대/소문자 구분은 JavaScript 백 엔드 버전과 일치합니다.

	>[AZURE.NOTE]기본 데이터베이스 이니셜라이저를 사용할 경우 Entity Framework에서는 Code First 모델 정의에서 데이터 모델 변경이 감지될 때 데이터베이스를 삭제하고 다시 만듭니다. 이 데이터 모델을 변경하고 데이터베이스에서 기존 데이터를 유지하려면 Code First 마이그레이션을 사용해야 합니다. Azure에서는 SQL 데이터베이스에 대해 기본 이니셜라이저를 사용할 수 없습니다. 자세한 내용은 [Code First 마이그레이션을 사용하여 데이터 모델을 업데이트하는 방법](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)을 참조하십시오.

##<a name="update-scripts"></a>TodoItem 컨트롤러를 업데이트하여 공유 액세스 서명 생성 

새 TodoItem 항목이 삽입되면 **PostTodoItem** 메서드에서 SAS를 생성되도록 기존 **TodoItemController**가 업데이트되었습니다. 또한

0. 저장소 계정을 만들지 않았다면 [저장소 계정을 만드는 방법]을 참조하세요.

1. 관리 포털에서 **저장소**를 클릭하고 저장소 계정을 클릭한 후 **키 관리**를 클릭합니다.

2. **저장소 계정 이름**과 **액세스 키**를 적어 두세요.
 
3. 모바일 서비스에서 **구성** 탭을 클릭하고 **앱 설정**까지 아래로 스크롤하여 다음에 대해 저장소 계정에서 얻은 각 **이름** 및 **값** 쌍을 입력한 후 **저장**을 클릭합니다.

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	저장소 계정 액세스 키는 암호화되어 앱 설정에 저장됩니다. 런타임 시 서버 스크립트에서 이 키에 액세스할 수 있습니다. 자세한 내용은 [앱 설정]을 참조하세요.

4. Visual Studio의 솔루션 탐색기에서 모바일 서비스 프로젝트의 Web.config 파일을 열고 다음과 같은 새 앱 설정을 추가하여 자리 표시자를 포털에서 방금 설정한 저장소 계정 이름 및 액세스 키로 바꿉니다.

		<add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
		<add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

	로컬 컴퓨터에서 실행하는 모바일 서비스는 이렇게 저장된 설정을 사용하므로 해당 서비스를 게시하기 전에 코드를 테스트할 수 있습니다. 그러나 Azure에서 실행하는 모바일 서비스는 포털의 앱 설정 값을 사용하고 이러한 프로젝트 설정을 무시합니다.

7.  Controllers 폴더에서 TodoItemController.cs 파일을 열고 다음 **using** 지시문을 추가합니다.

		using System;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
  
8.  기존 **PostTodoItem** 메서드를 다음 코드로 바꿉니다.

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            string storageAccountName;
            string storageAccountKey;

            // Try to get the Azure storage account token from app settings.  
            if (!(Services.Settings.TryGetValue("STORAGE_ACCOUNT_NAME", out storageAccountName) |
            Services.Settings.TryGetValue("STORAGE_ACCOUNT_ACCESS_KEY", out storageAccountKey)))
            {
                Services.Log.Error("Could not retrieve storage account settings.");
            }

            // Set the URI for the Blob Storage service.
            Uri blobEndpoint = new Uri(string.Format("https://{0}.blob.core.windows.net", storageAccountName));

            // Create the BLOB service client.
            CloudBlobClient blobClient = new CloudBlobClient(blobEndpoint, 
                new StorageCredentials(storageAccountName, storageAccountKey));

            if (item.containerName != null)
            {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.ToLower();

                // Create a container, if it doesn't already exist.
                CloudBlobContainer container = blobClient.GetContainerReference(item.containerName);
                await container.CreateIfNotExistsAsync();

                // Create a shared access permission policy. 
                BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

                // Enable anonymous read access to BLOBs.
                containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
                container.SetPermissions(containerPermissions);

                // Define a policy that gives write access to the container for 5 minutes.                                   
                SharedAccessBlobPolicy sasPolicy = new SharedAccessBlobPolicy()
                {
                    SharedAccessStartTime = DateTime.UtcNow,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddMinutes(5),
                    Permissions = SharedAccessBlobPermissions.Write
                };

                // Get the SAS as a string.
                item.sasQueryString = container.GetSharedAccessSignature(sasPolicy); 

                // Set the URL used to store the image.
                item.imageUri = string.Format("{0}{1}/{2}", blobEndpoint.ToString(), 
                    item.containerName, item.resourceName);
            }

            // Complete the insert operation.
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

   	이제 이 POST 메서드는 삽입을 위해 5분 동안 유효한 새 SAS를 생성하고, 생성된 SAS 값을 반환된 항목의 `sasQueryString` 속성에 할당합니다. `imageUri` 속성은 클라이언트 UI에서 바인딩할 때 이미지를 표시하도록 새 Blob의 리소스 경로로도 설정됩니다.

	>[AZURE.NOTE]이 코드는 각 BLOB의 SAS를 생성합니다. 동일한 SAS를 사용하여 컨테이너에 여러 Blob을 업로드해야 하는 경우 빈 Blob 리소스 이름을 사용하여 <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">generateSharedAccessSignature method</a>를 호출할 수 있습니다. 다음과 같습니다. <pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

다음 과정에서는 삽입 시 생성된 SAS를 사용하여 이미지 업로드 기능을 추가하는 퀵 스타트 앱을 업데이트합니다.
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[저장소 계정을 만드는 방법]: ../articles/storage/storage-create-storage-account.md
[앱 설정]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=July15_HO1-->