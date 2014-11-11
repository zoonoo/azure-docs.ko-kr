<properties linkid="dev-nodejs-cloud9" urlDisplayName="Deploying with Cloud9" pageTitle="Node.js deploying with Cloud9 - Azure tutorial" metaKeywords="Cloud9 IDE Azure, Azure node.js, Azure node apps" description="Learn how to use Cloud9 IDE to develop, build, and deploy a Node.js application to Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Deploying an Azure App from Cloud9" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Cloud9에서 Azure 앱 배포

이 자습서에서는 Cloud9 IDE를 사용하여 Node.js 응용 프로그램을 개발 및 빌드하고 Azure에 배포하는 방법을 설명합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

-   Cloud9 IDE 프로젝트 만들기
-   Azure에 프로젝트 배포
-   기존 Azure 배포 업데이트
-   스테이징 배포와 프로덕션 배포 간에 프로젝트 이동

[Cloud9 IDE][Cloud9 IDE]에서는 크로스 플랫폼 브라우저 기반 개발 환경을 제공합니다. Cloud9에서 Node.js 프로젝트에 대해 지원하는 기능 중 하나는 IDE 내에서 Azure에 직접 배포할 수 있는 기능입니다. 또한 Cloud9은 GitHub 및 BitBucket 리포지토리 서비스와 통합되므로 다른 사용자와 프로젝트를 쉽게 공유할 수 있습니다.

Cloud9을 사용하면 추가 개발 도구나 SDK를 로컬에 설치하지 않고도 대부분의 최신 브라우저와 운영 체제에서 응용 프로그램을 개발하고 Azure에 배포할 수 있습니다. 아래에서는 Mac에서 Google Chrome을 사용하여 실행하는 단계를 보여 줍니다.

## 등록

Cloud9을 사용하려면 먼저 해당 웹 사이트를 방문하여 [구독을 등록][Cloud9 IDE]해야 합니다. 기존GitHub 또는 BitBucket 계정으로 로그인하거나 Cloud9 계정을 만들 수 있습니다. 무료 구독뿐 아니라 추가 기능을 제공하는 유료 구독도 사용할 수 있습니다. 자세한 내용은 [Cloud9 IDE][Cloud9 IDE]를 참조하십시오.

## Node.js 프로젝트 만들기

1.  Cloud9에 로그인하고 **내 프로젝트** 옆의 **+** 기호를 클릭한 다음 **새 프로젝트 만들기**를 선택합니다.

    ![새 Cloud9 프로젝트 만들기][새 Cloud9 프로젝트 만들기]

2.  **새 프로젝트 만들기** 대화 상자에서 프로젝트 이름, 액세스 및 프로젝트 형식을 입력합니다. **만들기**를 클릭하여 프로젝트를 만듭니다.

    ![새 프로젝트 만들기 대화 상자 Cloud9][새 프로젝트 만들기 대화 상자 Cloud9]

    <div class="dev-callout">
<strong>참고</strong>
<p>일부 옵션에는 유료 Cloud9 플랜이 필요합니다.</p>
</div>

    <div class="dev-callout">
<strong>참고</strong>
<p>Azure에 배포할 때 Cloud9 프로젝트의 프로젝트 이름은 사용되지 않습니다.</p>
</div>

3.  프로젝트를 만든 후 **편집 시작**을 클릭합니다. Cloud9 IDE를 처음 사용하는 경우 서비스 둘러보기 옵션이 제공됩니다. 둘러보기를 건너뛰고 나중에 보려면 **Just the editor,please**를 선택합니다.

    ![편집 시작 Cloud9 프로젝트][편집 시작 Cloud9 프로젝트]

4.  새 노드 응용 프로그램을 만들려면 **파일**을 선택한 후 **새 파일**을 선택합니다.

    ![Cloud9 프로젝트의 새 파일 만들기][Cloud9 프로젝트의 새 파일 만들기]

5.  **Untitled1**이라는 새 탭이 표시됩니다. **Untitled1** 탭에서 다음 코드를 입력하여 노드 응용 프로그램을 만듭니다.

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure\n');
        }).listen(port);

    <div class="dev-callout">
