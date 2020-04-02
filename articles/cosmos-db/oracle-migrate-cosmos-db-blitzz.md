---
title: 블리츠를 사용하여 오라클에서 Azure 코스모스 DB 카산드라 API로 데이터 마이그레이션
description: 블리츠를 사용하여 오라클 데이터베이스에서 Azure 코스모스 DB 카산드라 API로 데이터를 마이그레이션하는 방법을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 43d15a7252819a3e4f7635e37458b75e9b7ecca7
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546278"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>블리츠를 사용하여 오라클에서 Azure 코스모스 DB 카산드라 API 계정으로 데이터 마이그레이션

Azure Cosmos DB의 Cassandra API는 다음과 같은 다양한 이유로 오라클에서 실행되는 엔터프라이즈 워크로드에 적합한 선택이 되었습니다.

* **더 나은 확장성 및 가용성:** 단일 실패 지점, 확장성 및 애플리케이션가용성을 제거합니다.

* **상당한 비용 절감:** VM, 대역폭 및 해당 오라클 라이선스 비용을 포함하는 Azure Cosmos DB를 사용하면 비용을 절감할 수 있습니다. 또한 데이터 센터, 서버, SSD 스토리지, 네트워킹 및 전기 비용을 관리할 필요가 없습니다.

* **관리 및 모니터링에 대한 오버헤드 없음:** 완전히 관리되는 클라우드 서비스인 Azure Cosmos DB는 무수한 설정을 관리하고 모니터링하는 오버헤드를 제거합니다.

