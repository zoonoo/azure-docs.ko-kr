---
title: 확인 된 도메인 변경의 감사 데이터 문제 해결 | Microsoft Docs
description: 사용자가 확인 된 도메인을 변경할 때 Azure Active Directory 활동 로그에 표시 되는 정보를 제공 합니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/22/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c9ec3b1e96e47dbf46c6acb2c81147b614d069
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117435"
---
# <a name="troubleshoot-audit-data-on-verified-domain-change"></a>문제 해결: 확인 된 도메인 변경에 대 한 감사 데이터 


## <a name="i-have-a-lot-of-changes-to-my-users-and-i-am-not-sure-what-the-cause-of-it-is"></a>사용자에 대 한 변경 내용이 많고 그 원인을 잘 모를 수 있습니다.

### <a name="symptoms"></a>증상

Azure AD 감사 로그를 확인 하 고 Azure AD 테 넌 트에서 발생 하는 여러 사용자 업데이트를 확인 합니다. 이러한 **업데이트 사용자** 이벤트는 **행위자** 정보를 표시 하지 않습니다 .이로 인해 사용자에 대 한 대량 변경을 트리거한 항목에 대해 불확실성이 발생 합니다. 

### <a name="cause"></a>원인

 대량 개체가 변경 되는 일반적인 이유는 **Proxycalc**라는 비 동기 백 엔드 작업입니다.  **Proxycalc** 는 Azure AD 사용자, 그룹 또는 연락처에서 업데이트 된 적절 한 **UserPrincipalName** 및 **프록시 주소**를 결정 하는 논리입니다. **Proxycalc** 의 디자인은 언제 든 지 Azure AD에서 모든 **UserPrincipalName** 및 **프록시 주소가** 일치 하는지 확인 하는 것입니다. **Proxycalc** 는 확인 된 도메인 변경과 같이 명시적으로 변경 해야 하며 백그라운드에서 작업으로 영구적으로 실행 되지 않습니다. 

  

#### <a name="what-does-userprincipalname-consistency-mean"></a>UserPrincipalName 일관성은 무엇을 의미 하나요? 

클라우드 전용 사용자의 경우 일관성은 **UserPrincipalName** 확인 된 도메인 접미사로 설정 됨을 의미 합니다. 일치 하지 않는 **UserPrincipalName** 처리 되 면 **proxycalc** 는 다음과 같은 기본 onmicrosoft.com 접미사로 변환 합니다.username@Contoso.onmicrosoft.com 

동기화 된 사용자의 경우 일관성은 **UserPrincipalName** 이 확인 된 도메인 접미사로 설정 되 고 온-프레미스 **UserPrincipalName** 값 (ShadowUserPrincipalName)과 일치 함을 의미 합니다. 일치 하지 않는 **UserPrincipalName** 처리 되 면 **Proxycalc** 가 **ShadowUserPrincipalName** 와 동일한 값으로 되돌아가고, 도메인 접미사가 테 넌 트에서 제거 된 경우는 기본 *. onmicrosoft.com 도메인 접미사로 변환 합니다. 

  

#### <a name="what-does-proxy-address-consistency-mean"></a>프록시 주소 일관성은 무엇을 의미 하나요? 

클라우드 전용 사용자의 경우 일관성은 프록시 주소가 확인 된 도메인 접미사와 일치 하는 것을 의미 합니다. 일치 하지 않는 프록시 주소를 처리 하는 경우 **Proxycalc** 는이를 기본 *. onmicrosoft.com 도메인 접미사로 변환 합니다. 예를 들면 다음과 같습니다.SMTP:username@Contoso.onmicrosoft.com 

