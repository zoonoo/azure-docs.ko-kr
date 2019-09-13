---
title: 대량 다운로드 그룹 구성원 목록-Azure Active Directory 포털 | Microsoft Docs
description: Azure 관리 센터에서 사용자를 대량으로 추가 합니다.
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
ms.openlocfilehash: 120b5606dd6b6f4451b563843f112bd614341eac
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914784"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Azure Active Directory에서 그룹 구성원 대량 다운로드 (미리 보기)

Azure Active Directory (Azure AD) 포털을 사용 하 여 조직의 그룹 멤버를 쉼표로 구분 된 값 (CSV) 파일에 대량으로 다운로드할 수 있습니다.

> [!NOTE]
> Azure AD 대량 작업은 Azure AD의 공개 미리 보기 기능이 며 유료 Azure AD 라이선스 계획과 함께 사용할 수 있습니다. 미리 보기 사용 약관에 대 한 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

## <a name="bulk-download-service-limits"></a>대량 다운로드 서비스 제한

그룹 구성원 목록을 다운로드 하는 각 대량 작업은 최대 1 시간 동안 실행할 수 있습니다. 이를 통해 50만 이상의 멤버 목록을 다운로드할 수 있습니다.

## <a name="to-bulk-download-group-membership"></a>그룹 구성원을 대량으로 다운로드 하려면

1. 조직의 사용자 관리자 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다. 그룹 소유자는 자신이 소유한 그룹의 멤버를 대량으로 다운로드할 수도 있습니다.
1. Azure AD에서 **그룹** > **모든 그룹**을 선택 합니다.
1. 구성원이 다운로드 될 그룹을 열고 **구성원**을 선택 합니다.
1. **구성원** 페이지에서 **구성원 다운로드** 를 선택 하 여 그룹 구성원을 나열 하는 CSV 파일을 다운로드 합니다.

   ![멤버 다운로드 명령은 그룹의 프로필 페이지에 있습니다.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>다운로드 상태 확인

**대량 작업 결과 (미리 보기)** 페이지에서 보류 중인 모든 대량 요청의 상태를 볼 수 있습니다.

   ![대량 작업 결과 페이지에 대량 요청 상태가 표시 됩니다.](./media/groups-bulk-download-members/bulk-center.png)

## <a name="next-steps"></a>다음 단계

- [그룹 구성원 대량 가져오기](groups-bulk-import-members.md)
- [그룹 구성원 대량 제거](groups-bulk-download-members.md)
