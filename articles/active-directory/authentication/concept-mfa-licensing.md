---
title: Azure 다단계 인증 버전 및 소비 계획
description: Azure 다단계 인증 클라이언트 및 사용 가능한 다양한 방법 및 버전에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648005"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Azure 다단계 인증을 위한 기능 및 라이센스

조직의 사용자 계정을 보호하려면 다단계 인증을 사용해야 합니다. 이 기능은 리소스에 대한 액세스 권한이 있는 계정에 특히 중요합니다. Office 365 및 Azure Active Directory(Azure AD) 관리자는 추가 비용 없이 기본 다단계 인증 기능을 사용할 수 있습니다. 관리자의 기능을 업그레이드하거나 나머지 사용자에게 다단계 인증을 확장하려는 경우 여러 가지 방법으로 Azure 다단계 인증을 구입할 수 있습니다.

> [!IMPORTANT]
> 이 문서에서는 Azure 다단계 인증에 라이선스를 부여하고 사용할 수 있는 다양한 방법에 대해 자세히 설명합니다. 가격 및 청구에 대한 자세한 내용은 [Azure 다단계 인증 가격 책정 페이지를](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)참조하십시오.

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication의 사용 가능한 버전

Azure 다단계 인증을 조직의 요구 사항에 따라 몇 가지 다른 방법으로 사용하고 라이선스를 부여할 수 있습니다. Azure AD, Office 365, EMS 또는 현재 가지고 있는 Microsoft 365 라이선스에 따라 Azure 다단계 인증을 사용할 수 있습니다. 다음 표에서는 Azure 다단계 인증을 받는 다양한 방법과 각 기능에 대한 몇 가지 기능 및 사용 사례를 자세히 설명합니다.

| 사용자인 경우 | 기능 및 사용 사례 |
| --- | --- |
| EMS 또는 마이크로 소프트 365 E3 및 E5 | EMS E3 또는 Microsoft 365 E3(EMS 및 Office 365 포함)에는 Azure AD 프리미엄 P1이 포함되어 있습니다. EMS E5 또는 마이크로 소프트 365 E5는 Azure AD 프리미엄 P2를 포함한다. 다음 섹션에 언급된 것과 동일한 조건부 액세스 기능을 사용하여 사용자에게 다단계 인증을 제공할 수 있습니다. |
| Azure AD Premium P1 | [Azure AD 조건부 액세스를](../conditional-access/overview.md) 사용하여 비즈니스 요구 사항에 맞게 특정 시나리오 또는 이벤트 중에 다단계 인증을 요청하는 사용자에게 메시지를 표시할 수 있습니다. |
| Azure AD Premium P2 | 가장 강력한 보안 위치와 향상된 사용자 환경을 제공합니다. 사용자의 패턴에 적응하고 다단계 인증 프롬프트를 최소화하는 Azure AD Premium P1 기능에 [위험 기반 조건부 액세스를](../conditional-access/howto-conditional-access-policy-risk.md) 추가합니다. |
| 오피스 365 비즈니스 프리미엄, E3 또는 E5 | Azure 다단계 인증은 모든 로그인 이벤트에 대해 모든 사용자에 대해 활성화되거나 비활성화됩니다. 사용자의 하위 집합에 대해서만 또는 특정 시나리오에서만 다단계 인증을 사용하도록 설정할 수 있는 기능은 없습니다. 관리는 Office 365 포털을 통해 관리됩니다. 향상된 사용자 환경을 위해 Azure AD 프리미엄 P1 또는 P2로 업그레이드하고 조건부 액세스를 사용합니다. 자세한 내용은 [다단계 인증을 통해 안전한 Office 365 리소스를](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)참조하십시오. |
| Azure AD Free | [보안 기본값을](../fundamentals/concept-fundamentals-security-defaults.md) 사용하여 인증 요청이 이루어질 때마다 모든 사용자에 대해 다단계 인증을 활성화할 수 있습니다. 사용 가능한 사용자 나 시나리오를 세부적으로 제어할 수는 없지만 추가 보안 단계를 제공합니다.<br /> 모든 사용자에 대해 다단계 인증을 사용하도록 설정하는 데 보안 기본값을 사용하지 않는 경우에도 *Azure AD Global Administrator* 역할을 할당한 사용자는 다단계 인증을 사용하도록 구성할 수 있습니다. 프리 티어의 이 기능을 사용하면 중요한 관리자 계정이 다단계 인증으로 보호됩니다. |

## <a name="feature-comparison-of-versions"></a>버전 기능 비교

다음 표에서 다양한 버전의 Azure Multi-Factor Authentication에서 사용 가능한 기능의 목록을 제공합니다. 사용자 인증 보안에 대한 요구 사항을 계획한 다음 이러한 요구 사항을 충족하는 방법을 결정합니다. 예를 들어 Azure AD Free는 Azure 다단계 인증을 제공하는 보안 기본값을 제공하지만 전화 통화 나 SMS가 아닌 인증 프롬프트에만 모바일 인증 앱을 사용할 수 있습니다. 이 방법은 모바일 인증 앱이 사용자의 개인 장치에 설치되어 있는지 확인할 수 없는 경우 제한될 수 있습니다.

