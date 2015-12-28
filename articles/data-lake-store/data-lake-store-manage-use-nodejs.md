<properties 
   pageTitle="Node.js용 Azure SDK를 사용하여 Azure 데이터 레이크 저장소 관리 | Azure" 
   description="데이터 레이크 저장소 계정 및 파일 시스템을 관리하는 방법을 알아봅니다." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Node.js용 Azure SDK를 사용하여 Azure 데이터 레이크 저장소 관리

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)


Node.js용 Azure ADK는 Azure 데이터 레이크 저장소 계정 및 파일 시스템 관리에 사용할 수 있습니다.

- 계정 관리: 만들기, 가져오기, 나열, 업데이트 및 삭제
- 파일 시스템 관리: 만들기, 가져오기, 업로드, 추가, 다운로드, 읽기, 삭제, 나열

**필수 구성 요소**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- **Azure 데이터 레이크 저장소 계정**. 계정을 만들려면 [Azure 포털을 사용하여 Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)을 참조하세요.
- **데이터 레이크 분석 계정에 액세스할 수 있는 권한이 있는 서비스 사용자**입니다. [Azure 리소스 관리자를 사용하여 서비스 사용자 인증](resource-group-authenticate-service-principal.md)을 참조하세요.

## SDK 설치

SDK를 설치하려면 다음 단계를 사용합니다.

1. [Node.js](https://nodejs.org/)를 설치합니다.
2. 명령 프롬프트, 터미널 또는 Bash 창에서 다음 명령을 실행합니다.

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-store
	
## Node.js 샘플

다음 예제에서는 데이터 레이크 저장소 계정에 파일을 만들고 여기에 데이터를 추가합니다.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeStore = require('azure-arm-datalake-store');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adls_resourcegroup_name';
	
	var accountName = 'adls_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
	var destinationFilePath = '/newFileName.txt';
	var content = 'desired file contents';
	
	async.series([
		function (next) {
			context.acquireTokenWithClientCredentials(resourceUri, clientId, clientSecret, function(err, result){
				if (err) throw err;
				response = result;
				next();
			});
		},
		function (next) {
			var credentials = new azureCommon.TokenCloudCredentials({
				subscriptionId : subscriptionId,
				authorizationScheme : response.tokenType,
				token : response.accessToken
			});
		
			client = azureDataLakeStore.createDataLakeStoreFileSystemManagementClient(credentials, 'azuredatalakestore.net');
	
			next();
		},
		function (next) {
			client.fileSystem.directCreate(destinationFilePath, accountName, content, function(err, result){
				if (err) throw err;
			});
		}
	]);


##참고 항목 

- [Node.js용 Azure SDK](http://azure.github.io/azure-sdk-for-node/)
- [Node.js를 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-use-nodejs.md)

<!---HONumber=AcomDC_1217_2015-->