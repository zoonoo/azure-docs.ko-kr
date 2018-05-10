---
title: Azure Security and Compliance Blueprint - FedRAMP 웹 응용 프로그램 자동화 - 시스템 및 통신 보호
description: FedRAMP 웹 응용 프로그램 자동화 - 시스템 및 통신 보호
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 6749ad50cd1ea1cd4ec6ca2f86fef43a9f1515d9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="system-and-communications-protection-sc"></a>시스템 및 통신 보호(SC)

> [!NOTE]
> 이러한 제어는 NIST와 미국 상무부에 의해 NIST 특별 출판물 800-53 개정 4의 일부로 정의됩니다. 각 제어에 대한 테스트 절차 및 지침에 대한 정보는 NIST 800-53 개정 4를 참조하세요.

## <a name="nist-800-53-control-sc-1"></a>NIST 800-53 제어 SC-1

#### <a name="system-and-communications-protection-policy-and-procedures"></a>시스템 및 통신 보호 정책과 절차

**SC-1** 조직은 목적, 범위, 역할, 책임, 관리 약속, 조직 엔터티 간 조정 및 준수를 다루는 시스템 및 통신 보호 정책 및 시스템 및 통신 보호 정책과 관련 시스템 및 정보 무결성 제어의 구현을 용이하게 하는 절차를 개발, 문서화하여 [할당: 조직이 정의한 담당자 또는 역할]에게 배포하고, [할당: 조직이 정의한 빈도]마다 현재 시스템 및 통신 보호 정책을 검토하고 업데이트하며, [할당: 조직이 정의한 빈도]마다 시스템 및 통신 보호 절차를 검토하고 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 시스템 및 통신 보호 정책 및 절차가 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-2"></a>NIST 800-53 제어 SC-2

#### <a name="application-partitioning"></a>응용 프로그램 분할

**SC-2** 정보 시스템은 정보 시스템 관리 기능에서 사용자 기능(사용자 인터페이스 서비스 포함)을 분리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 논리 액세스 제어 및 시스템 아키텍처를 적용하여 시스템 관리 기능에서 사용자 기능을 분리합니다. 사용자 기능은 고객이 배포한 웹 응용 프로그램 인터페이스로 제한됩니다. 시스템 관리 기능용 인터페이스는 사용자 인터페이스에서 분리됩니다. 모든 관리 연결은 프로덕션 리소스에 대한 액세스를 적절히 제한하는 네트워크 보안 그룹 규칙을 사용하여 관리 서브넷에 있는 보안 요새 호스트(jumpbox)를 통해 이루어집니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-3"></a>NIST 800-53 제어 SC-3

#### <a name="security-function-isolation"></a>보안 기능 격리

**SC-3** 정보 시스템은 비보안 기능에서 보안 기능을 분리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 가상 머신은 Windows 운영 체제를 실행합니다. Windows는 각 프로세스에 개인 가상 주소 공간을 할당하여 실행 중인 각 프로세스에 대해 별도의 실행 도메인을 유지 관리합니다. 또한 이 솔루션은 필요한 경우 보안 기능을 분리하도록 고안된 아키텍처 및 액세스 제어를 구현합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-4"></a>NIST 800-53 제어 SC-4

#### <a name="information-in-shared-resources"></a>공유 리소스의 정보

**SC-4** 정보 시스템은 공유 시스템 리소스를 통해 권한 없는 의도치 않은 정보 전송을 방지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 가상 머신은 Windows 운영 체제를 실행합니다. 운영 체제는 적절한 권한이 있는 사용자 및 역할만 정보에 액세스할 수 있도록 리소스(예: 메모리, 저장소)를 관리합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-5"></a>NIST 800-53 제어 SC-5

#### <a name="denial-of-service-protection"></a>서비스 거부 보호

