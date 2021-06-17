---
title: Azure Active Directory를 사용하는 서비스 원격 데스크톱 게이트웨이
description: Azure Active Directory를 사용하여 원격 데스크톱 게이트웨이 서비스를 실현하는 아키텍처 지침입니다.
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
ms.openlocfilehash: 9208a0076038dd9977c1488ab25836e249e290d0
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111966030"
---
# <a name="remote-desktop-gateway-services"></a>원격 데스크톱 게이트웨이 서비스

표준 RDS(원격 데스크톱 서비스) 배포에는 Windows Server에서 실행되는 다양한 [원격 데스크톱 역할 서비스](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture)가 포함됩니다. Azure Active Directory(Azure AD) 애플리케이션 프록시를 사용하는 RDS 배포에는 커넥터 서비스를 실행하는 서버의 영구적인 아웃바운드 연결이 있습니다. 기타 배포에서는 부하 분산 장치를 통해 열린 인바운드 연결을 유지합니다. 이 인증 패턴을 사용하면 원격 데스크톱 서비스를 통해 온-프레미스 애플리케이션을 게시하여 더 많은 유형의 애플리케이션을 제공할 수 있습니다. 또한 Azure AD 애플리케이션 프록시를 사용하여 배포의 공격 표면을 줄일 수 있습니다.

## <a name="use-when"></a>적용 가능한 상황

사전 인증을 사용하여 원격 액세스를 제공하고 원격 데스크톱 서비스 배포를 보호해야 합니다.

![아키텍처 다이어그램](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>시스템의 구성 요소

* **사용자**: 애플리케이션 프록시에서 제공하는 RDS에 액세스합니다.

* **웹 브라우저**: 애플리케이션의 외부 URL에 액세스하기 위해 사용자가 상호 작용하는 구성 요소입니다.

* **Azure AD**: 사용자를 인증합니다. 

* **애플리케이션 프록시 서비스**: 사용자의 요청을 RDS로 전달하는 역방향 프록시 역할을 합니다. 애플리케이션 프록시는 모든 조건부 액세스 정책을 적용할 수도 있습니다. 

* **원격 데스크톱 서비스**: 개별 가상화된 애플리케이션의 플랫폼 역할을 하며, 안전한 모바일 및 원격 데스크톱 액세스를 제공하고, 최종 사용자에게 클라우드에서 애플리케이션과 데스크톱을 실행할 수 있는 기능을 제공합니다. 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Azure AD를 사용하여 원격 데스크톱 게이트웨이 서비스 구현

* [Azure AD 애플리케이션 프록시를 사용하여 원격 데스크톱 게시](../app-proxy/application-proxy-integrate-with-remote-desktop-services.md) 

* [Azure AD에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](../app-proxy/application-proxy-add-on-premises-application.md)