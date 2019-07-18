---
title: Vm에 대 한 검색 및 서버를 Azure Security Center에서 위협 | Microsoft Docs
description: 이 항목의 VM 및 서버 사용할 수 있는 Azure Security Center에서 경고를 표시합니다.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 5487b4f49f5dbf7b968cd45d40555c69b54c329a
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571583"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Vm 및 Azure Security Center에서에서 서버에 대 한 위협 검색

이 항목에서는 다음 운영 체제를 사용 하 여 Vm 및 서버에 대 한 다양 한 유형의 검색 방법 및 사용할 수 있는 경고를 표시합니다. 지원 되는 버전 목록은 참조 하세요 [플랫폼과 Azure Security Center에서 지 원하는 기능](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)합니다.

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

보안 센터 모니터링 하 고 Windows 기반 컴퓨터를 보호 하기 위해 Azure 서비스와 통합 됩니다.  Security Center 경고 및 재구성 제안을에서 이러한 모든 서비스를 사용 하기 쉬운 형식으로 제공합니다.

### Microsoft Server Defender ATP <a nanme="windows-atp"></a>

Azure Security Center 사용 하 여 Windows Defender Advanced Threat Protection (ATP)를 통합 하 여 해당 클라우드 워크 로드 보호 플랫폼을 확장 합니다. 이 포괄적인 끝점 검색 및 응답 (EDR) 기능을 제공 합니다.

> [!NOTE]
> Windows Server Defender ATP 센서는 Azure Security Center에 등록 된 Windows 서버에서 자동으로 활성화 됩니다.

Windows Server Defender ATP에서 위협을 검색, 경고를 트리거합니다. 경고는 Security Center 대시보드에 표시 됩니다. 대시보드에서 자세히 조사 공격의 범위를 파악 하는 데 Windows Defender ATP 콘솔에 피벗할 수 있습니다. Windows Server Defender ATP에 대 한 자세한 내용은 참조 하세요. [Windows Defender ATP 서비스에 등록 서버](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)합니다.

### 크래시 덤프 분석 <a nanme="windows-dump"></a>

소프트웨어가 충돌할 때 크래시 덤프는 충돌 시 메모리의 일부를 캡처합니다.

악성 프로그램에서 충돌이 발생 하거나 맬웨어를 포함 합니다. 보안 제품에서 탐지 되지를 방지 하려면 다양 한 형식의 맬웨어는 디스크에 기록 되는 암호화 소프트웨어 구성 요소 작성을 방지 하는 fileless 공격을 사용 하려면 디스크. 이러한 유형의 공격 기존의 디스크 기반 접근 방식을 사용 하 여 파악 하기가 어렵습니다.

그러나 메모리 분석을 사용 하 여 이러한 종류의 공격을 검색할 수 있습니다. Security Center는 크래시 덤프의 메모리를 분석 하 여 소프트웨어에서 취약점을 악용, 기밀 데이터에 액세스, 손상된 된 컴퓨터에서 은밀를 사용 하는 공격 기법을 검색할 수 있습니다. 이 호스트에 최소 성능에 영향을 사용 하 여 Security Center 백 엔드에 의해 이루어집니다.

> [!div class="mx-tableFixed"]

