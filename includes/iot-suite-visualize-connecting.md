## 대시보드에서 장치 원격 분석 보기

장치가 IoT Hub에 보내는 원격 분석 데이터를 원격 모니터링 솔루션의 대시보드에서 볼 수 있습니다.

1. 브라우저에서 원격 모니터링 솔루션 대시보드로 돌아가서 왼쪽 패널의 **장치**를 클릭하여 **장치 목록**으로 이동합니다.

2. **장치 목록**에 장치의 상태가 **실행 중**으로 표시되어야 합니다.

    ![][18]

3. 대시보드의 **볼 장치** 드롭다운에서 장치를 선택하여 원격 분석을 봅니다. 샘플 응용 프로그램의 원격 분석은 내부 온도가 50 단위이고 외부 온도가 55 단위이고 습도가 50 단위입니다. 대시보드에는 기본적으로 온도와 습도 값만 표시됩니다.

    ![][img-telemetry]

## 장치에 명령 보내기

원격 모니터링 솔루션의 대시보드에서는 장치에 명령을 보내도록 IoT Hub에 요청할 수 있습니다. 예를 들어, 원격 모니터링 솔루션에서 장치의 내부 온도를 설정하도록 명령을 보낼 수 있습니다.

1. 원격 모니터링 솔루션 대시보드에서 왼쪽의 **장치**를 클릭하여 **장치 목록**으로 이동합니다.

2. **장치 목록**에서 사용자 장치의 **장치 ID**를 클릭합니다.

3. **장치 세부 정보** 패널에서 **명령**을 클릭합니다.

    ![][13]

4. **명령** 드롭다운에서 **SetTemperature**를 선택한 후 **온도**에 온도 값을 입력합니다. **명령 전송**을 클릭하여 장치에 명령을 보냅니다.

    ![][14]

    > [AZURE.NOTE]처음에는 명령 기록에 명령 상태가 **보류 중**으로 표시됩니다. 장치에서 명령을 인지하면 상태가 **성공**으로 변경됩니다.

5. 대시보드에서 장치가 새 온도 값으로 75를 보내고 있는지 확인합니다.

## 다음 단계

[미리 구성된 솔루션 사용자 지정][lnk-customize] 문서에는 이 샘플을 확대하는 방법이 설명되어 있습니다. 가능한 확장에는 실제 센서 사용 및 추가적인 명령 구현이 포함됩니다.

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-dev-messaging]: ../articles/iot-hub/iot-hub-devguide.md#messaging

<!---HONumber=AcomDC_1125_2015-->