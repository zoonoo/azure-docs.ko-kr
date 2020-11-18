---
title: Azure AD MFA 및 ADFS를 사용 하 여 리소스 보호-Azure Active Directory
description: 이 페이지는 클라우드에서 Azure AD MFA 및 AD FS를 시작 하는 방법을 설명 하는 Azure AD Multi-Factor Authentication 페이지입니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 999fb350aaa5f11510db0d4ecc036e188d76e20f
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839338"
---
# <a name="securing-cloud-resources-with-azure-ad-multi-factor-authentication-and-ad-fs"></a>Azure AD Multi-Factor Authentication 및 AD FS를 사용 하 여 클라우드 리소스 보안 유지

조직이 Azure Active Directory 페더레이션 된 경우 Azure AD Multi-Factor Authentication 또는 Active Directory Federation Services (AD FS)를 사용 하 여 Azure AD에서 액세스 하는 리소스를 보호 합니다. Azure AD Multi-Factor Authentication 또는 Active Directory Federation Services를 사용 하 여 Azure Active Directory 리소스를 보호 하려면 다음 절차를 따르십시오.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>AD FS를 사용하여 Azure AD 리소스 보안 유지

클라우드 리소스를 보호하려면 사용자가 두 단계 인증을 성공적으로 수행했을 때 Active Directory Federation Services가 multipleauthn 클레임을 내보내도록 클레임 규칙을 설정합니다. 이 클레임은 Azure AD에 전달됩니다. 다음 단계를 수행하려면 이 절차를 따르세요.

1. AD FS 관리를 엽니다.
2. 왼쪽에서 **신뢰 당사자 트러스트** 를 선택합니다.
3. **Microsoft Office 365 Id 플랫폼** 을 마우스 오른쪽 단추로 클릭 하 고 **클레임 규칙 편집** 을 선택 합니다.

   ![ADFS 콘솔-신뢰 당사자 트러스트](./media/howto-mfa-adfs/trustedip1.png)

4. 발급 변환 규칙에서 **규칙 추가** 를 클릭 합니다.

   ![발급 변환 규칙 편집](./media/howto-mfa-adfs/trustedip2.png)

5. 변환 클레임 규칙 추가 마법사의 드롭다운 목록에서 **들어오는 클레임 통과 또는 필터링** 을 선택하고 **다음** 을 클릭합니다.

   ![클레임 규칙 템플릿을 선택할 수 있는 변환 클레임 규칙 추가 마법사가 스크린샷에 표시 됩니다.](./media/howto-mfa-adfs/trustedip3.png)

6. 규칙의 이름을 지정합니다. 
7. 들어오는 클레임 유형으로 **인증 방법 참조** 를 선택합니다.
8. **모든 클레임 값 통과** 를 선택합니다.
    ![모든 클레임 값 통과를 선택 하는 스크린샷에 변환 클레임 규칙 추가 마법사가 표시 됩니다.](./media/howto-mfa-adfs/configurewizard.png)
9. **Finish** 를 클릭합니다. AD FS 관리 콘솔을 닫습니다.

## <a name="trusted-ips-for-federated-users"></a>페더레이션 사용자를 위한 신뢰할 수 있는 IP

신뢰할 수 있는 IP를 사용하면 관리자가 특정 IP 주소 또는 자신의 인트라넷 내에서 시작된 요청을 가진 페더레이션 사용자에 대한 2단계 확인을 바이패스할 수 있습니다. 다음 섹션에서는 페더레이션된 사용자 인트라넷에서 요청이 발생 하는 경우 페더레이션 사용자로 Azure AD Multi-Factor Authentication 신뢰할 수 있는 Ip를 구성 하 고 2 단계 인증을 통과 하는 방법을 설명 합니다. 이 작업은 들어오는 클레임(회사 네트워크 내부 클레임 형식 사용) 통과 또는 필터링 템플릿을 사용하도록 AD FS를 구성하여 수행합니다.

