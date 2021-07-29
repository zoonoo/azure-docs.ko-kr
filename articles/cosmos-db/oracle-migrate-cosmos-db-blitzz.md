---
title: Blitzz를 사용하여 Oracle에서 Azure Cosmos DB Cassandra API로 데이터 마이그레이션
description: Blitzz를 사용하여 Oracle 데이터베이스에서 Azure Cosmos DB Cassandra API로 데이터를 마이그레이션하는 방법을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 15bcd0c54fc5f6614f4d1925759704309048acae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336444"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Blitzz를 사용하여 Oracle에서 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB의 Cassandra API는 다음과 같은 다양한 이유로 Oracle에서 실행되는 엔터프라이즈 워크로드에 적합한 선택이 되었습니다.

* **스케일링 수준 및 가용성 향상:** 단일 실패 지점을 제거하고 애플리케이션의 스케일링 수준과 가용성을 향상합니다.

* **대폭적인 비용 절감:** Azure Cosmos DB를 사용하여 비용을 절감할 수 있습니다. 여기에는 VM, 대역폭, 적용 가능한 Oracle 라이선스 비용이 포함됩니다. 또한 데이터 센터, 서버, SSD 스토리지, 네트워킹, 전기 비용을 관리할 필요가 없습니다.

* **관리 및 모니터링 오버헤드 없음:** 완전 관리형 클라우드 서비스인 Azure Cosmos DB는 많은 설정을 관리하고 모니터링하는 오버헤드를 제거합니다.

