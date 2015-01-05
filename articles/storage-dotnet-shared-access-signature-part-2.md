<properties urlDisplayName="" pageTitle="Blob 서비스를 통해 SAS 만들기 및 사용 | Microsoft Azure" metaKeywords="Azure blob, shared access signatures, stored access policy" description="Explore generating and using shared access signatures with the Blob service" metaCanonical="" services="storage" documentationCenter="" title="Part 2: Create and Use a SAS with the Blob Service" solutions="" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />


# 공유 액세스 서명, 2부: Blob 서비스를 통해 SAS 만들기 및 사용

이 자습서의 [1부](../storage-dotnet-shared-access-signature-part-1/) 에서는 SAS(공유 액세스 서명)에 대해 설명하고 SAS 사용을 위한 모범 사례를 살펴보았습니다. 2부에서는 Azure Blob 서비스를 사용하여 공유 액세스 서명을 생성한 다음 사용하는 방법에 대해 살펴봅니다. 예제는 C#으로 작성되었으며 Azure Storage Client Library for .NET을 사용합니다. 시나리오에서는 공유 액세스 서명 작업의 다음과 같은 측면을 다룹니다.

- 컨테이너에서 공유 액세스 서명 생성
- Blob에서 공유 액세스 서명 생성
- 컨테이너의 리소스에서 서명을 관리하는 저장된 액세스 정책 만들기
- 클라이언트 응용 프로그램에서 공유 액세스 서명 테스트

# 이 자습서 정보 #
이 자습서에서는 두 콘솔 응용 프로그램을 만들어 컨테이너 및 Blob에 대한 공유 액세스 서명을 만드는 과정을 중점적으로 다룹니다. 첫 번째 콘솔 응용 프로그램에서는 컨테이너 및 Blob에 대한 공유 액세스 서명을 생성합니다. 이 응용 프로그램에서는 저장소 계정 키를 알고 있습니다. 클라이언트 응용 프로그램 역할을 하는 두 번째 콘솔 응용 프로그램에서는 첫 번째 응용 프로그램에서 만든 공유 액세스 서명을 사용하여 컨테이너 및 Blob 리소스에 액세스합니다. 이 응용 프로그램에서는 컨테이너 및 Blob 리소스에 대한 액세스를 인증하는 데에만 공유 액세스 서명을 사용하고, 계정 키에 대해 알지 못합니다.

# 1부: 공유 액세스 서명을 생성하는 콘솔 응용 프로그램 만들기 #

