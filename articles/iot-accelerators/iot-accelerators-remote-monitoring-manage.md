---
title: Azure 기반 원격 모니터링 솔루션에서 장치 관리 | Microsoft Docs
description: 이 자습서에서는 원격 모니터링 솔루션 가속기에 연결된 장치를 관리하는 방법을 보여 줍니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/12/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 63baf6397b2542311525bac740c50b5eacbd35cf
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097430"
---
# <a name="tutorial-configure-and-manage-devices-connected-to-your-monitoring-solution"></a>자습서: 모니터링 솔루션에 연결된 장치 구성 및 관리

이 자습서에서는 원격 모니터링 솔루션 가속기를 사용하여 연결된 IoT 장치를 구성하고 관리합니다. 솔루션 가속기에 새 장치를 추가하고 장치를 구성한 다음, 장치의 펌웨어를 업데이트합니다.

Contoso는 해당 시설 중 하나를 확장하기 위해 새로운 기계를 주문했습니다. 배달될 새 기계를 기다리는 동안 솔루션의 동작을 테스트하는 시뮬레이션을 실행하려고 합니다. 시뮬레이션을 실행하려면 원격 모니터링 솔루션 가속기에 시뮬레이션된 새 엔진 장치를 추가하고 시뮬레이션된 이 장치가 작업 및 구성 업데이트에 올바르게 응답하는지 테스트합니다.

장치를 구성 및 관리하는 확장 가능한 방법을 제공하기 위해 원격 모니터링 솔루션 가속기는 [작업](../iot-hub/iot-hub-devguide-jobs.md) 및 [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md)와 같은 IoT Hub 기능을 사용합니다. 이 자습서에서는 시뮬레이션된 장치를 사용하지만 장치 개발자가 [원격 모니터링 솔루션 가속기에 연결된 물리적 장치](iot-accelerators-connecting-devices.md)에 직접 메서드를 구현할 수 있습니다.

이 자습서에서는 다음을 수행했습니다.

>[!div class="checklist"]
> * 시뮬레이트된 장치 프로비전
> * 시뮬레이션된 장치 테스트
> * 장치의 펌웨어 업데이트
> * 장치 다시 구성
> * 장치 구성

## <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 Azure 구독에서 원격 모니터링 솔루션 가속기의 배포된 인스턴스가 필요합니다.

원격 모니터링 솔루션 가속기를 아직 배포하지 않은 경우 [클라우드 기반 원격 모니터링 솔루션 배포](quickstart-remote-monitoring-deploy.md) 빠른 시작을 완료해야 합니다.

## <a name="add-a-simulated-device"></a>시뮬레이트된 장치 추가

솔루션의 **장치** 페이지로 이동한 다음, **+새 장치**를 클릭합니다.

