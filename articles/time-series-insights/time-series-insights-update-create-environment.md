---
title: '자습서: Azure Time Series Insights 미리 보기 환경 설정 | Microsoft Docs'
description: Azure Time Series Insights 미리 보기에서 환경을 설정하는 방법에 대해 알아봅니다.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.custom: seodec18
ms.openlocfilehash: 77b7b90b63ffebc14498183fc179b9c8ae76a722
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237857"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>자습서: Azure Time Series Insights 미리 보기 환경 설정

이 자습서에서는 Azure Time Series Insights 미리 보기 PAYG(종량제) 환경을 만드는 프로세스를 단계별로 설명합니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

* Azure Time Series Insights 미리 보기 환경 만들기
* Azure Event Hubs에서 이벤트 허브에 Azure Time Series Insights 미리 보기 환경 연결
* Azure Time Series Insights 미리 보기 환경으로 데이터를 스트리밍하도록 솔루션 가속기 샘플 실행
* 데이터에 대한 기본 분석을 수행합니다.
* 시계열 모델 형식 및 계층 구조 정의 및 인스턴스와 연결

## <a name="create-a-device-simulation"></a>디바이스 시뮬레이션 만들기

이 섹션에서는 Azure IoT Hub의 인스턴스로 데이터를 보내는 세 개의 시뮬레이션된 디바이스를 만듭니다.

