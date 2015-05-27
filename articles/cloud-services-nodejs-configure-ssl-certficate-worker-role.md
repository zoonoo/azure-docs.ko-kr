<properties 
	pageTitle="클라우드 서비스(Node.js) 작업자 역할에 대해 SSL 구성" 
	description="Azure에서 Node.js 클라우드 서비스 작업자 역할에 대한 SSL 구성" 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="mwasson"/>





# Azure 작업자 역할에서 Node.js 응용 프로그램에 대한 SSL 구성

SSL(Secure Socket Layer) 암호화는 인터넷을 통해 전송되는 데이터 보호에 가장 일반적으로 사용되는 방법입니다. 이 일반 작업에서는 작업자 역할에서 Azure 클라우드 서비스로 호스트되는 Node.js 응용 프로그램의 HTTPS 끝점을 지정하는 방법에 대해 설명합니다.

> [AZURE.NOTE]이 문서의 단계는 작업자 역할에서 Azure 클라우드 서비스로 호스트되는 노드 응용 프로그램에만 적용됩니다.

이 작업에는 다음 단계가 포함됩니다.

-   [1단계: Node.js 서비스 만들기 및 클라우드에 서비스 게시]
-   [2단계: SSL 인증서 다운로드]
-   [3단계: SSL 인증서를 사용하도록 응용 프로그램 수정]
-   [4단계: 서비스 정의 파일 수정]
-   [5단계: HTTPS를 사용하여 역할 인스턴스에 연결]

## <a name="step1"> </a>1단계: Node.js 서비스 만들기 및 클라우드에 서비스 게시

다음 단계에 따라 Azure PowerShell을 사용하여 간단한 Node.js 'hello world' 서비스를 만들 수 있습니다.

1. **시작 메뉴** 또는 **시작 화면**에서 **Azure PowerShell**을 검색합니다. 마지막으로, **Azure PowerShell**을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.

	![Azure PowerShell 아이콘][powershell-menu]

	

2.  **New-AzureServiceProject** cmdlet을 사용하여 새 서비스를 만듭니다.

	![][1]

3.  다음과 같이 **Add-AzureNodeWorkerRole** cmdlet을 사용하여 서비스에 작업자 역할을 추가합니다.

    ![][2]

