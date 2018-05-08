---
title: Azure Security 및 Compliance Blueprint - FedRAMP 웹 응용 프로그램 자동화 - 식별 및 인증
description: FedRAMP용 웹 응용 프로그램 자동화 - 식별 및 인증
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 21b5c453716f99be26c8dd6400bb3489477b4956
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="identification-and-authentication-ia"></a>IA(식별 및 인증)

## <a name="nist-800-53-control-ia-1"></a>NIST 800-53 Control IA-1

> [!NOTE]
> 이러한 제어는 NIST와 미국 상무부에 의해 NIST 특별 출판물 800-53 개정 4의 일부로 정의됩니다. 각 제어에 대한 테스트 절차 및 지침에 대한 정보는 NIST 800-53 개정 4를 참조하세요.

#### <a name="identification-and-authentication-policy-and-procedures"></a>식별 및 인증 정책과 절차

**IA-1** 조직에서는 목적, 범위, 역할, 책임, 관리 약속, 조직 엔터티 간 조정 및 준수를 다루는 식별 및 인증 정책 및 식별 및 인증 정책과 관련 식별 및 인증 제어의 구현을 용이하게 하는 절차를 개발, 문서화하여 [할당: 조직에서 정의한 담당자 또는 역할]에게 배포하고, [할당: 조직에서 정의한 빈도]마다 현재 식별 및 인증 정책을 검토하고 업데이트하며, [할당: 조직에서 정의한 빈도]마다 식별 및 인증 절차를 검토하고 업데이트합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객의 엔터프라이즈 수준 식별 및 인증 정책과 절차로 이 제어를 충분히 해결할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-2"></a>NIST 800-53 Control IA-2

#### <a name="identification-and-authentication-organizational-users"></a>식별 및 인증(조직 사용자)

**IA-2** 정보 시스템은 조직 사용자(또는 조직 사용자를 대신하는 프로세스)를 고유하게 식별 및 인증합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에서 만든 계정에는 고유 식별자가 있습니다. 고유하지 않은 식별자가 있는 기본 제공 계정은 비활성화되거나 제거됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-2-1"></a>NIST 800-53 Control IA-2 (1)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>식별 및 인증(조직 사용자) | 네트워크를 통해 권한 있는 계정에 액세스

**IA-2 (1)** 정보 시스템은 네트워크를 통해 권한 있는 계정에 액세스하기 위한 다단계 인증을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 네트워크를 통해 권한 있는 계정에 액세스하기 위한 다단계 인증을 구현할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-2-2"></a>NIST 800-53 Control IA-2 (2)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>식별 및 인증(조직 사용자) | 권한 없는 계정에 대한 네트워크 액세스

**IA-2 (2)** 정보 시스템은 네트워크를 통해 권한 없는 계정에 액세스하기 위한 다단계 인증을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 네트워크를 통해 권한 없는 계정에 액세스하기 위한 다단계 인증을 구현할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-2-3"></a>NIST 800-53 Control IA-2 (3)

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>식별 및 인증(조직 사용자) | 권한 있는 계정에 대한 로컬 액세스

**IA-2 (3)** 정보 시스템은 권한 있는 계정에 로컬로 액세스하기 위한 다단계 인증을 구현합니다.

**책임:** `Azure Only`

|||
|---|---|
| **고객** | 고객은 Azure 데이터 센터의 시스템 리소스에 로컬로 액세스할 권한이 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 물리적 액세스가 필요한 경우를 제외하고 로컬 액세스를 허용하지 않습니다. 로컬 관리자 액세스는 구성원 서버에 네트워크 문제가 있어서 도메인 인증이 작동하지 않는 상황을 해결해야 할 때만 사용해야 합니다. <br /> Azure는 환경에 물리적으로 액세스하는 데 필요한 액세스 제어 메커니즘을 통해 로컬 액세스에 대한 다단계 인증을 구현합니다. 시스템 경계 내에 모든 Azure 인프라 시스템을 포함하는 Azure 데이터 센터 내의 공간은 회사 스마트 카드 배지 액세스 및 생체 인식 장치에 대한 요구 사항을 포함하여 다양한 물리적 보안 메커니즘을 통해 제한됩니다. 진입점에서 Azure 데이터 센터 공동 구역에 물리적으로 액세스하려면 두 가지 인증 양식이 모두 필요합니다. |


 ### <a name="nist-800-53-control-ia-2-4"></a>NIST 800-53 Control IA-2 (4)

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>식별 및 인증(조직 사용자) | 권한 없는 계정에 대한 로컬 액세스

