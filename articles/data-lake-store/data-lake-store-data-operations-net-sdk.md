---
title: ".NET SDK: Azure Data Lake Store에서의 파일 시스템 작업 | Microsoft Docs"
description: "Azure Data Lake Store .NET SDK를 사용하여 Data Lake Store에서 폴더 만들기 등의 파일 시스템 작업을 수행합니다."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 7f6319dcf1ae66a686dd1c2ea2810b3041183098
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>.NET SDK를 사용한 Azure Data Lake Store에서의 파일 시스템 작업
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

이 문서에서는 .NET SDK를 사용하여 Data Lake Store에서 파일 시스템 작업을 수행하는 방법을 알아 봅니다. 파일 시스템 작업에는 Data Lake Store 계정에 폴더 만들기, 파일 업로드, 파일 다운로드 등이 포함됩니다.

.NET SDK를 사용하여 Data Lake Store에서 계정 관리 작업을 수행하는 방법에 대한 지침은 [.NET SDK를 사용한 Data Lake Store에서의 계정 관리 작업](data-lake-store-get-started-net-sdk.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
* **Visual Studio 2013, 2015 또는 2017**. 아래 지침에서는 Visual Studio 2017을 사용합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Data Lake Store 계정**. 계정을 만드는 방법에 대한 지침은 [Azure Data Lake Store 시작](data-lake-store-get-started-portal.md)을 참조하세요.

## <a name="create-a-net-application"></a>.NET 응용 프로그램 만들기
1. Visual Studio를 열고 콘솔 응용 프로그램을 만듭니다.
2. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.
3. **새 프로젝트**에서 다음 값을 입력하거나 선택합니다.

   | 속성 | 값 |
   | --- | --- |
   | Category |Templates/Visual C#/Windows |
   | Template |콘솔 응용 프로그램 |
   | 이름 |CreateADLApplication |
4. **확인** 을 클릭하여 프로젝트를 만듭니다.
5. NuGet 패키지를 프로젝트에 추가합니다.

   1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
   2. **NuGet 패키지 관리자** 탭에서 **패키지 원본**이 **nuget.org**로 설정되어 있고 **시험판 포함** 확인란이 선택되어 있는지 확인합니다.
   3. 다음 NuGet 패키지를 검색하고 설치합니다.

      * `Microsoft.Azure.Management.DataLake.Store` - 이 자습서에서는 v2.1.3-미리 보기를 사용합니다.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 이 자습서는 v2.2.12를 사용합니다.

        ![NuGet 원본 추가](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "새 Azure Data Lake 계정 만들기")
   4. **NuGet 패키지 관리자**를 닫습니다.
6. **Program.cs**를 열고 기존 코드를 삭제한 후 다음 문을 포함시켜서 네임스페이스에 대한 참조를 추가합니다.

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. 아래 표시된 것처럼 변수를 선언하고 자리 표시자에 대한 값을 제공합니다. 또한, 여기에 제공하는 로컬 경로와 파일 이름이 컴퓨터에 존재해야 합니다.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static async void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

문서의 나머지 섹션에서는 제공되는 .NET 메서드를 사용하여 인증, 파일 업로드 등의 작업을 수행하는 방법을 볼 수 있습니다.

## <a name="authentication"></a>인증

* 응용 프로그램에 대한 최종 사용자 인증의 경우 [.NET SDK를 사용한 Data Lake Store의 최종 사용자 인증](data-lake-store-end-user-authenticate-net-sdk.md)을 참조하세요.
* 응용 프로그램에 대한 서비스 간 인증의 경우 [.NET SDK를 사용한 Data Lake Store의 서비스 간 인증](data-lake-store-service-to-service-authenticate-net-sdk.md)을 참조하세요.


## <a name="create-client-objects"></a>클라이언트 개체 만들기
다음 코드 조각은 이는 서비스에 요청을 발급하는 데 사용되는 Data Lake Store 계정 및 파일 시스템 클라이언트 개체를 만듭니다.

    // Create client objects
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

## <a name="create-a-directory"></a>디렉터리 만들기
클래스에 다음 메서드를 추가합니다. 코드 조각은 Data Lake Store 계정 내 디렉터리 생성에 사용할 수 있는 `CreateDirectory()` 메서드를 보여 줍니다.

    // Create a directory
    public static void CreateDirectory(string path)
    {
            _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

`Main()` 메서드에 다음 코드 조각을 추가하여 `CreateDirectory()` 메서드를 호출합니다.

    CreateDirectory(remoteFolderPath);
    Console.WriteLine("Created a directory in the Data Lake Store account. Press any key to continue ...");
    Console.ReadLine();

## <a name="upload-a-file"></a>파일 업로드
클래스에 다음 메서드를 추가합니다. 코드 조각은 Data Lake Store 계정에 파일을 업로드하는 데 사용할 수 있는 `UploadFile()` 메서드를 보여 줍니다. SDK는 로컬 파일 경로와 Data Lake Store 파일 경로 간의 재귀 업로드 및 다운로드를 지원합니다.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

`Main()` 메서드에 다음 코드 조각을 추가하여 `UploadFile()` 메서드를 호출합니다.

    UploadFile(localFilePath, remoteFilePath, true);
    Console.WriteLine("Uploaded file in the directory. Press any key to continue...");
    Console.ReadLine();
    

## <a name="get-file-or-directory-info"></a>파일 또는 디렉터리 정보 가져오기
다음 코드 조각은 Data Lake Store에서 사용할 수 있는 파일이나 디렉터리에 대한 정보를 검색하는 데 사용할 수 있는 `GetItemInfo()` 메서드를 보여 줍니다.

    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

`Main()` 메서드에 다음 코드 조각을 추가하여 `GetItemInfo()` 메서드를 호출합니다.

    
    var fileProperties = GetItemInfo(remoteFilePath);
    Console.WriteLine("The owner of the file at the path is:", fileProperties.Owner);
    Console.WriteLine("Press any key to continue...");
    Console.ReadLine();

## <a name="list-file-or-directories"></a>파일 또는 디렉터리 나열
다음 코드 조각은 Data Lake Store 계정의 파일과 디렉터리를 나열하는 데 사용할 수 있는 `ListItems()` 메서드를 보여 줍니다.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

`Main()` 메서드에 다음 코드 조각을 추가하여 `ListItems()` 메서드를 호출합니다.

    var itemList = ListItems(remoteFolderPath);
    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
    Console.WriteLine(String.Join("\r\n", fileMenuItems));
    Console.WriteLine("Files and directories listed. Press any key to continue ...");
    Console.ReadLine();

## <a name="concatenate-files"></a>파일 연결
다음 코드 조각은 파일 연결에 사용하는 `ConcatenateFiles()` 메서드를 보여 줍니다.

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

`Main()` 메서드에 다음 코드 조각을 추가하여 `ConcatenateFiles()` 메서드를 호출합니다. 이 코드 조각은 Data Lake Store 계정에 다른 파일을 업로드하고, 파일의 경로가 *anotherRemoteFilePath* 문자열에 제공되었다고 가정합니다.

    string[] stringOfFiles = new String[] {remoteFilePath, anotherRemoteFilePath};
    string destFilePath = Path.Combine(remoteFolderPath, "Concatfile.txt");
    ConcatenateFiles(stringOfFiles, destFilePath);
    Console.WriteLine("Files concatinated. Press any key to continue ...");
    Console.ReadLine();

## <a name="append-to-a-file"></a>파일에 추가
다음 코드 조각은 Data Lake Store 계정에 이미 저장된 파일에 데이터를 추가하는 데 사용하는 `AppendToFile()` 메서드를 보여 줍니다.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

`Main()` 메서드에 다음 코드 조각을 추가하여 `AppendToFile()` 메서드를 호출합니다.

    AppendToFile(remoteFilePath, "123");
    Console.WriteLine("Content appended. Press any key to continue ...");
    Console.ReadLine();

## <a name="download-a-file"></a>파일 다운로드
다음 코드 조각은 Data Lake Store 계정에서 파일을 다운로드하는 데 사용하는 `DownloadFile()` 메서드를 보여 줍니다.

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:true);
    }

`Main()` 메서드에 다음 코드 조각을 추가하여 `DownloadFile()` 메서드를 호출합니다.

    DownloadFile(destFilePath, localFilePath);
    Console.WriteLine("File downloaded. Press any key to continue ...");
    Console.ReadLine();

## <a name="see-also"></a>참고 항목
* [.NET SDK를 사용한 Data Lake Store에서의 계정 관리 작업](data-lake-store-get-started-net-sdk.md)
* [Data Lake Store .NET SDK 참조](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>다음 단계
* [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
