# Azure 웹 사이트에 HTTPS 사용

HTTPS를 사용하여 웹 사이트를 방문하면 SSL(Secure Socket Layer) 암호화를 사용하여 웹 사이트와 브라우저 간의 통신 보안이 설정됩니다. 이는 인터넷을 통해 전송되는 데이터의 보안을 설정하는 가장 일반적인 방법으로, 방문자에게 사이트와의 트랜잭션이 안전함을 보장합니다. 이 문서에서는 Azure 웹 사이트에 HTTPS를 사용하도록 설정하는 방법을 설명합니다.

> [WACOM.NOTE] 사용자 지정 도메인 이름에 HTTPS를 사용하도록 설정하려면 표준 모드로 웹 사이트를 구성해야 합니다. 현재 무료 또는 공유 모드를 사용 중이면 추가 비용이 발생할 수도 있습니다. 공유 및 표준 모드 가격 책정에 대한 자세한 내용은 [가격 책정 세부 정보][]를 참조하세요. Azure에 등록하려면 [Microsoft Azure 무료 평가판][]을 참조하세요.

[][]

## \*.azurewebsites.net 도메인

</p>
사용자 지정 도메인 이름을 사용하지 않고 Azure에서 웹 사이트에 할당한 \*.azurewebsites.net 도메인(예: contoso.azurewebsites.net)을 사용하려는 경우 Microsoft에서 제공한 인증서로 사이트 보안이 이미 설정되었습니다. **<https://mywebsite.azurewebsites.net>**을 사용하여 안전하게 사이트에 액세스할 수 있습니다. 그러나 \*.azurewebsites.net은 공유 도메인이며 모든 공유 도메인과 마찬가지로 사용자의 인증서를 사용하는 사용자 지정 도메인을 사용하는 것만큼 안전하지 않습니다.

이 문서의 나머지 부분에서는 **contoso.com**, **www.contoso.com**, **\*.contoso.com** 등의 사용자 지정 도메인 이름에 HTTPS를 사용하도록 설정하는 방법에 대한 세부 정보를 제공합니다.

[][1]

## 사용자 지정 도메인 이름

</p>
**contoso.com** 등의 사용자 지정 도메인 이름에 HTTPS를 사용하도록 설정하려면 도메인 이름 등록 기관에 사용자 지정 도메인 이름을 등록해야 합니다. Azure 웹 사이트의 도메인 이름을 구성하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성][]을 참조하세요. 사용자 지정 도메인 이름을 등록하고 사용자 지정 이름에 응답하도록 웹 사이트를 구성한 후에는 도메인에 대한 SSL 인증서를 요청해야 합니다.

도메인 이름을 등록하면 **www.contoso.com**, **mail.contoso.com** 등의 하위 도메인도 만들 수 있습니다. SSL 인증서를 요청하기 전에 먼저 인증서로 보안을 설정할 도메인 이름을 결정해야 합니다. 이렇게 하면 받아야 하는 인증서 유형이 결정됩니다. **contoso.com** 또는 **www.contoso.com**과 같은 단일 도메인 이름의 보안을 설정해야 하는 경우 기본 인증서로 충분합니다. **contoso.com**, **www.contoso.com**, **mail.contoso.com** 등 여러 도메인 이름의 보안을 설정해야 하는 경우 와일드카드 인증서 또는 주체 대체 이름(subjectAltName, SAN)이 포함된 인증서가 필요합니다.

> [WACOM.NOTE] 대부분의 브라우저는 인증서에 지정된 도메인 이름이 브라우저에 입력된 도메인 이름과 일치하지 않을 경우 경고를 표시합니다. 예를 들어 인증서에 www.contoso.com만 나열되지만 Internet Explorer에서 사이트에 액세스하는 데 사용된 도메인 이름이 login.contoso.com인 경우 "이 웹 사이트에서 제시한 보안 인증서는 다른 웹 사이트 주소에 대해 발급되었습니다."라는 경고가 표시됩니다.

**기본 인증서**는 인증서의 CN(일반 이름)이 클라이언트에서 사이트를 방문하는 데 사용할 특정 도메인 또는 하위 도메인으로 설정된 인증서입니다. 예를 들어 **www.contoso.com**입니다. 이러한 인증서는 CN으로 지정된 단일 도메인 이름의 보안만 설정합니다.

