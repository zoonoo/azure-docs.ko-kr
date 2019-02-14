---
title: 온-프레미스 리소스에 대한 SSO가 Azure AD 조인 디바이스에서 작동하는 방식 | Microsoft Docs
description: 하이브리드 Azure Active Directory 가입 디바이스를 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2018
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c42a2240bd5b242634cbb255bf717f11610f063
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56161594"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>온-프레미스 리소스에 대한 SSO가 Azure AD 조인 디바이스에서 작동하는 방식

Azure AD(Azure Active Directory) 조인 디바이스에서 SSO(Single Sign-On) 환경을 테넌트의 클라우드 앱에 제공하는 것은 놀랄 일이 아닙니다. 사용자 환경에 온-프레미스 AD(Active Directory)가 있는 경우 이러한 디바이스의 SSO 환경을 이 온-프레미스 AD로 확장할 수 있습니다.

이 문서에서는 이러한 작동 방식에 대해 설명합니다.

## <a name="how-it-works"></a>작동 방법 

단일 사용자 이름과 암호만 기억하면 되므로 SSO는 리소스에 대한 액세스를 간소화하고 환경의 보안을 향상시킵니다. Azure AD 조인 디바이스를 사용하면 사용자는 이미 자신의 환경에서 클라우드 앱에 대한 SSO 환경을 갖추고 있습니다. 환경에 Azure AD와 온-프레미스 AD가 있는 경우 SSO 환경 범위를 온-프레미스 LOB(기간 업무) 앱, 파일 공유 및 프린터로 확장하려고 할 수도 있습니다.  


Azure AD 조인 디바이스는 온-프레미스 AD 환경에 조인되어 있지 않으므로 해당 환경에 대한 지식이 없습니다. 그러나 Azure AD Connect를 사용하여 온-프레미스 AD에 대한 추가 정보를 이러한 디바이스에 제공할 수 있습니다.
Azure AD와 온-프레미스 AD를 모두 갖춘 환경은 하이브리드 환경에 있다고도 알려져 있습니다. 하이브리드 환경을 사용하는 경우 온-프레미스 ID 정보를 클라우드에 동기화하기 위해 Azure AD Connect를 이미 배포했을 가능성이 높습니다. 동기화 프로세스의 일부로 Azure AD Connect는 온-프레미스 도메인 정보를 Azure AD에 동기화합니다. 사용자가 하이브리드 환경에서 Azure AD 조인 디바이스에 로그인하는 경우 다음을 수행합니다.

1. Azure AD에서 사용자가 멤버인 온-프레미스 도메인의 이름을 디바이스로 다시 보냅니다. 

2. LSA(로컬 보안 기관) 서비스를 통해 디바이스에서 Kerberos 인증을 사용할 수 있습니다.

사용자의 온-프레미스 도메인에 있는 리소스에 액세스하려고 시도하는 동안 디바이스에서 다음을 수행합니다.

1. 도메인 정보를 사용하여 DC(도메인 컨트롤러)를 찾습니다. 

2. 온-프레미스 도메인 정보와 사용자 자격 증명을 찾아낸 DC로 보내 사용자를 인증합니다.

3. AD 조인 리소스에 액세스하는 데 사용되는 Kerberos [TGT(Ticket-Granting Ticket)](https://docs.microsoft.com/windows/desktop/secauthn/ticket-granting-tickets)를 수신합니다.

사용자가 액세스하려고 시도하면 **Windows 통합 인증**에 구성된 모든 앱에서 SSO를 원활하게 받습니다.  

Azure AD 조인 디바이스에서 온-프레미스 SSO를 사용하도록 설정하려면 비즈니스용 Windows Hello를 추가로 구성해야 합니다. 자세한 내용은 [비즈니스용 Windows Hello를 사용하여 온-프레미스 Single Sign-On에 대한 Azure AD 조인 디바이스 구성](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base)을 참조하세요. 

## <a name="what-you-get"></a>결과

SSO를 사용하면 Azure AD 조인 디바이스에서 다음을 수행할 수 있습니다. 

- AD 멤버 서버의 UNC 경로에 액세스

- Windows 통합 보안에 구성된 AD 멤버 웹 서버에 액세스 



Windows 디바이스에서 온-프레미스 AD를 관리하려면 [Windows 10용 원격 서버 관리 도구](https://www.microsoft.com/en-us/download/details.aspx?id=45520)를 설치하세요.

다음 작업을 수행할 수 있습니다.

- ADUC(Active Directory 사용자 및 컴퓨터) 스냅인을 사용하여 모든 AD 개체를 관리합니다. 그러나 수동으로 연결하려는 도메인을 지정해야 합니다.

- DHCP 스냅인을 사용하여 AD 조인 DHCP 서버를 관리합니다. 그러나 DHCP 서버 이름 또는 주소를 지정해야 할 수 있습니다.

 
## <a name="what-you-should-know"></a>알아야 할 사항

필요한 도메인에 대한 데이터가 동기화되도록 Azure AD Connect에서 [도메인 기반 필터링](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering)을 조정해야 할 수 있습니다.

Azure AD 조인 디바이스에는 AD의 컴퓨터 개체가 없으므로 Active Directory 머신 인증을 사용하는 앱과 리소스가 작동하지 않습니다. 

Azure AD 조인 디바이스에서 다른 사용자와 파일을 공유할 수 없습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Active Directory에서 디바이스 관리란?](overview.md)을 참조하세요. 
