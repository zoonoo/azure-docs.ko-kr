<properties
   pageTitle=".Net용 리소스 관리자 SDK | Microsoft Azure"
   description="리소스 관리자 .Net SDK 인증 및 사용 예제 개요"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="navale;tomfitz;"/>

# .Net용 Azure Resource Manager SDK  
Azure Resource Manager SDK는 여러 언어 및 플랫폼에 사용할 수 있습니다. 이러한 언어 구현은 각각 해당 에코 시스템 패키지 관리자 및 GitHub를 통해 사용할 수 있습니다.

이러한 SDK의 각 코드는 [Azure RESTful API 사양](https://github.com/azure/azure-rest-api-specs)에서 생성됩니다. 이러한 사양은 오픈 소스이며 Swagger v2 사양을 기반으로 합니다. SDK 코드는 [AutoRest](https://github.com/azure/autorest)라는 오픈 소스 프로젝트를 통해 생성된 코드입니다. AutoRest는 이러한 RESTful API 사양을 여러 언어의 클라이언트 라이브러리로 변환합니다. SDK에서 생성된 코드의 어떤 측면을 개선하려면 SDK를 만드는 전체 도구는 오픈되어 자유롭게 사용 가능하고 광범위하게 채택된 API 사양 형식에 기반으로 해야 합니다.

[.NET용 Azure SDK](https://azure.microsoft.com/downloads/)는 Azure Resource Manager에서 노출되는 API 대부분을 호출하는 데 유용한 일련의 NuGet 패키지로 제공됩니다. SDK가 필요한 기능을 노출하지 않는 경우 백그라운드에서 ARM REST API에 대한 일반 호출로 SDK를 쉽게 결합할 수 있습니다.

이 설명서는 .NET용 Azure SDK, Azure ARM API 또는 Visual Studio의 모든 측면을 설명하려는 목적이지만 사용자가 신속하게 작업을 시작할 수 있는 방법을 제공합니다.

아래의 모든 코드 조각을 가져온 전체 다운로드 가능한 샘플 프로젝트를 [여기](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net)에서 찾을 수 있습니다.

## NuGet 패키지 설치

이 항목의 예제를 사용하려면 .NET용 Azure SDK 외에도 두 개의 NuGet 패키지가 필요합니다. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

1. **Microsoft.IdentityModel.Clients.ActiveDirectory**를 검색하고 최신 버전의 패키지를 설치합니다.
2. **Microsoft.Azure.Management.ResourceManager**를 검색하고 **시험판 포함**을 선택합니다. 최신 미리 보기 버전(예: 1.1.2-preview)을 설치합니다.

## 인증
Resource Manager에 대한 인증은 Azure AD(Active Directory)에 의해 처리됩니다. API에 연결하기 위해 먼저 모든 요청에 전달할 수 있는 인증 토큰을 수신하도록 Azure AD로 인증해야 합니다. 이 토큰을 가져오려면 먼저 로그인에 사용되는 Azure AD 응용 프로그램 및 서비스 주체를 만들어야 합니다. 단계별 지침은 [Azure PowerShell을 사용하여 리소스에 액세스할 Active Directory 응용 프로그램 만들기](resource-group-authenticate-service-principal.md), [Azure CLI를 사용하여 리소스에 액세스할 Active Directory 응용 프로그램 만들기](resource-group-authenticate-service-principal-cli.md), [포털을 사용하여 리소스에 액세스할 수 있는 Active Directory 응용 프로그램 만들기](resource-group-create-service-principal-portal.md) 중 하나를 따르세요.

서비스 주체를 만든 후에 다음을 수행해야 합니다.

- 클라이언트 ID 또는 응용 프로그램 ID(GUID)
- 클라이언트 암호(문자열)
- 테넌트 ID(GUID) 또는 도메인 이름(문자열)

### 코드에서 AccessToken 받기
Azure AD 테넌트 ID, Azure AD 응용 프로그램 클라이언트 ID 및 Azure AD 응용 프로그램 클라이언트 암호를 전달하여 아래 코드 줄로 인증 토큰을 쉽게 얻을 수 있습니다. 몇몇 요청에 대한 토큰은 기본적으로 1시간 동안 유효하므로 저장합니다.

```csharp
private static async Task<AuthenticationResult> GetAccessTokenAsync(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Aquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* AAD URI */
            + $"{tenantId}" /* Tenant ID */);

    var credential = new ClientCredential(clientId, clientSecret);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

로그인에 테넌트 ID를 사용하는 대신 아래와 같이 Active Directory 도메인을 사용할 수 있습니다. 이 방법을 사용하려면 테넌트 ID 대신 도메인 이름을 포함하도록 메서드 서명을 변경해야 합니다.

```csharp
AuthenticationContext authContext = new AuthenticationContext
    ("https://login.windows.net/" /* AAD URI */
    + $"{domain}.onmicrosoft.com");
```

인증을 위해 인증서를 사용하는 Active Directory 앱에 대한 액세스 토큰을 가져올 수 있습니다.

```csharp
private static async Task<AuthenticationResult> GetAccessTokenFromCertAsync(string tenantId, string clientId, string certName)
{
    Console.WriteLine("Aquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* AAD URI */
        + $"{tenantId}" /* Tenant ID or AAD domain */);

    X509Certificate2 cert = null;
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);

    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false);
        cert = certs[0];
    }
    finally
    {
        store.Close();
    }

    var certCredential = new ClientAssertionCertificate(clientId, cert);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", certCredential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### 인증된 응용 프로그램에 연결된 Azure 구독 쿼리
