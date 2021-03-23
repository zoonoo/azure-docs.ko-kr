---
title: CEF (일반 이벤트 형식) 키 및 CommonSecurityLog 필드 매핑
description: 이 문서에서는 CEF 키를 Azure 센티널에서 CommonSecurityLog의 해당 필드 이름에 매핑합니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 6c23fe86af030d371e12914062bb9558e8db3484
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104776303"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>CEF 및 CommonSecurityLog 필드 매핑

다음 표에서는 CEF (Common Event Format) 필드 이름을 Azure 센티널의 CommonSecurityLog에서 사용 하는 이름에 매핑하고, Azure 센티널에서 CEF 데이터 원본을 사용 하 여 작업할 때 유용할 수 있습니다.

자세한 내용은 [공용 이벤트 형식을 사용 하 여 외부 솔루션 연결](connect-common-event-format.md)을 참조 하세요.

## <a name="a---c"></a>A - C

|CEF 키 이름  |CommonSecurityLog 필드 이름  |Description  |
|---------|---------|---------|
| 역할    |    <a name="deviceaction"></a> DeviceAction     |  이벤트에 언급 된 작업입니다.       |
|   app  |    ApplicationProtocol     |  응용 프로그램에 사용 되는 프로토콜 (예: HTTP, HTTPS, SSHv2, Telnet, POP,  a, IMAPS 등)입니다.   |
| cnt    |    EventCount     |  동일한 이벤트가 관찰 된 횟수를 표시 하는 이벤트와 연결 된 개수입니다.       |
| | | |

## <a name="d"></a>D

|CEF 키 이름  |CommonSecurityLog 이름  |Description  |
|---------|---------|---------|
|장치 공급 업체     |  DeviceVendor       | 장치 제품 및 버전 정의와 함께 전송 장치 유형을 고유 하 게 식별 하는 문자열입니다.       |
|장치 제품     |   DeviceProduct      |   장치 공급 업체 및 버전 정의와 함께 전송 장치 유형을 고유 하 게 식별 하는 문자열입니다.        |
|장치 버전     |   DeviceVersion      |      장치 제품 및 공급 업체 정의와 함께 전송 장치 유형을 고유 하 게 식별 하는 문자열입니다.     |
|DeviceEventClassID     |   DeviceEventClassID     |   이벤트 유형별 고유 식별자 역할을 하는 문자열 또는 정수입니다.      |
| destinationDnsDomain    | DestinationDnsDomain        |   FQDN (정규화 된 도메인 이름)의 DNS 부분입니다.      |
| destinationServiceName | DestinationServiceName | 이벤트의 대상이 되는 서비스입니다. 예들 들어 `sshd`입니다.|
| destinationTranslatedAddress | DestinationTranslatedAddress | IP 네트워크에서 이벤트에 의해 참조 되는 변환 된 대상을 IPv4 IP 주소로 식별 합니다. |
| destinationTranslatedPort | DestinationTranslatedPort | 포트 (변환 후 방화벽) <br>유효한 포트 번호: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | 관찰 된 통신이 수행 된 방향에 대 한 정보입니다. 유효한 값은 <br>- `0` = 인바운드 <br>- `1` = 아웃 바운드 |
| deviceDnsDomain | DeviceDnsDomain | FQDN (정규화 된 도메인 이름)의 DNS 도메인 부분 |
| deviceExternalID | DeviceExternalID | 이벤트를 생성 하는 장치를 고유 하 게 식별 하는 이름입니다. |
| deviceFacility | DeviceFacility | 이벤트를 생성 하는 기능입니다.|
| deviceInboundInterface | DeviceInboundInterface |패킷 또는 데이터가 장치에 입력 한 인터페이스입니다.  |
| deviceNtDomain | DeviceNtDomain | 장치 주소의 Windows 도메인 |
| deviceOutboundInterface | DeviceOutboundInterface |패킷이 장치에서 남겨진 인터페이스입니다. |
| devicePayloadId |DevicePayloadId |이벤트와 연결 된 페이로드의 고유 식별자입니다. |
| deviceProcessName | ProcessName | 이벤트와 연결 된 프로세스 이름입니다. <br><br>예를 들어 UNIX에서 syslog 항목을 생성 하는 프로세스입니다. |
| deviceTranslatedAddress | DeviceTranslatedAddress | IP 네트워크에서 이벤트가 참조 하는 변환 된 장치 주소를 식별 합니다. <br><br>Ipv4 주소 형식입니다. |
| i 호스트 |DestinationHostName | IP 네트워크에서 이벤트가 참조 하는 대상입니다.  <br>노드를 사용할 수 있는 경우이 형식은 대상 노드와 연결 된 FQDN 이어야 합니다. 예를 들어 `host.domain.com` 또는 `host`입니다. |
| emac | DestinationMacAddress | 대상 MAC 주소 (FQDN) |
| dntdom | DestinationNTDomain | 대상 주소의 Windows 도메인 이름입니다.|
| dpid | DestinationProcessId |이벤트와 연결 된 대상 프로세스의 ID입니다.|
| d priv | DestinationUserPrivileges | 대상 사용 권한을 정의 합니다. <br>유효한 값: `Admninistrator` , `User` , `Guest` |
| 6proc | DestinationProcessName | 이벤트 대상 프로세스의 이름입니다 (예: 또는). `telnetd``sshd.` |
| user | DestinationPort | 대상 포트입니다. <br>유효한 값: `*0` - `65535` |
| 대상 | DestinationIP | IP 네트워크에서 이벤트가 참조 하는 대상 IpV4 주소입니다. |
| dtz | DeviceTimeZon | 이벤트를 생성 하는 장치의 표준 시간대 |
| duid |DestinationUserId | ID를 사용 하 여 대상 사용자를 식별 합니다. |
| 사용자 | DestinationUserName |이름으로 대상 사용자를 식별 합니다.|
| dvc | DeviceAddress | 이벤트를 생성 하는 장치의 IPv4 주소입니다. |
| dvchost | DeviceName | 노드를 사용할 수 있는 경우 장치 노드와 연결 된 FQDN입니다. 예를 들어 `host.domain.com` 또는 `host`입니다.| 
| dvcmac | DeviceMacAddress | 이벤트를 생성 하는 장치의 MAC 주소입니다. |
| dvcpid | 프로세스 ID | 이벤트를 생성 하는 장치에서 프로세스의 ID를 정의 합니다. |

