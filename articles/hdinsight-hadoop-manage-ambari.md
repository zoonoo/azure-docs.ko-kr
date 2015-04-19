<properties
   pageTitle="Ambari를 사용하여 HDInsight 클러스터 관리 | Aure"
   description="Ambari를 사용하여 Linux 기반 HDInsight 클러스터를 모니터링하고 관리하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Ambari를 사용하여 HDInsight 클러스터 관리(미리 보기)

Ambari를 사용하여 Linux 기반 HDInsight 클러스터를 관리하고 모니터링하는 방법에 대해 알아봅니다.

> [AZURE.NOTE] 이 문서에 있는 정보의 대부분은 Linux 기반 HDInsight 클러스터에만 적용됩니다. Windows 기반 HDInsight 클러스터에서는 Ambari REST API를 통한 모니터링만 사용할 수 있습니다. [Ambari API를 사용하여 HDInsight에서 Windows 기반 Hadoop 모니터링](hdinsight-monitor-use-ambari-api.md)을 참조하세요.

##<a id="whatis"></a>Ambari 정의

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a>에서는 Hadoop 클러스터 프로비전, 관리 및 모니터링에 사용할 수 있는 편리한 웹 UI를 제공하여 쉽게 Hadoop을 관리할 수 있습니다. 개발자는 <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">Ambari REST API</a>를 사용하여 자신의 응용 프로그램에 이러한 기능을 통합할 수 있습니다. 

Ambari는 Linux 기반 HDInsight 클러스터를 기본으로 제공합니다. Windows 기반 HDInsight 클러스터는 Ambari REST API를 통해 모니터링 기능을 제공합니다.

##SSH 프록시

> [AZURE.NOTE] 인터넷을 통해 직접 액세스할 수 있는 클러스터의 Ambari의 경우, 일부 기능은 클러스터에서 사용하는 내부 도메인 이름으로 노드 액세스해야 합니다. 공용이 아닌 내부 도메인 이름이어야 하므로 인터넷을 통해 일부 기능에 액세스하면 서버를 찾을 수 없다는 오류를 받습니다.

이 문제를 해결하려면 내부 도메인 이름을 해결할 수 있는 클러스터 헤드 노드에서 SSH 터널을 프록시 웹 트래픽으로 사용합니다. 다음 문서를 사용하여 로컬 컴퓨터의 포트에서 클러스터로 SSH 터널을 만듭니다.

* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Linux, Unix 또는 OS X에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용</a> -  `ssh` 명령을 사용하여 SSH 터널을 만드는 단계

* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용</a> - PuTTY를 사용하여 SSH 터널을 만드는 단계

##Ambari 웹 UI

Ambari 웹 UI는 **https://&lt;clustername>.azurehdinsight.net**에서 만든 각 Linux 기반 HDInsight 클러스터에서 사용할 수 있습니다. Azure 포털에서 클러스터 대시보드 맨 아래에 있는 **Ambari 웹** 단추를 사용하여 이 페이지에 접근할 수도 있습니다.

![ambari web icon](./media/hdinsight-hadoop-manage-ambari/ambari-web.png)

페이지에 인증하라는 메시지가 두 번 표시됩니다. 첫번째 프롬프트는 HDInsight 클러스터를 인증하고 두 번째는 Ambari를 인증합니다.

* **클러스터 인증** - 클러스터 관리자의 사용자 이름(기본값은 **admin**) 및 암호 사용

* **Ambari 인증** - 사용자 이름과 암호의 기본값은 모두 **admin**입니다.

	> [AZURE.NOTE] **admin**의 암호를 변경하는 경우 사용자는 새 암호를 입력해야 합니다.

페이지가 열릴 때 위쪽 막대에 다음 정보와 컨트롤이 포함됩니다.

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari 로고** - **대시보드**를 엽니다.

