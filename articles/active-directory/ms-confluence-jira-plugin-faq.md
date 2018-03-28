---
title: Microsoft Azure Active Directory Single Sign-On 플러그 인 FAQ | Microsoft Docs
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
ms.openlocfilehash: 571fbd5078f66375f6e81cba2a790121366f9d60
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-faq"></a>Microsoft Azure Active Directory Single Sign-On 플러그 인 FAQ 

## <a name="1-whats-the-microsoft-sso-add-on"></a>1. Microsoft SSO 추가 기능이란?

이 추가 기능은 Atlassian의 JIRA(JIRA Core, JIRA Software, JIRA Service Desk 포함) 및 Confluence 온-프레미스 소프트웨어에 대한 Single Sign On 기능을 제공합니다. 추가 기능은 IdP로 Azure AD와 함께 작동합니다.

## <a name="2-add-on-works-with-which-atlassian-products"></a>2. 추가 기능은 Atlassian 제품과 함께 작동하나요?

현재 추가 기능은 JIRA 및 Confluence의 온-프레미스 버전과 함께 작동합니다.

## <a name="3-does-this-add-on-work-on-cloud-version"></a>3. 이 추가 기능은 클라우드 버전에서 작동하나요?

번호 JIRA 및 Confluence 온-프레미스 버전만 지원됩니다.

## <a name="4-which-versions-of-jira-and-confluence-are-supported"></a>4. 지원되는 JIRA 및 Confluence 버전은 무엇인가요?

아래는 지원되는 버전 목록입니다.

* JIRA Core 및 Software: 6.0 ~ 7.2.2 
* JIRA 서비스 데스크: 3.0 ~ 3.2 
* Confluence: 5.0 ~ 5.10

## <a name="5-is-this-add-on-free-or-paid"></a>5. 이 추가 기능은 무료인가요 아니면 유료인가요?

이 추가 기능은 무료이며 Atlassian 마켓플레이스에서 설치할 수 있습니다.

## <a name="6-do-i-need-to-restart-jiraconfluence-once-i-deploy-the-add-on"></a>6. 추가 기능을 배포한 후 JIRA/Confluence를 다시 시작해야 하나요?

추가 기능 배포 후 다시 시작할 필요가 없습니다. 배포 후 즉시 추가 기능을 사용할 수 있습니다.

## <a name="7-how-do-i-get-support-for-the-add-on"></a>7. 추가 기능에 대한 지원을 받으려면 어떻게 하나요?

<email>로 연락을 주시면 <> 시간 내에 대답해 드립니다. 또한 Azure Portal 채널을 통해 Microsoft 지원 티켓을 발급할 수 있습니다. 평일 오전 <>시부터 오후 <>시 사이에 <Number>으로 전화를 주셔도 됩니다.

## <a name="8-would-this-add-on-work-on-mac-or-ubuntu-installation-of-jira-and-confluence"></a>8. 이 추가 기능은 JIRA 및 Confluence에 설치된 Mac 또는 Ubuntu에서 작동하나요?

이 추가 기능은 JIRA 및 Confluence에 설치된 64비트 Windows 서버에서만 테스트를 거쳤습니다.

## <a name="9-does-this-add-on-work-with-other-idps-than-azure-ad"></a>9. 이 추가 기능은 Azure AD 이외의 다른 IdP와 함께 작동하나요?

번호 이 추가 기능은 Azure AD와만 함께 작동합니다.

## <a name="10-what-version-of-saml-does-the-add-on-work-with"></a>10. 이 추가 기능과 함께 작동하는 SAML 버전은 무엇인가요?

이 추가 기능은 SAML 2.0과 함께 작동합니다.

## <a name="11-does-the-add-on-do-use-provisioning-as-well"></a>11. 이 추가 기능은 프로비전도 사용하나요?

번호 현재 이 추가 기능은 SAML 2.0 기반 SSO만 제공합니다. SSO 로그인 전에 응용 프로그램에서 사용자를 프로비전해야 합니다.

## <a name="12-are-cluster-versions-of-jira-and-confluence-supported-by-add-on"></a>12. 이 추가 기능이 JIRA 및 Confluence의 클러스터 버전을 지원하나요?

번호 이 추가 기능은 JIRA 및 Confluence의 온-프레미스 버전과 함께 작동합니다.

## <a name="13-would-this-plugin-work-with-http-version-of-jira-and-confluence"></a>13. 이 플러그 인은 JIRA 및 Confluence의 HTTP 버전과 함께 작동하나요?

번호 이 추가 기능은 HTTPS가 설정된 설치 버전에서만 작동합니다.

## <a name="14-do-i-need-to-buy-license-of-the-add-on"></a>14. 추가 기능 라이선스를 구입해야 하나요?

이 추가 기능은 무료입니다.