수행하려는 첫 번째 작업 중 하나는 인증된 응용 프로그램에 연결된 Azure 구독을 쿼리하는 것입니다. 대상으로 지정된 구독의 구독 ID는 지금부터 각 API 호출에 전달하는 필수 사항입니다.

아래 샘플 코드는 .NET용 Azure SDK의 기능을 사용하지 않고 REST API를 사용하여 직접 Azure API를 쿼리합니다.

```csharp
async private static Task<List<string>> GetSubscriptionsAsync(string token)
{
    Console.WriteLine("Querying for subscriptions");
    const string apiVersion = "2015-01-01";

    var client = new HttpClient();
    client.BaseAddress = new Uri("https://management.azure.com/");
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await client.GetAsync($"subscriptions?api-version={apiVersion}");

    var jsonResponse = response.Content.AsString();

    var subscriptionIds = new List<string>();
    dynamic json = JsonConvert.DeserializeObject(jsonResponse);

    for (int i = 0; i < json.value.Count; i++)
    {
        subscriptionIds.Add(json.value[i].subscriptionId.Value);
    }

    Console.WriteLine($"Found {subscriptionIds.Count} subscription(s)");
    return subscriptionIds;
}
```

ID의 목록을 반환하기 위해 다음 구독 ID를 추출하는 Azure에서 JSON 응답을 가져옵니다. 이 설명서에서 Azure Resource Manager API에 대한 모든 후속 호출은 단일 Azure 구독 ID를 사용합니다. 따라서 응용 프로그램을 여러 구독과 연결된 경우 그 중에 적합한 구독을 선택하고 앞으로 이동하는 매개 변수로 전달합니다.

여기에서부터 Azure API에 대해 수행하는 모든 호출은 .NET용 Azure SDK를 사용하므로 코드가 약간 다르게 보입니다.

### TokenCredentials 개체로 토큰 래핑
다음 API 호출은 모두 "TokenCredentials" 개체의 형식으로 Azure AD에서 받은 토큰이 필요합니다. 이러한 개체는 원시 토큰을 클래스의 생성자에 매개 변수로 전달하여 쉽게 만들어집니다.

```csharp
var credentials = new TokenCredentials(token);
```

이전 버전의 Resource Manager NuGet 패키지(**Microsoft.Azure.Management.Resources**로 이름 지정)가 있는 경우 다음 코드를 사용해야 합니다.

```csharp
var credentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);
```

## 리소스 그룹 만들기
Azure의 모든 기능은 리소스 그룹에 집중합니다. 이제 하나를 만들어 보겠습니다. 일반 리소스 및 리소스 그룹은 *ResourceManagementClient*에서 처리됩니다. 사용할 특수한 다음 관리 클라이언트 중 하나로 구독 ID 뿐만 아니라 사용자의 자격 증명을 제공하여 작업하려는 구독을 식별해야 합니다.

```csharp
private static async Task<ResourceGroup> CreateResourceGroupAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location)
{
    Console.WriteLine($"Creating Resource Group {resourceGroup}");
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await resourceClient.ResourceGroups.CreateOrUpdateAsync(resourceGroup,
        new ResourceGroup
        {
            Location = location
        });
}
```

## 수동 또는 템플릿을 사용하여 리소스 만들기
Azure Resource Manager API와 상호 작용하는 여러 가지 방법이 있지만 중요한 두가지는 다음과 같습니다.

* 수동으로 특정 리소스 공급자를 수동으로 호출하거나
* Azure Resource Manager 템플릿(즉, ARM 템플릿)을 사용합니다.

ARM 템플릿을 사용하면 다음과 같은 이점이 있습니다.