## <a name="e---i"></a>E-I

|CEF 키 이름  |CommonSecurityLog 이름  |Description  |
|---------|---------|---------|
|end     |  EndTime       | 이벤트와 관련 된 활동이 종료 된 시간입니다.        |
|externalId    |   ExternalID      | 원본 장치에서 사용 하는 ID입니다. 일반적으로 이러한 값은 각각 이벤트와 연결 된 값을 늘립니다.        |
|fileCreateTime     |  FileCreateTime      | 파일이 만들어진 시간입니다.        |
|Get-filehash     |   FileHash      |   파일의 해시입니다.      |
|fileId     |   FileID      |Inode와 같이 파일에 연결 된 ID입니다.         |
| fileModificationTime | FileModificationTime |파일이 마지막으로 수정 된 시간입니다. |
| filePath | FilePath | 파일 이름을 포함 하는 파일의 전체 경로입니다. 예를 들어 `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` 또는 `/usr/bin/zip`입니다.|
| filePermission |FilePermission |파일의 사용 권한입니다. |
| fileType | FileType | 파이프, 소켓 등의 파일 형식입니다.|
|fname | FileName| 경로 없이 파일의 이름입니다. |
| fsize | FileSize | 파일의 크기입니다. |
|호스트    |  Computer       | 호스트, Syslog        |
|in     |  ReceivedBytes      |인바운드 전송 된 바이트 수입니다.         |
| | | |

## <a name="m---p"></a>M-P

|CEF 키 이름  |CommonSecurityLog 이름  |Description  |
|---------|---------|---------|
|msg   |  메시지       | 이벤트에 대 한 자세한 정보를 제공 하는 메시지입니다.        |
|Name     |  활동       |   사용자가 읽고 이해할 수 있는 이벤트 설명을 나타내는 문자열입니다.     |
|oldFileCreateTime     |  OldFileCreateTime       | 이전 파일을 만든 시간입니다.        |
|oldFileHash     |   OldFileHash      |   이전 파일의 해시입니다.      |
|oldFileId     |   OldFileId     |   이전 파일 (예: inode)과 연결 된 ID입니다.      |
| oldFileModificationTime | OldFileModificationTime |이전 파일이 마지막으로 수정 된 시간입니다. |
| oldFileName |  OldFileName |이전 파일의 이름입니다. |
| oldFilePath | OldFilePath | 파일 이름을 포함 하 여 이전 파일의 전체 경로입니다. <br>예를 들어 `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` 또는 `/usr/bin/zip`입니다.|
| oldFilePermission | OldFilePermission |이전 파일의 권한입니다. |
|oldFileSize | OldFileSize | 이전 파일의 크기입니다.|
| oldFileType | OldFileType | 파이프, 소켓 등 이전 파일의 파일 형식입니다.|
| out | SentBytes | 아웃 바운드 전송 된 바이트 수입니다. |
| 결과 | 결과 | 또는와 같은 이벤트의 결과입니다 `success` `failure` .|
|proto    |  프로토콜       | 사용 된 계층 4 프로토콜을 식별 하는 전송 프로토콜입니다. <br><br>가능한 값에는 또는와 같은 프로토콜 이름이 포함 됩니다 `TCP` `UDP` .        |
| | | |

## <a name="r---t"></a>R-T

