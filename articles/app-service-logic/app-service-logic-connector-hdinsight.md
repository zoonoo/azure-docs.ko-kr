<properties
   pageTitle="논리 앱에서 HDInsight 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="HDInsight 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# HDInsight 커넥터 시작 및 논리 앱에 추가
HDInsight 커넥터를 사용하면 Azure에서 Hadoop 클러스터를 생성하고 Hive, Pig, MapReduce 및 스트리밍 MapReduce 작업과 같은 다양한 Hadoop 작업을 제출할 수 있습니다. Azure HDInsight 서비스는 빅 데이터를 관리, 분석 및 보고하기 위한 소프트웨어 프레임워크를 제공하여 클라우드에서 Apache Hadoop 클러스터를 배포하고 프로비전합니다. Hadoop 코어는 HDFS(Hadoop Distributed File System)을 사용하는 안정적인 데이터 저장소와 이 분산 시스템에 저장된 데이터를 병렬로 처리 및 분석하는 간단한 MapReduce 프로그래밍 모델을 제공합니다. HDInsight 커넥터를 사용하여 클러스터를 만들거나 삭제하고 작업을 제출하고 작업이 완료되기를 기다릴 수 있습니다.

커넥터는 논리 앱에 사용하여 흐름의 일부로 데이터 가져오기, 처리 또는 푸시를 수행할 수 있습니다. HDInsight 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.

### 기본 동작

- 클러스터 만들기
- 클러스터 생성 대기
- Pig 작업 제출
- Hive 작업 제출
- MapReduce 작업 제출
- 작업 완료 대기
- 클러스터 삭제


## HDInsight 커넥터 API 앱을 만듭니다. ##

커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. "HDInsight 커넥터"를 검색하여 선택하고 **만들기**를 선택합니다.
3. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다.
4. 패키지 설정에서 HDInsight 클러스터 사용자 이름 및 암호를 입력합니다. **확인**을 선택합니다.
5. **만들기**를 선택합니다. ![][1]  

## 인증서 구성(선택 사항) ##

> [AZURE.NOTE]이 단계는 논리 앱에서 관리 작업(클러스터 만들기 및 삭제)을 수행하고자 하는 경우에만 필요합니다.

방금 만든 HDInsight 커넥터 API 앱으로 이동하면 'Security' 구성 요소에서 0를 확인할 수 있으며 이것은 업로드된 관리 인증서가 없음을 의미합니다. ![][2]

API 앱에 관리 인증을 업로드하려면 다음을 수행해야 합니다.

1. '보안' 구성 요소를 선택합니다.
2. '보안' 블레이드에서 **인증서 업로드**를 선택합니다.
3. 다음 블레이드에서 인증서 파일을 찾아 선택합니다.
4. 인증서가 선택되면 **확인**을 선택합니다.

인증서가 성공적으로 업로드되면 인증서 세부 정보가 표시됩니다. ![][3]

> [AZURE.NOTE]인증서를 변경하려는 경우, 기존 인증서를 대치할 다른 인증서를 업로드할 수 있습니다.

## 논리 앱에서 커넥터 사용하기 ##

HDInsight 커넥터는 논리 앱에서 동작으로만 사용할 수 있습니다. 클러스터를 만들고 Hive 작업을 실행하며 작업 완료 시 클러스터를 삭제하는 단순한 논리 앱을 만들어 봅니다.


1. '논리 시작' 카드에서 '이 논리를 수동으로 실행'을 클릭합니다.
2. 갤러리에서 만든 HDInsight 커넥터 API 앱을 선택합니다. 사용할 수 있는 동작이 나열됩니다. ![][5]

3. '클러스터 만들기'를 선택하고 모든 필수 클러스터 매개 변수를 입력한 다음 ✓를 클릭합니다. ![][6]

4. 이제 논리 앱에서 구성된 대로 동작이 표시됩니다. 동작의 출력이 표시되며 후속 동작에서 입력으로 사용될 수 있습니다. ![][7]

5. 갤러리에서 동일한 HDInsight 커넥터를 동작으로 선택합니다. '클러스터 만들기 대기' 동작을 선택하고 모든 필수 매개 변수를 입력한 다음 ✓를 클릭합니다. ![][8]

6. 갤러리에서 동일한 HDInsight 커넥터를 동작으로 선택합니다. 'Hive 작업 제출' 동작을 선택하고 모든 필수 매개 변수를 입력한 다음 ✓를 클릭합니다. ![][9]

7. 갤러리에서 동일한 HDInsight 커넥터를 동작으로 선택합니다. '작업 완료 대기' 동작을 선택하고 모든 필수 매개 변수를 입력한 다음 ✓를 클릭합니다. ![][10]

8. 갤러리에서 동일한 HDInsight 커넥터를 동작으로 선택합니다. '클러스터 삭제' 동작을 선택하고 모든 필수 매개 변수를 입력한 다음 ✓를 클릭합니다. ![][11]

9. 디자이너 위쪽의 저장 명령을 사용하여 논리 앱을 저장합니다.

시나리오를 테스트하려면 **지금 실행**을 선택하여 논리 앱을 수동으로 시작합니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.


<!--Image references-->
[1]: ./media/app-service-logic-connector-hdinsight/Create.jpg
[2]: ./media/app-service-logic-connector-hdinsight/CertNotConfigured.jpg
[3]: ./media/app-service-logic-connector-hdinsight/CertConfigured.jpg
[5]: ./media/app-service-logic-connector-hdinsight/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-hdinsight/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-hdinsight/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-hdinsight/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-hdinsight/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-hdinsight/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-hdinsight/LogicApp7.jpg

<!---HONumber=Oct15_HO3-->