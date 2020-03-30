---
title: 사용자 지정 루트 CA를 통해 자체 서명된 인증서 생성
titleSuffix: Azure Application Gateway
description: 사용자 지정 루트 CA를 사용하여 Azure 응용 프로그램 게이트웨이 자체 서명 인증서를 생성하는 방법에 대해 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 0447e87fd8685188af8008995ba938092f2b87fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293604"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>사용자 지정 루트 CA를 사용 하 고 Azure 응용 프로그램 게이트웨이 자체 서명 된 인증서를 생성 합니다.

응용 프로그램 게이트웨이 v2 SKU백 엔드 서버를 허용 하기 위해 신뢰할 수 있는 루트 인증서의 사용을 소개 합니다. 이렇게 하면 v1 SKU에 필요한 인증 인증서가 제거됩니다. *루트 인증서는* Base-64 인코딩된 X.509(입니다. CER) 백 엔드 인증서 서버에서 루트 인증서를 포맷합니다. 서버 인증서를 발급한 CA(루트 인증 기관)를 식별한 다음 서버 인증서를 SSL 통신에 사용합니다.

응용 프로그램 게이트웨이는 잘 알려진 CA(예: GoDaddy 또는 DigiCert)에 의해 서명된 경우 기본적으로 웹 사이트의 인증서를 신뢰합니다. 이 경우 루트 인증서를 명시적으로 업로드할 필요가 없습니다. 자세한 내용은 [응용 프로그램 게이트웨이를 사용하여 SSL 종료 및 종단 간 SSL](ssl-overview.md)개요를 참조하십시오. 그러나 개발/테스트 환경이 있고 확인된 CA 서명된 인증서를 구입하지 않으려면 사용자 지정 CA를 직접 만들고 자체 서명된 인증서를 만들 수 있습니다. 

> [!NOTE]
> 자체 서명된 인증서는 기본적으로 신뢰할 수 없으며 유지 관리가 어려울 수 있습니다. 또한 강하지 않을 수 있는 오래된 해시 및 암호 제품군을 사용할 수도 있습니다. 보안을 강화하려면 잘 알려진 인증 기관에서 서명한 인증서를 구입하십시오.

이 아티클에서는 다음 방법을 설명합니다.

- 사용자 지정 인증서 기관 만들기
- 사용자 지정 CA에서 서명한 자체 서명인증서 만들기
- 자체 서명된 루트 인증서를 응용 프로그램 게이트웨이에 업로드하여 백 엔드 서버를 인증합니다.

## <a name="prerequisites"></a>사전 요구 사항

