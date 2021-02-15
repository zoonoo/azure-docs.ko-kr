---
title: Defender for IoT CLI 명령 작업
description: 이 문서에서는 센서 및 온-프레미스 관리 콘솔에 대 한 IoT CLI 명령인 Defender를 설명 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 2ec682bf76e35b54f58acc1956972c57128edd75
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523144"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Defender for IoT CLI 명령 작업

이 문서에서는 센서 및 온-프레미스 관리 콘솔에 대 한 CLI 명령을 설명 합니다. 이 명령은 관리자, cyberx 사용자 및 지원 사용자가 액세스할 수 있습니다.

유지 관리 작업을 계획 중이거나 경고를 요구 하지 않는 작업을 계획 하는 경우 제외 규칙을 정의 합니다.

## <a name="create-local-alert-exclusion-rules"></a>로컬 경고 제외 규칙 만들기

CLI에 다음 명령을 입력 하 여 제외 규칙을 만들 수 있습니다.

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

경고 제외 규칙 내에서 정의할 수 있는 특성은 다음과 같습니다.

| attribute | Description |
|--|--|
| [-h] | 명령에 대 한 도움말 정보를 인쇄 합니다. |
| -n 이름 | 만들고 있는 규칙의 이름입니다. |
| [-ts 시간] | 규칙이 활성화 되는 시간 범위입니다. 다음과 같이 지정 해야 합니다.<br />`xx:yy-xx:yy`<br />두 기간 사이에 쉼표를 사용 하 여 둘 이상의 기간을 정의할 수 있습니다. 예: `xx:yy-xx:yy, xx:yy-xx:yy` |
| [-dir DIRECTION] | 규칙이 적용 되는 방향입니다. 다음과 같이 지정 해야 합니다.<br />`both | src | dst` |
| [-dev 장치] | 규칙에 의해 제외 될 장치의 IP 주소 및 주소 유형으로, 다음과 같이 지정 됩니다.<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a 경고] | 규칙이 제외 될 경고의 이름:<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>로컬 경고 제외 규칙 추가

CLI에서 다음 명령을 입력 하 여 현재 경고 제외 규칙에 새 규칙을 추가할 수 있습니다.

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

여기서 사용 되는 특성은 로컬 경고 제외 규칙을 만들 때 설명 된 특성과 유사 합니다. 여기서 사용 하는 특성은 기존 규칙에 적용 됩니다.

## <a name="show-local-alert-exclusion-rules"></a>로컬 경고 제외 규칙 표시

다음 명령을 입력 하 여 기존 제외 규칙을 모두 표시 합니다.

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>로컬 경고 제외 규칙 삭제

다음 명령을 입력 하 여 기존 경고 제외 규칙을 삭제할 수 있습니다.

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

경고 제외 규칙과 함께 사용할 수 있는 특성은 다음과 같습니다.

| attribute | Description|
| --------- | ---------------------------------- |
| -n 이름 | 삭제할 규칙의 이름입니다. |

## <a name="sync-time-from-the-ntp-server"></a>NTP 서버의 동기화 시간

NTP 서버에서 시간 동기화를 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

### <a name="enable-ntp-sync"></a>NTP 동기화 사용

다음 명령을 입력 하면 지정 된 NTP 서버에서 현재 시간을 정기적으로 검색할 수 있습니다.

```azurecli-interactive
ntp enable IP
```

명령 내에서 정의할 수 있는 특성은 NTP 서버의 IP 주소입니다.

### <a name="disable-ntp-sync"></a>NTP 동기화 사용 안 함

다음 명령을 입력 하면 지정 된 NTP 서버와의 시간 동기화를 사용 하지 않도록 설정 합니다.

```azurecli-interactive
ntp disable IP
```

명령 내에서 정의할 수 있는 특성은 NTP 서버의 IP 주소입니다.

## <a name="configure-the-network"></a>네트워크 구성

다음 표에서는 IoT 용 Azure Defender에 대 한 네트워크 옵션을 구성 하는 데 사용할 수 있는 명령을 설명 합니다.