**IA-2 (4)** 정보 시스템은 권한 없는 계정에 로컬로 액세스하기 위한 다단계 인증을 구현합니다.

**책임:** `Azure Only`

|||
|---|---|
| **고객** | 고객은 Azure 데이터 센터의 시스템 리소스에 로컬로 액세스할 권한이 없습니다. |
| **공급자(Microsoft Azure)** | Microsoft Azure는 Microsoft Azure Government 담당자가 사용하는 모든 Microsoft Azure Government 계정을 권한 있는 계정으로 간주합니다. 스마트 카드와 핀을 사용하여 모든 Microsoft Azure Government 담당자 계정에 대해 다단계 인증이 구현되며, 로컬 액세스도 포함됩니다. |


 ### <a name="nist-800-53-control-ia-2-5"></a>NIST 800-53 Control IA-2 (5)

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>식별 및 인증(조직 사용자) | 그룹 인증

**IA-2 (5)** 그룹 인증자가 사용되면 조직에서는 개인 인증자를 사용하여 개인을 인증해야 합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에서 배포한 리소스에 공유/그룹 계정이 사용하도록 설정되어 있지 않습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-2-8"></a>NIST 800-53 Control IA-2 (8)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>식별 및 인증(조직 사용자) | 권한 있는 계정에 대한 네트워크 액세스 - 재생 공격 방어

**IA-2 (8)** 정보 시스템은 네트워크를 통해 권한 있는 계정에 액세스하기 위한 재생 공격 방어 인증을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 리소스에 대한 액세스는 Azure Active Directory, Active Directory 및 Windows 운영 체제의 기본 제공 Kerberos 기능을 통해 재생 공격으로부터 보호됩니다. Kerberos 인증에서, 클라이언트가 보내는 인증자에는 암호화된 IP 목록, 클라이언트 타임스탬프, 티켓 수명 등의 추가 데이터가 포함됩니다. 패킷이 재생되면 타임스탬프가 검사됩니다. 타임스탬프가 이전 인증자보다 빠르거나 동일하면 해당 패킷이 거부됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-2-9"></a>NIST 800-53 Control IA-2 (9)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>식별 및 인증(조직 사용자) | 권한 없는 계정에 대한 네트워크 액세스 - 재생 공격 방어

**IA-2 (9)** 정보 시스템은 네트워크를 통해 권한 없는 계정에 액세스하기 위한 재생 공격 방어 인증을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 청사진에서 배포한 리소스에 대한 액세스는 Azure Active Directory, Active Directory 및 Windows 운영 체제의 기본 제공 Kerberos 기능을 통해 재생 공격으로부터 보호됩니다. Kerberos 인증에서, 클라이언트가 보내는 인증자에는 암호화된 IP 목록, 클라이언트 타임스탬프, 티켓 수명 등의 추가 데이터가 포함됩니다. 패킷이 재생되면 타임스탬프가 검사됩니다. 타임스탬프가 이전 인증자보다 빠르거나 동일하면 해당 패킷이 거부됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-2-11"></a>NIST 800-53 Control IA-2 (11)

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>식별 및 인증(조직 사용자) | 원격 액세스 - 별도의 장치

**IA-2 (11)** 정보 시스템은 권한 있는 계정 및 권한 없는 계정에 원격으로 액세스하기 위한 다단계 인증을 구현하며, 인증 중 하나는 액세스 권한을 얻는 시스템과 분리된 장치에서 제공하고 해당 장치는 [할당: 조직에서 정의한 메커니즘 요구 사항의 강도]를 충족합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에 원격으로 액세스하기 위한 다단계 인증을 구현할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-2-12"></a>NIST 800-53 Control IA-2 (12)

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>식별 및 인증(조직 사용자) | Piv 자격 증명 수락

**IA-2 (12)** 정보 시스템은 PIV(개인 ID 검증) 자격 증명을 수락하고 전자적으로 검증합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 PIV(개인 ID 검증) 자격 증명을 수락하고 검증할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-3"></a>NIST 800-53 Control IA-3

