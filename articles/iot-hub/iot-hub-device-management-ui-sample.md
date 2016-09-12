<properties
 pageTitle="IoT Hub 장치 관리 UI 사용 | Microsoft Azure"
 description="Azure IoT Hub 장치 관리 UI 사용법 연습"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/25/2016"
 ms.author="dobett"/>

# 샘플 UI를 사용하여 Azure IoT Hub 장치 관리 탐색

샘플 장치 관리 UI를 사용해 보면 Azure IoT Hub 장치 관리 [개요][lnk-dm-overview]와 [시작하기][lnk-get-started] 문서에서 설명하는 개념과 기능을 명확하게 파악할 수 있습니다. 이 문서는 샘플 장치 관리 웹 UI에 나타난 바와 같이 각각의 세 가지 주요 장치 관리 개념(*장치 쌍*, *장치 쿼리*, *장치 작업*)을 둘러봅니다.

직접 대화형 장치 관리 경험을 빌드하고자 하는 개발자는 샘플 UI 코드 베이스를 분기하여 사용자 지정 프로젝트의 기본으로 사용할 수 있습니다. [Azure IoT 장치 관리 UI][lnk-dm-github] GitHub 리포지토리에서 상세한 개발자 기능을 설명한 추가 정보 문서와 전체 프로젝트 코드를 검토할 수 있습니다.

## 필수 조건

자습을 시작하기 전에 [Azure IoT Hub 장치 관리 시작하기][lnk-get-started] 문서에 있는 단계를 완료해야 합니다. 이 문서의 모든 단계를 아직 완료하지 않은 경우 계속 진행하기 전에 앞으로 돌아가서 모든 단계를 완료하세요.

"시작하기" 자습서를 완료하면 테스트 시스템에서 다음 항목이 실행됩니다.

- 콘솔/터미널 창에서 실행되는 **iotdm\_simple\_sample** 시뮬레이션 장치 6개, 각각 성공적으로 "등록됨" 메시지가 표시됩니다.

- <http://127.0.0.1:3003>에서 빌드 및 실행되는 장치 관리 샘플 UI 응용 프로그램.

## 기본 장치 보기

장치 관리 샘플 UI의 기본 홈 화면은 다음 5가지 구성 요소가 포함된 **장치** 뷰입니다.

![][1]

1.  *탐색 단추*: **장치** 뷰(선택됨), **작업 기록** 뷰, **장치 추가** 뷰.

2. *장치 검색*: 장치 ID로 단일 장치 검색 및 편집.

3.  *장치 동작*: **편집**, **삭제**, **내보내기** 작업.

4.  *장치 작업*: 장치 **재부팅**, **펌웨어 업데이트**, **공장 재설정**.

5.  *장치 그리드 필터*: 장치 그리드의 사용자 지정 뷰 빌드 및 저장을 위한 필터 편집기

6.  *장치 그리드*: 기본 속성(**장치 ID**, **상태**, **태그**)으로 IoT 허브 인스턴스에 등록된 모든 장치 보기.

[장치 관리 개요][lnk-dm-overview]에서는 Azure IoT Hub의 실제 또는 시뮬레이션 장치를 나타내는 *장치 쌍* 개념을 소개했습니다. 장치 그리드의 장치 목록에서 등록된 장치를 선택하면 해당 장치의 장치 쌍을 확인 및 편집할 수 있습니다.

첫 번째 시뮬레이션 장치(**Device11-7ce4a850**)에 대해 이 상세 보기를 실행하려면 해당하는 장치 행을 선택합니다. 그런 후에 **편집** 단추를 클릭합니다. 행을 두 번 클릭하거나 검색 상자에 장치 ID를 입력해도 됩니다.

그러면 장치 쌍 구성 요소가 완전히 표시됩니다. 여기서 쓰기 가능한 속성을 업데이트하고 아래와 같이 다른 장치 작업을 실행할 수 있습니다.

![][2]

1.  **장치 쌍 편집**: 이 옵션에는 해당 장치의 **활성화/비활성화** 상태를 전환하는 기능이 포함되어 있습니다.

2.  **서비스 속성**: 이 옵션에는 장치 **태그**가 포함되어 있습니다.

3.  **장치 속성**:이 섹션을 클릭하여 펼칩니다.

4.  **새로 고침** 단추: 가장 최근의 장치 쌍 속성과 값을 검색합니다.

계속하려면 이 뷰의 오른쪽 아래 모서리에 있는 **취소**를 클릭하고 기본 장치 목록 페이지로 돌아갑니다.

## 장치 쿼리를 사용하여 장치 뷰 필터링

장치 쿼리는 특정 속성(예: 특정 태그)으로 장치 또는 장치 그룹을 신속히 찾을 수 있는 강력한 방법입니다. 샘플 UI는 쿼리를 사용하여 기본 장치 목록 페이지의 장치 목록을 채웁니다. 샘플 UI는 이런 일부 속성의 테이블과 필터에서 서비스 속성을 추가/삭제할 수 있게 해줍니다.

