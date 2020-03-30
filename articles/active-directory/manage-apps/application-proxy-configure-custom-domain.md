---
title: Azure AD 애플리케이션 프록시의 사용자 지정 도메인 | Microsoft Docs
description: Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인을 구성하고 관리합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f35658a75adb4d4c6c279e45087e741b8117e65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481384"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용 하 고 사용자 지정 도메인 구성

Azure Active Directory 응용 프로그램 프록시를 통해 응용 프로그램을 게시할 때 사용자에 대 한 외부 URL을 만듭니다. 이 URL은 기본 도메인 *yourtenant.msappproxy.net*가져옵니다. 예를 들어 *Contoso라는*테넌트에 *Expenses라는* 앱을 게시하는 경우 외부 URL은 *https입니다 expenses-contoso.msappproxy.net.\/* *msappproxy.net*대신 고유한 도메인 이름을 사용하려는 경우 응용 프로그램에 대한 사용자 지정 도메인을 구성할 수 있습니다. 

## <a name="benefits-of-custom-domains"></a>사용자 지정 도메인의 이점

가능하면 앱에 대한 맞춤 도메인을 설정하는 것이 좋습니다. 사용자 지정 도메인을 사용하는 몇 가지 이유는 다음과 같습니다.

- 앱 간의 링크는 회사 네트워크 외부에서도 작동합니다. 사용자 지정 도메인이 없으면 앱에 응용 프로그램 프록시 외부의 대상에 대한 하드 코딩된 내부 링크가 있고 링크가 외부에서 해결할 수 없는 경우 연결이 끊어집니다. 내부 및 외부 URL이 동일한 경우 이 문제를 방지할 수 있습니다. 사용자 지정 도메인을 사용할 수 없는 경우 이 문제를 해결하는 다른 방법은 [Azure AD 응용 프로그램 프록시에 게시된 앱에 대한 하드코딩된 링크 리디렉션을](../application-proxy-link-translation.md) 참조하세요. 
  
- 사용자는 네트워크 내부 또는 외부에서 동일한 URL을 사용하여 앱에 도착할 수 있으므로 더 쉽게 환경을 경험할 수 있습니다. 다른 내부 및 외부 URL을 배우거나 현재 위치를 추적할 필요가 없습니다. 

- 브랜딩을 제어하고 원하는 URL을 만들 수 있습니다. 사용자 지정 도메인은 사용자가 *msappproxy.net*대신 친숙한 이름을 보고 사용하기 때문에 사용자의 신뢰를 구축하는 데 도움이 될 수 있습니다.

- 일부 구성은 사용자 지정 도메인에서만 작동합니다. 예를 들어 AD FS(Active Directory Federation Services)를 사용하지만 WS-페더레이션을 사용할 수 없는 경우와 같이 SAML(보안 어설션 태그 언어)을 사용하는 앱에 대한 사용자 지정 도메인이 필요합니다. 자세한 내용은 [응용 프로그램 프록시에서 클레임 인식 앱으로 작업](application-proxy-configure-for-claims-aware-applications.md)작업을 참조하십시오. 

내부 URL과 외부 URL을 일치시킬 수 없는 경우 맞춤 도메인을 사용하는 것이 중요하지 않지만 다른 이점을 활용할 수 있습니다. 

## <a name="dns-configuration-options"></a>DNS 구성 옵션

요구 사항에 따라 DNS 구성을 설정하는 몇 가지 옵션이 있습니다.

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>동일한 내부 및 외부 URL, 다른 내부 및 외부 동작 

내부 사용자가 응용 프로그램 프록시를 통해 전달되지 않도록 하려면 분할 *브레인 DNS를*설정할 수 있습니다. 분할 DNS 인프라는 내부 호스트를 내부 도메인 이름 서버로, 외부 호스트는 이름 확인을 위해 외부 도메인 이름 서버로 연결합니다. 

![스플릿 브레인(Split-Brain) DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>다양한 내부 및 외부 URL 

내부 URL과 외부 URL이 다른 경우 사용자 라우팅이 URL에 의해 결정되므로 분할 브레인 동작을 구성할 필요가 없습니다. 이 경우 외부 DNS만 변경하고 외부 URL을 응용 프로그램 프록시 끝점으로 라우팅합니다. 

외부 URL에 대한 사용자 지정 도메인을 선택하면 외부 DNS 공급자에 추가해야 하는 CNAME 항목이 정보 표시줄에 표시됩니다. 앱의 **응용 프로그램 프록시** 페이지로 이동하여 항상 이 정보를 볼 수 있습니다.

## <a name="set-up-and-use-custom-domains"></a>사용자 지정 도메인 설정 및 사용

사용자 지정 도메인을 사용하도록 온-프레미스 앱을 구성하려면 확인된 Azure Active Directory 사용자 지정 도메인, 사용자 지정 도메인에 대한 PFX 인증서 및 구성할 온-프레미스 앱이 필요합니다. 

### <a name="create-and-verify-a-custom-domain"></a>사용자 지정 도메인 만들기 및 확인

사용자 지정 도메인을 만들고 확인하려면 다음을 수행하십시오.

1. Azure Active Directory에서 왼쪽 탐색에서 **사용자 지정 도메인 이름을** 선택한 다음 사용자 지정 도메인 **추가를**선택합니다. 
1. 사용자 지정 도메인 이름을 입력하고 **도메인 추가를**선택합니다. 
1. 도메인 페이지에서 도메인의 TXT 레코드 정보를 복사합니다. 
1. 도메인 등록 기관으로 이동하여 복사된 DNS 정보를 기반으로 도메인에 대한 새 TXT 레코드를 만듭니다.
1. 도메인을 등록한 후 Azure Active Directory의 도메인 페이지에서 **확인을**선택합니다. 도메인 상태가 **확인되면**응용 프로그램 프록시를 포함한 모든 Azure AD 구성에서 도메인을 사용할 수 있습니다. 

자세한 지침은 Azure [Active Directory 포털을 사용하여 사용자 지정 도메인 이름 추가를](../fundamentals/add-custom-domain.md)참조하십시오.

### <a name="configure-an-app-to-use-a-custom-domain"></a>사용자 지정 도메인을 사용하도록 앱 구성

사용자 지정 도메인을 사용하여 응용 프로그램 프록시를 통해 앱을 게시하려면 다음을 수행합니다.

1. 새 앱의 경우 Azure Active Directory에서 왼쪽 탐색에서 **엔터프라이즈 응용 프로그램을 선택합니다.** **새 응용 프로그램을**선택합니다. **온-프레미스 애플리케이션** 섹션에서 **온-프레미스 애플리케이션 추가**를 선택합니다. 
   
   엔터프라이즈 응용 프로그램에 이미 있는 **앱의**경우 목록에서 선택한 다음 왼쪽 탐색에서 **응용 프로그램 프록시를** 선택합니다. 

2. 응용 프로그램 프록시 설정 페이지에서 자체 온-프레미스 응용 프로그램을 추가하는 경우 **이름을** 입력합니다.

3.  내부 **URL** 필드에 앱의 내부 URL을 입력합니다.
   
4. 외부 **URL** 필드에서 목록을 드롭다운하고 사용할 사용자 지정 도메인을 선택합니다.
   
5. **추가**를 선택합니다.
   
   ![사용자 지정 도메인 선택](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. 도메인에 인증서가 이미 있는 경우 **인증서** 필드에 인증서 정보가 표시됩니다. 그렇지 않으면 **인증서** 필드를 선택합니다. 
   
   ![인증서를 업로드하려면 클릭](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. **SSL 인증서** 페이지에서 PFX 인증서 파일을 찾아보고 선택합니다. 인증서의 암호를 입력하고 **인증서 업로드를**선택합니다. 인증서에 대한 자세한 내용은 [사용자 지정 도메인에 대한 인증서](#certificates-for-custom-domains) 섹션을 참조하십시오.
   
   ![인증서 업로드](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > 사용자 지정 도메인은 인증서를 한 번만 업로드하면 됩니다. 그런 다음 다른 앱에 대한 사용자 지정 도메인을 사용할 때 업로드된 인증서가 자동으로 적용됩니다.
   
8. 인증서를 추가한 경우 **응용 프로그램 프록시** 페이지에서 **저장을**선택합니다. 
   
9. **응용 프로그램 프록시** 페이지의 정보 표시줄에서 DNS 영역에 추가해야 하는 CNAME 항목을 확인합니다. 
   
   ![CNAME DNS 항목 추가](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Azure 포털을 사용하여 새 외부 URL을 *msappproxy.net* 도메인으로 리디렉션하는 DNS 레코드를 추가하여 [DNS 레코드 및 레코드 집합 관리의](../../dns/dns-operations-recordsets-portal.md) 지침을 따릅니다.
   
11. DNS 레코드가 올바르게 구성되어 있는지 확인하려면 [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) 명령을 사용하여 외부 URL에 연결할 수 있고 *msapproxy.net* 도메인이 별칭으로 표시되는지 확인합니다.

이제 응용 프로그램이 사용자 지정 도메인을 사용하도록 설정되었습니다. 사용자를 테스트하거나 릴리스하기 전에 응용 프로그램에 사용자를 할당해야 합니다. 

앱의 도메인을 변경하려면 앱의 **응용 프로그램 프록시** 페이지의 외부 **URL의** 드롭다운 목록에서 다른 도메인을 선택합니다. 필요한 경우 업데이트된 도메인에 대한 인증서를 업로드하고 DNS 레코드를 업데이트합니다. **외부 URL의**드롭다운 목록에 원하는 사용자 지정 도메인이 표시되지 않으면 확인되지 않을 수 있습니다.

응용 프로그램 프록시에 대한 자세한 지침은 [자습서: Azure Active Directory의 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가를](application-proxy-add-on-premises-application.md)참조하십시오.

## <a name="certificates-for-custom-domains"></a>사용자 지정 도메인에 대한 인증서

인증서는 사용자 지정 도메인에 대한 보안 TLS 연결을 만듭니다. 

### <a name="certificate-formats"></a>인증서 형식

필요한 모든 중간 인증서가 포함되도록 하려면 PFX 인증서를 사용해야 합니다. 인증서에는 개인 키가 포함되어야 합니다.

인증서 서명 방법에는 제한이 없습니다. 타원 곡선 암호화(ECC), 주체 대체 이름(SAN) 및 기타 일반적인 인증서 유형이 지원됩니다. 

와일드카드가 외부 URL과 일치하는 한 와일드카드 인증서를 사용할 수 있습니다. 와일드카드 응용 프로그램에 와일드카드 [인증서를](application-proxy-wildcard.md)사용해야 합니다. 인증서를 사용하여 하위 도메인에도 액세스하려면 하위 도메인 와일드카드를 동일한 인증서의 주체 대체 이름으로 추가해야 합니다. 예를 들어 * \*.adventure-works.com* 인증서는 * \*.apps.adventure-works.com* 제목 대체 이름으로 * \*.apps.adventure-works.com* 추가하지 않는 한 작동하지 않습니다. 

클라이언트 장치에 인증서 체인이 설치된 경우 PKI(공개 키 인프라)에서 발급한 인증서를 사용할 수 있습니다. Intune은 이러한 인증서를 관리되는 장치에 배포할 수 있습니다. 관리되지 않는 장치의 경우 이러한 인증서를 수동으로 설치해야 합니다. 

개인 루트 CA도 클라이언트 컴퓨터에 푸시해야 하므로 개인 루트 CA를 사용하지 않는 것이 좋습니다.

### <a name="certificate-management"></a>인증서 관리

모든 인증서 관리는 개별 응용 프로그램 페이지를 통해 관리됩니다. 응용 프로그램의 응용 **프로그램 프록시** 페이지로 이동하여 **인증서** 필드에 액세스합니다.

여러 응용 프로그램에 대해 동일한 인증서를 사용할 수 있습니다. 업로드된 인증서가 다른 응용 프로그램과 함께 작동하면 자동으로 적용됩니다. 앱을 추가하거나 구성할 때 다시 업로드하라는 메시지가 표시되지 않습니다. 

인증서가 만료되면 다른 인증서를 업로드하라는 경고가 표시됩니다. 인증서가 해지되면 앱에 액세스할 때 사용자에게 보안 경고가 표시될 수 있습니다. 앱의 인증서를 업데이트하려면 앱의 **응용 프로그램 프록시** 페이지로 이동하여 **인증서를**선택하고 새 인증서를 업로드합니다. 이전 인증서가 다른 앱에서 사용되지 않으면 자동으로 삭제됩니다. 

## <a name="next-steps"></a>다음 단계
* Azure AD 인증을 사용하여 게시된 앱에 대해 [Single Sign-On 사용](application-proxy-configure-single-sign-on-with-kcd.md)
* 게시된 앱에 대한 [조건부 액세스를 사용하도록 설정합니다.](../conditional-access/overview.md)

