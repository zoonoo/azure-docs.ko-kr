---
title: "Azure Data Lake Store에서 Java SDK를 사용하여 응용 프로그램 개발 | Microsoft Docs"
description: "Azure Data Lake Store Java SDK를 사용하여 Data Lake Store 계정을 만들고 Data Lake Store에서 기본 작업을 수행합니다."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 3e4302e7c5b54a46994eccaa8cf9ecbb0a84446d
ms.lasthandoff: 03/03/2017


---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>Java를 사용하여 Azure Data Lake 저장소 시작
> [!div class="op_single_selector"]
> * [포털](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Azure Data Lake Store Java SDK를 사용하여 폴더 만들기, 데이터 파일 업로드 및 다운로드 등의 기본 작업을 수행하는 방법에 대해 알아봅니다. Data Lake에 대한 자세한 내용은 [Azure Data Lake Store](data-lake-store-overview.md)를 참조하세요.

[Azure Data Lake Store Java API 문서](https://azure.github.io/azure-data-lake-store-java/javadoc/)에서 Azure Data Lake Store에 대한 Java SDK API 관련 문서에 액세스할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* Java 개발 키트(JDK 7 이상, Java 버전 1.7 이상 사용)
* Azure Data Lake Store 계정 [Azure Portal을 사용하여 Azure Data Lake Store 시작](data-lake-store-get-started-portal.md)에 있는 지침을 따릅니다.
* [Maven](https://maven.apache.org/install.html) 이 자습서에서는 빌드 및 프로젝트 종속성을 위해 Maven을 사용합니다. Maven 또는 Gradle과 같은 빌드 시스템을 사용하지 않고 빌드할 수 있지만 이러한 시스템 생성은 종속성을 훨씬 쉽게 관리할 수 있습니다.
* (선택 사항)[IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 또는 [Eclipse](https://www.eclipse.org/downloads/)나 유사한 IDE

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Azure Active Directory를 사용하여 인증하려면 어떻게 해야 하나요?
이 자습서에서는 Azure AD 응용 프로그램 클라이언트 암호를 사용하여 Azure Active Directory 토큰(서비스 간 인증)을 검색합니다. 작업 파일 및 디렉터리 작업을 수행하려면 이 토큰을 사용하여 Data Lake Store 클라이언트 개체를 만듭니다. 클라이언트 암호를 사용하여 Azure Data Lake Store로 인증하는 방법에 대한 지침은 다음과 같은 고도의 수행합니다.

1. Azure AD 웹 응용 프로그램 만들기
2. Azure AD 웹 응용 프로그램의 클라이언트 ID, 클라이언트 암호 및 토큰 끝점을 검색합니다.
3. 만들고 있는 Java 응용 프로그램에서 액세스하려는 Data Lake Store 파일/폴더에서 Azure AD 웹 응용 프로그램에 대한 액세스를 구성합니다.

이러한 단계를 수행하는 방법에 대한 지침은 [Active Directory 응용 프로그램 만들기](data-lake-store-authenticate-using-active-directory.md)를 참조하세요.

Azure Active Directory는 토큰을 검색할 뿐만 아니라 다른 옵션을 제공합니다. 다양한 시나리오에 맞게 다양한 인증 메커니즘을 선택할 수 있습니다(예: 브라우저에서 실행 중인 응용 프로그램, 데스크톱 응용 프로그램으로 배포된 응용 프로그램 또는 온-프레미스 또는 Azure 가상 컴퓨터에서 실행 중인 서버 응용 프로그램). 암호, 인증서, 2단계 인증과 같은 여러 유형의 자격 증명을 선택할 수 있습니다. 또한 Azure Active Directory를 사용하면 클라우드와 온-프레미스 Active Directory 사용자를 동기화할 수 있습니다. 자세한 내용은 [Azure Active directory 인증 시나리오](../active-directory/active-directory-authentication-scenarios.md)를 참조하세요. 

## <a name="create-a-java-application"></a>Java 응용 프로그램 만들기
[GitHub에서](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) 사용 가능한 코드 샘플은 저장소에서 파일 만들기, 파일 연결, 파일 다운로드 및 저장소에서 파일 삭제와 같은 프로세스를 설명합니다. 문서의 이 섹션은 코드의 주요 부분을 설명합니다.

1. 명령줄 또는 IDE에서 [mvn 원형](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)을 사용하여 Maven 프로젝트를 만듭니다. IntelliJ를 사용하여 Java 프로젝트를 만드는 방법에 대한 자세한 내용은 [여기](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)를 참조하세요. Eclipse를 사용하여 프로젝트를 만드는 방법에 대한 자세한 내용은 [여기](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)를 참조하세요. 
2. Maven **pom.xml** 파일에 다음 종속성을 추가합니다. **\</version>** 태그와 **\</project>** 태그 사이에 텍스트의 코드 조각을 추가합니다.
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.4</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    첫 번째 종속성은 Maven 리포지토리에서 Data Lake Store SDK(`azure-data-lake-store-sdk`)를 사용하는 것입니다. 두 번째 종속성(`slf4j-nop`)은 이 응용 프로그램에 사용하는 로깅 프레임워크를 지정하는 것입니다. Data Lake Store SDK는 [slf4j](http://www.slf4j.org/) 로깅 외관을 사용하며 이로 인해 log4j, Java 로깅, logback 혹은 로깅 없음과 같은 다양하고 인기 있는 로깅 프레임워크를 선택할 수 있습니다. 이 예제에서 로깅을 비활성화하므로 **slf4j nop** 바인딩을 사용합니다. 앱에서 다른 로깅 옵션을 사용하려면 [여기](http://www.slf4j.org/manual.html#projectDep)를 참조하세요.

### <a name="add-the-application-code"></a>응용 프로그램 코드 추가
코드에는 세 가지 주요 부분이 있습니다.

1. Azure Active Directory 토큰 가져오기
2. 토큰을 사용하여 Data Lake Store 클라이언트를 만듭니다.
3. Data Lake Store 클라이언트를 사용하여 작업을 수행합니다.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>1단계: Azure Active Directory 토큰 가져오기
Data Lake Store SDK는 Data Lake Store 계정에 대해 이야기하는 데 필요한 보안 토큰을 관리할 수 있는 편리한 방법을 제공합니다. 그러나 SDK에서는 이러한 방법만 필수로 사용해야 하는 것은 아닙니다. [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java) 또는 사용자 지정 코드를 사용할 뿐만 아니라 토큰을 가져오는 다른 방법을 사용할 수 있습니다.

Data Lake Store SDK를 사용하여 앞에서 만든 Active Directory 웹 응용 프로그램에 대한 토큰을 가져오려면 `AccessTokenProvider` 서브클래스 중 하나(아래 예에서 `ClientCredsTokenProvider` 사용)를 사용합니다. 토큰 공급자는 메모리에 토큰을 가져오는 데 사용 되는 자격 증명을 캐시하고 만료될 경우 토큰을 자동으로 갱신합니다. `AccessTokenProvider`의 자체 서브클래스를 만들 수 있기 때문에 고객 코드를 통해 토큰을 가져올 수 있지만 지금은 SDK에서 제공되는 토큰을 사용합니다.

**FILL-IN-HERE**를 Azure Active Directory 웹 응용 프로그램의 실제 값으로 바꿉니다.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>2단계: Azure Data Lake Store 클라이언트(ADLStoreClient) 개체 만들기
[ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) 개체를 만들면 마지막 단계에서 생성된 Data Lake Store 계정 이름 및 토큰 공급자를 지정해야 합니다. Data Lake Store 계정 이름은 정규화된 도메인 이름이어야 합니다. 예를 들어 **FILL-IN-HERE**를 **mydatalakestore.azuredatalakestore.net**과 같은 이름으로 바꿉니다.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>3단계: ADLStoreClient를 사용하여 파일 및 디렉터리 작업 수행
아래 코드에는 몇 가지 일반적인 작업의 예제 코드 조각이 포함되어 있습니다. 다른 작업을 확인하려면 **ADLStoreClient** 개체의 전체 [Data Lake Store Java SDK API 문서](https://azure.github.io/azure-data-lake-store-java/javadoc/) 개체를 살펴볼 수 있습니다.

표준 Java 스트림을 사용하여 파일을 읽고 작성합니다. 즉, Data Lake Store 스트림 기반으로 하는 Java 스트림 계층화하여 표준 Java 기능(예: 서식이 지정된 출력의 인쇄 스트림 또는 추가 기능에 대한 압축 또는 암호화 스트림)을 활용할 수 있습니다.

     // create file and write some content
     String filename = "/a/b/c.txt";
     OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
     PrintStream out = new PrintStream(stream);
     for (int i = 1; i <= 10; i++) {
         out.println("This is line #" + i);
         out.format("This is the same line (%d), but using formatted output. %n", i);
     }
     out.close();
    
    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>4단계: 응용 프로그램 빌드 및 실행
1. IDE 내에서 실행하려면 **실행** 단추를 찾아 누릅니다. Maven을 실행하려면 [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html)을 사용합니다.
2. 명령줄 빌드에서 실행할 수 있는 독립 실행형 jar을 생성하려면 [Maven 어셈블리 플러그 인](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html)을 사용하여 포함된 모든 종속성을 가진 jar을 빌드합니다. [GitHub의 원본 코드 예제](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml)에서 pom.xml에는 이 작업을 수행하는 예제가 있습니다.

## <a name="next-steps"></a>다음 단계
* [Java SDK에 대한 JavaDoc 탐색](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
* [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)


