---
title: 연결 문제 해결 – 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - 하이퍼스케일(Citus)에 대한 연결 이슈를 해결하는 방법을 알아봅니다.
keywords: PostgreSQL 연결, 연결 문자열, 연결 문제, 일시적 오류, 연결 오류
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/8/2019
ms.openlocfilehash: e1c6825820ae943d10157279dfe93922a7521b75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91295620"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - 하이퍼스케일(Citus)에 대한 연결 이슈 해결

연결 문제는 다음과 같은 여러 가지 이유로 발생할 수 있습니다.

* 방화벽 설정
* 연결 제한 시간
* 잘못된 로그인 정보
* 서버 그룹의 연결 한도에 도달함
* 서비스 인프라 관련 문제
* 서비스 유지 관리
* 코디네이터 노드가 새 하드웨어로 장애 조치(failover)됨

일반적으로 하이퍼스케일(Citus)에 대한 연결 이슈는 다음과 같이 분류할 수 있습니다.

* 일시적 오류(단기 또는 일시적)
* 영구적 또는 일시적이지 않은 오류(정기적으로 되풀이되는 오류)

## <a name="troubleshoot-transient-errors"></a>일시적인 오류 문제 해결

일시적인 오류는 여러 가지 이유로 발생합니다. 가장 일반적인 이유로는 시스템 유지 관리, 하드웨어 또는 소프트웨어 오류, 코디네이터 노드 vCore 업그레이드 등이 있습니다.

하이퍼스케일(Citus) 서버 그룹 노드에 고가용성을 사용하도록 설정하면 해당 유형의 문제를 자동으로 완화할 수 있습니다. 그러나 애플리케이션에서 일시적인 연결 끊김에 대비해야 있습니다. 대용량 트랜잭션으로 인해 장기 실행 복구가 발생하는 경우와 같은 다른 이벤트는 완화하는 데 시간이 오래 걸릴 수도 있습니다.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>일시적인 연결 문제를 해결하는 단계

1. [Microsoft Azure 서비스 대시보드](https://azure.microsoft.com/status)에서 애플리케이션이 오류를 보고한 시간 동안 발생한 알려진 중단을 모두 확인합니다.
2. 하이퍼스케일(Citus)과 같은 클라우드 서비스에 연결하는 애플리케이션은 일시적인 오류를 예상하고 정상적으로 대응해야 합니다. 예를 들어 애플리케이션은 사용자에게 애플리케이션 오류로 표시하는 대신 다시 시도 논리를 구현하여 오류를 처리해야 합니다.
3. 서버 그룹이 해당 리소스 한도에 가까워지면 오류가 일시적인 연결 이슈처럼 보일 수 있습니다. 노드 RAM을 늘리거나 작업자 노드를 추가하고 데이터 균형을 다시 조정하면 도움이 될 수 있습니다.
4. 연결 문제가 계속되거나 60초 넘게 지속되거나 하루에 두 번 이상 발생하는 경우 [Azure 지원](https://azure.microsoft.com/support/options) 사이트에서 **지원 받기** 를 선택하여 Azure 지원 요청을 제출합니다.

## <a name="troubleshoot-persistent-errors"></a>영구 오류 문제 해결

애플리케이션이 영구적으로 하이퍼스케일(Citus)에 연결하지 못하는 경우 가장 일반적인 원인은 방화벽 구성 오류 또는 사용자 오류입니다.

* 코디네이터 노드 방화벽 구성: 하이퍼스케일(Citus) 서버 방화벽이 프록시 서버 및 게이트웨이를 포함하여 클라이언트에서 연결을 허용하도록 구성되어 있는지 확인합니다.
* 클라이언트 방화벽 구성: 클라이언트의 방화벽은 데이터베이스 서버에 연결을 허용해야 합니다. 일부 방화벽의 경우 애플리케이션을 이름으로 허용하는 것은 물론 서버의 IP 주소와 포트도 허용해야 합니다.
* 사용자 오류: 연결 문자열을 다시 확인합니다. 서버 이름과 같은 매개 변수를 잘못 입력했을 수 있습니다. Azure Portal에서 다양한 언어 프레임워크 및 psql의 연결 문자열을 확인할 수 있습니다. 하이퍼스케일(Citus) 서버 그룹의 **연결 문자열** 페이지로 이동합니다. 또한 하이퍼스케일(Citus) 클러스터에는 데이터베이스가 하나만 있고 미리 정의된 이름은 **citus** 여야 합니다.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>영구적인 연결 문제를 해결하는 단계

1. 클라이언트 IP 주소를 허용하도록 [방화벽 규칙](howto-hyperscale-manage-firewall-using-portal.md) 을 설정합니다. 임시 테스트 용도로만 목적으로만 0.0.0.0을 시작 IP 주소로 사용하고 255.255.255.255를 끝 IP 주소로 사용하여 방화벽 규칙을 설정합니다. 해당 규칙은 모든 IP 주소에 대한 서버를 엽니다. 규칙에서 연결 이슈를 해결하는 경우 해당 규칙을 제거하고 적절하게 제한된 IP 주소 또는 주소 범위에 대한 방화벽 규칙을 만듭니다.
2. 클라이언트와 인터넷 사이에 있는 모든 방화벽에서 아웃바운드 연결에 대해 포트 5432가 열려 있는지 확인합니다.
3. 연결 문자열 및 기타 연결 설정을 확인합니다.
4. 대시보드에서 서비스 상태를 확인합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 방화벽 규칙](concepts-hyperscale-firewall-rules.md) 개념에 대해 알아봅니다.
* [Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 방화벽 규칙을 관리](howto-hyperscale-manage-firewall-using-portal.md)하는 방법을 참조하세요.
