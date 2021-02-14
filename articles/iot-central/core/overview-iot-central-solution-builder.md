---
title: Azure IoT Central에 대 한 솔루션 빌드 | Microsoft Docs
description: Azure IoT Central은 IoT 솔루션 만들기를 간소화하는 IoT 애플리케이션 플랫폼입니다. 이 문서에서는 IoT Central를 사용 하 여 통합 솔루션을 구축 하는 개요를 제공 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 72aa8e5e3284e0ee7fbe63e0fb617b9eba03292e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417804"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central 솔루션 빌더 가이드

이 문서는 솔루션 빌더에 적용됩니다.

IoT Central 애플리케이션을 사용하면 수백만 대의 디바이스를 수명 주기 전체 동안 모니터링하고 관리할 수 있습니다. 이 가이드는 IoT Central을 사용 하 여 통합 솔루션을 빌드하는 솔루션 빌더를 위한 것입니다. IoT Central 응용 프로그램을 사용 하 여 장치를 관리 하 고, 장치 원격 분석을 분석 하 고, 다른 백 엔드 서비스와 통합할 수 있습니다.

솔루션 빌더:

- IoT Central 웹 UI에서 대시보드 및 보기를 구성 합니다.
- 는 기본 제공 규칙 및 분석 도구를 사용 하 여 연결 된 장치에서 비즈니스 통찰력을 가져옵니다.
- 는 데이터 내보내기 및 규칙 기능을 사용 하 여 IoT Central을 다른 백 엔드 서비스와 통합 합니다.

## <a name="configure-dashboards-and-views"></a>대시보드 및 보기 구성

IoT Central 응용 프로그램에는 운영자가 응용 프로그램을 보고 상호 작용 하는 데 사용 하는 하나 이상의 대시보드가 있을 수 있습니다. 솔루션 빌더는 기본 대시보드를 사용자 지정 하 고 특수 한 대시보드를 만들 수 있습니다.

- 사용자 지정 대시보드의 몇 가지 예를 보려면 [업계 집중 된 템플릿](concepts-app-templates.md#industry-focused-templates)을 참조 하세요.
- 대시보드에 대해 자세히 알아보려면 [여러 대시보드 만들기 및 관리](howto-create-personal-dashboards.md) 및 [응용 프로그램 대시보드 구성](howto-add-tiles-to-your-dashboard.md)을 참조 하세요.

장치가 IoT Central에 연결 되 면 장치는 장치 유형에 대 한 장치 템플릿과 연결 됩니다. 장치 템플릿에는 운영자가 개별 장치를 관리 하는 데 사용 하는 사용자 지정 가능한 보기가 있습니다. 솔루션 개발자는 장치 유형에 대해 사용 가능한 보기를 만들고 사용자 지정할 수 있습니다. 자세히 알아보려면 [뷰 추가](howto-set-up-template.md#add-views)를 참조 하세요.

## <a name="use-built-in-rules-and-analytics"></a>기본 제공 규칙 및 분석 사용

솔루션 개발자는 사용자 지정 가능한 작업을 실행 하는 IoT Central 응용 프로그램에 규칙을 추가할 수 있습니다. 규칙은 장치에서 들어오는 데이터를 기반으로 조건을 평가 하 여 작업 실행 시기를 결정 합니다. 규칙에 대 한 자세한 내용은 다음을 참조 하세요.

- [자습서: Azure IoT Central 애플리케이션에서 규칙 생성 및 알림 설정](tutorial-create-telemetry-rules.md)
- [Azure IoT Central에서 규칙에 대해 웹후크 작업 만들기](howto-create-webhooks.md)
- [하나 이상의 규칙에서 실행할 여러 작업 그룹화](howto-use-action-groups.md)

IoT Central에는 운영자가 연결 된 장치에서 흐르는 데이터를 분석 하는 데 사용할 수 있는 기본 제공 분석 기능이 있습니다. 자세히 알아보려면 analytics를 사용 하 여 [장치 데이터를 분석 하는 방법](howto-create-analytics.md)을 참조 하세요.

## <a name="integrate-with-other-services"></a>다른 서비스와 통합

솔루션 빌더는 IoT Central의 데이터 내보내기 및 규칙 기능을 사용 하 여 다른 서비스와 통합할 수 있습니다. 자세히 알아보려면 다음을 참조하세요.

- [데이터 내보내기를 사용 하 여 클라우드 대상으로 IoT 데이터 내보내기](howto-export-data.md)
- [워크플로를 사용하여 Azure IoT Central 애플리케이션을 다른 클라우드 서비스와 통합](howto-configure-rules-advanced.md)
- [Stream Analytics, Azure Functions 및 SendGrid를 사용하여 사용자 지정 규칙으로 Azure IoT Central 확장](howto-create-custom-rules.md)
- [Azure Databricks를 사용 하 여 사용자 지정 분석으로 Azure IoT Central 확장](howto-create-custom-analytics.md)
- [Power BI 대시보드에서 Azure IoT Central 데이터 시각화 및 분석](howto-connect-powerbi.md)

## <a name="next-steps"></a>다음 단계

IoT Central 사용에 대해 자세히 알아보려면 [Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md)부터 시작하여 빠른 시작을 완료하세요.
