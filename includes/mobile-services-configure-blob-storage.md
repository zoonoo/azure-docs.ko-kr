새 할 일 항목이 삽입되면 SAS를 생성하는 새 삽입 스크립트가 등록됩니다.

1.  저장소 계정을 만들지 않았다면 [저장소 계정을 만드는 방법](/en-us/manage/services/storage/how-to-create-a-storage-account)을 참조하십시오.

2.  관리 포털에서 **Storage**를 클릭하고 저장소 계정을 클릭한 후 **Manage Keys**를 클릭합니다.

 	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png) 

1.  **Storage Account Name**과 **Access Key**를 적어 두십시오.
    
   	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

2.  모바일 서비스에서 **Configure** 탭을 클릭하고 **App settings**까지 아래로 스크롤하여 다음에 대해 저장소 계정에서 얻은 각 **Name**과 **Value** 쌍을 입력한 후 **Save**를 클릭합니다.
    * `STORAGE_ACCOUNT_NAME`
    * `STORAGE_ACCOUNT_ACCESS_KEY`
    
    ![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)
    
    저장소 계정 액세스 키는 암호화되어 앱 설정에 저장됩니다. 런타임 시 서버 스크립트에서 이 키에 액세스할 수 있습니다. 자세한 정보는 [앱 설정][1]을 참조하십시오.

3.  **Data** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.
    
       ![](./media/mobile-services-configure-blob-storage/mobile-portal-data-tables.png)

4.  **Todoitem**에서 **Script** 탭을 클릭하고 **Insert**를 선택하여 삽입 함수를 다음 코드로 바꾼 다음 **Save**를 클릭합니다.
    
        var azure = require('azure');
        var qs = require('querystring');
        var appSettings = require('mobileservice-config').appSettings;
        	
        function insert(item, user, request) {
            // 앱 설정에서 저장소 계정 설정을 가져옵니다. 
            var accountName = appSettings.STORAGE_ACCOUNT_NAME;
            var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
            var host = accountName + '.blob.core.windows.net';
        	
            if ((typeof item.containerName !== "undefined") && (
            item.containerName !== null)) {
                // 해당 항목의 Blob 저장소 컨테이너 이름을 설정합니다. 이름은 소문자여야 합니다.
                item.containerName = item.containerName.toLowerCase();
        	
                // 컨테이너가 기존에 없었다면 
                // Blob에 대해 public 읽기 권한이 있는 컨테이너를 만듭니다.        
                var blobService = azure.createBlobService(accountName, accountKey, host);
                blobService.createContainerIfNotExists(item.containerName, {
                    publicAccessLevel: 'blob'
                }, function(error) {
                    if (!error) {
        	
                        // 5분 동안 컨테이너에 쓰기 권한을 제공합니다.        
                        var sharedAccessPolicy = {
                            AccessPolicy: {
                                Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
                                Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
                            }
                        };
        	
                        // 새 이미지에 대한 SAS가 있는 업로드 URL을 만듭니다.
                        var sasQueryUrl = 
                        blobService.generateSharedAccessSignature(item.containerName, 
                        item.resourceName, sharedAccessPolicy);
        	
                        // 쿼리 문자열을 설정합니다.
                        item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
        	
                        // 클라이언트에서 데이터 바인딩에 사용되는 
                        // 새 항목의 전체 경로를 설정합니다. 
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

	이 작업을 수행하면 TodoItem 테이블에서 삽입할 때 호출되는 함수가 새 스크립트로 대체됩니다. 새 스크립트는 삽입을 위해 5분 동안 유효한 새 SAS를 생성하고, 생성된 SAS 값을 반환된 값의 `sasQueryString` 속성에 할당합니다. `imageUri` 속성은 클라이언트 UI에서 바인딩할 때 이미지를 표시하도록 새 Blob의 리소스 경로로 설정됩니다.
     >[WACOM.NOTE] 이 코드는 각 Blob의 SAS를 생성합니다. 같은 SAS를 사용하여 한 컨테이너에 복수의 Blob을 업로드해야 하는 경우 다음과 같이 빈 리소스 이름으로 <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">generateSharedAccessSignature 메서드</a>(영문)를 대신 호출할 수 있습니다. 
    <pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

다음 과정에서는 삽입 시 생성된 SAS를 사용하여 이미지 업로드 기능을 추가하는 빠른 시작 앱을 업데이트합니다.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7