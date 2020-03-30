---
title: Azure HDInsight에 타사 애플리케이션 설치
description: Azure HDInsight에서 타사 아파치 하두옵 응용 프로그램을 설치하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 22d27d50a7d2c304e5d1a04a9a5eaa03d2f0bfa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366345"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Azure HDInsight에 타사 Apache Hadoop 애플리케이션 설치

Azure HDInsight에 타사 [Apache Hadoop](https://hadoop.apache.org/) 애플리케이션을 설치하는 방법을 알아봅니다. 사용자 고유의 애플리케이션을 설치하는 방법에 대한 지침은 [사용자 지정 HDInsight 애플리케이션 설치](hdinsight-apps-install-custom-applications.md)를 참조하세요.

HDInsight 애플리케이션은 HDInsight 클러스터에 사용자가 설치할 수 있는 애플리케이션입니다. Microsoft, ISV(독립 소프트웨어 공급 업체) 또는 사용자가 직접 이러한 애플리케이션을 개발할 수 있습니다.  

다음 목록에서는 게시된 애플리케이션을 보여 줍니다.

|애플리케이션 |클러스터 유형 | 설명 |
|---|---|---|
|[AtScale 인텔리전스 플랫폼](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop은 |AtScale은 HDInsight 클러스터를 확장형 OLAP 서버로 전환하여 Microsoft Excel, Power BI, Tableau 소프트웨어에서 QlikView에 이르기까지 이미 알고 소유하고 사랑한 BI 도구를 사용하여 대화식으로 수십억 개의 데이터를 쿼리할 수 있습니다. |
|[HDInsight용 CDAP](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP는 Hadoop의 가치 창출 시간을 가속화하고 IT가 셀프 서비스 데이터를 제공할 수 있도록 지원하는 빅 데이터를 위한 최초의 통합 통합 플랫폼입니다. CDAP는 오픈 소스와 확장 가능한 혁신을 위한 장벽을 제거합니다. 요구 사항 : 4 리전 노드, 최소 D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop은 |분석을 위한 데이터를 준비, 탐색 및 관리하기 위한 Datameer의 셀프 서비스 확장 형 플랫폼은 복잡한 멀티소스 데이터를 가치 있는 비즈니스 지원 정보로 전환하여 엔터프라이즈 규모에서 더 빠르고 스마트한 통찰력을 제공합니다. |
|[HDInsight에 Dataiku DSS](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |데이터 과학자와 데이터 분석가가 협력하여 새로운 데이터 제품 및 서비스를 보다 효율적으로 설계 및 실행할 수 있는 엔터프라이즈 데이터 과학 플랫폼의 Dataiku DSS를 통해 원시 데이터를 영향력 있는 예측으로 전환할 수 있습니다. |
|[원디스코 퓨전 HDI 앱](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.fusion-hdi-app) |하두프, 스파크, H베이스, 스톰, 카프카 |분산 환경에서 데이터를 일관되게 유지하는 것은 방대한 데이터 운영 과제입니다. 엔터프라이즈급 소프트웨어 플랫폼인 WANdisco Fusion은 모든 환경에서 비정형 데이터 일관성을 지원하여 이 문제를 해결합니다. |
|[HDInsight를 위한 H2O 스파클링워터](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O 스파클링 워터는 GLM, Naïve Bayes, 분산 랜덤 포리스트, 그라데이션 부스팅 머신, 심층 신경망, 딥 러닝, K-means, PCA, 일반화 저랭크 모델, 변칙 탐지, 자동 엔코더와 같은 분산 알고리즘을 지원합니다. |
|[HDInsight에 대한 실시간 데이터 통합을 위한 Striim](https://azuremarketplace.microsoft.com/marketplace/apps/striim.hdinsightintegration) |하두프, H베이스, 스톰, 스파크, 카프카 |Striim("스트림"으로 발음)은 엔드투엔드 데이터 통합 + 인텔리전스 플랫폼으로, 서로 다른 데이터 스트림을 연속으로 수집, 처리, 분석할 수 있습니다. |
|[엔터프라이즈 가속화 빅데이터 분석](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |높은 수준에서 점분은 기업을 1로 지원합니다. 테즈 를 가속화, MapReduce& 스파크 엔진 기반 하이브, 자바, 스칼라 워크로드 성능. 2. 사전 하눕 클러스터 모니터링, 3. 분산 파일 시스템에서 데이터 품질 관리 설정 |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |하두롭, H베이스, 스파크 |아파치 카일린에 의해 구동, 카일리전스 기업은 빅 데이터에 BI를 가능하게. 하두프의 엔터프라이즈 OLAP 엔진인 Kyligence Enterprise는 비즈니스 분석가가 산업 표준 데이터 웨어하우스 및 BI 방법론을 통해 Hadoop에서 BI를 설계할 수 있도록 지원합니다. |
|[Azure HDInsight용 스타버스트 프레스토](https://azuremarketplace.microsoft.com/marketplace/apps/starburst.starburst-presto) |Hadoop은 |프레스토는 빠르고 확장 가능한 분산 SQL 쿼리 엔진입니다. 스토리지 및 컴퓨팅을 분리하기 위해 설계된 Presto는 Azure Data Lake Storage, Azure Blob Storage, SQL 및 NoSQL 데이터베이스와 기타 데이터 원본의 데이터를 쿼리하는 데 이상적입니다. |
|[HDInsight 클라우드를 위한 스트림세트 데이터 수집기](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |하두프, H베이스, 스파크, 카프카 |StreamSets 데이터 수집기는 실시간으로 데이터를 스트리밍하는 가볍고 강력한 엔진입니다. 데이터 수집기를 사용하여 데이터 스트림의 데이터를 라우팅하고 처리합니다. 그것은 함께 제공 30 일 평가판 라이센스. |
|[트리팩타 원격 조련 장비 엔터프라이즈](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.tr01) |하두롭, 스파크, HBase |HDInsight용 트라이팩타 랭글러 엔터프라이즈는 모든 데이터 규모에 대해 전사적 데이터 랭글링을 지원합니다. Azure에서 Trifacta를 실행하는 비용은 Trifacta 구독 비용과 가상 시스템에 대한 Azure 인프라 비용의 조합입니다. |
|[통합 데이터 플랫폼](https://unifisoftware.com/platform/) |하두프, H베이스, 스톰, 스파크 |Unifi 데이터 플랫폼은 비즈니스 사용자가 증분 수익, 비용 절감 또는 운영 복잡성을 유발하는 데이터 문제를 해결할 수 있도록 설계된 원활하게 통합된 셀프 서비스 데이터 도구 제품군입니다. |
|[해명 데이터 APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |HDInsight 스파크 클러스터에 대한 데이터 응용 프로그램을 해명. |
|[워터라인 AI 기반 데이터 카탈로그](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Waterline은 AI를 사용하여 비즈니스 용어로 데이터에 자동 태그를 적용하여 데이터를 카탈로그, 구성 및 관리합니다. Waterline의 비즈니스 리터럴 카탈로그는 셀프 서비스 분석, 규정 준수 및 거버넌스 및 IT 관리 이니셔티브를 위한 중요한 성공 요소입니다. |

이 문서에서 제공하는 지침은 Azure Portal을 사용합니다. 또한 포털에서 Azure Resource Manager 템플릿을 내보내거나 공급 업체에서 Resource Manager 템플릿의 복사본을 가져오고 Azure PowerShell 및 Azure 클래식 CLI를 사용하여 템플릿을 배포할 수 있습니다.  [Resource Manager 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
기존 HDInsight 클러스터에 HDInsight 애플리케이션을 설치하려면 HDInsight 클러스터가 있어야 합니다. HDInsight 클러스터를 만들려면 [클러스터 만들기](hadoop/apache-hadoop-linux-tutorial-get-started.md)를 참조하세요. HDInsight 클러스터를 만들 경우 HDInsight 애플리케이션도 설치할 수 있습니다.

## <a name="install-applications-to-existing-clusters"></a>기존 클러스터에 애플리케이션 설치
다음 절차에서는 기존 HDInsight 클러스터에 HDInsight 애플리케이션을 설치하는 방법을 보여 줍니다.

**HDInsight 애플리케이션 설치**

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 왼쪽 메뉴에서 모든 **서비스** > **분석** > **HDInsight 클러스터로 이동합니다.**
3. 목록에서 HDInsight 클러스터를 선택합니다.  HDInsight 클러스터가 없는 경우 만듭니다.  see [클러스터 만들기](hadoop/apache-hadoop-linux-tutorial-get-started.md)를 참조하세요.
4. **설정** 범주에서 **응용 프로그램을 선택합니다.** 메인 창에서 설치된 응용 프로그램 목록을 볼 수 있습니다. 
   
    ![HDInsight 애플리케이션 포털 메뉴](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. 메뉴에서 **+Add를** 선택합니다. 사용 가능한 응용 프로그램 목록을 볼 수 있습니다.  **+Add가** 회색으로 표시됩니다.
   
    ![HDInsight 애플리케이션 사용 가능한 애플리케이션](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. 사용 가능한 응용 프로그램 중 하나를 선택한 다음 지침에 따라 법적 약관에 동의합니다.

포털 알림에서 설치 상태를 볼 수 있습니다(포털 상단의 벨 아이콘 선택). 애플리케이션이 설치되면 설치된 앱 목록에 표시됩니다.

## <a name="install-applications-during-cluster-creation"></a>클러스터 생성 중에 애플리케이션 설치

클러스터를 만들 때 HDInsight 애플리케이션을 설치하는 옵션이 있습니다. 클러스터가 만들어지고 실행 상태가 되면 프로세스 중에 HDInsight 애플리케이션이 설치됩니다. Azure 포털을 사용하여 클러스터를 만드는 동안 **구성 + 가격 책정** 탭에서 응용 프로그램을 설치하려면 **+ 응용 프로그램 추가**를 선택합니다.

![Azure 포털 클러스터 구성 응용 프로그램](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>설치된 HDInsight 앱 및 속성 나열
포털에서는 클러스터에 설치된 HDInsight 애플리케이션의 목록과 설치된 애플리케이션 각각의 속성을 보여 줍니다.

**HDInsight 애플리케이션 나열 및 속성 표시**

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 왼쪽 메뉴에서 모든 **서비스** > **분석** > **HDInsight 클러스터로 이동합니다.**
3. 목록에서 HDInsight 클러스터를 선택합니다.
4. **설정** 범주에서 **응용 프로그램을 선택합니다.** 메인 창에서 설치된 응용 프로그램 목록을 볼 수 있습니다. 
   
    ![HDInsight 애플리케이션 설치 앱](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. 설치된 응용 프로그램 중 하나를 선택하여 속성을 표시합니다. 속성 목록:

    |속성 | 설명 |
    |---|---|
    |앱 이름 |애플리케이션 이름. |
    |상태 |응용 프로그램 상태입니다. |
    |웹 페이지 |에지 노드에 배포한 웹 애플리케이션의 URL입니다. 자격 증명은 클러스터에 대해 구성한 HTTP 사용자 자격 증명과 동일합니다. |
    |SSH 끝점 |SSH를 사용하여 에지 노드에 연결할 수 있습니다. SSH 자격 증명은 클러스터에 대해 구성한 SSH 사용자 자격 증명과 동일합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요. |
    |설명 | 응용 프로그램 설명. |

6. 애플리케이션을 삭제하려면 애플리케이션을 마우스 오른쪽 단추로 클릭한 다음, 상황에 맞는 메뉴에서 **삭제**를 클릭합니다.

## <a name="connect-to-the-edge-node"></a>에지 노드에 연결
HTTP 및 SSH를 사용하여 에지 노드에 연결할 수 있습니다. 엔드포인트 정보는 [포털](#list-installed-hdinsight-apps-and-properties)에서 찾을 수 있습니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

HTTP 엔드포인트 자격 증명은 HDInsight 클러스터에 대해 구성한 HTTP 사용자 자격 증명입니다. SSH 엔드포인트 자격 증명은 HDInsight 클러스터에 대해 구성한 SSH 자격 증명입니다.

## <a name="troubleshoot"></a>문제 해결
[설치 문제 해결](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [사용자 지정 HDInsight 애플리케이션 설치](hdinsight-apps-install-custom-applications.md): HDInsight로 게시 취소된 HDInsight 애플리케이션을 배포하는 방법을 알아봅니다.
* [HDInsight 애플리케이션 게시](hdinsight-apps-publish-applications.md): 사용자 지정 HDInsight 애플리케이션을 Azure Marketplace에 게시하는 방법을 알아봅니다.
* [MSDN: HDInsight 애플리케이션 설치](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight 애플리케이션을 정의하는 방법을 알아봅니다.
* [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 작업을 사용하여 추가 애플리케이션을 설치하는 방법을 알아봅니다.
* [Resource Manager 템플릿을 사용하여 HDInsight에서 Linux 기반 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Azure Resource Manager 템플릿을 호출하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.
* [HDInsight에서 비어 있는 에지 노드 사용](hdinsight-apps-use-edge-node.md): HDInsight 클러스터에 액세스, HDInsight 애플리케이션 테스트 및 HDInsight 애플리케이션 호스팅하는 데 비어 있는 에지 노드를 사용하는 방법을 알아봅니다.

