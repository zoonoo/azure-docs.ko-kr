---
title: Azure IoT Hub APT 매니페스트에 대 한 장치 업데이트 이해 | Microsoft Docs
description: IoT Hub에 대 한 장치 업데이트가 패키지 기반 업데이트에 대해 apt 매니페스트를 사용 하는 방식을 이해 합니다.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b68b78499aa3bf0d84d8bd0fa5ab55d1f969113
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679590"
---
# <a name="device-update-apt-manifest"></a>장치 업데이트 APT 매니페스트

APT 매니페스트는 APT 업데이트 처리기에 필요한 업데이트 정보를 설명 하는 JSON 파일입니다. 이 파일을 다른 업데이트와 마찬가지로 IoT Hub에 대 한 장치 업데이트로 가져올 수 있습니다.

장치 업데이트에 업데이트를 가져오는 방법에 [대해 자세히 알아보세요](import-update.md) .

## <a name="overview"></a>개요

APT 매니페스트가 업데이트로 장치 업데이트 에이전트로 배달 되는 경우 에이전트는 매니페스트를 처리 하 고 필요한 작업을 수행 합니다. 이러한 작업에는 APT 매니페스트 파일에 지정 된 패키지를 다운로드 하 고 설치 하는 작업과 해당 종속성이 포함 됩니다.

장치 업데이트는 APT UpdateType 및 APT 업데이트 처리기를 지원 합니다. 이 지원을 통해 장치 업데이트 에이전트는 설치 된 Debian 패키지를 평가 하 고 필요한 패키지를 업데이트할 수 있습니다. 

## <a name="schema"></a>스키마

APT 매니페스트 파일은 버전이 지정 된 스키마가 있는 JSON 파일입니다.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

예제:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

이 APT 매니페스트의 이름입니다. 시나리오에 적합 한 이름 또는 ID는 무엇이 든 될 수 있습니다. 예들 들어 `contoso-iot-edge`입니다.

### <a name="version"></a>버전

이 APT 매니페스트에 대 한 버전 번호입니다. 예들 들어 `1.0.0.0`입니다.


### <a name="packages"></a>패키지

패키지 관련 속성을 포함 하는 개체의 목록입니다.

#### <a name="name"></a>name

패키지의 이름 또는 ID입니다. 예들 들어 `iotedge`입니다.

#### <a name="version"></a>버전

패키지에 대 한 원하는 버전 조건입니다. 예들 들어 `1.0.8-2`입니다.

현재 정확한 버전 번호는 지원 됩니다. 버전 번호는 [epoch:] upstream_version [-debian_revision] 형식의 원하는 Debian 패키지 버전입니다.

**epoch** 는 부호 없는 정수입니다.

**upstream_version** 에는 ".", "+", "-" 및 "~"와 같은 영숫자 및 문자를 포함할 수 있습니다. 숫자로 시작 해야 합니다.
> [!NOTE]
> ' 1.0.8 '는 ' 1.0.8-0 '과 같습니다.

예를 들어, **`"name":"iotedge" and "version":"1.0.8-2"`** 명령을 사용 하 여 패키지를 설치 하는 것과 같습니다. `apt-get install iotedge=1.0.8-2`

> [!NOTE]
> 버전 값에 등호가 없습니다.

버전을 생략 하면 지정 된 패키지의 사용 가능한 최신 버전이 설치 됩니다.

Debian 패키지의 버전을 지정 하는 방법에 [대해 자세히 알아보세요](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) .

