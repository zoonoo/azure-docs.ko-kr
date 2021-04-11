---
title: Azure 산업용 IoT 플랫폼
description: 이 문서에서는 산업용 IoT 플랫폼과 해당 구성 요소에 대한 개요를 제공합니다.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 24932b17c630190cb3121d5310a865758fa6a920
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801556"
---
# <a name="what-is-the-industrial-iot-iiot-platform"></a>IIoT(산업용 IoT) 플랫폼이란?

Azure 산업용 IoT 플랫폼은 Azure에 배포되는 Microsoft 모듈 및 서비스 제품군입니다. 이러한 모듈 및 서비스는 완전한 개방성을 제공합니다. 특히 Edge 및 클라우드에서 Azure의 관리형 PaaS(Platform as a Service) 제품, MIT 라이선스를 통해 사용이 허가된 오픈 소스 소프트웨어, 통신(OPC UA, AMQP, MQTT, HTTP) 및 인터페이스(Open API)의 개방형 국제 표준 그리고 개방형 산업용 데이터 모델(OPC UA)을 적용합니다.

## <a name="enabling-shopfloor-connectivity"></a>작업 현장 연결 지원 

Azure 산업용 IoT 플랫폼은 작업 현장 자산(OPC UA 지원 자산의 검색 포함)의 산업용 연결을 다루고, 이러한 자산의 데이터를 OPC UA 형식으로 정규화하고, 자산 원격 분석 데이터를 Azure에 OPC UA PubSub 형식으로 전송합니다. 원격 분석 데이터는 클라우드 데이터베이스에 저장됩니다. 뿐만 아니라 이 플랫폼을 사용하면 클라우드에서 OPC UA를 통해 작업 현장 자산에 안전하게 액세스할 수 있습니다. 장치 관리 기능(보안 구성 포함)도 기본 제공됩니다. 손쉬운 배포와 관리를 위해 OPC UA 기능은 Docker 컨테이너 기술을 사용하여 구축되었습니다. OPC UA를 사용하지 않는 자산의 경우 업계 유수의 산업용 연결 공급자와 협력하여 OPC UA 어댑터 소프트웨어를 Azure IoT Edge로 이식할 수 있도록 도와주었습니다. 이러한 어댑터는 Azure Marketplace에서 사용할 수 있습니다.

## <a name="industrial-iot-components-iot-edge-modules-and-cloud-microservices"></a>산업용 IoT 구성 요소: IoT Edge 모듈 및 클라우드 마이크로 서비스

Edge 서비스는 Azure IoT Edge 모듈로 구현되고 온-프레미스에서 실행됩니다. 클라우드 마이크로 서비스는 REST 인터페이스를 사용하여 ASP.NET 마이크로 서비스로 구현되고 Azure App Service에서 관리형 Azure Kubernetes Services 또는 독립 실행형으로 실행됩니다. Edge 서비스와 클라우드 서비스 둘 다 MCR(Microsoft Container Registry)에 미리 빌드된 Docker 컨테이너가 제공되므로 고객이 이 단계를 수행할 필요가 없습니다. Edge 서비스와 클라우드 서비스는 서로를 활용하며 함께 사용해야 합니다. 또한 단일 명령으로 전체 플랫폼을 배포할 수 있는 사용하기 쉬운 배포 스크립트도 제공했습니다.

## <a name="next-steps"></a>다음 단계

Azure 산업용 IoT 플랫폼에 대해 알아보았으므로, 이제 OPC 게시자에 대해 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [OPC 게시자란?](overview-what-is-opc-publisher.md)