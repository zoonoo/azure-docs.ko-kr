---
title: 대량 다운로드 그룹 구성원 목록-Azure Active Directory 포털 | Microsoft Docs
description: Azure 관리 센터에서 사용자를 대량으로 추가 합니다.
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
ms.openlocfilehash: 3faca8d1a2538ed03a917d6db8d54323fe626369
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81533689"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Azure Active Directory에서 그룹의 멤버를 대량 다운로드

Azure Active Directory (Azure AD) 포털을 사용 하 여 조직의 그룹 멤버를 쉼표로 구분 된 값 (CSV) 파일에 대량으로 다운로드할 수 있습니다.

## <a name="to-bulk-download-group-membership"></a>그룹 구성원을 대량으로 다운로드 하려면

1. 조직의 사용자 관리자 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다. 그룹 소유자는 자신이 소유한 그룹의 멤버를 대량으로 다운로드할 수도 있습니다.
1. Azure AD에서 **그룹** > **모든 그룹**을 선택 합니다.
1. 구성원이 다운로드 될 그룹을 열고 **구성원**을 선택 합니다.
1. **구성원** 페이지에서 **구성원 다운로드** 를 선택 하 여 그룹 구성원을 나열 하는 CSV 파일을 다운로드 합니다.

   ![멤버 다운로드 명령은 그룹의 프로필 페이지에 있습니다.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>다운로드 상태 확인

**대량 작업 결과** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>대량 다운로드 서비스 제한

그룹 구성원 목록을 다운로드 하는 각 대량 작업은 최대 1 시간 동안 실행할 수 있습니다. 이를 통해 50만 이상의 멤버 목록을 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [그룹 구성원 대량 가져오기](groups-bulk-import-members.md)
- [그룹 구성원 대량 제거](groups-bulk-download-members.md)
