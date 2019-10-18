---
title: 그룹에 구성원을 추가 하는 대량 가져오기 업로드-Azure Active Directory | Microsoft Docs
description: Azure Active Directory 관리 센터에서 그룹 구성원을 대량으로 추가 합니다.
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
ms.openlocfilehash: 8ff4234d9fd28e655e868108e37b09c5afe2767c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517136"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Azure Active Directory에서 그룹 구성원 대량 가져오기 (미리 보기)

Azure Active Directory (Azure AD) 포털을 사용 하 여 쉼표로 구분 된 값 (CSV) 파일을 사용 하 여 그룹 멤버를 대량으로 가져올 수 있습니다.

## <a name="to-bulk-import-group-members"></a>그룹 구성원을 대량으로 가져오려면

1. 조직의 사용자 관리자 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다. 그룹 소유자는 자신이 소유한 그룹의 멤버를 대량으로 가져올 수도 있습니다.
1. Azure AD에서 **그룹**  > **모든 그룹**을 선택 합니다.
1. 구성원을 추가할 그룹을 연 다음 **멤버**를 선택 합니다.
1. **구성원** 페이지에서 **구성원 가져오기**를 선택 합니다.
1. **그룹 구성원 대량 가져오기 (미리 보기)** 페이지에서 **다운로드** 를 선택 하 여 필요한 그룹 구성원 속성이 포함 된 CSV 파일 템플릿을 가져옵니다.

    ![멤버 가져오기 명령은 그룹의 프로필 페이지에 있습니다.](./media/groups-bulk-import-members/import-panel.png)

1. CSV 파일을 열고 그룹으로 가져올 각 그룹 구성원에 대 한 줄을 추가 합니다 (필수 값은 **구성원 개체 ID** 또는 **사용자 계정 이름**). 그런 다음 파일을 저장합니다.

   ![CSV 파일에는 가져올 멤버에 대 한 이름 및 Id가 포함 되어 있습니다.](./media/groups-bulk-import-members/csv-file.png)

1. **대량 가져오기 그룹 구성원 (미리 보기)** 페이지의 **csv 파일 업로드**아래에서 파일을 찾습니다. 파일을 선택 하면 CSV 파일의 유효성 검사가 시작 됩니다.
1. 파일 내용의 유효성을 검사 하는 경우 대량 가져오기 페이지에 **파일이 업로드 되었습니다**.가 표시 됩니다. 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과 하면 **제출** 을 선택 하 여 그룹 구성원을 그룹으로 가져오는 Azure 대량 작업을 시작 합니다.
1. 가져오기 작업이 완료 되 면 대량 작업이 성공 했다는 알림이 표시 됩니다.

## <a name="check-import-status"></a>가져오기 상태 확인

**대량 작업 결과 (미리 보기)** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

   ![대량 작업 결과 페이지에 대량 요청 상태가 표시 됩니다.](./media/groups-bulk-import-members/bulk-center.png)

대량 작업 내의 각 줄 항목에 대 한 자세한 내용을 보려면 **# Success**, **# Failure**또는 **Total Requests** 열 아래의 값을 선택 합니다. 오류가 발생하면 실패 이유가 나열됩니다.

## <a name="bulk-import-service-limits"></a>대량 가져오기 서비스 제한

그룹 구성원 목록을 가져오는 각 대량 작업은 최대 1 시간 동안 실행할 수 있습니다. 이를 통해 4만 이상의 멤버 목록을 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [그룹 구성원 대량 제거](groups-bulk-remove-members.md)
- [그룹 구성원 다운로드](groups-bulk-download-members.md)
- [모든 그룹의 목록 다운로드](groups-bulk-download.md)