**SC-5** 정보 시스템은 [할당: 조직이 정의한 보안 안전 조치]를 사용하여 서비스 거부 공격 유형 [할당: 조직이 정의한 이러한 정보 원본에대 한 서비스 거부 공격 또는 참조 유형]으로부터 보호하거나 이러한 공격의 효과를 제한합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 웹 응용 프로그램 방화벽 및 부하 분산 기능을 포함하는 Application Gateway를 배포합니다. 웹 계층, 데이터베이스 계층 및 Active Directory를 지원하는 배포된 가상 머신은 확장 가능한 가용성 집합에 배포됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-6"></a>NIST 800-53 제어 SC-6

#### <a name="resource-availability"></a>리소스 가용성

**SC-6** 정보 시스템은 [선택 (하나 이상),우선 순위,할당량, [할당: 조직이 정의한 보안 안전 조치]]를 기준으로 [할당: 조직이 정의한 리소스]를 할당하여 리소스의 가용성을 보호합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 가상 머신은 Windows 운영 체제를 실행합니다. 각 Windows 프로세스는 프로그램을 실행하는 데 필요한 리소스를 제공합니다. 리소스 우선 순위는 운영 체제에 의해 관리됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-7a"></a>NIST 800-53 제어 SC-7.a

#### <a name="boundary-protection"></a>경계 보호

**SC-7.a** 정보 시스템은 시스템의 외부 경계 및 시스템 내의 주요 내부 경계에서 통신을 모니터링하고 제어합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 Application Gateway, 부하 분산 장치를 배포하고, 네트워크 보안 그룹 규칙을 구성하여 경계 외부 및 내부 서브넷 간 통신을 제어합니다. Application Gateway, Load Balancer 및 네트워크 보안 그룹 이벤트와 진단 로그는 고개 모니터링을 위해 Log Analytics에서 수집됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-7b"></a>NIST 800-53 제어 SC-7.b

#### <a name="boundary-protection"></a>경계 보호

**SC-7.b** 정보 시스템은 내부 조직 네트워크에서 [선택: 물리적으로, 논리적으로] 분리된 공개적으로 액세스할 수 있는 시스템 구성 요소에 대한 하위 네트워크를 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 별도 웹 서브넷, 데이터베이스 서브넷, Active Directory 서브넷 및 서브넷 관리를 포함하는 아키텍처에 리소스를 배포합니다. 서브넷은 서브넷 간 트래픽을 시스템 및 관리 기능에 필요한 경우로만 제한하기 위해 개별 서브넷에 적용되는 네트워크 보안 그룹 규칙에 의해 논리적으로 구분됩니다(예: 외부 트래픽은 데이터베이스, 관리 또는 Active Directory 서브넷에 액세스할 수 없음). |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-7c"></a>NIST 800-53 제어 SC-7.c

#### <a name="boundary-protection"></a>경계 보호

**SC-7.c** 정보 시스템은 조직 보안 아키텍처에 따라 정렬된 경계 보호 장치로 구성되는 관리되는 인터페이스를 통해서만 외부 네트워크 또는 정보 시스템에 연결됩니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 Application Gateway를 배포하여 고객이 배포한 웹 응용 프로그램에 대한 외부 연결을 관리합니다. 관리 액세스에 대한 외부 연결은 허가된 IP 주소로만 외부 연결을 제한하기 위해 적용되는 네트워크 보안 규칙을 사용하여 관리 서브넷에 배포된 요새 호스트/jumpbox로 제한됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-3"></a>NIST 800-53 제어 SC-7 (3)

#### <a name="boundary-protection--access-points"></a>경계 보호 | 액세스 지점

**SC-7(3)** 조직은 정보 시스템에 대한 외부 네트워크 연결 수를 제한합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 2개의 공용 IP 주소, 즉 Application Gateway에 연결된 IP 주소와 관리 요새 호스트/jumpbox에 연결된 IP 주소를 배포합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-4a"></a>NIST 800-53 제어 SC-7(4).a

#### <a name="boundary-protection--external-telecommunications-services"></a>경계 보호 | 외부 원격 통신 서비스

