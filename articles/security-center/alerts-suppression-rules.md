---
title: 경고 비 표시 규칙을 사용 하 여 Azure Security Center에서 가양성 또는 기타 원치 않는 보안 경고를 표시 하지 않습니다.
description: 이 문서에서는 Azure Security Center의 비 표시 규칙을 사용 하 여 원치 않는 보안 경고를 숨기는 방법을 설명 합니다.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 05/04/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: 5a5d6a96bd73a67c2611e7f334eba3daeda031ba
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007701"
---
# <a name="suppressing-alerts-from-azure-security-centers-threat-protection"></a>Azure Security Center의 위협 방지에서 경고 표시 안 함

이 페이지에서는 경고 비 표시 규칙을 사용 하 여 Azure Security Center에서 가양성 또는 기타 원치 않는 보안 경고를 표시 하지 않는 방법을 설명 합니다.

## <a name="availability"></a>가용성

- 릴리스 상태: **미리 보기**
- 필수 역할: 보안 관리자 및 소유자가 규칙을 만들거나 삭제할 수 있습니다. 보안 읽기 권한자 및 읽기 권한자는 규칙을 볼 수 있습니다.
- 클라우드: 모두 (Global, 국내, 정부 및 소 버린)


## <a name="introduction-to-suppression-rules"></a>비 표시 규칙 소개

Azure Security Center의 위협 방지 구성 요소는 사용자 환경의 모든 영역에서 위협을 감지 하 고 보안 경고를 생성 합니다.

단일 경고가 흥미 나 관련이 없는 경우 수동으로 해제할 수 있습니다. 또는 비 표시 규칙 기능을 사용 하 여 나중에 유사한 경고를 자동으로 해제 합니다. 일반적으로 다음과 같은 경우에는 비 표시 규칙을 사용 합니다.

- 거짓 긍정으로 식별 한 경고 표시 안 함

- 너무 자주 트리거되는 경고를 표시 하지 않습니다.

비 표시 규칙은 경고를 자동으로 해제할 조건을 정의 합니다.

> [!CAUTION]
> 보안 경고를 표시 하지 않으면 Security Center의 위협 방지가 줄어듭니다. 비 표시 규칙의 잠재적인 영향을 신중 하 게 확인 하 고 시간에 따라 모니터링 해야 합니다.

