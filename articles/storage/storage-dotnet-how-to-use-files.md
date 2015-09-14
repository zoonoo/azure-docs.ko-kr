<properties
			pageTitle="PowerShell 및 .NET와 함께 Azure 파일 저장소를 사용하는 방법 | Microsoft Azure"
	description="Azure 파일 저장소를 사용하여 클라우드 파일 공유를 만들고 파일 콘텐츠를 관리하는 방법을 알아봅니다. 파일 저장소를 사용하면 기업이 SMB 파일 공유에 기반한 응용 프로그램을 Azure로 이동할 수 있습니다. 가상 컴퓨터에 대한 저장소 계정 자격 증명을 유지하여 다시 부팅할 때 파일 공유에 다시 연결하도록 합니다."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="adinah"
	editor=""/>

<tags ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/04/2015"
	ms.author="tamram"/>

# PowerShell 및 .NET와 함께 Azure 파일 저장소를 사용하는 방법

## 개요

Azure 파일 서비스는 표준 SMB 2.1 프로토콜을 사용하여 파일 공유를 노출합니다. Azure에서 실행되는 응용 프로그램은 이 서비스를 통해 ReadFile 및 WriteFile과 같은 친숙한 표준 파일 시스템을 사용하는 VM 간에 파일을 쉽게 공유할 수 있습니다. 또한 다양한 하이브리드 시나리오를 여는 REST 인터페이스를 통해 파일에 동시에 액세스할 수도 있습니다. 마지막으로 Azure 파일은 Blob, 테이블 및 큐 서비스와 동일한 기술을 토대로 만들어졌으므로 Azure 파일로 Azure 저장소 플랫폼에 기본 제공되는 기존 가용성, 내구성, 확장성 및 지리적 중복을 활용할 수 있습니다.

## 이 자습서 정보

이 시작 자습서에서는 Microsoft Azure 파일 저장소 사용에 대한 기본 사항을 설명합니다. 이 자습서에서는 다음 작업을 수행합니다.

- Azure PowerShell을 사용하여 새 Azure 파일 공유를 만들고, 디렉터리를 추가하고, 로컬 파일을 공유로 업로드하고, 디렉터리의 파일을 나열하는 방법을 보여 줍니다.
- SMB 공유와 마찬가지로 Azure 가상 컴퓨터에서 파일 공유를 마운트합니다.
- .NET용 Azure 저장소 클라이언트 라이브러리를 사용하여 온-프레미스 응용 프로그램에서 파일 공유에 액세스할 수 있습니다. 콘솔 응용 프로그램을 만들고 파일 공유를 사용하여 다음과 같은 작업을 수행합니다.
	- 콘솔 창에 공유의 파일 내용 쓰기
	- 파일 공유에 대한 할당량(최대 크기) 설정
	- 공유에 정의된 공유 액세스 정책을 사용하는 파일에 대해 공유 액세스 서명 만들기
	- 동일한 저장소 계정의 다른 파일로 파일 복사
	- 동일한 저장소 계정의 blob으로 파일 복사

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [저장소-파일-개념-포함](../../includes/storage-file-concepts-include.md)]


## Azure 저장소 계정 만들기

Azure 파일 저장소는 현재 미리 보기에 있습니다. 미리 보기에 대한 액세스 권한을 요청하려면 [Azure 미리 보기 포털](/services/preview/)로 이동한 후 **Azure 파일**에 대한 액세스를 요청하세요. 요청이 승인되면 파일 저장소 미리 보기에 액세스할 수 있다는 알림이 제공됩니다. 그러면 파일 저장소에 액세스하기 위한 저장소 계정을 만들 수 있습니다.

> [AZURE.NOTE]파일 저장소는 현재 새 저장소 계정에 대해서만 사용할 수 있습니다. 사용자 구독에 파일 저장소에 대한 액세스 권한이 부여되면 이 설명서를 진행하면서 사용할 새 저장소 계정을 만듭니다.
>
> 현재 Azure 파일 저장소에서는 공유 액세스 서명을 지원하지 않습니다.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## PowerShell을 사용하여 파일 공유 관리하기

