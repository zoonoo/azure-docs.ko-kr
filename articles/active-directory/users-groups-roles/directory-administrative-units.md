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
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028422"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active 디렉터리에서 관리 단위 관리(미리 보기)

이 문서에서는 Azure Active Directory(Azure AD)의 관리 단위에 대해 설명합니다. 관리 단위는 다른 Azure AD 리소스에 대한 컨테이너가 될 수 있는 Azure AD 리소스입니다. 이 미리 보기 릴리스에서는 사용자만 이 리소스가 될 수 있습니다. 예를 들어 관리 단위 범위의 사용자 계정 관리자는 프로필 정보를 업데이트하고, 암호를 재설정하고, 관리 단위에서만 사용자에 대한 라이선스를 할당할 수 있습니다.

관리 단위를 사용하여 사용자 하위 집합에 대한 관리 권한을 위임하고 사용자 하위 집합에 정책을 적용할 수 있습니다. 관리 단위를 사용하여 지역 관리자에게 권한을 위임하거나 세부 수준에서 정책을 설정할 수 있습니다.

## <a name="deployment-scenario"></a>배포 시나리오

관리 단위는 독립적인 부서가 있는 조직에서 유용할 수 있습니다. 많은 자율 학교(경영대학, 공학부 등)로 구성된 대규모 대학의 예를 생각해 보십시오. 중앙 관리자는 경영대학의 관리 단위를 만들고 비즈니스 스쿨 학생및 직원만 채울 수 있습니다. 그런 다음 중앙 관리자는 비즈니스 학교 관리 단위의 Azure AD 사용자에 대해서만 관리 권한을 부여하는 범위 별 역할에 비즈니스 학교 IT 직원을 추가할 수 있습니다.

## <a name="license-requirements"></a>라이선스 요구 사항

관리 단위를 사용하려면 각 관리 단위 관리자에 대해 Azure Active Directory Premium 라이선스가 필요합니다. 자세한 내용은 [Azure AD 프리미엄을 시작하기](../fundamentals/active-directory-get-started-premium.md)를 참조하십시오.

## <a name="managing-administrative-units"></a>관리 단위 관리

이 미리 보기 릴리스에서 관리 단위를 만들고 관리할 수 있는 유일한 방법은 [관리 단위 작업에서](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0) 설명한 대로 Windows PowerShell cmdlets용 Azure Active Directory 모듈을 사용하는 것입니다.

소프트웨어 요구 사항 및 Azure AD 모듈 설치 및 구문, 매개 변수 설명 및 예제를 포함한 관리 단위 관리를 위한 Azure AD 모듈 cmdlets에 대한 참조 정보는 [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)을 참조하십시오.

## <a name="next-steps"></a>다음 단계

[Azure Active Directory 버전](../fundamentals/active-directory-whatis.md)