|경고|Description|
|---|---|
|**코드 삽입 발견**|코드 삽입은 실행 중인 프로세스 또는 스레드에 실행 파일 모듈을 삽입하는 작업입니다. 이 기술은 발견 하 고 제거 되 고 성공적으로 방지 하기 위해 자체를 숨기는 동안 데이터에 액세스 하는 맬웨어에서 사용 됩니다. <br/>이 경고는 삽입된 모듈이 크래시 덤프에 있는지를 알려줍니다. 악의적인 모듈과 악의적이 지 않은 삽입된 모듈을 구분할 Security Center는 삽입된 모듈이 의심 스러운 동작의 프로필을 준수 하는지를 확인 합니다.|
|**검색 된 의심 스러운 코드 세그먼트**|코드 세그먼트가 반사 삽입 및 프로세스 비우기와 같은 비표준 메서드를 사용 하 여 할당 된 것을 나타냅니다. 경고 기능에 대 한 컨텍스트를 제공 합니다. 처리 된 코드 세그먼트의 추가 특징 및 보고 된 코드 세그먼트의 동작을 제공 합니다.|
|**Shellcode 검색 됨**|Shellcode는 맬웨어가 소프트웨어 취약점을 악용한 후에 실행되는 페이로드입니다.<br/>이 경고는 크래시 덤프 분석이 악의적인 페이로드에서 일반적으로 수행 하는 동작을 보여 주는 실행 코드를 감지 했음을 나타냅니다. 악의적이 지 않은 소프트웨어가이 동작을 수행할 수도 있습니다, 있지만 보통 소프트웨어 개발 방법의 일반적인 아닙니다.|

### Fileless 공격 감지 <a nanme="windows-fileless"></a>

Azure에서 고객의 끝점을 대상으로 한 fileless 공격 정기적으로 표시 합니다.

검색을 방지 하려면 fileless 공격 메모리로 악의적인 페이로드를 삽입 합니다. 공격자가 페이로드 손상 된 프로세스의 메모리 내에서 유지 하 고 다양 한 범위의 악성 활동을 수행 합니다.

Fileless 공격 탐지를 사용 하 여 자동화 된 메모리 범죄 조사 기술을 fileless 공격 도구 키트, 기술 및 동작을 식별합니다. 이 솔루션은 주기적으로 런타임 시 컴퓨터를 검색 하 고 보안에 중요 한 프로세스의 메모리에서 직접 정보를 추출 합니다.

악용, 코드 삽입 및 악의적인 페이로드에서 실행의 증거를 찾습니다. Fileless 공격 검색 경고 심사, 상관관계 및 다운스트림 응답 시간을 가속화 하는 자세한 보안 경고를 생성 합니다. 이 방법은 큰 검색 범위를 제공 합니다. 이벤트 기반 EDR 솔루션을 보완 합니다.

> [!NOTE]
> 다운로드를 통해 Windows 경고를 시뮬레이션할 수 있습니다 [Azure Security Center 플레이 북](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef): 보안 경고는 제공 된 지침에 따라

> [!div class="mx-tableFixed"]

|경고|Description|
|---|---|
|**검색 fileless 공격 기법**|아래 지정 된 프로세스의 메모리 fileless 공격 도구 키트를 포함 합니다. Meterpreter 합니다. Fileless 공격 도구 키트 일반적으로 프로그램이에 없는 파일 시스템을 검색 하 여 기존 바이러스 백신 하기가 어렵습니다.|

### <a name="further-reading"></a>추가 정보

예제 및 Security Center 감지에 대 한 자세한 정보:

