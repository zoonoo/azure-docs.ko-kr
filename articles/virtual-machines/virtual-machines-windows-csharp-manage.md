<properties
	pageTitle="Azure Resource Manager 및 C#을 사용하여 VM 관리 | Microsoft Azure"
	description="Azure Resource Manager 및 C#을 사용하여 가상 컴퓨터를 관리합니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/24/2016"
	ms.author="davidmu"/>

# Azure Resource Manager 및 C를 사용하여 Azure 가상 컴퓨터 관리#  

이 문서의 작업은 가상 컴퓨터의 시작, 중지 및 업데이트와 같은 가상 컴퓨터 관리 방법을 보여 줍니다.

이 문서에서 작업을 완료하려면 다음이 필요합니다.

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [인증 토큰](../resource-group-authenticate-service-principal.md)

## Visual Studio 프로젝트를 만들고 패키지를 설치합니다.

NuGet 패키지는 이 문서를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. Azure Active Directory 인증 라이브러리 및 컴퓨터 리소스 공급자 라이브러리를 설치해야 합니다. Visual Studio에서 이 라이브러리를 가져오려면 다음을 수행합니다.

1. **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.

2. **템플릿** > **Visual C#**에서 **콘솔 응용 프로그램**을 선택하고, 프로젝트의 이름과 위치를 입력한 다음 **확인**을 클릭합니다.

3. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

4. 검색 상자에 *Active Directory*를 입력하고 Active Directory 인증 라이브러리 패키지에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

5. 페이지의 위쪽에서 **시험판 포함**을 선택합니다. 검색 상자에 *Microsoft.Azure.Management.Compute*를 입력하고 계산 .NET 라이브러리에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

이제 가상 컴퓨터를 관리하기 위해 라이브러리를 사용할 준비가 되었습니다.

## 프로젝트 설정

Azure Active Directory 응용 프로그램이 생성되고 인증 라이브러리가 설치되므로 Azure 리소스 관리자에 요청을 인증하는데 사용되는 자격 증명에 대한 응용 프로그램 정보의 서식을 지정합니다.

1. 사용자가 만든 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 추가합니다.

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
        
2. 변수를 Program 클래스의 Main 메서드에 추가하여 "미국 중부"와 같은 리소스의 위치 및 구독 식별자를 관리하려는 리소스 이름을 지정합니다.

        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var location = "location name";
        var subscriptionId = "subsciption id";

    모든 변수 값을 사용하려는 이름 및 식별자로 바꿉니다. Get-AzureRmSubscription을 실행하여 구독 식별자를 찾을 수 있습니다.
    
3. Program 클래스에 다음 메서드를 추가하여 자격 증명을 만드는 데 필요한 토큰을 가져옵니다.

	    private static async Task<AuthenticationResult> GetAccessTokenAsync()
	    {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var result = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (result == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return result;
        }
	
    {client-id}를 Azure Active Directory의 식별자로 바꾸고 {client-secret}을 AD 응용 프로그램의 선택키로 바꾸고, {tenant-id}를 구독의 테넌트 식별자로 바꿉니다. Get-AzureRmSubscription을 실행하여 테넌트 ID를 찾을 수 있습니다. 선택키는 Azure 포털을 사용하여 찾을 수 있습니다.
    
4. Program.cs의 Main 메서드에 다음 코드를 추가하여 자격 증명을 만듭니다.

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

5. Program.cs 파일을 저장합니다.

## 가상 컴퓨터에 대한 정보 표시

1. 이전에 만든 프로젝트의 Program 클래스에 이 메서드를 추가합니다.

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

2. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
    
3. Program.cs 파일을 저장합니다.

4. Visual Studio에서 **시작**을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

	이 메서드를 실행할 때 다음과 유사한 결과가 표시됩니다.
    
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0

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

## 가상 컴퓨터 시작

1. 자격 증명을 가져오는 코드를 제외하고 이전에 Main 메서드에 추가한 코드를 주석으로 처리합니다.

2. Program 클래스에 다음 메서드를 추가합니다.

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

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Program.cs 파일을 저장합니다.

5. Visual Studio에서 **시작**을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

	가상 컴퓨터의 상태가 실행 중으로 변경되어 표시됩니다.

## 가상 컴퓨터 중지

두 가지 방법으로 가상 컴퓨터를 중지할 수 있습니다. 가상 컴퓨터를 중지하고 모든 설정을 유지하지만 이에 대한 비용이 계속 청구되도록 할 수 있거나 가상 컴퓨터를 중지하고 가상 컴퓨터와 연결된 모든 리소스의 할당을 취소하여 가상 컴퓨터에 대한 청구를 종료할 수 있습니다.

1. 자격 증명을 가져오는 코드를 제외하고 이전에 Main 메서드에 추가한 코드를 주석으로 처리합니다.

2. Program 클래스에 다음 메서드를 추가합니다.

        public static void StopVirtualMachineAsync(
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

	가상 컴퓨터의 할당을 취소하려는 경우 PowerOff 호출을 다음으로 변경합니다.

        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Program.cs 파일을 저장합니다.

5. Visual Studio에서 **시작**을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

    가상 컴퓨터의 상태가 중지됨으로 변경되어 표시됩니다. 할당 취소를 호출하는 메서드를 실행하면 상태가 중지됨(할당 취소됨)으로 됩니다.

## 실행 중인 가상 컴퓨터 다시 시작

1. 자격 증명을 가져오는 코드를 제외하고 이전에 Main 메서드에 추가한 코드를 주석으로 처리합니다.

2. Program 클래스에 다음 메서드를 추가합니다.

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

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Program.cs 파일을 저장합니다.

5. Visual Studio에서 **시작**을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

## 가상 컴퓨터 삭제

1. 자격 증명을 가져오는 코드를 제외하고 이전에 Main 메서드에 추가한 코드를 주석으로 처리합니다.

2. Program 클래스에 다음 메서드를 추가합니다.

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

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Program.cs 파일을 저장합니다.

5. Visual Studio에서 **시작**을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

## 가상 컴퓨터 업데이트

이 예제에서는 실행 중인 가상 컴퓨터의 크기를 변경하는 방법을 보여 줍니다.

1. 자격 증명을 가져오는 코드를 제외하고 이전에 Main 메서드에 추가한 코드를 주석으로 처리합니다.

2. Program 클래스에 다음 메서드를 추가합니다.

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
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Program.cs 파일을 저장합니다.

5. Visual Studio에서 **시작**을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

    가상 컴퓨터의 크기가 Standard\_A1로 변경되어 표시됩니다.
    
## 다음 단계

배포에 문제가 있는 경우 [Azure 포털을 사용하여 리소스 그룹 배포 문제 해결](../resource-manager-troubleshoot-deployments-portal.md)을 살펴보세요.

<!---HONumber=AcomDC_0720_2016-->