---
title: C#을 사용하여 Azure 리소스 배포 | Microsoft Docs
description: C# 및 Azure Resource Manager를 사용하여 Microsoft Azure 리소스를 만드는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/06/2016
ms.author: davidmu

---
# <a name="deploy-azure-resources-using-c#"></a>C를 사용하여 Azure 리소스 배포
이 문서에서는 C#을 사용하여 Azure 리소스를 만드는 방법을 보여 줍니다.

먼저 다음 작업을 완료했는지 확인해야 합니다.

* [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
* [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 또는 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855) 설치 확인
* [인증 토큰](../resource-group-authenticate-service-principal.md)

이러한 단계를 수행하려면 약 30분이 걸립니다.

## <a name="step-1:-create-a-visual-studio-project-and-install-the-libraries"></a>1단계: Visual Studio 프로젝트를 만들고 라이브러리 설치
NuGet 패키지는 이 자습서를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. Visual Studio에서 필요한 라이브러리를 가져오려면 다음 단계를 수행합니다.

1. **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.
2. **템플릿** > **Visual C#**에서 **콘솔 응용 프로그램**을 선택하고, 프로젝트의 이름과 위치를 입력한 다음 **확인**을 클릭합니다.
3. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
4. 검색 상자에 *Active Directory* 를 입력하고 Active Directory 인증 라이브러리 패키지에 대해 **설치** 를 클릭한 다음 지침에 따라 패키지를 설치합니다.
5. 페이지의 위쪽에서 **시험판 포함**을 선택합니다. 검색 상자에 *Microsoft.Azure.Management.Compute* 를 입력하고 계산 .NET 라이브러리에 대해 **설치** 를 클릭한 다음 지침에 따라 패키지를 설치합니다.
6. 검색 상자에 *Microsoft.Azure.Management.Network* 를 입력하고 네트워크 .NET 라이브러리에 대해 **설치** 를 클릭한 다음 지침에 따라 패키지를 설치합니다.
7. 검색 상자에 *Microsoft.Azure.Management.Storage* 를 입력하고 저장소 .NET 라이브러리에 대해 **설치** 를 클릭한 다음 지침에 따라 패키지를 설치합니다.
8. 검색 상자에 *Microsoft.Azure.Management.ResourceManager* 를 입력하고 리소스 관리 라이브러리에 대해 **설치** 를 클릭합니다.

이제 라이브러리를 사용하기 시작하여 응용 프로그램을 만들 준비가 되었습니다.

## <a name="step-2:-create-the-credentials-that-are-used-to-authenticate-requests"></a>2단계: 요청을 인증하는 데 사용되는 자격 증명 만들기
이제 이전에 만든 응용 프로그램 정보의 서식을 Azure Resource Manager에 요청을 인증하는 데 사용되는 자격 증명으로 지정합니다.

1. 만들었던 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 추가합니다.
   
        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
2. 필요한 토큰을 만들려면 Program 클래스에 다음 메서드를 추가합니다.
   
        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }
   
    {client-id}를 Azure Active Directory의 식별자로 바꾸고 {client-secret}을 AD 응용 프로그램의 선택키로 바꾸고, {tenant-id}를 구독의 테넌트 식별자로 바꿉니다. Get-AzureRmSubscription을 실행하여 테넌트 ID를 찾을 수 있습니다. 선택키는 Azure 포털을 사용하여 찾을 수 있습니다.
3. 이전에 추가한 메서드를 호출하려면 Program.cs 파일의 Main 메서드에 다음 코드를 추가합니다.
   
        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);
4. Program.cs 파일을 저장합니다.

## <a name="step-3:-register-the-resource-providers-and-create-the-resources"></a>3단계: 리소스 공급자 등록 및 리소스 만들기
### <a name="register-the-providers-and-create-a-resource-group"></a>공급자 등록 및 리소스 그룹 만들기
모든 리소스는 리소스 그룹에 포함되어야 합니다. 그룹에 리소스를 추가할 수 있기 전에 구독은 리소스 공급자로 등록되어야 합니다.

1. Program 클래스의 Main 메서드에 변수를 추가하여 리소스에 사용할 이름을 지정합니다.
   
        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
   
    모든 변수 값을 사용하려는 이름 및 식별자로 바꿉니다. Get-AzureRmSubscription을 실행하여 구독 식별자를 찾을 수 있습니다.
2. 리소스 그룹을 만들고 공급자를 등록하려면 Program 클래스에 다음 메서드를 추가합니다.
   
        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
   
          Console.WriteLine("Registering the providers...");
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
   
          Console.WriteLine("Creating the resource group...");
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }
3. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### <a name="create-a-storage-account"></a>저장소 계정 만들기
가상 컴퓨터에 대해 생성된 가상 하드 디스크 파일을 저장하는 데 [저장소 계정](../storage/storage-create-storage-account.md) 이 필요합니다.

1. 저장소 계정을 만들려면 Program 클래스에 다음 메서드를 추가합니다.
   
        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }
2. 이전에 추가한 메서드를 호출하려면 Program 클래스의 Main 메서드에 다음 코드를 추가합니다.
   
        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-public-ip-address"></a>공용 IP 주소 만들기
