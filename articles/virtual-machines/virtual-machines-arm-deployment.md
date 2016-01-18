<properties
	pageTitle=".NET 라이브러리를 사용하여 리소스 배포 | Microsoft Azure"
	description="리소스 관리자를 사용하여 Microsoft Azure에 리소스를 만들고 삭제하기 위해 계산, 저장소 및 네트워크 .NET 라이브러리를 사용하는 방법에 대해 알아봅니다."
	services="virtual-machines,virtual-network,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="davidmu"/>

# 계산, 네트워크 및 저장소 .NET 라이브러리를 사용하여 Azure 리소스를 배포합니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.

이 자습서에서는 계산, 저장소 및 네트워크 .NET 라이브러리를 사용하여 Microsoft Azure의 리소스를 만들고 삭제하는 방법을 보여줍니다. 또한 Azure Active Directory를 사용하여 Azure 리소스 관리자에 요청을 인증하는 방법을 보여줍니다.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

이 자습서를 완료하려면 다음이 필요합니다.

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure 저장소 계정](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 또는 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

이러한 단계를 수행하려면 약 30분이 걸립니다.

## 1단계: Azure AD에 응용 프로그램 추가 및 사용 권한 설정

Azure AD를 사용하여 Azure 리소스 관리자에 요청을 인증하려면, 응용 프로그램이 기본 디렉터리에 추가되어야 합니다. 자세한 내용은 [Azure 리소스 관리자를 사용하여 서비스 주체 인증](../resource-group-authenticate-service-principal.md)을 참조하세요.

1. Azure PowerShell 프롬프트를 열고 이 명령을 실행한 다음 메시지가 표시되면 구독에 대한 자격 증명을 입력합니다.

			Login-AzureRmAccount

2. 다음 명령에서 {password}를 사용할 명령으로 바꾼 다음 실행하여 응용 프로그램을 만듭니다.

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE]다음 단계에 필요하므로 응용 프로그램이 만들어진 후 반환되는 응용 프로그램 식별자를 기록해 둡니다. 또한 Azure 포털의 Active Directory 섹션에 있는 응용 프로그램의 클라이언트 ID 필드에서 응용 프로그램 식별자를 찾을 수 있습니다.

3. {application-id}를 방금 기록된 식별자로 바꾼 다음 해당 응용 프로그램에 대한 서비스 주체를 만듭니다.

        New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. 응용 프로그램을 사용하는 사용 권한을 설정합니다.

	    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## 2단계: Visual Studio 프로젝트를 만들고 라이브러리 설치

NuGet 패키지는 이 자습서를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. Azure 리소스 관리 라이브러리, Azure Active Directory 인증 라이브러리 및 컴퓨터 리소스 공급자 라이브러리를 설치해야 합니다. Visual Studio에서 이 라이브러리를 가져오려면 다음을 수행합니다.

1. **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.

2. **템플릿** > **Visual C#**에서 **콘솔 응용 프로그램**을 선택하고, 프로젝트의 이름과 위치를 입력한 다음 **확인**을 클릭합니다.

3. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

4. 검색 상자에 *Active Directory*를 입력하고 Active Directory 인증 라이브러리 패키지에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

5. 페이지의 위쪽에서 **시험판 포함**을 선택합니다. 검색 상자에 *Microsoft.Azure.Management.Compute*를 입력하고 계산 .NET 라이브러리에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

6. 검색 상자에 *Microsoft.Azure.Management.Network*를 입력하고 네트워크 .NET 라이브러리에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

7. 검색 상자에 *Microsoft.Azure.Management.Storage*를 입력하고 네트워크 .NET 라이브러리에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

8. 검색 상자에 *Microsoft.Azure.Management.Resources*를 입력하고 리소스 관리 라이브러리에 대해 **설치**를 클릭합니다.

이제 라이브러리를 사용하기 시작하여 응용 프로그램을 만들 준비가 되었습니다.

## 3단계: 요청을 인증하는데 사용되는 자격 증명 만들기

Azure Active Directory 응용 프로그램이 생성되고 인증 라이브러리가 설치되므로 Azure 리소스 관리자에 요청을 인증하는데 사용되는 자격 증명에 대한 응용 프로그램 정보의 서식을 지정합니다.

1. 사용자가 만든 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 추가합니다.

	```
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
	using Microsoft.Rest;
	```

2. 자격 증명을 만드는 데 필요한 토큰을 가져올 프로그램 클래스에 다음 메서드를 추가합니다.

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);

		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	{application-id}를 이전에 기록한 응용 프로그램 ID로 바꾸고, {password}를 AD 응용 프로그램에 대해 선택한 암호로 바꾸고, {tenant-id}를 구독을 위한 테넌트 ID로 바꿉니다. Get-AzureSubscription을 실행하여 테넌트 ID를 찾을 수 있습니다.

