---
title: "Azure Resource Manager 및 C#을 사용하여 VM 관리 | Microsoft Docs"
description: "Azure Resource Manager 및 C#을 사용하여 가상 컴퓨터를 관리합니다."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 93898bad-b861-4359-9a4b-348e1d491822
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: e96eacd689ead229d0c48d15f53a932b903c5281
ms.openlocfilehash: d32dd39b7835e327e5bc1a40237da43617f6e61c
ms.lasthandoff: 03/02/2017


---
# <a name="manage-azure-virtual-machines-using-azure-resource-manager-and-c"></a>Azure Resource Manager 및 C를 사용하여 Azure 가상 컴퓨터 관리# #

이 문서의 작업은 가상 컴퓨터의 시작, 중지 및 업데이트와 같은 가상 컴퓨터 관리 방법을 보여 줍니다.

## <a name="set-up-visual-studio"></a>Visual Studio 설정

### <a name="create-a-project"></a>프로젝트 만들기

Visual Studio가 설치되어 있는지 확인하고 가상 컴퓨터를 관리하는 데 사용한 콘솔 응용 프로그램을 만듭니다.

1. [Visual Studio](https://www.visualstudio.com/)를 아직 설치하지 않았으면 설치합니다.
2. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.
3. **템플릿** > **Visual C#**에서 **콘솔 응용 프로그램**을 선택하고, 프로젝트의 이름과 위치를 입력한 다음 **확인**을 클릭합니다.

### <a name="install-libraries"></a>라이브러리 설치

NuGet 패키지는 이 문서에서 작업을 수행하는 데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. Visual Studio에서 필요한 라이브러리를 가져오려면 다음 단계를 수행합니다.

1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭하고 **찾아보기**를 클릭합니다.
2. 검색 상자에 *Microsoft.IdentityModel.Clients.ActiveDirectory*를 입력하고 **설치**를 클릭한 후 지침에 따라 패키지를 설치합니다.
3. 페이지의 위쪽에서 **시험판 포함**을 선택합니다. 검색 상자에 *Microsoft.Azure.Management.Compute*를 입력하고 **설치**를 클릭한 후 지침에 따라 패키지를 설치합니다.

이제 가상 컴퓨터를 관리하기 위해 라이브러리를 사용할 준비가 되었습니다.

### <a name="create-credentials-and-add-variables"></a>자격 증명 만들기 및 변수 추가

Azure Resource Manager와 상호 작용하려면 [Active Directory 서비스 주체](../azure-resource-manager/resource-group-authenticate-service-principal.md)에 액세스할 수 있는지 확인합니다. 서비스 주체에서 Azure Resource Manager에서 요청을 인증받기 위한 토큰을 얻을 수 있습니다.

1. 만들었던 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 추가합니다.

    ```   
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.Compute;
    using Microsoft.Azure.Management.Compute.Models;
    using Microsoft.Rest;
    ```

2. 변수를 Program 클래스의 Main 메서드에 추가하여 리소스 그룹 이름, 가상 컴퓨터 이름 및 구독 식별자를 지정합니다.

    ```   
    var groupName = "myResourceGroup";
    var vmName = "myVM";  
    var subscriptionId = "subsciptionId";
    ```

    Azure Portal의 구독 블레이드에서 구독 식별자를 찾을 수 있습니다.    

3. 자격 증명을 만드는 데 필요한 토큰을 가져오려면 Program 클래스에 다음 메서드를 추가합니다.
   
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
    - *{client-secret}*을 AD 응용 프로그램의 선택키 이 식별자는 AD 응용 프로그램의 속성 블레이드에서 찾을 수 있습니다.
    - *{tenant-id}*를 구독의 테넌트 식별자로 Azure Portal의 Azure Active Directory에 대한 속성 블레이드에서 테넌트 식별자를 찾을 수 있습니다. *디렉터리 ID*로 레이블이 지정되어 있습니다.

4. 이전에 추가한 메서드를 호출하려면 Program.cs 파일의 Main 메서드에 다음 코드를 추가합니다.
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

5. Program.cs 파일을 저장합니다.

## <a name="display-information-about-a-virtual-machine"></a>가상 컴퓨터에 대한 정보 표시

1. 이전에 만든 프로젝트의 Program 클래스에 이 메서드를 추가합니다.

    ```   
    public static async void GetVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Getting information about the virtual machine...");
   
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
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
      Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
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

2. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```   
    GetVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

3. Program.cs 파일을 저장합니다.

4. Visual Studio에서 **시작** 을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.
   
    이 메서드를 실행할 때 이 예제와 유사한 결과가 표시됩니다.
   
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_D1_v2
   
        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite
   
          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True
   
          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1
   
          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM
   
          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM
   
          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus
   
          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## <a name="stop-a-virtual-machine"></a>가상 컴퓨터 중지

두 가지 방법으로 가상 컴퓨터를 중지할 수 있습니다. 가상 컴퓨터를 중지하고 해당 설정을 모두 그대로 유지하면 계속 요금이 청구될 수 있습니다. 그렇지 않으려면 가상 컴퓨터를 중지하고 할당을 해제합니다. 가상 컴퓨터를 할당을 해제하면 연결된 모든 리소스의 할당이 취소되고 대금 청구가 끝납니다.

1. 자격 증명을 가져오는 코드를 제외하고 이전에 Main 메서드에 추가한 코드를 주석으로 처리합니다.

2. Program 클래스에 다음 메서드를 추가합니다.

    ```   
    public static async void StopVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Stopping the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
    }
    ```

    가상 컴퓨터의 할당을 취소하려는 경우 PowerOff 호출을 이 코드로 변경합니다.
    
    ```
    computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);
    ```

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
    
    ```
    StopVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Program.cs 파일을 저장합니다.

5. Visual Studio에서 **시작** 을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.
   
    가상 컴퓨터의 상태가 중지됨으로 변경되어 표시됩니다. 할당 취소를 호출하는 메서드를 실행하면 상태가 중지됨(할당 취소됨)으로 됩니다.

## <a name="start-a-virtual-machine"></a>가상 컴퓨터 시작

1. 자격 증명을 가져오는 코드를 제외하고 이전에 Main 메서드에 추가한 코드를 주석으로 처리합니다.

2. Program 클래스에 다음 메서드를 추가합니다.

    ```   
    public static async void StartVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Starting the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
    }
    ```

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```   
    StartVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Program.cs 파일을 저장합니다.

