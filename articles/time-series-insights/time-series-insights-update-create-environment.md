---
title: Azure Time Series Insights 미리 보기 설정- Azure Time Series Insights 미리 보기 환경 설정 자습서 | Microsoft Docs
description: Azure Time Series Insights 미리 보기에서 환경을 설정하는 방법에 대해 알아봅니다.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 9ad957d6378b1279f1ca51939eb4802b0ce7d78f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322621"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>자습서: Azure Time Series Insights 미리 보기 환경 설정

이 자습서에서는 Azure Time Series Insights 종량제(PAYG) 미리 보기 환경을 만드는 프로세스를 단계별로 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

* Azure Time Series Insights 미리 보기 환경 만들기
* Azure Event Hubs에서 이벤트 허브에 Azure Time Series Insights 미리 보기 환경 연결
* Azure Time Series Insights 미리 보기 환경으로 데이터를 스트리밍하도록 풍력 발전소 시뮬레이션 실행
* 데이터에 대한 기본 분석을 수행합니다.
* 시계열 모델 형식 및 계층 구조 정의 및 인스턴스와 연결

# <a name="create-a-device-simulation"></a>디바이스 시뮬레이션 만들기

이 섹션에서는 IoT Hub로 데이터를 전송할 3개의 시뮬레이션된 디바이스를 만듭니다.

