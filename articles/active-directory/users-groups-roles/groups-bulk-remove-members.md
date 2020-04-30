---
title: CSV 파일을 업로드 하 여 그룹 구성원 대량 제거-Azure Active Directory | Microsoft Docs
description: Azure 관리 센터에서 대량 작업의 그룹 구성원을 제거 합니다.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b3c6e471a8e44236baf9bfc2c8eb6c9d5526d72
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203461"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Azure Active Directory에서 그룹 구성원 대량 제거

Azure Active Directory (Azure AD) 포털을 사용 하 여 그룹 멤버를 대량으로 제거 하기 위해 CSV (쉼표로 구분 된 값) 파일을 사용 하 여 그룹에서 많은 수의 멤버를 제거할 수 있습니다.

## <a name="understand-the-csv-template"></a>CSV 템플릿 이해

대량 업로드 CSV 템플릿을 다운로드 하 고 입력 하 여 Azure AD 그룹 멤버를 대량으로 추가 합니다. CSV 템플릿은 다음 예제와 같이 보일 수 있습니다.

![각 행과 열에 대 한 용도 및 값을 설명 하는 업로드 및 호출에 대 한 스프레드시트](./media/groups-bulk-remove-members/template-example.png)

### <a name="csv-template-structure"></a>CSV 템플릿 구조

다운로드 된 CSV 템플릿의 행은 다음과 같습니다.

- **버전 번호**: 버전 번호를 포함 하는 첫 번째 행은 CSV 업로드에 포함 되어야 합니다.
- **열 제목**: &lt;열 머리글의 형식은 *항목 이름* &gt; [PropertyName] &lt; *Required 또는 blank*&gt;입니다. `Member object ID or user principal name [memberObjectIdOrUpn] Required`)을 입력합니다. 템플릿의 일부 이전 버전에는 약간의 변형이 있을 수 있습니다. 그룹 멤버 자격을 변경 하는 경우 사용할 식별자 (구성원 개체 ID 또는 사용자 계정 이름)를 선택할 수 있습니다.
- **예제 행**: 템플릿에는 각 열에 대해 허용 되는 값의 예제 행이 포함 되어 있습니다. 예 행을 제거 하 고 자신의 항목으로 바꾸어야 합니다.

### <a name="additional-guidance"></a>추가 지침

- 업로드 템플릿의 처음 두 행은 제거 하거나 수정 하면 안 됩니다. 또는 업로드를 처리할 수 없습니다.
- 먼저 필수 열이 나열 됩니다.
- 템플릿에 새 열을 추가 하지 않는 것이 좋습니다. 추가 하는 추가 열은 무시 되 고 처리 되지 않습니다.
- 최대한 자주 최신 버전의 CSV 템플릿을 다운로드 하는 것이 좋습니다.

## <a name="to-bulk-remove-group-members"></a>그룹 구성원을 대량 제거 하려면

1. 조직의 사용자 관리자 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다. 그룹 소유자는 자신이 소유 하 고 있는 그룹의 구성원도 대량 제거할 수 있습니다.
1. Azure AD에서 **그룹** > **모든 그룹**을 선택 합니다.
1. 구성원을 제거 하는 그룹을 열고 **구성원**을 선택 합니다.
1. **구성원** 페이지에서 **구성원 제거**를 선택 합니다.
1. **그룹 구성원 대량 제거** 페이지에서 **다운로드** 를 선택 하 여 필요한 그룹 구성원 속성이 포함 된 CSV 파일 템플릿을 가져옵니다.

   ![멤버 제거 명령은 그룹의 프로필 페이지에 있습니다.](./media/groups-bulk-remove-members/remove-panel.png)

1. CSV 파일을 열고 그룹에서 제거 하려는 각 그룹 구성원에 대 한 줄을 추가 합니다 (필수 값은 구성원 개체 ID 또는 사용자 계정 이름입니다). 그런 다음 파일을 저장합니다.

   ![CSV 파일에는 제거할 멤버의 이름 및 Id가 포함 되어 있습니다.](./media/groups-bulk-remove-members/csv-file.png)

1. **그룹 구성원 대량 제거** 페이지의 **csv 파일 업로드**에서 파일을 찾습니다. 파일을 선택 하면 CSV 파일의 유효성 검사가 시작 됩니다.
1. 파일 내용의 유효성을 검사 하는 경우 대량 가져오기 페이지에 **파일이 업로드 되었습니다**.가 표시 됩니다. 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과 하면 **제출** 을 선택 하 여 그룹에서 그룹 구성원을 제거 하는 Azure 대량 작업을 시작 합니다.
1. 제거 작업이 완료 되 면 대량 작업이 성공 했다는 알림이 표시 됩니다.

## <a name="check-removal-status"></a>제거 상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

대량 작업 내의 각 줄 항목에 대 한 자세한 내용을 보려면 **# Success**, **# Failure**또는 **Total Requests** 열 아래의 값을 선택 합니다. 오류가 발생하면 실패 이유가 나열됩니다.

## <a name="bulk-removal-service-limits"></a>대량 제거 서비스 제한

에서 그룹 구성원 목록을 제거 하는 각 대량 작업은 최대 1 시간 동안 실행할 수 있습니다. 이를 통해 4만 이상의 멤버 목록을 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [그룹 구성원 대량 가져오기](groups-bulk-import-members.md)
- [그룹 구성원 다운로드](groups-bulk-download-members.md)
- [모든 그룹의 목록 다운로드](groups-bulk-download.md)
