---
title: Defender for IoT API 작업
description: 외부 REST API를 사용 하 여 센서 및 관리 콘솔에서 검색 된 데이터에 액세스 하 고 해당 데이터를 사용 하 여 작업을 수행 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: reference
ms.service: azure
ms.openlocfilehash: 44ea6e8343203a9cb18947f31f45aa0b023178b0
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624577"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>IoT 센서 및 관리 콘솔 Api 용 Defender

외부 REST API를 사용 하 여 센서 및 관리 콘솔에서 검색 된 데이터에 액세스 하 고 해당 데이터를 사용 하 여 작업을 수행 합니다.

연결은 SSL을 통해 보안이 유지 됩니다.

## <a name="getting-started"></a>시작

일반적으로 IoT 센서 또는 온-프레미스 관리 콘솔에 대 한 Azure Defender에서 외부 API를 사용 하는 경우 액세스 토큰을 생성 해야 합니다. 센서 및 온-프레미스 관리 콘솔에서 사용 하는 인증 Api에는 토큰이 필요 하지 않습니다.

토큰을 생성 하려면:

1. **시스템 설정** 창에서 **액세스 토큰** 을 선택 합니다.
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="액세스 토큰 단추를 강조 표시 하는 시스템 설정 창의 스크린샷":::

2. **Generate new token**(새 토큰 생성)을 탭합니다.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="새 토큰을 생성 하는 단추를 선택 합니다.":::

3. 새 토큰의 용도를 설명 하 고 **다음** 을 선택 합니다.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="새 토큰을 생성 하 고 연결 된 통합의 이름을 입력 합니다.":::

4. 액세스 토큰이 표시 됩니다. 다시 표시 되지 않으므로 복사 합니다.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="통합을 위해 액세스 토큰을 복사 합니다.":::

5. **마침** 을 선택합니다. 만든 토큰이 **액세스 토큰** 대화 상자에 나타납니다.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="채워진 토큰이 있는 장치 토큰 대화 상자 스크린샷":::

   **사용** 됨이 토큰을 사용 하는 외부 호출을 마지막으로 받은 시간을 나타냅니다.

   이 토큰에 대해 **사용** 된 필드에 **N/A** 가 표시 되 면 센서와 연결 된 서버 간의 연결이 작동 하지 않습니다.

6. **권한 부여** 라는 HTTP 헤더를 요청에 추가 하 고 해당 값을 생성 한 토큰으로 설정 합니다.

## <a name="sensor-api-specifications"></a>센서 API 사양

이 섹션에서는 다음과 같은 센서 Api에 대해 설명 합니다.

- /api/v1/devices

- /api/v1/devices/connections

- /api/v1/devices/cves

- /api/v1/alerts

- /api/v1/events

- /api/v1/reports/vulnerabilities/devices

- /api/v1/reports/vulnerabilities/security

- /api/v1/reports/vulnerabilities/operational

- /sa/cv/cv/validation

- /external/authentication/set_password

- /external/authentication/set_password_by_admin

### <a name="retrieve-device-information"></a>장치 정보 검색

이 API를 사용 하 여 IoT 용 Defender 센서가 검색 한 모든 장치 목록을 요청 합니다.

#### <a name="apiv1devices"></a>/api/v1/devices

#### <a name="method"></a>메서드

**GET**

IoT 용 Defender 센서가 검색 한 모든 장치 목록을 요청 합니다.

#### <a name="query-parameters"></a>쿼리 매개 변수

- **권한 부여** 됨: 허가 되 고 권한이 없는 장치만 필터링 합니다.

  **예**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

장치를 나타내는 JSON 개체의 배열입니다.

#### <a name="device-fields"></a>장치 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **id** | 숫자 | No | - |
| **ipAddresses** | JSON 배열 | Yes | IP 주소 (인터넷 주소 또는 이중 Nic를 사용 하는 장치의 경우 두 개 이상의 주소를 사용할 수 있음) |
| **name** | String | No | - |
| **type** | String | No | 알 수 없음, 엔지니어링 스테이션, PLC, HMI, Historian, 도메인 컨트롤러, DB 서버, 무선 액세스 지점, 라우터, 스위치, 서버, 워크스테이션, IP 카메라, 프린터, 방화벽, 터미널 스테이션, VPN Gateway, 인터넷 또는 멀티 캐스트 및 브로드캐스트 |
| **macAddresses** | JSON 배열 | Yes | MAC 주소 (이중 Nic를 사용 하는 장치의 경우 두 개 이상의 주소를 사용할 수 있음) |
| **operatingSystem** | String | 예 | - |
| **engineeringStation** | 부울 | No | True 또는 False |
| **장치가** | 부울 | No | True 또는 False |
| **승인됨** | 부울 | No | True 또는 False |
| **업체인** | String | 예 | - |
| **인터넷용** | JSON 배열 | Yes | 프로토콜 개체 |
| **펌웨어입니다** | JSON 배열 | Yes | 펌웨어 개체 |

#### <a name="protocol-fields"></a>프로토콜 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **이름** | String | No |  |
| **주소** | JSON 배열 | Yes | Master 또는 numeric 값 |

#### <a name="firmware-fields"></a>펌웨어 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **연속적인** | String | No | 해당 없음 또는 실제 값 |
| **model** | String | No | 해당 없음 또는 실제 값 |
| **firmwareVersion** | Double | No | 해당 없음 또는 실제 값 |
| **additionalData** | String | No | 해당 없음 또는 실제 값 |
| **moduleAddress** | String | No | 해당 없음 또는 실제 값 |
| **랙마운트** | String | No | 해당 없음 또는 실제 값 |
| **슬롯** | String | No | 해당 없음 또는 실제 값 |
| **address** | String | No | 해당 없음 또는 실제 값 |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

### <a name="retrieve-device-connection-information"></a>장치 연결 정보 검색

이 API를 사용 하 여 장치 당 모든 연결 목록을 요청 합니다.

