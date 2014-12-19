<properties linkid="dev-nodejs-getting-started" urlDisplayName="Cloud Service" pageTitle="Node.js Getting Started Guide - Azure Tutorial" metaKeywords="Azure node.js getting started, Azure Node.js tutorial, Azure Node.js tutorial" description="An end-to-end tutorial that helps you develop a simple Node.js web application and deploy it to Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build and deploy a Node.js application to an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Azure 클라우드 서비스에서 Node.js 응용 프로그램 빌드 및 배포

이 가이드를 완료하면 간단한 Node.js 응용 프로그램을 Azure 클라우드 서비스에서실행할 수 있습니다. 클라우드 서비스는 Azure에서 확장 가능한클라우드 응용 프로그램의 구성 요소입니다. 이 클라우드 서비스는 응용 프로그램의 프런트엔드 및 백 엔드 구성 요소의 구분과 독립적인 관리 및 확장을 허용합니다. 클라우드 서비스는각 역할을 안정적으로 호스팅할 수 있는 강력한 전용 가상 컴퓨터를 제공합니다.

클라우드 서비스에 대한 자세한 내용 및 Azure 웹 사이트와 가상 컴퓨터와의 비교에 대한 자세한 내용은 [Azure 웹 사이트, 클라우드 서비스 및 가상 컴퓨터 비교][Azure 웹 사이트, 클라우드 서비스 및 가상 컴퓨터 비교]를 참조하십시오.

<p />

<div class="dev-callout"><strong>간단한 웹 사이트를 빌드하려는 경우</strong>
<p>시나리오에 간단한 웹 사이트 프런트 엔드만 포함된 경우 <a href="/ko-kr/documentation/articles/web-sites-nodejs-develop-deploy-mac/">간단한 Azure 웹 사이트를 사용</a>합니다. 그러면 웹 사이트가 커지고 요구 사항이 변경될 때 클라우드 서비스로 쉽게 업그레이드할 수 있습니다.</p>
</div>

이 자습서를 수행하여 웹 역할 내에서 호스트되는 간단한 웹 응용 프로그램을 빌드합니다. 계산에뮬레이터를 사용하여 로컬에서 응용 프로그램을 테스트한 다음 PowerShell 명령줄 도구를사용하여 배포합니다.아래에는 완성된 응용 프로그램의 스크린샷이 표시되어 있습니다.

![hello world 페이지를 표시하는 브라우저 창입니다. URL은 페이지가 Azure에서 호스트됨을 나타냅니다.][hello world 페이지를 표시하는 브라우저 창입니다. URL은 페이지가 Azure에서 호스트됨을 나타냅니다.]

## 새 노드 응용 프로그램 만들기

기본 Node.js 스캐폴딩과 함께 새 Azure 클라우드 서비스 프로젝트를 만들려면 다음 작업을 수행하십시오.

1.  **시작 메뉴** 또는 **시작 화면**에서 **Azure PowerShell**을 검색합니다. 마지막으로, **Azure PowerShell**을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.

    ![Azure PowerShell 아이콘][Azure PowerShell 아이콘]

    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  C 드라이브에 새 **node** 디렉터리를 만들고 c:\\node 디렉터리로 변경합니다.

    !['mkdir c:\node' 및 'cd node' 명령을 표시하는 명령 프롬프트][mkdir]


3.  다음 cmdlet을 입력하여 새 솔루션을 만듭니다.

        PS C:\node> New-AzureServiceProject helloworld

        You will see the following response:

    ![New-AzureService helloworld 명령의 결과][New-AzureService helloworld 명령의 결과]

    **New-AzureServiceProject** cmdlet은 클라우드 서비스에 게시될 새 Azure 노드 응용 프로그램을 만들 수 있는 기본 구조를 생성합니다. 여기에는 Azure에 게시하는 데 필요한 구성 파일이 포함됩니다. 또한 이 cmdlet은 작업 디렉터리를 서비스에 대한 디렉터리로 변경합니다.

    **New-AzureServiceProject** cmdlet으로 만들어지는 파일은 다음과 같습니다.

    -   **ServiceConfiguration.Cloud.cscfg**,
        **ServiceConfiguration.Local.cscfg** 및 **ServiceDefinition.csdef**는
        응용 프로그램을 게시하는 데 필요한 Azure 관련 파일입니다.

    이러한 파일에 대한 자세한 내용은 [Azure에 대한 호스티드 서비스 만들기 개요][Azure에 대한 호스티드 서비스 만들기 개요](영문)를 참조하십시오.

    -   **deploymentSettings.json**에는 Azure PowerShell 배포 cmdlet에
        사용되는 로컬 설정이 저장됩니다.

4.  
    **Add-AzureNodeWebRole cmdlet**을 사용하여 새 웹 역할을 추가하려면 다음 명령을 입력합니다.

        PS C:\node\helloworld> Add-AzureNodeWebRole

    다음과 같은 응답이 표시됩니다.

    ![Add-AzureNodeWebRole 명령의 출력][Add-AzureNodeWebRole 명령의 출력]

    **Add-AzureNodeWebRole** cmdlet은 응용 프로그램에 대한 새 디렉터리를 만들고 기본 Node.js 응용 프로그램에 대한 스캐폴딩을 생성합니다. 또한 이전 단계에서 생성된 **ServiceConfiguration.Cloud.csfg**, **ServiceConfiguration.Local.csfg** 및 **ServiceDefinition.csdef** 파일을 수정하여 새 역할에 대한 구성 항목을 추가합니다.

    <div class="dev-callout">
<b>참고</b>
<p>기본적으로 역할 이름을 제공하지 않으면 자동으로 생성됩니다. 이름을 <b>Add-AzureNodeWebRole</b>의 첫 번째 매개 변수로 제공할 수 있습니다. 예를 들면 <code data-inline="1">Add-AzureNodeWebRole MyRole</code>과 같습니다.</p>
</div>

5.  다음 명령을 사용하여 **WebRole1** 디렉터리로 이동한 다음 메모장에서 **server.js** 파일을 엽니다.

    PS C:\\node\\helloworld\> cd WebRole1
     PS C:\\node\\helloworld\\WebRole1\> notepad server.js

    **server.js** 파일은 **Add-AzureNodeWebRole** cmdlet으로 생성되었으며 다음과 같은 시작 코드를 포함합니다. 이 코드는 다음 사항을 제외하면 [nodejs.org][nodejs.org] 웹 사이트의 "Hello World" 샘플과 유사합니다.

    -   응용 프로그램이 클라우드 환경에 의해 할당된 올바른 포트를
        찾을 수 있도록 포트가 변경되었습니다.
    -   콘솔 로깅이 제거되었습니다.

    ![server.js의 내용이 표시된 메모장][server.js의 내용이 표시된 메모장]

## 에뮬레이터에서 로컬로 응용 프로그램 실행

Azure 계산 에뮬레이터는 Azure SDK를 통해 설치되는 도구중 하나이며, 이 도구를 통해 응용 프로그램을 로컬에서 테스트할 수 있습니다. 계산에뮬레이터는 응용 프로그램이 클라우드에 배포될 때 실행될 환경을시뮬레이션합니다. 에뮬레이터에서 응용 프로그램을 테스트하려면 다음 단계를 수행하십시오.

1.  메모장을 닫고 Windows PowerShell 창으로 전환합니다. 다음 cmdlet을 입력하여 에뮬레이터에서 서비스를 실행합니다.

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

    **-Launch** 매개 변수는 도구에서 자동으로 브라우저 창을 열고 응용 프로그램이 에뮬레이터에서 실행되고 있으면 표시하도록 지정합니다. 아래 스크린샷에 표시된 대로 브라우저가 열리고 "Hello World"가 표시됩니다. 이는 서비스가 계산 에뮬레이터에서 실행되며 올바르게 작동하고 있음을 나타냅니다.

    ![Hello World 웹 페이지를 표시하는 웹 브라우저][Hello World 웹 페이지를 표시하는 웹 브라우저]

