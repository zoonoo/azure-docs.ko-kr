---
title: Azure Active Directory를 사용 하 여 헤더 기반 인증
description: 이 인증 패턴을 달성 하는 데 대 한 아키텍처 지침
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f364e4e14dd1b7c60cb81f06051d9dedd94396a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114459"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Azure Active Directory를 사용 하 여 헤더 기반 인증

레거시 응용 프로그램은 일반적으로 헤더 기반 인증을 사용 합니다. 이 시나리오에서는 사용자 (또는 메시지 보낸 사람)가 중간 id 솔루션을 인증 합니다. 중간 솔루션은 사용자를 인증 하 고 필요한 하이퍼텍스트 전송 프로토콜 (HTTP) 헤더를 대상 웹 서비스로 전파 합니다. AD (Azure Active Directory)는 응용 프로그램 프록시 서비스를 통해이 패턴을 지원 하 고 다른 네트워크 컨트롤러 솔루션과의 통합을 지원 합니다. 

이 솔루션에서 응용 프로그램 프록시는 응용 프로그램에 대 한 원격 액세스를 제공 하 고, 사용자를 인증 하 고, 응용 프로그램에 필요한 헤더를 전달 합니다. 

## <a name="use-when"></a>적용 가능한 상황

원격 사용자는 헤더 기반 인증이 필요한 온-프레미스 응용 프로그램에 안전 하 게 (SSO)를 Single Sign-On 해야 합니다.

![아키텍처 이미지 헤더 기반 인증](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자**: 응용 프로그램 프록시에서 제공 하는 레거시 응용 프로그램에 액세스 합니다.

* **웹 브라우저**: 응용 프로그램의 외부 URL에 액세스 하기 위해 사용자가 상호 작용 하는 구성 요소입니다.

* **AZURE AD**: 사용자를 인증 합니다. 

* **응용 프로그램 프록시 서비스**: 사용자 로부터 온-프레미스 응용 프로그램으로 요청을 보내는 역방향 프록시로 작동 합니다. Azure AD에 상주 하며 모든 조건부 액세스 정책을 적용할 수도 있습니다.

* **응용 프로그램 프록시 커넥터**: 응용 프로그램에 대 한 연결을 제공 하기 위해 Windows server의 온-프레미스에 설치 됩니다. 아웃 바운드 연결만 사용 합니다. Azure AD에 대 한 응답을 반환 합니다.

* **레거시 응용 프로그램**: 응용 프로그램 프록시에서 사용자 요청을 수신 하는 응용 프로그램입니다. 레거시 응용 프로그램은 세션을 설정 하 고 응답을 반환 하는 데 필요한 HTTP 헤더를 수신 합니다. 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Azure AD를 사용 하 여 헤더 기반 인증 구현

* [Azure AD에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)  

* [애플리케이션 프록시 및 PingAccess를 사용하여 Single Sign-On에 대한 헤더 기반 인증](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-ping-access) 

* [앱 제공 컨트롤러 및 네트워크를 사용하여 레거시 앱 보호](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
