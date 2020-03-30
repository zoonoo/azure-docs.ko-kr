---
title: 사용자 지정 분석을 통해 Azure IoT 센트럴 확장 | 마이크로 소프트 문서
description: 솔루션 개발자는 사용자 지정 분석 및 시각화를 수행하도록 IoT Central 응용 프로그램을 구성합니다. 이 솔루션은 Azure 데이터 브릭을 사용합니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7c2c14a937b4ef55d0e5f71e7b20214428ecd68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158200"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Azure 데이터 브릭을 사용하여 사용자 지정 분석을 사용하여 Azure IoT Central 확장

이 방법 가이드는 솔루션 개발자로서 사용자 지정 분석 및 시각화를 통해 IoT Central 응용 프로그램을 확장하는 방법을 보여 줍니다. 이 예제에서는 [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) 작업 영역을 사용하여 IoT 중앙 원격 분석 스트림을 분석하고 상자 플롯과 같은 시각화를 [생성합니다.](https://wikipedia.org/wiki/Box_plot)

이 방법 가이드는 [내장 된 분석 도구로](./howto-create-custom-analytics.md)이미 할 수있는 것 이상으로 IoT Central을 확장하는 방법을 보여줍니다.

이 방법 가이드에서는 다음 방법을 배웁니다.

* *연속 데이터 내보내기를*사용하여 IoT Central 응용 프로그램에서 원격 분석을 스트리밍합니다.
* Azure Databricks 환경을 만들어 장치 원격 분석을 분석하고 플로팅합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 가이드의 수행 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

### <a name="iot-central-application"></a>IoT Central 애플리케이션

Azure IoT Central 응용 프로그램 관리자 웹 사이트에서 다음 설정을 사용하여 [IoT 중앙 응용 프로그램을](https://aka.ms/iotcentral) 만듭니다.

| 설정 | 값 |
| ------- | ----- |
| 요금제 | Standard |
| 애플리케이션 템플릿 | 매장 내 분석 - 상태 모니터링 |
| 애플리케이션 이름 | 기본값 수락 또는 사용자 고유의 이름 선택 |
| URL | 기본값 수락 또는 고유한 URL 접두사 선택 |
| 디렉터리 | Azure Active 디렉터리 테넌트 |
| Azure 구독 | Azure 구독 |
| 지역 | 가장 가까운 지역 |

이 문서의 예제 및 스크린샷은 **미국** 지역을 사용합니다. 가까운 위치를 선택하고 동일한 지역에 있는 모든 리소스를 만들어야 합니다.

이 응용 프로그램 템플릿에는 원격 분석을 보내는 두 개의 시뮬레이션된 온도 조절 장치가 포함되어 있습니다.

### <a name="resource-group"></a>Resource group

Azure [포털을](https://portal.azure.com/#create/Microsoft.ResourceGroup) 사용하여 **IoTCentralAnalysis라는** 리소스 그룹을 만들어 만든 다른 리소스를 포함합니다. IoT Central 응용 프로그램과 동일한 위치에 Azure 리소스를 만듭니다.

### <a name="event-hubs-namespace"></a>Event Hubs 네임스페이스

Azure 포털을 사용하여 다음 설정을 사용하여 [이벤트 허브 네임스페이스를 만듭니다.](https://portal.azure.com/#create/Microsoft.EventHub)

| 설정 | 값 |
| ------- | ----- |
| 이름    | 네임스페이스 이름 선택 |
| 가격 책정 계층 | Basic |
| Subscription | 사용자의 구독 |
| Resource group | IoTCentralAnalysis |
| 위치 | 미국 동부 |
| 처리량 단위 | 1 |

### <a name="azure-databricks-workspace"></a>Azure 데이터브릭 작업 영역

Azure 포털을 사용하여 다음 설정을 사용하여 [Azure Databricks 서비스를 만듭니다.](https://portal.azure.com/#create/Microsoft.Databricks)

| 설정 | 값 |
| ------- | ----- |
| 작업 영역 이름    | 작업 영역 이름 선택 |
| Subscription | 사용자의 구독 |
| Resource group | IoTCentralAnalysis |
| 위치 | 미국 동부 |
| 가격 책정 계층 | Standard |

필요한 리소스를 만들면 **IoTCentralAnalysis** 리소스 그룹이 다음과 같은 스크린샷처럼 보입니다.

![IoT 중앙 분석 리소스 그룹](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

이벤트 허브로 원격 분석을 지속적으로 내보내도록 IoT Central 응용 프로그램을 구성할 수 있습니다. 이 섹션에서는 IoT Central 응용 프로그램에서 원격 분석을 수신하는 이벤트 허브를 만듭니다. 이벤트 허브는 처리를 위해 스트림 애널리틱스 작업에 원격 분석을 제공합니다.

1. Azure 포털에서 이벤트 허브 네임스페이스로 이동하여 **+ 이벤트 허브**를 선택합니다.
1. 이벤트 허브의 이름을 **중앙내보내기에**이름을 지정하고 **만들기를**선택합니다.
1. 네임스페이스의 이벤트 허브 목록에서 중앙 **내보내기**를 선택합니다. 그런 다음 **공유 액세스 정책을**선택합니다.
1. **+ 추가**를 선택합니다. Listen 클레임으로 **수신(Listen)이라는** 정책을 만듭니다. **Listen**
1. 정책이 준비되면 목록에서 정책을 선택한 다음 연결 문자열 기본 키 값을 **복사합니다.**
1. 이 연결 문자열을 기록하면 나중에 Databricks 전자 필기장을 이벤트 허브에서 읽을 수 있도록 구성할 때 이 문자열을 사용합니다.

이벤트 허브 네임스페이스는 다음과 같은 스크린샷과 같습니다.

![Event Hubs 네임스페이스](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>IoT Central에서 내보내기 구성

Azure [IoT 중앙 응용 프로그램 관리자](https://aka.ms/iotcentral) 웹 사이트에서 Contoso 템플릿에서 만든 IoT 중앙 응용 프로그램으로 이동합니다. 이 섹션에서는 시뮬레이션된 장치에서 이벤트 허브로 원격 분석을 스트리밍하도록 응용 프로그램을 구성합니다. 내보내기를 구성하려면 다음을 수행하십시오.

1. **데이터 내보내기** 페이지로 **이동하여 + 새**를 선택한 다음 Azure 이벤트 **허브를 선택합니다.**
1. 다음 설정을 사용하여 내보내기를 구성한 다음 **저장을**선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 표시 이름 | 이벤트 허브로 내보내기 |
    | 사용 | 설정 |
    | Event Hubs 네임스페이스 | 이벤트 허브 네임스페이스 이름 |
    | 이벤트 허브 | 중앙 수출 |
    | 측정값 | 설정 |
    | 디바이스 | 꺼짐 |
    | 디바이스 템플릿 | 꺼짐 |

![데이터 내보내기 구성](media/howto-create-custom-analytics/cde-configuration.png)

계속하기 전에 내보내기 상태가 **실행 중일** 때까지 기다립니다.

## <a name="configure-databricks-workspace"></a>데이터 브릭 작업 영역 구성

Azure 포털에서 Azure Databricks 서비스로 이동하여 **작업 영역 시작**을 선택합니다. 브라우저에서 새 탭이 열리고 작업 영역에 로그인합니다.

### <a name="create-a-cluster"></a>클러스터 만들기

Azure **Databricks** 페이지에서 공통 작업 목록에서 새 **클러스터를**선택합니다.

다음 표의 정보를 사용하여 클러스터를 만듭니다.

| 설정 | 값 |
| ------- | ----- |
| 클러스터 이름 | 중앙 분석 |
| 클러스터 모드 | Standard |
| 데이터 브릭 런타임 버전 | 5.5 LTS (스칼라 2.11, 스파크 2.4.3) |
| Python 버전 | 3 |
| 자동 크기 조정 사용 | 예 |
| 비활성 후 종료 | 30 |
| 작업자 유형 | Standard_DS3_v2 |
| 작업자 | 1 |
| 드라이버 유형 | 작업자와 동일 |

클러스터를 만드는 데 몇 분 정도 걸릴 수 있으며 계속하기 전에 클러스터 만들기가 완료될 때까지 기다립니다.

### <a name="install-libraries"></a>라이브러리 설치

**클러스터** 페이지에서 클러스터 상태가 **실행 중일**때까지 기다립니다.

다음 단계에서는 샘플에 필요한 라이브러리를 클러스터로 가져오는 방법을 보여 줍니다.

1. **클러스터** 페이지에서 **중앙 분석** 대화형 클러스터의 상태가 **실행될**때까지 기다립니다.

1. 클러스터를 선택한 다음 **라이브러리** 탭을 선택합니다.

1. **라이브러리** 탭에서 **새 설치를 선택합니다.**

1. 라이브러리 **설치** 페이지에서 **Maven을** 라이브러리 소스로 선택합니다.

1. 좌표 텍스트 상자에 다음 값을 **입력합니다.**`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. 클러스터에 라이브러리를 설치하려면 **설치를** 선택합니다.

1. 라이브러리 상태가 **이제 설치되었습니다.**

    ![라이브러리 설치](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>데이터브릭 노트북 가져오기

다음 단계를 사용하여 파이썬 코드가 포함된 Databricks 노트북을 가져와 IoT 중앙 원격 분석을 분석하고 시각화합니다.

1. Databricks 환경에서 **작업 영역** 페이지로 이동합니다. 계정 이름 옆에 있는 드롭다운을 선택한 다음 **가져오기를**선택합니다.

1. URL에서 가져오고 다음 주소를 입력하도록 선택합니다.[https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. 전자 필기장을 가져오려면 **가져오기**를 선택합니다.

1. 가져온 전자 필기장을 보려면 **작업 영역을** 선택합니다.

    ![가져온 노트북](media/howto-create-custom-analytics/import-notebook.png)

1. 이전에 저장한 이벤트 허브 연결 문자열을 추가하려면 첫 번째 Python 셀에서 코드를 편집합니다.

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>실행 분석

분석을 실행하려면 랩치를 클러스터에 연결해야 합니다.

1. **분리된** 선택을 선택한 다음 **중앙 분석** 클러스터를 선택합니다.
1. 클러스터가 실행되고 있지 않으면 클러스터를 시작합니다.
1. 전자 필기장을 시작하려면 실행 버튼을 선택합니다.

마지막 셀에 오류가 표시될 수 있습니다. 그렇다면 이전 셀이 실행 중인지 확인하고 일부 데이터가 저장소에 기록될 때까지 잠시 기다린 다음 마지막 셀을 다시 실행합니다.

### <a name="view-smoothed-data"></a>스무딩 데이터 보기

노트북에서 셀 14로 스크롤하여 장치 유형별로 롤링 평균 습도의 플롯을 확인합니다. 이 플롯은 스트리밍 원격 분석이 도착함에 따라 지속적으로 업데이트됩니다.

![스무드 원격 분석 플롯](media/howto-create-custom-analytics/telemetry-plot.png)

전자 필기장에서 차트 크기를 조정할 수 있습니다.

### <a name="view-box-plots"></a>상자 플롯 보기

전자 필기장에서 셀 20으로 스크롤하여 [상자 플롯을 확인합니다.](https://en.wikipedia.org/wiki/Box_plot) 상자 플롯은 정적 데이터를 기반으로 하므로 이를 업데이트하려면 셀을 다시 실행해야 합니다.

![상자 플롯](media/howto-create-custom-analytics/box-plots.png)

전자 필기장에서 플롯의 크기를 조정할 수 있습니다.

## <a name="tidy-up"></a>정리

이 방법 이후에 정리하고 불필요한 비용을 방지하려면 Azure 포털에서 **IoTCentralAnalysis** 리소스 그룹을 삭제합니다.

응용 프로그램 내의 **관리** 페이지에서 IoT 중앙 응용 프로그램을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 다음 작업 방법을 배웁니다.

* *연속 데이터 내보내기를*사용하여 IoT Central 응용 프로그램에서 원격 분석을 스트리밍합니다.
* Azure Databricks 환경을 만들어 원격 분석 데이터를 분석하고 플로팅합니다.

사용자 지정 분석을 만드는 방법을 배웠으니 다음 단계는 응용 [프로그램을 관리하는](howto-administer.md)방법을 알아보는 것입니다.