1. [Azure IoT 솔루션 가속기 페이지](https://www.azureiotsolutions.com/Accelerators)로 이동합니다. 페이지에는 몇 가지 미리 작성된 예제가 표시됩니다. Azure 계정을 사용하여 로그인합니다. 그런 다음, **디바이스 시뮬레이션**을 선택합니다.

   [![Azure IoT 솔루션 가속기 페이지](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   **지금 사용해보기**를 선택합니다.

1. **디바이스 시뮬레이션 만들기** 솔루션 페이지에서 다음 매개 변수를 설정합니다.

    | 매개 변수 | 조치 |
    | --- | --- |
    | **솔루션 이름** | 새 리소스 그룹에 대한 고유 값을 입력합니다. 나열된 Azure 리소스가 생성되어 리소스 그룹에 할당됩니다. |
    | **구독** | Time Series Insights 환경을 만드는 데 사용한 구독을 선택합니다. |
    | **지역** | Time Series Insights 환경을 만드는 데 사용한 지역을 선택합니다. |
    | **선택적 Azure 리소스 배포** | **IoT Hub** 확인란을 선택한 상태로 둡니다. 시뮬레이션된 디바이스는 IoT Hub를 사용하여 데이터를 연결하고 스트림합니다. |
 
    **솔루션 만들기**를 선택합니다. 솔루션을 배포되도록 10~15분 동안 기다립니다.

    [![디바이스 시뮬레이션 솔루션 만들기 페이지](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. 솔루션 가속기 대시보드에서 **시작**을 선택합니다.

    [![디바이스 시뮬레이션 솔루션 시작](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. **Microsoft Azure IoT 디바이스 시뮬레이션** 페이지로 리디렉션됩니다. 페이지의 오른쪽 위 모서리에서 **새 시뮬레이션**을 선택합니다.

    [![Azure IoT 시뮬레이션 페이지](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. **시뮬레이션 설정** 창에서 다음 매개 변수를 설정합니다.

    | 매개 변수 | 조치 |
    | --- | --- |
    | **Name** | 시뮬레이터에 사용할 고유한 이름을 입력합니다. |
    | **설명** | 정의를 입력합니다. |
    | **시뮬레이션 기간** | **무기한 실행**으로 설정합니다. |
    | **디바이스 모델** | **이름**: **냉각기**를 입력합니다. <br />**수량**: **3**을 입력합니다. |
    | **대상 IoT Hub** | **사전 프로비전된 IoT Hub 사용**으로 설정합니다. |

    [![매개 변수 설정](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    **시뮬레이션 시작**을 선택합니다.

    디바이스 시뮬레이션 대시보드에서 **활성 디바이스** 및 **초당 메시지 수**에 대해 표시된 정보를 적어둡니다.

    [![Azure IoT 시뮬레이션 대시보드](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="list-device-simulation-properties"></a>디바이스 시뮬레이션 속성 나열

Azure Time Series Insights 환경을 만들기 전에 IoT Hub, 구독 및 리소스 그룹의 이름이 필요합니다.

1. 솔루션 가속기 대시보드로 이동합니다. 동일한 Azure 구독 계정을 사용하여 로그인합니다. 이전 섹션에서 만든 디바이스 시뮬레이션을 찾습니다.

1. 해당 디바이스 시뮬레이터를 선택한 다음, **시작**을 선택합니다. 오른쪽의 디바이스 시뮬레이터 솔루션 가속기 창에서 **Azure 관리 포털** 옵션을 선택합니다.

    [![시뮬레이터 목록](media/v2-update-provision/device-six-listings.png)](media/v2-update-provision/device-six-listings.png#lightbox)

1. IoT Hub, 구독 및 리소스 그룹의 이름을 적어둡니다.

    [![Azure Portal 디바이스 시뮬레이터 대시보드 세부 정보](media/v2-update-provision/device-eight-portal.png)](media/v2-update-provision/device-eight-portal.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Time Series Insights 미리 보기 PAYG 환경 만들기

이 섹션에서는 [Azure Portal](https://portal.azure.com/)을 사용하여 Azure Time Series Insights 미리 보기 환경을 만드는 방법에 대해 설명합니다.

1. 구독 계정을 사용하여 Azure Portal에 로그인합니다.

1. **리소스 만들기** > **사물 인터넷** > **Time Series Insights**를 선택합니다.

   [![사물 인터넷을 선택한 다음, Time Series Insights를 선택합니다.](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. **Time Series Insights 환경 만들기** 창의 **기본 사항** 탭에서 다음 매개 변수를 설정합니다.

    | 매개 변수 | 조치 |
    | --- | ---|
    | **환경 이름** | Azure Time Series Insights 미리 보기 환경에 대한 고유 이름을 입력합니다. |
    | **구독** | Azure Time Series Insights 미리 보기 환경을 만들려는 구독을 입력합니다. 디바이스 시뮬레이터에서 만든 나머지 IoT 리소스와 동일한 구독을 사용하는 것이 좋습니다. |
    | **리소스 그룹** | Azure Time Series Insights 미리 보기 환경 리소스에 대해 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스에 대한 컨테이너입니다. 디바이스 시뮬레이터에서 만든 다른 IoT 리소스와 동일한 리소스 그룹을 사용하는 것이 좋습니다. |
    | **위치**: | Azure Time Series Insights 미리 보기 환경에 대한 데이터 센터 지역을 선택합니다. 대역폭 비용과 대기 시간이 추가되지 않도록 Azure Time Series Insights 미리 보기 환경을 다른 IoT 리소스와 동일한 지역에 만드는 것이 가장 좋습니다. |
    | **계층** |  **PAYG**(*종량제*)를 선택합니다. Azure Time Series Insights 미리 보기 제품에 대한 SKU입니다. |
    | **속성 ID** | 시계열 인스턴스를 고유하게 식별하는 값을 입력합니다. **속성 ID** 상자에 입력한 값은 변경할 수 없으며, 나중에 변경할 수도 없습니다. 이 자습서에서는 **iothub-connection-device-id**를 입력합니다. 시계열 ID에 대한 자세한 내용은 [시계열 ID 선택 모범 사례](./time-series-insights-update-how-to-id.md)를 참조하세요. |
    | **스토리지 계정 이름** | 새로 만들 스토리지 계정에 대해 글로벌로 고유한 이름을 입력합니다. |
   
   완료되면 **다음: 이벤트 원본**을 클릭합니다.

   [![Time Series Insights 환경을 만들기 위한 창](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. **이벤트 원본** 탭에서 다음 매개 변수를 설정합니다.

   | 매개 변수 | 조치 |
   | --- | --- |
   | **이벤트 원본을 만드시겠습니까?** | **예**를 선택합니다.|
   | **Name** | 이벤트 원본 이름에 대한 고유 값을 입력합니다. |
   | **원본 유형** | **IoT Hub**를 선택합니다. |
   | **허브 선택** | **기존 항목 선택**을 선택합니다. |
   | **구독** | 디바이스 시뮬레이터에 사용한 구독을 선택합니다. |
   | **IoT Hub 이름** | 디바이스 시뮬레이터에 대해 만든 IoT Hub 이름을 선택합니다. |
   | **Iot Hub 액세스 정책** | **iothubowner**를 선택합니다. |
   | **IoT Hub 소비자 그룹** | **새로 만들기**를 선택하고, 고유한 이름을 입력한 다음, **추가**를 선택합니다. 소비자 그룹은 Azure Time Series Insights 미리 보기에서 고유한 값이어야 합니다. |
   | **타임스탬프 속성** | 이 값을 사용하여 들어오는 원격 분석 데이터의 **Timestamp**(타임스탬프) 속성을 식별합니다. 이 자습서에서는 이 상자를 비워 둡니다. 이 시뮬레이터는 Time Series Insights가 기본값을 지정한 IoT Hub에서 들어오는 타임스탬프를 사용합니다. |

   **검토 + 만들기**를 선택합니다.

   [![이벤트 원본 구성](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. **검토 + 만들기** 탭에서 선택 항목을 검토한 다음, **만들기**를 선택합니다.

    [![만들기 단추가 있는 검토 + 만들기 페이지](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    아래와 같이 배포의 상태를 확인할 수 있습니다.

    [![배포가 완료되었다는 알림](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. 테넌트를 소유한 경우 Azure Time Series Insights 미리 보기 환경에 대한 액세스 권한이 있어야 합니다. 액세스 권한이 있는지 확인하려면:

   1. 리소스 그룹을 검색한 다음, Azure Time Series Insights 미리 보기 환경을 선택합니다.

      [![선택된 환경](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Azure Time Series Insights 미리 보기 페이지에서 **데이터 액세스 정책**을 선택합니다.

      [![데이터 액세스 정책](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. 자격 증명이 나열되어 있는지 확인합니다.

      [![나열된 자격 증명](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   자격 증명이 나열되어 있지 않으면 환경에 대한 액세스 권한을 자신에게 부여해야 합니다. 사용 권한 설정에 대해 자세히 알아보려면 [데이터 액세스 권한 부여](./time-series-insights-data-access.md)를 읽어보세요.

## <a name="analyze-data-in-your-environment"></a>환경에서 데이터 분석

이 섹션에서는 [Azure Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)를 사용하여 시계열 데이터에 대해 기본 분석을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 리소스 페이지에서 URL을 선택하여 Azure Time Series Insights 미리 보기 탐색기로 이동합니다.

    [![Time Series Insights 미리 보기 탐색기 URL](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. 탐색기에서 **Time Series 인스턴스** 노드를 선택하여 환경의 모든 Azure Time Series Insights 미리 보기 인스턴스를 확인합니다.

    [![부모가 없는 인스턴스 목록](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. 첫 번째 시계열 인스턴스를 선택합니다. 그런 다음, **압력 표시**를 선택합니다.

    [![평균 압력을 표시하는 메뉴 명령으로 선택한 시계열 인스턴스](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    시계열 차트가 표시됩니다. **간격**을 **15초**로 변경합니다.

    [![시계열 차트](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. 다른 두 개의 시계열 인스턴스를 사용하여 3단계를 반복합니다. 아래 차트와 같이 모든 시계열 인스턴스를 볼 수 있습니다.

    [![모든 시계열에 대한 차트](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. **시간 프레임** 옵션 상자에서 지난 1시간 동안의 시계열 추세를 확인할 수 있도록 시간 범위를 수정합니다.

    [![시간 범위를 1시간으로 설정](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>모델 정의 및 적용

이 섹션에서는 데이터를 구성하는 모델을 적용합니다. 모델을 완료하려면 형식, 계층 구조 및 인스턴스를 정의합니다. 데이터 모델링에 대한 자세한 내용은 [시계열 모델](./time-series-insights-update-tsm.md)을 참조하세요.

1. 탐색기에서 **모델** 탭을 선택합니다.

   [![탐색기의 모델 탭](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. **추가**를 선택하여 형식을 추가합니다.

   [![형식에 대한 추가 단추](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. 다음으로, 형식에 대한 세 개의 변수인 *압력*, *온도* 및 *습도*를 정의합니다. **형식 추가** 창에서 다음 매개 변수를 설정합니다.

    | 매개 변수 | 조치 |
    | --- | ---|
    | **Name** | **냉각기**를 입력합니다. |
    | **설명** | **이는 냉각기의 유형 정의입니다**를 입력합니다. |

   * *압력*을 정의하려면 **변수** 아래에서 다음 매개 변수를 설정합니다.

     | 매개 변수 | 조치 |
     | --- | ---|
     | **Name** | **평균 압력**을 입력합니다. |
     | **값** | **압력(Double)** 을 선택합니다. Azure Time Series Insights 미리 보기에서 이벤트를 받기 시작한 후 **값**이 자동으로 채워지는 데 몇 분이 걸릴 수 있습니다. |
     | **집계 작업** | **평균**을 선택합니다. |

      [![압력을 정의하기 위한 선택 항목](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      다음 변수를 추가하려면 **변수 추가**를 선택합니다.

   * *온도*를 정의합니다.

     | 매개 변수 | 조치 |
     | --- | ---|
     | **Name** | **평균 온도**를 입력합니다. |
     | **값** | **온도(Double)** 를 선택합니다. Azure Time Series Insights 미리 보기에서 이벤트를 받기 시작한 후 **값**이 자동으로 채워지는 데 몇 분이 걸릴 수 있습니다. |
     | **집계 작업** | **평균**을 선택합니다.|

      [![온도를 정의하기 위한 선택 항목](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      다음 변수를 추가하려면 **변수 추가**를 선택합니다.

   * *습도*를 정의합니다.

      | | |
      | --- | ---|
      | **Name** | **최대 습도**를 입력합니다. |
      | **값** | **습도(Double)** 를 선택합니다. Azure Time Series Insights 미리 보기에서 이벤트를 받기 시작한 후 **값**이 자동으로 채워지는 데 몇 분이 걸릴 수 있습니다. |
      | **집계 작업** | **최대**를 선택합니다.|

      [![온도를 정의하기 위한 선택 항목](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    **만들기**를 선택합니다.

    추가한 형식이 표시됩니다.

    [![추가된 형식에 대한 정보](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. 다음 단계는 계층 구조를 추가하는 것입니다. **계층 구조** 아래에서 **추가**를 선택합니다.

    [![추가 단추가 포함된 계층 구조 탭](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. **계층 구조 편집** 창에서 다음 매개 변수를 설정합니다.

   | 매개 변수 | 조치 |
   | --- | ---|
   | **Name** | **위치 계층 구조**를 입력합니다. |
   | **수준 1** | **국가**를 입력합니다. |
   | **수준 2** | **시/군/구**를 입력합니다. |
   | **수준 3** | **건물**을 입력합니다. |

   **저장**을 선택합니다.

    [![만들기 단추가 포함된 계층 구조 필드](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   만든 계층 구조를 확인할 수 있습니다.

    [![계층 구조에 대한 정보](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. **인스턴스**를 선택합니다. 첫 번째 인스턴스를 선택한 다음, **편집**을 선택합니다.

    [![인스턴스에 대한 편집 단추 선택](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. **인스턴스 편집** 창에서 다음 매개 변수를 설정합니다.

    | 매개 변수 | 조치 |
    | --- | --- |
    | **형식** | **냉각기**를 선택합니다. |
    | **설명** | **냉각기-01.1에 대한 인스턴스**를 입력합니다. |
    | **계층 구조** | **위치 계층 구조**를 선택합니다. |
    | **국가** | **미국**을 입력합니다. |
    | **도시** | **시애틀**을 입력합니다. |
    | **빌딩** | **Space Needle**을 입력합니다. |

    [![저장 단추가 있는 인스턴스 필드](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   **저장**을 선택합니다.

1. 다른 센서에 대해 이전 단계를 반복합니다. 다음 값을 업데이트합니다.

   * 냉각기 01.2의 경우:

     | 매개 변수 | 조치 |
     | --- | --- |
     | **형식** | **냉각기**를 선택합니다. |
     | **설명** | **냉각기-01.2에 대한 인스턴스**를 입력합니다. |
     | **계층 구조** | **위치 계층 구조**를 선택합니다. |
     | **국가** | **미국**을 입력합니다. |
     | **도시** | **시애틀**을 입력합니다. |
     | **빌딩** | **태평양 과학 센터**를 입력합니다. |

   * 냉각기 01.3의 경우:

     | 매개 변수 | 조치 |
     | --- | --- |
     | **형식** | **냉각기**를 선택합니다. |
     | **설명** | **냉각기-01.3에 대한 인스턴스**를 입력합니다. |
     | **계층 구조** | **위치 계층 구조**를 선택합니다. |
     | **국가** | **미국**을 입력합니다. |
     | **도시** | **뉴욕**을 입력합니다. |
     | **빌딩** | **엠파이어 스테이트 빌딩**을 입력합니다. |

1. **분석** 탭을 선택한 다음, 페이지를 새로 고칩니다. **위치 계층 구조** 아래에서 모든 계층 수준을 펼쳐 시계열 인스턴스를 표시합니다.

   [![분석 탭](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. 지난 1시간 동안의 시계열 인스턴스를 살펴보려면 **빠른 시간**을 **지난 1시간**으로 변경합니다.

    [![지난 1시간이 선택된 빠른 시간 상자](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. **태평양 과학 센터** 아래에서 시계열 인스턴스를 선택한 다음, **최대 습도 표시**를 선택합니다.

    [![선택한 시계열 인스턴스 및 최대 습도 표시 메뉴 선택 항목](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. **1분 간격**으로 **최대 습도**에 대한 시계열이 열립니다. 범위를 필터링하려면 지역을 선택합니다. 시간 프레임에서 이벤트를 분석하려면 마우스 오른쪽 단추로 차트를 클릭한 다음, **확대/축소**를 선택합니다.

   [![바로 가기 메뉴의 확대/축소 명령을 사용하여 선택한 범위](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. 이벤트 세부 정보를 보려면 지역을 선택한 다음, 마우스 오른쪽 단추로 차트를 클릭합니다.

   [![이벤트의 자세한 목록](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.  

> [!div class="checklist"]
> * 디바이스 시뮬레이션 가속기 만들기 및 사용
> * Azure Time Series Insights 미리 보기 PAYG 환경 만들기
> * 이벤트 허브에 Azure Time Series Insights 미리 보기 환경 연결
> * Azure Time Series Insights 미리 보기 환경으로 데이터를 스트리밍하도록 솔루션 가속기 샘플 실행
> * 데이터에 대한 기본 분석 수행
> * 시계열 모델 형식 및 계층 구조 정의 및 인스턴스와 연결

이제 고유한 Azure Time Series Insights 미리 보기 환경을 만드는 방법을 알아보았으므로 Azure Time Series Insights의 주요 개념에 대해 알아봅니다.

Azure Time Series Insights 스토리지 구성에 대해 다음을 읽어보세요.

> [!div class="nextstepaction"]
> [Azure Time Series Insights 미리 보기 스토리지 및 수신](./time-series-insights-update-storage-ingress.md)

시계열 모델에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Time Series Insights 미리 보기 데이터 모델링](./time-series-insights-update-tsm.md)