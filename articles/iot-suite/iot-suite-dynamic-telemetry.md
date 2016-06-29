<properties
	pageTitle="동적 원격 분석 사용 | Microsoft Azure"
	description="이 자습서를 따라 원격 모니터링 사전 구성 솔루션으로 동적 원격 분석을 사용하는 방법을 익히세요."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/07/2016"
     ms.author="dobett"/>

# 원격 모니터링 사전 구성 솔루션으로 동적 원격 분석 사용

## 소개

동적 원격 분석을 사용하면 원격 모니터링 사전 구성 솔루션에 전송된 모든 원격 분석을 시각화할 수 있습니다. 사전 구성된 솔루션으로 배포한 시뮬레이션 장치는 온도와 습도 원격 분석을 전송합니다. 이 정보는 대시보드에서 시각화할 수 있습니다. 기존 시뮬레이션 장치를 사용자 지정하거나 새로운 시뮬레이션 장치를 만들거나 물리적 장치를 사전 구성된 솔루션에 연결할 경우, 외부 온도, RPM, 풍속 등의 다른 원격 분석 값을 전송할 수 있습니다. 대시보드에서 이런 추가 원격 분석을 시각화할 수 있습니다.

이 자습서는 간단한 Node.js 시뮬레이션 장치를 사용하므로 쉽게 수정하여 동적 원격 분석으로 실험할 수 있습니다.

이 자습서를 완료하려면 다음이 필요합니다.

- 활성 Azure 구독. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험][lnk_free_trial]을 참조하세요.
- [Node.js][lnk-node] 버전 0.12.x 이상

Windows나 Linux 등의 Node.js가 설치된 모든 운영 체제에서 이 자습서를 완료할 수 있습니다.

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## Node.js 시뮬레이션 장치 구성

1. 원격 모니터링 대시보드에서 **+ 장치 추가**를 클릭하고 새로운 사용자 지정 장치를 추가합니다. IoT Hub 호스트 이름, 장치 ID, 장치 키를 메모합니다. 이 자습서에서 나중에 remote\_monitoring.js 장치 클라이언트 응용 프로그램을 준비할 때 필요합니다.

2. Node.js 버전 0.12.x 이상이 개발 컴퓨터에 설치되었는지 확인합니다. 명령 프롬프트 또는 쉘에서 `node --version`을(를) 실행하고 버전을 확인합니다. Linux에서 Node.js를 설치하기 위한 패키지 관리자 사용에 관한 정보는 [패키지 관리자를 통해 Node.js 설치][node-linux]를 참조하세요.

3. Node.js를 설치했으면 [azure-iot-sdks][lnk-github-repo] 리포지토리의 최신 버전을 개발 컴퓨터에 복제합니다. 언제나 최신 라이브러리와 샘플 버전의 **마스터** 분기를 사용해야 합니다.

4. [azure-iot-sdks][lnk-github-repo] 리포지토리의 로컬 사본의 node/device/samples 폴더에서 다음 두 개 파일을 개발 컴퓨터의 빈 폴더에 복사합니다.

  - packages.json
  - remote\_monitoring.js

5. remote\_monitoring.js 파일을 열고 다음 변수 정의를 찾습니다.

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

6. **[IoT Hub device connection string]**을 장치 연결 문자열로 바꿉니다. 1단계에서 메모한 IoT Hub 호스트 이름, 장치 ID, 장치 키 값을 사용합니다. 장치 연결 문자열은 다음과 같은 형식입니다.

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    IoT Hub 호스트 이름이 **contoso**이고 장치 ID가 **mydevice**이면, 연결 문자열은 다음과 같습니다.

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

7. 파일을 저장합니다. 이러한 파일이 포함된 폴더에서 쉘 또는 명령 프롬프트에 다음 명령을 입력하고 필요한 패키지를 설치하고 샘플 응용 프로그램을 실행합니다.

    ```
    npm install
    node remote_monitoring.js
    ```

## 작업 중인 동적 원격 분석 관찰

대시보드는 기존 시뮬레이션 장치의 온도와 습도 원격 분석을 보여줍니다.

![기본 대시보드][image1]

이전 섹션에서 실행한 Node.js 시뮬레이션 장치를 선택했다면 온도, 습도, 외부 온도 원격 분석이 보일 것입니다.

![대시보드에 외부 온도 추가][image2]

원격 모니터링 솔루션은 추가 외부 온도 원격 분석 형식을 자동으로 인식하고 대시보드 차트에 추가합니다.

## 새로운 원격 분석 형식 추가

다음 단계는 Node.js 시뮬레이션 장치가 생성한 원격 분석을 새로운 값 집합으로 바꾸는 것입니다.