#### <a name="device-identification-and-authentication"></a>장치 식별 및 인증

**IA-3** 정보 시스템은 [할당: 조직에서 정의한 특정 장치 및/또는 특정 유형의 장치]를 고유하게 식별 및 인증한 후 [선택(하나 이상): 로컬, 원격, 네트워크] 연결을 설정합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 장치 식별 및 인증을 구현할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-4a"></a>NIST 800-53 Control IA-4.a

#### <a name="identifier-management"></a>식별자 관리

**IA-4.a** 조직에서는 [할당: 조직에서 정의한 담당자 또는 역할]로부터 권한을 부여받아 개인, 그룹, 역할 또는 장치 식별자를 할당함으로써 정보 시스템 식별자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 리소스에 대한 식별자(즉, 개인, 그룹, 역할 및 장치)를 관리할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-4b"></a>NIST 800-53 Control IA-4.b

#### <a name="identifier-management"></a>식별자 관리

**IA-4.b** 조직에서는 개인, 그룹, 역할 또는 장치를 식별하는 식별자를 선택하여 정보 시스템 식별자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 개인 계정에 대해 고객이 지정한 식별자를 배포하는 동안 이 Blueprint가 표시됩니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-4c"></a>NIST 800-53 Control IA-4.c

#### <a name="identifier-management"></a>식별자 관리

**IA-4.c** 조직에서는 의도하는 개인, 그룹, 역할 또는 장치에 식별자를 할당하여 정보 시스템 식별자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 리소스에 대한 식별자(즉, 개인, 그룹, 역할 및 장치)를 관리할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-4d"></a>NIST 800-53 Control IA-4.d

#### <a name="identifier-management"></a>식별자 관리

**IA-4.d** 조직에서는 [할당: 조직에서 정의한 기간]에는 식별자 재사용을 금지하여 정보 시스템 식별자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Active Directory 및 로컬 Windows 운영 체제 계정에는 고유한 SID(보안 식별자)가 할당됩니다. Azure Active Directory 계정에는 전역적으로 고유한 개체 ID가 할당됩니다. 이러한 고유 ID는 재사용할 수 없습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-4e"></a>NIST 800-53 Control IA-4.e

#### <a name="identifier-management"></a>식별자 관리

**IA-4.e** 조직에서는 [할당: 조직에서 정의한 비활성 기간]이 지나면 식별자를 비활성화하여 정보 시스템 식별자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 35일간 계정 활동이 없으면 Active Directory가 자동으로 계정을 비활성화하도록 예약된 작업을 구현합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-4-4"></a>NIST 800-53 Control IA-4 (4)

#### <a name="identifier-management--identify-user-status"></a>식별자 관리 | 사용자 상태 식별

**IA-4 (4)** 조직에서는 [할당: 조직에서 정의한 개인의 상태를 식별하는 특징]으로 각 개인을 고유하게 식별합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Active Directory 및 Active Directory는 해당 식별자에 적용되는 명명 규칙을 사용하여 계약자, 공급 업체 및 다른 사용자 유형을 나타내는 것을 지원합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-5a"></a>NIST 800-53 Control IA-5.a

#### <a name="authenticator-management"></a>인증자 관리

**IA-5.a** 조직에서는 초기 인증자 배포 과정에서 인증자를 수신하는 개인, 그룹, 역할 또는 장치 ID를 확인하여 정보 시스템 인증자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 인증자를 관리할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-5b"></a>NIST 800-53 Control IA-5.b

#### <a name="authenticator-management"></a>인증자 관리

**IA-5.b** 조직에서는 조직에서 정의한 인증자에 대한 초기 인증자 콘텐츠를 설정하여 정보 시스템 인증자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint가 만든 계정의 모든 초기 인증자 콘텐츠는 배포 과정에서 고객이 지정하면 확인되는 IA-5 (1)에 명시된 요구 사항을 충족합니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-5c"></a>NIST 800-53 Control IA-5.c

#### <a name="authenticator-management"></a>인증자 관리

**IA-5.c** 조직에서는 인증자의 강도를 원래 용도에 충분한 수준으로 유지하여 정보 시스템 인증자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint가 사용하는 인증자는 FedRAMP에서 요구하는 강도 요구 사항을 충족합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-5d"></a>NIST 800-53 Control IA-5.d

