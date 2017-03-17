---
title: "C#을 사용하여 Azure Virtual Machine 배포 | Microsoft Docs"
description: "C# 및 Azure Resource Manager를 사용하여 가상 컴퓨터 및 모든 지원 리소스를 배포합니다."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: e96eacd689ead229d0c48d15f53a932b903c5281
ms.openlocfilehash: a03a33ca275d0d81f8968ed19b1cc2fb8907cb1c
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c"></a>C를 사용하여 Azure Virtual Machine 배포# #

이 문서에서는 C#을 사용하여 Azure Virtual Machine 및 해당 지원 리소스를 만드는 방법을 보여 줍니다.

이러한 단계를 수행하려면 약 20분이 걸립니다.

## <a name="step-1-create-a-visual-studio-project"></a>1단계: Visual Studio 프로젝트 만들기

이 단계에서는 Visual Studio가 설치되어 있는지 확인하고 리소스를 만드는 데 사용되는 콘솔 응용 프로그램을 만듭니다.

1. [Visual Studio](https://www.visualstudio.com/)를 아직 설치하지 않았으면 설치합니다.
2. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.
3. **템플릿** > **Visual C#**에서 **콘솔 응용 프로그램**을 선택하고, 프로젝트의 이름과 위치를 입력한 다음 **확인**을 클릭합니다.

## <a name="step-2-install-libraries"></a>2단계: 라이브러리 설치

NuGet 패키지는 이러한 단계를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. Visual Studio에서 필요한 라이브러리를 가져오려면 다음 단계를 수행합니다.


1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭하고 **찾아보기**를 클릭합니다.
2. 검색 상자에 *Microsoft.IdentityModel.Clients.ActiveDirectory*를 입력하고 **설치**를 클릭한 후 지침에 따라 패키지를 설치합니다.
3. 페이지의 위쪽에서 **시험판 포함**을 선택합니다. 검색 상자에 *Microsoft.Azure.Management.Compute*를 입력하고 **설치**를 클릭한 후 지침에 따라 패키지를 설치합니다.
4. 검색 상자에 *Microsoft.Azure.Management.Network*를 입력하고 **설치**를 클릭한 후 지침에 따라 패키지를 설치합니다.
5. 검색 상자에 *Microsoft.Azure.Management.Storage*를 입력하고 **설치**를 클릭한 후 지침에 따라 패키지를 설치합니다.
6. 검색 상자에 *Microsoft.Azure.Management.ResourceManager*를 입력하고 **설치**를 클릭한 후 지침에 따라 패키지를 설치합니다.

이제 라이브러리를 사용하기 시작하여 응용 프로그램을 만들 준비가 되었습니다.

## <a name="step-3-create-the-credentials-used-to-authenticate-requests"></a>3단계: 요청을 인증하는데 사용되는 자격 증명 만들기

이 단계를 시작하기 전에 [Active Directory 서비스 사용자](../azure-resource-manager/resource-group-authenticate-service-principal.md)에 액세스할 수 있는지 확인합니다. 서비스 사용자에서 Azure Resource Manager에서 요청을 인증받기 위한 토큰을 얻을 수 있습니다.

1. 만들었던 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 추가합니다.
   
    ```
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
    ```

2. 자격 증명을 만드는 데 필요한 토큰을 가져오려면 Program 클래스에 다음 메서드를 추가합니다.
   
    ```    
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
    ```

    다음 값을 바꿉니다.
    
    - *{client-id}*를 Azure Active Directory 응용 프로그램의 식별자로 이 식별자는 AD 응용 프로그램의 속성 블레이드에서 찾을 수 있습니다. Azure Portal에서 AD 응용 프로그램을 찾으려면 리소스 메뉴에서 **Azure Active Directory**를 클릭한 다음 **앱 등록**을 클릭합니다.
    - *{client-secret}*을 AD 응용 프로그램의 액세스 키 이 식별자는 AD 응용 프로그램의 속성 블레이드에서 찾을 수 있습니다.
    - *{tenant-id}*를 구독의 테넌트 식별자로 Azure Portal의 Azure Active Directory에 대한 속성 블레이드에서 테넌트 식별자를 찾을 수 있습니다. *디렉터리 ID*로 레이블이 지정되어 있습니다.

3. 이전에 추가한 메서드를 호출하려면 Program.cs 파일의 Main 메서드에 다음 코드를 추가합니다.
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Program.cs 파일을 저장합니다.

## <a name="step-3-create-the-resources"></a>3단계: 리소스 만들기

### <a name="register-the-providers-and-create-a-resource-group"></a>공급자 등록 및 리소스 그룹 만들기

모든 리소스는 리소스 그룹에 포함되어야 합니다. 그룹에 리소스를 추가할 수 있기 전에 구독은 리소스 공급자로 등록되어야 합니다.

1. Program 클래스의 Main 메서드에 변수를 추가하여 리소스에 사용할 이름을 지정합니다.

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var storageName = "myStorageAccount";
    var ipName = "myPublicIP";
    var subnetName = "mySubnet";
    var vnetName = "myVnet";
    var nicName = "myNIC";
    var avSetName = "myAVSet";
    var vmName = "myVM";  
    var location = "location";
    var adminName = "adminName";
    var adminPassword = "adminPassword";
    ```

    다음 값을 바꿉니다.

    - *my*로 시작하는 모든 리소스 이름을 사용자 환경에서 의미 있는 이름으로
    - *subscriptionId*를 구독 식별자로 Azure Portal의 구독 블레이드에서 구독 식별자를 찾을 수 있습니다.
    - *location*를 리소스를 만들려는 [Azure 지역](https://azure.microsoft.com/regions/)으로
    - *adminName*을 가상 컴퓨터의 관리자 계정 이름으로
    - *adminPassword*를 관리자 계정의 암호로

2. 리소스 그룹을 만들고 공급자를 등록하려면 Program 클래스에 다음 메서드를 추가합니다.
   
    ```
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
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

### <a name="create-a-storage-account"></a>저장소 계정 만들기

관리되지 않는 디스크를 사용할 경우 가상 컴퓨터에 대해 생성된 가상 하드 디스크 파일을 저장하는 데 [저장소 계정](../storage/storage-create-storage-account.md)이 필요합니다.

1. 저장소 계정을 만들려면 Program 클래스에 다음 메서드를 추가합니다.

    ```   
    public static async Task<StorageAccount> CreateStorageAccountAsync(
      TokenCredentials credential,       
      string groupName,
      string subscriptionId,
      string location,
      string storageName)
    {
      var storageManagementClient = new StorageManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the storage account...");

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
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```
    var stResult = CreateStorageAccountAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      storageName);
    Console.WriteLine(stResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

공용 IP 주소는 가상 컴퓨터와 통신해야 합니다.

1. 가상 컴퓨터의 공용 IP 주소를 만들려면 Program 클래스에 다음 메서드를 추가합니다.
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
      TokenCredentials credential,  
      string groupName,
      string subscriptionId,
      string location,
      string ipName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the public ip...");

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
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      ipName);
    Console.WriteLine(ipResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

리소스 관리자 배포 모델로 만든 가상 컴퓨터는 가상 네트워크에 있어야 합니다.

1. 서브넷 및 가상 네트워크를 만들려면 Program 클래스에 다음 메서드를 추가합니다.

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string vnetName,
      string subnetName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      var subnet = new Subnet
        {
          Name = subnetName,
          AddressPrefix = "10.0.0.0/24"
        };
   
      var address = new AddressSpace 
        {
          AddressPrefixes = new List<string> { "10.0.0.0/16" }
        };
         
      Console.WriteLine("Creating the virtual network...");
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
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      vnetName,
      subnetName);
    Console.WriteLine(vnResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-network-interface"></a>네트워크 인터페이스 만들기

가상 컴퓨터는 가상 네트워크에서 통신하기 위해 네트워크 인터페이스가 필요합니다.

1. 네트워크 인터페이스를 만들려면 Program 클래스에 다음 메서드를 추가합니다.

    ```   
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
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        vnetName,
        subnetName
      );
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        ipName
      );
   
      Console.WriteLine("Creating the network interface...");
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
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```
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
    ```

### <a name="create-an-availability-set"></a>가용성 집합 만들기

가용성 집합은 응용 프로그램에서 사용되는 가상 컴퓨터의 유지 관리를 쉽게 관리할 수 있도록 합니다.

1. 가용성 집합을 만들려면 Program 클래스에 다음 메서드를 추가합니다.

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string avsetName)
    {
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
        
      Console.WriteLine("Creating the availability set...");
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        avsetName,
        new AvailabilitySet()
          { Location = location }
      );
    }
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      credential,  
      groupName,
      subscriptionId,
      location,
      avSetName);
    Console.ReadLine();
    ```

### <a name="create-a-virtual-machine"></a>가상 컴퓨터 만들기

모든 지원 리소스를 만들었으므로 가상 컴퓨터를 만들 수 있습니다.

1. 가상 컴퓨터를 만들려면 Program 클래스에 다음 메서드를 추가합니다.

    ```   
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
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };  
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        nicName);
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        avsetName);
   
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
    ```

    > [!NOTE]
    > 이 자습서는 Windows Server 운영 체제의 버전을 실행하는 가상 컴퓨터를 만듭니다. 기타 이미지 선택에 대해 자세히 알아보려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 탐색 및 선택](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
    > 
    >

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```
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
    ```

## <a name="step-4-delete-the-resources"></a>4단계: 리소스 삭제

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 가상 컴퓨터 및 모든 지원 리소스를 삭제하려는 경우, 리소스 그룹을 삭제해야 합니다.

1. 리소스 그룹을 삭제하려면 Program 클래스에 다음 메서드를 추가합니다.
   
    ```
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
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```   
    DeleteResourceGroupAsync(
      credential,
      groupName,
      subscriptionId);
    Console.ReadLine();
    ```

## <a name="step-5-run-the-console-application"></a>5단계: 콘솔 응용 프로그램 실행

1. 콘솔 응용 프로그램을 실행하려면, Visual Studio에서 **시작** 을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

2. *성공* 상태가 표시된 후에 **Enter** 키를 누릅니다. 
   
3. 가상 컴퓨터가 만들어진 다음 **Enter** 키를 눌러 리소스를 삭제하기 전에 Azure Portal에서 리소스 만들기를 확인하는 데에 몇 분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [C# 및 Resource Manager 템플릿을 사용하여 Azure 가상 컴퓨터 배포](virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)의 정보를 사용하여 가상 컴퓨터를 만드는 데 템플릿을 활용합니다.
* [Azure Resource Manager 및 C#을 사용하여 Azure Virtual Machines 관리](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 검토하여 자신이 만든 가상 컴퓨터를 관리하는 방법을 알아봅니다.