> [!NOTE]
> APT 패키지 관리자는 설치할 종속 패키지를 자동으로 확인 하는 경우 패키지에서 제공 하는 버전 관리 요구 사항을 무시 합니다. 종속 패키지의 명시적 버전을 지정 하지 않는 한 패키지 자체가 지정 된 버전에서 엄격한 요구 사항 (=)을 지정할 수 있는 경우에도 최신를 사용 합니다. 이 자동 해결을 통해 충족 되지 않는 종속성과 관련 된 오류가 발생할 수 있습니다. [자세한 내용](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Azure IoT Edge 보안 디먼의 특정 버전을 업데이트 하는 경우 `iotedge` APT 매니페스트에 원하는 패키지 버전과 해당 종속 패키지를 포함 해야 합니다 `libiothsm-std` .
[자세한 내용](https://docs.microsoft.com/azure/iot-edge/how-to-update-iot-edge#update-the-security-daemon)

> [!NOTE]
> Apt 매니페스트는 장치 업데이트 에이전트 및 해당 종속성을 업데이트 하는 데 사용할 수 있습니다. 다른 패키지에 대 한 것 처럼 apt 매니페스트에 장치 업데이트 에이전트 이름 및 원하는 버전을 나열 합니다. 그런 다음 IoT Hub 파이프라인에 대 한 장치 업데이트를 통해이 apt 매니페스트를 가져오고 배포할 수 있습니다. 

## <a name="removing-packages"></a>패키지 제거

Apt 매니페스트를 사용 하 여 장치에서 설치 된 패키지를 제거할 수도 있습니다. 단일 apt 매니페스트를 사용 하 여 여러 패키지를 제거, 추가 및 업데이트할 수 있습니다. 패키지를 제거 하려면 패키지 이름 뒤에 빼기 기호 "-"를 추가 합니다. 제거할 패키지의 버전 번호를 포함 하지 않아야 합니다. Apt 매니페스트를 통해 패키지를 제거 해도 종속성 및 구성은 제거 되지 않습니다.

예제:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
이 apt 매니페스트는 배포 된 장치에서 패키지 "foo"를 제거 합니다. 

## <a name="recommended-value-for-installed-criteria"></a>설치 된 조건에 권장 되는 값

APT 매니페스트에 설치 된 기준은입니다 `<name>-<version>` `<name>` . 여기서는 APT 매니페스트의 이름이 고 `<version>` 는 APT manifest의 버전입니다. 예들 들어 `contoso-iot-edge-1.0.0.0`입니다. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>APT 매니페스트를 만드는 방법에 대 한 지침

APT 매니페스트를 만드는 동안 다음과 같은 몇 가지 지침을 염두에 두어야 합니다.

- 항상 APT 매니페스트가 올바른 형식의 json 파일 인지 확인 합니다.
- 각 APT 매니페스트에는 고유한 버전이 있어야 합니다. 표준화 된 방법으로 APT 매니페스트 버전을 증가 시켜 시나리오에 적합 하 고 쉽게 따를 수 있도록 합니다.
- 각 개별 패키지의 원하는 상태가 되 면 장치에 설치할 패키지의 정확한 이름과 버전을 지정 합니다. 패키지에 대 한 원본으로 사용 하려는 패키지 리포지토리에 대해 항상 값의 유효성을 검사 합니다.
- APT 매니페스트의 패키지가 설치/제거 되어야 하는 순서 대로 나열 되는지 확인 합니다.
- 테스트 장치에서 패키지 설치의 유효성을 항상 검사 하 여 결과가 필요한 지 확인 합니다.
- 특정 버전의 패키지를 설치 하는 경우 (예: `iotedge 1.0.9-1` ) APT 매니페스트에 설치 될 종속 패키지의 명시적 버전 (예:)도 포함 하는 것이 좋습니다. `libiothsm 1.0.9-1`
- 반드시 필요한 것은 아니지만 APT 매니페스트가 누적 되어 장치가 알 수 없는 상태로 전환 되는 것을 방지 해야 합니다. 누적 업데이트를 사용 하는 경우 장치에서 설치에 실패 하거나 오프 라인으로 전환 하는 APT 업데이트 배포를 건너뛴 경우에도 관심 있는 모든 패키지의 desired 버전이 있는지 확인할 수 있습니다.

다음은 그 예입니다. 

**기본 APT 매니페스트**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**잘못 된 업데이트**

이 업데이트에는 도구 모음 패키지가 포함 되지만 foo 패키지는 포함 되지 않습니다.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**업데이트 양호**

이 업데이트는 foo 패키지를 포함 하며 가로 막대형 패키지도 포함 합니다.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [새 업데이트 가져오기](import-update.md)