|CEF 키 이름  |CommonSecurityLog 이름  |설명  |
|---------|---------|---------|
|이유     |  이유      |감사 이벤트가 생성 된 이유입니다. <br><br>예를 들어 `Bad password` 또는 `Unknown user`입니다.         |
|요청     |   RequestURL      | 프로토콜을 포함 하 여 HTTP 요청에 대해 액세스 되는 URL입니다. 예를 들어 `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   요청과 연결 된 사용자 에이전트입니다.      |
| requestContext | RequestContext | HTTP 참조 페이지와 같이 요청이 시작 된 콘텐츠를 설명 합니다. |
| requestCookies | RequestCookies |요청과 연결 된 쿠키입니다. |
| requestMethod | RequestMethod | URL에 액세스 하는 데 사용 되는 메서드입니다. <br><br>유효한 값에는, 등의 메서드가 포함 됩니다 `POST` `GET` . |
| rt | ReceiptTime | 작업과 관련 된 이벤트를 받은 시간입니다. |
|심각도     |  <a name="logseverity"></a> LogSeverity       |  이벤트의 중요도를 설명 하는 문자열 또는 정수입니다.<br><br> 유효한 문자열 값: `Unknown` , `Low` , `Medium` , `High` , `Very-High` <br><br>유효한 정수 값은 `0` - `3` = Low, `4` - `6` = Medium, `7` - `8` = High, `9` - `10` = Very-High입니다. |
| shost    | SourceHostName        |이벤트가 IP 네트워크에서 참조 하는 원본을 식별 합니다. 노드를 사용할 수 있는 경우 형식은 원본 노드와 연결 된 DQDN (정규화 된 도메인 이름) 이어야 합니다. 예를 들어 `host` 또는 `host.domain.com`입니다. |
| smac | SourceMacAddress | 원본 MAC 주소입니다. |
| sntdom | SourceNTDomain | 원본 주소에 대 한 Windows 도메인 이름입니다. |
| sourceDnsDomain | SourceDnsDomain | 전체 FQDN의 DNS 도메인 부분입니다. |
| sourceServiceName | SourceServiceName | 이벤트 생성을 담당 하는 서비스입니다. |
| sourceTranslatedAddress | SourceTranslatedAddress | IP 네트워크에서 이벤트가 참조 하는 번역 된 원본을 식별 합니다. |
| sourceTranslatedPort | SourceTranslatedPort | 변환 후의 원본 포트 (예: 방화벽) <br>유효한 포트 번호는 `0`  -  `65535` 입니다. |
| spid | SourceProcessId | 이벤트와 연결 된 원본 프로세스의 ID입니다.|
| spriv | SourceUserPrivileges | 원본 사용자의 권한입니다. <br><br>유효한 값은 다음과 같습니다. `Administrator` , `User` , `Guest` |
| 프로시저 | SourceProcessName | 이벤트 원본 프로세스의 이름입니다.|
| spt | SourcePort | 원본 포트 번호입니다. <br>유효한 포트 번호는 `0`  -  `65535` 입니다. |
| src | SourceIP |IP 네트워크에서 이벤트가 참조 하는 원본으로, IPv4 주소입니다. |
| start | StartTime | 이벤트에서 참조 하는 작업이 시작 되는 시간입니다. |
| suid | SourceUserID | ID로 원본 사용자를 식별 합니다. |
| 형식 | EventType | 이벤트 유형입니다. 값은 다음과 같습니다. <br>- `0`: 기본 이벤트 <br>- `1`: 집계 됨 <br>- `2`: 상관 관계 이벤트 <br>- `3`: action 이벤트 <br><br>**참고**:이 이벤트는 기본 이벤트에 대해 생략할 수 있습니다. |
| | | |

## <a name="unmapped-fields"></a>매핑되지 않은 필드

다음 **CommonSecurityLog** 필드 이름에는 cef 키에 대 한 매핑이 없습니다.


|CommonSecurityLog 필드 이름  |Description  |
|---------|---------|
|**OriginalLogSeverity**     |  CiscoASA와의 통합에 대해 지원 되는 항상 비어 있습니다. <br>로그 심각도 값에 대 한 자세한 내용은 [logseverity](#logseverity) 필드를 참조 하세요.       |
|**RemoteIP**     |     원격 IP 주소입니다. <br>가능 하면이 값은 [CommunicationDirection](#communicationdirection) 필드를 기반으로 합니다.     |
|**Server.remoteport**     |   원격 포트입니다. <br>가능 하면이 값은 [CommunicationDirection](#communicationdirection) 필드를 기반으로 합니다.      |
|**SimplifiedDeviceAction**     |   [Deviceaction 필드의](#deviceaction) 원래 값을 유지 하면서 정적 값 집합에 대 한 [deviceaction](#deviceaction) 값을 단순화 합니다. <br>예를 들면와 같습니다 `Denied`  >  `Deny` .      |
|     |         |


## <a name="next-steps"></a>다음 단계

자세한 내용은 [공용 이벤트 형식을 사용 하 여 외부 솔루션 연결](connect-common-event-format.md)을 참조 하세요.
