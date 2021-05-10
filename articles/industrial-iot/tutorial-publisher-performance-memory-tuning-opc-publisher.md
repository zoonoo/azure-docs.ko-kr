---
title: Microsoft OPC 게시자 성능 및 메모리 튜닝
description: 이 자습서에서는 OPC 게시자의 성능과 메모리를 튜닝하는 방법을 알아봅니다.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 89e288d1186efd405019d6474dcbd332e7925d67
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787378"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>자습서: OPC 게시자 성능 및 메모리 튜닝

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 성능 조정
> * 메모리 리소스에 대한 메시지 흐름 조정

프로덕션 설정에서 OPC 게시자를 실행하는 경우 네트워크 성능 요구 사항(처리량 및 대기 시간) 및 메모리 리소스를 고려해야 합니다. OPC 게시자는 이러한 요구 사항을 충족하는 데 도움이 되는 다음 명령줄 매개 변수를 표시합니다.

* 메시지 큐 용량(버전 2.5 이하의 경우 `mq`, 버전 2.6의 경우 사용할 수 없음, 버전 2.7의 경우 `om`)
* IoT Hub 보내기 간격(`si`)
* IoT Hub 메시지 크기(`ms`)

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>IoT Hub 보내기 간격 및 IoT Hub 메시지 크기 조정

`mq/om` 매개 변수는 내부 메시지 큐 용량의 상한을 제어합니다. 이 큐는 모든 메시지를 IoT Hub에 보내기 전에 버퍼링합니다. 큐의 기본 크기는 OPC 게시자 버전 2.5 이하의 경우 최대 2MB이고, 버전 2.7의 경우 최대 4,000개의 IoT Hub 메시지입니다(즉, IoT Hub 메시지 크기 설정이 256KB인 경우 큐 크기는 최대 1GB임). OPC 게시자에서 메시지를 IoT Hub로 충분히 빠르게 보낼 수 없는 경우 이 큐의 항목 수가 증가합니다. 이러한 상황이 테스트 실행 중에 발생하면 다음 중 하나 또는 둘 모두를 수행하여 완화할 수 있습니다.

* IoT Hub 보내기 간격 줄이기(`si`)

* IoT Hub 메시지 크기 늘리기(`ms`, 설정할 수 있는 최댓값은 256KB임)

`si` 및 `ms` 매개 변수를 조정한 경우에도 큐가 계속 증가하면 결국 최대 큐 용량에 도달하고 메시지가 손실됩니다. 이는 `si` 및 `ms` 매개 변수 모두에 물리적 제한이 있고, OPC 게시자와 IoT Hub 간의 인터넷 연결이 지정된 시나리오에서 보내야 하는 메시지 수에 비해 충분히 빠르지 않기 때문입니다. 이 경우 여러 개의 병렬 OPC 게시자를 설정하는 것만 도움이 됩니다. `mq/om` 매개 변수도 OPC 게시자의 메모리 사용량에 가장 큰 영향을 줍니다. 

`si` 매개 변수는 OPC 게시자에서 메시지를 지정된 간격으로 IoT Hub에 보내도록 합니다. 256KB 데이터의 최대 IoT Hub 메시지 크기를 사용할 수 있거나(다시 설정하기 위해 보내기 간격 트리거) 지정된 간격 시간이 경과한 경우 메시지가 보내집니다.

`ms` 매개 변수를 사용하면 IoT Hub에 보내는 메시지를 일괄 처리할 수 있습니다. 대부분의 네트워크 설정에서 단일 메시지를 IoT Hub에 보내는 대기 시간은 페이로드를 전송하는 데 걸리는 시간에 비해 높습니다. 이는 주로 IoT Hub에서 처리한 후에만 메시지를 승인하는 QoS(서비스 품질) 요구 사항 때문입니다. 따라서 IoT Hub에 도달하는 데이터에 대한 지연이 허용되는 경우 `ms` 매개 변수를 0으로 설정하여 최대 메시지 크기인 256KB를 사용하도록 OPC 게시자를 구성해야 합니다. 이는 OPC 게시자를 사용하는 가장 비용 효율적인 방법이기도 합니다.

기본 구성은 10초마다(`si=10`) 또는 256KB의 IoT Hub 메시지 데이터를 사용할 수 있는 경우(`ms=0`) 데이터를 IoT Hub에 보냅니다. 이렇게 하면 최대 10초의 지연이 추가되지만 큰 메시지 크기로 인해 데이터가 손실될 가능성은 낮습니다. OPC 게시자 버전 2.5 이하의 `monitored item notifications enqueue failure` 및 OPC 게시자 버전 2.7의 `messages lost` 메트릭은 손실된 메시지 수를 표시합니다.

`si` 및 `ms` 매개 변수가 모두 0으로 설정되면 OPC 게시자에서 데이터를 사용할 수 있는 즉시 메시지를 IoT Hub에 보냅니다. 이로 인해 평균 IoT Hub 메시지 크기가 200바이트를 조금 초과합니다. 그러나 이 구성의 장점은 OPC 게시자에서 연결된 자산의 데이터를 지연 없이 보낸다는 것입니다. 많은 양의 데이터를 게시해야 하는 사용 사례의 경우 손실된 메시지 수가 많아지므로 이러한 시나리오에서는 이 구성을 사용하지 않는 것이 좋습니다.

OPC 게시자의 성능을 측정하기 위해 `di` 매개 변수를 사용하여 성능 메트릭을 추적 로그에 지정된 간격(초)으로 출력할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 OPC 게시자의 성능과 메모리를 튜닝하는 방법을 알아보았으므로 OPC 게시자 GitHub 리포지토리에서 추가 리소스를 확인할 수 있습니다.

> [!div class="nextstepaction"]
> [OPC 게시자 GitHub 리포지토리](https://github.com/Azure/Industrial-IoT)