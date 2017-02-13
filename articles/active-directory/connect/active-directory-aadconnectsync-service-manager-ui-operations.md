---
title: "Azure AD Connect 동기화: Synchronization Service Manager UI | Microsoft Docs"
description: "Azure AD Connect의 Synchronization Service Manager에 있는 작업 탭을 이해합니다."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 610dab0af17f927d86b677f647acd0dfe2569583
ms.openlocfilehash: 53b98aaf67b874b0af7d0e94e29bcbe23fc6fc5b


---
# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Azure AD Connect 동기화: Synchronization Service Manager

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

작업 탭에서는 최근 작업의 결과를 보여 줍니다. 이 탭은 문제를 이해하고 해결하는 데 핵심적인 요소입니다.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>작업 탭에 표시되는 정보를 이해합니다.
위쪽 절반에 모든 실행이 연대별 순서로 표시됩니다. 기본적으로 작업 로그는 지난 7일에 대한 정보를 유지하지만 이 설정은 [스케줄러](active-directory-aadconnectsync-feature-scheduler.md)에 따라 변동될 수 있습니다. 성공 상태를 표시하지 않는 실행을 찾아보려고 합니다. 헤더를 클릭하여 정렬을 변경할 수 있습니다.

**상태** 열은 가장 중요한 정보이며 실행에 대해 가장 심각한 문제를 보여 줍니다. 다음은 조사할 우선 순위에 따른 가장 일반적인 상태에 대한 간단한 요약입니다(여기서 *는 여러 가능한 오류 문자열을 나타냄).

| 상태 | 주석 |
| --- | --- |
| stopped-* |실행을 완료하지 못했습니다. 예를 들어 원격 시스템이 다운되어 연결할 수 없는 경우입니다. |
| stopped-error-limit |5000개보다 많은 오류가 있습니다. 많은 오류로 인해 실행이 자동으로 중지되었습니다. |
| completed-\*-errors |실행이 완료되었지만 조사해야 할 오류가 있습니다(5,000개 미만). |
| completed-\*-warnings |실행이 완료되었지만 일부 데이터가 예상된 상태가 아닙니다. 오류가 발생하는 경우 이 메시지는 일반적으로 증상일 뿐입니다. 오류를 해결할 때까지 경고를 조사하지 않습니다. |
| 성공 |문제가 없습니다. |

행을 선택하면 해당 실행의 세부 정보가 표시되도록 아래쪽이 업데이트됩니다. 아래 맨 왼쪽에 **#단계**라는 목록이 있을 수 있습니다. 이 목록은 각 도메인을 단계로 나타내는 포리스트에 여러 도메인이 있는 경우에만 표시됩니다. 도메인 이름은 **파티션**머리글 아래에서 찾을 수 있습니다. **동기화 통계**아래에서 처리된 변경 내용의 수에 대한 자세한 정보를 찾을 수 있습니다. 링크를 클릭하여 변경된 개체의 목록을 가져올 수 있습니다. 오류가 있는 개체가 있으면 해당 오류는 **동기화 오류**아래에 표시됩니다.

## <a name="troubleshoot-errors-in-operations-tab"></a>작업 탭에서 오류 문제 해결
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
오류가 발생하는 경우 오류 개체 및 오류 자체가 모두 자세한 정보를 제공하는 링크입니다.

오류 문자열(그림의**sync-rule-error-function-triggered** )을 클릭하여 시작합니다. 먼저 개체의 개요가 나타납니다. 실제 오류를 확인하려면 **스택 추적**단추를 클릭합니다. 이 추적은 오류에 대한 디버그 수준 정보를 제공합니다.

**팁:** **호출 스택 정보** 상자에서 마우스 오른쪽 단추를 클릭하고 **모두 선택**, **복사**를 차례로 선택합니다. 그런 다음 스택을 복사하고 메모장 등 즐겨 사용하는 편집기에서 오류를 볼 수 있습니다.

* 오류가 **SyncRulesEngine**에서 발생한 경우 호출 스택 정보가 개체에 대한 모든 특성의 목록을 먼저 제공합니다. **InnerException =>** 머리글이 표시될 때까지 아래로 스크롤합니다.  
  ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
  다음 줄에서는 오류를 보여 줍니다. 위의 그림에서 오류는 생성된 사용자 지정 동기화 규칙 Fabrikam에서 발생했습니다.

오류 자체가 충분한 정보를 제공하지 않는 경우 이제 데이터 자체에 대해 살펴봅니다. 개체 식별자가 포함된 링크를 클릭하고 [시스템을 통해 개체 및 해당 데이터를 따릅니다](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.



<!--HONumber=Jan17_HO2-->