- **윈도우 또는 리눅스를 실행하는 컴퓨터에서 [OpenSSL](https://www.openssl.org/)** 

   인증서 관리에 사용할 수 있는 다른 도구가 있을 수 있지만 이 자습서에서는 OpenSSL을 사용합니다. 당신은 많은 리눅스 배포판과 함께 번들로 OpenSSL을 찾을 수 있습니다, 우분투 등.
- **웹 서버**

   예를 들어 아파치, IIS 또는 NGINX를 사용하여 인증서를 테스트합니다.

- **애플리케이션 게이트웨이 v2 SKU**
   
  기존 응용 프로그램 게이트웨이가 없는 경우 [빠른 시작: Azure 응용 프로그램 게이트웨이 - Azure 포털을 사용 하 고 직접 웹 트래픽을](quick-create-portal.md)참조 합니다.

## <a name="create-a-root-ca-certificate"></a>루트 CA 인증서 만들기

OpenSSL을 사용하여 루트 CA 인증서를 만듭니다.

### <a name="create-the-root-key"></a>루트 키 만들기

1. OpenSSL이 설치된 컴퓨터에 로그인하여 다음 명령을 실행합니다. 이렇게 하면 암호로 보호된 키가 만들어집니다.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. 프롬프트에서 강력한 암호를 입력합니다. 예를 들어 대문자, 소문자, 숫자 및 기호를 사용하여 9자 이상을 사용합니다.

### <a name="create-a-root-certificate-and-self-sign-it"></a>루트 인증서를 만들고 자체 서명합니다.

1. 다음 명령을 사용하여 csr 및 인증서를 생성합니다.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   이전 명령은 루트 인증서를 만듭니다. 이를 사용하여 서버 인증서에 서명합니다.

1. 메시지가 표시되면 루트 키에 대한 암호를 입력하고 국가, 주, 조직, OU 및 정규화된 도메인 이름(발급자의 도메인)과 같은 사용자 지정 CA에 대한 조직 정보를 입력합니다.

   ![루트 인증서 만들기](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>서버 인증서 만들기

다음으로 OpenSSL을 사용하여 서버 인증서를 만듭니다.

### <a name="create-the-certificates-key"></a>인증서 키 만들기

다음 명령을 사용하여 서버 인증서에 대한 키를 생성합니다.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>CSR 만들기(인증서 서명 요청)

CSR은 인증서를 요청할 때 CA에 부여되는 공개 키입니다. CA는 이 특정 요청에 대한 인증서를 발급합니다.

> [!NOTE]
> 서버 인증서의 CN(일반 이름)은 발급자의 도메인과 달라야 합니다. 예를 들어 이 경우 발급자의 CN은 `www.contoso.com` 이며 서버 인증서의 CN은 `www.fabrikam.com`.


1. 다음 명령을 사용하여 CSR을 생성합니다.

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. 메시지가 표시되면 루트 키에 대한 암호와 사용자 지정 CA: 국가, 주, 조직, OU 및 정규화된 도메인 이름에 대한 조직 정보를 입력합니다. 이것은 웹 사이트의 도메인이며 발급자와 다릅니다.

   ![서버 인증서](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>CSR 및 키로 인증서를 생성하고 CA의 루트 키로 서명합니다.

1. 다음 명령을 사용하여 인증서를 만듭니다.

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>새로 만든 인증서 확인

1. 다음 명령을 사용하여 CRT 파일의 출력을 인쇄하고 해당 내용을 확인합니다.

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![인증서 확인](media/self-signed-certificates/verify-cert.png)

1. 디렉터리에서 파일을 확인하고 다음 파일이 있는지 확인합니다.

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>웹 서버의 SSL 설정에서 인증서 구성

웹 서버에서 fabrikam.crt 및 fabrikam.key 파일을 사용하여 SSL을 구성합니다. 웹 서버에서 두 개의 파일을 사용할 수 없는 경우 OpenSSL 명령을 사용하여 파일을 단일 .pem 또는 .pfx 파일에 결합할 수 있습니다.

### <a name="iis"></a>IIS

인증서를 가져오고 IIS에서 서버 인증서로 업로드하는 방법에 대한 지침은 [Windows Server 2003의 웹 서버에 가져온 인증서 설치](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)방법을 참조하십시오.

SSL 바인딩 지침은 [IIS 7에서 SSL을 설정하는 방법을](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)참조하십시오.

### <a name="apache"></a>Apache

다음 구성은 아파치에서 [SSL에 대해 구성된 가상 호스트의](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) 예입니다.

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

다음 구성은 SSL 구성이 있는 [NGINX 서버 블록의](https://nginx.org/docs/http/configuring_https_servers.html) 예입니다.

![SSL을 가진 NGINX](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>서버에 액세스하여 구성을 확인합니다.

1. 컴퓨터의 신뢰할 수 있는 루트 저장소에 루트 인증서를 추가합니다. 웹 사이트에 액세스할 때 전체 인증서 체인이 브라우저에 있는지 확인합니다.

   ![신뢰할 수 있는 루트 인증서](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > DNS가 웹 서버 이름(이 예에서 www.fabrikam.com)을 웹 서버의 IP 주소로 가리키도록 구성되었다고 가정합니다. 그렇지 않은 경우 호스트 [파일을](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) 편집하여 이름을 확인할 수 있습니다.
1. 웹 사이트로 이동한 다음 브라우저 주소 상자의 잠금 아이콘을 클릭하여 사이트 및 인증서 정보를 확인합니다.

## <a name="verify-the-configuration-with-openssl"></a>OpenSSL로 구성 확인

또는 OpenSSL을 사용하여 인증서를 확인할 수 있습니다.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![OpenSSL 인증서 확인](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>루트 인증서를 응용 프로그램 게이트웨이의 HTTP 설정에 업로드

응용 프로그램 게이트웨이에 인증서를 업로드하려면 .crt 인증서를 .cer 형식 Base-64 인코딩된 .cer 형식으로 내보내야 합니다. .crt는 기본 64 인코딩 된 형식의 공개 키를 이미 포함하므로 파일 확장자의 이름을 .crt에서 .cer로 바꾸기만 하면 됩니다. 

### <a name="azure-portal"></a>Azure portal

포털에서 신뢰할 수 있는 루트 인증서를 업로드하려면 **HTTP 설정을** 선택하고 **HTTPS** 프로토콜을 선택합니다.

![포털을 사용하여 인증서 추가](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

또는 Azure CLI 또는 Azure PowerShell을 사용하여 루트 인증서를 업로드할 수 있습니다. 다음 코드는 Azure PowerShell 샘플입니다.

> [!NOTE]
> 다음 샘플에서는 응용 프로그램 게이트웨이에 신뢰할 수 있는 루트 인증서를 추가하고, 새 HTTP 설정을 만들고, 백 엔드 풀과 수신기가 이미 있다고 가정하여 새 규칙을 추가합니다.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that SSL handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```
### <a name="verify-the-application-gateway-backend-health"></a>응용 프로그램 게이트웨이 백 엔드 상태 확인

1. 응용 프로그램 게이트웨이의 **백 엔드 상태** 보기를 클릭하여 프로브가 정상인지 확인합니다.
1.    HTTPS 프로브의 상태가 **정상임을** 확인해야 합니다.

    ![HTTPS 프로브](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>다음 단계

응용 프로그램 게이트웨이의 SSL\TLS에 대한 자세한 내용은 [응용 프로그램 게이트웨이를 사용하여 SSL 종료 및 종단 간 SSL 개요를](ssl-overview.md)참조하십시오.