#### <a name="apiv1devicesconnections"></a>/api/v1/devices/connections

#### <a name="method"></a>메서드

**GET**

#### <a name="query-parameters"></a>쿼리 매개 변수

쿼리 매개 변수를 설정 하지 않으면 모든 장치 연결이 반환 됩니다.

**예제**:

`/api/v1/devices/connections`

- **deviceId**: 특정 장치 ID를 기준으로 필터링 하 여 연결을 확인 합니다.

  **예제**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**: 이제 시간이 지난 후 연결이 활성 상태인 시간 프레임입니다.

  **예제**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**: 특정 시간 (밀리초, UTC) 전에 검색 된 연결만 필터링 합니다.

  **예제**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**: 특정 시간 (밀리초, UTC) 이후에 검색 된 연결만 필터링 합니다.

  **예제**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

장치 연결을 나타내는 JSON 개체의 배열입니다.

#### <a name="fields"></a>필드

| 이름 | Type | Nullable | 값 목록 |
|--|--|--|--|
| **firstDeviceId** | 숫자 | No | - |
| **secondDeviceId** | 숫자 | No | - |
| **lastSeen** | 숫자 | No | Epoch (UTC) |
| **검색** | 숫자 | No | Epoch (UTC) |
| **포트** | 숫자 배열 | No | - |
| **인터넷용** | JSON 배열 | No | 프로토콜 필드 |

#### <a name="protocol-field"></a>프로토콜 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **name** | String | No | - |
| **명령을** | 문자열 배열 | No | - |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

### <a name="retrieve-information-on-cves"></a>CVEs에서 정보 검색

이 API를 사용 하 여 네트워크의 장치에서 검색 된 모든 알려진 CVEs 목록을 요청 합니다.

#### <a name="apiv1devicescves"></a>/api/v1/devices/cves

#### <a name="method"></a>메서드

**GET**

#### <a name="query-parameters"></a>쿼리 매개 변수

기본적으로이 API는 CVEs를 사용 하 여 모든 장치 Ip의 목록을 제공 합니다. 각 IP 주소에 대해 최대 100의 가장 높은 점수를 매긴 CVEs를 제공 합니다.

**예제**:

`/api/v1/devices/cves`

- **deviceId**: 특정 장치에서 식별 된 가장 높은 점수 100를 매긴 cves를 가져오도록 특정 장치 IP 주소를 기준으로 필터링 합니다.

  **예제**:

  `/api/v1/devices/<ipAddress>/cves`

- **top**: 각 장치 IP 주소에 대해 검색할 상위 점수가 매겨진 cves의 수입니다.

  **예제**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

IP 주소에서 식별 된 CVEs를 나타내는 JSON 개체의 배열입니다.

#### <a name="fields"></a>필드

| 이름 | Type | Nullable | 값 목록 |
|--|--|--|--|
| **cveId** | String | No | - |
| **\** | String | No | IP 주소 |
| **점수** | String | No | 0.0-10.0 |
| **attackVector** | String | No | 네트워크, 인접 네트워크, 로컬 또는 물리적 |
| **description** | String | No | - |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

### <a name="retrieve-alert-information"></a>경고 정보 검색

이 API를 사용 하 여 Defender 용 Defender 센서가 검색 한 모든 경고 목록을 요청 합니다.

#### <a name="apiv1alerts"></a>/api/v1/alerts

#### <a name="method"></a>메서드

**GET**

#### <a name="query-parameters"></a>쿼리 매개 변수

- **상태**: 처리 되거나 처리 되지 않은 경고만 필터링 합니다.

  **예제**:

  `/api/v1/alerts?state=handled`

- **fromtime**: 특정 시간 (밀리초, UTC)에서 만든 경고를 필터링 합니다.

  **예제**:

  `/api/v1/alerts?fromTime=<epoch>`

- **totime**: 특정 시간 (밀리초, UTC) 전에만 생성 된 경고를 필터링 합니다.

  **예제**:

  `/api/v1/alerts?toTime=<epoch>`

- **유형**: 특정 유형에 따라 경고를 필터링 합니다. 필터링 할 기존 형식: 예기치 않은 새 장치, 연결이 끊어지지 않습니다.

  **예제**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

경고를 나타내는 JSON 개체의 배열입니다.

#### <a name="alert-fields"></a>경고 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **ID** | 숫자 | No | - |
| **time** | 숫자 | No | Epoch (UTC) |
| **title** | String | No | - |
| **message** | String | No | - |
| **severity** | String | No | 경고, 부, 주 또는 위험 |
| **엔진** | String | No | 프로토콜 위반, 정책 위반, 맬웨어, 변칙 또는 작동 |
| **sourceDevice** | 숫자 | Yes | 디바이스 ID |
| **destinationDevice** | 숫자 | Yes | 디바이스 ID |
| **additionalInformation** | 추가 정보 개체 | Yes | - |

#### <a name="additional-information-fields"></a>추가 정보 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **description** | String | No | - |
| **내용을** | JSON 배열 | 예 | String |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

### <a name="retrieve-timeline-events"></a>타임 라인 이벤트 검색

이 API를 사용 하 여 이벤트 타임 라인에 보고 된 이벤트 목록을 요청 합니다.

#### <a name="apiv1events"></a>/api/v1/events

#### <a name="method"></a>메서드

**GET**

#### <a name="query-parameters"></a>쿼리 매개 변수

- **minutesTimeFrame**: 이제 이벤트가 보고 된 시간 프레임의 시간 (분)입니다.

  **예제**:

  `/api/v1/events?minutesTimeFrame=20`

- **형식**: 특정 형식으로 이벤트 목록을 필터링 합니다.

  **예**:

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

경고를 나타내는 JSON 개체의 배열입니다.

