#Azure 앱 서비스에서 웹앱에 대한 HTTPS를 사용하도록 설정

> [AZURE.NOTE]새로운 Azure의 [안내 방식 연습](http://support.microsoft.com/kb/2990804)을 사용하면 작업을 보다 빠르게 수행할 수 있습니다. 사용자 지정 도메인 이름을 연결하고 SSL을 사용하여 Azure 클라우드 서비스나 [앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714)와의 통신을 보호하는 작업을 매우 쉽게 완료할 수 있습니다.

SSL(Secure Socket Layer) 암호화를 사용하는 HTTPS를 통해 웹앱과 브라우저 간의 통신에 대한 보안을 설정할 수 있습니다. 인터넷을 통해 전송되는 데이터의 보안을 설정하는 가장 일반적인 방법으로, 방문자에게 앱과의 트랜잭션이 안전함을 보장합니다. 이 문서에서는 Azure 앱 서비스에서 웹앱에 대한 HTTPS를 구성하는 방법을 설명합니다. 이 문서는 클라이언트 인증서 인증을 다루지 않습니다. 이에 대한 정보는 [웹 앱용 TLS 상호 인증 구성 방법](../articles/app-service-web/app-service-web-configure-tls-mutual-auth.md)을 참조하십시오.

##<a name="bkmk_azurewebsites"></a>\*.azurewebsites.net 도메인에 대한 HTTPS

사용자 지정 도메인 이름을 사용하지 않고 Azure에서 웹 사이트에 할당한 \*.azurewebsites.net 도메인(예: contoso.azurewebsites.net)을 사용하려는 경우 HTTPS는 Microsoft에서 제공한 인증서로 사이트에서 이미 사용하도록 설정되었습니다. **https://mywebsite.azurewebsites.net**을 사용하여 앱에 액세스할 수 있습니다. 그러나 \*.azurewebsites.net은 와일드카드 도메인입니다. [모든 와일드카드 도메인](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/)과 마찬가지로, 자체 인증서로 사용자 지정 도메인을 사용하는 것만큼 안전하지 않습니다. 

이 문서의 나머지 부분은 **contoso.com**, **www.contoso.com** 또는 **\*.contoso.com**과 같은 사용자 지정 도메인에 대한 HTTPS를 사용하기 위한 세부 정보를 제공합니다.

##<a name="bkmk_domainname"></a>사용자 지정 도메인에 대해 SSL 사용

**contoso.com** 등의 사용자 지정 도메인에 HTTPS를 사용하도록 설정하려면 먼저 도메인 이름 등록 기관에 사용자 지정 도메인 이름을 등록해야 합니다. 웹앱의 도메인 이름을 구성하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성](/ko-kr/develop/net/common-tasks/custom-dns-web-site/)을 참조하세요. 사용자 지정 도메인 이름을 등록하고 사용자 지정 이름에 응답하도록 웹앱을 구성한 후에는 도메인에 대한 SSL 인증서를 요청해야 합니다.

> [AZURE.NOTE]사용자 지정 도메인 이름에 HTTPS를 사용하도록 설정하려면 **표준** 모드로 웹앱을 구성해야 합니다. 현재 무료 또는 공유 모드를 사용 중이면 추가 비용이 발생할 수도 있습니다. 공유 및 **표준** 가격 책정에 대한 자세한 내용은 [가격 정보][pricing]를 참조하세요.

##<a name="bkmk_getcert"></a>SSL 인증서 가져오기

