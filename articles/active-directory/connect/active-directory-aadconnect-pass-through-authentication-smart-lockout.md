---
title: "Azure AD Connect: 통과 인증 - 스마트 잠금 | Microsoft Docs"
description: "이 문서에서는 Azure AD(Active Directory) 통과 인증이 클라우드에서의 무차별 암호 대입 공격으로부터 온-프레미스 계정을 보호하는 방법에 대해 설명합니다."
services: active-directory
keywords: "Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 2cbef09aa7bfcd77160b47368bf3d8747dc22de3
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Azure Active Directory 통과 인증: 스마트 잠금

## <a name="overview"></a>개요

Azure AD는 무차별 암호 대입 공격으로부터 보호하고 실제 사용자가 Office 365 및 SaaS 응용 프로그램에서 잠기지 않게 차단합니다. 이 기능을 **스마트 잠금**이라고 하며 로그인 방법으로 통과 인증을 사용할 때 지원됩니다. 스마트 잠금은 기본적으로 모든 테넌트에 대해 사용하며 항상 사용자 계정을 보호하므로 사용자가 실행할 필요가 없습니다.

스마트 잠금은 실패한 모든 로그인 시도를 추적하며 특정 **잠금 임계값** 이후 **잠금 기간**을 시작하는 방식으로 작동합니다. 잠금 기간 동안 공격자의 모든 로그인 시도가 거부됩니다. 공격이 지속될 경우 잠근 기간이 종료된 후에 실패한 로그인 시도로 인해 잠금 기간이 더 길어집니다.

>[!NOTE]
>기본 잠금 임계값은 10회 실패이며 기본 잠금 기간은 60초입니다.

스마트 잠금은 실제 사용자의 로그인과 공격자의 로그인을 구분하며 대부분의 경우 공격자만 잠급니다. 이 기능은 공격자가 악의적으로 실제 사용자를 잠그지 못하게 차단합니다. 과거 로그인 행위, 사용자 장치 및 브라우저, 기타 신호를 사용하여 실제 사용자와 공격자를 구분합니다. 이 알고리즘은 지속적으로 개선 중입니다.

통과 인증은 암호 유효성 검사 요청을 온-프레미스 AD(Active Directory)로 전달하므로 공격자가 사용자의 AD 계정을 잠그지 못하게 차단해야 합니다. 자체 AD 계정 잠금 정책([**계정 잠금 임계값**](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) 및 [**일정 시간 이후 계정 잠금 재설정 카운터 정책**](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx))이 있으므로 Azure AD의 잠금 임계값과 잠금 기간 값을 적합하게 구성하여 공격자가 온-프레미스 AD에 도달하기 전에 클라우드의 공격을 필터링해야 합니다.

>[!NOTE]
>스마트 잠금 기능 자체는 무료이나 그래픽 API를 사용하여 Azure AD의 잠금 임계값 및 잠금 기간을 수정하는 것은 Azure AD Premium P2 기능입니다.  또한 사용자가 테넌트에서 전역 관리자여야 합니다.

사용자의 온-프레미스 AD 계정을 제대로 보호하기 위해 다음을 확인해야 합니다.

1.  Azure AD의 잠금 임계값은 AD의 계정 잠금 임계값보다 _작아야_ 합니다. AD의 계정 잠금 임계값은 Azure AD의 잠금 임계값보다 2-3배 이상은 크게 설정하는 것이 좋습니다.
2.  Azure AD의 잠금 기간(초 단위)이 AD의 다음 시간 이후 계정 잠금 재설정(분 단위)보다 _길어야_ 합니다.

## <a name="verify-your-ad-account-lockout-policies"></a>AD 계정 잠금 정책 확인

다음 지침을 사용하여 AD 계정 잠금 정책을 확인합니다.

1.  그룹 정책 관리 도구를 엽니다.
2.  모든 사용자에게 적용된 그룹 정책(예: 기본 도메인 정책)을 편집합니다.
3.  Computer Configuration\Policies\Windows Settings\Security Settings\Account Policies\Account Lockout Policy로 이동합니다.
4.  계정 잠금 임계값 및 다음 이후 계정 잠금 카운터 재설정 값을 확인합니다.

