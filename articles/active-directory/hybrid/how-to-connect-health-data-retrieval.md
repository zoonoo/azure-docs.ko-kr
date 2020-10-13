---
title: Azure AD Connect Health 명령 데이터 검색 | Microsoft Docs
description: 이 페이지에서는 Azure AD Connect Health에서 데이터를 검색 하는 방법을 설명 합니다.
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
ms.openlocfilehash: 8d147d2c094923e971e52e1dbfe3f7a19776d38c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463654"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>데이터 검색에 대 한 Azure AD Connect Health 지침

이 문서에서는 Azure AD Connect를 사용 하 여 Azure AD Connect Health에서 데이터를 검색 하는 방법을 설명 합니다.

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>상태 경고에 대해 구성 된 사용자에 대 한 모든 전자 메일 주소를 검색 합니다.

Azure AD Connect Health에서 구성 된 모든 사용자에 대 한 전자 메일 주소를 검색 하 여 경고를 받도록 하려면 다음 단계를 사용 합니다.

1.  Azure Active Directory Connect health 블레이드에서 시작 하 고 왼쪽 탐색 모음에서 **동기화 서비스** 를 선택 합니다.
 ![동기화 서비스](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  **경고** 타일을 클릭 합니다.</br>
 ![경고](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  **알림 설정**을 클릭 합니다.
 ![알림](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  **알림 설정** 블레이드에서 상태 경고 알림을 받는 사람으로 설정 된 전자 메일 주소 목록을 찾을 수 있습니다.
 ![전자](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>잘못 된 암호 시도 AD FS 플래그가 지정 된 계정을 검색 합니다.

잘못 된 암호 시도 AD FS 플래그가 지정 된 계정을 검색 하려면 다음 단계를 사용 합니다.

1.  Azure Active Directory 상태 블레이드에서 시작 하 여 **동기화 오류**를 선택 합니다.
 ![동기화 오류](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  **동기화 오류** 블레이드에서 **내보내기**를 클릭 합니다. 이렇게 하면 기록 된 동기화 오류 목록이 내보냅니다.
 ![내보내기](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>다음 단계
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health Agent 설치](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health 작업](how-to-connect-health-operations.md)
* [Azure AD Connect Health FAQ](reference-connect-health-faq.md)
* [Azure AD Connect Health 버전 기록](reference-connect-health-version-history.md)