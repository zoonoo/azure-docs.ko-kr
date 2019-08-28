---
title: Azure 오스트레일리아의 역할 기반 Access Control 및 Privileged Identity Management
description: 오스트레일리아 정부 정책, 규정 및 법규의 특정 요구 사항을 충족 하기 위해 오스트레일리아 지역 내에서 Access Control 및 Privileged Identity Management를 기반으로 하는 역할을 구현 하는 방법에 대 한 지침입니다.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e2a94f82e4830bd1e9c96039f5ef8fe6546b0d0b
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982672"
---
# <a name="role-based-access-control-rbac-and-privileged-identity-management-pim"></a>RBAC (역할 기반 Access Control) 및 Privileged Identity Management (PIM)

관리 권한 관리는 IT 환경 내에서 보안을 보장 하는 중요 한 단계입니다. 최소 권한 보안을 사용 하 여 관리 권한을 제한 하는 것은 acsc [ISM](https://acsc.gov.au/infosec/ism/index.htm) 및 양식에서 보안 권장 사항에 대 한 [acsc 필수 8](https://www.acsc.gov.au/infosec/mitigationstrategies.htm) 목록의 일부입니다.

Microsoft는 Microsoft Azure 내에서 적시에 액세스를 구현 하는 컨트롤 모음을 제공 합니다. 이러한 제어를 이해 하는 것은 클라우드의 효과적인 보안 상태에 필수적입니다. 이 가이드에서는 컨트롤 자체의 개요와 컨트롤을 구현할 때 주요 디자인 고려 사항을 제공 합니다.

## <a name="role-based-access-control-rbac"></a>역할 기반 Access Control(RBAC)

역할 기반 Access Control Microsoft Azure 내의 모든 리소스에 대 한 액세스 관리와 Azure Active Directory (Azure AD) 관리의 핵심이 됩니다. RBAC는 Azure에서 제공 되는 다양 한 보충 기능과 함께 구현할 수 있습니다. 이 문서에서는 Azure 관리 그룹, Azure Active Directory 그룹 및 Azure Privileged Identity Management (PIM)를 사용 하 여 효과적인 RBAC를 구현 하는 방법을 집중적으로 설명 합니다.

높은 수준에서 RBAC를 구현 하려면 다음 세 가지 구성 요소가 필요 합니다.

![RBAC-개요](media/rbac-overview.png)

* **보안 주체**: 보안 주체는 다음 중 하나일 수 있습니다. 사용자, 그룹, [서비스 주체](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)또는 [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)입니다. 보안 주체는 Azure Active Directory 그룹을 사용 하 여 권한을 할당 해야 합니다.

* **역할 정의**: 역할 (역할)이 라고도 하는 역할 정의는 사용 권한 컬렉션입니다. 이러한 권한은 역할 정의에 할당 된 보안 주체에서 수행할 수 있는 작업을 정의 합니다. 이 기능은 Azure 리소스 역할 및 Azure Active Directory 관리자 역할에 의해 제공 됩니다. Azure는 사용자 지정 역할로 확대할 수 있는 기본 제공 역할 (링크)의 집합과 함께 제공 됩니다.

* **범위**: 범위는 역할 정의가 적용 되는 Azure 리소스 집합입니다. Azure 관리 그룹을 사용 하 여 azure 리소스에 azure 역할을 할당할 수 있습니다.

이 세 가지 구성 요소는를 결합 하 여 역할 정의에 정의 된 액세스를 Azure 관리 그룹의 범위에 속하는 모든 리소스에 부여 합니다 .이를 역할 할당 이라고 합니다. 여러 역할 정의를 보안 주체에 할당할 수 있으며 여러 보안 주체를 단일 범위에 할당할 수 있습니다.

### <a name="azure-active-directory-groups"></a>Azure Active Directory 그룹

개인 또는 팀에 권한을 할당 하는 경우 가능한 경우 할당을 Azure Active Directory 그룹에 연결 하 고 해당 사용자에 게 직접 할당 하지 않아야 합니다. 이는 온-프레미스 Active Directory 구현에서 상속 된 것과 동일한 권장 방법입니다. Azure Active Directory 가능 하면 사용자가 만든 Azure 관리 그룹의 논리적 구조와 상호 보완적으로 팀 마다 그룹을 만들어야 합니다.

하이브리드 클라우드 시나리오에서 온-프레미스 Windows Server Active Directory 보안 그룹을 Azure Active Directory 인스턴스에 동기화 할 수 있습니다. 이러한 Windows Server Active Directory 보안 그룹을 사용 하 여 RBAC 온-프레미스를 이미 구현한 경우 이러한 그룹을 동기화 한 후에 사용 하 여 Azure 리소스에 대 한 RBAC를 구현할 수 있습니다. 그렇지 않으면 클라우드 환경을 깨끗 한 슬레이트로 확인 하 여 Azure Active Directory 구현을 기반으로 하는 강력한 권한 관리 계획을 설계 하 고 구현할 수 있습니다.

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Azure 리소스 역할 및 Azure Active Directory 관리자 역할

Microsoft Azure는 [Azure 리소스](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) 및 [Azure Active Directory 관리](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)를 위한 다양 한 기본 제공 역할을 제공 합니다. 두 역할 유형 모두 Azure 리소스 또는 Azure AD 관리자에 대 한 구체적인 액세스를 제공 합니다. Azure 리소스 역할은 azure AD에 대 한 관리 액세스를 제공 하는 데 사용할 수 없고 azure AD 역할은 Azure 리소스에 대 한 특정 액세스를 제공 하지 않는다는 점에 유의 해야 합니다.

기본 제공 역할을 사용 하 여 Azure 리소스에 할당할 수 있는 액세스 유형의 몇 가지 예는 다음과 같습니다.

* 한 사용자는 구독의 가상 머신을 관리하고 다른 사용자는 가상 네트워크를 관리하도록 허용
* DBA 그룹이 구독의 SQL 데이터베이스를 관리하도록 허용
* 사용자가 가상 머신, 웹 사이트, 서브넷 등 리소스 그룹의 모든 리소스를 관리하도록 허용
* 애플리케이션이 리소스 그룹의 모든 리소스에 액세스하도록 허용

Azure AD 관리에 대해 할당할 수 있는 액세스 형식의 예는 다음과 같습니다.

* 기술 지원팀 직원이 사용자 암호를 다시 설정할 수 있도록 허용
* 직원이 외부 사용자를 B2B 공동 작업을 위해 Azure AD 인스턴스에 초대 하도록 허용
* 관리 담당자가 로그인 및 감사 보고서에 대 한 읽기 액세스 허용
* 직원 들이 암호 재설정을 포함 하 여 모든 사용자 및 그룹을 관리할 수 있도록 허용 합니다.

과도 한에 대 한 액세스 권한이 부여 되지 않도록 기본 제공 역할에서 제공 하는 허용 된 작업의 전체 목록을 이해 하는 것이 중요 합니다. 기본 제공 역할 목록과 이러한 역할에서 제공 하는 액세스는 지속적으로 진화 하 고 있으며, 위에 연결 된 설명서를 검토 하거나 Azure PowerShell cmdlet을 사용 하 여 전체 역할 목록과 해당 정의를 볼 수 있습니다.

```PowerShell
PS C:\> Get-AzRoleDefinition

Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

또는 Azure CLI 명령:

```bash
PS C:\> az role definition list
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

또한 필요에 따라 사용자 지정 Azure 리소스 역할을 만들 수 있습니다. 이러한 사용자 지정 역할은 PowerShell 또는 Azure CLI를 통해 Azure Portal 만들 수 있습니다. 사용자 지정 역할을 만들 때 역할의 용도가 고유 하 고 기존 Azure 리소스 역할에서 해당 기능을 아직 제공 하지 않았는지 확인 하는 것이 중요 합니다. 이렇게 하면 지속적인 관리 복잡성이 줄어들고 보안 주체가 불필요 한 권한을 받는 위험을 줄일 수가 줄어듭니다. 예를 들어 기본 제공 되는 Azure 리소스 역할, "가상 컴퓨터 참가자" 및 "가상 컴퓨터 관리자 로그인" 사이에 있는 사용자 지정 Azure 리소스 역할을 만듭니다.

사용자 지정 역할은 기존 참가자 역할을 기반으로 할 수 있으며, 다음 액세스 권한을 부여 합니다.

| Azure 리소스 | 액세스 수준 |
| --- | --- |
| 가상 머신 | 관리할 수 있지만 액세스할 수 없음 |
| VM에 연결 된 Virtual Network | 액세스할 수 없음 |
| VM에 연결 된 저장소 | 액세스할 수 없음 |
|

사용자 지정 역할은이 기본 액세스를 유지할 수 있지만, 지정 된 사용자에 게 가상 컴퓨터의 네트워크 구성을 수정할 수 있는 몇 가지 기본 추가 권한을 허용 합니다.

Azure 리소스 역할은 Azure 관리 그룹를 통해 리소스에 할당할 수 있는 이점도 있습니다.

### <a name="azure-management-groups"></a>Azure 관리 그룹

Azure 관리 그룹는 조직에서 Azure 테 넌 트 내의 모든 구독과 해당 리소스에 대 한 역할 할당을 관리 하는 데 사용할 수 있습니다. Azure 관리 그룹는 Azure 내에서 계층적으로 조직 구조를 매핑하는 기능을 포함 하 여 관리 계층 구조를 만들 수 있도록 설계 되었습니다. 별도의 논리적 엔터티로 조직 비즈니스 단위를 만들면 각 팀의 특정 요구 사항에 따라 조직 내에서 사용 권한을 적용할 수 있습니다. Azure 관리 그룹을 사용 하 여 최대 6 수준까지 관리 계층 구조를 정의할 수 있습니다.

![관리 그룹](media/management-groups.png)

Azure 관리 그룹는 azure 테 넌 트 내에서 Azure 구독에 매핑됩니다. 이를 통해 조직에서는 특정 비즈니스 단위에 속한 Azure 리소스를 분리 하 고 비용 관리 및 권한 할당에 대 한 세부적인 제어 수준을 제공할 수 있습니다.

## <a name="privileged-identity-management-pim"></a>PIM(Privileged Identity Management)

Microsoft는 Azure Privileged Identity Management를 통해 JIT (Just-in-time) 및 JEA (Just Laccess)를 구현 했습니다. 이 서비스를 통해 관리 직원은 Azure 리소스에 대 한 권한 있는 액세스를 제어, 관리 및 모니터링할 수 있습니다. PIM을 사용 하면 관리 담당자가 역할에 대해 "적격"으로 보안 주체를 만들 수 있으므로 사용자가 Azure Portal 또는 PowerShell cmdlet을 통해 역할 활성화를 요청할 수 있습니다. 기본적으로 역할 할당은 1 ~ 007e; 72 시간 사이의 기간 동안 활성화 될 수 있습니다. 필요한 경우 사용자가 해당 역할 할당에 대 한 확장을 요청 하 고 역할 할당을 영구적으로 설정 하는 옵션을 설정할 수 있습니다. 필요에 따라 사용자가 적격 역할의 활성화를 요청 하는 경우 Multi-factor Authentication에 대 한 요구 사항을 적용할 수 있습니다. 역할 활성화의 할당 된 기간이 만료 된 후에는 보안 주체에 게 더 이상 역할에서 권한 있는 액세스 권한이 부여 되지 않습니다.

PIM을 사용 하면 Just-in-time 액세스를 사용 하지 않거나 권한 할당의 일상적인 감사를 수행 하지 않는 환경에서 발생할 수 있는 일반적인 권한 할당 문제가 방지 됩니다. 한 가지 일반적인 문제는 상승 된 권한 할당은 상승 된 권한 할당을 수행 하는 작업을 완료 한 후에 길게 남아 있습니다. 또 다른 문제는 다른 유사한 보안 주체를 구성할 때 보안 주체에 할당 된 액세스를 복제 하 여 환경 내에서 상승 된 권한으로 인 한 것입니다.

## <a name="key-design-considerations"></a>주요 디자인 고려 사항

최소 권한 보안을 적용 하기 위해 RBAC 전략을 설계할 때 다음 보안 요구 사항을 고려해 야 합니다.

* 권한 있는 액세스 요청에 대 한 유효성 검사
* 관리 권한은 특정 업무를 수행 하는 데 필요한 최소한의 액세스로 제한 됩니다.
* 관리 권한은 특정 업무를 수행 하는 데 필요한 최소 기간으로 제한 됩니다.
* 부여 된 관리 권한의 정기적인 검토가 수행 됩니다.

RBAC 전략을 디자인 하는 과정에는 고유한 비즈니스 역할 간의 액세스 차이와 승격 된 권한을 필요로 하는 작업의 유형 및 빈도를 이해 하는 비즈니스 기능에 대 한 자세한 검토가 필요 합니다. 백업 운영자, 보안 관리자 및 감사자 간 기능의 차이는 다양 한 수준의 지속적인 검토를 사용 하 여 서로 다른 수준의 액세스를 요구 합니다.

## <a name="validate-requests-for-access"></a>액세스 요청 유효성 검사

승격 된 권한은 명시적으로 승인 해야 합니다. 이를 지원 하려면 승인 프로세스를 개발 하 고 추가 권한에 대 한 모든 요청이 타당 한지 유효성을 검사 해야 합니다. Privileged Identity Management는 역할 할당 승인에 대 한 여러 옵션을 제공 합니다. 역할 활성화 요청은 자체 승인을 허용 하거나 제어 하 고, 지정한 승인자가 모든 역할 활성화 요청을 수동으로 검토 하 고 승인 하도록 구성할 수 있습니다. 또한 인증 요청은 티켓 번호와 같은 활성화 요청에 포함 된 추가 지원 정보를 요구 하도록 구성할 수 있습니다.

### <a name="restrict-privilege-based-on-duties"></a>의무에 따라 권한 제한

보안 주체에 부여 되는 권한 수준을 제한 하는 것이 중요 합니다. 권한 할당은 일반적인 IT 보안 공격 벡터입니다. 관리 되는 리소스의 유형 및 담당 하는 팀을 평가 해야 매일 업무에 필요한 최소 수준의 권한을 할당할 수 있습니다. 일상 업무에 필요한 것 이상의 추가 권한은 특정 작업을 수행 하는 데 필요한 기간 동안만 부여 되어야 합니다. 이에 대 한 예는 고객의 관리자에 게 "참가자" 액세스를 제공 하는 것 이며,이를 통해 임시 높은 수준의 액세스를 필요로 하는 특정 작업에 대 한 Azure 리소스에 대 한 "소유자" 권한을 요청할 수 있습니다.

이렇게 하면 각 개별 관리자가 최단 시간 동안 승격 된 액세스 권한만 갖습니다. 이러한 방식을 준수 하면 조직의 모든 IT 인프라에 대 한 전체 공격 노출 영역이 줄어듭니다.

### <a name="regular-evaluation-of-administrative-privilege"></a>관리자 권한 정기 평가

환경 내의 보안 주체를 정기적으로 감사 하 여 올바른 수준의 권한이 현재 할당 되어 있는지 확인 하는 것이 중요 합니다. Microsoft Azure는 Azure 보안 주체에 할당 된 권한을 감사 하 고 평가 하는 다양 한 방법을 제공 합니다. Privileged Identity Management를 통해 관리 직원은 보안 주체에 게 부여 된 역할의 "액세스 검토"를 정기적으로 수행할 수 있습니다. 액세스 검토를 수행 하 여 Azure 리소스 역할 할당과 Azure Active Directory 관리 역할 할당을 모두 감사할 수 있습니다. 액세스 검토는 다음 속성을 사용 하 여 구성할 수 있습니다.

* **이름을 검토 하 고 시작 및 종료 날짜를 검토**합니다. 검토는 지정 된 사용자가 완료 하기에 충분 한 기간으로 구성 해야 합니다.

* **검토할 역할**: 각 액세스 검토는 단일 Azure 역할을 중심으로 합니다.

* **지정한 검토자**: 검토를 수행하는 데 세 가지 옵션이 있습니다. 검토를 다른 사람에게 완료하도록 할당할 수 있습니다, 사용자가 직접 수행하거나 각 사용자가 자신의 액세스를 검토하도록 할 수 있습니다.

* **사용자에 게 액세스에 대 한 이유를 제공 해야 합니다**. 사용자는 액세스 검토를 완료할 때 권한 수준을 유지 하는 이유를 입력 해야 할 수 있습니다.

보류 중인 액세스 검토의 진행 상태는 언제 든 지 Azure Portal 대시보드를 통해 모니터링할 수 있습니다. 검토 중인 역할에 대 한 액세스는 액세스 검토가 완료 될 때까지 변경 되지 않은 상태로 유지 됩니다. 지정 된 기간 내에 모든 PIM 사용자 할당 및 활성화를 [감사할](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-use-audit-log) 수도 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure 오스트레일리아의 시스템 모니터링](system-monitor.md)에 대 한 문서를 검토 합니다.
