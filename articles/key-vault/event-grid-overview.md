---
title: Azure Event Grid로 Key Vault 모니터링
description: Azure Event Grid를 사용 하 여 Key Vault 이벤트 구독
services: media-services
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 1fa554e03188c4d8d6227a6d2c0a560c3080b0fe
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033499"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Azure Event Grid를 사용 하 여 Key Vault 모니터링 (미리 보기)

Event Grid와 Key Vault 통합은 현재 미리 보기로 제공 됩니다. 키 자격 증명 모음에 저장 된 암호의 상태가 변경 되 면 사용자에 게 알릴 수 있습니다. 상태 변경은 만료 되는 암호 (30 일 이내), 만료 된 비밀 또는 사용 가능한 새 버전의 암호로 정의 됩니다. 세 가지 비밀 유형 (키, 인증서 및 비밀)에 대 한 알림이 모두 지원 됩니다.

응용 프로그램은 복잡 한 코드 또는 비용이 많이 들고 비효율적인 폴링 서비스가 없어도 최신 서버를 사용 하지 않는 아키텍처를 사용 하 여 이러한 이벤트에 반응할 수 있습니다. 이벤트는 [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), 사용자의 Webhook와 같은 이벤트 처리기에 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) 를 통해 푸시되 며 사용한 만큼만 요금을 지불 합니다. 가격 책정에 대한 자세한 내용은 [Event Grid 가격 책정](https://azure.microsoft.com/pricing/details/event-grid/)을 참조하세요.

## <a name="key-vault-events-and-schemas"></a>이벤트 및 스키마 Key Vault

Event Grid는 [이벤트 구독](../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Key Vault 이벤트에는 데이터 변경 내용에 응답 하는 데 필요한 모든 정보가 포함 되어 있습니다. EventType 속성이 "Microsoft. KeyVault"로 시작 하므로 Key Vault 이벤트를 식별할 수 있습니다.

자세한 내용은 [Key Vault 이벤트 스키마](../event-grid/event-schema-key-vault.md)를 참조 하세요.

> [!NOTE]
> 이벤트는 구독이 설정 된 후에 생성 된 암호 버전 (세 유형 모두)에 대해서만 트리거됩니다.
>
> 기존 암호의 경우 새 버전을 생성 해야 합니다.

## <a name="practices-for-consuming-events"></a>이벤트 사용에 관한 지침

Key Vault 이벤트를 처리 하는 응용 프로그램은 다음과 같은 몇 가지 권장 방법을 따라야 합니다.

* 이벤트를 동일한 이벤트 처리기로 라우팅하도록 여러 구독을 구성할 수 있습니다. 이벤트를 특정 원본에서 가져온 것으로 가정 하지 않는 것이 중요 합니다. 단, 메시지의 항목을 확인 하 여 예상 되는 주요 자격 증명 모음에서 가져온 것인지 확인 해야 합니다.
* 마찬가지로, eventType이 본인이 처리하려는 형식인지 확인하고, 수신된 모든 이벤트가 예상하는 형식일 것이라고 간주하지 않도록 합니다.
* 이해할 수 없는 필드는 무시합니다.  이 지침은 나중에 추가될 수 있는 새로운 기능에 적용하는 데도 도움이 됩니다.
* "제목" 접두사 및 접미사 일치를 사용하여 이벤트를 특정 이벤트로 제한합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 개요](key-vault-overview.md)
- [Azure Event Grid 개요](../event-grid/overview.md)
- [방법: Automation Runbook으로 Key Vault 이벤트 라우팅 (미리 보기)](event-grid-tutorial.md)
- [Azure Key Vault에 대 한 Azure Event Grid 이벤트 스키마 (미리 보기)](../event-grid/event-schema-key-vault.md)
- [Azure Automation 개요](../automation/index.yml)
