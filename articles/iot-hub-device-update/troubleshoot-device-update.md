---
title: Azure IoT Hub 문제에 대 한 일반적인 장치 업데이트 문제 해결 | Microsoft Docs
description: 이 문서에서는 IoT Hub에 대 한 장치 업데이트와 관련 하 여 발생할 수 있는 다양 한 문제를 해결 하는 데 도움이 되는 팁과 요령 목록을 제공 합니다.
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: 3c1f60b214397b1f97e0157b5beca32d504102d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030633"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>IoT Hub 문제 해결 가이드에 대 한 장치 업데이트

이 문서에서는 사용자가 보고 한 몇 가지 일반적인 질문과 문제를 보여 줍니다. 장치 업데이트가 공개 미리 보기를 통해 진행 됨에 따라이 문제 해결 가이드는 새로운 질문과 솔루션으로 정기적으로 업데이트 됩니다. 이 문제 해결 가이드에 표시 되지 않는 문제가 발생 하는 경우 [연락 Microsoft 지원](#contact) 섹션을 참조 하 여 상황을 문서화 합니다.

## <a name="importing-updates"></a><a name="import"></a>업데이트 가져오기

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>Q: 내 IoT Hub 인스턴스에 장치 업데이트 인스턴스를 연결 하는 데 문제가 있습니다.
_[장치 업데이트 리소스](./device-update-resources.md) 설명서에 따라 IoT Hub 메시지 경로가 올바르게 구성 되었는지 확인 하세요._

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>Q: 역할 관련 오류가 발생 했습니다 (Azure Portal 또는 403 API 오류의 오류 메시지).
_액세스 권한이 올바르게 구성 되지 않은 것 같습니다. [장치 업데이트 액세스 제어](./device-update-control-access.md) 설명서에 따라 액세스 권한이 올바르게 구성 되었는지 확인 하세요._

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>Q: 장치 업데이트 서비스로 콘텐츠를 가져올 때 500 유형 오류가 발생 합니다.
_500 범위의 오류 코드는 장치 업데이트 서비스에 문제가 있음을 나타낼 수 있습니다. 5 분 정도 기다린 후 다시 시도 하세요. 동일한 오류가 계속 발생 하면 [Microsoft 지원에 연결](#contact) 섹션의 지침에 따라 Microsoft와의 지원 요청을 파일에 추가 하세요._

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>Q: 콘텐츠를 가져올 때 오류 코드가 발생 하 고 구문 분석 하려고 합니다.
_구문 분석 오류 코드에 대 한 자세한 내용은 [장치 업데이트 오류 코드](./device-update-error-codes.md) 설명서를 참조 하세요._

## <a name="device-failures"></a><a name="device-failure"></a>장치 오류

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>Q: 장치가 IoT Hub 장치 업데이트에 연결 되었는지 확인 하려면 어떻게 해야 하나요?
_장치가 Azure Portal의 준수 보기에서 "그룹화 되지 않은" 장치 섹션에 표시 되는지 확인 하 여 장치가 장치 업데이트에 연결 되었는지 확인할 수 있습니다._

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>Q: 하나 이상의 장치를 업데이트 하지 못했습니다.
_장치 업데이트 실패에 대 한 근본 원인에는 여러 가지가 있을 수 있습니다. 장치가 사용자의 IoT Hub 인스턴스에 연결 되었는지, 2) 장치 업데이트 인스턴스에 연결 되었는지, 3) 배달 최적화 (DO) 서비스가 실행 중인지 확인 하세요. 장치에 대해 3 개가 모두 true 인 경우 [Microsoft 지원 연결](#contact) 섹션의 지침에 따라 Microsoft와의 지원 요청을 파일에 추가 하세요._

## <a name="deploying-an-update"></a><a name="deploy"></a> 업데이트 배포

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>Q: 내 장치에 업데이트를 배포 했지만 준수 상태가 최신 업데이트가 아닙니다. 라고 표시 됩니다.   어떻게 해야 합니까?
_장치 준수 상태를 새로 고치는 데 최대 5 분이 걸릴 수 있습니다. 잠시 기다렸다가 다시 확인 하세요._
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>Q: 장치의 배포 상태가 호환 되지 않는 것으로 표시 되 면 어떻게 해야 하나요?
_장치를 IoT Hub에 연결한 후 장치를 현재 배포의 업데이트 콘텐츠와 호환 되지 않는 것으로 간주 하기 때문에 대상 장치의 제조업체 및 모델 속성이 변경 되었을 수 있습니다._

_[ADU Core 인터페이스](./device-update-plug-and-play.md) 를 확인 하 여 장치가 장치 업데이트 서비스에 보고 하는 제조업체 및 모델을 확인 하 고 배포 되는 업데이트 콘텐츠의 [가져오기 매니페스트에](./import-concepts.md) 지정한 제조업체 및 모델과 일치 하는지 확인 합니다. [장치 업데이트 구성 파일](./device-update-configuration-file.md)을 사용 하 여 지정 된 장치에 대 한 이러한 속성을 변경할 수 있습니다._

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>Q: 내 배포가 "활성" 단계 이지만 업데이트가 포함 된 장치가 "진행 중" 상태에 있지 않습니다.   어떻게 해야 합니까?
_나중에 배포 시작 날짜를 설정 하지 않았는지 확인 합니다. 새 배포를 만들 때 명시적으로 변경 하지 않는 한 배포 시작 날짜는 기본적으로 다음 날로 설정 됩니다. 배포 시작 날짜가 도착할 때까지 기다리거나 진행 중인 배포를 취소 하 고 원하는 시작 날짜로 새 배포를 만들 수 있습니다._

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>Q: 내 장치를 그룹화 하려고 하지만 그룹을 만들 때 드롭다운에 태그가 표시 되지 않습니다.
_[장치 업데이트 리소스](./device-update-resources.md) 설명서에 따라 IoT Hub의 메시지 경로를 올바르게 구성 했는지 확인 합니다. 경로를 구성한 후 장치에 다시 태그를 다시 만들어야 합니다._

_장치를 IoT Hub 장치 업데이트에 연결 하기 전에 태그를 적용 한 원인이 원인일 수 있습니다. 장치가 장치 업데이트에 이미 연결 되어 있는지 확인 합니다. 장치가 준수 보기에서 "그룹화 되지 않은" 장치 아래에 표시 되는지 확인 하 여 장치가 IoT Hub의 장치 업데이트에 연결 되어 있는지 확인할 수 있습니다. 다른 값의 태그를 임시로 추가한 다음 장치가 연결 되 면 원하는 태그를 다시 추가 합니다._

_DPS (장치 프로 비전 서비스)를 사용 하는 경우 장치를 생성 하는 동안에는 장치를 프로 비전 한 후 장치를 태그를 다시 만들어야 합니다. 장치를 만드는 단계에서 장치에 이미 태그가 지정 된 경우 장치를 프로 비전 한 후에 다른 값으로 임시로 태그를 지정 하 고 원하는 태그를 다시 추가 해야 합니다._

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>Q: 내 배포가 성공적으로 완료 되었지만 일부 장치를 업데이트 하지 못했습니다.
_이 오류는 실패 한 장치에서 클라이언트 쪽 오류로 인해 발생 했을 수 있습니다. 이 문제 해결 가이드의 장치 오류 섹션을 참조 하세요._

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>Q: 배포를 시작 하려고 할 때 UX에 오류가 발생 했습니다.
_이 문제는 서비스/UX 버그로 인해 발생 했거나 API 권한 문제로 인해 발생 했을 수 있습니다. [Microsoft 지원에 연결](#contact) 섹션의 지침에 따라 Microsoft와 함께 지원 요청을 파일 하세요._

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>Q: 배포를 시작 했지만 종료 상태에 도달 하지 않습니다.
_이 문제는 서비스 성능 문제, 서비스 버그 또는 클라이언트 버그로 인해 발생 했을 수 있습니다. 10 분 후에 배포를 다시 시도 하세요. 동일한 문제가 발생 하는 경우 장치 로그를 끌어오고이 문제 해결 가이드의 장치 오류 섹션을 참조 하세요. 동일한 문제가 지속 되 면 [Microsoft 지원에 연결](#contact) 섹션의 지침에 따라 Microsoft와의 지원 요청을 파일에 추가 하세요._

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> 장치에 업데이트 다운로드

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>Q: 연결을 끊은 후 장치가 다시 연결 되 면 다운로드를 어떻게 할까요? 합니다.
_24 시간 내에 연결이 복원 되 면 다운로드가 자체 다시 시작 됩니다. 24 시간 후에는 사용자가 다운로드를 reinitiated 해야 합니다._
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> MCC (Microsoft 연결 캐시) 사용

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>Q: IoT Edge 장치에 MCC 모듈을 배포 하려고 하면 문제가 발생 합니다.
_IoT Edge 장치에 Edge 모듈을 배포 하는 방법에 대 한 [IoT Edge 설명서]() 를 참조 하세요. 으로 이동 하 여 IoT Edge 장치에서 MCC 모듈이 성공적으로 실행 되 고 있는지 확인할 수 있습니다 http://localhost:5100/Summary ._
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>Q: 내 IoT 장치 중 하나가 MCC를 통해 업데이트를 다운로드 하려고 하지만 실패 합니다.
_MCC에 연결 하는 동안 IoT 장치에 오류가 발생 하는 몇 가지 문제가 있습니다. 문제를 진단 하려면 실패 한 장치에서 DO client 및 Nginx 로그를 수집 하세요 (클라이언트 로그를 수집 하는 방법에 대 한 지침은 [Microsoft 지원 연결](#contact) 섹션 참조)._

_사용 중인 URL이 허용 되지 않으므로 장치에서 해당 MCC 모듈에 전달 하기 위해 인터넷에서 콘텐츠를 끌어오는 데 실패할 수 있습니다. 이를 확인 하려면 Azure Portal에서 IoT Edge 환경 변수를 확인 해야 합니다._
## <a name="contacting-microsoft-support"></a><a name="contact"></a> Microsoft 지원에 연결

위의 Faq를 사용 하 여 해결할 수 없는 문제가 발생 하는 경우 Azure Portal 인터페이스를 통해 Microsoft 지원 지원 요청을 보낼 수 있습니다. 문제를 조사 하는 데 도움이 되는 추가 데이터를 수집 하 고 공유 하는 Microsoft 지원 데 도움이 되는 추가 데이터를 수집 하 고 공유 하 라는 메시지가 표시 될 수 있습니다. 

각 데이터 형식을 수집 하는 방법에 대 한 지침은 아래를 참조 하세요. [Getdevices]() 를 사용 하 여 API의 페이로드 응답에서 추가 정보를 확인할 수 있습니다.

또한 다음 정보는 문제의 근본 원인을 축소 하는 데 유용할 수 있습니다.
* 업데이트 하려는 장치 유형 (Azure Percept, IoT Edge 게이트웨이, 기타)
* 사용 중인 장치 업데이트 클라이언트 유형 (이미지 기반, 패키지 기반, 시뮬레이터)
* 장치에서 실행 중인 OS
* 장치의 아키텍처와 관련 된 세부 정보
* 장치 업데이트를 사용 하 여 이전에 장치를 업데이트 했는지 여부

위의 정보를 사용할 수 있는 경우 문제에 대 한 설명에 포함 해 주세요.

### <a name="collecting-client-logs"></a>클라이언트 로그 수집

* Raspberry Pi 장치에는 다음 두 개의 로그 집합이 있습니다.

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* 패키지 된 클라이언트의 경우 로그는 다음 위치에서 찾을 수 있습니다.

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
업데이트 가져오기, 장치 오류 또는 업데이트 배포와 관련 된 문제를 보고할 때 오류 코드를 제공 하 라는 메시지가 표시 될 수 있습니다.

[ADUCoreInterface](./device-update-plug-and-play.md) 인터페이스를 살펴보면 오류 코드를 얻을 수 있습니다. 자체 진단 및 문제 해결을 위한 오류 코드를 구문 분석 하는 방법에 대 한 자세한 내용은 [장치 업데이트 오류 코드](./device-update-error-codes.md) 설명서를 참조 하세요.

### <a name="trace-id"></a>추적 ID
업데이트 가져오기 또는 배포와 관련 된 문제를 보고할 때 추적 ID를 제공 하 라는 메시지가 표시 될 수 있습니다.

지정 된 사용자 동작에 대 한 추적 ID는 API 응답 또는 Azure Portal 사용자 인터페이스의 가져오기 기록 섹션에서 찾을 수 있습니다. 

현재 배포 작업에 대 한 추적 Id는 API 응답을 통해서만 액세스할 수 있습니다.

### <a name="deployment-id"></a>배포 ID
업데이트 배포와 관련 된 문제를 보고할 때 배포 ID를 제공 하 라는 메시지가 표시 될 수 있습니다.

배포 ID는 배포를 시작 하기 위해 API를 호출할 때 사용자가 만듭니다.

현재 Azure Portal 사용자 인터페이스에서 시작 된 배포에 대 한 배포 Id가 자동으로 생성 되 고 사용자에 게 표시 되지 않습니다.

### <a name="iot-hub-instance-name"></a>IoT Hub 인스턴스 이름
장치 실패와 관련 된 문제를 보고 하거나 업데이트를 배포 하는 경우 IoT Hub 인스턴스의 이름을 입력 하 라는 메시지가 표시 될 수 있습니다.

IoT Hub 이름은 처음 프로 비전 될 때 사용자가 선택 합니다.

### <a name="device-update-account-name"></a>장치 업데이트 계정 이름
업데이트 가져오기, 장치 오류 또는 업데이트 배포와 관련 된 문제를 보고할 때 장치 업데이트 계정의 이름을 입력 하 라는 메시지가 표시 될 수 있습니다.

장치 업데이트 계정 이름은 사용자가 서비스에 처음 등록할 때 선택 됩니다. 자세한 내용은 [장치 업데이트 리소스](./device-update-resources.md) 설명서에서 찾을 수 있습니다.

### <a name="device-update-instance-name"></a>장치 업데이트 인스턴스 이름
업데이트 가져오기, 장치 오류 또는 업데이트 배포와 관련 된 문제를 보고할 때 장치 업데이트 인스턴스의 이름을 제공 하 라는 메시지가 표시 될 수 있습니다.

장치 업데이트 인스턴스 이름은 처음 프로 비전 될 때 사용자가 선택 합니다. 자세한 내용은 [장치 업데이트 리소스](./device-update-resources.md) 설명서에서 찾을 수 있습니다.

### <a name="device-id"></a>디바이스 ID
장치 오류와 관련 된 문제를 보고 하거나 업데이트를 배포 하는 경우 장치 ID를 제공 하 라는 메시지가 표시 될 수 있습니다.

장치 ID는 장치를 처음 프로 비전 할 때 고객에 의해 정의 됩니다. 장치의 장치 쌍에서 검색할 수도 있습니다.

### <a name="update-id"></a>업데이트 ID
업데이트 배포와 관련 된 문제를 보고할 때 업데이트 ID를 제공 하 라는 메시지가 표시 될 수 있습니다.

업데이트 ID는 배포를 시작할 때 고객에 의해 정의 됩니다.

### <a name="nginx-logs"></a>Nginx 로그
Microsoft 연결 된 캐시와 관련 된 문제를 보고할 때 Nginx 로그를 제공 하 라는 메시지가 표시 될 수 있습니다.

### <a name="adu-conftxt"></a>ADU-conf.txt
업데이트 배포와 관련 된 문제를 보고할 때 장치 업데이트 구성 파일 ("adu-conf.txt")을 제공 하 라는 메시지가 표시 될 수 있습니다.

구성 파일은 선택 사항이 며 [장치 업데이트 구성](./device-update-configuration-file.md) 설명서의 지침에 따라 사용자가 만듭니다.

### <a name="import-manifest"></a>매니페스트 가져오기
업데이트 가져오기 또는 배포와 관련 된 문제를 보고할 때 가져오기 매니페스트 파일을 제공 하 라는 메시지가 표시 될 수 있습니다.

가져오기 매니페스트는 장치 업데이트 서비스에 업데이트 콘텐츠를 가져올 때 고객이 만든 파일입니다.

**[다음 단계: 장치 업데이트 오류 코드에 대 한 자세한 정보](.\device-update-error-codes.md)**