다음에는 Azure PowerShell을 사용하여 파일 공유를 만들겠습니다. 파일 공유가 만들어지면 SMB 2.1을 지원하는 모든 파일 시스템에서 마운트할 수 있습니다.

### Azure 저장소에 대한 PowerShell cmdlet 설치

PowerShell 사용을 준비하려면 Azure PowerShell cmdlet을 다운로드하여 설치합니다. 설치 지점 및 설치 지침에 대해서는 [Azure PowerShell 설치 및 구성 방법](../install-configure-powershell.md)을 참조하세요.

> [AZURE.NOTE]파일 서비스에 대한 PowerShell cmdlet은 최신 Azure PowerShell 모듈, 버전 0.8.5 이상에서만 사용할 수 있습니다. 최신 Azure PowerShell 모듈을 다운로드하여 설치하거나 최신 모듈로 업그레이드하는 것이 좋습니다.

**시작**을 클릭하고 **Azure PowerShell**을 입력하여 Azure PowerShell 창을 엽니다. Azure PowerShell 창에 Azure Powershell 모듈이 로드됩니다.

### 저장소 계정 및 키에 대한 컨텍스트 만들기

이제 저장소 계정 컨텍스트를 만듭니다. 이 컨텍스트는 저장소 계정 이름 및 계정 키를 캡슐화합니다. Azure 포털에서 계정 키 복사하기에 대한 지침은 [저장소 계정 키 보기, 복사 및 재생성](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)을 참조하세요.

다음 예제에서 `storage-account-name` 및 `storage-account-key`을(를) 본인의 저장소 계정 이름 및 키로 바꿉니다.

	# create a context for account and key
	$ctx=New-AzureStorageContext storage-account-name storage-account-key

### 새 파일 공유 만들기

다음에는 `logs`(이)라는 새 공유를 만듭니다.

	# create a new share
	$s = New-AzureStorageShare logs -Context $ctx

이제 파일 저장소에 파일 공유가 있습니다. 다음에는 디렉터리와 파일을 추가할 것입니다.

