---
title: 관리 단위 관리 (미리 보기)-Azure AD | Microsoft Docs
description: Azure Active Directory에서 보다 세부적인 권한 위임을 위해 관리 단위 사용
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12101b170066bc52e75f4aa281c6f1c21283c004
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406449"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active Directory의 관리 단위 관리 (미리 보기)

이 문서에서는 Azure AD (Azure Active Directory)의 관리 단위에 대해 설명 합니다. 관리 단위는 다른 Azure AD 리소스에 대 한 컨테이너가 될 수 있는 Azure AD 리소스입니다. 이 미리 보기 릴리스에서는 관리 단위에 사용자 및 그룹만 포함 될 수 있습니다.

관리 단위를 사용 하면 정의한 조직의 부서, 지역 또는 기타 세그먼트로 제한 된 관리자 권한을 부여할 수 있습니다. 관리 단위를 사용 하 여 지역 관리자에 게 권한을 위임 하거나 세부적인 수준에서 정책을 설정할 수 있습니다. 예를 들어 사용자 계정 관리자는 프로필 정보를 업데이트 하 고, 암호를 다시 설정 하 고, 관리 단위 에서만 사용자에 대 한 라이선스를 할당할 수 있습니다.

 예를 들어 지역 지원 전문가에 게 위임 하는 [기술 지원팀 관리자](directory-assign-admin-roles.md#helpdesk-administrator) 역할은 지원 되는 지역의 사용자만 관리 하도록 제한 됩니다.

## <a name="deployment-scenario"></a>배포 시나리오

관리 단위를 사용 하 여 관리 범위를 제한 하는 것은 모든 종류의 독립적인 부서로 구성 된 조직에서 유용할 수 있습니다. 각각에 대 한 액세스를 제어 하 고, 사용자를 관리 하 고, 학교에 대 한 정책을 설정 하는 IT 관리자 팀을 포함 하는 많은 자치 학교 (비즈니스 학교, 엔지니어링 학교 등)로 구성 된 대기업의 예를 생각해 보세요. 중앙 관리자는 다음과 같은 작업을 할 수 있습니다.

- 비즈니스 학교 관리 단위의 Azure AD 사용자만 관리 권한을 사용 하 여 역할 만들기
- 비즈니스 학교를 위한 관리 단위 만들기
- 비즈니스 학교 학생 및 교직원 에게만 관리 단위 채우기
- 해당 범위를 사용 하 여 비즈니스 school IT 팀을 역할에 추가

## <a name="license-requirements"></a>라이선스 요구 사항

관리 단위를 사용 하려면 각 관리 단위 관리자에 대 한 Azure Active Directory Premium 라이선스 및 관리 단위 구성원에 대 한 Azure Active Directory Free 라이선스가 필요 합니다. 자세한 내용은 [Azure AD Premium 시작을 참조하세요](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>관리 단위 관리

이 미리 보기 릴리스에서는 Azure Portal, PowerShell cmdlet 및 스크립트 또는 Microsoft Graph를 사용 하 여 관리 단위를 관리할 수 있습니다. 자세한 내용은 설명서를 참조할 수 있습니다.

- [관리 단위에 대 한 역할 만들기, 제거, 채우기 및 추가](roles-admin-units-manage.md): 전체 방법 절차
- [관리 단위 작업](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0): PowerShell을 사용 하 여 관리 단위로 작업 하는 방법
- [관리 단위 그래프 지원](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta): 관리 단위에 대 한 Microsoft Graph 자세한 설명서입니다.

### <a name="planning-your-administrative-units"></a>관리 단위 계획

관리 단위를 사용 하 여 Azure AD 리소스를 논리적으로 그룹화 할 수 있습니다. 예를 들어 IT 부서가 전체적으로 분산 된 조직의 경우 해당 지리적 경계를 정의 하는 관리 단위를 만드는 것이 적합할 수 있습니다. 다기능 조직의 "하위 조직"이 서로 다른 "하위 조직"을 포함 하는 또 다른 시나리오에서는 각 하위 조직이 관리 단위로 표시 될 수 있습니다.

관리 단위가 만들어지는 기준은 조직의 고유한 요구 사항에 따라 결정 됩니다. 관리 단위는 M365 services에서 구조를 정의 하는 일반적인 방법입니다. M365 서비스에서 사용 하 여 관리 단위를 준비 하는 것이 좋습니다. 관리 단위에서 M365 간에 공용 리소스를 연결할 수 있는 경우 관리 단위에서 최대 값을 얻을 수 있습니다.

조직에서 관리 단위를 만들 때 다음 단계를 수행 하는 것으로 예측할 수 있습니다.

1. 초기 적용: 조직에서 초기 조건에 따라 관리 단위를 만들기 시작 하 고, 조건이 구체화 됨에 따라 관리 단위 수가 증가 됩니다.
1. 정리: 조건이 잘 정의 되 면 더 이상 필요 하지 않은 관리 단위는 삭제 됩니다.
1. 안정화: 조직 구조가 잘 정의 되 고 관리 단위 수가 짧은 기간 동안 크게 변경 되지 않습니다.

## <a name="currently-supported-scenarios"></a>현재 지원 되는 시나리오

전역 관리자 또는 권한 있는 역할 관리자는 Azure AD 포털을 사용 하 여 관리 단위를 만들고, 사용자를 관리 단위의 멤버로 추가 하 고, IT 직원을 관리 단위 범위 관리자 역할에 할당할 수 있습니다. 그런 다음 관리 단위 범위 관리자는 Office 365 포털을 사용 하 여 관리 단위에서 사용자의 기본 관리를 수행할 수 있습니다.

또한 그룹은 관리 단위의 구성원으로 추가 될 수 있으며, 관리자 단위 범위 그룹 관리자는 PowerShell, Microsoft Graph 및 Azure AD 포털을 사용 하 여 관리할 수 있습니다.

아래 표에서는 관리 단위 시나리오에 대 한 현재 지원에 대해 설명 합니다.

### <a name="administrative-unit-management"></a>관리 단위 관리

사용 권한 |   MS Graph/PowerShell   | Azure AD 포털 | Microsoft 365 관리 센터
----------- | ----------------------- | --------------- | -----------------
관리 단위 만들기 및 삭제   |    지원 여부    |   지원됨   |    지원되지 않음
개별적으로 관리 단위 구성원 추가 및 제거    |   지원 여부    |   지원됨   |    지원되지 않음
.Csv 파일을 사용 하 여 관리 단위 구성원 대량 추가 및 제거   |    지원되지 않음     |  지원됨   |    지원 계획 없음
관리 단위 범위 관리자 할당  |     지원 여부    |   지원됨    |   지원되지 않음
특성에 따라 동적으로 AU 멤버 추가 및 제거 | 지원되지 않음 | 지원되지 않음 | 지원되지 않음

### <a name="user-management"></a>사용자 관리

사용 권한 |   MS Graph/PowerShell   | Azure AD 포털 | Microsoft 365 관리 센터
----------- | ----------------------- | --------------- | -----------------
사용자 속성, 암호, 라이선스의 관리 단위 범위 관리   |    지원 여부     |  지원 여부   |   지원 여부
사용자 로그인의 관리 단위 범위 차단 및 차단 해제    |   지원 여부   |    지원 여부   |    지원 여부
사용자 MFA 자격 증명의 관리 단위 범위 관리   |    지원 여부   |   지원됨   |   지원되지 않음

### <a name="group-management"></a>그룹 관리

사용 권한 |   MS Graph/PowerShell   | Azure AD 포털 | Microsoft 365 관리 센터
----------- | ----------------------- | --------------- | -----------------
그룹 속성 및 멤버의 관리 단위 범위 관리     |  지원 여부   |    지원됨    |  지원되지 않음
그룹 라이선스의 관리 단위 범위 관리   |    지원 여부  |    지원됨   |   지원되지 않음

> [!NOTE]
>
> 관리 단위 범위가 있는 관리자는 동적 그룹 멤버 자격 규칙을 관리할 수 없습니다.

관리 단위는 관리 권한에만 범위를 적용 합니다. 구성원이 나 관리자가 [기본 사용자 권한을](../fundamentals/users-default-permissions.md) 사용 하 여 관리 단위 외부의 다른 사용자, 그룹 또는 리소스를 검색 하는 것을 방지 하지 않습니다. Office 365 포털에서 범위가 지정 된 관리자의 관리 단위 외부의 사용자는 필터링 되지만 Azure AD 포털, PowerShell 및 기타 Microsoft 서비스에서 다른 사용자를 탐색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [오스트레일리아 관리](roles-admin-units-manage.md)
- [Au에서 사용자 관리](roles-admin-units-add-manage-users.md)
- [Au에서 그룹 관리](roles-admin-units-add-manage-groups.md)
- [AU에 범위 지정 역할 할당](roles-admin-units-assign-roles.md)