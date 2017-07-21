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
ms.date: 06/20/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: f04783896eb36e9a8c3e5cc8422a1fb054b36dd2
ms.contentlocale: ko-kr
ms.lasthandoff: 06/22/2017


---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>C#을 사용하여 Azure에서 Windows VM 생성 및 관리 #

[Azure 가상 컴퓨터](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(VM)에 몇 가지 지원 Azure 리소스가 필요합니다. 이 문서에서는 C#을 사용하여 VM 리소스 만들기, 관리 및 삭제에 대해 설명합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Visual Studio 프로젝트 만들기
> * 라이브러리 설치
> * 자격 증명 만들기
> * 리소스 만들기
> * 관리 작업 수행
> * 리소스 삭제
> * 응용 프로그램 실행

이러한 단계를 수행하려면 약 20분이 걸립니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio)를 아직 설치하지 않았으면 설치합니다. 작업 페이지에서 **.NET 데스크톱 개발**을 선택한 다음 **설치**를 클릭합니다. 요약에서 **.NET Framework 4 - 4.6 개발 도구**가 자동으로 선택되는 것을 볼 수 있습니다. Visual Studio를 이미 설치한 경우 Visual Studio 시작 관리자를 사용하여 .NET 작업을 추가할 수 있습니다.
2. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.
3. **템플릿** > **Visual C#**에서 **콘솔 앱(.NET Framework)**을 선택하고, 프로젝트의 이름에 *myDotnetProject*를 입력하고 프로젝트의 위치를 선택한 다음 **확인**을 클릭합니다.

## <a name="install-libraries"></a>라이브러리 설치

NuGet 패키지는 이러한 단계를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. Visual Studio에서 필요한 라이브러리를 가져오려면 다음 단계를 수행합니다.

1. 솔루션 탐색기에서 *myDotnetProject*를 마우스 오른쪽 단추로 클릭하고 **솔루션의 NuGet 패키지 관리**를 클릭한 다음 **찾아보기**를 클릭합니다.
2. 검색 상자에 *Microsoft.IdentityModel.Clients.ActiveDirectory*를 입력하고 **설치**를 클릭한 후 지침에 따라 패키지를 설치합니다.
3. 검색 상자에 *Microsoft.Azure.Management.Compute*를 입력하고 **설치**를 클릭한 후 지침에 따라 패키지를 설치합니다.
4. 검색 상자에 *Microsoft.Azure.Management.Network*를 입력하고 **설치**를 클릭한 후 지침에 따라 패키지를 설치합니다.
5. 검색 상자에 *Microsoft.Azure.Management.ResourceManager*를 입력하고 **설치**를 클릭한 후 지침에 따라 패키지를 설치합니다.

이제 라이브러리를 사용하기 시작하여 응용 프로그램을 만들 준비가 되었습니다.

## <a name="create-credentials"></a>자격 증명 만들기

이 단계를 시작하기 전에 [Active Directory 서비스 사용자](../../azure-resource-manager/resource-group-create-service-principal-portal.md)에 액세스할 수 있는지 확인합니다. 또한 이후 단계에서 필요한 응용 프로그램 ID, 인증 키 및 테넌트 ID를 기록해야 합니다.

1. 생성된 Program.cs 파일을 연 후, 다음 using 문을 파일 위쪽의 기존 문에 추가합니다.
   
    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Network;
    using Microsoft.Azure.Management.Network.Models;
    using Microsoft.Azure.Management.Compute;
    using Microsoft.Azure.Management.Compute.Models;
    using Microsoft.Rest;
    ```

2. 다음으로 Program.cs 파일의 Main 메서드에서 코드에 사용되는 공통 값을 지정하도록 변수를 추가합니다.

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciption-id";
    var location = "westus";
    var vmName = "myVM";
    ```

    **subscription-id**를 구독 ID로 바꿉니다.

3. 요청을 해야 하는 Active Directory 자격 증명을 만들려면 Program 클래스에 다음 메서드를 추가합니다.
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("application-id", "authentication-key");
      var context = new AuthenticationContext("https://login.windows.net/tenant-id");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    **application-id**, **authentication-key** 및 **tenant-id**를 Azure Active Directory 서비스 사용자를 만들 때 이전에 수집한 값으로 바꿉니다.

