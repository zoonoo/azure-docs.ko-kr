---
title: Azure 이벤트 그리드 - 문제 해결 가이드
description: 이 문서에서는 오류 코드, 오류 메시지, 설명 및 권장 작업 목록을 제공합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645075"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Azure 이벤트 그리드 오류 문제 해결
이 문제 해결 가이드는 Azure Event 오류 코드, 오류 메시지, 설명 및 이러한 오류를 받을 때 수행해야 하는 권장 작업 목록을 제공합니다. 

## <a name="error-code-400"></a>오류 코드: 400
| 오류 코드 | 오류 메시지 | 설명 | 권장 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | 주제 이름은 길이가 3~50자여야 합니다. | 사용자 지정 토픽 이름 길이는 길이가 3~50자여야 합니다. 주제 이름에는 영숫자, 숫자 및 '-' 문자만 허용됩니다. 또한 이름은 다음 예약된 단어로 시작해서는 안 됩니다. <ul><li>Microsoft</li><li>EventGrid</li><li>시스템</li></ul> | 토픽 이름 요구 사항을 준수하는 다른 주제 이름을 선택합니다. |
| HttpStatusCode.BadRequest<br/>400 | 도메인 이름은 길이가 3~50자여야 합니다. | 도메인 이름 길이는 길이가 3자에서 50자 사이여야 합니다. 주제 이름에는 영숫자, 숫자 및 '-' 문자만 허용됩니다. 또한 이름은 다음 예약된 단어로 시작해서는 안 됩니다.<ul><li>Microsoft</li><li>EventGrid</li><li>시스템</li> | 도메인 이름 요구 사항을 준수하는 다른 도메인 이름을 선택합니다. |
| HttpStatusCode.BadRequest<br/>400 | 유효 기간이 잘못되었습니다. | 이벤트 구독의 만료 시간에 따라 이벤트 구독이 사용 중지되는 시기가 결정됩니다. 이 값은 나중에 유효한 DateTime 값이어야 합니다.| 유효한 DateTime 형식으로 이벤트 구독 만료 시간을 확인하고 나중에 설정해야 합니다. |

## <a name="error-code-409"></a>오류 코드: 409
| 오류 코드 | 오류 메시지 | 설명 | 권장 작업 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.충돌 <br/>409 | 지정된 이름의 토픽이 이미 있습니다. 다른 주제 이름을 선택합니다.   | 올바른 게시 작업을 수행하려면 사용자 지정 항목 이름이 단일 Azure 리전에서 고유해야 합니다. 다른 Azure 지역에서 동일한 이름을 사용할 수 있습니다. | 토픽의 다른 이름을 선택합니다. |
| HttpStatusCode.충돌 <br/> 409 | 지정된 도메인이 이미 있습니다. 다른 도메인 이름을 선택합니다. | 올바른 게시 작업을 수행하려면 도메인 이름이 단일 Azure 리전에서 고유해야 합니다. 다른 Azure 지역에서 동일한 이름을 사용할 수 있습니다. | 도메인의 다른 이름을 선택합니다. |
| HttpStatusCode.충돌<br/>409 | 할당량 한도에 도달했습니다. 이러한 제한에 대한 자세한 내용은 [Azure 이벤트 그리드 제한을](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)참조하십시오.  | 각 Azure 구독에는 사용할 수 있는 Azure 이벤트 그리드 리소스 수에 제한이 있습니다. 이 할당량의 일부 또는 전부를 초과했으며 더 이상 리소스를 만들 수 없습니다. |    현재 리소스 사용량을 확인하고 필요하지 않은 리소스를 삭제합니다. 할당량을 늘려야 하는 경우 필요한 정확한 [aeg@microsoft.com](mailto:aeg@microsoft.com) 리소스 수로 이메일을 보냅니다. |


## <a name="next-steps"></a>다음 단계
도움이 더 필요한 경우 [스택 오버플로 포럼에](https://stackoverflow.com/questions/tagged/azure-eventgrid) 문제를 게시하거나 [지원 티켓을](https://azure.microsoft.com/support/options/)엽니다. 
