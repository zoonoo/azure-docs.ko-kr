---
title: Striim을 사용하여 Azure 코스모스 DB 카산드라 API 계정으로 데이터 마이그레이션
description: Striim을 사용하여 오라클 데이터베이스에서 Azure Cosmos DB Cassandra API 계정으로 데이터를 마이그레이션하는 방법을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: af99f369245d6006d0d4784e572020b820e406a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71266052"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Striim을 사용하여 Azure 코스모스 DB 카산드라 API 계정으로 데이터 마이그레이션

Azure 마켓플레이스의 Striim 이미지는 데이터 웨어하우스 및 데이터베이스에서 Azure로 의 지속적인 실시간 데이터 이동을 제공합니다. 데이터를 이동하는 동안 인라인 비정규화, 데이터 변환, 실시간 분석 및 데이터 보고 시나리오를 수행할 수 있습니다. Striim을 사용하여 엔터프라이즈 데이터를 Azure 코스모스 DB 카산드라 API로 지속적으로 이동하기 쉽게 시작할 수 있습니다. Azure는 Striim을 쉽게 배포하고 Azure Cosmos DB로 데이터를 마이그레이션할 수 있는 마켓플레이스 오퍼링을 제공합니다. 

이 문서에서는 Striim을 사용하여 **오라클 데이터베이스에서** **Azure Cosmos DB Cassandra API 계정으로**데이터를 마이그레이션하는 방법을 보여 주며 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure 구독이](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)없는 경우 시작하기 전에 [무료 계정을](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 만듭니다.

* 일부 데이터가 있는 온-프레미스에서 실행되는 Oracle 데이터베이스입니다.

## <a name="deploy-the-striim-marketplace-solution"></a>Striim 마켓플레이스 솔루션 배포

1. [Azure 포털에](https://portal.azure.com/)로그인합니다.

1. **리소스 만들기를** 선택하고 Azure 마켓플레이스에서 **Striim을** 검색합니다. 첫 번째 옵션을 선택하고 **을 만듭니다.**

   ![스트리임 마켓플레이스 아이템 찾기](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. 다음으로 Striim 인스턴스의 구성 속성을 입력합니다. Striim 환경은 가상 시스템에 배포됩니다. **기본** 창에서 **VM 사용자 이름**, **VM 암호를** 입력합니다(이 암호는 SSH를 VM에 사용합니다). Striim을 배포할 **구독,** **리소스 그룹**및 위치 **세부 정보를** 선택합니다. 완료되면 **확인을**선택합니다.

   ![Striim에 대한 기본 설정 구성](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. **Striim 클러스터 설정** 창에서 Striim 배포 유형과 가상 컴퓨터 크기를 선택합니다.

   |설정 | 값 | 설명 |
   | ---| ---| ---|
   |스트리임 배포 유형 |독립 실행형 | Striim은 독립 **실행형** 또는 **클러스터** 배포 유형에서 실행할 수 있습니다. 독립 실행형 모드는 단일 가상 컴퓨터에 Striim 서버를 배포하며 데이터 볼륨에 따라 VM 크기를 선택할 수 있습니다. 클러스터 모드는 선택한 크기의 두 개 이상의 VM에 Striim 서버를 배포합니다. 2개 이상의 노드가 있는 클러스터 환경은 자동 고가용성 및 장애 조치(failover)를 제공합니다.</br></br> 이 자습서에서는 독립 실행형 옵션을 선택할 수 있습니다. 기본 "Standard_F4s" 크기 VM을 사용합니다. | 
   | Striim 클러스터의 이름|    <Striim_cluster_Name>|  Striim 클러스터의 이름입니다.|
   | 스트릴클러스터 비밀번호|   <Striim_cluster_password>|  클러스터의 암호입니다.|

   양식을 채운 후 **확인을** 선택하여 계속합니다.

1. **Striim 액세스 설정** 창에서 **공용 IP 주소(기본값** 선택), **Striim의 도메인 이름, Striim**UI에 로그인하는 데 사용할 **관리자 암호를** 구성합니다. VNET 및 서브넷을 구성합니다(기본값 선택). 세부 정보를 입력한 후 **확인을** 선택하여 계속합니다.

   ![스트리임 액세스 설정](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure는 배포의 유효성을 검사하고 모든 것이 잘 보이는지 확인합니다. 유효성 검사를 완료하는 데 몇 분 정도 걸립니다. 유효성 검사가 완료된 후 **확인을**선택합니다.
  
1. 마지막으로 사용 약관을 검토하고 **만들기를** 선택하여 Striim 인스턴스를 만듭니다. 

## <a name="configure-the-source-database"></a>원본 데이터베이스 구성

이 섹션에서는 Oracle 데이터베이스를 데이터 이동의 원본으로 구성합니다.  [오라클에 연결하려면 오라클 JDBC 드라이버가](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) 필요합니다. 원본 Oracle 데이터베이스에서 변경 내용을 읽으려면 [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) 또는 [XStream API를](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)사용할 수 있습니다. Oracle JDBC 드라이버는 Oracle 데이터베이스에서 데이터를 읽거나 쓰거나 유지하려면 Striim의 Java 클래스 경로에 있어야 합니다.

[ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) 드라이버를 로컬 컴퓨터에 다운로드하십시오. 나중에 Striim 클러스터에 설치합니다.

## <a name="configure-target-database"></a>대상 데이터베이스 구성

이 섹션에서는 Azure Cosmos DB Cassandra API 계정을 데이터 이동의 대상으로 구성합니다.

1. Azure [포털을 사용하여 Azure 코스모스 DB 카산드라 API 계정을](create-cassandra-dotnet.md#create-a-database-account) 만듭니다.

1. Azure Cosmos 계정의 **데이터 탐색기** 창으로 이동합니다. **새 테이블을** 선택하여 새 컨테이너를 만듭니다. *제품을* 마이그레이션하고 Oracle 데이터베이스에서 Azure Cosmos DB로 데이터를 *주문한다고* 가정합니다. 주문 컨테이너를 사용하여 **StriimDemo라는** 새 키 스페이스를 만듭니다. 컨테이너를 **1000R로**프로비전합니다(이 예제에서는 1000개의 RUs를 사용하지만 워크로드에 대해 예상된 처리량을 사용해야 함) 및 **/ORDER_ID** 기본 키로 사용해야 합니다. 이러한 값은 원본 데이터에 따라 다릅니다. 

   ![카산드라 API 계정 만들기](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>오라클에서 Azure 코스모스 DB 데이터 흐름으로 구성

1. 이제 스트리임으로 돌아가봅시다. Striim과 상호 작용하기 전에 이전에 다운로드한 Oracle JDBC 드라이버를 설치합니다.

1. Azure 포털에 배포한 Striim 인스턴스로 이동합니다. 위쪽 메뉴 모음과 **SSH** 탭에서 **연결** 단추를 선택하고 **VM 로컬 계정** 필드를 사용하여 로그인의 URL을 복사합니다.

   ![SSH URL 받기](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. 새 터미널 창을 열고 Azure 포털에서 복사한 SSH 명령을 실행합니다. 이 문서에서는 MacOS에서 터미널을 사용 하며, PuTTY 또는 Windows 컴퓨터에서 다른 SSH 클라이언트를 사용 하 여 유사한 지침을 따를 수 있습니다. 메시지가 표시되면 **예를** 입력하여 계속하고 이전 단계에서 가상 컴퓨터에 대해 설정한 **암호를** 입력합니다.

   ![Striim VM에 연결](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. 이제 새 터미널 탭을 열어 이전에 다운로드한 **ojdbc8.jar** 파일을 복사합니다. 다음 SCP 명령을 사용하여 로컬 컴퓨터에서 Azure에서 실행되는 Striim 인스턴스의 tmp 폴더로 jar 파일을 복사합니다.

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![위치 컴퓨터에서 Striim으로 Jar 파일 복사](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. 그런 다음 SSH를 STRIIM 인스턴스로 하고 sudo로 로그인한 창으로 돌아갑니다. 다음 명령을 통해 **/tmp** 디렉토리에서 **ojdbc8.jar** 파일을 Striim 인스턴스의 **lib** 디렉토리로 이동합니다.

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Jar 파일을 lib 폴더로 이동](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. 동일한 터미널 창에서 다음 명령을 실행하여 Striim 서버를 다시 시작합니다.

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim을 시작하는 데 1 분 정도 걸릴 것입니다. 상태를 보려면 다음 명령을 실행합니다. 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. 이제 Azure로 돌아가서 Striim VM의 공용 IP 주소를 복사합니다. 

   ![복사 Striim VM IP 주소](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Striim의 웹 UI로 이동하려면 브라우저에서 새 탭을 열고 공개 IP를 복사한 다음 9080을 복사합니다. Azure 포털에서 지정한 관리자 암호와 함께 **관리자** 사용자 이름을 사용하여 로그인합니다.

   ![Striim에 로그인](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. 이제 Striim의 홈 페이지에 도착합니다. **대시보드,** **앱**및 **소스 미리 보기의**세 가지 창이 있습니다. 대시보드 창을 사용하면 실시간으로 데이터를 이동하고 시각화할 수 있습니다. 앱 창에는 스트리밍 데이터 파이프라인 또는 데이터 흐름이 포함됩니다. 페이지의 오른쪽에는 데이터를 이동하기 전에 미리 볼 수 있는 SourcePreview가 있습니다.

1. **앱** 창을 선택, 우리는 지금이 창에 초점을 맞출 것이다. Striim에 대해 배우는 데 사용할 수있는 다양한 샘플 앱이 있지만이 기사에서는 우리 고유의 앱을 만듭니다. 오른쪽 상단 모서리에 있는 **앱 추가** 버튼을 선택합니다.

   ![Striim 앱 추가](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Striim 응용 프로그램을 만드는 몇 가지 다른 방법이 있습니다. 이 **시나리오의 경우 처음부터 시작을** 선택합니다.

   ![처음부터 응용 프로그램을 시작합니다](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. **oraToCosmosDB와** 같은 응용 프로그램에 대한 친숙한 이름을 지정하고 **저장을**선택합니다.

   ![새 애플리케이션 만들기](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. 흐름 디자이너에 도착하여 상자 커넥터를 끌어서 삭제하여 스트리밍 응용 프로그램을 만들 수 있습니다. 검색 표시줄에 **Oracle을** 입력하고 **Oracle CDC** 소스를 앱 캔버스에 드래그앤드롭합니다.  

   ![오라클 CDC 소스](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. Oracle 인스턴스의 소스 구성 속성을 입력합니다. 소스 이름은 Striim 응용 프로그램에 대한 명명 규칙일 뿐이며 **src_onPremOracle**와 같은 이름을 사용할 수 있습니다. 또한 어댑터 유형, 연결 URL, 사용자 이름, 암호, 테이블 이름과 같은 다른 세부 정보를 입력합니다. 계속하려면 **저장을** 선택합니다.

   ![소스 매개 변수 구성](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. 이제 스트림의 웨이브 아이콘을 클릭하여 대상 Azure Cosmos DB 인스턴스를 연결합니다. 

   ![대상에 연결](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. 대상을 구성하기 전에 [Striim의 Java 환경에 볼티모어 루트 인증서를](/azure/java/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store)추가했는지 확인하십시오.

1. 대상 Azure Cosmos DB 인스턴스의 구성 속성을 입력하고 **계속하려면 저장을** 선택합니다. 주의해야 할 주요 매개 변수는 다음과 같습니다.

   * **어댑터** - **데이터베이스 라이터**를 사용합니다. Azure 코스모스 DB 카산드라 API에 쓸 때 데이터베이스 작성기가 필요합니다. 카산드라 드라이버 3.6.0은 스트림과 함께 제공됩니다. 데이터베이스 작성기 Azure Cosmos 컨테이너에 프로비전된 R의 수를 초과하면 응용 프로그램이 충돌합니다.

   * **연결 URL** - Azure 코스모스 DB JDBC 연결 URL을 지정합니다. URL이 형식입니다.`jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * **사용자 이름** - Azure Cosmos 계정 이름을 지정합니다.
   
   * **암호** - Azure Cosmos 계정의 기본 키를 지정합니다.

   * **테이블** - 대상 테이블에는 기본 키가 있어야 하며 기본 키는 업데이트할 수 없습니다.

   ![대상 속성 구성](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![대상 속성 구성](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. 이제 Striim 응용 프로그램을 실행하겠습니다. 위 메뉴 모음에서 Created 를 선택한 다음 **앱 배포를** **선택합니다.** 배포 창에서 배포 토폴로지의 특정 부분에 대해 응용 프로그램의 특정 부분을 실행할지 여부를 지정할 수 있습니다. Azure를 통해 간단한 배포 토폴로지에서 실행중이므로 기본 옵션을 사용합니다.

   ![앱 배포](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. 이제 스트림을 미리 보고 Striim을 통해 흐르는 데이터를 살펴보겠습니다. 웨이브 아이콘을 클릭하고 옆에 있는 눈 아이콘을 클릭합니다. 배포 후 스트림을 미리 보고 데이터가 흐르는 것을 볼 수 있습니다. **웨이브** 아이콘과 옆에 있는 **안구를** 선택합니다. 상단 메뉴 모음에서 **배포된** 단추를 선택하고 **앱 시작을**선택합니다.

   ![앱 시작](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. **CDC(데이터 캡처 변경)** 판독기를 사용하여 Striim은 데이터베이스에서 새 변경 내용만 선택합니다. 원본 테이블을 통해 데이터가 흐르는 경우 데이터가 표시됩니다. 그러나 이 테이블은 샘플 테이블이므로 응용 프로그램에 연결되지 않은 원본입니다. 샘플 데이터 생성기를 사용하는 경우 Oracle 데이터베이스에 이벤트 체인을 삽입할 수 있습니다.

1. Striim 플랫폼을 통해 데이터가 흐르는 것을 볼 수 있습니다. Striim은 테이블과 연결된 모든 메타데이터도 선택하므로 데이터를 모니터링하고 데이터가 올바른 대상에 있는지 확인하는 데 유용합니다.

   ![CDC 파이프라인 설정](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. 마지막으로 Azure에 로그인하여 Azure Cosmos 계정으로 이동해 보겠습니다. 데이터 탐색기를 새로 고치면 데이터가 도착했는지 확인할 수 있습니다. 

Azure에서 Striim 솔루션을 사용하면 오라클, 카산드라, 몽고DB 및 기타 다양한 소스에서 Azure 코스모스 DB로 데이터를 지속적으로 마이그레이션할 수 있습니다. 자세한 내용은 [Striim 웹 사이트를](https://www.striim.com/)방문하십시오, [Striim의 무료 30 일 평가판을 다운로드,](https://go2.striim.com/download-free-trial)및 Striim와 마이그레이션 경로를 설정할 때 어떤 문제에 대한, [지원 요청을 제출.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB SQL API로 데이터를 마이그레이션하는 경우 [Striim을 사용하여 Cassandra API 계정으로 데이터를 마이그레이션하는 방법을](cosmosdb-sql-api-migrate-data-striim.md) 참조하세요.

* [Azure Cosmos DB 메트릭으로 데이터 모니터링 및 디버깅](use-metrics.md)
