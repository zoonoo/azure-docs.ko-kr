---
title: Azure Event Grid - 문제 해결 가이드
description: 이 문서에서는 오류 코드, 오류 메시지, 설명 및 권장되는 작업에 대한 목록을 제공합니다.
ms.topic: conceptual
ms.date: 04/09/2021
ms.openlocfilehash: 4647039f67cf84a5471dc7b3f38ca754ca9eecb0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310872"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Azure Event Grid 오류 해결
이 문제 해결 가이드에서는 다음 정보를 제공합니다. 

- Azure Event Grid 오류 코드
- 오류 메시지
- 오류에 대한 설명
- 이러한 오류 코드를 수신하는 경우 권장되는 조치입니다. 

## <a name="error-code-400"></a>오류 코드: 400
| 오류 코드 | 오류 메시지 | Description | 권장 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | 토픽 이름은 3~50자여야 합니다. | 사용자 지정 토픽 이름의 길이는 3~50자여야 합니다. 토픽 이름에는 영어, 숫자 및 '-' 문자만 사용할 수 있습니다. 또한 이름은 다음과 같은 문자로 시작되면 안 됩니다. <ul><li>Microsoft-</li><li>EventGrid-</li><li>System-</li></ul> | 토픽 이름 요구 사항을 준수하는 토픽 이름을 선택합니다. |
| HttpStatusCode.BadRequest<br/>400 | 도메인 이름은 3~50자여야 합니다. | 도메인 이름의 길이는 3~50자여야 합니다. 도메인 이름에는 영어, 숫자 및 '-' 문자만 사용할 수 있습니다. 또한 이름은 다음과 같은 문자로 시작되면 안 됩니다.<ul><li>Microsoft-</li><li>EventGrid-</li><li>System-</li> | 도메인 이름 요구 사항을 준수하는 도메인 이름을 선택합니다. |
| HttpStatusCode.BadRequest<br/>400 | 잘못된 만료 시간 | 이벤트 구독의 만료 시간은 이벤트 구독이 사용 중지되는 시기를 결정합니다. 이 값은 미래에 유효한 DateTime 값이어야 합니다.| 이벤트 구독 만료 시간이 유효한 DateTime 형식이며 미래로 설정되어 있는지 확인합니다. |

## <a name="error-code-409"></a>오류 코드: 409
| 오류 코드 | 오류 메시지 | Description | 권장 조치 |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Conflict <br/>409 | 지정된 이름의 토픽이 이미 있습니다. 다른 토픽 이름을 선택하세요.   | 올바른 게시 작업을 보장하려면 단일 Azure 지역에서 사용자 지정 토픽 이름이 고유해야 합니다. 다른 Azure 지역에서는 동일한 이름을 사용할 수 있습니다. | 다른 토픽 이름을 선택하세요. |
| HttpStatusCode.Conflict <br/> 409 | 지정된 이름의 도메인이 이미 있습니다. 다른 도메인 이름을 선택하세요. | 올바른 게시 작업을 보장하려면 단일 Azure 지역에서 도메인 이름이 고유해야 합니다. 다른 Azure 지역에서는 동일한 이름을 사용할 수 있습니다. | 다른 도메인 이름을 선택하세요. |
| HttpStatusCode.Conflict<br/>409 | 할당량 한도에 도달했습니다. 한도에 대한 자세한 내용은 [Azure Event Grid 한도](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits)를 참조하세요.  | 각 Azure 구독에는 사용할 수 있는 Azure Event Grid 리소스 수가 제한되어 있습니다. 이 할당량의 일부 또는 전부를 초과하여 더 이상 리소스를 만들 수 없습니다. | 현재 리소스 사용량을 확인하고 필요하지 않은 모든 리소스를 삭제합니다. 리소스를 삭제할 수 없는 경우 다른 Azure 구독을 만들고 해당 구독에 Event Grid 리소스를 만듭니다. |

## <a name="error-code-403"></a>오류 코드: 403

| 오류 코드 | 오류 메시지 | Description | 권장 조치 |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode.Forbidden <br/>403 | IpAddress 필터링 규칙으로 인해 {IpAddress} 클라이언트에서 {Topic/Domain}에 게시하는 것이 거부되었습니다. | 토픽 또는 도메인에 IP 방화벽 규칙이 구성되어 있고, 구성된 IP 주소에만 액세스할 수 있습니다. | IP 방화벽 규칙에 IP 주소를 추가하려면 [IP 방화벽 구성](configure-firewall.md)을 참조하세요. |
| HttpStatusCode.Forbidden <br/> 403 | 요청이 프라이빗 엔드포인트에서 발생하고 리소스에 대해 일치하는 프라이빗 엔드포인트 연결을 찾을 수 없어 클라이언트에서 {Topic/Domain}에 게시하는 것이 거부되었습니다. | 토픽 또는 도메인에 프라이빗 엔드포인트가 있고, 구성되지 않았거나 승인되지 않은 프라이빗 엔드포인트에서 게시 요청이 있었습니다. | 토픽/도메인에 대한 프라이빗 엔드포인트를 구성합니다. [프라이빗 엔드포인트 구성](configure-private-endpoints.md) |

또한 웹후크가 Azure Application Gateway 또는 Web Application Firewall 뒤에 있는지 확인합니다. 이 경우 다음 방화벽 규칙을 사용하지 않도록 설정하고 HTTP POST를 다시 수행합니다.

- 920300(Accept 헤더가 누락된 요청)
- 942430(제한된 SQL 문자 이상 감지(인수): 특수 문자 # 초과(12))
- 920230(여러 URL 인코딩 감지)
- 942130(SQL 삽입 공격: SQL Tautology 감지)
- 931130(가능한 RFI(원격 파일 포함) 공격 = 오프-도메인 참조/링크)



## <a name="next-steps"></a>다음 단계
도움이 더 필요한 경우 [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-eventgrid)에 문제를 게시하거나 [지원 티켓](https://azure.microsoft.com/support/options/)을 여세요. 