#### <a name="event-fields"></a>이벤트 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|--|
| **timestamp** | 숫자 | No | Epoch (UTC) |
| **title** | String | No | - |
| **severity** | String | No | 정보, 알림 또는 경고 |
| **소유자도** | String | 예 | 이벤트를 수동으로 만든 경우이 필드에는 이벤트를 만든 사용자 이름이 포함 됩니다. |
| **content** | String | No | - |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

### <a name="retrieve-vulnerability-information"></a>취약성 정보 검색

이 API를 사용 하 여 각 장치에 대 한 취약성 평가 결과를 요청 합니다.

#### <a name="apiv1reportsvulnerabilitiesdevices"></a>/api/v1/reports/vulnerabilities/devices

#### <a name="method"></a>메서드

**GET**

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

평가 된 장치를 나타내는 JSON 개체의 배열입니다.

장치 개체는 다음을 포함 합니다.

- 일반 데이터

- 평가 점수

- 취약점

#### <a name="device-fields"></a>장치 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **name** | String | No | - |
| **ipAddresses** | JSON 배열 | No | - |
| **securityScore** | 숫자 | No | - |
| **업체인** | String | 예 |  |
| **firmwareVersion** | String | 예 | - |
| **model** | String | 예 | - |
| **isWirelessAccessPoint** | 부울 | No | True 또는 False |
| **operatingSystem** | 운영 체제 개체 | Yes | - |
| **취약성** | 취약성 개체 | Yes | - |

#### <a name="operating-system-fields"></a>운영 체제 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **이름** | String | 예 | - |
| **형식** | String | 예 | - |
| **버전** | String | 예 | - |
| **latestVersion** | String | 예 | - |

#### <a name="vulnerabilities-fields"></a>취약성 필드
 
| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **바이러스 방지** | JSON 배열 | Yes | 바이러스 백신 이름 |
| **plainTextPasswords** | JSON 배열 | Yes | Password 개체 |
| **remoteAccess** | JSON 배열 | Yes | 원격 액세스 개체 |
| **isBackupServer** | 부울 | No | True 또는 False |
| **openedPorts** | JSON 배열 | Yes | 열린 포트 개체 |
| **isEngineeringStation** | 부울 | No | True 또는 False |
| **isKnownScanner** | 부울 | No | True 또는 False |
| **같이** | JSON 배열 | Yes | CVE 개체 |
| **isUnauthorized 없음** | 부울 | No | True 또는 False |
| **malwareIndicationsDetected** | 부울 | No | True 또는 False |
| **weakAuthentication** | JSON 배열 | Yes | 약한 인증을 사용 하는 검색 된 응용 프로그램 |

#### <a name="password-fields"></a>암호 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **password** | String | No | - |
| **protocol** | String | No | - |
| **강도가** | String | No | 매우 약함, 약함, Medium 또는 Strong |

#### <a name="remote-access-fields"></a>원격 액세스 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **port** | 숫자 | No | - |
| **트랜스포트가** | String | No | TCP 또는 UDP |
| **클라이언트로** | String | No | IP 주소 |
| **clientSoftware** | String | No | SSH, VNC, 원격 데스크톱 또는 팀 뷰어 |

#### <a name="open-port-fields"></a>포트 필드 열기

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **port** | 숫자 | No | - |
| **트랜스포트가** | String | No | TCP 또는 UDP |
| **protocol** | String | 예 | - |
| **isConflictingWithFirewall** | 부울 | No | True 또는 False |

#### <a name="cve-fields"></a>CVE 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **ID** | String | No | - |
| **점수** | 숫자 | No | Double |
| **description** | String | No | - |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

### <a name="retrieve-security-vulnerabilities"></a>보안 취약성 검색

이 API를 사용 하 여 일반적인 취약성 평가 결과를 요청 합니다. 이 평가는 시스템의 보안 수준에 대 한 통찰력을 제공 합니다.

이 평가는 특정 장치 평가가 아니라 일반적인 네트워크 및 시스템 정보를 기반으로 합니다.

#### <a name="apiv1reportsvulnerabilitiessecurity"></a>/api/v1/reports/vulnerabilities/security

#### <a name="method"></a>메서드

**GET**

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

평가 된 결과를 나타내는 JSON 개체입니다. 각 키는 nullable 일 수 있습니다. 그렇지 않은 경우 null을 허용 하지 않는 키를 포함 하는 JSON 개체를 포함 합니다.

### <a name="result-fields"></a>결과 필드

**키**

**unauthorizedDevices**

| 필드 이름 | 형식 | 값 목록 |
| ---------- | ---- | -------------- |
| **address** | String | IP 주소 |
| **name** | String | - |
| **firstDetectionTime** | 숫자 | Epoch (UTC) |
| lastSeen | 숫자 | Epoch (UTC) |

**illegalTrafficByFirewallRules**

| 필드 이름 | 형식 | 값 목록 |
| ---------- | ---- | -------------- |
| **server** | String | IP 주소 |
| **클라이언트로** | String | IP 주소 |
| **port** | 숫자 | - |
| **트랜스포트가** | String | TCP, UDP 또는 ICMP |

**weakFirewallRules**

| 필드 이름 | 형식 | 값 목록 |
| ---------- | ---- | -------------- |
| **sources** | 소스의 JSON 배열입니다. 각 소스는 네 가지 형식 중 하나일 수 있습니다. | "Any", "ip 주소 (호스트)", "ip에서 ip로 (범위)", "ip 주소, 서브넷 마스크 (네트워크)" |
| **대상** | 대상의 JSON 배열입니다. 각 대상은 네 가지 형식 중 하나일 수 있습니다. | "Any", "ip 주소 (호스트)", "ip에서 ip로 (범위)", "ip 주소, 서브넷 마스크 (네트워크)" |
| **포트** | 3 가지 형식으로 된 포트의 JSON 배열 | "Any", "port (검색 된 경우 프로토콜)", "포트-포트 (프로토콜, 검색 된 경우)" |

**accessPoints**

