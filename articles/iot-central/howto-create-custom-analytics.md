---
title: 사용자 지정 분석을 사용 하 여 Azure IoT Central 확장 | Microsoft Docs
description: 솔루션 개발자는 사용자 지정 분석 및 시각화를 위해 IoT Central 응용 프로그램을 구성 합니다. 이 솔루션에서는 Azure Databricks를 사용 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e039e2b8d9c183b5bfee1bee47e4addc4c873bf7
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743442"
---
# <a name="extend-azure-iot-central-with-custom-analytics"></a>사용자 지정 분석을 사용 하 여 Azure IoT Central 확장

이 방법 가이드에서는, 솔루션 개발자는 사용자 지정 분석 및 시각화와 IoT Central 응용 프로그램을 확장 하는 방법입니다. 이 예제에서는 사용을 [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) IoT Central 원격 분석 스트림을 분석 하 고 같은 시각화를 생성 하도록 작업 영역 [그림 상자](https://wikipedia.org/wiki/Box_plot)합니다.

이 방법 가이드 수행할 수 있는 이미 사용 하 여 초과 IoT Central을 확장 하는 방법을 보여 줍니다.는 [기본 제공 분석 도구](howto-create-analytics.md)합니다.

이 방법 가이드에 알아봅니다 방법:

* 사용 하 여 IoT Central 응용 프로그램에서 원격 분석을 Stream *연속 데이터 내보내기*합니다.
* Azure Databricks 환경을 분석 하 고 장치 원격 분석을 그림을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 가이드의 수행 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

### <a name="iot-central-application"></a>IoT Central 응용 프로그램

IoT Central 응용 프로그램에서 만들기를 [Azure IoT Central-내 응용 프로그램](https://aka.ms/iotcentral) 다음 설정으로 페이지:

| 설정 | 값 |
| ------- | ----- |
| 결제 계획 | Pay-As-You-Go |
| 애플리케이션 템플릿 | 샘플 Contoso |
| 응용 프로그램 이름 | 기본값을 적용 하거나 고유한 이름을 선택합니다 |
| URL | 기본값 또는 자신의 고유 URL 접두사를 선택 합니다. |
| 디렉터리 | Azure Active Directory 테 넌 트 |
| Azure 구독 | Azure 구독 |
| 지역 | 미국 동부 |

예제 및이 스크린샷을 사용 하 여 문서를 **미국 동부** 지역입니다. 가까운 위치를 선택 하 고 동일한 지역에 모든 리소스를 만드는 하는지 확인 합니다.

### <a name="resource-group"></a>리소스 그룹

사용 합니다 [리소스 그룹을 만들려면 Azure portal](https://portal.azure.com/#create/Microsoft.ResourceGroup) 호출 **IoTCentralAnalysis** 만든 다른 리소스를 포함 하도록 합니다. IoT Central 응용 프로그램과 동일한 위치에서 Azure 리소스를 만듭니다.

### <a name="event-hubs-namespace"></a>Event Hubs 네임스페이스

사용 된 [Event Hubs 네임 스페이스를 만들려면 Azure portal](https://portal.azure.com/#create/Microsoft.EventHub) 다음 설정을 사용 하 여:

| 설정 | 값 |
| ------- | ----- |
| 이름    | 네임 스페이스 이름을 선택합니다 |
| 가격 책정 계층 | Basic |
| 구독 | 사용자의 구독 |
| 리소스 그룹 | IoTCentralAnalysis |
| 위치 | 미국 동부 |
| 처리량 단위 | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks 작업 영역

사용 된 [Azure Databricks 서비스를 만들려면 Azure portal](https://portal.azure.com/#create/Microsoft.Databricks) 다음 설정을 사용 하 여:

| 설정 | 값 |
| ------- | ----- |
| 작업 영역 이름    | 작업 영역 이름 선택 |
| 구독 | 사용자의 구독 |
| 리소스 그룹 | IoTCentralAnalysis |
| 위치 | 미국 동부 |
| 가격 책정 계층 | Standard |

필요한 리소스를 만들었으면 하 **IoTCentralAnalysis** 다음 스크린샷과 같은 리소스 그룹은:

![IoT Central 분석 리소스 그룹](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

이벤트 허브로 원격 분석을 지속적으로 내보낼 IoT Central 응용 프로그램을 구성할 수 있습니다. 이 섹션에서는 IoT Central 응용 프로그램에서 원격 분석을 받기 위해 이벤트 허브를 만들 수 있습니다. 이벤트 허브는 처리를 위해 Stream Analytics 작업으로 원격 분석을 제공합니다.

1. Azure portal에서 Event Hubs 네임 스페이스로 이동 및 선택 **+ 이벤트 허브**합니다.
1. 이벤트 허브 이름 **centralexport**, 선택한 **만들기**합니다.
1. 네임 스페이스에서 event hubs의 목록에서 선택 **centralexport**합니다. 선택한 **공유 액세스 정책**합니다.
1. 선택 **+ 추가**합니다. 명명 된 정책 만들기 **수신** 사용 하 여 합니다 **수신** 클레임 합니다.
1. 정책 준비 되 면 목록에서 선택 하 고 복사 합니다 **연결 문자열-기본 키** 값입니다.
1. 이 연결 문자열에 기록해, 나중에 사용 하 고 이벤트 허브에서 읽을 Databricks 노트북을 구성한 경우.

Event Hubs 네임 스페이스는 다음 스크린샷과 같습니다.

![Event Hubs 네임스페이스](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>IoT Central의 내보내기 구성

로 이동 합니다 [IoT Central 응용 프로그램](https://aka.ms/iotcentral) Contoso 템플릿에서 생성 합니다. 이 섹션에서는 event hub에 시뮬레이션 된 장치에서 원격 분석을 스트리밍하려면 응용 프로그램을 구성 합니다. 내보내기를 구성 합니다.

1. 로 이동 합니다 **연속 데이터 내보내기** 페이지에서 **+ 새로 만들기**를 차례로 **Azure Event Hubs**합니다.
1. 다음 설정을 사용 하 여 내보내기를 구성 하 고 선택 **저장할**:

    | 설정 | 값 |
    | ------- | ----- |
    | 표시 이름 | Event Hubs로 내보내기 |
    | 사용 | 켜짐 |
    | Event Hubs 네임스페이스 | Event Hubs 네임 스페이스 이름 |
    | 이벤트 허브 | centralexport |
    | 측정값 | 켜짐 |
    | 디바이스 | 꺼짐 |
    | 디바이스 템플릿 | 꺼짐 |

![연속 데이터 내보내기 구성](media/howto-create-custom-analytics/cde-configuration.png)

내보내기 상태가 될 때까지 기다립니다 **실행** 계속 하기 전에 합니다.

## <a name="configure-databricks-workspace"></a>Databricks 작업 영역 구성

Azure portal에서 Azure Databricks 서비스를 이동 하 고 선택 **작업 영역 시작**합니다. 새 탭 브라우저에서 열리고 작업 영역에 로그인 합니다.

### <a name="create-a-cluster"></a>클러스터 만들기

에 **Azure Databricks** 페이지의 일반적인 작업을 선택 목록에서 **새 클러스터**합니다.

클러스터를 만들려면 다음 표에 정보를 사용 합니다.

| 설정 | 값 |
| ------- | ----- |
| 클러스터 이름 | centralanalysis |
| 클러스터 모드 | Standard |
| Databricks 런타임 버전 | 5.3 (Scala 2.11, Spark 2.4.0) |
| Python Version | 3 |
| 자동 크기 조정 사용 | 아닙니다. |
| 비활성 분 후 종료 | 30 |
| 작업자 유형 | Standard_DS3_v2 |
| 작업자 | 1 |
| 드라이버 유형 | 작업자와 동일 |

클러스터를 만드는 몇 분 정도 걸릴 수 있습니다, 그리고 클러스터 만들기를 계속 하기 전에 완료 될 때까지 기다립니다.

### <a name="install-libraries"></a>라이브러리 설치

에 **클러스터** 페이지에서 클러스터 상태가 될 때까지 대기 **실행**합니다.

다음 단계를 클러스터에 샘플에 필요한 라이브러리를 가져오는 방법을 보여 줍니다.

1. 에 **클러스터** 페이지에서 상태가 될 때까지 대기 합니다 **centralanalysis** 대화형 클러스터는 **실행**합니다.

1. 선택한 후 클러스터를 선택 합니다 **라이브러리** 탭 합니다.

1. 에 **라이브러리** 탭에서 **새 설치**합니다.

1. 에 **라이브러리를 설치** 페이지에서 **Maven** 라이브러리 원본으로 합니다.

1. 에 **조정** 텍스트 상자에 다음 값을 입력: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. 선택할 **설치** 클러스터에 라이브러리를 설치 합니다.

1. 라이브러리 상태가 이제 **설치 됨**:

    ![라이브러리가 설치 됨](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Databricks notebook 가져오기

Python 코드를 분석 및 IoT Central 원격 분석 시각화를 포함 하는 Databricks notebook을 가져오려면 다음 단계를 사용 합니다.

1. 로 이동 합니다 **작업 영역** Databricks 환경의 페이지입니다. 계정 이름 옆의 드롭다운을 선택 하 고 선택한 **가져오기**합니다.

1. URL에서 가져오기 및 다음 주소를 입력 하려면 선택 합니다. [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Notebook을 가져오려면 선택 **가져올**합니다.

1. 선택 된 **작업 영역** 가져온된 노트북을 보려면:

    ![가져온된 notebook](media/howto-create-custom-analytics/import-notebook.png)

1. 이전에 저장 하는 Event Hubs 연결 문자열에 추가할 첫 번째 Python 셀에서 코드를 편집 합니다.

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>분석 실행

분석을 실행 하려면 클러스터에 노트북을 연결 해야 합니다.

1. 선택 **Detached** 한 다음 선택 합니다 **centralanalysis** 클러스터.
1. 클러스터 실행 중이 아닌 경우 시작 합니다.
1. 노트북을 시작 하려면 실행된 단추를 선택 합니다.

마지막 셀에 오류가 표시 될 수 있습니다. 그렇다면에 이전 셀을 실행 하는 확인 일부 데이터 저장소에 쓸 잠시 기다린 후 마지막 셀을 다시 실행 합니다.

### <a name="view-smoothed-data"></a>평활된 데이터 보기

노트북에서 14 장치 유형에 따라 롤링 평균 습도 그림을 보려면 셀까지 아래로 스크롤하십시오. 이 그림 도착 하면 스트리밍 원격 분석을 지속적으로 업데이트 합니다.

![원격 분석 그림 평활](media/howto-create-custom-analytics/telemetry-plot.png)

Notebook의 차트 크기를 조정할 수 있습니다.

### <a name="view-box-plots"></a>보기 상자 플롯

노트북에서 셀 20 보려면 아래로 스크롤하여 합니다 [그림 상자](https://en.wikipedia.org/wiki/Box_plot)합니다. 상자 그림 기반으로 정적 데이터 하므로 업데이트 셀을 다시 실행 해야 합니다.

![상자 그림](media/howto-create-custom-analytics/box-plots.png)

노트북의 출력 크기를 조정할 수 있습니다.

## <a name="tidy-up"></a>정리

이 방법은 후 정리 하 고 불필요 한 비용이 발생 하지 않도록 하려면 삭제 합니다 **IoTCentralAnalysis** Azure portal에서 리소스 그룹입니다.

IoT Central 응용 프로그램을 삭제할 수 있습니다 합니다 **관리** 응용 프로그램 내의 페이지입니다.

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 다음 작업 방법을 배웁니다.

* 사용 하 여 IoT Central 응용 프로그램에서 원격 분석을 Stream *연속 데이터 내보내기*합니다.
* 원격 분석 데이터 분석 및 그리기를 Azure Databricks 환경을 만듭니다.

자세한 제안 된 다음 단계는 사용자 지정 분석을 만드는 방법을 배웠으므로 하는 방법 [시각화 하 고 Azure IoT Central Power BI 대시보드에서 데이터를 분석](howto-connect-powerbi.md)합니다.
