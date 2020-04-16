---
title: Azure Event Grid를 사용하여 Key Vault 모니터링
description: Azure Event Grid를 사용하여 Key Vault 이벤트 구독
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: cc12cc9a4828404e960aee239bd388af5b1ea3b7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431905"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Azure Event Grid를 사용하여 Key Vault 모니터링(미리 보기)

Event Grid와 Key Vault 통합은 현재 미리 보기로 제공됩니다. Key Vault에 저장된 비밀의 상태가 변경되었을 때 사용자에게 알릴 수 있습니다. 상태 변경은 만료되려고 하는 비밀(30일 이내), 만료된 비밀 또는 사용 가능한 새 버전이 있는 비밀로 정의됩니다. 세 가지 비밀 유형(키, 인증서 및 비밀)에 대한 알림이 모두 지원됩니다.

애플리케이션은 복잡한 코드나 고가의 비효율적인 폴링 서비스 없이도 최신 서버리스 아키텍처를 사용하여 이러한 이벤트에 대응할 수 있습니다. 이벤트는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)를 통해 [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)와 같은 이벤트 처리기로 푸시되거나 Webhook으로도 푸시되며, 요금은 사용한 만큼만 청구됩니다. 가격 책정에 대한 자세한 내용은 [Event Grid 가격 책정](https://azure.microsoft.com/pricing/details/event-grid/)을 참조하세요.

## <a name="key-vault-events-and-schemas"></a>Key Vault 이벤트 및 스키마

Event Grid는 [이벤트 구독](../../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Key Vault 이벤트에는 데이터 변경에 대응하는 데 필요한 모든 정보가 포함되어 있습니다. eventType 속성이 "Microsoft.KeyVault"로 시작하므로 Key Vault 이벤트를 식별할 수 있습니다.

자세한 내용은 [Key Vault 이벤트 스키마](../../event-grid/event-schema-key-vault.md)를 참조하세요.

> [!WARNING]
> 알림 이벤트는 새 버전의 비밀, 키 및 인증서에서만 트리거되며, 이러한 알림을 받으려면 먼저 키 자격 증명 모음의 이벤트를 구독해야 합니다.
> 
> 인증서에 대해 지정한 정책에 따라 인증서가 자동으로 갱신되는 경우에만 인증서에 대한 알림 이벤트를 받을 수 있습니다.

## <a name="practices-for-consuming-events"></a>이벤트 사용에 관한 지침

Key Vault 이벤트를 처리하는 애플리케이션은 다음에 권장되는 몇 가지 지침을 따라야 합니다.

* 이벤트를 동일한 이벤트 처리기로 라우팅하도록 여러 구독을 구성할 수 있습니다. 이벤트가 특정 원본에서 온 것으로 가정하는 것이 아니라 메시지의 항목을 확인하여 예상하는 Key Vault에서 기인한 것인지 확인하는 것이 중요합니다.
* 마찬가지로, eventType이 본인이 처리하려는 형식인지 확인하고, 수신된 모든 이벤트가 예상하는 형식일 것이라고 간주하지 않도록 합니다.
* 이해할 수 없는 필드는 무시합니다.  이 지침은 나중에 추가될 수 있는 새로운 기능에 적용하는 데도 도움이 됩니다.
* "제목" 접두사 및 접미사 일치를 사용하여 이벤트를 특정 이벤트로 제한합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 키 볼트 개요)](overview.md)
- [Azure Event Grid 개요](../../event-grid/overview.md)
- 방법: [Key Vault 이벤트를 Automation Runbook으로 라우팅(미리 보기)](event-grid-tutorial.md)합니다.
- 방법: [키 자격 증명 모음 비밀 변경 시 이메일 받기](event-grid-logicapps.md)
- [Azure Key Vault에 대한 Azure Event Grid 이벤트 스키마(미리 보기)](../../event-grid/event-schema-key-vault.md)
- [Azure Automation 개요](../../automation/index.yml)