**SC-7(4).a** 조직은 각 외부 원격 통신 서비스에 대한 관리되는 인터페이스를 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 2개의 공용 IP 주소, 즉 Application Gateway에 연결된 IP 주소와 관리 요새 호스트/jumpbox에 연결된 IP 주소를 배포합니다. 이러한 인터페이스의 관리는 소프트웨어 정의 네트워킹을 통해 사용하도록 설정됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-4b"></a>NIST 800-53 제어 SC-7(4).b

#### <a name="boundary-protection--external-telecommunications-services"></a>경계 보호 | 외부 원격 통신 서비스

**SC-7(4).b** 조직은 관리되는 각 인터페이스에 대한 트래픽 흐름 정책을 설정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 2개의 공용 IP 주소, 즉 Application Gateway에 연결된 IP 주소와 관리 요새 호스트/jumpbox에 연결된 IP 주소를 배포합니다. 이러한 인터페이스의 관리는 소프트웨어 정의 네트워킹을 통해 사용하도록 설정됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-4c"></a>NIST 800-53 제어 SC-7(4).c

#### <a name="boundary-protection--external-telecommunications-services"></a>경계 보호 | 외부 원격 통신 서비스

**SC-7(4).c** 조직은 각 인터페이스를 통해 전송되는 정보의 기밀성 및 무결성을 보호합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에 의해 배포된 웹 응용 프로그램 게이트웨이는 HTTPS 수신기로 구성되어 통신 세션의 기밀성 및 무결성을 보장합니다. jumpbox에 대한 원격 데스크톱 연결도 암호화되어 기밀성과 무결성을 제공합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-4d"></a>NIST 800-53 제어 SC-7 (4).d

#### <a name="boundary-protection--external-telecommunications-services"></a>경계 보호 | 외부 원격 통신 서비스

**SC-7(4).d** 조직은 지원 임무/비즈니스 요구와 필요 기간을 포함하여 트래픽 흐름 정책의 각 예외를 문서화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 원격 통신 서비스를 포함하는 데이터 센터 작업에 대해 책임이 없습니다. 모든 원격 통신 서비스는 Microsoft Azure에서 제공하고 관리합니다. 이 제어는 Azure에서 상속됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-4e"></a>NIST 800-53 제어 SC-7(4).e

#### <a name="boundary-protection--external-telecommunications-services"></a>경계 보호 | 외부 원격 통신 서비스

**SC-7(4).e** 조직은 [할당: 조직이 정의한 빈도]마다 트래픽 흐름 정책에 대한 예외를 검토하고 명시적 임무/비즈니스 요구에 따라 더 이상 지원되지 않는 예외를 제거합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 원격 통신 서비스를 포함하는 데이터 센터 작업에 대해 책임이 없습니다. 모든 원격 통신 서비스는 Microsoft Azure에서 제공하고 관리합니다. 이 제어는 Azure에서 상속됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-5"></a>NIST 800-53 제어 SC-7(5)

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>경계 보호 | 기본적으로 거부 / 예외에 따라 허용

**SC-7(5)** 관리되는 인터페이스의 정보 시스템은 기본적으로 네트워크 통신 트래픽을 거부하고 예외를 통해 네트워크 통신 트래픽을 허용합니다(즉, 모두 거부, 예외를 통해 허용).

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에서 배포하는 네트워크 보안 그룹에 적용되는 규칙 집합은 기본적으로 거부 체계를 사용하여 구성됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-7"></a>NIST 800-53 제어 SC-7(7)

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>경계 보호 | 원격 장치에 대한 분할 터널링 방지

**SC-7(7)** 정보 시스템은 원격 장치와 함께, 장치가 시스템에 대해 비원격 연결을 설정하면서 동시에 다른 연결을 통해 외부 네트워크의 리소스와 통신하지 못하게 합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 원격 장치 구성 정책은 분할 터널링를 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-8"></a>NIST 800-53 제어 SC-7(8)

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>경계 보호 | 인증 된 프록시 서버로 트래픽 라우팅

