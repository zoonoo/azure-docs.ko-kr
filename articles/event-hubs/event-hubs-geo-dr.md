---
title: "Azure Event Hubs 지리적 재해 복구 | Microsoft Docs"
description: "지리적 지역을 사용하여 장애 조치(Failover)하고 Azure Event Hubs에서 재해 복구를 수행하는 방법"
services: event-hubs
documentationcenter: 
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sethm
ms.openlocfilehash: 94c2782b3166fbc65ae755291a82a2a14556b96f
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2017
---
# <a name="azure-event-hubs-geo-disaster-recovery-preview"></a>Azure Event Hubs 지리적 재해 복구(미리 보기)

지역 데이터 센터에서 가동 중지 시간이 발생하는 경우 데이터 처리가 다른 지역 또는 데이터 센터에서 계속 작동되는 것이 중요합니다. 이와 같이 *지리적 재해 복구* 및 *지리적 복제*는 기업에 중요한 기능입니다. Azure Event Hubs는 네임스페이스 수준에서 지리적 재해 복구 및 지리적 복제를 둘 다 지원합니다. 

Azure Event Hubs의 지리적 재해 복구 기능은 재해 복구 솔루션입니다. 이 문서에 설명된 개념 및 워크플로는 재해 시나리오에 적용되며 일시적이거나 임시적인 중단이 아닙니다.

Microsoft Azure의 재해 복구에 대해 자세히 알아보려면 [이 문서](/azure/architecture/resiliency/disaster-recovery-azure-applications)를 참조하세요. 

## <a name="terminology"></a>용어

**페어링**: 기본 네임스페이스는 *활성*으로 표시되며 메시지를 받습니다. 장애 조치(Failover) 네임스페이스는 *수동*이며 메시지를 받지 않습니다. 둘 간의 메타데이터의 동기화되므로 응용 프로그램 코드 변경 없이도 둘 다 메시지를 원활하게 수락할 수 있습니다. 활성 지역과 수동 지역 간에 재해 복구 구성을 설정하는 것을 지역의 *페어링*이라고 합니다.

**별칭**: 설정한 재해 복구 구성의 이름입니다. 별칭은 하나의 안정적인 FQDN(정규화된 도메인 이름) 연결 문자열을 제공합니다. 응용 프로그램은 이 별칭 연결 문자열을 사용하여 네임스페이스에 연결합니다.

**메타데이터**: 이벤트 허브 이름, 소비자 그룹, 파티션, 처리량 단위, 엔터티 및 네임스페이스와 연결된 속성을 나타냅니다.

## <a name="enable-geo-disaster-recovery"></a>지리적 재해 복구 사용

Event Hubs 지리적 재해 복구는 다음 3단계에 걸쳐 사용하도록 설정할 수 있습니다. 

1. 별칭 연결 문자열을 만들고 라이브 메타데이터 복제를 제공하는 지리적 페어링을 만듭니다. 
2. 기존 클라이언트 연결 문자열을 1단계에서 만든 별칭으로 업데이트합니다.
3. 장애 조치(Failover) 시작: 지리적 페어링이 끊어지고 별칭은 보조 네임스페이스를 새로운 기본 네임스페이스로 가리킵니다.

다음 그림은 이 워크플로를 보여 줍니다.

![지리적 페어링 흐름][1] 

### <a name="step-1-create-a-geo-pairing"></a>1단계: 지리적 페어링 만들기

두 지역 간에 페어링을 만들려면 기본 네임스페이스와 보조 네임스페이스가 필요합니다. 그런 다음 별칭을 만들어 지리적 쌍을 형성합니다. 네임스페이스가 별칭과 페어링되면 메타데이터는 네임스페이스에 주기적으로 복제됩니다. 

다음 코드는 이 방법을 보여 줍니다.

