<properties
 pageTitle="JDBC를 사용하여 Azure HDInsight에서 Hive 쿼리"
 description="JDBC를 사용하여 Azure HDInsight의 Hive를 연결하고 클라우드에 저장된 데이터에서 쿼리를 원격으로 실행하는 방법을 알아봅니다."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="09/23/2015"
 ms.author="larryfr"/>

#Hive JDBC 드라이버를 사용하여 Azure HDInsight에서 Hive에 연결

[AZURE.INCLUDE [ODBC JDBC 선택기](../../includes/hdinsight-selector-odbc-jdbc.md)]

이 문서에서는 Java 응용 프로그램에서 JDBC를 사용하여 원격으로 HDInsight 클러스터에 Hive 쿼리를 제출하는 방법에 대해 배웁니다. Hive JDBC 인터페이스에 대한 자세한 내용은 [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface)를 참조하세요.

##필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* HDInsight 클러스터의 Hadoop. Linux 또는 Windows 기반의 클러스터가 작동합니다.

* [JDK(Java Developer Kit) 버전 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 이상

* [Apache Maven](https://maven.apache.org). Maven은 이 문서와 관련된 프로젝트에서 사용되는 Java 프로젝트에 대한 프로젝트 빌드 시스템입니다.

##연결 문자열

Azure에서 HDInsight 클러스터에 대한 JDBC가 443을 통해 연결되어 트래픽이 SSL을 사용하여 보호됩니다. 클러스터가 뒤에 있는 공용 게이트웨이는 HiveServer2에서 실제로 수신하는 포트로 트래픽을 리디렉션합니다. 따라서 일반적인 연결 문자열은 다음과 같습니다.

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

##인증

연결을 설정할 때 HDInsight 클러스터 관리자 이름 및 암호를 지정해야 합니다. 해당 이름 및 암호가 게이트웨이에 대한 요청을 인증합니다. 예를 들어 다음 Java 코드는 연결 문자열, 관리자 이름 및 암호를 사용하여 새 연결을 엽니다.

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##쿼리

연결이 설정되면 Hive에 대한 쿼리를 실행할 수 있습니다. 예를 들어 다음 Java 코드는 테이블에서 3개의 행으로만 결과를 제한하는 __SELECT__를 수행하고 결과를 표시합니다.

    sql = "SELECT querytime, market, deviceplatform, devicemodel, state, country from " + tableName + " LIMIT 3";
    stmt2 = conn.createStatement();
    System.out.println("\nRetrieving inserted data:");

    res2 = stmt2.executeQuery(sql);

    while (res2.next()) {
      System.out.println( res2.getString(1) + "\t" + res2.getString(2) + "\t" + res2.getString(3) + "\t" + res2.getString(4) + "\t" + res2.getString(5) + "\t" + res2.getString(6));
    }

##Java 프로젝트 예제

HDInsight에서 Hive를 쿼리하는 데 Java 클라이언트를 사용하는 예제를 [https://github.com/Blackmist/hdinsight-hive-jdbc](https://github.com/Blackmist/hdinsight-hive-jdbc)에서 사용할 수 있습니다. 리포지토리의 지침에 따라 샘플을 빌드하고 실행합니다.

##다음 단계

JDBC를 사용하여 Hive와 함께 작업하는 방법을 살펴보았으므로 이제 다음 링크를 사용하여 Azure HDInsight로 작업하는 다른 방법을 알아봅니다.

* [HDInsight에 데이터 업로드](hdinsight-upload-data.md)
* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)

<!---HONumber=Sept15_HO4-->