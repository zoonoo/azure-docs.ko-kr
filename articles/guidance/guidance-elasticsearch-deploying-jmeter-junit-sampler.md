<properties
   pageTitle="Elasticsearch 성능을 테스트하기 위한 JMeter JUnit 샘플러 배포 | Microsoft Azure"
   description="JUnit 샘플러를 사용하여 Elasticsearch 클러스터에 데이터를 생성하고 업로드하는 방법입니다."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Elasticsearch 성능을 테스트하기 위한 JMeter JUnit 샘플러 배포

이 문서는 [시리즈의 일부](guidance-elasticsearch.md)입니다.

이 문서에서는 데이터를 만들어 Elasticsearch 클러스터에 업로드할 수 있는 JUnit 샘플러를 JMeter 테스트 계획의 일부로 생성 및 사용하는 방법을 설명합니다. 이 방법은 외부 데이터 파일에 의존하지 않고 대량의 테스트 데이터를 생성할 수 있는 부하 테스트에 대한 매우 유연한 방법을 제공합니다.

> [AZURE.NOTE] Azure의 Elasticsearch로 데이터 수집 성능 최대화 문서에서 설명된 데이터 수집의 성능을 평가하는 데 사용된 부하 테스트는 이 방법을 사용하여 구성되었습니다. 해당 문서에서 JUnit 코드에 대해 자세히 설명합니다.

테스트 데이터 수집 성능의 경우 JUnit 코드는 Eclipse(Mars)를 사용하여 개발되었고 종속성은 Maven을 사용하여 해결되었습니다. 다음 절차에서는 Eclipse를 설치하고 Maven을 구성하며 JUnit 테스트를 만들고 JMeter 테스트에서 이 테스트를 JUnit 요청 샘플러로 배포하는 단계별 프로세스에 대해 설명합니다.

> [AZURE.NOTE] 테스트 환경의 구조 및 구성에 대한 자세한 내용은 [Azure에 Elasticsearch에 대한 성능 테스트 환경 만들기][] 문서를 참조하세요.

## 필수 구성 요소 설치

개발 컴퓨터에는 [Java Runtime Environment](http://www.java.com/en/download/ie_manual.jsp)가 필요합니다. 또한 [Java 개발자를 위한 Eclipse IDE](https://www.eclipse.org/downloads/index.php?show_instructions=TRUE)를 설치해야 합니다.

> [AZURE.NOTE] [Azure에 Elasticsearch에 대한 성능 테스트 환경 만들기][]에서 설명한 JMeter 마스터 VM을 개발 환경으로 사용하는 경우 Windows 32비트 버전의 Eclipse 설치 관리자를 다운로드합니다.

## 부하 테스트 Elasticsearch에 대한 JUnit 테스트 프로젝트 만들기

실행되지 않은 경우 Eclipse IDE를 시작한 다음 *시작* 페이지를 닫습니다. *파일* 메뉴에서 *새로 만들기*와 *Java 프로젝트*를 차례로 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy7.png)

*새 Java 프로젝트* 창에서 프로젝트 이름을 입력하고 *기본 JRE 사용*을 선택한 다음 *마침*을 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy8.png)

*패키지 탐색기* 창에서 프로젝트 이름을 딴 노드를 확장합니다. *src* 폴더 및 지정한 JRE에 대한 참조를 포함하는지 확인합니다.

![](./media/guidance-elasticsearch/jmeter-deploy9.png)

*src* 폴더를 마우스 오른쪽 단추로 클릭하고 *새로 만들기*를 클릭한 다음 *JUnit 테스트 사례*를 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy10.png)

*새 JUnit 테스트 사례* 창에서 *새 Junit 4 테스트*를 선택하고 패키지의 이름(규칙에 따라 소문자로 시작해야 하지만 프로젝트의 이름과 동일할 수 있음)인 테스트 클래스의 이름을 입력하며 테스트에 필요한 메서드 스텁을 생성하는 옵션을 선택합니다. *테스트 중인 클래스* 상자를 비워둔 다음 *마침*을 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy11.png)

다음 *새 JUnit 테스트 사례* 대화 상자가 나타나면 옵션을 지정하여 빌드 경로에 JUnit 4 라이브러리를 추가한 다음 *확인*을 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy12.png)

JUnit 테스트에 대한 골격 코드가 생성되고 Java 편집기 창에서 표시되는지 확인합니다.

![](./media/guidance-elasticsearch/jmeter-deploy13.png)

*패키지 탐색기*에서 프로젝트에 대한 노드를 마우스 오른쪽 단추로 클릭하고 *구성*과 *Maven 프로젝트로 변환*을 차례로 클릭합니다.

> [AZURE.NOTE] Maven을 사용하면 프로젝트가 의존하는 외부 종속성(예: Elasticsearch Java 클라이언트 라이브러리)을 더욱 쉽게 관리할 수 있습니다.

