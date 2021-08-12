---
title: 일반적인 Device Update for Azure IoT Hub 문제 해결 | Microsoft Docs
description: 이 문서에서는 Device Update for IoT Hub와 관련하여 발생할 수 있는 다양한 문제를 해결하는 데 도움이 되는 팁과 요령 목록을 제공합니다.
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: f5ea8cfe1df2ae89bb67675c9bf235d62dca4bf5
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110082075"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>Device Update for IoT Hub 문제 해결 가이드

이 문서에서는 Device Update 사용자가 보고한 몇 가지 일반적인 질문과 문제를 보여 줍니다. Device Update가 공개 미리 보기를 통해 진행됨에 따라 이 문제 해결 가이드는 새로운 질문과 솔루션으로 정기적으로 업데이트됩니다. 이 문제 해결 가이드에 없는 문제가 발생하면 [Microsoft 지원 문의](#contact) 섹션을 참조하여 상황을 문서화합니다.

## <a name="importing-updates"></a><a name="import"></a>업데이트 가져오기

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>Q: 내 IoT Hub 인스턴스에 Device Update 인스턴스를 연결하는 데 문제가 있습니다.
_[Device Update 리소스](./device-update-resources.md) 문서에 따라 IoT Hub 메시지 경로가 올바르게 구성되었는지 확인하세요._

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>Q: 역할 관련 오류가 발생합니다(Azure Portal 또는 403 API 오류의 오류 메시지).
_올바르게 구성된 액세스 권한이 없을 수 있습니다. [Device Update 액세스 제어](./device-update-control-access.md) 문서에 따라 액세스 권한을 올바르게 구성했는지 확인하세요._

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>Q: Device Update 서비스로 콘텐츠를 가져올 때 500 유형 오류가 발생합니다.
_500 범위의 오류 코드는 Device Update 서비스에 문제가 있음을 나타낼 수 있습니다. 5분 후에 다시 시도하세요. 동일한 오류가 계속되면 [Microsoft 지원 문의](#contact) 섹션의 지침에 따라 Microsoft에 지원 요청을 제출하세요._

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>Q: 콘텐츠를 가져올 때 오류 코드가 발생하여 구문 분석하려고 합니다.
_오류 코드 구문 분석에 대한 정보는 [Device Update 오류 코드](./device-update-error-codes.md) 문서를 참조하세요._

## <a name="device-failures"></a><a name="device-failure"></a>디바이스 오류

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>Q: 디바이스가 Device Update for IoT Hub에 연결되었는지 확인하려면 어떻게 해야 하나요?
_Azure Portal의 규정 준수 보기에서 "그룹화되지 않음" 디바이스 섹션 아래에 표시되는지 확인하여 디바이스가 Device Update에 연결되었는지 확인할 수 있습니다._

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>Q: 하나 이상의 디바이스를 업데이트하지 못했습니다.
_Device Update 실패의 근본 원인은 여러 가지가 있습니다. 디바이스가 1) IoT Hub 인스턴스에 연결되었는지, 2) Device Update 인스턴스에 연결되었는지, 3) DO(전송 최적화) 서비스가 실행 중인지 확인합니다. 세 가지 모두 디바이스에 해당하는 경우 [Microsoft 지원 문의](#contact) 섹션의 지침에 따라 Microsoft에 지원 요청을 제출하세요._

## <a name="deploying-an-update"></a><a name="deploy"></a> 업데이트 배포

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>Q: 내 디바이스에 업데이트를 배포했지만 준수 상태가 최신 업데이트가 아니라고 표시됩니다.   어떻게 해야 합니까?
_디바이스 규정 준수 상태가 새로 고침되는 데 최대 5분이 걸릴 수 있습니다. 잠시 후 다시 확인하세요._
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>Q: 디바이스의 배포 상태가 호환되지 않는 것으로 표시되면 어떻게 해야 하나요?
_디바이스를 IoT Hub에 연결한 후 대상 디바이스의 제조업체 및 모델 속성이 변경되어 디바이스가 현재 배포의 업데이트 콘텐츠와 호환되지 않는 것으로 간주될 수 있습니다._

_[ADU 핵심 인터페이스](./device-update-plug-and-play.md)를 확인하여 디바이스가 Device Update 서비스에 보고하는 제조업체 및 모델을 확인하고 배포 중인 업데이트 콘텐츠의 [가져오기 매니페스트](./import-concepts.md)에서 지정한 제조업체 및 모델과 일치하는지 확인합니다. [Device Update 구성 파일](./device-update-configuration-file.md)을 사용하여 지정된 디바이스의 이러한 속성을 변경할 수 있습니다._

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>Q: 배포가 "활성" 단계에 있지만 업데이트를 통해 "진행 중"인 디바이스가 없습니다.   어떻게 해야 합니까?
_배포 시작 날짜가 미래로 설정되지 않았는지 확인합니다. 새 배포를 생성할 때 명시적으로 변경하지 않는 한 배포 시작 날짜는 보호 수단으로 다음 날로 기본 설정됩니다. 배포 시작 날짜가 될 때까지 기다리거나 진행 중인 배포를 취소하고 원하는 시작 날짜로 새 배포를 만들 수 있습니다._

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>Q: 내 디바이스를 그룹화하려고 하지만 그룹을 만들 때 드롭다운에 태그가 표시되지 않습니다.
_[Device Update 리소스](./device-update-resources.md) 문서에 따라 IoT Hub에서 메시지 경로를 올바르게 구성했는지 확인합니다. 경로를 구성한 후 디바이스에 다시 태그를 지정해야 합니다._

_또 다른 근본 원인은 디바이스를 Device Update for IoT Hub에 연결하기 전에 태그를 적용했기 때문일 수 있습니다. 디바이스가 이미 Device Update에 연결되어 있는지 확인합니다. 준수 보기의 그룹화되지 않은 디바이스 아래에 표시되는지 확인하여 디바이스가 Device Update for IoT Hub에 연결되어 있는지 확인할 수 있습니다. 일시적으로 다른 값의 태그를 추가한 다음 디바이스가 연결되면 원하는 태그를 다시 추가합니다._

_DPS(Device Provisioning Service)를 사용하는 경우 디바이스 생성 프로세스 중이 아니라 프로비저닝된 후 디바이스에 태그를 지정해야 합니다. 디바이스 생성 단계에서 이미 디바이스에 태그를 지정한 경우 프로비저닝 후 일시적으로 다른 값으로 디바이스에 태그를 지정한 다음 의도한 태그를 다시 추가해야 합니다._

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>Q: 내 배포가 성공적으로 완료되었지만 일부 디바이스를 업데이트하지 못했습니다.
_이는 실패한 디바이스의 클라이언트 측 오류로 인해 발생했을 수 있습니다. 이 문제 해결 가이드의 디바이스 오류 섹션을 참조하세요._

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>Q: 배포를 시작하려고 할 때 UX에 오류가 발생했습니다.
_service/UX 버그 또는 API 권한 문제로 인해 발생했을 수 있습니다. [Microsoft 지원 문의](#contact) 섹션의 지침에 따라 Microsoft에 지원 요청을 제출하세요._

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>Q: 배포를 시작했지만 최종 상태에 도달하지 못했습니다.
_서비스 성능 문제, 서비스 버그 또는 클라이언트 버그로 인해 발생할 수 있습니다. 10분 후에 배포를 다시 시도합니다. 동일한 문제가 발생하면 디바이스 로그를 가져와 이 문제 해결 가이드의 디바이스 오류 섹션을 참조하세요. 동일한 문제가 계속되면 [Microsoft 지원 문의](#contact) 섹션의 지침에 따라 Microsoft에 지원 요청을 제출하세요._

### <a name="q-i-migrated-from-a-device-level-agent-to-adding-the-agent-as-a-module-identity-on-the-device-and-my-update-shows-as-in-progress-even-though-it-has-been-applied-to-the-device"></a>Q: 디바이스 수준 에이전트에서 에이전트를 디바이스의 모듈 ID로 추가하기 위해 마이그레이션했는데 업데이트가 디바이스에 적용되었음에도 '진행 중'으로 표시됩니다.
_이는 디바이스 쌍을 통해 통신하던 이전 에이전트를 제거하지 않은 경우 발생할 수 있습니다. Device Update 에이전트를 모듈로 프로비저닝할 때([방법](device-update-agent-provisioning.md) 참조) 디바이스와 Device Update 서비스 간의 모든 통신은 모듈 쌍을 통해 이루어 지므로 [그룹](device-update-groups.md)을 만들 때 디바이스의 모듈 쌍에 태그를 지정하고 모든 [통신](device-update-plug-and-play.md)은 모듈 쌍을 통해 발생해야 합니다.

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> 디바이스에 업데이트 다운로드

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>Q: 연결을 끊은 후 디바이스가 다시 연결되었을 때 다운로드를 계속하려면 어떻게 해야 하나요?
_24시간 내에 연결이 복원되면 다운로드가 자동으로 다시 시작됩니다. 24시간이 지나면 사용자가 다운로드를 다시 시작해야 합니다._
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> MCC(Microsoft 연결된 캐시) 사용

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>Q: IoT Edge 디바이스에 MCC 모듈을 배포하려고 하면 문제가 발생합니다.
_Edge 모듈을 IoT Edge 디바이스에 배포하려면 [IoT Edge 문서]()를 참조하세요. http://localhost:5100/Summary 로 이동하여 IoT Edge 디바이스에서 MCC 모듈이 성공적으로 실행되고 있는지 확인할 수 있습니다._
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>Q: 내 IoT 디바이스 중 하나가 MCC를 통해 업데이트를 다운로드하려고 하지만 실패합니다.
_IoT 디바이스가 MCC에 연결하지 못하는 원인이 될 수 있는 몇 가지 문제가 있습니다. 문제를 진단하려면 오류가 발생한 디바이스에서 DO 클라이언트 및 Nginx 로그를 수집하세요(클라이언트 로그 수집에 대한 지침은 [Microsoft 지원 문의](#contact) 섹션 참조)._

_사용하는 URL이 허용되지 않기 때문에 디바이스가 인터넷에서 콘텐츠를 가져와 MCC 모듈로 전달하지 못할 수 있습니다. 이러한 경우 Azure Portal에서 IoT Edge 환경 변수를 확인해야 합니다._
## <a name="contacting-microsoft-support"></a><a name="contact"></a> Microsoft 지원에 문의

위의 FAQ를 사용하여 해결할 수 없는 문제가 발생하는 경우 Azure Portal 인터페이스를 통해 Microsoft 지원에 지원 요청을 보낼 수 있습니다. 문제가 속한 범주에 따라 Microsoft 지원에서 문제를 조사하는 데 도움이 되도록 추가 데이터를 수집하고 공유하라는 요청을 받을 수 있습니다. 

각 데이터 형식을 수집하는 방법에 대한 지침은 아래를 참조하세요. [getDevices]()를 사용하여 API의 페이로드 응답에서 추가 정보를 확인할 수 있습니다.

또한 다음 정보는 문제의 근본 원인을 축소하는 데 유용할 수 있습니다.
* 업데이트하려는 디바이스 유형(Azure Percept, IoT Edge 게이트웨이, 기타)
* 사용 중인 Device Update 클라이언트 유형(이미지 기반, 패키지 기반, 시뮬레이터)
* 디바이스에서 실행 중인 OS
* 디바이스의 아키텍처와 관련된 세부 정보
* Device Update를 사용하여 이전에 디바이스를 업데이트했는지 여부

위의 정보를 사용할 수 있는 경우 문제에 대한 설명에 포함해 주세요.

### <a name="collecting-client-logs"></a>클라이언트 로그 수집

* Raspberry Pi 디바이스에는 다음 두 개의 로그 집합이 있습니다.

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* 패키지된 클라이언트의 경우 로그는 다음 위치에서 찾을 수 있습니다.

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* 시뮬레이터의 경우 로그는 다음 위치에서 찾을 수 있습니다.

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>오류 코드
업데이트 가져오기, 디바이스 오류 또는 업데이트 배포와 관련된 문제를 보고할 때 오류 코드를 제공하라는 메시지가 표시될 수 있습니다.

[ADUCoreInterface](./device-update-plug-and-play.md) 인터페이스를 살펴보면 오류 코드를 얻을 수 있습니다. 자가 진단 및 문제 해결을 위해 오류 코드를 구문 분석하는 방법에 대한 내용은 [Device Update 오류 코드](./device-update-error-codes.md) 문서를 참조하세요.

### <a name="trace-id"></a>추적 ID
업데이트 가져오기 또는 배포와 관련된 문제를 보고할 때 추적 ID를 제공하라는 메시지가 표시될 수 있습니다.

지정된 사용자 동작에 대한 추적 ID는 API 응답 또는 Azure Portal 사용자 인터페이스의 가져오기 기록 섹션에서 찾을 수 있습니다. 

현재 배포 작업에 대한 추적 ID는 API 응답을 통해서만 액세스할 수 있습니다.

### <a name="deployment-id"></a>배포 ID
업데이트 배포와 관련된 문제를 보고할 때 배포 ID를 제공하라는 메시지가 표시될 수 있습니다.

배포 ID는 배포를 시작하기 위해 API를 호출할 때 사용자가 만듭니다.

현재 Azure Portal 사용자 인터페이스에서 시작된 배포에 대한 배포 ID가 자동으로 생성되고 사용자에게 표시되지 않습니다.

### <a name="iot-hub-instance-name"></a>IoT Hub 인스턴스 이름
디바이스 실패와 관련된 문제를 보고하거나 업데이트를 배포하는 경우 IoT Hub 인스턴스의 이름을 입력하라는 메시지가 표시될 수 있습니다.

IoT Hub 이름은 처음 프로비저닝될 때 사용자가 선택합니다.

### <a name="device-update-account-name"></a>Device Update 계정 이름
업데이트 가져오기, 디바이스 오류 또는 업데이트 배포와 관련된 문제를 보고할 때 Device Update 계정의 이름을 입력하라는 메시지가 표시될 수 있습니다.

Device Update 계정 이름은 사용자가 서비스에 처음 등록할 때 선택됩니다. 자세한 내용은 [Device Update 리소스](./device-update-resources.md) 문서에서 찾을 수 있습니다.

### <a name="device-update-instance-name"></a>Device Update 인스턴스 이름
업데이트 가져오기, 디바이스 오류 또는 업데이트 배포와 관련된 문제를 보고할 때 Device Update 인스턴스의 이름을 제공하라는 메시지가 표시될 수 있습니다.

Device Update 인스턴스 이름은 처음 프로비저닝될 때 사용자가 선택합니다. 자세한 내용은 [Device Update 리소스](./device-update-resources.md) 문서에서 찾을 수 있습니다.

### <a name="device-id"></a>디바이스 ID
디바이스 오류와 관련된 문제를 보고하거나 업데이트를 배포하는 경우 디바이스 ID를 제공하라는 메시지가 표시될 수 있습니다.

디바이스 ID는 디바이스를 처음 프로비저닝할 때 고객에 의해 정의됩니다. 디바이스의 디바이스 쌍에서 검색할 수도 있습니다.

### <a name="update-id"></a>업데이트 ID
업데이트 배포와 관련된 문제를 보고할 때 업데이트 ID를 제공하라는 메시지가 표시될 수 있습니다.

업데이트 ID는 배포를 시작할 때 고객에 의해 정의됩니다.

### <a name="nginx-logs"></a>Nginx 로그
Microsoft 연결된 캐시와 관련된 문제를 보고할 때 Nginx 로그를 제공하라는 메시지가 표시될 수 있습니다.

### <a name="adu-conftxt"></a>ADU-conf.txt
업데이트 배포와 관련된 문제를 보고할 때 Device Update 구성 파일("adu-conf.txt")을 제공하라는 요청을 받을 수 있습니다.

구성 파일은 선택 사항이며 [Device Update 구성](./device-update-configuration-file.md) 설명서의 지침에 따라 사용자가 만듭니다.

### <a name="import-manifest"></a>매니페스트 가져오기
업데이트 가져오기 또는 배포와 관련된 문제를 보고할 때 가져오기 매니페스트 파일을 제공하라는 메시지가 표시될 수 있습니다.

가져오기 매니페스트는 Device Update 서비스에 업데이트 콘텐츠를 가져올 때 고객이 만든 파일입니다.

**[다음 단계: Device Update 오류 코드에 대해 자세히 알아보기](.\device-update-error-codes.md)**