3. Program.cs 파일에서 Main 메서드에 다음 코드를 추가하여 자격 증명을 만듭니다.

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

5. Program.cs 파일을 저장합니다.

## 4단계: 코드를 추가하여 공급자를 등록하고 리소르르 만듭니다.

### 공급자 등록 및 리소스 그룹 만들기

모든 리소스는 리소스 그룹에 포함되어야 합니다. 그룹에 리소스를 추가할 수 있기 전에 구독은 리소스 공급자로 등록되어야 합니다.

1. 변수를 Program 클래스의 Main 메서드에 추가하여 리소스, "미국 서부"와 같은 리소스의 위치, 관리자 계정 정보 및 구독 식별자에 사용하려는 이름을 지정합니다.

	```
	var groupName = "{resource-group-name}";
	var ipName = "{public-ip-name}";
	var avSetName = "{availability-set-name}";
	var nicName = "{network-interface-name}";
	var storageName = "{storage-account-name}";
	var vmName = "{virtual-machine-name};  
	var vnetName = "{vnet-name}";
	var subnetName = "{subnet-name}";
	var adminName = "{administrator-account-name}";
	var adminPassword = "{administrator-account-password};
	var location = "{location}";
	var subscriptionId = "{subsciption-id}";
	```

   대괄호로 묶은 자리 표시자를 모두 사용하려는 이름으로 바꿉니다. Get-AzureSubscription을 실행하여 구독 식별자를 찾을 수 있습니다.