#### <a name="authenticator-management"></a>인증자 관리

**IA-5.d** 조직에서는 초기 인증자 배포, 인증자 분실/손실 또는 손상, 인증자 취소에 대한 관리 절차를 설정 및 구현하여 정보 시스템 인증자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 인증자를 관리할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-5e"></a>NIST 800-53 Control IA-5.e

#### <a name="authenticator-management"></a>인증자 관리

**IA-5.e** 조직에서는 정보 시스템을 설치하기 전에 인증자의 기본 콘텐츠를 변경하여 정보 시스템 인증자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint의 구성 요소에 대한 모든 인증자는 기본값에서 변경되었습니다. 이 솔루션을 배포하는 동안 고객이 인증자를 지정합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-5f"></a>NIST 800-53 Control IA-5.f

#### <a name="authenticator-management"></a>인증자 관리

**IA-5.f** 조직에서는 인증자의 최소 및 최대 수명 제한과 재사용 조건을 설정하여 정보 시스템 인증자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 인증자를 관리할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-5g"></a>NIST 800-53 Control IA-5.g

#### <a name="authenticator-management"></a>인증자 관리

**IA-5.g** 조직에서는 [할당: 조직에서 정의한 인증자 유형별 시간]마다 인증자를 변경/새로 고쳐서 정보 시스템 인증자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 배포되는 모든 가상 머신이 조인되는 도메인 컨트롤러를 배포합니다. 암호 수명 제한(60일)을 구현하도록 그룹 정책이 설정 및 구성됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-5h"></a>NIST 800-53 Control IA-5.h

#### <a name="authenticator-management"></a>인증자 관리

**IA-5.h** 조직에서는 인증자 콘텐츠가 무단으로 공개 및 수정되지 않도록 보호하여 정보 시스템 인증자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 인증자 콘텐츠가 무단으로 공개 및 수정되지 않도록 보호하는 Key Vault를 구현합니다. 인증자인 배포 계정에 대한 Azure 암호, 가상 머신 관리자 암호, SQL Server 서비스 계정 암호가 Key Vault에 저장됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-5i"></a>NIST 800-53 Control IA-5.i

#### <a name="authenticator-management"></a>인증자 관리

**IA-5.i** 조직에서는 개인에게 인증자를 보호하는 특정 보안 조치를 선택하여 장치에 구현하도록 요구함으로써 정보 시스템 인증자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 인증자 콘텐츠가 무단으로 공개 및 수정되지 않도록 보호하는 Key Vault를 구현합니다. 인증자인 배포 계정에 대한 Azure 암호, 가상 머신 관리자 암호, SQL Server 서비스 계정 암호가 Key Vault에 저장됩니다. Key Vault는 HSM(하드웨어 보안 모듈)으로 보호되는 키를 사용하여 키와 비밀(예: 인증 키, 저장소 계정 키, 데이터 암호화 키 및 암호)을 암호화합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-5j"></a>NIST 800-53 Control IA-5.j

#### <a name="authenticator-management"></a>인증자 관리

**IA-5.j** 조직에서는 그룹/역할 계정의 멤버 자격이 변경되면 그룹/역할 계정의 인증자를 변경하여 정보 시스템 인증자를 관리합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에서 배포한 리소스에 공유/그룹 계정이 사용하도록 설정되어 있지 않습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-1a"></a>NIST 800-53 Control IA-5 (1).a

#### <a name="authenticator-management--password-based-authentication"></a>인증자 관리 | 암호 기반 인증

**IA-5 (1).a** 암호 기반 인증을 위한 정보 시스템은 [할당: 각 유형의 최소 요구 사항을 포함하여 조직에서 대/소문자 구분, 문자 수, 대문자 조합, 소문자 조합, 숫자 및 특수 문자에 대해 정의한 요구 사항]의 최소 암호 복잡성을 적용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 배포되는 모든 가상 머신이 조인되는 도메인 컨트롤러를 배포합니다. 가상 머신 로컬 계정 및 AD 계정에 대한 암호 복잡성 요구 사항을 적용하도록 그룹 정책이 설정 및 구성됩니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-1b"></a>NIST 800-53 Control IA-5 (1).b

#### <a name="authenticator-management--password-based-authentication"></a>인증자 관리 | 암호 기반 인증

