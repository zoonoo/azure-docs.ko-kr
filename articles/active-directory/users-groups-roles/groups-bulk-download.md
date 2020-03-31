---
title: Azure Active Directory 포털에서 그룹 목록 다운로드 | 마이크로 소프트 문서
description: Azure Active Directory의 Azure 관리 센터에서 그룹 속성을 일괄 다운로드합니다.
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
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517145"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Azure Active Directory에서 그룹 목록(미리 보기)을 일괄 다운로드합니다.

Azure Active Directory(Azure AD) 포털을 사용하여 조직의 모든 그룹 목록을 CSV(쉼표 로 구분된 값) 파일에 대량 다운로드할 수 있습니다.

## <a name="to-download-a-list-of-groups"></a>그룹 목록을 다운로드하려면

1. 조직의 관리자 계정을 사용하여 [Azure 포털에](https://portal.azure.com) 로그인합니다.
1. Azure AD에서 **그룹** > **다운로드 그룹을**선택합니다.
1. 그룹 **다운로드** 페이지에서 그룹을 나열하는 CSV 파일을 받으려면 **시작을** 선택합니다.

   ![다운로드 그룹 명령이 모든 그룹 페이지에 있습니다.](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>다운로드 상태 확인

**대량 작업 결과(미리 보기)** 페이지에서 보류 중인 모든 대량 요청의 상태를 확인할 수 있습니다.

   ![대량 작업 결과 페이지에 대량 요청 상태가 표시 됩니다.](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>대량 다운로드 서비스 제한

그룹 목록을 다운로드하는 각 대량 활동은 최대 1시간 동안 실행할 수 있습니다. 이렇게 하면 300,000개 이상의 그룹 목록을 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [그룹 구성원 을 일괄 제거](groups-bulk-remove-members.md)
- [그룹의 구성원 다운로드](groups-bulk-download-members.md)
