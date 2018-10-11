---
title: Azure App Service에 대한 SSL 인증서 구입 및 구성 | Microsoft Docs
description: App Service Certificate를 구입하고 App Service 앱에 바인딩하는 방법을 알아봅니다
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: apurvajo;cephalin
ms.openlocfilehash: 0c2adcfa4e11e444f66e1a9c04bea6e3d352f117
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077729"
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Azure App Service에 대한 SSL 인증서 구입 및 구성

이 자습서에서는 **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)** 에 대한 SSL 인증서를 구매하여 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)에 안전하게 저장하고 사용자 지정 도메인과 연결하여 웹앱의 보안을 유지하는 방법을 보여 줍니다.

## <a name="step-1---sign-in-to-azure"></a>1단계 - Azure에 로그인

http://portal.azure.com에서 Azure Portal에 로그인합니다.

## <a name="step-2---place-an-ssl-certificate-order"></a>2단계 - SSL 인증서 주문하기

**Azure Portal**에서 새로운 [App Service Certificate](https://portal.azure.com/#create/Microsoft.SSL)를 만들어 SSL 인증서를 주문할 수 있습니다.

![인증서 만들기](./media/app-service-web-purchase-ssl-web-site/createssl.png)

SSL 인증서에 친숙한 **이름** 및 **도메인 이름**을 입력합니다.

> [!NOTE]
> 이 단계는 구매 프로세스의 가장 중요한 부분 중 하나입니다. 이 인증서로 보호하려면 올바른 호스트 이름(사용자 지정 도메인)을 입력 해야 합니다. 호스트 이름에 접두어 WWW를 붙이지 **마세요**. 
>

**구독**, **리소스 그룹** 및 **인증서 SKU**를 선택합니다.

> [!TIP]
> App Service Certificates는 Azure 또는 비 Azure 서비스에 대해 사용할 수 있으며 App Services로 제한되지 않습니다. 그렇게 하려면 어디서든 사용할 수 있는 App Service Certificate의 로컬 PFX 복사본을 만들어야 합니다. 자세한 내용은 [App Service Certificate의 로컬 PFX 복사본 만들기](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)를 참고하세요.
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>3단계 - Azure Key Vault에 인증서 저장

> [!NOTE]
> [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)는 클라우드 응용 프로그램 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 되는 Azure 서비스입니다.
>

SSL 인증서 구입을 완료했으면 [App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) 페이지를 열어야 합니다.

![KV에 저장할 준비 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

이 인증서를 사용하려면 몇 가지 추가 단계를 완료해야 하므로 인증서 상태는 **"발급 보류 중"** 입니다.

인증서 속성 페이지 내에서 **인증서 구성**을 클릭하고 **1단계: 저장**을 클릭하여 이 인증서를 Azure Key Vault에 저장합니다.

**Key Vault 상태** 페이지에서 **Key Vault 리포지토리**를 클릭하여 이 인증서를 저장할 기존 Key Vault를 선택하거나 **새 Key Vault 만들기**로 동일한 구독 및 리소스 그룹 내에 새 Key Vault를 만듭니다.

> [!NOTE]
> Azure Key Vault의 경우 이 인증서를 저장하는 데 약간의 요금이 부과됩니다.
> 자세한 내용은 **[Azure Key Vault 가격 책정 정보](https://azure.microsoft.com/pricing/details/key-vault/)** 를 참조하세요.
>

이 인증서를 저장할 Key Vault 리포지토리를 선택하면 **저장** 옵션이 표시됩니다.

![KV의 저장 성공 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>4단계 - 도메인 소유권 확인

3단계에서 사용한 것과 동일한 **인증서 구성** 페이지에서 **2단계: 확인**을 클릭합니다.

기본 도메인 확인 방법을 선택합니다. 

App Service Certificate에서는 도메인 확인 방법으로 App Service, 도메인 및 수동 확인을 지원합니다. 이러한 확인 유형은 [고급 섹션](#advanced)에 자세히 설명되어 있습니다.

> [!NOTE]
> **App Service 확인**은 확인하려는 도메인이 동일한 구독의 App Service 앱에 이미 매핑된 경우 가장 편리한 옵션입니다. App Service 앱이 도메인 소유권을 이미 확인했다는 사실을 활용합니다.
>

**확인** 단추를 클릭하고 이 단계를 완료합니다.

![도메인 확인 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

**확인**을 클릭한 후 **확인** 옵션이 성공할 때까지 **새로 고침** 단추를 클릭합니다.

![KV의 확인 성공 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>5단계: App Service 앱에 인증서 할당

> [!NOTE]
> 이 섹션의 단계를 수행하기 전에 사용자 지정 도메인 이름을 앱과 연결한 상태여야 합니다. 자세한 내용은 **[웹앱에 대한 사용자 지정 도메인 이름 구성](app-service-web-tutorial-custom-domain.md)** 을 참조하세요.
>

**[Azure Portal](https://portal.azure.com/)** 에서 페이지 왼쪽의 **App Service** 옵션을 클릭합니다.

이 인증서를 할당하려는 앱의 이름을 클릭합니다.

**설정**에서 **SSL 설정**을 클릭합니다.

**App Service Certificate 가져오기**를 클릭하고 방금 구입한 인증서를 선택합니다.

![인증서 가져오기 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

**ssl 바인딩** 섹션에서 **바인딩 추가**를 클릭하고 드롭다운을 사용하여 SSL로 보안을 설정할 도메인 이름과 사용할 인증서를 선택합니다. **[SNI(서버 이름 표시)](http://en.wikipedia.org/wiki/Server_Name_Indication)** 를 사용할지 또는 IP 기반 SSL을 사용할지 선택할 수도 있습니다.

![SSL 바인딩 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

**바인딩 추가** 를 클릭하여 변경 내용을 저장하고 SSL을 사용하도록 설정합니다.

> [!NOTE]
> **IP 기반 SSL**을 선택했으며 사용자 지정 도메인이 A 레코드를 사용하여 구성된 경우 다음과 같은 추가 단계를 수행해야 합니다. 자세한 내용은 [고급 섹션](#Advanced)을 참조하세요.

이 시점에서 `HTTP://` 대신 `HTTPS://`를 사용하여 앱에 방문하여 인증서가 올바르게 구성되었는지 확인할 수 있습니다.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>6단계 - 관리 작업

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>고급

### <a name="verifying-domain-ownership"></a>도메인 소유권 확인

App Service Certificate는 도메인 확인 방법으로 도메인 확인 및 수동 확인을 지원합니다.

#### <a name="domain-verification"></a>도메인 확인

[Azure에서 구매한 App Service 도메인](custom-dns-web-site-buydomains-web-app.md)에 대해서만 이 옵션을 선택합니다. Azure는 사용자에게 자동으로 확인 TXT 레코드를 추가하고 프로세스를 완료합니다.

#### <a name="manual-verification"></a>수동 확인

> [!IMPORTANT]
> HTML 웹 페이지 확인(표준 인증서 SKU로만 작동)
>

1. **"starfield.html"** 이라는 HTML 파일 만들기

1. 이 파일의 내용은 도메인 확인 토큰의 이름과 정확히 같아야 합니다. (토큰은 도메인 확인 상태 페이지에서 복사할 수 있습니다.)

1. 이 파일을 도메인을 호스팅하는 웹 서버의 루트에서 업로드합니다. `/.well-known/pki-validation/starfield.html`

1. 확인이 끝났으면 **새로 고침**을 클릭하여 인증서 상태를 업데이트합니다. 확인을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

> [!TIP]
> `curl -G http://<domain>/.well-known/pki-validation/starfield.html`을 사용하는 터미널에서 응답에 `<verification-token>`이 포함되어 있는지 확인합니다.

#### <a name="dns-txt-record-verification"></a>DNS TXT 레코드 확인

1. DNS 관리자를 사용하여 `@` 하위 도메인에 값이 도메인 확인 토큰과 같은 TXT 레코드를 만듭니다.
1. 확인이 끝났으면 **“새로 고침”** 을 클릭하여 인증서 상태를 업데이트합니다.

> [!TIP]
> `@.<domain>`에서 `<verification-token>` 값을 가진 TXT 레코드를 만들어야 합니다.

### <a name="assign-certificate-to-app-service-app"></a>App Service 앱에 인증서 할당

**IP 기반 SSL** 을 선택했으며 사용자 지정 도메인이 A 레코드를 사용하여 구성된 경우 다음과 같은 추가 단계를 수행해야 합니다.

IP 기반 SSL 바인딩을 구성하면 앱에 전용 IP 주소가 할당됩니다. **호스트 이름** 섹션.바로 위에 있는 앱 설정 아래 **사용자 지정 도메인** 페이지에서 이 IP 주소를 확인할 수 있습니다. **외부 IP 주소**로 나열됩니다.

![IP SSL 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

이 IP 주소는 이전에 도메인에 대한 A 레코드를 구성하는 데 사용된 가상 IP 주소와 다릅니다. SNI 기반 SSL을 사용하도록 구성되었거나 SSL을 사용하도록 구성되지 않은 경우에는 이 항목에 대한 주소가 표시되지 않습니다.

도메인 이름 등록 기관에서 제공한 도구를 사용하여 이전 단계의 IP 주소를 가리키도록 사용자 지정 도메인 이름에 대한 A 레코드를 수정합니다.

## <a name="rekey-and-sync-the-certificate"></a>인증서 키 다시 생성 및 동기화

인증서의 키를 다시 생성하려면 **인증서 속성** 페이지에서 **키 다시 생성 및 동기화** 옵션을 선택합니다.

프로세스를 시작하려면 **키 다시 생성** 단추를 클릭합니다. 이 프로세스는 완료하는 데 1-10분 정도 걸릴 수 있습니다.

![SSL 키 다시 생성 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

인증서 키를 다시 생성하면 인증서가 인증 기관에서 발급한 새 인증서로 롤링됩니다.

## <a name="renew-the-certificate"></a>인증서 갱신

언제든지 인증서의 자동 갱신을 켜려면 인증서 관리 페이지에서 **자동 갱신 설정**을 클릭합니다. **켜기**를 선택하고 **저장**을 클릭합니다. 자동 갱신을 켜 놓으면 인증서가 만료 90일 전에 자동으로 갱신됩니다.

![](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

인증서를 수동으로 갱신하려면 **수동 갱신**을 대신 클릭합니다. 만료 60일 전에 인증서를 자동으로 갱신하도록 요청할 수 있습니다.

> [!NOTE]
> 갱신된 인증서는 수동으로 갱신했는지 또는 자동으로 갱신했는지 여부에 따라 앱에 자동으로 바인딩되지 않습니다. 앱에 바인딩하려면 [인증서 갱신](./app-service-web-tutorial-custom-ssl.md#renew-certificates)을 참조하세요. 

## <a name="more-resources"></a>추가 리소스

* [HTTPS 적용](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [TLS 1.1/1.2 적용](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Azure App Service의 응용 프로그램 코드에서 SSL 인증서 사용](app-service-web-ssl-cert-load.md)
* [FAQ : App Service Certificates](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
