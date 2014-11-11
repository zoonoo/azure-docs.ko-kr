<properties linkid="dev-nodejs-how-to-debug-website" urlDisplayName="Debug Websites (Node)" pageTitle="How to Debug Azure Websites in Node.js" metaKeywords="debug website azure, debugging azure, troubleshooting azure web site, troubleshoot azure website node" description="Learn how to debug an Azure website in Node.js." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="How to debug a Node.js application in Azure Websites" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Azure 웹 사이트에서 Node.js 응용 프로그램을 디버그하는 방법

Azure는 Azure 웹 사이트에서 호스트되는 Node.js 응용 프로그램을 디버그하는 데 도움이 되는 기본 제공 진단 로그를 제공합니다. 이 문서에서는 stdout 및 stderr의 로깅을 사용하고, 브라우저에서 오류 정보를 표시하고, 로그 파일을 다운로드하여 보는 방법에 대해 알아봅니다.

Azure에서 호스트되는 Node.js 응용 프로그램에 대한 진단은 [IISNode][IISNode]에서 제공합니다. 이 문서에서 진단 정보 수집에 대한 가장 일반적인 설정을 논의하긴 하지만 IISNode 작업을 모두 다루지는 않습니다. IISNode 작업에 대한 자세한 내용은 GitHub의 [IISNode 추가 정보][IISNode 추가 정보](영문)를 참조하십시오.

## <span id="enablelogging"></span></a>로깅 사용

기본적으로, Azure 웹 사이트는 배포에 대한 진단 정보만 캡처합니다(예: Git를 사용하여 웹 사이트를 배포하는 경우에). 이 정보는 **package.json**에 참조된 모듈을 설치하거나 사용자 지정 배포 스크립트를 사용하는 경우에 실패가 발생하는 등 배포하는 동안 문제가 발생할 때 유용합니다.

stdout 및 stderr 스트림에 대한 로깅을 사용하려면 Node.js 응용 프로그램의 루트에서 **IISNode.yml** 파일을 만들고 다음을 추가해야 합니다.

    loggingEnabled: true

그러면 Node.js 응용 프로그램에서 stderr 및 stdout에 대한 로깅이 사용됩니다.

**IISNode.yml** 파일을 사용하여 실패가 발생할 때 브라우저로 친숙한 오류를 반환할지 아니면 개발자 오류를 반환할지 여부도 제어할 수 있습니다. 개발자 오류를 사용하려면 **IISNode.yml** 파일에 다음 줄을 추가하십시오.

    devErrorsEnabled: true

이 옵션을 사용하도록 설정하면 IISNode가 친숙한 오류(예: "내부 서버 오류가 발생했습니다.") 대신 stderr에 전송된 정보의 마지막 64K를 반환합니다.

<div class="dev-callout">
<strong>참고</strong>
<p>devErrorsEnabled가 개발 중 문제를 진단하는 데 유용하긴 하지만 프로덕션 환경에서 이 옵션을 사용하도록 설정하면 개발 오류가 최종 사용자에게 전달될 수 있습니다.</p>
</div>

**IISNode.yml** 파일이 응용 프로그램에 아직 없는 경우에는 업데이트된 응용 프로그램을 게시한 후 웹 사이트를 다시 시작해야 합니다. 이전에 게시했던 기존 **IISNode.yml** 파일에서 설정만 변경하는 경우에는 다시 시작하지 않아도 됩니다.

<div class="dev-callout">
<strong>참고</strong>
<p>Azure 명령줄 도구 또는 Azure PowerShell Cmdlet을 사용하여 웹 사이트를 만든 경우 기본 <strong>IISNode.yml</strong> 파일이 자동으로 만들어집니다.</p>
</div>

[Azure 관리 포털][Azure 관리 포털]에서 사이트를 선택한 후 **다시 시작** 단추를 선택하여 웹 사이트를 다시 시작할 수 있습니다.

![다시 시작 단추][다시 시작 단추]

Azure 명령줄 도구가 개발 환경에 설치되어 있는 경우 다음 명령을 사용하여 웹 사이트를 다시 시작할 수 있습니다.

    azure site restart [sitename]

<div class="dev-callout">
<strong>참고</strong>
<p>loggingEnabled 및 devErrorsEnabled가 진단 정보를 캡처하기 위해 가장 일반적으로 사용되는 IISNode.yml 구성 옵션이긴 하지만 IISNode.yml을 사용하여 호스팅 환경을 위한 다양한 옵션을 구성할 수 있습니다. 전체 구성 옵션 목록에 대해서는 <a href="https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml">iisnode_schema.xml</a>(영문) 파일을 참조하십시오.</p>
</div>

## <span id="viewlogs"></span></a>로그 액세스

