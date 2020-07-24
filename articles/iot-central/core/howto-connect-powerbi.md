---
title: Power BI 대시보드에서 Azure IoT Central 데이터 시각화 | Microsoft Docs
description: Azure IoT Central용 Power BI 솔루션을 사용하여 IoT Central 데이터를 시각화하고 분석할 수 있습니다.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: f996bb2d5126ef038ca872aee1f1893979a5229b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "87081001"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Power BI 대시보드에서 Azure IoT Central 데이터 시각화 및 분석

*이 항목은 관리자 및 솔루션 개발자에 게 적용 됩니다.*

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Power BI 솔루션 파이프라인":::

Azure IoT Central V3의 Power BI 솔루션을 사용 하 여 IoT 장치의 성능을 모니터링 하는 강력한 Power BI 대시보드를 만들 수 있습니다. Power BI 대시보드에서 다음을 수행할 수 있습니다.

- 시간이 지남에 따라 디바이스가 보내는 데이터의 양 추적
- 서로 다른 원격 분석 스트림 간의 데이터 볼륨 비교
- 특정 장치에서 보낸 데이터로 필터링
- 테이블에서 최신 원격 분석 데이터 보기

이 솔루션은 [연속 데이터 내보내기](howto-export-data-blob-storage.md) Azure Blob storage 계정에서 데이터를 읽는 파이프라인을 설정 합니다. 파이프라인은 Azure Functions, Azure Data Factory 및 Azure SQL Database를 사용 하 여 데이터를 처리 하 고 변환 합니다. .PBIX 파일로 다운로드 하는 Power BI 보고서의 데이터를 시각화 하 고 분석할 수 있습니다. 모든 리소스는 Azure 구독에 만들어지므로 요구에 맞게 각 구성 요소를 사용자 지정할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 가이드의 수행 단계를 완료하려면 활성 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

솔루션을 설정 하려면 다음 리소스가 필요 합니다.

- 버전 3 IoT Central 응용 프로그램입니다. 응용 프로그램 버전을 확인 하는 방법에 [대 한 자세한 내용은 응용 프로그램 정보](./howto-get-app-info.md)를 참조 하세요. IoT Central 응용 프로그램을 만드는 방법에 대 한 자세한 내용은 [Azure IoT Central 응용 프로그램 만들기](./quick-deploy-iot-central.md)를 참조 하세요.
- Azure Blob storage에 원격 분석, 장치 및 장치 템플릿을 내보내도록 구성 된 연속 데이터 내보내기 자세히 알아보려면 [Azure에서 IoT 데이터를 대상으로 내보내는 방법](howto-export-data.md)을 참조 하세요.
  - IoT Central 응용 프로그램만 blob 컨테이너로 데이터를 내보내고 있는지 확인 합니다.
  - [장치에서 JSON 인코딩된 메시지를 전송 해야](../../iot-hub/iot-hub-devguide-messages-d2c.md)합니다. 장치 `contentType:application/JSON` `contentEncoding:utf-8` `contentEncoding:utf-16` `contentEncoding:utf-32` 는 메시지 시스템 속성에서 and 또는 or를 지정 해야 합니다.
- Power BI Desktop (최신 버전). [Power BI 다운로드](https://powerbi.microsoft.com/downloads/)를 참조 하세요.
- Power BI Pro (대시보드를 다른 사용자와 공유 하려는 경우).

> [!NOTE]
> 버전 2 IoT Central 응용 프로그램을 사용 하는 경우 이전 버전 설명서 사이트의 [Power BI 대시보드에서 Azure IoT Central 데이터 시각화 및 분석](https://docs.microsoft.com/previous-versions/azure/iot-central/core/howto-connect-powerbi) 을 참조 하세요.

## <a name="install"></a>설치

파이프라인을 설정 하려면 **Microsoft AppSource** 사이트에서 [Azure IoT Central V3에 대 한 Power BI 솔루션](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) 페이지로 이동 합니다. **지금 가져오기**를 선택 하 고 지침을 따릅니다.

.PBIX 파일을 열 때 읽기를 확인 하 고 커버 페이지의 지침을 따르세요. 이러한 지침은 보고서를 SQL 데이터베이스에 연결 하는 방법을 설명 합니다.

## <a name="report"></a>보고서

.PBIX 파일에는 **장치 및 원격 분석** 보고서에 장치에서 보낸 원격 분석의 기록 보기가 표시 됩니다. 이 문서에서는 다양 한 유형의 원격 분석에 대 한 분석을 제공 하 고 장치에서 보낸 최신 원격 분석도 보여 줍니다.

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Power BI 장치 및 원격 분석 보고서":::

## <a name="pipeline-resources"></a>파이프라인 리소스

Azure Portal에서 파이프라인을 구성 하는 모든 Azure 리소스에 액세스할 수 있습니다. 모든 리소스는 파이프라인을 설정할 때 만든 리소스 그룹에 있습니다.

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="리소스 그룹의 Azure Portal 보기":::

다음 목록에서는 파이프라인의 각 리소스에 대 한 역할을 설명 합니다.

### <a name="azure-functions"></a>Azure Functions

Azure 함수 앱은 IoT Central 때마다 트리거되고 Blob storage에 새 파일을 쓸 수 있습니다. 함수는 원격 분석, 장치 및 장치 템플릿 blob에서 데이터를 추출 하 여 Azure Data Factory에서 사용 하는 중간 SQL 테이블을 채웁니다.

### <a name="azure-data-factory"></a>Azure 데이터 팩터리

Azure Data Factory 연결 된 서비스로 SQL Database에 연결 됩니다. 저장 프로시저를 실행 하 여 데이터를 처리 하 고 분석 테이블에 저장 합니다.

Azure Data Factory은 15 분 마다 실행 되어 SQL 테이블로 로드 되도록 최신 일괄 처리 데이터를 변환 합니다 ( **연속 창 트리거의**현재 최소 수).

### <a name="azure-sql-database"></a>Azure SQL Database

Azure Data Factory는 Power BI에 대 한 분석 테이블 집합을 생성 합니다. Power BI에서 이러한 스키마를 탐색 하 고이를 사용 하 여 고유한 시각화를 빌드할 수 있습니다.

## <a name="estimated-costs"></a>예상 비용

Microsoft AppSource 사이트의 [Azure IoT Central V3에 대 한 Power BI 솔루션](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) 페이지에는 배포 하는 리소스에 대 한 비용 평가기 링크가 포함 됩니다.

## <a name="next-steps"></a>다음 단계

Power BI에서 데이터를 시각화 하는 방법을 배웠으므로 이제 제안 된 다음 단계는 [장치를 관리 하는 방법을](howto-manage-devices.md)배우는 것입니다.