먼저 Azure Storage Client Library for .NET을 설치했는지 확인합니다. 클라이언트 라이브러리에 대한 최신 어셈블리가 들어 있는 [NuGet 패키지](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet package") 를 설치할 수 있습니다. 이는 최신 수정이 설치되어 있는지 확인하는 권장 방법입니다. 클라이언트 라이브러리를 [Azure SDK for .NET](http://www.windowsazure.com/ko-kr/downloads/)최신 버전의 일부로 다운로드할 수도 있습니다.

Visual Studio에서 새 Windows 콘솔 응용 프로그램을 만들고 이름을 **GenerateSharedAccessSignatures**로 지정합니다. 다음 중 한 가지 방법을 사용하여 **Microsoft.WindowsAzure.Configuration.dll** 및 **Microsoft.WindowsAzure.Storage.dll**에 참조를 추가합니다.

- 	NuGet 패키지를 설치하려면 먼저 [NuGet Package Manager Extension for Visual Studio](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)를 설치합니다. Visual Studio에서 **프로젝트 | NuGet 패키지 관리**를 선택하고 온라인에서 **Azure 저장소**를 검색한 다음 지침에 따라 설치합니다.
- 	또는 Azure SDK 설치에서 어셈블리를 찾은 다음 참조를 추가합니다.
 
Program.cs 파일 맨 위에 다음 **using** 문을 추가합니다.

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

저장소 계정을 가리키는 연결 문자열과 함께 구성 설정을 포함하도록 app.config 파일을 편집합니다. app.config 파일은 다음과 비슷합니다.

    <configuration>
      <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings> 
    </configuration>

## 컨테이너에 대한 공유 액세스 서명 URI 생성 ##

먼저 새 컨테이너에서 공유 액세스 서명을 생성하는 메서드를 추가합니다. 이 경우 서명은 저장된 액세스 정책과 연결되지 않으므로, URI에서 만료 시간과 허용된 권한을 나타내는 정보를 전달합니다.

먼저 저장소 계정에 대한 액세스를 인증하고 새 컨테이너를 만드는 코드를 **Main()** 메서드에 추가합니다.

    static void Main(string[] args)
    {
	    //Parse the connection string and return a reference to the storage account.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
	    
	    //Create the blob client object.
	    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	    
	    //Get a reference to a container to use for the sample code, and create it if it does not exist.
	    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
	    container.CreateIfNotExists();
	    
	    //Insert calls to the methods created below here...
	    
	    //Require user input before closing the console window.
	    Console.ReadLine();
    }

그런 다음 컨테이너에 대한 공유 액세스 서명을 생성하고 서명 URI를 반환하는 새 메서드를 추가합니다.

    static string GetContainerSasUri(CloudBlobContainer container)
    {
	    //Set the expiry time and permissions for the container.
	    //In this case no start time is specified, so the shared access signature becomes valid immediately.
	    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
	    sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;
	    
	    //Generate the shared access signature on the container, setting the constraints directly on the signature.
	    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
	    
	    //Return the URI string for the container, including the SAS token.
	    return container.Uri + sasContainerToken;
    }

**Main()** 메서드의 맨 아래에 다음 줄을 추가하여 **Console.ReadLine()**을 호출하기 전에 **GetContainerSasUri()**를 호출하고 콘솔 창에 서명 URI를 씁니다.

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

새 컨테이너에 대한 공유 액세스 서명 URI를 출력하도록 컴파일 및 실행합니다. URI는 다음과 비슷합니다.       

https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

코드를 실행하면 컨테이너에서 만든 공유 액세스 서명이 다음 4시간 동안 유효해집니다. 서명은 클라이언트에게 컨테이너에 있는 Blob을 나열하고 컨테이너에 새 Blob을 쓸 수 있는 권한을 부여합니다.

## Blob에 대한 공유 액세스 서명 URI 생성 ##

이제 컨테이너 내에서 새 Blob을 만들고 해당 Blob에 대한 공유 액세스 서명을 생성하는 비슷한 코드를 작성합니다. 이 공유 액세스 서명은 저장된 액세스 정책과 연결되지 않으므로, URI에 대한 시작 시간, 만료 시간 및 권한 정보를 포함합니다.

새 Blob을 만들고 이 Blob에 일부 텍스트를 쓴 다음 공유 액세스 서명을 생성하고 서명 URI를 반환하는 새 메서드를 추가합니다.

    static string GetBlobSasUri(CloudBlobContainer container)
    {
	    //Get a reference to a blob within the container.
	    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");
	    
	    //Upload text to the blob. If the blob does not yet exist, it will be created. 
	    //If the blob does exist, its existing content will be overwritten.
	    string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
	    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	    ms.Position = 0;
	    using (ms)
	    {
		    blob.UploadFromStream(ms);
	    }
	    
	    //Set the expiry time and permissions for the blob.
	    //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
	    //The shared access signature will be valid immediately.
	    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
	    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
	    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
	    
	    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
	    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);
	    
	    //Return the URI string for the container, including the SAS token.
	    return blob.Uri + sasBlobToken;
    }

**Main()** 메서드 맨 아래에서 다음 줄을 추가하여 **
    
    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();
    

Compile and run to output the shared access signature URI for the new blob. The URI will be similar to the following URI:

https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

## Create a Stored Access Policy on the Container ##

Now let's create a stored access policy on the container, which will define the constraints for any shared access signatures that are associated with it.

In the previous examples, we specified the start time (implicitly or explicitly), the expiry time, and the permissions on the shared access signature URI itself. In the following examples, we will specify these on the stored access policy and not on the shared access signature. Doing so enables us to change these constraints without reissuing the shared access signature.

Note that it's possible to have one or more of the constraints on the shared access signature and the remainder on the stored access policy. However, you can only specify the start time, expiry time, and permissions in one place or the other; for example, you can't specify permissions on the shared access signature and also specify them on the stored access policy.

Add a new method that creates a new stored access policy and returns the name of the policy:

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container, string policyName)
    {
	    //Create a new stored access policy and define its constraints.
	    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
	    {
		    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
		    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
	    };
	    
	    //Get the container's existing permissions.
	    BlobContainerPermissions permissions = new BlobContainerPermissions();
	    
	    //Add the new policy to the container's permissions.
	    permissions.SharedAccessPolicies.Clear();
	    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
	    container.SetPermissions(permissions);
    }

At the bottom of the **Main()** method, before the call to **Console.ReadLine()**, add the following lines to call the **CreateSharedAccessPolicy()** method:    

    //Create an access policy on the container, which may be optionally used to provide constraints for 
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

