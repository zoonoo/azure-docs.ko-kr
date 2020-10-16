---
title: Azure Active Directory로 RADIUS 인증
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
ms.openlocfilehash: ff6210741d87602b4f695633b11d2641a6bb6781
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114529"
---
# <a name="radius-authentication-with-azure-active-directory"></a>Azure Active Directory로 RADIUS 인증

RADIUS(Remote Authentication Dial-In User Service) (RADIUS)는 중앙 집중식 인증 및 전화 접속 사용자의 권한 부여를 사용 하 여 네트워크를 보호 하는 네트워크 프로토콜입니다. 많은 응용 프로그램은 여전히 사용자를 인증 하는 RADIUS 프로토콜을 사용 합니다.

Microsoft Windows Server에는 RADIUS 서버 역할을 하 고 RADIUS 인증을 지 원하는 NPS (네트워크 정책 서버) 라는 역할이 있습니다.

Azure Active Directory (Azure AD)는 RADIUS 기반 시스템에서 Multi-factor authentication을 사용 하도록 설정 합니다. 고객이 앞에서 설명한 RADIUS 작업에 Azure Multi-Factor Authentication를 적용 하려는 경우 Windows NPS 서버에 Azure Multi-Factor Authentication NPS 확장을 설치할 수 있습니다. 

Windows NPS 서버는 Active Directory에 대해 사용자의 자격 증명을 인증 한 다음 Multi-Factor Authentication 요청을 Azure에 보냅니다. 그러면 사용자가 모바일 인증자에 대 한 챌린지를 받게 됩니다. 성공적으로 완료 되 면 클라이언트 응용 프로그램에서 서비스에 연결할 수 있습니다. 

## <a name="usewhen"></a>사용하는 경우: 

다음과 같은 응용 프로그램에 Multi-Factor Authentication를 추가 해야 합니다.
* VPN (가상 사설망)
* WiFi 액세스
* 원격 데스크톱 게이트웨이 (RDG)
* VDI(가상 데스크톱 인프라)
* 사용자를 서비스로 인증 하기 위해 RADIUS 프로토콜에 종속 된 다른 모든. 

> [!NOTE]
> RADIUS 및 azure Multi-Factor Authentication NPS 확장을 사용 하 여 Azure Multi-Factor Authentication를 VPN 워크 로드에 적용 하는 대신 VPN을 SAML으로 업그레이드 하 고 Azure AD와 VPN을 직접 페더레이션 하는 것이 좋습니다. 이를 통해 조건부 액세스, Multi-Factor Authentication, 장치 준수 및 Id 보호를 포함 하 여 Azure AD 보호의 전체 범위를 VPN에 제공 합니다.

![아키텍처 다이어그램](./media/authentication-patterns/radius-auth.png)


## <a name="componentsofthe-system"></a>시스템의 구성 요소 

* **클라이언트 응용 프로그램 (VPN 클라이언트)**: RADIUS 클라이언트에 인증 요청을 보냅니다.

* **RADIUS 클라이언트**: 클라이언트 응용 프로그램에서 요청을 변환 하 고 NPS 확장이 설치 된 RADIUS 서버에 보냅니다.

* **Radius 서버**: Active Directory와 연결 하 여 radius 요청에 대 한 기본 인증을 수행 합니다. 성공 하면에서 Azure Multi-Factor Authentication NPS 확장에 요청을 전달 합니다.

* **NPS 확장**: 보조 인증을 위해 Azure Multi-Factor Authentication에 대 한 요청을 트리거합니다. 성공 하면 NPS 확장은 Azure의 보안 토큰 서비스에서 발급 한 Multi-Factor Authentication 클레임을 포함 하는 보안 토큰을 RADIUS 서버에 제공 하 여 인증 요청을 완료 합니다.

* **Azure Multi-Factor Authentication**: azure AD와 통신 하 여 사용자의 세부 정보를 검색 하 고 사용자가 구성한 인증 방법을 사용 하 여 보조 인증을 수행 합니다.

## <a name="implementradiuswith-azure-ad"></a>Azure AD를 사용 하 여 RADIUS 구현 

* [NPS를 사용 하 여 Azure Multi-Factor Authentication 기능 제공](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension) 

* [Azure Multi-Factor Authentication NPS 확장 구성](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-advanced) 

* [NPS 확장을 사용 하 여 Azure Multi-Factor Authentication와 VPN](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-vpn) 

  
‎ 

 
