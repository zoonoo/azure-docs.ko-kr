<properties
	pageTitle="Xamarin에서 Blob 저장소를 사용하는 방법(미리 보기) | Microsoft Azure"
	description="Xamarin 미리 보기용 Azure 저장소 클라이언트 라이브러리를 사용하면 개발자들이 기본 사용자 인터페이스를 가진 iOS, Android 및 Windows Store 앱을 만들 수 있습니다. 이 자습서에서는 Xamarin을 사용하여 Azure Blob 저장소를 사용하는 Android 응용 프로그램을 만드는 방법을 보여줍니다."
	services="storage"
	documentationCenter="xamarin"
	authors="micurd"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="micurd"/>

# Xamarin에서 Blob 저장소를 사용하는 방법(미리 보기)

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 개요

Xamarin을 사용하면 개발자들이 공유된 C# 코드베이스를 사용하여 기본 사용자 인터페이스를 가진 iOS, Android 및 Windows Store 앱을 만들 수 있습니다. Xamarin용 Azure 저장소 클라이언트 라이브러리는 미리 보기 라이브러리입니다. 나중에 변경될 수도 있음에 유의하십시오.

이 자습서는 Xamarin Android 응용 프로그램과 함께 Azure Blob 저장소를 사용하는 방법을 보여줍니다. 코드를 살펴보기 전에 Azure 저장소에 대해 더 알아보려면 이 문서의 끝에 있는 [다음 단계](#next-steps)를 참조하십시오.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 공유 액세스 서명 생성

Xamarin용 Azure 저장소 클라이언트 라이브러리와 함께 개발할 때는 계정 액세스 키를 사용하여 Azure 저장소 계정에 대한 액세스를 인증할 수 없습니다. 이것은 귀하의 계정 자격 증명이 귀하의 앱을 다운로드할 수도 있는 사용자에게 배포되지 않도록 예방하기 위해서입니다. 대신, 귀하의 계정 자격 증명을 노출하지 않는 공유된 액세스 서명(SAS)을 사용할 것을 권장합니다.

이 가이드에서는 Azure PowerShell을 사용하여 SAS 토큰을 생성합니다. 그 다음에는 생성된 SAS를 사용하는 Xamarin 앱을 만들어봅니다.

먼저, Azure PowerShell을 설치해야 합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md#Install)을 참조하십시오.

그 다음 Azure PowerShell을 열고 다음 명령을 실행합니다. `ACCOUNT_NAME` 및 `ACCOUNT_KEY== `을(를) 저장소 계정 자격 증명으로 바꾸는 것을 잊지 마십시오. `CONTAINER_NAME`을(를) 선택한 이름으로 바꿉니다.

    PS C:\> $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
	PS C:\> New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
	PS C:\> $now = Get-Date
	PS C:\> New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

새 컨테이너에 대한 공유 액세스 서명 URI는 다음과 유사해야 합니다.

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

컨테이너에서 만든 공유 액세스 서명이 다음 날 유효해집니다. 서명은 컨테이너 내에서 Blob에 대한 모든 권한을 부여합니다(*예*: 읽기, 쓰기, 삭제 및 나열).

공유된 액세스 서명에 대한 자세한 내용은 [.NET용 SAS 자습서](storage-dotnet-shared-access-signature-part-2.md)를 참조하십시오.

## 새 Xamarin 응용 프로그램 만들기

이 자습서를 위해 Visual Studio에서 Xamarin 응용 프로그램을 만들어 보겠습니다. 다음 단계에 따라 응용 프로그램을 만듭니다.

1. [Visual Studio](https://www.visualstudio.com/)를 다운로드 및 설치합니다.
2. [Xamarin](http://xamarin.com/platform)을 다운로드 및 설치합니다.
3. Visual Studio를 열고 **파일 > 새로 만들기 > 프로젝트 > Android > 빈 앱(Android)**을 선택합니다.
4. 솔루션 탐색기 창에서 해당 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 그런 다음 **Azure 저장소**를 검색하고 **Azure 저장소 4.4.0-미리 보기**를 설치합니다.

이제 단추를 클릭하고 카운터를 증가시켜주는 응용 프로그램이 완료되어야 합니다.

## 공유 액세스 서명을 사용하여 컨테이너 작업 수행

다음으로, 생성한 SAS URI를 사용하여 일련의 컨테이너 작업을 수행하는 코드를 추가합니다.

먼저 다음 **using** 명령문을 추가합니다.

	using System.IO;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.Storage.Blob;


다음으로, SAS 토큰에 대한 줄을 추가합니다. `"SAS_URI"` 문자열을 Azure PowerShell에서 생성한 SAS URI로 바꿉니다. 그런 다음 아래에서 생성할 `UseContainerSAS` 메서드에 호출을 위한 줄을 하나 추가합니다. 대리자 앞에 **async** 키워드가 추가된 것을 볼 수 있습니다.


	public class MainActivity : Activity
	{
    	int count = 1;
    	string sas = "SAS_URI";
    	protected override void OnCreate(Bundle bundle)
    	{
        	base.OnCreate(bundle);

        	// Set our view from the "main" layout resource
        	SetContentView(Resource.Layout.Main);

        	// Get our button from the layout resource, and attach an event to it
        	Button button = FindViewById<Button>(Resource.Id.MyButton);

        	button.Click += async delegate	{
             	button.Text = string.Format("{0} clicks!", count++);
             	await UseContainerSAS(sas);
         	};
     }

`OnCreate` 메서드 아래에 새 메서드 `UseContainerSAS`을(를) 추가합니다.

	static async Task UseContainerSAS(string sas)
	{
    	//Try performing container operations with the SAS provided.

    	//Return a reference to the container using the SAS URI.
    	CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));
    	string date = DateTime.Now.ToString();
    	try
    	{
        	//Write operation: write a new blob to the container.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_" + date + ".txt");

        	string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        	MemoryStream msWrite = new
        	MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        	msWrite.Position = 0;
        	using (msWrite)
         	{
             	await blob.UploadFromStreamAsync(msWrite);
         	}
         	Console.WriteLine("Write operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Write operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
     	try
     	{
        	//Read operation: Get a reference to one of the blobs in the container and read it.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
        	string data = await blob.DownloadTextAsync();

        	Console.WriteLine("Read operation succeeded for SAS " + sas);
        	Console.WriteLine("Blob contents: " + data);
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Additional error information: " + e.Message);
       		Console.WriteLine("Read operation failed for SAS " + sas);
        	Console.WriteLine();
     	}
     	Console.WriteLine();
     	try
     	{
        	//Delete operation: Delete a blob in the container.
         	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
         	await blob.DeleteAsync();

         	Console.WriteLine("Delete operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Delete operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
	}

## 응용 프로그램 실행

이제 Android 장치 또는 에뮬레이터에서 이 응용 프로그램을 실행할 수 있습니다.

이 시작 설명서는 Android를 중점적으로 다루고 있지만 `UseContainerSAS` 메서드를 iOS 또는 Windows Store 응용 프로그램에서도 사용할 수 있습니다. Xamarin은 또한 개발자들이 Windows Phone 앱을 만들 수 있게 해줍니다. 하지만 당사의 라이브러리는 아직 이것을 지원하지 않습니다.

## 다음 단계

이 자습서에서는 Xamarin 응용 프로그램과 함께 Azure Blob 저장소와 SAS를 사용하는 방법을 배웠습니다. 추가 연습으로, Azure 테이블 또는 큐에 대해 비슷한 패턴을 적용하여 SAS 토큰을 생성할 수 있었습니다.

다음 링크를 확인하여 blob, 테이블 및 큐에 대해 자세히 알아봅니다.

[Microsoft Azure 저장소 소개](storage-introduction.md) [.NET에서 Blob 저장소를 사용하는 방법](storage-dotnet-how-to-use-blobs.md) [.NET에서 테이블 저장소를 사용하는 방법](storage-dotnet-how-to-use-tables.md) [.NET에서 큐 저장소를 사용하는 방법](storage-dotnet-how-to-use-queues.md) [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy)

<!---HONumber=AcomDC_0114_2016-->