5. Visual Studio에서 **시작** 을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.
   
    가상 컴퓨터의 상태가 실행 중으로 변경되어 표시됩니다.

## <a name="restart-a-running-virtual-machine"></a>실행 중인 가상 컴퓨터 다시 시작

1. 자격 증명을 가져오는 코드를 제외하고 이전에 Main 메서드에 추가한 코드를 주석으로 처리합니다.

2. Program 클래스에 다음 메서드를 추가합니다.

    ```   
    public static async void RestartVirtualMachineAsync(
      TokenCredentials credential,
      string groupName,
      string vmName,
      string subscriptionId)
    {
      Console.WriteLine("Restarting the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
    }
    ```

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```   
    RestartVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Program.cs 파일을 저장합니다.

5. Visual Studio에서 **시작** 을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

## <a name="resize-a-virtual-machine"></a>가상 컴퓨터 크기 조정

이 예제에서는 실행 중인 가상 컴퓨터의 크기를 변경하는 방법을 보여 줍니다.

1. 자격 증명을 가져오는 코드를 제외하고 이전에 Main 메서드에 추가한 코드를 주석으로 처리합니다.

2. Program 클래스에 다음 메서드를 추가합니다.

    ```   
    public static async void UpdateVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Updating the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.HardwareProfile.VmSize = "Standard_D2_v2";
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```   
    UpdateVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Program.cs 파일을 저장합니다.

5. Visual Studio에서 **시작** 을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.
   
    가상 컴퓨터의 크기가 Standard_D2_v2로 변경되어 표시되어야 합니다.

## <a name="add-a-data-disk-to-a-virtual-machine"></a>데이터 디스크를 가상 컴퓨터에 추가

이 예제에서는 실행 중인 가상 컴퓨터에 데이터 디스크를 추가하는 방법을 보여 줍니다.

1. 자격 증명을 가져오는 코드를 제외하고 이전에 Main 메서드에 추가한 코드를 주석으로 처리합니다.

2. Program 클래스에 다음 메서드를 추가합니다.

    ```   
    public static async void AddDataDiskAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Adding the disk to the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
      vmResult.StorageProfile.DataDisks.Add(
        new DataDisk
          {
            Lun = 0,
            Name = "mydatadisk1",
            Vhd = new VirtualHardDisk
              {
                Uri = "https://mystorage1.blob.core.windows.net/vhds/mydatadisk1.vhd"
              },
            CreateOption = DiskCreateOptionTypes.Empty,
            DiskSizeGB = 2,
            Caching = CachingTypes.ReadWrite
          });
      await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
    }
    ```

    mystorage1을 디스크가 가상 컴퓨터용으로 저장되어 있는 저장소 계정 이름으로 바꿉니다. 

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
    
    ```
    AddDataDiskAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Program.cs 파일을 저장합니다.

5. Visual Studio에서 **시작** 을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

## <a name="delete-a-virtual-machine"></a>가상 컴퓨터 삭제

1. 자격 증명을 가져오는 코드를 제외하고 이전에 Main 메서드에 추가한 코드를 주석으로 처리합니다.

2. Program 클래스에 다음 메서드를 추가합니다.
   
    ```
    public static async void DeleteVirtualMachineAsync(
      TokenCredentials credential, 
      string groupName, 
      string vmName, 
      string subscriptionId)
    {
      Console.WriteLine("Deleting the virtual machine...");
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
    }
    ```

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
    
    ```
    DeleteVirtualMachineAsync(
      credential,
      groupName,
      vmName,
      subscriptionId);
    Console.WriteLine("\nPress enter to continue...");
    Console.ReadLine();
    ```

4. Program.cs 파일을 저장합니다.

5. Visual Studio에서 **시작** 을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

## <a name="next-steps"></a>다음 단계

- 배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md)을 살펴봅니다.
- [C#를 사용하여 Azure Virtual Machine 배포](virtual-machines-windows-csharp.md)를 검토하여 가상 컴퓨터 및 지원 리소스 배포 방법에 대해 알아 봅니다.
- [C# 및 Resource Manager 템플릿을 사용하여 Azure 가상 컴퓨터 배포](virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)의 정보를 사용하여 가상 컴퓨터를 만드는 데 템플릿을 활용합니다.



