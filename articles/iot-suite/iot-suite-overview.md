<properties
	pageTitle="Microsoft Azure IoT Suite 개요 | Microsoft Azure"
	description="패키징 및 미리 구성된 솔루션을 포함한 Azure IoT Suite에 대한 개요를 제공합니다."
	services=""
	documentationCenter=""
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/06/2015"
     ms.author="araguila"/>

# Azure IoT Suite의 개요

Microsoft는 Azure IoT 서비스를 사용한 다양한 기능을 제공합니다. 이러한 서비스는 장치에서 데이터의 수집, 데이터 스트림 인모션의 분석, 큰 데이터 집합의 저장 및 쿼리, 실시간 및 기록 데이터 모두의 시각화, 중요한 백 오피스 시스템과 통합하는 엔터프라이즈급 기능을 제공합니다. Azure IoT Suite는 일반적으로 활용되는 서비스 및 고객의 가치 창출 시간을 절감할 수 있는 확장된 기능의 집합을 패키지합니다. 이러한 확장은 가장 일반적인 솔루션 패턴의 기본 구현을 제공하는 미리 구성된 솔루션을 포함합니다. IoT 소프트웨어 개발 키트(SDK)와 함께 고객은 미리 구성된 솔루션을 쉽게 사용자 지정하거나 새 솔루션의 개발을 위한 예제로 활용할 수 있습니다.

다음 비디오는 Azure IoT Suite에 대한 소개를 제공합니다.

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Azure IoT Suite에서 Azure IoT 서비스

Azure IoT Suite의 핵심은 IoT Hub 서비스입니다. 이 서비스는 장치-클라우드 및 클라우드-서비스 메시징을 제공합니다. 클라우드 및 기타 주요 IoT Suite 서비스에 게이트웨이로 작동합니다.

인모션 데이터 분석은 Azure 스트림 분석에서 제공됩니다. IoT Suite는 들어오는 원격 분석 메시지를 처리하고 집계를 수행하며 이벤트를 감지하는 데 이 서비스를 활용합니다. 또한 장치 메타데이터 또는 명령 응답에 사용할 수 있는 정보 메시지를 처리하는 데 사용됩니다.

Azure 저장소 및 Azure Document DB의 결합을 통해 데이터 저장소 기능을 사용할 수 있습니다. Azure 저장소는 유지 및 이후 분석에 대한 원격 분석의 Blob 저장소를 사용할 수 있습니다. Document DB는 장치 메타데이터를 관리하는 반 구조화된 데이터에 인덱싱된 저장소 기능에 활용됩니다. 이를 통해 다른 장치가 다른 콘텐츠를 갖도록 하여 형식이 다른 장치를 관리할 수 있게 합니다.

데이터의 시각화는 Azure 웹 사이트 및 Microsoft Power BI의 조합으로 제공됩니다. Power BI의 유연성을 통해 고객은 IoT Suite 데이터에서 고유한 대화형 대시보드를 신속하게 빌드할 수 있습니다.

아키텍처 및 이러한 서비스를 사용하는 방법에 대한 자세한 세부 사항은 [Microsoft Azure 및 IoT(사물 인터넷)](iot-suite-what-is-azure-iot.md) 문서에서 찾을 수 있습니다.

## 미리 구성된 솔루션

미리 구성된 솔루션은 Azure IoT Suite에 포함되어 고객이 Azure IoT Suite에서 가능한 시나리오를 신속하게 시작하고 탐색할 수 있게 합니다.

첫 번째 미리 구성된 솔루션은 [Remote Monitoring](iot-suite-what-are-preconfigured-solutions.md)입니다.

<!---HONumber=Nov15_HO1-->