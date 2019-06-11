---
title: 기존 사용자 지정 SSL 인증서 바인딩 - Azure App Service | Microsoft Docs
description: Azure App Service의 웹앱, 모바일 앱 백 엔드 또는 API 앱에 사용자 지정 SSL 인증서를 바인딩하는 방법을 알아봅니다.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0a5b8bdbcd5a05574d824e3f57cfc23967278e27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66138763"
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-app-service"></a>자습서: Azure App Service에 기존 사용자 지정 SSL 인증서 바인딩

Azure App Service는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 신뢰할 수 있는 인증 기관에서 구매한 사용자 지정 SSL 인증서를 [Azure App Service](overview.md)에 바인딩하는 방법을 보여줍니다. 완료하면 사용자 지정 DNS 도메인의 HTTPS 엔드포인트에서 앱에 액세스할 수 있습니다.

![사용자 지정 SSL 인증서가 포함된 웹앱](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 앱의 가격 책정 계층 업그레이드
> * 사용자 지정 인증서를 App Service에 바인딩
> * 인증서 갱신
> * HTTPS 적용
> * TLS 1.1/1.2 적용
> * 스크립트를 사용하여 TLS 관리 자동화

> [!NOTE]
> 사용자 지정 SSL 인증서가 필요한 경우 Azure Portal에서 구매하고 앱에 바인딩할 수 있습니다. [App Service 인증서 자습서](web-sites-purchase-ssl-web-site.md)를 따르세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- [App Service 앱 만들기](/azure/app-service/)
- [App Service 앱에 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)
- 신뢰할 수 있는 인증 기관에서 SSL 인증서 구매
- SSL 인증서 요청을 서명하는 데 사용되는 프라이빗 키 보유

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>SSL 인증서에 대한 요구 사항

App Service에서 인증서를 사용하려면 인증서가 다음 요구 사항을 모두 충족해야 합니다.

* 신뢰할 수 있는 인증 기관에서 서명됨
* 암호로 보호된 PFX 파일로 내보냄
* 길이가 2048비트 이상인 프라이빗 키 포함
* 인증서 체인의 모든 중간 인증서를 포함함

> [!NOTE]
> **ECC(타원 곡선 암호화) 인증서**는 App Service에서 사용할 수 있지만 이 문서에서는 다루지 않습니다. ECC 인증서를 만드는 정확한 단계에서 인증 기관을 사용하세요.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>SSL 인증서 바인딩

SSL 인증서를 앱에 업로드할 준비가 되었습니다.

### <a name="merge-intermediate-certificates"></a>중간 인증서 병합

인증 기관에서 여러 인증서를 인증서 체인에 제공하면 인증서를 순서대로 병합해야 합니다. 

이렇게 하려면 받은 각 인증서를 텍스트 편집기에서 엽니다. 

_mergedcertificate.crt_라는 병합된 인증서의 파일을 만듭니다. 텍스트 편집기에서 각 인증서의 내용을 이 파일에 복사합니다. 사용자 인증서의 순서는 사용자의 인증서로 시작하고 루트 인증서로 끝나는 인증서 체인의 순서에 따라야 합니다. 다음 예제와 유사합니다.

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>PFX로 인증서 내보내기

인증서 요청 생성에 사용된 프라이빗 키로 병합된 SSL 인증서를 내보냅니다.

OpenSSL을 사용하여 인증서 요청을 생성한 경우 프라이빗 키 파일을 만든 것입니다. 인증서를 PFX로 내보내려면 다음 명령을 실행합니다. 자리 표시자 _&lt;private-key-file>_ 및 _&lt;merged-certificate-file>_ 을 사용자의 개인 키 및 병합된 인증서 파일에 대한 경로로 바꿉니다.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

메시지가 표시되면 내보내기 암호를 정의합니다. 나중에 SSL 인증서를 App Service에 업로드할 때 이 암호를 사용합니다.

IIS 또는 _Certreq.exe_를 사용하여 인증서 요청을 생성한 경우 인증서를 로컬 컴퓨터에 설치한 다음 [해당 인증서를 PFX로 내보냅니다](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>SSL 인증서 업로드

SSL 인증서를 업로드하려면 앱의 왼쪽 탐색 영역에서 **SSL 설정**을 클릭합니다.

**인증서 업로드**를 클릭합니다. 

**PFX 인증서 파일**에서 PFX 파일을 선택합니다. **인증서 암호**에서 PFX 파일을 내보낼 때 만든 암호를 입력합니다.

**업로드**를 클릭합니다.

![인증서 업로드](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

App Service에서 인증서 업로드가 완료되면 **SSL 설정** 페이지에 업로드된 인증서가 표시됩니다.

![업로드된 인증서](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>SSL 인증서 바인딩

**SSL 바인딩** 섹션에서 **바인딩 추가**를 클릭합니다.

**SSL 바인딩 추가** 페이지에서 드롭다운을 사용하여 보호할 도메인 이름과 사용할 인증서를 선택합니다.

> [!NOTE]
> 인증서를 업로드했지만 **Hostname** 드롭다운에서 해당 도메인 이름이 표시되지 않으면 브라우저 페이지를 새로 고쳐 봅니다.
>
>

**SSL 유형**에서 **[SNI(서버 이름 표시)](https://en.wikipedia.org/wiki/Server_Name_Indication)** 또는 IP 기반 SSL을 사용할지 선택합니다.

- **SNI 기반 SSL** - 여러 개의 SNI 기반 SSL 바인딩을 추가할 수 있습니다. 이 옵션을 사용하면 여러 SSL 인증서로 같은 IP 주소의 여러 도메인을 보호할 수 있습니다. 대부분의 최신 브라우저(Internet Explorer, Chrome, Firefox 및 Opera 포함)는 SNI를 지원합니다. [Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication)(서버 이름 표시)에서 더 포괄적인 브라우저 지원 정보를 찾을 수 있습니다.
- **IP 기반 SSL** - IP 기반 SSL 바인딩 하나만 추가할 수 있습니다. 이 옵션을 사용하면 전용 공용 IP 주소를 보호하는 데 하나의 SSL 인증서만 사용할 수 있습니다. 여러 도메인을 보호하려면 동일한 SSL 인증서를 사용하여 모두 보호해야 합니다. 이 옵션은 SSL 바인딩의 일반적인 옵션입니다.

**바인딩 추가**를 클릭합니다.

![SSL 인증서 바인딩](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

App Service에서 인증서 업로드가 완료되면 **SSL 바인딩** 섹션에 업로드된 인증서가 표시됩니다.

![웹앱에 바인딩된 인증서](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>IP SSL에 대한 A 레코드 다시 매핑

앱에서 IP 기반 SSL을 사용하지 않을 경우 [사용자 지정 도메인에 대한 HTTPS 테스트](#test)로 건너뜁니다.

기본적으로 앱에서는 공유 공용 IP 주소를 사용합니다. IP 기반 SSL을 사용하여 인증서를 바인딩하면 App Service에서 앱에 대한 새로운 전용 IP 주소를 만듭니다.

A 레코드를 앱에 매핑한 경우 이 새로운 전용 IP 주소로 도메인 레지스트리를 업데이트합니다.

앱의 **사용자 지정 도메인** 페이지가 새로운 전용 IP 주소로 업데이트됩니다. [이 IP 주소를 복사](app-service-web-tutorial-custom-domain.md#info)하고 이 새로운 IP 주소에 [A 레코드를 다시 매핑](app-service-web-tutorial-custom-domain.md#map-an-a-record)합니다.

<a name="test"></a>

## <a name="test-https"></a>HTTPS 테스트

이제 HTTPS가 사용자 지정 도메인에 작동하는지 확인하는 작업만 남았습니다. 다양한 브라우저에서 `https://<your.custom.domain>` 으로 이동하여 앱을 처리하는지 확인합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> 앱에서 인증서 유효성 검사 오류가 발생한 경우 자체 서명된 인증서를 사용하고 있을 수도 있습니다.
>
> 그렇지 않으면 인증서를 PFX 파일로 내보낼 때 중간 인증서를 생략했을 수도 있습니다.

<a name="bkmk_enforce"></a>

## <a name="renew-certificates"></a>인증서 갱신

바인딩이 IP 기반이라고 하더라도 바인딩을 삭제하면 인바운드 IP 주소가 변경될 수 있습니다. 이것은 IP 기반 바인딩에 이미 있는 인증서를 갱신할 때 특히 중요합니다. 앱의 IP 주소 변경을 방지하려면 다음 단계를 순서대로 수행합니다.

1. 새 인증서 업로드
2. 기존 인증서를 삭제하지 않고 원하는 사용자 지정 도메인에 새 인증서를 바인딩합니다. 이 작업은 기존 인증서를 제거하지 않고 바인딩을 바꿉니다.
3. 기존 인증서를 삭제합니다. 

## <a name="enforce-https"></a>HTTPS 적용

기본적으로 누구나 HTTP를 사용하여 앱에 액세스할 수 있습니다. HTTPS 포트에 모든 HTTP 요청을 리디렉션할 수 있습니다.

앱 페이지의 왼쪽 탐색 영역에서 **SSL 설정**을 선택합니다. 그런 다음 **HTTPS에만 해당**에서 **켜기**를 선택합니다.

![HTTPS 적용](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

작업이 완료되면 앱을 가리키는 HTTP URL 중 하나로 이동합니다. 예:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>TLS 버전 적용

앱에는 [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)와 같이 업계 표준에서 권장되는 TLS 수준인 [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2가 기본적으로 허용됩니다. 다른 TLS 버전을 적용하려면 다음 단계를 수행합니다.

앱 페이지의 왼쪽 탐색 영역에서 **SSL 설정**을 선택합니다. 그런 다음, **TLS 버전**에서 원하는 최소 TLS 버전을 선택합니다. 이 설정은 인바운드 호출만 제어합니다. 

![TLS 1.1 또는 1.2 적용](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

작업이 완료되면 앱에서 더 낮은 TLS 버전과의 모든 연결을 거부합니다.

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/overview)을 통해 스크립트를 사용하여 앱에 대한 SSL 바인딩을 자동화할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

다음 명령은 내보낸 PFX 파일을 업로드하고 지문을 가져옵니다.

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

다음 명령은 이전 명령의 지문을 사용하여 SNI 기반 SSL 바인딩을 추가합니다.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

다음 명령은 최소 TLS 버전 1.2를 적용합니다.

```bash
az webapp config set \
    --name <app_name> \
    --resource-group <resource_group_name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 명령은 내보낸 PFX 파일을 업로드하고 SNI 기반 SSL 바인딩을 추가합니다.

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>공용 인증서(선택 사항)
앱이 클라이언트로써 원격 리소스에 액세스해야 하고 원격 리소스에 인증서 인증이 필요한 경우 앱에 [공용 인증서](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/)를 업로드하면 됩니다. 앱의 SSL 바인딩에는 공용 인증서가 필요하지 않습니다.

앱에서 공용 인증서를 로드 및 사용하는 방법에 대한 자세한 내용은 [Azure App Service의 애플리케이션 코드에서 SSL 인증서 사용](app-service-web-ssl-cert-load.md)을 참조하세요. App Service Environments의 앱에도 공용 인증서를 사용할 수 있습니다. LocalMachine 인증서 저장소에 인증서를 저장해야 하는 경우 App Service Environment에서 앱을 사용해야 합니다. 자세한 내용은 [App Service 앱에 공용 인증서를 구성하는 방법](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer)을 참조하세요.

![공용 인증서 업로드](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 앱의 가격 책정 계층 업그레이드
> * 사용자 지정 인증서를 App Service에 바인딩
> * 인증서 갱신
> * HTTPS 적용
> * TLS 1.1/1.2 적용
> * 스크립트를 사용하여 TLS 관리 자동화

다음 자습서로 이동하여 Azure Content Delivery Network를 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure App Service에 CDN(Content Delivery Network) 추가](../cdn/cdn-add-to-web-app.md)

자세한 내용은 [Azure App Service의 애플리케이션 코드에서 SSL 인증서 사용](app-service-web-ssl-cert-load.md)을 참조하세요.
