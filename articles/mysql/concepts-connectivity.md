---
title: 일시적인 연결 오류 - MySQL용 Azure 데이터베이스
description: 일시적인 연결 오류를 처리하고 MySQL용 Azure 데이터베이스에 효율적으로 연결하는 방법을 알아봅니다.
keywords: mysql 연결, 연결 문자열, 연결 문제, 일시적인 오류, 연결 오류, 효율적으로 연결
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 79c5c7e485cc9cb03757b8a981cef92d79b81c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537179"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>일시적인 오류를 처리하고 MySQL용 Azure 데이터베이스에 효율적으로 연결

이 문서에서는 일시적인 오류를 처리하고 MySQL용 Azure 데이터베이스에 효율적으로 연결하는 방법을 설명합니다.

## <a name="transient-errors"></a>일시적 오류

일시적 결함이라고도 하는 일시적 오류는 자체적으로 해결되는 오류입니다. 가장 일반적으로 이러한 오류는 삭제되는 데이터베이스 서버에 대한 연결로 나타납니다. 또한 서버에 대한 새 연결을 열 수 없습니다. 예를 들어 하드웨어 또는 네트워크 오류가 발생할 때 일시적 오류가 발생할 수 있습니다. 또 다른 이유는 롤아웃 중인 PaaS 서비스의 새 버전일 수 있습니다. 이러한 이벤트의 대부분은 60초 이내에 시스템에서 자동으로 완화됩니다. 클라우드에서 애플리케이션을 설계하고 개발하는 가장 좋은 방법은 일시적 오류를 예상하는 것입니다. 언제든지 모든 구성 요소에서 발생할 수 있으며 이러한 상황을 처리할 수 있는 적절한 논리를 갖추고 있다고 가정합니다.

## <a name="handling-transient-errors"></a>일시적 오류 처리

일시적 오류는 다시 시도 논리를 사용하여 처리해야 합니다. 고려해야 할 상황은 다음과 같습니다.

* 연결을 열려고 하면 오류가 발생합니다.
* 서버 쪽에서 유휴 연결이 삭제됩니다. 명령을 실행하려고 하면 실행할 수 없습니다.
* 현재 명령을 실행하는 활성 연결이 삭제됩니다.

첫 번째와 두 번째 경우는 매우 간단하게 처리할 수 있습니다. 연결을 열기 위해 다시 시도합니다. 성공하면 일시적 오류가 시스템에서 완화됩니다. Azure Database for MySQL을 다시 사용할 수 있습니다. 연결을 다시 시도하기 전에 기다리는 것이 좋습니다. 초기 다시 시도가 실패하면 잠시 그대로 있습니다. 이렇게 하면 오류 상황을 극복하기 위해 시스템에서 사용 가능한 모든 리소스를 사용할 수 있습니다. 따라야 할 좋은 패턴은 다음과 같습니다.

* 5초 동안 기다린 후에 다시 시도합니다.
* 다시 시도할 때마다 대기 시간이 최대 60초까지 기하급수적으로 증가합니다.
* 애플리케이션에서 작업이 실패한 것으로 간주하는 시점의 최대 재시도 횟수를 설정합니다.

활성 트랜잭션과의 연결에 실패하면 복구를 제대로 처리하기가 더 어렵습니다. 두 가지 경우가 있습니다. 트랜잭션이 실제로 읽기 전용인 경우 연결을 다시 열고 트랜잭션을 다시 시도하는 것이 안전합니다. 그러나 트랜잭션이 데이터베이스에 쓰는 경우 트랜잭션이 롤백되었는지 또는 일시적인 오류가 발생하기 전에 트랜잭션이 성공했는지 확인해야 합니다. 이 경우 데이터베이스 서버에서 커밋 승인을 받지 못했을 수 있습니다.

이 작업을 수행하는 한 가지 방법은 클라이언트에서 모든 다시 시도에 사용되는 고유 ID를 생성하는 것입니다. 이 고유 ID를 트랜잭션의 일환으로 서버에 전달하고, 고유 제약 조건이 있는 열에 이를 저장합니다. 이렇게 하면 트랜잭션을 안전하게 다시 시도할 수 있습니다. 이전 트랜잭션이 롤백되고 클라이언트에서 생성된 고유 ID가 시스템에 아직 없는 경우 성공합니다. 이전 트랜잭션이 성공적으로 완료되었으므로 고유 ID가 이전에 저장된 경우 중복 키 위반을 나타내는 데 실패합니다.

프로그램에서 타사 미들웨어를 통해 Azure Database for MySQL과 통신하는 경우 공급업체에 문의하여 일시적 오류에 대한 다시 시도 논리가 미들웨어에 포함되어 있는지를 확인해야 합니다.

다시 시도 논리는 테스트해야 합니다. 예를 들어 MySQL 서버에 대한 Azure 데이터베이스의 계산 리소스를 확장하거나 축소하는 동안 코드를 실행해 보십시오. 애플리케이션에서 이 작업 중에 발생하는 짧은 가동 중지 시간을 아무 문제 없이 처리해야 합니다.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스에 효율적으로 연결

