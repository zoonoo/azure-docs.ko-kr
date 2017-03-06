---
title: "미리 구성된 솔루션 시작 | Microsoft Docs"
description: "미리 구성된 Azure IoT Suite 솔루션을 배포하는 방법을 알아보려면 이 자습서를 따릅니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9e1bcba086a9f70c689a5d7d7713a8ecdc764492
ms.openlocfilehash: 8248e0a02cb0775a87f0c8130e53b98f8bcfe581
ms.lasthandoff: 02/27/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>자습서: 미리 구성된 솔루션 시작
## <a name="introduction"></a>소개
Azure IoT Suite의 [미리 구성된 솔루션][lnk-preconfigured-solutions]은 여러 Azure IoT 서비스를 결합하여 일반적인 IoT 비즈니스 시나리오를 구현하는 종합적인 솔루션을 제공합니다. 미리 구성된 *원격 모니터링* 솔루션은 장치에 연결되고 장치를 모니터링합니다. 데이터의 해당 스트림에 자동으로 응답하는 프로세스를 만들어 장치에서 데이터 스트림을 분석하고 비즈니스 성과를 향상시키는 솔루션을 사용할 수 있습니다.

이 자습서는 미리 구성된 원격 모니터링 솔루션을 프로비전하는 방법을 보여 줍니다. 또한 미리 구성된 솔루션의 기본 기능을 안내합니다. 미리 구성된 솔루션의 일부로 배포한 솔루션 대시보드에서 이러한 기능 다수에 액세스할 수 있습니다.

![미리 구성된 원격 모니터링 솔루션 대시보드][img-dashboard]

이 자습서를 완료하려면 활성 Azure 구독이 필요합니다.

> [!NOTE]
> 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk_free_trial]을 참조하세요.
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>솔루션 대시보드 보기
솔루션 대시보드를 사용하면 배포된 솔루션을 관리할 수 있습니다. 예를 들어 원격 분석 보기, 장치 추가 및 규칙 구성 작업을 수행할 수 있습니다.

1. 프로비전이 완료되고 미리 구성된 솔루션에 대한 타일이 **준비**를 가리키면 **시작**을 클릭하여 새 탭에서 원격 모니터링 솔루션 포털을 엽니다.
   
   ![미리 구성된 솔루션 시작][img-launch-solution]
2. 기본적으로 솔루션 포털에 *솔루션 대시보드*가 표시됩니다. 왼쪽 메뉴를 사용하여 다른 뷰를 선택할 수 있습니다.
   
   ![미리 구성된 원격 모니터링 솔루션 대시보드][img-dashboard]

대시보드에서는 다음 정보를 표시합니다.

* 지도는 솔루션에 연결된 각 장치의 위치를 보여줍니다. 솔루션을 처음으로 실행하는 경우&4;개의 시뮬레이션된 장치가 있습니다. 시뮬레이션된 장치는 Azure WebJob으로 구현되며, 솔루션은 Bing 지도 API를 사용하여 지도에 대한 정보를 구상합니다.
* **원격 분석 기록** 패널은 거의 실시간으로 선택한 장치에서 습도 및 온도 원격 분석을 구상하며 최고, 최저 및 평균 습도와 같은 집계 데이터를 표시합니다.
* 원격 분석 값이 임계값을 초과하면 **경보 기록** 패널이 최근 경보 이벤트를 보여 줍니다. 미리 구성된 솔루션으로 생성된 예제 외에 자신만의 경보를 정의할 수 있습니다.
* **작업** 패널은 예약된 작업에 대한 정보를 표시합니다. **관리 작업** 페이지에서 사용자 고유의 작업을 예약할 수 있습니다.

## <a name="view-the-device-list"></a>장치 목록 보기
*장치 목록*에 솔루션에 등록된 모든 장치가 표시됩니다. 장치 목록에서 장치 메타데이터를 보고 편집하고 장치를 추가 또는 제거하고 장치의 메서드를 호출할 수 있습니다.

1. 왼쪽 메뉴에서 **장치**를 클릭하여 해당 솔루션에 대한 장치 목록을 표시합니다.
   
   ![대시보드의 장치 목록][img-devicelist]