4.  다음과 같이 **Publish-AzureServiceProject** cmdlet을 사용하여 클라우드에 서비스를 게시합니다.

    ![][3]

	> [AZURE.NOTE]이전에 Azure 구독의 게시 설정을 가져오지 않은 경우 게시를 시도할 때 오류가 발생합니다. 구독의 게시 설정 다운로드 및 가져오기에 대한 자세한 내용은 [Node.js에 Azure PowerShell을 사용하는 방법](https://www.windowsazure.com/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)(영문)을 참조하세요.

**Publish-AzureServiceProject** cmdlet에서 반환한 **Created Website URL** 값에는 호스팅 응용 프로그램의 정규화된 도메인 이름이 포함되어 있습니다. 이와 같은 특정 정규화된 도메인 이름의 SSL 인증서를 가져와서 Azure에 배포해야 합니다.

## <a name="step2"> </a>2단계: SSL 인증서 다운로드

응용 프로그램에 대해 SSL을 구성하려면 먼저 이 목적으로 인증서를 발급하는 신뢰할 수 있는 타사 CA(인증 기관)에서 서명한 SSL 인증서를 가져와야 합니다. 아직 없는 경우 SSL 인증서를 판매하는 회사에서 구입해야 합니다.

인증서는 Azure의 SSL 인증서에 대한 다음 요구 사항을 충족해야 합니다.

-   인증서에 개인 키가 포함되어 있어야 합니다.
-   인증서를 키 교환용으로 만들어야 합니다(**.pfx** 파일).
-   인증서의 주체 이름은 클라우드 서비스 액세스에 사용되는 도메인과 일치해야 합니다. cloudapp.net 도메인용 SSL 인증서를 얻을 수 없으므로 인증서의 주체 이름은 사용 중인 응용 프로그램 액세스에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다. 예를 들면 __mysecuresite.cloudapp.net__입니다.
-   인증서는 최소한 2048비트 암호화를 사용해야 합니다.

다음 단계에서는 인증서를 포함하는 **.pfx** 파일을 서비스 프로젝트에 추가하고 Azure에 배포합니다.

## <a name="step3"> </a>3단계: SSL 인증서를 사용하도록 응용 프로그램 수정

작업자 역할에 Node.js 응용 프로그램을 배포한 경우 Node.exe에서 서버 인증서와 SSL 연결을 관리합니다. SSL 트래픽을 처리하기 위해 'http' 대신 'https' 모듈을 사용해야 합니다. 다음 단계를 수행하여 프로젝트에 SSL 인증서를 추가한 후 이 인증서를 사용하도록 응용 프로그램을 수정합니다.

1.   응용 프로그램이 포함된 디렉터리에 CA(인증 기관)에서 제공한 **.pfx** 파일을 저장합니다. 예를 들어 이 문서에서 사용하는 응용 프로그램이 포함된 디렉터리는 **c:\\node\\securesite\\workerrole1**입니다.

2.   Notepad.exe를 사용하여 **c:\\node\\securesite\\workerrole1\\server.js** 파일을 열고 파일의 내용을 다음으로 바꿉니다.

		var https = require('https');
		var fs = require('fs');

		var options = {
			pfx: fs.readFileSync('certificate.pfx'),
			passphrase: "password"
		};
		var port = process.env.PORT || 8000;
		https.createServer(options, function (req, res) {
 		    res.writeHead(200, { 'Content-Type': 'text/plain' });
		    res.end('Hello World\n');
		}).listen(port);

	> [AZURE.IMPORTANT]'certificate.pfx'를 인증서 파일 이름으로 바꾸고, "password"를 인증서 파일의 암호(있는 경우)로 바꿔야 합니다.

3.   **server.js** 파일을 저장합니다.

**server.js** 파일을 수정하면 응용 프로그램이 Azure에 배포될 때 포트 443(SSL 통신의 표준 포트)에서 통신을 수신 대기합니다. **.pfx** 파일은 이 전송을 통해 SSL 통신을 구현하는 데 사용됩니다.

## <a name="step4"> </a>4단계: 서비스 정의 파일 수정

이제 응용 프로그램에서 포트 443을 통해 수신 대기하므로 이 포트를 통해 통신하도록 서비스 정의도 수정해야 합니다.

1.  서비스 디렉터리에서 서비스 정의 파일(**ServiceDefinition.csdef**)을 열고,포트 443을 통해 통신할 수 있도록 다음과 같이 **끝점** 섹션에 있는 http **InputEndpoint** 요소를 업데이트합니다.

        <WorkerRole name="WorkerRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpIn" protocol="tcp" 
                    port="443" />
            </Endpoints>
        ...
        </WorkerRole>

	이렇게 변경한 후 **ServiceDefinition.csdef** 파일을 저장합니다.

4.  서비스를 다시 게시하여 클라우드에서 업데이트한 구성을 새로 고칩니다. Azure PowerShell 프롬프트에서 서비스 디렉터리로 이동하여 **Publish-AzureServiceProject**를 입력합니다.

## <a name="step5"> </a>5단계: HTTPS를 사용하여 역할 인스턴스에 연결

이제 Azure에서 배포가 실행되고 있으므로 HTTPS를 사용하여 연결할 수 있습니다.

1.  관리 포털에서 클라우드 서비스를 선택한 후 **대시보드**를 클릭합니다.

2. 아래로 스크롤하고 다음과 같이 **사이트 URL**로 표시된 링크를 클릭합니다.

    ![사이트 URL][site-url]

	> [AZURE.IMPORTANT]포털에 표시된 사이트 URL에서 HTTPS를 지정하지 않은 경우에는 브라우저에서 HTTP 대신 HTTPS를 사용하여 수동으로 URL을 입력해야 합니다.

3.  새 브라우저가 열리고 웹 사이트를 표시합니다.

    브라우저에서 HTTPS 연결을 사용하고 있음을 나타내는 잠금 아이콘이 표시됩니다. 또한 이러한 잠금 아이콘은 응용 프로그램이 SSL에 대해 올바르게 구성되었다는 것도 의미합니다.

    ![][8]

## 추가 리소스

[인증서를 서비스와 연결하는 방법]

[Azure 웹 역할에서 Node.js 응용 프로그램에 대한 SSL 구성]

[HTTPS 끝점에서 SSL 인증서를 구성하는 방법]

[1단계: Node.js 서비스 만들기 및 클라우드에 서비스 게시]: #step1
[2단계: SSL 인증서 다운로드]: #step2
[3단계: SSL 인증서를 사용하도록 응용 프로그램 수정]: #step3
[4단계: 서비스 정의 파일 수정]: #step4
[5단계: HTTPS를 사용하여 역할 인스턴스에 연결]: #step5
[**Azure PowerShell**]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
[1]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-01.png
[2]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-02-worker.png
[3]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-03-worker.png
[Azure Management Portal]: http://manage.windowsazure.com
[인증서를 서비스와 연결하는 방법]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
[site-url]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/site-url.png
[8]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-08.png
[HTTPS 끝점에서 SSL 인증서를 구성하는 방법]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx
[powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/azure-powershell-start.png
[Azure 웹 역할에서 Node.js 응용 프로그램에 대한 SSL 구성]: /develop/nodejs/common-tasks/enable-ssl/

<!--HONumber=54-->