|속성|명령|Description|
|-----------|-------|-----------|
|Ping|`ping IP `| IoT 용 Defender 플랫폼 외부에서 주소를 ping 합니다.|
|Blink|`network blink`|네트워크 구성 매개 변수를 변경할 수 있습니다.|
|네트워크 다시 구성 |`network edit-settings`| 네트워크 구성 매개 변수를 변경할 수 있습니다. |
|네트워크 설정 표시 |`network list`|네트워크 어댑터 매개 변수를 표시 합니다. |
|네트워크 구성의 유효성을 검사 합니다. |`network validate` |출력 네트워크 설정을 표시 합니다. <br /> <br />예를 들면 다음과 같습니다. <br /> <br />현재 네트워크 설정: <br /> 인터페이스: eth0 <br /> ip: 10.100.100.1 <br />서브넷: 255.255.255.0 <br />기본 게이트웨이: 10.100.100.254 <br />dns: 10.100.100.254 <br />인터페이스 모니터링: eth1|
|인증서 가져오기 |`certificate import FILE` |HTTPS 인증서를 가져옵니다. .Crt 파일로 이어지는 전체 경로를 지정 해야 합니다. \* |
|날짜 표시 |`date` |호스트에서 GMT 형식으로 현재 날짜를 반환 합니다. |

## <a name="filter-network-configurations"></a>네트워크 구성 필터링

`network capture-filter`관리자는이 명령을 사용 하 여 분석 하지 않아도 되는 네트워크 트래픽을 제거할 수 있습니다. 포함 목록 또는 제외 목록을 사용 하 여 트래픽을 필터링 합니다.

```azurecli-interactive
network capture-filter
```

명령을 입력 하면 다음 질문에 대 한 메시지가 표시 됩니다.

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

`Y`다음 구문에 따라 장치, 채널, 포트 및 하위 집합을 추가할 수 있는 nano 파일을 열려면 선택 합니다.

| attribute | Description |
|--|--|
| 1.1.1.1 | 이 장치에 대 한 모든 트래픽을 포함 합니다. |
| 1.1.1.1, 2.2.2.2 | 이 채널에 대 한 모든 트래픽을 포함 합니다. |
| 1.1.1, 2.2.2 | 이 서브넷에 대 한 모든 트래픽을 포함 합니다. |

행을 삭제 하 여 인수를 구분 합니다.

장치, 채널 또는 서브넷을 포함 하는 경우 센서는 일반적으로 처리 되지 않는 포트 및 트래픽을 포함 하 여 해당 인수에 대해 유효한 모든 트래픽을 처리 합니다.

그러면 다음을 묻는 메시지가 표시 됩니다.

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

`Y`다음 구문에 따라 장치, 채널, 포트 및 하위 집합을 추가할 수 있는 nano 파일을 열려면 선택 합니다.

| attribute | Description |
|--|--|
| 1.1.1.1 | 이 장치에 대 한 모든 트래픽을 제외 합니다. |
| 1.1.1.1, 2.2.2.2 | 이 채널에 대 한 모든 트래픽을 제외 합니다. 즉, 두 장치 간의 모든 트래픽을 의미 합니다. |
| 1.1.1.1, 2.2.2.2, 443 | 포트에서이 채널에 대 한 모든 트래픽을 제외 합니다. |
| 1.1.1 | 이 서브넷에 대 한 모든 트래픽을 제외 합니다. |
| 1.1.1, 2.2.2 | 서브넷 간 트래픽을 모두 제외 합니다. |

행을 삭제 하 여 인수를 구분 합니다.

장치, 채널 또는 서브넷을 제외 하면 센서는 해당 인수에 대해 유효한 모든 트래픽을 제외 합니다.

### <a name="ports"></a>포트

모든 트래픽에 대해 UDP 및 TCP 포트를 포함 하거나 제외 합니다.

>`502`: 단일 포트

>`502,443`: 두 포트

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>구성 요소

다음을 묻는 메시지가 표시 됩니다.

>`In which component do you wish to apply this capture filter?`

옵션은,,  `all` `dissector` ,, `collector` `statistics-collector` `rpc-parser` 또는 `smb-parser` 입니다.

대부분의 경우에는를 선택 `all` 합니다.

### <a name="custom-base-capture-filter"></a>사용자 지정 기본 캡처 필터

기본 캡처 필터는 구성 요소에 대 한 기준입니다. 예를 들어 필터는 구성 요소에 사용할 수 있는 포트를 결정 합니다.

`Y`다음 옵션을 모두 선택 합니다. 모든 필터는 변경 내용이 설정 된 후 기준에 추가 됩니다. 변경을 수행 하는 경우 기존 기준을 덮어씁니다.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

1.1.1과 같은 서브넷을 제외 하도록 선택 하는 경우:

- `internal` 은 해당 서브넷만 제외 합니다.

- `all-connected` 에서 해당 서브넷 및 해당 서브넷에서 들어오고 나가는 모든 트래픽을 제외 합니다.

을 선택 하는 것이 좋습니다 `internal` .

> [!NOTE]
> 선택 항목은 도구의 모든 필터에 사용 되며 세션에 종속 되지 않습니다. 즉, `internal`   일부 필터 및 다른 필터에 대해서는 선택할 수 없습니다  `all-connected`   .

