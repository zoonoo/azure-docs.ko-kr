---
title: Azure 산업용 IoT 개요
description: 이 문서에서는 산업용 IoT에 대한 개요를 제공합니다. IIoT의 공장 연결 및 보안 구성 요소에 대해 설명합니다.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: ce582f810f483f2e5d3fdda2c3379ecad3842d51
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813275"
---
# <a name="what-is-industrial-iot-iiot"></a>IIoT(산업용 IoT)란?

![산업용 IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

Microsoft Azure IIoT(산업용 사물 인터넷)는 클라우드의 강력한 기능을 산업 및 제조 현장에 통합시키는 Azure 모듈 및 서비스 모음입니다. Azure IIoT는 [OPC UA(개방형 플랫폼 통신 통합 아키텍처)](https://opcfoundation.org/about/opc-technologies/opc-ua/)와 같은 업계 표준 개방형 인터페이스를 사용하여 제조 현장에서 이미 작동 중인 것들을 비롯해 자산 및 센서의 데이터를 Azure 클라우드에 통합할 수 있게 해줍니다. 데이터를 클라우드에 보관하면 혁신적인 비즈니스 및 산업 공정의 개발을 위한 피드백으로서 더욱 신속하고 유연하게 활용할 수 있습니다.

## <a name="discover-register-and-manage-your-industrial-assets-with-azure"></a>Azure를 하여 산업 자산 검색, 등록 및 관리

Azure IIoT에서는 공장 운영자가 공장 네트워크에서 OPC UA 지원 서버를 검색하여 Azure IoT Hub에 등록할 수 있습니다. 운영 직원이 전 세계 어디서나 제조 현장의 이벤트를 구독 및 대응하고, 경고 및 경보를 수신하고, 실시간으로 대응할 수 있습니다.

IIoT는 OPC UA 기능을 구현하는 마이크로서비스 세트를 제공합니다. 마이크로서비스의 REST API는 OPC UA 서비스의 Edge 측을 미러링합니다. AAD(Azure Active Directory)에서 지원하는 OAUTH 인증 및 권한 부여를 사용하여 보안이 유지됩니다. 이를 통해 클라우드 애플리케이션에서 서버 주소 공간을 찾아보거나 변수를 읽고 쓸 수 있으며, HTTPS 및 간단한 OPC UA JSON 페이로드를 사용하여 메서드를 실행할 수 있습니다. Edge 서비스는 Azure IoT Edge 모듈로 구현되고 온-프레미스에서 실행됩니다. 클라우드 마이크로 서비스는 REST 인터페이스를 사용하여 ASP.NET 마이크로 서비스로 구현되고 Azure App Service에서 관리형 Azure Kubernetes Services 또는 독립 실행형으로 실행됩니다. Edge 서비스와 클라우드 서비스 모두에서 IIoT가 MCR(Microsoft Container Registry)에 미리 빌드된 Docker 컨테이너를 제공하므로 고객이 이 단계를 수행할 필요가 없습니다. Edge 서비스와 클라우드 서비스는 서로를 활용하며 함께 사용해야 합니다. 또한 IIoT는 단일 명령으로 전체 플랫폼을 배포할 수 있는 사용하기 간편한 배포 스크립트도 제공합니다.

그리고 공개된 Open API 사양(Swagger)을 통해 모든 프로그래밍 언어에서 REST API를 사용할 수 있습니다. 즉, 개발자가 OPC UA를 클라우드 관리 솔루션에 통합할 때 자신의 스킬, 관심사 및 원하는 아키텍처와 일치하는 기술을 자유롭게 선택할 수 있습니다. 예를 들어 경보 및 이벤트 대시보드용 애플리케이션을 개발하는 풀 스택 웹 개발자는 OPC UA SDK, C, C++, Java 또는 C#을 늘리지 않고도 JavaScript 또는 TypeScript로 이벤트에 응답하는 로직을 작성할 수 있습니다.

## <a name="manage-certificates-and-trust-groups"></a>인증서 및 신뢰 그룹 관리

Azure IIoT는 OPC UA 애플리케이션 인증서와 제조 현장 기계/제어 시스템의 신뢰 목록을 관리하여 OPC UA 클라이언트와 서버 간 통신의 보안을 유지합니다. 서버와 통신할 수 있는 클라이언트를 제한합니다. 프라이빗 키의 저장 및 인증서 서명이 HSM(하드웨어 기반 보안)을 지원하는 Azure Key Vault에서 지원됩니다.

## <a name="industrial-iot-components"></a>산업용 IoT 구성 요소

Azure IIoT 솔루션은 특정 구성 요소로부터 빌드됩니다. 여기에 다음이 포함 됩니다.

- **하나 이상의 Azure IoT Hub.**
- **IoT Edge 디바이스.**
- **산업용 Edge 모듈.**

### <a name="iot-hub"></a>IoT Hub
[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/ )는 IoT 애플리케이션과 이를 통해 관리되는 디바이스 간의 양방향 보안 통신을 위한 중앙 메시지 허브 역할을 합니다. 오픈 소스 SDK와 여러 프로토콜을 지원하는 개방형의 유연한 클라우드 PaaS(Platform as a Service)입니다. 

산업 및 비즈니스 데이터를 IoT Hub에 수집하면 데이터를 안전하게 저장하고, 비즈니스 및 효율성 분석을 수행하고, 보고서를 생성할 수 있습니다. 또한 [Power BI](https://powerbi.microsoft.com)와 같은 Microsoft Azure 서비스 및 도구를 통합 데이터에 적용할 수 있습니다.

### <a name="iot-edge-devices"></a>IoT Edge 디바이스
[Edge 서비스](https://azure.microsoft.com/services/iot-edge/)는 Azure IoT Edge 모듈로 구현되며 온-프레미스에서 실행됩니다. 클라우드 마이크로 서비스는 REST 인터페이스를 사용하여 ASP.NET 마이크로 서비스로 구현되고 Azure App Service에서 관리형 Azure Kubernetes Services 또는 독립 실행형으로 실행됩니다. Edge 서비스와 클라우드 서비스 둘 다 MCR(Microsoft Container Registry)에 미리 빌드된 Docker 컨테이너가 제공되므로 고객이 이 단계를 수행할 필요가 없습니다. Edge 서비스와 클라우드 서비스는 서로를 활용하며 함께 사용해야 합니다. 또한 단일 명령으로 전체 플랫폼을 배포할 수 있는 사용하기 쉬운 배포 스크립트도 제공했습니다.

IoT Edge 디바이스는 Edge 런타임과 Edge 모듈로 구성됩니다.
- **Edge 모듈** 은 OPC 게시자 및 OPC 트윈과 같이 가장 작은 계산 단위인 Docker 컨테이너입니다. 
- **Edge 디바이스** 는 클라우드에서 OPC UA 서버와 IoT Hub 간의 중재자 역할을 하는 이러한 모듈을 배포하는 데 사용됩니다.

### <a name="industrial-edge-modules"></a>산업용 Edge 모듈
- **OPC 게시자**: OPC 게시자는 IoT Edge 내에서 실행됩니다. OPC UA 서버에 연결하고, 이러한 서버의 JSON 인코딩 원격 분석 데이터를 OPC UA "Pub/Sub" 형식으로 Azure IoT Hub에 게시합니다. Azure IoT Hub 클라이언트 SDK에서 지원하는 모든 전송 프로토콜(예: HTTPS, AMQP 및 MQTT)을 사용할 수 있습니다.
- **OPC 트윈**: OPC 트윈은 Azure IoT Edge 및 IoT Hub를 사용하여 클라우드와 공장 네트워크를 연결하는 마이크로서비스로 구성됩니다. OPC 쌍은 REST API를 통한 산업용 디바이스의 검색, 등록 및 원격 제어를 제공합니다. OPC 트윈에는 OPC UA(OPC 통합 아키텍처) SDK가 필요하지 않습니다. 프로그래밍 언어에 독립적이며, 서버리스 워크플로에 포함될 수 있습니다.
- **검색**: Discoverer ID로 표시되는 검색 모듈은 에지에서 OPC UA 서버 검색을 포함한 검색 서비스를 제공합니다. 검색이 구성되고 사용하도록 설정되면 모듈에서 IoT Edge 및 IoT Hub 원격 분석 경로를 통해 검사 프로브 결과를 온보딩 서비스에 보냅니다. 서비스에서 결과를 처리하고 레지스트리의 모든 관련 ID를 업데이트합니다.

## <a name="next-steps"></a>다음 단계
이제 산업용 IoT에 대해 알아보았으므로 산업용 IoT 플랫폼과 OPC 게시자에 대해 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [OPC 게시자란?](overview-what-is-opc-publisher.md)