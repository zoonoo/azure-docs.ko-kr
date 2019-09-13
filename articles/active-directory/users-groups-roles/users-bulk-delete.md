---
title: Azure Active Directory 포털에서 사용자 대량 삭제 (미리 보기) | Microsoft Docs
description: Azure Active Directory의 Azure 관리 센터에서 대량으로 사용자를 삭제 합니다.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c2204c572ca1f74f8060d0b6176df69359fe69a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901712"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Azure Active Directory에서 사용자 대량 삭제 (미리 보기)

Azure AD (Azure Active Directory)는 대량 사용자 만들기 및 삭제 작업, 게스트에 대 한 대량 초대, 사용자, 그룹 및 그룹 구성원 목록 다운로드를 지원 합니다.

## <a name="to-bulk-delete-users"></a>사용자를 대량으로 삭제 하려면

1. 조직의 사용자 관리자 계정으로 Azure AD 조직에 로그인 합니다.
1. Azure AD에서 **사용자** > **대량 삭제**를 선택 합니다.
1. **사용자 대량 삭제** 페이지에서 **다운로드** 를 선택 하 여 사용자 속성의 올바른 CSV 파일을 받은 다음 삭제할 사용자를 추가 합니다.
1. CSV 파일 편집을 완료 하거나 사용자가 업로드할 준비가 된 사용자가 있는 경우 유효성을 검사할 **csv 파일 업로드** 아래에서 파일을 선택 합니다.

   ![삭제 하려는 사용자를 나열 하는 로컬 CSV 파일을 선택 합니다.](./media/users-bulk-delete/bulk-delete.png)

1. 파일 내용의 유효성을 검사 한 후에는 작업을 제출 하기 전에 모든 오류를 수정 해야 합니다.
1. 파일이 유효성 검사를 통과 하면 **제출** 을 선택 하 여 사용자를 삭제 하는 Azure batch 작업을 시작 합니다. 오류가 있는 경우 대량 작업 결과 페이지에서 결과 파일을 다운로드 하 고 볼 수 있습니다. 파일에는 각 오류의 이유가 포함 되어 있습니다.

## <a name="check-status"></a>상태 확인

**대량 작업 결과 (미리 보기)** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

   ![대량 작업 결과 페이지에서 업로드 상태 확인](./media/users-bulk-delete/bulk-center.png)

다음으로, 사용자가 삭제 한 사용자가 Azure Portal 또는 PowerShell을 사용 하 여 Azure AD 조직에 있는지 확인할 수 있습니다.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Azure Portal에서 삭제 된 사용자 확인

1. 조직의 사용자 관리자 계정으로 Azure Portal에 로그인 합니다.
1. 탐색 창에서 **Azure Active Directory**를 선택합니다.
1. **관리**에서 **사용자**를 선택합니다.
1. **표시**아래에서 **모든 사용자** 만을 선택 하 고 삭제 한 사용자가 더 이상 나열 되지 않는지 확인 합니다.

### <a name="verify-deleted-users-with-powershell"></a>PowerShell을 사용 하 여 삭제 된 사용자 확인

다음 명령을 실행합니다.

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

삭제 한 사용자가 더 이상 나열 되지 않는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [사용자 일괄 추가](users-bulk-add.md)
- [사용자 목록 다운로드](users-bulk-download.md)
- [사용자 대량 복원](users-bulk-restore.md)