### <a name="comments"></a>주석

다음에서 필터를 볼 수 있습니다  ```/var/cyberx/properties/cybershark.properties``` .

- **통계-수집기**:  `bpf_filter property` 에서 ```/var/cyberx/properties/net.stats.collector.properties```

- **dissector**: `override.capture_filter`   속성 ```/var/cyberx/properties/cybershark.properties```

- **rpc-파서**: `override.capture_filter` 의 속성 ```/var/cyberx/properties/rpc-parser.properties```

- **smb-파서**:  `override.capture_filter`   의 속성 ```/var/cyberx/properties/smb-parser.properties```

- **수집기**: `general.bpf_filter`   의 속성 ```/var/cyberx/properties/collector.properties```

Cyberx 사용자에 대 한 다음 코드를 입력 하 여 기본 구성을 복원할 수 있습니다.

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>클라이언트 및 서버 호스트 정의

IoT 용 Defender가 클라이언트 및 서버 호스트를 자동으로 검색 하지 않는 경우 다음 명령을 입력 하 여 클라이언트 및 서버 호스트를 설정 합니다.

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

명령을 사용 하 여 다음 특성을 사용할 수 있습니다 `directions` .

| attribute | Description |
|--|--|
| [-h] | 명령에 대 한 도움말 정보를 인쇄 합니다. |
| [--식별자 식별자] | 서버 식별자입니다. |
| [--포트 포트] | 서버 포트입니다. |
| [--제거] | 목록에서 클라이언트 또는 서버 호스트를 제거 합니다. |
| [--추가] | 클라이언트 또는 서버 호스트를 목록에 추가 합니다. |
| [--tcp] | 이 호스트와 통신할 때 TCP를 사용 합니다. |
| [--udp] | 이 호스트와 통신할 때 UDP를 사용 합니다. |

## <a name="system-actions"></a>시스템 작업
다음 표에서는 IoT 용 Defender 내에서 다양 한 시스템 작업을 수행 하는 데 사용할 수 있는 명령을 설명 합니다.

|속성|코드|Description|
|----|----|-----------|
|호스트 다시 부팅|`system reboot`|호스트 장치를 다시 부팅 합니다.|
|호스트 종료|`system shutdown`|호스트를 종료 합니다.|
|시스템 백업|`system backup`|즉시 백업 (예약 되지 않은 백업)을 시작 합니다.|
|백업에서 시스템 복원|`system restore`|가장 최근의 백업에서 복원 합니다.|
|백업 파일 나열|`system backup-list`|사용 가능한 백업 파일을 나열 합니다.|
|IoT 플랫폼 서비스에 대 한 모든 Defender의 상태를 표시 합니다.|`system sanity`|IoT platform 서비스용 모든 Defender의 현재 상태를 나열 하 여 시스템의 성능을 확인 합니다.|
|소프트웨어 버전 표시|`system version`|시스템에서 현재 실행 중인 소프트웨어의 버전을 표시 합니다.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>어플라이언스에 SSL 및 TLS 인증서 배포

다음 명령을 입력 하 여 CLI로 SSL 및 TLS 엔터프라이즈 인증서를 가져옵니다.

```azurecli-interactive
cyberx-xsense-certificate-import
```
이 도구를 사용 하려면 인증서 파일을 장치에 업로드 해야 합니다. WinSCP 또는 Wget과 같은 도구를 통해이 작업을 수행할 수 있습니다. 

이 명령은 다음 입력 플래그를 지원 합니다.

| 플래그 | 설명 |
|--|--|
| -H | 명령줄 도움말 구문을 보여 줍니다. |
| --crt | 인증서 파일 (.crt 확장명)의 경로입니다. |
| --key | \*키 파일입니다. 키 길이는 2048 비트 이상 이어야 합니다. |
| --체인 | 인증서 체인 파일의 경로입니다 (선택 사항). |
| --pass | 인증서를 암호화 하는 데 사용 되는 암호입니다 (선택 사항). |
| --passphrase-set | 기본값은 **False**, **사용 되지** 않음입니다. <br />이전 인증서와 함께 제공 된 이전 암호를 사용 하려면 **True** 로 설정 합니다 (옵션). |  |

도구를 사용 하는 경우:

- 인증서 파일을 어플라이언스에서 읽을 수 있는지 확인 합니다. 

- DNS 서버와 해당 IP 주소를 사용 하 여 어플라이언스 도메인 (인증서에 표시 된 대로)을 확인 합니다. 
    
## <a name="next-steps"></a>다음 단계

[IoT API 센서 및 관리 콘솔 Api 용 Defender](references-work-with-defender-for-iot-apis.md)
