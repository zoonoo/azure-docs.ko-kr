---
title: Azure Active Directory 포털에서 사용자 대량 생성 (미리 보기) | Microsoft Docs
description: Azure Active Directory의 Azure AD 관리 센터에서 대량으로 사용자 추가
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174343"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Azure Active Directory에서 사용자 대량 생성 (미리 보기)

Azure AD (Azure Active Directory)는 대량 사용자 만들기 및 삭제 작업, 게스트에 대 한 대량 초대, 사용자, 그룹 및 그룹 구성원 목록 다운로드를 지원 합니다.

## <a name="required-permissions"></a>필요한 사용 권한

관리 포털에서 사용자를 대량으로 만들려면 전역 관리자 또는 사용자 관리자 권한으로 로그인 해야 합니다.

## <a name="to-create-users-in-bulk"></a>대량으로 사용자를 만들려면

1. 조직의 사용자 관리자 계정으로 [AZURE AD 조직에 로그인](https://aad.portal.azure.com) 합니다.
1. Azure AD에서 **사용자** > **대량 만들기**를 선택 합니다.
1. **사용자 대량 생성** 페이지에서 **다운로드** 를 선택 하 여 사용자 속성의 올바른 CSV (쉼표로 구분 된 값) 파일을 받은 다음 만들려는 사용자 추가를 추가 합니다.

   ![추가 하려는 사용자를 나열 하는 로컬 CSV 파일을 선택 합니다.](./media/users-bulk-add/upload-button.png)

1. CSV 파일을 열고 만들려는 각 사용자에 대 한 줄을 추가 합니다. 필요한 값은 **이름**, **사용자 계정 이름**, **초기 암호** 및 **블록 로그인 (예/아니요)** 뿐입니다. 그런 다음 파일을 저장합니다.

   ![CSV 파일에는 만들 사용자의 이름 및 Id가 포함 되어 있습니다.](./media/users-bulk-add/add-csv-file.png)

1. **대량 생성 사용자 (미리 보기)** 페이지에서 CSV 파일 업로드 아래에 있는 파일을 찾습니다. 파일을 선택 하 고 **제출**을 클릭 하면 CSV 파일의 유효성 검사가 시작 됩니다.
1. 파일 내용의 유효성을 검사 한 후에 **파일이 업로드**된 것을 볼 수 있습니다. 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과 하면 **제출** 을 선택 하 여 새 사용자를 가져오는 Azure 대량 작업을 시작 합니다.
1. 가져오기 작업이 완료 되 면 대량 작업 작업 상태 알림이 표시 됩니다.

오류가 있는 경우 **대량 작업 결과** 페이지에서 결과 파일을 다운로드 하 고 볼 수 있습니다. 파일에는 각 오류의 이유가 포함 되어 있습니다.

## <a name="check-status"></a>상태 확인

**대량 작업 결과 (미리 보기)** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

   ![대량 작업 결과 페이지에서 업로드 상태 확인](./media/users-bulk-add/bulk-center.png)

다음으로, 사용자가 만든 사용자가 Azure Portal 또는 PowerShell을 사용 하 여 Azure AD 조직에 있는지 확인할 수 있습니다.

## <a name="verify-users-in-the-azure-portal"></a>Azure Portal 사용자 확인

1. 조직의 사용자 관리자 계정으로 [AZURE AD 관리 센터에 로그인](https://aad.portal.azure.com) 합니다.
1. 탐색 창에서 **Azure Active Directory**를 선택합니다.
1. **관리**에서 **사용자**를 선택합니다.
1. **표시**아래에서 **모든 사용자** 를 선택 하 고 사용자가 만든 사용자가 나열 되는지 확인 합니다.

### <a name="verify-users-with-powershell"></a>PowerShell을 사용 하 여 사용자 확인

다음 명령을 실행합니다.

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

만든 사용자가 나열 됩니다.

## <a name="bulk-import-service-limits"></a>대량 가져오기 서비스 제한

사용자를 만드는 각 대량 활동은 최대 1 시간 동안 실행할 수 있습니다. 이렇게 하면 5만 명 이상의 사용자를 대량으로 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [사용자 대량 삭제](users-bulk-delete.md)
- [사용자 목록 다운로드](users-bulk-download.md)
- [사용자 대량 복원](users-bulk-restore.md)
