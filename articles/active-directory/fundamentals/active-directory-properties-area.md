---
title: 조직의 개인 정보 추가 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory 속성 영역에 조직의 개인 정보를 추가하는 방법에 대한 지침입니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.openlocfilehash: bfbb9a035c3f7a00de1610f1240448ff7b0b4abf
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452601"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Azure Active Directory를 사용하여 조직의 개인 정보 추가
이 아티클에서는 테넌트 관리자가 Azure Portal을 통해 조직의 Azure AD(Azure Active Directory) 테넌트에 관련 개인 정보를 추가할 수 있는 방법을 설명합니다.

내부 직원 및 외부 게스트가 정책을 검토할 수 있도록 전역 개인 정보 연락처 및 조직의 개인정보처리방침을 모두 추가하는 것이 좋습니다. 개인정보처리방침이 고유하게 생성되고 각 비즈니스에 맞게 조정되기 때문에 도움말은 변호사에게 문의하는 것이 좋습니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Azure AD에 개인 정보 추가
Azure AD의 **속성** 영역에 조직의 개인 정보를 추가합니다.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>속성 영역에 액세스하고 개인 정보를 추가하려면

1.  테넌트 관리자 권한으로 Azure Portal에 로그인합니다.

2.  왼쪽 탐색 모음에서 **Azure Active Directory**를 선택한 다음, **속성**을 선택합니다.

    **속성** 영역이 나타납니다.

    ![개인 정보 영역을 강조 표시하는 Azure AD 속성 영역](media/active-directory-properties-area/properties-area.png)

3.  직원의 개인 정보를 추가합니다.

    - **기술 담당자** 조직 내에서 기술 담당자에게 문의하기 위해 사용자의 이메일 주소를 입력합니다.
    
    - **전역 개인 정보 연락처** 개인 데이터 개인 정보에 대한 질문을 문의하기 위해 사용자의 이메일 주소를 입력합니다. 이 사용자는 데이터 위반이 발생하는 경우 Microsoft에서 연락할 사람이기도 합니다. 여기에 나열된 사용자가 없는 경우 Microsoft에서는 전역 관리자에게 연락합니다.

    - **개인정보처리방침 URL** 조직이 내부 및 외부 게스트의 데이터 개인 정보를 처리하는 방법을 모두 설명하는 조직의 문서에 대한 링크를 입력합니다.

        >[!Important]
        >고유한 개인정보취급방침 또는 개인 정보 연락처를 포함하지 않는 경우 외부 게스트는 **검토 권한** 상자에 **<_조직 이름_>에서는 검토할 해당 약관에 대한 링크를 제공하지 않습니다.** 라는 텍스트를 확인하게 됩니다. 예를 들어 게스트 사용자는 B2B 공동 작업을 통해 조직에 액세스하는 초대를 받을 때 이 메시지를 확인하게 됩니다.

        ![메시지를 포함한 B2B 공동 작업 검토 권한 상자](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계
- [Azure Active Directory B2B 공동 작업 초대 상환](https://aka.ms/b2bredemption)
- [Azure Active Directory에서 사용자에 대한 프로필 정보 추가 또는 변경](active-directory-users-profile-azure-portal.md)