데이터베이스 연결은 제한된 리소스이므로 MySQL용 Azure 데이터베이스에 액세스하기 위해 연결 풀링을 효과적으로 사용하면 성능이 최적화됩니다. 아래 섹션에서는 연결 풀링 또는 영구 연결을 사용하여 MySQL용 Azure 데이터베이스에 보다 효과적으로 액세스하는 방법을 설명합니다.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>연결 풀링을 사용하여 데이터베이스에 액세스(권장)

데이터베이스 연결을 관리하면 응용 프로그램의 전체 성능에 큰 영향을 미칠 수 있습니다. 응용 프로그램의 성능을 최적화하려면 연결 설정 횟수와 키 코드 경로에서 연결을 설정하는 시간을 줄이는 것이 목표입니다. MySQL용 Azure 데이터베이스에 연결하려면 데이터베이스 연결 풀링 또는 영구 연결을 사용하는 것이 좋습니다. 데이터베이스 연결 풀링은 데이터베이스 연결의 생성, 관리 및 할당을 처리합니다. 프로그램이 데이터베이스 연결을 요청하면 새 연결을 만드는 대신 기존 유휴 데이터베이스 연결의 할당에 우선 순위를 지정합니다. 프로그램이 데이터베이스 연결을 사용하여 완료되면 연결이 단순히 닫히는 것이 아니라 추가 사용을 준비하기 위해 복구됩니다.

더 나은 그림을 위해 이 [문서에서는 JAVA를](./sample-scripts-java-connection-pooling.md) 예로 사용하는 샘플 코드를 제공합니다. 자세한 내용은 [아파치 공통 DBCP](https://commons.apache.org/proper/commons-dbcp/)를 참조하십시오.

> [!NOTE]
> 서버는 리소스를 확보하기 위해 일정 시간 동안 유휴 상태에 있던 연결을 닫도록 시간 시간 메커니즘을 구성합니다. 사용 시 영구 연결의 효율성을 보장하도록 확인 시스템을 설정해야 합니다. 자세한 내용은 [클라이언트 측의 확인 시스템 구성을 참조하여 영구 연결의 효율성을 보장합니다.](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections)

## <a name="access-databases-by-using-persistent-connections-recommended"></a>영구 연결을 사용하여 데이터베이스에 액세스(권장)

영구 연결의 개념은 연결 풀링과 유사합니다. 짧은 연결을 영구 연결로 교체하려면 코드를 약간만 변경해야 하지만 많은 일반적인 응용 프로그램 시나리오에서 성능이 향상되는 측면에서 큰 영향을 미칩니다.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>짧은 연결로 대기 및 재시도 메커니즘을 사용하여 데이터베이스에 액세스

리소스 제한이 있는 경우 데이터베이스 풀링 또는 영구 연결을 사용하여 데이터베이스에 액세스하는 것이 좋습니다. 응용 프로그램이 짧은 연결을 사용하고 동시 연결 수의 상한에 접근할 때 연결 실패를 경험하는 경우 대기 및 재시도 메커니즘을 시도할 수 있습니다. 첫 번째 시도 후 대기 시간이 짧아지므로 적절한 대기 시간을 설정할 수 있습니다. 그런 다음 이벤트를 여러 번 기다릴 수 있습니다.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>클라이언트에서 확인 메커니즘을 구성하여 영구 연결의 효율성을 확인합니다.

서버는 리소스를 확보하기 위해 일정 시간 동안 유휴 상태에 있던 연결을 닫도록 시간 시간 메커니즘을 구성합니다. 클라이언트가 데이터베이스에 다시 액세스하면 클라이언트와 서버 간에 새 연결 요청을 만드는 것과 같습니다. 연결 을 사용하는 과정에서 연결의 효율성을 보장하려면 클라이언트에서 확인 메커니즘을 구성합니다. 다음 예제와 같이 Tomcat JDBC 연결 풀링을 사용하여 이 확인 메커니즘을 구성할 수 있습니다.

TestOnBorrow 매개 변수를 설정하 여 새 요청이 있을 때 연결 풀은 사용 가능한 유휴 연결의 효율성을 자동으로 확인합니다. 이러한 연결이 유효하면 직접 반환된 연결 풀에서 연결을 철회합니다. 그런 다음 연결 풀은 새 유효 연결을 만들고 반환합니다. 이 프로세스를 통해 데이터베이스에 효율적으로 액세스할 수 있습니다. 

특정 설정에 대한 자세한 내용은 [JDBC 연결 풀 공식 소개 문서를](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes)참조하십시오. 주로 다음 세 가지 매개 변수를 설정 해야: TestOnBorrow (true로 설정), 유효성 검사 쿼리 (SELECT 1로 설정), 유효성 검사쿼리 시간 설정 (1로 설정). 특정 샘플 코드는 다음과 같습니다.

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>다음 단계

* [Azure Database for MySQL에 대한 연결 문제 해결](howto-troubleshoot-common-connection-issues.md)
