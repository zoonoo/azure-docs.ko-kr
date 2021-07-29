---
title: 다운로드 가능한 액세스 검토 기록 보고서 만들기 및 관리(미리 보기)-Azure Active Directory
description: Azure Active Directory 액세스 검토를 사용하여 조직에서 액세스 검토에 대한 검토 기록을 다운로드할 수 있습니다.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/25/2021
ms.author: ajburnle
ms.openlocfilehash: 6f0d9b92b9aa3bb48533d5270c0fcd31aa8df82c
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026650"
---
# <a name="create-and-manage-downloadable-access-review-history-report-preview-in-azure-ad-access-reviews"></a>Azure AD 액세스 검토에서 다운로드 가능한 액세스 검토 기록 보고서(미리 보기) 만들기 및 관리

Azure AD(Azure Active Directory) 액세스 검토를 사용하여 조직에서 더 많은 인사이트를 얻을 수 있도록 다운로드 가능한 검토 기록을 만들 수 있습니다. 보고서를 만들 때 보고서에 검토자가 수행한 결정이 반영됩니다. 이러한 보고서는 특정 시간 프레임에 대한 특정 액세스 검토를 포함하도록 작성될 수 있으며, 다양한 검토 유형과 결과 검토를 포함하도록 필터링할 수 있습니다.
 
## <a name="who-can-access-and-request-review-history"></a>검토 기록에 액세스하고 요청할 수 있는 사람

액세스 검토를 볼 수 있는 권한이 있는 모든 사용자는 검토 기록과 요청 검토 기록을 사용할 수 있습니다. 액세스 검토를 보고 작성할 수 있는 역할을 확인하려면 [검토할 수 있는 리소스 유형](deploy-access-reviews.md#what-resource-types-can-be-reviewed)을 참조하세요. 전역 관리자 및 전역 읽기 권한자는 모든 액세스 검토를 볼 수 있습니다. 다른 모든 사용자는 자신이 생성한 액세스 검토에 대한 보고서만 볼 수 있습니다.

## <a name="how-to-create-a-review-history-report"></a>검토 기록 보고서를 만드는 방법

**필수 역할:** 액세스 검토를 볼 권한이 있는 모든 사용자

1. Azure Portal에서 **Azure Active Directory** 를 선택한 다음, **ID 거버넌스** 를 선택합니다.

1. 왼쪽 메뉴의 **검토 액세스** 에서 **검토 기록** 을 선택합니다.
 
1. **새 보고서** 를 선택합니다. 

1. 검토 시작 및 종료 날짜를 지정합니다.

1. 검토 유형을 선택하고 보고서에 포함하려는 결과를 검토합니다. 

    ![액세스 검토 - 액세스 검토 기록 보고서 - 만들기](./media/access-reviews-downloadable-review-history/create-review-history.png)

1. 그런 다음 **만들기** 를 선택하여 액세스 검토 내역 보고서를 만듭니다.

## <a name="how-to-download-review-history-reports"></a>검토 기록 보고서를 다운로드하는 방법

검토 기록 보고서를 만든 후에는 해당 보고서를 다운로드할 수 있습니다. 만들어진 모든 보고서는 CSV 형식의 30일 동안 다운로드할 수 있습니다.

1. **ID 거버넌스** 에서 **검토 기록** 을 선택합니다. 만들어진 모든 검토 기록 보고서를 사용할 수 있습니다. 
1. 다운로드할 보고서를 선택합니다. 

## <a name="what-is-included-in-a-review-history-report"></a>검토 기록 보고서에는 무엇이 포함되나요?

보고서는 다음을 표시하는 사용자 단위로 세부 정보를 제공합니다.

| 요소 이름 | Description |
| --- | --- |
| AccessReviewId |  개체 ID 검토 |
| ReviewType | 그룹, 애플리케이션, Azure AD 역할, Azure 역할 및 액세스 패키지 등의 유형을 검토합니다.|
|ResourceDisplayName | 검토 중인 리소스의 표시 이름 |
| ResourceId | 검토 중인 리소스의 ID |
| ReviewName |  리뷰 이름 |
| CreatedDateTime | 검토를 만든 날짜/시간 |
| ReviewStartDate | 검토 시작일
| ReviewEndDate | 검토 종료일 |
| ReviewStatus | 검토 상태. 모든 검토 상태는 [여기](create-access-review.md)에서 액세스 검토 상태 표를 참조하세요. |
| OwnerId | 검토자 소유자 ID |
| OwnerName | 검토자 소유자 이름 |
| OwnerUPN | 검토자 소유자 사용자 계정 이름 |
| PrincipalId | 검토 중인 계정 ID |
| 주체 이름 | 검토 중인 계정 이름 |
| PrincipalUPN | 검토 중인 사용자의 계정 이름 |
| PrincipalType | 계정 유형. 옵션에는 사용자, 그룹 및 서비스 계정이 포함됩니다. |
| ReviewDate | 검토 날짜 |
| ReviewResult | 검토 결과에는 거부, 승인 및 검토 안 함이 포함됩니다. |
|근거 | 검토자가 제공한 검토 결과의 근거 |
| ReviewerId | 검토자 ID |
| ReviewerName | 검토자 이름 |
| ReviewerUPN | 검토자 사용자 계정 이름 |
| ReviewerEmailAddress | 검토자 이메일 주소 |
| AppliedByName | 검토 결과를 적용한 사용자의 이름 |
| AppliedByUPN | 검토 결과를 적용한 사용자의 사용자 계정 이름|
| AppliedByEmailAddress | 검토 결과를 적용한 사용자의 이메일 주소 |
| AppliedDate | 검토 결과가 적용된 날짜 |
| AccessRecommendation | 시스템 권장 사항에는 승인, 거부 및 정보 없음이 포함됩니다. |
|SubmissionResult | 검토 결과 제출 상태에는 적용됨, 적용되지 않음이 포함됩니다. |

## <a name="next-steps"></a>다음 단계
- [그룹 또는 애플리케이션에 대한 액세스 검토](perform-access-review.md)
- [그룹 또는 애플리케이션에 대한 액세스 직접 검토](review-your-access.md)
- [그룹 또는 애플리케이션의 액세스 검토 완료](complete-access-review.md)
