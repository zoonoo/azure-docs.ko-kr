<properties
   pageTitle="Data Lake 저장소 Java SDK를 사용하여 응용 프로그램 개발 | Azure"
   description="Azure Data Lake 저장소 Java SDK를 사용하여 응용 프로그램 개발"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/12/2016"
   ms.author="nitinme"/>

# Java를 사용하여 Azure Data Lake 저장소 시작

> [AZURE.SELECTOR]
- [포털](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)

Azure Data Lake 저장소 Java SDK를 사용하여 Azure Data Lake 계정을 만들고 폴더 만들기, 데이터 파일 업로드 및 다운로드, 계정 삭제 등의 기본 작업을 수행하는 방법에 대해 알아봅니다. 데이터 레이크에 대한 자세한 내용은 [Azure 데이터 레이크 저장소](data-lake-store-overview.md)를 참조하세요.

## Azure Data Lake 저장소 Java SDK

다음 링크는 Data Lake 저장소 및 Java SDK 참조용 Java SDK에 대한 다운로드 위치를 제공합니다. 이 자습서에서는 SDK를 다운로드하거나 참조 문서를 수행할 필요가 없습니다. 이러한 링크는 정보만 제공합니다.

* Data Lake 저장소용 Java SDK에 대한 소스 코드는 [GitHub](https://github.com/Azure/azure-sdk-for-java)에서 사용할 수 있습니다.
* Data Lake 저장소용 Java SDK 참조는 [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/)에서 제공됩니다.

## 필수 조건

* JDK(Java Development Kit) 8 (Java 버전 1.8 사용).
* IntelliJ 또는 다른 적절한 Java 개발 환경. 선택 사항이지만 권장합니다. 아래 지침에서는 IntelliJ를 사용합니다.
* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 데이터 레이크 저장소 공개 미리 보기를 위해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.
* **Azure Active Directory 응용 프로그램 만들기**. **대화형** 및 **비대화형**과 같은 두 가지 방법으로 Azure Active Direcotry를 사용하여 인증할 수 있습니다. 인증하려는 방법에 따라 다른 필수 구성 요소가 있습니다.
	* **대화형 인증의 경우** - Azure Active Directory에서 **네이티브 클라이언트 응용 프로그램**을 만들어야 합니다. 응용 프로그램을 만든 후 응용 프로그램과 관련된 다음 값을 검색합니다.
		- 응용 프로그램에 **클라이언트 ID** 및 **리디렉션 URI** 가져오기
		- 위임된 권한 설정

	* **비대화형 인증**(이 문서에서 사용) - Azure Active Directory에서 **웹 응용 프로그램**을 만들어야 합니다. 응용 프로그램을 만든 후 응용 프로그램과 관련된 다음 값을 검색합니다.
		- 응용 프로그램의 **클라이언트 ID**, **클라이언트 암호** 및 **리디렉션 URI** 가져오기
		- 위임된 권한 설정
		- 역할에 Azure Active Directory 응용 프로그램을 할당합니다. Azure Active Directory 응용 프로그램에 권한을 부여하려는 범위의 수준을 역할에 지정할 수 있습니다. 예를 들어 응용 프로그램을 구독 수준 또는 리소스 그룹 수준에 할당할 수 있습니다. 

	이러한 값을 검색하고 권한을 설정하며 역할을 할당하는 방법에 대한 지침은 [포털을 사용하여 Active Directory 응용 프로그램 및 서비스 주체 만들기](../resource-group-create-service-principal-portal.md)를 참조하세요.

## Azure Active Directory를 사용하여 인증하려면 어떻게 해야 하나요?

아래 코드 조각은 응용 프로그램이 자체 자격 증명을 제공하는 **비대화형** 인증에 대한 코드를 제공합니다.

이 자습서를 수행하기 위해 응용 프로그램 Azure에서 리소스를 만드는 권한을 부여해야 합니다. 이 자습서의 목적상 응용 프로그램 참가자에게 Azure 구독에서 사용하지 않고 비어 있는 새 리소스 그룹에 대한 권한을 부여하는 것이 **좋습니다**.

## Java 응용 프로그램 만들기

1. **명령줄 앱** 템플릿을 사용하여 IntelliJ를 열고 새 Java 프로젝트를 만듭니다. 마법사를 완료하여 프로젝트를 만듭니다.

2. 화면의 왼쪽에 있는 프로젝트를 마우스 오른쪽 단추로 클릭하고 **프레임워크 지원 추가**를 클릭합니다. **Maven**을 선택하고 **확인**을 클릭합니다.

3. 새로 만든 **"pom.xml"** 파일을 열고 **</version>** 태그 및 **</project>** 태그 사이에 다음 텍스트의 코드 조각을 추가합니다.

    >[AZURE.NOTE] 이 단계는 Maven에서 Azure Data Lake 저장소 SDK를 사용할 수 있을 때까지 일시적입니다. SDK를 Maven에서 제공하면 이 문서가 업데이트됩니다. 이 SDK에 대한 모든 이후 업데이트는 Maven을 통해 사용할 수 있습니다.

        <repositories>
        	<repository>
	            <id>adx-snapshots</id>
	            <name>Azure ADX Snapshots</name>
	            <url>http://adxsnapshots.azurewebsites.net/</url>
	            <layout>default</layout>
	            <snapshots>
                	<enabled>true</enabled>
            	</snapshots>
        	</repository>
        	<repository>
	            <id>oss-snapshots</id>
	            <name>Open Source Snapshots</name>
	            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
	            <layout>default</layout>
	            <snapshots>
	                <enabled>true</enabled>
	                <updatePolicy>always</updatePolicy>
	            </snapshots>
        	</repository>
    	</repositories>
    	<dependencies>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-client-authentication</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-mgmt-datalake-store</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
    	</dependencies>


4. **파일**, **설정**, **빌드, 실행, 배포**로 차례로 이동합니다. **빌드 도구**, **Maven**을 차례로 확장한 다음 **가져오기**를 확장합니다. **Maven 프로젝트 자동으로 가져오기** 확인란을 선택합니다. **적용**을 클릭한 다음 **확인**을 클릭합니다.

5. 왼쪽 창에서 **src**, **main**, **java**, **<package name>**으로 이동한 다음 **Main.java**를 열고 기존 코드 블록을 다음 코드로 바꿉니다. 또한 **localFolderPath**, **\_adlsAccountName**, **\_resourceGroupName** 등의 코드 조각에서 호출되는 매개 변수의 값을 입력하고 **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** 및 **SUBSCRIPTION-ID**의 자리 표시자를 바꿉니다.

    이 코드는 Data Lake 저장소 계정 만들기, 저장소에서 폴더 만들기, 파일 연결, 파일 다운로드, 마지막으로 계정을 삭제하는 프로세스를 진행합니다.

        package com.company;
        
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
        
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Account(s) displayed.", "Creating files.");
        
                // Create two files in Data Lake Store: file1.csv and file2.csv
                CreateFile("/file1.csv", "123,abc", true);
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("Files created.", "Concatenating files.");
        
                // Concatenate two files in Data Lake Store
                List<String> srcFilePaths = new ArrayList<String>();
                srcFilePaths.add("/file1.csv");
                srcFilePaths.add("/file2.csv");
                ConcatenateFiles(srcFilePaths, "/input.csv");
                WaitForNewline("Files concatenated.", "Downloading file.");
        
                // Download file from Data Lake Store
                DownloadFile("/input.csv", localFolderPath + "input.csv");
                WaitForNewline("File downloaded.", "Deleting file.");
        
                // Delete file from Data Lake Store
                DeleteFile("/input.csv");
                WaitForNewline("File deleted.", "Deleting account.");
        
                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted.", "DONE.");
            }
        
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
        
                _adlsClient.setSubscriptionId(_subId);
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
                if (!nextAction.isEmpty())
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                    System.out.println(nextAction);
                }
                else
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                }
            }
        
            // Create Data Lake Store account
            public static void CreateAccount() throws InterruptedException, CloudException, IOException {
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }
        
            // Create file
            public static void CreateFile(String path) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path);
            }
        
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().append(_adlsAccountName, path, bytesContents);
            }
        
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().concat(_adlsAccountName, destFilePath, srcFilePaths);
            }
        
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(_adlsAccountName, filePath);
            }
        
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().getFileStatus(_adlsAccountName, path).getBody().getFileStatus();
            }
        
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().listFileStatus(_adlsAccountName, directoryPath).getBody().getFileStatuses().getFileStatus();
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(_adlsAccountName, srcPath).getBody();
        
                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
        
                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();
        
                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }
        
                pWriter.println(fileContents);
                pWriter.close();
            }
        
            // Delete account
            public static void DeleteAccount() throws InterruptedException, CloudException, IOException {
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
            }
        }


6. 응용 프로그램을 실행합니다. 프롬프트에 따라 응용 프로그램을 실행하고 완료합니다.

## 다음 단계

- [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
- [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [데이터 레이크 저장소와 함께 Azure HDInsight 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0518_2016-->