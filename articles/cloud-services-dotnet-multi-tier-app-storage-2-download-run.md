<properties linkid="develop-net-tutorials-multi-tier-web-site-2-download-and-run" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="riande,tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande,tdykstra"></tags>

# Azure 전자 메일 서비스 응용 프로그램 구성 및 배포 - 2/5

Azure 전자 메일 서비스 샘플 응용 프로그램을 빌드하고 배포하는 방법을 설명하는 5개 시리즈 중 두 번째 자습서입니다. 이 응용 프로그램 및 자습서 시리즈에 대한 자세한 내용은 [시리즈의 첫 번째 자습서][]를 참조하세요.

이 자습서에서는 다음에 대해 알아봅니다.

-   Azure SDK를 설치하여 개발자 컴퓨터에서 Azure를 개발할 수 있도록 설정하는 방법
-   로컬 컴퓨터에서 Azure 전자 메일 서비스 응용 프로그램을 구성하고 테스트하는 방법
-   응용 프로그램을 Azure에 게시하는 방법
-   Visual Studio 서버 탐색기를 사용하여 Azure 테이블, 큐 및 Blob을 보고 편집하는 방법
-   추적을 구성하고 추적 데이터를 보는 방법
-   작업자 역할 인스턴스 수를 늘려 응용 프로그램을 확장하는 방법

### 자습서 세그먼트

-   [개발 환경 설정][]
-   [완료된 솔루션 다운로드 및 실행][]
-   [Visual Studio에서 개발자 저장소 보기][]
-   [Azure 저장소 계정 만들기][]
-   [클라우드 서비스 만들기][]
-   [Azure 저장소에 맞게 응용 프로그램 구성][]
-   [SendGrid를 사용하도록 응용 프로그램 구성][]
-   [Azure에 응용 프로그램 배포][]
-   [스테이징에서 프로덕션으로 응용 프로그램 수준 올리기][]
-   [추적 데이터 구성 및 보기][]
-   [증가한 로드를 처리하도록 다른 작업자 역할 인스턴스 추가][]

[WACOM.INCLUDE [install-sdk-2013-only][]]

## <a name="downloadcnfg"></a><span class="short-header">다운로드 및 실행</span>완료된 솔루션 다운로드 및 실행

1.  [완료된 솔루션][]을 다운로드하고 압축 해제합니다.

2.  Visual Studio를 시작합니다.

3.  **파일** 메뉴에서 **프로젝트 열기**를 선택하고 솔루션을 다운로드한 위치로 이동한 후 솔루션 파일을 엽니다.

4.  Ctrl+Shift+B를 눌러 솔루션을 빌드합니다.

    기본적으로 Visual Studio는 *.zip* 파일에 포함되지 않은 NuGet 패키지 콘텐츠를 자동으로 복원합니다. 패키지가 복원되지 않는 경우 **솔루션의 NuGet 패키지 관리** 대화 상자로 이동하고 오른쪽 위에서 **복원** 단추를 클릭하여 수동으로 설치합니다.

5.  **솔루션 탐색기**에서 시작 프로젝트로 **AzureEmailService**가 선택되었는지 확인합니다.

6.  Ctrl+F5를 눌러 응용 프로그램을 실행합니다.

    응용 프로그램 홈페이지가 브라우저에 나타납니다.

7.  메뉴 모음에서 **메일 그룹**을 클릭합니다.

    (스크린샷은 Visual Studio 2012 프로젝트 템플릿의 웹 페이지 스타일이지만 내용은 Visual Studio 2013의 경우와 동일합니다.)

    ![앱을 실행합니다.][]

8.  **새로 만들기**를 클릭합니다.

9.  일부 테스트 데이터를 입력한 후 **만들기**를 클릭합니다.

    ![앱을 실행합니다.][1]

10. 메일 그룹 항목을 몇 가지 더 만듭니다.

    ![메일 그룹 인덱스 페이지][]

11. **Subscribers**를 클릭한 후 일부 구독자를 추가합니다. **확인됨**을 `true`로 설정합니다.

    ![구독자 인덱스 페이지][]

12. 보내려는 전자 메일의 본문이 포함된 *.txt* 파일을 만들어 메시지를 추가하도록 준비합니다. 그런 다음, 동일한 텍스트가 포함되어 있지만 일부 HTML(예; 메시지의 단어 중 하나를 굵은 글꼴이나 기울임꼴로 만들기)이 있는 *.htm* 파일을 만듭니다. 이 파일을 다음 단계에서 사용할 것입니다.

