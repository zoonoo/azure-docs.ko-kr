# 개요
## [Azure 및 IoT](iot-hub-what-is-azure-iot.md)
## [Azure IoT Hub란?](iot-hub-what-is-iot-hub.md)
## [장치 관리 개요](iot-hub-device-management-overview.md)

# [시작](iot-hub-get-started.md)

## 장치 설정
### 시뮬레이션된 장치 사용
#### [.NET](iot-hub-csharp-csharp-getstarted.md)
#### [Java](iot-hub-java-java-getstarted.md)
#### [Node.JS](iot-hub-node-node-getstarted.md)
#### [Python](iot-hub-python-getstarted.md)

### 시뮬레이션된 게이트웨이 사용 
#### [Linux에서 시뮬레이션](iot-hub-linux-gateway-sdk-get-started.md)
#### [Windows에서 시뮬레이션](iot-hub-windows-gateway-sdk-get-started.md)

### 실제 장치 사용
#### [Node.js를 사용하는 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)
#### [C를 사용하는 Raspberry Pi](iot-hub-raspberry-pi-kit-c-get-started.md)

#### [Node.js를 사용하는 Intel Edison](iot-hub-intel-edison-kit-node-get-started.md)
#### [C를 사용하는 Intel Edison](iot-hub-intel-edison-kit-c-get-started.md)

#### [Arduino IDE를 사용하는 Adafruit Feather HUZZAH ESP8266](iot-hub-arduino-huzzah-esp8266-get-started.md)
#### [Arduino IDE를 사용하는 Sparkfun ESP8266 Thing Dev](iot-hub-sparkfun-esp8266-thing-dev-get-started.md)
#### [Arduino IDE를 사용하는 Adafruit Feather M0](iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md)

#### IoT Gateway Starter Kit 사용
##### [Intel NUC를 IoT 게이트웨이로 설정](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
##### [IoT 게이트웨이를 IoT Hub에 연결](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
##### [데이터 변환에 IoT 게이트웨이 사용](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)

## [iothub-explorer를 사용하여 클라우드 장치 메시지 관리](iot-hub-explorer-cloud-device-messaging.md)
## [IoT Hub 메시지를 Azure 데이터 저장소에 저장](iot-hub-store-data-in-azure-table-storage.md)
## [Power BI에서 데이터 시각화](iot-hub-live-data-visualization-in-power-bi.md)
## [Web Apps를 사용하여 데이터 시각화](iot-hub-live-data-visualization-in-web-apps.md)
## [Azure Machine Learning을 사용한 날씨 예측](iot-hub-weather-forecast-machine-learning.md)
## [iothub-explorer를 사용하여 장치 관리](iot-hub-device-management-iothub-explorer.md)
## [Logic Apps를 사용하여 원격 모니터링 및 알림](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

