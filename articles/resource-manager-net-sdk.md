<properties
   pageTitle=".NET용 리소스 관리자 SDK | Microsoft Azure"
   description="리소스 관리자 .NET SDK 인증 및 사용 예제 개요"
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

# .NET용 Azure Resource Manager SDK  
Azure Resource Manager SDK는 여러 언어 및 플랫폼에 사용할 수 있습니다. 이러한 언어 구현은 각각 해당 에코 시스템 패키지 관리자 및 GitHub를 통해 사용할 수 있습니다.

이러한 SDK의 각 코드는 [Azure RESTful API 사양](https://github.com/azure/azure-rest-api-specs)에서 생성됩니다. 이러한 사양은 오픈 소스이며 Swagger 2.0 사양을 기반으로 합니다. SDK 코드는 [AutoRest](https://github.com/azure/autorest)라는 오픈 소스 프로젝트를 통해 생성됩니다. AutoRest는 이러한 RESTful API 사양을 여러 언어의 클라이언트 라이브러리로 변환합니다. SDK에서 생성된 코드의 어떤 측면을 개선하려면, SDK를 만드는 전체 도구는 오픈되어 자유롭게 사용 가능하고 광범위하게 채택된 API 사양 형식에 기반으로 해야 합니다.

[.NET용 Azure SDK](https://azure.microsoft.com/downloads/)는 대부분의 Azure Resource Manager API를 호출하는 데 유용한 일련의 NuGet 패키지입니다. SDK가 필요한 기능을 제공하지 않는 경우 백그라운드에서 Resource Manager REST API에 대한 일반 호출로 SDK를 쉽게 결합할 수 있습니다.

이 문서는 .NET용 Azure SDK, Azure Resource Manager 또는 Visual Studio의 모든 측면을 설명하기 위한 페이지는 아닙니다. 빠르게 시작할 수 있는 방법을 설명하기 위해 제공됩니다.

다음의 모든 코드 조각은 [다운로드할 수 있는 샘플 프로젝트](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net)로부터 제공됩니다.

## NuGet 패키지 설치

이 문서의 예제를 사용하려면 .NET용 Azure SDK 외에도 두 개의 NuGet 패키지가 필요합니다. 설치하려면:

1. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
2. **Microsoft.IdentityModel.Clients.ActiveDirectory**를 검색하고 최신 버전의 패키지를 설치합니다.
3. **Microsoft.Azure.Management.ResourceManager**를 검색하고 **시험판 포함**을 선택합니다. 최신 미리 보기 버전(예: 1.1.2-preview)을 설치합니다.

## 인증
Resource Manager에 대한 인증은 Azure AD(Active Directory)에서 처리됩니다. API에 연결하려면, 먼저 모든 요청에 전달할 수 있는 액세스 토큰을 수신하도록 Azure AD로 인증해야 합니다. 이 토큰을 가져오려면, Azure AD 응용 프로그램을 만들고 로그인에 사용할 서비스 주체를 만들어야 합니다. 단계별 지침은 아래 문서 중 하나를 따르십시오.

- [Azure PowerShell을 사용하여 리소스에 액세스하는 Active Directory 응용 프로그램 만들기](resource-group-authenticate-service-principal.md)
- [Azure CLI를 사용하여 리소스에 액세스하는 Active Directory 응용 프로그램 만들기](resource-group-authenticate-service-principal-cli.md)
- [Azure 포털을 사용하여 리소스에 액세스할 수 있는 Active Directory 응용 프로그램 만들기](resource-group-create-service-principal-portal.md)

서비스 주체를 만든 후에 다음을 수행해야 합니다.

- 클라이언트 ID 또는 응용 프로그램 ID(GUID)
- 클라이언트 암호(문자열)
- 테넌트 ID(GUID) 또는 도메인 이름(문자열)

### 코드에서 액세스 토큰 수신
다음 코드 줄을 사용하고, Azure AD 테넌트 ID, Azure AD 응용 프로그램 클라이언트 ID 및 Azure AD 응용 프로그램 클라이언트 암호에만 전달하여, 액세스 토큰을 얻을 수 있습니다. 기본적으로 한 시간 동안 유효하므로, 몇몇 요청에 대해 토큰을 저장합니다.

```csharp
private static async Task<AuthenticationResult> GetAccessTokenAsync(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
            + $"{tenantId}" /* Tenant ID */);

    var credential = new ClientCredential(clientId, clientSecret);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

로그인에 테넌트 ID를 사용하는 대신, 다음 코드와 같이, Azure AD 도메인을 사용할 수 있습니다. 이 방법을 사용하려면 테넌트 ID 대신 도메인 이름을 포함하도록 메서드 서명을 변경해야 합니다.

```csharp
AuthenticationContext authContext = new AuthenticationContext
    ("https://login.windows.net/" /* Azure AD URI */
    + $"{domain}.onmicrosoft.com");
```

이 코드를 사용하여 인증에 인증서를 사용하는 Azure AD 응용 프로그램에 대한 액세스 토큰을 가져올 수 있습니다.

```csharp
private static async Task<AuthenticationResult> GetAccessTokenFromCertAsync(string tenantId, string clientId, string certName)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
        + $"{tenantId}" /* Tenant ID or Azure AD domain */);

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
인증된 응용 프로그램에 연결된 Azure 구독을 쿼리할 수 있습니다. 대상으로 지정된 구독의 구독 ID는 지금부터 각 API 호출에 전달하는 필수 사항입니다.

다음 샘플 코드는 REST API를 사용하여 Azure API를 직접 쿼리합니다. 다시 말해, .NET용 Azure SDK의 기능을 사용하지 않습니다.

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

Azure로부터 JSON 응답을 얻습니다. 그 다음 ID 목록을 반환하기 위해, 이 응답으로부터 구독 ID를 추출합니다. 이 문서의 Azure Resource Manager에 대한 모든 후속 호출은 단일 Azure 구독 ID를 사용합니다. 따라서 응용 프로그램이 여러 개의 구독과 연결되어 있으면, 그 들 중 적합한 것을 선택하여, 전달할 매개 변수로 전달합니다.

이 지점부터, Azure API에 대해 생성하는 모든 호출은 .NET용 Azure SDK를 사용하게 됩니다. 따라서 코드가 약간 달라 보일 수 있습니다.

### TokenCredentials 개체로 토큰 래핑
다음 API 호출은 모두 TokenCredentials 개체의 형식으로 Azure AD에서 받은 토큰이 필요합니다. 이러한 개체는 원시 토큰을 클래스 생성자에게 매개 변수로 전달하여 만들 수 있습니다.

```csharp
var credentials = new TokenCredentials(token);
```

이전 버전의 Resource Manager NuGet 패키지(Microsoft.Azure.Management.Resources로 이름 지정)가 있는 경우 다음 코드를 사용해야 합니다.

```csharp
var credentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);
```

## 리소스 그룹 만들기
Azure의 모든 기능은 리소스 그룹에 집중되어 있습니다. 따라서 리소스를 만들겠습니다. *ResourceManagementClient*는 일반 리소스 및 리소스 그룹을 처리합니다. 다음과 같은 경우 즉, 사용하려는 특수화된 관리 클라이언트에 대해, 작업할 구독을 식별하기 위한 자격 증명 및 구독 ID를 제공해야 합니다.

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
Azure Resource Manager API와 상호 작용하는 방법을 여러 가지이며, 주요한 두 가지 방법은 다음과 같습니다.

* 수동으로, 특정 리소스 공급자를 호출하여
* Azure Resource Manager 템플릿을 사용하여

Resource Manager 템플릿 사용에는 다음과 같은 이점이 있습니다.

* 달성되는 과정이 아닌 원하는 결과의 모습을 선언적으로 지정합니다.
* 배포의 병렬 실행을 수동으로 처리하지 않아도 됩니다. Resource Manager에서 대신 수행됩니다.
* 모든 언어를 사용하여 템플릿이 지정된 배포를 시작할 수 있지만 Resource Manager 템플릿을 배포하기 위해 C# 또는 기타 언어를 배울 필요가 없습니다.
* 템플릿에 사용되는 DSL(Domain-Specific Language)은 JSON을 통해 빌드됩니다. JSON으로 작업한 경험이 있다면 충분히 이해할 수 있을 정도로 쉽습니다.

이러한 템플릿의 장점에도 불구하고 API를 수동으로 호출하는 방법을 표시하여 시작하겠습니다.

### 하나씩 가상 컴퓨터 만들기
구독 및 리소스 그룹이 있습니다. 가상 컴퓨터(VM)를 배포하려면, 무엇으로 구성되는지 알아야 합니다.

* 영구적인 디스크를 저장하기 위한 하나 이상의 저장소 계정
* 인터넷을 통해 Azure의 리소스를 액세스할 수 있도록 해 주는 하나 이상의 공용 IP 주소(DNS 이름 포함)
* 리소스 간의 내부 통신을 위한 하나 이상의 가상 네트워크
* VM이 통신할 수 있도록 하는 하나 이상의 NIC(네트워크 인터페이스 카드)
* 소프트웨어 실행을 위한 하나 이상의 가상 컴퓨터

이들 리소스 중 일부는 동시에 만들 수 있지만, 나머지는 그렇지 않습니다. 예:

* NIC는 공용 IP 주소 및 가상 네트워크에 따라 다릅니다.
* VM은 NIC 및 저장소 계정에 따라 다릅니다.

필요한 종속성을 생성하기 전에 리소스를 인스턴스화하려고 하지 마십시오. 전체 [샘플](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net)은 만든 항목을 추적하면서 리소스를 효율적으로 동시에 만드는 방법을 보여줍니다.

#### 저장소 계정 만들기
가상 컴퓨터의 가상 하드 디스크를 저장하려면 저장소 계정이 필요합니다. 기존 저장소 계정이 있으면, 몇 개의 VM에 사용할 수 있습니다. 다만, 한도에 도달하지 않도록 부하를 여러 개의 저장소 계정에 분산해야 합니다. 모든 VM 크기를 모든 지역이나 모든 저장소 계정 유형에 사용할 수 없기 때문에 저장소 계정의 유형과 해당 위치가 선택할 수 있는 VM 크기를 제한할 수 있습니다.

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

#### 공용 IP 주소 만들기
공용 IP 주소는 Azure의 리소스를 인터넷에서 액세스할 수 있도록 합니다. IP 주소와 함께 쉽게 액세스하는 데 사용할 수 있는 FQDN(정규화된 도메인 이름)을 할당 받습니다.

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
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support static IP addresses but can be extended to do so
        });

    return createPipTask;
}
```

#### 가상 네트워크 만들기
VM만이 있는 경우라도 Resource Manager API를 사용하여 만든 모든 VM은 가상 네트워크의 일부가 되어야 합니다. 가상 네트워크는 하나 이상의 서브넷을 포함해야 하지만 여러 서브넷을 사용하여 리소스를 나누고 보호할 수 있습니다.

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

#### 네트워크 인터페이스 카드 만들기
NIC는 VM을 VM이 상주하는 가상 네트워크와 연결합니다. VM에는 많은 NIC가 있고 따라서 몇몇 가상 네트워크와 연결될 수 있습니다. 이 샘플은 VM을 하나의 가상 네트워크에 연결한다고 가정합니다.

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
마지막으로, 가상 컴퓨터를 만들 차례입니다. VM은 앞서 만든 모든 리소스에 (직접 또는 간접적으로) 종속되므로 VM을 프로비전하기 전에 리소스가 모두 준비될 때까지 대기해야 합니다. VM 프로비전은 다른 리소스를 만드는 것보다 시간이 오래 걸리므로, 이것을 수행하려면, 응용 프로그램이 한동안 대기 상태가 될 것을 예상합니다.

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

### 템플릿 배포
템플릿을 배포하는 방법에 대한 자세한 지침은 [.NET 라이브러리 및 템플릿을 사용하여 Azure 리소스 배포](./virtual-machines/virtual-machines-windows-csharp-template.md) 문서를 참조하세요.

즉, 템플릿 배포는 수동으로 리소스를 프로비전하는 것보다 훨씬 쉽습니다. 다음 코드는 템플릿과 매개 변수 파일이 있는 URI를 가리켜서 수행하는 방법을 보여줍니다.

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

<!---HONumber=AcomDC_0629_2016-->