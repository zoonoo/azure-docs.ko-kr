---
title: OPC 게시자란? - Azure | Microsoft Docs
description: OPC 게시자 개요
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c738e927a352373d7f5a4aeb5697e07134a98cba
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603658"
---
# <a name="what-is-opc-publisher"></a>OPC 게시자란?

OPC 게시자는 다음 방법을 보여 주는 참조 구현입니다.

- 기존 OPC UA 서버에 연결합니다.
- JSON 페이로드를 사용하여 OPC UA 서버로부터 JSON으로 인코딩된 원격 분석 데이터를 OPC UA Pub/Sub 형식으로 Azure IoT Hub에 게시합니다.

Azure IoT Hub 클라이언트 SDK에서 지원하는 전송 프로토콜인 HTTPS, AMQP 및 MQTT를 사용할 수 있습니다.

참조 구현에는 다음이 포함됩니다.

- 네트워크에 있는 기존 OPC UA 서버에 연결하기 위한 OPC UA ‘클라이언트’ 
- 게시된 항목을 관리하데 사용할 수 있고 동일한 작업을 수행하는 IoT Hub 직접 메서드를 제공하는 포트 62222의 OPC UA ‘서버’ 

GitHub에서 [OPC 게시자 참조 구현](https://github.com/Azure/iot-edge-opc-publisher)을 다운로드할 수 있습니다.

이 애플리케이션은 .NET Core 기술을 통해 구현되며 .NET Core에서 지원되는 플랫폼에서 실행할 수 있습니다.

OPC 게시자는 특정 개수의 연결 유지 요청에 응답하지 않는 엔드포인트에 대한 연결을 설정하기 위한 재시도 논리를 구현합니다. 예를 들어 정전으로 인해 OPC UA 서버가 응답을 중지하는 경우입니다.

OPC UA 서버에 대한 각각의 고유 게시 간격에 대해 애플리케이션은 이 게시 간격을 사용하는 모든 노드가 업데이트되는 별도의 구독을 만듭니다.

네트워크 부하를 줄이기 위해 OPC 게시자는 IoT Hub로 전송되는 데이터의 일괄 처리를 지원합니다. 이 일괄 처리는 구성된 패킷 크기에 도달하는 경우에만 패킷을 IoT Hub로 보냅니다.

이 애플리케이션은 OPC Foundations OPC UA 참조 스택을 NuGet 패키지로 사용합니다. 사용 조건에 대해서는 [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/)을 참조하세요.

### <a name="next-steps"></a>다음 단계

지금까지 OPC 게시자의 개념을 알아보았고, 제안된 다음 단계에서는 [OPC 게시자를 구성](howto-opc-publisher-configure.md)하는 방법을 알아봅니다.
