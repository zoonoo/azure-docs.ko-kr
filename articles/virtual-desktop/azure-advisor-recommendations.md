---
title: Azure Advisor Azure Virtual Desktop 연습 - Azure
description: Azure Virtual Desktop에 대한 Azure Advisor 권장 사항을 해결하는 방법입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b8bc514f495a46e76ad882720c918824aab0aaac
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745484"
---
# <a name="how-to-resolve-azure-advisor-recommendations"></a>Azure Advisor 권장 사항 해결 방법

이 문서에서는 Azure Advisor for Azure Virtual Desktop에 표시되는 권장 사항을 해결할 수 있는 방법을 설명합니다.

## <a name="no-validation-environment-enabled"></a>“사용 설정된 유효성 검사 환경이 없음”

>[!div class="mx-imgBorder"]
>![Azure Advisor Operational Excellence 페이지 스크린샷. "사용 설정된 유효성 검사 환경이 없음" 권장 사항은 빨간색으로 강조 표시됩니다.](media/no-validation-environment.png)

이 권장 사항은 Operational Excellence 아래에 나타납니다. 권장 사항에 다음과 같은 경고 메시지가 표시되어야 합니다.

"이 구독에서 사용 설정된 유효성 검사 환경이 없습니다. 호스트 풀을 만들 때 속성 탭에서 "유효성 검사 환경"에 대해 **아니요** 를 선택했습니다. Azure Virtual Desktop 서비스 배포를 통해 비즈니스 연속성을 보장하려면 잠재적인 문제를 검사할 수 있는 유효성 검사 환경을 갖춘 호스트 풀이 하나 이상 있어야 합니다.”

호스트 풀 중 하나에서 유효성 검사 환경을 사용하도록 설정하면 이 경고 메시지가 나타나지 않도록 할 수 있습니다.

유효성 검사 환경을 사용하도록 설정하려면 다음을 수행합니다.

1. Azure Portal 홈페이지로 이동하여 변경할 호스트 풀을 선택합니다.

2. 그런 다음, 프로덕션 환경에서 유효성 검사 환경으로 변경하려는 호스트 풀을 선택합니다.

3. 호스트 풀의 왼쪽 열에서 **속성** 을 선택합니다. 그런 다음, "유효성 검사 환경"이 표시될 때까지 아래로 스크롤합니다. **예** 를 선택한 다음, **적용** 을 선택합니다.

>[!div class="mx-imgBorder"]
>![속성 메뉴 스크린샷. "유효성 검사 환경"이 빨간색으로 강조 표시되고 "예" 단추가 선택됩니다.](media/validation-yes.png)

이러한 변경으로 인해 경고가 즉시 나타나지 않도록 할 수는 없지만, 결국에는 사라집니다. Azure Advisor는 하루에 두 번 업데이트합니다. 그때까지는 권장 사항을 수동으로 연기하거나 해제할 수 있습니다. 권장 사항이 자체적으로 나타나지 않도록 하는 것이 좋습니다. 이렇게 하면 설정이 변경될 때 문제가 발생하는 경우 Azure Advisor가 알려줄 수 있습니다.

## <a name="not-enough-production-non-validation-environments-enabled"></a>“사용 설정된 프로덕션(비유효성 검사) 환경이 충분하지 않음”

이 권장 사항은 Operational Excellence 아래에 나타납니다.

이 권장 사항에 대한 경고 메시지는 다음과 같은 이유 중 하나로 표시됩니다.

- 유효성 검사 환경에 호스트 풀이 너무 많습니다.
- 프로덕션 호스트 풀이 없습니다.

유효성 검사 환경에서 사용자의 호스트 풀이 절반 미만인 것이 좋습니다.

이 문제를 해결하려면 다음을 수행합니다.

1. Azure Portal 홈페이지로 이동합니다.

2. 유효성 검사에서 프로덕션으로 변경하려는 호스트 풀을 선택합니다.

3. 호스트 풀에서 화면 오른쪽에 있는 열에서 **속성** 탭을 선택합니다. 그런 다음, "유효성 검사 환경"이 표시될 때까지 아래로 스크롤합니다. **아니요** 를 선택한 다음, **적용** 을 선택합니다.

>[!div class="mx-imgBorder"]
>![속성 메뉴 스크린샷. "유효성 검사 환경"이 빨간색으로 강조 표시되고 "아니요" 단추가 선택됩니다.](media/validation-no.png)

이러한 변경으로 인해 경고가 즉시 나타나지 않도록 할 수는 없지만, 결국에는 사라집니다. Azure Advisor는 하루에 두 번 업데이트합니다. 그때까지는 권장 사항을 수동으로 연기하거나 해제할 수 있습니다. 권장 사항이 자체적으로 나타나지 않도록 하는 것이 좋습니다. 이렇게 하면 설정이 변경될 때 문제가 발생하는 경우 Azure Advisor가 알려줄 수 있습니다.

## <a name="not-enough-links-are-unblocked-to-successfully-implement-your-vm"></a>"VM을 성공적으로 구현하기 위해 차단 해제된 링크가 충분하지 않음"

이 권장 사항은 Operational Excellence 아래에 나타납니다.

VM(가상 머신)이 제대로 작동하는지 확인하려면 특정 URL을 차단 해제해야 합니다. [안전 URL 목록](safe-url-list.md)에서 목록을 볼 수 있습니다. URL이 차단 해제되지 않으면 VM이 제대로 작동하지 않습니다.

이 권장 사항을 해결하려면 [안전 URL 목록](safe-url-list.md)에서 모든 URL의 차단을 해제해야 합니다. 서비스 태그 또는 FQDN 태그를 사용하여 URL의 차단을 해제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

일반적인 문제를 해결하는 방법에 대한 자세한 가이드를 찾는 경우 [ Virtual Desktop에 대한 문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조하세요.
