<properties 
	pageTitle="클라우드 서비스에 대해 SSL 구성(Node.js) - Azure" 
	description="Node.js 웹 역할에 대해 HTTPS 끝점을 설정하고 응용 프로그램 보안을 위해 SSL 인증서를 업로드합니다." 
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
	ms.date="02/20/2015" 
	ms.author="mwasson"/>




# Azure 웹 역할에서 Node.js 응용 프로그램에 대한 SSL 구성

SSL(Secure Socket Layer) 암호화는 인터넷을 통해 전송되는 데이터 보호에 가장 일반적으로 사용되는 방법입니다. 이 일반 작업에서는 웹 역할에서 Azure 클라우드 서비스로 호스트되는 Node.js 응용 프로그램의 HTTPS 끝점을 지정하는 방법과 응용 프로그램을 보호하기 위해 SSL 인증서를 업로드하는 방법에 대해 설명합니다.

> [AZURE.NOTE]이 문서의 단계는 웹 역할에서 Azure 클라우드 서비스로 호스트되는 노드 응용 프로그램에만 적용됩니다. 웹 사이트에 대한 자세한 내용은 [Azure 웹 사이트에 대한 SSL 인증서 구성](../web-sites-configure-ssl-certificate.md)을 참조하세요.

이 작업에는 다음 단계가 포함됩니다.

-   [1단계: Node.js 서비스 만들기 및 클라우드에 서비스 게시]
-   [2단계: SSL 인증서 다운로드]
-   [3단계: SSL 인증서 가져오기]
-   [4단계: 서비스 정의 및 구성 파일 수정]
-   [5단계: HTTPS를 사용하여 역할 인스턴스에 연결]

## <a name="step1"> </a>1단계: Node.js 서비스 만들기 및 클라우드에 서비스 게시

Node.js 응용 프로그램을 Azure 웹 역할에 배포할 때 서버 인증서 및 SSL 연결을 IIS(인터넷 정보 서비스)에서 관리하므로 마치 http 서비스인 것처럼 Node.js 서비스를 작성할 수 있습니다. 다음 단계에 따라 Azure PowerShell을 사용하여 간단한 Node.js 'hello world' 서비스를 만들 수 있습니다.

1. **시작 메뉴** 또는 **시작 화면**에서 **Azure PowerShell**을 검색합니다. 마지막으로, **Azure PowerShell**을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.

	![Azure PowerShell 아이콘][powershell-menu]

[AZURE.INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]


2.  **New-AzureServiceProject** cmdlet을 사용하여 새 서비스 프로젝트를 만듭니다. 

	![][1]

3.  다음과 같이 **Add-AzureNodeWebRole** cmdlet을 사용하여 서비스에 웹 역할을 추가합니다.

    ![][2]

