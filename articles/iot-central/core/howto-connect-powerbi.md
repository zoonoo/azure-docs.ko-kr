---
title: Power BI 대시보드에서 Azure IoT Central 데이터 시각화 | Microsoft Docs
description: Azure IoT Central용 Power BI 솔루션을 사용하여 IoT Central 데이터를 시각화하고 분석할 수 있습니다.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: 571f338345a8fe87c47609e9d50cef7b9e0f5711
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529662"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Power BI 대시보드에서 Azure IoT Central 데이터 시각화 및 분석

> [!Important]
> 이 솔루션은 [레거시 데이터 내보내기 기능](./howto-export-data-legacy.md)을 사용합니다. 최신 데이터 내보내기를 사용하여 Power BI에 연결하는 방법에 대한 업데이트된 지침을 계속 주목해 주세요.

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Power BI 솔루션 파이프라인":::

Azure IoT Central V3용 Power BI 솔루션을 사용하여 IoT 디바이스의 성능을 모니터링하는 강력한 Power BI 대시보드를 만들 수 있습니다. Power BI 대시보드에서 다음을 수행할 수 있습니다.

- 시간이 지남에 따라 디바이스가 보내는 데이터의 양 추적
- 다양한 원격 분석 스트림 간의 데이터 볼륨 비교
- 특정 디바이스에서 보낸 데이터로 필터링
- 테이블에서 최신 원격 분석 데이터 보기

이 솔루션은 [레거시 데이터 내보내기](./howto-export-data-legacy.md) Azure Blob Storage 계정에서 데이터를 읽는 파이프라인을 설정합니다. 이 파이프라인은 Azure Functions, Azure Data Factory 및 Azure SQL Database를 사용하여 데이터를 처리하고 변환합니다. PBIX 파일로 다운로드하는 Power BI 보고서의 데이터를 시각화하고 분석할 수 있습니다. 이 리소스는 모두 Azure 구독에서 만들어지므로, 필요에 맞게 각 구성 요소를 사용자 지정할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드의 단계를 완료하려면 다음이 필요합니다.

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

- 원격 분석과 디바이스, 디바이스 템플릿을 Azure Blob Storage로 내보내도록 구성된 레거시 연속 데이터 내보내기. 자세한 내용은 [레거시 데이터 내보내기 설명서](howto-export-data-legacy.md)를 참조하세요.
  - IoT Central 애플리케이션만 Blob 컨테이너로 데이터를 내보내고 있는지 확인합니다.
  - [디바이스에서 JSON 인코딩된 메시지를 전송해야](../../iot-hub/iot-hub-devguide-messages-d2c.md) 합니다. 디바이스에서는 메시스 시스템 속성에 `contentType:application/JSON` 및 `contentEncoding:utf-8` 또는 `contentEncoding:utf-16` 또는 `contentEncoding:utf-32`를 지정해야 합니다.
- Power BI Desktop(최신 버전) [Power BI 다운로드](https://powerbi.microsoft.com/downloads/)를 참조하세요.
- Power BI Pro(다른 사용자와 대시보드를 공유하려는 경우)

> [!NOTE]
> 버전 2 IoT Central 애플리케이션을 사용하는 경우 이전 버전 설명서 사이트에 있는 [Power BI 대시보드에서 Azure IoT Central 데이터 시각화 및 분석](/previous-versions/azure/iot-central/core/howto-connect-powerbi)을 참조하세요.

## <a name="install"></a>설치

파이프라인을 설정하려면 **Microsoft AppSource** 사이트에서 [Azure IoT Central V3용 Power BI 솔루션](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) 페이지로 이동합니다. **지금 가져오기** 를 선택하고 지침을 따릅니다.

PBIX 파일을 열 때 표지의 지침을 읽고 따르십시오. 이러한 지침에서는 보고서를 SQL 데이터베이스에 연결하는 방법을 설명합니다.

## <a name="report"></a>보고서

PBIX 파일에는 **디바이스 및 원격 분석** 보고서가 포함되며, 디바이스에서 보낸 원격 분석의 기록 보기가 나와 있습니다. 여기에는 다양한 유형의 원격 분석이 자세히 설명되어 있고, 디바이스에서 보낸 최신 원격 분석 정보도 나와 있습니다.

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Power BI 디바이스 및 원격 분석 보고서":::

## <a name="pipeline-resources"></a>파이프라인 리소스

Azure Portal에서 파이프라인을 구성하는 모든 Azure 리소스에 액세스할 수 있습니다. 모든 리소스는 파이프라인을 설정할 때 만든 리소스 그룹에 있습니다.

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="Azure Portal의 리소스 그룹 보기":::

다음 목록에서는 파이프라인에 있는 각 리소스의 역할을 설명합니다.

### <a name="azure-functions"></a>Azure Functions

Azure Function 앱은 IoT Central에서 Blob Storage에 새 파일을 쓸 때마다 트리거됩니다. 함수는 원격 분석과 디바이스, 디바이스 템플릿 Blob에서 데이터를 추출하여 Azure Data Factory에서 사용하는 중간 SQL 테이블을 채웁니다.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory는 연결된 서비스로 SQL 데이터베이스에 연결됩니다. 여기에서는 저장 프로시저를 실행하여 데이터를 처리하고 분석 테이블에 저장합니다.

Azure Data Factory는 15분마다 실행되어 SQL 테이블에 로드할 최신 데이터 일괄 처리를 변환합니다(**연속 창 트리거** 의 현재 최소 수).

### <a name="azure-sql-database"></a>Azure SQL Database

Azure Data Factory는 Power BI용 분석 테이블 세트를 생성합니다. Power BI에서 이러한 스키마를 탐색한 후 이를 사용해 고유한 시각화를 빌드할 수 있습니다.

## <a name="estimated-costs"></a>예상 비용

Microsoft AppSource 사이트의 [Azure IoT Central V3용 Power BI 솔루션](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) 페이지에는 배포하는 리소스에 대한 비용 예측 도구 링크가 포함됩니다.

## <a name="next-steps"></a>다음 단계

Power BI에서 데이터를 시각화하는 방법을 배웠으므로, 이제 제안된 다음 단계는 [디바이스 관리 방법](howto-manage-devices-individually.md)을 배우는 것입니다.
