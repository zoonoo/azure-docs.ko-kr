---
title: Azure ID 및 액세스 제어를 사용하여 개인 데이터 보호 | Microsoft Docs
description: Azure ID 및 액세스 제어는 개인 데이터를 보호하는 데 도움이 되며, GDPR(일반 데이터 보호 규정)을 준수하는 데 유용할 수 있습니다.
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 08c26942294d7027ffdcd3fcaa0ff0831e95509f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Azure Active Directory 및 Multi-Factor Authentication: ID 및 액세스 제어를 사용하여 개인 데이터 보호

이 문서에서는 Azure Active Directory 및 Multi-Factor Authentication 보안 기능 및 서비스를 사용하여 개인 데이터를 보호하는 데 사용할 수 있는 정보와 절차를 제공합니다. 이 문서에 포함된 정보는 GDPR(일반 데이터 보호 규정)을 준수하기 위해 사용할 수 있습니다.

## <a name="scenario"></a>시나리오

미국에 본사를 둔 대형 크루즈 회사는 영국 제도뿐만 아니라 지중해, 아드리아해 및 발트해의 여정을 제공하기 위해 운영을 확대하고 있습니다. 이러한 노력을 지원하기 위해 이탈리아, 독일, 덴마크 및 영국에 기반을 둔 몇 개의 소형 크루즈 라인을 인수했습니다. 

회사에서 Microsoft Azure를 사용하여 클라우드에 회사 데이터를 저장합니다. 여기에는 전 세계 고객 기반의 이름, 주소, 전화 번호 및 신용 카드 정보와 같이 식별 가능한 개인 정보가 포함됩니다. 주소, 전화 번호, 납세자 번호 및 모든 위치의 회사 직원에 대한 기타 정보와 같이 기존의 인적 자원 정보도 포함됩니다. 또한 크루즈 라인에서 현재 및 과거 고객과의 관계를 추적하기 위해 개인 정보가 포함된 보상 및 충성도 프로그램 구성원에 대한 대규모 데이터베이스를 유지하고 있습니다.

회사 직원은 회사의 원격 사무실에서 네트워크에 액세스하고 전 세계에 위치한 여행사는 일부 회사 리소스에 액세스할 수 있습니다.

## <a name="problem-statement"></a>문제 설명

회사는 노출된 ID를 사용하여 액세스 권한을 얻으려고 하는 공격자로부터 고객 및 직원 개인 데이터의 개인 정보를 보호해야 합니다. 또한 합법적인 사용자가 개인 데이터에 액세스하는 경우 자신의 직업을 수행하는 데 필요한 사람들에게만 제한되도록 해야 합니다.

## <a name="company-goal"></a>회사 목표

