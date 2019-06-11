---
title: Azure MFA 버전 및 사용 계획-Azure Active Directory
description: Multi-Factor Authentication 클라이언트 및 사용 가능한 다양한 방법과 버전에 대해 설명합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a1ee55dd3aebca869da47bbc994f546aa4fe528
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496759"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 획득 방법

계정을 보호하려는 경우 두 단계 인증 과정이 조직 간에 표준화되어야 합니다. 이 기능은 리소스에 대한 액세스 권한이 있는 계정에 특히 중요합니다. 따라서 Microsoft는 Office 365 및 Azure AD(Azure Active Directory) 관리자에게 기본적인 2단계 인증 기능을 추가 비용 없이 제공합니다. 관리자를 위한 기능을 업그레이드하거나 2단계 인증을 나머지 사용자에게 확장하려는 경우 여러 가지 방법으로 Azure Multi-Factor Authentication을 구매할 수 있습니다.

> [!IMPORTANT]
> 이 문서는 Azure Multi-Factor Authentication을 구입하는 여러 가지 방법을 이해하는 데 도움이 됩니다. 가격 책정 및 대금 청구에 대한 자세한 내용은 항상 [Multi-Factor Authentication 가격 페이지](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)를 참조하세요.
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication의 사용 가능한 버전

다음 표에서는 세 가지 버전의 Multi-Factor Authentication 간 차이점을 설명합니다.