**SC-7(8)** 정보 시스템은 관리되는 인터페이스의 인증된 프록시 서버를 통해 [할당: 조직이 정의한 내부 통신 트래픽]을 [할당: 조직이 정의한 외부 네트워크]로 라우팅합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객이 정의한 정보를 인증된 프록시를 통해 외부 네트워크로 라우팅할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-10"></a>NIST 800-53 제어 SC-7(10)

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>경계 보호 | 무단 방출 방지

**SC-7(10)** 조직은 관리되는 인터페이스를 통해 정보가 무단으로 방출되는 것을 방지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 관리되는 인터페이스를 통해 정보가 무단으로 방출되는 것을 방지할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-12"></a>NIST 800-53 제어 SC-7(12)

#### <a name="boundary-protection--host-based-protection"></a>경계 보호 | 호스트 기반 보호

**SC-7(12)** 조직은 [할당: 조직이 정의한 정보 시스템 구성 요소]에서 [할당: 조직이 정의한 호스트 기반 경계 보호 메커니즘]을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에서 배포된 가상 머신은 호스트 기반 방화벽이 사용되도록 설정된 상태로 구성됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-13"></a>NIST 800-53 제어 SC-7(13)

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>경계 보호 | 보안 도구 분리 / 메커니즘 / 지원 구성 요소

**SC-7(13)** 조직은 다른 시스템 구성 요소에 대한 관리되는 인터페이스를 사용하여 물리적으로 분리된 하위 네트워크를 구현하여 다른 내부 정보 시스템 구성 요소에서 [할당: 조직이 정의한 정보 보안 도구, 메커니즘 및 지원 구성 요소]를 분리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 정보 보안 도구 및 지원 구성 요소의 고객 배포를 위한 별도 관리 서브넷이 있는 아키텍처에 리소스를 배포합니다. 서브넷은 네트워크 보안 그룹 규칙에 의해 논리적으로 구분됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-18"></a>NIST 800-53 제어 SC-7(18)

#### <a name="boundary-protection--fail-secure"></a>경계 보호 | 보안 실패

**SC-7(18)** 정보 시스템은 경계 보호 장치의 작동이 실패할 경우 안전하게 실패합니다.

**책임:** `Azure Only`

|||
|---|---|
| **고객** | Azure에 배포된 시스템의 범위 내에 물리적 경계 보호 장치가 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 지리적으로 분리되고 중복된 게이트웨이 서버 및 SSL VPN을 배포합니다. 게이트웨이 시스템에 오류가 발생하는 경우 안전하게 실패하며 해당 환경에 대한 액세스가 제한됩니다. Microsoft Azure 환경에 대한 연결을 설정하려면 사용자는 Microsoft Azure에서 관리하는 활성 게이트웨이 서버에 대해 별도 연결을 설정해야 합니다. <br /> 또한 Microsoft Azure 네트워크 장치(에지 라우터, 액세스 라우터, Load Balancer, 집계 스위치 및 TORS 포함)가 실패하는 경우 영향을 받는 회로 연결이 끊어지므로 안전하게 실패합니다. Microsoft Azure 네트워크 장치에 오류가 발생하더라도 시스템 외부 정보가 장치로 유입되거나, 허가되지 않은 정보 배출이 허용될 수 없습니다. 중복성이 기본 제공되어 있으므로 Microsoft Azure 자산에 오류가 발생해도 가용성에 영향을 주지 않습니다. |


 ### <a name="nist-800-53-control-sc-7-20"></a>NIST 800-53 제어 SC-7(20)

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>경계 보호 | 동적 격리 / 분리

**SC-7(20)** 정보 시스템은 시스템의 다른 구성 요소에서 [할당: 조직이 정의한 정보 시스템 구성 요소]를 동적으로 격리/분리하는 기능을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 시스템이 고객이 배포한 리소스를 동적으로 격리하는 기능을 확인할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-7-21"></a>NIST 800-53 제어 SC-7(21)