## Generate a Shared Access Signature URI on the Container That Uses an Access Policy ##

Next, we'll create another shared access signature on the container that we created earlier, but this time we'll associate the signature with the access policy that we created in the previous example.

Add a new method to generate another shared access signature on the container:

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
	    //Generate the shared access signature on the container. In this case, all of the constraints for the 
	    //shared access signature are specified on the stored access policy.
	    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);
	    
	    //Return the URI string for the container, including the SAS token.
	    return container.Uri + sasContainerToken;
    }
    
At the bottom of the **Main()** method, before the call to **Console.ReadLine()**, add the following lines to call the **GetContainerSasUriWithPolicy** method:

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

## Generate a Shared Access Signature URI on the Blob That Uses an Access Policy ##

Finally, we'll add a similar method to create another blob and generate a shared access signature that's associated with an access policy.

Add a new method to create a blob and generate a shared access signature:

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
	    //Get a reference to a blob within the container.
	    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");
	    
	    //Upload text to the blob. If the blob does not yet exist, it will be created. 
	    //If the blob does exist, its existing content will be overwritten.
	    string blobContent = "This blob will be accessible to clients via a shared access signature. " + 
	    "A stored access policy defines the constraints for the signature.";
	    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	    ms.Position = 0;
	    using (ms)
	    {
		    blob.UploadFromStream(ms);
	    }
	    
	    //Generate the shared access signature on the blob.
	    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);
	    
	    //Return the URI string for the container, including the SAS token.
	    return blob.Uri + sasBlobToken;
    }

At the bottom of the **Main()** method, before the call to **Console.ReadLine()**, add the following lines to call the **GetBlobSasUriWithPolicy** method:    

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

The **Main()** method should now look like this in its entirety. Run it to write the shared access signature URIs to the console window, then copy and paste them into a text file for use in the second part of this tutorial.    

    static void Main(string[] args)
    {
	    //Parse the connection string and return a reference to the storage account.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
	    
	    //Create the blob client object.
	    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	    
	    //Get a reference to a container to use for the sample code, and create it if it does not exist.
	    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
	    container.CreateIfNotExists();
	    
	    //Generate a SAS URI for the container, without a stored access policy.
	    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
	    Console.WriteLine();
	    
	    //Generate a SAS URI for a blob within the container, without a stored access policy.
	    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
	    Console.WriteLine();
	    
	    //Create an access policy on the container, which may be optionally used to provide constraints for 
	    //shared access signatures on the container and the blob.
	    string sharedAccessPolicyName = "tutorialpolicy";
	    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
	    
	    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
	    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
	    Console.WriteLine();
	    
	    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
	    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
	    Console.WriteLine();
	    
	    Console.ReadLine();
    }

When you run the GenerateSharedAccessSignatures console application, you'll see output similar to the following in the console window. These are the shared access signatures that you'll use in Part 2 of the tutorial.

![sas-console-output-1][sas-console-output-1]

# Part 2: Create a Console Application to Test the Shared Access Signatures #

To test the shared access signatures created in the previous examples, we'll create a second console application that uses the signatures to perform operations on the container and on a blob.

Note that if more than four hours have passed since you completed the first part of the tutorial, the signatures you generated where the expiry time was set to four hours will no longer be valid. Similarly, the signatures associated with the stored access policy expire after 10 hours. If one or both of these intervals have passed, you should run the code in the first console application to generate fresh shared access signatures for use in the second part of the tutorial.

In Visual Studio, create a new Windows console application and name it **ConsumeSharedAccessSignatures**. Add references to **Microsoft.WindowsAzure.Configuration.dll** and **Microsoft.WindowsAzure.Storage.dll**, as you did previously.

At the top of the Program.cs file, add the following **using** statements:

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
In the body of the **Main()** method, add the following constants, and update their values to the shared access signatures that you generated in part 1 of the tutorial.

    static void Main(string[] args)
    {
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }
    
## Add a Method to Try Container Operations Using a Shared Access Signature ##

Next, we'll add a method that tests some representative container operations using a shared access signature on the container. Note that the shared access signature is used to return a reference to the container, authenticating access to the container based on the signature alone.