```csharp
ArmDisasterRecovery adr = await client.DisasterRecoveryConfigs.CreateOrUpdateAsync(
                                    config.PrimaryResourceGroupName,
                                    config.PrimaryNamespace,
                                    config.Alias,
                                    new ArmDisasterRecovery(){ PartnerNamespace = config.SecondaryNamespace});
```

### <a name="step-2-update-existing-client-connection-strings"></a>2단계: 기존 클라이언트 연결 문자열 업데이트

지리적 페어링이 완료되면 기본 네임스페이스를 가리키는 연결 문자열을 별칭 연결 문자열을 가리키도록 업데이트해야 합니다. 다음 예제와 같이 연결 문자열을 가져옵니다.

```csharp
var accessKeys = await client.Namespaces.ListKeysAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
```

### <a name="step-3-initiate-a-failover"></a>3단계: 장애 조치(Failover) 시작

재해가 발생하거나 보조 네임스페이스로의 장애 조치(Failover)를 시작하기로 결정한 경우 메타데이터 및 데이터가 보조 네임스페이스로의 흐름을 시작합니다. 응용 프로그램은 별칭 연결 문자열을 사용하여 보조 네임스페이스의 이벤트 허브에 대한 읽기 및 쓰기를 자동으로 시작하므로 추가 조치가 필요하지 않습니다. 

다음 코드에서는 장애 조치(Failover)를 트리거하는 방법을 보여 줍니다.

```csharp
await client.DisasterRecoveryConfigs.FailOverAsync(config.SecondaryResourceGroupName,
                                                   config.SecondaryNamespace, config.Alias);
```

장애 조치가 완료되었으나 기본 네임스페이스에 있는 데이터가 필요한 경우 해당 데이터를 추출하려면 기본 네임스페이스의 이벤트 허브에 대한 명시적 연결 문자열을 사용해야 합니다.

### <a name="other-operations-optional"></a>기타 작업(선택 사항)

다음 코드와 같이 지리적 페어링을 끊거나 별칭을 삭제할 수도 있습니다. 별칭 연결 문자열을 삭제하려면 먼저 지리적 페어링을 끊어야 합니다.

```csharp
// Break pairing
await client.DisasterRecoveryConfigs.BreakPairingAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, config.Alias);

// Delete alias connection string
// Before the alias is deleted, pairing must be broken
await client.DisasterRecoveryConfigs.DeleteAsync(config.PrimaryResourceGroupName,
                                                 config.PrimaryNamespace, config.Alias);
```

## <a name="considerations-for-public-preview"></a>공개 미리 보기에 대한 고려 사항

이 릴리스에 대한 다음과 같은 고려 사항에 유의하세요.

1. 지리적 재해 복구 기능은 미국 중북부 및 미국 중남부에서만 사용할 수 있습니다. 
2. 이 기능은 새로 만든 네임스페이스에 대해서만 지원됩니다.
3. 미리 보기 릴리스의 경우 메타데이터 복제만 사용하도록 설정됩니다. 실제 데이터는 복제되지 않습니다.
4. 미리 보기 릴리스에서는 이 기능 사용에 따른 비용 청구가 없습니다. 그러나 예약된 처리량 단위에 대해서는 기본 및 보조 네임스페이스 둘 다 요금이 부과됩니다.

## <a name="next-steps"></a>다음 단계

* [GitHub의 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient)은 지리적 페어링을 만들고 재해 복구 시나리오에 대한 장애 조치(Failover)를 시작하는 간단한 워크플로를 진행합니다.
* [REST API 참조](/rest/api/eventhub/disasterrecoveryconfigs)에서는 지리적 재해 복구 구성을 수행하기 위한 API에 대해 설명합니다.

Event Hubs에 대한 자세한 내용은 다음 링크를 방문하세요.

* [Event Hubs 자습서](event-hubs-dotnet-standard-getstarted-send.md) 시작
* [Event Hubs FAQ](event-hubs-faq.md)
* [Event Hubs를 사용하는 샘플 응용 프로그램](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/eh-geodr1.png