[![경고 표시 안 함 옵션을 사용 하 여 보안 경고 페이지 Azure Security Center](media/alerts-suppression-rules/alerts-screen-with-options.png)](media/alerts-suppression-rules/alerts-screen-with-options.png#lightbox)

## <a name="creating-a-suppression-rule"></a>비 표시 규칙 만들기

원치 않는 보안 경고를 표시 하지 않는 규칙을 만드는 몇 가지 방법이 있습니다.

- 관리 그룹 수준에서 경고를 표시 하지 않으려면 Azure Policy를 사용 합니다.

- 구독 수준에서 경고를 표시 하지 않으려면 아래 설명 된 대로 Azure Portal 또는 REST API를 사용할 수 있습니다.

억제 규칙은 선택한 구독에서 이미 트리거된 경고만 해제할 수 있습니다.

Azure Portal에서 직접 규칙을 만들려면 다음을 수행 합니다.

1. Security Center의 보안 경고 페이지에서:

    - 더 이상 표시 하지 않을 특정 경고를 찾고, 경고에 대 한 줄임표 메뉴 (...)에서 비 표시 제거 **규칙 만들기**를 선택 합니다.

        [![* * 비 표시 규칙 만들기 * * 옵션](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox)

    - 또는 페이지 맨 위에 있는 **억제 규칙** 링크를 선택 하 고, 제거 규칙 페이지에서 **새로 만들기 비 표시 규칙 만들기**를 선택 합니다.

        ![새 비 표시 규칙 만들기 * * 단추](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. 새 비 표시 규칙 창에서 새 규칙의 세부 정보를 입력 합니다.

    - 규칙은 **모든 리소스** 에 대 한 경고를 해제할 수 있으므로 나중에 이와 같은 경고를 얻지 못합니다. 
    
    - 규칙은 특정 IP 주소, 프로세스 이름, 사용자 계정, Azure 리소스 또는 위치와 관련 된 **특정 조건에 대 한** 경고를 해제할 수 있습니다.

    > [!TIP]
    > 특정 경고에서 새 규칙 페이지를 열면 새 규칙에 경고 및 구독이 자동으로 구성 됩니다. **새 비 표시 규칙 만들기** 링크를 사용 하는 경우 선택한 구독은 포털의 현재 필터와 일치 합니다.

    [![억제 규칙 만들기 창](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)

1. 규칙의 세부 정보를 입력 합니다.

    - **이름** -규칙의 이름입니다. 규칙 이름은 문자 또는 숫자로 시작 해야 하 고, 2 ~ 007e; 50 자 여야 하며, 대시 (-) 또는 밑줄 (_)이 아닌 기호를 포함 하지 않아야 합니다. 
    - **상태** -사용 또는 사용 안 함.
    - **이유** -기본 제공 이유 중 하나를 선택 하거나, 요구 사항에 맞지 않는 경우 ' 기타 '를 선택 합니다.
    - **만료 날짜** -규칙의 종료 날짜 및 시간입니다. 최대 6 개월 동안 규칙을 실행할 수 있습니다.

1. 필요에 따라 **시뮬레이트** 단추를 사용 하 여 규칙을 테스트 하 여이 규칙이 활성화 된 경우 해제 된 경고 수를 확인 합니다.

1. 규칙을 저장합니다. 

## <a name="editing-suppression-rules"></a>비 표시 규칙 편집

만든 규칙을 편집 하려면 비 표시 규칙 페이지를 사용 합니다.

1. Security Center의 보안 경고 페이지에서 페이지 맨 위에 있는 **비 표시 규칙** 링크를 선택 합니다.

1. 제거 규칙 페이지가 열리고 현재 선택 된 구독에 따라 사용 가능한 모든 규칙이 나열 됩니다. 

    [![억제 규칙 목록](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. 단일 규칙을 편집 하려면 규칙에 대 한 줄임표 메뉴 (...)를 열고 **편집**을 선택 합니다.

1. 필요한 변경을 수행 하 고 **적용**을 선택 합니다. 

## <a name="deleting-suppression-rules"></a>비 표시 규칙 삭제

만든 규칙을 하나 이상 삭제 하려면 비 표시 규칙 페이지를 사용 합니다.

1. Security Center의 보안 경고 페이지에서 페이지 맨 위에 있는 **비 표시 규칙** 링크를 선택 합니다.

1. 제거 규칙 페이지가 열리고 현재 선택 된 구독에 따라 사용 가능한 모든 규칙이 나열 됩니다. 

1. 단일 규칙을 삭제 하려면 규칙에 대 한 줄임표 메뉴 (...)를 열고 **삭제**를 선택 합니다.

1. 여러 규칙을 삭제 하려면 삭제할 규칙의 확인란을 선택 하 고 **삭제**를 선택 합니다.

    ![하나 이상의 비 표시 규칙 삭제](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="viewing-alerts-that-have-been-suppressed"></a>억제 된 경고 보기

활성화 된 비 표시 규칙과 **일치 하는**경고는 계속 생성 되지만 해당 상태는 해제 됨으로 설정 됩니다. Azure Portal에서 상태를 볼 수 있지만 Security Center 보안 경고에 액세스할 수 있습니다. 

> [!TIP]
> [Azure 센티널](https://azure.microsoft.com/services/azure-sentinel/) 은 억제 되는 경고에 대 한 인시던트를 만들지 않습니다. 다른 SIEMs의 경우 경고 상태 (' 해제 됨 ')를 사용 하 여 표시 되지 않은 경고를 필터링 할 수 있습니다.

Security Center의 필터를 사용 하 여 규칙에 의해 해제 된 경고를 볼 수 있습니다.

* Security Center의 보안 경고 페이지에서 필터 옵션을 열고 해제 됨을 **선택 합니다**.  

   [![해제 한 경고 보기](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="using-the-api-to-create-and-manage-suppression-rules"></a>API를 사용 하 여 비 표시 규칙 만들기 및 관리

Security Center의 REST API를 통해 경고 비 표시 규칙을 만들거나 보거나 삭제할 수 있습니다. 

REST API의 비 표시 규칙에 대 한 관련 HTTP 메서드는 다음과 같습니다.

- **PUT**: 지정 된 구독에서 비 표시 규칙을 만들거나 업데이트 합니다.

- **가져오기**:

    - 지정 된 구독에 대해 구성 된 모든 규칙을 나열 합니다. 이 메서드는 해당 하는 규칙의 배열을 반환 합니다.

    - 지정 된 구독에 대 한 특정 규칙의 세부 정보를 가져옵니다. 이 메서드는 억제 규칙 하나를 반환 합니다.

    - 디자인 단계에서 억제 규칙의 영향을 시뮬레이션 합니다. 이 호출은 규칙이 활성화 된 경우 해제 된 기존 경고를 식별 합니다.

- **DELETE**: 기존 규칙을 삭제 하지만 이미 해제 된 경고의 상태는 변경 하지 않습니다.

전체 세부 정보 및 사용 예제는 [API 설명서](https://docs.microsoft.com/api/securitycenter/)를 참조 하세요. 


## <a name="next-steps"></a>다음 단계

이 문서에서는 원치 않는 경고를 자동으로 해제 하는 Azure Security Center의 비 표시 규칙에 대해 설명 했습니다.

Azure Security Center의 보안 경고에 대 한 자세한 내용은 다음 페이지를 참조 하세요.

- [보안 경고 및 의도 kill 체인](alerts-reference.md) -Azure Security Center의 위협 방지 모듈에서 볼 수 있는 보안 경고에 대 한 참조 가이드입니다.
- [Azure Security Center에서 위협 방지](threat-protection.md) -Azure Security Center의 위협 방지 모듈을 통해 모니터링 되는 환경의 여러 측면에 대 한 설명입니다.