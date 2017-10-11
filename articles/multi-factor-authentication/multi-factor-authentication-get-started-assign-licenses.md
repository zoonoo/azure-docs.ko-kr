---
title: "Azure MFA의 라이선스 할당 | Microsoft Docs"
description: "Microsoft Azure Multi-Factor Authentication의 라이선스를 할당하는 방법에 대해 알아봅니다."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 514ef423-8ee6-4987-8a4e-80d5dc394cf9
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/13/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ROBOTS: NOINDEX
ms.openlocfilehash: 45522bf526c4aeab1d6ccc8891a55a0436ff9320
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>사용자에게 Azure MFA, Azure AD Premium 또는 Enterprise Mobilitiy Suite 라이선스 할당
Azure MFA, Azure AD Premium 또는 Enterprise Mobility Suite 라이선스가 있으면 Multi-Factor Auth 공급자를 만들 필요가 없습니다. 사용자에게 라이선스를 할당하면 MFA에 사용하기 시작할 수 있습니다.

## <a name="to-assign-a-license"></a>라이선스를 할당하려면
1. 관리자 권한으로 [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. Active Directory 페이지에서 사용하도록 설정하려는 사용자가 있는 디렉토리를 두 번 클릭합니다.
4. 디렉토리 페이지의 맨 위에서 **라이센스**를 선택합니다.
   ![라이선스 할당](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. 라이선스 페이지에서 **Azure Multi-Factor Authentication**, **Active Directory Premium** 또는 **엔터프라이즈 이동성 제품군**을 선택합니다.  하나만 있는 경우에는 자동으로 선택됩니다.
6. 페이지 맨 아래에서 **할당**을 클릭합니다.
   ![라이선스 할당](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
7. 표시되는 상자에서 라이선스를 할당하려는 사용자 또는 그룹 옆을 클릭합니다.  녹색 확인 표시가 나타납니다.
8. 확인 아이콘을 클릭하여 변경 내용을 저장합니다.
   ![라이선스 할당](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
9. 할당된 라이선스 수와 실패한 수를 알려주는 메시지가 표시됩니다.  **확인**을 클릭합니다.
   ![라이선스 할당](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Microsoft Azure Active Directory 라이선스란?](../active-directory/active-directory-licensing-what-is.md)을 참조하세요.