Add the following method to Program.cs:


	static void UseContainerSAS(string sas)
	{
	    //Try performing container operations with the SAS provided.
	
	    //Return a reference to the container using the SAS URI.
	    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));
	
	    //Create a list to store blob URIs returned by a listing operation on the container.
	    List<Uri> blobUris = new List<Uri>();
	
	    try
	    {
	        //Write operation: write a new blob to the container. 
	        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
	        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
	        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	        msWrite.Position = 0;
	        using (msWrite)
	        {
	            blob.UploadFromStream(msWrite);
	        }
	        Console.WriteLine("Write operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Write operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	
	    try
	    {
	        //List operation: List the blobs in the container, including the one just added.
	        foreach (ICloudBlob blobListing in container.ListBlobs())
	        {
	            blobUris.Add(blobListing.Uri);
	        }
	        Console.WriteLine("List operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("List operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	
	    try
	    {
	        //Read operation: Get a reference to one of the blobs in the container and read it. 
	        CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
	        MemoryStream msRead = new MemoryStream();
	        msRead.Position = 0;
	        using (msRead)
	        {
	            blob.DownloadToStream(msRead);
	            Console.WriteLine(msRead.Length);
	        }
	        Console.WriteLine("Read operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Read operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	    Console.WriteLine();
	
	    try
	    {
	        //Delete operation: Delete a blob in the container.
	        CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
	        blob.Delete();
	        Console.WriteLine("Delete operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Delete operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }        
	}


Update the **Main()** method to call **UseContainerSAS()** with both of the shared access signatures that you created on the container:


	static void Main(string[] args)
	{
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
	
	    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
	    UseContainerSAS(containerSAS);
	    UseContainerSAS(containerSASWithAccessPolicy); 
	    
	    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
	    UseBlobSAS(blobSAS);
	    UseBlobSAS(blobSASWithAccessPolicy);
	
	    Console.ReadLine();
	}


## Add a Method to Try Blob Operations Using a Shared Access Signature ##

Finally, we'll add a method that tests some representative blob operations using a shared access signature on the blob. In this case we use the constructor **CloudBlockBlob(String)**, passing in the shared access signature, to return a reference to the blob. No other authentication is required; it's based on the signature alone.

Add the following method to Program.cs:


	static void UseBlobSAS(string sas)
	{
	    //Try performing blob operations using the SAS provided.
	
	    //Return a reference to the blob using the SAS URI.
	    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));
	
	    try
	    {
	        //Write operation: write a new blob to the container. 
	        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
	        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	        msWrite.Position = 0;
	        using (msWrite)
	        {
	            blob.UploadFromStream(msWrite);
	        }
	        Console.WriteLine("Write operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Write operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	
	    try
	    {
	        //Read operation: Read the contents of the blob.
	        MemoryStream msRead = new MemoryStream();
	        using (msRead)
	        {
	            blob.DownloadToStream(msRead);
	            msRead.Position = 0;
	            using (StreamReader reader = new StreamReader(msRead, true))
	            {
	                string line;
	                while ((line = reader.ReadLine()) != null)
	                {
	                    Console.WriteLine(line);
	                }
	            }
	        }
	        Console.WriteLine("Read operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Read operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }
	
	    try
	    {
	        //Delete operation: Delete the blob.
	        blob.Delete();
	        Console.WriteLine("Delete operation succeeded for SAS " + sas);
	        Console.WriteLine();
	    }
	    catch (StorageException e)
	    {
	        Console.WriteLine("Delete operation failed for SAS " + sas);
	        Console.WriteLine("Additional error information: " + e.Message);
	        Console.WriteLine();
	    }        
	}


Update the **Main()** method to call **UseBlobSAS()** with both of the shared access signatures that you created on the blob:

	static void Main(string[] args)
	{
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
	
	    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
	    UseContainerSAS(containerSAS);
	    UseContainerSAS(containerSASWithAccessPolicy); 
	    
	    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
	    UseBlobSAS(blobSAS);
	    UseBlobSAS(blobSASWithAccessPolicy);
	
	    Console.ReadLine();
	}

Run the console application and observe the output to see which operations are permitted for which signatures. The output in the console window will look similar to the following:

![sas-console-output-2][sas-console-output-2]

# Next Steps #

[Shared Access Signatures, Part 1: Understanding the SAS Model](../storage-dotnet-shared-access-signature-part-1/)

[Manage Access to Azure Storage Resources](http://msdn.microsoft.com/ko-kr/library/windowsazure/ee393343.aspx)

[Delegating Access with a Shared Access Signature (REST API)](http://msdn.microsoft.com/ko-kr/library/windowsazure/ee395415.aspx)

[Introducing Table and Queue SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG


<!--HONumber=35.1-->