<strong>참고</strong>
<p>process.env.PORT를 사용하면 Cloud9 디버거에서 실행했는지 또는 Azure에 배포했는지에 관계없이 응용 프로그램이 올바른 포트를 선택합니다.</p>
</div>

6.  코드를 저장하려면 **파일**을 선택한 후 **다른 이름으로 저장**을 선택합니다. **다른 이름으로 저장** 대화 상자에서 파일 이름으로 **server.js**를 입력하고 **저장**을 클릭합니다.

    <div class="dev-callout">
<strong>참고</strong>
<p>req 변수가 사용되지 않았음을 나타내는 경고 기호가 표시될 수도 있습니다. 이 경고는 무시해도 됩니다.</p>
</div>

    ![server.js 파일 저장][server.js 파일 저장]

## 응용 프로그램 실행

<div class="dev-callout">
<strong>참고</strong>
<p>이 섹션에 제공된 단계는 Hello World 응용 프로그램에 충분하지만 외부 모듈을 사용하는 응용 프로그램의 경우 디버그 환경을 위해 특정 버전의 Node.js를 선택해야 할 수도 있습니다. 디버그 드롭다운에서 <strong>구성...</strong>을 선택한 후 특정 버전의 Node.js를 선택하면 됩니다. 예를 들어 Node.js 0.6.x가 선택되지 않은 경우 'azure' 모듈을 사용할 때 인증 오류가 표시될 수도 있습니다.</p>
</div>

1.  **디버그**를 클릭하여 Cloud9 디버거에서 응용 프로그램을 실행합니다.

    ![디버거에서 실행][디버거에서 실행]

2.  출력 창이 표시됩니다. 나열된 URL을 클릭하여 브라우저 창을 통해 응용 프로그램에 액세스합니다.

    ![출력 창][출력 창]

    결과 응용 프로그램은 다음과 같이 표시됩니다.

    ![브라우저에서 실행 중인 응용 프로그램][브라우저에서 실행 중인 응용 프로그램]

3.  응용 프로그램 디버그를 중지하려면 **중지**를 클릭합니다.

## Azure 계정 만들기

응용 프로그램을 Azure에 배포하려면 계정이 필요합니다. Azure 계정이 아직 없는 경우 다음 단계에 따라 무료 평가판에 등록할 수 있습니다.

[WACOM.INCLUDE [create-azure-account](../includes/create-azure-account.md)]

## 배포 만들기

1.  새 배포를 만들려면 **배포**를 선택하고 **+**를 클릭하여 배포 서버를 만듭니다.

    [create a new deployment][create a new deployment]

2.  **배포 대상 추가** 대화 상자에서 배포 이름을 입력하고 **유형 선택** 목록에서 **Azure**를 선택합니다. 지정한 배포 이름은 Cloud9 내에서 배포를 식별하는 데 사용됩니다. Azure 내의 배포 이름과 일치하지 않습니다.

3.  Azure를 사용하는 Cloud9 배포를 처음 만든 경우 Azure 게시 설정을 구성해야 합니다. 다음 단계에 따라 이러한 설정을 Cloud9으로 다운로드하여 설치합니다.

    1.  **Azure 설정 다운로드**를 클릭합니다.

        ![게시 설정 다운로드][게시 설정 다운로드]

        Azure 관리 포털이 열리고 Azure 게시 설정을 다운로드하라는 메시지가 표시됩니다. 먼저 Azure 계정에 로그인해야 시작할 수 있습니다.

    2.  게시 설정 파일을 로컬 드라이브에 저장합니다.

    3.  **배포 대상 추가** 대화 상자에서 **파일 선택**을 선택한 후 이전 단계에서 다운로드한 파일을 선택합니다.

    4.  파일을 선택한 후 **업로드**를 클릭합니다.

4.  **+ 새로 만들기**를 클릭하여 새 호스팅 서비스를 만듭니다. *호스팅 서비스*는 응용 프로그램이 Azure에 배포될 때 호스트되는 컨테이너입니다. 자세한 내용은 [Azure에 대한 호스팅 서비스 만들기 개요][Azure에 대한 호스팅 서비스 만들기 개요](영문)를 참조하십시오.

    ![새 배포 만들기][새 배포 만들기]