**와일드카드 인증서**는 인증서 CN의 하위 도메인 수준에 와일드카드 '\*'가 포함된 인증서입니다. 이렇게 하면 인증서에서 주어진 도메인에 대한 단일 수준의 하위 도메인을 일치시킬 수 있습니다. 예를 들어 **\*.contoso.com**에 대한 와일드카드 인증서는 **www.contoso.com**, **payment.contoso.com** 및 **login.contoso.com**에 유효합니다. 하위 도메인 수준이 추가되는 **test.login.contoso.com**에는 유효하지 않습니다. 또한 루트 도메인 수준이며 하위 도메인이 아닌 **contoso.com**에도 유효하지 않습니다.

와일드카드 인증서는 Microsoft에서 웹 사이트에 대해 자동으로 만들어진 \*.azurewebsites.net 도메인 이름에 제공하는 인증서입니다.

**subjectAltName**은 다양한 값 또는 주체 대체 이름을 인증서에 연결할 수 있게 해 주는 인증서 확장입니다. SSL 인증서를 위해 이 확장을 사용하여 인증서가 유효한 DNS 이름을 추가할 수 있습니다. 예를 들어 subjectAltName을 사용하는 인증서의 CN은 **contoso.com**이지만 대체 이름은 **www.contoso.com**, **payment.contoso.com**, **test.login.contoso.com**, **fabrikam.com**일 수 있습니다. 이러한 인증서는 일반 이름 및 subjectAltName에 지정된 모든 도메인 이름에 유효합니다.

한 인증서에서 와일드카드와 subjectAltName을 둘 다 지원할 수 있습니다.

[][2]

## 인증서 얻기

</p>
Azure 웹 사이트에 사용되는 SSL 인증서는 이 목적으로 인증서를 발급하는 신뢰할 수 있는 타사 CA(인증 기관)에서 서명되어야 합니다. 아직 없는 경우 SSL 인증서를 판매하는 회사에서 구입해야 합니다. 인증 기관 목록은 Microsoft TechNet Wiki의 [Windows 및 Windows Phone 8 SSL 루트 인증서 프로그램(멤버 CA)][]을 참조하세요.

인증서는 Azure의 SSL 인증서에 대한 다음 요구 사항을 충족해야 합니다.

-   인증서에 개인 키가 포함되어 있어야 합니다.

-   개인 정보 교환(.pfx) 파일로 내보낼 수 있는 키 교환용 인증서를 만들어야 합니다.

-   인증서의 주체 이름은 웹 사이트 액세스에 사용되는 도메인과 일치해야 합니다. 이 인증서로 여러 도메인을 처리해야 하는 경우 와일드카드 값을 사용하거나 앞에서 설명한 대로 subjectAltName 값을 지정해야 합니다.

    -   Azure 웹 사이트에 대한 사용자 지정 도메인 이름을 구성하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성][]을 참조하세요.

    > [WACOM.NOTE] azurewebsites.net 도메인에 대한 인증서를 받거나 생성하지 마세요.

-   인증서는 최소한 2048비트 암호화를 사용해야 합니다.

> [WACOM.NOTE] 개인 CA에서 발급된 인증서는 Azure 웹 사이트에서 지원되지 않습니다.

인증 기관에서 SSL 인증서를 가져오려면 CA로 전송되는 CSR(인증서 서명 요청)을 생성해야 합니다. 그러면 CA에서 CSR을 완료하는 데 사용되는 인증서를 반환합니다. CSR을 생성하는 두 가지 일반적인 방법은 certmgr.exe 또는 [OpenSSL][] 응용 프로그램을 사용하는 것입니다. Certmgr.exe는 Windows에서만 사용할 수 있고 OpenSSL은 대부분의 플랫폼에 사용할 수 있습니다. 두 가지 유틸리티를 사용하는 단계는 다음과 같습니다.

> [WACOM.NOTE] ECC(타원 곡선 암호화) 인증서는 Azure 웹 사이트에서 지원되지만 상대적으로 새로운 인증서이며 CSR을 만드는 것과 정확히 동일한 단계로 CA에 대해 작업해야 합니다. ECC 인증서를 얻은 후에는 아래 단계에서 설명된 웹 사이트에 업로드할 수 있습니다.

CA에서 사용되는 경우 **중간 인증서**(체인 인증서라고도 함)를 받아야 할 수도 있습니다. 중간 인증서 사용은 '체인화되지 않은 인증서'보다 더 안전한 것으로 간주되므로 CA에서 중간 인증서를 사용하는 것이 일반적입니다. 중간 인증서는 CA 웹 사이트에서 개별 다운로드로 제공되는 경우가 많습니다. 이 문서의 단계에서는 중간 인증서가 Azure 웹 사이트에 업로드된 인증서와 병합되도록 하는 방법을 제공합니다.

