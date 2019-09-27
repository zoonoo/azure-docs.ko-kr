---
title: Azure Service Bus 리소스 관리자 예외 | Microsoft Docs
description: Azure Resource Manager 및 제안 된 작업으로 표시 되는 Service Bus 예외 목록입니다.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: e666503b9e8888e7d61445639fe0f3adeeffe55f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329306"
---
# <a name="service-bus-resource-manager-exceptions"></a>예외 리소스 관리자 Service Bus

이 문서에서는 Azure Resource Manager via 템플릿 또는 직접 호출을 사용 하 여 Azure Service Bus와 상호 작용할 때 생성 되는 예외를 나열 합니다.

> [!IMPORTANT]
> 이 문서는 자주 업데이트 됩니다. 업데이트를 다시 확인 하세요.

Azure Resource Manager를 통해 표시 되는 다양 한 예외/오류는 다음과 같습니다.

## <a name="error-bad-request"></a>오류: 잘못된 요청

"잘못 된 요청"은 리소스 관리자에서 받은 요청이 유효성 검사에 실패 한 것을 의미 합니다.

| 오류 코드 | 오류 하위 코드 | 오류 메시지 | 설명 | 권장 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 잘못된 요청 | 40000 | 하위 코드 = 40000. ' *Namespace name* ' 네임 스페이스에서 ' Basic ' 계층을 사용 하 고 있으므로 큐를 만들 때 *' property name '* 속성을 설정할 수 없습니다. 이 작업은 ' 표준 ' 또는 ' 프리미엄 ' 계층 에서만 지원 됩니다. | Azure Service Bus 기본 계층에서는 아래 속성을 설정 하거나 업데이트할 수 없습니다. <ul> <li> Queuedescription.requiresduplicatedetection </li> <li> AutoDeleteOnIdle </li> <li>requiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> 항목 </li> </ul> | 이 기능을 활용 하려면 Basic에서 Standard 또는 Premium 계층으로 업그레이드 하는 것이 좋습니다. |
| 잘못된 요청 | 40000 | 하위 코드 = 40000. 기존 큐의 ' Queuedescription.requiresduplicatedetection ' 속성 값은 변경할 수 없습니다. | 중복 검색은 엔터티를 만들 때 설정/해제 해야 합니다. 만든 후에는 중복 검색 구성 매개 변수를 변경할 수 없습니다. | 이전에 만든 큐/구독에서 중복 검색을 사용 하도록 설정 하려면 중복 검색을 사용 하 여 새 큐를 만든 다음 원래 큐에서 새 큐로 전달할 수 있습니다. |
| 잘못된 요청 | 40000 | 하위 코드 = 40000. 지정 된 값 16384이 잘못 되었습니다. ' MaxSizeInMegabytes ' 속성은 다음 값 중 하나 여야 합니다. 1024; 2048; 3072; 4096; 5120. | MaxSizeInMegabytes 값이 잘못 되었습니다. | MaxSizeInMegabytes가 다음 중 하나에 해당 하는 1024, 2048, 3072, 4096, 5120 중 하나 인지 확인 합니다. |
| 잘못된 요청 | 40000 | 하위 코드 = 40000. 큐에 대 한 분할을 변경할 수 없습니다. | 엔터티에 대 한 분할을 변경할 수 없습니다. | 새 엔터티를 만들고 파티션을 사용 하도록 설정 합니다. | 
| 잘못된 요청 | 없음 | 네임 스페이스 *' namespace name '* 이 없습니다. | 네임 스페이스는 Azure 구독 내에 존재 하지 않습니다. | 이 오류를 해결 하려면 다음을 시도 하세요. <ul> <li> Azure 구독이 올바른지 확인 합니다. </li> <li> 네임 스페이스가 있는지 확인 하십시오. </li> <li> 네임 스페이스 이름이 올바른지 확인 합니다 (철자 오류 또는 null 문자열 없음). </li> </ul> | 
| 잘못된 요청 | 40400 | 하위 코드 = 40400. 자동 전달 대상 엔터티가 없습니다. | 자동 전달 대상 엔터티의 대상이 존재 하지 않습니다. | 대상 엔터티 (큐 또는 토픽)는 원본이 만들어지기 전에 존재 해야 합니다. 대상 엔터티를 만든 후 다시 시도 하세요. |


## <a name="error-code-429"></a>오류 코드: 429

| 오류 코드 | 오류 하위 코드 | 오류 메시지 | 설명 | 권장 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | 하위 코드 = 50004. *네임 스페이스* 의 네임 스페이스가 제한 되기 때문에 요청이 종료 되었습니다. | 들어오는 요청 수가 리소스의 제한을 초과 하는 경우이 오류 조건이 발생 합니다. | 몇 초 동안 기다린 후 다시 시도 하세요. <br/> <br/> [할당량](service-bus-quotas.md) 및 [Azure Resource Manager 요청 제한](../azure-resource-manager/resource-manager-request-limits.md) 에 대해 자세히 알아보세요.|
| 429 | 40901 | 하위 코드 = 40901. 충돌 하는 다른 작업이 진행 중입니다. | 동일한 리소스/엔터티에서 충돌 하는 다른 작업이 진행 중입니다. | 현재 진행 중인 작업이 완료 될 때까지 기다린 후 다시 시도 하십시오. |
| 429 | 40900 | 하위 코드 = 40900. 약속. 리소스의 현재 상태에서 허용되지 않는 작업을 요청하고 있습니다. | 동일한 엔터티 (큐, 토픽, 구독 또는 규칙)에서 동시에 작업을 수행 하기 위해 여러 요청이 수행 되는 경우이 조건이 발생할 수 있습니다. | 몇 초 동안 기다린 후 다시 시도 하세요. |
| 429 | 없음 | 리소스 충돌이 발생 했습니다. 충돌 하는 다른 작업이 진행 중일 수 있습니다. 실패 한 작업을 다시 시도 하는 경우 백그라운드 정리가 아직 보류 중입니다. 나중에 다시 시도하십시오. | 이 상태는 동일한 엔터티에 대해 보류 중인 작업이 있는 경우 적중 될 수 있습니다. | 이전 작업이 완료 될 때까지 기다린 후 다시 시도 하십시오. |