#### <a name="boundary-protection--isolation-of-information-system-components"></a>경계 보호 | 정보 시스템 구성 요소 격리

**SC-7(21)** 조직은 경계 보호 메커니즘을 사용하여 [할당: 조직이 정의한 임무 및/또는 비즈니기스 능]을 지원하는 [할당: 조직이 정의한 정보 시스템 구성 요소]를 분리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 별도 웹 서브넷, 데이터베이스 서브넷, Active Directory 서브넷 및 서브넷 관리를 포함하는 아키텍처에 리소스를 배포합니다. 서브넷은 서브넷 간 트래픽을 시스템 및 관리 기능에 필요한 경우로만 제한하기 위해 개별 서브넷에 적용되는 네트워크 보안 그룹 규칙에 의해 논리적으로 구분됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-8"></a>NIST 800-53 제어 SC-8

#### <a name="transmission-confidentiality-and-integrity"></a>전송 기밀성 및 무결성

**SC-8** 정보 시스템은 전송된 정보의 [선택(하나 이상): 기밀성, 무결성]을 보호합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | SI-8(1) 구현은 이 제어 요구 사항을 충족합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-8-1"></a>NIST 800-53 제어 SC-8(1)

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>전송 기밀성 및 무결성 | 암호화 또는 대체 물리적 보호

**SC-8(1)** 정보 시스템은 [할당: 조직이 정의한 보안 안전 조치]에 의해 보호되지 않는 한, 전송 중에 암호화 메커니즘을 구현하여 [선택(하나 이상): 무단 정보 공개를 방지, 정보 변경을 감지]합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진은 보안 프로토콜만 사용하여 통신하도록 리소스를 구성합니다. Application Gateway의 WAF 구성 요소는 HTTPS/TLS를 통한 외부 사용의 통신자를 수락하고 HTTPS/TLS를 통해서만 백 엔드 풀과 통신하도록 구성됩니다. SQL Server는 HTTPS/TLS를 통해서만 통신하도록 구성됩니다. 원격 데스크톱 서비스는 보안 연결을 사용하도록 구성됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-10"></a>NIST 800-53 제어 SC-10

#### <a name="network-disconnect"></a>네트워크 연결 끊기

**SC-10** 정보 시스템은 세션 끝에서 또는 비활성 상태를 [할당: 조직이 정의한 기간] 동안 유지한 후 통신 세션에 연결된 네트워크 연결을 종료합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 원격 데스크톱 세션에 대한 인증은 Active Directory에서 관리됩니다. Active Directory에서 사용자에 대한 액세스를 사용하지 않도록 설정하면 원격 세션이 즉시 종료됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-12"></a>NIST 800-53 제어 SC-12

#### <a name="cryptographic-key-establishment-and-management"></a>암호화 키 설정 및 관리

**SC-12** 조직은 [할당: 조직이 정의한 키 생성, 배포, 저장, 액세스 및 소멸에 대한 요구 사항]에 따라 정보 시스템 내에서 사용된 필수 암호화에 대해 암호화 키를 설정하고 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 Azure Key Vault를 배포합니다. Azure Key Vault는 클라우드 응용 프로그램 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. Azure Key Vault는 FIPS 140-2 수준 2 HSM(하드웨어 보안 모듈) 키 생성 기능을 사용하여 키를 생성할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-12-1"></a>NIST 800-53 제어 SC-12(1)

#### <a name="cryptographic-key-establishment-and-management--availability"></a>암호화 키 설정 및 관리 | 가용성

**SC-12(1)** 조직은 사용자가 암호화 키를 분실한 경우 정보의 가용성을 유지 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Key Vault는 이 Blueprint에 사용되는 암호화 키 및 비밀을 저장하는 데 사용됩니다. Key Vault는 데이터를 액세스하고 암호화하는 키에 대한 키 관리 프로세스를 간소화합니다. 인증자인 배포 계정에 대한 Azure 암호, 가상 머신 관리자 암호, SQL Server 서비스 계정 암호가 Key Vault에 저장됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-12-2"></a>NIST 800-53 제어 SC-12(2)

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>암호화 키 설정 및 관리 | 대칭 키