> [WACOM.NOTE] 일련의 단계 중 하나를 따를 경우 **일반 이름**을 입력하라는 메시지가 표시됩니다. 여러 도메인(www.contoso.com, sales.contoso.com)에 사용할 와일드카드 인증서를 받으려는 경우 이 값은 \*.domainname(예: \*.contoso.com)이어야 합니다. 단일 도메인 이름에 대한 인증서를 받으려는 경우 이 값은 사용자가 웹 사이트를 방문하기 위해 브라우저에 입력하는 값과 같아야 합니다. 예를 들어 www.contoso.com입니다.
>
> \*.contoso.com 등의 와일드카드 이름과 contoso.com 등의 루트 도메인 이름을 둘 다 지원해야 하는 경우 와일드카드 SAN(주체 대체 이름) 인증서를 사용할 수 있습니다. SubjectAltName 확장을 사용하는 인증서 요청을 만드는 방법의 예는 [SubjectAltName 인증서][]를 참조하세요.
>
> Azure 웹 사이트의 도메인 이름을 구성하는 방법에 대한 자세한 내용은 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성][]을 참조하세요.

### Certreq.exe를 사용하여 인증서 받기(Windows에만 해당)

Certreq.exe는 인증서 요청을 만들기 위한 Windows 유틸리티입니다. 이 유틸리티는 Windows XP/Windows Server 2000 이후 기본 Windows 설치의 일부였으므로 최근 Windows 시스템에서 사용할 수 있습니다. certreq.exe를 사용하여 SSL 인증서를 받으려면 다음 단계를 따르세요.

테스트를 위해 자체 서명된 인증서를 만들려는 경우 이 문서의 [자체 서명된 인증서][] 섹션을 참조하세요.

IIS 관리자를 사용하여 인증서 요청을 만들려는 경우 [IIS 관리자를 사용하여 인증서 받기][] 섹션을 참조하세요.

1.  **메모장**을 열고 다음이 포함된 새 문서를 만듭니다. 제목 줄의 **mysite.com**을 웹 사이트의 사용자 지정 도메인 이름으로 바꿉니다. 예를 들어 Subject = "CN=www.contoso.com"입니다.

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

    위에서 지정된 옵션 및 사용 가능한 기타 옵션에 대한 자세한 내용은 [Certreq 참조 설명서][]를 참조하세요.

2.  텍스트 파일을 **myrequest.txt**로 저장합니다.

3.  **시작 화면** 또는 **시작 메뉴**에서 **cmd.exe**를 실행합니다.

4.  명령 프롬프트에서 다음 명령을 사용하여 인증서 요청 파일을 만듭니다.

        certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

    1단계에서 만든 **myrequest.txt** 파일의 경로 및 **myrequest.csr** 파일을 만들 때 사용할 경로를 지정합니다.

5.  인증 기관에 **myrequest.csr**을 제출하여 SSL 인증서를 받습니다. 이 경우 파일을 업로드하거나 메모장에서 파일을 열고 파일 내용을 웹 양식에 직접 붙여 넣어야 합니다.

    인증 기관 목록은 Microsoft TechNet Wiki의 [Windows 및 Windows Phone 8 SSL 루트 인증서 프로그램(멤버 CA)][]을 참조하세요.

6.  인증 기관에서 인증서(.CER) 파일을 제공하면 요청을 생성하는 데 사용되는 컴퓨터에 이 파일을 저장한 후 다음 명령을 사용하여 요청을 수락하고 인증서 생성 프로세스를 완료합니다.

        certreq -accept -user mycert.cer

    이 경우 인증 기관에서 받은 **mycert.cer** 인증서를 사용하여 인증서의 서명을 완료합니다. 아무 파일도 만들어지지 않고, 대신 인증서가 Windows 인증서 저장소에 저장됩니다.

7.  CA에서 중간 인증서를 사용하는 경우 다음 단계에서 인증서를 내보내기 전에 이러한 인증서를 설치해야 합니다. 일반적으로 이 인증서는 CA에서 개별 다운로드로 제공되며, 각 웹 서버 유형에 따라 여러 형식으로 제공됩니다. Microsoft IIS용으로 제공된 버전을 선택합니다.

    인증서를 다운로드한 후 탐색기에서 인증서를 마우스 오른쪽 단추로 클릭하고 **인증서 설치**를 선택합니다. **인증서 가져오기 마법사**에서 기본값을 사용하고 가져오기가 완료될 때까지 **다음**을 계속 선택합니다.