13. **메시지**를 클릭한 후 일부 메시지를 추가합니다. 이전 단계에서 만든 파일을 선택합니다. 기본적으로 향후 일주일 후로 설정되어 있는 예약 날짜를 변경하지 마세요. SendGrid를 구성할 때까지는 응용 프로그램이 메시지를 보낼 수 없습니다.

    ![메시지 작성 페이지][]

    ![메시지 인덱스 페이지][]

    입력하고 본 데이터는 Azure 저장소 에뮬레이터에서 확인 및 관리됩니다. 저장소 에뮬레이터는 SQL Server Express LocalDB 데이터베이스를 사용하여 클라우드에서 Azure 저장소가 작동하는 방식을 에뮬레이트합니다. 프로젝트를 다운로드하면 프로젝트에서 사용하도록 구성된 곳이 저장소 에뮬레이터이기 때문에, 응용 프로그램이 저장소 에뮬레이터를 사용합니다. 이 설정은 **AzureEmailService** 프로젝트의 *.cscfg* 파일에 저장되어 있습니다. *ServiceConfiguration.Local.cscfg* 파일은 로컬에서 Visual Studio에서 응용 프로그램을 실행할 때 사용되는 항목을 결정하고, *ServiceConfiguration.Cloud.cscfg* 파일은 응용 프로그램을 클라우드에 배포할 때 사용되는 항목을 결정합니다. Azure 저장소 계정을 사용하도록 응용 프로그램을 구성하는 방법에 대해 나중에 알아보겠습니다.

14. 브라우저를 닫습니다.

## <a name="StorageExpVS"></a>Visual Studio에서 개발 저장소 보기

**서버 탐색기**의 **Azure 저장소** 브라우저에서 직접 Azure 저장소 리소스를 편리하게 작업할 수 있습니다.

1.  Visual Studio의 **보기** 메뉴에서 **서버 탐색기**를 선택합니다.

2.  **Azure** 노드를 확장하고 **저장소** 노드를 확장한 다음 **Azure 저장소** 노드 아래의 **(Development)** 노드를 확장합니다.

    아직 Visual Studio에서 Azure에 로그인하지 않은 경우 Azure 자격 증명을 요구하는 메시지가 나타납니다. 클라우드 서비스를 실행할 구독이 있는 계정의 자격 증명을 입력합니다.

3.  **테이블**을 확장하여 이전 단계에서 만든 테이블을 표시합니다.

    ![서버 탐색기][]

4.  **MailingList** 테이블을 두 번 클릭합니다.

    ![VS 저장소 탐색기][]

    창의 테이블에서 다른 스키마가 표시되는 것을 볼 수 있습니다. `MailingList` 엔터티에는 `Description` 및 `FromEmailAddress` 속성이 있고 `Subscriber` 엔터티에는 `Verified` 속성이 있습니다(이외에 `SubscriberGUID`도 있지만 이미지 너비가 부족하여 표시되지 않음). 테이블에는 모든 속성에 해당하는 열이 있으며, 특정 테이블 열이 특정 속성을 가지지 않은 엔터티와 관련된 경우 해당 셀은 비어 있습니다.

Azure 저장소 리소스 작업에 사용할 수 있는 다른 도구는 [Azure 저장소 탐색기][](영문)입니다.

## <a name="createWASA"></a>Azure 저장소 계정 만들기

Visual Studio에서 샘플 응용 프로그램을 실행할 때 Azure 저장소 에뮬레이터 또는 클라우드의 Azure 저장소 계정에서 테이블, 큐 및 Blob에 액세스할 수 있습니다. 이 자습서 섹션에서는 Azure 저장소 계정을 만듭니다. 자습서 뒷부분에서 이 계정을 사용하도록 Visual Studio를 구성하게 됩니다.

1.  브라우저에서 [Azure 관리 포털][]을 엽니다.

2.  [Azure 관리 포털][]에서 **저장소**를 클릭한 후 **새로 만들기**를 클릭합니다.

    ![새 저장소][]

1.  **빠른 생성**을 클릭합니다.

    ![빠른 생성][]

1.  URL 입력 상자에 URL 접두사를 입력합니다.

    이 접두사와 상자 아래에 표시되는 텍스트가 저장소 계정의 고유 URL이 됩니다. 입력한 접두사를 이미 누군가가 사용하고 있는 경우 텍스트 상자 위에 "The storage name is already in use"가 표시되며 다른 접두사를 선택해야 합니다.

2.  응용 프로그램을 배포하려는 곳으로 지역을 설정합니다.

3.  **복제** 드롭다운 상자를 **로컬 중복**으로 설정합니다.

    저장소 계정에 대해 지역에서 복제를 사용하는 경우에는 저장된 콘텐츠가 보조 위치에 복제되어 기본 위치에서 주요 재해가 발생하는 경우 보조 위치로 장애 조치(Failover)할 수 있도록 합니다. 지역에서 복제는 추가 비용을 발생시킬 수 있습니다. 테스트 및 개발 계정의 경우 일반적으로 지역에서 복제 비용을 지불하지 않는 것이 좋습니다. 자세한 내용은 [저장소 계정을 관리하는 방법][]을 참조하세요.

4.  **저장소 계정 만들기**를 클릭합니다.

    이미지에서는 `aestest3.core.windows.net` URL을 사용하여 저장소 계정이 생성되었습니다.

    ![URL 접두사를 사용하여 저장소 만들기][]

    이 단계를 완료하는 데는 몇 분 정도 걸릴 수 있습니다. 기다리는 동안 이 단계를 반복하여 프로덕션 저장소 계정을 만들 수 있습니다. 로컬 개발용으로 사용할 테스트 저장소 계정, Azure에서 테스트용으로 사용할 다른 테스트 저장소 계정, 그리고 프로덕션 저장소 계정을 따로 사용하는 것이 편리한 경우가 많습니다.

