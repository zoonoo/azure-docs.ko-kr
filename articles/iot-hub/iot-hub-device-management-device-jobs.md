<properties
	pageTitle="IoT Hub 장치 관리 작치 작업 | Microsoft Azure"
	description="장치 작업을 사용하여 원격 펌웨어 업데이트와 같은 작업을 수행하는 방법을 설명하는 Azure IoT Hub 장치 관리 자습서."
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# 자습서: 장치 작업을 사용하여 장치 펌웨어를 업데이트하는 방법(미리 보기)

[AZURE.INCLUDE [iot-hub-device-management-job-selector](../../includes/iot-hub-device-management-jobs-selector.md)]

## 소개
Azure IoT 장치 관리를 통해 장치 작업을 사용하여 물리적 장치와 상호 작용할 수 있습니다. 장치 쌍(물리적 장치의 서비스 표시)을 식별한 후 장치 작업을 사용하여 해당 물리적 장치와 상호 작용할 수 있습니다. 장치 작업을 사용하여 여러 장치의 복잡한 프로세스를 조정할 수 있습니다. 이 프로세스는 여러 단계 및 긴 실행 작업을 포함할 수 있습니다.

현재 Azure IoT Hub 장치 관리를 통해 제공되는 장치 작업 유형은 여섯 가지입니다.(고객이 필요로 하는 작업이 있다면 추가할 예정입니다.)

- **펌웨어 업데이트**: 물리적 장치의 펌웨어(또는 OS 이미지)를 업데이트합니다.
- **다시 부팅**: 물리적 장치를 다시 부팅합니다.
- **공장 재설정**: 물리적 장치의 펌웨어(또는 OS 이미지)를 장치에 저장되어 있는 공장에서 제공한 백업 이미지로 되돌립니다.
- **구성 업데이트**: 물리적 장치에서 실행되는 IoT Hub 클라이언트 에이전트를 구성합니다.
- **장치 속성 읽기**: 물리적 장치에서 장치 속성에 대한 최신 값을 가져옵니다.
- **장치 속성 쓰기:** 물리적 장치의 장치 속성을 변경합니다.

이러한 각 작업의 사용법에 대한 자세한 내용은 [API 설명서][lnk-apidocs]를 참조하세요.

시작한 작업의 상태를 이해하기 위해 작업 내역을 쿼리할 수 있습니다. 몇 가지 예제 쿼리를 보려면 [쿼리 식 라이브러리][lnk-query-samples]를 참조하세요.

## 장치 작업을 사용하여 펌웨어 업데이트: 아키텍처

아래 다이어그램은 단일 물리적 장치와 상호 작용하는 펌웨어 업데이트 장치 작업을 보여 줍니다.

![][img-architecture]

펌웨어 업데이트 장치 작업을 위한 단계:

1.  클라우드 기반 IoT 솔루션은 펌웨어 업데이트 장치 작업을 시작하고 펌웨어 패키지의 위치에 대한 URI를 제공합니다. 펌웨어 패키지를 장치가 다운로드할 수 있는 위치에 배치하는 작업은 IoT 솔루션이 담당합니다.

2.  물리적 장치는 이 URI를 수신할 때 제공된 URI에서 다운로드를 자동으로 시작합니다.

3.  장치에 대한 코드는 IoT Hub에서 요청을 수신하고 제공된 위치 URI에서 펌웨어 패키지를 다운로드합니다.

4.  다운로드 완료 장치 상태 메시지를 수신한 후, 장치 작업은 장치에 대해 다운로드한 펌웨어 이미지를 적용하도록 지시합니다.

5.  장치가 펌웨어 이미지를 적용한 후 다시 부팅하고 IoT Hub에 다시 연결하고 새 펌웨어가 적용되었음을 IoT Hub에 알린 다음 장치 작업이 완료됩니다.

## 펌웨어 업데이트 샘플 실행

