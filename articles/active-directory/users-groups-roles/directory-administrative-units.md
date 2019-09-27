---
title: 관리 단위 관리(미리 보기) - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 보다 세부적인 권한 위임을 위해 관리 단위 사용
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b61186a876af90c812ec7faf41fa9f5b14bf4e
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336914"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active Directory의 관리 단위 관리 (미리 보기)

이 문서에서는 Azure AD (Azure Active Directory)의 관리 단위에 대해 설명 합니다. 관리 단위는 다른 Azure AD 리소스에 대 한 컨테이너가 될 수 있는 Azure AD 리소스입니다. 이 미리 보기 릴리스에서는 사용자만이 리소스를 사용할 수 있습니다. 예를 들어 관리 단위 범위 사용자 계정 관리자는 프로필 정보를 업데이트 하 고, 암호를 다시 설정 하 고, 사용자의 관리 단위 에서만 라이선스를 할당할 수 있습니다.

관리 단위를 사용 하 여 사용자의 하위 집합에 대 한 관리 권한을 위임 하 고 일부 사용자에 게 정책을 적용할 수 있습니다. 관리 단위를 사용 하 여 지역 관리자에 게 권한을 위임 하거나 세부적인 수준에서 정책을 설정할 수 있습니다.

## <a name="deployment-scenario"></a>배포 시나리오

관리 단위는 독립적인 부서가 있는 조직에서 유용할 수 있습니다. 액세스를 제어 하 고, 사용자를 관리 하 고, 학교에 대 한 정책을 설정 하는 고유한 IT 관리자가 있는 많은 자치 학교 (비즈니스 학교, 엔지니어링 학교 등)로 구성 된 대기업의 예를 생각해 보세요. 중앙 관리자는 비즈니스용 학교에 대 한 관리 단위를 만들어 비즈니스 학교 학생 및 직원만 채울 수 있습니다. 그런 다음 중앙 관리자는 회사 학교 관리 단위의 Azure AD 사용자에 대해서만 관리 권한을 부여 하는 비즈니스 school IT 직원을 범위 지정 된 역할에 추가할 수 있습니다.

## <a name="license-requirements"></a>라이선스 요구 사항

관리 단위를 사용 하려면 각 관리 단위 관리자에 대 한 Azure Active Directory Premium 라이선스가 필요 합니다. 자세한 내용은 [Azure AD Premium 시작을 참조하세요](../fundamentals/active-directory-get-started-premium.md).

## <a name="managing-administrative-units"></a>관리 단위 관리

이 미리 보기 릴리스에서는 관리 단위 [작업](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0) 에 설명 된 대로 관리 단위를 만들고 관리할 수 있는 유일한 방법은 Windows PowerShell cmdlet에 대 한 Azure Active Directory 모듈을 사용 하는 것입니다.

소프트웨어 요구 사항 및 Azure AD 모듈 설치에 대 한 자세한 내용과 구문, 매개 변수 설명 및 예제 등 관리 단위를 관리 하기 위한 Azure AD 모듈 cmdlet에 대 한 참조 정보는 Azure Active를 참조 하세요. [ 디렉터리 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>다음 단계

[Azure Active Directory 버전](../fundamentals/active-directory-whatis.md)
