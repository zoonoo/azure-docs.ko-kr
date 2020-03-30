---
title: 클러스터 생성 중 아파치 하이브 라이브러리 - Azure HDInsight
description: 클러스터를 만드는 동안 HDInsight 클러스터에 아파치 하이브 라이브러리(jar 파일)를 추가하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: 0b746963cea5a950ba47d8b4dfeb074cb0910436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471026"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>HDInsight 클러스터를 만들 때 사용자 지정 Apache Hive 라이브러리 추가

HDInsight에서 [아파치 하이브](https://hive.apache.org/) 라이브러리를 미리 로드하는 방법을 알아봅니다. 이 문서에는 클러스터를 만드는 동안 스크립트 작업을 사용하여 라이브러리를 미리 로드하는 방법에 대한 정보가 포함되어 있습니다. 이 문서의 단계를 사용하여 추가된 라이브러리는 Hive에서 전 세계적으로 [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) 사용할 수 있습니다 .

## <a name="how-it-works"></a>작동 방법

클러스터를 만들 때 스크립트 작업을 사용하여 클러스터 노드가 생성될 때 클러스터 노드를 수정할 수 있습니다. 이 문서의 스크립트는 라이브러리의 위치인 단일 매개 변수를 허용합니다. 이 위치는 Azure Storage 계정에 있어야 하고 라이브러리는 jar 파일로 저장되어야 합니다.

클러스터를 만들 때 스크립트는 파일을 열거하고 헤드 및 작업자 노드의 `/usr/lib/customhivelibs/` 디렉터리에 복사한 다음 `core-site.xml` 파일의 `hive.aux.jars.path` 속성에 추가합니다. 또한 Linux 기반 클러스터에서 파일의 위치로 `hive-env.sh` 파일을 업데이트합니다.

이 문서에서 스크립트 작업을 사용하면 **WebHCat**및 **HiveServer2에**대한 Hive 클라이언트를 사용할 때 라이브러리를 사용할 수 있습니다.

## <a name="the-script"></a>스크립트

**스크립트 위치**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>요구 사항

* 스크립트는 **헤드 노드** 및 **작업자 노드** 모두에 적용되어야 합니다.

* 설치하려는 jar을 **단일 컨테이너**에 있는 Azure Blob Storage에 저장해야 합니다.

* 만드는 동안 jar 파일의 라이브러리를 포함하는 스토리지 계정을 HDInsight 클러스터에 **연결해야** 합니다. 기본 저장소 계정이거나 __저장소 계정 설정을__통해 추가된 계정이어야 합니다.

* 컨테이너에 대한 WASB 경로를 스크립트 작업에 대한 매개 변수로 지정해야 합니다. 예를 들어 jar이 **mystorage** 스토리지 계정의 **libs** 컨테이너에 저장되는 경우 매개 변수는 `wasbs://libs@mystorage.blob.core.windows.net/`입니다.

  > [!NOTE]  
  > 이 문서에서는 사용자가 이미 스토리지 계정과 BLOB 컨테이너를 만들고 거기에 파일을 업로드했다고 가정합니다.
  >
  > 스토리지 계정을 만들지 않은 경우 [Azure Portal](https://portal.azure.com)을 통해 수행할 수 있습니다. [Azure Storage Explorer](https://storageexplorer.com/)와 같은 유틸리티를 사용하여 계정에 컨테이너를 만들고 파일을 업로드할 수 있습니다.

## <a name="create-a-cluster-using-the-script"></a>스크립트를 사용하여 클러스터 만들기

1. [Linux의 HDInsight 클러스터 프로비저닝](hdinsight-hadoop-provision-linux-clusters.md)단계를 사용하여 클러스터 프로비저닝을 시작하지만 프로비저닝을 완료하지는 않습니다. 또한 이 스크립트를 사용하여 클러스터를 만드는 데 Azure PowerShell 또는 HDInsight.NET SDK를 사용할 수도 있습니다. 이 방법을 사용하는 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요. Azure 포털의 경우 **구성 + 가격 책정** 탭에서 **+ 스크립트 추가 작업을**선택합니다.

1. **저장소의**경우 jar 파일 라이브러리가 포함된 저장소 계정이 클러스터에 사용되는 계정과 다른 경우 **추가 저장소 계정을**완료합니다.

1. **스크립트 작업의**경우 다음 정보를 제공합니다.

    |속성 |값 |
    |---|---|
    |스크립트 유형|- 사용자 지정|
    |이름|라이브러리 |
    |Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |노드 유형|머리, 노동자|
    |매개 변수|jar을 포함하는 컨테이너 및 스토리지 계정에 WASB 주소를 입력합니다. `wasbs://libs@mystorage.blob.core.windows.net/`)을 입력합니다.|

    > [!NOTE]
    > 아파치 스파크 2.1의 경우 이 bash `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh`스크립트 URI를 사용합니다.

1. [Linux에서 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md)에서 설명한 대로 클러스터를 계속 프로비전합니다.

클러스터 만들기가 완료되면 `ADD JAR` 문을 사용하지 않고도 Hive에서 이 스크립트를 통해 추가된 jar을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Hive로 작업하는 방법에 대한 자세한 내용은 [HDInsight로 Apache Hive 사용](hadoop/hdinsight-use-hive.md)을 참조하세요.