8.  인증서 저장소에서 인증서를 내보내려면 **시작 화면** 또는 **시작 메뉴**에서 **certmgr.msc**를 실행합니다. **인증서 관리자**가 나타나면 **개인** 폴더를 확장하고 **인증서**를 선택합니다. **발급 대상** 필드에서 인증서를 요청한 사용자 지정 도메인 이름을 가진 항목을 찾습니다. **발급자** 필드에 이 인증서에 사용한 인증 기관에 표시되어야 합니다.

    ![여기에 인증서 관리자 이미지 삽입][]

9.  인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업**을 선택한 후 **내보내기**를 선택합니다. **인증서 내보내기 마법사**에서 **다음**을 클릭하고 **예, 개인 키를 내보냅니다.**를 선택합니다. **다음**을 클릭합니다.

    ![개인 키 내보내기][]

10. **개인 정보 교환 - PKCS \#12**, **Include all certificates in the certificate chain** 및 **확장된 속성 모두 내보내기**를 선택합니다. **다음**을 클릭합니다.

    ![모든 인증서 및 확장 속성 포함][]

11. **암호**를 선택한 후 암호를 입력하고 확인합니다. **다음**을 클릭합니다.

    ![암호 지정][]

12. 내보낸 인증서가 포함될 경로와 파일 이름을 제공합니다. 파일 이름은 확장명이 **.pfx**여야 합니다. **다음**을 클릭하여 프로세스를 완료합니다.

    ![파일 경로 제공][]

이제 내보낸 PFX 파일을 Azure 웹 사이트에 업로드할 수 있습니다.

### OpenSSL을 사용하여 인증서 받기

1.  명령줄, bash 또는 터미널 세션에서 다음을 사용하여 개인 키와 인증서 서명 요청을 생성합니다.

        openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2.  메시지가 표시되면 해당 정보를 입력합니다. 예를 들면 다음과 같습니다.

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

3.  인증 기관에 CSR을 제출하여 SSL 인증서를 받습니다. 인증 기관 목록은 Microsoft TechNet Wiki의 [Windows 및 Windows Phone 8 SSL 루트 인증서 프로그램(멤버 CA)][]을 참조하세요.

4.  CA에서 인증서를 받은 후 **myserver.crt**라는 파일에 저장합니다. CA에서 인증서를 텍스트 형식으로 제공한 경우 인증서 텍스트를 **myserver.crt** 파일에 붙여 넣습니다. 텍스트 편집기에서 볼 때 파일 내용은 다음과 유사해야 합니다.

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

5.  명령줄, Bash 또는 터미널 세션에서 다음 명령을 사용하여 **myserver.key** 및 **myserver.crt**를 Azure 웹 사이트에 필요한 형식인 **myserver.pfx**로 변환합니다.

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    메시지가 표시되면 암호를 입력하여 .pfx 파일의 보안을 설정합니다.

    <div class="dev-callout"> 
<b>참고</b>
<p>CA에서 중간 인증서를 사용하는 경우 다음 단계에서 인증서를 내보내기 전에 이러한 인증서를 설치해야 합니다. 일반적으로 이 인증서는 CA에서 개별 다운로드로 제공되며, 각 웹 서버 유형에 따라 여러 형식으로 제공됩니다. PEM 파일(.pem 파일 확장명)로 제공된 버전을 선택합니다.</p>
<p>다음 명령은 <b>intermediate-cets.pem</b> 파일에 포함된 중간 인증서를 포함하는 .pfx 파일을 만드는 방법을 보여 줍니다.</p>
<pre><code>
openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
</code></pre>
</div>

    이 명령을 실행하면 Azure 웹 사이트에 사용하는 데 적합한 **myserver.pfx** 파일이 만들어집니다.

[][3]

## 표준 모드 구성

</p>
사용자 지정 도메인에 HTTPS를 사용하도록 설정하는 기능은 Azure 웹 사이트의 표준 모드에만 사용할 수 있습니다. 표준 모드로 전환하려면 다음 단계를 따르세요.

> [WACOM.NOTE] 무료 웹 사이트 모드에서 표준 웹 사이트 모드로 웹 사이트를 전환하기 전에 웹 사이트 구독에 대한 지출 한도를 제거해야 합니다. 그렇지 않으면 대금 청구 기간이 끝나기 전에 한도에 도달할 경우 사이트를 사용할 수 없게 됩니다. 공유 및 표준 모드 가격 책정에 대한 자세한 내용은 [가격 책정 세부 정보][]를 참조하세요.