동기화 된 사용자의 경우 일관성은 프록시 주소가 온-프레미스 프록시 주소 (즉, ShadowProxyAddresses). **ProxyAddresses** 는 **ShadowProxyAddresses**와 동기화 되어야 합니다. 동기화 된 사용자에 게 Exchange 라이선스가 할당 된 경우 프록시 주소는 온-프레미스 프록시 주소 값과 일치 해야 하며 확인 된 도메인 접미사와도 일치 해야 합니다. 이 시나리오에서 **Proxycalc** 는 확인 되지 않은 도메인 접미사로 일치 하지 않는 프록시 주소를 삭제 하 고 Azure AD의 개체에서 제거 됩니다. 확인 되지 않은 도메인을 나중에 확인 하는 경우 **Proxycalc** 는 다시 계산 하 고 **ShadowProxyAddresses** 의 프록시 주소를 Azure AD의 개체에 다시 추가 합니다.  

> [!NOTE]
> 동기화 된 개체의 경우 예기치 않은 결과를 계산 하 여 **Proxycalc** 논리가 발생 하지 않도록 하려면 온-프레미스 개체에서 Azure AD 확인 된 도메인으로 프록시 주소를 설정 하는 것이 가장 좋습니다.  

  
**Proxycalc** 를 트리거할 수 있는 관리 작업 중 하나는 확인 된 도메인 변경이 있을 때마다입니다. 이 작업은 내부적으로 **Proxycalc**를 트리거하는 Azure AD 테 넌 트에서 확인 된 도메인이 추가/제거 될 때마다 발생 합니다.  

예를 들어 확인 된 도메인 Fabrikam.com를 Contoso.onmicrosoft.com 테 넌 트에 추가 하는 경우이 작업은 테 넌 트의 모든 개체에 대해 ProxyCalc 작업을 트리거합니다. 이 이벤트는 Azure AD 감사 로그에 **업데이트 사용자** 이벤트로 캡처된 후 **확인 된 도메인 추가** 이벤트가 발생 합니다. 반면에 Contoso.onmicrosoft.com 테 넌 트에서 Fabrikam.com이 제거 된 경우 모든 **업데이트 사용자** 이벤트 앞에는 **확인 된 도메인 제거** 이벤트가 발생 합니다.   

#### <a name="additional-notes"></a>추가 참고 사항:

ProxyCalc로 인해 다음과 같은 특정 개체가 변경 되는 것은 아닙니다. 

- 활성 Exchange 라이선스가 없습니다. 
- **MSExchRemoteRecipientType** 가 Null로 설정 되어 있어야 합니다. 
- 공유 리소스로 간주 되지 않습니다. 공유 리소스는 **CloudMSExchRecipientDisplayType** 가 다음 값 중 하나를 포함 하는 경우입니다. **MailboxUser (shared)**, **publicfolder**, **ConferenceRoomMailbox**, **EquipmentMailbox**, **ArbitrationMailbox**, **RoomList**, **TeamMailboxUser**, **Group mailbox**, **예약은 mailbox**, **aclablemailboxuser**, **ACLableTeamMailboxUser** 
  
 이러한 두 가지 서로 다른 이벤트 간에 더 많은 상관 관계를 구축 하기 위해 Microsoft는 확인 된 도메인 변경에 의해 트리거되는 이러한 변경 내용을 확인 하기 위해 감사 로그의 **행위자** 정보를 업데이트 하는 작업을 수행 합니다. 이 작업은 확인 된 도메인 변경 이벤트가 발생 했을 때 확인 하 고 테 넌 트의 개체를 대량 업데이트 하기 시작 합니다. 

또한 대부분의 경우 **UserPrincipalName** 및 **프록시 주소가** 일치 하므로 사용자에 대 한 변경 내용이 없으므로 감사 로그에는 개체에 대 한 실제 변경 작업을 일으킨 업데이트만 표시 됩니다. 이 작업을 수행 하면 감사 로그의 노이즈가 방지 되 고 관리자가 위에서 설명한 대로 남은 사용자 변경 내용을 확인 된 도메인 변경 이벤트와 상호 연결 하는 데 도움이 됩니다. 

## <a name="next-steps"></a>다음 단계

[Azure AD Connect 동기화 서비스 섀도 특성](../hybrid/how-to-connect-syncservice-shadow-attributes.md)
