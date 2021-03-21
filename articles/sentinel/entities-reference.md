---
title: Azure 센티널 엔터티 형식 참조 | Microsoft Docs
description: 이 문서에서는 Azure 센티널 엔터티 형식 및 필수 식별자를 표시 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 17a4df3037f9922d92fca924de0d246458cfa08e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456306"
---
# <a name="azure-sentinel-entity-types-reference"></a>Azure 센티널 엔터티 형식 참조

## <a name="entity-types-and-identifiers"></a>엔터티 형식 및 식별자

다음 표에서는 현재 Azure 센티널에서 매핑에 사용할 수 있는 **엔터티 형식** 및 각 엔터티 형식에 대 한 **식별자** 로 사용할 수 있는 특성을 보여 줍니다 .이 **특성** 은 [분석 규칙 마법사](tutorial-detect-threats-custom.md)의 [엔터티 매핑](map-data-fields-to-entities.md) 섹션에 있는 **식별자** 드롭다운 목록에 표시 됩니다.

**필수 식별자** 열의 식별자 중 하나는 해당 엔터티를 식별 하는 데 최소한 필요 합니다. 그러나 필수 식별자 만으로는 *고유한* id를 제공 하기에 충분 하지 않을 수 있습니다. 사용 되는 식별자가 많을 수록 고유 id의 가능성이 높아집니다. 단일 엔터티 매핑에 대해 최대 3 개의 식별자를 사용할 수 있습니다.

최상의 결과를 위해 보장 된 고유 id를 사용 하려면 가능한 경우 **가장 강력한 식별자** 열의 식별자를 사용 해야 합니다. 여러 강력한 식별자를 사용 하면 다양 한 데이터 원본 및 스키마에서 강력한 식별자 간의 상관 관계를 설정할 수 있습니다. 이렇게 하면 Azure 센티널에서 지정 된 엔터티에 대 한 보다 포괄적인 정보를 제공할 수 있습니다.

