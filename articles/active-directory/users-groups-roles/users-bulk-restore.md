---
title: Azure Active Directory 포털에서 삭제된 사용자를 대량 복원 | 마이크로 소프트 문서
description: Azure Active Directory의 Azure AD 관리자 센터에서 삭제된 사용자를 대량으로 복원
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: f75fe224491c2853f819a45db678e87849dc72d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532720"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Azure Active Directory에서 삭제된 사용자를 대량 복원

Azure Active Directory(Azure AD)는 대량 사용자 만들기 및 삭제 작업, 게스트에 대한 일괄 초대를 지원하며 사용자, 그룹 및 그룹 구성원의 다운로드 목록을 지원합니다.

## <a name="to-bulk-restore-users"></a>사용자를 일괄 복원하려면

1. [Azure AD 조직의](https://aad.portal.azure.com) 사용자 관리자인 계정으로 Azure AD 조직에 로그인합니다.
1. Azure AD에서 **Users** > **삭제된 사용자를 선택합니다.**
1. **삭제된 사용자** 페이지에서 **대량 복원을** 선택하여 복원할 사용자의 속성에 대한 유효한 CSV 파일을 업로드합니다.

   ![삭제된 사용자 페이지에서 대량 복원 명령을 선택합니다.](./media/users-bulk-restore/bulk-restore.png)

1. CSV 파일을 열고 복원할 각 사용자에 대한 줄을 추가합니다. 필요한 값은 **ObjectID**입니다. 그런 다음 파일을 저장합니다.

   ![추가할 사용자를 나열하는 로컬 CSV 파일을 선택합니다.](./media/users-bulk-restore/upload-button.png)

1. 대량 **복원** 페이지에서 **csv 파일 업로드**에서 파일을 찾아봅습니다. 파일을 선택하고 **제출을**클릭하면 CSV 파일의 유효성 검사가 시작됩니다.
1. 파일 콘텐츠의 유효성 검사가 완료되면 **파일 업로드 성공**이 표시됩니다. 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과하면 **제출을** 선택하여 사용자를 복원하는 Azure 대량 작업을 시작합니다.
1. 복원 작업이 완료되면 대량 작업이 성공했다는 알림이 표시됩니다.

오류가 있는 경우 **대량 작업 결과** 페이지에서 결과 파일을 다운로드하여 볼 수 있습니다. 파일에각 오류에 대한 이유가 포함되어 있습니다.

## <a name="check-status"></a>상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

[![](media/users-bulk-restore/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-restore/bulk-center.png#lightbox)

그런 다음 복원한 사용자가 Azure 포털또는 PowerShell을 사용하여 Azure AD 조직에 있는지 확인할 수 있습니다.

## <a name="view-restored-users-in-the-azure-portal"></a>Azure 포털에서 복원된 사용자 보기

1. 조직의 사용자 관리자인 계정을 사용하여 [Azure AD 관리자 센터에 로그인합니다.](https://aad.portal.azure.com)
1. 탐색 창에서 **Azure Active Directory**를 선택합니다.
1. **관리**에서 **사용자**를 선택합니다.
1. **표시**에서 **모든 사용자를** 선택하고 복원한 사용자가 나열되었는지 확인합니다.

### <a name="view-users-with-powershell"></a>PowerShell을 통해 사용자 보기

다음 명령을 실행합니다.

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

복원한 사용자가 나열되어 있는지 확인해야 합니다.

## <a name="next-steps"></a>다음 단계

- [대량 가져오기 사용자](users-bulk-add.md)
- [대량 사용자 삭제](users-bulk-delete.md)
- [사용자 목록 다운로드](users-bulk-download.md)