**IA-5 (1).b** 암호 기반 인증을 위한 정보 시스템은 새 암호를 만들 때 [할당: 조직에서 정의한 개수] 이상의 변경된 문자 수를 적용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스 내에서 암호 기반 인증을 사용할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-1c"></a>NIST 800-53 Control IA-5 (1).c

#### <a name="authenticator-management--password-based-authentication"></a>인증자 관리 | 암호 기반 인증

**IA-5 (1).c** 암호 기반 인증을 위한 정보 시스템은 암호화로 보호되는 암호만 저장하고 전송합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Directory는 모든 암호가 저장 및 전송되는 동안 암호화로 보호하는 데 사용됩니다. Active Directory가 저장하고 Windows 가상 머신에 로컬로 배포하는 암호는 기본 보안 기능의 일부로 자동 해시됩니다. 전송되는 인증자를 보호하기 위해 TLS를 사용하여 원격 데스크톱 인증 세션의 보안을 유지합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-1d"></a>NIST 800-53 Control IA-5 (1).d

#### <a name="authenticator-management--password-based-authentication"></a>인증자 관리 | 암호 기반 인증

**IA-5 (1).d** 암호 기반 인증을 위한 정보 시스템은 [할당: 조직에서 정의한 최소 수명, 최대 수명]의 암호 최소 및 최대 수명 제한을 적용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 배포되는 모든 가상 머신이 조인되는 도메인 컨트롤러를 배포합니다. 로컬 계정 및 AD에 최소 수명(1일) 및 최대 수명(60 일) 제한을 적용하도록 그룹 정책이 설정 및 구성됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-1e"></a>NIST 800-53 Control IA-5 (1).e

#### <a name="authenticator-management--password-based-authentication"></a>인증자 관리 | 암호 기반 인증

**IA-5 (1).e** 암호 기반 인증을 위한 정보 시스템은 [할당: 조직에서 정의한 수]회 생성 동안 암호 재사용을 금지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint는 배포되는 모든 가상 머신이 조인되는 도메인 컨트롤러를 배포합니다. 로컬 계정 및 AD 계정에 대한 재사용 조건(24개 암호)을 적용하도록 그룹 정책이 설정 및 구성됩니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-1f"></a>NIST 800-53 Control IA-5 (1).f

#### <a name="authenticator-management--password-based-authentication"></a>인증자 관리 | 암호 기반 인증

**IA-5 (1).f** 암호 기반 인증을 위한 정보 시스템은 임시 암호를 사용하여 시스템에 로그온하는 즉시 영구 암호로 변경하는 것을 허용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Azure Active Directory는 정보 시스템에 대한 액세스 제어를 관리하는 데 사용됩니다. 계정이 처음으로 생성되거나 임시 암호가 생성되면 Azure Active Directory를 사용하여 사용자가 다음에 로그인할 때 암호를 변경하도록 요청합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-2a"></a>NIST 800-53 Control IA-5 (2).a

#### <a name="authenticator-management--pki-based-authentication"></a>인증자 관리 | Pki 기반 인증

**IA-5 (2).a** Pki 기반 인증을 위한 정보 시스템은 인증서 상태 정보 검사를 포함하여 허용된 트러스트 앵커에 대한 인증 경로를 생성 및 검증하여 인증의 유효성을 검사합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스 내에서 PKI 기반 인증을 사용할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-2b"></a>NIST 800-53 Control IA-5 (2).b

#### <a name="authenticator-management--pki-based-authentication"></a>인증자 관리 | Pki 기반 인증

**IA-5 (2).b** PKI 기반 인증을 위한 정보 시스템은 해당 개인 키에 대한 권한 있는 액세스를 적용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스 내에서 PKI 기반 인증을 사용할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-2c"></a>NIST 800-53 Control IA-5 (2).c

#### <a name="authenticator-management--pki-based-authentication"></a>인증자 관리 | Pki 기반 인증

**IA-5 (2).c** PKI 기반 인증을 위한 정보 시스템은 인증된 ID를 개인 또는 그룹 계정에 매핑합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스 내에서 PKI 기반 인증을 사용할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-2d"></a>NIST 800-53 Control IA-5 (2).d

#### <a name="authenticator-management--pki-based-authentication"></a>인증자 관리 | Pki 기반 인증

