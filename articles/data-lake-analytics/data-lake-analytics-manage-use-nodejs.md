<properties 
   pageTitle="Node.js용 Azure SDK를 사용하여 Azure 데이터 레이크 분석 관리 | Azure" 
   description="Node.js용 Azure SDK를 사용하여 데이터 레이크 분석 계정, 데이터 원본, 작업 및 사용자를 관리하는 방법을 알아봅니다." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Node.js용 Azure SDK를 사용하여 Azure 데이터 레이크 분석 관리


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Node.js용 Azure ADK는 Azure 데이터 레이크 분석 계정, 작업 및 카탈로그 관리에 사용할 수 있습니다. 다른 도구를 사용하여 관리 항목을 보려면 위의 탭 선택을 클릭합니다.

**필수 구성 요소**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- **Azure 데이터 레이크 분석 계정**. 계정을 만들려면 [Azure 포털을 사용하여 Azure 데이터 레이크 분석 시작](data-lake-analytics-get-started-portal.md)을 참조하세요.
- **데이터 레이크 분석 계정에 액세스할 수 있는 권한이 있는 서비스 사용자**입니다. [Azure 리소스 관리자를 사용하여 서비스 사용자 인증](resource-group-authenticate-service-principal.md)을 참조하세요.

## SDK 설치

SDK를 설치하려면 다음 단계를 사용합니다.

1. [Node.js](https://nodejs.org/)를 설치합니다.
2. 명령 프롬프트, 터미널 또는 Bash 창에서 다음 명령을 실행합니다.

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-analytics
	
## Node.js 샘플

다음 예제에서는 지정된 Azure 데이터 레이크 분석 계정에 대한 작업 목록을 가져옵니다.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeAnalytics = require('azure-arm-datalake-analytics');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adla_resourcegroup_name';
	
	var accountName = 'adla_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
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
		
			client = azureDataLakeAnalytics.createDataLakeAnalyticsJobManagementClient(credentials, 'azuredatalakeanalytics.net');
	
			next();
		},
		function (next) {
			client.jobs.list(resourceGroup, accountName, function(err, result){
				if (err) throw err;
				console.log(result);
			});
		}
	]);


##참고 항목 

- [Node.js용 Azure SDK](http://azure.github.io/azure-sdk-for-node/)
- [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
- [Azure 포털을 사용하여 데이터 레이크 분석 시작](data-lake-analytics-get-started-portal.md)
- [Azure 포털을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-use-portal.md)
- [Azure 포털을 사용하여 Azure 데이터 레이크 분석 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_1217_2015-->