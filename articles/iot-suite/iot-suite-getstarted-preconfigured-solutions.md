<properties
	pageTitle="미리 구성된 솔루션 시작 | Microsoft Azure"
	description="미리 구성된 Azure IoT Suite 솔루션을 배포하는 방법을 알아보려면 이 자습서를 따릅니다."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# 자습서: 미리 구성된 솔루션 시작

## 소개

Azure IoT Suite의 [미리 구성된 솔루션][lnk-preconfigured-solutions]은 여러 Azure IoT 서비스를 결합하여 일반적인 IoT 비즈니스 시나리오를 구현하는 종합적인 솔루션을 제공합니다. 미리 구성된 *원격 모니터링* 솔루션은 장치에 연결되고 장치를 모니터링합니다. 데이터의 해당 스트림에 자동으로 응답하는 프로세스를 만들어 장치에서 데이터 스트림을 분석하고 비즈니스 성과를 향상시키는 솔루션을 사용할 수 있습니다.

이 자습서는 미리 구성된 원격 모니터링 솔루션을 프로비전하는 방법을 보여 줍니다. 또한 원격 모니터링 솔루션의 기본 기능을 안내합니다. 미리 구성된 솔루션과 함께 배포한 솔루션 대시보드를 통해 이러한 기능 다수에 액세스할 수 있습니다.

![미리 구성된 원격 모니터링 솔루션 대시보드][img-dashboard]

이 자습서를 완료하려면 활성 Azure 구독이 필요합니다.

> [AZURE.NOTE]  계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험][lnk_free_trial]을 참조하세요.

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## 솔루션 대시보드 보기

솔루션 대시보드를 사용하면 배포된 솔루션을 관리할 수 있습니다. 예를 들어 원격 분석 보기, 장치 추가 및 규칙 구성 작업을 수행할 수 있습니다.

1.  프로비전이 완료되고 미리 구성된 솔루션에 대한 타일이 **준비**를 가리키면 **시작**을 클릭하여 새 탭에서 원격 모니터링 솔루션 포털을 엽니다.

    ![미리 구성된 솔루션 시작][img-launch-solution]

2.  기본적으로 솔루션 포털에 *솔루션 대시보드*가 표시됩니다. 왼쪽 메뉴를 사용하여 다른 뷰를 선택할 수 있습니다.

    ![미리 구성된 원격 모니터링 솔루션 대시보드][img-dashboard]

대시보드에서는 다음 정보를 표시합니다.

- 지도는 솔루션에 연결된 각 장치의 위치를 보여줍니다. 솔루션을 처음으로 실행하는 경우 4개의 시뮬레이션된 장치가 있습니다. 시뮬레이션된 장치는 Azure WebJob으로 구현되며, 솔루션은 Bing 지도 API를 사용하여 지도에 대한 정보를 구상합니다.
- **원격 분석 기록** 패널은 거의 실시간으로 선택한 장치에서 습도 및 온도 원격 분석을 구상하며 최고, 최저 및 평균 습도와 같은 집계 데이터를 표시합니다.
- 원격 분석 값이 임계값을 초과하면 **경보 기록** 패널이 최근 경보 이벤트를 보여 줍니다. 미리 구성된 솔루션으로 생성된 예제 외에 자신만의 경보를 정의할 수 있습니다.

## 장치 목록 보기

장치 목록에 솔루션에 등록된 모든 장치가 표시됩니다. 장치 메타데이터 보기 및 편집, 장치 추가 및 제거, 장치에 명령 보내기 작업을 수행합니다.

1.  왼쪽 메뉴에서 **장치**를 클릭하여 해당 솔루션에 대한 *장치 목록*을 표시합니다.

    ![대시보드의 장치 목록][img-devicelist]

2.  장치 목록은 프로비전 프로세스에서 생성된 네 개의 시뮬레이션된 장치를 보여줍니다.

3.  장치 목록의 장치를 클릭하면 해당 장치의 세부 정보를 볼 수 있습니다.

    ![대시보드의 장치 세부 정보][img-devicedetails]

**장치 세부 정보** 패널은 다음과 같은 세 가지 섹션을 포함합니다.

- **작업** 섹션은 장치에서 수행할 수 있는 작업을 나열합니다. 장치를 비활성화하면 원격 분석을 보내거나 명령을 수신하도록 더 이상 허용되지 않습니다. 장치를 비활성화한 후에는 다시 활성화할 수 있습니다. 원격 분석 값이 임계값을 초과할 때 경보를 트리거하는 장치와 연결된 규칙을 추가할 수 있습니다. 장치에 명령을 보낼 수도 있습니다. 장치가 처음으로 연결되면, 응답할 수 있는 명령을 솔루션에 지시합니다.
- **장치 속성** 섹션에는 장치 메타데이터가 나열됩니다. 메타데이터 중 일부는 장치에서 제공되며(예: 제조업체) 일부는 솔루션에 의해 생성됩니다(예: 만든 날짜). 장치 메타데이터는 여기에서 편집할 수 있습니다.
- **인증 키** 섹션은 장치가 솔루션을 통해 인증에 사용할 수 있는 키를 나열합니다.

