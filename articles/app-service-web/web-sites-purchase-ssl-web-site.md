---
title: "Azure 앱 서비스에 대한 SSL 인증서 구입 및 구성"
description: "Azure 앱 서비스에 대한 SSL 인증서 구입 및 구성 방법을 알아봅니다."
services: app-service
documentationcenter: .net
author: apurvajo
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f9ff33f33a196e65f6cb7ee7f5332aacb9231f6d
ms.lasthandoff: 03/29/2017


---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Azure 앱 서비스에 대한 SSL 인증서 구입 및 구성
> [!div class="op_single_selector"]
> * [Azure에서 SSL 인증서 구입](web-sites-purchase-ssl-web-site.md)
> * [다른 곳에서 SSL 인증서 사용](web-sites-configure-ssl-certificate.md)
> 
> 

기본적으로 **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)**는 *.azurewebsites.net 도메인에 대해 와일드카드 인증서를 사용하는 웹앱에 대해 HTTPS를 사용하도록 설정합니다. 사용자 지정 도메인을 구성하지 않으려는 경우 기본 HTTPS 인증서를 활용할 수 있습니다. 그러나 *[모든 와일드카드 도메인](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates)과 마찬가지로, 자체 인증서로 사용자 지정 도메인을 사용하는 것만큼 안전하지 않습니다. 이제 Azure 앱 서비스는 Azure 포털에서 포털을 종료하지 않고 SSL 인증서를 구입 및 관리하는 매우 간단한 방법을 제공합니다.  
이 문서에서는 간단한 3단계로 **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)**에 대한 SSL 인증서를 구입 및 구성하는 방법을 설명합니다. 