“bacon” 서비스 속성 태그에 사용자 지정 필터를 만들려면 다음 단계를 수행합니다.

1.  깔때기 모양 아이콘을 클릭하여 장치 쿼리 편집 뷰를 엽니다.

    ![][3]

2.  **+ 필터 추가**를 클릭하여 편집기를 펼칩니다. 속성 드롭다운에서 **태그**를 선택하고 텍스트 필드에 **bacon**을 입력한 다음, **적용**을 클릭합니다. 이제 장치 목록에는 “bacon” 태그가 달린 장치 3개만 표시됩니다.

    ![][4]

3.  쿼리 제목 필드(깔때기 모양 아이콘 옆)에서 **Only Bacon**으로 쿼리 이름을 지정하고 **저장**을 클릭합니다.

    ![][5]

4.  깔때기 모양 아이콘을 클릭하여 장치 쿼리 편집기를 나갑니다.

## 장치 작업을 사용하여 장치 재부팅 시뮬레이션 

장치 관리 개요에서 배운 바와 같이 장치 작업을 사용하여 하나 이상의 물리적 장치에서 간단하거나 복잡한 작업을 오케스트레이션할 수 있습니다. 이 섹션에서는 샘플 UI에서 장치 작업을 만들고, "bacon" 태그가 달린 모든 시뮬레이션 장치에서 다시 부팅 작업을 수행합니다.

1.  **Only Bacon** 장치 쿼리 목록에서 각 장치 행을 클릭하여 장치를 재부팅 작업 대상으로 표시합니다.

    ![][6]

2.  장치 작업 도구 모음에서 **재부팅** 단추를 클릭하고 재부팅 작업을 만듭니다. **예**를 선택한 후 나타나는 **장치 작업이 시작되었습니다** 대화 상자에서 **작업 기록**을 클릭하고 장치 작업 뷰를 탐색합니다.

    ![][7]

이제 세 개의 하위 작업이 포함된 하나의 상위 작업이 생성되었습니다. 각 하위 작업은 “bacon” 태그가 달린 장치 각각에서 재부팅 작업을 수행합니다.

![][8]

몇 분 후에 이 화면을 새로 고치면 부모 작업과 3개 자식 작업의 상태가 **완료됨**으로 변경되었음을 확인할 수 있습니다. 새로운 상태 값은 다시 부팅 작업이 정상적으로 완료되었으며 시뮬레이션 장치에 의해 확인되었음을 나타냅니다. **장치 ID** 열을 사용하여 어느 작업과 어느 장치가 연결되어 있는지 확인합니다.


> [AZURE.NOTE] 하위 작업이 “실패함" 상태를 반환하면 시뮬레이션 장치가 테스트 시스템에서 실행 중이 아닌지 확인합니다. 그렇지 않다면 simulate.bat 또는 simulate.sh 스크립트를 다시 실행하고 이전 섹션의 장치 다시 부팅 작업 단계를 반복합니다.

## 다음 단계

이제 여러분은 안내에 따라 샘플 장치 관리 UI 경험을 사용하여 장치 관리 개념의 탐색을 완료하였습니다. 장치 관리 API와 코드 예제 실험에 대해 더욱 심도 있게 이해하고 싶다면 다음 개발자 자습서 및 리소스를 참조하세요.

- [장치 쌍을 사용하는 방법][lnk-tutorial-twin]
- [쿼리를 사용하여 장치 쌍을 찾는 방법][lnk-tutorial-queries]
- [장치 작업을 사용하여 장치 펌웨어를 업데이트하는 방법][lnk-tutorial-jobs]
- [IoT 게이트웨이 뒤에서 관리되는 장치 사용][lnk-dm-gateway]
- [Azure IoT Hub 장치 관리 클라이언트 라이브러리 소개][lnk-library-c]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

- [솔루션 디자인][lnk-design]
- [개발자 가이드][lnk-devguide]
- [Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]
- [Azure 포털을 사용하여 IoT Hub 관리][lnk-portal]
- [처음부터 IoT 솔루션 보안 유지][lnk-securing]

[1]: media/iot-hub-device-management-ui-sample/image1.png
[2]: media/iot-hub-device-management-ui-sample/image2.png
[3]: media/iot-hub-device-management-ui-sample/image3.png
[4]: media/iot-hub-device-management-ui-sample/image4.png
[5]: media/iot-hub-device-management-ui-sample/image5.png
[6]: media/iot-hub-device-management-ui-sample/image6.png
[7]: media/iot-hub-device-management-ui-sample/image7.png
[8]: media/iot-hub-device-management-ui-sample/image8.png

[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management/

[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0831_2016-->