진단 로그에는 세 가지 방법 즉, FTP(파일 전송 프로토콜) 사용이나 Zip 보관 파일 다운로드를 통해 또는 로그(또는 비상 로그)의 업데이트된 라이브 스트림으로 액세스할 수 있습니다. 로그 파일의 Zip 보관 파일을 다운로드하거나 라이브 스트림을 보려면 Azure 명령줄 도구가 있어야 합니다. 다음 명령을 사용하여 명령줄 도구를 설치할 수 있습니다.

    npm install azure-cli -g

설치한 도구에는 'azure' 명령을 사용하여 액세스할 수 있습니다. 먼저 Azure 구독을 사용하도록 명령줄 도구를 구성해야 합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Azure 명령줄 도구를 사용하는 방법][Azure 명령줄 도구를 사용하는 방법] 문서의 **게시 설정을 다운로드하여 가져오는 방법** 섹션을 참조하세요.

### FTP

FTP를 통해 진단 정보에 액세스하려면 [Azure 포털]을 방문하고, 웹 사이트를 선택한 후 **대시보드**를 선택합니다. **빠른 연결** 섹션에서 **FTP 진단 로그** 및 **FTPS 진단 로그** 링크는 FTP 프로토콜을 사용하여 로그에 액세스할 수 있는 방법을 제공합니다.

<div class="dev-callout">
<strong>참고</strong>
<p>FTP 또는 배포의 사용자 이름 및 암호를 아직 구성하지 않은 경우 <strong>배포 자격 증명 설정</strong>을 선택하여 <strong>빠른 시작</strong> 관리 페이지에서 사용자 이름 및 암호를 구성할 수 있습니다.</p>
</div>

대시보드에 반환된 FTP URL은 다음 하위 디렉터리를 포함하는 **LogFiles** 디렉터리에 대한 것입니다.

-   [배포 방법] - Git와 같은 배포 방법을 사용하는 경우 동일한 이름의 디렉터리가 만들어지고 배포와 관련된 정보가 포함됩니다.

-   nodejs - 응용 프로그램의 모든 인스턴스에서 캡처된 stdout 및 stderr 정보(loggingEnabled가 True인 경우)

### Zip 보관 파일

진단 로그의 Zip 보관 파일을 다운로드하려면 Azure 명령줄 도구에서 다음 명령을 사용하십시오.

    azure site log download [sitename]

현재 디렉터리에 **diagnostics.zip**이 다운로드됩니다. 이 보관 파일은 다음 디렉터리 구조를 포함합니다.

-   deployments - 응용 프로그램의 배포에 대한 정보 로그

-   LogFiles

    -   [배포 방법] - Git와 같은 배포 방법을 사용하는 경우 동일한 이름의 디렉터리가 만들어지고 배포와 관련된 정보가 포함됩니다.

    -   nodejs - 응용 프로그램의 모든 인스턴스에서 캡처된 stdout 및 stderr 정보(loggingEnabled가 True인 경우)

### 라이브 스트림(비상 로그)

진단 로그 정보의 라이브 스트림을 보려면 Azure 명령줄 도구에서 다음 명령을 사용하십시오.

    azure site log tail [sitename]

서버에서 발생하는 업데이트된 로그 이벤트의 스트림이 반환됩니다. 이 스트림은 배포 정보와 stdout 및 stderr 정보를 반환합니다(loggingEnabled가 True인 경우).

## <span id="nextsteps"></span></a>다음 단계

이 문서에서는 Azure에 대한 진단 정보를 사용하고 액세스하는 방법을 알아보았습니다. 이 정보는 응용 프로그램에서 발생하는 문제를 이해하는 데 유용하며, 사용 중인 모듈의 문제를 나타내거나 Azure 웹 사이트에 사용된 Node.js 버전이 배포 환경에 사용된 버전과 다르다는 점을 나타낼 수 있습니다.

Azure에서 모듈 작업에 대한 자세한 내용은 [Azure 응용 프로그램에 Node.js 모듈 사용][Azure 응용 프로그램에 Node.js 모듈 사용]을 참조하십시오.

Node.js 버전의 응용 프로그램 지정에 대한 자세한 내용은 [Azure 응용 프로그램에서 Node.js 버전 지정][Azure 응용 프로그램에서 Node.js 버전 지정]을 참조하십시오.

  [IISNode]: https://github.com/tjanczuk/iisnode
  [IISNode 추가 정보]: https://github.com/tjanczuk/iisnode#readme
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [다시 시작 단추]: ./media/web-sites-nodejs-debug/restartbutton.png
  [Azure 명령줄 도구를 사용하는 방법]: /ko-KR/documentation/articles/xplat-cli/
  [Azure 응용 프로그램에 Node.js 모듈 사용]: /ko-KR/documentation/articles/nodejs-use-node-modules-azure-apps/
  [Azure 응용 프로그램에서 Node.js 버전 지정]: /ko-KR/documentation/articles/nodejs-specify-node-version-azure-apps/
