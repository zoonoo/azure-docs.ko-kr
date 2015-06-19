<properties 
	pageTitle="계산, 네트워크 및 저장소 .NET 라이브러리를 사용하여 Azure 리소스를 배포합니다." 
	description="계산, 저장소 및 네트워크 .NET 라이브러리에서 사용할 수 있는 클라이언트 중 일부를 사용하여 Microsoft Azure의 리소스를 만들고 삭제하는 방법에 대해 알아봅니다." 
	services="virtual-machines,virtual-network,storage" 
	documentationCenter="" 
	authors="davidmu1" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="davidmu"/>

# 계산, 네트워크 및 저장소 .NET 라이브러리를 사용하여 Azure 리소스를 배포합니다.

이 자습서에서는 계산, 저장소 및 네트워크 .NET 라이브러리에서 사용할 수 있는 클라이언트 중 일부를 사용하여 Microsoft Azure의 리소스를 만들고 삭제하는 방법을 보여줍니다. 또한 Azure Active Directory를 사용하여 Azure 리소스 관리자에 요청을 인증하는 방법을 보여줍니다.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

이 자습서를 완료하려면 다음이 필요합니다.

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure 저장소 계정](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/ko-kr/download/details.aspx?id=34595) 또는 [Windows Management Framework 4.0](http://www.microsoft.com/ko-kr/download/details.aspx?id=40855)
- [Azure PowerShell](../install-configure-powershell.md)

이러한 단계를 수행하려면 약 30분이 걸립니다.

## 1단계: Azure AD에 응용 프로그램 추가 및 사용 권한 설정

Azure AD를 사용하여 Azure 리소스 관리자에 요청을 인증하려면, 응용 프로그램이 기본 디렉터리에 추가되어야 합니다. 응용 프로그램을 추가하려면 다음을 수행 합니다.

1. Azure PowerShell 프롬프트를 열고 이 명령을 실행합니다.

        Switch-AzureMode –Name AzureResourceManager

2. 이 자습서에 사용하려는 Azure 계정을 설정합니다. 이 명령을 실행하고 메시지가 표시되면 구독에 대한 자격 증명을 입력합니다.

	    Add-AzureAccount

3. 다음 명령에서 {password}를 사용할 명령으로 바꾼 다음 실행하여 응용 프로그램을 만듭니다.

	    New-AzureADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

4. 이전 단계의 응답에서 ApplicationId 값을 기록합니다. 이 값은 자습서의 뒷부분에서 필요합니다.

	![AD 응용 프로그램 만들기](./media/virtual-machines-arm-deployment/azureapplicationid.png)

	>[AZURE.NOTE]또한 관리 포털에서 응용 프로그램의 클라이언트 id 필드에서 응용 프로그램 ID를 찾을 수 있습니다.

5. {application-id}를 방금 기록된 식별자로 바꾼 다음 해당 응용 프로그램에 대한 서비스 사용자를 만듭니다.

        New-AzureADServicePrincipal -ApplicationId {application-id} 

6. 응용 프로그램을 사용하는 사용 권한을 설정합니다.

	    New-AzureRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## 2단계: Visual Studio 프로젝트를 만들고 라이브러리 설치

NuGet 패키지는 이 자습서를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. Azure 리소스 관리 라이브러리, Azure Active Directory 인증 라이브러리 및 컴퓨터 리소스 공급자 라이브러리를 설치해야 합니다. Visual Studio에서 이 라이브러리를 가져오려면 다음을 수행합니다.

1. **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.

2. **템플릿** > **Visual C#**에서 **콘솔 응용 프로그램**을 선택하고, 프로젝트의 이름과 위치를 입력한 다음 **확인**을 클릭합니다.

3. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

4. 검색 상자에 *Active Directory*를 입력하고 Active Directory 인증 라이브러리 패키지에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

5. 페이지의 위쪽에서 **시험판 포함**을 선택합니다. 검색 상자에 *Azure 계산*을 입력하고 계산 .NET 라이브러리에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

6. 검색 상자에 *Azure 네트워크*를 입력하고 네트워크 .NET 라이브러리에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

7. 검색 상자에 *Azure 저장소*를 입력하고 네트워크 .NET 라이브러리에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

8. 검색 상자에 *Azure 리소스 관리*를 입력하고 리소스 관리 라이브러리에 대해 **설치**를 클릭합니다.

이제 라이브러리를 사용하기 시작하여 응용 프로그램을 만들 준비가 되었습니다.

## 3단계: 요청을 인증하는데 사용되는 자격 증명 만들기

Azure Active Directory 응용 프로그램이 생성되고 인증 라이브러리가 설치되므로 Azure 리소스 관리자에 요청을 인증하는데 사용되는 자격 증명에 대한 응용 프로그램 정보의 서식을 지정합니다. 다음을 수행합니다.

1.	사용자가 만든 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 추가합니다.

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure.Management.Resources;
		using Microsoft.Azure.Management.Resources.Models;
		using Microsoft.Azure.Management.Storage;
		using Microsoft.Azure.Management.Storage.Models;
		using Microsoft.Azure.Management.Network;
		using Microsoft.Azure.Management.Network.Models;
		using Microsoft.Azure.Management.Compute;
		using Microsoft.Azure.Management.Compute.Models;


2. 자격 증명을 만드는 데 필요한 토큰을 가져올 프로그램 클래스에 다음 메서드를 추가합니다.

		private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);
          
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	{application-id}를 이전에 기록한 응용 프로그램 ID로 바꾸고, {password}를 AD 응용 프로그램에 대해 선택한 암호로 바꾸고, {tenant-id}를 구독을 위한 테넌트 ID로 바꿉니다. Get-AzureSubscription을 실행하여 테넌트 ID를 찾을 수 있습니다.