데이터베이스 워크로드를 한 플랫폼에서 다른 플랫폼으로 마이그레이션하는 방법에는 여러 가지가 있습니다. [Blitzz는](https://www.blitzz.io) 다양한 데이터베이스에서 Azure Cosmos DB로의 가동 중지 시간 마이그레이션을 수행하는 안전하고 신뢰할 수 있는 방법을 제공하는 도구입니다. 이 문서에서는 블리츠를 사용하여 오라클 데이터베이스에서 Azure 코스모스 DB 카산드라 API로 데이터를 마이그레이션하는 데 필요한 단계를 설명합니다.

## <a name="benefits-using-blitzz-for-migration"></a>마이그레이션을 위한 Blitzz를 사용한 이점

Blitzz의 마이그레이션 솔루션은 복잡한 운영 워크로드를 마이그레이션하기 위한 단계별 접근 방식을 따릅니다. 다음은 Blitzz의 가동 중지 시간 제로 마이그레이션 계획의 주요 측면 중 일부입니다.

* 오라클 데이터베이스에서 Azure Cosmos DB로 비즈니스 논리(테이블, 인덱스, 보기)를 자동으로 마이그레이션합니다. 스키마를 수동으로 만들 필요가 없습니다.

* Blitzz는 대용량 및 병렬 데이터베이스 복제를 제공합니다. CDC(변경 데이터 캡처)라는 기술을 사용하여 마이그레이션 중에 원본 및 대상 플랫폼을 모두 동기화할 수 있습니다. 블리츠는 CDC를 사용하여 원본 데이터베이스(Oracle)에서 변경 내용 스트림을 지속적으로 가져와 대상 데이터베이스(Azure Cosmos DB)에 적용합니다.

* 내결함성이 있으며 시스템의 하드웨어 또는 소프트웨어 오류 발생 시에도 정확히 한 번 데이터를 전송할 수 있습니다.

* TLS/SSL, 암호화와 같은 다양한 보안 방법을 사용하여 전송 중에 데이터를 보호합니다.

* PL/SQL로 작성된 복잡한 비즈니스 논리를 Azure Cosmos DB의 동등한 비즈니스 논리로 변환하는 서비스를 제공합니다.

## <a name="steps-to-migrate-data"></a>데이터 마이그레이션 단계

이 섹션에서는 Blitzz를 설정하고 Oracle 데이터베이스에서 Azure Cosmos DB로 데이터를 마이그레이션하는 데 필요한 단계를 설명합니다.

1. Blitzz 복제본을 설치하려는 컴퓨터에서 보안 인증서를 추가합니다. 이 인증서는 Blitzz 복제본에서 지정된 Azure Cosmos DB 계정과 TLS 연결을 설정하는 데 필요합니다. 다음 단계로 인증서를 추가할 수 있습니다.

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. ou는 [블리츠 웹 사이트에서](https://www.blitzz.io)데모를 요청하여 블리츠 설치 및 바이너리 파일을 얻을 수 있습니다. 또는 팀에 [이메일을](mailto:success@blitzz.io) 보낼 수도 있습니다.

   ![블리츠 복제 도구 다운로드](./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![블리츠 복제 파일](./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png)

1. CLI 터미널에서 소스 데이터베이스 구성을 설정합니다. 명령을 사용하여 **`vi conf/conn/oracle.yml`** 구성 파일을 열고 오라클 노드, 포트 번호, 사용자 이름, 암호 및 기타 필요한 세부 정보의 쉼표로 구분된 IP 주소 목록을 추가합니다. 다음 코드는 예제 구성 파일을 보여 주며 다음과 같은 예제 구성 파일을 보여 주며 다음과 같은 코드입니다.

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   ![오라클 연결 편집기 열기](./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png)

   ![오라클 연결 구성](./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png)

   구성 세부 정보를 입력한 후 파일을 저장하고 닫습니다.

1. 선택적으로 원본 데이터베이스 필터 파일을 설정할 수 있습니다. 필터 파일은 마이그레이션할 스키마 또는 테이블을 지정합니다. 명령을 사용하여 **`vi filter/oracle_filter.yml`** 구성 파일을 열고 다음 구성 세부 정보를 입력합니다.

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   데이터베이스 필터 세부 정보를 입력한 후 파일을 저장하고 닫습니다.

1. 다음으로 대상 데이터베이스의 구성을 설정합니다. 구성을 정의하기 전에 [Azure 코스모스 DB 카산드라 API 계정을 만듭니다.](create-cassandra-dotnet.md#create-a-database-account) 데이터에서 [올바른 파티션 키를 선택한](partitioning-overview.md#choose-partitionkey) 다음 키스페이스와 마이그레이션된 데이터를 저장할 테이블을 만듭니다.

1. 데이터를 마이그레이션하기 전에 컨테이너 처리량을 응용 프로그램이 빠르게 마이그레이션하는 데 필요한 양으로 늘립니다. 예를 들어 처리량을 1000000 R로 늘릴 수 있습니다. 마이그레이션을 시작하기 전에 처리량을 조정하면 더 적은 시간에 데이터를 마이그레이션하는 데 도움이 됩니다. 

   ![Azure 코스모스 컨테이너 전체 확장](./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png)

   마이그레이션이 완료된 후 처리량을 줄여야 합니다. 각 작업에 필요한 데이터 양과 US에 따라 데이터 마이그레이션 후 필요한 처리량을 예측할 수 있습니다. 필요한 RU를 추정하는 방법에 대한 자세한 내용은 [컨테이너 및 데이터베이스에 대한 프로비저닝 처리량](set-throughput.md) 및 Azure Cosmos DB 용량 플래너 문서를 사용하여 [RU/s 를 추정하는](estimate-ru-with-capacity-planner.md) 것을 참조하십시오.

1. **연결 문자열** 창에서 Azure Cosmos 계정의 **연락처 지점, 포트, 사용자 이름**및 기본 **암호를** 가져옵니다. 구성 파일에서 이러한 값을 사용합니다.

1. CLI 터미널에서 대상 데이터베이스 구성을 설정합니다. 명령을 사용하여 **`vi conf/conn/cosmosdb.yml`** 구성 파일을 열고 호스트 URI, 포트 번호, 사용자 이름, 암호 및 기타 필수 매개 변수의 쉼표로 구분된 목록을 추가합니다. 다음은 구성 파일의 내용 예입니다.

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. 다음으로 Blitzz를 사용하여 데이터를 마이그레이션합니다. 전체 **또는** **스냅샷** 모드에서 블리즈 복제본을 실행할 수 있습니다.

   * **전체 모드** – 이 모드에서는 마이그레이션 후에도 복제본이 계속 실행되고 원본 Oracle 시스템의 변경 내용을 수신합니다. 변경 내용을 감지하면 대상 Azure Cosmos 계정에서 실시간으로 복제됩니다.

   * **스냅숏 모드** - 이 모드에서스키마 마이그레이션 및 일회성 데이터 복제를 수행할 수 있습니다. 이 옵션에는 실시간 복제가 지원되지 않습니다.


   위의 두 모드를 사용하면 가동 중지 시간 제로로 마이그레이션을 수행할 수 있습니다.

1. Blitzz 복제 CLI 터미널에서 데이터를 마이그레이션하려면 다음 명령을 실행합니다.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   복제 UI에는 복제 진행률을 보여 주며 스키마 마이그레이션 및 스냅숏 작업이 완료되면 진행률에 100%가 표시됩니다. 마이그레이션이 완료되면 대상 Azure Cosmos 데이터베이스의 데이터의 유효성을 검사할 수 있습니다.

   ![오라클 데이터 마이그레이션 출력](./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png)

1. 마이그레이션에 전체 모드를 사용했기 때문에 원본 Oracle 데이터베이스에서 데이터를 삽입, 업데이트 또는 삭제하는 등의 작업을 수행할 수 있습니다. 나중에 대상 Azure Cosmos 데이터베이스에서 실시간으로 복제되는지 확인할 수 있습니다. 마이그레이션 후 Azure Cosmos 컨테이너에 대해 구성된 처리량을 줄이십시오.

1. 복제점을 중지하고 **--resume** 스위치로 다시 시작할 수 있습니다. 데이터 일관성을 손상시키지 않고 중지된 지점에서 복제가 다시 시작됩니다. 다음 명령은 이력서 스위치를 사용하는 방법을 보여줍니다.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

대상으로의 데이터 마이그레이션, 실시간 마이그레이션에 대한 자세한 내용은 [Blitzz 복제 데모를](https://www.youtube.com/watch?v=y5ZeRK5A-MI)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md) 
* [분할 주요 모범 사례](partitioning-overview.md#choose-partitionkey)
* [Azure 코스모스 DB 용량 플래너 문서를 사용하여 RU/s 추정](estimate-ru-with-capacity-planner.md)