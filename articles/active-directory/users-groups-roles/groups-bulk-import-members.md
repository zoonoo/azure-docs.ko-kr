---
title: 그룹에 구성원을 추가하기 위한 대량 가져오기 업로드 - Azure Active Directory | 마이크로 소프트 문서
description: Azure Active Directory 관리 센터에서 그룹 구성원을 일괄추가합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517136"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Azure Active Directory에서 대량 가져오기 그룹 구성원(미리 보기)

Azure Active Directory(Azure AD) 포털을 사용하여 CSV(쉼표로 구분된 값) 파일을 사용하여 그룹 구성원을 대량 가져오기하여 그룹에 많은 수의 구성원을 추가할 수 있습니다.

## <a name="to-bulk-import-group-members"></a>그룹 구성원을 대량 가져오기하려면

1. 조직의 사용자 관리자 계정으로 [Azure 포털에](https://portal.azure.com) 로그인합니다. 그룹 소유자는 소유한 그룹의 구성원을 대량 가져올 수도 있습니다.
1. Azure AD에서 **Groups** > **모든 그룹**그룹을 선택합니다.
1. 구성원을 추가할 그룹을 연 다음 **구성원을**선택합니다.
1. **멤버** 페이지에서 멤버 **가져오기를**선택합니다.
1. 대량 **가져오기 그룹 구성원(미리 보기)** 페이지에서 **다운로드를** 선택하여 필요한 그룹 구성원 속성이 있는 CSV 파일 템플릿을 가져옵니다.

    ![구성원 가져오기 명령이 그룹의 프로필 페이지에 있습니다.](./media/groups-bulk-import-members/import-panel.png)

1. CSV 파일을 열고 그룹에 가져올 각 그룹 구성원에 대한 줄을 추가합니다(필요한 값은 **Member 개체 ID** 또는 사용자 **주체 이름).** 그런 다음 파일을 저장합니다.

   ![CSV 파일에는 멤버가 가져올 이름과 ID가 포함되어 있습니다.](./media/groups-bulk-import-members/csv-file.png)

1. 대량 **가져오기 그룹 구성원(미리 보기)** 페이지에서 **csv 파일 업로드**에서 파일을 찾아봅니다. 파일을 선택하면 CSV 파일의 유효성 검사가 시작됩니다.
1. 파일 내용의 유효성이 검사되면 대량 가져오기 페이지에 **파일업로드가 성공적으로 표시됩니다.** 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과하면 그룹 구성원을 그룹으로 가져오는 Azure 대량 작업을 시작하려면 **제출을** 선택합니다.
1. 가져오기 작업이 완료되면 대량 작업이 성공했다는 알림이 표시됩니다.

## <a name="check-import-status"></a>가져오기 상태 확인

**대량 작업 결과(미리 보기)** 페이지에서 보류 중인 모든 대량 요청의 상태를 확인할 수 있습니다.

   ![대량 작업 결과 페이지에 대량 요청 상태가 표시 됩니다.](./media/groups-bulk-import-members/bulk-center.png)

벌크 작업 내의 각 광고 항목에 대한 자세한 내용은 **#성공,** **실패**또는 **총 요청** 열 아래의 값을 선택합니다. 오류가 발생하면 실패 이유가 나열됩니다.

## <a name="bulk-import-service-limits"></a>대량 가져오기 서비스 제한

그룹 구성원 목록을 가져오는 각 대량 활동은 최대 1시간 동안 실행할 수 있습니다. 이렇게 하면 최소 40,000명의 멤버 목록을 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [그룹 구성원 을 일괄 제거](groups-bulk-remove-members.md)
- [그룹의 구성원 다운로드](groups-bulk-download-members.md)
- [모든 그룹 목록 다운로드](groups-bulk-download.md)