| 필드 이름 | 형식 | 값 목록 |
| ---------- | ---- | -------------- |
| **macAddress** | String | MAC 주소 |
| **업체인** | String | 공급업체 이름 |
| **\** | String | IP 주소 또는 해당 없음 |
| **name** | String | 장치 이름 또는 해당 없음 |
| **wireless-n** | String | 아니요, 의심 됨 또는 예 |

**connectionsBetweenSubnets**

| 필드 이름 | 형식 | 값 목록 |
| ---------- | ---- | -------------- |
| **server** | String | IP 주소 |
| **클라이언트로** | String | IP 주소 |

**industrialMalwareIndicators**

| 필드 이름 | 형식 | 값 목록 |
| ---------- | ---- | -------------- |
| **detectionTime** | 숫자 | Epoch (UTC) |
| **Alertmessage가 잘못** | String | - |
| **description** | String | - |
| **디바이스가** | JSON 배열 | 디바이스 이름 | 

**internetConnections**

| 필드 이름 | 형식 | 값 목록 |
| ---------- | ---- | -------------- |
| **internalAddress** | String | IP 주소 |
| **승인됨** | 부울 | Yes 또는 No | 
| **externalAddresses** | JSON 배열 | IP 주소 |

#### <a name="response-example"></a>응답 예제

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

### <a name="retrieve-operational-vulnerabilities"></a>운영 취약성 검색

이 API를 사용 하 여 일반적인 취약성 평가 결과를 요청 합니다. 이 평가는 네트워크의 작동 상태에 대 한 통찰력을 제공 합니다. 특정 장치 평가가 아닌 일반적인 네트워크 및 시스템 정보를 기반으로 합니다.

#### <a name="apiv1reportsvulnerabilitiesoperational"></a>/api/v1/reports/vulnerabilities/operational

#### <a name="method"></a>메서드

**GET**

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

평가 된 결과를 나타내는 JSON 개체입니다. 각 키에는 결과의 JSON 배열이 포함 되어 있습니다.

#### <a name="result-fields"></a>결과 필드

**키**

**backupServer**

| 필드 이름 | 형식 | 값 목록 |
|--|--|--|
| **source** | String | IP 주소 |
| **destination** | String | IP 주소 |
| **port** | 숫자 | - |
| **트랜스포트가** | String | TCP 또는 UDP |
| **backupMaximalInterval** | String | - |
| **lastSeenBackup** | 숫자 | Epoch (UTC) |

**ipNetworks**

| 필드 이름 | 형식 | 값 목록 |
|--|--|--|
| **addresse** s | 숫자 | - |
| **network** | String | IP 주소 |
| **마스크할** | String | 서브넷 마스크 |

**protocolProblems**

| 필드 이름 | 형식 | 값 목록 |
|--|--|--|
| **protocol** | String | - |
| **주소로** | JSON 배열 | IP 주소 |
| **오류** | String | - |
| **reportTime** | 숫자 | Epoch (UTC) |

**protocolDataVolumes**

| 필드 이름 | 형식 | 값 목록 |
|--|--|--|
| protocol | String | - |
| 볼륨 | String | "볼륨 번호 MB" |

**연결 끊김**

| 필드 이름 | 형식 | 값 목록 |
|--|--|--|
| **assetAddress** | String | IP 주소 |
| **assetName** | String | - |
| **lastDetectionTime** | 숫자 | Epoch (UTC) |
| **backToNormalTime** | 숫자 | Epoch (UTC) |     

#### <a name="response-example"></a>응답 예제

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

### <a name="validate-user-credentials"></a>사용자 자격 증명 유효성 검사

이 API를 사용 하 여 IoT의 사용자 이름 및 암호에 대 한 유효성을 검사 합니다. IoT 사용자 역할에 대 한 모든 Defender는 API와 함께 작동할 수 있습니다.

이 API를 사용 하는 데 IoT 용 Defender 액세스 토큰이 필요 하지 않습니다.

#### <a name="apiexternalauthenticationvalidation"></a>/sa/cv/cv/validation

#### <a name="method"></a>메서드

**POST**

#### <a name="request-type"></a>요청 유형

**JSON**

#### <a name="query-parameters"></a>쿼리 매개 변수

| **이름** | **형식** | **Null 허용** |
|--|--|--|
| **username** | String | No |
| **password** | String | No |

#### <a name="request-example"></a>요청 예제

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

작업 상태 세부 정보를 포함 하는 메시지 문자열:

- **성공-메시지**: 인증 성공

- **실패-오류**: 자격 증명 유효성 검사에 실패 했습니다.

#### <a name="response-example"></a>응답 예제

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

### <a name="change-password"></a>암호 변경

이 API를 사용 하 여 사용자가 자신의 암호를 변경할 수 있습니다. IoT 사용자 역할에 대 한 모든 Defender는 API와 함께 작동할 수 있습니다. 이 API를 사용 하는 데 IoT 용 Defender 액세스 토큰이 필요 하지 않습니다.

#### <a name="externalauthenticationset_password"></a>/external/authentication/set_password

#### <a name="method"></a>메서드

**POST**

#### <a name="request-type"></a>요청 유형

**JSON**

#### <a name="request-example"></a>요청 예제

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

작업 상태 세부 정보를 포함 하는 메시지 문자열:

- **성공 – 메시지**: 암호가 바뀌었습니다.

- **실패-오류**: 사용자 인증 실패

- **실패 – 오류**: 암호가 보안 정책과 일치 하지 않습니다.

#### <a name="response-example"></a>응답 예제

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>장치 필드

| **이름** | **형식** | **Null 허용** |
|--|--|--|
| **username** | String | No |
| **password** | String | No |
| **new_password** | String | No |

### <a name="user-password-update-by-system-admin"></a>시스템 관리자의 사용자 암호 업데이트

이 API를 사용 하 여 시스템 관리자가 지정 된 사용자에 대 한 암호를 변경할 수 있습니다. IoT 용 Defender 관리자 사용자 역할은 API를 사용할 수 있습니다. 이 API를 사용 하는 데 IoT 용 Defender 액세스 토큰이 필요 하지 않습니다.

