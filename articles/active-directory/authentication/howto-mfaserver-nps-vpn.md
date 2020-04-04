---
title: Azure MFA 서버 및 타사 VPN - Azure Active 디렉터리
description: Azure MFA 서버가 시스코, Citrix 및 주니퍼와 통합할 수 있도록 단계별 구성 가이드를 참조합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01decb99a9eb24ae60250f83f1f961b4c1690bc0
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652857"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Azure MFA 서버 및 타사 VPN 솔루션을 사용하여 고급 시나리오

Azure 다단계 인증 서버(Azure MFA Server)를 사용하여 다양한 타사 VPN 솔루션과 원활하게 연결할 수 있습니다. 이 문서에서는 시스코&reg; ASA VPN 어플라이언스, Citrix NetScaler SSL VPN 어플라이언스 및 주니퍼 네트웍스 보안 액세스/펄스 보안 연결 보안 SSL VPN 어플라이언스에 중점을 둡니다. 이러한 세 가지 일반적인 어플라이언스를 해결하기 위해 구성 지침을 작성했습니다. Azure MFA Server는 Radius, LDAP, IIS 또는 클레임 기반 인증을 AD FS에 사용하는 대부분의 다른 시스템과 통합할 수도 있습니다. 자세한 내용은 Azure [MFA 서버 구성에서](howto-mfaserver-deploy.md#next-steps)확인할 수 있습니다.

> [!IMPORTANT]
> 2019년 7월 1일부터 Microsoft는 더 이상 새 배포를 위해 MFA 서버를 제공하지 않습니다. 사용자로부터 다단계 인증을 요구하려는 신규 고객은 클라우드 기반 Azure 다단계 인증을 사용해야 합니다. 7월 1일 이전에 MFA Server를 활성화한 기존 고객은 최신 버전, 향후 업데이트를 다운로드하고 평소와 같이 정품 인증 자격 증명을 생성할 수 있습니다.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>시스코 ASA VPN 어플라이언스 및 Azure MFA 서버
Azure MFA 서버는 Cisco&reg; ASA VPN 어플라이언스와 통합되어 Cisco AnyConnect&reg; VPN 로그인 및 포털 액세스에 대한 추가 보안을 제공합니다.  LDAP 또는 RADIUS 프로토콜을 사용할 수 있습니다.  자세한 단계별 구성 가이드를 다운로드하려면 다음 중 하나를 선택합니다.

| 구성 가이드 | 설명 |
| --- | --- |
| [Anyconnect VPN을 사용한 Cisco ASA 및 LDAP를 위한 Azure MFA 구성](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | LDAP를 사용하는 Azure MFA와 Cisco ASA VPN 어플라이언스 통합 |
| [Anyconnect VPN을 사용한 Cisco ASA 및 RADIUS를 위한 Azure MFA 구성](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | RADIUS를 사용하는 Azure MFA와 Cisco ASA VPN 어플라이언스 통합 |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix 넷 스케일러 SSL VPN 및 Azure MFA 서버
Azure MFA 서버는 Citrix NetScaler SSL VPN 어플라이언스와 통합되어 Citrix NetScaler SSL VPN 로그인 및 포털 액세스에 대한 추가 보안을 제공합니다.  LDAP 또는 RADIUS 프로토콜을 사용할 수 있습니다.  자세한 단계별 구성 가이드를 다운로드하려면 다음 중 하나를 선택합니다.

| 구성 가이드 | 설명 |
| --- | --- |
| [Citrix NetScaler SSL VPN 및 LDAP를 위한 Azure MFA 구성](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | LDAP를 사용하는 Azure MFA와 Citrix NetScaler SSL VPN 어플라이언스 통합 |
| [Citrix NetScaler SSL VPN 및 RADIUS를 위한 Azure MFA 구성](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | RADIUS를 사용하는 Azure MFA와 Citrix NetScaler SSL VPN 어플라이언스 통합 |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>주니퍼/펄스 보안 SSL VPN 어플라이언스 및 Azure MFA 서버
Azure MFA 서버는 주니퍼/펄스 보안 SSL VPN 어플라이언스와 통합되어 주니퍼/펄스 보안 SSL VPN 로그인 및 포털 액세스에 대한 추가 보안을 제공합니다.  LDAP 또는 RADIUS 프로토콜을 사용할 수 있습니다.  자세한 단계별 구성 가이드를 다운로드하려면 다음 중 하나를 선택합니다.

| 구성 가이드 | 설명 |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN 및 LDAP를 위한 Azure MFA 구성](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | LDAP를 사용하는 Azure MFA와 Juniper/Pulse Secure SSL VPN 어플라이언스 통합 |
| [Juniper/Pulse Secure SSL VPN 및 RADIUS를 위한 Azure MFA 구성](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | RADIUS를 사용하는 Azure MFA와 Juniper/Pulse Secure SSL VPN 어플라이언스 통합 |

## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication에 대한 NPS 확장을 사용하여 기존 인증 인프라 보강](howto-mfa-nps-extension.md)

- [Azure 다단계 인증 설정 구성](howto-mfa-mfasettings.md)