* [Azure Security Center는 사이버 공격을 감지를 자동화 하는 방법](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure Security Center에서 관리 도구를 사용 하 여 취약점을 검색 하는 방법](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

Security Center 수집 감사를 사용 하 여 Linux 컴퓨터에서 레코드 **auditd**하나씩의 가장 일반적인 Linux 프레임 워크를 감사 합니다. auditd는 오랫동안 및 메인 라인 커널에 살고 있는 관련 된 장점이 있습니다. 

### Linux auditd 경고 및 모니터링 에이전트 (MMA (Microsoft) 통합 <a name="linux-auditd"></a>

시스템 호출을 모니터링, 지정된 된 규칙 집합을 기준으로 필터링 하 및 소켓에 메시지를 작성 하는 일을 담당 하는 커널 수준 하위 시스템의 auditd 시스템 구성 됩니다. Security Center auditd 패키지 내에서 모니터링 에이전트 (MMA (Microsoft)의 기능을 통합합니다. 이 통합을 통해 모든 필수 구성 요소 없이 지원 되는 모든 Linux 배포판에 auditd 이벤트 컬렉션입니다.  

레코드인 수집 하 고 보강 하 고 Linux MMA 에이전트를 사용 하 여 이벤트에 집계 됩니다. Security Center 새 분석, 해당 활용 Linux 클라우드의 악의적인 행동을 감지에 신호를 보냅니다 및 온-프레미스 Linux 컴퓨터를 추가 하는 방법에 지속적으로 작동 합니다. Windows 기능와 마찬가지로 이러한 분석에 걸쳐 의심 스러운 프로세스, 수상한 로그인 시도, 커널 모듈 로드 및 컴퓨터를 공격을 받고이 또는 이미 위반 되었는지 나타내는 기타 작업.  

다음은 분석 공격 수명 주기의 다른 단계 걸쳐 우리가 하는 방법을 보여 주는 몇 가지 예입니다.

> [!div class="mx-tableFixed"]

|경고|설명|
|---|---|
|**프로그램에서 비정상적인 SSH 권한 있는 키 파일에 액세스를 표시 하는 프로세스**|알려진된 맬웨어 캠페인이 비슷합니다 메서드에서 SSH 권한 있는 키 파일에 액세스 합니다. 공격자가 컴퓨터에 영구 액세스 하려고 시도 하는이 액세스 나타낼 수 있습니다.|
|**검색 된 지 속성 시도**|호스트 데이터 분석에는 단일 사용자 모드에 대 한 시작 스크립트 설치 되어 있는지 감지 합니다. <br/>합법적인 프로세스 모드에서 실행 하는 데 필요한는 드문 이므로 지 속성을 보장 하기 위해 모든 실행 수준으로 공격자가 악의적인 프로세스를 추가이 나타낼 수 있습니다.|
|**검색 예약 된 작업의 조작**|호스트 데이터 분석에는 예약 된 작업의 가능한 조작을 검색 했습니다. 공격자는 종종 지 속성을 얻으려고 손상 했으므로 해당 컴퓨터에 예약 된 작업을 추가 합니다.|
|**의심 스러운 파일 타임 스탬프 수정**|호스트 데이터 분석에는 의심 스러운 타임 스탬프 수정 작업을 발견 했습니다. 공격자가 자주 복사할 타임 스탬프 올바른 기존 파일에서 이러한 새로 끌어 놓은 파일의 감지를 피하기 위해 새로운 도구|
|**새 사용자가 sudoers 그룹에 추가**|호스트 데이터 분석 사용자를 해당 멤버를 높은 권한으로 명령을 실행할 수 있도록 하는 sudoers 그룹에 추가한는 검색.|
|**Dhcp 클라이언트의 DynoRoot 취약점을 악용 하 가능성이**|호스트 데이터 분석 dhclient 스크립트의 상위 프로세스를 사용 하 여 비정상적인 명령의 실행을 검색 합니다.|
|**검색 된 의심 스러운 커널 모듈**|호스트 데이터 분석으로는 커널 모듈이 로드 되는 공유 개체 파일을 검색 합니다. 합법적인 활동 수 또는 손상 된 컴퓨터의 하나를 표시 합니다.|
|**디지털 통화 마이닝 감지와 관련 된 프로세스**|호스트 데이터 분석에는 일반적으로 연결 된 디지털 통화 마이닝 프로세스의 실행 검색|
|**외부 IP 주소로 잠재적인 포트 전달**|호스트 데이터 분석의 외부 IP 주소를 전달 하는 포트의 시작을 검색 합니다.|

> [!NOTE]
> 다운로드 하 여 Windows 경고를 시뮬레이션할 수 있습니다 [Azure Security Center 플레이 북: 보안 경고](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) 고 제공 된 지침을 따릅니다.


자세한 내용은 다음 문서를 참조하세요.  

* [손상 된 Linux 컴퓨터 공격 하는 경우 검색을 활용 하 여 Azure Security Center](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure Security Center는 Linux에서 새로운 취약점을 검색할 수 있습니다.](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 