---
title: Azure Active Directory 포털에서 대량으로 사용자 만들기 | 마이크로 소프트 문서
description: Azure Active Directory의 Azure AD 관리자 센터에 사용자를 일괄 추가
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3a8b9cb9701288d24534ab08940f6dbd4a698ad
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532942"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Azure Active 디렉터리에서 대량 으로 사용자 만들기

Azure Active Directory(Azure AD)는 대량 사용자 만들기 및 삭제 작업, 게스트에 대한 일괄 초대를 지원하며 사용자, 그룹 및 그룹 구성원의 다운로드 목록을 지원합니다.

## <a name="required-permissions"></a>필요한 사용 권한

관리 포털에서 사용자를 대량으로 만들려면 글로벌 관리자 또는 사용자 관리자로 로그인해야 합니다.

## <a name="to-create-users-in-bulk"></a>대량으로 사용자를 만들려면

1. 조직의 사용자 관리자인 계정을 사용하여 [Azure AD 조직에 로그인합니다.](https://aad.portal.azure.com)
1. Azure AD에서 **사용자** > **대량 만들기를**선택합니다.
1. 대량 **사용자 만들기** 페이지에서 **다운로드를** 선택하여 사용자 속성의 유효한 쉼표 분리값(CSV) 파일을 받은 다음 만들 사용자를 추가합니다.

   ![추가할 사용자를 나열하는 로컬 CSV 파일을 선택합니다.](./media/users-bulk-add/upload-button.png)

1. CSV 파일을 열고 만들려는 각 사용자에 대한 줄을 추가합니다. 필요한 값은 **이름,** **사용자 주체 이름,** **초기 암호** 및 **블록 로그인(예/아니요)입니다.** 그런 다음 파일을 저장합니다.

   ![CSV 파일에는 만들 사용자의 이름과 ID가 포함되어 있습니다.](./media/users-bulk-add/add-csv-file.png)

1. 대량 **생성 사용자** 페이지에서 CSV 파일 업로드 에서 파일을 찾아봅습니다. 파일을 선택하고 **제출을**클릭하면 CSV 파일의 유효성 검사가 시작됩니다.
1. 파일 내용의 유효성이 검사되면 **파일이 성공적으로 업로드된**것을 볼 수 있습니다. 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과하면 **제출을** 선택하여 새 사용자를 가져오는 Azure 대량 작업을 시작합니다.
1. 가져오기 작업이 완료되면 대량 작업 작업 상태에 대한 알림이 표시됩니다.

오류가 있는 경우 **대량 작업 결과** 페이지에서 결과 파일을 다운로드하여 볼 수 있습니다. 파일에각 오류에 대한 이유가 포함되어 있습니다.

## <a name="check-status"></a>상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

그런 다음 만든 사용자가 Azure 포털또는 PowerShell을 사용하여 Azure AD 조직에 있는지 확인할 수 있습니다.

## <a name="verify-users-in-the-azure-portal"></a>Azure 포털의 사용자 확인

1. 조직의 사용자 관리자인 계정을 사용하여 [Azure AD 관리자 센터에 로그인합니다.](https://aad.portal.azure.com)
1. 탐색 창에서 **Azure Active Directory**를 선택합니다.
1. **관리**에서 **사용자**를 선택합니다.
1. **표시**에서 **모든 사용자를** 선택하고 만든 사용자가 나열되었는지 확인합니다.

### <a name="verify-users-with-powershell"></a>PowerShell으로 사용자 확인

다음 명령을 실행합니다.

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

만든 사용자가 나열되어 있음을 알 수 있습니다.

## <a name="bulk-import-service-limits"></a>대량 가져오기 서비스 제한

사용자를 만드는 각 대량 활동은 최대 1시간 동안 실행할 수 있습니다. 이렇게 하면 최소 50,000명의 사용자를 대량으로 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [대량 사용자 삭제](users-bulk-delete.md)
- [사용자 목록 다운로드](users-bulk-download.md)
- [대량 복원 사용자](users-bulk-restore.md)