5.  새 호스팅 서비스의 이름과 인스턴스 수, 호스트 OS, 데이터 센터 등의 구성 옵션을 묻는 메시지가 표시됩니다. 지정한 배포 이름은 Azure에서 호스팅 서비스로 사용됩니다. 이 이름은 Azure 시스템 내에서 고유해야 합니다.

    ![새 호스팅 서비스 만들기][새 호스팅 서비스 만들기]

    <div class="dev-callout">
<strong>참고</strong>
<p><strong>배포 대상 추가</strong> 대화 상자의 <strong>기존 배포 선택</strong> 섹션 아래에 기존 Azure 호스팅 서비스가 모두 나열됩니다. 기존 호스팅 서비스를 선택하면 이 프로젝트가 해당 서비스에 배포됩니다.</p>
</div>

    <div class="dev-callout">
<strong>참고</strong>
<p><strong>RDP 사용</strong>을 선택하고 사용자 이름 및 암호를 제공하면 배포에 원격 데스크톱을 사용할 수 있습니다.</p>
</div>

## Azure 프로덕션 환경에 배포

1.  이전 단계에서 만든 배포를 선택합니다. 이 배포에 대한 정보 및 Azure에 배포한 후 사용되는 프로덕션 URL을 제공하는 대화 상자가 나타납니다.

    ![배포 선택][배포 선택]

2.  **프로덕션 환경에 배포**를 선택합니다.

3.  **배포**를 클릭하여 배포를 시작합니다.

4.  이 프로젝트를 Azure에 처음 배포하는 경우 **'web.config를 찾을 수 없음'** 오류가 표시됩니다. **예**를 선택하여 파일을 만듭니다. 'Web.cloud.config' 파일이 프로젝트에 추가됩니다.

    ![web.config 파일을 찾을 수 없음 메시지][web.config 파일을 찾을 수 없음 메시지]

5.  이 프로젝트를 Azure에 처음 배포하는 경우 **''csdef' 파일이 없음'** 오류가 표시됩니다. **예**를 선택하여 .csdef 파일을 만듭니다. 'ServiceDefinition.csdef' 파일이 프로젝트에 추가됩니다. ServiceDefinition.csdef는 응용 프로그램을 게시하는 데 필요한 Azure 관련 파일입니다. 자세한 내용은 [Azure에 대한 호스팅 서비스 만들기 개요][Azure에 대한 호스팅 서비스 만들기 개요](영문)를 참조하십시오.

6.  이 응용 프로그램에 대한 인스턴스 크기를 선택하라는 메시지가 표시됩니다. **작음**을 선택하고 **만들기**를 클릭합니다. Azure VM 크기에 대한 자세한 내용은 [가상 컴퓨터 크기를 구성하는 방법][가상 컴퓨터 크기를 구성하는 방법]을 참조하십시오.

    ![csdef 파일 값 지정][csdef 파일 값 지정]

7.  배포 항목은 배포 프로세스의 상태를 표시합니다. 완료하면 배포가 **활성**으로 표시됩니다.

    ![배포 상태][배포 상태]

    <div class="dev-callout">
<strong>참고</strong>
<p>Cloud 9 IDE를 통해 배포된 프로젝트에는 Azure의 배포 이름으로 GUID가 할당됩니다.</p>
</div>

8.  배포 대화 상자에는 프로덕션 URL에 대한 링크가 포함되어 있습니다. 배포가 완료되면 URL을 클릭하여 Azure에서 실행 중인 응용 프로그램으로 이동합니다.

    ![Azure 프로덕션 URL 링크][Azure 프로덕션 URL 링크]

## 응용 프로그램 업데이트

응용 프로그램을 변경하는 경우 Cloud9를 사용하여 업데이트된 응용 프로그램을 동일한 Azure 호스팅 서비스에 배포할 수 있습니다.

1.  server.js 파일에서 코드를 업데이트하여 "hello azure v2"가 화면에 출력되도록 합니다. 기존 코드를 업데이트된 다음 코드로 바꿀 수 있습니다.

        var http = require('http');
        var port = process.env.PORT;
        http.createServer(function(req,res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('hello azure v2\n');
        }).listen(port);