**IA-5 (2).d** PKI 기반 인증을 위한 정보 시스템은 네트워크를 통해 해지 정보에 액세스할 수 없는 경우에 경로 검색 및 유효성 검사를 지원할 수 있도록 해지 데이터의 로컬 캐시를 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스 내에서 PKI 기반 인증을 사용할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-3"></a>NIST 800-53 Control IA-5 (3)

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>인증자 관리 | 직접 또는 신뢰할 수 있는 타사 등록

**IA-5 (3)** 조직에서는 [할당: 조직에서 정의한 담당자 또는 역할]이 권한을 부여한 [할당: 조직에서 정의한 등록 기관] 전에 [할당: 조직에서 정의한 유형의 인증자 및/또는 특정 인증자]를 받는 등록 프로세스를 [선택: 직접 또는 신뢰할 수 있는 타사를 통해] 수행할 것을 요구합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 인증자를 등록할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-4"></a>NIST 800-53 Control IA-5 (4)

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>인증자 관리 | 암호 강도 결정에 대한 자동 지원

**IA-5 (4)** 조직에서는 자동화 도구를 사용하여 암호 인증자가 [할당: 조직에서 정의한 요구 사항]을 충족하기에 충분히 강력한지 여부를 확인합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint를 사용하여 배포된 사용자 계정은 AD 및 로컬 사용자 계정을 포함하고 있습니다. 둘 다 암호를 변경하는 동안 초기 암호를 만들 수 있도록 설정된 암호 요구 사항을 준수하는 메커니즘을 제공합니다. Azure Active Directory는 암호 인증자가 IA-5 (1)에 설정된 암호 길이, 복잡성, 회전 및 수명 제한을 만족할 만큼 충분히 강력한지 확인하는 데 사용되는 자동화 도구입니다. Azure Active Directory는 암호 생성 시 이러한 표준을 충족하도록 암호 인증자 강도를 보장합니다. 이 솔루션을 배포하는 데 사용된 사용자 지정 암호를 검사하여 암호 강도 요구 사항을 충족하는지 확인합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-6"></a>NIST 800-53 Control IA-5 (6)

#### <a name="authenticator-management--protection-of-authenticators"></a>인증자 관리 | 인증자 보호

**IA-5 (6)** 조직에서는 인증자를 사용하여 액세스를 허용하는 정보 보안 범주에 비례하여 인증자를 보호합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 인증자를 보호할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-7"></a>NIST 800-53 Control IA-5 (7)

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>인증자 관리 | 암호화되지 않은 정적 인증자가 포함되지 않음

**IA-5 (7)** 조직에서는 암호화되지 않은 정적 인증자가 응용 프로그램에 포함되거나 스크립트에 액세스하거나 함수 키에 저장되지 않도록 보장합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 암호화되지 않은 정적 인증자가 응용 프로그램에 포함되거나, 스크립트에 액세스하거나, 이 Blueprint에서 배포한 함수 키에 저장되어도 아무 쓸모가 없습니다. 인증자를 사용하는 스크립트 또는 응용 프로그램은 매번 사용하기 전에 Azure Key Vault 컨테이너를 호출합니다. Azure Key Vault 컨테이너에 대한 액세스를 감사하며, 상응하는 Azure Key Vault 컨테이너 호출 없이 서비스 계정을 사용하여 시스템에 액세스하는 경우 이 금지 사항 위반을 발견할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-8"></a>NIST 800-53 Control IA-5 (8)

#### <a name="authenticator-management--multiple-information-system-accounts"></a>인증자 관리 | 여러 정보 시스템 계정

**IA-5 (8)** 조직에서는 개인이 여러 정보 시스템에 계정을 갖고 있어서 발생할 수 있는 보안 위험을 관리하기 위해 [할당: 조직에서 정의한 보안 조치]를 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 여러 시스템에 계정을 갖고 있는 개인과 관련된 위험을 관리하기 위해 엔터프라이즈 수준의 보안 조치를 사용할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-11"></a>NIST 800-53 Control IA-5 (11)

#### <a name="authenticator-management--hardware-token-based-authentication"></a>인증자 관리 | 하드웨어 토큰 기반 인증

