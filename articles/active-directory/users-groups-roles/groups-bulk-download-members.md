---
title: 대량 다운로드 그룹 구성원 목록 - Azure Active Directory 포털 | 마이크로 소프트 문서
description: Azure 관리 센터에서 사용자를 일괄으로 추가합니다.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e29aacb1357509e2b000a9d05c5ced8f9a30dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517167"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Azure Active Directory에서 그룹의 일괄 다운로드 멤버(미리 보기)

Azure Active Directory(Azure AD) 포털을 사용하여 조직의 그룹 구성원을 CSV(쉼표로 구분된 값) 파일에 대량 다운로드할 수 있습니다.

## <a name="to-bulk-download-group-membership"></a>그룹 멤버십을 일괄 다운로드하려면

1. 조직의 사용자 관리자 계정으로 [Azure 포털에](https://portal.azure.com) 로그인합니다. 그룹 소유자는 소유한 그룹의 구성원을 일괄 다운로드할 수도 있습니다.
1. Azure AD에서 **Groups** > **모든 그룹**그룹을 선택합니다.
1. 다운로드할 멤버를 가진 그룹을 연 다음 **구성원을**선택합니다.
1. **구성원** 페이지에서 구성원 **다운로드를** 선택하여 그룹 구성원을 나열하는 CSV 파일을 다운로드합니다.

   ![구성원 다운로드 명령이 그룹의 프로필 페이지에 있습니다.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>다운로드 상태 확인

**대량 작업 결과(미리 보기)** 페이지에서 보류 중인 모든 대량 요청의 상태를 확인할 수 있습니다.

   ![대량 작업 결과 페이지에 대량 요청 상태가 표시 됩니다.](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>대량 다운로드 서비스 제한

그룹 구성원 목록을 다운로드하는 각 대량 활동은 최대 1시간 동안 실행할 수 있습니다. 이렇게 하면 500,000명 이상의 회원 목록을 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [대량 가져오기 그룹 구성원](groups-bulk-import-members.md)
- [그룹 구성원 을 일괄 제거](groups-bulk-download-members.md)
