---
title: 대량 다운로드 그룹 구성원 목록-Azure Active Directory 포털 | Microsoft Docs
description: Azure 관리 센터에서 사용자를 대량으로 추가 합니다.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9abfe79989dbefe79a001e4638c7318bd99d54a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421708"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Azure Active Directory에서 그룹의 멤버를 대량 다운로드

Azure Active Directory (Azure AD) 포털을 사용 하 여 조직의 그룹 멤버를 쉼표로 구분 된 값 (CSV) 파일에 대량으로 다운로드할 수 있습니다.

## <a name="to-bulk-download-group-membership"></a>그룹 구성원을 대량으로 다운로드 하려면

1. 조직의 사용자 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 그룹 소유자는 자신이 소유한 그룹의 멤버를 대량으로 다운로드할 수도 있습니다.
1. Azure AD에서 **그룹** > **모든 그룹**을 선택합니다.
1. 구성원이 다운로드 될 그룹을 열고 **구성원**을 선택 합니다.
1. **구성원** 페이지에서 **구성원 다운로드** 를 선택 하 여 그룹 구성원을 나열 하는 CSV 파일을 다운로드 합니다.

   ![멤버 다운로드 명령은 그룹의 프로필 페이지에 있습니다.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>다운로드 상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

[![대량 작업 결과 페이지에서 상태를 확인 합니다.](media/groups-bulk-download-members/bulk-center.png)](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>대량 다운로드 서비스 제한

그룹 구성원 목록을 다운로드 하는 각 대량 작업은 최대 1 시간 동안 실행할 수 있습니다. 이를 통해 50만 이상의 멤버 목록을 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [그룹 멤버 대량 가져오기](groups-bulk-import-members.md)
- [그룹 구성원 대량 제거](groups-bulk-download-members.md)
