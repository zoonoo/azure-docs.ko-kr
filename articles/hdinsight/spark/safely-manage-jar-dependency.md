---
title: Jar 종속성 안전 하 게 관리-Azure HDInsight
description: 이 문서에서는 HDInsight 응용 프로그램에 대 한 JAR (Java Archive) 종속성을 관리 하기 위한 모범 사례를 설명 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: 5834d3512c95e77e6ce45472ff8f2e1b4b2ed456
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545585"
---
# <a name="safely-manage-jar-dependencies"></a>안전하게 jar 종속성 관리

HDInsight 클러스터에 설치 된 구성 요소에는 타사 라이브러리에 대 한 종속성이 있습니다. 일반적으로는이 기본 제공 구성 요소에서 참조 하는 공용 모듈의 특정 버전 (예: 가드 Ava)입니다. 해당 종속성을 사용 하 여 응용 프로그램을 제출 하면 동일한 모듈의 서로 다른 버전 간에 충돌이 발생할 수 있습니다. 먼저 classpath에서 참조 하는 구성 요소 버전이 있는 경우 기본 제공 구성 요소는 버전 비 호환성으로 인해 예외를 throw 할 수 있습니다. 그러나 기본 제공 구성 요소가 클래스 경로에 대 한 종속성을 먼저 주입 하는 경우 응용 프로그램에서와 같은 오류를 throw 할 수 있습니다 `NoSuchMethod` .

버전 충돌을 방지 하려면 응용 프로그램 종속성을 음영 처리 하는 것이 좋습니다.

## <a name="what-does-package-shading-mean"></a>패키지 음영은 무엇을 의미 하나요?
음영은 종속성을 포함 하 고 이름을 바꾸는 방법을 제공 합니다. 클래스를 재배치 하 고 영향을 받는 바이트 코드와 리소스를 다시 작성 하 여 종속성의 전용 복사본을 만듭니다.

## <a name="how-to-shade-a-package"></a>패키지를 음영 처리 하는 방법

### <a name="use-uber-jar"></a>Uber 사용-jar
Uber-jar는 응용 프로그램 jar 및 해당 종속성을 모두 포함 하는 단일 jar 파일입니다. Uber-jar의 종속성은 기본적으로 음영이 적용 되지 않습니다. 경우에 따라 다른 구성 요소나 응용 프로그램이 다른 버전의 라이브러리를 참조 하는 경우 버전 충돌이 발생할 수 있습니다. 이 문제를 방지 하려면 종속성의 일부 (또는 모두)가 회색으로 표시 된 Uber-Jar 파일을 빌드할 수 있습니다.

### <a name="shade-package-using-maven"></a>Maven를 사용 하 여 패키지 음영 처리
Maven는 Java 및 Scala 둘 다에서 작성 된 응용 프로그램을 빌드할 수 있습니다. Maven-플러그 인을 통해 음영으로 된 uber를 쉽게 만들 수 있습니다.

아래 예제에서는 `pom.xml` maven-플러그 인을 사용 하 여 패키지를 음영으로 업데이트 한 파일을 보여 줍니다.  XML 섹션은 `<relocation>…</relocation>` `com.google.guava` `com.google.shaded.guava` 해당 JAR 파일 항목을 이동 하 고 영향을 받는 바이트 코드를 다시 작성 하 여 패키지에서 패키지로 클래스를 이동 합니다.

를 변경한 후를 `pom.xml` 실행 `mvn package` 하 여 음영 처리 된 uber을 빌드할 수 있습니다.

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

### <a name="shade-package-using-sbt"></a>SBT를 사용 하 여 패키지 음영 처리
SBT는 Scala 및 Java에 대 한 빌드 도구 이기도 합니다. SBT에는 maven와 같은 음영 플러그 인이 없습니다. `build.sbt`패키지를 음영으로 파일을 수정할 수 있습니다. 

예를 들어 음영을 표시 하려면 `com.google.guava` 다음 명령을 파일에 추가 하면 됩니다 `build.sbt` .

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

그런 다음 `sbt clean` 및를 실행 `sbt assembly` 하 여 음영 처리 된 jar 파일을 빌드할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [HDInsight IntelliJ 도구 사용](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

* [IntelliJ에서 Spark 용 Scala Maven 응용 프로그램 만들기](./apache-spark-create-standalone-application.md)