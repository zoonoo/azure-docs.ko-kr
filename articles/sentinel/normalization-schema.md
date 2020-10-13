---
title: Azure 센티널 데이터 정규화 스키마 참조 | Microsoft Docs
description: 이 문서에서는 Azure 센티널 데이터 정규화 스키마를 표시 합니다.
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
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: eb1752ea66f2cbebf6a653705b5a760e8e268240
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937604"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Azure 센티널 데이터 정규화 스키마 참조

## <a name="terminology"></a>용어

센티널의 스키마에서 사용 되는 용어는 다음과 같습니다.

| 용어 | 정의 |
| ---- | ---------- |
| 보고 장치 | Azure 센티널로 레코드를 보내는 시스템입니다. 이는 레코드의 제목 시스템이 아닐 수도 있습니다. |
| 레코드 | 보고 장치에서 전송 되는 데이터의 단위입니다. 이를 종종 "로그", "이벤트" 또는 "경고" 라고 하지만 반드시 이러한 항목 중 하나일 필요는 없습니다. |
|

## <a name="data-types-and-formats"></a>데이터 형식 및 형식

아래 지침에 따라 값을 정규화 해야 합니다. 정규화 된 필드에 필수 이며 다른 필드에 권장 됩니다. 

| 데이터 형식 | 물리적 형식 | 형식 및 값 |
| --------- | ------------- | ---------------- |
| **날짜/시간** | 수집 방법 기능에 따라 내림차순 우선 순위를 사용 합니다.<ul><li>Log Analytics 기본 제공 datetime 형식</li><li>Datetime 숫자 표현을 Log Analytics를 사용 하는 정수 필드</li><li>Log Analytics datetime 숫자 표현을 사용 하는 문자열 필드입니다.</li></ul> | Datetime 표시를 Log Analytics 합니다. <br></br>Log Analytics date & 시간 표현은 본질적으로 비슷하지만 Unix 시간 표현과는 다릅니다. 이러한 변환 지침은을 참조 하세요. <br></br>날짜 & 시간은 표준 시간대가 조정 되어야 합니다. |
| **MAC 주소** | String | Colon-Hexadecimal 표기법 |
| **IP 주소** | IP 주소 | 이 스키마에는 별도의 IPv4 및 IPv6 주소가 없습니다. IP 주소 필드는 IPv4 주소 또는 IPv6 주소를 포함할 수 있습니다.<ul><li>점으로 구분 된 십진수 표기법의 IPv4</li><li>8 hextets 표기법으로, 여기서 설명 하는 약식 문자를 허용 합니다.</li></ul> |
| **사용자** | String | 다음 3 개의 사용자 필드를 사용할 수 있습니다.<ul><li>사용자 이름</li><li>사용자 UPN</li><li>사용자 도메인</li></ul> |
| **사용자 ID** | String | 현재 지원 되는 사용자 Id의 두 가지는 다음과 같습니다.<ul><li>사용자 SID</li><li>Azure Active directory ID</li></ul> |
| **디바이스** | String | 다음 3 개의 장치/호스트 열이 지원 됩니다.<ul><li>ID</li><li>속성</li><li>FQDN (정규화 된 도메인 이름)</li></ul> |
| **국가** | String | 이 우선 순위에 따라 ISO 3166-1를 사용 하는 문자열:<ul><li>알파 2 코드 (예: 미국)</li><li>알파-3 코드 (예: 미국)</li><li>짧은 이름</li></ul> |
| **지역** | String | ISO 3166-2를 사용 하는 국가 하위 부서 이름 |
| **도시** | String | |
| **경도** | Double | ISO 6709 좌표 표현 (부호 있는 10 진수) |
| **위도** | Double | ISO 6709 좌표 표현 (부호 있는 10 진수) |
| **해시 알고리즘** | String | 다음 4 개의 해시 열이 지원 됩니다.<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **파일 형식** | String | 파일 형식의 형식입니다.<ul><li>내선 번호</li><li>클래스</li><li>NamedType</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>네트워크 세션 테이블 스키마