1.  브라우저에서 [관리 포털][]을 엽니다.

2.  **웹 사이트** 탭에서 웹 사이트 이름을 클릭합니다.

    ![웹 사이트 선택][]

3.  **크기 조정** 탭을 클릭합니다.

    ![크기 조정 탭][]

4.  **일반** 섹션에서 **표준**을 클릭하여 웹 사이트 모드를 설정합니다.

    ![표준 모드 선택됨][]

5.  **저장**을 클릭합니다. 메시지가 표시되면 **Yes**를 클릭합니다.

    > [WACOM.NOTE] "'\<사이트 이름\>' 웹 사이트에 대한 규모 구성 실패" 오류가 표시되는 경우 자세히 단추를 사용하여 추가 정보를 확인할 수 있습니다. "사용 가능한 표준 인스턴스 서버가 부족하여 이 요청을 충족할 수 없습니다." 오류가 표시될 수도 있습니다. 이 오류가 표시되면 [Azure 지원][]으로 문의하세요.

[][4]

## SSL 구성

</p>
이 섹션의 단계를 수행하기 전에 사용자 지정 도메인 이름을 Azure 웹 사이트와 연결한 상태여야 합니다. 자세한 내용은 [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성][]을 참조하세요.

1.  브라우저에서 [Azure 관리 포털][관리 포털]을 엽니다.

2.  **웹 사이트** 탭에서 사이트 이름을 클릭하고 **구성** 탭을 선택합니다.

    ![구성 탭][]

3.  **certificates** 섹션에서 **upload a certificate**를 클릭합니다.

    ![인증서 업로드][]

4.  **Upload a certificate** 대화 상자에서 IIS 관리자 또는 OpenSSL을 사용하여 앞에서 만든 .pfx 인증서 파일을 선택합니다. .pfx 파일의 보안을 설정하는 데 사용된 암호(있는 경우)를 지정합니다. 마지막으로, **check**를 클릭하여 인증서를 업로드합니다.

    ![인증서 업로드 대화 상자][]

5.  **CONFIGURE** 탭의 **ssl bindings** 섹션에서 드롭다운을 사용하여 SSL로 보안을 설정할 도메인 이름과 사용할 인증서를 선택합니다. SNI([서버 이름 표시][])를 사용할지 또는 IP 기반 SSL을 사용할지 선택할 수도 있습니다.

    ![SSL 바인딩][]

    -   IP 기반 SSL은 서버의 전용 공용 IP 주소를 도메인 이름에 매핑하여 인증서를 도메인 이름과 연결합니다. 이렇게 하려면 서비스와 연결된 각 도메인 이름(contoso.com, fabricam.com 등)에 전용 IP 주소가 있어야 합니다. 이 방법은 SSL 인증서를 웹 서버와 연결하는 일반적인 방법입니다.

    -   SNI 기반 SSL은 SSL 및 TLS([전송 계층 보안][])에 대한 확장으로, 각 도메인에 별도의 보안 인증서를 사용하여 여러 도메인이 동일한 IP 주소를 공유할 수 있게 해 줍니다. 대부분의 최신 브라우저(Internet Explorer, Chrome, Firefox 및 Opera 포함)는 SNI를 지원하지만 이전 브라우저는 SNI를 지원하지 않을 수도 있습니다. SNI에 대한 자세한 내용은 Wikipedia의 [서버 이름 표시][] 문서를 참조하세요.

6.  **Save**를 클릭하여 변경 내용을 저장하고 SSL을 사용하도록 설정합니다.

> [WACOM.NOTE] **IP based SSL** 및 사용자 지정 도메인이 A 레코드를 사용하여 구성된 경우 다음과 같은 추가 단계를 수행해야 합니다.
>
> 1.  IP 기반 SSL 바인딩을 구성하면 웹 사이트에 전용 IP 주소가 할당됩니다. 이 IP 주소는 웹 사이트의 **Dashboard** 페이지에 있는 **quick glance** 섹션에서 확인할 수 있습니다. **Virtual IP Address**로 나열됩니다.
>
>     ![가상 IP 주소][]
>
>     이 IP 주소는 이전에 도메인에 대한 A 레코드를 구성하는 데 사용된 가상 IP 주소와 다릅니다. SNI 기반 SSL을 사용하도록 구성되었거나 SSL을 사용하도록 구성되지 않은 경우에는 이 항목에 대한 주소가 표시되지 않습니다.
>
> 2.  도메인 이름 등록 기관에서 제공한 도구를 사용하여 이전 단계의 IP 주소를 가리키도록 사용자 지정 도메인 이름에 대한 A 레코드를 수정합니다.
>
이제 HTTPS를 통해 웹 사이트를 방문하여 인증서가 올바르게 구성되었는지 확인할 수 있습니다.