[![시뮬레이션된 장치 프로비전](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

**새 장치**패널에서 **시뮬레이션**을 선택하고 프로비전할 장치 수를 **1**로 유지하고 **Faulty Engine**(엔진 결함) 장치 모델을 선택한 다음, **적용**을 선택하여 시뮬레이션된 장치를 만듭니다.

[![시뮬레이션된 엔진 장치 프로비전](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>시뮬레이트된 장치 테스트

시뮬레이션된 장치가 원격 분석 데이터 및 보고 속성 값을 보내는지 테스트하려면 **장치** 페이지의 장치 목록에서 해당 항목을 선택합니다. 장치에 대한 실시간 정보가 **장치 세부 정보** 패널에 표시됩니다.

[![시뮬레이션된 새 엔진 장치 보기](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

**장치 세부 정보**에서 새 장치가 원격 분석 데이터를 전송하는지 확인합니다. 장치에서 다른 진동 원격 분석 스트림을 보려면 **진동**을 클릭합니다.

[![보려는 원격 분석 스트림 선택](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

**장치 세부 정보** 패널은 태그 값, 지원하는 메서드 및 장치에서 보고되는 속성과 같은 장치에 대한 기타 정보를 표시합니다.

자세한 진단을 보려면 아래로 스크롤하여 **진단**을 봅니다.

[![장치 진단 보기](./media/iot-accelerators-remote-monitoring-manage/devicediagnostics-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicediagnostics-expanded.png#lightbox)

## <a name="act-on-a-device"></a>장치에서 작동

시뮬레이션된 엔진 장치가 솔루션 가속기에서 시작된 작업에 제대로 응답하는지 테스트하려면 **FirmwareUpdate** 메서드를 실행합니다. 메서드를 실행하여 장치에 조치를 취하려면 장치 목록에서 장치를 선택한 다음, **작업**을 선택합니다. 여러 장치에 조치를 취하려면 둘 이상의 장치를 선택할 수 있습니다. **작업** 패널에서 **메서드 실행**을 선택합니다. **엔진** 장치 모델은 세 가지 메서드 즉, **FirmwareUpdate**, **FillTank** 및 **EmptyTank**를 지정합니다.

[![엔진 메서드](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmethods-expanded.png#lightbox)

**FirmwareUpdate**를 선택하고 작업 이름을 **UpdateEngineFirmware**로 설정하고 펌웨어 버전을 **2.0.0**으로 설정하고 펌웨어 URI를 **http://contoso.com/engine.bin**으로 설정한 다음, **적용**을 클릭합니다.

[![펌웨어 업데이트 예약 메서드](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatejob-expanded.png#lightbox)

작업의 상태를 추적하려면 **작업 상태 보기**를 클릭합니다.

[![예약된 펌웨어 업데이트 작업 모니터링](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/firmwareupdatestatus-expanded.png#lightbox)

작업이 완료되면 **장치** 페이지로 다시 돌아갑니다. 엔진 장치에 새 펌웨어 버전이 표시됩니다.

**장치** 페이지에서 다른 유형의 여러 장치를 선택하더라도 여러 장치에서 메서드를 실행할 작업을 만들 수 있습니다. **작업** 패널은 선택한 모든 장치에 공통적인 메서드만 표시합니다.

## <a name="reconfigure-a-device"></a>장치 다시 구성

엔진의 구성 속성을 업데이트할 수 있는지 테스트하려면 **장치** 페이지의 장치 목록에서 해당 항목을 선택합니다. 그런 다음, **작업**을 클릭하고 **다시 구성**을 선택합니다. 작업 패널에 선택한 장치에 대해 업데이트 가능한 속성 값이 표시됩니다.

[![장치 다시 구성](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

엔진의 위치를 업데이트하려면 작업 이름을 **UpdateEngineLocation**으로 설정하고 경도를 **-122.15**로 설정하고 위치를 **Factory 2**로 설정하고 위도를 **47.62**로 설정한 다음, **적용**을 클릭합니다.

[![장치 속성 값 업데이트](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

작업의 상태를 추적하려면 **작업 상태 보기**를 클릭합니다.

[![장치 속성 값 업데이트](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

작업이 완료되면 **대시보드** 페이지로 이동합니다. 엔진 장치가 맵에서 새 위치에 표시됩니다.

[![엔진 위치 보기](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>장치 구성

운영자로서 장치를 쉽게 구성하고 관리하기 위해 적절한 팀 이름으로 태그를 지정하려고 합니다. Contoso에는 필드 서비스 활동에 대한 두 개의 다른 팀이 있습니다.

* Smart Vehicle 팀은 트럭 및 프로토타입 장치를 관리합니다.
* Smart Building 팀은 냉각기, 엘리베이터 및 엔진을 관리합니다.

모든 장치를 표시하려면 **장치** 페이지로 이동하고 **모든 장치** 필터를 선택합니다.

[![모든 장치 표시](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>태그 추가

모든 **트럭** 및 **프로토타입** 장치를 선택합니다. 그런 다음, **작업**을 선택합니다.

[![프로토타입 및 트럭 장치 선택](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect-expanded.png#lightbox)

**태그**를 선택하고 작업 이름을 **AddConnectedVehicleTag**로 설정한 다음, 이름이 **FieldService**고 값이 **ConnectedVehicle**인 텍스트 태그를 추가합니다. 그런 다음 **적용**을 클릭합니다.

[![프로토타입 및 트럭 장치에 태그 추가](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

장치 페이지에서 **냉각기**, **엘리베이터** 및 **엔진** 장치를 모두 선택합니다. 그런 다음, **작업**을 선택합니다.

[![냉각기, 엘리베이터 및 엔진 장치 선택](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmultiselect2-expanded.png#lightbox)

**태그**를 선택하고 작업 이름을 **AddSmartBuildingTag**으로 설정한 다음, 이름이 **FieldService**고 값이 **SmartBuilding**인 텍스트 태그를 추가합니다. 그런 다음 **적용**을 클릭합니다.

[![냉각기, 엘리베이터 및 엔진 장치에 태그 추가](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>필터 생성

이제 태그 값을 사용하여 필터를 만들 수 있습니다. **장치** 페이지에서 **장치 그룹 관리**를 클릭합니다.

[![장치 그룹 관리](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

조건에서 태그 이름 **FieldService** 및 값 **SmartBuilding**을 사용하는 텍스트 필터를 만듭니다. 필터를 **스마트 빌딩**으로 저장합니다.

[![스마트 빌딩 필터 만들기](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

조건에서 태그 이름 **FieldService** 및 값 **ConnectedVehicle**을 사용하는 텍스트 필터를 만듭니다. **Connected Vehicle**로 필터를 저장합니다.

[![커넥티드 차량 필터 만들기](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

Contoso 운영자는 운영 팀을 기반으로 장치를 쿼리할 수 있습니다.

[![커넥티드 차량 필터 만들기](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 진행하려는 경우 배포된 원격 모니터링 솔루션 가속기를 그대로 둡니다. 솔루션 가속기를 사용하지 않는 동안 실행 비용을 줄이기 위해 설정 패널에서 시뮬레이션된 장치를 중지할 수 있습니다.

[![원격 분석 일시 중지](./media/iot-accelerators-remote-monitoring-manage/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/togglesimulation-expanded.png#lightbox)

다음 자습서를 시작할 준비가 되면 시뮬레이션된 장치를 다시 시작할 수 있습니다.

솔루션 가속기가 더 이상 필요하지 않은 경우 [프로비전된 솔루션](https://www.azureiotsolutions.com/Accelerators#dashboard) 페이지에서 삭제합니다.

![솔루션 삭제](media/iot-accelerators-remote-monitoring-manage/deletesolution.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 원격 모니터링 솔루션 가속기에 연결된 장치를 구성하고 관리하는 방법을 보여줬습니다. 솔루션 가속기를 사용하여 연결된 장치의 문제를 확인하고 해결하는 방법을 알아보려면 다음 자습서를 계속 진행하십시오.

> [!div class="nextstepaction"]
> [장치 경고를 사용하여 모니터링 솔루션에 연결된 장치의 문제를 확인 및 해결](iot-accelerators-remote-monitoring-maintain.md)
