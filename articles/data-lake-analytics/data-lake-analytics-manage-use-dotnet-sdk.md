<properties 
   pageTitle="Azure .NET SDK를 사용하여 Azure Data Lake Analytics 관리 | Azure" 
   description="데이터 레이크 분석 작업, 데이터 원본, 사용자를 관리하는 방법에 대해 알아봅니다. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="jgao"/>

# Azure .NET SDK를 사용하여 Azure Data Lake Analytics 관리

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure .NET SDK를 사용하여 Azure Data Lake Analytics 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법에 대해 알아봅니다. 다른 도구를 사용하여 관리 항목을 보려면 위의 탭 선택을 클릭합니다.

**필수 구성 요소**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.


<!-- ################################ -->
<!-- ################################ -->


## Azure Data Lake Analytics에 연결

다음 NuGet 패키지를 설치해야 합니다.

	Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
	Install-Package Microsoft.Azure.Common 
	Install-Package Microsoft.Azure.Management.ResourceManager -Pre
	Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


다음 코드 샘플은 Azure에 연결하고 Azure 구독 아래에 기존 Data Lake Analytics 계정을 나열하는 방법을 보여 줍니다.

	using System;
	using System.Collections.Generic;
	using System.Threading;

	using Microsoft.Rest;
	using Microsoft.Rest.Azure.Authentication;

	using Microsoft.Azure.Management.ResourceManager;
	using Microsoft.Azure.Management.DataLake.Store;
	using Microsoft.Azure.Management.DataLake.Analytics;
	using Microsoft.Azure.Management.DataLake.Analytics.Models;

	namespace ConsoleAcplication1
	{
		class Program
		{

			private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
			private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
			private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

			private static DataLakeAnalyticsAccountManagementClient _adlaClient;

			private static void Main(string[] args)
			{

				var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

				_adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
				_adlaClient.SubscriptionId = SUBSCRIPTIONID;

				var adlaAccounts = ListADLAAccounts();

				Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
				for (int i = 0; i < adlaAccounts.Count; i ++)
				{
					Console.WriteLine(adlaAccounts[i].Name);
				}

				System.Console.WriteLine("Press ENTER to continue");
				System.Console.ReadLine();
			}

			public static ServiceClientCredentials AuthenticateAzure(
			string domainName,
			string nativeClientAppCLIENTID)
			{
				// User login via interactive popup
				SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
				// Use the client ID of an existing AAD "Native Client" application.
				var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
				return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
			}

			public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
			{
				var response = _adlaClient.Account.List();
				var accounts = new List<DataLakeAnalyticsAccount>(response);

				while (response.NextPageLink != null)
				{
					response = _adlaClient.Account.ListNext(response.NextPageLink);
					accounts.AddRange(response);
				}

				return accounts;
			}
		}
	}


## 계정 관리

데이터 레이크 분석 작업을 실행하려면 데이터 레이크 분석 계정이 있어야 합니다. Azure HDInsight와 달리 작업을 실행하지 않는 경우 분석 계정에 대해 비용을 지불하지 않습니다. 작업이 실행되는 시간에 대해서만 비용을 지불합니다. 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

###계정 만들기

다음 샘플을 실행하려면 Azure 리소스 관리 그룹과 Data Lake Store 계정이 있어야 합니다.

다음 코드는 리소스 그룹을 만드는 방법을 보여 줍니다.

	public static async Task<ResourceGroup> CreateResourceGroupAsync(
		ServiceClientCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{

		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential)
		{ SubscriptionId = subscriptionId };
		var resourceGroup = new ResourceGroup { Location = location };
		return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
	}

다음 코드는 Data Lake Store 계정을 만드는 방법을 보여 줍니다.

	var adlsParameters = new DataLakeStoreAccount(location: _location);
	_adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

다음 코드는 Data Lake Analytics 계정을 만드는 방법을 보여 줍니다.

	var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
	var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
	var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
	var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

###계정 나열

[Azure Data Lake Analytics에 연결](#connect_to_azure_data_lake_analytics)을 참조하세요.

###계정 찾기

Data Lake Analytics 계정 목록의 개체를 가져온 후 다음을 사용하여 한 계정을 찾을 수 있습니다.

	Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
	var myAdlaAccount = adlaAccounts.Find(accountFinder);

###데이터 레이크 분석 계정 삭제

다음 코드 조각은 Data Lake Analytics 계정을 삭제합니다.

	_adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## 계정 데이터 원본 관리

데이터 레이크 분석은 현재 다음 데이터 원본을 지원합니다.

- [Azure 데이터 레이크 저장소](../data-lake-store/data-lake-store-overview.md)
- [Azure 저장소](../storage/storage-introduction.md)

분석 계정을 만들 때 Azure 데이터 레이크 저장소 계정이 기본 저장소 계정이 되도록 지정해야 합니다. 기본 데이터 레이크 저장소 계정은 작업 메타데이터 및 작업 감사 로그를 저장하는 데 사용됩니다. 분석 계정을 만든 후 데이터 레이크 저장소 계정 및/또는 Azure 저장소 계정을 더 추가할 수 있습니다.

### 기본 데이터 레이크 저장소 계정 찾기

Data Lake Analytics 계정을 찾으려면 이 문서의 계정 찾기를 참조하세요. 그런 다음, 다음을 사용합니다.

	string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## Azure 리소스 관리자 그룹 사용

응용 프로그램은 일반적으로 웹앱, 데이터베이스, 데이터베이스 서버, 저장소 및 타사 서비스 등 많은 구성 요소로 구성됩니다. Azure Resource Manager를 사용하면 Azure 리소스 그룹이라고 하는 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트, 모니터링 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다. 배포용 템플릿을 사용하고 이 템플릿을 테스트, 스테이징 및 프로덕션과 같은 여러 환경에서 사용할 수 있습니다. 전체 그룹에 대한 롤업 비용을 확인하여 조직에 요금 청구를 명확히 할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자 개요](../resource-group-overview.md)를 참조하세요.

데이터 레이크 분석 서비스는 다음 구성 요소를 포함할 수 있습니다.

- Azure 데이터 레이크 분석 계정
- 필수 기본 Azure 데이터 레이크 저장소 계정
- 추가 Azure 데이터 레이크 저장소 계정
- 추가 Azure 저장소 계정

이러한 모든 구성을 쉽게 관리할 수 있도록 하나의 리소스 관리 그룹 아래 만들 수 있습니다.

![Azure 데이터 레이크 분석 계정 및 저장소](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

데이터 레이크 분석 계정 및 종속 저장소 계정은 동일한 Azure 데이터 센터에 있어야 합니다. 그러나 리소스 관리 그룹은 다른 데이터 센터에 있을 수 있습니다.

##참고 항목 

- [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
- [Azure Portal을 사용하여 Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)
- [Azure 포털을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)
- [Azure 포털을 사용하여 Azure Data Lake Analytics 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0928_2016-->