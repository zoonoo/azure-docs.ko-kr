---
title: Azure AD 커넥터 계정 암호 변경 | Microsoft Docs
description: 이 항목에서는 Azure AD 커넥터 계정 복원 하는 방법을 설명 합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d68c190b51b9bbb5faf21e8ea75b07d1a82005e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64571409"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Azure AD Connect 계정 암호 변경
Azure AD 커넥터 계정 서비스 체험 간주 됩니다. 자격 증명을 재설정해야 할 경우 이 항목을 참조하세요. 예를 들어, 실수로 전역 관리자가 PowerShell을 사용 하 여 계정의 암호를 다시 설정 합니다.

## <a name="reset-the-credentials"></a>자격 증명 다시 설정
Azure AD 커넥터 계정 인증 문제로 인해 Azure AD에 연결할 수 없는 경우에 암호를 재설정할 수 있습니다.

1. Azure AD Connect 동기화 서버에 로그인하고 PowerShell을 시작합니다.
2. `Add-ADSyncAADServiceAccount`.  
   ![PowerShell cmdlet addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Azure AD 전역 관리자 자격 증명을 제공합니다.

이 cmdlet은 서비스 계정의 암호를 재설정하고 Azure AD와 동기화 엔진에서 암호를 업데이트합니다.

## <a name="known-issues-these-steps-can-solve"></a>이 단계에서 해결할 수 있다고 알려진 문제
이 섹션은 Azure AD 커넥터 계정에서 다시 설정 하는 자격 증명으로 해결 된 고객이 보고 한 오류 목록입니다.

- - -
이벤트 6900  
서버가 암호 변경 알림을 처리하는 동안 예기치 않은 오류가 발생했습니다.  
AADSTS70002: 자격 증명의 유효성 검사 오류. AADSTS50054: 이전 암호가 인증에 사용되었습니다.

- - -
이벤트 659  
암호 정책 동기화 구성을 검색하는 도중 오류 발생. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: 자격 증명의 유효성 검사 오류. AADSTS50054: 이전 암호가 인증에 사용되었습니다.

## <a name="next-steps"></a>다음 단계
**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](how-to-connect-sync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)