## 장치에 명령 보내기

장치 세부 정보 창에 특정 장치가 지원하고 장치에 명령을 보낼 수 있도록 하는 모든 명령이 표시됩니다. 장치가 처음으로 시작되면, 지원하는 명령에 대한 정보를 솔루션에 보냅니다.

1.  선택한 장치에 대한 장치 세부 정보 창에서 **명령**을 클릭합니다.

    ![대시보드의 장치 명령][img-devicecommands]

2.  명령 목록에서 **PingDevice**를 선택합니다.

3.  **명령 전송**을 클릭합니다.

4.  명령 기록에서 명령 상태를 볼 수 있습니다.

    ![대시보드의 명령 상태][img-pingcommand]

솔루션은 보내는 각 명령의 상태를 추적합니다. 초기에 결과는 **보류 중**입니다. 장치가 명령을 실행했다고 보고하면, 결과는 **성공**으로 설정됩니다.

## 시뮬레이션된 새 장치 추가

미리 구성된 솔루션을 배포할 때 장치 목록에서 볼 수 있는 네 개의 샘플 장치를 자동으로 프로비전합니다. 이러한 장치는 Azure WebJob에서 실행되는 *시뮬레이션된 장치*입니다. 시뮬레이션된 장치를 사용하면 실제 물리적 장치를 배포하지 않고도 미리 구성된 솔루션을 쉽게 실험할 수 있습니다. 솔루션에 실제 장치를 연결하려면 [미리 구성된 원격 모니터링 솔루션에 장치 연결][lnk-connect-rm] 자습서를 참조하세요.

다음 단계에서는 솔루션에 시뮬레이션된 장치를 추가하는 방법을 보여 줍니다.

1.  다시 장치 목록으로 이동합니다.

2.  왼쪽 아래 모서리에서 **+ 장치 추가**를 클릭하여 장치를 추가합니다.

    ![미리 구성된 솔루션에 장치 추가][img-adddevice]

3.  **시뮬레이션된 장치** 타일에서 **새 장치 추가**를 클릭합니다.

    ![대시보드에서 새 장치 세부 정보 설정][img-addnew]
    
    시뮬레이션된 새 장치를 만드는 것 외에, **사용자 지정 장치**를 만들도록 선택하면 물리적 장치를 추가할 수도 있습니다. 솔루션에 물리적 장치를 연결하는 방법을 자세히 알아보려면 [미리 구성된 IoT Suite 원격 모니터링 솔루션에 장치 연결][lnk-connect-rm]을 참조하세요.

4.  **나만의 장치 ID 정의**를 선택하고 **mydevice\_01**처럼 고유한 장치 ID 이름을 입력합니다.

5.  **만들기**를 클릭합니다.

    ![새 장치 저장][img-definedevice]

6. 3단계 **시뮬레이션된 장치 추가**에서 **완료**를 클릭하여 장치 목록으로 돌아갑니다.

7. 장치 목록에서 장치가 **실행 중**인 것을 볼 수 있습니다.

    ![장치 목록에서 새 장치 보기][img-runningnew]

8. 대시보드에서 새 장치의 시뮬레이션된 원격 분석을 볼 수도 있습니다.

    ![새 장치에서 원격 분석 보기][img-runningnew-2]

## 장치 메타데이터 편집

장치를 솔루션에 처음 연결할 때 솔루션에 해당 메타데이터를 보냅니다. 솔루션 대시보드를 통해 장치 메타데이터를 편집할 때 장치에 새 메타데이터 값을 전송하고 솔루션 DocumentDB 데이터베이스에 새 값을 저장합니다. 자세한 내용은 [장치 ID 레지스트리 및 DocumentDB][lnk-devicemetadata]를 참조하세요.

1.  다시 장치 목록으로 이동합니다.

2.  **장치 목록**에서 새 장치를 선택한 후 **편집**을 클릭하여 **장치 속성**을 편집합니다.

    ![장치 메타데이터 편집][img-editdevice]

3. 아래로 스크롤하여 위도 및 경도 값을 변경합니다. 그 후 **장치 레지스트리에 변경 사항 저장**을 클릭합니다.

    ![장치 메타데이터 편집][img-editdevice2]

4. 대시보드로 다시 이동하면, 장치의 위치가 지도에서 변경되었습니다.

    ![장치 메타데이터 편집][img-editdevice3]

## 새 장치에 대한 규칙 추가