**SC-12(2)** 조직은 [선택: NIST FIPS 규격, NSA 승인] 키 관리 기술 및 프로세스를 사용하여 대칭 암호화 키를 생성, 제어 및 배포합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Key Vault는 암호화를 만들고, 제어하고, 배포하는 데 사용됩니다. Azure Key Vault는 FIPS 140-2 수준 2 HSM(하드웨어 보안 모듈) 키 생성 기능을 사용하여 키를 생성할 수 있습니다. 키는 Azure Key Vault에 안전하게 저장 및 암호화되는 컨테이너 내에 저장되고 관리됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-12-3"></a>NIST 800-53 제어 SC-12(3)

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>암호화 키 설정 및 관리 | 비대칭 키

**SC-12(3)** 조직은 [선택: NSA 승인 키 관리 기술 및 프로세스, 승인된 PKI 클래스 3 인증서 또는 사전 배치된 키 관리 자료, 승인된 PKI 클래스 3 또는 클래스 4 인증서 및 사용자의 개인 키를 보호하는 하드웨어 보안 토큰]을 사용하여 비대칭 암호화 키를 생성, 제어 및 배포합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 비대칭 암호화 키(고객이 배포한 리소스 내에서 사용될 경우)를 생성, 제어 및 배포할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-13"></a>NIST 800-53 제어 SC-13

#### <a name="cryptographic-protection"></a>암호화 보호

**SC-13** 정보 시스템은 해당 연방법, 행정 명령, 지시, 정책, 규정 및 표준에 따라 [할당: 조직이 정의한 암호화 사용 및 각 사용에 필요한 암호화 유형]을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Windows 인증, 원격 데스크톱 및 BitLocker가 이 Blueprint에서 사용됩니다. 이러한 구성 요소는 FIPS 140에서 검증된 암호화 모듈을 사용하도록 구성할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-15a"></a>NIST 800-53 제어 SC-15.a

#### <a name="collaborative-computing-devices"></a>공동 컴퓨팅 장치

**SC-15.a** 정보 시스템은 [할당: 조직이 정의한 원격 활성화 허용 예외]를 제외하고 공동 컴퓨팅 장치의 원격 활성화를 금지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint의 일부로 배포된 공동 컴퓨팅 장치는 없습니다. 참고: Azure에 배포된 시스템의 범위 내에 실제 공동 컴퓨팅 장치가 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-15b"></a>NIST 800-53 제어 SC-15.b

#### <a name="collaborative-computing-devices"></a>공동 컴퓨팅 장치

**SC-15.b** 정보 시스템은 장치에 실제로 존재하는 사용자에게 명시적 사용 지시를 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint의 일부로 배포된 공동 컴퓨팅 장치는 없습니다. 참고: Azure에 배포된 시스템의 범위 내에 실제 공동 컴퓨팅 장치가 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-17"></a>NIST 800-53 제어 SC-17

#### <a name="public-key-infrastructure-certificates"></a>공개 키 인프라 인증서

**SC-17** 조직은 [할당: 조직이 정의한 인증서 정책]에 따라 공개 키 인증서를 발급하거나 승인된 서비스 공급자로부터 공개 키 인증서를 획득합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 인증서 발급을 위해 엔터프라이즈 수준의 공개 키 인프라를 사용될 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-18a"></a>NIST 800-53 제어 SC-18.a

#### <a name="mobile-code"></a>모바일 코드

**SC-18.a** 조직은 허용되거나 허용되지 않는 모바일 코드 및 모바일 코드 기술을 정의합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 시스템 및 통신 보호 절차는 허용되거나 허용되지 않는 모바일 코드를 정의할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-18b"></a>NIST 800-53 제어 SC-18.b