* 달성되는 과정이 아닌 원하는 최종 결과의 모습을 선언적으로 지정합니다.
* 배포의 병렬 실행을 수동으로 처리하지 않아도 ARM이 작업을 대신 수행합니다.
* 모든 언어를 사용하여 템플릿이 지정된 배포를 시작할 수 있지만 ARM 템플릿을 배포하기 위해 C# 또는 기타 언어를 배울 필요가 없습니다.
* 템플릿에 사용되는 DSL(도메인 특정 언어)는 JSON을 사용하여 작성되며 JSON로 작업하는 모든 사용자가 이해하기 쉽습니다.

이러한 템플릿의 장점에도 불구하고 API를 수동으로 호출하는 방법을 표시하여 시작합니다.

### 하나씩 가상 컴퓨터 만들기
따라서 이제 구독 및 리소스 그룹이 있습니다. 가상 컴퓨터를 배포하려는 경우 실제로 가상 컴퓨터를 구성하는 부분을 파악해야 하며 다음의 몇 가지 부분을 알아낼 수 있습니다.

* 영구적인 디스크를 저장하기 위한 1 또는 여러 저장소 계정
* 인터넷(DNS 이름 포함)에서 액세스할 수 있는 1 또는 여러 PIP(공용 IP 주소)
* 리소스 간의 내부 통신을 위한 1 또는 여러 VNET(가상 네트워크)
* VM이 통신할 수 있도록 하는 1 또는 여러 NIC(네트워크 인터페이스 카드)
* 자사 소프트웨어를 실행하는 1 또는 여러 VM(가상 컴퓨터)

또 다른 흥미로운 부분은 이러한 리소스의 일부를 병렬적으로 생성할 수 있고 일부는 불가능하다는 점입니다. 예:

* NIC는 PIP 및 VNet에 따라 다릅니다.
* VM은 NIC 및 저장소 계정에 따라 다릅니다.

필요한 종속성을 생성하기 전에 리소스를 인스턴스화하려고 하지 않는지 확인해야 합니다. 이 설명서에 제공된 전체 [샘플](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net)은 만든 항목을 추적하는 동시에 효율적이고 병렬적으로 리소스를 만드는 방법을 보여줍니다.

#### 저장소 계정 만들기
가상 컴퓨터에 가상 VHD를 저장하려면 저장소 계정이 필요합니다. 여러 VM을 사용할 수 있는 기존 저장소 계정이 있지만 한계까지 실행하지 않고 여러 저장소 계정에 부하를 분산해야 합니다. 모든 VM 크기를 모든 지역에서 및/또는 모든 저장소 계정 유형에 사용할 수 없기 때문에 저장소 계정의 유형과 해당 위치가 선택할 수 있는 VM 크기를 제한할 수 있습니다.

```csharp
private static async Task<StorageAccount> CreateStorageAccountAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, AccountType accountType = AccountType.StandardLRS)
{
    Console.WriteLine("Creating Storage Account");
    var storageClient = new StorageManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await storageClient.StorageAccounts.CreateAsync(resourceGroup, storageAccountName,
        new StorageAccountCreateParameters
        {
            Location = location,
            AccountType = accountType,
        });
}
```

#### PIP(공용 IP 주소) 만들기
공용 IP 주소는 Azure의 리소스를 인터넷에서 액세스할 수 있도록 합니다. IP 주소와 함께 쉽게 액세스하는 데 사용할 수 있는 FQDN(정규화된 도메인 이름)를 할당받습니다.

```csharp
private static Task<PublicIPAddress> CreatePublicIPAddressAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string pipAddressName, string pipDnsName)
{
    Console.WriteLine("Creating Public IP");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createPipTask = networkClient.PublicIPAddresses.CreateOrUpdateAsync(resourceGroup, pipAddressName,
        new PublicIPAddress
        {
            Location = location,
            DnsSettings = new PublicIPAddressDnsSettings { DomainNameLabel = pipDnsName },
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support Static IP Addresses but could be extended to do so
        });

    return createPipTask;
}
```

#### VNET(가상 네트워크) 만들기
VM만이 있는 경우라도 ARM API를 사용하여 만든 모든 VM은 가상 네트워크의 일부가 되어야 합니다. 가상 네트워크는 하나 이상의 서브넷을 포함해야 하지만 여러 서브넷에 있는 리소스를 나누고 보호할 수 있습니다.

```csharp
private static Task<VirtualNetwork> CreateVirtualNetworkAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string vNetName, string vNetAddressPrefix, Subnet[] subnets)
{
    Console.WriteLine("Creating Virtual Network");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createVNetTask = networkClient.VirtualNetworks.CreateOrUpdateAsync(resourceGroup, vNetName,
        new VirtualNetwork
        {
            Location = location,
            AddressSpace = new AddressSpace(new[] { vNetAddressPrefix }),
            Subnets = subnets
        });

    return createVNetTask;
}
```

