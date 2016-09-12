<properties
	pageTitle="배치 관리 .NET으로 계정 관리 | Microsoft Azure"
	description="응용 프로그램에서 배치 관리 .NET 라이브러리로 Azure 배치 계정을 만들고, 삭제하며 수정합니다."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="08/03/2016"
	ms.author="marsma"/>

# 배치 관리 .NET으로 Azure 배치 계정 및 할당량 관리

> [AZURE.SELECTOR]
- [Azure 포털](batch-account-create-portal.md)
- [배치 관리 .NET](batch-management-dotnet.md)

[배치 관리 .NET][api_mgmt_net] 라이브러리를 사용하여 배치 계정 만들기, 삭제, 키 관리 및 할당량 검색을 자동화하므로 Azure 배치 응용 프로그램에서 유지 관리 오버헤드를 낮출 수 있습니다.

- 모든 지역 내에서 **배치 계정을 만들고 삭제**합니다. 예를 들어 ISV(독립 소프트웨어 공급업체)가 대금 청구를 위해 각각 별도의 배치 계정에 할당되는 클라이언트용 서비스를 제공하는 경우 고객 포털에 계정 만들기 및 삭제 기능을 추가할 수 있습니다.
- 모든 배치 계정에 대해 프로그래밍 방식으로 **계정 키를 검색하고 다시 생성**합니다. 정기적인 롤오버 또는 계정 키의 만료를 적용할 수 있는 보안 정책에 대한 규정 준수를 유지하는 데 특히 유용합니다. 다양한 Azure 지역에 여러 배치 계정이 있는 경우 이 롤오버 프로세스를 자동화하면 솔루션의 효율성이 증가합니다.
- **계정 할당량을 확인**하고 어떤 배치 계정에 어떤 제한이 있는지를 확인하는 데 시행 착오 추측을 배제합니다. 작업을 시작하고 풀을 만들거나 계산 노드를 추가하기 전에 계정 할당량을 확인하여 이러한 계산 리소스를 만드는 위치 또는 시기를 사전에 조정할 수 있습니다. 해당 계정에 추가 리소스를 할당하기 전에 어떤 계정에 할당량 증가가 필요한지 확인할 수 있습니다.
- 배치 관리 .NET, [Azure Active Directory][aad_about] 및 [Azure Resource Manager][resman_overview]를 동일한 응용 프로그램에서 함께 활용하고 **다른 Azure 서비스의 기능을 결합**하여 모든 기능을 갖춘 관리 환경을 제공합니다. 이러한 기능과 해당 API를 사용하여 원활한 인증 환경, 리소스 그룹을 만들고 삭제하는 기능 및 종단 간 관리 솔루션에 대해 위에 설명된 기능을 제공할 수 있습니다.