#### <a name="externalauthenticationset_password_by_admin"></a>/external/authentication/set_password_by_admin

#### <a name="method"></a>메서드

**POST**

#### <a name="request-type"></a>요청 유형

**JSON**

#### <a name="request-example"></a>요청 예제

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

작업 상태 세부 정보를 포함 하는 메시지 문자열:

- **성공 – 메시지**: 암호가 바뀌었습니다.

- **실패-오류**: 사용자 인증 실패

- **실패-오류**: 사용자가 없습니다.

- **실패 – 오류**: 암호가 보안 정책과 일치 하지 않습니다.

- **실패 – 오류**: 사용자에 게 암호를 변경할 수 있는 권한이 없습니다.

#### <a name="response-example"></a>응답 예제

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>장치 필드

| **이름** | **형식** | **Null 허용** |
|--|--|--|
| **admin_username** | String | No |
| **admin_password** | String | No |
| **username** | String | No |
| **new_password** | String | No |

## <a name="on-premises-management-console-api-specifications"></a>온-프레미스 관리 콘솔 API 사양

이 섹션에서는 다음 온-프레미스 관리 콘솔 Api에 대해 설명 합니다.

- **/external/v1/alerts/<UUID>**

- **경고 제외 (유지 관리 기간)**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="활성 규칙을 보여 주는 경고 제외 창입니다.":::

경고를 보내지 않을 조건을 정의 합니다. 예를 들어 중지 및 시작 시간, 경고를 트리거할 때 제외 되어야 하는 장치 또는 서브넷, 제외 되어야 하는 IoT 엔진의 Defender를 정의 하 고 업데이트 합니다. 예를 들어 유지 관리 기간 동안 중요 한 장치에 대 한 맬웨어 경고를 제외 하 고 모든 경고의 배달을 중지 하는 것이 좋습니다.

여기에서 정의 하는 Api는 온-프레미스 관리 콘솔의 **경고 제외** 창에 읽기 전용 제외 규칙으로 나타납니다.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/cvrv/유효성 검사**

- **응답 예제**

- **응답이**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password"></a>암호 변경

이 API를 사용 하 여 사용자가 자신의 암호를 변경할 수 있습니다. IoT 사용자 역할에 대 한 모든 Defender는 API와 함께 작동할 수 있습니다. 이 API를 사용 하는 데 IoT 용 Defender 액세스 토큰이 필요 하지 않습니다.

- **/external/authentication/set_password**

#### <a name="user-password-update-by-system-admin"></a>시스템 관리자의 사용자 암호 업데이트

이 API를 사용 하 여 시스템 관리자가 특정 사용자에 대 한 암호를 변경할 수 있습니다. IoT 용 Defender 관리 사용자 역할은 API를 사용할 수 있습니다. 이 API를 사용 하는 데 IoT 용 Defender 액세스 토큰이 필요 하지 않습니다.

- **/external/authentication/set_password_by_admin**

### <a name="retrieve-device-information"></a>장치 정보 검색

이 API는 온-프레미스 관리 콘솔에 연결 된 IoT 센서에 대해 Defender에서 검색 한 모든 장치 목록을 요청 합니다.

- **/external/v1/devices**

#### <a name="method"></a>메서드

**GET**

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

장치를 나타내는 JSON 개체의 배열입니다.

#### <a name="query-parameters"></a>쿼리 매개 변수

- **권한 부여** 됨: 허가 되 고 권한이 없는 장치만 필터링 합니다.

- **siteId**: 특정 사이트와 관련 된 장치만 필터링 합니다.