개인 데이터에 대한 액세스를 엄격하게 제어해야 하는 것이 회사의 목표입니다. 강력한 인증을 통해 개인 데이터에 대한 액세스 권한이 있는 사용자의 ID를 보호해야 합니다. 합법적인 사용자에게 필요한 수준의 액세스 권한만 허용하도록 [최소 권한](https://en.wikipedia.org/wiki/Principle_of_least_privilege) 정책을 적용해야 합니다.

## <a name="solutions"></a>솔루션

Microsoft Azure는 회사에서 개인 데이터가 포함된 리소스에 대한 액세스 권한을 가진 사용자를 제어하는 데 도움이 되는 ID 및 액세스 관리 도구를 제공합니다.

### <a name="azure-active-directory"></a>Azure Active Directory

[AAD(Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/)는 ID를 관리하고, 기타 온-프레미스 및 기타 클라우드 리소스, 데이터, 응용 프로그램에 대한 액세스뿐만 아니라 Azure에 대한 액세스도 제어합니다. [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)를 사용하면 Azure 관리자가 개인 데이터와 같은 특정 정보에 액세스할 수 있는 사용자의 수를 최소화할 수 있습니다. 이를 통해 권한 있는 ID와 해당 리소스에 대한 액세스를 검색, 제한 및 모니터링하고, 자격이 있는 사용자에게 일시적인 JIT(Just-In-Time) 관리 권한을 할당할 수 있습니다. 또한 AAD 관리 권한을 가진 사용자에 대한 정보도 제공합니다.

AAD PIM 사용과 관련된 활동은 다음과 같습니다.

- 디렉터리에 대한 Privileged Identity Management 활성화

- Privileged Identity Management 관리 대시보드를 사용하여 한 눈에 중요한 정보 확인

- 각 역할에 영구 또는 적격 관리자를 추가하거나 제거하여 권한 있는 ID(관리자) 관리

- 역할 활성화 설정 구성

- 역할 활성화

- 역할 활동 검토

#### <a name="how-do-i-enable-aad-pim"></a>AAD PIM을 사용하도록 설정하려면 어떻게 할까요?

디렉터리에 PIM을 사용하려면 다음을 수행합니다.

1. Azure Portal에 디렉터리의 전역 관리자로 로그인합니다.

2. 조직에 둘 이상의 디렉터리가 있는 경우 Azure 포털의 오른쪽 위에서 사용자 이름을 선택합니다. Azure AD Privileged Identity Management를 사용할 디렉터리를 선택합니다.

3. **더 많은 서비스**를 선택하고 **필터** 텍스트 상자를 사용하여 Azure AD Privileged Identity Management를 검색합니다.

4. **대시보드에 고정** 옵션을 선택하고 **만들기**를 클릭합니다. Privileged Identity Management 응용 프로그램이 열립니다.

Azure AD Privileged Identity Management를 설정하면 응용 프로그램을 열 때마다 탐색 블레이드가 표시됩니다.

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

AAD PIM 시작에 대한 자세한 내용과 지침은 [Azure AD Privileged Identity Management 시작](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started)을 참조하세요.

### <a name="azure-role-based-access-control"></a>Azure 역할 기반 Access Control

[Azure 역할 기반 Access Control(RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)을 사용하면 Azure 관리자가 사용자에게 할당된 역할에 따라 액세스 권한을 부여함으로써 Azure 리소스에 대한 액세스를 관리할 수 있습니다. 팀 내에서 직무를 분리하고, 자신들의 작업을 수행하는 데 필요한 사용자, 그룹 및 응용 프로그램에 대한 액세스 권한만 부여할 수 있습니다.

Azure 포털, Azure 명령줄 도구 또는 Azure 관리 API를 사용하여 사용자에게 역할 기반 액세스를 부여할 수 있습니다.

Azure RBAC 기본 사항에 대한 자세한 내용은 [Azure Portal에서 역할 기반 Access Control 시작](https://docs.microsoft.com/azure/role-based-access-control/overview)을 참조하세요.

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>PowerShell을 사용하여 Azure RBAC를 관리하려면 어떻게 할까요?

PowerShell cmdlet을 사용하여 다음 관리 작업을 포함한 Azure RBAC를 관리할 수 있습니다.

- 역할 나열

- 액세스 권한이 있는 사용자 확인

- 액세스 권한 부여

- 액세스 권한 제거

- 사용자 지정 역할 만들기

- 리소스 공급자에 대한 작업 가져오기

- 사용자 지정 역할 수정

- 사용자 지정 역할 삭제

- 사용자 지정 역할 나열

PowerShell을 사용하여 Azure RBAC를 관리하는 방법에 대한 지침은 [Azure PowerShell을 사용하여 역할 기반 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)를 참조하세요.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

[Azure MFA(Multi-Factor Authentication)](https://docs.microsoft.com/azure/multi-factor-authentication/)는 간단한 로그인 프로세스에 대한 사용자의 요구를 충족하면서 데이터와 응용 프로그램에 대한 액세스를 보호하는 데 도움이 되는 2단계 인증 솔루션입니다. 전화 통화, 문자 메시지 또는 모바일 앱 확인과 같은 다양한 확인 방법을 통해 강력한 인증을 전달합니다.

Azure 클라우드에 MFA를 배포하려면 먼저 이를 사용하도록 설정한 다음 사용자에 대한 2단계 인증을 설정해야 합니다.

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>Azure에서 MFA를 사용하도록 설정하려면 어떻게 할까요?

사용자에게 Azure Multi-Factor Authentication을 포함하는 라이선스가 있으면 사용자 또는 그룹 기준으로 Azure MFA를 구성하면 됩니다. 

![MFA 사용 가능 사용자](media/protect-personal-data-identity-access-controls/enable-mfa.png)

현재 라이선스가 없는 경우 시나리오에 가장 적합한 배포 유형을 결정하는 프로세스를 진행해야 합니다. 먼저 [사용자를 위한 Azure Multi-Factor Authentication 솔루션 선택](../active-directory/authentication/concept-mfa-whichversion.md) 문서를 확인하세요. 이를 결정할 경우 Multi-Factor Authentication 서버를 만들어야 합니다. 다음 단계를 수행하여 시작할 수 있습니다.

1. Azure Portal에서 **Active Directory**를 선택합니다(관리자 권한으로 로그인함).

2. **MFA 서버**를 선택합니다.

3. 시간 제한 값을 지정합니다. 

    ![](media/protect-personal-data-identity-access-controls/mfa-server-settings.png)

4. 페이지 맨 아래에 있는 **저장**

이 창에는 MFA 서버를 다운로드할 수 있는 옵션도 있습니다. 배포 규모를 지정하고 배포를 계획하는 방법에 대한 자세한 내용은 [Azure Multi-Factor Authentication 서버로 시작하기](../multi-factor-authentication/multi-factor-authentication-get-started-server.md) 문서를 참조하세요.

Multi-Factor Auth 공급자를 관리하는 방법에 대한 자세한 내용은 [Azure Multi-Factor Auth 공급자 시작](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider)을 참조하세요.

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>사용자에 대한 2단계 인증을 설정하려면 어떻게 할까요?

모든 로그인에 대해 2단계 인증을 적용하거나 특정 조건이 적용될 때만 2단계 인증을 요구하는 조건부 액세스 정책을 만들 수 있습니다.

사용자 상태를 변경하여 Azure MFA를 사용하도록 설정하는 것이 2단계 인증을 요구하는 일반적인 방법입니다. 사용하도록 설정한 모든 사용자는 로그인할 때마다 2단계 인증을 수행해야 하는 동일한 요구 사항을 갖게 됩니다. 사용자가 사용하도록 설정되면 해당 사용자에게 영향을 줄 수 있는 모든 조건부 액세스 정책이 무시됩니다.

조건부 액세스 정책을 사용하여 Azure MFA를 사용하도록 설정하는 것은 2단계 인증을 요구하는 것보다 더 유연한 방법입니다. 개별 사용자뿐만 아니라 그룹에도 적용되는 조건부 액세스 정책을 만들 수 있습니다. 위험 수준이 높은 그룹에는 위험 수준이 낮은 그룹보다 더 많은 제한이 제공되거나 위험 수준이 높은 클라우드 앱에만 2단계 인증이 요구되고 위험 수준이 낮은 그룹에는 건너뛸 수 있습니다. 그러나 조건부 액세스는 Azure Active Directory의 유료 기능입니다.

사용자 상태를 변경하여 MFA를 사용하도록 설정하려면 다음을 수행합니다.

1. Azure Portal에 관리자 권한으로 로그인합니다.
2. **Azure Active Directory \> 사용자 및 그룹 \> 모든 사용자**로 차례로 이동합니다.
3. **Multi-Factor Authentication**을 선택합니다.
4. Azure MFA에 사용하려는 사용자를 찾습니다. 위쪽에서 보기를 변경해야 할 수 있습니다.
5. 사용자 이름 옆의 확인란을 선택합니다.
6. 오른쪽의 빠른 단계 아래에서 **사용**을 선택합니다.

   ![](media/protect-personal-data-identity-access-controls/mfa-bulk.png)

7. 열리는 팝업 창에서 선택한 내용을 확인합니다.  MFA를 사용하도록 설정된 사용자에게는 다음에 로그인할 때 등록하도록 요구하는 메시지가 표시됩니다.

조건부 액세스 정책을 사용하여 Azure MFA를 사용하도록 설정하려면 다음을 수행합니다.

1. Azure Portal에 관리자 권한으로 로그인합니다.

2. **Azure Active Directory \> 조건부 액세스**로 차례로 이동합니다.

3. **새 정책**을 선택합니다.

4. **할당** 아래에서 **사용자 및 그룹**을 선택합니다. **포함** 및 **제외** 탭을 사용하여 정책으로 관리될 사용자와 그룹을 지정합니다.

5. **할당** 아래에서 **클라우드 앱**을 선택합니다. **모든 클라우드 앱**을 포함하도록 선택합니다.
6.  **액세스 제어** 아래에서 **허용**을 선택합니다. **다단계 인증 필요**를 선택합니다.
7.  **정책 사용**을 **설정**으로 선택한 다음 **저장**을 선택합니다.

사기 행위 경고 설정, 일회성 바이패스 만들기, 사용자 지정 음성 메시지 사용, 캐싱 구성, 신뢰할 수 있는 IP 지정, 앱 암호 만들기, 사용자가 신뢰하는 장치에 대한 MFA 기억 사용 및 확인 방법에 대한 Azure MFA 설정을 구성하는 방법은 [Azure Multi-Factor Authentication 설정 구성](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure AD에서 권한 있는 액세스 보안](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [Azure Multi-Factor Authentication에 대한 질문과 대답](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [역할 기반 Access Control 문제 해결](https://docs.microsoft.com/azure/role-based-access-control/troubleshooting)

- [Azure Active Directory ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