| 기능 | Azure AD 무료 - 보안 기본값 | Azure AD 무료 - Azure AD 글로벌 관리자 | 오피스 365 비즈니스 프리미엄, E3 또는 E5 | Azure AD Premium P1 또는 P2 |
| --- |:---:|:---:|:---:|:---:|
| MFA를 통해 Azure AD 테넌트 관리자 계정 보호 | ● | ● *(Azure AD 글로벌 관리자* 계정만 해당) | ● | ● |
| 두 번째 단계로 모바일 앱 | ● | ● | ● | ● |
| 두 번째 단계로 전화 통화 | | ● | ● | ● |
| 두 번째 단계로 SMS | | ● | ● | ● |
| 확인 방법에 대한 관리자 제어 | | ● | ● | ● |
| 사기 행위 경고 | | | | ● |
| MFA 보고서 | | | | ● |
| 전화 통화에 대한 사용자 지정 인사말 | | | | ● |
| 전화 통화에 대한 사용자 지정 발신자 번호 | | | | ● |
| 신뢰할 수 있는 IP | | | | ● |
| 신뢰할 수 있는 디바이스에 대한 MFA 기억 | | ● | ● | ● |
| 온-프레미스 애플리케이션에 대한 MFA | | | | ● |

> [!IMPORTANT]
> 2019년 3월부터 Azure AD Free/평가판 테넌에서 Azure 다단계 인증 및 Azure 셀프 서비스 암호 재설정 사용자가 전화 통화 옵션을 더 이상 사용할 수 없습니다. SMS 메시지는 이 변경의 영향을 받지 않습니다. Azure AD Premium P1 또는 P2 테넌트또는 Office 365 비즈니스 프리미엄, E3 또는 E5를 사용하거나 사용하는 사용자가 전화 통화를 계속 사용할 수 있습니다.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Azure 다단계 인증 구매 및 사용 설정

Azure 다단계 인증을 사용하려면 적격 Azure AD 계층을 등록하거나 구입합니다. Azure AD는 무료, Office 365 앱 에디션(Office 365 비즈니스 프리미엄 E3 또는 E5 고객용), 프리미엄 P1 및 프리미엄 P2의 4가지 버전으로 제공됩니다.

무료 버전은 Azure 구독에 포함되어 있습니다. *Azure AD 전역 관리자* 역할을 사용하여 보안 기본값을 사용하거나 계정을 보호하는 방법에 대한 자세한 내용은 아래 [섹션을](#azure-ad-free-tier) 참조하세요.

Azure AD 프리미엄 에디션은 Microsoft 담당자, [오픈 볼륨 라이선스 프로그램](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)및 [클라우드 솔루션 공급자 프로그램을](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)통해 사용할 수 있습니다. Azure 및 Office 365 구독자는 온라인에서 Active Directory Premium P1 및 P2를 구입할 수도 있습니다. [로그인하여](https://portal.office.com/Commerce/Catalog.aspx) 구입합니다.

> [!IMPORTANT]
> 소비 기반 라이선스는 2018년 9월 1일부터 새로운 고객에게 더 이상 제공되지 않습니다. 사용량 기반 모델을 사용하는 기존 고객은 사용 가능한 사용자당 또는 인증 청구당 계속 사용할 수 있습니다.

필요한 Azure AD 계층을 구입한 후 [Azure 다단계 인증을 계획하고 배포합니다.](howto-mfa-getstarted.md)

### <a name="azure-ad-free-tier"></a>Azure AD 프리 티어

Azure AD Free 테넌트의 모든 사용자는 보안 기본값을 사용하여 Azure 다단계 인증을 사용할 수 있습니다. 이러한 보안 기본값은 로그인할 때마다 모든 사용자에 대해 Azure 다단계 인증을 활성화합니다. 모바일 인증 앱은 Azure AD 무료 보안 기본값을 사용할 때 Azure 다단계 인증에 사용할 수 있는 유일한 방법입니다.

* [Azure AD 보안 기본값에 대해 자세히 알아보기](../fundamentals/concept-fundamentals-security-defaults.md)
* [Azure AD Free의 사용자에 대한 보안 기본 설정](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

모든 사용자 및 모든 로그인 이벤트에 대해 Azure 다단계 인증을 사용하도록 설정하지 않으려면 *대신 Azure AD 글로벌 관리자* 역할을 사용하여 사용자 계정만 보호할 수 있습니다. 이 방법은 중요한 관리자 계정에 대한 추가 인증 프롬프트를 제공합니다. 사용하는 계정 유형에 따라 다음 방법 중 하나로 Azure 다단계 인증을 사용하도록 설정합니다.

* Microsoft 계정을 사용하는 경우 [다단계 인증에 등록합니다.](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)
* Microsoft 계정을 사용하지 않는 경우 [Azure AD의 사용자 또는 그룹에 대한 다단계 인증을 켭니다.](howto-mfa-userstates.md)

## <a name="next-steps"></a>다음 단계

비용에 대한 자세한 내용은 [Azure 다단계 인증 가격 책정을](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)참조하십시오.
