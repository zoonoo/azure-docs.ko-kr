<properties 
	pageTitle="Azure 파일 저장소 사용 방법 | Microsoft Azure" 
	description="Microsoft Azure 파일 저장소를 사용하여 파일 공유를 만들고 파일 콘텐츠를 관리하는 방법을 알아봅니다. 샘플은 PowerShell 및 C#으로 작성되었습니다." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="tamram"/>

# Azure 파일 저장소 사용 방법

## 개요

이 시작 설명서에서는 Microsoft Azure 파일 저장소 사용의 기본 사항을 설명합니다. 먼저 PowerShell을 사용하여 새 Azure 파일 공유를 만들고, 디렉터리를 추가하고, 로컬 파일을 공유로 업로드하고, 디렉터리의 파일을 나열하는 방법을 보여 줍니다. 그런 후 SMB 공유와 마찬가지로 Azure 가상 컴퓨터에서 파일 공유를 마운트하는 방법을 보여 줍니다.

Azure 가상 컴퓨터나 클라우드 서비스뿐만 아니라 온-프레미스 응용 프로그램에서 공유의 파일에 액세스하려는 사용자를 위해 Azure .NET Storage Client Library를 사용하여 데스크톱 응용 프로그램에서 파일 공유로 작업하는 방법을 설명합니다.

> [AZURE.NOTE] 이 가이드의 .NET 코드 예제를 실행하려면 Azure .NET Storage Client Library 4.x 이상이 필요합니다. Storage Client Library는 [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/)을 통해 사용할 수 있습니다.

## 파일 저장소 정의

파일 저장소는 표준 SMB 2.1 프로토콜을 사용하여 응용 프로그램을 위한 공유 저장소를 제공합니다. Microsoft Azure 가상 컴퓨터 및 클라우드 서비스는 마운트된 공유를 통해 여러 응용 프로그램 구성 요소에서 파일 데이터를 공유할 수 있으며 온-프레미스 응용 프로그램은 파일 저장소 API를 통해 공유의 파일 데이터에 액세스할 수 있습니다.

Azure 가상 컴퓨터나 클라우드 서비스에서 실행되는 응용 프로그램은 데스크톱 응용 프로그램이 일반적인 SMB 공유를 마운트하는 것처럼 파일 저장소 공유를 마운트하여 파일 데이터에 액세스할 수 있습니다. 제한 없는 수의 Azure 가상 컴퓨터 또는 역할이 파일 저장소 공유를 동시에 마운트하고 액세스할 수 있습니다.

파일 저장소 공유는 표준 SMB 2.1 파일 공유이므로 Azure에서 실행되는 응용 프로그램은 파일 I/O API를 통해 공유의 데이터에 액세스할 수 있습니다. 따라서 개발자는 기존의 코드와 기술을 이용하여 기존 응용 프로그램을 마이그레이션할 수 있습니다. IT 전문가는 PowerShell cmdlet을 사용하여 Azure 응용 프로그램 관리의 일부로 파일 저장소 공유를 만들고 마운트하고 관리할 수 있습니다. 이 설명서는 두 가지 예를 모두 보여 줍니다.

파일 저장소의 일반적인 사용은 다음과 같습니다.

- 비용이 많이 드는 다시 쓰기 없이, Azure 가상 컴퓨터 또는 클라우드 서비스에서 실행되기 위해 파일 공유에 의존하는 온-프레미스 응용 프로그램 마이그레이션
- 예를 들어 구성 파일에 공유 응용 프로그램 설정 저장
- 로그, 메트릭 및 크래시 덤프와 같은 진단 데이터를 공유 위치에 저장 
- Azure 가상 컴퓨터 또는 클라우드 서비스를 개발하거나 관리하는 데 필요한 도구 및 유틸리티 저장

## 파일 저장소 개념

파일 저장소에는 다음 구성 요소가 포함됩니다.

![files-concepts][files-concepts]


