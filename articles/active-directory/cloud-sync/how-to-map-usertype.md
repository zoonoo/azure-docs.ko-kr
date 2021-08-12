---
title: Azure AD Connect 클라우드 동기화를 사용하여 UserType 매핑을 사용하는 방법
description: 이 문서에서는 클라우드 동기화를 사용하여 UserType 특성 매핑을 사용하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14bbeb7339b3af41b24b25aed2332ac8367e99ae
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776925"
---
# <a name="map-usertype-with-cloud-sync"></a>클라우드 동기화를 사용하여 UserType 매핑

클라우드 동기화는 사용자 개체에 대한 UserType 특성의 동기화를 지원합니다. 

기본적으로 해당하는 UserType 특성이 온-프레미스 Active Directory에 없기 때문에 동기화에 대해 UserType 특성을 사용하도록 설정되지 않습니다. 동기화를 위해 이 매핑을 수동으로 추가해야 합니다. 이에 앞서 Azure AD가 시행하는 다음 동작에 유의합니다.

- Azure AD는 UserType 특성에 대해 멤버 및 게스트 두 값만 허용합니다.
- UserType 특성이 클라우드 동기화에 매핑되지 않은 경우 디렉터리 동기화를 통해 만든 Azure AD 사용자는 UserType 특성이 멤버로 설정되었을 것입니다.

UserType 특성에 대한 매핑을 추가하기 전에 먼저 이 특성이 온-프레미스 Active Directory에서 파생되는 방법을 결정해야 합니다. 다음은 가장 일반적인 방식입니다.

 - 사용하지 않는 온-프레미스 AD 특성(예: extensionAttribute1)을 원본 속성으로 사용하도록 지정합니다. 지정된 온-프레미스 AD 특성은 문자열 형식이고 단일 값이고 값 멤버 또는 게스트를 포함해야 합니다.
 - 이 방법을 선택한 경우 UserType 특성의 동기화를 사용하도록 설정하기 전에 지정된 특성이 Azure AD에 동기화된 온-프레미스 Active Directory의 모든 기존 사용자 개체에 대해 올바른 값으로 채워져 있는지 확인해야 합니다.

## <a name="to-add-the-usertype-mapping"></a>UserType 매핑을 추가하려면
UserType 매핑을 추가하려면 다음 단계를 사용합니다.

 1. Azure Portal에서 **Azure Active Directory** 를 선택합니다.
 2. **Azure AD Connect** 를 선택합니다.
 3. **클라우드 동기화 관리** 를 선택합니다.
 4. **구성** 아래에서 구성을 선택합니다.
 5. **특성 관리** 에서 **매핑을 편집하려면 클릭** 을 선택합니다.
  ![특성 매핑 편집](media/how-to-map-usertype/usertype-1.png) 

 6. **특성 매핑 추가** 를 클릭합니다.
    ![새 특성 매핑 추가](media/how-to-map-usertype/usertype-2.png) 
7. 매핑 유형을 선택합니다. 다음 세 가지 방법 중 하나로 매핑을 수행할 수 있습니다.
 - 직접 매핑(AD 특성에서)
 - 식(IIF(InStr([userPrincipalName], "@partners") > 0,"Guest","Member"))
 - 상수(즉, 모든 사용자 개체를 게스트로 설정)
  ![usertype 추가](media/how-to-map-usertype/usertype-3.png) 
8. 대상 특성 드롭다운에서 UserType을 선택합니다.
9. 페이지 맨 아래에 있는 **적용** 단추를 클릭합니다. 그러면 Azure AD UserType 특성에 대한 매핑이 만들어집니다.

## <a name="next-steps"></a>다음 단계 

- [Azure Active Directory에서 특성 매핑에 대한 식 작성](reference-expressions.md)
- [클라우드 동기화 구성](how-to-configure.md)