[][5]

## SubjectAltName 인증서(선택 사항)

</p>
OpenSSL을 사용하면 SubjectAltName 확장을 사용하여 단일 인증서로 여러 도메인 이름을 지원하는 인증서 요청을 만들 수 있지만 구성 파일이 필요합니다. 다음 단계에서는 구성 파일을 만든 다음 이 파일을 사용하여 인증서를 요청하는 과정을 안내합니다.

1.  **sancert.cnf**라는 새 파일을 만들고 다음을 파일 내용으로 사용합니다.

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

2.  **sancert.cnf** 파일을 저장합니다.

3.  sancert.cnf 구성 파일을 사용하여 개인 키와 인증서 서명 요청을 생성합니다. bash 또는 터미널 세션에서 다음 명령을 사용합니다.

        openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

4.  메시지가 표시되면 해당 정보를 입력합니다. 예를 들면 다음과 같습니다.

        Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com

    이 프로세스를 완료하면 두 개의 파일인 **myserver.key** 및 **server.csr**이 만들어집니다. **server.csr**에는 인증서 서명 요청이 포함되어 있습니다.

5.  인증 기관에 CSR을 제출하여 SSL 인증서를 받습니다. 인증 기관 목록은 Microsoft TechNet Wiki의 [Windows 및 Windows Phone 8 SSL 루트 인증서 프로그램(멤버 CA)][]을 참조하세요.

6.  CA에서 인증서를 받은 후 **myserver.crt**라는 파일에 저장합니다. CA에서 인증서를 텍스트 형식으로 제공한 경우 인증서 텍스트를 **myserver.crt** 파일에 붙여 넣습니다. 텍스트 편집기에서 볼 때 파일 내용은 다음과 유사해야 합니다.

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

7.  명령줄, Bash 또는 터미널 세션에서 다음 명령을 사용하여 **myserver.key** 및 **myserver.crt**를 Azure 웹 사이트에 필요한 형식인 **myserver.pfx**로 변환합니다.

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    메시지가 표시되면 암호를 입력하여 .pfx 파일의 보안을 설정합니다.

    <div class="dev-callout"> 
<b>참고</b>
<p>CA에서 중간 인증서를 사용하는 경우 다음 단계에서 인증서를 내보내기 전에 이러한 인증서를 설치해야 합니다. 일반적으로 이 인증서는 CA에서 개별 다운로드로 제공되며, 각 웹 서버 유형에 따라 여러 형식으로 제공됩니다. PEM 파일(.pem 파일 확장명)로 제공된 버전을 선택합니다.</p>
<p>다음 명령은 <b>intermediate-cets.pem</b> 파일에 포함된 중간 인증서를 포함하는 .pfx 파일을 만드는 방법을 보여 줍니다.</p>
<pre><code>
openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
</code></pre>
</div>

    이 명령을 실행하면 Azure 웹 사이트에 사용하는 데 적합한 **myserver.pfx** 파일이 만들어집니다.

## <a name="bkmk_iismgr"></a>IIS 관리자를 사용하여 인증서 받기(옵션)

IIS 관리자에 익숙한 경우 IIS 관리자를 통해 Azure 웹 사이트에 사용할 수 있는 인증서를 생성할 수 있습니다.

1.  IIS 관리자를 사용하여 인증 기관에 전송할 CSR(인증서 서명 요청)을 생성합니다. CSR 생성에 대한 자세한 내용은 [인터넷 서버 인증서 요청(IIS 7)][]을 참조하세요.

2.  인증 기관에 CSR을 제출하여 SSL 인증서를 받습니다. 인증 기관 목록은 Microsoft TechNet Wiki의 [Windows 및 Windows Phone 8 SSL 루트 인증서 프로그램(멤버 CA)][]을 참조하세요.

3.  인증 기관 공급업체에서 제공한 인증서를 사용하여 CSR을 완료합니다. CSR 완료에 대한 자세한 내용은 [인터넷 서버 인증서 설치(IIS 7)][]를 참조하세요.

