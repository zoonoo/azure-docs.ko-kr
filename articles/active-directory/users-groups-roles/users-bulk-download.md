---
title: Azure Active Directory 포털에서 사용자 목록 다운로드 | 마이크로 소프트 문서
description: Azure Active Directory의 Azure 관리 센터에서 사용자 레코드를 대량으로 다운로드합니다.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b35163387ed4ce71f7a2019835a1d9fdbff3051
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532652"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Azure Active Directory 포털의 사용자 목록 다운로드

Azure Active Directory(Azure AD)는 대량 사용자 가져오기(만들기) 작업을 지원합니다.

## <a name="required-permissions"></a>필요한 사용 권한

Azure AD 관리자 센터에서 사용자 목록을 다운로드하려면 Azure AD에서 하나 이상의 조직 수준 관리자 역할에 할당된 사용자와 함께 로그인해야 합니다(사용자 관리자는 필요한 최소 역할임). 게스트 초대자 및 응용 프로그램 개발자는 관리자 역할로 간주되지 않습니다.

## <a name="to-download-a-list-of-users"></a>사용자 목록을 다운로드하려면

1. 조직의 사용자 관리자 계정을 사용하여 [Azure AD 조직에 로그인합니다.](https://aad.portal.azure.com)
2. Azure Active 디렉터리 > 사용자로 이동합니다. 그런 다음 각 사용자 옆에 있는 왼쪽 열에 있는 상자를 선택하여 다운로드에 포함할 사용자를 선택합니다. 참고: 현재로서는 내보낼 모든 사용자를 선택할 수 있는 방법이 없습니다. 각 항목이 개별적으로 선택되어야 합니다.
3. Azure AD에서 **사용자** > **다운로드 사용자를**선택합니다.
4. 사용자 **다운로드** 페이지에서 CSV 파일 목록 사용자 프로필 속성을 수신하려면 **시작을** 선택합니다. 오류가 있는 경우 대량 작업 결과 페이지에서 결과 파일을 다운로드하여 볼 수 있습니다. 파일에각 오류에 대한 이유가 포함되어 있습니다.

   ![다운로드할 사용자 목록을 선택할 위치](./media/users-bulk-download/bulk-download.png)

   다운로드 파일에는 필터링된 사용자 목록이 포함됩니다.

   다음 사용자 특성이 포함됩니다.

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - mobile
   - 인증전화번호
   - 인증대체 전화 번호
   - 인증이메일
   - 대체이메일주소
   - 연령대
   - 동의제공포마이너
   - 법적연령그룹분류

## <a name="check-status"></a>상태 확인

**대량 작업 결과** 페이지에서 보류 중인 대량 요청의 상태를 확인할 수 있습니다.

[![](media/users-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>대량 다운로드 서비스 제한

사용자 목록을 만드는 각 대량 활동은 최대 1시간 동안 실행할 수 있습니다. 이를 통해 최소 500,000명의 사용자 목록을 만들고 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [사용자 일괄 추가](users-bulk-add.md)
- [대량 사용자 삭제](users-bulk-delete.md)
- [대량 복원 사용자](users-bulk-restore.md)