다음은 네트워크 세션 테이블, 버전 1.0.0의 스키마입니다.

| 필드 이름 | 값 형식 | 예제 | 설명 | 연결 된 OSSEM 엔터티 |
|-|-|-|-|-|
| EventType | String | 트래픽 | 수집 되는 이벤트의 유형입니다. | 이벤트 |
| EventSubType 형식 | String | 인증 | 해당 하는 경우 형식의 추가 설명 | 이벤트 |
| EventCount | 정수  | 10 | 집계 된 이벤트의 수입니다 (해당 하는 경우). | 이벤트 |
| EventEndTime | 날짜/시간 | "데이터 형식"을 참조 하세요. | 이벤트가 종료 된 시간입니다. | 이벤트 |
| EventMessage | 문자열 |  액세스 거부 | 에 포함 되었거나 레코드에서 생성 된 일반 메시지 또는 설명입니다. | 이벤트 |
| DvcIpAddr | IP 주소 |  23.21.23.34 | 레코드를 생성 하는 장치의 IP 주소입니다. | 디바이스<br>IP |
| DvcMacAddr | String | 06:10:9f: eb: 8f: 14 | 이벤트를 보낸 보고 장치의 네트워크 인터페이스에 대 한 MAC 주소입니다. | 디바이스<br>Mac |
| DvcHostname | 장치 이름 (문자열) | syslogserver1.contoso.com | 메시지를 생성 하는 장치의 장치 이름입니다. | 디바이스 |
| EventProduct | String | OfficeSharepoint | 이벤트를 생성 하는 제품입니다. | 이벤트 |
| EventProductVersion | 문자열 | 9.0 |  이벤트를 생성 하는 제품의 버전입니다. | 이벤트 |
| EventResourceId | 장치 ID (문자열) | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05/resourcegroups/contoso77/providers/microsoft.compute/virtualmachines/syslogserver1 | 메시지를 생성 하는 장치의 리소스 ID입니다. | 이벤트 |
| EventReportUrl | String | https://192.168.1.1/repoerts/ae3-56.htm | 보고 장치에서 만든 전체 보고서에 대 한 링크 | 이벤트 |
| EventVendor | String | Microsoft | 이벤트를 생성 하는 제품의 공급 업체입니다. | 이벤트 |
| EventResult | 다중값: Success, Partial, Failure, [Empty] (String) | 성공 | 작업에 대해 보고 된 결과입니다. 해당 하지 않는 경우 빈 값입니다. | 이벤트 |
| EventResultDetails | String | 잘못 된 암호 | EventResult에 보고 된 결과의 이유 또는 세부 정보 | 이벤트 |
| EventSchemaVersion | Real | 0.1 | Azure 센티널 스키마 버전입니다. 현재 0.1입니다. | 이벤트 |
| EventSeverity | String | 낮음 | 보고 된 작업이 보안에 영향을 주는 경우는 영향의 심각도를 나타냅니다. | 이벤트 |
| EventOriginalUid | String | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | 보고 장치의 레코드 ID입니다. | 이벤트 |
| EventStartTime | 날짜/시간 | "데이터 형식"을 참조 하세요. | 이벤트가 명시 된 시간 | 이벤트 |
| TimeGenerated | 날짜/시간 | "데이터 형식"을 참조 하세요. | 보고 원본에서 보고 한 이벤트가 발생 한 시간입니다. | 사용자 지정 필드 |
| EventTimeIngested | 날짜/시간 | "데이터 형식"을 참조 하세요. | 이벤트가 Azure 센티널로 수집 된 시간입니다. Azure 센티널에서 추가 됩니다. | 이벤트 |
| EventUid | Guid (문자열) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | 센티널에서 행을 표시 하는 데 사용 하는 고유 식별자입니다. | 이벤트 |
| NetworkApplicationProtocol | String | HTTPS | 연결 또는 세션에서 사용 하는 응용 프로그램 계층 프로토콜입니다. | 네트워크 |
| DstBytes | int | 32455 | 연결 또는 세션에 대 한 대상에서 원본으로 보낸 바이트 수입니다. | 대상 |
| SrcBytes | int | 46536 | 연결 또는 세션의 원본에서 대상으로 보낸 바이트 수입니다. | 원본 |
| NetworkBytes | int | 78991 | 양방향으로 전송 된 바이트 수입니다. BytesReceived와 BytesSent가 모두 있으면 BytesTotal는 해당 합계와 같아야 합니다. | 네트워크 |
| NetworkDirection | 다중 값: Inbound, Outbound (string) | 인바운드 | 조직에 대 한 연결 또는 세션의 방향입니다. | 네트워크 |
| Dstgeoc | String | 벌링턴 | 대상 IP 주소와 연결 된 구/군/시입니다. | 대상이<br>지역 |
| DstGeoCountry | Country (문자열) | 미국 | 원본 IP 주소와 연결 된 국가 | 대상이<br>지역 |
| DstDvcHostname | 장치 이름 (문자열) |  victim_pc | 대상 장치의 장치 이름 | 대상<br>디바이스 |
| DstDvcFqdn | String | victim_pc. contoso.com | 로그가 생성 된 호스트의 정규화 된 도메인 이름 | 대상이<br>디바이스 |
| DstDomainHostname | 문자열 | CONTOSO | 대상 도메인, 대상 호스트의 도메인 (웹 사이트, 도메인 이름 등) (예: DNS 조회 또는 NS 조회) | 대상 |
| DstInterfaceName | 문자열 | 네트워크 어댑터 Microsoft Hyper-V | 대상 장치에서 연결 또는 세션에 사용 되는 네트워크 인터페이스입니다. | 대상 |
| Dst인터페이스 Guid | 문자열 | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | 인증 요청에 사용 된 네트워크 인터페이스의 GUID입니다.  | 대상 |
| DstIpAddr | IP 주소 | 2001: db8:: ff00:42:8329 | 네트워크 패킷에서 가장 일반적으로 대상 IP로 참조 되는 연결 또는 세션 대상의 IP 주소입니다. | 대상이<br>IP |
| DstDvcIpAddr | IP 주소 | 75.22.12.2 | 네트워크 패킷과 직접 연결 되지 않은 장치의 대상 IP 주소 | 대상이<br>디바이스<br>IP
| DstGeoLatitude | 위도 (Double) | 44.475833 | 대상 IP 주소와 연결 된 지리적 좌표의 위도입니다. | 대상이<br>지역 |
| DstMacAddr | String | 06:10:9f: eb: 8f: 14 | 연결 또는 세션이 종료 된 네트워크 인터페이스의 MAC 주소입니다. 가장 일반적으로 네트워크 패킷의 대상 MAC을 참조 합니다. | 대상이<br>용 |
| DstDvcMacAddr | String | 06:10:9f: eb: 8f: 14 | 네트워크 패킷과 직접 연결 되지 않은 장치의 대상 MAC 주소입니다. | 대상이<br>디바이스<br>용 |
| DstDvcDomain | String | CONTOSO | 대상 장치의 도메인입니다. | 대상이<br>디바이스 |
| DstPortNumber | 정수 | 443 | 대상 IP 포트입니다. | 대상이<br>포트 |
| DstGeoRegion | Region (문자열) | 버몬트 | 대상 IP 주소와 연결 된 국가 내의 지역입니다. | 대상이<br>지역 |
| DstResourceId | 장치 ID (문자열) |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05/resourcegroups/contoso77/providers/microsoft.compute/virtualmachines | 대상 장치의 리소스 Id입니다. | 대상 |
| DstNatIpAddr | IP 주소 | 2::1 | 방화벽과 같은 중간 NAT 장치에서 보고 하는 경우에는 NAT 장치에서 원본과 통신 하는 데 사용 하는 IP 주소입니다. | 대상 NAT,<br>IP |
| DstNatPortNumber | int | 443 | 방화벽과 같은 중간 NAT 장치에서 보고 하는 경우 NAT 장치에서 원본과 통신 하는 데 사용 하는 포트입니다. | 대상 NAT,<br>포트 |
| DstUserSid | 사용자 SID |  S-12-1445 | 세션 대상에 연결 된 id의 사용자 ID입니다. 일반적으로 서버를 인증 하는 데 사용 되는 id입니다. 자세한 내용은 "데이터 형식"을 참조 하세요. | 대상이<br>사용자 |
| DstUserAadId | 문자열 (guid) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | 세션의 대상 끝에 있는 사용자의 Azure AD 계정 개체 ID입니다. | 대상이<br>사용자 |
| DstUserName | 사용자 이름 (문자열) | johnd | 세션 대상에 연결 된 id의 사용자 이름입니다.  | 대상이<br>사용자 |
| DstUserUpn | 문자열 | johnd@anon.com | 세션 대상에 연결 된 id의 UPN입니다. | 대상이<br>사용자 |
| DstUserDomain | 문자열 | 작업 그룹 | 세션 대상의 계정 도메인 또는 컴퓨터 이름 | 대상이<br>사용자 |
| DstZone | String | Dmz | 보고 장치에 정의 된 대상의 네트워크 영역입니다. | 대상 |
| DstGeoLongitude | 경도 (Double) | -73.211944 | 대상 IP 주소와 연결 된 지리적 좌표의 경도입니다. | 대상이<br>지역 |
| DvcAction | 다중 값: Allow, Deny, Drop (string) | 허용 | 방화벽과 같은 중간 장치에서 보고 한 경우 장치에서 수행 하는 작업입니다. | 디바이스 |
| DvcInboundInterface | String | eth0 | 방화벽과 같은 중간 장치에서 보고 하는 경우 원본 장치에 연결 하는 데 사용 하는 네트워크 인터페이스입니다. | 디바이스 |
| DvcOutboundInterface | String  | 이더넷 어댑터 이더넷 4 | 방화벽과 같은 중간 장치에서 보고 하는 경우 대상 장치에 연결 하는 데 사용 하는 네트워크 인터페이스입니다. | 디바이스 |
| NetworkDuration | 정수 | 1500 | 네트워크 세션 또는 연결을 완료 하는 데 걸리는 시간 (밀리초)입니다. | 네트워크 |
| NetworkIcmpCode | 정수 | 34 | ICMP 메시지의 경우 ICMP 메시지 유형 숫자 값 (RFC 2780 또는 RFC 4443)입니다. | 네트워크 |
| NetworkIcmpType | String | 대상에 연결할 수 없음 | ICMP 메시지의 경우 ICMP 메시지 유형 텍스트 표현 (RFC 2780 또는 RFC 4443)입니다. | 네트워크 |
| DstPackets | int  | 446 | 연결 또는 세션에 대 한 대상에서 원본으로 보내는 패킷의 수입니다. 패킷의 의미는 보고 장치에 의해 정의 됩니다. | 대상 |
| SrcPackets | int  | 6478 | 원본에서 연결 또는 세션에 대 한 대상으로 보낸 패킷 수입니다. 패킷의 의미는 보고 장치에 의해 정의 됩니다. | 원본 |
| NetworkPackets | int  | 0 | 양방향으로 전송 된 패킷 수입니다. PacketsReceived와 PacketsSent가 모두 있는 경우 BytesTotal는 해당 합계와 같아야 합니다. | 네트워크 |
| HttpRequestTime | 정수 | 700 | 서버에 요청을 보내는 데 걸린 시간입니다 (해당 하는 경우). | Http |
| HttpResponseTime | 정수 | 800 | 서버에서 응답을 수신 하는 데 걸린 시간입니다 (해당 하는 경우). | Http |
| NetworkRuleName | String | AnyAnyDrop | DeviceAction이 결정 된 규칙의 이름 또는 ID입니다. | 네트워크 |
| NetworkRuleNumber | int |  23 | 일치 하는 규칙 번호  | 네트워크 |
| NetworkSessionId | 문자열 | 172_12_53_32_4322__123_64_207_1_80 | 보고 장치에서 보고 하는 세션 식별자입니다. 예를 들어 인증 후 특정 응용 프로그램에 대 한 L7 세션 식별자 | 네트워크 |
| SrcGeoCity | String | 벌링턴 | 원본 IP 주소와 연결 된 도시입니다. | 원본<br>지역 |
| SrcGeoCountry | Country (문자열) | 미국 | 원본 IP 주소와 연결 된 국가 | 원본<br>지역 |
| SrcDvcHostname | 장치 이름 (문자열) |  villain | 원본 장치의 장치 이름 | 원본<br>디바이스 |
| SrcDvcFqdn | 문자열 | Villain.malicious.com | 로그가 생성 된 호스트의 정규화 된 도메인 이름 | 원본<br>디바이스 |
| SrcDvcDomain | 문자열 | EVILORG | 세션이 시작 된 장치의 도메인 | 원본<br>디바이스 |
| SrcDvcOs | String | iOS | 원본 장치의 OS | 원본<br>디바이스 |
| SrcDvcModelName | String | Samsung Galaxy 참고 사항 | 원본 장치의 모델 이름입니다. | 원본<br>디바이스 |
| SrcDvcModelNumber | String | 10.0 | 원본 장치의 모델 번호 | 원본<br>디바이스 |
| SrcDvcType | String | 모바일 | 원본 장치의 유형입니다. | 원본<br> 디바이스 |
| SrcIntefaceName | String | eth01 | 원본 장치에서 연결 또는 세션에 사용 되는 네트워크 인터페이스입니다. | 원본 |
| Src인터페이스 Guid | String | 46ad544b-eaf0-47ef-827c-266030f545a6 | 사용 된 네트워크 인터페이스의 GUID입니다. | 원본 |
| SrcIpAddr | IP 주소 | 77.138.103.108 | 연결 또는 세션이 시작 된 IP 주소입니다. | 원본<br>IP |
| SrcDvcIpAddr | IP 주소 | 77.138.103.108 | 네트워크 패킷과 직접 연결 되지 않은 장치의 원본 IP 주소 (공급자가 수집 하거나 명시적으로 계산 함)입니다. | 원본<br>디바이스<br>IP |
| SrcGeoLatitude | 위도 (Double) | 44.475833 | 원본 IP 주소와 연결 된 지리적 좌표의 위도입니다. | 원본<br>지역 |
| SrcGeoLongitude | 경도 (Double) | -73.211944 | 원본 IP 주소와 연결 된 지리적 좌표의 경도입니다. | 원본<br>지역 |
| SrcMacAddr | String | 06:10:9f: eb: 8f: 14 | 연결 od 세션이 시작 된 네트워크 인터페이스의 MAC 주소입니다. | 원본<br>Mac |
| SrcDvcMacAddr | String | 06:10:9f: eb: 8f: 14 | 네트워크 패킷과 직접 연결 되지 않은 장치의 원본 MAC 주소입니다. | 원본<br>디바이스<br>Mac |
| SrcPortNumber | 정수 | 2335 | 연결이 시작 된 IP 포트입니다. 여러 연결을 구성 하는 세션과 관련이 없을 수 있습니다. | 원본<br>포트 |
| SrcGeoRegion | Region (문자열) | 버몬트 | 원본 IP 주소와 연결 된 국가 내의 지역입니다. | 원본<br>지역 |
| SrcResourceId | String | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05/resourcegroups/contoso77/providers/microsoft.compute/virtualmachines/syslogserver1 | 메시지를 생성 하는 장치의 리소스 ID입니다. | 원본 |
| SrcNatIpAddr | IP 주소 | 4.3.2.1 | 방화벽과 같은 중간 NAT 장치에서 보고 하는 경우에는 NAT 장치에서 대상과 통신 하는 데 사용 하는 IP 주소입니다. | 원본 NAT,<br>IP |
| SrcNatPortNumber | 정수 | 345 | 방화벽과 같은 중간 NAT 장치에서 보고 하는 경우에는 NAT 장치에서 대상과 통신 하는 데 사용 하는 포트입니다. | 원본 NAT,<br>포트 |
| SrcUserSid | 사용자 ID (문자열) | S-15-1445 | 세션 원본과 연결 된 id의 사용자 ID입니다. 일반적으로 사용자는 클라이언트에서 작업을 수행 합니다. 자세한 내용은 "데이터 형식"을 참조 하세요. | 원본<br>사용자 |
| SrcUserAadId | 문자열 (guid) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | 세션의 원본 끝에 있는 사용자의 Azure AD 계정 개체 ID입니다. | 원본<br>사용자 |
| SrcUserName | 사용자 이름 (문자열) | 게 | 세션 소스와 연결 된 id의 사용자 이름입니다. 일반적으로 사용자는 클라이언트에서 작업을 수행 합니다. 자세한 내용은 "데이터 형식"을 참조 하세요. | 원본<br>사용자 |
| SrcUserUpn | 문자열 | bob@alice.com | 세션을 시작 하는 계정의 UPN | 원본<br>사용자 |
| SrcUserDomain | 문자열 | 데스크톱 | 세션을 시작 하는 계정의 도메인 | 원본<br>사용자 |
| SrcZone | String | 탭 | 보고 장치에서 정의한 원본 네트워크 영역입니다. | 원본 |
| NetworkProtocol | String | TCP | 연결 또는 세션에서 사용 하는 IP 프로토콜입니다. 일반적으로 TCP, UDP 또는 ICMP | 네트워크 |
| CloudAppName | String | Facebook | 프록시로 식별 되는 HTTP 응용 프로그램에 대 한 대상 응용 프로그램의 이름입니다. | 클라우드 |
| CloudAppId | String | 124 | 프록시로 식별 되는 HTTP 응용 프로그램에 대 한 대상 응용 프로그램의 ID입니다. 이 값은 일반적으로 사용 되는 프록시와 관련 됩니다. | 클라우드 |
| CloudAppOperation | String | DeleteFile | 프록시에 의해 식별 되는 HTTP 응용 프로그램에 대 한 대상 응용 프로그램의 컨텍스트에서 사용자가 수행한 작업입니다. 이 값은 일반적으로 사용 되는 프록시와 관련 됩니다. | 클라우드 |
| CloudAppRiskLevel | String | 3 | 프록시에 의해 식별 되는 HTTP 응용 프로그램과 관련 된 위험 수준입니다. 이 값은 일반적으로 사용 되는 프록시와 관련 됩니다. | 클라우드 |
| FileName | String | ImNotMalicious.exe | FTP와 같은 프로토콜 및 파일 이름 정보를 제공 하는 HTTP의 네트워크 연결을 통해 전송 되는 파일 이름입니다. | 파일 |
| FilePath | String | C:\Malicious\ImNotMalicious.exe | 파일 이름을 포함 하는 파일의 전체 경로입니다. | 파일 |
| FileHashMd5 | String | 51BC68715FC7C109DCEA406B42D9D78F | 프로토콜에 대 한 네트워크 연결을 통해 전송 되는 파일의 MD5 해시 값입니다. | 파일 |
| FileHashSha1 | String | 491AE3... C299821476F4 | 프로토콜에 대 한 네트워크 연결을 통해 전송 되는 파일의 SHA1 해시 값입니다. | 파일 |
| FileHashSha256 | String | 9B8F8EDB ... C129976F03 | 프로토콜에 대 한 네트워크 연결을 통해 전송 되는 파일의 SHA256 해시 값입니다. | 파일 |
| FileHashSha512 | String | 5E127D ... F69F73F01F361 | 프로토콜에 대 한 네트워크 연결을 통해 전송 되는 파일의 SHA512 해시 값입니다. | 파일 |
| FileExtension |  String | exe | FTP 및 HTTP와 같은 프로토콜에 대 한 네트워크 연결을 통해 전송 되는 파일의 유형입니다. | 파일
| FileMimeType | String | 응용 프로그램/msword | FTP 및 HTTP와 같은 프로토콜에 대 한 네트워크 연결을 통해 전송 되는 파일의 MIME 형식입니다. | 파일 |
| FileSize | 정수 | 23500 | 프로토콜에 대 한 네트워크 연결을 통해 전송 되는 파일의 크기 (바이트)입니다. | 파일 |
| HttpVersion | String | 2.0 | HTTP/HTTPS 네트워크 연결에 대 한 HTTP 요청 버전입니다. | Http |
| HttpRequestMethod | String | GET | HTTP/HTTPS 네트워크 세션에 대 한 HTTP 메서드입니다. | Http |
| HttpStatusCode | String | 404 | HTTP/HTTPS 네트워크 세션에 대 한 HTTP 상태 코드입니다. | Http |
| HttpContentType | String | 다중 파트/폼 데이터; 경계 = 항목 | HTTP/HTTPS 네트워크 세션의 HTTP 응답 콘텐츠 형식 헤더입니다. | Http |
| HttpReferrerOriginal | String | https://developer.mozilla.org/en-US/docs/Web/JavaScript | HTTP/HTTPS 네트워크 세션의 HTTP 참조 페이지 헤더입니다. | Http |
| HttpUserAgentOriginal | String | Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.97 Safari/537.36 | HTTP/HTTPS 네트워크 세션의 HTTP 사용자 에이전트 헤더입니다. | Http |
| HttpRequestXff | String | 120.12.41.1 | Http/HTTPS 네트워크 세션의 경우 HTTP X로 전달 된 헤더입니다. | Http |
| UrlCategory | String | 검색 엔진 | 정의 된 URL 그룹 (또는 URL의 도메인을 기반으로 하는 경우)과 관련 된 항목 (예: 성인, 뉴스, 광고, 파킹 된 도메인 등) | url |
| UrlOriginal | String | https://contoso.com/fo/?k=v&q = u # f | HTTP/HTTPS 네트워크 세션의 HTTP 요청 URL입니다. | Url |
| UrlHostname | String | contoso.com | HTTP/HTTPS 네트워크 세션에 대 한 HTTP 요청 URL의 도메인 부분입니다. | Url |
| ThreatCategory | String | 바이러스 | IP의 웹 보안 게이트웨이와 같은 보안 시스템에 의해 식별 되는 위협의 범주 이며이 네트워크 세션과 연결 됩니다. | 위협 |
| ThreatId | String | Tr. 124 | IP의 웹 보안 게이트웨이와 같은 보안 시스템에 의해 식별 되는 위협의 ID 이며이 네트워크 세션과 연결 됩니다. | 위협 |
| ThreatName | String | EICAR 테스트 파일 | 식별 된 위협 또는 맬웨어 이름 | 위협 |
| AdditionalFields | 동적 (JSON 모음) | {<br>Property1: "val1",<br>Property2: "val2"<br>} | 스키마의 각 열이 일치 하지 않는 경우 추가 필드를 JSON 모음에 저장할 수 있습니다.<br>쿼리 시간 구문 분석의 경우 JSON으로 데이터를 압축 하면 쿼리 성능이 저하 될 수 있으므로이 방법을 사용 하지 않는 것이 좋습니다. 대신 추가 열을 승격 하는 것이 좋습니다.<br>향후 수집 시간 구문 분석 시나리오의 경우이 JSON 모음 열에 추가 데이터가 수집 됩니다. | 사용자 지정 필드 |
| 