이 예제에서는 신뢰 당사자 트러스트에 대 한 Microsoft 365를 사용 합니다.

### <a name="configure-the-ad-fs-claims-rules"></a>AD FS 클레임 규칙 구성

가장 먼저 AD FS 클레임을 구성합니다. 두 개의 클레임 규칙을 만듭니다. 하나는 회사 네트워크 내부 클레임 형식에 대한 규칙이고 다른 하나는 사용자의 로그인 상태를 유지하기 위한 규칙입니다.

1. AD FS 관리를 엽니다.
2. 왼쪽에서 **신뢰 당사자 트러스트** 를 선택합니다.
3. **Microsoft Office 365 Id 플랫폼** 을 마우스 오른쪽 단추로 클릭 하 고 **클레임 규칙 편집 ...** 
    ![ 을 선택 합니다. ADFS 콘솔-클레임 규칙 편집](./media/howto-mfa-adfs/trustedip1.png)
4. 발급 변환 규칙에서 **규칙 추가를 클릭 합니다.** 
    ![ 클레임 규칙 추가](./media/howto-mfa-adfs/trustedip2.png)
5. 변환 클레임 규칙 추가 마법사의 드롭다운 목록에서 **들어오는 클레임 통과 또는 필터링** 을 선택하고 **다음** 을 클릭합니다.
   ![들어오는 클레임 통과 또는 필터링을 선택 하는 스크린샷 변환 클레임 규칙 추가 마법사를 보여 줍니다.](./media/howto-mfa-adfs/trustedip3.png)
6. 클레임 규칙 이름 옆에 있는 상자에 규칙의 이름을 지정합니다. 예를 들어 InsideCorpNet입니다.
7. 들어오는 클레임 형식 옆의 드롭다운 목록에서 **회사 네트워크 내부** 를 선택합니다.
   ![회사 네트워크 클레임 내부 추가](./media/howto-mfa-adfs/trustedip4.png)
8. **Finish** 를 클릭합니다.
9. 발급 변환 규칙에서 **규칙 추가** 를 클릭 합니다.
10. 변환 클레임 규칙 추가 마법사의 드롭다운 목록에서 **사용자 지정 규칙을 사용하여 클레임 보내기** 를 선택하고 **다음** 을 클릭합니다.
11. 클레임 규칙 이름 아래에 있는 상자에 *로그인한 사용자 유지* 를 입력합니다.
12. 사용자 지정 규칙 상자에서 다음을 입력합니다.

```ad-fs-claim-rule
        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Create custom claim to keep users signed in](./media/howto-mfa-adfs/trustedip5.png)
```

13. **Finish** 를 클릭합니다.
14. **적용** 을 클릭합니다.
15. **Ok** 를 클릭합니다.
16. AD FS 관리를 닫습니다.

### <a name="configure-azure-ad-multi-factor-authentication-trusted-ips-with-federated-users"></a>페더레이션된 사용자를 사용 하 여 Azure AD Multi-Factor Authentication 신뢰할 수 있는 Ip 구성

이제 클레임이 적용되었으므로 신뢰할 수 있는 IP를 구성할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory**  >  **보안**  >  **조건부 액세스**  >  **명명 된 위치** 를 선택 합니다.
3. **조건부 액세스-명명 된 위치** 블레이드에서 **MFA 신뢰할 수 있는 ip 구성** 을 선택 합니다.

   ![Azure AD 조건부 액세스 명명 된 위치 MFA 신뢰할 수 있는 Ip 구성](./media/howto-mfa-adfs/trustedip6.png)

4. [서비스 설정] 페이지의 **신뢰할 수 있는 IP** 에서 **인트라넷의 페더레이션 사용자로부터 발생한 요청인 경우 다단계 인증 건너뛰기** 를 선택합니다.  
5. **저장** 을 클릭합니다.

이것으로 끝입니다. 이 시점에서 페더레이션 Microsoft 365 사용자는 클레임이 회사 인트라넷 외부에서 시작 된 경우에만 MFA를 사용 해야 합니다.
