다음 표에서는 IoT Hub Device Provisioning Service 리소스에 적용되는 제한을 나열합니다.

| 리소스 | 제한 |
| --- | --- |
| Azure 구독당 최대 Device Provisioning Services | 10 |
| 최대 등록(enrollment) 수 | 500,000 |
| 최대 등록 수 | 500,000 |
| 최대 등록(enrollment) 그룹 수 | 100 |
| 최대 CA 수 | 25 |

> [!NOTE]
> [Microsoft 지원](https://azure.microsoft.com/support/options/)에 문의하여 구독에 있는 인스턴스 수를 늘릴 수 있습니다.

> [!NOTE]
> [Microsoft 지원](https://azure.microsoft.com/support/options/)에 문의하여 구독의 등록 수를 늘릴 수 있습니다.

다음 할당량이 초과되면 Device Provisioning Service가 요청을 제한합니다.

| 제한 | 단위당 값 |
| --- | --- |
| 작업 | 200/분/서비스 |
| 장치 등록 | 200/분/서비스 |
| 장치 폴링 작업 | 5/10초/장치 |
