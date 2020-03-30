---
title: Azure AD 연결 1.4.xx.x 및 장치 소멸 이해 | 마이크로 소프트 문서
description: 이 문서에서는 Azure AD Connect의 버전 1.4.xx.x에서 발생하는 문제에 대해 설명합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176031"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Azure AD 연결 1.4.xx.x 및 장치 소멸 이해
Azure AD Connect의 버전 1.4.xx.x를 사용하면 일부 고객은 Azure AD에서 Windows 장치의 일부 또는 전부가 사라지는 것을 볼 수 있습니다. 조건부 액세스 권한 부여 중에 Azure AD에서 이러한 장치 ID를 사용하지 않기 때문에 이는 문제가 되지 않습니다. 이 변경 사항은 하이브리드 Azure AD 조인에 대해 Azure AD에 올바르게 등록된 Windows 장치를 삭제하지 않습니다.

내보내기 삭제 임계값을 초과하는 Azure AD에서 장치 개체가 삭제되는 경우 고객은 삭제를 허용하는 것이 좋습니다. [방법: 삭제 임계값을 초과할 때 삭제가 흐를 수 있도록 허용](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>배경
하이브리드 Azure AD 조인으로 등록된 Windows 장치는 Azure AD에 장치 개체로 표시됩니다. 이러한 장치 개체는 조건부 액세스에 사용할 수 있습니다. Windows 10 장치는 Azure AD Connect를 통해 클라우드에 동기화되고, 다운 레벨 Windows 장치는 AD FS 또는 원활한 단일 사인온을 사용하여 직접 등록됩니다.

## <a name="windows-10-devices"></a>Windows 10 디바이스
하이브리드 Azure AD Join에 의해 구성된 특정 userCertificate 특성 값을 가진 Windows 10 장치만 Azure AD Connect에 의해 클라우드에 동기화되어야 합니다. 이전 버전의 Azure AD Connect에서는 이 요구 사항이 엄격하게 적용되지 않아 Azure AD에서 불필요한 장치 개체가 발생했습니다. Azure AD의 이러한 장치는 Azure AD에 등록할 수 없기 때문에 항상 "보류 중" 상태로 유지되었습니다. 

이 버전의 Azure AD Connect는 하이브리드 Azure AD 가조된 것으로 올바르게 구성된 Windows 10 장치만 동기화합니다. Azure AD가 없는 Windows 10 장치 개체는 특정 사용자인증서를 Azure AD에서 제거합니다.

## <a name="down-level-windows-devices"></a>다운 레벨 Windows 장치
Azure AD Connect는 [다운레벨 Windows 장치를](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)동기화해서는 안 됩니다. 이전에 잘못 동기화된 Azure AD의 모든 장치는 이제 Azure AD에서 삭제됩니다. Azure AD Connect가 [다운 레벨 Windows 장치를](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)삭제하려고 시도하는 경우 장치는 Windows [가 아닌 컴퓨터 MSI용 Microsoft Workplace Join에서](https://www.microsoft.com/download/details.aspx?id=53554) 만든 장치가 아니며 다른 Azure AD 기능에서 사용할 수 없습니다.

일부 고객은 Windows 장치를 올바르게 등록하고 이러한 장치가 장치 기반 조건부 액세스에 완전히 참여할 수 있도록 [하이브리드 Azure Active Directory 조인 구현을 계획하는 방법을](../devices/hybrid-azuread-join-plan.md) 다시 검토해야 할 수 있습니다. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>이 업데이트로 삭제된 장치를 확인하려면 어떻게 해야 합니까?

삭제된 장치를 확인하려면 다음 PowerShell 스크립트를 사용할 수 있습니다.https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

이 스크립트는 Active Directory Computer 개체에 저장된 인증서, 특히 하이브리드 Azure AD 조인 기능에 의해 발급된 인증서에 대한 보고서를 생성합니다.
AD에 있는 컴퓨터 개체의 UserCertificate 속성에 있는 인증서를 검사하고 만료되지 않은 각 인증서에 대해 하이브리드 Azure AD 조인 기능에 대해 인증서가 발급되었는지 확인합니다(예: 주체 이름이 CN={ObjectGUID}와 일치).
이전에 Azure AD Connect는 하나 이상의 유효한 인증서를 포함하지만 Azure AD Connect 버전 1.4에서 시작하는 모든 컴퓨터가 Azure AD에 동기화되도록 하여 하이브리드 Azure AD 조인 인증서를 식별하고 유효한 하이브리드 Azure AD 조인 인증서가 없는 한 컴퓨터 개체가 Azure AD에 동기화되지 않습니다.
이미 AD에 동기화되었지만 유효한 하이브리드 Azure AD 조인 인증서가 없는 Azure AD 장치는 동기화 엔진에서 삭제됩니다(CloudFiltered=TRUE).

## <a name="next-steps"></a>다음 단계
- [Azure AD 연결 버전 기록](reference-connect-version-history.md)
