---
title: Azure Active Directory 포털에서 사용자를 대량 삭제 | 마이크로 소프트 문서
description: Azure Active Directory의 Azure 관리 센터에서 사용자를 일괄 삭제
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
ms.openlocfilehash: beb8b4f35dc5f02e59cced05a6bcfc235d42f996
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532822"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Azure Active 디렉터리에서 사용자를 대량 삭제

Azure Active Directory(Azure AD) 포털을 사용하면 CSV(쉼표로 구분된 값) 파일을 사용하여 사용자를 대량 삭제하여 그룹에 많은 구성원을 제거할 수 있습니다.

## <a name="to-bulk-delete-users"></a>사용자를 일괄 삭제하려면

1. 조직의 사용자 관리자인 계정을 사용하여 [Azure AD 조직에 로그인합니다.](https://aad.portal.azure.com)
1. Azure AD에서 **사용자** > **대량 삭제를 선택합니다.**
1. 대량 **삭제 사용자** 페이지에서 사용자 속성의 유효한 CSV 파일을 받으려면 **다운로드를** 선택합니다.

   ![삭제할 사용자를 나열하는 로컬 CSV 파일을 선택합니다.](./media/users-bulk-delete/bulk-delete.png)

1. CSV 파일을 열고 삭제하려는 각 사용자에 대한 줄을 추가합니다. 필요한 값은 **사용자 주체 이름입니다.** 그런 다음 파일을 저장합니다.

   ![CSV 파일에는 삭제할 사용자의 이름과 ID가 포함되어 있습니다.](./media/users-bulk-delete/delete-csv-file.png)

1. 대량 **삭제 사용자** 페이지에서 **csv 파일 업로드에서**파일을 찾아봅습니다. 파일을 선택하고 제출을 클릭하면 CSV 파일의 유효성 검사가 시작됩니다.
1. 파일 콘텐츠의 유효성 검사가 완료되면 **파일 업로드 성공**이 표시됩니다. 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과하면 **제출을** 선택하여 사용자를 삭제하는 Azure 대량 작업을 시작합니다.
1. 삭제 작업이 완료되면 대량 작업이 성공했다는 알림이 표시됩니다.

오류가 있는 경우 **대량 작업 결과** 페이지에서 결과 파일을 다운로드하여 볼 수 있습니다. 파일에각 오류에 대한 이유가 포함되어 있습니다.

## <a name="check-status"></a>상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

   [![](media/users-bulk-delete/bulk-center.png "Check delete status in the Bulk Operations Results page")](media/users-bulk-delete/bulk-center.png#lightbox)

그런 다음 삭제한 사용자가 Azure 포털또는 PowerShell을 사용하여 Azure AD 조직에 있는지 확인할 수 있습니다.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Azure 포털에서 삭제된 사용자 확인

1. 조직에서 사용자 관리자인 계정으로 Azure Portal에 로그인합니다.
1. 탐색 창에서 **Azure Active Directory**를 선택합니다.
1. **관리**에서 **사용자**를 선택합니다.
1. **표시에서** **모든 사용자만** 선택하고 삭제한 사용자가 더 이상 나열되지 않았는지 확인합니다.

### <a name="verify-deleted-users-with-powershell"></a>PowerShell을 통해 삭제된 사용자 확인

다음 명령을 실행합니다.

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

삭제한 사용자가 더 이상 나열되지 않았는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [사용자 일괄 추가](users-bulk-add.md)
- [사용자 목록 다운로드](users-bulk-download.md)
- [대량 복원 사용자](users-bulk-restore.md)