2. Program 클래스에 다음 메서드를 추가하여 리소스 그룹을 만듭니다.

	```
	public static void CreateResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var resourceGroup = new ResourceGroup {
			Location = location
		};
		var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
		Console.WriteLine(rgResult.Properties.ProvisioningState);

		var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
		Console.WriteLine(rpResult.RegistrationState);
	}
	```

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

	```
	CreateResourceGroup(
		credential,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### 저장소 계정 만들기

가상 컴퓨터에 대해 생성된 가상 하드 디스크 파일을 저장하는 데 저장소 계정이 필요합니다.

1. Program 클래스에 다음 메서드를 추가하여 저장소 계정을 만듭니다.

	```
	public static void CreateStorageAccount(
		TokenCredentials credential,         
		string storageName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the storage account...");
		var storageManagementClient = new StorageManagementClient(credential);
		storageManagementClient.SubscriptionId = subscriptionId;
		var saResult = storageManagementClient.StorageAccounts.Create(
			groupName,
			storageName,
			new StorageAccountCreateParameters()
			{
				AccountType = AccountType.StandardLRS,
				Location = location
			}
		);
		Console.WriteLine(saResult.ProvisioningState);
	}
	```

2. 방금 추가한 메서드를 호출하려면 Program 클래스의 Main 메서드에 다음 코드를 추가합니다.

	```
	CreateStorageAccount(
		credential,
		storageName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### 공용 IP 주소 만들기

공용 IP 주소는 가상 컴퓨터와 통신해야 합니다.

1. Program 클래스에 다음 메서드를 추가하여 공용 IP 주소를 만듭니다.

	```
	public static void CreatePublicIPAddress(
		TokenCredentials credential,
		string ipName,  
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the public ip...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var ipResult = networkManagementClient.PublicIPAddresses.CreateOrUpdate(
			groupName,
			ipName,
			new PublicIPAddress
			{
				Location = location,
				PublicIPAllocationMethod = "Dynamic"
			}
		);
		Console.WriteLine(ipResult.ProvisioningState);
	}
	```

2. 방금 추가한 메서드를 호출하려면 Program 클래스의 Main 메서드에 다음 코드를 추가합니다.

	```
	CreatePublicIPAddress(
		credential,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### 가상 네트워크 만들기

리소스 관리자 배포 모델로 만든 가상 컴퓨터는 가상 네트워크에 있어야 합니다.

1. Program 클래스에 다음 메서드를 추가하여 서브넷 및 가상 네트워크를 만듭니다.

	```
	public static void CreateNetwork(
		TokenCredentials credential,
		string vnetName,
		string subnetName,
		string nicName,
		string ipName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the virtual network...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;

		var subnet = new Subnet
		{
			Name = subnetName,
			AddressPrefix = "10.0.0.0/24"
		};

		var address = new AddressSpace {
			AddressPrefixes = new List<string> { "10.0.0.0/16" }
		};

		var vnResult = networkManagementClient.VirtualNetworks.CreateOrUpdate(
			groupName,
			vnetName,
			new VirtualNetwork
			{
				Location = location,
				AddressSpace = address,
				Subnets = new List<Subnet> { subnet }
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);

		var subnetResponse = networkManagementClient.Subnets.Get(
			groupName,
			vnetName,
			subnetName
		);

		var pubipResponse = networkManagementClient.PublicIPAddresses.Get(groupName, ipName);

		Console.WriteLine("Updating the network with the nic...");
		var nicResult = networkManagementClient.NetworkInterfaces.CreateOrUpdate(
			groupName,
			nicName,
			new NetworkInterface
			{
				Location = location,
				IpConfigurations = new List<NetworkInterfaceIPConfiguration>
				{
					new NetworkInterfaceIPConfiguration
					{
						Name = "nicConfig1",
						PublicIPAddress = pubipResponse,
						Subnet = subnetResponse
					}
				}
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);
	}
	```

2. 방금 추가한 메서드를 호출하려면 Program 클래스의 Main 메서드에 다음 코드를 추가합니다.

	```
	CreateNetwork(
		credential,
		vnetName,
		subnetName,
		nicName,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### 가용성 집합 만들기

가용성 집합은 응용 프로그램에서 사용되는 가상 컴퓨터의 유지 관리를 쉽게 관리할 수 있도록 합니다.

1. Program 클래스에 다음 메서드를 추가하여 가용성 집합을 만듭니다.

	```
	public static void CreateAvailabilitySet(
		TokenCredentials credential,
		string avsetName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the availability set...");
		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avResult = computeManagementClient.AvailabilitySets.CreateOrUpdate(
			groupName,
			avsetName,
			new AvailabilitySet()
			{
				Location = location
			}
		);
	}
	```

2. 방금 추가한 메서드를 호출하려면 Program 클래스의 Main 메서드에 다음 코드를 추가합니다.

	```
	CreateAvailabilitySet(
		credential,
		avsetName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### 가상 컴퓨터 만들기

모든 지원 리소스를 만들었으므로 가상 컴퓨터를 만들 수 있습니다.

1. Program 클래스에 다음 메서드를 추가하여 가상 컴퓨터를 만듭니다.

	```
	public static void CreateVirtualMachine(
		TokenCredentials credential,
		string vmName,
		string groupName,
		string nicName,
		string avsetName,
		string storageName,
		string adminName,
		string adminPassword,
		string subscriptionId,
		string location)
	{
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

		Console.WriteLine("Creating the virtual machine...");
		var vm = computeManagementClient.VirtualMachines.CreateOrUpdate(
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
						CreateOption = "FromImage",
						Vhd = new VirtualHardDisk
						{
							Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
						}
					}
				}
			}
		);
		Console.WriteLine(vm.ProvisioningState);
	}
	```

	>[AZURE.NOTE]이 자습서는 Windows Server 운영 체제의 버전을 실행하는 가상 컴퓨터를 만듭니다. 기타 이미지 선택에 대해 자세히 알아보려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 탐색 및 선택](resource-groups-vm-searching.md)을 참조하세요.

2. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

	```
	CreateVirtualMachine(
		credential,
		vmName,
		groupName,
		nicName,
		avsetName,
		storageName,
		adminName,
		adminPassword,
		subscriptionId,
		location);
	Console.ReadLine();
	```

##5단계: 코드를 추가하여 리소스 삭제

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 가상 컴퓨터 및 모든 지원 리소스를 삭제하려는 경우, 리소스 그룹을 삭제해야 합니다.

1. Program 클래스에 다음 메서드를 추가하여 리소스 그룹을 삭제합니다.

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

	```
	DeleteResourceGroup(
		credential,
		groupName,
		subscriptionId);
	Console.ReadLine();
	```

##6단계: 콘솔 응용 프로그램 실행

1.	콘솔 응용 프로그램을 실행하려면, Visual Studio에서 **시작**을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

2.	각 리소스를 생성하도록 각 상태 코드가 반환된 후 **Enter**를 누릅니다. 가상 컴퓨터를 만든 후 Enter 키를 눌러 모든 리소스를 삭제하기 전에 다음 단계를 수행합니다.

	이 콘솔 응용 프로그램을 처음부터 끝까지 완전히 실행하려면 약 5분이 필요합니다. Enter를 눌러 리소스를 삭제하기 전에 Azure 포털에서 리소스 만들기를 확인하는 데에 몇 분이 걸릴 수 있습니다.

3. 리소스의 상태를 보려면 Azure 포털에서 감사 로그로 이동합니다.

	![AD 응용 프로그램 만들기](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=AcomDC_0107_2016-->