다음 샘플은 [Azure IoT Hub 장치 관리 시작][lnk-get-started] 자습서 기능을 확장합니다. 먼저 서로 다른 시뮬레이션된 장치를 실행하고 모든 장치에 대해 펌웨어를 업데이트하는 작업을 시작합니다.

### 필수 조건 

이 샘플을 실행하기 전에 [Azure IoT Hub 장치 관리 시작][lnk-get-started]의 단계를 완료했어야 합니다. 즉, 시뮬레이션된 장치가 실행 중이어야 합니다. 이전에 프로세스를 완료한 경우 시뮬레이션된 장치를 지금 다시 시작하세요.

### 샘플 시작

샘플을 시작하려면 **FirmwareUpdate.exe** 프로세스를 실행해야 합니다. 그러면 모든 시뮬레이션된 장치에 대해 펌웨어 업데이트 프로세스가 시작됩니다. 아래 단계에 따라 샘플을 시작하세요.

1.  **azure-iot-sdks** 리포지토리를 복제한 루트 폴더에서 **azure-iot-sdks\\csharp\\service\\samples\\bin** 폴더로 이동합니다.

2.  `FirmwareUpdate.exe <IoT Hub Connection String>` 실행

시뮬레이션된 장치 6개에 대해 시뮬레이션된 펌웨어 업데이트를 추적하는 장치 작업 7개를 보여 주는 명령줄 창의 출력이 표시되어야 합니다.

### 장치 작업 시작

일반적으로 장치 작업은 **JobClient** 인스턴스의 다양한 **Schedule&lt;JobName&gt;Async** 메서드를 사용하여 시작됩니다. 펌웨어 업데이트 샘플에서는 **ScheduleFirmwareUpdateAsync** 메서드를 호출합니다. 여기서는 장치 ID 6개가 포함된 배열을 전달하므로 장치 작업 7개가 시작됩니다(업데이트 중인 각 장치마다 1개씩 및 다른 6개를 추적하기 위해 사용되는 상위 장치 작업 1개).

아래 코드 조각에서는 **FirmwareUpdate** 프로젝트의 **Program.cs**에서 펌웨어 업데이트 작업이 시작됩니다. 이 호출은 업데이트할 장치를 표시하는 **deviceId** 값의 문자열 배열을 매개 변수로 받습니다.

```
var jobResponse = await deviceJobClient.ScheduleFirmwareUpdateAsync(Guid.NewGuid().ToString(), deviceIds, packageURI, TimeSpan.FromMinutes(25));
```

### 작업 기록 쿼리

이 샘플에서는 장치 작업을 현재 실행 중인 장치 목록이 주기적으로 표시됩니다. 장치 작업이 완료되면 표시되는 목록에서 연결된 장치가 사라집니다. 아래 그림은 실행 중인 **FirmwareUpdate.exe**의 스크린샷입니다.

![][img-output1]

위의 목록은 **FirmwareUpdate** 프로젝트의 **Program.cs**에서 아래 코드 조각에 나타난 대로 모든 활성 작업을 쿼리하여 생성됩니다.

```
private static async Task OutputRunningJobs()
{
  JobClient deviceJobClient = JobClient.CreateFromConnectionString(connectionString);

  string json = JsonConvert.SerializeObject(
    new
    {
      filter = new
      {
        property = new
        {
          name = "Status",
          type = "default"
        },
        value = "Running",
        comparisonOperator = "eq",
        type = "comparison"
      }
    }
  );

  JobQueryResult result = await deviceJobClient.QueryJobHistoryJsonAsync(json);

  // List query result
  foreach (JobResponse job in result.Result)
  {
    Console.WriteLine("DeviceID: {0}\t\tJobId: {1}",
      job.DeviceId,
      job.JobId
      );
  }
}
```