# 방법
## 계획
### [IoT Hub를 Event Hubs에 비교](iot-hub-compare-event-hubs.md)
### [솔루션 확장](iot-hub-scaling.md)
### [고가용성 및 재해 복구](iot-hub-ha-dr.md)
### [추가 프로토콜 지원](iot-hub-protocol-gateway.md)
## [개발](iot-hub-how-to.md)
### [개발자 가이드](iot-hub-devguide.md)
#### [메시지 보내기 및 받기](iot-hub-devguide-messaging.md)
#### [장치-클라우드 기능 가이드](iot-hub-devguide-d2c-guidance.md)
#### [클라우드-장치 기능 가이드](iot-hub-devguide-c2d-guidance.md)
#### [장치에서 파일 업로드](iot-hub-devguide-file-upload.md)
#### [장치 ID 관리](iot-hub-devguide-identity-registry.md)
#### [IoT Hub에 대한 액세스 제어](iot-hub-devguide-security.md)
#### [쌍 장치 이해](iot-hub-devguide-device-twins.md)
#### [장치에서 직접 메서드 호출](iot-hub-devguide-direct-methods.md)
#### [여러 장치에서 작업 예약](iot-hub-devguide-jobs.md)
#### [IoT Hub 끝점](iot-hub-devguide-endpoints.md)
#### [쿼리 언어](iot-hub-devguide-query-language.md)
#### [할당량 및 제한](iot-hub-devguide-quotas-throttling.md)
#### [가격 책정 예](iot-hub-devguide-pricing.md)
#### [장치 및 서비스 SDK](iot-hub-devguide-sdks.md)
#### [MQTT 지원](iot-hub-mqtt-support.md)
#### [용어](iot-hub-devguide-glossary.md)
### [C용 IoT 장치 SDK 사용](iot-hub-device-sdk-c-intro.md)
#### [IoTHubClient 사용](iot-hub-device-sdk-c-iothubclient.md)
#### [직렬 변환기 사용](iot-hub-device-sdk-c-serializer.md)
### 장치-클라우드 메시지 처리
#### [.NET](iot-hub-csharp-csharp-process-d2c.md)
#### [Java](iot-hub-java-java-process-d2c.md)
### 클라우드-장치 메시지 보내기
#### [.NET](iot-hub-csharp-csharp-c2d.md)
#### [Java](iot-hub-java-java-c2d.md)
#### [Node.JS](iot-hub-node-node-c2d.md)
### [장치에서 파일 업로드](iot-hub-csharp-csharp-file-upload.md)
### 쌍 장치 시작
#### [Node.js 백 엔드/Node.js 장치](iot-hub-node-node-twin-getstarted.md)
#### [.NET 백 엔드/Node.js 장치](iot-hub-csharp-node-twin-getstarted.md)
### 직접 메서드 사용
#### [Node.js 백 엔드/Node.js 장치](iot-hub-node-node-direct-methods.md)
#### [.NET 백 엔드/Node.js 장치](iot-hub-csharp-node-direct-methods.md)
#### [Java 백 엔드/Java 장치](iot-hub-java-java-direct-methods.md)
### 장치 관리 시작
#### [Node.js 백 엔드/Node.js 장치](iot-hub-node-node-device-management-get-started.md)
#### [.NET 백 엔드/Node.js 장치](iot-hub-csharp-node-device-management-get-started.md)
### 쌍 속성 사용 방법
#### [Node.js 백 엔드/Node.js 장치](iot-hub-node-node-twin-how-to-configure.md)
#### [.NET 백 엔드/Node.js 장치](iot-hub-csharp-node-twin-how-to-configure.md)
### 장치 작업을 사용하여 장치 펌웨어 업데이트
#### [노드 백 엔드/노드 장치](iot-hub-node-node-firmware-update.md)
#### [.NET 백 엔드/Node.js 장치](iot-hub-csharp-node-firmware-update.md)
### 작업 예약 및 브로드캐스트
#### [Node.js 백 엔드/Node.js 장치](iot-hub-node-node-schedule-jobs.md)
#### [.NET 백 엔드/Node.js 장치](iot-hub-csharp-node-schedule-jobs.md)
## 관리
### IoT Hub 만들기 
#### [포털 사용](iot-hub-create-through-portal.md)
#### [PowerShell 사용](iot-hub-create-using-powershell.md)
#### [CLI 2.0 사용](iot-hub-create-using-cli.md)
#### [CLI 사용](iot-hub-create-using-cli-nodejs.md)
#### [REST API 사용](iot-hub-rm-rest.md)
#### [PowerShell의 템플릿 사용](iot-hub-rm-template-powershell.md)
#### [.NET의 템플릿 사용](iot-hub-rm-template.md)
### 파일 업로드 구성
#### [포털 사용](iot-hub-configure-file-upload.md)
#### [PowerShell 사용](iot-hub-configure-file-upload-powershell.md)
### [IoT 장치 대량 관리](iot-hub-bulk-identity-mgmt.md)
### [사용 현황 메트릭](iot-hub-metrics.md)
### [작업 모니터링](iot-hub-operations-monitoring.md)
### [IP 필터링 구성](iot-hub-ip-filtering.md)
## 보안
### [처음부터 보안을 고려](iot-hub-security-ground-up.md)
### [보안 모범 사례](iot-hub-security-best-practices.md)
### [보안 아키텍처](iot-hub-security-architecture.md)
### [IoT 배포 보안 유지](iot-hub-security-deployment.md)
## Gateway SDK
### 장치 시뮬레이션
#### [Linux](iot-hub-linux-gateway-sdk-simulated-device.md)
#### [Windows](iot-hub-windows-gateway-sdk-simulated-device.md)
### [실제 장치 사용](iot-hub-gateway-sdk-physical-device.md)

# 참조
## [Azure CLI 2.0 미리 보기](/cli/azure/iot)
## [.NET(서비스)](/dotnet/api/microsoft.azure.devices)
## [.NET(장치)](/dotnet/api/microsoft.azure.devices.client)
## [.NET(서비스)](/java/api/com.microsoft.azure.sdk.iot.service)
## [Java(장치)](/java/api/com.microsoft.azure.sdk.iot.device)
## [Azure IoT SDK](http://azure.github.io/azure-iot-sdks/)
## [Gateway SDK](http://azure.github.io/azure-iot-gateway-sdk)
## [REST(리소스 공급자)](https://docs.microsoft.com/rest/api/iothub/iothubresource)
## [REST(장치 ID)](https://docs.microsoft.com/rest/api/iothub/deviceapi)
## [REST(장치 쌍)](https://docs.microsoft.com/rest/api/iothub/devicetwinapi)
## [REST(장치 메시징)](https://docs.microsoft.com/rest/api/iothub/httpruntime)
## [REST(작업)](https://docs.microsoft.com/rest/api/iothub/jobapi)

# 관련 항목
## [Azure IoT Suite](https://azure.microsoft.com/documentation/suites/iot-suite/)
## [Azure 이벤트 허브](https://azure.microsoft.com/documentation/services/event-hubs/)
## [Stream Analytics](https://azure.microsoft.com/documentation/services/stream-analytics/)
## [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)

# 리소스
## [iothub-explorer 도구](https://github.com/Azure/iothub-explorer)
## [iothub-diagnostics 도구](https://github.com/Azure/iothub-diagnostics)
## [DeviceExplorer 도구](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)
## [가격 책정](https://azure.microsoft.com/pricing/details/iot-hub/)
## [MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azureiothub)
## [스택 오버플로](http://stackoverflow.com/questions/tagged/azure-iot-hub)
## [비디오](https://azure.microsoft.com/documentation/videos/index/?services=iot-hub)
## [서비스 업데이트](https://azure.microsoft.com/updates/?product=iot-hub)
## [학습 경로](https://azure.microsoft.com/documentation/learning-paths/iot-hub/)
## [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot/)
## [IoT용 Azure Certified 장치 카탈로그](https://catalog.azureiotsuite.com/)
