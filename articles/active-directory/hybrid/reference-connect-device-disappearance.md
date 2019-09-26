---
title: Azure AD Connect 1.4. x x x x 및 device disappearance 이해 | Microsoft Docs
description: 이 문서에서는의 버전 1.4. x. x에서 발생 하는 문제에 대해 설명 Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 1ba17feea880bb55c3b4a14a06b3d803dba2350a
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316955"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Azure AD Connect 1.4. x x x x. x 및 장치 disappearance 이해
Azure AD Connect 버전 1.4. x. x를 사용 하는 경우 일부 고객은 Azure AD에서 일부 또는 모든 Windows 장치를 볼 수 있습니다. 이러한 장치 id는 조건부 액세스 권한 부여 중에 Azure AD에서 사용 되지 않기 때문에 문제가 발생 하지 않습니다. 이 변경은 하이브리드 Azure AD 조인에 대해 Azure AD에 올바르게 등록 된 Windows 장치를 삭제 하지 않습니다.

Azure AD의 장치 개체 삭제가 내보내기 삭제 임계값을 초과 하는 것으로 확인 되 면 고객이 삭제를 통과 하도록 허용 하는 것이 좋습니다. [방법: 삭제가 삭제 임계값을 초과할 때 흐름이 전달 되도록 허용](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>배경
하이브리드 Azure AD 조인으로 등록 된 Windows 장치는 Azure AD에서 장치 개체로 표시 됩니다. 이러한 장치 개체는 조건부 액세스에 사용할 수 있습니다. Windows 10 장치는 Azure AD Connect을 통해 클라우드와 동기화 됩니다. 다운 수준 Windows 장치는 AD FS 또는 원활한 Single Sign-On를 사용 하 여 직접 등록 됩니다.

## <a name="windows-10-devices"></a>Windows 10 장치
하이브리드 Azure AD 조인에서 구성 된 특정 userCertificate 특성 값을 가진 Windows 10 장치만 Azure AD Connect에 의해 클라우드로 동기화 됩니다. 이전 버전의 Azure AD Connect이 요구 사항은 엄격 하 게 적용 되지 않았기 때문에 Azure AD에서 불필요 한 장치 개체가 생성 됩니다. 이러한 장치는 Azure AD에 등록 하기 위한 것이 아니므로 Azure AD의 이러한 장치는 항상 "보류 중" 상태로 높게 유지 됩니다. 

이 버전의 Azure AD Connect는 하이브리드 Azure AD에 연결 되도록 올바르게 구성 된 Windows 10 장치만 동기화 합니다. Azure AD 조인 특정 userCertificate 없는 Windows 10 장치 개체는 Azure AD에서 제거 됩니다.

## <a name="down-level-windows-devices"></a>하위 수준 Windows 장치
Azure AD Connect는 [하위 Windows 장치](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)를 동기화 해서는 안 됩니다. Azure AD에서 이전에 잘못 동기화 된 모든 장치는 이제 Azure AD에서 삭제 됩니다. [하위 수준 windows 장치](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)를 삭제 하려고 시도 하는 Azure AD Connect 경우 해당 장치는 [windows 10이 아닌 컴퓨터에 대 한 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554) 에 의해 만들어진 MSI가 아닌 다른 Azure AD 기능에서 사용할 수 없습니다.

일부 고객은 다음 방법으로 [다시 방문 해야 할 수 있습니다. 하이브리드 Azure Active Directory 조인 구현을](../devices/hybrid-azuread-join-plan.md) 계획 하 여 Windows 장치를 올바르게 등록 하 고 이러한 장치가 장치 기반 조건부 액세스에 완전히 참여할 수 있도록 합니다. 

## <a name="next-steps"></a>다음 단계
- [Azure AD Connect 버전 기록](reference-connect-version-history.md)
