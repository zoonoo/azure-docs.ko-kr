---
title: Azure IoT Hub 장치 SDK를 사용하여 연결 및 신뢰할 수 있는 메시징을 관리하는 방법
description: Azure IoT Hub 장치 SDK를 사용할 때 장치 연결 및 메시징을 개선하는 방법을 알아봅니다.
services: iot-hub
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 13e9eff9f7a9298db64b40d0fefcd7f1498137da
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052859"
---
# <a name="how-to-manage-connectivity-and-reliable-messaging-using-azure-iot-hub-device-sdks"></a>Azure IoT Hub 장치 SDK를 사용하여 연결 및 신뢰할 수 있는 메시징을 관리하는 방법

이 가이드에서는 Azure IoT 장치 SDK의 연결 및 신뢰할 수 있는 메시징 기능을 활용하여 복원력 있는 장치 응용 프로그램을 디자인하는 방법을 개괄적으로 설명합니다. 이 문서의 목표는 질문에 답변하고 다음 시나리오를 처리할 수 있도록 지원하는 것입니다.

- 삭제된 네트워크 연결 관리
- 서로 다른 네트워크 연결 간의 전환 관리
- 서비스의 일시적 연결 오류로 인한 다시 연결 관리

구현 세부 정보는 언어에 따라 다를 수 있습니다. 자세한 내용은 연결된 API 문서 또는 특정 SDK를 참조하세요.

- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)


## <a name="designing-for-resiliency"></a>복원력을 위한 디자인

IoT 장치는 주로 불연속 네트워크 연결 및/또는 불안정한 네트워크 연결(예: GSM 또는 위성)을 사용합니다. 또한 클라우드 기반 서비스와 상호 작용할 때 일시적인 서비스 가용성, 인프라 수준 오류(일반적으로 일시적 오류라고 함) 등의 일시적 조건으로 인해 오류가 발생할 수 있습니다. 장치에서 실행되는 응용 프로그램이 연결 및 다시 연결 메커니즘과 메시지 전송/수신에 대한 재시도 논리를 관리해야 합니다. 또한 재시도 전략 요구 사항은 장치가 참여하는 IoT 시나리오와 장치의 컨텍스트 및 기능에 따라 달라집니다.

Azure IoT Hub 장치 SDK는 Azure IoT Hub에 연결하고 메시지를 전송/수신하는 강력하고 포괄적인 방법으로 제공하여 클라우드-장치 및 장치-클라우드에서 연결 및 통신을 간소화하려고 합니다. 개발자가 기존 구현을 수정하여 지정된 시나리오에 대한 올바른 재시도 전략을 개발할 수도 있습니다.

연결 및 신뢰할 수 있는 메시징을 지원하는 관련 SDK 기능은 다음 섹션에서 설명합니다.

## <a name="connection-and-retry"></a>연결 및 재시도

이 섹션에서는 연결을 관리할 때 사용 가능한 연결 및 재시도 패턴, 장치 응용 프로그램에서 다른 재시도 정책을 사용하는 방법에 대한 구현 지침, 장치 SDK의 관련 API를 간략하게 설명합니다.

### <a name="error-patterns"></a>오류 패턴
연결 실패는 다음과 같은 여러 수준에서 발생할 수 있습니다.

-  네트워크 오류(예: 연결이 끊어진 소켓, 이름 확인 오류)
- HTTP, AMQP 및 MQTT 전송에 대한 프로토콜 수준 오류(예: 연결 분리, 세션 만료)
- 로컬 실수(예: 잘못된 자격 증명) 또는 서비스 동작(예: 할당량 또는 제한 초과)에서 발생하는 응용 프로그램 수준 오류

장치 SDK는 세 수준의 오류를 모두 검색합니다.  OS 관련 오류 및 하드웨어 오류는 장치 SDK에서 검색 및 처리되지 않습니다.  이 디자인은 Azure 아키텍처 센터의 [일시적 오류 처리 지침](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines)을 기반으로 합니다.

### <a name="retry-patterns"></a>재시도 패턴

연결 오류가 검색될 때 재시도하는 전체 프로세스는 다음과 같습니다. 
1. SDK가 네트워크, 프로토콜 또는 응용 프로그램에서 오류 및 관련 오류를 검색합니다.
2. 오류 유형에 따라 SDK는 오류 필터를 사용하여 재시도를 수행해야 하는지 결정합니다.  SDK가 **복구할 수 없는 오류**를 확인하면 작업(연결 및 전송/수신)이 중지되고, SDK에서 사용자에게 알립니다. 복구할 수 없는 오류는 SDK가 복구할 수 없음을 확인할 수 있는 오류(예: 인증 또는 잘못된 엔드포인트 오류)입니다.
3. **복구할 수 있는 오류**가 확인되면, SDK는 정의된 시간 제한이 만료될 때까지 지정된 재시도 정책을 사용하여 재시도하기 시작합니다.
4. 정의된 시간 제한이 만료되면 SDK는 연결 또는 전송 시도를 중지하고 사용자에게 알립니다.
5.  SDK를 사용하면 사용자가 콜백을 연결하여 연결 상태 변경을 수신할 수 있습니다. 

