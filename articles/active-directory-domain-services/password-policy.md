---
title: 'Azure Active Directory Domain Services: 암호 정책 | Microsoft Docs'
description: 관리되는 도메인의 암호 정책에 대해 살펴봅니다.
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: 71511fd83f9c00f768f5f7bedb3516fef8599e70
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246841"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>관리되는 도메인의 암호 및 계정 잠금 정책
이 문서에서는 관리되는 도메인의 기본 암호 정책과 이러한 정책을 구성할 수 있는 방법을 설명합니다.

## <a name="fine-grained-password-policies-fgpp"></a>FGPP(세분화된 암호 정책)
단일 도메인 내에서 여러 암호 정책을 지정하려면 세분화된 암호 정책을 사용합니다. FGPP를 사용하면 도메인의 각 사용자 집합에 대해 암호 및 계정 잠금 정책에 서로 다른 제한을 적용할 수 있습니다. 예를 들어 권한 있는 계정에 엄격한 암호 설정을 적용할 수 있습니다.

FGPP를 사용하여 구성할 수 있는 암호 설정은 다음과 같습니다.
* 최소 암호 길이
* 암호 기록
* 암호는 복잡성을 만족해야 함
* 최소 암호 사용 기간
* 최대 암호 사용 기간
* 계정 잠금 정책
    * 계정 잠금 기간
    * 허용되는 로그온 시도 실패 횟수
    * 다음 시간 이후 로그온 시도 실패 횟수 다시 설정


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>관리되는 도메인의 기본 세분화된 암호 정책 설정
아래 스크린샷에는 Azure AD Domain Services 관리되는 도메인에 구성되어 있는 기본 세분화된 암호 정책이 나와 있습니다.

![기본 세분화된 암호 정책](./media/how-to/default-fgpp.png)

> [!NOTE]
> 기본 제공 세분화된 암호 정책은 수정하거나 삭제할 수 없습니다. 'AAD DC 관리자' 그룹 구성원은 사용자 지정 FGPP를 만들고 기본 제공 FGPP를 재정의하도록(사용자 지정 FGPP가 우선적으로 적용됨) 구성할 수 있습니다.
>
>

## <a name="password-policy-settings"></a>암호 정책 설정
관리되는 도메인에서 기본적으로 구성되는 암호 정책은 다음과 같습니다.
* 최소 암호 길이(문자): 7
* 최대 암호 사용 기간(수명): 90일
* 암호는 복잡성을 만족해야 함

### <a name="account-lockout-settings"></a>계정 잠금 설정
관리되는 도메인에서 기본적으로 구성되는 계정 잠금 정책은 다음과 같습니다.
* 계정 잠금 기간: 30
* 허용되는 로그온 시도 실패 횟수: 5
* 다음 시간 이후 로그온 시도 실패 횟수 다시 설정: 30분

실제로 2분 내에 잘못된 암호를 5번 사용하면 사용자 계정이 30분 동안 잠깁니다. 계정은 30분 후 자동으로 잠금 해제됩니다.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>관리되는 도메인에서 사용자 지정 FGPP(세분화된 암호 정책) 만들기
사용자 지정 FGPP를 만들어 관리되는 도메인의 특정 그룹에 적용할 수 있습니다. 이 구성은 관리되는 도메인용으로 구성된 기본 FGPP를 실제로 재정의합니다.

> [!TIP]
> **'AAD DC 관리자'** 그룹의 구성원에게만 사용자 지정 세분화된 암호 정책을 만들 권한이 있습니다.
>
>

또한 사용자 지정 세분화된 암호 정책을 만들어서 관리되는 도메인에 생성하는 사용자 지정 OU에 적용할 수도 있습니다.

사용자 지정 FGPP를 구성할 수 있는 이유는 다음과 같습니다.
* 다른 계정 잠금 정책을 설정하려는 경우
* 관리되는 도메인의 기본 암호 수명 설정을 구성하려는 경우

관리되는 도메인에서 사용자 지정 FGPP를 만들려면 다음 단계를 수행합니다.
1. 관리되는 도메인을 관리하는 데 사용하는 Windows VM에 로그인합니다. 계정이 없는, 지침에 따라 [는 Azure AD Domain Services 도메인 관리](manage-domain.md)합니다.
2. VM에서 **Active Directory 관리 센터**를 시작합니다.
3. 'contoso100.com' 등의 도메인 이름을 클릭합니다.
4. **시스템**을 두 번 클릭하여 시스템 컨테이너를 엽니다.
5. **암호 설정 컨테이너**를 두 번 클릭합니다.
6. 관리되는 도메인 **AADDSSTFPSO**의 기본 제공 FGPP가 표시됩니다. 이 기본 제공 FGPP는 수정할 수 없습니다. 그러나 새 사용자 지정 FGPP를 만들어 기본 FGPP를 재정의할 수는 있습니다.
7. 오른쪽의 **작업** 패널에서 **새로 만들기**를 클릭한 다음 **암호 설정**을 클릭합니다.
8. **암호 설정 만들기** 대화 상자에서 사용자 지정 FGPP의 일부로 적용할 사용자 지정 암호 설정을 지정합니다. 기본 FGPP가 재정의되도록 우선 순위를 적절하게 설정해야 합니다.

   ![사용자 지정 FGPP 만들기](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **실수로 삭제되지 않도록 보호 옵션의 선택을 취소해야 합니다.** 이 옵션을 선택하는 경우에는 FGPP를 저장할 수 없습니다.
   >
   >

9. **직접 적용 대상**에서 **추가** 단추를 클릭합니다. **사용자 또는 그룹 선택** 대화 상자에서 **위치** 단추를 클릭합니다.

   ![사용자 및 그룹 선택](./media/how-to/fgpp-applies-to.png)

10. **위치** 대화 상자에서 도메인 이름을 확장하고 **AADDC 사용자**를 클릭합니다. 이제 기본 제공 사용자 OU에서 FGPP를 적용할 그룹을 선택할 수 있습니다.

    ![그룹이 속하는 OU 선택](./media/how-to/fgpp-container.png)

11. 그룹의 이름을 입력하고 **이름 확인** 단추를 클릭하여 그룹 유무의 유효성을 검사합니다.

    ![FGPP를 적용할 그룹 선택](./media/how-to/fgpp-apply-group.png)

12. 그룹 이름이 **직접 적용 대상** 섹션에 표시됩니다. **확인** 단추를 클릭하여 이러한 변경 내용을 저장합니다.

    ![적용된 FGPP](./media/how-to/fgpp-applied.png)

> [!TIP]
> **사용자 지정 OU의 사용자 계정에 사용자 지정 암호 정책을 적용하려면:** 세밀하게 세분화된 암호 정책은 그룹에만 적용할 수 있습니다. 사용자 지정 OU의 사용자만을 대상으로 사용자 지정 암호 정책을 구성하려면 해당 OU의 사용자가 포함된 그룹을 만드세요.
>
>

## <a name="next-steps"></a>다음 단계
* [Active Directory 세분화된 암호 정책에 대해 알아보기](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [AD 관리 센터를 사용하여 세분화된 암호 정책 구성](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)
