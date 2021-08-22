---
title: Azure AD Connect Health 명령 데이터 검색 | Microsoft Docs
description: 이 페이지에서는 Azure AD Connect Health에서 데이터를 검색하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 435cc73d9c2a91a9c72bf07258a15dced581d580
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113110064"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>데이터 검색을 위한 Azure AD Connect Health 명령

이 문서에서는 Azure AD Connect를 사용하여 Azure AD Connect Health에서 데이터를 검색하는 방법을 설명합니다.

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>상태 경고에 대해 구성된 사용자의 모든 이메일 주소를 검색합니다.

경고를 수신하도록 Azure AD Connect Health에서 구성된 모든 사용자의 이메일 주소를 검색하려면 다음 단계를 사용하세요.

1.  Azure Active Directory Connect 상태 블레이드에서 시작하고 왼쪽 탐색 모음에서 **동기화 서비스** 를 선택합니다.
 ![동기화 서비스](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  **경고** 타일을 클릭합니다.</br>
 ![경고](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  **알림 설정** 을 클릭합니다.
 ![알림](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  **알림 설정** 블레이드에서 상태 경고 알림의 수신자로 설정된 이메일 주소 목록을 찾을 수 있습니다.
 ![전자 메일](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>AD FS 잘못된 암호 시도로 플래그가 지정된 계정 검색

AD FS 잘못된 암호 시도로 플래그가 지정된 계정을 검색하려면 다음 단계를 수행합니다.

1.  Azure Active Directory Health 블레이드에서 시작해서 **동기화 오류** 를 선택합니다.
 ![동기화 오류](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  **동기화 오류** 블레이드에서 **내보내기** 를 클릭합니다. 이렇게 하면 기록된 동기화 오류 목록을 내보냅니다.
 ![내보내기](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>다음 단계
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health Agent 설치](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health 작업](how-to-connect-health-operations.md)
* [Azure AD Connect Health FAQ](reference-connect-health-faq.yml)
* [Azure AD Connect Health 버전 내역](reference-connect-health-version-history.md)