3.	Program.cs 파일에서 Main 메서드에 다음 코드를 추가하여 자격 증명을 만듭니다.

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

	{subscription-id}를 구독 ID로 바꿉니다.

4.	Program.cs 파일을 저장합니다.

## 4단계: 코드를 추가하여 공급자를 등록하고 리소르르 만듭니다.

### 공급자 등록 및 리소스 그룹 만들기

리소스는 항상 리소스 그룹에 배포됩니다. [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) 및 [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) 클래스를 사용하여 리소스가 배포되는 리소스 그룹을 만듭니다.

1.	Program 클래스에 다음 메서드를 추가하여 리소스 그룹을 만듭니다.

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");
		  
          using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", new ResourceGroup { Location = "West US" });
            Console.WriteLine(rgResult.StatusCode);
            var rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Storage");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Network");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Compute");
            Console.WriteLine(rpResult.StatusCode);
		  }
		}

2.	방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

		CreateResourceGroup(credential);
		Console.ReadLine();

###저장소 계정 만들기

가상 컴퓨터에 대해 생성된 가상 하드 디스크 파일을 저장하는 데 저장소 계정이 필요합니다.

1.	Program 클래스에 다음 메서드를 추가하여 저장소 계정을 만듭니다.

		public async static void CreateStorageAccount(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the storage account...");
          
          using (var storageManagementClient = new StorageManagementClient(credential))
          {
            var saResult = await storageManagementClient.StorageAccounts.CreateAsync(
              "mytestrg1",
              "mytestsa1",
              new StorageAccountCreateParameters()
              { AccountType = AccountType.StandardLRS, Location = "West US" } );
            Console.WriteLine(saResult.StatusCode);
          }
        }

3.	방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.
		
		CreateStorageAccount(credential);
		Console.ReadLine();

###가상 네트워크 만들기

가상 컴퓨터는 가상 네트워크에 추가될 때가 가장 생산적입니다.

1.	Program 클래스에 다음 메서드를 추가하여 서브넷, 공용 IP 주소 및 가상 네트워크를 만듭니다.

		public async static void CreateNetwork(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the virtual network...");
          using (var networkClient = new NetworkResourceProviderClient(credential))
          {
            var subnet = new Subnet
            {
              Name = "mytestsn1",
              AddressPrefix = "10.0.0.0/24"
            };

            var vnResult = await networkClient.VirtualNetworks.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestvn1",
              new VirtualNetwork
              {
                Location = "West US",
                AddressSpace = new AddressSpace { AddressPrefixes = new List<string> { "10.0.0.0/16" } },
                Subnets = new List<Subnet> { subnet }
              });
            Console.WriteLine(vnResult.StatusCode);

            var subnetResponse = await networkClient.Subnets.GetAsync(
              "mytestrg1",
              "mytestvn1",
              "mytestsn1"
            );

            Console.WriteLine("Creating the public ip...");
            vnResult = await networkClient.PublicIpAddresses.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestip1",
              new PublicIpAddress
              {
                Location = "West US",
                PublicIpAllocationMethod = "Dynamic"
              });
            Console.WriteLine(vnResult.StatusCode);

            var pubipResponse = await networkClient.PublicIpAddresses.GetAsync("mytestrg1", "mytestip1");

            Console.WriteLine("Updating the network with the nic...");
            vnResult = await networkClient.NetworkInterfaces.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestnic1",
              new NetworkInterface
              {
                Name = "mytestnic1",
                Location = "West US",
                IpConfigurations = new List<NetworkInterfaceIpConfiguration>
                {
                  new NetworkInterfaceIpConfiguration
                  {
                    Name = "nicconfig1",
                    PublicIpAddress = new ResourceId
                    {
                      Id = pubipResponse.PublicIpAddress.Id
                    },
                    Subnet = new ResourceId
                    {
                      Id = subnetResponse.Subnet.Id
                    }
                  }
                }
              });
            Console.WriteLine(vnResult.StatusCode);
          }
        }