* **클러스터 이름 # ops** - 진행 중인 Ambari 작업의 수를 표시 합니다. 클러스터 이름 또는 **# ops**를 선택하면 백그라운드 작업의 목록을 표시 합니다.

* **# 경고** - 클러스터에 대한 경고 또는 중요한 알림(있을 경우). 선택하면 경고 목록이 표시됩니다.

* **대시보드** - 클러스터를 모니터링하는데 사용할 수 있는 대시보드를 표시합니다.

* **서비스** - 클러스터에서 서비스에 대한 정보 및 구성 설정

* **호스트** -클러스터의 노드에 대한 정보 및 구성 설정

* **경고** - 긴 정보, 경고 및 중요한 알림

* **Admin** - 설치되었거나 클러스터, 서비스 계정 정보, Kerberos 보안에 추가될 수 있는 소프트웨어 스택/서비스

* **Admin 단추** -Ambari 관리, 사용자 설정 및 로그 아웃

###모니터링

####경고

Ambari는 많은 경고를 보여주며 다음 상태 중 하나입니다.

* **OK**

* **Warning**

* **CRITICAL**

* **UNKNOWN**

**OK**가 아닌 다른 경고는 **# alerts** 항목으로 페이지의 상단에 경고 개수를 표시합니다. 이 항목을 선택하면 경고 및 해당 상태가 표시됩니다.

경고는 여러 가지 기본 그룹으로 구성되며 **경고** 페이지에서 볼 수 있습니다. 

![alerts page](./media/hdinsight-hadoop-manage-ambari/alerts.png)

**작업** 메뉴를 사용하고 **경고 그룹 관리**를 선택하여 그룹을 관리할 수 있습니다. 이 옵션을 사용하면 기존 그룹을 수정하거나 새 그룹을 만들 수 있습니다.

