---
title: Microsoft Azure Active Directory Single Sign-On 플러그 인 관리자 가이드 | Microsoft Docs
description: Azure Active Directory 및 JIRA에 대한 Microsoft Azure Active Directory Single Sign-On 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: af949d1db8af37a534a16364f9f0763479c436e4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-admin-guide"></a>Microsoft Azure Active Directory Single Sign-On 플러그 인 관리자 가이드

## <a name="table-of-contents"></a>목차

1. **[개요](#overview)**
2. **[작동 방법](#how-it-works)**
3. **[대상](#audience)**
4. **[가정](#assumptions)**
5. **[필수 조건](#prerequisites)**
6. **[지원되는 JIRA 및 CONFLUENCE 버전](#supported-versions-of-jira-and-confluence)**
7. **[설치](#installation)**
8. **[플러그 인 구성](#plugin-configuration)**
9. **[추가 기능 구성 화면에 대한 필드 설명:](#field-explanation-for-add---on-configuration-screen:)**
10. **[문제 해결](#troubleshooting)**

## <a name="overview"></a>개요

이러한 추가 기능은 Microsoft Azure AD 고객이 조직 사용자 이름과 암호를 사용하여 Atlassian Jira 및 Confluence Server 기반 제품에 로그인할 수 있게 해줍니다. SAML 2.0 기반 SSO를 구현합니다.

## <a name="how-it-works"></a>작동 방법

사용자가 Atlassian Jira 또는 Confluence 응용 프로그램에 로그인하려는 경우 로그인 페이지에 **Azure AD로 로그인** 단추가 보입니다. 이 단추를 클릭하면 Azure AD 조직 로그인 페이지를 사용하여 로그인해야 합니다.

사용자가 인증되면 응용 프로그램에 로그인할 수 있습니다. 사용자가 이미 조직 ID 및 암호를 사용하여 인증된 경우에는 응용 프로그램에 바로 로그인됩니다. 로그인은 JIRA 및 Confluence에서도 작동합니다. 사용자가 JIRA 응용 프로그램에 로그인하면 같은 브라우저 창에서 Confluence도 열리며, 한 번만 로그인하면 다른 앱에서 자격 증명을 다시 제공할 필요가 없습니다. 또한 사용자는 Azure 계정 하에서 myapps를 통해 Atlassian 제품에 액세스할 수 있으며 자격 증명을 입력하지 않아도 로그인됩니다.

> [!NOTE]
> 사용자 프로비전은 이 추가 기능을 사용하여 수행되지 않습니다.

## <a name="audience"></a>대상

이 플러그 인을 사용하여 Azure AD를 통한 SSO를 설정하려는 JIRA 및 Confluence 관리자.

## <a name="assumptions"></a>가정

* JIRA/Confluence 인스턴스에서 HTTPS 사용
* JIRA/Confluence에서 사용자를 이미 생성
* JIRA/Confluence에서 사용자에게 역할 할당
* 관리자가 플러그 인 구성에 필요한 정보에 액세스 가능
* 회사 네트워크 외부에서도 JIRA/Confluence 사용 가능
* 추가 기능이 JIRA 및 Confluence의 온-프레미스 버전에서만 작동

## <a name="prerequisites"></a>필수 조건

추가 기능을 설치하기 전에 다음 필수 구성 요소를 확인하세요.

* Windows 64비트 버전에 JIRA/Confluence가 설치되어 있음
* JIRA/Confluence 버전에서 HTTPS가 사용됨
* 아래의 "지원되는 버전" 섹션에서 플러그 인에 지원되는 버전을 살펴보세요.
* JIRA/Confluence는 인터넷에서 받을 수 있습니다.
* JIRA/Confluence에 대한 관리자 자격 증명
* Azure AD에 대한 관리자 자격 증명
* WebSudo는 JIRA 및 Confluence에서 해제해야 함

## <a name="supported-versions-of-jira-and-confluence"></a>지원되는 JIRA 및 Confluence 버전

현재 다음 버전의 JIRA 및 Confluence 버전이 지원됩니다.

* JIRA 코어 및 소프트웨어: 6.0 ~ 7.2.0
* JIRA 서비스 데스크: 3.0 ~ 3.2
* Confluence: 5.0 ~ 5.10

## <a name="installation"></a>설치

관리자는 아래에 설명된 단계에 따라 플러그 인을 설치해야 합니다.

1. JIRA/Confluence 인스턴스에 관리자로 로그인합니다.
    
2. JIRA/Confluence 관리로 이동하여 추가 기능을 클릭합니다.
    
3. Atlassian Market place에서 **Microsoft SAML SSO 플러그 인**을 검색합니다.
 
4. 적절한 추가 기능 버전이 검색에 표시됩니다.
 
5. 플러그 인을 선택하면 UPM이 동일한 버전을 설치합니다.
 
6. 플러그 인이 설치되면 추가 기능 관리 섹션의 사용자가 설치한 추가 기능 섹션에 표시됩니다.
 
7. 플러그 인을 사용하려면 먼저 플러그 인을 구성해야 합니다.
 
8. 플러그 인을 클릭하면 구성 단추가 보입니다.
 
9. 이 단추를 클릭하고 구성 입력을 제공합니다.
    
## <a name="plugin-configuration"></a>플러그 인 구성

다음 이미지는 JIRA 및 Confluence의 추가 기능 구성 화면을 보여줍니다.
    
![추가 기능 구성](./media/ms-confluence-jira-plugin-adminguide/jira.png)

### <a name="field-explanation-for-add-on-configuration-screen"></a>추가 기능 구성 화면에 대한 필드 설명:

1.   메타데이터 URL: Azure AD에서 페더레이션 메타데이터를 가져오는 URL
 
2.   식별자: Azure AD가 요청의 소스 유효성을 검사하는 데 사용됩니다. Azure AD의 식별자 요소에 매핑됩니다. 자동으로 플러그 인에서 https://<domain:port>/로 파생됩니다.
 
3.   회신 URL: SAML 로그인을 시작하려면 IdP에 회신 URL을 사용합니다. Azure AD의 회신 URL 요소에 매핑됩니다. 자동으로 플러그 인에서 https://<domain:port>/plugins/servlet/saml/auth로 파생됩니다.
 
4.   로그온 URL: SAML 로그인을 시작하려면 IdP에 로그온 URL을 사용합니다. Azure AD의 로그온 요소에 매핑됩니다. 자동으로 플러그 인에서 https://<domain:port>/plugins/servlet/saml/auth로 파생됩니다.
 
5.   IdP 엔터티 ID: IdP에서 사용하는 엔터티 ID입니다. 메타데이터 URL을 확인할 때 채워집니다.
 
6.   로그인 URL: IdP의 로그인 URL입니다. 메타데이터 URL을 확인할 때 Azure AD에서 채워집니다.
 
7.   로그아웃 URL: IdP의 로그아웃 URL입니다. 메타데이터 URL을 확인할 때 Azure AD에서 채워집니다.
 
8.   X.509 인증서: IdP의 X.509 인증서입니다. 메타데이터 URL을 확인할 때 Azure AD에서 채워집니다.
 
9.   로그인 단추 이름: 조직에서 원하는 로그인 단추 이름을 지정합니다. 이 텍스트는 로그인 화면의 로그인 단추에 표시됩니다.
 
10.   SAML 사용자 ID 위치: SAML 응답에서 사용자 id가 있을 것으로 예상되는 위치입니다. NameID에 있을 수도 있고 사용자 지정 특성 이름에 있을 수도 있습니다. 이 ID는 JIRA/Confluence 사용자 id여야 합니다.
 
11.   특성 이름: 사용자 Id를 예상할 수 있는 특성 이름입니다.
 
12.   홈 영역 검색 사용: 회사에서 ADFS 기반 로그인을 사용하는 경우 이 플래그를 확인합니다.
 
13.   도메인 이름: ADFS 기반 로그인을 사용하는 경우 여기에 도메인 이름을 입력합니다.
 
14.   Single Sign-Out 사용: 사용자가 JIRA/Confluence에서 로그아웃할 때 Azure AD에서 로그아웃하려면 이 옵션을 선택합니다.

### <a name="troubleshooting"></a>문제 해결

* 여러 인증서 오류가 발생하는 경우
    
    * Azure AD에 로그인하여 앱에서 사용할 수 있는 여러 인증서를 제거합니다. 인증서를 하나만 남겨 둡니다.

* 인증서가 Azure AD에서 곧 만료됩니다.
    
    * 추가 기능이 인증서의 자동 롤오버를 처리합니다. 인증서가 곧 만료되는 경우 새 인증서를 활성으로 표시하고 사용하지 않는 인증서를 삭제해야 합니다. 이 시나리오에서 사용자가 JIRA에 로그인하려고 시도하면 추가 기능은 새 인증서를 가져와서 플러그 인에 저장합니다.

* WebSudo를 사용하지 않도록 설정하는 방법(보안 관리자 세션을 해제)
    
    * JIRA: 보안 관리자 세션(즉, 관리 기능에 액세스하기 전에 암호 확인)이 기본적으로 사용됩니다. JIRA 인스턴스에서 이 옵션을 해제하려면 jira-config.properties 파일에서 "ira.websudo.is.disabled = true" 줄을 지정하여 이 기능을 해제합니다.
    
    * Confluence: 다음 URL https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html에 제공된 단계를 따릅니다.

* 메타데이터 URL에서 채우는 필드는 채워지지 않습니다.
    
    * URL이 올바른지 확인합니다. 올바른 테넌트 및 앱 id를 매핑했는지 확인합니다.
    
    * 브라우저에서 URL을 누르고 페더레이션 메타데이터 XML을 수신하는지 확인합니다.

* 내부 서버 오류:
    
    * 설치의 로그 디렉터리에 있는 로그를 살펴봅니다. 사용자가 Azure AD SSO를 사용하여 로그인하려고 시도하면 오류가 발생하는 경우 이 문서의 아래에 제공된 지원 정보와 함께 로그를 공유할 수 있습니다.

* 사용자가 로그인하려고 할 때 사용자 ID를 찾을 수 없는 오류
    
    * JIRA/Confluence에서 사용자가 만들어지지 않았습니다. 따라서 같은 사용자를 만들어야 합니다.

* Azure AD에서 앱을 찾을 수 없는 오류
    
    * Azure AD에서 적절한 URL이 앱에 매핑되는지 확인합니다.

* 지원 세부 정보: [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)으로 문의하세요. 영업 시간 기준으로 24-48시간 내에 대답해 드립니다.
    
    * 또한 Azure Portal 채널을 통해 Microsoft 지원 티켓을 발급할 수 있습니다.