2.	방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

		CreateNetwork(credential);
		Console.ReadLine();

###가상 컴퓨터 만들기

모든 지원 리소스를 만들었으므로 가상 컴퓨터를 만들 수 있습니다.

1.	Program 클래스에 다음 메서드를 추가하여 가상 컴퓨터를 만듭니다.

		public async static void CreateVirtualMachine(TokenCloudCredentials credential)
        {
          using (var computeClient = new ComputeManagementClient(credential))
          {
            Console.WriteLine("Creating the availability set...");
            var avSetResponse = await computeClient.AvailabilitySets.CreateOrUpdateAsync(
              "mytestrg1",
              new AvailabilitySet
              {
                Name = "mytestav1",
                Location = "West US"
              } );
            Console.WriteLine(avSetResponse.StatusCode);
                
            var networkClient = new NetworkResourceProviderClient(credential);
            var nicResponse = await networkClient.NetworkInterfaces.GetAsync("mytestrg1", "mytestnic1");

            Console.WriteLine("Creating the virtual machine...");
            var putVMResponse = await computeClient.VirtualMachines.CreateOrUpdateAsync(
              "mytestrg1",
              new VirtualMachine
              {
                Name = "mytestvm1",
                Location = "West US",
                AvailabilitySetReference = new AvailabilitySetReference
                {
                  ReferenceUri = avSetResponse.AvailabilitySet.Id
                },
                HardwareProfile = new HardwareProfile
                {
                  VirtualMachineSize = "Standard_A0"
                },
                OSProfile = new OSProfile
                {
                  AdminUsername = "mytestuser1",
                  AdminPassword = "Mytestpass1",
                  ComputerName = "mytestsv1",
                  WindowsConfiguration = new WindowsConfiguration
                  {
                    ProvisionVMAgent = true
                  }
                },
                NetworkProfile = new NetworkProfile
                {
                  NetworkInterfaces = new List<NetworkInterfaceReference>
                  {
                    new NetworkInterfaceReference
                    {
                      ReferenceUri = nicResponse.NetworkInterface.Id
                    }
                  }
                },
                StorageProfile = new StorageProfile
                {
                  SourceImage = new SourceImageReference
                  {
                    ReferenceUri = "/{subscription-id}/services/images/a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201502.01-en.us-127GB.vhd"
                  },
                  OSDisk = new OSDisk
                  {
                    Name = "myosdisk1",
                    CreateOption = "FromImage",
                    VirtualHardDisk = new VirtualHardDisk 
                    {
                      Uri = "http://mytestsa1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    }
                  }
                }
              } );
            Console.WriteLine(putVMResponse.StatusCode);
          }
        }

	>[AZURE.NOTE]이미지 vhd 이름은 이미지 갤러리에서 정기적으로 변경되므로, 가상 컴퓨터를 배포하는 데 현재 이미지 이름을 가져와야 합니다. 이렇게 하려면 [Windows PowerShell을 사용하여 이미지 Windows 관리](https://msdn.microsoft.com/library/azure/dn790330.aspx)를 참조한 다음 , {source-image-name}을 사용 하려는 vhd 파일의 이름으로 바꿉니다. 예를 들면, "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201411.01-en.us-127GB.vhd"입니다.

	{subscription-id}를 사용자의 구독 ID로 바꿉니다.

2.	방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

		CreateVirtualMachine(credential);
        Console.ReadLine();

##5단계: 코드를 추가하여 리소스 삭제

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 가상 컴퓨터 및 모든 지원 리소스를 삭제하려는 경우, 리소스 그룹을 삭제해야 합니다.

1.	Program 클래스에 다음 메서드를 추가하여 리소스 그룹을 삭제합니다.

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
        {
          Console.WriteLine("Deleting resource group...");
          using (var resourceGroupClient = new ResourceManagementClient(credential))
          {
            var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
            Console.WriteLine(rgResult.StatusCode);
          }
        }

2.	방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

		DeleteResourceGroup(credential);
        Console.ReadLine();

##6단계: 콘솔 응용 프로그램 실행

1.	콘솔 응용 프로그램을 실행하려면, Visual Studio에서 **시작**을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

2.	각 리소스를 생성하도록 각 상태 코드가 리턴된 후 **Enter**를 누릅니다. 가상 컴퓨터를 만든 후 Enter 키를 눌러 모든 리소스를 삭제하기 전에 다음 단계를 수행합니다.

	이 콘솔 응용 프로그램을 처음부터 끝까지 완전히 실행하려면 약 5분이 필요합니다. Enter를 눌러 리소스 삭제를 시작하기 전에, 삭제하기 전 Azure 미리 보기 포털에서 리소스의 생성을 확인하려면 몇 분이 걸릴 수 있습니다.

3. 리소스의 상태를 보려면 Azure 미리 보기 포털에서 감사 로그를 찾습니다.

	![AD 응용 프로그램 만들기](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=58--> 