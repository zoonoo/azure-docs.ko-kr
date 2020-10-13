---
title: Azure AD 자격 관리에서 액세스 패키지에 대 한 승인 설정 변경-Azure Active Directory
description: Azure Active Directory 자격 관리에서 액세스 패키지에 대 한 승인 및 요청자 정보 설정을 변경 하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e36b2d4576b43544bec89efd326363344b35be9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997084"
---
# <a name="change-approval-and-requestor-information-preview-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지에 대 한 승인 및 요청자 정보 (미리 보기) 설정 변경

액세스 패키지 관리자는 기존 정책을 편집 하거나 새 정책을 추가 하 여 언제 든 지 액세스 패키지에 대 한 승인 및 요청자 정보 설정을 변경할 수 있습니다.

이 문서에서는 기존 액세스 패키지의 승인 및 요청자 정보 설정을 변경 하는 방법을 설명 합니다.

## <a name="approval"></a>승인

승인 섹션에서 사용자가이 액세스 패키지를 요청할 때 승인이 필요한 지 여부를 지정 합니다. 승인 설정은 다음과 같은 방식으로 작동 합니다.

- 선택한 승인자 또는 대체 승인자 중 하나만 단일 단계 승인 요청을 승인 해야 합니다. 
- 각 단계에서 선택한 승인자 중 하나만 2 단계 승인 요청을 승인 해야 합니다.
- 승인자는 정책을 통해 액세스를 관리 하는 사람에 따라 관리자, 내부 스폰서 또는 외부 스폰서 일 수 있습니다.
- 단일 또는 2 단계 승인에는 선택한 모든 승인자의 승인이 필요 하지 않습니다.
- 승인 결정은 먼저 요청을 검토 하는 승인자에 따라 결정 됩니다.

요청 정책에 승인자를 추가 하는 방법에 대 한 데모를 보려면 다음 비디오를 시청 하세요.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

요청 정책에 다단계 승인을 추가 하는 방법에 대 한 데모를 보려면 다음 비디오를 시청 하세요.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]


## <a name="change-approval-settings-of-an-existing-access-package"></a>기존 액세스 패키지의 승인 설정 변경

액세스 패키지에 대 한 요청에 대 한 승인 설정을 지정 하려면 다음 단계를 수행 합니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 편집할 정책을 선택 하거나 액세스 패키지에 새 정책을 추가 합니다.
    1. 정책을 클릭 한 다음 새 정책을 만들려면 **정책 추가** **를 클릭 합니다** .
    1. 편집 하려는 정책을 클릭 한 다음 **편집**을 클릭 합니다.

1. **요청** 탭으로 이동 합니다.

1. 선택한 사용자의 요청에 대 한 승인을 요청 하려면 **승인 필요** 를 **예**로 설정 합니다. 또는 요청을 자동으로 승인 하려면 토글을 **아니요**로 설정 합니다.

1. 사용자가 액세스 패키지를 요청 하는 근거를 제공 하도록 요구 하려면 **요청자 사유 필요** 를 **예**로 설정 합니다.
    
1. 이제 요청에 단일 또는 2 단계 승인이 필요한 지 확인 합니다. 단일 단계 승인에 대해 **단계 수** 를 **1** 로 설정 하거나 2 단계 승인의 경우 토글을 **2** 로 설정 합니다.

    ![액세스 패키지-요청-승인 설정](./media/entitlement-management-access-package-approval-policy/approval.png)


다음 단계를 사용 하 여 필요한 단계 수를 선택한 후 승인자를 추가 합니다. 

### <a name="single-stage-approval"></a>단일 단계 승인

