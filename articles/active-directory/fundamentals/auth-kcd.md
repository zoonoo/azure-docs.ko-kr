---
title: Azure Active Directory를 사용 하는 Kerberos 제한 위임
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
ms.openlocfilehash: 77f90cd7aa8d972226a8f134eaa7b3abfe7bea66
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114460"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Windows 인증-Azure Active Directory를 사용 하는 Kerberos 제한 위임

KCD (Kerberos 제한 위임)는 리소스 간의 제한 된 위임을 제공 하며 서비스 주체 이름을 기반으로 합니다. 도메인 관리자가 위임을 만들어야 하며 단일 도메인으로 제한 됩니다. 리소스 기반 KCD는 Active Directory 포리스트 내에서 여러 도메인의 사용자를 포함 하는 웹 응용 프로그램에 대 한 Kerberos 인증을 제공 하는 방법으로 사용 되는 경우가 많습니다.

Azure Active Directory 응용 프로그램 프록시는 액세스 및 KCD (Kerberos 제한 위임)를 위해 Kerberos 티켓이 필요한 KCD 기반 응용 프로그램에 대 한 Single Sign-On (SSO) 및 원격 액세스를 제공할 수 있습니다.

IWA (Windows 통합 인증)를 사용 하는 온-프레미스 KCD 응용 프로그램에 SSO를 사용 하도록 설정 합니다. 응용 프로그램 프록시 커넥터에는 Active Directory에서 사용자를 가장할 수 있는 권한이 부여 됩니다. 응용 프로그램 프록시 커넥터는이 사용 권한을 사용 하 여 사용자를 대신 하 여 토큰을 보내고 받습니다.

## <a name="use-when"></a>적용 가능한 상황

원격 액세스를 제공 하 고, 사전 인증을 사용 하 여 보호 하 고, 온-프레미스 IWA 응용 프로그램에 SSO를 제공 해야 합니다.

![아키텍처 다이어그램](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자**: 응용 프로그램 프록시에서 제공 하는 레거시 응용 프로그램에 액세스 합니다.

* **웹 브라우저**: 응용 프로그램의 외부 URL에 액세스 하기 위해 사용자가 상호 작용 하는 구성 요소입니다.

* **AZURE AD**: 사용자를 인증 합니다. 

* **응용 프로그램 프록시 서비스**: 사용자 로부터 온-프레미스 응용 프로그램으로 요청을 보내는 역방향 프록시로 작동 합니다. Azure AD에 있습니다. 응용 프로그램 프록시는 모든 조건부 액세스 정책을 적용할 수도 있습니다.

* **응용 프로그램 프록시 커넥터**: 응용 프로그램에 대 한 연결을 제공 하기 위해 Windows server의 온-프레미스에 설치 됩니다. Azure AD에 대 한 응답을 반환 합니다. Active Directory와 함께 KCD 협상을 수행 하 고, 사용자를 가장 하 여 응용 프로그램에 대 한 Kerberos 토큰을 가져옵니다.

* **Active Directory**: 응용 프로그램 프록시 커넥터에 응용 프로그램에 대 한 Kerberos 토큰을 보냅니다.

* **레거시 응용 프로그램**: 응용 프로그램 프록시에서 사용자 요청을 수신 하는 응용 프로그램입니다. 레거시 응용 프로그램은 응용 프로그램 프록시 커넥터에 대 한 응답을 반환 합니다.

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Azure AD를 사용 하 여 Windows 인증 (KCD) 구현

* [애플리케이션 프록시를 사용하여 앱에 Single Sign-On에 대한 Kerberos 제한된 위임](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd) 

* [Azure Active Directory에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