2. 장치 목록은 프로비전 프로세스에서 생성된 네 개의 시뮬레이션된 장치를 처음에 보여 줍니다. 솔루션에 추가 시뮬레이션된 장치 및 물리적 장치를 추가할 수 있습니다.
3. **열 편집기**를 클릭하여 장치 목록에 표시되는 정보를 사용자 지정할 수 있습니다. reported 속성 및 태그 값을 표시하는 열을 추가 및 제거할 수 있습니다. 또한 열의 순서를 변경하고 이름을 바꿀 수도 있습니다.
   
   ![대시보드의 열 편집기][img-columneditor]
4. 장치의 세부 정보를 보려면 장치 목록의 장치를 클릭합니다.
   
   ![대시보드의 장치 세부 정보][img-devicedetails]

**장치 세부 정보** 패널은 다음과 같은 여섯 가지 섹션을 포함합니다.

* 장치 아이콘을 사용자 지정, 장치를 비활성화, 규칙 추가, 메서드 호출 또는 명령을 보낼 수 있도록 하는 링크의 컬렉션입니다. 명령(장치-클라우드 메시지) 및 메서드(직접 메서드)의 비교는 [클라우드-장치 통신 지침][lnk-c2d-guidance]을 참조하세요.
* **장치 쌍 - 태그** 섹션을 통해 장치에 대한 태그 값을 편집할 수 있습니다. 장치 목록에 태그 값을 표시할 수 있으며 태그 값을 사용하여 장치 목록을 필터링할 수 있습니다.
* **장치 쌍 - Desired 속성** 섹션을 통해 장치에 전송될 속성 값을 설정할 수 있습니다.
* **장치 쌍 - Reported 속성** 섹션은 장치에서 전송된 속성 값을 보여 줍니다.
* **장치 속성** 섹션은 장치 ID 및 인증 키와 같은 ID 레지스트리에서 정보를 표시합니다.
* **최근 작업** 섹션은 이 장치에 가장 최근에 지정된 모든 작업에 대한 정보를 표시합니다.

## <a name="customize-the-device-icon"></a>장치 아이콘 사용자 지정

다음과 같이 **장치 세부 정보** 패널에서 장치 목록에 표시되는 장치 아이콘을 사용자 지정할 수 있습니다.

1. 연필 아이콘을 클릭하여 장치에 대한 **이미지 편집** 패널을 엽니다.
   
   ![장치 이미지 편집기 열기][img-startimageedit]
2. 새 이미지를 업로드하거나 기존 이미지 중 하나를 사용한 다음 **저장**을 클릭합니다.
   
   ![장치 이미지 편집기 편집][img-imageedit]
3. 이제 선택한 이미지가 장치에 대한 **아이콘** 열에 표시됩니다.

> [!NOTE]
> 이미지는 Blob 저장소에 저장됩니다. 장치 쌍의 태그는 Blob 저장소의 이미지에 대한 링크를 포함합니다.
> 
> 

## <a name="invoke-a-method-on-a-device"></a>장치에서 메서드 호출
**장치 세부 정보** 패널에서 장치에 대한 메서드를 호출할 수 있습니다. 장치가 처음으로 시작되면, 지원하는 메서드에 대한 정보를 솔루션에 보냅니다.

1. 선택한 장치에 대한 **장치 세부 정보** 창에서 **메서드**를 클릭합니다.
   
   ![대시보드의 장치 메서드][img-devicemethods]
2. 메서드 목록에서 **다시 부팅**을 선택합니다.
3. **메서드 호출**을 클릭합니다.
4. 메서드 기록에서 메서드 호출의 상태를 볼 수 있습니다.
   
   ![대시보드의 메서드 상태][img-pingmethod]

솔루션은 호출하는 각 메서드의 상태를 추적합니다. 장치가 메서드를 완료하면 메서드 기록 테이블에 새 항목이 표시됩니다.

일부 메서드는 장치에서 비동기 작업을 시작합니다. 예를 들어 **InitiateFirmwareUpdate** 메서드는 비동기 작업을 시작하여 업데이트를 수행합니다. 장치는 reported 속성을 사용하여 진행됨에 따라 펌웨어 업데이트의 상태를 보고합니다.