> [AZURE.IMPORTANT]파일 공유의 이름은 모두 소문자여야 합니다. 파일 공유 및 파일 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](https://msdn.microsoft.com/library/azure/dn167011.aspx)를 참조하세요.

### 파일 공유에 디렉터리 만들기

다음에는 공유에 디렉터리를 만듭니다. 다음 예제에서 디렉터리 이름은 `CustomLogs`입니다.

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path CustomLogs

### 디렉터리에 로컬 파일 업로드

이제 디렉터리에 로컬 파일을 업로드합니다. 다음 예제에서는 `C:\temp\Log1.txt`에서 파일을 업로드합니다. 로컬 컴퓨터의 유효한 파일을 가리키도록 파일 경로를 편집합니다.

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs

### 디렉터리의 파일 나열

디렉터리의 파일을 보려면 디렉터리의 파일을 나열할 수 있습니다. 이 명령은 하위 디렉터리도 나열하지만 이 예에는 하위 디렉터리가 없으므로 파일만 나열됩니다.

	# list files in the new directory
	Get-AzureStorageFile -Share $s -Path CustomLogs

### 파일 복사

Azure PowerShell 버전 0.9.7부터 파일을 다른 파일로, 파일을 Blob으로 또는 Blob을 파일로 복사할 수 있습니다. 아래에는 cmdlet을 사용하여 이러한 복사 작업을 수행하는 방법이 나와 있습니다.

	# copy a file to the new directory
    Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx
    # copy a blob to a file directory
    Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx


## Windows를 실행하는 Azure 가상 컴퓨터에서 공유 마운트

Azure 파일 공유를 마운트하는 방법을 보여 주기 위해 Windows를 실행하는 Azure 가상 컴퓨터를 만든 후 원격으로 연결하여 공유를 마운트하겠습니다.

1. 먼저 [Windows Server를 실행하는 가상 컴퓨터 만들기](../virtual-machines-windows-tutorial.md)의 지침에 따라 새 Azure 가상 컴퓨터를 만듭니다.
2. 다음에는 [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법](../virtual-machines-log-on-windows-server.md)의 지침에 따라 가상 컴퓨터에 원격으로 연결합니다.
3. 가상 컴퓨터에서 PowerShell 창을 엽니다.

### 가상 컴퓨터의 저장소 계정 자격 증명 유지

파일 공유에 마운트하기 전에 먼저 가상 컴퓨터에서 저장소 계정 자격 증명을 유지합니다. 이 단계를 진행하면 Windows는 가상 컴퓨터가 다시 부팅될 때 파일 공유에 자동으로 다시 연결될 수 있습니다. 계정 자격 증명을 유지하려면 가상 컴퓨터의 PowerShell 창에서 `cmdkey` 명령을 실행합니다. `<storage-account-name>`를 저장소 계정의 이름으로 바꾸고 `<storage-account-key>`를 저장소 계정 키로 바꿉니다.

	cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>

이제 Windows는 가상 컴퓨터가 다시 부팅될 때 해당 파일 공유에 다시 연결됩니다. PowerShell 창에서 `net use` 명령을 실행하여 공유에 다시 연결되었는지 확인할 수 있습니다.

### 유지된 자격 증명을 사용하여 파일 공유 마운트

가상 컴퓨터에 원격으로 연결되면 `net use` 명령의 다음 구문을 사용하여 파일 공유를 마운트할 수 있습니다. `<storage-account-name>`를 저장소 계정의 이름으로 바꾸고 `<share-name>`을 파일 저장소 공유의 이름으로 바꿉니다.

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name>

	example :
	net use z: \\samples.file.core.windows.net\logs

이전 단계의 저장소 계정 자격 증명을 저장했으므로 `net use` 명령에 이러한 자격 증명을 제공할 필요가 없습니다. 자격 증명을 저장하지 않은 경우 다음 예제와 같이 `net use` 명령에 전달되는 매개 변수로 포함합니다.

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name> /u:<storage-account-name> <storage-account-key>

	example :
	net use z: \\samples.file.core.windows.net\logs /u:samples <storage-account-key>

이제 다른 드라이브의 경우처럼 가상 컴퓨터에서 파일 저장소 공유를 사용할 수 있습니다. 명령 프롬프트에서 표준 파일 명령을 실행하거나 파일 탐색기에서 마운트된 공유 및 해당 내용을 확인할 수 있습니다. .NET Framework의 [System.IO 네임스페이스](http://msdn.microsoft.com/library/gg145019.aspx)에서 제공하는 것과 같은 표준 Windows 파일 I/O API를 사용하여 파일 공유에 액세스하는 가상 컴퓨터 내에서 코드를 실행할 수도 있습니다.

역할에 원격으로 연결하여 Azure 클라우드 서비스에서 실행되는 역할에서 파일 공유를 마운트할 수도 있습니다.

## 온-프레미스 응용 프로그램을 만들어 파일 저장소 사용

이전에서 설명한 것처럼 Azure에서 실행되는 클라우드 서비스 또는 가상 컴퓨터에서 파일 공유를 마운트할 수 있습니다. 그러나 온-프레미스 응용 프로그램에서는 파일 공유를 마운트할 수 없습니다. 온-프레미스 응용 프로그램에서 공유 데이터에 액세스하려면 파일 저장소 API를 사용해야 합니다. 이 예에서는 [Azure .NET 저장소 클라이언트 라이브러리](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)를 통해 파일 공유를 사용하는 방법을 보여 줍니다.

온-프레미스 응용 프로그램에서 API를 사용하는 방법을 보려 주기 위해 데스크톱에서 실행되는 단일 콘솔 응용 프로그램을 만들 것입니다.

### 콘솔 응용 프로그램 만들기 및 어셈블리 가져오기

Visual Studio에서 새 콘솔 응용 프로그램을 만들고 Azure 저장소 NuGet 패키지를 설치하려면

1. Visual Studio에서 **파일 > 새 프로젝트**를 선택한 다음 Visual C# 템플릿 목록에서 **Windows > 콘솔 응용 프로그램**을 선택합니다.
2. 콘솔 응용 프로그램 이름을 지정한 다음 **확인**을 클릭합니다.
3. 프로젝트가 만들어지면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 온라인에서 "WindowsAzure.Storage"를 검색하고 **설치**를 클릭하여 Azure 저장소 패키지와 종속성을 설치합니다.

### 저장소 계정 자격 증명을 app.config 파일에 저장

다음에는 프로젝트의 app.config 파일에 자격 증명을 저장합니다. 다음과 비슷하게 app.config 파일을 편집합니다. 여기서는 `myaccount`을(를) 저장소 계정 이름으로 바꾸고 `mykey`을(를) 저장소 계정 키로 바꿉니다.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

> [AZURE.NOTE]최신 버전의 Azure 저장소 에뮬레이터는 파일 저장소를 지원하지 않습니다. 연결 문자열은 파일 저장소 미리 보기에 대한 액세스 권한이 있는 클라우드의 Azure 저장소 계정을 대상으로 해야 합니다.


### 네임스페이스 선언 추가

솔루션 탐색기에서 program.cs 파일을 열고 파일 맨 위에 다음 네임스페이스 선언을 추가합니다.

	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.WindowsAzure.Storage.File;

### 프로그래밍 방식으로 연결 문자열 검색

`Microsoft.WindowsAzure.CloudConfigurationManager` 클래스 또는 `System.Configuration.ConfigurationManager ` 클래스를 사용하여 app.config 파일에서 저장된 자격 증명을 검색할 수 있습니다. `Microsoft.WindowsAzure.CloudConfigurationManager` 클래스를 포함하는 Microsoft Azure 구성 관리자 패키지는 [Nuget](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)에서 사용할 수 있습니다.

이 예제에서는 `CloudConfigurationManager` 클래스를 사용하여 자격 증명을 검색한 다음 `CloudStorageAccount` 클래스를 사용하여 캡슐화하는 방법을 보여 줍니다. program.cs의 `Main()` 메서드에 다음 코드를 추가합니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString")); 

### 프로그래밍 방식으로 파일 공유 액세스

다음에는 위에 표시된 코드 뒤에 나오는 `Main()` 메서드에 다음 코드를 추가하여 연결 문자열을 검색합니다. 이 코드는 이전에 만든 파일에 대한 참조를 가져오고 해당 내용을 콘솔 창에 출력합니다.

	// Create a CloudFileClient object for credentialed access to File storage.
	CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	// Get a reference to the file share we created previously.
	CloudFileShare share = fileClient.GetShareReference("logs");

	// Ensure that the share exists.
	if (share.Exists())
	{
	    // Get a reference to the root directory for the share.
	    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

	    // Get a reference to the directory we created previously.
	    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

	    // Ensure that the directory exists.
	    if (sampleDir.Exists())
	    {
	        // Get a reference to the file we created previously.
	        CloudFile file = sampleDir.GetFileReference("Log1.txt");

	        // Ensure that the file exists.
	        if (file.Exists())
	        {
	            // Write the contents of the file to the console window.
	            Console.WriteLine(file.DownloadTextAsync().Result);
	        }
	    }
	}

콘솔 응용 프로그램을 실행하여 출력을 확인합니다.

## 파일 공유에 대한 최대 크기 설정

Azure 저장소 클라이언트 라이브러리 버전 5.x부터 파일 공유에 대한 할당량(또는 최대 크기)을 기가바이트 단위로 설정할 수 있습니다. 또한 공유에 현재 저장된 데이터의 양도 확인할 수 있습니다.

공유에 대한 할당량을 설정하여 공유에 저장되는 파일의 전체 크기를 제한할 수 있습니다. 공유에 있는 파일의 총 크기가 공유에 대해 설정된 할당량을 초과하면 클라이언트는 해당 파일이 비어 있지 않는 한, 기존 파일의 크기를 늘리거나 새 파일을 만들 수 없습니다.

아래 예제에서는 공유에 대한 현재 사용량을 확인하고 공유에 대해 할당량을 설정하는 방법을 보여 줍니다.

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    // Ensure that the share exists.
    if (share.Exists())
    {
        // Check current usage stats for the share.
		// Note that the ShareStats object is part of the protocol layer for the File service.
        Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
        Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

        // Specify the maximum size of the share, in GB.
        // This line sets the quota to be 10 GB greater than the current usage of the share.
        share.Properties.Quota = 10 + stats.Usage;
        share.SetProperties();

        // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
        share.FetchAttributes();
        Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
    }

## 파일 또는 파일 공유에 대한 공유 액세스 서명 생성

Azure 저장소 클라이언트 라이브러리 버전 5.x부터 파일 공유 또는 개별 파일에 대해 SAS(공유 액세스 서명)를 생성할 수 있습니다. 또한 파일 공유에 대해 공유 액세스 정책을 만들어 공유 액세스 서명을 관리할 수도 있습니다. 공유 액세스 정책을 만들면 노출된 SAS를 해지할 수 있으므로 권장됩니다.

다음 예제에서는 공유에 대해 공유 액세스 정책을 만들고 해당 정책을 사용하여 공유의 파일에 대해 SAS에 대한 제약 조건을 제공합니다.

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    // Ensure that the share exists.
    if (share.Exists())
    {
        string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

        // Create a new shared access policy and define its constraints.
        SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
            };

        // Get existing permissions for the share.
        FileSharePermissions permissions = share.GetPermissions();

        // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        share.SetPermissions(permissions);

        // Generate a SAS for a file in the share and associate this access policy with it.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
        CloudFile file = sampleDir.GetFileReference("Log1.txt");
        string sasToken = file.GetSharedAccessSignature(null, policyName);
        Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

        // Create a new CloudFile object from the SAS, and write some text to the file.
        CloudFile fileSas = new CloudFile(fileSasUri);
        fileSas.UploadText("This write operation is authenticated via SAS.");
        Console.WriteLine(fileSas.DownloadText());
    }

공유 액세스 서명 생성 및 사용에 대한 자세한 내용은 [공유 액세스 서명: SAS 모델 이해](storage-dotnet-shared-access-signature-part-1.md) 및 [Blob 서비스를 통해 SAS 생성 및 사용](storage-dotnet-shared-access-signature-part-2.md)을 참조하세요.

## 파일 복사

Azure 저장소 클라이언트 라이브러리 버전 5.x부터 파일을 다른 파일로, 파일을 blob으로 또는 blob을 파일로 복사할 수 있습니다. 다음 섹션에는 이러한 복사 작업을 프로그래밍 방식으로 수행하는 방법이 나와 있습니다.

AzCopy를 사용하여 파일을 다른 파일로 복사하거나 blob을 파일로 복사할 수도 있고 그 반대로 복사할 수도 있습니다. AzCopy를 사용하여 파일을 복사하는 방법에 대한 자세한 내용은 [Microsoft Azure 저장소와 함께 AzCopy를 사용하는 방법](storage-use-azcopy.md#copy-files-in-azure-file-storage-with-azcopy-preview-version-only)을 참조하세요.

> [AZURE.NOTE]blob을 파일에 복사하거나 파일을 blob에 복사하는 경우 두 항목이 동일한 저장소 계정 내에 있더라도 SAS(공유 액세스 서명)를 사용하여 원본 개체를 인증해야 합니다.

### 파일을 다른 파일에 복사

다음 예제에서는 파일을 동일한 공유의 다른 파일에 복사합니다. 이 복사 작업은 동일한 저장소 계정의 파일 간에 복사를 수행하므로 공유 키 인증을 사용하여 복사를 수행할 수 있습니다.

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    // Ensure that the share exists.
    if (share.Exists())
    {
        // Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

        // Get a reference to the directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

        // Ensure that the directory exists.
        if (sampleDir.Exists())
        {
            // Get a reference to the file we created previously.
            CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

            // Ensure that the source file exists.
            if (sourceFile.Exists())
            {
                // Get a reference to the destination file.
                CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

                // Start the copy operation.
                destFile.StartCopy(sourceFile);

                // Write the contents of the destination file to the console window.
                Console.WriteLine(destFile.DownloadText());
            }
        }
    }


### 파일을 Blob에 복사

다음 예제에서는 파일을 만들고 동일한 저장소 계정 내의 blob에 복사합니다. 이 예제에서 서비스는 복사 작업 동안 원본 파일에 대한 액세스를 인증하는 데 사용하는 소스 파일용 SAS를 만듭니다.

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Create a new file share, if it does not already exist.
    CloudFileShare share = fileClient.GetShareReference("sample-share");
    share.CreateIfNotExists();

    // Create a new file in the root directory.
    CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
    sourceFile.UploadText("A sample file in the root directory.");

    // Get a reference to the blob to which the file will be copied.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
    CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

    // Create a SAS for the file that's valid for 24 hours.
    // Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
    // to authenticate access to the source object, even if you are copying within the same
    // storage account.
    string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
    {
        // Only read permissions are required for the source file.
        Permissions = SharedAccessFilePermissions.Read,
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
    });

    // Construct the URI to the source file, including the SAS token.
    Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

    // Copy the file to the blob.
    destBlob.StartCopy(fileSasUri);

    // Write the contents of the file to the console window.
    Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
    Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());

