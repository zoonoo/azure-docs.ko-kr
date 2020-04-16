---
title: 라이선스 셀프 서비스 암호 재설정 - Azure Active Directory
description: Azure Active Directory 셀프 서비스 암호 재설정 라이선스 요구 사항의 차이점에 대해 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca11af061e37cf4f804ce2d7ceed72a9448294
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393073"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory 셀프 서비스 암호 재설정에 대한 라이선스 요구 사항

사용자가 장치 또는 응용 프로그램에 로그인할 수 없을 때 헬프 데스크 호출 및 생산성 손실을 줄이기 위해 Azure Active Directory(Azure AD)의 사용자 계정을 셀프 서비스 암호 재설정(SSPR)에 사용할 수 있습니다. SSPR을 구성하는 기능에는 온-프레미스 디렉터리로의 암호 변경, 재설정, 잠금 해제 및 쓰기 백이 포함됩니다. 기본 SSPR 기능은 Office 365 및 모든 Azure AD 사용자가 무상으로 사용할 수 있습니다.

이 문서에서는 셀프 서비스 암호 재설정에 라이선스를 부여하고 사용할 수 있는 다양한 방법에 대해 자세히 설명합니다. 가격 및 청구에 대한 자세한 내용은 [Azure AD 가격 책정 페이지를](https://azure.microsoft.com/pricing/details/active-directory/)참조하십시오.

## <a name="compare-editions-and-features"></a>버전 및 기능 비교

SSPR은 사용자당 라이선스가 부여됩니다. 규정 준수를 유지하려면 조직에서 사용자에게 적절한 라이선스를 할당해야 합니다.

다음 표에서는 암호 변경, 재설정 또는 온-프레미스 쓰기 에 대한 다양한 SSPR 시나리오와 이 기능을 제공하는 SCO에 대해 간략하게 설명합니다.

| 기능 | Azure AD Free | Office 365 Business Premium | Microsoft 365 Business | Azure AD Premium P1 또는 P2 |
| --- |:---:|:---:|:---:|:---:|
| **클라우드 전용 사용자 암호 변경**<br />Azure AD의 사용자가 암호를 알고 있고 암호를 새 암호로 변경하려는 경우 | ● | ● | ● | ● |
| **클라우드 전용 사용자 암호 재설정**<br />Azure AD의 사용자가 암호를 잊어버렸고 암호를 재설정해야 하는 경우 | | ● | ● | ● |
| **하이브리드 사용자 암호 변경 또는 온-프레임 쓰기 로 재설정**<br />Azure AD Connect를 사용하여 온-프레미스 디렉터리에서 동기화된 Azure AD의 사용자가 암호를 변경하거나 재설정하고 새 암호를 온프레미스로 다시 작성하려는 경우 | | | ● | ● |

> [!WARNING]
> 독립 실행형 Office 365 라이선스 계획은 온-프레미스 쓰기를 통해 SSPR을 지원하지 않습니다. 이러한 Office 365 라이선스 계획에는 이 기능이 작동하려면 Azure AD 프리미엄 P1, 프리미엄 P2 또는 Microsoft 365 비즈니스가 필요합니다.

비용을 포함한 추가 라이선스 정보는 다음 페이지를 참조하십시오.

* [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 기능 및 특성](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [엔터프라이즈 모빌리티 + 보안](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [마이크로소프트 365 비즈니스](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>그룹 또는 사용자 기반 라이선스 사용

Azure AD는 그룹 기반 라이선스를 지원합니다. 관리자는 라이선스를 한 번에 하나씩 할당하는 대신 사용자 그룹에 한꺼번에 라이선스를 할당할 수 있습니다. 자세한 내용은 [라이선스 할당, 확인 및 문제 해결](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)을 참조하세요.

일부 Microsoft 서비스는 일부 위치에서 사용할 수 없습니다. 사용자에게 라이선스를 할당하려면 먼저 관리자는 사용자에 **대한 사용 위치** 속성을 지정해야 합니다. 라이선스 할당은 Azure 포털의 **사용자** > **프로필** > **설정** 섹션에서 수행할 수 있습니다. *그룹 라이선스 할당을 사용할 때 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다.*

## <a name="next-steps"></a>다음 단계

SSPR을 시작하려면 다음 자습서를 완료하십시오.

> [!div class="nextstepaction"]
> [자습서: 셀프 서비스 암호 재설정(SSPR) 사용](tutorial-enable-sspr.md)
