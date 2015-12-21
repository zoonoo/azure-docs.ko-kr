<properties
	pageTitle="Microsoft Azure IoT Suite 개요 | Microsoft Azure"
	description="패키징 및 미리 구성된 솔루션을 포함한 Azure IoT Suite에 대한 개요를 제공합니다."
	services=""
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/27/2015"
     ms.author="dobett"/>

# Azure IoT Suite의 개요

Azure IoT 서비스는 광범위한 기능을 제공합니다. 이러한 엔터프라이즈급 서비스를 사용하면 다음과 같은 작업을 할 수 있습니다.

- 장치에서 데이터 수집
- 동작 내 데이터 스트림 분석
- 큰 데이터 집합 저장 및 쿼리
- 실시간 및 기록 데이터 시각화
- 백 오피스 시스템과 통합

Azure IoT Suite 패키지는 일반적으로 사용자 지정 확장을 통해 Azure 서비스를 미리 구성된 솔루션으로 활용했습니다. 이러한 미리 구성된 솔루션은 IoT 솔루션을 제공하는 데 걸릴 시간을 줄이는 데 도움이 되는 일반적인 IoT 솔루션 패턴의 기본 구현입니다. [IoT 소프트웨어 개발 키트][lnk-sdks]를 사용하여 사용자의 요구 사항을 충족하거나 새로운 솔루션을 개발할 때 예제로 활용하기 위해 이러한 미리 구성된 솔루션을 쉽게 사용자 지정할 수 있습니다.

다음 비디오는 Azure IoT Suite에 대한 소개를 제공합니다.

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Azure IoT Suite에서 Azure IoT 서비스

Azure IoT Suite의 핵심은 [Azure IoT Hub][lnk-iot-hub] 서비스입니다. 이 서비스는 장치-클라우드 및 클라우드-장치 메시징 기능을 제공하고 클라우드 및 다른 핵심 IoT Suite 서비스에 대한 게이트웨이의 역할을 합니다.

[Azure 스트림 분석][lnk-asa]은 모션 내 데이터 분석을 제공합니다. IoT Suite는 이 서비스를 활용하여 들어오는 원격 분석을 처리하고 집계를 수행하며 이벤트를 검색합니다. 또는 미리 구성된 솔루션은 스트림 분석을 사용하여 장치에서 메타데이터 또는 명령 응답과 같은 데이터를 포함하는 정보 메시지를 처리합니다.

[Azure 저장소][lnk-azure-storage] 및 [Azure DocumentDB][lnk-document-db]는 데이터 저장소 기능을 제공합니다. 미리 구성된 솔루션에서 Blob 저장소를 사용하여 원격 분석을 저장하고 분석을 위해 사용할 수 있도록 합니다. 미리 구성된 솔루션은 장치 메타데이터를 관리하기 위해 DocumentDB의 반 구조화된 데이터 기능에 인덱싱된 저장소를 사용합니다. 이를 통해 솔루션이 다른 콘텐츠 저장소 요구 사항이 있는 유형이 다른 장치를 관리합니다.

[Azure 웹앱][lnk-web-apps] 및 [Microsoft Power BI][lnk-power-bi]는 데이터 시각화 기능을 제공합니다. Power BI의 유연성을 통해 IoT Suite 데이터를 사용하는 고유한 대화형 대시보드를 신속하게 빌드할 수 있습니다.

일반적인 IoT 솔루션의 아키텍처에 대한 개요는 [Microsoft Azure 및 사물 인터넷(IoT)][iot-suite-what-is-azure-iot]을 참조하세요.

## 미리 구성된 솔루션

IoT 제품군은에 Azure IoT Suite가 가능하게 하는 일반적인 IoT 시나리오를 신속하게 시작하고 탐색해 볼 수 있는 미리 구성된 솔루션을 포함합니다. Azure 구독에 미리 구성된 솔루션을 배포하고 완전한 종단 간 IoT 솔루션을 실행할 수 있습니다.

## 다음 단계

IoT Suite에서 미리 구성된 솔루션에 대해 자세히 알아보려면 [Azure IoT 미리 구성된 솔루션이란?][lnk-what-are-preconfig]을 참조하세요.

미리 구성된 솔루션 중 하나를 사용하여 시작하려면 [IoT 미리 구성한 솔루션 시작][lnk-preconfig-start]을 참조하세요.

Azure IoT Hub 서비스에 대한 자세한 내용을 보려면 [IoT Hub 설명서][lnk-iot-hub]를 참조하세요.


[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-preconfig-start]: iot-suite-getstarted-preconfigured-solutions/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/

<!---HONumber=AcomDC_1210_2015-->