4. 이전에 추가한 메서드를 호출하려면 Program.cs 파일의 Main 메서드에 다음 코드를 추가합니다.
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

## <a name="create-resources"></a>리소스 만들기

### <a name="initialize-management-clients"></a>관리 클라이언트 초기화

Azure에서 .NET SDK를 사용하여 리소스를 만들고 관리하는 데 관리 클라이언트가 필요합니다. 관리 클라이언트를 만들려면 Program.cs 파일에서 Main 메서드에 다음 코드를 추가합니다.

```
var resourceManagementClient = new ResourceManagementClient(credential)
    { SubscriptionId = subscriptionId };
var networkManagementClient = new NetworkManagementClient(credential)
    { SubscriptionId = subscriptionId };
var computeManagementClient = new ComputeManagementClient(credential)
    { SubscriptionId = subscriptionId };
```

### <a name="create-the-vm-and-supporting-resources"></a>VM 및 지원 리소스 만들기

모든 리소스는 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md)에 포함되어야 합니다.

1. 리소스 그룹을 만들려면 Program 클래스에 다음 메서드를 추가합니다.
   
    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      ResourceManagementClient resourceManagementClient,
      string groupName,
      string location)
    {   
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      resourceManagementClient,
      groupName,
      location);
    Console.WriteLine("Resource group creation: " + 
      rgResult.Result.Properties.ProvisioningState + 
      ". Press enter to continue...");
    Console.ReadLine();
    ```

[가용성 집합](tutorial-availability-sets.md)은 응용 프로그램에서 사용되는 가상 컴퓨터를 쉽게 유지 관리할 수 있도록 합니다.

1. 가용성 집합을 만들려면 Program 클래스에 다음 메서드를 추가합니다.

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      ComputeManagementClient computeManagementClient,
      string groupName,
      string location)
    {
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        "myAVSet",
        new AvailabilitySet()
        { 
          Sku = new Microsoft.Azure.Management.Compute.Models.Sku("Aligned"),
          PlatformFaultDomainCount = 3,
          Location = location 
        });
    }
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      computeManagementClient,
      groupName,
      location);
    Console.WriteLine("Availability set created. Press enter to continue...");
    Console.ReadLine();
    ```

[공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)는 가상 컴퓨터와 통신해야 합니다.

