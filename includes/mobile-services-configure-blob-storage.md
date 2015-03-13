새 할 일 항목이 삽입되면 SAS를 생성하는 새 삽입 스크립트가 등록됩니다.

0. 저장소 계정을 만들지 않았다면 [저장소 계정을 만드는 방법]을 참조하세요.

1. 관리 포털에서 **저장소**를 클릭하고 저장소 계정을 클릭한 후 **키 관리**를 클릭합니다. 

  	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png)

2. **저장소 계정 이름**과 **액세스 키**를 적어 둡니다.

   	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

3. 모바일 서비스에서 **구성** 탭을 클릭하고 **앱 설정**까지 아래로 스크롤하여 다음에 대해 저장소 계정에서 얻은 각 **이름** 및 **값** 쌍을 입력한 후 **저장**을 클릭합니다.

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	저장소 계정 액세스 키는 암호화되어 앱 설정에 저장됩니다. 런타임 시 서버 스크립트에서 이 키에 액세스할 수 있습니다. 자세한 내용은 [앱 설정]을 참조하세요.

4. **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다. 

   	![](./media/mobile-services-configure-blob-storage/mobile-portal-data-tables.png)

5.  **Todoitem**에서 **Script** 탭을 클릭하고 **Insert**를 선택하여 삽입 함수를 다음 코드로 바꾼 다음 **Save**를 클릭합니다.

		var azure = require('azure');
		var qs = require('querystring');
		var appSettings = require('mobileservice-config').appSettings;
		
		function insert(item, user, request) {
		    // Get storage account settings from app settings. 
		    var accountName = appSettings.STORAGE_ACCOUNT_NAME;
		    var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
		    var host = accountName + '.blob.core.windows.net';
		
		    if ((typeof item.containerName !== "undefined") && (
		    item.containerName !== null)) {
		        // Set the BLOB store container name on the item, 이때 대/소문자를 구분해야 합니다.
		        item.containerName = item.containerName.toLowerCase();
		
		        // If it does not already exist, create the container 
		        // with public read access for blobs.        
		        var blobService = azure.createBlobService(accountName, accountKey, host);
		        blobService.createContainerIfNotExists(item.containerName, {
		            publicAccessLevel: 'blob'
		        }, function(error) {
		            if (!error) {
		
		                // Provide write access to the container for the next 5 mins.        
		                var sharedAccessPolicy = {
		                    AccessPolicy: {
		                        Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
		                        Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
		                    }
		                };
		
		                // Generate the upload URL with SAS for the new image.
		                var sasQueryUrl = 
		                blobService.generateSharedAccessSignature(item.containerName, 
		                item.resourceName, sharedAccessPolicy);
		
		                // Set the query string.
		                item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
		
		                // Set the full path on the new new item, 
		                // which is used for data binding on the client. 
		                item.imageUri = sasQueryUrl.baseUrl + sasQueryUrl.path;
		
		            } else {
		                console.error(error);
		            }
		            request.execute();
		        });
		    } else {
		        request.execute();
		    }
		}

 	![](./media/mobile-services-configure-blob-storage/mobile-insert-script-blob.png)

   	이 작업을 수행하면 TodoItem 테이블에서 삽입할 때 호출되는 함수가 새 스크립트로 대체됩니다. 새 스크립트는 삽입을 위해 5분 동안 유효한 새 SAS를 생성하고, 생성된 SAS 값을 반환된 값의  `sasQueryString` 속성에 할당합니다.  `imageUri` 속성은 클라이언트 UI에서 바인딩할 때 이미지를 표시하도록 새 Blob의 리소스 경로로 설정됩니다.

	>[AZURE.NOTE] 이 코드는 각 BLOB의 SAS를 생성합니다. 동일한 SAS를 사용하여 컨테이너에 여러 Blob을 업로드해야 하는 경우 다음과 유사하게 빈 Blob 리소스 이름을 사용하여 <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">generateSharedAccessSignature 메서드</a>를 호출할 수 있습니다. 
	<pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

다음 과정에서는 삽입 시 생성된 SAS를 사용하여 이미지 업로드 기능을 추가하는 빠른 시작 앱을 업데이트합니다.
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[저장소 계정을 만드는 방법]: /ko-kr/manage/services/storage/how-to-create-a-storage-account
[앱 설정]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
<!--HONumber=42-->
