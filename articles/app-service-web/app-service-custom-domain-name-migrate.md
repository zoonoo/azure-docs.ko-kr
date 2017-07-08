---
title: "Azure App Service로 활성 사용자 지정 도메인 마이그레이션 | Microsoft Docs"
description: "가동 중지 시간 없이 라이브 사이트에 이미 할당된 사용자 지정 도메인을 Azure App Service의 앱으로 마이그레이션하는 방법을 알아봅니다."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: 5f6537a45bcb092b5ef463e8069b9fc5582c14c2
ms.contentlocale: ko-kr
ms.lasthandoff: 06/14/2017


---
# <a name="migrate-an-active-custom-domain-to-azure-app-service"></a>Azure App Service로 활성 사용자 지정 도메인 마이그레이션

이 문서에서는 가동 중지 시간 없이 [Azure App Service](../app-service/app-service-value-prop-what-is.md)로 활성 사용자 지정 도메인을 마이그레이션하는 방법을 보여 줍니다.

라이브 사이트와 도메인 이름을 App Service로 마이그레이션할 때 해당 도메인 이름이 이미 라이브 트래픽을 제공하고 있으면 마이그레이션 프로세스 도중 DNS 확인에서 가동 중지 시간을 원하지 않습니다. 이 경우 도메인 확인을 위해 Azure 앱에 도메인 이름을 먼저 바인딩해야 합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 [App Service로 사용자 지정 도메인을 수동으로 매핑](app-service-web-tutorial-custom-domain.md)하는 방법을 이미 알고 있다고 가정합니다.

## <a name="bind-the-domain-name-preemptively"></a>도메인 이름을 먼저 바인딩

사용자 지정 도메인을 먼저 바인딩하는 경우 DNS 레코드를 변경하기 전에 다음 두 가지를 수행합니다.

- 도메인 소유권 확인
- 앱에 대해 도메인 이름 사용

마지막으로 App Service 앱을 가리키도록 DNS를 변경하는 경우 클라이언트는 DNS 확인 시 가동 중지 시간 없이 이전 사이트에서 App Service 앱으로 리디렉션됩니다.

다음 단계를 따르세요.

1. 첫째, [DNS 레코드 만들기](app-service-web-tutorial-custom-domain.md) 단계를 수행하여 DNS 레지스트리를 사용해서 확인 TXT 레코드를 만듭니다.
추가 TXT 레코드는 &lt;*subdomain*>.&lt;*rootdomain*>에서 &lt;*appname*>.azurewebsites.net으로 매핑하는 규칙을 사용합니다.
예는 다음 표를 참조하세요.  

    <table cellspacing="0" border="1">
    <tr>
    <th>FQDN 예</th>
    <th>TXT 호스트</th>
    <th>TXT 값</th>
    </tr>
    <tr>
    <td>contoso.com(루트)</td>
    <td>awverify.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>www.contoso.com(하위)</td>
    <td>awverify.www.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>\*.contoso.com(와일드카드)</td>
    <td>awverify\*.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    </table>

2. 그런 후 [앱에 대해 사용자 지정 도메인 이름 사용](app-service-web-tutorial-custom-domain.md#enable-a) 단계를 사용하여 Azure 앱에 사용자 지정 도메인 이름을 추가합니다.

    이제 Azure 앱에서 사용자 지정 도메인이 활성화되었습니다. 이제 남은 작업은 도메인 등록 기관을 통해 DNS 레코드를 업데이트하는 것입니다.

3. 마지막으로, [DNS 레코드 만들기](app-service-web-tutorial-custom-domain.md)에 표시된 대로 Azure 앱을 가리키도록 도메인의 DNS 레코드를 업데이트합니다.

    사용자 트래픽은 DNS가 전파된 후 바로 Azure 앱에 리디렉션되어야 합니다.

## <a name="next-steps"></a>다음 단계
[Azure에서 SSL 인증서를 구입](web-sites-purchase-ssl-web-site.md)하거나 [다른 곳에서 SSL 인증서를 사용](app-service-web-tutorial-custom-ssl.md)하여 HTTPS로 사용자 지정 도메인 이름의 보안을 유지하는 방법을 알아봅니다.

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 체험](https://azure.microsoft.com/try/app-service/)으로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
>
>

[Azure DNS 시작](../dns/dns-getstarted-create-dnszone.md)  
[사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기](../dns/dns-web-sites-custom-domain.md)  
[Azure DNS로 도메인 위임](../dns/dns-domain-delegation.md)

