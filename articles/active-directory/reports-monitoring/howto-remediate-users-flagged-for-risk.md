---
title: Azure Active Directory 포털의 위험에 대한 플래그가 지정된 사용자 보안 보고서 | Microsoft Docs
description: Azure Active Directory 포털의 위험에 대한 플래그가 지정된 사용자 보안 보고서에 대해 알아보기
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 13d81e655b98771d985277974bd3081437e2a3e8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51255782"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털에서 위험 플래그가 지정된 사용자 수정

Azure AD(Azure Active Directory)에서 보안 보고를 통해 사용자 환경에서 손상된 사용자 계정의 확률에 대한 정보를 얻을 수 있습니다. 위험 플래그가 지정된 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

Microsoft는 환경을 안전하게 유지하기 위해 최선을 다하고 있습니다. 이 약속의 일환으로, Microsoft는 비정상적이거나 알려진 공격 패턴과 일치하는 활동을 지속적으로 모니터링합니다. 


일부 사용자 계정에 대한 무단 액세스를 나타낼 수 있는 비정상적인 활동이 검색되면 사용자가 조치를 취할 수 있도록 하는 알림을 받습니다. 알림을 제공한다고 해서 Microsoft의 자체 시스템이 어떤 방식으로든 손상된 것은 아닙니다.
 

## <a name="access-the-users-flagged-for-risk-report"></a>위험 플래그가 지정된 사용자 보고서에 액세스

Azure AD(Active Directory)에서 관련된 [보고서](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UsersAtRisk)를 통해 위험 플래그가 지정된 사용자를 검토할 수 있습니다. Azure AD에 대한 구독자가 아닌 경우 [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD)에서 일회성 구독 프로세스를 추가 비용 없이 살펴볼 수 있습니다. 이 보고서에서 다음과 같은 다양한 작업을 수행할 수 있습니다.

- 임시 암호 생성
- 사용자에게 다음에 로그인할 암호를 안전하게 다시 설정하도록 요구
- 업데이트 관리 작업을 수행하지 않고 사용자 위험을 해제합니다.

자세한 내용은 [Azure Active Directory 포털의 위험에 대한 플래그가 지정된 사용자 보안 보고서](concept-user-at-risk.md)를 참조하세요.

### <a name="azure-ad-subscription-for-office-365-customers"></a>Office 365 고객에 대한 Azure AD 구독

완료되면 Office 365 자격 증명을 사용하여 Azure 관리 센터에 액세스할 수 있습니다. Azure AD에 대한 액세스가 활성화되면 Azure AD 포털로 리디렉션됩니다. 기본 구독 수준에서 보고서에서 제공되는 세부 정보의 양은 제한됩니다. Azure Premium 구독자에 대한 추가 데이터 및 분석을 사용할 수 있습니다.


**Office 365 관리 센터에서 위험 플래그가 지정된 사용자 보고서에 액세스하려면**

1.  왼쪽의 탐색 메뉴에서 **관리 센터**를 클릭합니다. 
2.  **Azure AD**를 클릭합니다.
3.  **Azure Active Directory 관리 센터**에 로그인합니다.
4.  페이지 위쪽에 **새 포털 확인**이라는 배너가 표시되면 해당 링크를 클릭합니다.
4.  왼쪽의 탐색 메뉴에서 **Azure Active Directory**를 클릭합니다. 
5.  탐색 창의 **보안** 아래에서 **위험 플래그가 지정된 사용자**를 클릭합니다.

여기에 표시된 정보를 검토합니다. 나열된 계정에 대한 암호를 다시 설정해야 합니다. 

## <a name="remediation-actions"></a>수정 작업

다음 작업을 수행하면 영향을 받는 계정을 수정하고 환경을 보호할 수 있습니다.

1.  다단계 인증 및 셀프 서비스 암호 재설정에 대한 올바른 정보를 [확인](https://aka.ms/MFAValid)합니다. 
2.  모든 사용자에 대해 다단계 인증을 [사용하도록 설정합니다](https://aka.ms/MFAuth). 
3.  이 [수정 스크립트](https://aka.ms/remediate)를 사용하여 영향을 받는 모든 계정에 대해 다음 단계를 자동으로 수행할 수 있습니다. 

    a. 암호를 다시 설정하여 계정을 보호하고 활성 세션을 종료합니다.

    b. 사서함 대리자를 제거합니다.

    다. 외부 도메인에 대한 메일 전달 규칙을 사용하지 않도록 설정합니다.

    d. 사서함에서 전역 메일 전달 속성을 제거합니다.

    e. 사용자 계정에서 MFA를 사용하도록 설정합니다.

    f. 계정에서 암호 복잡성을 높게 설정합니다.

    g. 사서함 감사를 사용하도록 설정합니다.

    h. 관리자가 검토할 감사 로그를 생성합니다.

4. 가능한 모든 수정에 대해 모든 테넌트 설정, 사용자 계정 및 사용자별 구성 설정을 검토하여 Office 365 테넌트 및 기타 IT 인프라를 조사합니다. 지속성 메서드에 대한 표시기 외에도, 침입자가 VPN 자격 증명을 얻거나 다른 조직 리소스에 액세스하기 위해 초기 발판을 활용했을 수 있는 표시기를 확인합니다. 

5.  조사의 일환으로, 법 집행 기관을 포함하여 정부 기관에 알리거나 알려야 하는지 여부를 고려해야 합니다.

또한 다음을 수행해야 합니다.

- 비정상적인 활동을 해결할 때 이 [지침](https://aka.ms/fixaccount)을 참조하고 구현합니다. 
- [감사 파이프라인을 사용하도록 설정](https://aka.ms/improvesecurity)하여 테넌트 활동을 분석할 수 있습니다. 완료되면 감사 저장소가 모든 활동 로그로 채워지기 시작합니다. 이 시점에서 [보안 및 규정 준수 센터의 검색 및 조사](https://aka.ms/sccsearch)를 활용할 수도 있습니다. 
- 이 [스크립트](https://aka.ms/mailboxaudit1)를 사용하여 모든 계정에 대해 사서함 감사를 사용하도록 설정합니다. 
- 모든 사서함에 대한 대리자 권한 및 메일 전달 규칙을 검토합니다. 이 [PowerShell 스크립트](https://aka.ms/delegateforwardrules)를 사용하여 이 작업을 수행할 수 있습니다. 



## <a name="next-steps"></a>다음 단계

- Azure Active Directory ID 보호에 대한 자세한 내용은 [Azure Active Directory ID 보호](../active-directory-identityprotection.md)를 참조하세요.

