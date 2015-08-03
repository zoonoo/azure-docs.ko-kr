<properties
   pageTitle="Ambari를 사용하여 HDInsight 클러스터 관리 | Microsoft Azure"
   description="Ambari를 사용하여 Linux 기반 HDInsight 클러스터를 모니터링하고 관리하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/01/2015"
   ms.author="larryfr"/>

#Ambari를 사용하여 HDInsight 클러스터 관리(미리 보기)

Ambari를 사용하여 Linux 기반 Azure HDInsight 클러스터를 관리하고 모니터링하는 방법에 대해 알아봅니다.

> [AZURE.NOTE]이 문서에 있는 정보의 대부분은 Linux 기반 HDInsight 클러스터에만 적용됩니다. Windows 기반 HDInsight 클러스터에서는 Ambari REST API를 통한 모니터링만 사용할 수 있습니다. [Ambari API를 사용하여 HDInsight에서 Windows 기반 Hadoop 모니터링](hdinsight-monitor-use-ambari-api.md)를 참조하세요.

##<a id="whatis"></a>Ambari 정의

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a>에서는 Hadoop 클러스터 프로비전, 관리 및 모니터링에 사용할 수 있는 편리한 웹 UI를 제공하여 쉽게 Hadoop을 관리할 수 있습니다. 개발자는 <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">Ambari REST API</a>를 사용하여 자신의 응용 프로그램에 이러한 기능을 통합할 수 있습니다.

Ambari는 Linux 기반 HDInsight 클러스터를 기본으로 제공합니다. Windows 기반 HDInsight 클러스터는 Ambari REST API를 통해 모니터링 기능을 제공합니다.

##SSH 프록시

> [AZURE.NOTE]클러스터의 Ambari는 인터넷을 통해 직접 액세스할 수 있지만 Ambari 웹 UI의 일부 링크(예: JobTracker)는 인터넷에 노출되지 않습니다. 따라서 클러스터 헤드 노드로 웹 트래픽을 프록시하는 데 SSH(보안 셸) 터널을 사용하지 않는 경우, 이러한 기능에 액세스 하려고 하면 "서버를 찾을 수 없음" 오류가 발생합니다.

다음 문서를 사용하여 로컬 컴퓨터의 포트에서 클러스터로 SSH 터널을 만듭니다.

* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Linux, Unix 또는 OS X에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용</a>: `ssh` 명령을 사용하여 SSH 터널을 만드는 단계

* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용</a> - PuTTY를 사용하여 SSH 터널을 만드는 단계

##Ambari 웹 UI

Ambari 웹 UI는 **https://&lt;clustername>.azurehdinsight.net**에서 만든 각 Linux 기반 HDInsight 클러스터에서 사용할 수 있습니다. Azure 포털에서 클러스터 대시보드 맨 아래에 있는 **Ambari 웹** 단추를 사용하여 이 페이지에 접근할 수도 있습니다.

![ambari 웹 아이콘](./media/hdinsight-hadoop-manage-ambari/ambari-web.png)

페이지에 인증하라는 메시지가 두 번 표시됩니다. 첫 번째는 HDInsight 클러스터에 인증하라는 메시지이고 두 번째는 Ambari에 인증하라는 메시지입니다.

* **클러스터 인증** - 클러스터 관리자의 사용자 이름(기본값은 **admin**) 및 암호를 사용합니다.

* **Ambari 인증** - 사용자 이름과 암호의 기본값은 모두 **admin**입니다.

	> [AZURE.NOTE]**admin**의 암호를 변경한 경우 사용자는 새 암호를 입력해야 합니다.

페이지가 열리면 위쪽의 표시줄을 확인합니다. 여기에는 다음 정보 및 컨트롤이 포함되어 있습니다.

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari 로고** - 클러스터를 모니터링하는 데 사용할 수 있는 대시보드를 엽니다.

