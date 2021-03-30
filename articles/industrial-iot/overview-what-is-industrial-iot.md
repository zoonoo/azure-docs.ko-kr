---
title: Azure 산업용 IoT 개요
description: 이 문서에서는 산업용 IoT에 대한 개요를 제공합니다. IIoT의 공장 연결 및 보안 구성 요소에 대해 설명합니다.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 940391d26b5a8455fef01c8094cd08e05ab51290
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787612"
---
# <a name="what-is-industrial-iot-iiot"></a>IIoT(산업용 IoT)란?

IIoT(산업용 IoT)는 제조 산업에서 IoT를 적용하여 산업 효율성을 향상시킵니다.

![산업용 IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

## <a name="improve-industrial-efficiencies"></a>산업 효율성 개선
Azure 산업용 IoT를 사용하여 작업 생산성 및 수익성을 향상시킵니다. 현장에서 자산에 액세스하는 데 시간이 많이 걸리는 프로세스를 줄입니다. 이미 제조 현장에서 작동하고 있는 컴퓨터를 포함한 산업 장비 및 디바이스를 클라우드에서 연결하고 모니터링합니다. IoT 데이터를 분석하여 전체 사이트의 성능을 높이는 데 도움이 되는 인사이트를 얻습니다.

## <a name="industrial-iot-components"></a>산업용 IoT 구성 요소

**IoT Edge 디바이스** IoT Edge 디바이스는 Edge 런타임 및 Edge 모듈로 구성됩니다. 
- *Edge 모듈* 은 OPC 게시자 및 OPC 트윈과 같이 가장 작은 계산 단위인 Docker 컨테이너입니다. 
- *Edge 디바이스* 는 클라우드에서 OPC UA 서버와 IoT Hub 간의 중재자 역할을 하는 이러한 모듈을 배포하는 데 사용됩니다. IoT Edge에 대한 자세한 내용은 [여기](https://azure.microsoft.com/services/iot-edge/)를 참조하세요.

**IoT Hub** IoT Hub는 IoT 애플리케이션과 이를 통해 관리되는 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다. 오픈 소스 SDK 및 여러 프로토콜을 지원하는 서비스로서 개방적이고 유연한 클라우드 플랫폼입니다. IoT Hub에 대한 자세한 내용은 [여기](https://azure.microsoft.com/services/iot-hub/)를 참조하세요.

**산업용 Edge 모듈**
- *OPC 게시자*: OPC 게시자는 IoT Edge 내에서 실행됩니다. OPC UA 서버에 연결하고, 이러한 서버의 JSON 인코딩 원격 분석 데이터를 OPC UA "Pub/Sub" 형식으로 Azure IoT Hub에 게시합니다. Azure IoT Hub 클라이언트 SDK에서 지원하는 모든 전송 프로토콜(예: HTTPS, AMQP 및 MQTT)을 사용할 수 있습니다.
- *OPC 트윈*: OPC 트윈은 Azure IoT Edge 및 IoT Hub를 사용하여 클라우드와 공장 네트워크를 연결하는 마이크로서비스로 구성됩니다. OPC 쌍은 REST API를 통한 산업용 디바이스의 검색, 등록 및 원격 제어를 제공합니다. OPC 트윈에는 OPC UA(OPC 통합 아키텍처) SDK가 필요하지 않습니다. 프로그래밍 언어에 독립적이며, 서버리스 워크플로에 포함될 수 있습니다.
- *검색*: Discoverer ID로 표시되는 검색 모듈은 에지에서 OPC UA 서버 검색을 포함한 검색 서비스를 제공합니다. 검색이 구성되고 사용하도록 설정되면 모듈에서 IoT Edge 및 IoT Hub 원격 분석 경로를 통해 검사 프로브 결과를 온보딩 서비스에 보냅니다. 서비스에서 결과를 처리하고 레지스트리의 모든 관련 ID를 업데이트합니다.


**Azure를 사용하여 산업 자산 검색, 등록 및 관리** Azure 산업용 IoT를 사용하면 공장 운영자가 공장 네트워크에서 OPC UA 사용 서버를 검색하여 Azure IoT Hub에 등록할 수 있습니다. 운영 직원은 전 세계 어디서나 제조 현장의 이벤트를 구독하고 이에 대응할 수 있습니다. 마이크로서비스의 REST API는 OPC UA 서비스 에지 쪽을 미러링합니다. AAD(Azure Active Directory)에서 지원하는 OAUTH 인증 및 권한 부여를 사용하여 보안이 유지됩니다. 이를 통해 클라우드 애플리케이션에서 서버 주소 공간을 찾아보거나 변수를 읽고 쓸 수 있으며, HTTPS 및 간단한 OPC UA JSON 페이로드를 사용하여 메서드를 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 산업용 IoT에 대해 알아보았으므로 산업용 IoT 플랫폼과 OPC 게시자에 대해 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [산업용 IoT 플랫폼이란?](overview-what-is-industrial-iot-platform.md)

> [!div class="nextstepaction"]
> [OPC 게시자란?](overview-what-is-opc-publisher.md)