4.  다음과 같이 **Publish-AzureServiceProject** cmdlet을 사용하여 클라우드에 서비스를 게시합니다.

    ![][3]

	> [AZURE.NOTE]이전에 Azure 구독의 게시 설정을 가져오지 않은 경우 게시를 시도할 때 오류가 발생합니다. 구독의 게시 설정 다운로드 및 가져오기에 대한 자세한 내용은 [Node.js에 Azure PowerShell을 사용하는 방법](https://www.windowsazure.com/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)(영문)을 참조하세요.

**Publish-AzureServiceProject** cmdlet에서 반환한 **Created Website URL** 값에는 호스팅 응용 프로그램의 정규화된 도메인 이름이 포함되어 있습니다. 이와 같은 특정 정규화된 도메인 이름의 SSL 인증서를 가져와서 Azure에 배포해야 합니다.

## <a name="step2"> </a>2단계: SSL 인증서 다운로드

응용 프로그램에 대해 SSL을 구성하려면 먼저 이 목적으로 인증서를 발급하는 신뢰할 수 있는 타사 CA(인증 기관)에서 서명한 SSL 인증서를 가져와야 합니다. 아직 없는 경우 SSL 인증서를 판매하는 회사에서 구입해야 합니다.

인증서는 Azure의 SSL 인증서에 대한 다음 요구 사항을 충족해야 합니다.

-   인증서에 개인 키가 포함되어 있어야 합니다.
-   인증서를 키 교환용으로 만들어야 합니다(.pfx 파일).
-   인증서의 주체 이름은 클라우드 서비스 액세스에 사용되는 도메인과 일치해야 합니다. cloudapp.net 도메인용 SSL 인증서를 얻을 수 없으므로 인증서의 주체 이름은 사용 중인 응용 프로그램 액세스에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다. 예를 들면 __mysecuresite.cloudapp.net__입니다.
-   인증서는 최소한 2048비트 암호화를 사용해야 합니다.

## <a name="step3"> </a>3단계: SSL 인증서 가져오기

인증서가 있다면 개발 컴퓨터의 인증서 저장소에 해당 인증서를 설치합니다. 이 인증서는 후속 단계에서 수행한 구성 변경 내용에 따라 응용 프로그램 배포 패키지의 일부로 검색되어 Azure에 업로드됩니다.

> [AZURE.NOTE]이 섹션에서 사용한 단계는 Windows 8 버전의 인증서 가져오기 마법사를 기준으로 합니다. 이전 버전의 Windows를 사용하는 경우 여기에서 나열한 단계가 마법사에 표시된 순서와 일치하지 않을 수 있습니다. 이전 버전의 마법사를 사용하는 경우 인증서 가져오기 마법사를 사용하기 전에 이 섹션을 주의하여 읽어본 후 수행해야 하는 전반적인 작업에 대해 알아보세요.

SSL 인증서를 가져오려면 다음 단계를 따르세요.

1.   Windows 탐색기를 사용하여 인증서가 포함된 **.pfx** 파일이 있는 디렉터리로 이동한 후 인증서를 두 번 클릭합니다. 그러면 인증서 가져오기 마법사가 표시됩니다.
	
	![인증서 마법사][cert-wizard]

2.   **저장소 위치** 섹션에서 **현재 사용자**를 선택한 후 **다음**을 클릭합니다. 그러면 사용자 계정의 인증서 저장소에 인증서가 설치됩니다.

3.   **개인 키 보호** 화면이 나올 때까지 기본값을 그대로 사용하여 마법사를 계속 진행합니다. 여기에서 인증서의 암호(있는 경우)를 입력해야 합니다. 또한 **이 키를 내보낼 수 있도록 표시**도 선택해야 합니다. 마지막으로 **다음**을 클릭합니다.

	![개인 키 보호][key-protection]

4. 인증서 설치가 완료될 때까지 기본값을 그대로 사용하여 마법사를 계속 진행합니다.

이제 설치한 인증서를 참조하도록 서비스 정의를 수정해야 합니다.

## <a name="step4"> </a>4단계: 서비스 정의 및 구성 파일 수정

인증서를 참조하도록 응용 프로그램을 구성하고 HTTPS 끝점을 추가해야 합니다. 따라서 서비스 정의 및 서비스 구성 파일을 업데이트해야 합니다.

1.  서비스 디렉터리에서 서비스 정의 파일(ServiceDefinition.csdef)을 열고 **WebRole** 섹션 내에 **Certificates** 섹션을 추가한 후 인증서에 대한 다음 정보를 포함합니다.

        <WebRole name="WebRole1" vmsize="ExtraSmall">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    storeLocation="LocalMachine" storeName="My" />
            </Certificates>
        ...
        </WebRole>

    **Certificates** 섹션에서는 인증서의 이름, 위치 및 인증서가 위치한 저장소의 이름을 정의합니다. 인증서를 사용자 인증서 저장소에 설치했으므로 "My"라는 값을 사용합니다. 다른 인증서 저장소 위치도 사용할 수 있습니다. 자세한 내용은 [인증서를 서비스와 연결하는 방법]을 참조하세요.

2.  서비스 정의 파일에서 **Endpoints** 섹션 내의 http **InputEndpoint** 요소를 업데이트하여 HTTPS를 사용하도록 설정합니다.

        <WebRole name="WebRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="Endpoint1" protocol="https" 
                    port="443" certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

    서비스 정의 파일에서 필요한 사항은 모두 변경했지만 인증서 정보를 서비스 구성 파일에 추가해야 합니다.

3.  서비스 구성 파일(**ServiceConfiguration.Cloud.cscfg** 및 **ServiceConfiguration.Local.cscfg**)에서 **Role** 섹션 내의 비어 있는 **Certificates** 섹션에 인증서를 추가하고 아래 샘플 지문 값을 사용 중인 인증서의 값으로 바꿉니다.

        <Role name="WebRole1">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

4.  서비스를 다시 게시하여 클라우드에서 서비스 구성을 새로 고칩니다. Azure PowerShell 프롬프트에서 서비스 디렉터리로 이동하여 **Publish-AzureServiceProject**를 입력합니다.

	게시 프로세스 중 참조된 인증서가 로컬 인증서 저장소에서 복사되며 배포 패키지에 포함됩니다.

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

[Azure 작업자 역할에서 Node.js 응용 프로그램에 대한 SSL 구성]

[HTTPS 끝점에서 SSL 인증서를 구성하는 방법]

  [1단계: Node.js 서비스 만들기 및 클라우드에 서비스 게시]: #step1
  [2단계: SSL 인증서 다운로드]: #step2
  [3단계: SSL 인증서 가져오기]: #step3
  [4단계: 서비스 정의 및 구성 파일 수정]: #step4
  [5단계: HTTPS를 사용하여 역할 인스턴스에 연결]: #step5
  [**Azure PowerShell**]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  
  
  
  
  [1]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-01.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-02.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-03.png
  [Azure Management Portal]: http://manage.windowsazure.com
  
  
  [인증서를 서비스와 연결하는 방법]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  
  [site-url]: ./media/cloud-services-nodejs-configure-ssl-certificate/site-url.png
  [8]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-08.png
  [HTTPS 끝점에서 SSL 인증서를 구성하는 방법]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx
  [powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certificate/azure-powershell-start.png
  [cert-wizard]: ./media/cloud-services-nodejs-configure-ssl-certificate/certificateimport.png
  [key-protection]: ./media/cloud-services-nodejs-configure-ssl-certificate/exportable.png
  [Azure 작업자 역할에서 Node.js 응용 프로그램에 대한 SSL 구성]: /develop/nodejs/common-tasks/enable-ssl-worker-role/
 

<!---HONumber=July15_HO3-->