![](./media/guidance-elasticsearch/jmeter-deploy14.png)

*새 POM 만들기* 대화 상자의 *패키징* 드롭다운 목록 상자에서 *jar*을 선택한 다음 *마침*을 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy15.png)

아래의 POM 편집기에 표시되는 창이 경고를 표시할 수 있습니다. *빌드 경로는 실행 환경 J2SE-1.5를 지정합니다. 이 환경*으로 엄격하게 호환되는 작업 영역에 설치된 JRE가 없으며 개발 컴퓨터에 설치한 Java의 버전에 따라 다릅니다. 1.5 버전 보다 최신인 Java 버전인 경우 이 경고는 무시해도 됩니다.

![](./media/guidance-elasticsearch/jmeter-deploy16.png)

POM 편집기에서 *속성*을 확장한 다음 *만들기*를 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy17.png)

*속성 추가* 대화 상자의 *이름* 상자에 *es.version*을 입력하고 *값* 상자에 *1.7.2*를 입력한 다음 *확인*을 클릭합니다. 사용할 Elasticsearch Java 클라이언트 라이브러리의 버전입니다. 이 버전은 나중에 대체될 수 있으며 이 버전을 POM 속성으로 정의하고 프로젝트 내의 다른 곳에서 이 속성을 참조하면 버전을 신속하게 변경할 수 있습니다.

![](./media/guidance-elasticsearch/jmeter-deploy18.png)

POM 편집기의 맨 아래에 있는 *종속성* 탭을 클릭한 다음 *종속성* 목록 상자 옆의 *추가*를 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy19.png)

*종속성 선택* 대화 상자의 *그룹 ID* 상자에 *org.elasticsearch*를 입력하고 *아티팩트 ID* 상자에 *elasticsearch*를 입력하며 *버전* 상자에 *\\${es.version}*을 입력한 다음 *확인*을 클릭합니다. Java Elasticsearch 클라이언트 라이브러리에 대한 정보는 온라인 Maven 중앙 리포지토리에 보관되며 이 구성은 프로젝트가 작성될 때 자동으로 라이브러리 및 종속성을 다운로드합니다.

![](./media/guidance-elasticsearch/jmeter-deploy20.png)

*파일* 메뉴에서 *모두 저장*을 클릭합니다. 이 작업은 프로젝트를 저장하고 빌드하며 이는 Maven에서 지정한 종속성을 다운로드합니다. *Maven 종속성* 폴더가 패키지 탐색기에 나타나는지 확인합니다. 이 폴더를 확장하여 Elasticsearch Java 클라이언트 라이브러리를 지원하기 위해 다운로드한 jar 파일을 봅니다.

![](./media/guidance-elasticsearch/jmeter-deploy21.png)

## Eclipse로 기존 JUnit 테스트 프로젝트 가져오기

이 절차에서는 Eclipse를 사용하여 이전에 만든 Maven 프로젝트를 다운로드했다고 가정합니다.

Eclipse IDE를 시작합니다. *파일* 메뉴에서 *가져오기*를 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy22.png)

*선택* 창에서 *Maven* 폴더를 확장하고 *기존 Maven 프로젝트*를 클릭한 후 *다음*을 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy23.png)

*Maven 프로젝트* 창에서 프로젝트(pom.xml 파일을 포함한 폴더)를 포함하는 폴더를 지정하고 *모두 선택*을 클릭한 다음 *마침*을 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy24.png)

*패키지 탐색기* 창에서 프로젝트에 해당하는 노드를 확장합니다. 프로젝트가 *src* 폴더를 포함하는지 확인합니다. 이 폴더는 JUnit 테스트에 대한 원본 코드를 포함합니다. 아래 지침에 따라 프로젝트를 컴파일 후 배포할 수 있습니다.

![](./media/guidance-elasticsearch/jmeter-deploy25.png)

## JMeter에 JUnit 테스트 배포

이 프로젝트에서는 생성자에 단일 문자열로 전달하는 구성 매개 변수를 허용하는 `BulkLoadTest.java`라는 JUnit 테스트 클래스를 포함하는 LoadTest라는 프로젝트를 만들었다고 가정합니다(JMeter에서 예상하는 메커니즘임).

Eclipse IDE의 패키지 탐색기에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 *내보내기*를 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy26.png)

*내보내기 마법사*의 *선택* 페이지에서 *Java* 노드를 확장하고 *JAR 파일*을 클릭한 후에 *다음*을 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy27.png)

*JAR 파일 사양* 페이지의 *내보낼 리소스 선택* 상자에서 내보낼 프로젝트를 확장하고 *src* 폴더를 제외한 모든 항목의 선택을 취소하며 *.classpath*, *.project* 및 *pom.xml*의 선택을 취소합니다. *JAR 파일* 상자에서 JAR에 대한 파일 이름 및 위치(.jar 파일 확장명이 지정되어야 함)를 제공한 다음 *마침*을 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy28.png)