**IA-5 (11)** 하드웨어 토큰 기반 인증을 위한 정보 시스템은 [할당: 조직에서 정의한 토큰 품질 요구 사항]을 충족하는 메커니즘을 사용합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 하드웨어 토큰 기반 인증 품질 요구 사항을 충족하는 메커니즘을 사용할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-5-13"></a>NIST 800-53 Control IA-5 (13)

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>인증자 관리 | 캐시된 인증자 만료

**IA-5 (13)** 정보 시스템은 [할당: 조직에서 정의한 기간]이 경과하면 캐시된 인증자 사용을 금지합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint를 통해 배포된 리소스는 캐시된 인증자의 사용을 허용하도록 구성되지 않습니다. 배포된 가상 머신을 인증하려면 인증 시 인증자를 입력해야 합니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-6"></a>NIST 800-53 Control IA-6

#### <a name="authenticator-feedback"></a>인증자 피드백

**IA-6** 정보 시스템은 권한 없는 개인이 정보를 악용/사용할 수 없도록 인증 프로세스 중에 인증 정보 피드백을 보이지 않게 가립니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 이 Blueprint에서 배포한 리소스는 원격 데스크톱을 통해 액세스되며 Windows 인증을 사용합니다. Windows 인증 세션의 기본 동작은 인증 세션 중에 암호를 입력하면 암호를 마스킹하는 것입니다.  |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-7"></a>NIST 800-53 Control IA-7

#### <a name="cryptographic-module-authentication"></a>암호화 모듈 인증

**IA-7** 정보 시스템은 인증과 관련된 해당 연방법, 임원의 명령, 지시, 정책, 규정, 표준 및 지침을 충족하는 암호화 모듈에 인증하기 위한 메커니즘을 구현합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | Windows 인증, 원격 데스크톱 및 BitLocker가 이 Blueprint에서 사용됩니다. 이러한 구성 요소는 FIPS 140에서 검증된 암호화 모듈을 사용하도록 구성할 수 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ## <a name="nist-800-53-control-ia-8"></a>NIST 800-53 Control IA-8

#### <a name="identification-and-authentication-non-organizational-users"></a>식별 및 인증(비 조직 사용자)

**IA-8** 정보 시스템은 비 조직 사용자(또는 비 조직 사용자를 대신하는 프로세스)를 고유하게 식별 및 인증합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 고객 배포 리소스에 액세스하는 비 조직 사용자를 식별하고 인증할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-8-1"></a>NIST 800-53 Control IA-8 (1)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>식별 및 인증(비 조직 사용자) | 다른 기관의 Piv 자격 증명 수락

**IA-8 (1)** 정보 시스템은 다른 연방 기관의 PIV(개인 ID 검증) 자격 증명을 수락하고 전자적으로 검증합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 다른 연방 기관에서 발급한 PIV(개인 ID 검증) 자격 증명을 수락하고 검증할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-8-2"></a>NIST 800-53 Control IA-8 (2)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>식별 및 인증(비 조직 사용자) | 타사 자격 증명 수락

**IA-8 (2)** 정보 시스템은 FICAM 승인을 받은 타사 자격 증명만 수락합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 FICAM(Federal Identity, Credential, and Access Management) Trust Framework Solutions 이니셔티브의 승인을 받은 타사 자격 증명만 수락할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-8-3"></a>NIST 800-53 Control IA-8 (3)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>식별 및 인증(비 조직 사용자) | Ficam 승인을 받은 제품 사용

**IA-8 (3)** 조직에서는 [할당: 조직에서 정의한 정보 시스템]에서 FICAM 승인을 받은 정보 시스템 구성 요소만 사용하여 타사 자격 증명을 수락합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 FICAM(Federal Identity, Credential, and Access Management) Trust Framework Solutions 이니셔티브의 승인을 받은 리소스만 사용하여 타사 자격 증명을 수락할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |


 ### <a name="nist-800-53-control-ia-8-4"></a>NIST 800-53 Control IA-8 (4)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>식별 및 인증(비 조직 사용자) | Ficam 승인을 받은 프로필 사용

**IA-8 (4)** 정보 시스템은 FICAM에서 발급한 프로필을 준수합니다.

**책임:** `Customer Only`

|||
|---|---|
| **고객** | 고객은 FICAM(Federal Identity, Credential, and Access Management) Trust Framework Solutions 이니셔티브에서 발급한 프로필을 준수할 책임이 있습니다. |
| **공급자(Microsoft Azure)** | 해당 없음 |