상위 장치 작업이 완료되면 샘플이 모든 장치 작업의 목록을 출력합니다. 이 출력은 아래 그림과 같습니다. 상위 작업은 모든 연결된 하위 작업이 완료된 후에만 완료됩니다. 아래 스크린샷에서 상위 작업은 목록의 마지막 작업이며, 마지막 작업이기 때문에 **ParentJobId**가 **''**입니다. 또한 상위 작업에는 쿼리의 집계 결과를 나타내는 문자열로 설정된 **이유** 필드도 있습니다. 이 경우 장치 6개가 업데이트되었고 모든 업데이트가 성공했음을 보여 줍니다.

![][img-output2]

위의 목록은 모든 작업을 쿼리하고 시작 시간 기준으로 정렬하여 생성됩니다. 아래에 표시된 **JobResponse** 개체에 대한 속성을 사용하여 장치 작업 기록을 쿼리할 수 있습니다.

![][img-properties]

장치 작업 기록을 쿼리하는 추가 샘플은 [쿼리 식 라이브러리][lnk-query-samples]를 참조하세요.

### 장치 시뮬레이터 구현 세부 정보

이전 섹션에서는 서비스 관점에서 펌웨어 업데이트의 구현 세부 사항(장치 작업을 시작하고 해당 상태를 쿼리하는 방법)을 보여 주었습니다. 이제 장치 측면에서 해당 구현을 설명합니다.

Azure IoT Hub 장치 관리 클라이언트 라이브러리는 장치와 서비스 간의 통신을 처리하므로 장치별 논리의 구현만 남습니다. 이는 두 부분으로 구성됩니다.

- 장치별 펌웨어 업데이트 프로세스 구현: 이 구현 과정에서는 펌웨어 패키지를 다운로드하고 아래에 나열된 해당 콜백에서 업데이트를 적용하는 장치별 논리를 작성합니다. 시뮬레이션된 샘플에서 이는 [샘플][lnk-github-firmware]에서 구현됩니다.

  ```
  object_firmwareupdate *obj = get_firmwareupdate_object(0);
  obj->firmwareupdate_packageuri_write_callback = start_firmware_download;
  // platform specific code
  obj->firmwareupdate_update_execute_callback = start_firmware_update;
  //platform specific code
  ```

- 서비스에 펌웨어 업데이트 프로세스 알림: 이 구현 과정에서는 펌웨어 업데이트 상태 및 펌웨어 업데이트 결과 필드를 수정합니다. **set\_firmwareupdate\_state** 및 **set\_firmwareupdate\_updateresult** API를 호출하여 이 작업을 수행합니다. 시뮬레이션된 샘플에서 이는 [샘플][lnk-github-firmware]에서 구현됩니다.

## 다음 단계

Azure IoT Hub 장치 관리 기능에 대해 자세히 알아보려면 이 자습서를 차례로 실행해 볼 수 있습니다.

- [IoT 게이트웨이 뒤에서 관리되는 장치 사용][lnk-dm-gateway]
- [Azure IoT Hub 장치 관리 클라이언트 라이브러리 소개][lnk-library-c]
- Azure IoT Hub DM 클라이언트 라이브러리는 [Intel Edison 장치][lnk-edison]를 사용하여 종단 간 샘플을 제공합니다.

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

- [솔루션 디자인][lnk-design]
- [개발자 가이드][lnk-devguide]
- [Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]
- [Azure 포털을 사용하여 IoT Hub 관리][lnk-portal]

<!-- Images and links -->

[img-architecture]: media/iot-hub-device-management-device-jobs/image1.png
[img-output1]: media/iot-hub-device-management-device-jobs/image2.png
[img-output2]: media/iot-hub-device-management-device-jobs/image3.png
[img-properties]: media/iot-hub-device-management-device-jobs/image4.png

[lnk-apidocs]: iot-hub-sdks-summary.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-github-firmware]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/c/iotdm_client/samples/iotdm_simple_sample/iotdm_simple_sample.c
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md

[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->