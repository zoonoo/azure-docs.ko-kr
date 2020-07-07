---
title: 연결 문제 해결-Hyperscale (Citus)-Azure Database for PostgreSQL
description: Azure Database for PostgreSQL-Hyperscale (Citus)에 대 한 연결 문제를 해결 하는 방법을 알아봅니다.
keywords: PostgreSQL 연결, 연결 문자열, 연결 문제, 일시적 오류, 연결 오류
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: abcc979bb36fe0acb663a511875a1e186c2c0739
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82583974"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale (Citus)에 대 한 연결 문제 해결

연결 문제는 다음과 같은 여러 가지 이유로 인해 발생할 수 있습니다.

* 방화벽 설정
* 연결 제한 시간
* 잘못 된 로그인 정보
* 서버 그룹에 대 한 연결 제한에 도달 했습니다.
* 서비스 인프라 관련 문제
* 서비스 유지 관리
* 코디네이터 노드가 새 하드웨어로 장애 조치 (failover)

일반적으로 Hyperscale에 대 한 연결 문제는 다음과 같이 분류할 수 있습니다.

* 일시적 오류(단기 또는 일시적)
* 영구적 또는 일시적이지 않은 오류(정기적으로 되풀이되는 오류)

## <a name="troubleshoot-transient-errors"></a>일시적인 오류 문제 해결

일시적인 오류는 여러 가지 이유로 발생 합니다. 가장 일반적인 작업에는 시스템 유지 관리, 하드웨어 또는 소프트웨어에 대 한 오류 및 코디네이터 노드 vCore 업그레이드가 포함 됩니다.

하이퍼 확장 서버 그룹 노드에 대해 고가용성을 사용 하도록 설정 하면 이러한 유형의 문제를 자동으로 완화할 수 있습니다. 그러나 응용 프로그램은 계속 해 서 연결을 손실할 준비가 되어 있습니다. 또한 큰 트랜잭션이 장기 실행 복구를 야기 하는 경우와 같이 다른 이벤트를 완화 하는 데 더 많은 시간이 걸릴 수 있습니다.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>일시적인 연결 문제를 해결하는 단계

1. 응용 프로그램에서 오류를 보고 하는 동안 발생 한 알려진 작동 중단에 대 한 [Microsoft Azure 서비스 대시보드](https://azure.microsoft.com/status) 를 확인 합니다.
2. Citus (Hyperscale)와 같은 클라우드 서비스에 연결 하는 응용 프로그램은 일시적인 오류를 예측 하 고 정상적으로 반응 해야 합니다. 예를 들어 응용 프로그램은 사용자에 게 응용 프로그램 오류로 지정 하는 대신 재시도 논리를 구현 하 여 이러한 오류를 처리 해야 합니다.
3. 서버 그룹은 해당 리소스 제한에 가까워지면 일시적인 연결 문제와 같은 오류가 발생할 수 있습니다. 노드 RAM을 늘리거나 작업자 노드를 추가 하 고 데이터를 균형 있게 조정 하면 도움이 될 수 있습니다.
4. 연결 문제가 계속 되거나 60 초 보다 오래 지속 되거나 하루에 두 번 이상 발생 하는 경우 [Azure 지원](https://azure.microsoft.com/support/options) 사이트에서 **지원 받기** 를 선택 하 여 azure 지원 요청을 파일에 추가 합니다.

## <a name="troubleshoot-persistent-errors"></a>영구 오류 문제 해결

응용 프로그램이 Citus (Hyperscale)에 지속적으로 연결 하지 못하는 경우 가장 일반적인 원인은 방화벽 구성 오류 또는 사용자 오류입니다.

* 코디네이터 노드 방화벽 구성: 하이퍼 확장 서버 방화벽이 프록시 서버 및 게이트웨이를 포함 하 여 클라이언트의 연결을 허용 하도록 구성 되어 있는지 확인 합니다.
* 클라이언트 방화벽 구성: 클라이언트의 방화벽은 데이터베이스 서버에 대 한 연결을 허용 해야 합니다. 일부 방화벽의 경우 응용 프로그램을 이름 으로만 허용 하는 것이 아니라 서버의 IP 주소와 포트를 허용 해야 합니다.
* 사용자 오류: 연결 문자열을 두 번 확인 합니다. 서버 이름과 같은 매개 변수를 잘못 입력 했을 수 있습니다. Azure Portal에서 다양 한 언어 프레임 워크 및 psql에 대 한 연결 문자열을 찾을 수 있습니다. Citus (Hyperscale) 서버 그룹의 **연결 문자열** 페이지로 이동 합니다. 또한 Citus (Hyperscale) 클러스터는 데이터베이스를 하나만 포함 하 고 미리 정의 된 이름은 **Citus**입니다.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>영구적인 연결 문제를 해결하는 단계

1. 클라이언트 IP 주소를 허용 하도록 [방화벽 규칙](howto-hyperscale-manage-firewall-using-portal.md) 을 설정 합니다. 임시 테스트 용도로만 목적으로만 0.0.0.0을 시작 IP 주소로 사용하고 255.255.255.255를 끝 IP 주소로 사용하여 방화벽 규칙을 설정합니다. 해당 규칙은 모든 IP 주소에 대 한 서버를 엽니다. 규칙에서 연결 문제를 해결 하는 경우 해당 규칙을 제거 하 고 적절 하 게 제한 된 IP 주소 또는 주소 범위에 대 한 방화벽 규칙을 만듭니다.
2. 클라이언트와 인터넷 간의 모든 방화벽에서 아웃 바운드 연결에 대 한 포트 5432가 열려 있는지 확인 합니다.
3. 연결 문자열 및 기타 연결 설정을 확인합니다.
4. 대시보드에서 서비스 상태를 확인합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Database for PostgreSQL-Hyperscale (Citus)에서 방화벽 규칙](concepts-hyperscale-firewall-rules.md) 의 개념에 대해 알아봅니다.
* [Azure Database for PostgreSQL-Hyperscale의 방화벽 규칙을 관리 하는 방법 (Citus)](howto-hyperscale-manage-firewall-using-portal.md) 을 참조 하세요.