| 버전 | 설명 |
| --- | --- |
| Office 365용 Multi-Factor Authentication <br> Microsoft 365 Business | 이 버전은 Office 365 또는 Microsoft 365 포털에서 관리 됩니다. 관리자는 [2단계 인증을 사용하여 Office 365 리소스의 보안을 유지](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)할 수 있습니다. 이 버전은 Office 365 또는 Microsoft 365 Business 구독의 일부입니다. |
| Azure AD 관리자를 위한 Multi-Factor Authentication | Azure AD 테넌트에서 Azure AD 전역 관리자 역할이 할당된 사용자는 추가 비용 없이 2단계 인증을 사용하도록 설정할 수 있습니다. |
| Azure Multi-Factor Authentication | 대개 "전체" 버전이라고 하는 Azure Multi-factor Authentication은 가장 다양한 기능을 제공합니다. [Azure Portal](https://portal.azure.com), 고급 보고, 다양한 온-프레미스 및 클라우드 애플리케이션 지원을 통해 추가 구성 옵션을 제공합니다. Azure Multi-factor Authentication의 기능은 [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features)합니다. |

> [!NOTE]
> 신규 고객은 2018년 9월 1일부터 제공되는 독립 제품 형태의 Azure Multi-Factor Authentication을 더 이상 구매할 수 없습니다. Multi-Factor Authentication은 Azure AD Premium 라이선스의 기능으로 계속 사용할 수 있게 지원됩니다.

## <a name="feature-comparison-of-versions"></a>버전 기능 비교

다음 표에서 다양한 버전의 Azure Multi-Factor Authentication에서 사용 가능한 기능의 목록을 제공합니다.

> [!NOTE]
> 다음은 각 Multi-Factor Authentication에 포함된 기능을 비교하는 테이블입니다. 완전한 Azure Multi-Factor Authentication 서비스를 사용하는 경우 [클라우드 또는 온-프레미스에서 MFA를 사용하는지](concept-mfa-whichversion.md)에 따라 일부 기능을 사용할 수 없을 수도 있습니다.
>

| 기능 | Office 365용 Multi-Factor Authentication | Azure AD 관리자를 위한 Multi-Factor Authentication | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| MFA를 사용하여 Azure AD 관리자 계정 보호 |● |● (Azure AD 전역 관리자 계정에만 해당) |● |
| 두 번째 단계로 모바일 앱 |● |● |● |
| 두 번째 단계로 전화 통화 |● |● |● |
| 두 번째 단계로 SMS |● |● |● |
| MFA를 지원하지 않는 클라이언트에 대한 앱 암호 |● |● |● |
| 확인 방법에 대한 관리자 제어 |● |● |● |
| MFA를 사용하여 비관리자 계정 보호 |● | |● |
| PIN 모드 | | |● |
| 사기 행위 경고 | | |● |
| MFA 보고서 | | |● |
| 일회성 바이패스 | | |● |
| 전화 통화에 대한 사용자 지정 인사말 | | |● |
| 전화 통화에 대한 사용자 지정 발신자 번호 | | |● |
| 신뢰할 수 있는 IP | | |● |
| 신뢰할 수 있는 디바이스에 대한 MFA 기억 |● |● |● |
| 온-프레미스 애플리케이션에 대한 MFA | | |● |

> [!IMPORTANT]
> 전화 통화 옵션의 2019 년 3 월부터 Azure AD 체험/평가판 테 넌 트에서 MFA 및 SSPR 사용자에 게 제공 되지 않습니다. SMS 메시지는이 변경의 영향을 받지 않습니다. 전화 통화는 유료 Azure AD 테 넌 트의 사용자에 게 사용 가능 하도록 계속 됩니다. 이 변경은 Azure AD 체험/평가판 테 넌 트에만 영향을 줍니다.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Azure AD 관리자에 Azure Multi-Factor Authentication을 설정하는 방법

Azure AD 테넌트에서 전역 관리자 역할이 할당된 사용자는 추가 비용 없이 Azure AD 전역 관리자 계정에 2단계 인증을 사용하도록 설정할 수 있습니다. Microsoft 계정을 사용하는 경우 Microsoft 계정 지원 문서 [2단계 인증 정보](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)의 지침에 따라 다단계 인증을 등록할 수 있습니다. Microsoft 계정을 사용하지 않는 경우 [사용자 또는 그룹에 대해 2단계 인증을 요구하는 방법](howto-mfa-userstates.md) 문서의 지침에 따라 전역 관리자에게 다단계 인증을 사용합니다.

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication을 구매하는 방법

Azure Active Directory Premium 또는 Azure AD Premium 또는 조건부 액세스를 포함 하는 라이선스 번들과 같은 Azure Multi-factor Authentication을 포함 하 고 Azure Active Directory에서 사용자에 게 할당할 라이선스를 구입 합니다.

### <a name="consumption-based-licensing"></a>사용량 기반 라이선스

사용량 기반 라이선스를 더 이상 유효한 새 고객에 게 제공 2018 년 9 월 1 일.

더 이상 유효 2018 년 9 월 1 일에 새로운 인증 공급자를 만들 수 없습니다. 기존 인증 공급자는 계속 사용하고 업데이트할 수 있습니다. Multi-Factor Authentication은 Azure AD Premium 라이선스에서 계속 사용할 수 있게 지원됩니다.

Azure Multi-Factor Authentication 제공자를 사용하는 경우 Azure 구독을 통해 청구되는 두 가지 사용 모델을 사용할 수 있습니다.

1. **활성화된 사용자별** - 규칙적으로 인증이 필요한 고정된 수의 직원에 대해 2단계 인증을 사용하려는 기업에 적합합니다. 사용자당 요금 청구는 Azure AD 테넌트 및 Azure MFA 서버에서 MFA에 대해 활성화된 사용자 수를 기반으로 합니다. 사용자가 Azure AD 및 Azure MFA 서버 모두에서 MFA에 대해 활성화되고 도메인 동기화(Azure AD Connect)가 사용하도록 설정된 경우 더 많은 사용자 집합을 계산합니다. 도메인 동기화를 사용할 수 없는 경우 Azure AD 및 Azure MFA 서버에서 MFA에 대해 활성화된 모든 사용자의 합계를 계산합니다. 대금 청구는 매일 계산되어 상거래 시스템에 보고됩니다.

   > [!NOTE]
   > 대금 청구 예제 1: 오늘 MFA에 대해 5,000명의 사용자가 활성화되었습니다. MFA 시스템은 이 숫자를 31로 나누고 해당 날짜에 대해 161.29명의 사용자를 보고합니다. 내일은 15명의 사용자를 추가로 활성화하므로 MFA 시스템은 해당 날짜에 대해 161.77명의 사용자를 보고합니다. 청구 주기가 끝날 때까지 Azure 구독에 대해 대금 청구된 총 사용자 수는 약 5,000으로 합산됩니다.
   >
   > 대금 청구 예제 2: 라이선스가 있는 사용자와 없는 사용자가 혼합되어 있으므로 사용자당 Azure MFA 공급 기업을 포함하여 차액을 메웁니다. 테넌트에는 4,500개의 Enterprise Mobility + Security 라이선스가 있지만 MFA에 대해 5,000명의 사용자가 활성화되어 있습니다. Azure 구독에는 500명의 사용자에 대해 대금이 청구되고 매일 16.13명의 사용자료 계산 및 보고됩니다.
   >

1. **인증당** - 비규칙적으로 인증이 필요한 큰 그룹의 사용자에 대해 2단계 인증을 사용하려는 기업에 적합합니다. 대금 청구는 인증의 성공 또는 거부 여부에 관계없이 2단계 인증 요청 수를 기반으로 합니다. 이 대금 청구는 10개 인증 팩의 Azure 사용량 명세서에 표시되며 매일 보고됩니다.

   > [!NOTE]
   > 대금 청구 예제 3: 오늘 Azure MFA 서비스가 3,105개의 2단계 인증 요청을 수신했습니다. Azure 구독에는 310.5개 인증 팩에 대한 요금이 청구됩니다.
   >

사용자에게 라이선스가 있을 수 있지만 사용량 기반 구성에 따라 요금이 청구된다는 점을 고려해야 합니다. 인증당 Azure MFA 공급자를 설정했다면 라이선스가 있는 사용자가 수행했어도 2단계 인증 요청마다 요금이 청구됩니다. Azure AD 테넌트에 연결되지 않은 도메인에서 사용자당 Azure MFA 공급자를 설정하면 사용자에게 Azure AD에 대한 라이선스가 있어도 활성화된 사용자당 요금이 청구됩니다.

## <a name="next-steps"></a>다음 단계

- 가격 책정에 대한 자세한 내용은 [Azure MFA 가격 책정](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)을 참조하세요.

- [클라우드 또는 온-프레미스](concept-mfa-whichversion.md)에서 Azure MFA를 배포할지 여부 선택