![manage alert groups dialog](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

**작업** 메뉴에서 **경고 알림**을 생성할 수도 있습니다. 이를 통해 특정 경고/심각도 조합이 발생한 경우 **전자 메일** 또는 **SNMP**를 통해 알림을 보내는 트리거를 만들 수 있습니다. 예를 들어, **YARN 기본** 그룹에 **Critical**로 설정된 경고가 있으면 이 경고를 보낼 수 있습니다.

![Create alert dialog](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

####클러스터

**대시보드**의 **메트릭** 탭은 클러스터의 상태를 한 눈에 쉽게 모니터할 수 있는 일련의 위젯을 포함합니다. **CPU 사용량**과 같은 여러 위젯은 클릭하면 추가 정보를 제공합니다.

![dashboard with metrics](./media/hdinsight-hadoop-manage-ambari/metrics.png)

**히트맵** 탭은 녹색에서 빨간색으로 변하는 히트맵처럼 메트릭을 표시합니다.

![dashboard with heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

클러스터 내의 노드에 관한 자세한 정보를 보려면 **호스트**를 선택한 다음 관심있는 특정 노드를 선택합니다.

![host details](./media/hdinsight-hadoop-manage-ambari/host-details.png)

####부여

대시보드의 **서비스** 세로 막대는 클러스터에서 실행되는 서비스 상태에 대한 빠른 정보를 제공합니다. 서비스가 재활용되는 경우 노란색 재활용 기호가 표시되는 것과 같이 수행해야 하는 동작 또는 상태가 다양한 아이콘으로 나타납니다.

![services side-bar](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

서비스를 선택하면 서비스에 대해 보다 자세한 정보를 표시합니다.

![service summary information](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#####빠른 링크

일부 서비스는 페이지의 위쪽에 **빠른 링크**를 표시합니다. 다음과 같이 특정 웹 서비스 UI를 액세스하는데 사용할 수 있습니다.

* **작업 기록** - MapReduce 작업 기록

* **리소스 관리자** - YARN 리소스 관리자 UI

* **NameNode** - HDFS NameNode UI

* **Oozie 웹 UI** - Oozie UI

이러한 링크 중 하나를 선택하면 브라우저에서 선택한 페이지를 표시하는 새 탭이 열립니다.

> [AZURE.NOTE] 서비스에 대한 **빠른 링크**를 선택할 때 프록시 웹 트래픽 클러스터에 SSL 터널을 사용하는 경우가 아니면 서버를 오류를 찾을 수 없다는 오류가 반환됩니다. Ambari가 이러한 링크에 내부 도메인 이름을 사용하기 때문입니다.
> 
> HDInsight와 함께 SSL 터널 사용에 대한 내용은 다음 중 하나를 참조하세요..
> 
> * <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Linux, Unix 또는 OS X에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용</a> -  `ssh` 명령을 사용하여 SSH 터널을 만드는 단계
>
>* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용</a> - Putty를 사용하여 SSH 터널을 만드는 단계

###관리

####Ambari 사용자, 그룹 및 사용 권한

Linux 기반 HDInsight 미리 보기 중 사용자, 그룹 및 사용 권한 관리는 사용되지 않아야 합니다.

####호스트

**호스트** 페이지는 클러스터의 모든 호스트를 나열합니다. 호스트를 관리하려면 다음 단계를 따르세요.

![hosts page](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE] 호스트 추가, 서비스 해제 또는 서비스 등록은 HDInsight 클러스터와 함께 쓰일 수 없습니다.

1. 관리하려는 호스트를 선택합니다.

2. 수행하려는 작업을 선택하려면 **작업** 메뉴를 사용합니다.

	* **모든 구성 요소 시작** - 호스트에서 모든 구성 요소를 시작 합니다.

	* **모든 구성 요소 중지** - 호스트에서 모든 구성 요소를 중지 합니다.

	* **모든 구성 요소 다시 시작** - 호스트에서 모든 구성 요소를 중지하고 시작 합니다.

	* **유지 관리 모드 설정** - 호스트에 대한 경고를 표시하지 않습니다. 경고를 생성하는 작업을 수행하는 경우 사용해야 합니다. 실행 중인 서비스에 의존 하는 서비스를 다시 시작 등

	* **유지 관리 모드 해제** - 호스트의 일반적인 경고를 반환합니다.

	* **중지** - 호스트에서 DataNode 또는 NodeManagers 중지

	* **시작** - 호스트에서 DataNode 또는 NodeManagers 시작

	* **다시 시작** - 호스트에서 DataNode 또는 NodeManagers를 중지하고 시작

	* **서비스 해제** - 호스트를 클러스터에서 제거합니다.

		> [AZURE.NOTE] HDInsight 클러스터에서 이 기능을 사용하지 마십시오

	* **서비스 등록** -이전에 서비스를 해지한 호스트를 클러스터에 추가합니다.

		> [AZURE.NOTE] HDInsight 클러스터에서 이 기능을 사용하지 마십시오

####<a id="service"></a>서비스

**대시보드** 또는 **서비스** 페이지에서 서비스 목록의 아래쪽에 있는 **작업** 버튼을 사용하여 새 서비스를 **추가**하거나 모든 서비스를 **중지** 및 **시작**합니다.

![service actions](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

다음은 **서비스 추가**하는 일반적인 단계입니다.

1. **대시보드** 또는 **서비스** 페이지에서 **작업** 단추를 클릭하고 **서비스 추가**를 선택합니다.

2. **서비스 추가 마법사**에서 추가할 서비스를 선택한 다음 **다음**을 클릭합니다.

	![add service](./media/hdinsight-hadoop-manage-ambari/add-service.png)

3. 서비스에 대한 구성 정보를 제공하는 마법사를 계속 진행합니다. 구성 요구 사항에 대한 자세한 내용은 추가하는 특정 서비스에 대한 설명서를 참조하세요.

4. **검토** 페이지에서 구성 정보를 **인쇄**하거나 클러스터에 서비스를 **배포**할 수 있습니다.

5. 서비스를 배포한 후에는 **설치, 시작, 테스트** 페이지가 서비스 설치되고 테스트되는 진행 정보를 표시합니다. **상태**가 녹색이면 **다음**을 선택합니다.

	![image of install, start, and test page](./media/hdinsight-hadoop-manage-ambari/install-start-test.png)

6. **요약** 페이지는 수행 가능한 작업뿐만 아니라 설치 프로세스의 요약을 표시합니다. 예를 들어, 다른 서비스를 다시 시작합니다. **완료**를 선택하여 마법사를 종료합니다.

**작업** 단추는 모든 서비스를 다시 시작할 수 있는 반면, 특정 서비스를 시작하거나 중지, 다시 시작하려는 경우가 많습니다. 다음 단계를 사용하여 **개별 서비스에서 작업을 수행**할 수 있습니다.

1. **대시보드** 또는 **서비스** 페이지에서 서비스를 선택합니다.

2. **요약** 탭의 맨 위에서 **서비스 동작** 단추를 클릭하고 수행할 동작을 선택합니다. 모든 노드의 서비스를 다시 시작합니다.

	![service action](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE] 클러스터가 실행되는 동안 일부 서비스를 다시 시작하면 경고가 발생할 수 있습니다. 이 문제를 방지하려면 **서비스 동작** 단추를 사용하여 다시 시작하기 전에 서비스에 대한 **유지 관리 모드**를 설정합니다.

3. 동작이 선택되어 있고, 백그라운드 작업이 발생하면 페이지 위쪽에 있는 **# op** 항목의 숫자가 증가합니다. 백그라운드 작업 목록을 표시하도록 구성한 경우 백그라운드 작업 목록이 표시됩니다.

	> [AZURE.NOTE] 서비스에서 **유지 관리 모드**를 사용하도록 설정한 경우 동작이 완료되면 **서비스 동작** 단추를 사용하여 이 모드를 해제합니다.

**서비스를 구성**하려면 다음 단계를 사용합니다.

1. **대시보드** 또는 **서비스** 페이지에서 서비스를 선택합니다.

2. **구성** 탭을 선택합니다. 현재 구성이 표시됩니다. 이전 구성의 목록도 표시됩니다.

	![configurations](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. 구성을 수정하려면 표시된 목록을 클릭하고 **저장**을 선택합니다. 또는 이전 구성을 선택한 다음 **현재**를 선택하여 이전 설정으로 롤백할 수 있습니다.

##REST API

Ambari 웹은 관리 및 모니터링 도구를 만드는데 활용할 수 있는 기본 REST API에 의존 합니다. API는 비교적 간단히 사용할 수 있지만 Azure에서 몇 가지 주의해야 할 사항이 있습니다.

* **인증** - 클러스터 관리자의 사용자 이름(기본값은 **admin**)
*  및 암호는 서비스를 인증하는데 사용해야 합니다.

* **보안** - Ambari 기본 인증을 사용하므로 API와 통신할 때 항상 HTTPS를 사용해야 합니다.

* **IP 주소** - 클러스터가 Azure 가상 네트워크의 멤버가 아니라면 클러스터 내부의 호스트가 반환하는 주소는 클러스터 외부에서는 액세스할 수 없습니다. 하지만 네트워크 외부가 아니라면 가상 네트워크의 다른 구성원은 액세스할 수 있습니다.

* **일부 기능은 설정 되어있지 않습니다** - HDInsight 클라우드 서비스에 의해 관리되는 일부 Ambari 기능이 해제됩니다. 예를 들어 클러스터에서 호스트 추가 또는 제거 기능. Linux 기반 HDInsight의 미리 보기 중에는 다른 기능이 완벽하게 구현되지 않습니다.

REST API의 모든 참조 문서를 보려면 [Ambari API 참조 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)를 참조하세요.


<!--HONumber=47-->
