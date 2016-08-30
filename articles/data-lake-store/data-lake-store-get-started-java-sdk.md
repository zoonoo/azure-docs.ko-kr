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
   ms.date="08/18/2016"
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
* Data Lake Store 공개 미리 보기에 대해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.
* **Azure Active Directory 응용 프로그램 만들기**. **대화형** 및 **비대화형**과 같은 두 가지 방법으로 Azure Active Directory를 사용하여 인증할 수 있습니다. 인증하려는 방법에 따라 다른 필수 구성 요소가 있습니다.
	* **대화형 인증의 경우** - Azure Active Directory에서 **네이티브 클라이언트 응용 프로그램**을 만들어야 합니다. 응용 프로그램을 만든 후 응용 프로그램과 관련된 다음 값을 검색합니다.
		- 응용 프로그램에 **클라이언트 ID** 및 **리디렉션 URI** 가져오기
		- 위임된 권한 설정

	* **비대화형 인증**(이 문서에서 사용) - Azure Active Directory에서 **웹 응용 프로그램**을 만들어야 합니다. 응용 프로그램을 만든 후 응용 프로그램과 관련된 다음 값을 검색합니다.
		- 응용 프로그램의 **클라이언트 ID**, **클라이언트 암호** 및 **리디렉션 URI** 가져오기
		- 위임된 권한 설정
		- 역할에 Azure Active Directory 응용 프로그램을 할당합니다. Azure Active Directory 응용 프로그램에 권한을 부여하려는 범위의 수준을 역할에 지정할 수 있습니다. 예를 들어 구독 수준 또는 리소스 그룹 수준으로 응용 프로그램을 할당할 수 있습니다.

	이러한 값을 검색하고 권한을 설정하며 역할을 할당하는 방법에 대한 지침은 [포털을 사용하여 Active Directory 응용 프로그램 및 서비스 주체 만들기](../resource-group-create-service-principal-portal.md)를 참조하세요.

## Azure Active Directory를 사용하여 인증하려면 어떻게 해야 하나요?

아래 코드 조각은 응용 프로그램이 자체 자격 증명을 제공하는 **비대화형** 인증에 대한 코드를 제공합니다.

이 자습서를 수행하기 위해 응용 프로그램에 Azure에서 리소스를 만드는 사용 권한을 부여해야 합니다. 이 자습서의 목적상 응용 프로그램 참가자에게 Azure 구독에서 사용하지 않고 비어 있는 새 리소스 그룹에 대한 사용 권한을 부여하는 것이 **가장 좋습니다**.

## Java 응용 프로그램 만들기

1. **명령줄 앱** 템플릿을 사용하여 IntelliJ를 열고 새 Java 프로젝트를 만듭니다. 마법사를 완료하여 프로젝트를 만듭니다.

2. **파일** -> **프로젝트 구조** -> **모듈**(프로젝트 설정에서) -> **종속성** -> **+** -> **라이브러리** -> **Maven에서**를 엽니다.

3. 다음 Maven 패키지를 검색하고 프로젝트를 추가합니다.

    * com.microsoft.azure:azure-mgmt-datalake-store:1.0.0-beta1.2
    * com.microsoft.azure:azure-mgmt-datalake-store-uploader:1.0.0-beta1.2
    * com.microsoft.azure:azure-client-authentication:1.0.0-beta2