## <a name="send-a-command-to-a-device"></a>장치에 명령 보내기
**장치 세부 정보** 패널에서 장치에 명령을 보낼 수 있습니다. 장치가 처음으로 시작되면, 지원하는 명령에 대한 정보를 솔루션에 보냅니다.

1. 선택한 장치에 대한 **장치 세부 정보** 창에서 **명령**을 클릭합니다.
   
   ![대시보드의 장치 명령][img-devicecommands]
2. 명령 목록에서 **PingDevice** 를 선택합니다.
3. **명령 전송**을 클릭합니다.
4. 명령 기록에서 명령 상태를 볼 수 있습니다.
   
   ![대시보드의 명령 상태][img-pingcommand]

솔루션은 보내는 각 명령의 상태를 추적합니다. 초기에 결과는 **보류 중**입니다. 장치가 명령을 실행했다고 보고하면, 결과는 **성공**으로 설정됩니다.

## <a name="add-a-new-simulated-device"></a>시뮬레이션된 새 장치 추가
미리 구성된 솔루션을 배포할 때 장치 목록에서 볼 수 있는 네 개의 샘플 장치를 자동으로 프로비전합니다. 이러한 장치는 Azure WebJob에서 실행되는 *시뮬레이션된 장치* 입니다. 시뮬레이션된 장치를 사용하면 실제 물리적 장치를 배포하지 않고도 미리 구성된 솔루션을 쉽게 실험할 수 있습니다. 솔루션에 실제 장치를 연결하려면 [미리 구성된 원격 모니터링 솔루션에 장치 연결][lnk-connect-rm] 자습서를 참조하세요.

다음 단계에서는 솔루션에 시뮬레이션된 장치를 추가하는 방법을 보여 줍니다.

1. 다시 장치 목록으로 이동합니다.
2. 장치를 추가하려면 왼쪽 아래 모서리에서 **+ 장치 추가**를 클릭합니다.
   
   ![미리 구성된 솔루션에 장치 추가][img-adddevice]
3. **시뮬레이션된 장치** 타일에서 **새 장치 추가**를 클릭합니다.
   
   ![대시보드에서 새 장치 세부 정보 설정][img-addnew]
   
   시뮬레이션된 새 장치를 만드는 것 외에, **사용자 지정 장치**를 만들도록 선택하면 물리적 장치를 추가할 수도 있습니다. 솔루션에 물리적 장치를 연결하는 방법을 자세히 알아보려면 [미리 구성된 IoT Suite 원격 모니터링 솔루션에 장치 연결][lnk-connect-rm]을 참조하세요.
4. **나만의 장치 ID 정의**를 선택하고 **mydevice_01**처럼 고유한 장치 ID 이름을 입력합니다.
5. **만들기**를 클릭합니다.
   
   ![새 장치 저장][img-definedevice]
6. 3단계 **시뮬레이션된 장치 추가**에서 **완료**를 클릭하여 장치 목록으로 돌아갑니다.
7. 장치 목록에서 장치가 **실행 중** 인 것을 볼 수 있습니다.
   
    ![장치 목록에서 새 장치 보기][img-runningnew]
8. 대시보드에서 새 장치의 시뮬레이션된 원격 분석을 볼 수도 있습니다.
   
    ![새 장치에서 원격 분석 보기][img-runningnew-2]

## <a name="device-properties"></a>장치 속성
원격 모니터링 미리 구성된 솔루션은 [장치 쌍][lnk-device-twin]을 사용하여 장치와 솔루션 백 엔드 간의 장치 메타데이터를 동기화합니다. 장치 쌍은 개별 장치에 대한 속성 값을 저장하는 IoT Hub에 저장된 JSON 문서입니다. 장치는 *reported 속성*으로 메타데이터를 솔루션 백 엔드에 정기적으로 보내 장치 쌍에 저장합니다. 솔루션 백 엔드는 장치 쌍에서 *desired 속성*을 설정하여 장치에 메타데이터 업데이트를 보낼 수 있습니다. reported 속성은 장치에서 전송되는 가장 최근의 메타데이터 값을 보여 줍니다. 자세한 내용은 [장치 ID 레지스트리, 장치 쌍 및 DocumentDB][lnk-devicemetadata]를 참조하세요.

