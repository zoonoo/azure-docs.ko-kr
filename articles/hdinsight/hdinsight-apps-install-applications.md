<properties
   	pageTitle="HDInsight에서 Hadoop 응용 프로그램 설치 | Microsoft Azure"
   	description="HDInsight 응용 프로그램에서 HDInsight 응용 프로그램을 설치하는 방법을 알아봅니다."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="06/29/2016"
   	ms.author="jgao"/>

# HDInsight 응용 프로그램 설치

HDInsight 응용 프로그램은 Linux 기반 HDInsight 클러스터에 사용자가 설치할 수 있는 응용 프로그램입니다. Microsoft, ISV(독립 소프트웨어 공급 업체) 또는 사용자가 직접 이러한 응용 프로그램을 개발할 수 있습니다. 이 문서에서는 게시된 응용 프로그램을 설치하는 방법을 알아봅니다. 사용자 고유의 응용 프로그램을 설치하는 방법은 [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md)를 참조하세요.

현재 하나의 게시된 응용 프로그램이 있습니다.

- **Datameer**: [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft)에서는 분석가에게 빅 데이터의 결과를 검색, 분석 및 시각화하는 대화형 방법을 제공합니다. 새 관계를 검색하고 필요한 답변을 신속하게 가져오도록 쉽게 추가 데이터 원본을 끌어옵니다.

>[AZURE.NOTE] Datameer에서는 현재 Azure HDInsight 버전 3.2 클러스터에서만 지원됩니다.