5.  이전 단계에서 만든 테스트 계정을 클릭한 후 **액세스 키 관리** 아이콘을 클릭합니다.

    ![키 관리][]

    ![키 GUID][]

    저장소 계정을 선택하면 Visual Studio가 이 키 중 하나를 사용하여 자동으로 연결 문자열을 구성합니다. 수동으로 연결 문자열을 업데이트할 수도 있습니다.

    응용 프로그램에 제공되는 서비스를 중단시키지 않고도 사용 중인 키를 주기적으로 변경할 수 있도록 두 가지 키가 있습니다. 사용하고 있지 않은 키를 다시 생성한 후에 다시 생성된 키를 사용하도록 응용 프로그램에서 연결 문자열을 변경할 수 있습니다. 키가 한 개만 있는 경우에는 키를 다시 생성하면 응용 프로그램에서 저장소 계정 연결이 끊어집니다. 이미지에 표시된 키는 이미지 캡처 후에 다시 생성되었기 때문에 더 이상 유효하지 않습니다.

## <a name="createcloudsvc"></a><span class="short-header">클라우드 서비스 만들기</span>클라우드 서비스 만들기

1.  [Azure 관리 포털][]에서 **클라우드 서비스**를 클릭한 후 **새로 만들기** 아이콘을 클릭합니다.

    ![빠른 클라우드][]

2.  **빠른 생성**을 클릭합니다.

3.  URL 입력 상자에 URL 접두사를 입력합니다.

    이 URL은 저장소 URL과 마찬가지로 고유해야 하므로, 선택한 접두사를 이미 누군가가 사용 중인 경우에는 오류 메시지가 나타납니다.

4.  응용 프로그램을 배포하려는 곳으로 지역을 설정합니다.

    저장소 계정을 만든 것과 동일한 지역에서 클라우드 서비스를 만들어야 합니다. 클라우드 서비스와 저장소 계정이 서로 다른 데이터 센터, 즉 서로 다른 지역에 있는 경우 대기 시간이 길어지고 데이터 센터 외부 대역폭에 대한 요금이 발생합니다. 데이터 센터 내부 대역폭은 무료입니다.

    Azure 선호도 그룹은 데이터 센터 리소스 사이의 거리를 최소화하는 메커니즘을 제공하며, 이로 인해 대기 시간이 줄어들 수 있습니다. 이 자습서는 선호도 그룹을 사용하지 않습니다. 자세한 내용은 [Azure에서 선호도 그룹을 만드는 방법][]을 참조하세요.

5.  **클라우드 서비스 만들기**를 클릭합니다.

    다음 이미지에서는 aescloud.cloudapp.net이라는 URL을 사용하여 클라우드 서비스가 생성되었습니다.

    ![URL 접두사를 사용하여 저장소 만들기][2]

## <a name="conf4azureStorage"></a><span class="short-header">저장소 계정 사용</span>Azure 저장소 계정을 사용하도록 응용 프로그램 구성

다음으로, Visual Studio에서 실행될 때 개발 저장소 대신 Azure 저장소 계정을 사용하도록 응용 프로그램을 구성하는 방법을 알아봅니다.

1.  **솔루션 탐색기**에서 **AzureEmailService** 프로젝트의 **역할** 아래에 있는 **MvcWebRole**을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

    ![마우스 오른쪽 단추 클릭 속성][]

2.  **MvcWebRole [Role]** 창에서 **설정** 탭을 클릭합니다.

3.  **서비스 구성** 드롭다운 상자에서 **로컬**을 선택합니다.

4.  **StorageConnectionString** 항목을 선택하면 줄 오른쪽 끝에 줄임표(**...**) 단추가 표시됩니다. 줄임표 단추를 클릭하여 **저장소 계정 연결 문자열** 대화 상자를 엽니다.

    ![마우스 오른쪽 단추 클릭 속성][3]

5.  **저장소 연결 문자열 만들기** 대화 상자에서 **구독**을 클릭한 다음 **구독** 및 저장소 **계정 이름**을 선택합니다.

    ![마우스 오른쪽 단추 클릭 속성][4]

