---
title: Azure AD Connect 1.4.xx.x 및 디바이스 누락 해석 | Microsoft Docs
description: 이 문서에서는 Azure AD Connect 버전 1.4.xx.x에서 발생하는 문제에 대해 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "73176031"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Azure AD Connect 1.4.xx.x 및 디바이스 누락 해석
Azure AD Connect 버전 1.4.xx.x를 사용하는 고객 중 일부는 Windows 디바이스의 일부 또는 전부가 Azure AD에서 사라지는 것을 보는 경우가 있습니다. 조건부 액세스 권한 부여 중에는 Azure AD에서 해당 디바이스 ID가 사용되지 않기 때문에 걱정할 필요가 없습니다. 하이브리드 Azure AD 조인을 위해 Azure AD에 올바르게 등록된 Windows 디바이스는 절대 삭제되지 않습니다.

Azure AD에서 디바이스 개체를 삭제하는 것이 내보내기 삭제 임계값을 초과하는 것으로 보이면, 삭제가 진행되도록 허용하는 것이 좋습니다. [삭제 임계값을 초과하는 경우 삭제가 진행되도록 하는 방법](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>배경
하이브리드 Azure AD 조인으로 등록된 Windows 디바이스는 Azure AD에서 디바이스 개체로 표시됩니다. 이러한 디바이스 개체는 조건부 액세스에 사용할 수 있습니다. Windows 10 디바이스는 Azure AD Connect를 통해 클라우드와 동기화됩니다. 하위 수준 Windows 디바이스는 AD FS 또는 원활한 Single Sign-On을 사용하여 직접 등록됩니다.

## <a name="windows-10-devices"></a>Windows 10 디바이스
하이브리드 Azure AD 조인에서 구성된 특정 userCertificate 특성 값을 가진 Windows 10 디바이스만 Azure AD Connect에 의해 클라우드로 동기화됩니다. 이전 버전의 Azure AD Connect에서는 이 요구 사항이 엄격하게 적용되지 않았기 때문에 Azure AD에 불필요한 디바이스 개체가 생성되었습니다. Azure AD의 이러한 불필요한 디바이스는 Azure AD에 등록하기 위한 것이 아니므로 항상 "보류 중" 상태를 유지했습니다. 

반면, 새 버전의 Azure AD Connect는 하이브리드 Azure AD 조인되도록 올바르게 구성된 Windows 10 디바이스만 동기화합니다. Azure AD 조인 특정 userCertificate이 없는 Windows 10 디바이스 개체는 Azure AD에서 제거됩니다.

## <a name="down-level-windows-devices"></a>하위 수준 Windows 디바이스
Azure AD Connect는 [하위 수준 Windows 디바이스](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)를 동기화해서는 안 됩니다. Azure AD에서 이전에 잘못 동기화된 모든 디바이스는 이제 Azure AD에서 삭제됩니다. Azure AD Connect가 [하위 수준 Windows 디바이스](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)를 삭제하려고 시도한다면 해당 디바이스는 [Windows 10 컴퓨터를 제외한 컴퓨터 MSI의 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)에 의해 생성된 것이므로 다른 Azure AD 기능에서 사용할 수 없습니다.

필요에 따라 [하이브리드 Azure Active Directory 조인 구현을 계획하는 방법](../devices/hybrid-azuread-join-plan.md)을 다시 참고하여 Windows 디바이스를 올바르게 등록하고 디바이스 기반의 조건부 액세스에 완전히 참여할 수 있도록 합니다. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>이번 업데이트로 어떤 디바이스가 삭제됐는지 어떻게 확인할 수 있나요?

다음 PowerShell 스크립트를 사용하여 삭제된 디바이스를 확인할 수 있습니다: https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

이 스크립트는 Active Directory 컴퓨터 개체에 저장된 인증서, 특히 하이브리드 Azure AD 조인 기능에서 발급한 인증서에 대해 보고서를 작성합니다.
또, AD에 있는 컴퓨터 개체의 UserCertificate 속성에 있는 인증서를 확인하고, 만료되지 않은 각 인증서에 대해 인증서가 하이브리드 Azure AD 조인 기능(즉, 주체 이름이 CN={ObjectGUID}과 일치)에 대해 발급되었는지 확인합니다.
이전에는 Azure AD Connect가 하나 이상의 유효한 인증서를 포함하는 모든 컴퓨터를 Azure AD에 동기화했지만 Azure AD Connect 버전 1.4부터는 하이브리드 Azure AD 조인 인증서를 식별할 수 있으며, 유효한 하이브리드 Azure AD 조인 인증서가 존재하지 않을 때는 컴퓨터 개체를 ‘클라우드 필터’하여 Azure AD로 동기화되지 않도록 합니다.
이미 AD에 동기화되었지만 유효한 하이브리드 Azure AD 조인 인증서가 없는 Azure AD 디바이스는 Azure AD Connect가 삭제합니다(CloudFiltered = TRUE).

## <a name="next-steps"></a>다음 단계
- [Azure AD Connect 버전 내역](reference-connect-version-history.md)