#### NIC(네트워크 인터페이스 카드) 만들기
NIC(네트워크 인터페이스 카드)는 자신이 위치한 가상 네트워크로 VM을 연결합니다. VM에는 많은 NIC가 있고 따라서 몇몇 가상 네트워크와 연결될 수 있습니다. 이 샘플에서는 하나의 VNET에 VM을 첨부하기만 한다고 가정합니다.

```csharp
private static Task<NetworkInterface> CreateNetworkInterfaceAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string nicName, string nicIPConfigName, PublicIPAddress pip, Subnet subnet)
{
    Console.WriteLine("Creating Network Interface");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createNicTask = networkClient.NetworkInterfaces.CreateOrUpdateAsync(resourceGroup, nicName,
        new NetworkInterface()
        {
            Location = location,
            IpConfigurations = new[] {
                new NetworkInterfaceIPConfiguration
                {
                    Name = nicIPConfigName,
                    PrivateIPAllocationMethod = "Dynamic",
                    PublicIPAddress = pip,
                    Subnet = subnet
                }
            }
        });

    return createNicTask;
}
```

#### 가상 컴퓨터 만들기
마지막으로, 실제 가상 컴퓨터를 만들 차례입니다. VM은 위에서 만든 리소스와 직접 또는 간접적으로 종속되므로 VM을 프로비전하기 전에 위의 항목이 모두 준비될 때까지 대기해야 합니다. VM을 프로비전하는 작업은 위의 리소스에서 가장 오랜 시간이 걸리므로 이러한 상황이 발생하려면 응용 프로그램이 잠시 대기할 것을 예상합니다.

```csharp
private static async Task<VirtualMachine> CreateVirtualMachineAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, string vmName, string vmSize, string vmAdminUsername, string vmAdminPassword, string vmImagePublisher, string vmImageOffer, string vmImageSku, string vmImageVersion, string vmOSDiskName, string nicId)
{
    Console.WriteLine("Creating Virtual Machine (this may take a while)");
    var computeClient = new ComputeManagementClient(credentials) { SubscriptionId = subscriptionId };
    var vm = await computeClient.VirtualMachines.CreateOrUpdateAsync(resourceGroup, vmName,
        new VirtualMachine
        {
            Location = location,
            HardwareProfile = new HardwareProfile(vmSize),
            OsProfile = new OSProfile(vmName, vmAdminUsername, vmAdminPassword),
            StorageProfile = new StorageProfile(
                new ImageReference
                {
                    Publisher = vmImagePublisher,
                    Offer = vmImageOffer,
                    Sku = vmImageSku,
                    Version = vmImageVersion
                },
                new OSDisk
                {
                    Name = vmOSDiskName,
                    Vhd = new VirtualHardDisk($"http://{storageAccountName}.blob.core.windows.net/vhds/{vmOSDiskName}.vhd"),
                    Caching = "ReadWrite",
                    CreateOption = "FromImage"
                }),
            NetworkProfile = new NetworkProfile(
                new[] { new NetworkInterfaceReference { Id = nicId } }),
            DiagnosticsProfile = new DiagnosticsProfile(
                new BootDiagnostics
                {
                    Enabled = true,
                    StorageUri = $"http://{storageAccountName}.blob.core.windows.net"
                })
        });

    return vm;
}
```

### 템플릿 기반 배포 사용
템플릿을 배포하는 방법에 대한 자세한 지침은 [.NET 라이브러리 및 템플릿을 사용하여 Azure 리소스 배포](./virtual-machines/virtual-machines-windows-csharp-template.md) 자습서를 읽고 수행합니다.

간단히 말해 템플릿을 배포하는 작업이 리소스를 수동으로 프로비전하는 것보다 훨씬 쉽습니다. 아래 코드는 템플릿과 매개 변수 파일이 있는 URI을 가리켜서 이를 수행하는 방법을 보여줍니다.

```csharp
private static async Task<DeploymentExtended> CreateTemplatedDeployment(TokenCredentials credentials, string subscriptionId, string resourceGroup, string templateUri, string parametersUri)
{
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };

    return await resourceClient.Deployments.BeginCreateOrUpdateAsync(resourceGroup, "mytemplateddeployment", new Deployment(
        new DeploymentProperties()
        {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink(templateUri),
            ParametersLink = new ParametersLink(parametersUri)
        }));

}
```


 
   

<!---HONumber=AcomDC_0622_2016-->