1. 가상 컴퓨터의 공용 IP 주소를 만들려면 Program 클래스에 다음 메서드를 추가합니다.
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync( 
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
        groupName,
        "myIpAddress",
        new PublicIPAddress
        {
          Location = location,
          PublicIPAllocationMethod = "Dynamic"
        });
    }
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("IP address creation: " + 
      ipResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

가상 컴퓨터는 [가상 네트워크](../../virtual-network/virtual-networks-overview.md)의 서브넷에 있어야 합니다.

1. 서브넷 및 가상 네트워크를 만들려면 Program 클래스에 다음 메서드를 추가합니다.

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      var subnet = new Subnet
      {
        Name = "mySubnet",
        AddressPrefix = "10.0.0.0/24"
      };
      var address = new AddressSpace 
      {
        AddressPrefixes = new List<string> { "10.0.0.0/16" }
      };
      return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
        groupName,
        "myVNet",
        new VirtualNetwork
        {
          Location = location,
          AddressSpace = address,
          Subnets = new List<Subnet> { subnet }
        });
    }
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("Virtual network creation: " + 
      vnResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

가상 컴퓨터는 가상 네트워크에서 통신하기 위해 네트워크 인터페이스가 필요합니다.

1. 네트워크 인터페이스를 만들려면 Program 클래스에 다음 메서드를 추가합니다.

    ```   
    public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
      NetworkManagementClient networkManagementClient,
      string groupName,
      string location)
    {
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        "myVNet",
        "mySubnet");
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        "myIPaddress");
      return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
        groupName,
        "myNic",
        new NetworkInterface
        {
          Location = location,
          IpConfigurations = new List<NetworkInterfaceIPConfiguration>
          {
            new NetworkInterfaceIPConfiguration
            {
              Name = "myNic",
              PublicIPAddress = publicIP,
              Subnet = subnet
            }
          }
        });
    }
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```
    var ncResult = CreateNetworkInterfaceAsync(
      networkManagementClient,
      groupName,
      location);
    Console.WriteLine("Network interface creation: " + 
      ncResult.Result.ProvisioningState + 
      ". Press enter to continue...");  
    Console.ReadLine();
    ```

모든 지원 리소스를 만들었으므로 가상 컴퓨터를 만들 수 있습니다.

1. 가상 컴퓨터를 만들려면 Program 클래스에 다음 메서드를 추가합니다.

    ```   
    public static async Task<VirtualMachine> CreateVirtualMachineAsync(
      NetworkManagementClient networkManagementClient,
      ComputeManagementClient computeManagementClient,
      string groupName,
      string location)
    {
 
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        "myNic");
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        "myAVSet");
      return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
        groupName,
        "myVM",
        new VirtualMachine
        {
          Location = location,
          AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
          {
            Id = avSet.Id
          },
          HardwareProfile = new HardwareProfile
          {
            VmSize = "Standard_DS1"
          },
          OsProfile = new OSProfile
          {
            AdminUsername = "azureuser",
            AdminPassword = "Azure12345678",
            ComputerName = "myVM",
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
            }
          }
        });
    }
    ```

    > [!NOTE]
    > 이 자습서는 Windows Server 운영 체제의 버전을 실행하는 가상 컴퓨터를 만듭니다. 기타 이미지 선택에 대해 자세히 알아보려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 탐색 및 선택](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
    > 
    >

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```
    var vmResult = CreateVirtualMachineAsync(
      networkManagementClient,
      computeManagementClient,
      groupName,
      location);
    Console.WriteLine("Virtual machine creation: " + 
      vmResult.Result.ProvisioningState + 
      ". Press enter to continue...");
    Console.ReadLine();
    ```

## <a name="perform-management-tasks"></a>관리 작업 수행

가상 컴퓨터의 수명 주기 동안 가상 컴퓨터 시작, 중지 또는 삭제 등의 관리 작업을 실행하려고 할 수 있습니다. 또한 반복적이거나 복잡한 작업을 자동화하는 코드를 만들 수도 있습니다.

### <a name="get-information-about-the-vm"></a>VM 관련 정보 가져오기

1. 가상 컴퓨터에 대한 정보를 가져오려면 Program 클래스에 다음 메서드를 추가합니다.

    ```
    public static async void GetVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Getting information about the virtual machine...");

      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
        groupName,
        vmName,
        InstanceViewTypes.InstanceView);

      Console.WriteLine("hardwareProfile");
      Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);

      Console.WriteLine("\nstorageProfile");
      Console.WriteLine("  imageReference");
      Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
      Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
      Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
      Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
      Console.WriteLine("  osDisk");
      Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
      Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
      Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
      Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);
      Console.WriteLine("\nosProfile");
      Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
      Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
      Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
      Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);

      Console.WriteLine("\nnetworkProfile");
      foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
      {
        Console.WriteLine("  networkInterface id: " + nic.Id);
      }

      Console.WriteLine("\nvmAgent");
      Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
      Console.WriteLine("    statuses");
      foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
      {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    message: " + stat.Message);
        Console.WriteLine("    time: " + stat.Time);
      }

      Console.WriteLine("\ndisks");
      foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
      {
        Console.WriteLine("  name: " + idisk.Name);
        Console.WriteLine("  statuses");
        foreach (InstanceViewStatus istat in idisk.Statuses)
        {
          Console.WriteLine("    code: " + istat.Code);
          Console.WriteLine("    level: " + istat.Level);
          Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
          Console.WriteLine("    time: " + istat.Time);
        }
      }

      Console.WriteLine("\nVM general status");
      Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
      Console.WriteLine("  id: " + vmResult.Id);
      Console.WriteLine("  name: " + vmResult.Name);
      Console.WriteLine("  type: " + vmResult.Type);
      Console.WriteLine("  location: " + vmResult.Location);
      Console.WriteLine("\nVM instance status");
      foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
      {
        Console.WriteLine("\n  code: " + istat.Code);
        Console.WriteLine("  level: " + istat.Level);
        Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
      }
    }
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```
    GetVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="stop-the-vm"></a>VM을 중지합니다.

가상 컴퓨터를 중지하고 해당 설정을 모두 그대로 유지하면 계속 요금이 청구될 수 있습니다. 그렇지 않으려면 가상 컴퓨터를 중지하고 할당을 해제합니다. 가상 컴퓨터를 할당을 해제하면 연결된 모든 리소스의 할당이 취소되고 대금 청구가 끝납니다.

1. 할당 취소하지 않고 가상 컴퓨터를 중지하려면 Program 클래스에 다음 메서드를 추가합니다.

    ```
    public static async void StopVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Stopping the virtual machine...");
      await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
    }
    ```

    가상 컴퓨터의 할당을 취소하려는 경우 PowerOff 호출을 이 코드로 변경합니다.

    ```
    await computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
    ```

2. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```
    StopVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="start-the-vm"></a>VM 시작