한 플랫폼에서 다른 플랫폼으로 데이터베이스 워크로드를 마이그레이션하는 방법에는 여러 가지가 있습니다. [Blitzz](https://www.blitzz.io)는 가동 중지 시간 없이 다양한 데이터베이스에서 Azure Cosmos DB로 마이그레이션하는 안전하고 안정적인 방법을 제공하는 도구입니다. 이 문서에서는 Blitzz를 사용하여 Oracle 데이터베이스에서 Azure Cosmos DB Cassandra API로 데이터를 마이그레이션하는 데 필요한 단계를 설명합니다.

## <a name="benefits-using-blitzz-for-migration"></a>마이그레이션에 Blitzz를 사용할 경우의 이점

Blitzz 마이그레이션 솔루션은 복잡한 운영 워크로드를 마이그레이션하는 단계별 방법을 따릅니다. 가동 중지 시간 없는 Blitzz 마이그레이션 계획의 몇 가지 주요 측면은 다음과 같습니다.

* Oracle 데이터베이스에서 Azure Cosmos DB로 비즈니스 논리(테이블, 인덱스, 뷰)를 자동으로 마이그레이션합니다. 수동으로 스키마를 만들 필요가 없습니다.

* Blitzz는 대용량 및 병렬 데이터베이스 복제를 제공합니다. 따라서 CDC(변경 데이터 캡처)라는 기술을 사용하여 마이그레이션하는 동안 원본 및 대상 플랫폼을 동기화할 수 있습니다. Blitzz는 CDC를 사용하여 원본 데이터베이스(Oracle)에서 변경 내용 스트림을 지속적으로 풀하고 대상 데이터베이스(Azure Cosmos DB)에 적용합니다.

* 내결함성이 있으며, 시스템에서 하드웨어 또는 소프트웨어 오류가 발생하는 경우에도 정확히 한 번 데이터를 전달합니다.

* TLS/SSL, 암호화 등의 다양한 보안 방법을 사용하여 전송하는 동안 데이터를 보호합니다.

* PL/SQL로 작성된 복잡한 비즈니스 논리를 Azure Cosmos DB의 동등한 비즈니스 논리로 변환하는 서비스를 제공합니다.

## <a name="steps-to-migrate-data"></a>데이터를 마이그레이션하는 단계

이 섹션에서는 Blitzz를 설정하고 Oracle 데이터베이스에서 Azure Cosmos DB로 데이터를 마이그레이션하는 데 필요한 단계를 설명합니다.

1. Blitzz 복제를 설치하려는 컴퓨터에서 보안 인증서를 추가합니다. 이 인증서는 Blitzz 복제에서 지정된 Azure Cosmos DB 계정으로 TLS 연결을 설정하는 데 필요합니다. 다음 단계에 따라 인증서를 추가할 수 있습니다.

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. [Blitzz 웹 사이트](https://www.blitzz.io)에서 데모를 요청하여 Blitzz 설치와 이진 파일을 가져올 수 있습니다. 또는 팀에 [메일](mailto:success@blitzz.io)을 보낼 수도 있습니다.

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Blitzz 복제 도구 다운로드":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Blitzz 복제 파일":::

1. CLI 터미널에서 원본 데이터베이스 구성을 설정합니다. **`vi conf/conn/oracle.yml`** 명령을 사용하여 구성 파일을 열고 Oracle 노드의 IP 주소, 포트 번호, 사용자 이름, 암호, 기타 필요한 세부 정보의 쉼표로 구분된 목록을 추가합니다. 다음 코드는 예제 구성 파일을 보여 줍니다.

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

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png" alt-text="Oracle 연결 편집기 열기":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png" alt-text="Oracle 연결 구성":::

   구성 세부 정보를 입력한 후 파일을 저장하고 닫습니다.

1. 필요에 따라 원본 데이터베이스 필터 파일을 설정할 수 있습니다. 필터 파일은 마이그레이션할 스키마 또는 테이블을 지정합니다. **`vi filter/oracle_filter.yml`** 명령을 사용하여 구성 파일을 열고 다음 구성 세부 정보를 입력합니다.

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   데이터베이스 필터 정보를 입력한 후 파일을 저장하고 닫습니다.

1. 다음에는 대상 데이터베이스 구성을 설정합니다. 구성을 정의하기 전에 [Azure Cosmos DB Cassandra API 계정을 만듭니다](create-cassandra-dotnet.md#create-a-database-account). 데이터에서 [올바른 파티션 키를 선택](partitioning-overview.md#choose-partitionkey)한 다음, 키스페이스와 마이그레이션된 데이터를 저장할 테이블을 만듭니다.

1. 데이터를 마이그레이션하기 전에 컨테이너 처리량을 애플리케이션에서 신속하게 마이그레이션하는 데 필요한 양만큼 늘립니다. 예를 들어 처리량을 100,000RU로 늘릴 수 있습니다. 마이그레이션을 시작하기 전에 처리량을 스케일링하면 데이터 마이그레이션에 걸리는 시간을 줄이는 데 도움이 됩니다. 

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Azure Cosmos 컨테이너 전체 스케일링":::

   마이그레이션이 완료된 후 처리량을 줄여야 합니다. 각 작업에서 저장된 데이터 양과 필요한 RU에 따라 데이터 마이그레이션 후에 필요한 처리량을 예상할 수 있습니다. 필요한 RU를 예상하는 방법에 대한 자세한 내용은 [컨테이너 및 데이터베이스의 처리량 프로비저닝](set-throughput.md) 및 [Azure Cosmos DB Capacity Planner를 사용하여 RU/s 예상](estimate-ru-with-capacity-planner.md)을 참조하세요.

1. **연결 문자열** 창에서 Azure Cosmos 계정의 **접점, 포트, 사용자 이름**, **기본 암호** 를 가져옵니다. 이 값은 구성 파일에서 사용합니다.

1. CLI 터미널에서 대상 데이터베이스 구성을 설정합니다. **`vi conf/conn/cosmosdb.yml`** 명령을 사용하여 구성 파일을 열고 호스트 URI, 포트 번호, 사용자 이름, 암호, 기타 필수 매개 변수의 쉼표로 구분된 목록을 추가합니다. 다음은 구성 파일 내용의 예입니다.

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. 다음에는 Blitzz를 사용하여 데이터를 마이그레이션합니다. Blizz 복제는 **전체** 또는 **스냅샷** 모드로 실행할 수 있습니다.

   * **전체 모드** – 이 모드에서는 마이그레이션 후에 복제가 계속 실행되고 원본 Oracle 시스템의 변경 내용을 수신 대기합니다. 변경 내용이 검색되면 대상 Azure Cosmos 계정에 실시간으로 복제됩니다.

   * **스냅샷 모드** – 이 모드에서는 스키마 마이그레이션과 일회성 데이터 복제를 수행할 수 있습니다. 이 옵션을 사용하는 경우 실시간 복제는 지원되지 않습니다.


   위의 두 모드를 사용하면 가동 중지 시간 없이 마이그레이션할 수 있습니다.

1. 데이터를 마이그레이션하려면 Blitzz 복제 CLI 터미널에서 다음 명령을 실행합니다.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   복제 UI는 복제 진행률을 보여 줍니다. 스키마 마이그레이션과 스냅샷 작업이 완료되면 진행률이 100%로 표시됩니다. 마이그레이션이 완료된 후 대상 Azure Cosmos 데이터베이스에서 데이터 유효성을 검사할 수 있습니다.

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png" alt-text="Oracle 데이터 마이그레이션 출력":::

1. 마이그레이션을 위해 전체 모드를 사용했으므로 원본 Oracle 데이터베이스에서 데이터 삽입, 업데이트 또는 삭제와 같은 작업을 수행할 수 있습니다. 나중에 대상 Azure Cosmos 데이터베이스에서 실시간으로 복제되는지 확인할 수 있습니다. 마이그레이션 후에는 Azure Cosmos 컨테이너에 대해 구성된 처리량을 줄여야 합니다.

1. 언제든지 복제를 중지하고 **--resume** 스위치를 사용하여 다시 시작할 수 있습니다. 데이터 일관성 손상 없이 복제가 중지된 지점부터 다시 시작됩니다. 다음 명령은 resume 스위치를 사용하는 방법을 보여 줍니다.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

대상에 대한 데이터 마이그레이션(실시간 마이그레이션)에 관한 자세한 내용은 [Blitzz 복제 데모](https://www.youtube.com/watch?v=y5ZeRK5A-MI)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md) 
* [파티션 키 모범 사례](partitioning-overview.md#choose-partitionkey)
* [Azure Cosmos DB Capacity Planner를 사용하여 RU/s 예상](estimate-ru-with-capacity-planner.md) 문서