![AD 계정 잠금 정책](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values"></a>Graph API를 사용하여 테넌트의 스마트 잠금 값 관리

>[!IMPORTANT]
>그래픽 API를 사용하여 Azure AD의 잠금 임계값 및 잠금 기간을 수정하는 것은 Azure AD Premium P2 기능입니다.  또한 사용자가 테넌트에서 전역 관리자여야 합니다.

[Graph 탐색기](https://developer.microsoft.com/graph/graph-explorer)를 사용하여 Azure AD의 스마트 잠금 값을 읽고, 설정하고, 업데이트할 수 있습니다. 하지만 이러한 작업을 프로그래밍 방식으로 수행할 수도 있습니다.

### <a name="read-smart-lockout-values"></a>스마트 잠금 값 읽기

다음 단계에 따라 테넌트의 스마트 잠금 값을 읽습니다.

1. Graph 탐색기에 테넌트의 전역 관리자로 로그인합니다. 메시지가 표시되면 요청된 권한에 대해 액세스를 부여합니다.
2. "권한 수정"을 클릭하고 "Directory.ReadWrite.All" 권한을 선택합니다.
3. Graph API 요청을 다음과 같이 구성합니다. 버전을 "BETA"로, 요청 유형을 "GET"으로, URL을 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`로 설정합니다.
4. "쿼리 실행"을 클릭하여 테넌트의 스마트 잠금 값을 확인합니다. 앞서 테넌트의 값을 설정하지 않은 경우 빈 집합이 표시됩니다.

### <a name="set-smart-lockout-values"></a>스마트 잠금 값 설정

다음 단계에 따라 테넌트의 스마트 잠금 값을 설정합니다(최초에만).

1. Graph 탐색기에 테넌트의 전역 관리자로 로그인합니다. 메시지가 표시되면 요청된 권한에 대해 액세스를 부여합니다.
2. "권한 수정"을 클릭하고 "Directory.ReadWrite.All" 권한을 선택합니다.
3. Graph API 요청을 다음과 같이 구성합니다. 버전을 "BETA"로, 요청 유형을 "POST"로, URL을 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`로 설정합니다.
4. 다음 JSON 요청을 복사하여 "요청 본문" 필드에 붙여 넣습니다. 스마트 잠금 값을 적절하게 변경하고 `templateId`에 대해 임의의 GUID를 사용합니다.
5. "쿼리 실행"을 클릭하여 테넌트의 스마트 잠금 값을 설정합니다.

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>이들을 사용하지 않는 경우 **BannedPasswordList** 및**EnableBannedPasswordCheck** 값이 각각 공백("") 및 "false"가 됩니다.

[이 단계](#read-smart-lockout-values)를 사용하여 테넌트의 스마트 잠금 값을 올바르게 설정했는지 확인합니다.

### <a name="update-smart-lockout-values"></a>스마트 잠금 값 업데이트

이 단계에 따라 테넌트의 스마트 잠금 값을 업데이트합니다(앞서 설정한 경우).

1. Graph 탐색기에 테넌트의 전역 관리자로 로그인합니다. 메시지가 표시되면 요청된 권한에 대해 액세스를 부여합니다.
2. "권한 수정"을 클릭하고 "Directory.ReadWrite.All" 권한을 선택합니다.
3. [다음 단계에 따라 테넌트의 스마트 잠금 값을 읽습니다](#read-smart-lockout-values). "displayName"이 "PasswordRuleSettings"인 항목의 `id` 값(GUID)을 복사합니다.
4. Graph API 요청을 다음과 같이 구성합니다. 버전을 "BETA"로, 요청 유형을 "PATCH"로, URL을 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`로 설정합니다. `<id>`에는 3단계의 GUID를 사용합니다.
5. 다음 JSON 요청을 복사하여 "요청 본문" 필드에 붙여 넣습니다. 스마트 잠금 값을 적절하게 변경합니다.
6. "쿼리 실행"을 클릭하여 테넌트의 스마트 잠금 값을 업데이트합니다.

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

[이 단계](#read-smart-lockout-values)를 사용하여 테넌트의 스마트 잠금 값을 올바르게 업데이트했는지 확인합니다.

## <a name="next-steps"></a>다음 단계
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 새로운 기능 요청을 제출합니다.