이 문서에서 제공하는 지침은 Azure 포털을 사용합니다. 또한 포털에서 Azure Resouce Manager 템플릿을 내보내거나 공급 업체에서 Resouce Manager 템플릿의 복사본을 가져오고 Azure PowerShell 및 Azure CLI를 사용하여 템플릿을 배포할 수 있습니다. [Resource Manager 템플릿을 사용하여 HDInsight의 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.

## 필수 조건

기존 HDInsight 클러스터에 HDInsight 응용 프로그램을 설치하려면 HDInsight 클러스터가 있어야 합니다. HDInsight 클러스터를 만들려면 [클러스터 만들기](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요. HDInsight 클러스터를 만들 경우 HDInsight 응용 프로그램도 설치할 수 있습니다.

## 기존 클러스터에 응용 프로그램 설치

다음 절차에서는 기존 HDInsight 클러스터에 HDInsight 응용 프로그램을 설치하는 방법을 보여 줍니다.

**HDInsight 응용 프로그램을 설치하려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **HDInsight 클러스터**를 클릭합니다. 표시되지 않으면 **찾아보기**를 클릭한 다음 **HDInsight 클러스터**를 클릭합니다.
3. HDInsight 클러스터를 클릭합니다. 하나도 없는 경우 [클러스터 만들기](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하여 HDInsight 클러스터를 만듭니다.
4. **설정** 블레이드에서 **일반** 범주에 있는 **응용 프로그램**을 클릭합니다. **설치된 앱** 블레이드에서는 설치된 모든 응용 프로그램을 나열합니다.

    ![HDInsight 응용 프로그램 포털 메뉴](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)

5. 블레이드 메쥬에서 **추가**를 클릭합니다.

    ![HDInsight 응용 프로그램 설치된 앱](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)

	기존 HDInsight 응용 프로그램 목록이 표시됩니다.

	![HDInsight 응용 프로그램 사용 가능한 응용 프로그램](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)

6. 응용 프로그램 중 하나를 클릭하고 약관에 동의한 다음 **선택**을 클릭합니다.

포털 알림에서 설치 상태를 확인할 수 있습니다(포털 맨 위에 있는 종 모양 아이콘 클릭). 응용 프로그램이 설치되면 설치된 앱 블레이드에 표시됩니다.

## 클러스터 생성 중에 응용 프로그램 설치

클러스터를 만들 경우 HDInsight 응용 프로그램을 설치하는 옵션이 있습니다. 클러스터가 만들어지고 실행 상태가 되면 프로세스 중에 HDInsight 응용 프로그램이 설치됩니다. 다음 절차에서는 클러스터를 만들 때 HDInsight 응용 프로그램을 설치하는 방법을 보여 줍니다.

**HDInsight 응용 프로그램을 설치하려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **데이터 + 분석** 및 **HDInsight**를 차례로 클릭합니다.
3. **클러스터 이름** 입력: 이 이름은 전역적으로 고유해야 합니다.
4. **구독**을 클릭하여 이 클러스터에 사용할 Azure 구독을 선택합니다.
5. **클러스터 유형 선택**을 클릭한 후 다음을 선택합니다.

    - **클러스터 유형**: 어떤 유형을 선택할지 잘 모르는 경우 **Hadoop**을 선택합니다. 가장 인기 있는 클러스터 유형입니다.
    - **운영 체제**: **Linux**를 선택합니다.
    - **버전**: 어떤 버전을 선택할지 잘 모르는 경우 기본 버전을 사용합니다. 자세한 내용은 [HDInsight 클러스터 버전](hdinsight-component-versioning.md)을 참조하세요.
    - **클러스터 계층**: Azure HDInsight는 빅 데이터 클라우드 제품을 표준 계층 및 프리미엄 계층의 두 범주로 제공합니다. 자세한 내용은 [클러스터 계층](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers)을 참조하세요.
6. **응용 프로그램**, 게시된 응용 프로그램 중 하나, **선택**을 차례로 클릭합니다.
6. **자격 증명**을 클릭한 다음 관리자의 암호를 입력합니다. 또한 **SSH 사용자 이름**과 **암호** 또는 **공개 키**(SSH 사용자를 인증하는 데 사용됨) 중 하나를 입력해야 합니다. 공개 키를 사용하는 것이 권장 방식입니다. 아래쪽의 **선택**을 클릭하여 자격 증명 구성을 저장합니다.
8. **데이터 원본**을 클릭하고 클러스터에 대한 기본 저장소 계정으로 사용할 기존 저장소 계정을 선택하거나 새 저장소 계정을 만듭니다.
9. **리소스 그룹**을 클릭하여 기존 리소스 그룹을 선택하거나 **새로 만들기**를 클릭하여 새 리소스 그룹을 만듭니다.

10. **새 HDInsight 클러스터** 블레이드에서 **시작 보드에 고정**이 선택되어 있는지 확인한 다음 **만들기**를 클릭합니다.

## 설치된 HDInsight 앱 및 속성 나열

포털에서는 클러스터에 설치된 HDInsight 응용 프로그램의 목록과 설치된 응용 프로그램 각각의 속성을 보여 줍니다.

**HDInsight 응용 프로그램을 나열하고 속성을 표시하려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **HDInsight 클러스터**를 클릭합니다. 표시되지 않으면 **찾아보기**를 클릭한 다음 **HDInsight 클러스터**를 클릭합니다.
3. HDInsight 클러스터를 클릭합니다.
4. **설정** 블레이드에서 **일반** 범주에 있는 **응용 프로그램**을 클릭합니다. 설치된 앱 블레이드에서는 설치된 모든 응용 프로그램을 나열합니다.

	![HDInsight 응용 프로그램 설치된 앱](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)

5. 속성을 표시하려면 설치된 응용 프로그램 중 하나를 클릭합니다. 속성 블레이드에서는 다음을 나열합니다.

    - 앱 이름: 응용 프로그램 이름입니다.
    - 상태: 응용 프로그램 상태입니다.
    - 웹 페이지: 에지 노드에 배포한 웹 응용 프로그램이 있는 경우 해당하는 URL입니다. 자격 증명은 클러스터에 대해 구성한 HTTP 사용자 자격 증명과 동일합니다.
    - HTTP 끝점: 자격 증명은 클러스터에 대해 구성한 HTTP 사용자 자격 증명과 동일합니다.
    - SSH 끝점: [SSH](hdinsight-hadoop-linux-use-ssh-unix.md)를 사용하여 에지 노드에 연결할 수 있습니다. SSH 자격 증명은 클러스터에 대해 구성한 SSH 사용자 자격 증명과 동일합니다.

6. 응용 프로그램을 삭제하려면 응용 프로그램을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **삭제**를 클릭합니다.

## 에지 노드에 연결

HTTP 및 SSH를 사용하여 에지 노드에 연결할 수 있습니다. 끝점 정보는 [포털](#list-installed-hdinsight-apps-and-properties)에서 찾을 수 있습니다. SSH 사용에 대한 자세한 내용은 [Linux, Unix 및 OS X에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

HTTP 끝점 자격 증명은 HDInsight 클러스터에 대해 구성한 HTTP 사용자 자격 증명입니다. SSH 끝점 자격 증명은 HDInsight 클러스터에 대해 구성한 SSH 자격 증명입니다.

## 문제 해결

[설치 문제 해결](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation)을 참조하세요.

## 다음 단계

- [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md): HDInsight로 게시 취소된 HDInsight 응용 프로그램을 배포하는 방법을 알아봅니다.
- [HDInsight 응용 프로그램 게시](hdinsight-apps-publish-applications.md): 사용자 지정 HDInsight 응용 프로그램을 Azure 마켓플레이스에 게시하는 방법을 알아봅니다.
- [MSDN: HDInsight 응용 프로그램 설치](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight 응용 프로그램을 정의하는 방법을 알아봅니다.
- [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 작업을 사용하여 추가 응용 프로그램을 설치하는 방법을 알아봅니다.
- [Resource Manager 템플릿을 사용하여 HDInsight의 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Azure Resource Manager 템플릿을 호출하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.

<!---HONumber=AcomDC_0706_2016-->