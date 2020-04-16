---
title: 관리 단위 관리(미리 보기) - Azure AD | 마이크로 소프트 문서
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406449"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active 디렉터리에서 관리 단위 관리(미리 보기)

이 문서에서는 Azure Active Directory(Azure AD)의 관리 단위에 대해 설명합니다. 관리 단위는 다른 Azure AD 리소스에 대한 컨테이너가 될 수 있는 Azure AD 리소스입니다. 이 미리 보기 릴리스에서 관리 단위는 사용자 및 그룹만 포함할 수 있습니다.

관리 단위를 사용하면 정의한 조직의 부서, 지역 또는 기타 세그먼트로 제한되는 관리자 권한을 부여할 수 있습니다. 관리 단위를 사용하여 지역 관리자에게 권한을 위임하거나 세부 수준에서 정책을 설정할 수 있습니다. 예를 들어 사용자 계정 관리자는 프로필 정보를 업데이트하고, 암호를 재설정하고, 관리 단위의 사용자에 대해서만 라이선스를 할당할 수 있습니다.

 예를 들어 지역 지원 전문가에게 [헬프데스크 관리자](directory-assign-admin-roles.md#helpdesk-administrator) 역할을 위임하는 것은 지원하는 지역의 사용자만 관리하는 것으로 제한됩니다.

## <a name="deployment-scenario"></a>배포 시나리오

관리 단위를 사용하여 관리 범위를 제한하는 것은 모든 종류의 독립적인 부서로 구성된 조직에서 유용할 수 있습니다. 많은 자율 학교(경영대학, 공학부 등)로 구성된 대규모 대학의 예를 생각해 보십시오. 중앙 관리자는 다음을 수행할 수 있습니다.

- 비즈니스 스쿨 관리 단위의 Azure AD 사용자에 대해서만 관리 권한이 있는 역할을 만듭니다.
- 경영학부 의 관리 부서 만들기
- 비즈니스 스쿨 학생 및 직원만 관리자 단위채우기
- 비즈니스 스쿨 IT 팀을 해당 범위의 역할에 추가

## <a name="license-requirements"></a>라이선스 요구 사항

관리 단위를 사용하려면 각 관리 단위 관리자에 대한 Azure Active Directory Premium 라이선스와 관리 단위 구성원에 대한 Azure Active Directory Free 라이선스가 필요합니다. 자세한 내용은 [Azure AD Premium 시작을 참조하세요](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>관리 단위 관리

이 미리 보기 릴리스에서는 Azure 포털, PowerShell cmdlet 및 스크립트 또는 Microsoft 그래프를 사용하여 관리 단위를 관리할 수 있습니다. 자세한 내용은 설명서를 참조하십시오.

- [관리 단위에 역할 만들기, 제거, 채우기 및 추가:](roles-admin-units-manage.md)방법 수행 절차 완료
- [관리 장치 작업](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0): PowerShell을 사용하여 관리 단위로 작업하는 방법
- [관리 단위 그래프 지원](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta): 관리 단위에 대한 Microsoft 그래프에 대한 자세한 설명서입니다.

### <a name="planning-your-administrative-units"></a>관리 단위 계획

관리 단위는 Azure AD 리소스를 논리적으로 그룹화하는 데 사용할 수 있습니다. 예를 들어 IT 부서가 전 세계에 분산되어 있는 조직의 경우 해당 지리적 경계를 정의하는 관리 단위를 만드는 것이 적합할 수 있습니다. 다국적 조직에 운영에서 반자율적인 다른 "하위 조직"이 있는 또 다른 시나리오에서는 각 하위 조직이 관리 단위로 표시될 수 있습니다.

관리 단위가 생성되는 기준은 조직의 고유한 요구 사항에 따라 안내됩니다. 관리 단위는 M365 서비스 전체에서 구조를 정의하는 일반적인 방법입니다. M365 서비스 전반에서 사용하며 관리 단위를 준비하는 것이 좋습니다. 관리 단위에서 M365에서 공통 리소스를 연결할 수 있는 경우 관리 단위에서 최대 값을 얻을 수 있습니다.

조직에서 관리 단위를 만들어 다음 단계를 수행할 수 있습니다.

1. 초기 채택: 조직은 초기 기준에 따라 관리 단위를 만들기 시작하고 기준이 구체화됨에 따라 관리 단위 수가 증가합니다.
1. 가지 치기: 조건이 잘 정의되면 더 이상 필요하지 않은 관리 단위가 삭제됩니다.
1. 안정화: 조직 구조가 잘 정의되어 있으며 관리 단위 수가 짧은 기간 동안 크게 변경되지 않습니다.

## <a name="currently-supported-scenarios"></a>현재 지원되는 시나리오

전역 관리자 또는 권한 있는 역할 관리자는 Azure AD 포털을 사용하여 관리 단위를 만들고, 사용자를 관리 단위의 구성원으로 추가한 다음 IT 직원을 관리 단위 범위관리자 역할에 할당할 수 있습니다. 그런 다음 관리 단위 범위의 관리자는 Office 365 포털을 사용하여 관리 단위의 사용자를 기본으로 관리할 수 있습니다.

또한 그룹을 관리 단위의 구성원으로 추가할 수 있으며 관리자 단위 범위 그룹 관리자는 PowerShell, Microsoft 그래프 및 Azure AD 포털을 사용하여 그룹을 관리할 수 있습니다.

아래 표는 관리 단위 시나리오에 대한 현재 지원에 대해 설명합니다.

### <a name="administrative-unit-management"></a>관리 단위 관리

사용 권한 |   MS 그래프/파워쉘   | Azure AD 포털 | Microsoft 365 관리 센터
----------- | ----------------------- | --------------- | -----------------
관리 단위 만들기 및 삭제   |    지원됨    |   지원됨   |    지원 안 함
관리 단위 멤버를 개별적으로 추가 및 제거    |   지원됨    |   지원됨   |    지원 안 함
.csv 파일을 사용하여 관리 단위 구성원을 일괄 추가 및 제거   |    지원 안 함     |  지원됨   |    지원할 계획 없음
관리 단위 범위 관리자 할당  |     지원됨    |   지원됨    |   지원 안 함
특성에 따라 AU 멤버 추가 및 제거 | 지원 안 함 | 지원 안 함 | 지원 안 함

### <a name="user-management"></a>사용자 관리

사용 권한 |   MS 그래프/파워쉘   | Azure AD 포털 | Microsoft 365 관리 센터
----------- | ----------------------- | --------------- | -----------------
사용자 속성, 암호, 라이센스의 관리 단위 범위 관리   |    지원됨     |  지원됨   |   지원됨
관리 단위 범위 차단 및 사용자 로그인 차단 해제    |   지원됨   |    지원됨   |    지원됨
사용자 MFA 자격 증명의 관리 단위 범위 관리   |    지원됨   |   지원됨   |   지원 안 함

### <a name="group-management"></a>그룹 관리

사용 권한 |   MS 그래프/파워쉘   | Azure AD 포털 | Microsoft 365 관리 센터
----------- | ----------------------- | --------------- | -----------------
그룹 속성 및 구성원의 관리 단위 범위 관리     |  지원됨   |    지원됨    |  지원 안 함
그룹 라이선싱의 관리 단위 범위 관리   |    지원됨  |    지원됨   |   지원 안 함

> [!NOTE]
>
> 관리 단위 범위를 가진 관리자는 동적 그룹 구성원 규칙을 관리할 수 없습니다.

관리 단위는 관리 권한에만 범위를 적용합니다. 구성원 또는 관리자가 기본 사용자 [권한을](../fundamentals/users-default-permissions.md) 사용하여 관리 단위 외부의 다른 사용자, 그룹 또는 리소스를 찾아보는 것을 방지하지는 않습니다. Office 365 포털에서는 범위가 설정된 관리자의 관리 단위 외부의 사용자가 필터링되지만 Azure AD 포털, PowerShell 및 기타 Microsoft 서비스에서 다른 사용자를 찾아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [오세관리](roles-admin-units-manage.md)
- [AUS의 사용자 관리](roles-admin-units-add-manage-users.md)
- [AUS에서 그룹 관리](roles-admin-units-add-manage-groups.md)
- [AU에 범위 별 역할 할당](roles-admin-units-assign-roles.md)