SSL 인증서를 요청하기 전에 먼저 인증서로 보안을 설정할 도메인 이름을 결정해야 합니다. 받아야 하는 인증서 유형이 결정됩니다. **contoso.com** 또는 **www.contoso.com**과 같은 단일 도메인 이름의 보안을 설정해야 하는 경우 기본 인증서로 충분합니다. **contoso.com**, **www.contoso.com** 및 **mail.contoso.com**과 같은 여러 도메인 이름의 보안을 설정해야 하는 경우, [와일드카드 인증서](http://en.wikipedia.org/wiki/Wildcard_certificate) 또는 [주체 대체 이름](http://en.wikipedia.org/wiki/SubjectAltName)(subjectAltName)이 포함된 인증서를 가져올 수 있습니다.

웹앱에 사용되는 SSL 인증서는 CA([Certificate Authority](http://en.wikipedia.org/wiki/Certificate_authority))에서 서명되어야 합니다. 아직 없는 경우 SSL 인증서를 발급하는 회사에서 구입해야 합니다. 인증 기관 목록은 Microsoft TechNet Wiki의 [Windows 및 Windows Phone 8 SSL 루트 인증서 프로그램(멤버 CA)][cas]을 참조하세요.

인증서는 Azure의 SSL 인증서에 대한 다음 요구 사항을 충족해야 합니다.

* 인증서에 개인 키가 포함되어 있어야 합니다.
* 개인 정보 교환(.pfx) 파일로 내보낼 수 있는 키 교환용 인증서를 만들어야 합니다.
* 인증서의 주체 이름은 웹 사이트 액세스에 사용되는 도메인과 일치해야 합니다. 이 인증서로 여러 도메인을 처리해야 하는 경우 와일드카드 값을 사용하거나 앞에서 설명한 대로 subjectAltName 값을 지정해야 합니다.
* 인증서는 최소한 2048비트 암호화를 사용해야 합니다.
* 개인 CA 서버에서 발급된 인증서는 Azure 앱 서비스에서 지원되지 않습니다.

Azure 웹 사이트에서 사용할 SSL 인증서를 받으려면 인증 기관에 CSR(인증서 서명 요청)을 제출하고 다시 받는 인증서에서 .pfx 파일을 생성합니다. 선택한 도구로 이 작업을 수행할 수 있습니다. 인증서를 받는 몇 가지 일반적인 방법은 다음과 같습니다.

- [Certreq.exe를 사용하여 인증서 가져오기](#bkmk_certreq)
- [IIS 관리자를 사용하여 인증서 가져오기](#bkmk_iismgr)
- [OpenSSL을 사용하여 인증서 가져오기](#bkmk_openssl)
- [OpenSSL을 사용하여 SubjectAltName 인증서 가져오기](#bkmk_subjectaltname)
- [자체 서명된 인증서 생성(테스트 전용)](#bkmk_selfsigned) 

> [AZURE.NOTE]다음 단계에서 **일반 이름**(예: `www.contoso.com`)을 입력하라는 메시지가 표시됩니다. 와일드카드 인증서의 경우 이 값은 \*.domainname(예: \*.contoso.com)이어야 합니다. \*.contoso.com과 같은 와일드카드 이름과 contoso.com과 같은 루트 도메인 이름을 둘 다 지원해야 하는 경우 와일드카드 subjectAltName 인증서를 사용할 수 있습니다.
>
> ECC(Elliptic Curve Cryptography) 인증서는 Azure 앱 서비스에서 지원되지만 상대적으로 새로운 인증서이며 CSR을 만드는 것과 정확히 동일한 단계로 CA에 대해 작업해야 합니다.

CA에서 사용되는 경우 **[중간 인증서](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)**(체인 인증서라고도 함)를 가져와야 할 수도 있습니다. 중간 인증서 사용은 '체인화되지 않은 인증서'보다 더 안전한 것으로 간주되므로 CA에서 중간 인증서를 사용하는 것이 일반적입니다. 중간 인증서는 CA 웹 사이트에서 개별 다운로드로 제공되는 경우가 많습니다. 이 문서에서는 중간 인증서가 웹앱에 업로드된 인증서와 병합되도록 하는 단계를 제공합니다.

<a name="bkmk_certreq"></a>
###Certreq.exe를 사용하여 인증서 받기(Windows에만 해당)

Certreq.exe는 인증서 요청을 만들기 위한 Windows 유틸리티입니다. 이 유틸리티는 Windows XP/Windows Server 2000 이후 기본 Windows 설치의 일부였으므로 최근 Windows 시스템에서 사용할 수 있습니다. certreq.exe를 사용하여 SSL 인증서를 받으려면 다음 단계를 따르세요.

1. **메모장**을 열고 다음이 포함된 새 문서를 만듭니다. 제목 줄의 **mysite.com**을 웹앱의 사용자 지정 도메인 이름으로 바꿉니다. 예를 들어 Subject = "CN=www.contoso.com"입니다.

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		RequestType = CMC

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	위에서 지정된 옵션 및 사용 가능한 기타 옵션에 대한 자세한 내용은 [Certreq 참조 설명서](http://technet.microsoft.com/library/cc725793.aspx)를 참조하세요.

2. 텍스트 파일을 **myrequest.txt**로 저장합니다.

3. **시작 화면** 또는 **시작 메뉴**에서 **cmd.exe**를 실행합니다.

4. 명령 프롬프트에서 다음 명령을 사용하여 인증서 요청 파일을 만듭니다.

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	1단계에서 만든 **myrequest.txt** 파일의 경로 및 **myrequest.csr** 파일을 만들 때 사용할 경로를 지정합니다.

5. 인증 기관에 **myrequest.csr**을 제출하여 SSL 인증서를 받습니다. 이 경우 파일을 업로드하거나 메모장에서 파일을 열고 파일 내용을 웹 양식에 직접 붙여 넣어야 합니다.

	인증 기관 목록은 Microsoft TechNet Wiki의 [Windows 및 Windows Phone 8 SSL 루트 인증서 프로그램(멤버 CA)][cas]을 참조하세요.

6. 인증 기관에서 인증서(.CER) 파일을 제공하면 요청을 생성하는 데 사용되는 컴퓨터에 이 파일을 저장한 후 다음 명령을 사용하여 요청을 수락하고 인증서 생성 프로세스를 완료합니다.

		certreq -accept -user mycert.cer

	이 경우 인증 기관에서 받은 **mycert.cer** 인증서를 사용하여 인증서의 서명을 완료합니다. 아무 파일도 만들어지지 않고, 대신 인증서가 Windows 인증서 저장소에 저장됩니다.

6. CA에서 중간 인증서를 사용하는 경우 다음 단계에서 인증서를 내보내기 전에 이러한 인증서를 설치해야 합니다. 일반적으로 이 인증서는 CA에서 개별 다운로드로 제공되며, 각 웹 서버 유형에 따라 여러 형식으로 제공됩니다. Microsoft IIS용으로 제공된 버전을 선택합니다.

	인증서를 다운로드한 후 탐색기에서 인증서를 마우스 오른쪽 단추로 클릭하고 **인증서 설치**를 선택합니다. **인증서 가져오기 마법사**에서 기본값을 사용하고 가져오기가 완료될 때까지 **다음**을 계속 선택합니다.

7. 인증서 저장소에서 인증서를 내보내려면 **시작 화면** 또는 **시작 메뉴**에서 **certmgr.msc**를 실행합니다. **인증서 관리자**가 나타나면 **개인** 폴더를 확장하고 **인증서**를 선택합니다. **발급 대상** 필드에서 인증서를 요청한 사용자 지정 도메인 이름을 가진 항목을 찾습니다. **발급자** 필드에 이 인증서에 사용한 인증 기관에 표시되어야 합니다.

	![여기에 인증서 관리자 이미지 삽입][certmgr]

9. 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 선택한 후 **내보내기**를 선택합니다. **인증서 내보내기 마법사**에서 **다음**을 클릭하고 **예, 개인 키를 내보냅니다.**를 선택합니다. **다음**을 클릭합니다.

	![개인 키 내보내기][certwiz1]

10. **개인 정보 교환 - PKCS #12**, **Include all certificates in the certificate chain** 및 **확장된 속성 모두 내보내기**를 선택합니다. **다음**을 클릭합니다.

	![모든 인증서 및 확장 속성 포함][certwiz2]

11. **암호**를 선택한 후 암호를 입력하고 확인합니다. **다음**을 클릭합니다.

	![암호 지정][certwiz3]

12. 내보낸 인증서가 포함될 경로와 파일 이름을 제공합니다. 파일 이름은 확장명이 **.pfx**여야 합니다. **다음**을 클릭하여 프로세스를 완료합니다.

	![파일 경로 제공][certwiz4]

이제 내보낸 PFX 파일을 Azure 웹앱에 업로드할 수 있습니다.

<a name="bkmk_openssl"></a>
###OpenSSL을 사용하여 인증서 받기

1. 명령줄, bash 또는 터미널 세션에서 다음을 사용하여 개인 키와 인증서 서명 요청을 생성합니다.

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. 메시지가 표시되면 해당 정보를 입력합니다. 예를 들면 다음과 같습니다.

 		Country Name (2 letter code) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Please enter the following 'extra' attributes to be sent with your certificate request

       	A challenge password []: 

	이 프로세스를 완료하면 두 개의 파일인 **myserver.key** 및 **server.csr**이 만들어집니다. **server.csr**에는 인증서 서명 요청이 포함되어 있습니다.

3. 인증 기관에 CSR을 제출하여 SSL 인증서를 받습니다. 인증 기관 목록은 Microsoft TechNet Wiki의 [Windows 및 Windows Phone 8 SSL 루트 인증서 프로그램(멤버 CA)][cas]을 참조하세요.

4. CA에서 인증서를 받은 후 **myserver.crt**라는 파일에 저장합니다. CA에서 인증서를 텍스트 형식으로 제공한 경우 인증서 텍스트를 **myserver.crt** 파일에 붙여 넣습니다. 텍스트 편집기에서 볼 때 파일 내용은 다음과 유사해야 합니다.

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	파일을 저장합니다.

5. 명령줄, Bash 또는 터미널 세션에서 다음 명령을 사용하여 **myserver.key** 및 **myserver.crt**를 Azure 앱 서비스에 필요한 형식인 **myserver.pfx**로 변환합니다.

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	메시지가 표시되면 암호를 입력하여 .pfx 파일의 보안을 설정합니다.

	> [AZURE.NOTE]CA에서 중간 인증서를 사용하는 경우 다음 단계에서 인증서를 내보내기 전에 이러한 인증서를 설치해야 합니다. 일반적으로 이 인증서는 CA에서 개별 다운로드로 제공되며, 각 웹 서버 유형에 따라 여러 형식으로 제공됩니다. PEM 파일(.pem 파일 확장명)로 제공된 버전을 선택합니다.
	> 
	> 다음 명령은 **intermediate-cets.pem** 파일에 포함된 중간 인증서를 포함하는 .pfx 파일을 만드는 방법을 보여 줍니다.
	>
	>
	`````
	openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	`````

	이 명령을 실행하면 Azure 앱 서비스에 사용하기 적합한 **myserver.pfx** 파일이 만들어집니다.

###<a name="bkmk_iismgr"></a>IIS 관리자를 사용하여 인증서 가져오기

IIS 관리자에 익숙한 경우 IIS 관리자를 통해 Azure 앱 서비스에 사용할 수 있는 인증서를 생성할 수 있습니다.

1. IIS 관리자로 인증 기관에 보낼 CSR을 생성합니다. CSR 생성에 대한 자세한 내용은 [인터넷 서버 인증서 요청(IIS 7)][iiscsr]을 참조하세요.

2. 인증 기관에 CSR을 제출하여 SSL 인증서를 받습니다. 인증 기관 목록은 Microsoft TechNet Wiki의 [Windows 및 Windows Phone 8 SSL 루트 인증서 프로그램(멤버 CA)][cas]을 참조하세요.

3. 인증 기관 공급업체에서 제공한 인증서를 사용하여 CSR을 완료합니다. CSR 완료에 대한 자세한 내용은 [인터넷 서버 인증서 설치(IIS 7)][installcertiis]를 참조하세요.

4. CA에서 중간 인증서를 사용하는 경우 다음 단계에서 인증서를 내보내기 전에 이러한 인증서를 설치해야 합니다. 일반적으로 이 인증서는 CA에서 개별 다운로드로 제공되며, 각 웹 서버 유형에 따라 여러 형식으로 제공됩니다. Microsoft IIS용으로 제공된 버전을 선택합니다.

	인증서를 다운로드한 후 탐색기에서 인증서를 마우스 오른쪽 단추로 클릭하고 **인증서 설치**를 선택합니다. **인증서 가져오기 마법사**에서 기본값을 사용하고 가져오기가 완료될 때까지 **다음**을 계속 선택합니다.

4. IIS 관리자에서 인증서를 내보냅니다. 인증서 내보내기에 대한 자세한 내용은 [서버 인증서 내보내기(IIS 7)][exportcertiis]를 참조하세요. 내보낸 파일은 Azure 웹앱에 사용하기 위해 다음 단계에서 Azure로 업로드됩니다.

	> [AZURE.NOTE]내보내는 동안 <strong>예, 개인 키를 내보냅니다.</strong> 옵션을 선택해야 합니다. 여기에는 내보낸 인증서의 개인 키가 포함됩니다.

	> [AZURE.NOTE]내보내는 동안 **인증 경로에 있는 인증서 모두 포함** 및 **확장된 속성 모두 내보내기** 옵션을 선택해야 합니다. 여기에는 내보낸 인증서의 중간 인증서가 모두 포함됩니다.


###<a name="bkmk_subjectaltname"></a>OpenSSL을 사용하여 SubjectAltName 인증서 가져오기

OpenSSL을 사용하면 SubjectAltName 확장을 사용하여 단일 인증서로 여러 도메인 이름을 지원하는 인증서 요청을 만들 수 있지만 구성 파일이 필요합니다. 다음 단계에서는 구성 파일을 만든 다음 이 파일을 사용하여 인증서를 요청하는 과정을 안내합니다.

1. __sancert.cnf__라는 새 파일을 만들고 다음을 파일 내용으로 사용합니다.
 
		# -------------- BEGIN custom sancert.cnf -----
		HOME = .
		oid_section = new_oids
		[ new_oids ]
		[ req ]
		default_days = 730
		distinguished_name = req_distinguished_name
		encrypt_key = no
		string_mask = nombstr
		req_extensions = v3_req # Extensions to add to certificate request
		[ req_distinguished_name ]
		countryName = Country Name (2 letter code)
		countryName_default = 
		stateOrProvinceName = State or Province Name (full name)
		stateOrProvinceName_default = 
		localityName = Locality Name (eg, city)
		localityName_default = 
		organizationalUnitName  = Organizational Unit Name (eg, section)
		organizationalUnitName_default  = 
		commonName              = Your common name (eg, domain name)
		commonName_default      = www.mydomain.com
		commonName_max = 64
		[ v3_req ]
		subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
		# -------------- END custom sancert.cnf -----

	'subjectAltName'으로 시작되는 줄을 확인합니다. 현재 나열된 도메인 이름을 일반 이름 외에 지원하려는 도메인 이름으로 바꿉니다. 예를 들면 다음과 같습니다.

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	commonName\_default 필드를 변경할 필요는 없습니다. 다음 단계 중 하나에서 일반 이름을 입력하라는 메시지가 표시되기 때문입니다.

2. __sancert.cnf__ 파일을 저장합니다.

1. sancert.cnf 구성 파일을 사용하여 개인 키와 인증서 서명 요청을 생성합니다. bash 또는 터미널 세션에서 다음 명령을 사용합니다.

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. 메시지가 표시되면 해당 정보를 입력합니다. 예를 들면 다음과 같습니다.

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
 

	이 프로세스를 완료하면 두 개의 파일인 **myserver.key** 및 **server.csr**이 만들어집니다. **server.csr**에는 인증서 서명 요청이 포함되어 있습니다.

3. 인증 기관에 CSR을 제출하여 SSL 인증서를 받습니다. 인증 기관 목록은 Microsoft TechNet Wiki의 [Windows 및 Windows Phone 8 SSL 루트 인증서 프로그램(멤버 CA)][cas]을 참조하세요.

4. CA에서 인증서를 받은 후 **myserver.crt**라는 파일에 저장합니다. CA에서 인증서를 텍스트 형식으로 제공한 경우 인증서 텍스트를 **myserver.crt** 파일에 붙여 넣습니다. 텍스트 편집기에서 볼 때 파일 내용은 다음과 유사해야 합니다.

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	파일을 저장합니다.

5. 명령줄, Bash 또는 터미널 세션에서 다음 명령을 사용하여 **myserver.key** 및 **myserver.crt**를 Azure 앱 서비스에 필요한 형식인 **myserver.pfx**로 변환합니다.

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	메시지가 표시되면 암호를 입력하여 .pfx 파일의 보안을 설정합니다.

	> [AZURE.NOTE]CA에서 중간 인증서를 사용하는 경우 다음 단계에서 인증서를 내보내기 전에 이러한 인증서를 설치해야 합니다. 일반적으로 이 인증서는 CA에서 개별 다운로드로 제공되며, 각 웹 서버 유형에 따라 여러 형식으로 제공됩니다. PEM 파일(.pem 파일 확장명)로 제공된 버전을 선택합니다.
	> 
	> 다음 명령은 **intermediate-cets.pem** 파일에 포함된 중간 인증서를 포함하는 .pfx 파일을 만드는 방법을 보여 줍니다.
	>
	> 
	`````
	openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	`````

	이 명령을 실행하면 Azure 앱 서비스에 사용하기 적합한 **myserver.pfx** 파일이 만들어집니다.


###<a name="bkmk_selfsigned"></a>자체 서명된 인증서 생성(테스트 전용)

테스트를 위해 인증서를 받고 프로덕션으로 진행할 때까지 신뢰할 수 있는 CA로부터 인증서 구입을 지연하려는 경우도 있습니다. 자체 서명된 인증서로 이 간격을 채울 수 있습니다. 자체 서명된 인증서는 인증 기관인 것처럼 직접 만들고 서명하는 인증서입니다. 이 인증서를 사용하여 웹 사이트의 보안을 설정할 수 있지만 인증서가 신뢰할 수 있는 CA에서 서명되지 않았기 때문에 사이트를 방문할 때 대부분의 브라우저에서 오류를 반환합니다. 일부 브라우저는 사이트 보기를 허용하지 않을 수도 있습니다.

- [makecert를 사용하여 자체 서명된 인증서 생성](#bkmk_ssmakecert)
- [OpenSSL을 사용하여 자체 서명된 인증서 생성](#bkmk_ssopenssl)

<a name="bkmk_ssmakecert"></a>
#### makecert를 사용하여 자체 서명된 인증서 생성 ####

다음 단계를 수행하여 Visual Studio가 설치되어 있는 Windows 시스템에서 테스트 인증서를 만들 수 있습니다.

1. **시작 메뉴** 또는 **시작 화면**에서 **개발자 명령 프롬프트**를 검색합니다. 마지막으로, **개발자 명령 프롬프트**를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.

	사용자 계정 컨트롤 대화 상자가 표시되면 **예**를 선택하여 계속합니다.

2. 개발자 명령 프롬프트에서 다음 명령을 사용하여 자체 서명된 인증서를 새로 만듭니다. **serverdnsname**을 웹앱의 DNS로 대체해야 합니다.

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	이 명령은 01/01/2013과 01/01/2014 사이의 날짜에 유효한 인증서를 만들고 CurrentUser 인증서 저장소에 위치를 저장합니다.

3. **시작 메뉴** 또는 **시작 화면**에서 **Windows PowerShell**을 검색하고 이 응용 프로그램을 시작합니다.

4. Windows PowerShell 프롬프트에서 다음 명령을 사용하여 이전에 만든 인증서를 내보냅니다.

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	이 명령은 지정한 암호를 $mypwd에 보안 문자열로 저장한 다음 **dnsname** 매개 변수에 지정된 DNS 이름을 사용하여 인증서를 찾고 **filepath** 매개 변수에 지정된 파일로 내보냅니다. 암호가 포함된 보안 문자열은 내보낸 파일의 보안을 설정하는 데 사용됩니다.

<a name="bkmk_ssopenssl"></a>
####OpenSSL을 사용하여 자체 서명된 인증서 생성 ####

1. **serverauth.cnf**라는 새 문서를 만들고 다음을 이 파일 내용으로 사용합니다.

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your app's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

	이 코드는 Azure 앱 서비스에서 사용할 수 있는 SSL 인증서를 생성하는 데 필요한 구성 설정을 지정합니다.

2. 명령줄, bash 또는 터미널 세션에서 다음을 사용하여 자체 서명된 인증서를 새로 생성합니다.

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	이 코드는 **serverauth.cnf** 파일에 지정된 구성 설정을 사용하여 새 인증서를 만듭니다.

3. 웹앱에 업로드할 수 있는 .PFX 파일로 인증서를 내보내려면 다음 명령을 사용합니다.

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	메시지가 표시되면 암호를 입력하여 .pfx 파일의 보안을 설정합니다.

	이 명령으로 생성된 **myserver.pfx**를 사용하여 테스트를 위해 웹앱의 보안을 설정할 수 있습니다.

<a name="bkmk_standardmode"></a><h2>표준 모드 구성</h2>

사용자 지정 도메인에 HTTPS를 사용하도록 설정하는 기능은 웹앱의 **표준** 모드에만 사용할 수 있습니다. **표준** 모드로 전환하려면 다음 단계를 따르세요.

> [AZURE.NOTE]**무료** 모드에서 **표준** 모드로 웹앱을 전환하기 전에, 웹앱 구독에 대한 지출 한도를 제거해야 합니다. 그렇지 않으면 대금 청구 기간이 끝나기 전에 한도에 도달할 경우 사이트를 사용할 수 없게 됩니다. 공유 및 **표준** 모드 가격 책정에 대한 자세한 내용은 [가격 정보][pricing]를 참조하세요.

1.	브라우저에서 [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)을 엽니다.
2.	페이지의 왼쪽에서 **찾아보기** 옵션을 클릭합니다.
3.	**웹앱** 블레이드를 클릭합니다.
4.	웹앱의 이름을 클릭 합니다.
5.	**필수** 페이지에서 **설정**을 클릭합니다.
6.	**크기 조정** 
	![크기 조정 탭][scale]을 클릭합니다.
7.	**크기 조정** 섹션에서 **선택**을 클릭하여 앱 서비스 계획 모드를 설정합니다. 
	![가격 책정 계층][sslreserved]

	> [AZURE.NOTE]"웹앱 '&lt;앱 이름&gt;'에 대한 크기 구성' 실패" 오류가 표시되면 자세히 단추를 사용하여 추가 정보를 확인할 수 있습니다. "사용 가능한 표준 인스턴스 서버가 부족하여 이 요청을 충족할 수 없습니다." 오류가 표시될 수도 있습니다. 이 오류가 표시되면 [Azure 지원](/support/options/)으로 문의하세요.


##<a name="bkmk_configuressl"></a>SSL 구성

이 섹션의 단계를 수행하기 전에 사용자 지정 도메인 이름을 웹앱과 연결한 상태여야 합니다. 자세한 내용은 [웹앱에 대한 사용자 지정 도메인 이름 구성][customdomain]을 참조하세요.

1.	브라우저에서 [Azure 관리 포털](https://portal.azure.com)을 엽니다.
2.	페이지의 왼쪽에서 **찾아보기** 옵션을 클릭합니다.
3.	**웹앱** 블레이드를 클릭합니다.
4.	웹앱의 이름을 클릭 합니다.
5.	**필수** 페이지에서 **설정**을 클릭합니다.	
6.	**사용자 지정 도메인 및 SSL**을 클릭합니다. 
	![구성 탭][sslconfig]
7.	**인증서** 섹션에서 **업로드**를 클릭합니다.
8.	**Upload a certificate** 대화 상자에서 IIS 관리자 또는 OpenSSL을 사용하여 앞에서 만든 .pfx 인증서 파일을 선택합니다. .pfx 파일의 보안을 설정하는 데 사용된 암호(있는 경우)를 지정합니다. 끝으로, **저장**을 클릭하여 인증서를 업로드합니다. 
	![ssl 업로드][ssluploadcert]
9. **SSL 설정** 탭의 **ssl 바인딩** 섹션에서 드롭다운을 사용하여 SSL로 보안을 설정할 도메인 이름과 사용할 인증서를 선택합니다. SNI([서버 이름 표시][sni])를 사용할지 또는 IP 기반 SSL을 사용할지 선택할 수도 있습니다.

	![SSL 바인딩][sslbindings]
	
	* IP 기반 SSL은 서버의 전용 공용 IP 주소를 도메인 이름에 매핑하여 인증서를 도메인 이름과 연결합니다. 이렇게 하려면 서비스와 연결된 각 도메인 이름(contoso.com, fabricam.com 등)에 전용 IP 주소가 있어야 합니다. 이 방법은 SSL 인증서를 웹 서버와 연결하는 일반적인 방법입니다.

	* SNI 기반 SSL은 SSL 및 TLS([전송 계층 보안][tls])에 대한 확장으로, 각 도메인에 별도의 보안 인증서를 사용하여 여러 도메인이 동일한 IP 주소를 공유할 수 있게 해 줍니다. 대부분의 최신 브라우저(Internet Explorer, Chrome, Firefox 및 Opera 포함)는 SNI를 지원하지만 이전 브라우저는 SNI를 지원하지 않을 수도 있습니다. SNI에 대한 자세한 내용은 Wikipedia의 [서버 이름 표시][sni] 문서를 참조하세요.

10. **Save**를 클릭하여 변경 내용을 저장하고 SSL을 사용하도록 설정합니다.

> [AZURE.NOTE]**IP 기반 SSL**을 선택했으며 사용자 지정 도메인이 A 레코드를 사용하여 구성된 경우 다음과 같은 추가 단계를 수행해야 합니다.
>
> 1. IP 기반 SSL 바인딩을 구성하면 웹앱에 전용 IP 주소가 할당됩니다. 이 IP 주소는 웹앱의 **대시보드** 페이지에 있는 **간략 상태** 섹션에서 확인할 수 있습니다. **Virtual IP Address**로 나열됩니다.
>    
>     ![가상 IP 주소](./media/configure-ssl-web-site/staticip.png)
>    
>     이 IP 주소는 이전에 도메인에 대한 A 레코드를 구성하는 데 사용된 가상 IP 주소와 다릅니다. SNI 기반 SSL을 사용하도록 구성되었거나 SSL을 사용하도록 구성되지 않은 경우에는 이 항목에 대한 주소가 표시되지 않습니다.
>
> 2. 도메인 이름 등록 기관에서 제공한 도구를 사용하여 이전 단계의 IP 주소를 가리키도록 사용자 지정 도메인 이름에 대한 A 레코드를 수정합니다.


이제 `HTTP://` 대신 `HTTPS://`를 통해 웹앱을 방문하여 인증서가 올바르게 구성되었는지 확인할 수 있습니다.

##<a name="bkmk_enforce"></a>웹앱에 HTTPS 적용

Azure 앱 서비스는 HTTPS를 적용하지 *않습니다*. 방문자가 웹앱의 보안을 손상시킬 수 있는 HTTP를 사용하여 웹앱에 액세스할 수 있습니다. 웹앱에 HTTPS를 적용하려면 **URL 다시 쓰기** 모듈을 사용할 수 있습니다. URL 재작성 모듈은 Azure 앱 서비스에 포함되어 있으며 응용 프로그램으로 요청을 전달하기 전에 들어오는 요청에 적용되는 규칙을 정의할 수 있습니다. **Azure에서 지원하는 모든 프로그래밍 언어로 작성한 응용 프로그램에 이 모듈을 사용할 수 있습니다.**

> [AZURE.NOTE].NET MVC 응용 프로그램은 URL 다시 쓰기 대신 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 필터를 사용해야 합니다. RequireHttps 필터 사용에 대한 자세한 내용은 [웹앱에 보안 ASP.NET MVC 5 앱 배포](../article/app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)를 참조하세요.
> 
> 다른 프로그래밍 언어 및 프레임워크를 사용한 요청의 프로그래밍 방식 리디렉션에 대한 자세한 내용은 해당 기술에 대한 설명서를 참조하세요.

URL 다시 쓰기 규칙은 응용 프로그램 루트에 저장된 **web.config** 파일에 정의되어 있습니다. 다음 예에는 들어오는 모든 트래픽이 강제로 HTTPS를 사용하도록 하는 기본 URL 다시 쓰기 규칙이 포함되어 있습니다.

<a name="example"></a>\*\*URL 다시 쓰기 예제 Web.Config\*\*

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <rewrite>
	      <rules>
	        <rule name="Force HTTPS" enabled="true">
	          <match url="(.*)" ignoreCase="false" />
	          <conditions>
	            <add input="{HTTPS}" pattern="off" />
	          </conditions>
	          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
	        </rule>
	      </rules>
	    </rewrite>
	  </system.webServer>
	</configuration>

이 규칙은 사용자가 HTTP를 사용하여 페이지를 요청할 때 HTTP 상태 코드 of 301(영구 리디렉션)을 반환하여 작동합니다. 301은 방문자가 요청한 것과 같은 URL로 요청을 리디렉션하지만 요청의 HTTP 부분을 HTTPS로 바꿉니다. 예를 들어 HTTP://contoso.com은 HTTPS://contoso.com로 리디렉션됩니다.

> [AZURE.NOTE]응용 프로그램이 **Node.js**, **PHP**, **Python Django** 또는 **Java**로 작성된 경우 web.config 파일을 포함하지 않을 가능성이 높습니다. 그러나 **Node.js**, **Python Django** 및 **Java**는 Azure 앱 서비스에 호스트될 때 실제 web.config를 사용합니다. Azure는 배포 중에 이 파일을 자동으로 만들기 때문에 사용자가 이 파일을 직접 확인할 수는 없습니다. 응용 프로그램의 일부로 이 파일을 하나 포함할 경우 Azure가 자동으로 생성하는 파일이 재정의됩니다.

###.NET

.NET 응용 프로그램의 경우 응용 프로그램에 대한 web.config 파일을 수정하고 **example**의 [&lt;rewrite>](#example) 섹션을 **&lt;system.WebServer>** 섹션에 추가합니다.

web.config 파일에 이미 **&lt;rewrite>** 섹션이 포함되어 있으면 **example**의 the [&lt;rule>](#example)을 **&lt;rules>** 섹션의 첫 번째 항목으로 추가합니다.

###PHP

PHP 응용 프로그램의 경우 [예제](#example)를 응용 프로그램 루트에 web.config 파일로 저장한 다음, 응용 프로그램을 웹앱으로 다시 배포하기만 하면 됩니다.

###Node.js, Python Django 및 Java

Node.js, Python Django 및 Java 앱이 아직 web.config 파일을 제공하지 않으면 이러한 앱에 대해 이 파일이 자동으로 만들어지지만 배포 중에 만들어지므로 서버에만 존재합니다. 자동으로 생성된 이 파일에는 Azure에 응용 프로그램 호스트 방법을 알려주는 설정이 포함되어 있습니다.

웹앱에서 자동으로 생성된 파일을 검색하고 수정하려면 다음 단계를 사용하세요.

1. FTP를 사용하여 파일을 다운로드합니다. [FTP를 통해 파일 업로드/다운로드 및 진단 로그 수집](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)(영문)을 참조하세요.

2. 응용 프로그램 루트에 파일을 추가합니다.

3. 다음 정보를 사용하여 재작성 규칙을 추가합니다.

	* **Node.js 및 Python Django**

		Node.js 및 Python Django 응용 프로그램용으로 생성된 web.config 파일에는 사이트의 적절한 작동에 필요한 **&lt;rule>** 항목이 포함된 **&lt;rewrite>** 섹션이 이미 있습니다. 강제로 이 사이트에서 HTTPS를 사용하게 하려면 예제의 **&lt;rule>**을 **&lt;rules>** 섹션의 첫 번째 항목으로 추가합니다. 이렇게 하면 나머지 규칙은 그대로 유지되면서 HTTPS가 강제로 적용됩니다.

	* **Java**
	
		Apache Tomcat을 사용하는 Java 응용 프로그램용 web.config 파일에는 **&lt;rewrite>** 섹션이 포함되어 있지 않으므로 예제의 **&lt;rewrite>** 섹션을 **&lt;system.webServer>** 섹션에 추가해야 합니다.

4. Azure에 프로젝트(업데이트된 web.config 포함)를 다시 배포

HTTPS를 강제로 적용하기 위한 다시 쓰기 규칙을 사용하여 web.config를 배포하면 즉시 효과를 발휘하여 모든 요청이 HTTPS로 리디렉션됩니다.

IIS URL 다시 쓰기 모듈에 대한 자세한 내용은 [URL 다시 쓰기](http://www.iis.net/downloads/microsoft/url-rewrite)(영문) 설명서를 참조하세요.

## 추가 리소스 ##
- [Microsoft Azure 보안 센터](/support/trust-center/security/)
- [Azure 웹 사이트에서 잠금 해제된 구성 옵션](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [진단 로깅 사용](../article/app-service-web/web-sites-enable-diagnostic-log.md)
- [웹 사이트 구성](../article/app-service-web/web-sites-configure.md)
- [Azure 관리 포털](https://manage.windowsazure.com)

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

[customdomain]: ../articles/app-service-web/web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/configure-ssl-web-site/sslconfig.png
[uploadcert]: ./media/configure-ssl-web-site/ssluploadcert.png
[uploadcertdlg]: ./media/configure-ssl-web-site/ssluploaddlg.png
[sslbindings]: ./media/configure-ssl-web-site/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/configure-ssl-web-site/waws-certmgr.png
[certwiz1]: ./media/configure-ssl-web-site/waws-certwiz1.png
[certwiz2]: ./media/configure-ssl-web-site/waws-certwiz2.png
[certwiz3]: ./media/configure-ssl-web-site/waws-certwiz3.png
[certwiz4]: ./media/configure-ssl-web-site/waws-certwiz4.png

<!----HONumber=July15_HO5-->