2.  코드를 저장하려면 **파일**을 선택한 후 **저장**을 선택합니다.

## Azure 스테이징 환경에 업데이트 배포

1.  **스테이징에 배포**를 선택합니다.

2.  **배포**를 클릭하여 배포를 시작합니다.

    각 Azure 호스팅 서비스는 스테이징과 프로덕션의 두 환경을 지원합니다. Azure에서 생성된 애매한 GUID 기반 URL로 스테이징된 응용 프로그램에만 액세스할 수 있다는 점을 제외하고 스테이징 환경은 프로덕션 환경과 동일합니다. 스테이징 환경을 사용하여 응용 프로그램을 테스트할 수 있으며, 변경 내용을 확인한 후 이 자습서의 뒷부분에서 설명한 대로 VIP(가상 IP) 교환을 수행하여 스테이징 버전에서 프로덕션으로 이동할 수 있습니다.

3.  응용 프로그램을 스테이징에 배포하면 아래 스크린샷과 같이 GUID 기반 스테이징 URL이 콘솔 출력에 표시됩니다. URL을 클릭하여 브라우저에서 스테이징된 응용 프로그램을 엽니다.

    ![스테이징 URL을 표시하는 콘솔 출력][스테이징 URL을 표시하는 콘솔 출력]

## VIP 교환을 사용하여 업데이트를 프로덕션으로 이동

프로덕션 또는 스테이징 환경에 서비스를 배포하면 VIP(가상 IP 주소)가 해당 환경의 서비스에 할당됩니다. 스테이징 환경에서 프로덕션 환경으로 서비스를 이동하려는 경우 다시 배포하지 않고 스테이징 및 프로덕션 배포를 교환하는 VIP 교환을 통해 이동할 수 있습니다. VIP 교환을 사용하면 프로덕션 환경의 가동 중지 시간 없이 테스트 및 스테이징된 응용 프로그램이 프로덕션에 배포됩니다. 자세한 내용은 [Azure에서 배포 관리 개요][Azure에서 배포 관리 개요]를 참조하세요.

1.  배포 대화 상자에서 **포털 열기** 링크를 클릭하여 Azure 관리 포털을 엽니다.

    [Link from deploy dialog to Azure Management Portal][Link from deploy dialog to Azure Management Portal]

2.  자격 증명을 사용하여 포털에 로그인합니다.

3.  웹 페이지의 왼쪽에서 **호스팅된 서비스, 저장소 계정 및 CDN**을 선택하고 **호스팅된 서비스**를 클릭합니다.

    ![Azure Management Portal][Azure Management Portal]

    결과 창에 Cloud9에서 지정한 이름의 호스팅 서비스와 두 개의 배포가 표시됩니다. 배포의 **환경** 값은 각각 **스테이징**과 **프로덕션**입니다.

4.  VIP 교환을 수행하려면 호스팅 서비스를 선택하고 리본 메뉴에서 **VIP 교환**을 클릭합니다.

    ![VIP 교환][VIP 교환]

5.  표시되는 VIP 교환 대화 상자에서 **확인**을 클릭합니다.

6.  프로덕션 응용 프로그램으로 이동합니다. 이전에 스테이징에 배포한 응용 프로그램 버전이 이제 프로덕션에 표시됩니다.

    ![Azure에서 실행 중인 프로덕션 응용 프로그램][Azure에서 실행 중인 프로덕션 응용 프로그램]

## 원격 데스크톱 사용

RDP를 사용하도록 설정했으며 배포를 만들 때 사용자 이름 및 암호를 지정한 경우 원격 데스크톱을 사용하여 특정 인스턴스를 선택한 후 리본 메뉴에서 연결을 선택하면 호스팅된 서비스에 연결할 수 있습니다.

![인스턴스에 연결][인스턴스에 연결]

연결을 클릭하면 .RDP 파일을 열거나 다운로드하라는 메시지가 표시됩니다. 이 파일에는 원격 데스크톱 세션에 연결하는 데 필요한 정보가 포함되어 있습니다. Windows 시스템에서 이 파일을 실행하면 배포를 만들 때 입력한 사용자 이름 및 암호를 묻는 메시지가 표시되고 선택한 인스턴스의 데스크톱에 연결됩니다.