> [!NOTE]
> 또한 솔루션은 DocumentDB 데이터베이스를 사용하여 명령 및 메서드와 관련된 장치별 데이터를 저장합니다.
> 
> 

1. 다시 장치 목록으로 이동합니다.
2. **장치 목록**에서 새 장치를 선택한 후 **편집**을 클릭하여 **장치 쌍 - Desired 속성**을 편집합니다.
   
   ![장치 desired 속성 편집][img-editdevice]
3. **Desired 속성 이름**을 **위도**로 설정하고 값을 **47.639521**로 설정합니다. 그런 다음 **장치 레지스트리에 변경 사항 저장**을 클릭합니다.
   
    ![장치 desired 속성 편집][img-editdevice2]
4. **장치 세부 정보** 패널에서 새 위도 값은 desired 속성으로 처음 표시하고 이전 위도 값은 reported 속성으로 표시합니다.
   
    ![reported 속성 보기][img-editdevice3]
5. 현재 미리 구성된 솔루션에서 시뮬레이션된 장치는 **Desired.Config.TemperatureMeanValue** 및 **Desired.Config.TelemetryInterval** desired 속성만을 처리합니다. 실제 장치는 IoT Hub에서 모든 desired 속성을 읽고 해당 구성을 변경하고 reported 속성으로 허브에 새 값을 보고해야 합니다.

**장치 세부 정보** 패널에서 **장치 쌍 - Desired 속성**을 편집하는 동일한 방식으로 **장치 쌍 - 태그**를 편집할 수도 있습니다. 그러나 desired 속성과 달리 태그는 장치와 동기화되지 않습니다. 태그는 IoT Hub의 장치 쌍에만 존재합니다. 태그는 장치 목록에서 사용자 지정 필터를 빌드하는 데 유용합니다.

## <a name="sort-the-device-list"></a>장치 목록 정렬

열 머리글 옆을 클릭하여 장치 목록을 정렬할 수 있습니다. 첫 번째 클릭은 오름차순으로 정렬하고 두 번째 클릭은 내림차순으로 정렬합니다.

![장치 목록 정렬][img-sortdevices]

## <a name="filter-the-device-list"></a>장치 목록 필터링

장치 목록에서 허브에 연결된 장치의 사용자 지정된 목록을 표시하는 필터를 생성, 저장 및 다시 로드할 수 있습니다. 필터를 만들려면:

1. 장치 목록 위의 편집 필터 아이콘을 클릭합니다.
   
   ![필터 편집기 열기][img-editfiltericon]
2. **필터 편집기**에서 필드, 연산자 및 값을 추가하여 장치 목록을 필터링합니다. 여러 절을 추가하여 필터를 조정할 수 있습니다. **필터**를 클릭하여 필터를 적용합니다.
   
   ![필터 만들기][img-filtereditor]
3. 이 예제에서는 제조업체 및 모델 번호로 목록이 필터링됩니다.
   
   ![필터링된 목록][img-filterelist]
4. 사용자 지정 이름으로 필터를 저장하려면 **다른 이름으로 저장** 아이콘을 클릭합니다.
   
   ![필터 저장][img-savefilter]
5. 이전에 저장한 필터를 다시 적용하려면 **저장된 필터 열기** 아이콘을 클릭합니다.
   
   ![필터 열기][img-openfilter]

장치 ID, 장치 상태, desired 속성, reported 속성 및 태그를 기준으로 필터를 만들 수 있습니다.

> [!NOTE]
> **필터 편집기**에서 **고급 보기**를 사용하여 쿼리 텍스트를 직접 편집할 수 있습니다.
> 
> 

## <a name="add-a-rule-for-the-new-device"></a>새 장치에 대한 규칙 추가
방금 추가한 새 장치에 대해서는 규칙이 없습니다. 이 섹션에서는 새 장치에서 보고하는 온도가 47도를 넘으면 경보를 트리거하는 규칙을 추가하게 됩니다. 시작하기 전에, 대시보드의 새 장치에 대한 원격 분석 기록은 장치 온도가 45도를 절대 넘지 않도록 표시됩니다.