1. 가상 컴퓨터를 시작하려면 Program 클래스에 다음 메서드를 추가합니다.

    ```
    public static async void StartVirtualMachineAsync(
      string groupName, 
      string vmName, 
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Starting the virtual machine...");
      await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
    }
    ```

2. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```
    StartVirtualMachineAsync(
      groupName,
      vmName,
      ComputeManagementClient computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="resize-the-vm"></a>VM 크기 조정

가상 컴퓨터의 크기를 결정할 때 배포의 여러 측면을 고려해야 합니다. 자세한 내용은 [VM 크기](sizes.md)를 참조하세요.  

1. 가상 컴퓨터의 크기를 변경하려면 Program 클래스에 다음 메서드를 추가합니다.

    ```
    public static async void UpdateVirtualMachineAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Updating the virtual machine...");
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.HardwareProfile.VmSize = "Standard_DS3";
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

2. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```
    UpdateVirtualMachineAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

### <a name="add-a-data-disk-to-the-vm"></a>VM에 데이터 디스크 추가

1. 가상 컴퓨터에 데이터 디스크를 추가하려면 Program 클래스에 다음 메서드를 추가합니다.

    ```
    public static async void AddDataDiskAsync(
      string groupName,
      string vmName,
      ComputeManagementClient computeManagementClient)
    {
      Console.WriteLine("Adding the disk to the virtual machine...");
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.StorageProfile.DataDisks.Add(
        new DataDisk
        {
          Lun = 0,
          Name = "myDataDisk1",
          CreateOption = DiskCreateOptionTypes.Empty,
          DiskSizeGB = 2,
          Caching = CachingTypes.ReadWrite
        });
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

2. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```
    AddDataDiskAsync(
      groupName,
      vmName,
      computeManagementClient);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

## <a name="delete-resources"></a>리소스 삭제

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 가상 컴퓨터 및 모든 지원 리소스를 삭제하려는 경우, 리소스 그룹을 삭제해야 합니다.

1. 리소스 그룹을 삭제하려면 Program 클래스에 다음 메서드를 추가합니다.
   
    ```
    public static async void DeleteResourceGroupAsync(
      ResourceManagementClient resourceManagementClient,
      string groupName)
    {
      Console.WriteLine("Deleting resource group...");
      await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
    }
    ```

2. 이전에 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
   
    ```   
    DeleteResourceGroupAsync(
      resourceManagementClient,
      groupName);
    Console.ReadLine();
    ```

3. 프로젝트를 저장합니다.

## <a name="run-the-application"></a>응용 프로그램 실행

1. 콘솔 응용 프로그램을 실행하려면, Visual Studio에서 **시작** 을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

2. 각 리소스의 상태가 반환된 후 **Enter** 키를 누릅니다. 상태 정보에 **Succeeded** 프로비저닝 상태가 표시됩니다. 가상 컴퓨터를 만든 후 만든 모든 리소스를 삭제할 기회가 있습니다. **Enter** 키를 눌러 리소스를 삭제하기 전에 Azure Portal에서 만들기를 확인하는 데에 몇 분이 걸릴 수 있습니다. Azure Portal이 열려 있는 경우 새 리소스를 보려면 블레이드를 새로 고쳐야 할 수 있습니다.  

    이 콘솔 응용 프로그램을 처음부터 끝까지 완전히 실행하려면 약 5분이 필요합니다. 응용 프로그램이 종료된 후 모든 리소스 및 리소스 그룹이 삭제되기 전에 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [C# 및 Resource Manager 템플릿을 사용하여 Azure 가상 컴퓨터 배포](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)의 정보를 사용하여 가상 컴퓨터를 만드는 데 템플릿을 활용합니다.
* [.NET용 Azure 라이브러리](https://docs.microsoft.com/dotnet/azure/index?view=azure-dotnet) 사용에 대해 자세히 알아봅니다.