- **zoneId**: 특정 영역과 관련 된 장치만 필터링 합니다. [1](#1)

- **sensorId**: 특정 센서가 검색 한 장치만 필터링 합니다. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *사이트 및 영역 ID가 없을 수 있습니다. 이 경우 모든 장치를 쿼리하여 사이트와 영역 ID를 검색 합니다.*

#### <a name="query-parameters-example"></a>쿼리 매개 변수 예제

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>장치 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **sensorId** | 숫자 | No | - |
| **zoneId** | 숫자 | Yes | - |
| **siteId** | 숫자 | Yes | - |
| **ipAddresses** | JSON 배열 | Yes | IP 주소 (인터넷 주소 또는 이중 Nic를 사용 하는 장치의 경우 두 개 이상의 주소를 사용할 수 있음) |
| **name** | String | No | - |
| **type** | String | No | 알 수 없음, 엔지니어링 스테이션, PLC, HMI, Historian, 도메인 컨트롤러, DB 서버, 무선 액세스 지점, 라우터, 스위치, 서버, 워크스테이션, IP 카메라, 프린터, 방화벽, 터미널 스테이션, VPN Gateway, 인터넷 또는 멀티 캐스트 및 브로드캐스트 |
| **macAddresses** | JSON 배열 | Yes | MAC 주소 (이중 Nic를 사용 하는 장치의 경우 두 개 이상의 주소를 사용할 수 있음) |
| **operatingSystem** | String | 예 | - |
| **engineeringStation** | 부울 | No | True 또는 False |
| **장치가** | 부울 | No | True 또는 False |
| **승인됨** | 부울 | No | True 또는 False |
| **업체인** | String | 예 | - |
| **프로토콜** | JSON 배열 | Yes | 프로토콜 개체 |
| **펌웨어입니다** | JSON 배열 | Yes | 펌웨어 개체 |

#### <a name="protocol-fields"></a>프로토콜 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| Name | String | No | - |
| 주소 | JSON 배열 | Yes | Master 또는 numeric 값 |

#### <a name="firmware-fields"></a>펌웨어 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **연속적인** | String | No | 해당 없음 또는 실제 값 |
| **model** | String | No | 해당 없음 또는 실제 값 |
| **firmwareVersion** | Double | No | 해당 없음 또는 실제 값 |
| **additionalData** | String | No | 해당 없음 또는 실제 값 |
| **moduleAddress** | String | No | 해당 없음 또는 실제 값 |
| **랙마운트** | String | No | 해당 없음 또는 실제 값 |
| **슬롯** | String | No | 해당 없음 또는 실제 값 |
| **address** | String | No | 해당 없음 또는 실제 값 |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

### <a name="retrieve-alert-information"></a>경고 정보 검색

이 API를 사용 하 여 온-프레미스 관리 콘솔에서 모든 또는 필터링 된 경고를 검색 합니다.

#### <a name="externalv1alerts"></a>/external/v1/alerts

#### <a name="method"></a>메서드

**GET**

#### <a name="query-parameters"></a>쿼리 매개 변수

- **상태**: 처리 및 처리 되지 않은 경고만 필터링 합니다.

  **예제**:

  `/api/v1/alerts?state=handled`

- **fromtime**: 특정 시간 (밀리초, UTC)에서 만든 경고를 필터링 합니다.

  **예제**:

  `/api/v1/alerts?fromTime=<epoch>`

- **totime**: 특정 시간 (밀리초, UTC) 전에만 생성 된 경고를 필터링 합니다.

  **예제**:

  `/api/v1/alerts?toTime=<epoch>`

- **siteId**: 경고가 검색 된 사이트입니다. [2](#2)

- **zoneId**: 경고가 검색 된 영역입니다. [2](#2)

- **센서**: 경고가 검색 된 센서입니다.

##### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="2">2</a> *사이트 및 영역 ID가 없을 수 있습니다. 이 경우 모든 장치를 쿼리하여 사이트와 영역 ID를 검색 합니다.*

#### <a name="alert-fields"></a>경고 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **ID** | 숫자 | No | - |
| **time** | 숫자 | No | Epoch (UTC) |
| **title** | String | No | - |
| **message** | String | No | - |
| **severity** | String | No | 경고, 부, 주 또는 위험 |
| **엔진** | String | No | 프로토콜 위반, 정책 위반, 맬웨어, 변칙 또는 작동 |
| **sourceDevice** | 숫자 | Yes | 디바이스 ID |
| **destinationDevice** | 숫자 | Yes | 디바이스 ID |
| **additionalInformation** | 추가 정보 개체 | Yes | - |

#### <a name="additional-information-fields"></a>추가 정보 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **description** | String | No | - |
| **내용을** | JSON 배열 | 예 | String |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

### <a name="qradar-alerts"></a>QRadar 경고

QRadar와 IoT 용 Defender 통합은 IoT 용 Defender에서 생성 된 경고를 식별 하 고 이러한 경고를 사용 하 여 작업을 수행 하는 데 도움이 됩니다. QRadar는 IoT 용 Defender에서 데이터를 받은 다음 공용 API 온-프레미스 관리 콘솔 구성 요소에 연결 합니다.

Defender for IoT에서 검색 한 데이터를 QRadar에 보내려면 IoT 용 Defender 시스템에서 전달 규칙을 정의 하 고 **원격 지원 경고 처리** 옵션을 선택 합니다.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="필요에 맞게 전달 규칙을 편집 합니다.":::

전달 규칙을 구성 하는 과정에서이 옵션을 선택 하면 다음과 같은 추가 필드가 QRadar에 표시 됩니다.

- **UUID**: 고유한 경고 식별자 (예: 1-1555245116250)입니다.

- **사이트**: 경고가 검색 된 사이트입니다.

- **영역**: 경고가 검색 된 영역입니다.

QRadar으로 전송 된 페이로드의 예:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/ &lt; UUID&gt;

#### <a name="method"></a>메서드

**PUT**

#### <a name="request-type"></a>요청 유형

**JSON**

#### <a name="request-content"></a>요청 콘텐츠

UUID를 포함 하는 경고에 대해 수행할 작업을 나타내는 JSON 개체입니다.

#### <a name="action-fields"></a>작업 필드

| Name | Type | Nullable | 값 목록 |
|--|--|--|--|
| **action** | String | No | 핸들 또는 handleAndLearn |

#### <a name="request-example"></a>요청 예제

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

장치를 나타내는 JSON 개체의 배열입니다.

#### <a name="response-fields"></a>응답 필드


| Name | Type | Nullable | Description |
|--|--|--|--|
| **내용/오류** | String | No | 요청에 성공 하면 content 속성이 나타납니다. 그렇지 않으면 오류 속성이 나타납니다. |

#### <a name="possible-content-values"></a>가능한 콘텐츠 값

| 상태 코드 | 콘텐츠 값 | Description |
|--|--|--|
| 200 | 경고 업데이트 요청이 성공적으로 완료 되었습니다. | 업데이트 요청이 성공적으로 완료 되었습니다. 설명이 없습니다. |
| 200 | 경고가 이미 처리 되었습니다 (**handle**). | 경고에 대 한 핸들 요청을 받았을 때 경고가 이미 처리 되었습니다.<br />경고는 **처리** 된 상태로 유지 됩니다. |
| 200 | 경고를 이미 처리 하 고 학습 했습니다 (**Handleandlearn**). | **Handleandlearn** 요청이 수신 될 때 경고가 이미 처리 되 고 학습 되었습니다.<br />경고는 **handledAndLearn** 상태로 유지 됩니다. |
| 200 | 경고가 이미 처리 되었습니다 (**처리** 됨).<br />경고에 대해 핸들 및 학습 (**Handleandlearn**)이 수행 되었습니다. | **Handleandlearn** 요청이 수신 될 때 경고가 이미 처리 되었습니다.<br />이 경고는 **Handleandlearn** 수 있습니다. |
| 200 | 경고를 이미 처리 하 고 학습 했습니다 (**Handleandlearn**). 핸들 요청이 무시 되었습니다. | 경고 처리에 대 한 요청을 받은 경우 경고가 이미 **Handleandlearn** 입니다. 이 경고는 **Handleandlearn** 로 유지 됩니다. |
| 500 | 잘못 된 작업입니다. | 전송 된 작업은 경고에 대해 수행할 수 있는 올바른 작업이 아닙니다. |
| 500 | 예기치 않은 오류가 발생했습니다. | 예기치 않은 오류가 발생했습니다. 이 문제를 해결 하려면 기술 지원 서비스에 문의 하십시오. |
| 500 | 이 UUID에 대 한 경고를 찾을 수 없어 요청을 실행할 수 없습니다. | 지정 된 경고 UUID를 시스템에서 찾을 수 없습니다. |

#### <a name="response-example"></a>응답 예제

**성공**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**실패**

```rest
{
    "error": "Invalid action"
}
```

### <a name="alert-exclusions-maintenance-window"></a>경고 제외 (유지 관리 기간)

경고를 보내지 않을 조건을 정의 합니다. 예를 들어 중지 및 시작 시간, 경고를 트리거할 때 제외 되어야 하는 장치 또는 서브넷, 제외 되어야 하는 IoT 엔진의 Defender를 정의 하 고 업데이트 합니다. 예를 들어 유지 관리 기간 동안 중요 한 장치에 대 한 맬웨어 경고를 제외 하 고 모든 경고의 경고 배달을 중지할 수 있습니다.

여기에서 정의 하는 Api는 온-프레미스 관리 콘솔의 **경고 제외** 창에 읽기 전용 제외 규칙으로 나타납니다.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="모든 제외 규칙의 목록을 보여 주는 경고 제외 창 ":::

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

#### <a name="method---post"></a>메서드-POST

#### <a name="query-parameters"></a>쿼리 매개 변수

- **ticketId**: 사용자 시스템의 유지 관리 티켓 ID를 정의 합니다.

- **ttl**: 유지 관리 기간의 시간 (분) 인 ttl (time to live)을 정의 합니다. 이 매개 변수가 정의 된 기간 후에 시스템에서 자동으로 경고를 보내기 시작 합니다.

- **엔진**: 유지 관리 프로세스 중에 경고를 표시 하지 않을 보안 엔진을 정의 합니다.

   - 예외적

   - 악성

   - 주기와

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds**: 유지 관리 프로세스 중에 경고를 표시 하지 않을 IoT 센서의 Defender를 정의 합니다. /Api/v1/appliances (GET)에서 검색 된 것과 동일한 ID입니다.

- **서브넷**: 유지 관리 프로세스 중에 경고를 표시 하지 않을 서브넷을 정의 합니다. 서브넷은 192.168.0.0/16 형식으로 전송 됩니다.

#### <a name="error-codes"></a>오류 코드

- **201 (만들어짐)**: 작업이 성공적으로 완료 되었습니다.

- **400 (잘못 된 요청)**: 다음과 같은 경우에 나타납니다.

   - **Ttl** 매개 변수가 숫자가 아니거나 양수가 아닙니다.

   - **서브넷** 매개 변수가 잘못 된 형식을 사용 하 여 정의 되었습니다.

   - **TicketId** 매개 변수가 없습니다.

   - **엔진** 매개 변수가 기존 보안 엔진과 일치 하지 않습니다.

- **404 (찾을 수 없음)**: 센서 중 하나가 존재 하지 않습니다.

- **409 (충돌)**: 티켓 ID는 다른 열려 있는 유지 관리 창에 연결 됩니다.

- **500 (내부 서버 오류)**: 다른 예기치 않은 오류가 발생 했습니다.

> [!NOTE]
> 티켓 ID가 기존에 열려 있는 창에 연결 되지 않았는지 확인 합니다. 다음 제외 규칙이 생성 됩니다. 유지 관리-{token name}-{티켓 ID}.

#### <a name="method---put"></a>메서드-PUT

**Ttl** 매개 변수를 변경 하 여 유지 관리 프로세스를 시작한 후 유지 관리 기간 업데이트를 허용 합니다. 새 기간 정의가 이전 기간 정의를 재정의 합니다.

이 메서드는 현재 구성 된 기간 보다 긴 기간을 설정 하려는 경우에 유용 합니다.

#### <a name="query-parameters"></a>쿼리 매개 변수

- **ticketId**: 사용자 시스템의 유지 관리 티켓 ID를 정의 합니다.

- **ttl**: 기간 (분)을 정의 합니다.

#### <a name="error-code"></a>오류 코드

- **200 (OK)**: 작업이 성공적으로 완료 되었습니다.

- **400 (잘못 된 요청)**: 다음과 같은 경우에 나타납니다.

   - **Ttl** 매개 변수가 숫자가 아니거나 양수가 아닙니다.

   - **TicketId** 매개 변수가 없습니다.

   - **Ttl** 매개 변수가 없습니다.

- **404 (찾을 수 없음)**: 티켓 ID가 열린 유지 관리 창에 연결 되어 있지 않습니다.

- **500 (내부 서버 오류)**: 다른 예기치 않은 오류가 발생 했습니다.

> [!NOTE]
> 티켓 ID가 기존에 열려 있는 창에 연결 되어 있는지 확인 합니다.

#### <a name="method---delete"></a>메서드-삭제

기존 유지 관리 기간을 닫습니다.

#### <a name="query-parameters"></a>쿼리 매개 변수

- **ticketId**: 사용자 시스템의 유지 관리 티켓 ID를 기록 합니다.

#### <a name="error-code"></a>오류 코드

- **200 (OK)**: 작업이 성공적으로 완료 되었습니다.

- **400 (잘못 된 요청)**: **ticketId** 매개 변수가 없습니다.

- **404 (찾을 수 없음)**: 티켓 ID가 열린 유지 관리 창에 연결 되어 있지 않습니다.

- **500 (내부 서버 오류)**: 다른 예기치 않은 오류가 발생 했습니다.

> [!NOTE]
> 티켓 ID가 기존에 열려 있는 창에 연결 되어 있는지 확인 합니다.

#### <a name="method---get"></a>메서드-GET

유지 관리 중 시스템에서 수행 된 모든 열기, 닫기 및 업데이트 작업의 로그를 검색 합니다. 이전에 활성화 되었으며 종결 된 유지 관리 기간에 대해서만 로그를 검색할 수 있습니다.

#### <a name="query-parameters"></a>쿼리 매개 변수

- **fromDate**: 미리 정의 된 날짜 이상에서 로그를 필터링 합니다. 형식은 2019-12-30입니다.

- **toDate**: 미리 정의 된 날짜로 로그를 필터링 합니다. 형식은 2019-12-30입니다.

- **ticketId**: 특정 티켓 ID와 관련 된 로그를 필터링 합니다.

- **tokenName**: 특정 토큰 이름과 관련 된 로그를 필터링 합니다.

#### <a name="error-code"></a>오류 코드

- **200 (OK)**: 작업이 성공적으로 완료 되었습니다.

- **400 (잘못 된 요청)**: 날짜 형식이 잘못 되었습니다.

- **204 (콘텐츠 없음)**: 표시할 데이터가 없습니다.

- **500 (내부 서버 오류)**: 다른 예기치 않은 오류가 발생 했습니다.

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

유지 관리 기간 작업을 나타내는 JSON 개체의 배열입니다.

#### <a name="response-structure"></a>응답 구조

| Name | Type | 의견 | Nullable |
|--|--|--|--|
| **dateTime** | String | 예: "2012-04-23T18:25:43.511 Z" | 아니요 |
| **ticketId** | String | 예: "9a5fe99c-d914-4bda-9332-307384fe40bf" | 아니요 |
| **tokenName** | String | - | 아니요 |
| **엔진** | 문자열 배열 | - | 예 |
| **sensorIds** | 문자열 배열 | - | 예 |
| **서브넷** | 문자열 배열 | - | 예 |
| **ttl** | 숫자 | - | 예 |
| **operationType** | String | 값은 "OPEN", "UPDATE" 및 "CLOSE"입니다. | 아니요 |

### <a name="authenticate-user-credentials"></a>사용자 자격 증명 인증

이 API를 사용 하 여 사용자 자격 증명의 유효성을 검사 합니다. IoT 사용자 역할에 대 한 모든 Defender는 API와 함께 작동할 수 있습니다. 이 API를 사용 하는 데 IoT 용 Defender 액세스 토큰이 필요 하지 않습니다.

#### <a name="externalauthenticationvalidation"></a>/external/cvrv/유효성 검사

#### <a name="method"></a>메서드

**POST**

#### <a name="request-type"></a>요청 유형

**JSON**

#### <a name="request-example"></a>요청 예제

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

작업 상태 세부 정보를 포함 하는 메시지 문자열:

- **성공 – 메시지**: 인증 성공

- **실패 – 오류**: 자격 증명의 유효성을 검사 하지 못했습니다.

#### <a name="device-fields"></a>장치 필드

| **이름** | **형식** | **Null 허용** |
|--|--|--|
| **username** | String | No |
| **password** | String | No |

#### <a name="response-example"></a>응답 예제

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

### <a name="change-password"></a>암호 변경

이 API를 사용 하 여 사용자가 자신의 암호를 변경할 수 있습니다. IoT 사용자 역할에 대 한 모든 Defender는 API와 함께 작동할 수 있습니다. 이 API를 사용 하는 데 IoT 용 Defender 액세스 토큰이 필요 하지 않습니다.

#### <a name="externalauthenticationset_password"></a>/external/authentication/set_password

#### <a name="method"></a>메서드

**POST**

#### <a name="request-type"></a>요청 유형

**JSON**

#### <a name="request-example"></a>요청 예제

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

작업 상태 세부 정보를 포함 하는 메시지 문자열:

- **성공 – 메시지**: 암호가 바뀌었습니다.

- **실패-오류**: 사용자 인증 실패

- **실패 – 오류**: 암호가 보안 정책과 일치 하지 않습니다.

#### <a name="response-example"></a>응답 예제

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>장치 필드

| **이름** | **형식** | **Null 허용** |
|--|--|--|
| **username** | String | No |
| **password** | String | No |
| **new_password** | String | No |

### <a name="user-password-update-by-system-admin"></a>시스템 관리자의 사용자 암호 업데이트

이 API를 사용 하 여 시스템 관리자가 지정 된 사용자에 대 한 암호를 변경할 수 있습니다. IoT 용 Defender 관리 사용자 역할은 API를 사용할 수 있습니다. 이 API를 사용 하는 데 IoT 용 Defender 액세스 토큰이 필요 하지 않습니다.

#### <a name="externalauthenticationset_password_by_admin"></a>/external/authentication/set_password_by_admin

#### <a name="method"></a>메서드

**POST**

#### <a name="request-type"></a>요청 유형

**JSON**

#### <a name="request-example"></a>요청 예제

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>응답 형식

**JSON**

#### <a name="response-content"></a>응답 콘텐츠

작업 상태 세부 정보를 포함 하는 메시지 문자열:

- **성공 – 메시지**: 암호가 바뀌었습니다.

- **실패-오류**: 사용자 인증 실패

- **실패-오류**: 사용자가 없습니다.

- **실패 – 오류**: 암호가 보안 정책과 일치 하지 않습니다.

- **실패 – 오류**: 사용자에 게 암호를 변경할 수 있는 권한이 없습니다.

#### <a name="response-example"></a>응답 예제

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>장치 필드

| **이름** | **형식** | **Null 허용** |
|--|--|--|
| **admin_username** | String | No |
| **admin_password** | String | No |
| **username** | String | No |
| **new_password** | String | 예 |

## <a name="see-also"></a>참고 항목
[장치 인벤토리에서](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 센서 검색 조사 [장치 인벤토리에서 모든 엔터프라이즈 센서 검색 조사](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
