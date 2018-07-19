---
title: Azure 인프라 무결성
description: 이 아티클에서는 Azure 인프라의 무결성을 확인합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 867bc66a68bec662153d8336e649cf46df02f101
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901319"
---
# <a name="azure-infrastructure-integrity"></a>Azure 인프라 무결성

## <a name="software-installation"></a>소프트웨어 설치
Azure 환경에 설치된 소프트웨어 스택의 모든 구성 요소는 Microsoft SDL(Security Development Lifecycle) 프로세스에 따라 빌드된 사용자 지정입니다. 모든 소프트웨어 구성 요소(OS 이미지 및 SQL Database 포함)는 변경 및 Release Management 프로세스의 일부로 배포됩니다. 모든 노드에서 실행되는 OS는 Windows Server 2008 또는 Windows Server 2012의 사용자 지정된 버전입니다. 정확한 버전은 재생할 OS에 대해 정해진 역할에 따라 FC에 의해 선택됩니다. 또한 호스트 OS는 승인되지 않은 소프트웨어 구성 요소의 설치를 허용하지 않습니다.

일부 Microsoft Azure 구성 요소(예: RDFE, 개발자 포털 등)는 게스트 OS에서 실행되는 게스트 VM에서 Azure 고객으로 배포됩니다.

## <a name="virus-scans-on-builds"></a>빌드에 대한 바이러스 검사
Azure 소프트웨어 구성 요소(OS 포함) 빌드는 MEP(Microsoft Endpoint Protection) 바이러스 백신 도구를 사용하여 바이러스 검사를 거쳐야 합니다. 각 바이러스 검사는 연결된 빌드 디렉터리 내에서 로그를 만듭니다. 여기서는 검사된 내용 및 검사 결과를 자세히 설명합니다. 바이러스 검사는 Azure 내에서 모든 구성 요소에 대한 빌드 소스 코드의 일부입니다. 명확하고 성공적인 바이러스 검사를 하지 않으면 코드는 프로덕션으로 이동되지 않습니다. 문제가 명시되지 않으면 빌드가 중단된 다음, Microsoft Security 내에서 보안 팀으로 이동하여 "악의적인" 코드가 빌드를 입력한 위치를 식별합니다.

## <a name="closedlocked-environment"></a>닫힌/잠긴 환경
기본적으로 Azure 인프라 노드 및 게스트 VM에는 사용자 계정이 생성되지 않았습니다. 또한 기본 Windows 관리자 계정이 비활성화됩니다. Microsoft Azure 라이브 지원(WALS)의 관리자는 적절한 인증을 사용하여 이러한 머신에 로그인하고 응급 복구 작업에 대한 Azure 프로덕션 네트워크를 관리합니다.

## <a name="microsoft-azure-sql-database-authentication"></a>Microsoft Azure SQL Database 인증
SQL Server의 모든 구현과 마찬가지로 사용자 계정 관리는 긴밀하게 제어되어야 합니다. Microsoft Azure SQL Database는 SQL Server 인증만 지원합니다. 강력한 암호를 사용하고 특정 권한으로 구성된 사용자 계정은 고객의 데이터 보안 모델을 보완하는 데에도 사용되어야 합니다.

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>MSFT CorpNet과 Microsoft Azure 클러스터 간 방화벽/ACL
서비스 플랫폼과 MS 회사 네트워크 간의 ACL/방화벽은 권한이 없는 내부자 액세스로부터 Microsoft Azure SQL Database를 보호합니다. 또한 Microsoft CorpNet의 IP 주소 범위에서 사용자만이 WinFabric 플랫폼 관리 엔드포인트에 액세스할 수 있습니다.

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Azure SQL DB 클러스터에서 노드 간 방화벽/ACL
심층 방어 전략의 일환인 추가적인 보호로 Microsoft Azure SQL DB 클러스터의 노드 간에 ACL/방화벽이 구현되었습니다. WinFabric 플랫폼 클러스터 내부의 모든 통신뿐만 아니라 모든 실행 중인 코드를 신뢰할 수 있습니다.

## <a name="custom-mas-watchdogs"></a>사용자 지정 MA(Watchdog)
Microsoft Azure SQL Database는 Microsoft Azure SQL DB 클러스터의 상태를 모니터링하기 위해 watchdog라는 사용자 지정 MA를 사용합니다.

## <a name="web-protocols"></a>웹 프로토콜

### <a name="role-instance-monitoring-and-restart"></a>역할 인스턴스 모니터링 및 다시 시작
Azure를 사용하면 배포되고 실행 중인 모든 역할(인터넷 연결 웹 또는 백 엔드 처리 작업자 역할)이 지속적인 상태 모니터링에 종속되어 효과적이고 효율적으로 프로비전한 서비스를 제공하는지 확인합니다. 응용 프로그램에서 심각한 결함이 호스트되거나 역할 인스턴스 자체 내에서 기본 구성 문제에 의해 역할이 비정상 상태가 된 이벤트에서 Microsoft Azure FC는 역할 인스턴스 내의 문제를 감지하고 수정 상태를 시작합니다.

### <a name="compute-connectivity"></a>Compute 연결
Azure에서는 배포된 응용 프로그램/서비스가 표준 웹 기반 프로토콜을 통해 연결될 수 있는지 확인합니다. 인터넷 연결 웹 역할 가상 인스턴스는 외부 인터넷과 연결되고 웹 사용자에 의해 직접 연결될 수 있습니다. 백 엔드 처리 작업자 역할 가상 인스턴스는 작업자 역할이 공개적으로 액세스할 수 있는 웹 역할 가상 인스턴스 대신 수행하는 작업의 민감도 및 무결성을 보호하기 위해 외부 인터넷에 연결되어 있지만 외부 웹 사용자가 직접 액세스할 수 없습니다.

## <a name="next-steps"></a>다음 단계
Azure 인프라를 보호하기 위해 Microsoft에서 수행하는 작업에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 시설, 구역 및 물리적 보안](azure-physical-security.md)
- [Azure 인프라 가용성](azure-infrastructure-availability.md)
- [Azure 정보 시스템 구성 요소 및 경계](azure-infrastructure-components.md)
- [Azure 네트워크 아키텍처](azure-infrastructure-network.md)
- [Azure 프로덕션 네트워크](azure-production-network.md)
- [Microsoft Azure SQL Database 보안 기능](azure-infrastructure-sql.md)
- [Azure 프로덕션 운영 및 관리](azure-infrastructure-operations.md)
- [Azure 인프라 모니터링](azure-infrastructure-monitoring.md)
- [Azure에서 고객 데이터 보호](azure-protection-of-customer-data.md)