-   **저장소 계정:** Azure 저장소에 대한 모든 액세스는 저장소 계정을 통해 수행됩니다. 저장소 계정 용량에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표](http://msdn.microsoft.com/library/azure/dn249410.aspx)(영문)를 참조하세요.

-   **공유:** Azure에서 파일 저장소 공유는 SMB 2.1 파일 공유입니다.
    모든 디렉터리 및 파일을 부모 공유에 만들어야 합니다. 계정에 포함할 수 있는 공유 수에는 제한이 없으며, 공유에 저장할 수 있는 파일 수에는 저장소 계정의 최대 용량 한도까지 제한이 없습니다.

-   **디렉터리:** 선택적인 디렉터리 계층 구조입니다.

-	**파일:** 공유의 파일입니다. 한 파일의 크기는 최대 1TB일 수 있습니다.

-   **URL 형식:** 다음 URL 형식을 사용하여 파일에 주소를 지정할 수 있습니다.
    format:   
    https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`  
    
    다음 예제 URL을 사용하여 위의 다이어그램에 있는 파일 중 하나의 주소를 지정할 수 있습니다.:  
    `http://acmecorp.file.core.windows.net/cloudfiles/diagnostics/log.txt`



공유, 디렉터리 및 파일 이름 지정 방법에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](http://msdn.microsoft.com/library/azure/dn167011.aspx)(영문)를 참조하세요.

## Azure 저장소 계정 만들기

Azure 파일 저장소는 현재 미리 보기에 있습니다. 미리 보기에 대한 액세스 권한을 요청하려면 [Microsoft Azure 미리 보기 페이지](/services/preview/)로 이동한 후 **Azure 파일**에 대한 액세스를 요청합니다. 요청이 승인되면 파일 저장소 미리 보기에 액세스할 수 있다는 알림이 제공됩니다. 그러면 파일 저장소에 액세스하기 위한 저장소 계정을 만들 수 있습니다.

> [AZURE.NOTE] 파일 저장소는 현재 새 저장소 계정에 대해서만 사용할 수 있습니다. 사용자 구독에 파일 저장소에 대한 액세스 권한이 부여되면 이 설명서를 진행하면서 사용할 새 저장소 계정을 만듭니다.

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## PowerShell을 사용하여 파일 공유 만들기

다음에는 Azure PowerShell을 사용하여 파일 공유를 만들겠습니다. 파일 공유가 만들어지면 SMB 2.1을 지원하는 모든 파일 시스템에서 마운트할 수 있습니다. 

### Azure 저장소에 대한 PowerShell cmdlet 설치

PowerShell 사용을 준비하려면 Azure PowerShell cmdlet을 다운로드하여 설치합니다. 설치 지점 및 설치 지침은 [Azure PowerShell을 설치 및 구성하는 방법](install-configure-powershell.md) (영문)을 참조하세요.

> [AZURE.NOTE] 파일 서비스에 대한 PowerShell cmdlet은 최신 Azure PowerShell 모듈, 버전 0.8.5 이상에서만 사용할 수 있습니다. 최신 Azure PowerShell 모듈을 다운로드하여 설치하거나 최신 모듈로 업그레이드하는 것이 좋습니다.

**시작**을 클릭하고 **Azure PowerShell**을 입력하여 Azure PowerShell 창을 엽니다. Azure PowerShell 창에 Azure Powershell 모듈이 로드됩니다.

### 저장소 계정 및 키에 대한 컨텍스트 만들기

이제 저장소 계정 컨텍스트를 만듭니다. 이 컨텍스트는 계정 이름 및 계정 키를 캡슐화합니다. 다음 예제에서 `account-name` 및 `account-key`를 본인의 계정 이름 및 키로 바꿉니다.

    # create a context for account and key
    $ctx=New-AzureStorageContext account-name account-key
    
### 새 파일 공유 만들기

다음에는 이 예제에 `sampleshare`라는 새 공유를 만듭니다.

    # create a new share
    $s = New-AzureStorageShare sampleshare -Context $ctx

이제 파일 저장소에 파일 공유가 있습니다. 다음에는 디렉터리와 파일을 추가할 것입니다.

### 파일 공유에 디렉터리 만들기

다음에는 공유에 디렉터리를 만듭니다. 다음 예제에서 디렉터리 이름은 `sampledir`입니다.

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path sampledir

### 디렉터리에 로컬 파일 업로드

이제 디렉터리에 로컬 파일을 업로드합니다. 다음 예제에서는 `C:\temp\samplefile.txt`에서 파일을 업로드합니다. 로컬 컴퓨터의 유효한 파일을 가리키도록 파일 경로를 편집합니다. 
    
    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\samplefile.txt -Path sampledir

### 디렉터리의 파일 나열

디렉터리의 파일을 보려면 디렉터리의 파일을 나열할 수 있습니다. 이 명령은 하위 디렉터리도 나열하지만 이 예에는 하위 디렉터리가 없으므로 파일만 나열됩니다.  

    # list files in the new directory
    Get-AzureStorageFile -Share $s -Path sampledir

## Windows를 실행하는 Azure 가상 컴퓨터에서 공유 마운트

Azure 파일 공유를 마운트하는 방법을 보여 주기 위해 Windows를 실행하는 Azure 가상 컴퓨터를 만든 후 원격으로 연결하여 공유를 마운트하겠습니다. 

1. 먼저 [Windows Server를 실행하는 가상 컴퓨터 만들기]의 지침에 따라 새 Azure 가상 컴퓨터를 만듭니다(virtual-machines-windows-tutorial.md).
2. 다음에는 [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]의 지침에 따라 가상 컴퓨터에 원격으로 연결합니다(virtual-machines-log-on-windows-server.md).
3. 가상 컴퓨터에서 PowerShell 창을 엽니다. 

### 가상 컴퓨터의 저장소 계정 자격 증명 유지

파일 공유에 마운트하기 전에 먼저 가상 컴퓨터에서 저장소 계정 자격 증명을 유지합니다. 이 단계를 진행하면 Windows는 가상 컴퓨터가 다시 부팅될 때 파일 공유에 자동으로 다시 연결될 수 있습니다. 계정 자격 증명을 유지하려면 가상 컴퓨터의 PowerShell 창에서 `cmdkey` 명령을 실행합니다. `<storage-account>`을 저장소 계정의 이름으로 바꾸고 `<account-key>`를 저장소 계정 키로 바꿉니다.

	cmdkey /add:<storage-account>.file.core.windows.net /user:<storage-account> /pass:<account-key>

이제 Windows는 가상 컴퓨터가 다시 부팅될 때 해당 파일 공유에 다시 연결됩니다. PowerShell 창에서 `net use` 명령을 실행하여 공유에 다시 연결되었는지 확인할 수 있습니다.

### 유지된 자격 증명을 사용하여 파일 공유 마운트

가상 컴퓨터에 원격으로 연결되면 `net use` 명령의 다음 구문을 사용하여 파일 공유를 마운트할 수 있습니다. `<storage-account>`를 저장소 계정의 이름으로 바꾸고 `<share-name>`을 파일 저장소 공유의 이름으로 바꿉니다.

	net use z: <storage-account>.file.core.windows.net<share-name>

> [AZURE.NOTE] 이전 단계의 저장소 계정 자격 증명을 저장했으므로 `net use` 명령에 이러한 자격 증명을 제공할 필요가 없습니다. 자격 증명을 저장하지 않은 경우 `net use` 명령에 전달되는 매개 변수로 포함합니다. `<storage-account>`를 저장소 계정의 이름으로 바꾸고 `<share-name>`을 파일 저장소 공유의 이름으로 바꾸고 `<account-key>`를 저장소 계정 키로 바꿉니다.
	   
	net use z: <storage-account>.file.core.windows.net<share-name> /u:<storage-account> <account-key>

이제 다른 드라이브의 경우처럼 가상 컴퓨터에서 파일 저장소 공유를 사용할 수 있습니다. 명령 프롬프트에서 표준 파일 명령을 실행하거나 파일 탐색기에서 마운트된 공유 및 해당 내용을 확인할 수 있습니다. .NET Framework의 [System.IO 네임스페이스](http://msdn.microsoft.com/library/gg145019(v=vs.110).aspx)에서 제공하는 것과 같은 표준 Windows 파일 I/O API를 사용하여 파일 공유에 액세스하는 가상 컴퓨터 내에서 코드를 실행할 수도 있습니다. 

역할에 원격으로 연결하여 Azure 클라우드 서비스에서 실행되는 역할에서 파일 공유를 마운트할 수도 있습니다.

## 온-프레미스 응용 프로그램을 만들어 파일 저장소 사용

위에서 설명한 것처럼 Azure에서 실행되는 클라우드 서비스 또는 가상 컴퓨터에서 파일 공유를 마운트할 수 있습니다. 그러나 온-프레미스 응용 프로그램에서는 파일 공유를 마운트할 수 없습니다. 온-프레미스 응용 프로그램에서 공유 데이터에 액세스하려면 파일 저장소 API를 사용해야 합니다. 이 예제에서는 [Azure .NET Storage Client Library](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)를 통해 파일 공유를 사용하는 방법을 보여 줍니다. 

온-프레미스 응용 프로그램에서 API를 사용하는 방법을 보려 주기 위해 데스크톱에서 실행되는 단일 콘솔 응용 프로그램을 만들 것입니다.

### 콘솔 응용 프로그램 만들기 및 어셈블리 가져오기

Visual Studio에서 새 콘솔 응용 프로그램을 만들고 Azure 저장소 NuGet 패키지를 설치하려면

1. Visual Studio에서 **파일** -> **새 프로젝트**를 선택한 다음 Visual C# 템플릿 목록에서 **Windows** -> **콘솔 응용 프로그램**을 선택합니다.
2. 콘솔 응용 프로그램의 이름을 지정하고 **확인**을 클릭합니다.
3. 프로젝트가 만들어지면 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 온라인에서 "WindowsAzure.Storage"를 검색하고 **설치**를 클릭하여 Azure 저장소 패키지와 종속성을 설치합니다.

### 저장소 계정 자격 증명을 app.config 파일에 저장

다음에는 프로젝트의 app.config 파일에 자격 증명을 저장합니다. 다음과 비슷하게 app.config 파일을 편집합니다. 여기서는 `myaccount`를 저장소 계정 이름으로 바꾸고 `mykey`를 저장소 계정 키로 바꿉니다.

    <?xml version="1.0" encoding="utf-8" ?>
	<configuration>
		<startup> 
			<supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		</startup>
		<appSettings>
			<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey" />
		</appSettings>
	</configuration>

> [AZURE.NOTE] 최신 버전의 Azure 저장소 에뮬레이터는 파일 저장소를 지원하지 않습니다. 연결 문자열은 파일 미리 보기에 대한 액세스 권한이 있는 클라우드의 Azure 저장소 계정을 대상으로 해야 합니다.


### 네임스페이스 선언 추가
솔루션 탐색기에서 program.cs 파일을 열고 파일 맨 위에 다음 네임스페이스 선언을 추가합니다.

    using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.File;

### 프로그래밍 방식으로 연결 문자열 검색
 `Microsoft.WindowsAzure.CloudConfigurationManager` 클래스 또는 `System.Configuration.ConfigurationManager `클래스를 사용하여 app.config 파일에서 저장된 자격 증명을 검색할 수 있습니다. 이 예제에서는 `CloudConfigurationManager` 클래스를 사용하여 자격 증명을 검색한 다음 `CloudStorageAccount` 클래스를 사용하여 캡슐화하는 방법을 보여 줍니다. program.cs의 `Main()` 메서드에 다음 코드를 추가합니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

### 프로그래밍 방식으로 파일 저장소 공유 액세스

다음에는 위에 표시된 코드 뒤에 나오는 `Main()` 메서드에 다음 코드를 추가하여 연결 문자열을 검색합니다. 이 코드는 이전에 만든 파일에 대한 참조를 가져오고 해당 내용을 콘솔 창에 출력합니다.

	//Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("sampleshare");

	//Ensure that the share exists.
    if (share.Exists())
    {
		//Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

		//Get a reference to the sampledir directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("sampledir");

		//Ensure that the directory exists.
        if (sampleDir.Exists())
        {
			//Get a reference to the file we created previously.
            CloudFile file = sampleDir.GetFileReference("samplefile.txt");

			//Ensure that the file exists.
            if (file.Exists())
            {
				//Write the contents of the file to the console window.
                Console.WriteLine(file.DownloadTextAsync().Result);
            }
        }
    }

콘솔 응용 프로그램을 실행하여 출력을 확인합니다.

## Linux를 실행하는 Azure 가상 컴퓨터에서 공유 마운트

Azure 가상 컴퓨터를 만들 때 디스크 이미지 갤러리에서 Ubuntu 이미지를 지정하여 SMB 2.1을 지원하도록 할 수 있습니다. 그러나 SMB 2.1을 지원하는 Linux 배포는 모두 Azure 파일 공유를 마운트할 수 있습니다. 

Linux에 Azure 파일 공유를 마운트하는 방법은 Channel 9에서 [Azure 파일 미리 보기를 통한 Linux의 공유 저장소 - 1부](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)(영문)를 참조하세요.

## 다음 단계

이제 파일 저장소의 기본 사항을 살펴보았으므로 다음 링크를 통해
자세한 내용을 알아보세요.
<ul>
<li>사용 가능한 API에 대한 자세한 내용은 파일 서비스 참조 설명서를 참조하세요.
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Storage Client Library for .NET 참조</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dn167006.aspx">파일 서비스 REST API 참조</a>(영문)</li>
  </ul>
</li>
<li>파일 서비스와 관련된 Azure 저장소 팀의 블로그 게시물을 확인할 수 있습니다.
  <ul>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx">Microsoft Azure 파일 서비스 소개</a>(영문)
    </li>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx">Microsoft Azure 파일에 대한 연결 유지</a>(영문)</li>
  </ul>
</li><li>Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하세요.
  <ul>
    <li><a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">Blob 저장소</a>를 사용하여 구조화되지 않은 데이터를 저장합니다.</li>
    <li><a href="/documentation/articles/storage-dotnet-how-to-use-tables/">테이블 저장소</a>를 사용하여 구조화된 데이터를 저장합니다.</li>
    <li><a href="/documentation/articles/storage-dotnet-how-to-use-queues/">큐 저장소</a>를 사용하여 메시지를 안정적으로 저장합니다.</li>
    <li><a href="/documentation/articles/sql-database-dotnet-how-to-use/">SQL 데이터베이스</a>를 사용하여 관계형 데이터를 저장합니다.</li>
  </ul>
</li>
</ul>

[files-concepts]: ./media/storage-dotnet-how-to-use-files/files-concepts.png


<!--HONumber=49--> 

<!--HONumber=49-->