---
title: Azure Security Center에서 ID 및 액세스 모니터링 | Microsoft Docs
description: Azure Security Center의 ID 및 액세스 기능을 사용하여 사용자의 액세스 작업과 ID 관련 문제를 모니터링하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: 02e78969ce30f109f16309075b040b06c773b0dd
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946226"
---
# <a name="monitor-identity-and-access"></a>ID 및 액세스 모니터링

보안 경계가 네트워크 경계에서 ID 경계로 발전되었습니다. 이 개발을 통해 보안은 네트워크를 방어 하는 것과 응용 프로그램, 데이터 및 사용자의 보안을 관리 하는 방법에 대해 더 낮습니다.

Id와 관련 된 활동 및 구성 설정을 모니터링 하 여 인시던트가 발생 하기 전에 사전 대응 조치를 취하 려 고 시도 된 공격을 중지 하는 사후 조치를 수행할 수 있습니다.

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>어떤 id 및 액세스 보호 기능이 제공 Security Center? 

Azure Security Center에는 조직의 id 및 보안 요구 사항을 충족 하는 데 사용할 수 있는 두 가지 전용 보안 컨트롤이 있습니다. 

 - **액세스 및 사용 권한 관리** - [최소 권한 액세스 모델](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) 을 채택 하 고 사용자에 게 작업을 수행 하는 데 필요한 액세스만 부여 하도록 권장 합니다. 이 컨트롤에는 리소스에 대 한 액세스를 제어 하기 위해 [RBAC (역할 기반 액세스 제어)](../role-based-access-control/overview.md) 를 구현 하기 위한 권장 사항도 포함 됩니다.
 
 - **Mfa 사용** - [mfa](https://www.microsoft.com/security/business/identity/mfa) 를 사용 하도록 설정 하 고, 계정을 더 안전 하 게 유지 하 고, 사용자가 Single Sign-On를 사용 하 여 거의 모든 응용 프로그램에 인증할 수 있습니다.

### <a name="example-recommendations-for-identity-and-access"></a>Id 및 액세스에 대 한 예제 권장 사항

Security Center의 **권장** 사항 페이지에서 다음 두 가지 컨트롤에 표시 될 수 있는 권장 사항의 예입니다.

- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- 구독에 최대 3명의 소유자를 지정해야 합니다.
- 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.
- 사용 되지 않는 계정은 구독에서 제거 해야 합니다. 사용 되지 않는 계정은 더 이상 필요 하지 않으며 Azure Active Directory에의 한 로그인에서 차단 된 계정입니다.

> [!TIP]
> 이러한 권장 사항과 이러한 권장 사항에 대 한 자세한 내용은 [id 및 액세스 권장 사항](recommendations-reference.md#recs-identity)을 참조 하세요.

### <a name="limitations"></a>제한 사항

Security Center의 id 및 액세스 보호에는 몇 가지 제한 사항이 있습니다.

- 600 개 이상의 계정을 사용 하는 구독에는 id 권장 사항을 사용할 수 없습니다. 이 경우 이러한 권장 사항은 "사용할 수 없는 평가" 아래에 나열 됩니다.
- CSP (클라우드 솔루션 공급자) 파트너의 관리 에이전트에는 id 권장 사항을 사용할 수 없습니다.
- Id 권장 사항은 PIM (권한 있는 id 관리) 시스템을 사용 하 여 관리 되는 계정을 식별 하지 않습니다. PIM 도구를 사용 하는 경우 **액세스 및 권한 관리** 제어에서 부정확 한 결과가 표시 될 수 있습니다.

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>MFA (multi-factor authentication) 및 Azure Active Directory 

MFA를 사용하려면 [Azure AD(Active Directory) 테넌트 권한](../active-directory/users-groups-roles/directory-assign-admin-roles.md)이 필요합니다.

- AD Premium Edition을 사용하는 경우 [조건부 액세스](../active-directory/conditional-access/concept-conditional-access-policy-common.md)를 사용하여 MFA를 사용하도록 설정합니다.
- AD free edition을 사용 하는 경우 [Azure Active Directory 설명서](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)에 설명 된 대로 **보안 기본값** 을 사용 하도록 설정 합니다.

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>MFA (multi-factor authentication)를 사용 하도록 설정 하지 않은 계정 식별

MFA를 사용 하도록 설정 되지 않은 계정을 확인 하려면 다음 Azure 리소스 그래프 쿼리를 사용 합니다. 이 쿼리는 "구독에 대 한 소유자 권한이 있는 계정에 대해 MFA를 사용 하도록 설정 해야 합니다." 라는 권장 사항의 모든 비정상 리소스-계정을 반환 합니다. 

1. **Azure 리소스 그래프 탐색기**를 엽니다.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Azure 리소스 그래프 탐색기 시작 * * 권장 사항 페이지" :::

1. 다음 쿼리를 입력 하 고 **쿼리 실행**을 선택 합니다.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData`속성은 MFA가 적용 되지 않은 계정에 대 한 계정 개체 id의 목록을 표시 합니다. 

    > [!NOTE]
    > 계정 소유자의 개인 정보를 보호 하기 위해 계정 이름이 아니라 개체 Id로 표시 됩니다.

> [!TIP]
> 또는 Security Center의 REST API 메서드 [평가 (Get)](https://docs.microsoft.com/rest/api/securitycenter/assessments/get)를 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용 되는 권장 사항에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)