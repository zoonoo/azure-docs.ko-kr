---
title: Premium P2 라이선스 기능 검사 목록 - Azure Active Directory | Microsoft Docs
description: 30일, 90일 및 90일 이상 기간 동안의 Azure Active Directory Premium P2 기능 배포 검사 목록입니다.
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.custom: it-pro, seodec18
ms.openlocfilehash: 4fcb692d7189c84e32f55995538ffc692cb67dd6
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064715"
---
# <a name="azure-active-directory-premium-p2-licensing-feature-checklist"></a>Azure Active Directory Premium P2 라이선스 기능 검사 목록

Azure AD(Azure Active Directory)를 조직에 배포하고 보안을 유지하는 것은 어려울 수 있습니다. 이 문서에서는 고객에게 유용한 몇 가지 일반적인 작업을 식별합니다. 고객은 보안 강화를 위해 이러한 작업을 일반적으로 30일, 90일 또는 그 이상 동안 완료합니다. Azure AD를 이미 배포한 조직이더라도 이 검사 목록을 사용하여 투자를 최대한 활용할 수 있습니다.

ID 인프라를 잘 계획하고 실행해 두면 인증된 사용자와 디바이스를 통해서만 생산성 워크로드와 데이터에 보다 안전하게 액세스할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 가이드에서는 Azure AD Premium P2 라이선스, Enterprise Mobility + Security E5, Microsoft 365 E5 또는 이와 유사한 라이선스 번들을 사용한다고 가정합니다.

[Azure AD 라이선스](https://azure.microsoft.com/pricing/details/active-directory/)

[Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)

[Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)

## <a name="plan-and-deploy-day-1-30"></a>플랜 및 배포: 1-30일

- 둘 이상의 글로벌 관리자 지정(비상 계정)
   - [Azure AD에서 응급 액세스 관리 계정 관리](../users-groups-roles/directory-emergency-access.md)
- Azure AD PIM(Privileged Identity Management)을 켜서 보고서 보기
   - [PIM 사용 시작](../privileged-identity-management/pim-getting-started.md)
- 가능한 경우 비전역 관리 역할 사용
   - [Azure Active Directory에서 관리자 역할 할당](../users-groups-roles/directory-assign-admin-roles.md)
- 인증
   - [셀프 서비스 암호 재설정 롤아웃](../authentication/howto-sspr-deployment.md)
   - Azure AD 암호 보호(미리 보기) 배포
      - [조직에서 잘못된 암호 제거](../authentication/concept-password-ban-bad.md)
      - [Windows Server Active Directory에 Azure AD 암호 보호 강제 적용](../authentication/concept-password-ban-bad-on-premises.md)
   - 계정 잠금 정책 구성
      - [Azure Active Directory 스마트 잠금](../authentication/howto-password-smart-lockout.md)
      - [AD FS 엑스트라넷 잠금 및 엑스트라넷 스마트 잠금](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)
   - [조건부 액세스 정책을 사용하여 Azure AD Multi-Factor Authentication 배포](../authentication/howto-mfa-getstarted.md)
   - [셀프 서비스 암호 재설정 및 Azure AD Multi-Factor Authentication에 대한 등록 수렴 사용(미리 보기)](../authentication/concept-registration-mfa-sspr-converged.md)
   - [Azure Active Directory Identity Protection 사용](../identity-protection/enable.md)
      - [위험 이벤트를 사용하여 Multi-Factor Authentication 및 암호 변경 트리거](../authentication/tutorial-risk-based-sspr-mfa.md)
   - [암호 지침](https://www.microsoft.com/research/publication/password-guidance/)
      - 최소 길이 요구 사항(8자리)을 유지합니다(더 길다고 반드시 좋지는 않음).
      - 문자 조합을 요구 사항을 제거합니다.
      - [사용자 계정에 대한 정기적인 필수 암호 재설정을 제거합니다.](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire)
- 온-프레미스 Active Directory의 사용자 동기화
   - [Azure AD Connect 설치](../connect/active-directory-aadconnect-select-installation.md)
   - [암호 해시 동기화 구현](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)
   - [비밀번호 쓰기 저장 구현](../authentication/howto-sspr-writeback.md)
   - [Azure AD Connect Health 구현](../connect-health/active-directory-aadconnect-health.md)
- [Azure Active Directory에서 그룹 멤버 자격별로 사용자에게 라이선스 할당](../users-groups-roles/licensing-groups-assign.md)

## <a name="plan-and-deploy-day-31-90"></a>플랜 및 배포: 31-90일

- [게스트 사용자 액세스에 대한 계획](../b2b/what-is-b2b.md)
   - [Azure Portal에서 Azure Active Directory B2B 공동 작업 사용자 추가](../b2b/add-users-administrator.md)
   - [특정 조직의 B2B 사용자 초대 허용 또는 차단](../b2b/allow-deny-list.md)
   - [Azure AD의 B2B 사용자에게 온-프레미스 애플리케이션에 대한 액세스 권한 부여](../b2b/hybrid-cloud-to-on-premises.md)
- 사용자 수명 주기 관리 전략 결정
- [디바이스 관리 전략 결정](../devices/overview.md)
   - [Azure AD 연결에 대한 사용 시나리오와 배포 고려 사항](../devices/azureadjoin-plan.md)
- [조직의 비즈니스용 Windows Hello 관리](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

## <a name="plan-and-deploy-day-90-and-beyond"></a>플랜 및 배포: 90일 이상

- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md)
   - [PIM에서 Azure AD 디렉터리 역할 설정 구성](../privileged-identity-management/pim-how-to-change-default-settings.md)
   - [PIM에서 Azure AD 디렉터리 역할 할당](../privileged-identity-management/pim-how-to-add-role-to-user.md)
- [PIM에서 Azure AD 디렉터리 역할에 대한 액세스 검토 완료](../privileged-identity-management/pim-how-to-start-security-review.md)
- 전체적으로 사용자 수명 주기 관리
   - Azure AD에 ID 수명 주기를 관리하는 방법이 있습니다.
   - 무단 액세스를 방지하려면 직원 계정 수명 주기에서 수동 단계를 제거하십시오.
      - HR 시스템(진실의 근원)의 ID를 Azure AD에 동기화합니다.
      - [동적 그룹을 사용하여 부서, 직함, 지역 및 기타 속성과 같은 HR(또는 진실의 근원)의 특성을 기반으로 사용자를 그룹에 자동으로 할당합니다.](../users-groups-roles/groups-dynamic-membership.md)
      - [그룹 기반 액세스 관리 프로비전을 사용하여 SaaS 애플리케이션에 사용자를 자동으로 프로비전합니다.](../manage-apps/what-is-access-management.md)

## <a name="next-steps"></a>다음 단계

[ID 및 디바이스 액세스 구성](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[일반적으로 권장되는 ID 및 디바이스 액세스 정책](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