동일한 방식으로 blob을 파일에 복사할 수 있습니다. 원본 개체가 blob인 경우 복사 작업 동안 해당 blob에 대한 액세스를 인증하는 SAS를 만듭니다.

## Linux에서 파일 저장소 사용

Linux에서 파일 공유를 파일을 만들고 관리하려면 Azure CLI를 사용합니다. 파일 저장소에서 Azure CLI를 사용하는 방법에 대한 자세한 내용은 [Azure 저장소에서 Azure CLI 사용](storage-azure-cli.md#create-and-manage-file-shares)을 참조하세요.

Linux를 실행하는 Azure 가상 컴퓨터에서 Azure 파일 공유를 마운트할 수 있습니다. Azure 가상 컴퓨터를 만들 때 Azure 이미지 갤러리에서 최신 버전의 Ubuntu와 같은 SMB 2.1을 지원하는 Linux 이미지를 지정할 수 있습니다. 그러나 SMB 2.1을 지원하는 Linux 배포는 Azure 파일 공유를 마운트할 수 있습니다.

Linux에 Azure 파일 공유를 마운트하는 방법에 대한 자세한 내용은 Channel 9에서 [Azure 파일 미리 보기를 통한 Linux의 공유 저장소 - 1부](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)를 참조하세요.

## 다음 단계

Azure 파일 저장소에 대한 자세한 내용은 다음 링크를 참조합니다.

### 자습서 및 참조

- [Storage Client Library for .NET 참조](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [파일 서비스 REST API 참조](http://msdn.microsoft.com/library/azure/dn167006.aspx)
- [Microsoft Azure 저장소와 함께 AzCopy 사용](storage-use-azcopy.md)
- [Azure 저장소와 함께 Azure PowerShell 사용](storage-powershell-guide-full.md)
- [Azure 저장소에서 Azure CLI 사용](storage-azure-cli.md)

### 블로그 게시물

- [Microsoft Azure 파일 서비스 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Microsoft Azure 파일에 대한 연결 유지](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=September15_HO1-->