Windows 탐색기를 사용하여 방금 만든 JAR 파일을 JMeter 마스터 JVM에 복사하고 JMeter를 설치한 폴더에 있는 *apache-jmeter-2.13\\lib\\junit* 폴더에 저장합니다. 자세한 내용은 [JMeter 마스터 가상 컴퓨터에서 JMeter 만들기](guidance-elasticsearch-creating-performance-testing-environment.md#creating-the-jmeter-master-virtual-machine) 절차를 참조하세요.

Eclipse 돌아가서 패키지 탐색기 창을 확장하고 프로젝트에 대한 *Maven 종속성* 폴더에 나열된 모든 JAR 파일 및 해당 위치를 적어둡니다. 사용 중인 Elasticsearch의 버전에 따라 다음 그림에 표시된 파일이 달라질 수 있습니다.

![](./media/guidance-elasticsearch/jmeter-deploy29.png)

Windows 탐색기를 사용하여 Maven 종속성 폴더에서 참조된 각 JAR 파일을 JMeter 마스터 VM의 *apache-jmeter-2.13\\lib\\junit* 폴더에 복사합니다.

*lib\\junit* 폴더가 이미 이러한 JAR 파일의 이전 버전을 포함한 경우 제거합니다. 그대로 두면 참조가 잘못된 JAR로 해결될 수 있기 때문에 JUnit 테스트는 작동하지 않을 수 있습니다.

JMeter 마스터 VM에서 JMeter이 현재 실행 중인 경우 중지합니다. JMeter를 시작합니다. JMeter에서 *테스트 계획*을 마우스 오른쪽 단추로 클릭하고 *추가*, *스레드(사용자)* 및 *스레드 그룹*을 차례로 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy30.png)

*테스트 계획* 노드에서 *스레드 그룹*을 마우스 오른쪽 단추로 클릭하고 *추가*, *샘플러* 및 *JUnit 요청*을 차례로 클릭합니다.

![](./media/guidance-elasticsearch/jmeter-deploy31.png)

*JUnit 요청* 페이지에서 *JUnit4 주석 검색(JUnit 3 아님)*을 선택합니다. *Classname* 드롭다운 목록 상자에서 JUnit 부하 테스트 클래스를 선택하고(*&lt;package&gt;.&lt;class&gt;* 형태로 나열됨) *테스트 메서드* 드롭다운 목록 상자에서 JUnit 테스트 메서드를 선택하고(실제로 테스트와 관련된 작업을 수행하는 메서드이며 Eclipse 프로젝트에서 *@test* 주석으로 표시됨). *생성자 문자열 레이블* 상자의 생성자에 전달되는 값을 입력합니다. 다음 이미지에 표시된 세부 정보는 예제일 뿐입니다. 실제 *Classname*, *테스트 메서드* 및 *생성자 문자열 레이블*은 표시된 것과 다를 것입니다.

![](./media/guidance-elasticsearch/jmeter-deploy32.png)

클래스가 *Classname* 드롭다운 목록 상자에 표시되지 않으면 아마도 JAR이 제대로 내보내지지 않았거나 *lib\\junit* 폴더에 위치하지 않거나 또는 종속 JAR 일부가 *lib\\junit* 폴더에서 누락되었다는 의미입니다. 이 경우 Eclipse에서 프로젝트를 다시 내보내고 *src* 리소스를 선택해야 하며 JAR을 *lib\\junit* 폴더로 복사한 Maven에서 *lib* 폴더로 나열한 모든 종속 JAR을 복사했는지 확인합니다.

JMeter를 닫습니다. 테스트 계획을 저장하지 않아도 됩니다. 각 JMeter 하위 VM의 */home/&lt;username&gt;/apache-jmeter-2.13/lib/junit* 폴더에 JUnit 테스트 클래스를 포함하는 JAR 파일을 복사합니다. *&lt;username&gt;*은 VM을 만들 때 지정한 관리 사용자의 이름입니다. 자세한 내용은 [JMeter 하위 가상 컴퓨터 만들기](guidance-elasticsearch-creating-performance-testing-environment.md#creating-the-jmeter-subordinate-virtual-machines)를 참조하세요.

JUnit 테스트 클래스에서 필요한 종속 JAR 파일을 각 JMeter 하위 VM의 `/home/[username]/apache-jmeter-2.13/lib/junit` 폴더에 복사합니다. 이 폴더에서 JAR 파일의 이전 버전을 먼저 제거해야 합니다.

`pscp` 유틸리티를 사용하여 Windows 컴퓨터에서 Linux에 파일을 복사할 수 있습니다.

[Azure에 Elasticsearch에 대한 성능 테스트 환경 만들기]: guidance-elasticsearch-creating-performance-testing-environment.md

<!---HONumber=AcomDC_0224_2016-->