1. 명령 프롬프트 또는 쉘에 **Ctrl+C**를 입력하여 Node.js 시뮬레이션 장치를 중단합니다.

2. remote\_monitoring.js 파일에 기존 온도, 습도, 외부 온도 원격 분석의 기본 데이터 값이 표시됩니다. 다음과 같이 **rpm**의 새 기본 데이터 값을 추가합니다.

    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Node.js 시뮬레이션 장치는 remote\_monitoring.js 파일의 **generateRandomIncrement** 함수를 사용하여 기본 데이터 값에 무작위 증분을 추가함으로써 원격 분석을 생성합니다. 기존 불규칙화 뒤에 코드 한 줄을 추가하여 **rpm** 값을 불규칙화합니다.

    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. 장치가 IoT Hub에 전송하는 JSON 페이로드에 새 rpm 값을 추가합니다.

    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. 다음 명령을 사용하여 Node.js 시뮬레이션 장치를 실행합니다.

    ```
    node remote_monitoring.js
    ```

6. 대시보드 차트에 표시되는 새 RPM 원격 분석 형식을 관찰합니다.

![대시보드에 RPM 추가][image3]

> [AZURE.NOTE] 변경을 즉시 확인하려면 대시보드의 **장치** 페이지에서 Node.js 장치를 비활성화한 다음 활성화합니다.

## 대시보드 디스플레이 사용자 지정

**Device-Info** 메시지에는 장치가 IoT Hub에 전송하는 원격 분석 관련 메타데이터가 포함될 수 있습니다. 이 메타데이터는 장치가 전송하는 원격 분석 형식을 지정할 수 있습니다. remote\_monitoring.js 파일에서 **deviceMetaData** 값을 수정하고, 다음 코드 조각에 표시된 **Commands** 정의를 따르는 **Telemetry** 정의를 포함합니다(**Commands** 정의 뒤에 `,`를 추가합니다).

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [AZURE.NOTE] 원격 모니터링 솔루션은 대/소문자 구분을 하지 않는 일치를 사용하여 메타데이터 정의와 원격 분석 스트림의 데이터를 비교합니다.

위와 같이 샘플에 **Telemetry** 정의를 추가하더라도 대시보드의 동작이 변경되지 않습니다. 그러나 메타데이터는 **DisplayName** 특성을 포함하여 대시보드 디스플레이를 사용자 지정할 수 있습니다. 다음과 같이 **Telemetry** 메타데이터를 업데이트합니다.

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

다음 스크린샷은 이러한 변경이 대시보드의 차트 범례를 어떻게 수정하는지 보여줍니다.

![차트 범례 사용자 지정][image4]

> [AZURE.NOTE] 변경을 즉시 확인하려면 대시보드의 **장치** 페이지에서 Node.js 장치를 비활성화한 다음 활성화합니다.

## 원격 분석 형식 필터링

기본적으로 대시보드 차트는 원격 분석 스트림의 모든 데이터 계열을 표시합니다. **Device-Info** 메타데이터를 사용하여 차트에서 특정 원격 분석 형식이 표시되지 않게 할 수 있습니다.

차트가 온도와 습도 원격 분석만 표시하게 하려면 다음과 같이 **Device-Info** **Telemetry** 메타데이터에서 **ExternalTemperature**를 제거합니다.

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

이제 차트에서 **실외 온도**가 표시되지 않습니다.

![대시보드에서 원격 분석 필터링][image5]

참고로 이 작업은 차트 디스플레이에만 영향을 미치고 **ExternalTemperature** 데이터 값은 여전히 저장되어 있으며 백엔드 프로세싱에서 사용할 수 있습니다.

> [AZURE.NOTE] 변경을 즉시 확인하려면 대시보드의 **장치** 페이지에서 Node.js 장치를 비활성화한 다음 활성화합니다.

## 오류 처리

데이터 스트림을 차트에 표시하려면 **Device-Info** 메타데이터의 **Type**이 원격 분석 값의 데이터 형식과 일치해야 합니다. 예를 들어, 메타데이터가 습도 데이터의 **형식**을 **정수**라고 정의했는데 원격 분석 스트림에서 **실수**가 발견된다면, 습도 원격 분석이 차트에 표시되지 않습니다. 그러나 **습도** 값은 여전히 저장되어 있고 백엔드 프로세싱에서 사용할 수 있습니다.

## 다음 단계

이제 미리 구성된 작업 솔루션을 구축했으므로 다음 연습으로 이동할 수 있습니다.

-   [미리 구성된 솔루션 사용자 지정에 대한 지침][lnk-customize]
-   [예측 정비 사전 구성 솔루션 개요][lnk-predictive]

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks

<!---HONumber=AcomDC_0615_2016-->