#### <a name="mobile-code"></a>모바일 코드

**SC-18.b** 조직은 허용되는 모바일 코드 및 모바일 코드 기술에 대한 사용 제한 및 구현 지침을 설정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 시스템 및 통신 보호 절차는 모바일 코드 사용에 대한 제한을 설정할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-18c"></a>NIST 800-53 제어 SC-18.c

#### <a name="mobile-code"></a>모바일 코드

**SC-18.c** 조직은 정보 시스템 내에서 모바일 코드의 사용을 허가, 모니터링 및 제어합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 모바일 코드의 허가, 모니터링 및 제어에 대한 엔터프라이즈 수준의 프로세스를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-19a"></a>NIST 800-53 제어 SC-19.a

#### <a name="voice-over-internet-protocol"></a>Voice Over Internet Protocol

**SC-19.a** 조직은 악의적으로 사용될 경우 정보 시스템에 대한 손상 가능성에 따라 VoIP(Voice Over Internet Protocol) 기술에 대한 사용량 제한 및 구현 지침을 설정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint의 일부로 배포되는 Voice Over Internet Protocol 기술은 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-19b"></a>NIST 800-53 제어 SC-19.b

#### <a name="voice-over-internet-protocol"></a>Voice Over Internet Protocol

**SC-19.b** 조직은 정보 시스템 내에서 VoIP의 사용을 허가, 모니터링 및 제어합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint의 일부로 배포되는 Voice Over Internet Protocol 기술은 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-20a"></a>NIST 800-53 제어 SC-20.a

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>보안 이름/주소 확인 서비스(신뢰할 수 있는 원본)

**SC-20.a** 정보 시스템은 외부 이름/주소 확인 쿼리에 대한 응답으로 시스템이 반환하는 신뢰할 수 있는 이름 확인 데이터와 함께 추가적인 데이터 원본 인증 및 무결성 확인 아티팩트를 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 보안 이름 및 주소 확인 서비스를 담당해야 합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-20b"></a>NIST 800-53 제어 SC-20.b

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>보안 이름/주소 확인 서비스(신뢰할 수 있는 원본)

**SC-20.b** 정보 시스템은 하위 영역의 보안 상태를 나타내고 (하위 항목이 보안 확인 서비스를 지원할 경우) 분산된 계층형 네임스페이스의 일부로 작동할 경우 상위 및 하위 도메인 간 신뢰 체인을 확인할 수 있는 방법을 제공합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 보안 이름 및 주소 확인 서비스를 담당해야 합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-21"></a>NIST 800-53 제어 SC-21

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>보안 이름/주소 확인 서비스(재귀 또는 캐싱 확인자)

**SC-21** 정보 시스템은 시스템이 신뢰할 수 있는 원본에서 수신하는 이름/주소 확인 응답에 대해 데이터 원본 인증 및 데이터 무결성 확인을 요청하고 수행합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 신뢰할 수 있는 원본에서 받은 이름/주소 확인 응답에 대해 데이터 원본 인증 및 데이터 무결성 확인을 요청하고 수행하도록 고객 배포 리소스를 구성할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-22"></a>NIST 800-53 제어 SC-22

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>이름/주소 확인 서비스의 아키텍처 및 프로비전

**SC-22** 조직에 대해 전체적인 이름/주소 확인 서비스를 제공하는 정보 시스템은 내결함 상태를 유지하며 내부/외부 역할 구분을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에 대해 주소 확인 서비스를 제공하는 시스템이 내결함 상태를 유지하고 내부/외부 역할 구분을 구현하도록 할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-23"></a>NIST 800-53 제어 SC-23

#### <a name="session-authenticity"></a>세션 신뢰성