<div class="dev-callout">
<strong>참고</strong>
<p>응용 프로그램의 호스트된 인스턴스에 연결하는 .RDP 파일은 Windows의 원격 데스크톱 응용 프로그램에서만
작동합니다.</p>
</div>

## 응용 프로그램 중지 및 삭제

Azure는 사용된 서버 시간을 기준으로 역할 인스턴스에 청구하며, 응용 프로그램이 배포된 동안에는 인스턴스가 실행되지 않고 중지된 상태인 경우에도 서버 시간이 사용됩니다. 또한 서버 시간은 프로덕션 배포와 스테이징 배포 둘 다에서 사용됩니다.

Cloud9은 IDE 제공에 중점을 두며, Azure에 배포된 후 응용 프로그램을 중지 또는 삭제하는 직접적인 방법을 제공하지 않습니다. Azure에 호스트된 응용 프로그램을 삭제하려면 다음 단계를 따르십시오.

1.  배포 대화 상자에서 **포털 열기** 링크를 클릭하여 Azure 관리 포털을 엽니다.

    ![배포 대화 상자에서 Azure 관리 포털에 연결][배포 대화 상자에서 Azure 관리 포털에 연결]

2.  자격 증명을 사용하여 포털에 로그인합니다.

3.  웹 페이지의 왼쪽에서 **호스팅 서비스, 저장소 계정 및 CDN**을 선택하고 **호스팅 서비스**를 클릭합니다.

4.  **환경** 값에 표시된 스테이징 배포를 선택합니다. 리본 메뉴에서 **삭제**를 클릭하여 응용 프로그램을 삭제합니다.

    ![배포 삭제][배포 삭제]

5.  프로덕션 배포를 선택하고 **삭제**를 클릭하여 해당 응용 프로그램도 삭제합니다.

## 추가 리소스

-   [Cloud9 설명서][Cloud9 설명서]

  [Cloud9 IDE]: http://cloud9ide.com/
  [새 Cloud9 프로젝트 만들기]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_create_project.png
  [새 프로젝트 만들기 대화 상자 Cloud9]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_project.png
  [편집 시작 Cloud9 프로젝트]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_startediting.png
  [Cloud9 프로젝트의 새 파일 만들기]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_filenew.png
  [server.js 파일 저장]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_saveas.png
  [디버거에서 실행]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug.png
  [출력 창]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_output.png
  [브라우저에서 실행 중인 응용 프로그램]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_debug_browser.png
  [create-azure-account]: ../includes/create-azure-account.md
  [게시 설정 다운로드]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_choosetypeandcert.png
  [Azure에 대한 호스팅 서비스 만들기 개요]: http://msdn.microsoft.com/ko-KR/library/windowsazure/jj155995.aspx
  [새 배포 만들기]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createdeployment.png
  [새 호스팅 서비스 만들기]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_new_hosted_service_settings.png
  [배포 선택]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_select_deployment.png
  [web.config 파일을 찾을 수 없음 메시지]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_no_web_config.png
  [가상 컴퓨터 크기를 구성하는 방법]: http://msdn.microsoft.com/ko-KR/library/windowsazure/ee814754.aspx
  [csdef 파일 값 지정]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_createcsdef.png
  [배포 상태]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deployment_status.png
  [Azure 프로덕션 URL 링크]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_url.png
  [스테이징 URL을 표시하는 콘솔 출력]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_staging_console_output.png
  [Azure에서 배포 관리 개요]: http://msdn.microsoft.com/ko-KR/library/windowsazure/gg433027.aspx
  [VIP 교환]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_vipswap.png
  [Azure에서 실행 중인 프로덕션 응용 프로그램]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_production_on_azure.png
  [인스턴스에 연결]: ./media/cloud-services-nodejs-develop-deploy-cloud9/connect.png
  [배포 대화 상자에서 Azure 관리 포털에 연결]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_portal_link.png
  [배포 삭제]: ./media/cloud-services-nodejs-develop-deploy-cloud9/cloud9_deletedeployment.png
  [Cloud9 설명서]: http://go.microsoft.com/fwlink/?LinkId=241421&clcid=0x409