4. 왼쪽 창에서 **src**, **main**, **java**, **<package name>**으로 이동한 다음 **Main.java**를 열고 기존 코드 블록을 다음 코드로 바꿉니다. 또한 **localFolderPath**, **DATA-LAKE-STORE-NAME**, **RESOURCE-GROUP-NAME** 등의 코드 조각에서 호출되는 매개 변수의 값을 입력하고 **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** 및 **SUBSCRIPTION-ID**의 자리 표시자를 구독 및 해당 Azure Active Directory에 대한 정보로 바꿉니다. 이 정보를 찾는 방법에 대한 자세한 내용은 [서비스 주체를 만드는 Azure 가이드](../resource-group-authenticate-service-principal.md)를 참조하세요.

    이 코드는 Data Lake 저장소 계정 만들기, 저장소에서 폴더 만들기, 파일 연결, 파일 다운로드, 마지막으로 계정을 삭제하는 프로세스를 진행합니다.

        package com.company;

        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.implementation.DataLakeStoreAccountManagementClientImpl;
        import com.microsoft.azure.management.datalake.store.implementation.DataLakeStoreFileSystemManagementClientImpl;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.azure.management.datalake.store.uploader.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;

        public class Main {
            final static String ADLS_ACCOUNT_NAME = <DATA-LAKE-STORE-NAME>;
            final static String RESOURCE_GROUP_NAME = "<RESOURCE-GROUP-NAME>";
            final static String LOCATION = "East US 2";
            final static String TENANT_ID = "<TENANT-ID>";
            final static String SUBSCRIPTION_ID =  "<SUBSCRIPTION-ID>";
            final static String CLIENT_ID = "<CLIENT-ID>";
            final static String CLIENT_SECRET = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.

            private static DataLakeStoreAccountManagementClientImpl _adlsClient;
            private static DataLakeStoreFileSystemManagementClientImpl _adlsFileSystemClient;

            public static void main(String[] args) throws Exception {
                String localFolderPath = "C:\\local_path\"; // TODO: Change this to any unused, new, empty folder on your local machine.

                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(CLIENT_ID, TENANT_ID, CLIENT_SECRET, null);
                SetupClients(creds);

                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");

                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", SUBSCRIPTION_ID));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.accounts().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.name());
                }
                WaitForNewline("Account(s) displayed.", "Uploading file.");

                // Upload a file to Data Lake Store: file1.csv
                UploadFile(localFolderPath + "file1.csv", "/file1.csv");
                WaitForNewline("File uploaded.", "Appending newline.");

                // Append newline to file1.csv
                AppendToFile("/file1.csv", "\r\n");
                WaitForNewline("Newline appended.", "Creating file.");

                // Create a new file in Data Lake Store: file2.csv
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("File created.", "Concatenating files.");

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
                _adlsClient.withSubscriptionId(SUBSCRIPTION_ID);
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
                adlsParameters.withLocation(LOCATION);

                _adlsClient.accounts().create(RESOURCE_GROUP_NAME, ADLS_ACCOUNT_NAME, adlsParameters);
            }

            // Create file
            public static void CreateFile(String path) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().create(ADLS_ACCOUNT_NAME, path);
            }

            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, AdlsErrorException {
                byte[] bytesContents = contents.getBytes();

                _adlsFileSystemClient.fileSystems().create(ADLS_ACCOUNT_NAME, path, bytesContents, force);
            }

            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, AdlsErrorException {
                byte[] bytesContents = contents.getBytes();

                _adlsFileSystemClient.fileSystems().append(ADLS_ACCOUNT_NAME, path, bytesContents);
            }

            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().concat(ADLS_ACCOUNT_NAME, destFilePath, srcFilePaths);
            }

            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().delete(ADLS_ACCOUNT_NAME, filePath);
            }

            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, AdlsErrorException {
                return _adlsFileSystemClient.fileSystems().getFileStatus(ADLS_ACCOUNT_NAME, path).getBody().fileStatus();
            }

            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, AdlsErrorException {
                return _adlsFileSystemClient.fileSystems().listFileStatus(ADLS_ACCOUNT_NAME, directoryPath).getBody().fileStatuses().fileStatus();
            }

            // Upload file
            public static void UploadFile(String srcPath, String destPath) throws Exception {
                UploadParameters parameters = new UploadParameters(srcPath, destPath, ADLS_ACCOUNT_NAME);
                FrontEndAdapter frontend = new DataLakeStoreFrontEndAdapterImpl(ADLS_ACCOUNT_NAME, _adlsFileSystemClient);
                DataLakeStoreUploader uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.execute();
            }

            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, AdlsErrorException {
                InputStream stream = _adlsFileSystemClient.fileSystems().open(ADLS_ACCOUNT_NAME, srcPath).getBody();

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
                _adlsClient.accounts().delete(RESOURCE_GROUP_NAME, ADLS_ACCOUNT_NAME);
            }
        }


6. 응용 프로그램을 실행합니다. 프롬프트에 따라 응용 프로그램을 실행하고 완료합니다.

## 다음 단계

- [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
- [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [데이터 레이크 저장소와 함께 Azure HDInsight 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0824_2016-->