* **클러스터 이름 # ops** - 진행 중인 Ambari 작업 수를 표시합니다. 클러스터 이름 또는 **# ops**를 선택하면 백그라운드 작업 목록이 표시됩니다.

* **# alerts** - 클러스터에 대한 경고 또는 중요한 알림(있을 경우)입니다. 선택하면 경고 목록이 표시됩니다.

* **Dashboard** - 대시보드를 표시합니다.

* **Services** - 클러스터의 서비스에 대한 정보 및 구성 설정입니다.

* **Hosts** - 클러스터의 노드에 대한 정보 및 구성 설정입니다.

* **Alerts** - 정보, 경고 및 중요한 알림에 대한 로그입니다.

* **Admin** - 설치되었거나 클러스터, 서비스 계정 정보 및 Kerberos 보안에 추가할 수 있는 소프트웨어 스택/서비스입니다.

* **Admin 단추** - Ambari 관리, 사용자 설정 및 로그 아웃입니다.

##모니터링

###경고

Ambari는 많은 경고를 보여주며 다음 상태 중 하나입니다.

* **OK**

* **Warning**

* **CRITICAL**

* **UNKNOWN**

**OK**가 아닌 다른 경고는 페이지 위쪽의 **# alerts** 항목에 경고 수를 표시합니다. 이 항목을 선택하면 경고 및 해당 상태가 표시됩니다.

경고는 여러 가지 기본 그룹으로 구성되며 **Alerts** 페이지에서 볼 수 있습니다.

![경고 페이지](./media/hdinsight-hadoop-manage-ambari/alerts.png)

**Actions** 메뉴를 사용하고 **Manage Alert Groups**를 선택하여 그룹을 관리할 수 있습니다. 이 옵션을 사용하면 기존 그룹을 수정하거나 새 그룹을 만들 수 있습니다.

