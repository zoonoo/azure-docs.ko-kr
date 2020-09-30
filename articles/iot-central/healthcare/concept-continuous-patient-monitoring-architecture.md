---
title: Azure IoT Central의 지속적인 환자 모니터링 아키텍처 | Microsoft Docs
description: 자습서 - 지속적인 환자 모니터링 솔루션 아키텍처에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 09/14/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: ffecd09d1084188195da83568ab3fe32ef2cdaac
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90972228"
---
# <a name="continuous-patient-monitoring-architecture"></a>환자 연속 모니터링 아키텍처

이 문서에서는 **지속적인 환자 모니터링** 애플리케이션 템플릿에서 빌드된 솔루션의 아키텍처에 대해 설명합니다.

지속적인 환자 모니터링 솔루션은 제공된 앱 템플릿을 사용하고 아래에서 설명하는 아키텍처를 지침으로 사용하여 빌드할 수 있습니다.

:::image type="content" source="media/cpm-architecture.png" alt-text="환자 연속 모니터링 아키텍처":::

## <a name="details"></a>세부 정보

이 섹션에서는 아키텍처 다이어그램의 각 부분을 자세히 설명합니다.

### <a name="bluetooth-low-energy-ble-medical-devices"></a>BLE(Bluetooth Low Energy) 의료 디바이스

의료 IoT 솔루션에서 사용되는 다수의 의료 착용식 장치는 BLE 디바이스입니다. 이러한 디바이스는 클라우드와 직접 통신할 수 없으며 클라우드 솔루션과 데이터를 교환하는 데 게이트웨이를 사용해야 합니다. 이 아키텍처는 휴대폰 애플리케이션을 게이트웨이로 사용합니다.

### <a name="mobile-phone-gateway"></a>휴대폰 게이트웨이

휴대폰 애플리케이션의 기본 기능은 의료 디바이스에서 BLE 데이터를 수집하여 IoT Central에 전달하는 것입니다. 이 앱은 디바이스 설정을 통해 환자를 안내하고 개인 상태 데이터를 볼 수 있도록 합니다. 다른 솔루션은 병실에 있는 태블릿 게이트웨이 또는 정적 게이트웨이를 사용할 수 있습니다. Android 및 iOS용 오픈 소스 샘플 모바일 애플리케이션을 애플리케이션 개발의 출발점으로 사용할 수 있습니다. 자세한 내용은 [IoT Central Continuous Patient Monitoring 모바일 앱](https://docs.microsoft.com/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/)을 참조하세요.

### <a name="export-to-azure-api-for-fhirreg"></a>Azure API for FHIR에 배포&reg;

Azure IoT Central은 HIPAA 규격이며 HITRUST&reg; 인증을 받았습니다. [Azure API for FHIR](../../healthcare-apis/overview.md)을 사용하여 환자 상태 데이터를 다른 서비스로 보낼 수 있습니다. Azure API for FHIR은 임상 상태 데이터를 위한 표준 기반 API입니다. [FHIR용 Azure IoT 커넥터](https://docs.microsoft.com/azure/healthcare-apis/iot-fhir-portal-quickstart)를 사용하면 Azure API for FHIR을 IoT Central의 연속 데이터 내보내기 대상으로 사용할 수 있습니다.

### <a name="machine-learning"></a>기계 학습

FHIR 데이터와 함께 기계 학습 모델을 사용하여 인사이트를 생성하고 의료 팀의 의사 결정을 지원합니다. 자세한 내용은 [Azure 기계 학습 설명서](../../machine-learning/index.yml)를 참조하세요.

### <a name="provider-dashboard"></a>공급자 대시보드

Azure API for FHIR 데이터를 사용하여 환자 인사이트 대시보드를 빌드하거나 의료 팀에서 사용하는 전자 의료 기록에 직접 통합합니다. 의료 팀은 대시보드를 사용하여 환자를 지원하고 악화되는 상태의 조기 경고 징후를 식별할 수 있습니다. 자세한 내용은 [Power BI 공급자 대시보드 빌드](howto-health-data-triage.md) 자습서를 참조하세요.

## <a name="next-steps"></a>다음 단계

제안된 다음 단계는 [지속적인 환자 모니터링 애플리케이션 템플릿 배포 방법 알아보기](tutorial-continuous-patient-monitoring.md)에 대한 것입니다.
