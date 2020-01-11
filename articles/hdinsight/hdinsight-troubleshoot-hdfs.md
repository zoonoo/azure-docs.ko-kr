---
title: Azure HDInsight에서 HDFS 문제 해결
description: HDFS 및 Azure HDInsight 작업에 대한 일반적인 질문에 답합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 6b0a81a2f3af10a1e5ad60c6c33357a6e906ee47
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895240"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Azure HDInsight를 사용하여 Apache Hadoop HDFS 문제 해결

Apache Ambari에서 HDFS(Hadoop 분산 파일 시스템) 페이로드를 사용할 때의 주요 문제 및 해결 방법을 알아봅니다. 전체 명령 목록은 [HDFS 명령 가이드](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) 및 [파일 시스템 셸 가이드](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)를 참조 하세요.

## <a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>클러스터 내부에서 로컬 HDFS에 액세스하는 방법

### <a name="issue"></a>문제

HDInsight 클러스터 내에서 Azure Blob Storage 또는 Azure Data Lake Storage를 사용하는 대신, 명령줄 및 애플리케이션 코드에서 로컬 HDFS에 액세스합니다.

### <a name="resolution-steps"></a>해결 단계:

1. 다음 명령과 같이 명령 프롬프트에서 `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...`를 그대로 사용합니다.

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. 다음 예제 애플리케이션과 같이 소스 코드에서 URI `hdfs://mycluster/`를 그대로 사용합니다.

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. 다음 명령을 사용하여 HDInsight 클러스터에서 컴파일된 .jar 파일(예: `java-unit-tests-1.0.jar`)을 실행합니다.

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="du"></a>du

[-Du](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du) 명령은 지정 된 디렉터리에 포함 된 파일 및 디렉터리의 크기나 파일의 길이에 대 한 파일 길이를 표시 합니다.

`-s` 옵션은 표시 되는 파일 길이의 집계 요약을 생성 합니다.  
`-h` 옵션은 파일 크기의 형식을 지정 합니다.

예:

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>rm

[-Rm](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) 명령은 인수로 지정 된 파일을 삭제 합니다.

예:

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* [@AzureSupport](https://twitter.com/azuresupport) 연결-고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