1. **첫 번째 승인자**를 추가 합니다.
    
    디렉터리의 사용자에 대 한 액세스를 제어 하도록 정책이 설정 된 경우 **관리자를 승인자로**선택할 수 있습니다. 또는 드롭다운 메뉴에서 특정 승인자 선택을 선택한 후 **승인자 추가** 를 클릭 하 여 특정 사용자를 추가 합니다.
    
    ![액세스 패키지-요청-디렉터리 우선 승인자의 사용자 용](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    이 정책이 디렉터리에 없는 사용자에 대 한 액세스를 제어 하도록 설정 된 경우 **외부 스폰서** 또는 **내부 스폰서**를 선택할 수 있습니다. 또는 특정 승인자 선택에서 승인자 또는 그룹 **추가** 를 클릭 하 여 특정 사용자를 추가 합니다.
    
    ![액세스 패키지-요청-사용자에 대 한 디렉터리 우선 승인자](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. 첫 번째 승인자로 **관리자** 를 선택한 경우 대체 ( **fallback) 추가** 를 클릭 하 여 대체 승인자가 될 디렉터리에서 하나 이상의 사용자 또는 그룹을 선택 합니다. 자격 관리 승인자는 액세스를 요청 하는 사용자에 대 한 관리자를 찾을 수 없는 경우 요청을 받습니다.

    관리자는 **관리자** 특성을 사용 하 여 권한 관리에서 찾을 수 있습니다. 특성은 Azure AD의 사용자 프로필에 있습니다. 자세한 내용은 [Azure Active Directory를 사용 하 여 사용자의 프로필 정보 추가 또는 업데이트](../fundamentals/active-directory-users-profile-azure-portal.md)를 참조 하세요.

1. **특정 승인자 선택**을 선택한 경우 승인자 **추가** 를 클릭 하 여 승인자가 될 디렉터리에서 하나 이상의 사용자 또는 그룹을 선택 합니다.

1. 결정 아래의 상자에서 **일 수를 결정 해야 합니다**. 상자에서 승인자가이 액세스 패키지에 대 한 요청을 검토 해야 하는 일 수를 지정 합니다.

    이 기간 내에 승인 되지 않은 요청은 자동으로 거부 됩니다. 사용자는 액세스 패키지에 대 한 다른 요청을 제출 해야 합니다.

1. 승인자가 결정에 대 한 근거를 제공 하도록 요구 하려면 승인자 사유 필요를 **예**로 설정 합니다.

    다른 승인자 및 요청자에 게 근거를 표시 합니다.

### <a name="2-stage-approval"></a>2 단계 승인

2 단계 승인을 선택한 경우 두 번째 승인자를 추가 해야 합니다.

1. **두 번째 승인자**를 추가 합니다. 
    
    사용자가 디렉터리에 있는 경우 특정 승인자 선택에서 **승인자 추가** 를 클릭 하 여 두 번째 승인자로 특정 사용자를 추가 합니다.

    ![액세스 패키지-요청-디렉터리-초 승인자의 사용자](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    사용자가 디렉터리에 없는 경우 **내부 스폰서** 또는 **외부 스폰서** 를 두 번째 승인자로 선택 합니다. 승인자를 선택한 후 대체 승인자를 추가 합니다.

    ![액세스 패키지-요청-사용자에 대 한 디렉터리-초 승인자](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. 두 번째 승인자가 결정 하는 상자에서 요청을 승인 **해야**하는 일 수를 지정 합니다. 

1. 승인자 사유 필요를 **예** 또는 **아니요**로 설정 합니다.

### <a name="alternate-approvers"></a>대체 승인자

요청을 승인할 수 있는 첫 번째와 두 번째 승인자를 지정 하는 것과 유사 하 게 대체 승인자를 지정할 수 있습니다. 대체 승인자가 있으면 요청이 만료 되기 전에 승인 또는 거부 되는지 확인 하는 데 도움이 됩니다 (시간 제한). 2 단계 승인에 대해 첫 번째 승인자와 두 번째 승인자 인 대체 승인자를 나열할 수 있습니다. 

첫 번째 또는 두 번째 승인자가 요청을 승인 하거나 거부할 수 없는 경우 대체 승인자를 지정 하 여 보류 중인 요청을 정책 설정 중에 지정한 전달 일정에 따라 대체 승인자에 게 전달 합니다. 보류 중인 요청을 승인 하거나 거부 하는 전자 메일을 받습니다.

요청을 대체 승인자에 게 전달한 후에는 첫 번째 또는 두 번째 승인자가 요청을 승인 하거나 거부할 수 있습니다. 대체 승인자는 동일한 내 액세스 사이트를 사용 하 여 보류 중인 요청을 승인 하거나 거부 합니다.

승인자 및 대체 승인자가 될 사람 또는 사람 그룹을 나열할 수 있습니다. 첫 번째, 두 번째 및 대체 승인자가 될 여러 사용자 집합을 나열 하세요.
예를 들어 첫 번째 승인자로 Alice와 Bob을 나열 하는 경우 대체 승인자에 게 고 Dave를 나열 합니다. 다음 단계를 사용 하 여 액세스 패키지에 대체 승인자를 추가 합니다.

1. 첫 번째 승인자, 두 번째 승인자 또는 둘 다에서 **고급 요청 설정 표시**를 클릭 합니다.

    ![액세스 패키지-정책-고급 요청 설정 표시](./media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png)

1. **수행 된 작업이 없는 경우를 설정 하 고, 다른 승인자에 게 전달 하 시겠습니까?** **예**로 전환 합니다.

1. **대체 승인자 추가** 를 클릭 하 고 목록에서 대체 승인자를 선택 합니다.

    ![액세스 패키지-정책-대체 승인자 추가](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

1. **남은 승인자 (일 수) 후의 대체 승인자** 에서 요청을 승인 하거나 거부 해야 하는 일 수를 입력 합니다. 요청 기간 전에 요청을 승인 하거나 거부 한 승인자가 없는 경우 요청은 만료 됩니다 (시간 제한). 사용자는 액세스 패키지에 대 한 다른 요청을 제출 해야 합니다. 

    요청 기간이 반기에 도달 하 고 주 승인자의 결정이 최소 4 일 후에 제한 시간을 초과 하는 경우에만 요청을 대체 승인자에 게 전달할 수 있습니다. 요청 제한 시간이 3 보다 작거나 같으면 요청을 대체 승인자에 게 전달할 시간이 충분 하지 않습니다. 이 예제에서 요청 기간은 14 일입니다. 따라서 요청 기간은 7 일에 반기에 도달 합니다. 따라서 요청을 8 일 이전에 전달할 수 없습니다. 또한 요청 기간의 마지막 날에 요청을 전달할 수 없습니다. 따라서이 예제에서는 최신 요청을 전달할 수 있습니다.

## <a name="enable-requests"></a>요청 사용

1. 요청 정책에 있는 사용자가 액세스 패키지를 즉시 사용할 수 있게 하려면 사용 설정/해제를 **예**로 이동 합니다.

    액세스 패키지 만들기를 완료 한 후에는 나중에 언제 든 지 사용할 수 있습니다.

    **없음 (관리자 직접 할당에만 해당)** 을 선택 하 고 사용을 **아니요**로 설정 하면 관리자가이 액세스 패키지를 직접 할당할 수 없습니다.

    ![액세스 패키지-정책-정책 설정 사용](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. **다음**을 클릭합니다.

## <a name="collect-additional-requestor-information-for-approval-preview"></a>승인 (미리 보기)에 대 한 추가 요청자 정보 수집

사용자가 올바른 액세스 패키지에 액세스할 수 있도록 하기 위해 요청 시 요청자에 게 사용자 지정 텍스트 필드 또는 여러 선택 질문에 응답 하도록 요구할 수 있습니다. 정책 당 20 개의 질문이 제한 되며 여러 선택 질문에 대 한 답변은 25 개로 제한 됩니다. 그런 다음 결정을 내리는 데 도움이 되는 승인자에 게 질문을 표시 합니다.

1. **요청자 정보** 탭으로 이동 하 여 **질문** 하위 탭을 클릭 합니다.
 
1. **질문** 상자의 질문에 대해 요청자에 게 표시 문자열이 라고도 하는 요청을 입력 합니다.

    ![액세스 패키지-정책-요청자 정보 사용 설정](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. 액세스 패키지에 액세스 해야 하는 사용자의 커뮤니티에 모두 공통 된 기본 언어가 없는 경우 myaccess.microsoft.com에서 액세스를 요청 하는 사용자의 환경을 개선할 수 있습니다. 환경을 개선 하기 위해 다른 언어에 대 한 대체 표시 문자열을 제공할 수 있습니다. 예를 들어 사용자의 웹 브라우저가 스페인어로 설정 되어 있고 스페인어 표시 문자열이 구성 된 경우 해당 문자열은 요청 하는 사용자에 게 표시 됩니다. 요청에 대 한 지역화를 구성 하려면 **지역화 추가**를 클릭 합니다.
    1. **지역화 추가 질문** 창에서 질문을 지역화할 언어에 대 한 **언어 코드** 를 선택 합니다.
    1. 구성한 언어로 **지역화 된 텍스트** 상자에 질문을 입력 합니다.
    1. 필요한 모든 지역화를 추가 했으면 **저장**을 클릭 합니다.

    ![액세스 패키지-정책-지역화 된 텍스트 구성](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. 요청자에 게 답변할 **대답 형식을** 선택 합니다. 응답 형식에는 *짧은 텍스트*, *다중 선택*및 *긴 텍스트가*포함 됩니다.
 
    ![액세스 패키지-정책-여러 선택 응답 형식 보기 및 편집을 선택 합니다.](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. 여러 항목을 선택 하는 경우 **보기 및 편집** 단추를 클릭 하 여 응답 옵션을 구성 합니다.
    1. 보기 및 편집을 선택 하면 **보기/편집 질문** 창이 열립니다.
    1. 응답 **값** 상자에서 질문에 대답할 때 요청자에 게 제공 하려는 응답 옵션을 입력 합니다.
    1. 필요한 만큼 응답을 입력 한 다음 **저장**을 클릭 합니다.
    
    ![액세스 패키지-정책-여러 선택 옵션 입력](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. 액세스 패키지에 대 한 액세스를 요청할 때 요청자에 게이 질문에 대 한 대답을 요구 하려면 **필요**에서 확인란을 클릭 합니다.

1. 필요에 따라 나머지 탭 (예: 수명 주기)을 입력 합니다.

액세스 패키지 정책에서 요청자 정보를 구성한 후에서 요청자의 질문에 대 한 응답을 볼 수 있습니다. 요청자 정보를 보는 방법에 대 한 지침은 [요청자의 질문에 대 한 답변 보기 (미리 보기)](entitlement-management-request-approve.md#view-requestors-answers-to-questions-preview)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
- [액세스 패키지의 수명 주기 설정 변경](entitlement-management-access-package-lifecycle-policy.md)
- [액세스 패키지에 대 한 요청 보기](entitlement-management-access-package-requests.md)