공용 IP 주소는 가상 컴퓨터와 통신해야 합니다.

1. 가상 컴퓨터의 공용 IP 주소를 만들려면 Program 클래스에 다음 메서드를 추가합니다.
   
        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }
2. 이전에 추가한 메서드를 호출하려면 Program 클래스의 Main 메서드에 다음 코드를 추가합니다.
   
        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기
리소스 관리자 배포 모델로 만든 가상 컴퓨터는 가상 네트워크에 있어야 합니다.

1. 서브넷 및 가상 네트워크를 만들려면 Program 클래스에 다음 메서드를 추가합니다.
   
        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
   
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
   
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
   
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
2. 이전에 추가한 메서드를 호출하려면 Program 클래스의 Main 메서드에 다음 코드를 추가합니다.
   
        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-network-interface"></a>네트워크 인터페이스 만들기
가상 컴퓨터는 가상 네트워크에서 통신하기 위해 네트워크 인터페이스가 필요합니다.

1. 네트워크 인터페이스를 만들려면 Program 클래스에 다음 메서드를 추가합니다.
   
        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);
   
          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }
2. 이전에 추가한 메서드를 호출하려면 Program 클래스의 Main 메서드에 다음 코드를 추가합니다.
   
        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-an-availability-set"></a>가용성 집합 만들기
가용성 집합은 응용 프로그램에서 사용되는 가상 컴퓨터의 유지 관리를 쉽게 관리할 수 있도록 합니다.

1. 가용성 집합을 만들려면 Program 클래스에 다음 메서드를 추가합니다.
   
        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }
2. 이전에 추가한 메서드를 호출하려면 Program 클래스의 Main 메서드에 다음 코드를 추가합니다.
   
        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### <a name="create-a-virtual-machine"></a>가상 컴퓨터 만들기
모든 지원 리소스를 만들었으므로 가상 컴퓨터를 만들 수 있습니다.

1. 가상 컴퓨터를 만들려면 Program 클래스에 다음 메서드를 추가합니다.
   
        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);
   
          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);
   
          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
            groupName,
            vmName,
            new VirtualMachine
            {
              Location = location,
              AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
              HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
              OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                {
                  ProvisionVMAgent = true
                }
              },
              NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                {
                  new NetworkInterfaceReference { Id = nic.Id }
                }
              },
              StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                {
                  Publisher = "MicrosoftWindowsServer",
                  Offer = "WindowsServer",
                  Sku = "2012-R2-Datacenter",
                  Version = "latest"
                },
                OsDisk = new OSDisk
                {
                  Name = "mytestod1",
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
        }
   
   > [!NOTE]
   > 이 자습서는 Windows Server 운영 체제의 버전을 실행하는 가상 컴퓨터를 만듭니다. 기타 이미지 선택에 대해 자세히 알아보려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 탐색 및 선택](virtual-machines-linux-cli-ps-findimage.md)을 참조하세요.
   > 
   > 
2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

## <a name="step-4:-delete-the-resources"></a>4단계: 리소스 삭제
Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 가상 컴퓨터 및 모든 지원 리소스를 삭제하려는 경우, 리소스 그룹을 삭제해야 합니다.

1. 리소스 그룹을 삭제하려면 Program 클래스에 다음 메서드를 추가합니다.
   
       public static async void DeleteResourceGroupAsync(
         TokenCredentials credential,
         string groupName,
         string subscriptionId)
       {
         Console.WriteLine("Deleting resource group...");
         var resourceManagementClient = new ResourceManagementClient(credential)
           { SubscriptionId = subscriptionId };
         await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
       }
2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
       DeleteResourceGroupAsync(
         credential,
         groupName,
         subscriptionId);
       Console.ReadLine();

## <a name="step-5:-run-the-console-application"></a>5단계: 콘솔 응용 프로그램 실행
1. 콘솔 응용 프로그램을 실행하려면, Visual Studio에서 **시작** 을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.
2. 각 리소스를 생성하도록 각 상태 코드가 반환된 후 **Enter** 를 누릅니다. 가상 컴퓨터를 만든 후 Enter 키를 눌러 모든 리소스를 삭제하기 전에 다음 단계를 수행합니다.
   
    이 콘솔 응용 프로그램을 처음부터 끝까지 완전히 실행하려면 약 5분이 필요합니다. Enter를 눌러 리소스를 삭제하기 전에 Azure 포털에서 리소스 만들기를 확인하는 데에 몇 분이 걸릴 수 있습니다.
3. 리소스의 상태를 보려면 Azure 포털에서 감사 로그로 이동합니다.
   
    ![Azure 포털에서 감사 로그 찾아보기](./media/virtual-machines-windows-csharp/crpportal.png)

## <a name="next-steps"></a>다음 단계
* [C# 및 Resource Manager 템플릿을 사용하여 Azure 가상 컴퓨터 배포](virtual-machines-windows-csharp-template.md)의 정보를 사용하여 가상 컴퓨터를 만드는 데 템플릿을 활용합니다.
* [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-csharp-manage.md)를 검토하여 자신이 만든 가상 컴퓨터를 관리하는 방법을 알아봅니다.

<!--HONumber=Oct16_HO2-->