> [!NOTE]
> 사용자 지정 도메인 이름에 대한 SSL 인증서는 무료 및 공유 웹앱과 함께 사용할 수 없습니다. 기본, 표준 또는 프리미엄 모드에 대해 웹앱을 구성해야 하지만 구독의 결제 비용이 달라질 수 있습니다. 자세한 내용은 **[Web Apps 가격 책정 정보](https://azure.microsoft.com/pricing/details/web-sites/)**를 참조하세요.
> 
> 

## <a name="bkmk_Overview"></a>개요
> [!NOTE]
> 연결된 활성 신용 카드가 없는 구독을 사용하여 SSL 인증서를 구입하지 않도록 합니다. 구독을 사용할 수 없게 될 수 있습니다. 
> 
> 

## <a name="a-purchase-store-and-assign-an-ssl-certificate-for-your-custom-domain-a"></a><a> 사용자 지정 도메인에 대한 SSL 인증서 구입, 저장 및 할당 </a>
contoso.com과 같은 사용자 지정 도메인에 대해 HTTPS를 사용하도록 설정하려면 먼저 **[Azure App Service에서 사용자 지정 도메인 이름을 구성](web-sites-custom-domain-name.md)**해야 합니다.

SSL 인증서를 요청하기 전에 먼저 인증서로 보안을 설정할 도메인 이름을 결정해야 합니다. 받아야 하는 인증서 유형이 결정됩니다. contoso.com 또는 www.contoso.com과 같은 단일 도메인 이름의 보안을 설정해야 하는 경우 표준(기본) 인증서로 충분합니다. contoso.com, www.contoso.com 및 mail.contoso.com과 같은 여러 도메인 이름의 보안을 설정해야 하는 경우, **[와일드카드 인증서](http://en.wikipedia.org/wiki/Wildcard_certificate)**를 가져올 수 있습니다.

## <a name="bkmk_purchasecert"></a>0단계: SSL 인증서 주문 입력
이 단계에서는 사용자가 선택한 SSL 인증서에 대한 주문을 입력하는 방법을 배웁니다.

1. **[Azure Portal](https://portal.azure.com/)**에서 찾아보기를 클릭하고 검색 표시줄에 "App Service Certificate"를 입력하고 결과에서 "App Service Certificate"를 선택한 후 추가를 클릭합니다. 
   
   ![찾아보기를 사용하여 만들기의 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/browse.jpg)
   
   ![찾아보기를 사용하여 만들기의 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/add.jpg)
2. SSL 인증서에 대한 **이름** 을 입력합니다.
3. SSL 인증서에 대한 **호스트 이름**
   
   > [!NOTE]
   > 구매 프로세스의 가장 중요한 부분 중 하나입니다. 이 인증서로 보호하려면 올바른 호스트 이름(사용자 지정 도메인)을 입력 해야 합니다. **마세요** . 예를 들어 사용자 지정 도메인 이름이 www.contoso.com이면 호스트 이름 필드에 contoso.com을 입력하면 해당 인증서가 www 및 루트 도메인 모두 보호합니다. 
   > 
   > 
4. **구독**을 선택합니다. 
   
   구독이 여러 개인 경우 사용자 지정 도메인 또는 해당 웹앱에 사용된 동일한 구독에 SSL 인증서를 만들어야 합니다.
5. **리소스 그룹**을 선택하거나 만듭니다.
   
   리소스 그룹을 통해 관련 Azure 리소스를 하나의 단위로 관리할 수 있으며 앱에 대해 역할 기반 액세스 제어(RBAC) 규칙을 설정할 때 유용합니다. 자세한 내용은 Azure 리소스 관리를 참조하세요.
6. **인증서 SKU** 
   
   마지막으로, 요구 사항에 맞는 인증서 SKU를 선택하고 만들기를 클릭합니다. 지금 Azure App Service를 통해 두 개의 서로 다른 SKU를 구입할 수 있습니다.
   
        •    S1 – Standard Certificate with 1-year validity and auto renewal  
        •    W1 – Wild card Certificate with 1-year validity and auto renewal      
   자세한 내용은 **[Web Apps 가격 책정 정보](https://azure.microsoft.com/pricing/details/web-sites/)**를 참조하세요.

![인증서 SKU의 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

> [!NOTE]
> SSL 인증서 만들기는 어디서나 1-10분 정도 소요될 수 있습니다. 이 프로세스는 백그라운드에서 여러 단계를 수행하며 그렇지 않고 수동으로 수행할 경우 매우 번거로울 수 있습니다.  
> 
> 

## <a name="bkmk_StoreKeyVault"></a>1단계: Azure 주요 자격 증명 모음에 인증서 저장
이 단계에서는 사용자가 선택한 Azure Key Vault에 구입한 SSL 인증서를 저장하는 방법을 배웁니다.

1. SSL 인증서 구입을 완료했으면 다시 탐색하여 **App Service Certificate** 리소스 블레이드를 수동으로 열어야 합니다(위의 1단계 참조).   
   
   ![KV에 저장할 준비 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)
   
   이 인증서를 사용하려면 몇 가지 추가 단계를 완료해야 하므로 인증서 상태가 **"발급 보류 중"**으로 표시됩니다.
2. 인증서 속성 블레이드 안에서 **"인증서 구성"**을 클릭하고 **"1단계 저장"**을 클릭하여 이 인증서를 Azure Key Vault에 저장합니다.
3. **"Key Vault 상태"** 블레이드에서 **"Key Vault 리포지토리"**를 클릭하여 이 인증서를 저장할 기존 Key Vault를 선택하거나 **"새 Key Vault 만들기"**로 동일한 구독 및 리소스 그룹 내에 새 Key Vault를 만듭니다.
   
   ![새 KV 만들기 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
   
   > [!NOTE]
   > Azure 키 자격 증명 모음의 경우 이 인증서를 저장하는 데 약간의 요금이 부과됩니다. 자세한 내용은 **[Azure Key Vault 가격 책정 정보](https://azure.microsoft.com/pricing/details/key-vault/)**를 참조하세요.
   > 
   > 
4. 이 인증서를 저장할 Key Vault 리포지토리를 선택했으면 계속해서 **"Key Vault 상태"** 블레이드 맨 위에 있는 **"저장"** 단추를 클릭하여 저장합니다.  
   
    그러면 사용자가 선택한 Azure 키 자격 증명 모음으로 구입한 인증서를 저장하는 단계가 완료됩니다. 블레이드를 새로 고치면 이 단계에 대해서도 녹색 확인 표시가 표시됩니다.

## <a name="bkmk_VerifyOwnership"></a>2단계: 도메인 소유권 확인
이 단계에서는 방금 주문한 SSL 인증서에 대한 도메인 소유권 확인을 수행하는 방법을 배웁니다. 

1. **"인증서 구성"** 블레이드에서 **"2단계: 확인"** 단계를 클릭합니다. App Service Certificate에서 지원하는 3가지 유형의 도메인 확인이 있습니다.
   
   * **도메인 확인** 
     
     * **[Azure App Service에서 사용자 지정 도메인을 구입](custom-dns-web-site-buydomains-web-app.md)**한 **경우에만** 가장 간단한 프로세스입니다.
     * **"확인"** 단추를 클릭하고 이 단계를 완료합니다.
     * 확인이 끝났으면 **"새로 고침"** 을 클릭하여 인증서 상태를 업데이트합니다. 확인을 완료하는 데 몇 분 정도 걸릴 수 있습니다.
   * **메일 확인**
     
     * 확인 전자 메일이 이 사용자 지정 도메인과 연결된 전자 메일 주소로 이미 전송되었습니다.
     * 전자 메일을 열고 확인 링크를 클릭하여 전자 메일 확인 단계를 완료합니다. 
     * 확인 전자 메일을 다시 전송해야 하는 경우 **"전자 메일 다시 보내기"** 단추를 클릭합니다.
   * **수동 확인**    
     
      **HTML 웹 페이지 확인(표준 인증서 SKU로만 작동)**

        * **"starfield.html"**이라는 HTML 파일 만들기
        * 이 파일의 내용은 도메인 확인 토큰의 이름과 정확히 같아야 합니다. (도메인 확인 상태 블레이드에서 토큰을 복사할 수 있습니다.)
        * 이 파일을 도메인을 호스팅하는 웹 서버의 루트**/.well-known/pki-validation/starfield.html**로 업로드합니다.
        * 확인이 끝났으면 **"새로 고침"** 을 클릭하여 인증서 상태를 업데이트합니다. 확인을 완료하는 데 몇 분 정도 걸릴 수 있습니다.
          
          예를 들어 도메인 확인 토큰 **tgjgthq8d11ttaeah97s3fr2sh**로 **contosocertdemo.com**에 대한 표준 인증서를 구입하는 경우 **http://contosocertdemo.com/.well-known/pki-validation/starfield.html**에 대한 웹 요청은 **tgjgthq8d11ttaeah97s3fr2sh**를 반환해야 합니다.

      **DNS TXT 레코드 확인**
        
        * DNS 관리자를 사용하여 **‘@’** 하위 도메인에 값이 **도메인 확인 토큰**과 같은 TXT 레코드를 만듭니다.
        * 확인이 끝났으면 **"새로 고침"** 을 클릭하여 인증서 상태를 업데이트합니다. 확인을 완료하는 데 몇 분 정도 걸릴 수 있습니다.
          
          예를 들어 호스트 이름 **\*.contosocertdemo.com** 또는 **\*.subdomain.contosocertdemo.com** 및 도메인 확인 토큰 **tgjgthq8d11ttaeah97s3fr2sh**로 와일드카드 인증서에 대한 확인을 수행하려면 **contosocertdemo.com**에 값이 **tgjgthq8d11ttaeah97s3fr2sh**인 TXT 레코드를 만들어야 합니다.     

## <a name="bkmk_AssignCertificate"></a>3단계: 앱 서비스 앱에 인증서 할당
이 단계에서는 앱 서비스 앱에 새로 구입한 인증서를 할당하는 방법에 대해 알아봅니다. 

> [!NOTE]
> 이 섹션의 단계를 수행하기 전에 사용자 지정 도메인 이름을 앱과 연결한 상태여야 합니다. 자세한 내용은 **[웹앱에 대한 사용자 지정 도메인 이름 구성](web-sites-custom-domain-name.md)**을 참조하세요.
> 
> 

1. 브라우저에서 **[Azure Portal](https://portal.azure.com/)**을 엽니다.
2. 페이지의 왼쪽에서 **앱 서비스** 옵션을 클릭합니다.
3. 이 인증서를 할당하려는 앱의 이름을 클릭합니다. 
4. **설정**에서 **SSL 인증서**를 클릭합니다.
5. **앱 서비스 인증서 가져오기** 를 클릭하고 방금 구입한 인증서를 선택합니다.
   
   ![인증서 가져오기 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)
6. **ssl 바인딩** 섹션에서 **바인딩 추가**를 클릭합니다.
7. **ssl bindings** 블레이드에서 드롭다운을 사용하여 SSL로 보안을 설정할 도메인 이름과 사용할 인증서를 선택합니다. **[SNI(서버 이름 표시)](http://en.wikipedia.org/wiki/Server_Name_Indication)**를 사용할지 또는 IP 기반 SSL을 사용할지 선택할 수도 있습니다.
   
    ![SSL 바인딩 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)
   
       •    IP based SSL associates a certificate with a domain name by mapping the dedicated public IP address of the server to the domain name. This requires each domain name (contoso.com, fabricam.com, etc.) associated with your service to have a dedicated IP address. This is the traditional          method of associating SSL certificates with a web server.
       •    SNI based SSL is an extension to SSL and **[Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) that allows multiple domains to share the same IP address, with separate security certificates for each domain. Most modern browsers (including Internet Explorer, Chrome, Firefox and Opera) support SNI, however older browsers may not support SNI. For more information on SNI, see the **[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** article on Wikipedia.
8. **바인딩 추가** 를 클릭하여 변경 내용을 저장하고 SSL을 사용하도록 설정합니다.

**IP 기반 SSL** 을 선택했으며 사용자 지정 도메인이 A 레코드를 사용하여 구성된 경우 다음과 같은 추가 단계를 수행해야 합니다.

* IP 기반 SSL 바인딩을 구성하면 앱에 전용 IP 주소가 할당됩니다. **호스트 이름** 섹션.바로 위에 있는 앱 설정 아래 **사용자 지정 도메인** 페이지에서 이 IP 주소를 확인할 수 있습니다. **외부 IP 주소**
  
    ![IP SSL 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)
  
    이 IP 주소는 이전에 도메인에 대한 A 레코드를 구성하는 데 사용된 가상 IP 주소와 다릅니다. SNI 기반 SSL을 사용하도록 구성되었거나 SSL을 사용하도록 구성되지 않은 경우에는 이 항목에 대한 주소가 표시되지 않습니다.

* 도메인 이름 등록 기관에서 제공한 도구를 사용하여 이전 단계의 IP 주소를 가리키도록 사용자 지정 도메인 이름에 대한 A 레코드를 수정합니다.
   이 시점에서 HTTP:// 대신 HTTPS://를 사용하여 앱에 방문하여 인증서가 올바르게 구성 되었는지 확인할 수 있습니다.

## <a name="bkmk_Export"></a>App Service Certificate 내보내기
다른 Azure 서비스와 함께 사용할 수 있도록 App Service Certificate의 로컬 PFX 복사본을 만들 수 있습니다. 자세한 내용은 **[이 블로그 게시물을 읽으세요](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)**.

## <a name="bkmk_Renew"></a>App Service Certificate 자동 갱신
인증서에 대한 자동 갱신 설정을 설정/해제하거나 인증서를 수동으로 갱신하려면 **"인증서 속성"** 블레이드에서 **"자동 갱신 설정"** 옵션을 선택하면 됩니다. 


  ![찾아보기를 사용하여 만들기의 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/autorenew.png)

인증서가 만료되기 전에 자동으로 갱신하려면 **"자동 갱신"**을 설정으로 지정합니다. 이것이 기본 옵션입니다. 이 옵션을 설정하면 만료되기 90일 전부터 인증서를 갱신하려고 시도합니다. Azure Portal 환경을 사용하여 App Service 앱에 대해 SSL 바인딩을 만든 경우 준비가 되면 새 인증서와 함께 해당 바인딩도 업데이트됩니다(키 다시 생성 및 동기화 시나리오와 같음). 그렇지 않고 수동으로 갱신을 처리하려는 경우에는 이 설정을 해제해야 합니다. 만료되기 90일 이전인 경우에만 App Service Certificate를 수동으로 갱신할 수 있습니다.

## <a name="bkmk_Rekey"></a>인증서 키 다시 생성 및 동기화
1. 보안상의 이유로 인증서 키를 다시 생성하려면 **"인증서 속성"** 블레이드에서 **"키 다시 생성 및 동기화"** 옵션을 선택하면 됩니다. 
2. 프로세스를 시작하려면 **"키 다시 생성"** 단추를 클릭합니다. 이 프로세스는 완료하는 데 1-10분 정도 걸릴 수 있습니다. 
   
    ![SSL 키 다시 생성 이미지 삽입](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)
3. 인증서 키를 다시 생성하면 인증서가 인증 기관에서 발급한 새 인증서로 롤링됩니다.
4. 인증서의 수명 동안에는 키를 다시 생성하는 데 비용이 청구되지 않습니다. 
5. 인증서 키를 다시 생성하면 발급 보류 중 상태를 통과합니다. 
6. 인증서가 준비되면 이 인증서를 사용하여 리소스를 동기화하고 서비스가 중단되지 않도록 해야 합니다.
7. 동기화 옵션은 웹앱에 아직 할당되지 않은 인증서에는 사용할 수 없습니다. 

## <a name="more-resources"></a>추가 리소스
* [Azure 앱 서비스에서 앱에 대한 HTTPS를 사용하도록 설정](web-sites-configure-ssl-certificate.md)
* [Azure 앱 서비스에서 사용자 지정 도메인 이름 구입 및 구성](custom-dns-web-site-buydomains-web-app.md)
* [Microsoft Azure 보안 센터](/support/trust-center/security/)
* [Azure 웹 사이트에서 잠금 해제된 구성 옵션](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [Azure 관리 포털](https://manage.windowsazure.com)

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 체험](https://azure.microsoft.com/try/app-service/)으로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 