1. [Azure IoT 솔루션 가속기 홈페이지](https://www.azureiotsolutions.com/Accelerators)로 이동합니다. Azure IoT 솔루션 가속기 홈페이지에는 몇 가지 미리 작성된 예제가 표시됩니다. Azure 계정을 사용하여 로그인합니다. 그런 다음, **디바이스 시뮬레이션**을 선택합니다.

   ![Azure IoT 솔루션 가속기 홈페이지][1]

   마지막으로, **지금 사용해보기**를 클릭합니다.

1. **디바이스 시뮬레이션 만들기** 솔루션 페이지에서 필수 매개 변수를 입력합니다.

   | 매개 변수 | 설명 |
   | --- | --- |
   | 솔루션 이름 |    새 리소스 그룹의 생성에 사용되는 고유 값입니다. 나열된 Azure 리소스가 | 생성되고 리소스 그룹에 할당됩니다. |
   | 구독 | TSI 환경을 만드는 데 사용된 동일한 구독을 지정합니다. |
   | 지역 |   TSI를 만드는 데 사용된 동일한 지역을 지정합니다. |
   | 선택적 Azure 리소스 배포    | 시뮬레이션된 디바이스가 데이터를 연결/스트리밍하는 데 사용하므로 IoT Hub를 선택한 상태로 둡니다. |

   필수 매개 변수를 입력한 후에 **솔루션 만들기**를 클릭합니다. 솔루션을 배포하려면 약 10~15분 동안 기다립니다.

   ![디바이스 시뮬레이션 솔루션 만들기][2]

1. **솔루션 가속기 대시보드**에서 **시작** 단추를 클릭합니다.

   ![디바이스 시뮬레이션 솔루션 시작][3]

1. **Microsoft Azure IoT 디바이스 시뮬레이션** 페이지에 리디렉션됩니다. 화면의 오른쪽 위에 있는 **+ 새 시뮬레이션**을 클릭합니다.

   ![Azure IoT 시뮬레이션 페이지][4]

1.  필수 매개 변수를 다음과 같이 입력합니다.

    ![입력할 매개 변수][5]

    |||
    | --- | --- |
    | **Name** | 시뮬레이터에 대한 고유한 이름을 입력합니다. |
    | **설명** | 정의를 입력합니다. |
    | **시뮬레이션 기간** | `Run indefinitely` |
    | **디바이스 모델** | **이름**: `Chiller`**수량**을 입력합니다. `3`을 입력합니다. |
    | **대상 IoT Hub** | `Use pre-provisioned IoT Hub` |

    필수 매개 변수를 입력한 후에 **시뮬레이션 시작**을 클릭합니다.

1. 디바이스 시뮬레이션 대시보드에서 **활성 디바이스** 및 **초당 메시지 수**가 표시됩니다.

    ![Azure IoT 시뮬레이션 대시보드][6]

## <a name="list-device-simulation-properties"></a>디바이스 시뮬레이션 속성 나열

Azure Time Series Insights 환경을 만들기 전에 IoT Hub, 구독 및 리소스 그룹 이름의 이름이 필요합니다.

1. **솔루션 가속기 대시보드**로 이동하고 동일한 Azure 구독 계정을 사용하여 로그인합니다. 이전 단계에서 만든 디바이스 시뮬레이션을 찾습니다.

1. 디바이스 시뮬레이터에 클릭하고 **시작**을 클릭합니다. 오른쪽에 표시된 **Azure 관리 포털** 링크를 클릭합니다.

    ![시뮬레이터 목록][7]

1. IoT Hub, 구독 및 리소스 그룹 이름을 기록해둡니다.

    ![Azure portal][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Time Series Insights 미리 보기 PAYG 환경 만들기

이 섹션에서는 [Azure Portal](https://portal.azure.com/)을 사용하여 Azure Time Series Insights 미리 보기 환경을 만드는 방법에 대해 설명합니다.

1. 구독 계정을 사용하여 Azure Portal에 로그인합니다.

1. **리소스 만들기**를 선택합니다.

1. **사물 인터넷** 범주를 선택한 다음, **Time Series Insights**를 선택합니다.

   ![리소스 만들기를 선택한 다음, 사물 인터넷 및 Time Series Insights를 차례로 선택합니다][9]

1. 페이지의 필드를 다음과 같이 입력합니다.

   | | |
   | --- | ---|
   | **환경 이름** | Azure Time Series Insights 미리 보기 환경에 고유한 이름을 선택합니다. |
   | **구독** | Azure Time Series Insights 미리 보기 환경을 만들려는 구독을 입력합니다. 디바이스 시뮬레이터에서 만든 IoT 리소스의 나머지와 동일한 구독을 사용하는 것이 좋습니다. |
   | **리소스 그룹** | 리소스 그룹은 Azure 리소스에 대한 컨테이너입니다. Azure Time Series Insights 미리 보기 환경 리소스에 대해 기존 리소스 그룹을 선택하거나 새 항목을 만듭니다. 디바이스 시뮬레이터에서 만든 IoT 리소스의 나머지와 동일한 리소스 그룹을 사용하는 것이 좋습니다. |
   | **위치**: | Azure Time Series Insights 미리 보기 환경에 대한 데이터 센터 지역을 선택합니다. 추가된 대역폭 비용과 대기 시간을 피하려면 다른 IoT 리소스와 동일한 지역에 Azure Time Series Insights 미리 보기 환경을 유지하는 것이 가장 좋습니다. |
   | **계층** |  종량제를 의미하는 `PAYG`를 선택합니다. Azure Time Series Insights 미리 보기 제품에 대한 SKU입니다. |
   | **속성 ID** | 시계열을 고유하게 식별합니다. 이 필드는 변경할 수 없고 나중에 변경할 수 없습니다. 이 자습서의 경우 필드를 `iothub-connection-device-id`로 설정합니다. 시간 시계열 ID에 대한 자세한 내용은 [시계열 ID를 선택하는 방법](./time-series-insights-update-how-to-id.md)을 참고하세요. |
   | **Storage 계정 이름** | 만들려는 새 스토리지 계정에 대한 글로벌 고유 이름을 입력합니다. |

   위의 필드를 입력한 후에 **다음: 이벤트 원본**을 클릭합니다.

   ![다음을 클릭합니다. 이벤트 원본][10]

1. 페이지에서 필드를 다음과 같이 입력합니다.

   | | |
   | --- | --- |
   | **이벤트 원본 만들기** | `Yes`를 입력합니다.|
   | **Name** | 이벤트 원본 이름을 지정하는 데 사용되는 고유한 값이 필요합니다.|
   | **원본 유형** | `IoT Hub`를 입력합니다. |
   | **허브 선택** | `Select Existing`을 입력합니다. |
   | **구독** | 디바이스 시뮬레이터에 사용된 구독을 입력합니다. |
   | **IoT Hub 이름** | 디바이스 시뮬레이터에 대해 만든 IoT 허브 이름을 입력합니다. |
   | **Iot Hub 액세스 정책** | `iothubowner`를 입력합니다. |
   | **IoT Hub 소비자 그룹** | Azure Time Series Insights 미리 보기에 대한 고유한 소비자 그룹이 필요합니다. |
   | **Timestamp** | 이 필드를 사용하여 들어오는 원격 분석 데이터에서 타임스탬프 속성을 식별합니다. 이 자습서에서는 필드를 입력하지 마십시오. 이 시뮬레이터는 Time Series Insights가 기본값을 지정한 IoT Hub에서 들어오는 타임스탬프를 사용합니다.|

   고유한 소비자 그룹을 만들려면:

   1. **IoT Hub 소비자 그룹** 필드 옆에 있는 **새로 만들기**를 클릭합니다.

      ![다음을 클릭합니다. 이벤트 원본][11]

   1. 소비자 그룹에 고유한 이름을 지정하고 **추가**를 클릭합니다.

      ![추가를 클릭합니다.][12]

   위의 필드를 채운 후에 **검토 + 만들기**를 클릭합니다.

      ![검토 및 만들기][13]

1. 검토 페이지에 있는 모든 필드를 검토하고 **만들기**를 클릭합니다.

   ![생성][14]

1. 배포의 상태를 볼 수 있습니다.

   ![배포 완료][15]

1. 테넌트를 소유한 경우 시계열 환경에 대한 액세스 권한을 받아야 합니다. 액세스 권한이 있는지 확인하려면:

   * 새로 만든 Azure Time Series Insights 미리 보기 환경으로 이동합니다. 리소스 그룹을 검색하여 이를 수행할 수 있습니다. 그런 다음, 시계열 환경을 클릭합니다.

      ![배포 완료][16]

   * Azure Time Series Insights 미리 보기 페이지에서 **데이터 액세스 정책**으로 이동합니다.

     ![데이터 액세스 정책][17]

   * 자격 증명이 나열되어 있는지 확인합니다.

     ![자격 증명 확인][18]

   자격 증명이 나열되지 않는 경우 자신에게 환경에 액세스할 수 있는 사용 권한을 주어야 합니다. 사용 권한을 설정하는 방법에 대한 자세한 내용을 알아보려면 [데이터 액세스 권한 부여](./time-series-insights-data-access.md)를 참고하세요.

## <a name="analyze-data-in-your-environment"></a>환경에서 데이터 분석

이 섹션에서는 [Azure Time Series Insights 미리 보기 탐색기](./time-series-insights-update-explorer.md)를 사용하여 시계열 데이터에 대해 기본 분석을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 리소스 페이지에서 URL을 클릭하여 Azure Time Series Insights 미리 보기 탐색기로 이동합니다.

   ![Time Series Insights 탐색기 URL][19]

1. 탐색기에서 **부모가 아닌 인스턴스** 노드를 선택하여 환경에서 모든 Azure Time Series Insights 미리 보기를 확인합니다.

   ![부모가 없는 인스턴스 목록][20]

1. 표시된 시계열에서 첫 번째 인스턴스를 클릭합니다. 그런 다음, **평균 압력 표시**를 클릭합니다.

   ![평균 압력 표시][21]

1. 시계열 차트는 오른쪽에 표시됩니다.

   ![시계열 차트][22]

1. 다른 두 개의 시계열을 사용하여 **3단계**를 반복합니다. 그러면 아래와 같이 모든 시계열을 볼 수 있습니다.

   ![모든 시계열 차트][23]

1. **시간 범위**를 수정하여 지난 1시간 동안 시계열 추세를 확인합니다. 아래와 같이 **시작 시간** 옵션 상자를 선택합니다.

   ![시작 시간 옵션 선택][24]

1. **시작 시간** 옵션 상자 내에서 시간을 변경하여 지난 1시간 동안 이벤트를 표시합니다.

   ![시작 시간 옵션 선택][25]

1. 그런 다음, 세 가지 모든 디바이스에서 지난 1시간 동안 압력을 비교할 수 있습니다.

   ![시작 시간 옵션 선택][26]

## <a name="define-and-apply-a-model"></a>모델 정의 및 적용

이 섹션에서는 데이터를 구성하는 모델을 적용합니다. 모델을 완료하려면 형식, 계층 구조 및 인스턴스를 정의합니다. 데이터 모델링에 대한 자세한 내용은 [시계열 모델](./time-series-insights-update-tsm.md)로 이동합니다.

1. 탐색기에서 **모델** 탭을 선택합니다.

   ![모델 탭 선택][27]

1. 다음으로, **+ 추가**를 클릭하여 형식을 추가합니다. 오른쪽에서 형식 편집기가 열립니다.

   ![추가를 클릭합니다.][28]

1. 다음으로, 세 가지 변수를 정의합니다. 형식의 압력, 온도 및 습도 다음 필드를 입력합니다.

   | | |
   | --- | ---|
   | **Name** | `Chiller`를 입력합니다. |
   | **설명** | `This is a type definition of Chiller`를 입력합니다. |

   * 이제 세 개의 변수를 포함한 압력을 정의합니다.

      | | |
      | --- | ---|
      | **Name** | `Avg Pressure`를 입력합니다. |
      | **값** | **압력(Double)** 을 선택합니다. Azure Time Series Insights가 이벤트를 수신하기 시작하면 이 필드를 채우는 데 몇 분 정도가 걸릴 수 있습니다 |
      | **집계 작업** | `AVG`을(를) 선택합니다. |

      ![변수 추가][29]

      **+ 변수**를 클릭하여 다음 변수를 추가합니다.

   * 이제 온도를 정의합니다.

      | | |
      | --- | ---|
      | **Name** | `Avg Temperature`를 입력합니다. |
      | **값** | **온도(Double)** 를 선택합니다. Azure Time Series Insights가 이벤트를 수신하기 시작하면 이 필드를 채우는 데 몇 분 정도가 걸릴 수 있습니다 |
      | **집계 작업** | `AVG`을(를) 선택합니다.|

      ![온도 정의][30]

   * 이제 습도를 정의합니다.

      | | |
      | --- | ---|
      | **Name** | `Max Humidity`를 입력합니다. |
      | **값** | **습도(Double)** 를 선택합니다. Azure Time Series Insights가 이벤트를 수신하기 시작하면 이 필드를 채우는 데 몇 분 정도가 걸릴 수 있습니다 |
      | **집계 작업** | `MAX`을(를) 선택합니다.|

      ![온도 정의][31]

   변수를 정의한 후에 **만들기**를 클릭합니다.

1. 추가된 형식을 확인할 수 있습니다.

   ![추가된 형식 확인][32]

1. 다음 단계는 계층 구조를 추가하는 것입니다. **계층 구조** 섹션에서 **+ 추가**를 선택하여 새 계층 구조를 만듭니다.

   ![계층 구조 추가][33]

1. 계층 구조를 정의합니다. 다음과 같이 필드를 입력합니다.

   | | |
   | --- | ---|
   | **Name** | `Location Hierarchy`를 입력합니다. |
   | **수준 1** | `Country`를 입력합니다. |
   | **수준 2** | `City`를 입력합니다. |
   | **수준 3** | `Building`을 입력합니다. |

   위의 필드를 입력한 후에 **만들기**를 클릭합니다.

   ![계층 구조 정의][34]

1. 만든 계층 구조를 확인할 수 있습니다.

   ![계층 구조 확인][35]

1. 계층 구조를 정의한 후에 왼쪽에 있는 **인스턴스**를 클릭합니다. 인스턴스가 표시되면 첫 번째 인스턴스를 클릭하고 **편집**을 선택합니다.

   ![인스턴스 편집][36]

1. 오른쪽에서 텍스트 편집기가 표시됩니다. 다음 필드를 추가합니다.

   | | |
   | --- | --- |
   | **형식** | `Chiller`을(를) 선택합니다. |
   | **설명** | `Instance for Chiller-01.1`을 입력합니다. |
   | **계층 구조** | `Location Hierarchy`를 입력합니다. |
   | **국가** | `USA`를 입력합니다. |
   | **도시** | `Seattle`을 입력합니다. |
   | **빌딩** | `Space Needle`을 입력합니다. |

    위의 필드를 입력한 후에 **저장**을 클릭합니다.

   ![냉각기 저장][37]

1. 다른 센서에서 이전 단계를 반복합니다. 다음 필드를 사용합니다.

   * 냉각기 01.2의 경우:

     | | |
     | --- | --- |
     | **형식** | `Chiller`을(를) 선택합니다. |
     | **설명** | `Instance for Chiller-01.2`를 입력합니다. |
     | **계층 구조** | `Location Hierarchy`를 입력합니다. |
     | **국가** | `USA`를 입력합니다. |
     | **도시** | `Seattle`을 입력합니다. |
     | **빌딩** | `Pacific Science Center`를 입력합니다. |

   * 냉각기 01.3의 경우:

     | | |
     | --- | --- |
     | **형식** | `Chiller`을(를) 선택합니다. |
     | **설명** | `Instance for Chiller-01.1`을 입력합니다. |
     | **계층 구조** | `Location Hierarchy`를 입력합니다. |
     | **국가** | `USA`를 입력합니다. |
     | **도시** | `New York`을 입력합니다. |
     | **빌딩** | `Empire State Building`을 입력합니다. |

1. **분석** 탭으로 이동하고 페이지를 새로 고칩니다. 모든 계층 수준을 확장하여 시계열을 찾습니다.

   ![분석 탭 보기][38]

1. 지난 1시간 동안 시계열을 살펴보려면 **빠른 시간**을 지난 1시간으로 변경합니다.

   ![지난 1시간 살펴보기][39]

1. **태평양 과학 센터**에서 시계열을 클릭하고 **최대 습도 표시**를 클릭합니다.

   ![최대 습도 표시][40]

1. 1분 간격으로 **최대 습도**에 대한 시계열이 열립니다. 지역을 마우스 왼쪽 단추로 클릭하여 범위를 필터링합니다. 그런 다음, 마우스 오른쪽 단추로 클릭하고 확대/축소하여 시간 프레임에서 이벤트를 분석합니다.

   ![보기, 필터 및 확대/축소][41]

   ![보기, 필터 및 확대/축소][42]

1. 지역을 마우스 왼쪽 단추로 클릭한 다음, 클릭하여 이벤트 세부 정보를 볼 수도 있습니다.

   ![보기, 필터 및 확대/축소][43]

   ![보기, 필터 및 확대/축소][44]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.  

* 디바이스 시뮬레이션 가속기 만들기 및 사용
* Azure Time Series Insights 미리 보기 PAYG 환경 만들기
* 이벤트 허브에 Azure Time Series Insights 미리 보기 환경 연결
* Azure Time Series Insights 미리 보기 환경으로 데이터를 스트리밍하도록 풍력 발전소 시뮬레이션 실행
* 데이터에 대한 기본 분석 수행
* 시계열 모델 형식 및 계층 구조 정의 및 인스턴스와 연결

이제 고유한 Azure Time Series Insights 미리 보기 환경을 만드는 방법을 알아보았으므로 Azure Time Series Insights의 주요 개념에 대해 알아봅니다.

Azure Time Series Insights 스토리지 구성에 대해 참고하세요.

> [!div class="nextstepaction"]
> [Azure Time Series Insights 미리 보기 스토리지 및 수신](./time-series-insights-update-storage-ingress.md)

시계열 모델에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Time Series Insights 미리 보기 데이터 모델링](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-six-listings.png
[7]: media/v2-update-provision/device-seven-dashboard.png
[8]: media/v2-update-provision/device-eight-portal.png

[9]: media/v2-update-provision/payg-one-azure.png
[10]: media/v2-update-provision/payg-two-create.png
[11]: media/v2-update-provision/payg-three-new.png
[12]: media/v2-update-provision/payg-four-add.png
[13]: media/v2-update-provision/payg-five-event-source.png
[14]: media/v2-update-provision/payg-six-review.png
[15]: media/v2-update-provision/payg-seven-deploy.png
[16]: media/v2-update-provision/payg-eight-environment.png
[17]: media/v2-update-provision/payg-nine-data-access.png
[18]: media/v2-update-provision/payg-ten-verify.png

[19]: media/v2-update-provision/analyze-one-portal.png
[20]: media/v2-update-provision/analyze-two-unparented.png
[21]: media/v2-update-provision/analyze-three-show-pressure.png
[22]: media/v2-update-provision/analyze-four-chart.png
[23]: media/v2-update-provision/analyze-five-chart.png
[24]: media/v2-update-provision/analyze-six-from.png
[25]: media/v2-update-provision/analyze-seven-change-from.png
[26]: media/v2-update-provision/analyze-eight-all.png

[27]: media/v2-update-provision/define-one-model.png
[28]: media/v2-update-provision/define-two-add.png
[29]: media/v2-update-provision/define-three-variable.png
[30]: media/v2-update-provision/define-four-avg.png
[31]: media/v2-update-provision/define-five-humidity.png
[32]: media/v2-update-provision/define-six-type.png
[33]: media/v2-update-provision/define-seven-hierarchy.png
[34]: media/v2-update-provision/define-eight-add-hierarchy.png
[35]: media/v2-update-provision/define-nine-created.png
[36]: media/v2-update-provision/define-ten-edit.png
[37]: media/v2-update-provision/define-eleven-chiller.png
[38]: media/v2-update-provision/define-twelve.png
[39]: media/v2-update-provision/define-thirteen-explore.png
[40]: media/v2-update-provision/define-fourteen-show-max.png
[41]: media/v2-update-provision/define-fifteen-filter.png
[42]: media/v2-update-provision/define-sixteen.png
[43]: media/v2-update-provision/define-seventeen.png
[44]: media/v2-update-provision/define-eighteen.png