다음 세 가지 재시도 정책이 제공됩니다.
- **지터를 사용한 지수 백오프**: 적용되는 기본 재시도 정책입니다.  대체로 시작할 때는 적극적이다가 점점 느려진 다음, 최대 지연을 적중하며 이 지연을 초과하지 않습니다.  이 디자인은 [Azure 아키텍처 센터의 재시도 지침](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)을 기반으로 합니다.
- **사용자 지정 재시도**: 사용자 지정 재시도 정책을 구현하고, 선택한 언어에 따라 RetryPolicy에 삽입할 수 있습니다. 시나리오에 적합한 재시도 정책을 디자인할 수 있습니다.  C SDK에서는 사용할 수 없습니다.
- **재시도 안 함**: 재시도 정책을 “재시도 안 함”으로 설정하는 옵션이 있으며, 재시도 논리를 사용할 수 없게 됩니다.  SDK는 한 번 연결을 시도하고, 연결이 설정되면 한 번 메시지를 보냅니다. 이 정책은 일반적으로 대역폭 또는 비용이 중요한 경우에 사용됩니다.   이 옵션을 선택하면 보내지 못한 메시지가 손실되며 복구할 수 없습니다. 

### <a name="retry-policy-apis"></a>재시도 정책 API

   | SDK) | SetRetryPolicy 메서드 | 정책 구현 | 구현 지침 |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **기본값**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**사용자 지정:** 사용 가능한 [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) 사용<BR>**재시도 안 함:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/Python/iOS 구현](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | 자바| [SetRetryPolicy](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_client_config.setretrypolicy?view=azure-java-stable)        | **기본값**: [ExponentialBackoffWithJitter 클래스](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**사용자 지정:** [RetryPolicy 인터페이스](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java) 구현<BR>**재시도 안 함:** [NoRetry 클래스](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java 구현](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_SetRetryPolicy_Microsoft_Azure_Devices_Client_IRetryPolicy) | **기본값**: [ExponentialBackoff 클래스](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**사용자 지정:** [IRetryPolicy 인터페이스](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet) 구현<BR>**재시도 안 함:** [NoRetry 클래스](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C# 구현](https://github.com/Azure/azure-iot-sdk-csharp) |
   | 노드| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **기본값**: [ExponentialBackoffWithJitter 클래스](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**사용자 지정:** [RetryPolicy 인터페이스](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest) 구현<BR>**재시도 안 함:** [NoRetry 클래스](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Node 구현](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   

다음은 이 흐름을 보여 주는 코드 샘플입니다. 

#### <a name="net-implementation-guidance"></a>.NET 구현 지침

아래 코드 샘플은 기본 재시도 정책을 정의하고 설정하는 방법을 보여 줍니다.

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

높은 CPU 사용량을 방지하기 위해, 코드가 즉시 실패할 경우(예: 네트워크가 없거나 대상의 경로가 없는 경우) 다음 재시도를 실행하는 최소 시간이 1초가 되도록 재시도가 제한됩니다. 

서비스가 제한 오류로 응답하는 경우, 재시도 정책이 서로 다르며, 공용 API를 통해 변경할 수 없습니다.

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

재시도 메커니즘은 현재 4분으로 설정된 `DefaultOperationTimeoutInMilliseconds` 이후에 중지됩니다.

#### <a name="other-languages-implementation-guidance"></a>기타 언어 구현 지침
기타 언어의 경우, 아래 구현 문서를 검토하세요.  재시도 정책 API 사용을 보여 주는 샘플이 리포지토리에 제공됩니다.
- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>다음 단계
- [장치 및 서비스 SDK 사용](.\iot-hub-devguide-sdks.md)
- [C용 IoT 장치 SDK 사용](.\iot-hub-device-sdk-c-intro.md)
- [제한된 장치에 대한 개발](.\iot-hub-devguide-develop-for-constrained-devices.md)
- [모바일 장치용 개발](.\iot-hub-how-to-develop-for-mobile-devices.md)
- [장치 연결 끊김 문제 해결](iot-hub-troubleshoot-connectivity.md)
