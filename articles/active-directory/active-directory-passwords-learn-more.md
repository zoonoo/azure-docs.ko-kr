---
title: "자세한 정보: Azure Active Directory 암호 관리 | Microsoft Docs"
description: "비밀번호 쓰기 저장의 작동 원리, 비밀번호 쓰기 저장 보안, 암호 재설정 포털의 작동 원리, 암호 재설정에서 사용되는 데이터를 포함하여 Azure AD 암호 관리에 대한 고급 항목을 제공합니다."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: d3be2912-76c8-40a0-9507-b7b1a7ce2f8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: a07051ea0be58cafcf1a7d7ae800b44e7abd05cd
ms.lasthandoff: 04/13/2017


---
# <a name="learn-more-about-password-management"></a>암호 관리에 대한 자세한 정보
> [!IMPORTANT]
> **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md#reset-my-password).
>
>

암호 관리를 이미 배포했거나 배포하기 전에 작동 원리에 대한 기술적 핵심 사항을 자세히 알아보려는 경우, 이 섹션에서 해당 서비스의 기술적 개념에 대한 훌륭한 개요를 제공합니다. 여기서 다루는 내용은 다음과 같습니다.

* [**암호 재설정 포털의 작동 원리**](#how-does-the-password-reset-portal-work)
* [**암호 쓰기 저장 개요**](#password-writeback-overview)
 * [암호 쓰기 저장의 작동 원리](#how-password-writeback-works)
* [**비밀번호 쓰기 저장에 지원되는 시나리오**](#scenarios-supported-for-password-writeback)
 * [지원되는 Azure AD Connect, Azure AD Sync 및 DirSync 클라이언트](#supported-clients)
 * [비밀번호 쓰기 저장에 필요한 라이선스](#licenses-required-for-password-writeback)
 * [비밀번호 쓰기 저장에 지원되는 온-프레미스 인증 모드](#on-premises-authentication-modes-supported-for-password-writeback)
 * [비밀번호 쓰기 저장에 지원되는 사용자 및 관리자 작업](#user-and-admin-operations-supported-for-password-writeback)
 * [비밀번호 쓰기 저장에 지원되지 않는 사용자 및 관리자 작업](#user-and-admin-operations-not-supported-for-password-writeback)
* [**비밀번호 쓰기 저장 보안 모델**](#password-writeback-security-model)
 * [비밀번호 쓰기 저장 암호화 세부 정보](#password-writeback-encryption-details)
 * [비밀번호 쓰기 저장 대역폭 사용](#password-writeback-bandwidth-usage)
* [**사용자에 대한 암호 재설정 데이터 배포, 관리 및 액세스**](#deploying-managing-and-accessing-password-reset-data-for-your-users)
 * [암호 재설정에서 사용되는 데이터](#what-data-is-used-by-password-reset)
 * [최종 사용자를 등록할 필요 없이 암호 재설정 배포](#deploying-password-reset-without-requiring-end-user-registration)
 * [사용자가 암호 재설정에 등록하면 어떻게 되나요?](#what-happens-when-a-user-registers)
 * [사용자의 암호 재설정 데이터에 액세스하는 방법](#how-to-access-password-reset-data-for-your-users)
 * [PowerShell을 사용하여 암호 재설정 데이터 설정](#setting-password-reset-data-with-powershell)
 * [PowerShell을 사용하여 암호 재설정 데이터 읽기](#reading-password-reset-data-with-powershell)
* [**B2B 사용자에 대한 암호 재설정은 어떻게 작동하나요?**](#how-does-password-reset-work-for-b2b-users)

## <a name="how-does-the-password-reset-portal-work"></a>암호 재설정 포털의 작동 원리
사용자가 암호 재설정 포털로 이동하면 사용자 계정이 유효한지 여부, 사용자가 속한 조직, 사용자의 암호를 관리하는 위치, 해당 기능 사용에 대해 사용자가 사용 허가를 받았는지 여부 등을 확인하는 워크플로가 시작됩니다.  암호 재설정 페이지의 논리에 대해 자세히 알아보려면 아래 단계를 읽어보세요.

1. 계정에 액세스할 수 없습니까? 링크를 클릭하거나 [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com)으로 직접 이동합니다.
2. 사용자 ID를 입력하고 captcha를 전달합니다.
3. Azure AD가 다음을 수행하여 사용자가 이 기능을 사용할 수 있는지 확인합니다.
   * 사용자가 이 기능을 사용할 수 있고 Azure AD 라이선스가 할당되어 있는지 확인합니다.
     * 사용자가 이 기능을 사용할 수 없거나 라이선스가 할당되어 있지 않은 경우에는 암호를 재설정하려면 관리자에게 문의하라는 메시지가 해당 사용자에게 표시됩니다.
   * 관리자 정책에 따라 사용자의 계정에 올바른 챌린지 데이터가 정의되어 있는지 확인합니다.
     * 정책에 하나의 챌린지만 필요하면 관리자 정책에서 사용하도록 설정한 최소 하나 이상의 챌린지에 대해 정의된 적절한 데이터가 해당 사용자에게 있는지 확인합니다.
       * 사용자가 구성되어 있지 않은 경우에는 암호를 재설정하려면 관리자에게 문의해야 한다고 사용자에게 알려줍니다.
     * 정책에 두 개의 챌린지가 필요하면 관리자 정책에서 사용하도록 설정한 최소 두 개 이상의 챌린지에 대해 정의된 적절한 데이터가 해당 사용자에게 있는지 확인합니다.
       * 사용자가 구성되어 있지 않은 경우에는 암호를 재설정하려면 관리자에게 문의해야 한다고 사용자에게 알려줍니다.
   * 사용자의 암호가 온-프레미스(페더레이션 또는 암호 해시 동기화)로 관리되는지 여부를 확인합니다.
     * 쓰기 저장 기능이 배포되어 있고 사용자의 암호가 온-프레미스로 관리되면 사용자는 인증을 진행하고 암호를 재설정할 수 있습니다.
     * 쓰기 저장 기능이 배포되어 있지 않고 사용자의 암호가 온-프레미스로 관리되는 경우에는 암호를 재설정하려면 관리자에게 문의해야 한다고 사용자에게 알려줍니다.
4. 사용자가 성공적으로 암호를 재설정할 수 있다고 판단되면 사용자가 재설정 프로세스를 계속 진행하도록 안내해줍니다.

비밀번호 쓰기 저장을 배포하는 방법에 대한 자세한 내용은 [시작: Azure AD 암호 관리](active-directory-passwords-getting-started.md)를 참조하세요.

## <a name="password-writeback-overview"></a>암호 쓰기 저장 개요
암호 쓰기 저장은 Azure Active Directory Premium의 현재 구독자가 설정하여 사용할 수 있는 [Azure Active Directory Connect](connect/active-directory-aadconnect.md) 구성 요소입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

암호 쓰기 저장을 사용하면 암호를 온-프레미스 Active Directory에 쓸 수 있도록 클라우드 테넌트를 구성할 수 있습니다.  복잡한 온-프레미스 셀프 서비스 암호 재설정 솔루션을 설정 및 관리할 필요가 없으며 사용자가 어디에 있든 상관 없이 온-프레미스 암호를 재설정할 수 있는 편리한 클라우드 기반 방법을 제공합니다.  암호 쓰기 저장의 주요 기능 중 몇 가지가 나와 있으므로 잘 읽어보시기 바랍니다.

* **지연 없는 피드백.**  암호 쓰기 저장은 동기식 작업입니다.  사용자의 암호가 정책에 맞지 않거나 어떤 이유로든 재설정 또는 변경할 수 없는 경우 즉시 사용자에게 알려줍니다.
* **AD FS 또는 기타 페더레이션 기술을 사용하여 사용자의 암호 재설정 지원.**  페더레이션된 사용자 계정이 Azure AD 테넌트로 동기화되는 동안 암호 쓰기 저장을 사용하여 온-프레미스 AD 암호를 클라우드에서 관리할 수 있습니다.
* **암호 해시 동기화를 사용하여 사용자의 암호 재설정 지원.** 암호 재설정 서비스에서 동기화된 사용자 계정을 암호 해시 동기화에 사용할 수 있음을 감지하면 이 계정의 온-프레미스 및 클라우드 암호를 동시에 재설정합니다.
* **액세스 패널 및 Office 365에서 암호 변경 지원.**  페더레이션 또는 암호가 동기화된 사용자가 만료되었거나 만료되지 않은 암호를 변경하면 해당 암호를 로컬 AD 환경에 다시 씁니다.
* **관리자가 **Azure 관리 포털**에서** [암호를 재설정할 때 암호 쓰기 저장 지원.](https://manage.windowsazure.com)  사용자가 페더레이션 또는 암호가 동기화된 경우 관리자가 [Azure 관리 포털](https://manage.windowsazure.com)에서 해당 사용자의 암호를 재설정할 때마다 로컬 AD에서 관리자가 선택하는 암호도 설정합니다.  이 기능은 현재 Office 관리자 포털에서 지원되지 않습니다.
* **온-프레미스 AD 암호 정책 적용.**  사용자가 자신의 암호를 재설정하는 경우 해당 디렉터리에 커밋하기 전에 온-프레미스 AD 정책에 맞는지 확인합니다.  여기에는 기록, 복잡성, 나이, 암호 필터 및 로컬 AD에서 사용자가 정의한 기타 암호 제한 사항이 포함됩니다.
* **인바운드 방화벽 규칙 불필요.**  암호 쓰기 저장 기능은 기본 통신 채널로 Azure 서비스 버스 릴레이를 사용합니다. 따라서 이 기능이 작동하기 위해 방화벽에서 인바운드 포트를 열 필요가 없습니다.
* **온-프레미스 Active Directory의 보호된 그룹 내에 존재하는 사용자 계정에 대해서는 지원되지 않음.** 보호된 그룹에 대한 자세한 내용은 [Active Directory의 보호된 계정 및 그룹](https://technet.microsoft.com/library/dn535499.aspx).

### <a name="how-password-writeback-works"></a>암호 쓰기 저장의 작동 원리
암호 쓰기 저장에는 세 가지 주요 구성 요소가 있습니다.

* 암호 재설정 클라우드 서비스(Azure AD의 암호 변경 페이지에도 통합되어 있음)
* 테넌트별 Azure 서비스 버스 릴레이
* 온-프레미스 암호 재설정 끝점

아래 다이어그램에 설명된 것처럼 연동됩니다.

  ![][001]

페더레이션 또는 암호 해시 동기화된 사용자가 클라우드에서 자신의 암호를 재설정하거나 변경하려고 하면 다음이 수행됩니다.

1. 사용자가 어떤 종류의 암호를 가지고 있는지 확인합니다.  암호가 온-프레미스로 관리되는 경우는 쓰기 저장 서비스가 가동되어 실행 중인지 확인합니다.  실행 중인 경우는 사용자가 작업을 계속 진행하도록 하고, 실행 중이 아닌 경우는 여기서 암호를 재설정할 수 없다고 사용자에게 알려줍니다.
2. 다음으로, 사용자는 적절한 인증 게이트를 통과하여 암호 재설정 화면에 도달합니다.
3. 사용자는 새 암호를 선택하고 다시 확인합니다.
4. 제출을 클릭하면 쓰기 저장 설정 과정에서 만든 대칭 키로 일반 텍스트 암호를 암호화합니다.
5. 암호를 암호화한 후 HTTPS 채널을 통해 테넌트별 서비스 버스 릴레이(쓰기 저장 설정 과정 중에 설정될 수도 있음)로 전송되는 페이로드에 해당 암호를 포함시킵니다.  이 릴레이는 온-프레미스 설치만 알고 있는 임의로 생성된 암호에 의해 보호됩니다.
6. 메시지가 서비스 버스에 도달하면 암호 재설정 끝점이 자동으로 절전 모드에서 해제되어 보류 중인 재설정 요청이 있는지 확인합니다.
7. 그런 다음, 서비스에서 클라우드 앵커 특성을 사용하여 해당 사용자를 찾습니다.  이 조회가 성공하기 위해서는 사용자 개체가 AD 커넥터 공간에 있고 해당 MV 개체에 연결되고 해당 AAD 커넥터 개체에 연결되어야 합니다. 마지막으로, 동기화에서 이 사용자 계정을 찾기 위해서는 AD 커넥터 개체에서 MV로의 링크에 `Microsoft.InfromADUserAccountEnabled.xxx` 동기화 규칙이 있어야 합니다.  클라우드에서 호출이 들어오면 동기화 엔진이 cloudAnchor 특성을 사용하여 AAD 커넥터 공간 개체를 조회한 후 MV 개체로 링크를 다시 따라간 다음 AD 개체로 다시 링크를 따라가기 때문에 이 규칙이 필요합니다. 동일한 사용자에 대해 여러 AD 개체(다중 포리스트)가 있을 수 있기 때문에 동기화 엔진은 `Microsoft.InfromADUserAccountEnabled.xxx` 링크에 의존하여 정확한 개체를 선택합니다. 이 논리의 결과로, 암호 쓰기 저장이 작동하려면 Azure AD Connect를 주 도메인 컨트롤러에 연결해야 합니다.  이 경우 Active Directory 동기화 커넥터의 **속성**을 마우스 오른쪽 단추로 클릭하고 **디렉터리 파티션 구성**을 선택하여 주 도메인 컨트롤러 에뮬레이터를 사용하도록 Azure AD Connect를 구성할 수 있습니다. 여기에서 **도메인 컨트롤러 연결 설정** 섹션을 찾고 **기본 설정 도메인 컨트롤러만 사용**이라는 상자를 선택합니다. 참고: 기본 설정된 DC가 PDC 에뮬레이터가 아닌 경우 Azure AD Connect는 비밀번호 쓰기 저장의 PDC에 연결됩니다.
8. 사용자 계정을 찾은 후에는 적절한 AD 포리스트에서 직접 암호를 재설정합니다.
9. 암호 설정 작업에 성공한 경우 사용자에게 암호가 수정되었으므로 작업을 계속 진행해도 된다고 알려줍니다. 사용자 암호가 암호 동기화를 통해 Azure AD에 동기화되는 경우 온-프레미스 암호 정책이 클라우드 암호 정책보다 약할 수 있습니다. 이 경우 온-프레미스 정책이 무엇이든 이를 적용하고 암호 해시 동기화가 해당 암호의 해시를 동기화하도록 허용합니다. 이렇게 하면 Single Sign-On을 제공하는 데 암호 동기화를 사용하든, 페더레이션을 사용하든 관계없이 온-프레미스 정책이 클라우드에서 적용됩니다.
10. 암호 설정 작업에 실패한 경우는 사용자에게 오류를 반환하고 다시 시도하라고 알려줍니다.  서비스가 다운되었거나, 선택한 암호가 조직의 정책과 맞지 않거나, 로컬 AD에서 해당 사용자를 찾을 수 없거나, 또는 기타 여러 가지 이유로 인해 작업에 실패할 수 있습니다.  이러한 경우를 위한 특정 메시지가 있어서 문제를 해결하기 위해 어떤 작업을 수행할 수 있는지 사용자에게 알려줍니다.

## <a name="scenarios-supported-for-password-writeback"></a>암호 쓰기 저장에 지원되는 시나리오
아래 섹션에서는 동기화 기능의 버전에 따라 지원되는 시나리오를 설명합니다.  일반적으로 비밀번호 쓰기 저장을 사용하려는 경우 Azure AD Connect의 자동 업데이트 기능을 사용하거나 최신 버전의 [Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect)를 설치하는 것이 좋습니다.

* [**지원되는 Azure AD Connect, Azure AD Sync 및 DirSync 클라이언트**](#supported-clients)
* [**비밀번호 쓰기 저장에 필요한 라이선스**](#licenses-required-for-password-writeback)
* [**비밀번호 쓰기 저장에 지원되는 온-프레미스 인증 모드**](#on-premises-authentication-modes-supported-for-password-writeback)
* [**비밀번호 쓰기 저장에 지원되는 사용자 및 관리자 작업**](#user-and-admin-operations-supported-for-password-writeback)
* [**비밀번호 쓰기 저장에 지원되지 않는 사용자 및 관리자 작업**](#user-and-admin-operations-not-supported-for-password-writeback)

### <a name="supported-clients"></a>지원되는 클라이언트
비밀번호 쓰기 저장을 사용하려는 경우 Azure AD Connect의 자동 업데이트 기능을 사용하거나 최신 버전의 [Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect)를 설치하는 것이 좋습니다.

* **DirSync(1.0.6862 이상의 모든 버전)** - _지원되지 않음_ - 기본 쓰기 저장 기능만을 지원하고 제품 그룹에서 더 이상 지원되지 않습니다.
* **Azure AD Sync** - _사용되지 않음_ - 기본 쓰기 저장 기능을 지원하고 계정 잠금 해제 기능, 다양한 로깅 및 Azure AD Connect에서 향상된 안정성이 제공되지 않습니다. 따라서 **항상** 업그레이드하는 것이 좋습니다.
* **Azure AD Connect** - _완전히 지원됨_ - 모든 쓰기 저장 기능을 지원합니다. 가장 훌륭한 새로운 기능 및 안정성을 가능하게 하는 최신 버전으로 업그레이드하세요.

### <a name="licenses-required-for-password-writeback"></a>비밀번호 쓰기 저장에 필요한 라이선스
비밀번호 쓰기 저장을 사용하기 위해 다음 라이선스 중 하나가 테넌트에 할당되어야 합니다.

* **Azure AD Premium P1** - 비밀번호 쓰기 저장 사용에 대한 제한 없음
* **Azure AD Premium P2** - 비밀번호 쓰기 저장 사용에 대한 제한 없음
* **Enterprise Mobility Suite** - 비밀번호 쓰기 저장 사용에 대한 제한 없음
* **Enterprise Cloud Suite** - 비밀번호 쓰기 저장 사용에 대한 제한 없음

평가판 또는 유료인지에 상관 없이 Office 365 라이선스 계획에서 비밀번호 쓰기 저장을 사용하지 않을 수 있습니다. 이 기능을 사용하기 위해 위의 계획 중 하나로 업그레이드해야 합니다.

Office 365 SKU에 비밀번호 쓰기 저장을 사용할 예정이 없습니다.

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>비밀번호 쓰기 저장에 지원되는 온-프레미스 인증 모드
비밀번호 쓰기 저장은 다음 사용자 암호 유형에 사용할 수 있습니다.

* **클라우드 전용 사용자**: 온-프레미스 암호가 없기 때문에 비밀번호 쓰기 저장은 이 상황에서 적용되지 않음
* **암호 동기화된 사용자**: 비밀번호 쓰기 저장이 지원됨
* **페더레이션된 사용자**: 비밀번호 쓰기 저장이 지원됨
* **통과 인증 사용자**: 비밀번호 쓰기 저장이 지원됨

### <a name="user-and-admin-operations-supported-for-password-writeback"></a>비밀번호 쓰기 저장에 지원되는 사용자 및 관리자 작업
암호는 다음과 같은 경우에 모두 다시 기록됩니다.

* **지원되는 최종 사용자 작업**
 * 모든 최종 사용자 셀프 서비스 자발적 변경 암호 작업
 * 모든 최종 사용자 셀프 서비스 적용 변경 암호 작업(예: 암호 만료)
 * [암호 재설정 포털](https://passwordreset.microsoftonline.com)에서 시작되는 모든 최종 사용자 셀프 서비스 암호 재설정
* **지원되는 관리자 작업**
 * 모든 관리자 셀프 서비스 자발적 변경 암호 작업
 * 모든 관리자 셀프 서비스 적용 변경 암호 작업(예: 암호 만료)
 * [암호 재설정 포털](https://passwordreset.microsoftonline.com)에서 시작되는 모든 관리자 셀프 서비스 암호 재설정
 * [클래식 Azure 관리 포털](https://manage.windowsazure.com)에서 관리자 시작 최종 사용자 암호 재설정
 * [Azure Portal](https://portal.azure.com)에서 관리자 시작 최종 사용자 암호 재설정

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>비밀번호 쓰기 저장에 지원되지 않는 사용자 및 관리자 작업
암호는 다음과 같은 경우에 다시 기록되지 않습니다.

* **지원되지 않는 최종 사용자 작업**
 * PowerShell v1, v2 또는 Azure AD Graph API를 사용하여 자신의 암호를 다시 설정하는 최종 사용자
* **지원되지 않는 관리자 작업**
 * [Office 관리 포털](https://portal.office.com)에서 관리자 시작 최종 사용자 암호 재설정
 * PowerShell v1, v2 또는 Azure AD Graph API에서 관리자 시작 최종 사용자 암호 재설정

이러한 제한을 제거하기 위해 노력하고 있지만 아직 공유할 수 있는 일정은 없습니다.

## <a name="password-writeback-security-model"></a>암호 쓰기 저장 보안 모델
암호 쓰기 저장은 매우 안전하고 강력한 서비스입니다.  사용자 정보를 확실하게 보호하기 위해 아래에서 설명하는 4계층 보안 모델이 사용됩니다.

* **테넌트별 서비스 버스 릴레이** – 사용자가 이 서비스를 설정하면 임의로 생성된 강력한 암호에 의해 보호되는 테넌트별 서비스 버스 릴레이가 설정됩니다. 이때 해당 암호는 Microsoft에도 전혀 액세스 권한이 없습니다.
* **잠겨 있는 강력한 암호 암호화 키** – 서비스 버스 릴레이를 만든 후 회선을 통해 도착하는 암호를 암호화하는 데 사용할 강력한 대칭 키를 만듭니다.  이 키는 클라우드의 회사의 암호 저장소에만 존재하며 디렉터리의 암호와 마찬가지로 강력하게 잠겨 있고 감사됩니다.
* **업계 표준 TLS** – 클라우드에서 암호 재설정 또는 변경 작업을 수행하면 일반 텍스트 암호를 가져와서 공용 키로 암호화합니다.  그런 다음, Microsoft의 SSL 인증서를 사용하여 암호화된 채널을 통해 서비스 버스 릴레이로 전송되는 HTTPS 메시지에 해당 암호를 삽입합니다.  서비스 버스에 해당 메시지가 도착한 후에는 온-프레미스 에이전트가 절전 모드에서 해제되고, 이전에 생성된 강력한 암호를 사용하여 서비스 버스에 대한 인증을 수행하고, 암호화된 메시지를 받아서 미리 생성된 개인 키를 사용하여 암호를 해독한 다음, AD DS SetPassword API를 통해 해당 암호를 설정합니다.  이 단계에서는 클라우드에서 AD 온-프레미스 암호 정책(복잡성, 나이, 기록, 필터 등)을 적용할 수 있습니다.
* **메시지 만료 정책** – 마지막으로, 어떤 이유로 온-프레미스 서비스가 다운되었기 때문에 메시지가 서비스 버스에 그대로 있는 경우는 보안을 더욱 강화하기 위해 몇 분 후 시간 초과되어 제거됩니다.

### <a name="password-writeback-encryption-details"></a>비밀번호 쓰기 저장 암호화 세부 정보
아래에서는 사용자가 암호 재설정 요청을 전송한 이후부터 온-프레미스 환경에 도달하기 전까지 최대 서비스 안정성 및 보안을 보장하기 위해 진행되는 암호화 단계를 설명합니다.

* **1단계 - 2048비트 RSA 키를 사용한 암호 암호화** - 사용자가 온-프레미스에 다시 작성된 암호를 제출하면 먼저 제출된 암호 자체를 2048비트 RSA 키로 암호화합니다.

* **2단계 - AES-GCM을 사용한 패키지 수준 암호화** - AES-GCM을 사용하여 전체 패키지(암호 + 필수 메타데이터)를 암호화합니다. 그러면 기본 Service Bus 채널에 대한 직접 액세스 권한을 가진 사용자가 콘텐츠를 보기/변조하지 않도록 방지합니다.

* **3단계 - 모든 통신이 TLS/SSL에 발생** - 또한 Service Bus와 모든 통신은 SSL/TLS 채널에서 발생합니다. 권한이 없는 제3자에게서 콘텐츠를 보호합니다.

* **4단계 - 6개월마다 자동 키 롤오버** - 마지막으로 자동으로 6개월마다 또는 비밀번호 쓰기 저장을 Azure AD Connect에서 사용/다시 사용할 때마다 이러한 키를 모두 롤오버하여 최대 서비스의 보안 및 안전을 보장합니다.

### <a name="password-writeback-bandwidth-usage"></a>비밀번호 쓰기 저장 대역폭 사용

비밀번호 쓰기 저장은 다음과 같은 경우에만 온-프레미스 에이전트로 요청을 다시 보내는 매우 낮은 대역폭 서비스입니다.

1. 기능을 활성화 또는 비활성화할 때 Azure AD Connect를 통해 두 개의 메시지를 전송했습니다.
2. 서비스가 실행되는 동안 한 메시지를 서비스 하트 비트로 5분마다 한 번씩 전송합니다.
3. 새 비밀번호가 제출될 때마다 두 메시지, 즉 작업을 수행하도록 요청하는 메시지 및 작업 결과를 포함하고 있는 후속 메시지를 전송합니다. 이러한 메시지는 다음과 같은 경우에 전송됩니다.
4. 사용자 셀프 서비스 비밀번호 다시 설정 중에 새 비밀번호가 제출될 때마다
5. 사용자 비밀번호 변경 작업 중에 새 비밀번호가 제출될 때마다
6. 관리자가 시작한 사용자 비밀번호 다시 설정 중에 새 비밀번호가 제출될 때마다(Azure 관리 포털에서만)

#### <a name="message-size-and-bandwidth-considerations"></a>메시지 크기 및 대역폭 고려 사항

위에서 설명한 각 메시지의 크기는 일반적으로 1Kb 미만입니다. 즉 극단적인 부하에서도 비밀번호 쓰기 저장 서비스 자체에서 초당 최대 수 Kb의 대역폭을 소비합니다. 각 메시지가 비밀번호 업데이트 작업에서 요구할 때만 실시간으로 전송되고 메시지 크기가 너무 작기 때문에 쓰기 저장 기능의 대역폭 사용은 실제 측정 가능한 영향을 미치기에는 실질적으로 너무 작습니다.

## <a name="deploying-managing-and-accessing-password-reset-data-for-your-users"></a>사용자에 대한 암호 재설정 데이터 배포, 관리 및 액세스
Azure AD Connect, PowerShell, Graph 또는 등록 환경을 통해 사용자에 대한 암호 재설정 데이터를 관리하거나 액세스할 수 있습니다.  아래 설명한 옵션을 활용하여 등록할 사용자에게 요청하지 않고 전체 조직에 암호 재설정을 배포할 수도 있습니다.

  * [암호 재설정에서 사용되는 데이터](#what-data-is-used-by-password-reset)
  * [최종 사용자를 등록할 필요 없이 암호 재설정 배포](#deploying-password-reset-without-requiring-end-user-registration)
  * [사용자가 암호 재설정에 등록하면 어떻게 되나요?](#what-happens-when-a-user-registers)
  * [사용자의 암호 재설정 데이터에 액세스하는 방법](#how-to-access-password-reset-data-for-your-users)
  * [PowerShell을 사용하여 암호 재설정 데이터 설정](#setting-password-reset-data-with-powershell)
  * [PowerShell을 사용하여 암호 재설정 데이터 읽기](#reading-password-reset-data-with-powershell)

### <a name="what-data-is-used-by-password-reset"></a>암호 재설정에서 사용되는 데이터
다음 테이블에는 암호 재설정 중에 이 데이터를 사용하는 위치와 방법이 나와 있으며 조직에 적합한 인증 옵션을 결정하는 데 도움이 되도록 설계되어 있습니다. 또한 이 테이블에서는 이 데이터의 유효성을 검사하지 않는 입력 경로에서 사용자를 대신하여 데이터를 제공하는 경우를 위한 형식 요구 사항도 보여줍니다.

> [!NOTE]
> 사무실 전화는 등록 포털에 나타나지 않는데, 사용자가 현재 디렉터리에서 이 속성을 편집할 수 없기 때문입니다. 관리자만이 값을 설정할 수 있습니다.
>
>

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>연락 방법 이름</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Active Directory 데이터 요소</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Azure Active Directory 데이터 요소</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>사용되는 위치/설정 가능한 위치</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>형식 요구 사항</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>사무실 전화</p>
            </td>
            <td>
              <p>telephoneNumber</p>
              <p>이 속성은 Azure Active Directory의 PhoneNumber 특성에 동기화되어 사용자를 먼저 등록하도록 요청하지 않고 암호 재설정을 위해 즉시 사용할 수 있습니다.</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>예: Set-MsolUser -UserPrincipalName JWarner@contoso.com -PhoneNumber "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>사용 위치:</p>
              <p>암호 재설정 포털</p>
              <p>설정 가능한 위치:</p>
              <p>PhoneNumber는 PowerShell, DirSync, Azure 관리 포털 및 Office 관리자 포털에서 설정할 수 있습니다.</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz(예: +1 1234567890)</p>
              <ul>
                <li class="unordered">
국가 코드를 제공해야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
지역 번호를 제공해야 합니다(있는 경우).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
국가 코드 앞에 +를 제공해야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
국가 코드와 나머지 번호 사이에는 공백이 하나 있어야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
내선 번호는 지원되지 않습니다. 내선 번호가 지정된 경우에는 전화 통화를 하기 전에 번호에서 제거합니다.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>휴대폰</p>
            </td>
            <td>
              <p>모바일</p>
              <p>이 속성은 Azure Active Directory의 MobilePhone 특성에 동기화되어 사용자를 먼저 등록하도록 요청하지 않고 암호 재설정을 위해 즉시 사용할 수 있습니다.</p>
              <p>현재는 AuthenticationPhone에 이 속성을 동기화할 수 없습니다.</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>또는</p>
              <p>MobilePhone</p>
              <p>(인증 전화는 제공된 데이터가 있는 경우에 사용되며, 그렇지 않은 경우는 휴대폰 필드로 대체됩니다.)</p>
              <p>예: Set-MsolUser -UserPrincipalName JWarner@contoso.com -MobilePhone "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>사용 위치:</p>
              <p>암호 재설정 포털</p>
              <p>등록 포털</p>
              <p>설정 가능한 위치: </p>
              <p>AuthenticationPhone은 암호 재설정 등록 포털 또는 MFA 등록 포털에서 설정할 수 있습니다.</p>
              <p>MobilePhone은 PowerShell, Azure AD Connect, Azure 관리 포털 및 Office 관리자 포털에서 설정할 수 있습니다.</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz(예: +1 1234567890)</p>
              <ul>
                <li class="unordered">
국가 코드를 제공해야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
지역 번호를 제공해야 합니다(있는 경우).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
국가 코드 앞에 +를 제공해야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
국가 코드와 나머지 번호 사이에는 공백이 하나 있어야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
내선 번호는 지원되지 않습니다. 내선 번호가 지정된 경우에는 전화 통화를 할 때 무시됩니다.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>대체 전자 메일</p>
            </td>
            <td>
              <p>사용할 수 없음</p>
              <p>현재는 Active Directory에서 AuthenticationEmail 또는 AlternateEmailAddresses[0] 속성 중 하나에 값을 동기화할 수 없습니다. </p>
              <p>PowerShell을 사용하여 AlternateEmailAddresses[0]을 설정할 수 있습니다. 이에 대한 지침은 이 테이블의 바로 아래 섹션에 있습니다.</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>또는</p>
              <p>AlternateEmailAddresses[0] </p>
              <p>(인증 전자 메일은 제공된 데이터가 있는 경우에 사용되며, 그렇지 않은 경우는 대체 전자 메일로 대체됩니다.)</p>
              <p>참고: 대체 전자 메일 필드는 디렉터리에 문자열의 배열로 지정됩니다.  이 배열의 첫 번째 항목을 사용합니다.</p>
              <p>예: Set-MsolUser -UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses "email@live.com"</p>
            </td>
            <td>
              <p>사용 위치:</p>
              <p>암호 재설정 포털</p>
              <p>등록 포털</p>
              <p>설정 가능한 위치: </p>
              <p>AuthenticationEmail은 암호 재설정 등록 포털 또는 MFA 등록 포털에서 설정할 수 있습니다.</p>
              <p>AlternateEmail은 PowerShell, Azure 관리 포털 및 Office 관리자 포털에서 설정할 수 있습니다.</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a> 또는 甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
전자 메일은 표준 형식을 따라야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
유니코드 전자 메일이 지원됩니다.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>보안 질문 및 답변</p>
            </td>
            <td>
              <p>사용할 수 없음</p>
              <p>현재는 Active Directory의 보안 질문 또는 대답을 동기화할 수 없습니다.</p>
            </td>
            <td>
              <p>디렉터리에서 직접 수정할 수 없습니다.</p>
              <p>암호 재설정 최종 사용자를 등록하는 프로세스 중에만 설정할 수 있습니다.</p>
            </td>
            <td>
              <p>사용 위치:</p>
              <p>암호 재설정 포털</p>
              <p>등록 포털 </p>
              <p>설정 가능한 위치: </p>
              <p>보안 질문을 설정하는 유일한 방법은 Azure 관리 포털을 통해 설정하는 방법입니다.</p>
              <p>지정된 사용자의 보안 질문에 대한 답변을 설정하는 유일한 방법은 등록 포털을 통해 설정하는 방법입니다.</p>
            </td>
            <td>
              <p>보안 질문은 최대 200자, 최소 3자입니다.</p>
              <p>답변은 최대 40자, 최소 3자입니다.</p>
            </td>
          </tr>
        </tbody></table>


### <a name="deploying-password-reset-without-requiring-end-user-registration"></a>최종 사용자를 등록할 필요 없이 암호 재설정 배포
사용자를 등록하도록 요청하지 않고 암호 재설정을 배포하려는 경우 아래 옵션 중 하나를 수행하여 쉽게 수행할 수 있습니다. 이것은 사용자가 등록 과정을 통해 해당 정보의 유효성을 검사하도록 허용하는 동시에 다수의 사용자가 SSPR을 사용하도록 차단을 해제하는 유용한 방법입니다.

대부분의 고객은 현재 이 기능을 사용하여 암호 재설정을 매우 빠르게 시작합니다.

#### <a name="synchronize-phone-numbers-with-azure-ad-connect"></a>Azure AD Connect와 전화 번호 동기화
아래 필드 중 하나 또는 모두에 데이터를 동기화하는 경우 사용자를 먼저 등록하도록 요청하지 않고 즉시 암호 재설정을 사용할 수 있습니다.

* 휴대폰
* 사무실 전화

온-프레미스에 업데이트해야 하는 속성에 대해 알아보려면 [암호 재설정에서 사용하는 데이터는 무엇인가요?](#what-data-is-used-by-password-reset) 섹션으로 이동하고 위에서 언급한 필드를 조회합니다.  

모든 전화 번호는 "+1 1234567890" 형식이므로 시스템에서 제대로 작동하는지 확인합니다.

#### <a name="set-phone-numbers-or-emails-with-powershell"></a>PowerShell을 사용하여 전화 번호 또는 전자 메일 설정
이러한 필드 중 하나 이상을 설정하면 사용자를 먼저 등록하도록 요청하지 않고 암호 재설정을 위해 즉시 사용할 수 있습니다.

* 휴대폰
* 사무실 전화
* 대체 전자 메일

PowerShell을 사용하여 이러한 속성을 설정하는 방법을 알아보려면 [PowerShell을 사용하여 암호 재설정 데이터 설정](#setting-password-reset-data-with-powershell) 섹션으로 이동합니다.

모든 전화 번호는 "+1 1234567890" 형식이므로 시스템에서 제대로 작동하는지 확인합니다.

### <a name="what-happens-when-a-user-registers"></a>사용자가 등록하면 어떻게 되나요?
사용자가 등록하면 등록 페이지에 다음 필드가 **항상** 설정됩니다.

* 인증 전화
* 인증 전자 메일
* 보안 질문 및 답변

**휴대폰** 또는 **대체 전자 메일**에 대한 값을 제공하면, 서비스에 등록하지 않은 사용자도 그 값을 사용하여 자신의 암호를 즉시 재설정할 수 있습니다.  또한 사용자는 처음으로 등록할 때 해당 값을 볼 수 있고, 원하는 경우에는 값을 변경할 수 있습니다.  하지만 등록을 완료한 후에는 **인증 전화** 및 **인증 전자 메일** 필드에 각각 그 값이 유지됩니다.

### <a name="how-to-access-password-reset-data-for-your-users"></a>사용자의 암호 재설정 데이터에 액세스하는 방법
#### <a name="data-settable-via-synchronization"></a>동기화를 통해 설정할 수 있는 데이터
다음 필드는 온-프레미스에서 동기화할 수 있습니다.

* 휴대폰
* 사무실 전화

#### <a name="data-settable-with-azure-ad-powershell--azure-ad-graph"></a>Azure AD PowerShell 및 Azure AD Graph를 사용하여 설정할 수 있는 데이터
Azure AD PowerShell 및 Azure AD Graph API를 사용하여 다음 필드를 설정할 수 있습니다.

* 대체 전자 메일
* 휴대폰
* 사무실 전화

#### <a name="data-settable-with-registration-ui-only"></a>등록 UI를 통해서만 설정할 수 있는 데이터
다음 필드는 SSPR 등록 UI(https://aka.ms/ssprsetup)를 통해서만 액세스할 수 있습니다.

* 보안 질문 및 답변

#### <a name="data-readable-with-azure-ad-powershell--azure-ad-graph"></a>Azure AD PowerShell 및 Azure AD Graph를 사용하여 읽을 수 있는 데이터
다음 필드는 Azure AD PowerShell 및 Azure AD Graph API를 통해 액세스할 수 있습니다.

* 대체 전자 메일
* 휴대폰
* 사무실 전화
* 인증 전화
* 인증 전자 메일

### <a name="setting-password-reset-data-with-powershell"></a>PowerShell을 사용하여 암호 재설정 데이터 설정
Azure AD PowerShell을 사용하여 다음 필드에 대한 값을 설정할 수 있습니다.

* 대체 전자 메일
* 휴대폰
* 사무실 전화

**_PowerShell V1_**

시작하려면 우선 [Azure AD PowerShell 모듈을 다운로드하고 설치](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)해야 합니다.  설치를 완료한 후에는 아래 단계에 따라서 각 필드를 구성합니다.

**_PowerShell V2_**

시작하려면 우선 [Azure AD V2 PowerShell 모듈을 다운로드하고 설치](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md)해야 합니다. 설치를 완료한 후에는 아래 단계에 따라서 각 필드를 구성합니다.

Install-Module을 지원하는 최신 버전의 PowerShell을 신속하게 설치하려면 다음 명령을 실행합니다(첫 번째 줄은 해당 항목이 이미 설치되어 있는지 확인함).

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="alternate-email---how-to-set-alternate-email-with-powershell"></a>대체 전자 메일 - PowerShell을 사용하여 대체 전자 메일을 설정하는 방법
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
```

#### <a name="mobile-phone---how-to-set-mobile-phone-with-powershell"></a>휴대폰 - PowerShell을 사용하여 휴대폰을 설정하는 방법
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 1234567890"
```

#### <a name="office-phone---how-to-set-office-phone-with-powershell"></a>사무실 전화 - PowerShell을 사용하여 사무실 전화를 설정하는 방법
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"
```

### <a name="reading-password-reset-data-with-powershell"></a>PowerShell을 사용하여 암호 재설정 데이터 읽기
Azure AD PowerShell을 사용하여 다음 필드에 대한 값을 읽을 수 있습니다.

* 대체 전자 메일
* 휴대폰
* 사무실 전화
* 인증 전화
* 인증 전자 메일

#### <a name="alternate-email---how-to-read-alternate-email-with-powershell"></a>대체 전자 메일 - PowerShell을 사용하여 대체 전자 메일을 읽는 방법
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select otherMails
```

#### <a name="mobile-phone---how-to-read-mobile-phone-with-powershell"></a>휴대폰 - PowerShell을 사용하여 휴대폰을 읽는 방법
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select Mobile
```

#### <a name="office-phone---how-to-read-office-phone-with-powershell"></a>사무실 전화 - PowerShell을 사용하여 사무실 전화를 읽는 방법
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber
```

#### <a name="authentication-phone---how-to-read-authentication-phone-with-powershell"></a>인증 전화 - PowerShell을 사용하여 인증 전화를 읽는 방법
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

**_PowerShell V2_**

```
Not possible in PowerShell V2
```

#### <a name="authentication-email---how-to-read-authentication-email-with-powershell"></a>인증 전자 메일 - PowerShell을 사용하여 인증 전자 메일을 읽는 방법
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

**_PowerShell V2_**

```
Not possible in PowerShell V2
```
## <a name="how-does-password-reset-work-for-b2b-users"></a>B2B 사용자에 대한 암호 재설정은 어떻게 작동하나요?
암호 재설정 및 변경은 모든 B2B 구성에서 완전히 지원됩니다.  암호 재설정에서 지원하는 3가지 명시적 B2B 사례는 아래를 참고하세요.

1. **기존 Azure AD 테넌트를 사용하는 파트너 조직의 사용자** - 제휴한 조직에 기존 Azure AD 테넌트 작업이 있는 경우 **해당 테넌트에서 사용되는 암호 재설정 정책을 따릅니다**. 암호 재설정이 작동하기 위해 파트너 조직은 Azure AD SSPR을 사용하는지 확인해야 합니다. 이 작업은 O365 고객에 대한 추가 비용 없이 [암호 관리 시작](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) 가이드의 단계를 수행하여 활성화될 수 있습니다.
2. **[셀프 서비스 등록](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup)을 사용하여 로그인한 사용자** - 제휴한 조직이 [셀프 서비스 등록](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup) 기능을 사용하여 테넌트에 들어간 경우 등록한 전자 메일을 사용하여 재설정을 즉시 다시 설정하도록 합니다.
3. **B2B 사용자** - 새 [Azure AD B2B 기능](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)을 사용하여 만든 모든 새 B2B 사용자는 초대를 처리하는 동안 등록된 전자 메일을 사용하여 즉시 암호를 재설정할 수 있습니다.

이를 테스트하려면 이러한 파트너 사용자 권한을 사용하여 http://passwordreset.microsoftonline.com으로 이동합니다.  대체 전자 메일 또는 인증 전자 메일이 정의되어 있으면 암호 재설정이 예상대로 작동됩니다.  SSPR에서 사용한 데이터에 대한 자세한 정보는 [암호 재설정에서 사용되는 데이터](https://azure.microsoft.com/en-us/documentation/articles/active-directory-passwords-learn-more/#what-data-is-used-by-password-reset) 개요에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음은 모든 Azure AD 암호 재설정 설명서 페이지에 대한 링크입니다.

* **로그인하는 데 문제가 있나요?** 그렇다면 [암호를 변경하고 재설정하는 방법은 다음과 같습니다](active-directory-passwords-update-your-own-password.md#reset-my-password).
* [**작동 방식**](active-directory-passwords-how-it-works.md) - 6개의 다양한 구성 요소 서비스 및 기능에 대해 알아봅니다.
* [**시작하기**](active-directory-passwords-getting-started.md) -사용자가 클라우드 또는 온-프레미스 암호를 다시 설정하고 변경할 수 있는 방법에 대해 알아봅니다.
* [**사용자 지정**](active-directory-passwords-customize.md) - 모양과 느낌 및 조직의 요구에 맞게 서비스의 동작을 사용자 지정하는 방법에 대해 알아봅니다
* [**모범 사례**](active-directory-passwords-best-practices.md) - 사용자의 조직에서 신속하게 배포하고 효과적으로 암호를 관리하는 방법에 대해 알아봅니다.
* [**정보 활용**](active-directory-passwords-get-insights.md) -우리의 통합된 보고 기능에 대해 알아봅니다
* [**FAQ**](active-directory-passwords-faq.md) -자주 묻는 질문에 답변합니다.
* [**문제해결**](active-directory-passwords-troubleshoot.md) -신속하게 서비스와의 문제를 해결하는 방법에 대해 알아봅니다.

[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"

