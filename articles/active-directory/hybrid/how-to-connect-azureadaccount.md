---
title: Azure AD Connect 계정 암호 변경 | Microsoft Docs
description: 이 항목은 Azure AD Connector 계정을 복원하는 방법을 설명합니다.
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
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 5a16e571478428ef1eb068236369b6e9af9f19e1
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111853697"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Azure AD Connect 계정 암호 변경
Azure AD Connector 계정은 무료로 제공됩니다. 자격 증명을 재설정해야 할 경우 이 항목을 참조하세요. 예를 들어 전역 관리자가 PowerShell을 사용하여 실수로 계정의 암호를 재설정한 경우입니다.

## <a name="reset-the-credentials"></a>자격 증명 다시 설정
인증 문제로 인해 Azure AD Connector 계정으로 Azure AD에 연결할 수 없다면 암호를 재설정할 수 있습니다.

1. Azure AD Connect 동기화 서버에 로그인하고 PowerShell을 엽니다.
2. Azure AD 전역 관리자 자격 증명을 제공하려면 `$credential = Get-Credential`을 실행합니다.
3. `Add-ADSyncAADServiceAccount -AADCredential $credential` cmdlet을 실행합니다.

   cmdlet이 성공하면 PowerShell 명령 프롬프트가 나타납니다. 
   
이 cmdlet은 서비스 계정의 암호를 재설정하고 Azure AD와 동기화 엔진에서 암호를 업데이트합니다.

## <a name="known-issues-these-steps-can-solve"></a>이 단계에서 해결할 수 있다고 알려진 문제
이 섹션은 Azure AD Connector 계정에서 자격 증명을 다시 설정하여 오류를 수정한 고객이 보고한 오류 목록입니다.

---
이벤트 6900 암호 변경 알림을 처리하는 동안 서버에서 오류가 발생했습니다. AADSTS70002: 자격 증명의 유효성 검사 중 발생한 오류입니다. AADSTS70002: 자격 증명의 유효성 검사 오류 AADSTS50054: 이전 암호가 인증에 사용되었습니다.

---
이벤트 659 암호 정책 동기화 구성을 검색하는 중 발생한 오류입니다. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: 자격 증명의 유효성 검사 중 발생한 오류입니다. AADSTS70002: 자격 증명의 유효성 검사 오류 AADSTS50054: 이전 암호가 인증에 사용되었습니다.

## <a name="next-steps"></a>다음 단계
**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](how-to-connect-sync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)