방금 추가한 새 장치에 대해서는 규칙이 없습니다. 이 섹션에서는 새 장치에서 보고하는 온도가 47도를 넘으면 경보를 트리거하는 규칙을 추가하게 됩니다. 시작하기 전에, 대시보드의 새 장치에 대한 원격 분석 기록은 장치 온도가 45도를 절대 넘지 않도록 표시됩니다.

1.  다시 장치 목록으로 이동합니다.

2.  **장치 목록**에서 새 장치를 선택한 후 **규칙 추가**를 클릭하여 장치에 대한 규칙을 추가합니다.

3. 온도가 47도를 넘으면 **온도**를 데이터 필드로 사용하고 **AlarmTemp**를 출력으로 사용하는 규칙을 만듭니다.

    ![장치 규칙 추가][img-adddevicerule]

4. **규칙 저장 및 보기**를 클릭하여 변경 내용을 저장합니다.

5.  새 장치에 대한 장치 세부 정보 창에서 **명령**을 클릭합니다.

    ![장치 규칙 추가][img-adddevicerule2]

6.  명령 목록에서 **ChangeSetPointTemp**를 선택한 후 **SetPointTemp**를 45로 설정합니다. 그런 다음 **명령 전송**을 클릭합니다.

    ![장치 규칙 추가][img-adddevicerule3]

7.  솔루션 대시보드로 다시 이동합니다. 잠시 후, 새 장치에 의해 보고된 온도가 47도 임계값을 초과하면 **알람 기록** 창에서 새 항목을 확인할 수 있습니다.

    ![장치 규칙 추가][img-adddevicerule4]

8. 대시보드의 **규칙** 페이지에서 모든 규칙을 검토하고 편집할 수 있습니다.

    ![장치 규칙 나열][img-rules]

9. 대시보드 **작업** 페이지의 규칙에 대응하여 취할 수 있는 모든 작업을 검토하고 편집할 수 있습니다.

    ![장치 작업 나열][img-actions]

> [AZURE.NOTE] 규칙에 대한 전자 메일 메시지 또는 SMS를 보낼 수 있는 작업을 정의하거나 [논리 앱][lnk-logic-apps]을 통해 LOB(기간 업무) 시스템과 통합할 수 있습니다. 자세한 내용은 [미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 논리 앱 연결][lnk-logicapptutorial]을 참조하세요.

## 기타 기능

솔루션 포털을 사용하여 모델 번호와 같은 특정 특성을 가진 장치를 검색할 수 있습니다.

![장치 검색][img-search]

장치를 비활성화할 수 있으며, 비활성화된 후에는 파일을 제거할 수 있습니다.

![장치 비활성화 및 제거][img-disable]

## 배후 상황

미리 구성된 솔루션을 배포할 때, 배포 프로세스는 사용자가 선택한 Azure 구독에 여러 리소스를 만듭니다. Azure [포털][lnk-portal]에서 이러한 리소스를 볼 수 있습니다. 배포 프로세스는 미리 구성된 솔루션에 대해 선택한 이름을 기반으로 하는 이름으로 **리소스 그룹**을 만듭니다.

![Azure 포털에서 미리 구성된 솔루션][img-portal]

리소스 그룹의 리소스 목록에서 각 리소스를 선택하여 설정을 볼 수 있습니다.

미리 구성된 솔루션의 소스 코드를 볼 수도 있습니다. 원격 모니터링 사전 구성 솔루션 소스 코드는 [azure-iot-remote-monitoring][lnk-rmgithub] GitHub 리포지토리에 있습니다.

- **DeviceAdministration** 폴더는 대시보드에 대한 소스 코드를 포함합니다.
- **Simulator** 폴더는 시뮬레이션된 장치에 대한 소스 코드를 포함합니다.
- **EventProcessor** 폴더는 들어오는 원격 분석을 처리하는 백 엔드 프로세스에 대한 소스 코드를 포함합니다.

완료되면 [azureiotsuite.com][lnk-azureiotsuite] 사이트의 Azure 구독에서 미리 구성된 솔루션을 삭제할 수 있습니다. 이 사이트를 사용하면 미리 구성된 솔루션을 만들 때 프로비전된 모든 리소스를 쉽게 삭제할 수 있습니다.

> [AZURE.NOTE] 미리 구성된 솔루션과 관련된 모든 항목을 삭제하려면 포털에서 리소스 그룹을 삭제하지 않고 [azureiotsuite.com][lnk-azureiotsuite]에서 삭제합니다.

## 다음 단계

미리 구성한 작업 솔루션을 배포했으므로 다음 문서를 참고하여 IoT Suite 시작을 계속할 수 있습니다.

- [미리 구성된 원격 모니터링 솔루션 연습][lnk-rm-walkthrough]
- [미리 구성된 원격 모니터링 솔루션에 장치 연결][lnk-connect-rm]
- [azureiotsuite.com 사이트에 대한 사용 권한][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
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
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md

<!---HONumber=AcomDC_0817_2016-->