4.  CA에서 중간 인증서를 사용하는 경우 다음 단계에서 인증서를 내보내기 전에 이러한 인증서를 설치해야 합니다. 일반적으로 이 인증서는 CA에서 개별 다운로드로 제공되며, 각 웹 서버 유형에 따라 여러 형식으로 제공됩니다. Microsoft IIS용으로 제공된 버전을 선택합니다.

    인증서를 다운로드한 후 탐색기에서 인증서를 마우스 오른쪽 단추로 클릭하고 **인증서 설치**를 선택합니다. **인증서 가져오기 마법사**에서 기본값을 사용하고 가져오기가 완료될 때까지 **다음**을 계속 선택합니다.

5.  IIS 관리자에서 인증서를 내보냅니다. 인증서 내보내기에 대한 자세한 내용은 [서버 인증서 내보내기(IIS 7)][]를 참조하세요. 내보낸 파일은 Azure 웹 사이트에 사용하기 위해 다음 단계에서 Azure로 업로드됩니다.

    <div class="dev-callout"> 
<b>참고</b>
<p>내보내는 동안 <strong>예, 개인 키를 내보냅니다.</strong> 옵션을 선택해야 합니다. 여기에는 내보낸 인증서의 개인 키가 포함됩니다.</p>
</div>

    <div class="dev-callout"> 
<b>참고</b>
<p>내보내는 동안 <strong>include all certs in the certification path</strong> 및 <strong>Export all extended properties</strong> 옵션을 선택해야 합니다. 여기에는 내보낸 인증서의 중간 인증서가 모두 포함됩니다.</p>
</div>

[][6]

## 자체 서명된 인증서(선택 사항)

</p>
테스트를 위해 인증서를 받고 프로덕션으로 진행할 때까지 신뢰할 수 있는 CA로부터 인증서 구입을 지연하려는 경우도 있습니다. 자체 서명된 인증서로 이 간격을 채울 수 있습니다. 자체 서명된 인증서는 인증 기관인 것처럼 직접 만들고 서명하는 인증서입니다. 이 인증서를 사용하여 웹 사이트의 보안을 설정할 수 있지만 인증서가 신뢰할 수 있는 CA에서 서명되지 않았기 때문에 사이트를 방문할 때 대부분의 브라우저에서 오류를 반환합니다. 일부 브라우저는 사이트 보기를 허용하지 않을 수도 있습니다.

자체 서명된 인증서를 만드는 방법에는 여러 가지가 있지만, 이 문서에서는 **makecert** 및 **OpenSSL** 사용에 대한 정보만 제공합니다.

### makecert를 사용하여 자체 서명된 인증서 만들기

다음 단계를 수행하여 Visual Studio가 설치되어 있는 Windows 시스템에서 테스트 인증서를 만들 수 있습니다.

1.  **시작 메뉴** 또는 **시작 화면**에서 **개발자 명령 프롬프트**를 검색합니다. 마지막으로, **개발자 명령 프롬프트**를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.

    사용자 계정 컨트롤 대화 상자가 표시되면 **예**를 선택하여 계속합니다.

2.  개발자 명령 프롬프트에서 다음 명령을 사용하여 자체 서명된 인증서를 새로 만듭니다. **serverdnsname**을 웹 사이트의 DNS로 대체해야 합니다.

        makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

    이 명령은 01/01/2013과 01/01/2014 사이의 날짜에 유효한 인증서를 만들고 CurrentUser 인증서 저장소에 위치를 저장합니다.

3.  **시작 메뉴** 또는 **시작 화면**에서 **Windows PowerShell**을 검색하고 이 응용 프로그램을 시작합니다.

4.  Windows PowerShell 프롬프트에서 다음 명령을 사용하여 이전에 만든 인증서를 내보냅니다.

        $mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
        get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

    이 명령은 지정한 암호를 $mypwd에 보안 문자열로 저장한 다음 **dnsname** 매개 변수에 지정된 DNS 이름을 사용하여 인증서를 찾고 **filepath** 매개 변수에 지정된 파일로 내보냅니다. 암호가 포함된 보안 문자열은 내보낸 파일의 보안을 설정하는 데 사용됩니다.

### OpenSSL을 사용하여 자체 서명된 인증서 만들기

