---
title: Azure IoT Hub 메시지 보강 개요
description: 이 문서에서는 메시지를 지정된 끝점으로 보내기 전에 IoT Hub에 추가 정보로 메시지에 스탬프를 지정하는 기능을 제공하는 메시지 보강을 보여 줍니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429117"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>디바이스-클라우드 IoT 허브 메시지를 위한 메시지 보강

*메시지 보강은* 메시지가 지정된 끝점으로 전송되기 전에 추가 정보와 함께 메시지를 *스탬프하는* IoT Hub의 기능입니다. 메시지 보강을 사용하는 한 가지 이유는 다운스트림 처리를 단순화하는 데 사용할 수 있는 데이터를 포함하기 위해서입니다. 예를 들어 장치 쌍 태그로 장치 원격 분석 메시지를 보강하면 고객의 부하를 줄여 이 정보에 대한 장치 쌍 API 호출을 할 수 있습니다.

![메시지 보강 흐름](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

메시지 보강에는 세 가지 핵심 요소가 있습니다.

* 보강 이름 또는 키

* 값

* 보강을 적용해야 하는 하나 이상의 [끝점.](iot-hub-devguide-endpoints.md)

**키는** 문자열입니다. 키에는 [하이픈(하이픈) ( ] 및`-`[anuma(`_`] 및`.`마침표)과 같은 특수 문자만 포함할 수 있습니다.

**값은** 다음 예제 중 어느 한 값일 수 있습니다.

* 모든 정적 문자열입니다. 조건, 논리, 작업 및 함수와 같은 동적 값은 허용되지 않습니다. 예를 들어 여러 고객이 사용하는 SaaS 응용 프로그램을 개발하는 경우 각 고객에게 식별자를 할당하고 해당 식별자를 응용 프로그램에서 사용할 수 있도록 할 수 있습니다. 응용 프로그램이 실행되면 IoT Hub는 고객의 식별자로 장치 원격 분석 메시지를 스탬프처리하여 각 고객마다 다르게 메시지를 처리할 수 있도록 합니다.

* 메시지를 보내는 IoT 허브의 이름입니다. 이 값은 *$iothubname.*

* 경로와 같은 장치 쌍의 정보입니다. 예를 들어 *$twin.tags.field* 및 *$twin.tags.latitude입니다.*

   > [!NOTE]
   > 현재 $iothubname $twin.tags, $twin.properties.desired 및 $twin.properties.reported만 메시지 보강을 위해 지원되는 변수입니다.

메시지 보강은 선택한 끝점으로 전송된 메시지에 응용 프로그램 속성으로 추가됩니다.  

## <a name="applying-enrichments"></a>농축 적용

메시지는 다음 예제를 포함하여 [IoT Hub 메시지 라우팅에서](iot-hub-devguide-messages-d2c.md)지원하는 모든 데이터 원본에서 올 수 있습니다.

* 온도 또는 압력과 같은 장치 원격 분석
* 장치 쌍 변경 알림 - 장치 쌍변경
* 장치 수명 주기 이벤트(예: 장치를 만들거나 삭제하는 경우)

IoT Hub의 기본 제공 끝점으로 가는 메시지 또는 Azure Blob 저장소, 서비스 버스 큐 또는 서비스 버스 항목과 같은 사용자 지정 끝점으로 라우팅되는 메시지에 보강을 추가할 수 있습니다.

끝점을 이벤트 그리드로 선택하여 이벤트 그리드에 게시되는 메시지에 보강을 추가할 수 있습니다. 이벤트 그리드 구독에 따라 IoT Hub에서 장치 원격 분석으로의 기본 경로를 만듭니다. 이 단일 경로는 모든 이벤트 그리드 구독을 처리할 수 있습니다. 장치 원격 분석으로 이벤트 그리드 구독을 만든 후 이벤트 그리드 끝점에 대한 보강을 구성할 수 있습니다. 자세한 내용은 [Iot 허브 및 이벤트 그리드를](iot-hub-event-grid.md)참조하십시오.

끝점당 보강이 적용됩니다. 특정 끝점에 대해 스탬프할 5개의 보강을 지정하면 해당 끝점으로 가는 모든 메시지에 동일한 5개의 보강이 스탬프됩니다.

보강은 다음 방법을 사용하여 구성할 수 있습니다.

| **메서드** | **명령** |
| ----- | -----| 
| 포털 | [Azure 포털](https://portal.azure.com) | 메시지 [보강 자습서 보기](tutorial-message-enrichments.md) | 
| Azure CLI   | [az iot 허브 메시지 강화](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

메시지 보강을 추가해도 메시지 라우팅에 대기 시간이 추가되지 않습니다.

메시지 보강을 사용해 보십시오. [message enrichments tutorial](tutorial-message-enrichments.md)

## <a name="limitations"></a>제한 사항

* 표준 또는 기본 계층의 허브에 대해 IoT Hub당 최대 10개의 보강을 추가할 수 있습니다. 프리 티어의 IoT Hubs의 경우 최대 2개의 보강 기능을 추가할 수 있습니다.

* 경우에 따라 장치 쌍에서 태그 또는 속성에 설정된 값으로 보강을 적용하는 경우 값이 문자열 값으로 스탬프됩니다. 예를 들어 보강 값이 $twin.tags.field로 설정된 경우 메시지에는 트윈에서 해당 필드의 값이 아니라 "$twin.tags.field" 문자열로 스탬프가 찍혀 집니다. 이 경우 다음과 같은 경우에 발생 합니다.

   * IoT 허브는 기본 계층에 있습니다. 기본 계층 IoT 허브는 장치 쌍을 지원하지 않습니다.

   * IoT Hub가 표준 계층에 있지만 메시지를 보내는 장치에 장치 쌍이 없습니다.

   * IoT Hub가 표준 계층에 있지만 보강 값에 사용되는 장치 쌍 경로가 없습니다. 예를 들어 보강 값이 $twin.tags.location으로 설정되어 있고 장치 쌍에 태그 아래에 위치 속성이 없는 경우 메시지에 "$twin.tags.location" 문자열이 스탬프됩니다. 

* 장치 쌍에 대한 업데이트는 해당 보강 값에 반영되기까지 최대 5분이 걸릴 수 있습니다.

* 보강을 포함한 총 메시지 크기는 256KB를 초과할 수 없습니다. 메시지 크기가 256KB를 초과하면 IoT Hub가 메시지를 삭제합니다. [IoT Hub 메트릭을](iot-hub-metrics.md) 사용하여 메시지가 삭제될 때 오류를 식별하고 디버깅할 수 있습니다. 예를 들어 d2c.telemetry.egress.invalid를 모니터링할 수 있습니다.

* 메시지 보강은 디지털 트윈 변경 [이벤트(IoT 플러그 앤 플레이 공개 미리 보기의](../iot-pnp/overview-iot-plug-and-play.md)일부)에는 적용되지 않습니다.

## <a name="pricing"></a>가격 책정

메시지 보강은 추가 비용 없이 사용할 수 있습니다. 현재 IoT Hub로 메시지를 보낼 때 요금이 부과됩니다. 메시지가 여러 끝점으로 이동하더라도 해당 메시지에 대해 한 번만 요금이 청구됩니다.

## <a name="next-steps"></a>다음 단계

IoT Hub로 메시지를 라우팅하는 데 대한 자세한 내용은 다음 문서를 확인하십시오.

* [메시지 보강 튜토리얼](tutorial-message-enrichments.md)

* [IoT Hub 메시지 라우팅을 사용하여 다양한 엔드포인트에 디바이스-클라우드 메시지 보내기](iot-hub-devguide-messages-d2c.md)

* [자습서: IoT 허브 라우팅](tutorial-routing.md)
