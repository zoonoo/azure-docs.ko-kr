---
title: CSV 파일 - Azure Active Directory를 업로드하여 그룹 구성원을 일괄 제거 | 마이크로 소프트 문서
description: Azure 관리 센터에서 대량 작업에서 그룹 구성원을 제거합니다.
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
ms.openlocfilehash: 1e6d0752245e3864a8ad25efd5181d5cc1eec7ae
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533264"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Azure Active 디렉터리에서 그룹 구성원을 일괄 제거

Azure Active Directory(Azure AD) 포털을 사용하면 CSV(쉼표로 구분된 값) 파일을 사용하여 그룹 구성원을 일괄 제거하여 그룹에서 많은 수의 구성원을 제거할 수 있습니다.

## <a name="to-bulk-remove-group-members"></a>그룹 구성원을 일괄 제거하려면

1. 조직의 사용자 관리자 계정으로 [Azure 포털에](https://portal.azure.com) 로그인합니다. 그룹 소유자는 소유한 그룹의 구성원을 일괄 제거할 수도 있습니다.
1. Azure AD에서 **Groups** > **모든 그룹**그룹을 선택합니다.
1. 구성원을 제거하는 그룹을 연 다음 **구성원을**선택합니다.
1. **멤버** 페이지에서 구성원 **제거를**선택합니다.
1. 대량 **제거 그룹 구성원** 페이지에서 **다운로드를** 선택하여 필요한 그룹 구성원 속성이 있는 CSV 파일 템플릿을 가져옵니다.

   ![구성원 제거 명령이 그룹의 프로필 페이지에 있습니다.](./media/groups-bulk-remove-members/remove-panel.png)

1. CSV 파일을 열고 그룹에서 제거할 각 그룹 구성원에 대한 줄을 추가합니다(필요한 값은 Member 개체 ID 또는 사용자 주값 이름임). 그런 다음 파일을 저장합니다.

   ![CSV 파일에는 구성원이 제거할 이름과 ID가 포함되어 있습니다.](./media/groups-bulk-remove-members/csv-file.png)

1. 대량 **제거 그룹 구성원** 페이지에서 **csv 파일 업로드**에서 파일을 찾아봅습니다. 파일을 선택하면 CSV 파일의 유효성 검사가 시작됩니다.
1. 파일 내용의 유효성이 검사되면 대량 가져오기 페이지에 **파일업로드가 성공적으로 표시됩니다.** 오류가 있는 경우 해당 오류를 해결해야 작업을 제출할 수 있습니다.
1. 파일이 유효성 검사를 통과하면 **제출을** 선택하여 그룹에서 그룹 구성원을 제거하는 Azure 대량 작업을 시작합니다.
1. 제거 작업이 완료되면 대량 작업이 성공했다는 알림이 표시됩니다.

## <a name="check-removal-status"></a>제거 상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

벌크 작업 내의 각 광고 항목에 대한 자세한 내용은 **#성공,** **실패**또는 **총 요청** 열 아래의 값을 선택합니다. 오류가 발생하면 실패 이유가 나열됩니다.

## <a name="bulk-removal-service-limits"></a>대량 제거 서비스 제한

그룹 구성원 목록을 제거하는 각 대량 활동은 최대 1시간 동안 실행할 수 있습니다. 이렇게 하면 최소 40,000명의 구성원 목록을 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [대량 가져오기 그룹 구성원](groups-bulk-import-members.md)
- [그룹의 구성원 다운로드](groups-bulk-download-members.md)
- [모든 그룹 목록 다운로드](groups-bulk-download.md)