1.  **serverauth.cnf**라는 새 문서를 만들고 다음을 이 파일 내용으로 사용합니다.

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName         = Country Name (2 letter code)
        countryName_min         = 2
        countryName_max         = 2
        stateOrProvinceName     = State or Province Name (full name)
        localityName            = Locality Name (eg, city)
        0.organizationName      = Organization Name (eg, company)
        organizationalUnitName      = Organizational Unit Name (eg, section)
        commonName          = Common Name (eg, your website's domain name)
        commonName_max          = 64
        emailAddress            = Email Address
        emailAddress_max        = 40

        [ req_attributes ]
        challengePassword       = A challenge password
        challengePassword_min       = 4
        challengePassword_max       = 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

    이 코드는 Azure 웹 사이트에서 사용할 수 있는 SSL 인증서를 생성하는 데 필요한 구성 설정을 지정합니다.

2.  명령줄, bash 또는 터미널 세션에서 다음을 사용하여 자체 서명된 인증서를 새로 생성합니다.

        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

    이 코드는 **serverauth.cnf** 파일에 지정된 구성 설정을 사용하여 새 인증서를 만듭니다.

3.  Azure 웹 사이트에 업로드할 수 있는 .PFX 파일로 인증서를 내보내려면 다음 명령을 사용합니다.

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    메시지가 표시되면 암호를 입력하여 .pfx 파일의 보안을 설정합니다.

    이 명령으로 생성된 **myserver.pfx**를 사용하여 테스트를 위해 Azure 웹 사이트의 보안을 설정할 수 있습니다.

  [가격 책정 세부 정보]: https://www.windowsazure.com/en-us/pricing/details/
  [Microsoft Azure 무료 평가판]: http://azure.microsoft.com/en-us/pricing/free-trial/
  []: bkmk_azurewebsites
  [1]: bkmk_domainname
  [Azure 웹 사이트에 대한 사용자 지정 도메인 이름 구성]: /en-us/develop/net/common-tasks/custom-dns-web-site/
  [2]: bkmk_getcert
  [Windows 및 Windows Phone 8 SSL 루트 인증서 프로그램(멤버 CA)]: http://go.microsoft.com/fwlink/?LinkID=269988
  [OpenSSL]: http://www.openssl.org/
  [SubjectAltName 인증서]: #bkmk_subjectaltname
  [자체 서명된 인증서]: #bkmk_selfsigned
  [IIS 관리자를 사용하여 인증서 받기]: #bkmk_iismgr
  [Certreq 참조 설명서]: http://technet.microsoft.com/library/cc725793.aspx
  [여기에 인증서 관리자 이미지 삽입]: ./media/configure-ssl-web-site/waws-certmgr.png
  [개인 키 내보내기]: ./media/configure-ssl-web-site/waws-certwiz1.png
  [모든 인증서 및 확장 속성 포함]: ./media/configure-ssl-web-site/waws-certwiz2.png
  [암호 지정]: ./media/configure-ssl-web-site/waws-certwiz3.png
  [파일 경로 제공]: ./media/configure-ssl-web-site/waws-certwiz4.png
  [3]: bkmk_standardmode
  [관리 포털]: https://manage.windowsazure.com/
  [웹 사이트 선택]: ./media/configure-ssl-web-site/sslwebsite.png
  [크기 조정 탭]: ./media/configure-ssl-web-site/sslscale.png
  [표준 모드 선택됨]: ./media/configure-ssl-web-site/sslreserved.png
  [Azure 지원]: http://www.windowsazure.com/en-us/support/options/
  [4]: bkmk_configuressl
  [구성 탭]: ./media/configure-ssl-web-site/sslconfig.png
  [인증서 업로드]: ./media/configure-ssl-web-site/ssluploadcert.png
  [인증서 업로드 대화 상자]: ./media/configure-ssl-web-site/ssluploaddlg.png
  [서버 이름 표시]: http://en.wikipedia.org/wiki/Server_Name_Indication
  [SSL 바인딩]: ./media/configure-ssl-web-site/sslbindings.png
  [전송 계층 보안]: http://en.wikipedia.org/wiki/Transport_Layer_Security
  [가상 IP 주소]: ./media/configure-ssl-web-site/staticip.png
  [5]: bkmk_subjectaltname
  [인터넷 서버 인증서 요청(IIS 7)]: http://technet.microsoft.com/en-us/library/cc732906(WS.10).aspx
  [인터넷 서버 인증서 설치(IIS 7)]: http://technet.microsoft.com/en-us/library/cc771816(WS.10).aspx
  [서버 인증서 내보내기(IIS 7)]: http://technet.microsoft.com/en-us/library/cc731386(WS.10).aspx
  [6]: bkmk_selfsigned
