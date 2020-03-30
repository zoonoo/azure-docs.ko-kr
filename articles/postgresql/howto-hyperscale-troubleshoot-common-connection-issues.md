---
title: 연결 문제 해결 - 하이퍼스케일(Citus) - PostgreSQL용 Azure 데이터베이스
description: PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스에 대한 연결 문제 해결 방법 알아보기
keywords: PostgreSQL 연결, 연결 문자열, 연결 문제, 일시적 오류, 연결 오류
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: c064aca484f85c44dada9888012140784a96863f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977508"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스에 대한 연결 문제 해결

연결 문제는 다음과 같은 여러 가지 로 인해 발생할 수 있습니다.

* 방화벽 설정
* 연결 제한 시간
* 잘못된 로그인 정보
* 서버 그룹에 대한 연결 제한에 도달했습니다.
* 서비스 인프라 관련 문제
* 서비스 유지 관리
* 코디네이터 노드가 새 하드웨어로 장애 로 장애

일반적으로 하이퍼스케일에 대한 연결 문제는 다음과 같이 분류할 수 있습니다.

* 일시적 오류(단기 또는 일시적)
* 영구적 또는 일시적이지 않은 오류(정기적으로 되풀이되는 오류)

## <a name="troubleshoot-transient-errors"></a>일시적인 오류 문제 해결

일시적인 오류는 여러 가지 이유로 발생합니다. 가장 일반적인 것은 시스템 유지 관리, 하드웨어 또는 소프트웨어오류 및 코디네이터 노드 vCore 업그레이드입니다.

하이퍼스케일 서버 그룹 노드에 대한 고가용성을 사용하면 이러한 유형의 문제를 자동으로 완화할 수 있습니다. 그러나 응용 프로그램은 여전히 잠시 연결을 잃을 준비를해야 합니다. 또한 큰 트랜잭션으로 인해 장기 실행 복구가 발생하는 경우와 같이 다른 이벤트를 완화하는 데 시간이 오래 걸릴 수 있습니다.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>일시적인 연결 문제를 해결하는 단계

1. 응용 프로그램이 오류를 보고하는 동안 발생한 알려진 중단이 있는지 [Microsoft Azure 서비스 대시보드에서](https://azure.microsoft.com/status) 확인합니다.
2. 하이퍼스케일(Citus)과 같은 클라우드 서비스에 연결하는 응용 프로그램은 일시적인 오류를 예상하고 적절하게 대응해야 합니다. 예를 들어 응용 프로그램은 사용자에게 응용 프로그램 오류로 자신을 드러내는 대신 이러한 오류를 처리하기 위해 재시도 논리를 구현해야 합니다.
3. 서버 그룹이 리소스 제한에 가까워지면 오류는 일시적인 연결 문제처럼 보일 수 있습니다. 노드 RAM을 늘리거나 작업자 노드를 추가하고 데이터를 재조정하는 것이 도움이 될 수 있습니다.
4. 연결 문제가 계속하거나 60초 이상 지속하거나 하루에 두 번 이상 발생하는 경우 [Azure 지원](https://azure.microsoft.com/support/options) 사이트에서 **지원 받기를** 선택하여 Azure 지원 요청을 제출합니다.

## <a name="troubleshoot-persistent-errors"></a>영구 오류 문제 해결

응용 프로그램이 지속적으로 하이퍼스케일(Citus)에 연결하지 못하는 경우 가장 일반적인 원인은 방화벽 구성 오류 또는 사용자 오류입니다.

* 코디네이터 노드 방화벽 구성: 하이퍼스케일 서버 방화벽이 프록시 서버 및 게이트웨이를 포함하여 클라이언트에서 연결을 허용하도록 구성되어 있는지 확인합니다.
* 클라이언트 방화벽 구성: 클라이언트의 방화벽은 데이터베이스 서버에 대한 연결을 허용해야 합니다. 일부 방화벽에서는 이름으로 응용 프로그램뿐만 아니라 서버의 IP 주소와 포트를 허용해야 합니다.
* 사용자 오류: 연결 문자열을 다시 확인합니다. 서버 이름과 같은 매개 변수를 잘못 입력했을 수 있습니다. Azure 포털에서 다양한 언어 프레임워크 및 psql에 대한 연결 문자열을 찾을 수 있습니다. 하이퍼스케일(Citus) 서버 그룹의 **연결 문자열** 페이지로 이동합니다. 또한 하이퍼스케일(Citus) 클러스터에는 데이터베이스가 하나만 있고 미리 정의된 이름은 **citus입니다.**

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>영구적인 연결 문제를 해결하는 단계

1. 클라이언트 IP 주소를 허용하도록 [방화벽 규칙을](howto-hyperscale-manage-firewall-using-portal.md) 설정합니다. 임시 테스트 용도로만 목적으로만 0.0.0.0을 시작 IP 주소로 사용하고 255.255.255.255를 끝 IP 주소로 사용하여 방화벽 규칙을 설정합니다. 이 규칙은 모든 IP 주소에 서버를 엽니다. 규칙이 연결 문제를 해결하는 경우 이를 제거하고 적절히 제한된 IP 주소 또는 주소 범위에 대한 방화벽 규칙을 만듭니다.
2. 클라이언트와 인터넷 사이의 모든 방화벽에서 포트 5432가 아웃바운드 연결을 위해 열려 있는지 확인합니다.
3. 연결 문자열 및 기타 연결 설정을 확인합니다.
4. 대시보드에서 서비스 상태를 확인합니다.

## <a name="next-steps"></a>다음 단계

* [PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스에서 방화벽 규칙의](concepts-hyperscale-firewall-rules.md) 개념 알아보기
* [PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스의 방화벽 규칙을 관리하는](howto-hyperscale-manage-firewall-using-portal.md) 방법 보기