6.  앞서 `StorageConnectionString` 연결 문자열에 사용한 것과 동일한 절차에 따라 `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 연결 문자열을 설정합니다.

7.  두 연결 문자열에 사용한 것과 동일한 절차에 따라 MvcWebRole 역할에서 WorkerRoleA 역할 및 workerRoleB 역할의 연결 문자열을 설정합니다.

8.  **AzureEmailService** 프로젝트에 위치한 **ServiceConfiguration.Local.cscfg** 파일을 엽니다.

    `Role` 요소(`MvcWebRole`)에 Visual Studio UI를 사용하여 업데이트한 설정이 들어 있는 `ConfigurationSettings` 요소를 볼 수 있습니다.

          <Role name="MvcWebRole">
            <Instances count="1" />
            <ConfigurationSettings>
              <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[Key]" />
              <Setting name="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=aestest;AccountKey=[Key]" />
            </ConfigurationSettings>
          </Role>

    두 작업자 역할의 `Role` 요소에 동일한 두 개의 연결 문자열이 표시됩니다.

    Visual Studio **[Role]** 창을 사용하지 않고 바로 이 파일을 편집할 수 있습니다. 구성 파일에 대한 자세한 내용은 [Azure 프로젝트 구성][]을 참조하세요.

### 저장소 계정을 사용하도록 구성된 응용 프로그램 테스트

1.  Ctrl+F5를 눌러 응용 프로그램을 실행합니다. 이 자습서 앞부분에서 한 것처럼 **Mailing Lists**, **Subscribers** 및 **Messages** 링크를 클릭하여 일부 데이터를 입력합니다.

2.  Visual Studio에서 **서버 탐색기**를 엽니다.

3.  **Azure** 노드 아래에 있는 **저장소** 노드를 확장한 다음 응용 프로그램에서 사용하도록 구성한 저장소 계정에 해당하는 노드를 확장합니다.

4.  **테이블**을 확장하고 `MailingList` 테이블을 두 번 클릭하여 응용 프로그램의 **메일 그룹** 및 **구독자** 페이지에서 입력한 데이터를 표시합니다.

### Azure 저장소 에뮬레이터 자동 시작을 사용하지 않도록 설정하는 선택적 단계

저장소 에뮬레이터를 사용하고 있지 않은 경우 Azure 저장소 에뮬레이터의 자동 시작을 사용하지 않도록 설정함으로써 프로젝트 시작 시간을 단축하고 로컬 리소스를 사용량을 줄일 수 있습니다.

1.  **솔루션 탐색기**에서 **AzureEmailService** 클라우드 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **속성**을 선택합니다.

    ![클라우드 프로젝트 속성 선택][]

2.  **개발** 탭을 선택합니다.

3.  **Start Azure storage emulator**를 **False**로 설정합니다.

    ![저장소 에뮬레이터 자동 시작 사용 안 함][]

    **참고**: 저장소 에뮬레이터를 사용하지 않는 경우에는 이 옵션을 false로만 설정해야 합니다.

    이 창에서는 로컬에서 응용 프로그램을 실행할 때 사용되는 **서비스 구성** 파일을 **로컬**에서 **클라우드**로 변경할 수도 있습니다(*ServiceConfiguration.Local.cscfg*에서 *ServiceConfiguration.Cloud.cscfg*로).

4.  Windows 시스템 트레이에서 계산 에뮬레이터 아이콘을 마우스 오른쪽 단추로 클릭하고 **Shutdown Storage Emulator**를 클릭합니다.

    ![ASE][]

## <a name="sendGrid"></a><span class="short-header">SendGrid</span>SendGrid를 사용하도록 응용 프로그램 구성

샘플 응용 프로그램은 SendGrid를 사용하여 전자 메일을 보냅니다. SendGrid를 사용하여 전자 메일을 보내려면 SendGrid 계정을 설정해야 하며 그런 다음 SendGrid 자격 증명으로 구성 파일을 업데이트해야 합니다.

<div class="note"><p><strong>참고:</strong> SendGrid를 사용하지 않으려는 경우나 SendGrid를 사용할 수 없는 경우에는 고유한 전자 메일 서비스를 쉽게 대체할 수 있습니다. SendGrid를 사용하는 코드는 작업자 역할 B에서 두 가지 방법으로 격리됩니다. [자습서 5][tut5]에서는 다른 전자 메일 전송 방법을 구현하기 위해 변경해야 할 사항을 설명합니다. 다른 전자 메일 전송 방법을 구현하려는 경우 이 절차를 건너뛰고 이 자습서를 계속 진행하세요. 응용 프로그램에서 실제 전자 메일 전송을 제외하고 다른 모든 기능(웹 페이지, 전자 메일 예약 등)이 작동합니다.</p></div>

### SendGrid 계정 만들기

1.  [Azure와 함께 SendGrid를 사용하여 전자 메일을 보내는 방법][](영문)의 지침에 따라 무료 계정을 등록합니다.

### 작업자 역할 속성에서 SendGrid 자격 증명 업데이트

자습서 앞부분에서 웹 역할 및 두 개의 작업자 역할에 대해 저장소 계정 자격 증명을 설정할 때 작업자 역할 B에 웹 역할이나 작업 역할 A에는 없는 세 가지 설정이 있음을 발견하셨을 수 있습니다. 이제 동일한 UI를 사용하여 이 세 가지 설정을 구성할 수 있습니다(**서비스 구성** 드롭다운 목록에서 **클라우드** 선택).

다음 단계는 구성 파일을 편집하여 속성을 설정하는 대체 방법을 보여 줍니다.

1.  *ServiceConfiguration.Cloud.cscfg* 파일을 `AzureEmailService` 프로젝트에서 편집하고 이전 단계에서 가져온 SendGrid 사용자 이름 및 암호 값을 이 설정이 포함된 `WorkerRoleB` 요소에 입력합니다. 다음 코드는 WorkerRoleB 요소를 보여 줍니다.

    ![SendGridSettings][]

2.  AzureMailServiceURL 설정도 있습니다. 이 값을 Azure 클라우드 서비스 생성 시 선택한 URL로 설정합니다. 예를 들어 "<http://aescloud.cloudapp.net>"입니다.

클라우드 구성 파일을 업데이트하면 클라우드에서 응용 프로그램을 실행할 때 사용될 설정이 구성됩니다. 응용 프로그램이 로컬에서 실행되는 동안 전자 메일을 보내도록 하려는 경우 *ServiceConfiguration.Local.cscfg* 파일도 업데이트해야 합니다.

## <a name="deployAz"></a><span class="short-header">Azure에 배포</span>Azure에 응용 프로그램 배포

응용 프로그램을 배포하려면 Visual Studio에서 패키지를 만들고 Azure 관리 포털을 사용하여 업로드하거나 Visual Studio에서 직접 게시하면 됩니다. 이 자습서에서는 게시 방법을 사용합니다.

먼저 스테이징 환경에 응용 프로그램을 게시한 후에 스테이징에서 프로덕션으로 배포 수준을 올립니다.

### IP 제한 구현

스테이징에 배포하면 응용 프로그램은 URL을 아는 사람이 공개적으로 액세스할 수 있게 됩니다. 따라서 첫 번째 단계는 권한 없는 사용자가 응용 프로그램을 사용할 수 없도록 IP 제한을 구현하는 것입니다. 프로덕션 응용 프로그램에서는 ASP.NET Identity와 같은 인증 및 권한 부여 메커니즘을 구현합니다. 하지만 샘플 응용 프로그램에서는 설정, 배포, 테스트를 간소하게 유지하기 위해 이런 기능을 생략했습니다.

1.  `MvcWebRole` 프로젝트의 루트 폴더에 있는 *Web.Release.config* 파일을 열고 **ipAddress** 특성 값 127.0.0.1을 IP 주소로 바꿉니다. **솔루션 탐색기**에서 **Web.Release.config** 파일을 보려면 *Web.config* 파일을 확장해야 합니다.

    [Bing][] 또는 다른 검색 엔진에서 "내 IP 찾기"를 검색하면 자신의 IP 주소를 찾을 수 있습니다.

    응용 프로그램이 게시되면 *Web.release.config* 파일에 지정된 변환이 적용되며 클라우드에 배포된 *web.config* 파일에서 IP 제한 요소가 업데이트됩니다. 변환된 *web.config* 파일은 패키지가 만들어진 후 *AzureEmailService\\MvcWebRole\\obj\\Release\\TransformWebConfig\\transformed* 폴더에서 볼 수 있습니다.

### 클라우드에서 실행될 때 저장소 계정을 사용하도록 응용 프로그램 구성

자습서 앞부분에서 웹 역할 및 두 작업자 역할의 저장소 계정 자격 증명을 설정할 때 응용 프로그램 로컬 실행 시 사용할 자격 증명을 설정했습니다. 이제 클라우드에서 응용 프로그램을 실행할 때 사용할 저장소 계정 자격 증명을 설정해야 합니다.

이 테스트 실행을 위해 로컬에서 실행하는 데 사용한 것과 동일한 자격 증명을 클라우드에 사용합니다. 프로덕션 응용 프로그램을 배포하는 경우 일반적으로 테스트에 사용하는 것과 다른 계정을 프로덕션에 사용합니다. 또한 프로덕션의 경우 진단 connectionString에 저장소 연결 문자열과 다른 계정을 사용하는 것이 모범 사례입니다. 하지만 이 테스트의 경우 동일한 계정을 사용하겠습니다.

동일한 UI를 사용하여 연결 문자열을 구성합니다(**서비스 구성** 드롭다운 목록에서 **클라우드** 선택). 대신 다음 단계의 설명대로 구성 파일을 편집해도 됩니다.

1.  **AzureEmailService** 프로젝트의 *ServiceConfiguration.Local.cscfg* 파일을 열고 `StorageConnectionString`, `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`에 대한 `Setting` 요소를 복사합니다.

2.  **AzureEmailService** 프로젝트의 *ServiceConfiguration.Cloud.cscfg* 파일을 열고 복사한 요소를 `MvcWebRole`, `WorkerRoleA`, `WorkerRoleB`의 `Configuration Settings` 요소에 붙여 넣어 기존에 있던 `Setting` 요소를 바꿉니다.

3.  웹 역할 및 두 작업자 역할 요소가 모두 동일한 연결 문자열을 정의하는지 확인합니다.

### 응용 프로그램 게시

1.  아직 열려 있지 않은 경우 Visual Studio를 시작하고 **AzureEmailService** 솔루션을 엽니다.

2.  **AzureEmailService** 클라우드 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

    ![패키지][]

    **Publish Azure Application** 대화 상자가 나타납니다.

    ![클라우드 패키지][]

3.  앞부분에서 저장소 계정 자격 증명을 가져오는 데 자동 방법을 사용한 경우 Azure 구독이 드롭다운 목록에 나타납니다. 이를 선택한 후 **다음**.을 클릭하면 됩니다. 그렇지 않으면 **Sign in to download credentials**를 클릭하여 [Azure 저장소에 맞게 응용 프로그램 구성][]의 지침에 따라 게시 설정을 다운로드하고 가져옵니다.

4.  **일반 설정** 탭의 **클라우드 서비스** 드롭다운 목록에서 클라우드 서비스가 선택되었는지 확인합니다.

5.  **환경** 드롭다운 목록에서 **프로덕션**을 **스테이징**으로 변경합니다.

    ![대시보드][]

6.  **빌드 구성**의 기본 **릴리스** 설정을 그대로 두고 **서비스 구성**은 **클라우드**로 선택합니다.

    **고급** 탭의 기본 설정은 이 자습서 내용에 적합합니다. **고급** 탭에는 개발 및 테스트에 유용한 몇 가지 설정이 있습니다. 고급 탭에 대한 자세한 내용은 [Azure 응용 프로그램 게시 마법사][]를 참조하세요.

7.  **다음**을 클릭합니다.

8.  마법사의 **요약** 단계에서 **저장** 아이콘(대상 프로필 드롭다운 목록 오른쪽에 표시되는 디스켓 아이콘)을 클릭하여 게시 설정을 저장합니다.

    다음에 응용 프로그램을 게시할 때 저장된 설정이 사용되므로, 다시 게시 마법사 절차를 진행할 필요가 없습니다.

9.  설정을 검토한 후 **게시**를 클릭합니다.

    ![게시][]

    Visual Studio에서 **Azure Activity Log** 창이 열립니다.

10.  오른쪽 화살표 아이콘을 클릭하여 배포 세부 정보를 확장합니다.

    ![게시][5]
	<br/><br/>
    ![게시][6]

    배포가 완료되는 데 약 5분 이상 걸릴 수 있습니다.

2.  배포 완료 상태가 되면 **웹 사이트 URL**을 클릭하여 응용 프로그램을 시작합니다.

    ![대시보드][7]

3.  **Mailing List**, **Subscriber** 및 **Message** 웹 페이지에 일부 데이터를 입력하여 응용 프로그램을 테스트합니다.

    **참고**: 테스트를 완료한 후에는 사용하지 않는 리소스에 대해 비용을 부담하지 않도록 응용 프로그램을 삭제하세요. [Azure 무료 평가판 계정][]을 사용하는 경우에는 배포된 세 개의 역할이 몇 주 만에 월 한도를 소진합니다. Azure 관리 포털을 사용하여 배포를 삭제하려면 클라우드 서비스를 선택하고 페이지 아래쪽에 있는 **삭제**를 클릭한 다음 프로덕션 배포나 스테이징 배포를 선택합니다.

    ![게시][8]

4.  Visual Studio에서 Azure 활동 로그에 있는 **서버 탐색기에서 열기**를 선택합니다.

    **서버 탐색기**의 **Azure Compute**에서 배포를 모니터링할 수 있습니다. **Publish Azure Application** 마법사에서 **Enable Remote Desktop for all roles**를 선택한 경우 역할 인스턴스를 마우스 오른쪽 단추로 클릭하여 **Connect using Remote Desktop**을 선택할 수 있습니다.

    ![게시][9]

## <a name="swap"></a>스테이징에서 프로덕션으로 응용 프로그램 수준 올리기

1.  [Azure 관리 포털][]에서 왼쪽 창의 **클라우드 서비스** 아이콘을 클릭한 후 클라우드 서비스를 선택하고 **대시보드** 탭을 클릭합니다.

2.  **교환**을 클릭합니다.

3.  **예**를 클릭하여 VIP(가상 IP) 교환을 완료합니다. 이 단계를 완료하는 데는 몇 분 정도 걸릴 수 있습니다.

    ![대시보드][10]

4.  교환이 완료되면 **프로덕션** 배포의 **대시보드** 탭에서 페이지 오른쪽 아래에 있는 **간략 상태** 섹션으로 스크롤합니다. **사이트 URL**이 GUID 접두사에서 클라우드 서비스 이름으로 변경된 것을 확인하게 됩니다.

    ![대시보드][11]

5.  **사이트 URL** 아래의 링크를 클릭하거나 링크를 복사해서 브라우저에 붙여 넣어 프로덕션에서 응용 프로그램을 테스트합니다.

    저장소 계정 설정을 변경하지 않은 경우 스테이징된 버전의 응용 프로그램을 테스트하는 동안 입력한 데이터가 클라우드에서 응용 프로그램을 실행할 때 표시됩니다.

## <a name="trace"></a>추적 데이터 구성 및 보기

추적은 클라우드 응용 프로그램 디버깅에 중요한 도구입니다. 이 자습서 섹션에서는 추적 데이터를 보는 방법을 알아봅니다.

1.  진단 연결 문자열이 개발 저장소가 아니라 Azure 저장소 계정을 사용하도록 구성되어 있는지 확인합니다.

    자습서 앞부분의 지침을 따랐다면 이 계정은 동일합니다. Visual Studio UI(역할의 **속성**에 있는 **설정** 탭)를 사용하거나 *ServiceConfiguration.\*.cscfg* 파일을 검토하면 계정이 동일한지 확인할 수 있습니다.

     **참고:** 추적 데이터의 저장소 계정을 프로덕션 데이터의 저장소 계정과 달리 사용하는 것이 좋습니다. 하지만 편의상 이 자습서에서는 추적에 동일한 계정을 구성했습니다.

2.  Visual Studio에서 **WorkerRoleA** 프로젝트의 *WorkerRoleA.cs*를 열고 `ConfigureDiagnostics`를 검색한 후 `ConfigureDiagnostics` 메서드를 검토합니다.

        private void ConfigureDiagnostics()
        {
            DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
            config.ConfigurationChangePollInterval = TimeSpan.FromMinutes(1d);
            config.Logs.BufferQuotaInMB = 500;
            config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
            config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

            DiagnosticMonitor.Start(
                "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
                config);
        }

    이 코드에서는 최대 500MB의 추적 정보를 저장(500MB를 넘으면 가장 오래된 데이터를 덮어씀)하고 모든 추적 메시지를 저장(LogLevel.Verbose)하도록 `DiagnosticMonitor`가 구성되어 있습니다. `ScheduledTransferPeriod`는 일 분마다 추적 데이터를 저장소로 전송합니다. 추적 데이터를 저장하려면 `ScheduledTransferPeriod`를 설정해야 합니다.

    각 작업자 역할 및 웹 역할의 `ConfigureDiagnostics` 메서드는 추적 API 호출 시 데이터를 기록하도록 추적 리스너를 구성합니다. 자세한 내용은 [Azure 클라우드 응용 프로그램에서 추적 사용][](영문)을 참조하세요.

2.  **서버 탐색기**에서 이전에 추가한 저장소 계정의 **WADLogsTable**을 두 번 클릭합니다(**Azure** / **저장소** / **yourstorageaccountname** / **테이블** 확장). [WCF 데이터 서비스 필터][]를 입력하여 표시 엔터티를 제한할 수 있습니다. 다음 이미지에는 경고 및 오류 메시지만 표시되어 있습니다.

    ![대시보드][12]

## <a name="addRole"></a>증가한 로드를 처리하도록 다른 작업자 역할 인스턴스 추가

Azure 역할에서 계산 리소스를 확장하는 데는 두 가지 방법이 있습니다. 즉, [가상 컴퓨터 크기][]를 지정하거나 실행 중인 가상 컴퓨터 인스턴스 수를 지정합니다.

VM(가상 컴퓨터) 크기는 *ServiceDefinition.csdef* 파일에 있는 `WebRole` 또는 `WorkerRole` 요소의 `vmsize` 특성에서 지정됩니다. 기본 설정은 `Small`로서 코어 하나 및 1.75GB RAM을 제공합니다. 다중 스레드이며 많은 메모리, 디스크, 대역폭을 사용하는 응용 프로그램의 경우 성능 향상을 위해 VM 크기를 늘릴 수 있습니다. 예를 들어 `ExtraLarge` VM에는 8개의 CPU 코어와 14GB RAM이 있습니다. 단일 컴퓨터에서 메모리, CPU 코어, 디스크 및 대역폭을 높이는 것을 *강화*라고 합니다. [비동기 메서드][]를 사용하는 ASP.NET 웹 응용 프로그램의 경우 강화가 필요합니다. VM 크기별로 제공되는 리소스에 대한 설명은 [Virtual 가상 컴퓨터 크기][가상 컴퓨터 크기]를 참조하세요.

이 응용 프로그램의 작업자 역할 B는 전자 메일 보내기 작업을 하므로 큰 부하를 받는 제한적 구성 요소입니다. (작업자 역할 A는 큐 메시지를 만들기만 하므로 리소스를 많이 사용하지 않습니다.) 작업자 역할 B는 다중 스레드가 아니며 메모리 사용 공간이 크지 않기 때문에 적합한 강화 대상이 아닙니다. 작업자 역할 B는 인스턴스 수를 늘려 선형적으로(즉, 인스턴스를 두 배로 늘리면 성능도 거의 두 배가 됨) 확장할 수 있습니다. 계산 인스턴스 수를 늘리는 것을 *규모 확장*이라고 합니다. 인스턴스별로 비용이 발생하므로, 응용 프로그램에 필요할 때만 규모를 확장해야 합니다.

Visual Studio UI에서 설정을 업데이트하거나 *ServiceConfiguration.\*.cscfg* 파일을 직접 편집하여 웹 역할이나 작업자 역할의 규모를 확장할 수 있습니다. 인스턴스 수는 역할 **속성** 창의 **구성** 탭 및 *.cscfg* 파일의 `Instances` 요소에서 지정됩니다. 설정을 업데이트하는 경우 변경 내용이 적용되려면 업데이트된 구성 파일을 배포해야 합니다. 또는 일시적인 로드 증가의 경우, 역할 인스턴스의 수를 수동으로 변경하거나 지정한 조건을 기준으로 인스턴스 수를 자동으로 변경하도록 Azure를 구성할 수 있습니다. 자동 크기 조정에 대한 자세한 내용은 [이 시리즈의 마지막 자습서][]를 참조하세요.

이 자습서 섹션에서는 관리 포털을 사용하여 작업자 역할 B의 규모를 확장합니다. 하지만 먼저 Visual Studio에서 이를 수행하는 방법을 알아봅니다.

Visual Studio에서 수행하려면 클라우드 프로젝트의 **역할** 아래에서 역할을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

![마우스 오른쪽 단추 클릭 속성][]

그런 다음, 왼쪽에 있는 **구성** 탭을 선택하고 **서비스 구성** 드롭다운에서 **클라우드**를 선택합니다.

![인스턴스 수][]

이 탭에서 VM 크기를 구성할 수도 있습니다.

다음 단계는 Azure 관리 포털을 사용한 규모 확장 방법을 설명합니다.

1.  Azure 관리 포털에서 클라우드 서비스를 선택한 후 **크기 조정**을 클릭합니다.

2.  작업자 역할 B의 인스턴스 수를 늘린 후 **저장**을 클릭합니다.

    ![인스턴스 증가][]

    새로운 VM이 프로비전되는 데는 몇 분 정도 걸릴 수 있습니다.

3.  응용 프로그램의 각 역할 인스턴스를 보려면 **인스턴스** 탭을 선택합니다.

    ![인스턴스 보기][]

## <a name="nextsteps"></a>다음 단계

완료된 응용 프로그램을 구성, 배포 및 확장하는 방법을 알아보았습니다. 다음 자습서는 응용 프로그램을 처음부터 빌드하는 방법을 보여 줍니다. [다음 자습서][]에서는 웹 역할을 작성합니다.

Azure 저장소 테이블, 큐 및 Blob 작업 관련 추가 리소스에 대한 링크는 [이 시리즈의 마지막 자습서][13]를 참조하세요.

<div><a href="/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/" class="site-arrowboxcta download-cta">자습서 3</a></div>

  [시리즈의 첫 번째 자습서]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
  [개발 환경 설정]: #setupdevenv
  [완료된 솔루션 다운로드 및 실행]: #downloadcnfg
  [Azure 저장소 계정 만들기]: #createWASA
  [클라우드 서비스 만들기]: #createcloudsvc
  [Azure 저장소에 맞게 응용 프로그램 구성]: #conf4azureStorage
  [SendGrid를 사용하도록 응용 프로그램 구성]: #sendGrid
  [Azure에 응용 프로그램 배포]: #deployAz
  [스테이징에서 프로덕션으로 응용 프로그램 수준 올리기]: #swap
  [추적 데이터 구성 및 보기]: #trace
  [증가한 로드를 처리하도록 다른 작업자 역할 인스턴스 추가]: #addRole
  [install-sdk-2013-only]: ../includes/install-sdk-2013-only.md
  [완료된 솔루션]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [앱을 실행합니다.]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailinglist1.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create1.png
  [메일 그룹 인덱스 페이지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-mailing-list-index-page.png
  [구독자 인덱스 페이지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-subscribers-index-page.png
  [메시지 작성 페이지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-create-page.png
  [메시지 인덱스 페이지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-message-index-page.png
  [서버 탐색기]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-serverExplorer.png
  [VS 저장소 탐색기]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-wasVSdata.png
  [Azure 저장소 탐색기]: http://azurestorageexplorer.codeplex.com/
  [Azure 관리 포털]: http://manage.windowsazure.com
  [새 저장소]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-portal-new-storage.png
  [빠른 생성]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-storage-quick.png
  [저장소 계정을 관리하는 방법]: /en-us/manage/services/storage/how-to-manage-a-storage-account/
  [URL 접두사를 사용하여 저장소 만들기]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-storage-url-test.png
  [키 관리]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-manage-keys.png
  [키 GUID]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-guid-keys.PNG
  [빠른 클라우드]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-new-cloud.png
  [Azure에서 선호도 그룹을 만드는 방법]: http://msdn.microsoft.com/en-us/library/jj156209.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-create-cloud.png
  [마우스 오른쪽 단추 클릭 속성]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-rt-prop.png
  [3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-elip.png
  [4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-enter.png
  [Azure 프로젝트 구성]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405486.aspx
  [클라우드 프로젝트 속성 선택]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-aesp.png
  [저장소 에뮬레이터 자동 시작 사용 안 함]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-1.png
  [ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-se4.png
  [Azure와 함께 SendGrid를 사용하여 전자 메일을 보내는 방법]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/sendgrid-email-service/ "SendGrid"
  [SendGridSettings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-sg.png
  [Bing]: http://www.bing.com/search?q=find+my+IP&qs=n&form=QBLH&pq=find+my+ip&sc=8-10&sp=-1&sk= "내 IP 찾기"
  [패키지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-6.png
  [클라우드 패키지]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-16.png
  [대시보드]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-7.png
  [Azure 응용 프로그램 게시 마법사]: http://msdn.microsoft.com/library/windowsazure/hh535756.aspx "게시 마법사"
  [게시]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-8.png
  [5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-11.png
  [6]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-9.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c55.png
  [Azure 무료 평가판 계정]: http://www.windowsazure.com/en-us/pricing/free-trial/ "무료 평가판 계정"
  [8]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-19.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-12.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c6.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-c7.png
  [Azure 클라우드 응용 프로그램에서 추적 사용]: http://blogs.msdn.com/b/windowsazure/archive/2012/10/24/using-trace-in-windows-azure-cloud-applications-1.aspx "Azure에서 추적 사용"
  [WCF 데이터 서비스 필터]: http://msdn.microsoft.com/en-us/library/windowsazure/ff683669.aspx "WCF 필터"
  [12]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-trc.png
  [가상 컴퓨터 크기]: http://msdn.microsoft.com/en-us/library/windowsazure/ee814754.aspx "VM 크기"
  [비동기 메서드]: http://www.asp.net/mvc/tutorials/mvc-4/using-asynchronous-methods-in-aspnet-mvc-4 "비동기 MVC"
  [이 시리즈의 마지막 자습서]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
  [인스턴스 수]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-instanceCnt.png
  [인스턴스 증가]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in3.png
  [인스턴스 보기]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-download-run/mtas-in2.png
  [다음 자습서]: /en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [13]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