![경고 그룹 관리 대화 상자](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

**Actions** 메뉴에서 경고 알림을 생성할 수도 있습니다. 이를 통해 특정 경고/심각도 조합이 발생한 경우 **EMAIL** 또는 **SNMP**를 통해 알림을 보내는 트리거를 만들 수 있습니다. 예를 들어 **YARN Default** 그룹에 **Critical**로 설정된 경고가 있으면 이 경고를 보낼 수 있습니다.

![경고 만들기 대화 상자](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

###프로비전

대시보드의 **Metrics** 탭은 클러스터의 상태를 한 눈에 쉽게 모니터할 수 있는 일련의 위젯을 포함합니다. **CPU Usage**와 같은 여러 위젯은 클릭하면 추가 정보를 제공합니다.

![메트릭이 표시된 대시보드](./media/hdinsight-hadoop-manage-ambari/metrics.png)

**Heatmaps** 탭은 녹색에서 빨간색으로 변하는 히트맵처럼 메트릭을 표시합니다.

![히트맵이 표시된 대시보드](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

클러스터 내의 노드에 관한 자세한 정보를 보려면 **Hosts**를 선택한 다음 관심 있는 특정 노드를 선택합니다.

![호스트 세부 정보](./media/hdinsight-hadoop-manage-ambari/host-details.png)

###서비스

대시보드의 **Services** 세로 막대는 클러스터에서 실행되는 서비스 상태에 대한 빠른 정보를 제공합니다. 서비스가 재활용되는 경우 노란색 재활용 기호가 표시되는 것과 같이 수행해야 하는 동작 또는 상태가 다양한 아이콘으로 나타납니다.

![서비스 세로 막대](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

서비스를 선택하면 해당 서비스에 대한 자세한 정보가 표시됩니다.

![서비스 요약 정보](./media/hdinsight-hadoop-manage-ambari/service-details.png)

####빠른 링크

일부 서비스는 페이지의 위쪽에 **Quick Links** 링크를 표시합니다. 이를 사용하여 서비스 관련 웹 UI에 액세스할 수 있습니다.

* **Job History** - MapReduce 작업 기록

* **Resource Manager** - YARN ResourceManager UI

* **NameNode** - HDFS(Hadoop Distributed File System) NameNode UI

* **Oozie Web UI** - Oozie UI

이러한 링크 중 하나를 선택하면 브라우저에서 선택한 페이지를 표시하는 새 탭이 열립니다.

> [AZURE.NOTE]서비스에 대한 **Quick Links** 링크를 선택한 경우 SSL(Secure Sockets Layer) 터널을 사용하여 클러스터에 대한 웹 트래픽을 프록시하지 않으면 "server not found" 오류가 반환됩니다. 이 정보를 표시하는 데 사용되는 웹 응용 프로그램이 인터넷에 노출되지 않기 때문입니다.
>
> HDInsight와 함께 SSL 터널 사용에 대한 내용은 다음 중 하나를 참조하세요..
>
> * <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Linux, Unix 또는 OS X에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용</a> - `ssh` 명령을 사용하여 SSH 터널을 만드는 단계
>
>* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용</a> - PuTTY를 사용하여 SSH 터널을 만드는 단계

##관리

###Ambari 사용자, 그룹 및 사용 권한

Linux 기반 HDInsight 미리 보기 중에는 사용자, 그룹 및 사용 권한 관리를 사용할 수 없습니다.

###호스트

**Hosts** 페이지는 클러스터의 모든 호스트를 나열합니다. 호스트를 관리하려면 다음 단계를 따르세요.

![호스트 페이지](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE]호스트 추가, 서비스 해제 또는 서비스 등록은 HDInsight 클러스터에서 사용할 수 없습니다.

1. 관리하려는 호스트를 선택합니다.

2. **Actions** 메뉴를 사용하여 수행할 작업을 선택합니다.

	* **Start all components** - 호스트에서 모든 구성 요소를 시작합니다.

	* **Stop all components** - 호스트에서 모든 구성 요소를 중지합니다.

	* **Restart all components** - 호스트에서 모든 구성 요소를 중지하고 시작합니다.

	* **Turn on maintenance mode** - 호스트에 대한 경고를 표시하지 않습니다. 실행 중인 서비스가 의존하는 서비스를 다시 시작하는 등 경고가 생성되는 작업을 수행 중인 경우 이를 사용해야 합니다.

	* **Turn off maintenance mode** - 호스트를 정상 경고 상태로 되돌립니다.

	* **Stop** - 호스트에서 DataNode 또는 NodeManagers를 중지합니다.

	* **Start** - 호스트에서 DataNode 또는 NodeManagers를 시작합니다.

	* **Restart** - 호스트에서 DataNode 또는 NodeManagers를 중지하고 시작합니다.

	* **Decommission** - 호스트를 클러스터에서 제거합니다.

		> [AZURE.NOTE]HDInsight 클러스터에서는 이 작업을 사용하지 마세요.

	* **Recommission** - 이전에 서비스를 해지한 호스트를 클러스터에 추가합니다.

		> [AZURE.NOTE]HDInsight 클러스터에서는 이 작업을 사용하지 마세요.

###<a id="service"></a>Services

**Dashboard** 또는 **Services** 페이지에서 서비스 목록의 아래쪽에 있는 **Actions** 단추를 사용하여 새 서비스를 추가하거나 모든 서비스를 중지 및 시작합니다.

![서비스 작업](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

다음은 서비스를 추가하는 일반적인 단계입니다.

1. **Dashboard** 또는 **Services** 페이지에서 **Actions** 단추를 사용하여 **Add Service**를 선택합니다.

2. **Add Service Wizard**에서 추가할 서비스를 선택한 다음 **Next**를 클릭합니다.

	![서비스 추가](./media/hdinsight-hadoop-manage-ambari/add-service.png)

3. 서비스에 대한 구성 정보를 제공하는 마법사를 계속 진행합니다. 구성 요구 사항에 대한 자세한 내용은 추가하는 특정 서비스에 대한 설명서를 참조하세요.

4. **Review** 페이지에서 구성 정보를 **Print**하거나 클러스터에 서비스를 **Deploy**할 수 있습니다.

5. 서비스를 배포한 후에는 **Install, Start and Test** 페이지에 서비스 설치 및 테스트 진행률 정보가 표시됩니다. **Status**가 녹색이면 **Next**를 선택합니다.

	![설치, 시작 및 테스트 페이지의 이미지](./media/hdinsight-hadoop-manage-ambari/install-start-test.png)

6. **Summary** 페이지에는 설치 프로세스의 요약 및 수행해야 하는 가능한 모든 작업(예: 다른 서비스 다시 시작)이 표시됩니다. **Complete**를 선택하여 마법사를 종료합니다.

**Actions** 단추는 모든 서비스를 다시 시작할 수 있는 반면, 특정 서비스를 시작하거나 중지, 다시 시작하려는 경우가 많습니다. 다음 단계를 사용하여 개별 서비스에서 작업을 수행할 수 있습니다.

1. **Dashboard** 또는 **Services** 페이지에서 서비스를 선택합니다.

2. **Summary** 탭의 위쪽에서 **Service Actions** 단추를 사용하여 수행할 작업을 선택합니다. 모든 노드의 서비스를 다시 시작합니다.

	![서비스 작업](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE]클러스터가 실행되는 동안 일부 서비스를 다시 시작하면 경고가 발생할 수 있습니다. 이 문제를 방지하려면 **Service Actions** 단추를 사용하여 다시 시작하기 전에 서비스에 대한 **Maintenance mode**를 설정합니다.

3. 동작이 선택되어 있고, 백그라운드 작업이 발생하면 페이지 위쪽에 있는 **# op** 항목의 숫자가 증가합니다. 백그라운드 작업 목록을 표시하도록 구성한 경우 백그라운드 작업 목록이 표시됩니다.

	> [AZURE.NOTE]서비스에 대한 **Maintenance mode**를 사용하도록 설정한 경우 작업이 완료되면 **Service Actions** 단추를 사용하여 이 모드를 해제합니다.

서비스를 구성하려면 다음 단계를 사용합니다.

1. **Dashboard** 또는 **Services** 페이지에서 서비스를 선택합니다.

2. **Configs** 탭을 선택합니다. 현재 구성이 표시됩니다. 이전 구성의 목록도 표시됩니다.

	![구성](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. 구성을 수정하려면 표시된 목록을 클릭하고 **Save**를 선택합니다. 또는 이전 구성을 선택한 다음 **Make current**를 선택하여 이전 설정으로 롤백할 수 있습니다.

##REST API

Ambari 웹은 관리 및 모니터링 도구를 만드는데 활용할 수 있는 기본 REST API에 의존 합니다. API는 비교적 간단히 사용할 수 있지만 Azure에서 몇 가지 주의해야 할 사항이 있습니다.

* **인증** - 클러스터 관리자 사용자 이름(기본값은 **admin**) 및 암호를 사용하여 서비스에 인증해야 합니다.

* **보안** - Ambari 기본 인증을 사용하므로 API와 통신할 때 항상 HTTPS(보안 HTTP)를 사용해야 합니다.

* **IP 주소** - 클러스터가 Azure 가상 네트워크의 구성원이 아니면 클러스터 외부에서 클러스터 내부의 호스트에 대해 반환되는 주소에 액세스할 수 없습니다. 하지만 네트워크 외부가 아니라면 가상 네트워크의 다른 구성원은 액세스할 수 있습니다.

* **일부 기능은 사용할 수 없음** - HDInsight 클라우드 서비스에 의해 관리되는 일부 Ambari 기능(예: 클러스터에서 호스트 추가 또는 제거)은 사용할 수 없도록 설정됩니다. Linux 기반 HDInsight의 미리 보기 중에는 다른 기능이 완벽하게 구현되지 않습니다.

REST API의 모든 참조 문서를 보려면 [Ambari API 참조 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)을 참조하세요.

<!---HONumber=July15_HO4-->