**SC-23** 정보 시스템은 통신 세션의 신뢰성을 보호합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Portal, 원격 데스크톱 연결 및 웹 응용 프로그램 게이트웨이를 비롯하여 이 Blueprint에서 배포한 리소스에 대한 원격 액세스는 TLS를 사용하여 보호됩니다. TLS는 세션 수준에서 통신에 대한 신뢰성을 제공합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-23-1"></a>NIST 800-53 제어 SC-23(1)

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>세션 신뢰성 | 로그아웃 시 세션 식별자 무효화

**SC-23(1)** 정보 시스템은 사용자 로그아웃 또는 기타 세션 종료 시 세션 식별자를 무효화합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Portal, 원격 데스크톱 연결 및 웹 응용 프로그램 게이트웨이를 비롯하여 이 Blueprint에서 배포한 리소스에 대한 원격 액세스는 TLS를 사용하여 보호됩니다. Azure Portal 및 원격 데스크톱 세션은 로그아웃 시 세션 식별자를 무효화합니다. 웹 세션 무효화는 Azure Application Gateway - WAF(웹 응용 프로그램 방화벽) 규칙을 통해 적용됩니다. WAF는 세션당 선호도 쿠키를 적용하고 클라이언트가 30분(구성 가능한 조직별 배포 후 규칙) 동안 비활동 상태를 유지한 후 세션 시간 제한을 적용합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-24"></a>NIST 800-53 제어 SC-24

#### <a name="fail-in-known-state"></a>알려진 상태로 실패

**SC-24** 정보 시스템은 오류가 발생할 때 [할당: 조직이 정의한 실패 유형]에 대해 [할당: 조직이 정의한 알려진 상태]로 실패하여 [할당: 조직이 정의한 시스템 상태 정보]를 보존합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 오류 발생 시 고객이 배포한 리소스가 알려진 상태가 되도록 할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-28"></a>NIST 800-53 제어 SC-28

#### <a name="protection-of-information-at-rest"></a>미사용 정보 보호

**SC-28** 정보 시스템은 [할당: 조직이 정의한 미사용 정보]의 [선택(하나 이상): 기밀성, 무결성]을 보호합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | SC-28(1) 구현은 이 제어 요구 사항을 충족합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-sc-28-1"></a>NIST 800-53 제어 SC-28(1)

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>미사용 정보 보호 | 암호화 보호

**SC-28(1)** 정보 시스템은 [할당: 조직이 정의한 정보 시스템 구성 요소]에 대한 [할당: 조직이 정의한 정보]의 무단 공개 및 수정을 방지하기 위한 암호화 메커니즘을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에서 배포한 가상 머신은 디스크 암호화를 구현하여 미사용 정보의 기밀성 및 무결성을 보호합니다. Windows용 Azure 디스크 암호화는 Windows의 BitLocker 기능을 사용하여 구현됩니다. SQL Server는 TDE(투명한 데이터 암호화)를 사용하도록 구성됩니다. 이 기능은 데이터 및 로그 파일의 실시간 암호화 및 암호 해독을 수행하여 미사용 정보를 보호합니다. TDE는 저장된 데이터가 무단으로 액세스되지 못하게 합니다. 고객은 추가적인 응용 프로그램 수준 제어를 구현하여 저장된 정보의 무결성을 보호해야 할 수 있습니다. 이 Blueprint에서 배포한 모든 저장소 BLOB의 기밀성과 무결성은 Azure SSE(Storage 서비스 암호화)를 사용하여 보호됩니다. SSE는 256비트 AES 암호화를 사용하여 Azure 저장소 계정 내에서 미사용 데이터를 보호합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-sc-39"></a>NIST 800-53 제어 SC-39

#### <a name="process-isolation"></a>프로세스 격리

**SC-39** 정보 시스템은 실행 중인 각 프로세스에 대해 별도 실행 도메인을 유지 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 가상 머신은 Windows 운영 체제를 실행합니다. Windows는 각 프로세스에 개인 가상 주소 공간을 할당하여 실행 중인 각 프로세스에 대해 별도의 실행 도메인을 유지 관리합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |
