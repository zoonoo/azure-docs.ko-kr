---
title: Jar 종속성 안전하게 관리하기 - Azure HDInsight
description: 이 문서에서는 HDInsight 응용 프로그램에 대한 JAR(Java Archive) 종속성을 관리하기 위한 모범 사례를 설명합니다.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: 9868e32665c89bbe9aadc06f1c2834704e6534e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942387"
---
# <a name="safely-manage-jar-dependencies"></a>안전하게 jar 종속성 관리

HDInsight 클러스터에 설치된 구성 요소에는 타사 라이브러리에 대한 종속성이 있습니다. 일반적으로는 이 기본 제공 구성 요소는 공용 모듈의 특정 버전(예: Guava)을 참조합니다. 해당 종속성을 사용하여 응용 프로그램을 제출하면 동일한 모듈의 서로 다른 버전 간에 충돌이 발생할 수 있습니다. 먼저 클래스 경로에서 참조하는 구성 요소 버전이 있는 경우, 기본 제공 구성 요소는 버전 비 호환성으로 인해 예외를 발생시킬 수 있습니다. 그러나 기본 제공 구성 요소가 클래스 경로에 대한 종속성을 먼저 주입하는 경우 응용 프로그램에서 `NoSuchMethod`과 같은 오류를 발생시킬 수 있습니다.

버전 충돌을 방지하려면 응용 프로그램 종속성을 셰이딩하는 것이 좋습니다.

## <a name="what-does-package-shading-mean"></a>패키지 셰이딩은 무엇을 의미하나요?
셰이딩은 종속성을 포함하고 이름을 바꾸는 방법을 제공합니다. 클래스를 재배치하고 영향을 받는 바이트 코드와 리소스를 다시 작성하여 종속성의 전용 복사본을 생성합니다.

## <a name="how-to-shade-a-package"></a>패키지를 셰이딩하는 방법

### <a name="use-uber-jar"></a>Uber-jar 사용
Uber-jar는 응용 프로그램 jar 및 해당 종속성을 모두 포함하는 단일 jar 파일입니다. Uber-jar의 종속성은 기본적으로 셰이딩이 적용되지 않습니다. 경우에 따라 다른 구성 요소나 응용 프로그램이 다른 버전의 라이브러리를 참조하는 경우 버전 충돌이 발생할 수 있습니다. 이 문제를 방지하려면 종속성의 일부(또는 모두)가 회색으로 표시된 Uber-Jar 파일을 빌드할 수 있습니다.

### <a name="shade-package-using-maven"></a>Maven를 사용하여 패키지 셰이딩
Maven는 Java 및 Scala에서 작성된 응용 프로그램을 빌드할 수 있습니다. Maven-shade-plugin을 통해 uber-jar를 쉽게 생성할 수 있습니다.

아래 예시에서는 maven-shade-plugin을 사용하여 패키지를 셰이딩하도록 업데이트된 `pom.xml` 파일을 보여줍니다.  XML 섹션 `<relocation>…</relocation>`은 해당 JAR 파일 항목을 이동하고 영향을 받는 바이트 코드를 다시 작성하여 패키지 `com.google.guava`에서 패키지 `com.google.shaded.guava`으로 클래스를 이동합니다.

`pom.xml`을 변경한 후 `mvn package`를 실행하여 셰이딩된 uber-jar을 빌드할 수 있습니다.

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

### <a name="shade-package-using-sbt"></a>SBT를 사용하여 패키지 셰이딩
SBT는 Scala 및 Java용 빌드 도구이기도 합니다. SBT에는 maven-shade-plugin과 같은 셰이딩 플러그인이 없습니다. `build.sbt` 파일을 수정하여 패키지를 셰이딩할 수 있습니다. 

예를 들어, `com.google.guava`을 셰이딩하려면 아래 명령을 `build.sbt` 파일에 추가할 수 있습니다.

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

그런 다음, `sbt clean` 및 `sbt assembly`를 실행하여 셰이딩된 jar 파일을 빌드할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [HDInsight IntelliJ 도구 사용](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

* [IntelliJ Spark용 Scala Maven 응용 프로그램 생성하기](./apache-spark-create-standalone-application.md)