1. 다시 장치 목록으로 이동합니다.
2. 장치에 대한 규칙을 추가하려면 **장치 목록**에서 새 장치를 선택한 후 **규칙 추가**를 클릭합니다.
3. 온도가 47도를 넘으면 **온도**를 데이터 필드로 사용하고 **AlarmTemp**를 출력으로 사용하는 규칙을 만듭니다.
   
    ![장치 규칙 추가][img-adddevicerule]
4. 변경 내용을 저장하려면 **규칙 저장 및 보기**를 클릭합니다.
5. 새 장치에 대한 장치 세부 정보 창에서 **명령** 을 클릭합니다.
   
   ![장치 규칙 추가][img-adddevicerule2]
6. 명령 목록에서 **ChangeSetPointTemp**를 선택한 후 **SetPointTemp**를 45로 설정합니다. 그런 다음 **명령 전송**을 클릭합니다.
   
   ![장치 규칙 추가][img-adddevicerule3]
7. 솔루션 대시보드로 다시 이동합니다. 잠시 후, 새 장치에 의해 보고된 온도가 47도 임계값을 초과하면 **알람 기록** 창에서 새 항목을 확인할 수 있습니다.
   
   ![장치 규칙 추가][img-adddevicerule4]
8. 대시보드의 **규칙** 페이지에서 모든 규칙을 검토하고 편집할 수 있습니다.
   
    ![장치 규칙 나열][img-rules]
9. 대시보드 **작업** 페이지의 규칙에 대응하여 취할 수 있는 모든 작업을 검토하고 편집할 수 있습니다.
   
    ![장치 작업 나열][img-actions]

> [!NOTE]
> 규칙에 대한 전자 메일 메시지 또는 SMS를 보낼 수 있는 작업을 정의하거나 [Logic App][lnk-logic-apps]을 통해 LOB(기간 업무) 시스템과 통합할 수 있습니다. 자세한 내용은 [미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 Logic App 연결][lnk-logicapptutorial]을 참조하세요.
> 
> 

## <a name="disable-and-remove-devices"></a>장치 비활성화 및 제거
장치를 비활성화할 수 있으며, 비활성화된 후에는 파일을 제거할 수 있습니다.

![장치 비활성화 및 제거][img-disable]

