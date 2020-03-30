---
title: JAR 종속성 관리 - Azure HDInsight
description: 이 문서에서는 HDInsight 응용 프로그램에 대한 JAVA 아카이브(JAR) 종속성을 관리하는 모범 사례에 대해 설명합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77135734"
---
# <a name="jar-dependency-management-best-practices"></a>JAR 종속성 관리 모범 사례

HDInsight 클러스터에 설치된 구성 요소는 타사 라이브러리에 종속됩니다. 일반적으로 Guava와 같은 특정 버전의 공통 모듈은 이러한 기본 제공 구성 요소에 의해 참조됩니다. 종속성이 있는 응용 프로그램을 제출하면 동일한 모듈의 다른 버전 간에 충돌이 발생할 수 있습니다. classpath에서 참조하는 구성 요소 버전이 먼저 있는 경우 기본 제공 구성 요소는 버전 비호환성으로 인해 예외를 throw할 수 있습니다. 그러나 기본 제공 구성 요소가 클래스 경로에 먼저 종속성을 삽입하는 경우 `NoSuchMethod`응용 프로그램에서 와 같은 오류가 발생할 수 있습니다.

버전 충돌을 방지하려면 응용 프로그램 종속성을 섀도잉하는 것이 좋습니다.

## <a name="what-does-package-shading-mean"></a>패키지 샤딩은 무엇을 의미합니까?
차광은 종속성을 포함하고 이름을 바꿀 수 있는 방법을 제공합니다. 클래스를 재배치하고 영향을 받는 바이트 코드 및 리소스를 다시 작성하여 종속성의 개인 복사본을 만듭니다.

## <a name="how-to-shade-a-package"></a>패키지를 음영하는 방법?

### <a name="use-uber-jar"></a>동네 짱 항아리를 사용 하 여
Uber-jar는 응용 프로그램 jar과 해당 종속성을 모두 포함하는 단일 항아리 파일입니다. Uber-jar의 종속성은 기본적으로 그늘이 지정되지 않습니다. 경우에 따라 다른 구성 요소 또는 응용 프로그램이 해당 라이브러리의 다른 버전을 참조하는 경우 버전 충돌이 발생할 수 있습니다. 이를 방지하려면 종속성의 일부(또는 전부)가 그늘진 Uber-Jar 파일을 빌드할 수 있습니다.

### <a name="shade-package-using-maven"></a>메이븐을 사용한 쉐이드 패키지
Maven은 자바와 스칼라로 작성된 응용 프로그램을 빌드할 수 있습니다. 메이븐 그늘 플러그인은 쉽게 음영 동네 짱 항아리를 만들 수 있습니다.

아래 예제는 maven-shade-플러그인을 사용하여 패키지를 음영으로 업데이트 한 파일을 `pom.xml` 보여줍니다.  XML 섹션은 `<relocation>…</relocation>` 해당 `com.google.guava` JAR `com.google.shaded.guava` 파일 항목을 이동하고 영향을 받는 바이트 코드를 다시 작성하여 클래스를 패키지에서 패키지로 이동합니다.

변경 `pom.xml`한 후, `mvn package` 당신은 그늘진 동네 짱 항아리를 구축하기 위해 실행할 수 있습니다.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>SBT를 사용한 쉐이드 패키지
SBT는 스칼라와 자바를위한 빌드 도구이기도합니다. SBT는 메이븐 그늘 플러그인 같은 그늘 플러그인이 없습니다. 파일을 수정하여 패키지를 음영으로 만들 `build.sbt` 수 있습니다. 

예를 들어 음영을 `com.google.guava`보려면 `build.sbt` 다음 명령을 파일에 추가할 수 있습니다.

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

그런 다음 `sbt clean` 실행하고 `sbt assembly` 그늘진 항아리 파일을 작성할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [HDInsight 인텔리J 도구 사용](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [IntelliJ에서 스파크에 대한 스칼라 메이븐 응용 프로그램 만들기](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