| 엔터티 유형 | 식별자 | 필수 식별자 | 가장 강력한 식별자 |
| - | - | - | - |
| [**사용자 계정**](#user-account)<br>*계정일* | Name<br>FullName<br>NTDomain<br>DnsDomain<br>UPNSuffix<br>Sid<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined 됨<br>DisplayName<br>ObjectGuid | FullName<br>Sid<br>Name<br>AadUserId<br>PUID<br>ObjectGuid | 이름 + NTDomain<br>이름 + UPNSuffix<br>AADUserId<br>Sid |
| [**호스트**](#host) | DnsDomain<br>NTDomain<br>HostName<br>FullName<br>NetBiosName<br>AzureID<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined 됨 | FullName<br>HostName<br>NetBiosName<br>AzureID<br>OMSAgentID | 호스트 이름 + NTDomain<br>호스트 이름 + DnsDomain<br>NetBiosName + NTDomain<br>NetBiosName + DnsDomain<br>AzureID<br>OMSAgentID |
| [**IP 주소**](#ip-address)<br>*TCP/IP* | 주소 | 주소 | |
| [**맬웨어**](#malware) | 속성<br>Category | Name | |
| [**File**](#file) | 디렉터리<br>속성 | 속성 | |
| [**Process**](#process) | ProcessId<br>명령줄<br>ElevationToken<br>CreationTimeUtc | 명령줄<br>ProcessId | |
| [**클라우드 응용 프로그램**](#cloud-application)<br>*(CloudApplication)* | AppId<br>Name<br>InstanceName | AppId<br>Name | |
| [**도메인 이름**](#domain-name)<br>*DNS* | DomainName | DomainName | |
| [**Azure 리소스**](#azure-resource) | ResourceId | ResourceId | |
| [**파일 해시**](#file-hash)<br>*Get-filehash* | 알고리즘<br>값 | 알고리즘 + 값 | |
| [**레지스트리 키**](#registry-key) | Hive<br>키 | Hive<br>키 | Hive + 키 |
| [**레지스트리 값**](#registry-value) | Name<br>값<br>ValueType | Name | |
| [**보안 그룹**](#security-group) | DistinguishedName<br>SID<br>ObjectGuid | DistinguishedName<br>SID<br>ObjectGuid | |
| [**URL**](#url) | Url | Url | |
| [**IoT 장치**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>원본<br>SourceRef<br>제조업체<br>모델<br>OperatingSystem<br>IpAddress<br>MacAddress<br>프로토콜<br>SerialNumber | IoTHub<br>DeviceId | IoTHub + DeviceId |
| [**편지함**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>Upn<br>Msds-externaldirectoryobjectid<br>RiskLevel | MailboxPrimaryAddress | |
| [**메일 클러스터**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>위협<br>쿼리<br>QueryTime<br>MailCount<br>IsVolumeAnomaly<br>원본<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>Get-clustergroup | 쿼리<br>원본 | 쿼리 + 소스 |
| [**메일 메시지**](#mail-message) | 받는 사람<br>Urls<br>위협<br>보낸 사람<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>이름별<br>NetworkMessageId<br>InternetMessageId<br>주체<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>위조 방지<br>DeliveryAction<br>DeliveryLocation<br>언어<br>ThreatDetectionMethods | NetworkMessageId<br>받는 사람 | NetworkMessageId + 받는 사람 |
| [**전송 메일**](#submission-mail) | SubmissionId<br>SubmissionDate<br>제출자<br>NetworkMessageId<br>타임스탬프<br>받는 사람<br>보낸 사람<br>SenderIp<br>주체<br>ReportType | SubmissionId<br>NetworkMessageId<br>받는 사람<br>제출자 |  |
|

## <a name="entity-type-schemas"></a>엔터티 형식 스키마

다음은 각 엔터티 형식의 전체 스키마를 자세히 살펴보는 것입니다. 이러한 스키마 중 상당수는 다른 엔터티 형식에 대 한 링크를 포함 하 고 있습니다. 예를 들어 사용자 계정 스키마에는 호스트 엔터티 형식에 대 한 링크가 포함 되어 있습니다. 예를 들어 사용자 계정의 한 특성이 정의 된 호스트로 사용 됩니다. 이러한 외부에 연결 된 엔터티는 엔터티 매핑의 식별자로 사용할 수 없지만 엔터티 페이지 및 조사 그래프의 엔터티에 대 한 전체 그림을 제공 하는 데 매우 유용 합니다.

> [!NOTE]
> **유형** 열의 값 뒤에 있는 물음표는 필드가 null을 허용 함을 나타냅니다.

## <a name="user-account"></a>사용자 계정

*엔터티 이름: 계정*

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | 계정일 |
| Name | String | 계정의 이름입니다. 이 필드에는 도메인을 추가 하지 않은 이름만 포함 해야 합니다. |
| *아니어야* | *해당 사항 없음* | *이전 버전의 엔터티 매핑과의 호환성을 위해 포함 된 스키마의 일부가 아닙니다.*
| NTDomain | String | 경고 형식에 표시 되는 NETBIOS 도메인 이름 – domain\username 예: 재무, NT 기관 |
| DnsDomain | String | 정규화 된 도메인 DNS 이름입니다. 예: finance.contoso.com |
| UPNSuffix | String | 계정의 사용자 계정 이름 접미사입니다. 일부 경우에는 도메인 이름 이기도 합니다. 예: contoso.com |
| 호스트 | 엔터티 | 계정이 포함 된 호스트로, 로컬 계정인 경우입니다. |
| Sid | String | 계정 보안 식별자입니다 (예: S-1-5-18). |
| AadTenantId | Eid? | Azure AD 테 넌 트 ID (알려진 경우)입니다. |
| AadUserId | Eid? | Azure AD 계정 개체 ID (알려진 경우)입니다. |
| PUID | Eid? | Azure AD Passport 사용자 ID (알려진 경우)입니다. |
| IsDomainJoined 됨 | Bool? | 도메인 계정 인지 여부를 확인 합니다. |
| DisplayName | String | 계정의 표시 이름입니다. |
| ObjectGuid | Eid? | ObjectGUID 특성은 Active Directory에 의해 할당 되는 개체의 고유 식별자 인 단일 값 특성입니다. |
|

계정 엔터티의 강력한 식별자:

- 이름 + UPNSuffix
- AadUserId
- Sid + 호스트 (기본 제공 계정의 Sid에 필요 함)
- Sid (기본 제공 계정의 Sid 제외)
- 이름 + NTDomain (NTDomain가 기본 도메인 (예: "Workgroup") 인 경우는 제외)
- 이름 + 호스트 (NTDomain가 기본 도메인 (예: "Workgroup") 인 경우)
- 이름 + DnsDomain
- PUID
- ObjectGuid

계정 엔터티의 약한 식별자:

- Name

## <a name="host"></a>호스트

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | 호스팅하기 |
| DnsDomain | String | 이 호스트가 속한 DNS 도메인입니다. 도메인에 대 한 전체 DNS 접미사 (알려진 경우)를 포함 해야 합니다. |
| NTDomain | String | 이 호스트가 속한 NT 도메인입니다. |
| HostName | String | 도메인 접미사가 없는 호스트 이름입니다. |
| *아니어야* | *해당 사항 없음* | *이전 버전의 엔터티 매핑과의 호환성을 위해 포함 된 스키마의 일부가 아닙니다.*
| NetBiosName | String | 호스트 이름 (Windows 이전 2000)입니다. |
| IoTDevice | 엔터티 | IoT 장치 엔터티 (이 호스트가 IoT 장치를 나타내는 경우). |
| AzureID | String | VM의 Azure 리소스 ID (알려진 경우)입니다. |
| OMSAgentID | String | Oms 에이전트 ID 이며, 호스트에 OMS 에이전트가 설치 되어 있는 경우입니다. |
| OSFamily | 열거형? | 다음 값 중 하나입니다. <li>Linux<li>Windows<li>Android<li>IOS |
| OSVersion | String | 운영 체제의 자유 텍스트 표현입니다.<br>이 필드는 OSFamily 보다 더 세분화 된 특정 버전을 보유 하거나 OSFamily 열거에서 지원 하지 않는 미래 값을 보유 하 고 있습니다. |
| IsDomainJoined 됨 | Bool | 이 호스트가 도메인에 속하는지 여부를 확인 합니다. |
|

호스트 엔터티의 강력한 식별자:
- 호스트 이름 + NTDomain
- 호스트 이름 + DnsDomain
- NetBiosName + NTDomain
- NetBiosName + DnsDomain
- AzureID
- OMSAgentID
- IoTDevice (엔터티 매핑에 지원 되지 않음)

호스트 엔터티의 약한 식별자:
- HostName
- NetBiosName

## <a name="ip-address"></a>IP 주소

*엔터티 이름: IP*

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | tcp/ip |
| 주소 | String | 문자열 (예: IPv4 또는 IPv6)의 IP 주소입니다. |
| 위치 | GeoLocation | IP 엔터티에 연결 된 지리적 위치 컨텍스트입니다. |
|

IP 엔터티의 강력한 식별자:
- 주소

## <a name="malware"></a>맬웨어

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | 악성 |
| Name | String | 공급 업체의 맬웨어 이름 (예:) `Win32/Toga!rfn` 입니다. |
| 범주 | String | 공급 업체의 맬웨어 범주 (예: 트로이). |
| 파일 | 은\<Entity> | 맬웨어가 발견 된 연결 된 파일 엔터티의 목록입니다. 는 파일 엔터티를 인라인 또는 참조로 포함할 수 있습니다.<br>구조에 대 한 자세한 내용은 파일 엔터티를 참조 하세요. |
| 프로세스 | 은\<Entity> | 맬웨어가 발견 된 연결 된 프로세스 엔터티의 목록입니다. Fileless 활동에서 경고가 트리거될 때 주로 사용 됩니다.<br>구조에 대 한 자세한 내용은 [Process](#process) 엔터티를 참조 하십시오. |
|

맬웨어 엔터티의 강력한 식별자:

- 이름 + 범주

## <a name="file"></a>File

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | 파일과 |
| 디렉터리 | String | 파일의 전체 경로입니다. |
| Name | String | 경로가 없는 파일 이름입니다. 일부 경고에는 경로가 포함 되지 않을 수 있습니다. |
| 호스트 | 엔터티 | 파일이 저장 된 호스트입니다. |
| FileHashes | &lt;엔터티 나열&gt; | 이 파일과 연결 된 파일 해시입니다. |
|

파일 엔터티의 강력한 식별자:
- 이름 + 디렉터리
- 이름 + FileHash
- 이름 + 디렉터리 + FileHash

## <a name="process"></a>Process

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | 프로세스 |
| ProcessId | String | 프로세스 ID입니다. |
| 명령줄 | String | 프로세스를 만드는 데 사용 되는 명령줄입니다. |
| ElevationToken | 열거형? | 프로세스와 연결 된 권한 상승 토큰입니다.<br>가능한 값은 다음과 같습니다.<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | DateTime? | 프로세스가 실행을 시작한 시간입니다. |
| 가는 | 엔터티 (파일) | 는 파일 엔터티 인라인 또는 참조로 포함 될 수 있습니다.<br>구조에 대 한 자세한 내용은 파일 엔터티를 참조 하세요. |
| 계정 | 엔터티 | 프로세스를 실행 하는 계정입니다.<br>는 [계정](#user-account) 엔터티를 인라인 또는 참조로 포함할 수 있습니다.<br>구조에 대 한 자세한 내용은 [계정](#user-account) 엔터티를 참조 하세요. |
| ParentProcess | 엔터티 (프로세스) | 부모 프로세스 엔터티입니다. <br>일부 데이터 (예: PID만 포함)를 포함할 수 있습니다. |
| 호스트 | 엔터티 | 프로세스가 실행 되 고 있는 호스트입니다. |
| LogonSession | Entity (HostLogonSession) | 프로세스가 실행 되 고 있는 세션입니다. |
|

프로세스 엔터티의 강력한 식별자:

- 호스트 + ProcessId + CreationTimeUtc
- 호스트 + ParentProcessId + CreationTimeUtc + CommandLine
- 호스트 + ProcessId + CreationTimeUtc + 추가
- 호스트 + ProcessId + CreationTimeUtc + 이미지 \ Filefilehash

프로세스 엔터티의 약한 식별자:

- ProcessId + CreationTimeUtc + CommandLine (호스트 없음)
- ProcessId + CreationTimeUtc + 추가 (호스트 없음)

## <a name="cloud-application"></a>클라우드 응용 프로그램

*엔터티 이름: CloudApplication*

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | ' 클라우드 응용 프로그램 ' |
| AppId | Int | 응용 프로그램의 기술 식별자입니다. 이 값은 [클라우드 응용 프로그램 식별자](#cloud-application-identifiers)목록에 정의 된 값 중 하나 여야 합니다. AppId 필드의 값은 선택 사항입니다. |
| Name | String | 관련 클라우드 응용 프로그램의 이름입니다. 응용 프로그램 이름 값은 선택 사항입니다. |
| InstanceName | String | 클라우드 응용 프로그램의 사용자 정의 인스턴스 이름입니다. 일반적으로 고객에 게 동일한 유형의 여러 응용 프로그램을 구분 하는 데 사용 됩니다. |
|

클라우드 응용 프로그램 엔터티의 강력한 식별자:
 - AppId (InstanceName 제외)
 - 이름 (InstanceName 제외)
 - AppId + InstanceName
 - 이름 + InstanceName

## <a name="domain-name"></a>도메인 이름

*엔터티 이름: DNS*

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | dns |
| DomainName | String | 경고와 연결 된 DNS 레코드의 이름입니다. |
| IpAddress | &lt;엔터티 나열 (IP)&gt; | 확인 된 IP 주소에 해당 하는 엔터티입니다. |
| DnsServerIp | IP (엔터티) | 요청을 확인 하는 DNS 서버를 나타내는 엔터티입니다. |
| HostIpAddress | IP (엔터티) | DNS 요청 클라이언트를 나타내는 엔터티입니다. |
|

DNS 엔터티의 강력한 식별자:
- DomainName + DnsServerIp + HostIpAddress

DNS 엔터티의 약한 식별자:
- DomainName + HostIpAddress

## <a name="azure-resource"></a>Azure 리소스

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | ' azure-리소스 ' |
| ResourceId | String | 리소스의 Azure 리소스 ID입니다. |
| SubscriptionId | String | 리소스의 구독 ID입니다. |
| TryGetResourceGroup | Bool | 리소스 그룹 값 (있는 경우)입니다. |
| TryGetProvider | Bool | 공급자 값 (있는 경우)입니다. |
| TryGetName | Bool | 이름 값 (있는 경우)입니다. |
|

Azure 리소스 엔터티의 강력한 식별자:
- ResourceId

## <a name="file-hash"></a>파일 해시

*엔터티 이름: FileHash*

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | get-filehash |
| 알고리즘 | 열거형 | 해시 알고리즘 유형입니다. 가능한 값은 다음과 같습니다.<li>알 수 없음<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| 값 | String | 해시 값입니다. |
|

파일 해시 엔터티의 강력한 식별자:
- 알고리즘 + 값

## <a name="registry-key"></a>레지스트리 키

*엔터티 이름: RegistryKey*

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | ' 레지스트리 키 ' |
| Hive | 열거형? | 다음 값 중 하나입니다.<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| 키 | String | 레지스트리 키 경로입니다. |
|

레지스트리 키 엔터티의 강력한 식별자:
- Hive + 키

## <a name="registry-value"></a>레지스트리 값

*엔터티 이름: RegistryValue*

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | ' registry-값 ' |
| 키 | 엔터티 (RegistryKey) | 레지스트리 키 엔터티입니다. |
| Name | String | 레지스트리 값 이름입니다. |
| 값 | String | 값 데이터의 문자열 형식 표현입니다. |
| ValueType | 열거형? | 다음 값 중 하나입니다.<li>String<li>이진<li>Dword<li>Qword<li>MultiString<li>ExpandString<li>없음<li>알 수 없음<br>값은 RegistryValueKind 열거형을 따라야 합니다. |
|

레지스트리 값 엔터티의 강력한 식별자:
- 키 + 이름

레지스트리 값 엔터티의 약한 식별자:
- 이름 (키 불포함)

## <a name="security-group"></a>보안 그룹

*엔터티 이름: SecurityGroup*

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | ' 보안 그룹 ' |
| DistinguishedName | String | 그룹의 고유 이름입니다. |
| SID | String | SID 특성은 그룹의 SID (보안 식별자)를 지정 하는 단일 값 특성입니다. |
| ObjectGuid | Eid? | ObjectGUID 특성은 Active Directory에 의해 할당 되는 개체의 고유 식별자 인 단일 값 특성입니다. |
|

보안 그룹 엔터티의 강력한 식별자:
 - DistinguishedName
 - SID
 - ObjectGuid

## <a name="url"></a>URL

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | url |
| Url | URI | 엔터티가 가리키는 전체 URL입니다. |
|

URL 엔터티의 강력한 식별자:
- Url (절대 URL 인 경우)

URL 엔터티의 약한 식별자:
- Url (상대 URL 인 경우)

## <a name="iot-device"></a>IoT 디바이스

*엔터티 이름: IoTDevice*

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | ' iotdevice ' |
| IoTHub | Entity (Remove-azureresource) | 장치가 속한 IoT Hub 나타내는 Remove-azureresource 엔터티입니다. |
| DeviceId | String | IoT Hub 컨텍스트에서 장치의 ID입니다. |
| DeviceName | String | 장치의 이름입니다. |
| IoTSecurityAgentId | Eid? | 장치에서 실행 중인 *IoT* 에이전트의 Defender ID입니다. |
| DeviceType | String | 장치의 유형 (' 온도 센서 ', ' freezer ', ' 바람 터빈 ' 등)입니다. |
| 원본 | String | 장치 엔터티의 원본 (Microsoft/Vendor)입니다. |
| SourceRef | 엔터티 (Url) | 장치가 관리 되는 원본 항목에 대 한 URL 참조입니다. |
| 제조업체 | String | 디바이스의 제조업체입니다. |
| 모델 | String | 디바이스의 모델입니다. |
| OperatingSystem | String | 장치가 실행 중인 운영 체제입니다. |
| IpAddress | IP (엔터티) | 장치의 현재 IP 주소입니다. |
| MacAddress | String | 디바이스의 Mac 주소입니다. |
| 프로토콜 | 목록 &lt; 문자열&gt; | 장치에서 지 원하는 프로토콜 목록입니다. |
| SerialNumber | String | 디바이스의 일련 번호입니다. |
|

IoT 장치 엔터티의 강력한 식별자:
- IoTHub + DeviceId

IoT 장치 엔터티의 약한 식별자:
- DeviceId (IoTHub 제외)

## <a name="mailbox"></a>사서함

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | 편지함 |
| MailboxPrimaryAddress | String | 사서함의 기본 주소입니다. |
| DisplayName | String | 사서함의 표시 이름입니다. |
| Upn | String | 사서함의 UPN입니다. |
| RiskLevel | 열거형? | 이 사서함의 위험 수준입니다. 가능한 값은 다음과 같습니다.<li>없음<li>낮음<li>중간<li>높음 |
| Msds-externaldirectoryobjectid | Eid? | 사서함의 AzureAD 식별자입니다. 계정 엔터티의 AadUserId와 유사 하지만이 속성은 사무실 측의 사서함 개체에만 적용 됩니다. |
|

사서함 엔터티의 강력한 식별자:
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>메일 클러스터

*엔터티 이름: MailCluster*

> [!NOTE]
> **Mdo**  =  **Office 365 용 Microsoft Defender**(이전에는 Office 365 O365 ATP (Advanced Threat Protection)로 알려져 있습니다.

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | ' 메일-클러스터 ' |
| NetworkMessageIds | IList &lt; 문자열&gt; | 메일 클러스터의 일부인 메일 메시지 Id입니다. |
| CountByDeliveryStatus | IDictionary &lt; 문자열, Int&gt; | DeliveryStatus 문자열 표현의 메일 메시지 수입니다. |
| CountByThreatType | IDictionary &lt; 문자열, Int&gt; | ThreatType 문자열 표현의 메일 메시지 수입니다. |
| CountByProtectionStatus | IDictionary &lt; 문자열, long&gt; | 위협 방지 상태별 메일 메시지 수입니다. |
| 위협 | IList &lt; 문자열&gt; | 메일 클러스터의 일부인 메일 메시지의 위협입니다. |
| 쿼리 | String | 메일 클러스터의 메시지를 식별 하는 데 사용 된 쿼리입니다. |
| QueryTime | DateTime? | 쿼리 시간입니다. |
| MailCount | Int? | 메일 클러스터의 일부인 메일 메시지 수입니다. |
| IsVolumeAnomaly | Bool? | 볼륨 변칙 메일 클러스터 인지 여부를 확인 합니다. |
| 원본 | String | 메일 클러스터의 원본 (기본값은 ' O365 ATP ')입니다. |
| ClusterSourceIdentifier | String | 이 메일 클러스터의 원본인 메일의 네트워크 메시지 ID입니다. |
| ClusterSourceType | String | 메일 클러스터의 원본 유형입니다. MDO의 MailClusterSourceType 설정에 매핑됩니다 (위의 참고 사항 참조). |
| ClusterQueryStartTime | DateTime? | 클러스터 시작 시간-클러스터 수 쿼리의 시작 시간으로 사용 됩니다. 일반적으로 MDO의 종료 시간-DaysToLookBack 설정에 대 한 날짜입니다 (위의 참고 사항 참조). |
| ClusterQueryEndTime | DateTime? | 클러스터 종료 시간-클러스터 수 쿼리의 종료 시간으로 사용 됩니다. 일반적으로 메일 데이터의 수신 시간입니다. |
| Get-clustergroup | String | MDO에서 사용 되는 Kusto 쿼리 키에 해당 합니다 (위의 참고 사항 참조). |
|

메일 클러스터 엔터티의 강력한 식별자:
- 쿼리 + 소스

## <a name="mail-message"></a>메일 메시지

*엔터티 이름: Send-mailmessage*

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | ' 메일 메시지 ' |
| 파일 | IList &lt; 파일&gt; | 이 메일 메시지의 첨부 파일에 대 한 파일 엔터티입니다. |
| 받는 사람 | String | 이 메일 메시지의 받는 사람입니다. 받는 사람이 여러 개인 경우 메일 메시지가 복사 되 고 각 복사본에는 하나의 받는 사람이 있습니다. |
| Urls | IList &lt; 문자열&gt; | 이 메일 메시지에 포함 된 Url입니다. |
| 위협 | IList &lt; 문자열&gt; | 이 메일 메시지에 포함 된 위협입니다. |
| 보낸 사람 | String | 보낸 사람의 전자 메일 주소입니다. |
| P1Sender | String | "P2 (기본) 사용자를 대신 하 여"이 메일을 보낸 사용자의 메일 ID (위임)입니다. 대리인이 메일을 보내지 않은 경우이 값은 P2Sender와 같습니다. |
| P1SenderDisplayName | String | P2 (기본) 사용자를 대신 하 여이 메일을 보낸 (위임 된) 사용자의 표시 이름입니다. 전자 메일 헤더에 "OnbehalfofSenderDisplayName" 속성으로 표시 됩니다. |
| P1SenderDomain | String | P2 (기본) 사용자를 대신 하 여이 메일을 보낸 (위임 된) 사용자의 전자 메일 도메인입니다. 대리인이 메일을 보내지 않은 경우이 값은 P2SenderDomain와 같습니다. |
| P2Sender | String | 이 전자 메일을 보낸 사람을 대신 하 여 (기본) 사용자의 전자 메일입니다. |
| P2SenderDisplayName | String | 이 전자 메일을 보낸 사람을 대신 하 여 (기본) 사용자의 표시 이름입니다. 대리인이 메일을 보내지 않은 경우 보낸 사람의 표시 이름을 나타냅니다. |
| P2SenderDomain | String | 이 전자 메일을 보낸 사람을 대신 하 여 (기본) 사용자의 전자 메일 도메인입니다. 대리인이 메일을 보내지 않은 경우 보낸 사람의 도메인을 나타냅니다. |
| SenderIP | String | 보낸 사람의 IP 주소입니다. |
| 이름별 | DateTime | 이 메시지를 받은 날짜입니다. |
| NetworkMessageId | Eid? | 이 메일 메시지의 네트워크 메시지 ID입니다. |
| InternetMessageId | String | 이 메일 메시지의 인터넷 메시지 ID입니다. |
| 주체 | String | 이 메일 메시지의 제목입니다. |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | 단위? | Microsoft Defender for Office 365에서 일치 하거나 비슷한 메일 메시지를 찾는 데 사용 됩니다. |
| 위조 방지 | 열거형? | 이 메일 메시지의 방향입니다. 가능한 값은 다음과 같습니다.<li>알 수 없음<li>인바운드<li>아웃바운드<li>Intraorg (내부) |
| DeliveryAction | 열거형? | 이 메일 메시지의 배달 동작입니다. 가능한 값은 다음과 같습니다.<li>알 수 없음<li>배달 Edas스팸<li>배달됨<li>차단<li>대체됨 |
| DeliveryLocation | 열거형? | 이 메일 메시지의 배달 위치입니다. 가능한 값은 다음과 같습니다.<li>알 수 없음<li>받은 편지함<li>JunkFolder<li>DeletedFolder<li>격리<li>외부<li>실패<li>Dropped<li>전환 |
| 언어 | String | 메일 내용이 작성 되는 언어입니다. |
| ThreatDetectionMethods | IList &lt; 문자열&gt; | 이 메일에 적용 된 위협 검색 방법의 목록입니다. |
|

메일 메시지 엔터티의 강력한 식별자:
- NetworkMessageId + 받는 사람

## <a name="submission-mail"></a>전송 메일

*엔터티 이름: SubmissionMail*

| 필드 | Type | 설명 |
| ----- | ---- | ----------- |
| Type | String | 'SubmissionMail' |
| SubmissionId | Eid? | 제출 ID입니다. |
| SubmissionDate | DateTime? | 이 제출에 대해 보고 된 날짜 시간입니다. |
| 제출자 | String | 제출자 전자 메일 주소입니다. |
| NetworkMessageId | Eid? | 제출이 속한 전자 메일의 네트워크 메시지 ID입니다. |
| 타임스탬프 | DateTime? | 메시지를 받은 시간 스탬프 (메일)입니다. |
| 받는 사람 | String | 메일의 받는 사람입니다. |
| 보낸 사람 | String | 메일을 보낸 사람입니다. |
| SenderIp | String | 발신자의 IP입니다. |
| 주체 | String | 제출 메일의 제목입니다. |
| ReportType | String | 지정 된 인스턴스의 전송 유형입니다. 정크, Phish, 맬웨어 또는 NotJunk에 매핑됩니다. |
|

SubmissionMail 엔터티의 강력한 식별자:
- SubmissionId, 제출자, NetworkMessageId, Recipient

## <a name="cloud-application-identifiers"></a>클라우드 응용 프로그램 식별자

다음 목록에서는 알려진 클라우드 응용 프로그램에 대 한 식별자를 정의 합니다. 앱 ID 값은 [클라우드 응용 프로그램](#cloud-application) 엔터티 식별자로 사용 됩니다.

|앱 ID|Name|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|Box|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|cornerstone ondemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jive 소프트웨어|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote 온라인|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Services|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|비즈니스용 Microsoft OneDrive|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc.|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Cloud App Security|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Autodesk Fusion 수명 주기|
|23043|Slack|
|23233|온라인 Microsoft Office|
|25275|비즈니스용 Microsoft Skype|
|25988|Google 문서|
|26055|Microsoft Office 365 관리 센터|
|26060|OPSWAT 기어|
|26061|Microsoft Word Online|
|26062|Microsoft PowerPoint 온라인|
|26063|Microsoft Excel Online|
|26069|Google 드라이브|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Microsoft Delve|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|CAS 프록시 에뮬레이터|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics 인재|
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널에서 엔터티 구조, 식별자 및 스키마에 대해 알아보았습니다.

[엔터티](entities-in-azure-sentinel.md) 및 [엔터티 매핑에](map-data-fields-to-entities.md)대해 자세히 알아보세요. 