## <a name="run-jobs"></a>작업 실행
장치에서 대량 작업을 수행하는 작업을 예약할 수 있습니다. 장치 목록에 대한 작업을 만듭니다. 이 목록은 모든 장치를 포함하거나 장치 목록의 [필터 도구](#filter-the-device-list)를 사용하여 만든 필터링된 된 목록일 수 있습니다. 작업은 목록의 모든 장치에서 메서드를 호출하거나 장치 목록의 모든 장치에 대해 장치 쌍을 업데이트할 수 있습니다.

### <a name="create-a-job-to-invoke-a-method"></a>메서드를 호출하는 작업 만들기

다음 단계는 목록의 모든 장치에서 펌웨어 업데이트 메서드를 호출하는 작업을 만드는 방법을 보여 줍니다. 메서드는 메서드를 지원하는 장치에서만 호출됩니다.

1. 장치 목록의 필터 도구를 사용하여 펌웨어 업데이트를 수신하도록 장치 목록을 만듭니다.
   
   ![필터 편집기 열기][img-editfiltericon]
2. 필터링된 목록에서 **작업 스케줄러**를 클릭합니다.
   
   ![작업 스케줄러 열기][img-clickjobscheduler]
3. **작업 예약** 패널에서 **메서드 호출**을 클릭합니다.
4. **메서드 호출** 페이지에서 호출할 메서드의 세부 정보를 입력한 다음 **일정**을 클릭합니다.
   
   ![메서드 작업 구성][img-invokemethodjob]

**InitiateFirmwareUpdate** 메서드는 장치에서 작업을 비동기적으로 시작하고 즉시 반환합니다. 그러면 펌웨어 업데이트 프로세스는 reported 속성을 사용하여 실행되는 업데이트 프로세스에 대해 보고합니다.

### <a name="create-a-job-to-edit-the-device-twin"></a>장치 쌍 편집을 위한 작업 만들기

다음 단계는 목록의 모든 장치에서 장치 쌍을 편집하는 작업을 만드는 방법을 보여 줍니다.

1. 장치 목록의 필터 도구를 사용하여 장치 쌍 편집을 수신하도록 장치 목록을 만듭니다.
   
   ![필터 편집기 열기][img-editfiltericon]
2. 필터링된 목록에서 **작업 스케줄러**를 클릭합니다.
   
   ![작업 스케줄러 열기][img-clickjobscheduler]
3. **작업 예약** 패널에서 **장치 쌍 편집**을 클릭합니다.
4. **장치 쌍 편집** 페이지에서 편집할 **Desired 속성** 및 **태그**의 세부 정보를 입력한 다음 **일정**을 클릭합니다.
   
   ![메서드 작업 구성][img-edittwinjob]

### <a name="monitor-the-job"></a>작업 모니터링
**관리 작업** 페이지에서 예약한 작업의 상태를 모니터링할 수 있습니다. **작업 세부 정보** 패널은 선택한 작업에 대한 정보를 표시합니다.
   
   ![작업 상태 보기][img-jobstatus]

**대시보드**에서 작업에 대한 정보를 볼 수도 있습니다.
   
   ![대시보드에서 작업 보기][img-jobdashboard]


## <a name="behind-the-scenes"></a>배후 상황
미리 구성된 솔루션을 배포할 때, 배포 프로세스는 사용자가 선택한 Azure 구독에 여러 리소스를 만듭니다. Azure [Portal][lnk-portal]에서 이러한 리소스를 볼 수 있습니다. 배포 프로세스는 미리 구성된 솔루션에 대해 선택한 이름을 기반으로 하는 이름으로 **리소스 그룹** 을 만듭니다.

![Azure 포털에서 미리 구성된 솔루션][img-portal]

리소스 그룹의 리소스 목록에서 각 리소스를 선택하여 설정을 볼 수 있습니다.

미리 구성된 솔루션의 소스 코드를 볼 수도 있습니다. 원격 모니터링 사전 구성 솔루션 소스 코드는 [azure-iot-remote-monitoring][lnk-rmgithub] GitHub 리포지토리에 있습니다.

* **DeviceAdministration** 폴더는 대시보드에 대한 소스 코드를 포함합니다.
* **Simulator** 폴더는 시뮬레이션된 장치에 대한 소스 코드를 포함합니다.
* **EventProcessor** 폴더는 들어오는 원격 분석을 처리하는 백 엔드 프로세스에 대한 소스 코드를 포함합니다.

완료되면 [azureiotsuite.com][lnk-azureiotsuite] 사이트의 Azure 구독에서 미리 구성된 솔루션을 삭제할 수 있습니다. 이 사이트를 사용하면 미리 구성된 솔루션을 만들 때 프로비전된 모든 리소스를 쉽게 삭제할 수 있습니다.

> [!NOTE]
> 미리 구성된 솔루션과 관련된 모든 항목을 삭제하려면 포털에서 리소스 그룹을 삭제하지 않고 [azureiotsuite.com][lnk-azureiotsuite]에서 삭제합니다.
> 
> 

## <a name="next-steps"></a>다음 단계
미리 구성한 작업 솔루션을 배포했으므로 다음 문서를 참고하여 IoT Suite 시작을 계속할 수 있습니다.

* [미리 구성된 원격 모니터링 솔루션 연습][lnk-rm-walkthrough]
* [미리 구성된 원격 모니터링 솔루션 연습][lnk-connect-rm]
* [azureiotsuite.com 사이트에 대한 사용 권한][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-devicemethods]: media/iot-suite-getstarted-preconfigured-solutions/devicemethods.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-pingmethod]: media/iot-suite-getstarted-preconfigured-solutions/pingmethod.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-sortdevices]: media/iot-suite-getstarted-preconfigured-solutions/sortdevices.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-clickjobscheduler]: media/iot-suite-getstarted-preconfigured-solutions/clickscheduler.png
[img-invokemethodjob]: media/iot-suite-getstarted-preconfigured-solutions/invokemethodjob.png
[img-edittwinjob]: media/iot-suite-getstarted-preconfigured-solutions/edittwinjob.png
[img-jobstatus]: media/iot-suite-getstarted-preconfigured-solutions/jobstatus.png
[img-jobdashboard]: media/iot-suite-getstarted-preconfigured-solutions/jobdashboard.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-device-twin-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
