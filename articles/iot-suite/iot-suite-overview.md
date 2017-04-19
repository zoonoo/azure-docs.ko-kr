---
title: "Microsoft Azure IoT Suite 개요 | Microsoft Docs"
description: "Azure IoT Suite에서 미리 구성된 사물 인터넷 솔루션을 제공하여 데이터를 수집, 분석 및 저장하고 시각화를 제공하며 다른 시스템과 통합하는 방법의 개요입니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: ecae2cb9c0cdc78226c100cd287b840b6b2a6bb8
ms.lasthandoff: 04/18/2017


---
# <a name="overview-of-azure-iot-suite"></a>Azure IoT Suite의 개요
Azure IoT(사물 인터넷) 서비스는 광범위한 기능을 제공합니다. 이러한 엔터프라이즈급 서비스를 사용하면 다음과 같은 작업을 할 수 있습니다.

* 장치에서 데이터 수집
* 동작 내 데이터 스트림 분석
* 큰 데이터 집합 저장 및 쿼리
* 실시간 및 기록 데이터 시각화
* 백 오피스 시스템과 통합
* 장치 관리

이러한 기능을 제공하기 위해 Azure IoT Suite는 사용자 지정 확장이 포함된 여러 Azure 서비스를 *미리 구성된 솔루션*으로 패키지화했습니다. 이러한 미리 구성된 솔루션은 IoT 솔루션을 제공하는 데 걸릴 시간을 줄이는 데 도움이 되는 일반적인 IoT 솔루션 패턴의 기본 구현입니다. [IoT 소프트웨어 개발자 키트][lnk-sdks]를 통해 이러한 솔루션을 사용자 지정 및 확장하여 사용자의 요구 사항을 충족할 수 있습니다. 또한 새로운 IoT 솔루션을 개발할 때 이러한 솔루션을 예제 또는 템플릿으로 사용할 수도 있습니다.

다음 비디오는 Azure IoT Suite에 대한 소개를 제공합니다.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON309/player]
> 
> 

## <a name="azure-iot-services-in-azure-iot-suite"></a>Azure IoT Suite에서 Azure IoT 서비스
미리 구성된 솔루션은 일반적으로 다음 서비스를 사용합니다.

* Azure IoT Suite의 핵심은 [Azure IoT Hub][lnk-iot-hub] 서비스입니다. 이 서비스는 장치-클라우드 및 클라우드-장치 메시징 기능을 제공하고 클라우드 및 다른 핵심 IoT Suite 서비스에 대한 게이트웨이의 역할을 합니다. 이 서비스를 사용하면 장치로부터 대규모 메시지를 수신하고 장치로 명령을 보낼 수 있습니다. 서비스를 통해 [장치를 관리][lnk-device-management]할 수도 있습니다. 예를 들어 허브에 연결된 하나 이상의 장치에서 공장 재설정을 구성, 다시 부팅 또는 수행할 수 있습니다.
* [Azure Stream Analytics][lnk-asa]는 모션 내 데이터 분석을 제공합니다. IoT Suite는 이 서비스를 사용하여 들어오는 원격 분석을 처리하고 집계를 수행하며 이벤트를 검색합니다. 또는 미리 구성된 솔루션은 스트림 분석을 사용하여 장치에서 메타데이터 또는 명령 응답과 같은 데이터를 포함하는 정보 메시지를 처리합니다. 이 솔루션은 스트림 분석을 사용하여 장치로부터 받은 메시지를 처리하고 이러한 메시지를 다른 서비스로 전달합니다.
* [Azure Storage][lnk-azure-storage] 및 [Azure DocumentDB][lnk-document-db]는 데이터 저장소 기능을 제공합니다. 미리 구성된 솔루션에서 Blob 저장소를 사용하여 원격 분석을 저장하고 분석을 위해 사용할 수 있도록 합니다. 이 솔루션은 DocumentDB를 사용하여 장치 메타데이터를 저장하고 솔루션의 장치 관리 기능을 사용하도록 설정합니다.
* [Azure Web Apps][lnk-web-apps] 및 [Microsoft Power BI][lnk-power-bi]는 데이터 시각화 기능을 제공합니다. Power BI의 유연성을 통해 IoT Suite 데이터를 사용하는 고유한 대화형 대시보드를 신속하게 빌드할 수 있습니다.

일반적인 IoT 솔루션의 아키텍처에 대한 개요는 [Microsoft Azure 및 IoT(사물 인터넷)][iot-suite-what-is-azure-iot]를 참조하세요.

## <a name="preconfigured-solutions"></a>미리 구성된 솔루션
IoT 제품군은 일반적인 IoT 시나리오(예: *원격 모니터링* 및 *예측 유지 관리*)를 신속하게 시작하고 탐색해 볼 수 있는 미리 구성된 솔루션을 포함합니다. Azure 구독에 이들 솔루션을 배포하여 완전한 종단 간 IoT 시나리오를 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계
IoT Suite에서 수행할 수 있는 작업 및 해당 주요 구성 요소의 개요를 알았으므로 IoT Suite에서 미리 구성된 솔루션에 대해 자세히 알아볼 수 있습니다. 자세한 내용은 [미리 구성된 Azure IoT 솔루션은 무엇입니까?][lnk-what-are-preconfig]를 참조하세요.

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md

