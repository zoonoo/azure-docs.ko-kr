---
title: Azure Active Directory를 사용한 RADIUS 인증
description: Azure Active Directory를 사용한 RADIUS 인증에 대한 아키텍처 지침.
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
ms.openlocfilehash: 97c524e1f4c05787f1dd61dea5a463e8fa83511a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168630"
---
# <a name="radius-authentication-with-azure-active-directory"></a>Azure Active Directory를 사용한 RADIUS 인증

RADIUS(Remote Authentication Dial-In User Service)는 중앙 집중식 인증 및 전화 접속 사용자의 권한 부여를 사용하여 네트워크를 보호하는 네트워크 프로토콜입니다. 많은 애플리케이션은 여전히 사용자를 인증하기 위해 RADIUS 프로토콜을 사용합니다.

Microsoft Windows Server에는 RADIUS 서버 역할을 하고 RADIUS 인증을 지원하는 NPS(네트워크 정책 서버)라는 역할이 있습니다.

Azure AD(Azure Active Directory)는 RADIUS 기반 시스템에서 다단계 인증을 사용하도록 설정합니다. 앞서 설명한 RADIUS 워크로드 중 하나에 고객이 Azure AD 다단계 인증을 적용하려는 경우 해당 Windows NPS 서버에 Azure AD 다단계 인증 NPS 확장을 설치할 수 있습니다. 

Windows NPS 서버는 Active Directory에 대해 사용자의 자격 증명을 인증한 다음 다단계 인증 요청을 Azure에 보냅니다. 그러면 사용자가 모바일 인증자에 대한 챌린지를 받게 됩니다. 성공적으로 완료되면 클라이언트 애플리케이션이 서비스에 연결할 수 있습니다. 

## <a name="use-when"></a>사용하는 경우: 

다음과 같은 경우 애플리케이션에 다단계 인증을 추가해야 합니다.
* VPN(가상 사설망)
* WiFi 액세스
* RDG(원격 데스크톱 게이트웨이)
* VDI(가상 데스크톱 인프라)
* RADIUS 프로토콜에 따라 사용자를 서비스로 인증하는 다른 모든 항목. 

> [!NOTE]
> RADIUS 및 Azure AD 다단계 인증 NPS 확장을 사용하여 Azure AD 다단계 인증을 VPN 워크로드에 적용하기 보다는 VPN을 SAML로 업그레이드하여 Azure AD와 VPN을 직접 페더레이션하는 것이 좋습니다. 이를 통해 VPN은 조건부 액세스, 다단계 인증, 디바이스 규정 준수 및 ID 보호를 포함하여 Azure AD 보호의 전체 범위를 VPN에 제공합니다.

![아키텍처 다이어그램](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>시스템의 구성 요소 

* **클라이언트 애플리케이션(VPN 클라이언트)** : RADIUS 클라이언트에 인증 요청을 보냅니다.

* **RADIUS 클라이언트**: 클라이언트 애플리케이션에서 요청을 변환하고 NPS 확장이 설치된 RADIUS 서버에 보냅니다.

* **RADIUS 서버**: Active Directory와 연결하여 RADIUS 요청에 대한 기본 인증을 수행합니다. 성공하면에서 요청을 Azure AD 다단계 인증 NPS 확장에 전달합니다.

* **NPS 확장**: 보조 인증을 위해 Azure AD 다단계 인증에 대한 요청을 트리거합니다. 성공하면 NPS 확장은 Azure의 보안 토큰 서비스에서 발급한 다단계 인증 클레임을 포함하는 보안 토큰을 RADIUS 서버에 제공하여 인증 요청을 완료합니다.

* **Azure AD 다단계 인증**: Azure AD와 통신하여 사용자의 세부 정보를 검색하고 사용자가 구성한 인증 방법을 사용하여 보조 인증을 수행합니다.

## <a name="implement-radius-with-azure-ad"></a>Azure AD를 사용하여 RADIUS 구현 

* [NPS를 사용하여 Azure AD 다단계 인증 기능 제공](../authentication/howto-mfa-nps-extension.md) 

* [Azure AD 다단계 인증 NPS 확장 구성](../authentication/howto-mfa-nps-extension-advanced.md) 

* [NPS 확장을 사용하는 Azure AD 다단계 인증을 통한 VPN](../authentication/howto-mfa-nps-extension-vpn.md) 

  
‎ 

