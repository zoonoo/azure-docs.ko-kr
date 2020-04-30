---
title: Azure Active Directory 포털에서 사용자 대량 생성 | Microsoft Docs
description: Azure Active Directory의 Azure AD 관리 센터에서 대량으로 사용자 추가
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03f6e3d6edde51598b1d148469aceb1ff3b3d636
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203409"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Azure Active Directory에서 사용자 대량 생성

Azure AD (Azure Active Directory)는 대량 사용자 만들기 및 삭제 작업을 지원 하 고 사용자 목록 다운로드를 지원 합니다. Azure AD 포털에서 다운로드할 수 있는 쉼표로 구분 된 값 (CSV) 템플릿만 입력 하면 됩니다.

## <a name="required-permissions"></a>필요한 사용 권한

관리 포털에서 사용자를 대량으로 만들려면 전역 관리자 또는 사용자 관리자 권한으로 로그인 해야 합니다.

## <a name="understand-the-csv-template"></a>CSV 템플릿 이해

대량 업로드 CSV 템플릿을 다운로드 하 고 입력 하 여 대량으로 Azure AD 사용자를 성공적으로 만들 수 있도록 합니다. 다운로드 한 CSV 템플릿은 다음 예제와 같이 표시 될 수 있습니다.

![각 행과 열에 대 한 용도 및 값을 설명 하는 업로드 및 호출에 대 한 스프레드시트](./media/users-bulk-add/create-template-example.png)

### <a name="csv-template-structure"></a>CSV 템플릿 구조

다운로드 된 CSV 템플릿의 행은 다음과 같습니다.

- **버전 번호**: 버전 번호를 포함 하는 첫 번째 행은 CSV 업로드에 포함 되어야 합니다.
- **열 제목**: &lt;열 머리글의 형식은 *항목 이름* &gt; [PropertyName] &lt; *Required 또는 blank*&gt;입니다. `Name [displayName] Required`)을 입력합니다. 템플릿의 일부 이전 버전에는 약간의 변형이 있을 수 있습니다.
- **예제 행**: 템플릿에는 각 열에 대해 허용 되는 값의 예제 행이 포함 되어 있습니다. 예 행을 제거 하 고 자신의 항목으로 바꾸어야 합니다.

### <a name="additional-guidance"></a>추가 지침

- 업로드 템플릿의 처음 두 행은 제거 하거나 수정 하면 안 됩니다. 또는 업로드를 처리할 수 없습니다.
- 먼저 필수 열이 나열 됩니다.
- 템플릿에 새 열을 추가 하지 않는 것이 좋습니다. 추가 하는 추가 열은 무시 되 고 처리 되지 않습니다.
- 최대한 자주 최신 버전의 CSV 템플릿을 다운로드 하는 것이 좋습니다.

## <a name="to-create-users-in-bulk"></a>대량으로 사용자를 만들려면

1. 조직의 사용자 관리자 계정으로 [AZURE AD 조직에 로그인](https://aad.portal.azure.com) 합니다.
1. Azure AD에서 **사용자** > **대량 생성**을 선택 합니다.
1. **사용자 대량 생성** 페이지에서 **다운로드** 를 선택 하 여 사용자 속성의 올바른 CSV (쉼표로 구분 된 값) 파일을 받은 다음 만들려는 사용자 추가를 추가 합니다.

   ![추가 하려는 사용자를 나열 하는 로컬 CSV 파일을 선택 합니다.](./media/users-bulk-add/upload-button.png)

1. CSV 파일을 열고 만들려는 각 사용자에 대 한 줄을 추가 합니다. 필요한 값은 **이름**, **사용자 계정 이름**, **초기 암호** 및 **블록 로그인 (예/아니요)** 뿐입니다. 그런 다음 파일을 저장합니다.

   [![](media/users-bulk-add/add-csv-file.png "The CSV file contains names and IDs of the users to create")](media/users-bulk-add/add-csv-file.png#lightbox)

1. **대량 생성 사용자** 페이지의 CSV 파일 업로드에서 파일을 찾습니다. 파일을 선택 하 고 **제출**을 클릭 하면 CSV 파일의 유효성 검사가 시작 됩니다.
1. 파일 내용의 유효성을 검사 한 후에 **파일이 업로드**된 것을 볼 수 있습니다. 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과 하면 **제출** 을 선택 하 여 새 사용자를 가져오는 Azure 대량 작업을 시작 합니다.
1. 가져오기 작업이 완료 되 면 대량 작업 작업 상태 알림이 표시 됩니다.

오류가 있는 경우 **대량 작업 결과** 페이지에서 결과 파일을 다운로드 하 고 볼 수 있습니다. 파일에는 각 오류의 이유가 포함 되어 있습니다.

## <a name="check-status"></a>상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

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
