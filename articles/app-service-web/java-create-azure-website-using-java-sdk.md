<properties 
	pageTitle="Java용 Azure SDK를 사용하여 Azure 앱 서비스에서 웹 앱 만들기" 
	description="Java 용 Azure SDK를 사용하여 프로그래밍 방식으로 Azure 앱 서비스에 웹 앱을 만드는 방법에 대해 알아봅니다." 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter="Java" 
	authors="donntrenton" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="v-donntr"/>


# Java용 Azure SDK를 사용하여 Azure 앱 서비스에서 웹 앱 만들기

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## 개요

이 연습에서는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)에서 웹 앱을 만드는 Java 응용 프로그램용 Azure SDK를 만드는 방법을 나타낸 다음 응용 프로그램을 배포합니다. 두 부분으로 구성됩니다.

- 1부에서는 웹 앱을 만드는 Java 응용 프로그램을 구축하는 방법을 보여줍니다.
- 2부에서는 간단한 "Hello World" 앱을 만드는 방법을 보여준 다음, FTP 클라이언트를 사용하여 코드를 앱 서비스에 배포합니다.


## 필수 조건

### 소프트웨어 설치

이 문서의 AzureWebDemo 응용 프로그램 코드는 Azure Java SDK 0.7.0를 사용하여 작성되며, [웹 플랫폼 설치 관리자 (WebPI)](http://go.microsoft.com/fwlink/?LinkID=252838)를 사용하여 설치할 수 있습니다. 또한, 최신 버전의 [Eclipse용 Azure Toolkit](https://msdn.microsoft.com/library/azure/hh690946.aspx)을 사용해야 합니다. SDK를 설치한 후, **Maven 리포지토리**에서 **인덱스 업데이트**를 실행하여 Eclipse 프로젝트에서 종속성을 업데이트한 다음 **종속성** 창에서 각 패키지의 최신 버전을 다시 추가합니다. **도움말 > 설치 세부 정보**를 클릭하여 Eclipse에 설치된 소프트웨어의 버전을 확인할 수 있습니다. 다음 버전 이상이 있어야 합니다.

- Java용 Microsoft Azure 라이브러리 패키지 0.7.0.20150309
- Java EE Developers용 Eclipse IDE 4.4.2.20150219


### Azure에서 클라우드 리소스 만들기 및 구성

이 절차를 시작하기 전에 활성 Azure를 구독하고 Azure에서 기본 AD(Active Directory)를 설정해야 합니다.


### Azure에 Active Directory(AD) 만들기

Azure 구독에 Active Directory(AD)가 없는 경우, Microsoft 계정과 함께 [Azure 클래식 포털](https://manage.windowsazure.com)로 로그인합니다. 다중 구독인 경우, **구독**을 클릭하고 이 프로젝트에 대해 사용 하려는 구독에 대한 기본 디렉터리를 선택합니다. **적용**을 클릭하여 해당 구독 뷰로 전환합니다.

1. 왼쪽 창에서 **Active Directory**를 선택합니다. **새로 만들기 > 디렉터리 > 사용자 지정 만들기를 클릭합니다**.

2. **디렉터리 추가**에서 **새 디렉터리 만들기**를 선택합니다.

3. **이름**에 디렉터리 이름을 입력합니다.

4. **도메인**에 도메인 이름을 입력합니다. 기본적으로 디렉터리와 함께 포함된 기본 도메인 이름입니다. `<domain_name>.onmicrosoft.com` 형식이 있습니다. 소유하는 다른 도메인 이름 또는 디렉터리 이름에 따라 이름을 지정할 수 있습니다. 나중에, 조직에서 이미 사용하는 다른 도메인 이름을 추가할 수 있습니다.

5. **국가 또는 지역**에서 사용자 로캘을 선택합니다.

AD에 대한 자세한 내용은 [Azure AD 디렉터리란?](http://technet.microsoft.com/library/jj573650.aspx)을 참조하세요.


### Azure용 관리 인증서 만들기

Java용 Azure SDK는 관리 인증서를 사용하여 Azure 구독으로 인증합니다. 서비스 관리 API를 사용하여 구독 리소스를 관리하는 구독 소유자를 대신하여 역할하는 클라이언트 응용 프로그램을 인증하는데 사용하는 X.509 v3 인증서입니다.

이 절차의 코드는 자체 서명된 인증서를 사용하여 Azure와 인증합니다. 이 절차는 인증서를 만들고 미리 [Azure 클래식 포털](https://manage.windowsazure.com)로 업로드해야 합니다. 다음 단계를 포함합니다.

- 클라이언트 인증서를 나타내는 PFX 파일을 생성하고 로컬로 저장합니다.
- PFX 파일에서 관리 인증서(CER 파일)를 생성합니다.
- CER 파일을 Azure 구독에 업로드합니다.
- Java가 해당 형식을 사용하여 인증서를 인증하기 때문에 PFX 파일을 JKS로 변환합니다.
- 로컬 JKS 파일을 참조하는 응용 프로그램의 인증 코드를 작성합니다.

이 절차를 완료하면 CER 인증서는 Azure 구독에 상주하며 JKS 인증서는 로컬 드라이브에 상주합니다. 관리 인증서에 대한 자세한 내용은 [Azure용 관리 인증서 만들기 및 업로드](http://msdn.microsoft.com/library/azure/gg551722.aspx)를 참조하세요.


#### 인증서 만들기

자체 서명된 인증서를 만들려면, 운영 체제에서 명령 콘솔을 열고 다음 명령을 실행합니다.

> **참고:** 이 명령을 실행하는 컴퓨터에 JDK가 설치되어 있어야 합니다. 또한 keytool 경로는 JDK를 설치한 위치에 따라 달라집니다. 자세한 내용은 Java 온라인 문서의 [키 및 인증서 관리 도구(keytool)](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html)를 참조하세요.

.Pfx 파일을 만들려면:

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

.cer 파일을 만들려면:

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

설명:

- `<java-install-dir>` Java를 설치한 디렉터리의 경로입니다.
- `<keystore-id>` 키 저장소 항목 ID입니다(예: `AzureRemoteAccess`).
- `<cert-store-dir>` 인증서를 저장할 디렉터리의 경로입니다(예: `C:/Certificates`).
- `<cert-file-name>` 인증서의 이름입니다(예: `AzureWebDemoCert`).
- `<password>` 인증서를 보호하기 위해 선택하는 암호입니다. 최소 6자여야 합니다. 권장하지는 않지만 암호 없이 입력할 수 있습니다.
- `<dname>` 별칭과 연관되는 X.500 고유 이름이며 자체 서명된 인증서의 발급자 및 주제 필드로 사용됩니다.

자세한 내용은 [Azure 용 관리 인증서 만들기 및 업로드](http://msdn.microsoft.com/library/azure/gg551722.aspx)를 참조하세요.


#### 인증서 업로드

자체 서명된 인증서를 Azure에 업로드하려면, 클래식 포털의 **설정** 페이지로 이동한 다음 **관리 인증서** 탭을 클릭합니다. 페이지의 맨 아래에서 **업로드**를 클릭하고 사용자가 만든 CER 파일의 위치로 이동합니다.


#### PFX 파일을 JKS로 변환

Windows 명령 프롬프트에서(관리자로 실행), cd를 사용하여 인증서를 포함한 디렉토리로 이동하고 다음 명령을 실행합니다. 여기서 `<java-install-dir>`은 사용자 컴퓨터에 설치된 디렉터리입니다.

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. 메시지가 표시되면 대상 키 저장소 암호를 입력합니다. 이 암호는 JKS 파일의 암호입니다.

2. 메시지가 표시되면 원본 키 저장소 암호를 입력합니다. 이 암호는 PFX 파일에 대 해 지정된 암호입니다.

두 암호가 동일하지 않아도 됩니다. 권장하지는 않지만 암호 없이 입력할 수 있습니다.


## 웹 앱 만들기 응용 프로그램 빌드

### Eclipse 작업 영역 및 Maven 프로젝트 만들기

이 섹션에서는 AzureWebDemo라는 웹 앱 만들기 응용 프로그램을 위한 Maven 프로젝트 및 작업 영역을 만듭니다.

1. 새 Maven 프로젝트를 만듭니다. **파일 > 새로 만들기 > Maven 프로젝트**를 클릭합니다. **새 Maven 프로젝트**에서 **간단한 프로젝트 만들기** 및 **기본 작업 영역 위치 사용**을 선택합니다.

2. **새 Maven 프로젝트**의 두 번째 페이지에서 다음을 지정합니다.

    - 그룹 ID: `com.<username>.azure.webdemo`
    - 아티팩트 ID: AzureWebDemo
    - 버전: 0.0.1-SNAPSHOT
    - 패키징: jar
    - 이름: AzureWebDemo

    **마침**을 클릭합니다.

3. 프로젝트 탐색기에서 새 프로젝트의 pom.xml 파일을 엽니다. **종속성** 탭을 선택합니다. 새 프로젝트이면, 아직 패키지가 표시되지 않습니다.

4. Maven 리포지토리 보기를 엽니다. **창 > 보기 표시 > 기타 > Maven > Maven 리포지토리**를 클릭하고 **확인**을 클릭합니다. **Maven 리포지토리** 보기는 IDE의 아래쪽에 표시됩니다.

5. **전역 리포지토리**를 열고 **중앙** 리포지토리를 마우스 오른쪽 단추로 클릭한 다음 **인덱스 다시 작성**을 선택합니다.

    ![][1]
    
    이 단계는 사용자의 연결 속도에 따라 몇 분정도 걸릴 수 있습니다. 인덱스를 다시 빌드하면, **중앙** Maven 저장소에 Microsoft Azure 패키지가 나타납니다.

6. **종속성**에서 **추가**를 클릭합니다. **그룹 ID 입력...**에 `azure-management`를 입력합니다. 기본 관리 및 앱 서비스 웹 앱 관리에 대한 패키지를 선택합니다.

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **참고:** 새 버전 릴리스 후 종속성을 업데이트하면, 이 목록에서 각 종속성을 다시 추가해야 합니다. **추가**를 클릭하고 각 종속성을 선택한 후, **종속성** 목록에서 새 버전 번호로 표시됩니다.

**확인**을 클릭합니다. 그러면 Azure 패키지가 **종속성** 목록에 표시됩니다.


### Azure SDK를 호출하여 웹 앱을 만들도록 Java 코드 작성

다음으로, 앱 서비스 웹 앱을 만드는 Java 용 Azure SDK에서 API를 호출하는 코드를 작성합니다.

1. 기본 진입점 코드를 포함하는 Java 클래스를 만듭니다. 프로젝트 탐색기에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **새로 만들기 > 클래스**를 선택합니다.

2. **새 Java 클래스**에서, 클래스의 이름을 `WebCreator`로 지정하고 **public static void main** 확인란을 선택합니다. 선택 항목은 다음과 같이 나타나야 합니다.

    ![][2]

3. **마침**을 클릭합니다. 프로젝트 탐색기에 WebCreator.java 파일이 나타납니다.


### Azure API를 호출하여 앱 서비스 웹 앱 만들기


#### 필요한 가져오기 추가

WebCreator.java에서, 다음 가져오기를 추가합니다. 이 가져오기는 Azure API 사용을 위한 관리 라이브러리의 클래스에 대한 액세스를 제공합니다.

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### 주 진입점 클래스 정의

AzureWebDemo 응용 프로그램의 목적은 웹 서비스 웹 앱을 만드는 것이기 때문에, 이 응용 프로그램의 기본 클래스에 대한 이름을 `WebAppCreator`로 지정합니다. 이 클래스는 Azure 서비스 관리 API를 호출하여 웹 앱을 만드는 주 진입점 코드를 제공합니다.

웹 앱 및 웹 공간에 대한 다음 매개 변수 정의를 추가합니다. 사용자 고유의 Azure 구독 ID 및 인증서 정보를 제공해야 합니다.

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

설명:

- `<subscription-id>` 리소스를 만들려는 Azure 구독 ID입니다.
- `<certificate-store-path>` 로컬 인증서 저장소 디렉터리의 JKS 파일에 대한 경로 파일 이름입니다. 예를 들어, Linux에 대해서는 `C:/Certificates/CertificateName.jks`이며 Windows에 대해서는 `C:\Certificates\CertificateName.jks`입니다.
- `<certificate-password>` JKS 인증서를 만들 때 지정한 암호입니다.
- `webAppName` 선택한 모든 이름일 수 있습니다. 이 프로시저는 이름 `WebDemoWebApp`을 사용합니다. 전체 도메인 이름은 `domainName`이 추가된 `webAppName`으로 이 경우 전체 도메인은 `webdemowebapp.azurewebsites.net`입니다.
- `domainName` 위와 같이 지정해야 합니다.
- `webSpaceName` [WebSpaceNames](http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html) 클래스에 정의된 값 중 하나여야 합니다.
- `appServicePlanName` 위와 같이 지정해야 합니다.

> **참고:** 이 응용 프로그램을 실행할 때마다, 응용 프로그램을 다시 실행하기 전에 `webAppName` 및 `appServicePlanName`의 값을 변경해야 합니다(또는 Azure 포털에서 웹 앱 삭제). 그렇지 않은 경우, 동일한 리소스가 Azure에 이미 존재하기 때문에 실행이 실패합니다.


#### 웹 만들기 메서드 정의

다음으로, 웹 앱을 만드는 메서드를 정의 합니다. 이 메소드, `createWebApp`는 웹 앱 및 웹 공간의 매개 변수를 지정합니다. 웹 서비스 웹 앱 관리 클라이언트도 만들고 구성하며, [WebSiteManagementClient](http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/WebSiteManagementClient.html) 개체로 정의됩니다. 관리 클라이언트는 웹 앱을 작성하는 키입니다. 서비스 관리 API를 호출하여 응용 프로그램이 웹 앱(만들기, 업데이트 및 삭제와 같은 작업 수행)을 관리할 수 있는 RESTful 웹 서비스를 제공합니다.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

코드는 성공 또는 실패를 나타내는 응답의 HTTP 상태를 출력하고 성공하는 경우작성한 웹 앱의 이름을 출력합니다.


#### Main () 메서드 정의

createWebApp()을 호출하여 웹 앱을 만드는 main () 메서드 코드를 제공합니다.

마지막으로 `main`에서 `createWebApp`을 호출합니다.

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### 응용 프로그램을 실행하고 웹 앱 작성을 확인합니다.

응용 프로그램이 실행되는 지를 확인하려면 **실행 > 실행**을 클릭합니다. 응용 프로그램 실행이 완료되면, Eclipse 콘솔에서 다음 출력이 표시되어야 합니다.

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Azure 클래식 포털에 로그인하고 **웹 앱**을 클릭합니다. 새 웹 앱은 몇분 내에 웹 앱 목록에 나타나야 합니다.


## 웹 앱에 응용 프로그램 배포

AzureWebDemo를 실행하고 새 웹 앱을 만든 후 클래식 포털로 로그인하고 **웹 앱**을 클릭하고 **웹 앱** 목록에서 **WebDemoWebApp**을 선택합니다. 웹 앱의 대시보드 페이지에서 **찾아보기**(또는 URL, `webdemowebapp.azurewebsites.net`을 클릭하여) 이동합니다. 아직 웹 앱에 게시된 내용이 없기 때문에 빈 자리 표시자 페이지가 나타납니다.

다음으로, "Hello World" 응용 프로그램을 만들고 웹 앱에 배포합니다.


### JSP Hello World 응용 프로그램 만들기

#### 응용 프로그램 만들기

웹 응용 프로그램을 웹에 배포하는 방법을 보여주려면, 다음 절차에서는 간단한 "Hello World" Java 응용 프로그램을 만들고 응용 프로그램에서 만든 웹 서비스 웹 앱에 업로드 하는 방법을 나타냅니다.

1. **파일 > 새로 만들기 > 동적 웹 프로젝트**를 클릭합니다. 이름을 `JSPHello`로 지정합니다. 이 대화 상자에서 다른 설정을 변경할 필요가 없습니다. **마침**을 클릭합니다.

    ![][3]

2. 프로젝트 탐색기에서 **JSPHello** 프로젝트를 확장하고 **WebContent**를 마우스 오른쪽 단추로 클릭한 다음 **새로 만들기 > JSP 파일**을 클릭합니다. 새 JSP 파일 대화 상자에서 새 파일의 이름을 `index.jsp`로 지정합니다. **다음**을 클릭합니다.

3. **Select JSP Template** 대화 상자에서 **New JSP File (html)**을 선택하고 **Finish**를 클릭합니다.

4. Index.jsp에서, `<head>` 및 `<body>` 섹션 태그에서 다음 코드를 추가합니다.

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### localhost에서 Hello World 응용 프로그램 실행

이 응용 프로그램을 실행하기 전에 몇가지 속성을 구성해야 합니다.

1.  **JSPHello** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

2. **속성** 대화 상자에서 **Java 빌드 경로**를 선택하고 **주문 및 내보내기** 탭을 선택하고 **JRE 시스템 라이브러리**를 확인한 다음 **위로**를 클릭하여 목록의 위쪽으로 이동합니다.

    ![][4]

3. 또한 **속성** 대화 상자에서 **대상으로 지정된 런타임**을 선택하고 **새로 만들기**를 클릭합니다.

4. **새 서버 런타임 환경** 대화 상자에서 **Apache Tomcat v7.0**과 같은 서버를 선택하고 **다음**을 클릭합니다. **Tomcat 서버** 대화 상자에서 **이름**을 `Apache Tomcat v7.0`로 설정하고 **Tomcat 설치 디렉터리**를 사용하려는 Tomcat 서버의 버전을 설치한 디렉터리로 설정합니다.

    ![][5]

    **마침**을 클릭합니다.

5. 그러면 **속성** 대화 상자의 **대상으로 지정된 런타임**으로 돌아갑니다. **Apache Tomcat v7.0**을 선택한 다음 **확인**을 클릭합니다.

    ![][6]

6. Eclipse **실행** 메뉴에서 **실행**을 클릭합니다. **다음으로 실행** 대화 상자에서 **서버에서 실행**을 선택합니다. **서버에서 실행** 대화 상자에서 **Tomcat v7.0 서버**를 선택합니다.

    ![][7]

    **마침**을 클릭합니다.

7. 응용 프로그램을 실행하면 다음 메시지를 표시하는 Eclipse(`http://localhost:8080/JSPHello/`)의 localhost 창에 **JSPHello** 페이지가 표시되어야 합니다.

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### WAR로 응용 프로그램 내보내기

웹 앱을 배포할 수 있도록 웹 보관 파일 (WAR) 파일로 웹 프로젝트 파일을 내보냅니다. 다음 웹 프로젝트 파일은 WebContent 폴더에 저장합니다.

    META-INF
    WEB-INF
    index.jsp

1. WebContent 폴더를 마우스 오른쪽 단추로 클릭하고 **내보내기**를 선택합니다.

2. **선택 내보내기** 대화 상자에서 **웹 > WAR** 파일을 클릭한 후 **다음**을 클릭합니다.

3. **WAR 내보내기** 대화 상자에서 현재 프로젝트에서 src 디렉터리를 선택하고 끝에 WAR 파일의 이름을 포함합니다. 예:

    `<project-path>/JSPHello/src/JSPHello.war`

WAR 파일의 배포에 대한 자세한 내용은 [Java 응용 프로그램을 Azure 앱 서비스 웹 앱에 추가](web-sites-java-add-app.md)를 참조하세요.


### FTP를 사용하여 Hello World 응용 프로그램 배포

응용 프로그램을 게시할 타사 FTP 클라이언트를 선택합니다. 이 절차에서는 Azure로 빌드된 Kudu 콘솔 및 편리한 그래픽 UI의 널리 사용되는 도구인 FileZilla의 두 옵션을 설명합니다.

> **참고:** Java 2.4를 포함한 Eclipse용 Azure 플러그인은 저장소 계정 및 클라우드 서비스에 대한 배포를 지원하지만, 현재는 웹 앱에 대한 배포를 지원하지 않습니다. [Eclipse에서 Azure용 Hello World 응용 프로그램을 만들기](http://msdn.microsoft.com/library/azure/hh690944.aspx)에서 설명한 대로 Azure 배포 프로젝트를 사용하여 저장소 계정 및 클라우드 서비스를 배포할 수 있지만 웹 앱에는 배포하지 않습니다. FTP 또는 GitHub와 같은 다른 메서드를 사용하여 웹 앱에 파일을 전송합니다.

> **참고:** Windows 명령 프롬프트(Windows와 함께 제공되는 명령줄 FTP.EXE 유틸리티)에서 FTP를 사용하는 것은 권장하지 않습니다. FTP.EXE와 같은 활성 FTP를 사용하는 FTP 클라이언트는 방화벽을 통해 작동하는 데 자주 실패합니다. 활성 FTP는 FTP 서버가 연결에 실패할 가능성이 있는 내부 LAN 기반 주소를 지정합니다.

FTP를 사용하여 웹 서비스 웹 앱에 배포에 대한 자세한 내용은 다음 항목을 참조하세요.

- [Azure 포털을 사용하여 웹 앱 관리: 배포](web-sites-manage.md/#deployment)
- [FTP 유틸리티를 사용하여 배포](web-sites-deploy.md)


#### 배포 자격 증명 설정

**AzureWebDemo** 응용 프로그램을 실행하여 웹 앱을 만드는지 확인합니다. 이 위치에 파일을 전송합니다.

1. 클래식 포털에 로그인하고 **웹 앱**을 클릭합니다. **WebDemoWebApp**은 웹 앱 목록에 표시되며 실행 중인지 확인하세요. **WebDemoWebApp**을 클릭하여 해당 **대시보드** 페이지를 엽니다.

2. **대시보드** 페이지의 **빠른 보기**에서, **배포 자격 증명 설정**(배포 자격 증명이 이미 있는 경우 **배포 자격 증명 재설정**을 읽음)을 클릭합니다.

    배포 자격 증명은 Microsoft 계정과 연결됩니다. Git 및 FTP를 사용하여 배포하는 데 사용할 수 있는 사용자 이름 및 암호를 지정해야 합니다. 이러한 자격 증명을 사용하여 Microsoft 계정에 연결된 모든 Azure 구독에서 모든 웹 앱에 배포할 수 있습니다. 대화 상자에서 Git 및 FTP 배포 자격 증명을 제공하고 나중에 사용할 사용자 이름 및 암호를 기록합니다.


#### FTP 연결 정보 가져오기

FTP를 사용하여 새로 만든된 웹 앱에 응용 프로그램 파일을 배포하려면 연결 정보를 가져와야 합니다. 두 가지 방법으로 연결 정보를 얻을 수 있습니다. 한 가지 방법은 웹 앱의 **대시보드** 페이지를 방문하는 것이고, 다른 방법은 웹 앱의 게시 프로필을 다운로드하는 것입니다. 게시 프로필은 Azure 앱 서비스에서 웹 앱에 대한 FTP 호스트 이름 및 로그온 자격 증명과 같은 정보를 제공하는 XML 파일입니다. 이 사용자 이름 및 암호를 사용하여 Azure 계정과 연결된 모든 구독에서 하나가 아닌 모든 웹 앱에 배포할 수 있습니다.

[Azure 포털](https://portal.azure.com)의 웹 앱의 블레이드에서 FTP 연결 정보를 가져오려면:

1. **Essentials**에서 **FTP 호스트 이름**을 찾아 복사합니다. `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`와 유사한 URI입니다.

2. **Essentials**에서 **FTP/Deployment username**을 찾아 복사합니다. *webappname\\deployment-username*의 형식이 됩니다(예: `WebDemoWebApp\deployer77`).

게시 프로필에서 FTP 연결 정보를 얻으려면:

1. 웹 앱의 블레이드에서 **게시 프로필 가져오기**를 클릭합니다. .Publishsettings 파일을 로컬 드라이브에 다운로드합니다.

2. XML 편집기나 텍스트 편집기에서.publishsettings 파일을 열고 `publishMethod="FTP"`을 포함한 `<publishProfile>` 요소를 찾습니다. 다음과 같아야 합니다.

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. 다음과 같이 웹 앱의 `publishProfile` 설정은 FileZilla 사이트 관리자 설정으로 매핑됩니다.

- `publishUrl`은 **FTP 호스트 이름**과 같으며 **호스트**에서 설정한 값입니다.
- `publishMethod="FTP"`은 **프로토콜**을 **FTP-파일 전송 프로토콜**로 설정되며 **암호화**를 **일반 FTP 사용**으로 설정함을 의미합니다.
- `userName` 및 `userPWD`는 배포 자격 증명을 재설정할 때 지정된 실제 사용자 이름 및 암호 값이며 `userName`은 **배포 / FTP 사용자**과 동일합니다. FileZilla에서 **사용자** 및 **암호**로 매핑됩니다.
- `ftpPassiveMode="True"`은 FTP 사이트가 수동 FTP 전송을 사용함을 의미합니다. **전송 설정** 탭에서 **수동**을 선택합니다.


#### Java 응용 프로그램을 호스트하도록 웹 앱 구성

응용 프로그램을 게시하기 전에, 웹 앱이 Java 응용 프로그램을 호스팅할 수 있도록 몇가지 구성 설정을 변경해야 합니다.

1. 클래식 포털에서 웹 앱의 **대시보드** 페이지로 이동하고 **구성**을 클릭합니다. **구성** 페이지에서 다음 설정을 지정합니다.

2. **Java 버전**에서 기본값은 **Off**입니다. Java 버전을 응용 프로그램 대상에서 선택합니다(예: 1.7.0_51). 이 작업을 수행한 후, **웹 컨테이너**가 Tomcat 서버 버전으로 설정되는지도 확인합니다.

3. **기본 문서**에서, index.jsp를 추가하고 위쪽 목록으로 이동합니다. (웹 앱에 대한 기본 파일은 hostingstart.html입니다.)

4. **Save**를 클릭합니다.


#### Kudu를 사용하여 응용 프로그램 게시

응용 프로그램을 게시하는 한 가지 방법은 Azure에 기본 제공되는 Kudu 디버그 콘솔을 사용하는 것입니다. kudu는 안정적이며 웹 서비스 웹 앱 및 Tomcat 서버와 일치된다고 알려져 있습니다. 다음 형식의 URL로 이동하여 웹 앱에 대한 콘솔에 액세스할 수 있습니다.

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. 이 절차의 경우, Kudu 콘솔은 다음 URL에 있습니다. 이 위치를 찾습니다.

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. 최상위 메뉴에서 **디버그 콘솔 > CMD**를 선택합니다.

3. 콘솔 명령줄에서 `/site/wwwroot`로 이동합니다(페이지 위쪽의 디렉터리 보기에서 `site`, `wwwroot`을 차례로 클릭).

    `cd /site/wwwroot`

4. **Java 버전**을 지정한 후, Tomcat 서버는 webapps 디렉터리를 만들어야 합니다. 콘솔 명령줄에서 webapps 디렉터리로 이동합니다.

    `mkdir webapps`

    `cd webapps`

5. `<project-path>/JSPHello/src/`에서 JSPHello.war를 끌어 `/site/wwwroot/webapps` 아래 Kudu 디렉토리로 놓습니다. Tomcat이 압축을 풀기 때문에 "여기로 끌어서 업로드하고 압축" 영역으로 끌어놓지 마십시오.

  ![][8]

처음에 JSPHello.war는 단독으로 디렉터리 영역에 나타납니다.

  ![][9]

짧은 시간(약 5분 미만)에 Tomcat 서버는 JSPHello 압축을 해제한 디렉터리로 WAR 파일의 압축을 해제합니다. ROOT 디렉터리를 클릭하여 Index.jsp의 압축을 해제하고 복사하는 지 확인합니다. 그런 경우, 압축이 해제된 JSPHello 디렉터리가 만들어졌는지 여부를 볼 수 있는 webapps 디렉터리로 다시 이동합니다. 이 항목이 표시되지 않으면 대기하고 반복합니다.

  ![][10]


#### FileZilla를 사용하여 응용 프로그램 게시(선택 사항)

응용 프로그램을 게시하는데 사용할 수 있는 다른 도구는 FileZilla로, 편리한 그래픽 UI가 있는 타사 FTP 클라이언트입니다. 없는 경우, [http://filezilla-project.org/](http://filezilla-project.org/)에서 FileZilla를 다운로드하고 설치할 수 있습니다. 클라이언트 사용에 대한 자세한 내용은 [FileZilla 설명서](https://wiki.filezilla-project.org/Documentation) 및 [FTP 클라이언트-파트 4: FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx)의 이 블로그 항목을 참조하세요.

1. FileZilla에서 **파일 > 사이트 관리자**를 클릭합니다.
2. **사이트 관리자** 대화 상자에서 **새 사이트**를 클릭합니다. 비어 있는 새 FTP 사이트는 이름을 입력하라는 메시지를 표시하는 **항목 선택**에 나타납니다. 이 절차에 대한 이름을 `AzureWebDemo-FTP`로 지정합니다.

    **일반** 탭에서 다음 설정을 지정합니다.
    - **호스트:** 대시보드에서 복사한 **FTP 호스트 이름**을 입력합니다. 
    - **포트:** (공백으로 둠, 수동 전송이며 서버는 사용할 포트를 결정함)-
    - **프로토콜:** FTP 파일 전송 프로토콜
    - **암호화:** 일반 FTP 사용
    - **로그온 유형:** 보통
    - **사용자:** 대시보드에서 복사한 배포 / FTP 사용자를 입력합니다. 전체 FTP 사용자 이름으로, *webappname\\username* 형식입니다.- 
    - **암호:** 배포 자격 증명을 설정하는 경우 지정된 암호를 입력합니다.

    **전송 설정** 탭에서 **수동**을 선택합니다.

3. **Connect**를 클릭합니다. 성공하는 경우, FileZilla의 콘솔은 `Status: Connected` 메시지를 표시하며 `LIST` 명령을 실행하여 디렉터리 내용을 표시합니다.

4. **로컬** 사이트 패널에서, JSPHello.war 파일이 상주하는 원본 디렉터리를 선택합니다. 경로는 다음과 유사하게 됩니다.

    `<project-path>/JSPHello/src/`

5. **원격** 사이트 패널에서 대상 폴더를 선택합니다. WAR 앱 루트 아래 `webapps` 디렉토리로 WAR 파일을 배포합니다. `/site/wwwroot`으로 이동하고 `wwwroot`를 마우스 오른쪽 단추로 클릭하고 **디렉터리 만들기**를 선택합니다. 디렉터리의 이름을 `webapps`로 지정하고 해당 디렉터리를 입력합니다.

6. JSPHello.war을 `/site/wwwroot/webapps`에 전송합니다. **로컬** 파일 목록에서 JSPHello.war를 선택하고 마우스 오른쪽 단추로 클릭하고 **업로드**를 선택합니다. `/site/wwwroot/webapps`에 보이도록 표시 되어야 합니다.

7. webapps 디렉터리로 JSPHello.war를 복사한 후, Tomcat 서버는 자동으로 파일을 WAR 파일로 압축 해제합니다. Tomcat 서버가 거의 즉시 압축 해제를 시작하더라도, 파일이 FTP 클라이언트에 표시되기 까지는 긴 시간(몇 시간)이 걸릴 수 있습니다.


#### 웹 앱에서 Hello World 응용 프로그램 실행

1. WAR 파일을 업로드하고 Tomcat 서버가 압축을 해제한 `JSPHello` 디렉터리를 만든 후, `http://webdemowebapp.azurewebsites.net/JSPHello`로 이동하여 응용 프로그램을 실행합니다.

    > **참고:** 클래식 포털에서 **찾아보기**를 클릭하면, "이 Java 기반 웹 응용 프로그램이 성공적으로 만들었습니다." 라는 기본 웹 페이지를 만들 수 있습니다. 기본 웹 페이지 대신 응용 프로그램 출력을 보려면 웹 페이지를 새로 고쳐야 할 수 있습니다.

2. 응용 프로그램이 실행되면 다음 출력이 있는 웹 페이지가 표시되어야 합니다.

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### Azure 리소스 정리

이 절차에서는 앱 서비스 웹 앱을 만듭니다. 존재하는 한 리소스에 대해 청구됩니다. 테스트나 개발에 웹 앱을 계속 사용하지 않으려면, 중지 또는 삭제를 고려해야 합니다. 중지된 웹 앱은 여전히 작은 요금을 부과하지만 언제든지 다시 시작할 수 있습니다. 웹 앱을 삭제하면 업로드한 데이터를 모두 지웁니다.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

[1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
[2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
[3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
[4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
[5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
[6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
[7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
[8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
[9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
[10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png

<!--HONumber=54--> 