2.  계산 에뮬레이터를 중지하려면 **Stop-AzureEmulator** 명령을 사용합니다.

    PS C:\\node\\helloworld\\WebRole1\> Stop-AzureEmulator

## Azure에 응용 프로그램 배포

    [WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

### <span id="download_publishing_settings"></span></a>Azure 게시 설정 다운로드

응용 프로그램을 Azure에 배포하려면 먼저 Azure 구독에 대한 게시 설정을 다운로드해야 합니다. 다음 단계에서는 이 프로세스를 안내합니다.

1.  Windows PowerShell 창에서 다음 cmdlet을 실행하여 다운로드 페이지를 시작합니다.

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

    그러면 브라우저를 사용하여 게시 설정 다운로드 페이지로 이동합니다. Microsoft 계정으로 로그인하라는 메시지가 표시될 수 있습니다. 그럴 경우 Azure 구독과 연결된 계정을 사용합니다.

    다운로드한 프로필을 쉽게 액세스할 수 있는 파일 위치에 저장합니다.

2.  Azure PowerShell 창에서 다음 cmdlet을 사용하여 다운로드한 Azure 게시 프로필을 사용하도록 Node.js에 대한 Windows PowerShell cmdlet을 구성합니다.

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [path to file]

    <div class="dev-callout">
<b>참고</b>
<p>다운로드한 .publishSettings 파일에는 다른 사람이 사용자 계정에 액세스할 수 있는 정보가 포함되어 있으므로 게시 설정을 가져온 후에는 파일을 삭제하는 것이 좋습니다.</p>
</div>

### 응용 프로그램 게시

1.  아래와 같이 **Publish-AzureServiceProject** cmdlet을 사용하여 응용
    프로그램을 게시합니다.

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

    -   **servicename** 매개 변수는 이 배포에 사용되는 이름을 지정합니다. 이 이름은 고유해야 합니다. 그렇지 않으면 게시 프로세스가 실패합니다.

    -   **location** 매개 변수는 응용 프로그램이 호스트될 데이터 센터를 지정합니다. 사용 가능한 데이터 센터 목록을 표시하려면 **Get-AzureLocation** cmdlet을 사용합니다.

    -   **launch** 매개 변수는 브라우저를 시작하고 배포가 완료된 후 호스티드 서비스로 이동합니다.

    게시가 성공하면 다음과 같은 응답이 표시됩니다.

    ![Publish-AzureService 명령의 출력][Publish-AzureService 명령의 출력]

    **Publish-AzureServiceProject** cmdlet은 다음 단계를 수행합니다.

2.  Azure에 배포될 패키지를 만듭니다. 이 패키지에는 node.js 응용 프로그램 폴더의 모든 파일이 포함됩니다.

3.  **저장소 계정**이 없는 경우 새로 만듭니다. Azure 저장소 계정은 배포 중 응용 프로그램 패키지를 저장하는 데 사용됩니다. 배포가 완료된 후에는 저장소 계정을 삭제해도 안전합니다.

4.  **클라우드 서비스**가 아직 없는 경우 새로 만듭니다. **클라우드 서비스**는 응용 프로그램이 Azure에 배포될 때 호스트되는 컨테이너입니다. 자세한 내용은 [Azure에 대한 호스티드 서비스 만들기 개요][Azure에 대한 호스티드 서비스 만들기 개요](영문)를 참조하십시오.

5.  배포 패키지를 Azure에 게시합니다.

    > [WACOM.NOTE]
    > 응용 프로그램이 배포되고 처음 게시된 후 사용할 수 있으려면 5~7분 정도 걸릴 수 있습니다.

    배포가 완료되면 브라우저 창이 열리고 클라우드 서비스로 이동합니다.

    ![hello world 페이지를 표시하는 브라우저 창입니다. URL은 페이지가 Azure에서 호스트됨을 나타냅니다.][1]

    이제 응용 프로그램이 Azure에서 실행되고 있습니다.

## 응용 프로그램 중지 및 삭제

응용 프로그램을 배포한 후 사용하지 않도록 설정하여 추가 비용을 방지할 수 있습니다. Azure는 사용된 서버 시간의 시간당 웹 역할 인스턴스 요금을 청구합니다. 서버 시간은 응용 프로그램이 배포된 다음에 사용되며 인스턴스가 실행되지 않고 중지된 상태인 경우에도 사용됩니다.

1.  Windows PowerShell 창에서, 이전 섹션에서 만든 서비스 배포를 다음 cmdlet을 사용하여 중지합니다.

        PS C:\node\helloworld\WebRole1> Stop-AzureService

    서비스를 중지하려면 몇 분 정도 걸릴 수 있습니다. 서비스가 중지되면 서비스가 중지되었다는 메시지가 표시됩니다.

    ![Stop-AzureService 명령의 상태][Stop-AzureService 명령의 상태]

2.  서비스를 삭제하려면 다음 cmdlet을 호출합니다.

        PS C:\node\helloworld\WebRole1> Remove-AzureService

    메시지가 표시되면 **Y**를 입력하여 서비스를 삭제합니다.

    서비스를 삭제하려면 몇 분 정도 걸릴 수 있습니다. 서비스가 삭제되면 서비스가 삭제되었다는 메시지가 표시됩니다.

    ![Remove-AzureService 명령의 상태][Remove-AzureService 명령의 상태]

    <div class="dev-callout">
<strong>참고</strong>
<p>서비스를 삭제해도 서비스가 처음 게시될 때 만들어진 저장소 계정은 삭제되지 않으므로 사용된 저장소에 대해 계속 요금이 청구됩니다. 저장소 계정 삭제에 대한
자세한 내용은 <a href="http://msdn.microsoft.com/ko-kr/library/windowsazure/hh531562.aspx">Azure 구독에서 저장소 계정을 삭제하는 방법</a>(영문)을 참조하십시오.</p>
</div>

  [Azure 웹 사이트, 클라우드 서비스 및 가상 컴퓨터 비교]: http://azure.microsoft.com/ko-kr/documentation/articles/choose-web-site-cloud-service-vm/
  [hello world 페이지를 표시하는 브라우저 창입니다. URL은 페이지가 Azure에서 호스트됨을 나타냅니다.]: https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/en-us/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png
  [Azure PowerShell 아이콘]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png
  [New-AzureService helloworld 명령의 결과]: ./media/cloud-services-nodejs-develop-deploy-app/node9.png
  [Azure에 대한 호스티드 서비스 만들기 개요]: http://msdn.microsoft.com/ko-kr/library/windowsazure/jj155995.aspx
  [Add-AzureNodeWebRole 명령의 출력]: ./media/cloud-services-nodejs-develop-deploy-app/node11.png
  [nodejs.org]: http://nodejs.org/
  [server.js의 내용이 표시된 메모장]: ./media/cloud-services-nodejs-develop-deploy-app/node13.png
  [Hello World 웹 페이지를 표시하는 웹 브라우저]: ./media/cloud-services-nodejs-develop-deploy-app/node14.png
  [Publish-AzureService 명령의 출력]: ./media/cloud-services-nodejs-develop-deploy-app/node19.png
  [1]: ./media/cloud-services-nodejs-develop-deploy-app/node21.png
  [Stop-AzureService 명령의 상태]: ./media/cloud-services-nodejs-develop-deploy-app/node48.png
  [Remove-AzureService 명령의 상태]: ./media/cloud-services-nodejs-develop-deploy-app/node49.png
  [mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
