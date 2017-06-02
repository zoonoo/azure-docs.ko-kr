---
title: "응용 프로그램 액세스 패널 브라우저 확장 설치 관련 문제 | Microsoft Docs"
description: "액세스 패널 브라우저 확장을 설치할 때 발생하는 일반적인 오류를 해결하는 방법"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 759631bca9b29134098dba55ef07513d0ee42549
ms.contentlocale: ko-kr
ms.lasthandoff: 05/26/2017


---

# <a name="problem-installing-the-application-access-panel-browser-extension"></a>응용 프로그램 액세스 패널 브라우저 확장 설치 관련 문제

액세스 패널은 웹 기반 포털로 Azure AD(Azure Active Directory)에 회사 또는 학교 계정이 있는 사용자가 Azure AD 관리자를 통해 액세스 권한을 부여받은 클라우드 기반 응용 프로그램을 보고 시작할 수 있도록 합니다. 또한 Azure AD 버전의 사용자는 액세스 패널을 통해 셀프 서비스 그룹 및 앱 관리 기능을 사용할 수도 있습니다. 액세스 패널은 Azure Portal과 별개이며, 사용자에게 Azure 구독을 요구하지 않습니다.

액세스 패널에서 암호 기반 SSO(Single Sign-On)를 사용하려면 사용자의 브라우저에 액세스 패널 확장이 설치되어 있어야 합니다. 사용자가 암호 기반 SSO에 구성된 응용 프로그램을 선택할 때 이 확장이 자동으로 다운로드됩니다.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>액세스 패널에 대한 브라우저 요구 사항 충족

액세스 패널에는 JavaScript를 지원하고 CSS를 사용하도록 설정한 브라우저가 필요합니다. 액세스 패널에서 암호 기반 SSO(Single Sign-On)를 사용하려면 사용자의 브라우저에 액세스 패널 확장이 설치되어 있어야 합니다. 사용자가 암호 기반 SSO에 구성된 응용 프로그램을 선택할 때 이 확장이 자동으로 다운로드됩니다.

암호 기반 SSO의 경우 최종 사용자 브라우저는 다음 중 하나일 수 있습니다.

-   Internet Explorer 8, 9, 10, 11 - Windows 7 이상

-   Chrome - Windows 7 이상 및 Mac OS X 이상

-   Firefox 26.0 이상 - Windows XP SP2 이상 및 Mac OS X 10.6 이상

**참고**: 브라우저 확장이 Edge에서 지원되는 경우 Windows 10의 Edge에 암호 기반 SSO 확장을 사용할 수 있습니다.

## <a name="how-to-install-the-access-panel-browser-extension"></a>액세스 패널 브라우저 확장을 설치하는 방법

액세스 패널 브라우저 확장을 설치하려면 아래 단계를 수행합니다.

1.  지원되는 브라우저 중 하나에서 [액세스 패널](https://myapps.microsoft.com)을 열고 Azure AD에서 **사용자**로 로그인합니다.

2.  액세스 패널에서 **암호-SSO 응용 프로그램**을 클릭합니다.

3.  소프트웨어를 설치하라는 프롬프트에서 **지금 설치**를 선택합니다.

4.  브라우저에 따라 다운로드 링크로 이동됩니다. 브라우저에 확장을 **추가**합니다.

5.  브라우저에서 요청하면 확장을 **사용** 또는 **허용**하도록 선택합니다.

6.  설치되면 브라우저 세션을 **다시 시작**합니다.

7.  액세스 패널에 로그인하고 암호 SSO 응용 프로그램을 **시작**할 수 있는지 확인합니다.

아래와 같은 직접 링크에서 Chrome 및 Firefox에 대한 확장을 다운로드할 수 있습니다.

-   [Chrome 액세스 패널 확장](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 액세스 패널 확장](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Internet Explorer에 대한 그룹 정책 설정

사용자의 컴퓨터에 Internet Explorer용 액세스 패널 확장을 원격으로 설치할 수 있는 그룹 정책을 설정할 수 있습니다.

필수 구성 요소는 다음과 같습니다.

-   [Active Directory 도메인 서비스](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)를 설정하고, 사용자 컴퓨터를 도메인에 가입시킨 상태여야 합니다.

-   그룹 정책 개체(GPO)를 편집하는 "설정 편집" 사용 권한이 있어야 합니다. 기본적으로 도메인 관리자, 엔터프라이즈 관리자 및 그룹 정책 작성자/소유자 보안 그룹의 멤버에게 이 권한이 있습니다. [자세히 알아보기](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

자습서에 따라 그룹 정책을 구성하고 사용자에게 배포하는 방법에 대한 단계별 지침은 [그룹 정책을 사용하여 Internet Explorer용 액세스 패널 확장을 배포하는 방법](active-directory-saas-ie-group-policy.md)을 따릅니다.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Internet Explorer에서 액세스 패널 문제 해결

진단 도구 및 IE용 확장을 구성하는 방법에 대한 단계별 지침에 액세스하려면 [Internet Explorer의 액세스 패널 확장 문제 해결](active-directory-saas-ie-troubleshooting.md) 가이드를 따릅니다.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>이러한 문제 해결 단계로 문제가 해결되지 않는 경우

사용할 수 있는 경우 다음 정보로 지원 티켓을 엽니다.

-   상관 관계 오류 ID

-   UPN(사용자 전자 메일 주소)

-   TenantID

-   브라우저 종류

-   오류가 발생하는 동안 표준 시간대 및 시간/기간

-   Fiddler 추적

## <a name="next-steps"></a>다음 단계
[Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

