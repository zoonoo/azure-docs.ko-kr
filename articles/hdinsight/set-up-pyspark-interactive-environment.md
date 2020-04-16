---
title: Azure HDInsight 도구를 사용하여 파이스파크 인터랙티브 환경
description: Azure HDInsight Tools for Visual Studio Code를 사용하여 쿼리와 스크립트를 만들고 제출하는 방법에 대해 알아봅니다.
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,대화형 Hive,대화형 쿼리
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 2a725f3c5c9e1428079807b5b76dbe72d416a9c7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393668"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Visual Studio Code용 PySpark 대화형 환경 설정

다음 단계에서는 VS Code에서 PySpark 대화형 환경을 설정하는 방법을 보여 줍니다.

**python/pip** 명령을 사용하여 홈 경로에서 가상 환경을 빌드합니다. 다른 버전을 사용하려면 **python/pip** 명령의 기본 버전을 수동으로 변경해야 합니다. 자세한 내용은 [update-alternatives](https://linux.die.net/man/8/update-alternatives)를 참조하세요.

1. [파이썬을](https://www.python.org/downloads/) 설치하고 [핍](https://pip.pypa.io/en/stable/installing/).

   + 에서 파이썬을 설치합니다. [https://www.python.org/downloads/](https://www.python.org/downloads/)
   + 에서 [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) 핍을 설치하십시오 (파이썬 설치에서 설치되지 않은 경우).
   + 다음 명령을 사용하여 파이썬 및 핍이 성공적으로 설치되어 있는지 확인합니다. (선택 사항)

        ![파이썬 핍 버전 명령 확인](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > macOS 기본 버전을 사용하는 대신 수동으로 파이썬을 설치하는 것이 좋습니다.

2. 아래 명령을 실행하여 **virtualenv**를 설치합니다.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>기타 패키지

오류 메시지가 발생하면 다음 명령을 실행하여 필요한 패키지를 설치합니다.

   ![파이썬을위한 libkrb5 패키지를 설치](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

VS Code를 다시 시작하고 **HDInsight: PySpark 대화형**을 실행하는 스크립트 편집기로 돌아옵니다.

## <a name="next-steps"></a>다음 단계

### <a name="demo"></a>데모

* HDInsight for VS Code: [비디오](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>도구 및 확장

* [Azure HDInsight Tool for Visual Studio Code 사용](hdinsight-for-vscode.md)
* [IntelliJ용 Azure 도구 키트를 사용하여 Apache Spark Scala 애플리케이션 만들기 및 제출](spark/apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ를 사용하여 SSH를 통해 원격으로 Apache Spark 애플리케이션 디버그](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Azure Toolkit for IntelliJ를 사용하여 VPN을 통해 원격으로 Apache Spark 애플리케이션 디버그](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for Eclipse의 HDInsight 도구를 사용하여 Apache Spark 애플리케이션 만들기](spark/apache-spark-eclipse-tool-plugin.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](spark/apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Azure HDInsight에서 Microsoft Power BI를 사용하여 Apache Hive 데이터 시각화](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight에서 Apache Zeppelin을 사용하여 Apache Hive 쿼리 실행](./interactive-query/hdinsight-connect-hive-zeppelin.md)