> [AZURE.NOTE] 이 문서에서 배치 계정, 키 및 할당량을 프로그래밍 방식으로 관리하는 방법에 대해 주로 설명하는 동안 [Azure 포털][azure_portal]을 사용하여 이러한 다양한 작업을 수행할 수 있습니다. 자세한 내용은 [Azure 포털에서 Azure 배치 계정 만들기](batch-account-create-portal.md)와 [Azure 배치 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요.

## 배치 계정 만들기 및 삭제

위의 설명대로 배치 관리 API의 주요 기능은 Azure 지역 내에서 배치 계정을 만들고 삭제하는 것입니다. 이를 위해 [BatchManagementClient.Account.CreateAsync][net_create], [DeleteAsync][net_delete] 또는 해당 동기 항목을 사용합니다.

다음 코드 조각은 계정을 만들고 배치 서비스에서 새로 만든 계정을 가져온 후 삭제합니다. 이 코드와 이 문서의 다른 코드 조각에서 `batchManagementClient`는 [BatchManagementClient][net_mgmt_client]의 완전히 초기화된 인스턴스입니다.

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
	"mynewaccount",
	new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
	"MyResourceGroup",
	"mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] 배치 관리 .NET 라이브러리 및 해당 BatchManagementClient 클래스를 사용하는 응용 프로그램에는 관리할 배치 계정을 소유하는 구독에 대한 **서비스 관리자** 또는 **공동 관리자** 액세스 권한이 필요합니다. 자세한 내용은 아래 [Azure Active Directory](#azure-active-directory) 섹션과 [AccountManagement][acct_mgmt_sample] 코드 샘플을 참조하세요.

## 계정 키 검색 및 다시 생성

[ListKeysAsync][net_list_keys]를 사용하여 구독 내 배치 계정에서 기본 및 보조 계정 키를 가져옵니다. [RegenerateKeyAsync][net_regenerate_keys]를 사용하여 해당 키를 다시 생성할 수 있습니다.

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
	await batchManagementClient.Account.ListKeysAsync(
		"MyResourceGroup",
		"mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
	await batchManagementClient.Account.RegenerateKeyAsync(
		"MyResourceGroup",
		"mybatchaccount",
		new BatchAccountRegenerateKeyParameters() {
			KeyName = AccountKeyType.Primary
			});
```

> [AZURE.TIP] 관리 응용 프로그램에 대한 간소화된 연결 워크플로를 만들 수 있습니다. 먼저 [ListKeysAsync][net_list_keys]를 사용하여 관리하려는 배치 계정에 대한 계정 키를 가져옵니다. 그런 다음 [BatchClient][net_batch_client]를 초기화할 때 사용되는 배치 .NET 라이브러리의 [BatchSharedKeyCredentials][net_sharedkeycred] 클래스를 초기화할 때 이 키를 사용합니다.

## Azure 구독 및 배치 계정 할당량 확인

Azure 구독 및 배치와 같은 개별 Azure 서비스는 모두 포함되는 특정 엔터티 수를 제한하는 기본 할당량이 있습니다. Azure 구독에 대한 기본 할당량의 경우 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](./../azure-subscription-service-limits.md)을 참조하세요. 배치 서비스의 기본 할당량의 경우 [Azure 배치 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요. 배치 관리 .NET 라이브러리를 사용하여 응용 프로그램 내에서 이러한 할당량을 확인할 수 있습니다. 계정 또는 풀과 같은 계산 리소스 및 계산 노드를 추가하기 전에 할당 결정을 내릴 수 있습니다.

### Azure 구독에서 배치 계정 할당량 확인

지역에 배치 계정을 만들기 전에 Azure 구독에서 해당 지역에 계정을 추가할 수 있는지 여부를 확인할 수 있습니다.

아래 코드 조각에서 먼저 [BatchManagementClient.Account.ListAsync][net_mgmt_listaccounts]를 사용하여 구독 내에서 모든 배치 계정의 컬렉션을 가져옵니다. 이 컬렉션을 가져온 후 대상 영역의 계정 수를 결정합니다. 그런 다음 [BatchManagementClient.Subscriptions][net_mgmt_subscriptions]를 사용하여 배치 계정 할당량을 가져오고 해당 지역에서 얼마나 많은 계정(있는 경우)을 만들 수 있는지 결정합니다.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
		await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
	creds.SubscriptionId,
	accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

위의 코드 조각에서 `creds`는 [TokenCloudCredentials][azure_tokencreds]의 인스턴스입니다. 이 개체를 만드는 예제를 보려면 GitHub에서 [AccountManagement][acct_mgmt_sample] 코드 샘플을 참조하세요.

### 배치 계정에서 계산 리소스 할당량 확인

배치 솔루션 내에서 계산 리소스를 늘리기 전에 할당하려는 리소스가 현재 가능한 계정 할당량을 초과하지 않는지 확인할 수 있습니다. 아래 코드 조각에서 `mybatchaccount`(이)라는 배치 계정에 대한 할당량 정보를 간단히 인쇄합니다. 하지만 응용 프로그램에서 계정이 만들려는 추가 리소스를 처리할 수 있는지 여부를 확인하도록 이러한 정보를 사용할 수 있습니다.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
	await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Azure 구독 및 서비스에 기본 할당량이 있기는 하지만 [Azure 포털][azure_portal]에서 요청을 실행하여 이러한 여러 제한을 늘릴 수 있습니다. 예를 들어 배치 계정 할당량을 늘리는 방법에 대한 지침은 [Azure 배치 서비스에 대한 할당량 및 제한](batch-quota-limit.md)을 참조하세요.

## 배치 관리 .NET, Azure AD 및 리소스 관리자

일반적으로 배치 관리 .NET 라이브러리를 사용할 때는 [Azure Active Directory][aad_about](Azure AD) 및 [Azure Resource Manager][resman_overview]의 두 기능을 모두 활용합니다. 아래 설명된 샘플 프로젝트에서는 배치 관리 .NET API를 설명하는 동안 Azure Active Directory와 리소스 관리자를 모두 사용합니다.

### Azure Active Directory

Azure에서는 해당 고객, 서비스 관리자 및 조직 사용자의 인증을 위해 Azure AD를 자체적으로 사용합니다. 배치 관리 .NET의 컨텍스트에서 구독 관리자 또는 공동 관리자를 인증하는 데 사용합니다. 관리 라이브러리가 배치 서비스를 쿼리하고 이 문서에서 설명된 작업을 수행하도록 허용합니다.

아래 설명된 샘플 프로젝트에서 Azure [Active Directory Authentication Library][aad_adal](ADAL)는 사용자에게 Microsoft 자격 증명을 묻는 데 사용됩니다. 서비스 관리자 또는 공동 관리자 자격 증명을 제공하는 경우 응용 프로그램이 Azure에 구독 목록을 쿼리하고 리소스 그룹 및 배치 계정을 만들고 삭제할 수 있습니다.

### 리소스 관리자

배치 관리 .NET 라이브러리로 배치 계정을 만들 때는 일반적으로 [리소스 그룹][resman_overview] 내에서 만듭니다. [리소스 관리자 .NET][resman_api] 라이브러리의 [ResourceManagementClient][resman_client] 클래스를 사용하여 프로그래밍 방식으로 리소스 그룹을 만들 수 있습니다. 또는 [Azure 포털][azure_portal]을 사용하여 이전에 만든 기존 리소스 그룹에 계정을 추가할 수 있습니다.

## GitHub에서 샘플 프로젝트

실제 배치 관리 .NET 라이브러리를 보려면 GitHub에서 [AccountManagment][acct_mgmt_sample] 샘플 프로젝트를 참조하세요. 이 콘솔 응용 프로그램은 [BatchManagementClient][net_mgmt_client] 및 [ResourceManagementClient][resman_client]의 만들기 및 사용을 보여 줍니다. 또한 두 클라이언트 모두에서 필요한 Azure [Active Directory Authentication Library][aad_adal](ADAL)의 사용을 보여 줍니다.

응용 프로그램 예제를 성공적으로 실행하려면 먼저 Azure 포털을 사용하여 Azure AD에 등록해야 합니다. [Azure Active Directory와 응용 프로그램 통합][aad_integrate]의 [응용 프로그램 추가](../active-directory/active-directory-integrating-applications.md#adding-an-application) 단계에 따라 사용자 고유 계정의 기본 디렉터리 내에 샘플 응용 프로그램을 등록합니다. 응용 프로그램 유형으로 **네이티브 클라이언트 응용 프로그램**을 선택해야 합니다. **리디렉션 URI**에 유효한 URI(예: `http://myaccountmanagementsample`)를 지정할 수 있으며, 실제 끝점일 필요는 없습니다.

응용 프로그램을 추가한 후에는 포털의 응용 프로그램 설정에서 *Windows Azure Service Management API* 응용 프로그램에 **조직으로 Azure 서비스 관리에 액세스** 권한을 위임합니다.

![Azure 포털의 응용 프로그램 권한][2]

> [AZURE.TIP] *다른 응용 프로그램에 대한 권한* 아래에 **Windows Azure Service Management API**가 보이지 않으면 **응용 프로그램 추가**를 클릭하고, **Windows Azure Service Management API**를 선택한 다음 확인란 단추를 클릭합니다. 그런 다음 위에 지정된 대로 권한을 위임합니다.

위의 설명대로 응용 프로그램을 추가했으면 응용 프로그램의 리디렉션 URI 및 클라이언트 ID를 사용하여 [AccountManagment][acct_mgmt_sample] 샘플 프로젝트에서 `Program.cs`를 업데이트합니다. 응용 프로그램의 **구성** 탭에서 이러한 값을 찾을 수 있습니다.

![Azure 포털의 응용 프로그램 구성][3]

[AccountManagment][acct_mgmt_sample] 샘플 응용 프로그램은 다음 작업을 보여줍니다.

1. [ADAL][aad_adal]을 사용하여 Azure AD에서 보안 토큰을 획득합니다. 사용자가 아직 로그인하지 않은 경우 Azure 자격 증명을 묻는 메시지가 표시됩니다.
2. Azure AD에서 획득한 보안 토큰을 사용하여 [SubscriptionClient][resman_subclient]를 만들고 Azure에 해당 계정과 연결된 구독 목록을 쿼리합니다. 발견된 구독이 여러 개인 경우 사용자가 하나를 선택할 수 있도록 합니다.
3. 선택한 구독에 연결된 새 자격 증명 개체를 만듭니다.
4. 새 자격 증명을 사용하여 [ResourceManagementClient][resman_client]를 만듭니다.
5. [ResourceManagementClient][resman_client]를 사용하여 새 리소스 그룹을 만듭니다.
6. [BatchManagementClient][net_mgmt_client]를 사용하여 여러 배치 계정 작업을 수행합니다.
  - 새로 만든 리소스 그룹 내에서 새 배치 계정을 만듭니다.
  - 배치 서비스에서 새로 만든 계정을 가져옵니다.
  - 새 계정에 대한 계정 키를 인쇄합니다.
  - 계정에 대한 새 기본 키를 다시 생성합니다.
  - 계정에 대한 할당량 정보를 인쇄합니다.
  - 구독에 대한 할당량 정보를 인쇄합니다.
  - 구독 내에서 모든 계정을 인쇄합니다.
  - 새로 만든 계정을 삭제합니다.
7. 해당 리소스 그룹을 삭제합니다.

새로 만든 배치 계정 및 리소스 그룹을 삭제하기 전에 [Azure 포털][azure_portal]에서 둘 다 검사할 수 있습니다.

![리소스 그룹 및 배치 계정을 표시하는 Azure 포털][1] <br /> *새 리소스 그룹 및 배치 계정을 표시하는 Azure 포털*

[aad_about]: ../active-directory/active-directory-whatis.md "Azure Active Directory란?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Azure AD의 인증 시나리오"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Azure Active Directory와 응용 프로그램 통합"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png

<!---HONumber=AcomDC_0831_2016-->