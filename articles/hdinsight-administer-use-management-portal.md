<properties urlDisplayName="Administration" pageTitle="Azure 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리 | Azure" metaKeywords="" description="HDInsight 서비스를 관리하는 방법에 대해 알아봅니다. HDInsight 클러스터를 만들고 대화형 JavaScript 콘솔을 열고 Hadoop 명령 콘솔을 여는 방법에 대해 설명합니다." metaCanonical="" services="hdinsight" documentationCenter="" title="Azure 관리 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Azure 관리 포털을 사용하여 HDInsight의 Hadoop 클러스터 관리

Azure 관리 포털에서 HDInsight의 Hadoop 클러스터를 프로비전하고, Hadoop 사용자 암호를 변경하고, RDP를 사용하도록 설정하여 클러스터의 Hadoop 명령 콘솔에 액세스할 수 있습니다. 관리 포털뿐 아니라 HDInsight 관리에 사용할 수 있는 기타 도구도 있습니다.

-   Azure PowerShell을 사용하여 HDInsight를 관리하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 HDInsight 관리][PowerShell을 사용하여 HDInsight 관리]를 참조하세요.

-   크로스 플랫폼 명령줄 도구를 사용하여 HDInsight를 관리하는 방법에 대한 자세한 내용은 [크로스 플랫폼 명령줄 도구를 사용하여 HDInsight 관리][크로스 플랫폼 명령줄 도구를 사용하여 HDInsight 관리]를 참조하세요.

**필수 조건:**

이 문서를 시작하기 전에 다음이 있어야 합니다.

-   **Azure 구독**. Azure는 구독 기반 플랫폼입니다. 구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션][구매 옵션], [구성원 제공 항목][구성원 제공 항목] 또는 [무료 평가판][무료 평가판]을 참조하세요.

## 이 문서에서는 다음을 수행합니다.

-   [HDInsight 클러스터 프로비전][HDInsight 클러스터 프로비전]
-   [HDInsight 클러스터 사용자 지정][HDInsight 클러스터 사용자 지정]
-   [HDInsight 클러스터 사용자 이름 및 암호 변경][HDInsight 클러스터 사용자 이름 및 암호 변경]
-   [RDP를 사용하여 HDInsight 클러스터에 연결][RDP를 사용하여 HDInsight 클러스터에 연결]
-   [자체 서명된 인증서 만들기][자체 서명된 인증서 만들기]
-   [HTTP 서비스 액세스 권한 부여/해지][HTTP 서비스 액세스 권한 부여/해지]
-   [Hadoop 명령 콘솔 열기][Hadoop 명령 콘솔 열기]
-   [다음 단계][다음 단계]

## <span id="create"></span></a>HDInsight 클러스터 프로비전

HDInsight 클러스터를 만드는 여러 가지 방법이 있습니다. 이 문서에서는 Azure 관리 포털의 빠른 생성 옵션 사용에 대해서만 설명합니다. 다른 옵션은 [HDInsight 클러스터 프로비전][1]을 참조하세요.

HDInsight 클러스터는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. Azure Blob 저장소가 HDInsight 클러스터에서 매끄럽게 작동하는 방식에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][HDInsight에서 Azure Blob 저장소 사용]을 참조하세요.

HDInsight 클러스터를 프로비전할 데이터 센터에 Azure 저장소 계정을 만들어야 합니다. 현재 HDInsight 클러스터는 다음 5개 데이터 센터에 프로비전할 수 있습니다.

-   동남아시아
-   북유럽
-   서유럽
-   미국 동부
-   미국 서부

Azure 저장소 계정 만들기에 대한 자세한 내용은 [저장소 계정을 만드는 방법][저장소 계정을 만드는 방법]을 참조하세요.

**HDInsight 클러스터를 프로비전하려면**

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  페이지 맨 아래에서 **새로 만들기**를 클릭한 후 **데이터 서비스**, **HDInsight**, **빠른 생성**를 차례로 클릭합니다.

3.  **클러스터 이름**, **클러스터 크기**, **클러스터 관리자 암호** 및 Azure **저장소 계정**을 제공하고 **HDInsight 클러스터 만들기**를 클릭합니다. 클러스터를 만든 후 실행하면 상태가 *실행 중*으로 표시됩니다.

    ![HDI.QuickCreate][HDI.QuickCreate]

    빠른 생성 옵션을 사용하여 클러스터를 만드는 경우 Hadoop 사용자 계정의 기본 사용자 이름은 *admin*입니다. 계정에 다른 사용자 이름을 제공하려면 빠른 생성 옵션 대신 사용자 지정 만들기 옵션을 사용할 수 있습니다. 또는 프로비전된 후 계정 이름을 변경합니다.

    빠른 생성 옵션을 사용하여 클러스터를 만드는 경우 HDInsight 클러스터 이름을 가진 새 컨테이너가 지정한 저장소 계정에 자동으로 생성됩니다. 클러스터에서 기본적으로 사용되는 컨테이너 이름을 사용자 지정하려면 사용자 지정 만들기 옵션을 사용해야 합니다.

    > [WACOM.NOTE] HDInsight 클러스터에 대한 Azure 저장소 계정을 선택한 후에는 클러스터를 삭제하고 원하는 저장소 계정으로 새 클러스터를 만들어야 저장소 계정을 변경할 수 있습니다.

4.  새로 만든 클러스터를 클릭합니다. 방문 페이지가 표시됩니다.

    ![HDI.ClusterLanding][HDI.ClusterLanding]

## <span id="customize"></span></a>HDInsight 클러스터 사용자 지정

HDInsight는 다양한 Hadoop 구성 요소에서 작동합니다. 검증되어 지원되는 구성 요소 목록은 [Azure HDInsight에 포함된 Hadoop 버전][Azure HDInsight에 포함된 Hadoop 버전](영문)을 참조하세요. 다음 옵션 중 하나를 사용하여 HDInsight 사용자 지정을 수행할 수 있습니다.

-   클러스터 프로비전 중 HDInsight .NET SDK 또는 Azure PowerShell의 클러스터 사용자 지정 매개 변수를 사용합니다. 그러면 이러한 구성 변경 내용이 클러스터 수명 동안 유지되며 Azure 플랫폼이 유지 관리를 위해 정기적으로 수행하는 클러스터 노드 이미지로 다시 설치의 영향을 받지 않습니다. 클러스터 사용자 지정 매개 변수 사용에 대한 자세한 내용은 [HDInsight 클러스터 프로비전][1]을 참조하세요.
-   Mahout, Cascading 등의 일부 네이티브 Java 구성 요소는 클러스터에서 JAR 파일로 실행할 수 있습니다. 이러한 JAR 파일은 Azure Blob 저장소(WASB)에 배포되고 Hadoop 작업 제출 메커니즘을 통해 HDInsight 클러스터에 제출될 수 있습니다. 자세한 내용은 [프로그래밍 방식으로 Hadoop 작업 제출][프로그래밍 방식으로 Hadoop 작업 제출]을 참조하세요.

    > [WACOM.NOTE] HDInsight 클러스터에 jar 파일을 배포하거나 HDInsight 클러스터에서 jar 파일을 호출하는 데 문제가 있는 경우 [Microsoft 지원 센터][Microsoft 지원 센터]로 문의하세요.

    > Cascading은 HDInsight에서 지원되지 않으며 Microsoft 지원 대상이 아닙니다. 지원되는 구성 요소 목록은 [HDInsight에서 제공하는 클러스터 버전의 새로운 기능][Azure HDInsight에 포함된 Hadoop 버전]을 참조하세요.

원격 데스크톱 연결을 사용하여 클러스터에 사용자 지정 소프트웨어를 설치하는 기능은 지원되지 않습니다. 헤드 노드의 드라이브에는 파일을 저장하면 안 됩니다. 클러스터를 다시 만들어야 하는 경우 파일이 손실됩니다. Azure Blob 저장소에 파일을 저장하는 것이 좋습니다. Blob 저장소는 영구적입니다.

## <span id="password"></span></a>HDInsight 클러스터 사용자 이름 및 암호 변경

HDInsight 클러스터마다 두 개의 사용자 계정이 포함될 수 있습니다. HDInsight 클러스터 사용자 계정은 프로비전 프로세스 중에 생성됩니다. RDP를 통해 클러스터에 액세스하기 위해 RDP 사용자 계정을 만들 수도 있습니다. [원격 데스크톱 사용][원격 데스크톱 사용]을 참조하세요.

**HDInsight 클러스터 사용자 이름 및 암호를 변경하려면**

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 배포된 HDInsight 클러스터 목록이 표시됩니다.
3.  사용자 이름 및 암호를 다시 설정할 HDInsight 클러스터를 클릭합니다.
4.  페이지 맨 위에서 **구성**을 클릭합니다.
5.  **HADOOP 서비스** 옆의 **끄기**를 클릭합니다.
6.  페이지 맨 아래에서 **저장**을 클릭하고 비활성화가 완료될 때까지 기다립니다.
7.  서비스가 비활성화된 후 **HADOOP 서비스** 옆의 **켜기**를 클릭합니다.
8.  **사용자 이름** 및 **새 암호**를 입력합니다. 클러스터의 새 사용자 이름 및 암호가 됩니다.
9.  **저장**을 클릭합니다.

## <span id="rdp"></span></a>RDP를 사용하여 HDInsight 클러스터에 연결

생성 시 제공한 클러스터의 자격 증명은 원격 데스크톱을 통해 클러스터 자체가 아니라 클러스터의 서비스에 대한 액세스를 제공합니다. 원격 데스크톱 액세스는 기본적으로 꺼져 있으므로 원격 데스크톱을 통해 클러스터에 직접 액세스하려면 만든 후 추가 구성이 필요합니다.

**원격 데스크톱을 사용하도록 설정하려면**

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 배포된 HDInsight 클러스터 목록이 표시됩니다.
3.  연결할 HDInsight 클러스터를 클릭합니다.
4.  페이지 맨 위에서 **구성**을 클릭합니다.
5.  페이지 맨 아래에서 **원격 사용**을 클릭합니다.
6.  **원격 데스크톱 구성** 마법사에서 원격 데스크톱의 사용자 이름 및 암호를 입력합니다. 사용자 이름은 클러스터를 만드는 데 사용된 이름(빠른 생성 옵션을 사용한 경우 기본적으로 *admin*)과 달라야 합니다. **만료 날짜** 상자에 만료 날짜를 입력합니다. 만료 날짜는 지금부터 1주일 이내의 미래 날짜여야 합니다. 만료 시간은 기본적으로 지정한 날짜의 자정으로 가정됩니다. 확인 아이콘을 클릭합니다.

    ![HDI.CreateRDPUser][HDI.CreateRDPUser]

    만료 날짜는 지금부터 1주일 이내의 미래 날짜여야 합니다. 시간은 선택한 날짜의 자정입니다.

> [WACOM.NOTE] 클러스터에 RDP를 사용하도록 설정한 후 페이지를 새로 고쳐야 클러스터에 연결할 수 있습니다.

**RDP를 사용하여 클러스터에 연결하려면**

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 배포된 HDInsight 클러스터 목록이 표시됩니다.
3.  연결할 HDInsight 클러스터를 클릭합니다.
4.  페이지 맨 위에서 **구성**을 클릭합니다.
5.  **연결**을 클릭하고 지침을 따릅니다.

## <span id="cert"></span></a>자체 서명된 인증서 만들기

.NET SDK를 사용하여 클러스터에서 작업을 수행하려면 워크스테이션에서 자체 서명된 인증서를 만들어 Azure 구독에 업로드해야 합니다. 이 작업은 한 번만 수행하면 됩니다. 인증서가 유효하면 다른 컴퓨터에도 같은 인증서를 설치할 수 있습니다.

**자체 서명된 인증서를 만들려면**

1.  요청을 인증하는 데 사용되는 자체 서명된 인증서를 만듭니다. IIS 또는 [makecert][makecert]를 사용하여 인증서를 만듭니다.

2.  인증서 위치로 이동한 후 인증서를 마우스 오른쪽 단추로 클릭하고 **인증서 설치**를 클릭한 후 컴퓨터의 개인 저장소에 인증서를 설치합니다. 인증서 속성을 편집하여 이름을 지정합니다.

3.  인증서를 Azure 관리 포털로 가져옵니다. 포털에서 페이지 왼쪽 아래에 있는 **설정**을 클릭하고 **관리 인증서**를 클릭합니다. 페이지 아래쪽에서 **업로드**를 클릭하고 지침에 따라 이전 단계에서 만든 .cer 파일을 업로드합니다.

    ![HDI.ClusterCreate.UploadCert][HDI.ClusterCreate.UploadCert]

## <span id="httpservice"></span></a>HTTP 서비스 액세스 권한 부여/해지

HDInsight 클러스터에는 다음과 같은 HTTP 웹 서비스가 있습니다(이러한 모든 서비스에 RESTful 끝점이 있음).

-   ODBC
-   JDBC
-   Ambari
-   Oozie
-   Templeton

이러한 서비스에는 기본적으로 액세스 권한이 부여됩니다. 관리 포털에서 액세스 권한을 해지/부여할 수 있습니다.

> [WACOM.NOTE] 액세스 권한을 부여/해지하여 클러스터 사용자 이름 및 암호를 다시 설정합니다.

**HTTP 웹 서비스 액세스 권한을 부여/해지하려면**

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 배포된 HDInsight 클러스터 목록이 표시됩니다.
3.  구성할 HDInsight 클러스터를 클릭합니다.
4.  페이지 맨 위에서 **구성**을 클릭합니다.
5.  **HADOOP 서비스** 옆의 **켜기** 또는 **끄기**를 클릭합니다.
6.  **사용자 이름** 및 **새 암호**를 입력합니다. 클러스터의 새 사용자 이름 및 암호가 됩니다.
7.  **저장**을 클릭합니다.

Azure PowerShell cmdlet을 사용하여 이 작업을 수행할 수도 있습니다.

-   Grant-AzureHDInsightHttpServicesAccess
-   Revoke-AzureHDInsightHttpServicesAccess

[PowerShell을 사용하여 HDInsight 관리][PowerShell을 사용하여 HDInsight 관리]를 참조하세요.

## <span id="hadoopcmd"></span></a>Hadoop 명령줄 열기

원격 데스크톱을 통해 클러스터에 연결하고 Hadoop 명령줄을 사용하려면 이전 섹션에 설명된 대로 먼저 클러스터에 대한 원격 데스크톱 액세스를 사용하도록 설정해야 합니다.

**Hadoop 명령줄을 열려면**

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 배포된 Hadoop 클러스터 목록이 표시됩니다.
3.  연결할 HDInsight 클러스터를 클릭합니다.
4.  페이지 맨 위에서 **구성**을 클릭합니다.
5.  페이지 맨 아래에서 **연결**을 클릭합니다.
6.  **열기**를 클릭합니다.
7.  자격 증명을 입력한 다음 **확인**을 클릭합니다. 클러스터를 만들 때 구성한 사용자 이름 및 암호를 사용합니다.
8.  **예**를 클릭합니다.
9.  데스크톱에서 **Hadoop 명령줄**을 두 번 클릭합니다.

    ![HDI.HadoopCommandLine][HDI.HadoopCommandLine]

    Hadoop 명령에 대한 자세한 내용은 [Hadoop 명령 참조][Hadoop 명령 참조](영문)를 참조하세요.

이전 스크린샷의 폴더 이름에는 Hadoop 버전 번호가 포함되어 있습니다. 버전 번호는 클러스터에 설치된 Hadoop 구성 요소의 버전에 따라 변경될 수 있습니다. Hadoop 환경 변수를 사용하여 해당 폴더를 참조할 수 있습니다. 예를 들면 다음과 같습니다.

    cd %hadoop_home%
    cd %hive_home%
    cd %pig_home%
    cd %sqoop_home%   
    cd %hcatalog_home%

## <span id="nextsteps"></span></a>다음 단계

이 문서에서는 Azure 관리 포털을 사용하여 HDInsight 클러스터를 만드는 방법 및 Hadoop 명령줄 도구를 여는 방법을 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.

-   [PowerShell을 사용하여 HDInsight 관리][PowerShell을 사용하여 HDInsight 관리]
-   [크로스 플랫폼 명령줄 인터페이스를 사용하여 HDInsight 관리][크로스 플랫폼 명령줄 도구를 사용하여 HDInsight 관리]
-   [HDInsight 클러스터 프로비전][1]
-   [프로그래밍 방식으로 Hadoop 작업 제출][프로그래밍 방식으로 Hadoop 작업 제출]
-   [Azure HDInsight 시작][Azure HDInsight 시작]
-   [Azure HDInsight에 포함된 Hadoop 버전][Azure HDInsight에 포함된 Hadoop 버전]

  [PowerShell을 사용하여 HDInsight 관리]: ../hdinsight-administer-use-powershell/
  [크로스 플랫폼 명령줄 도구를 사용하여 HDInsight 관리]: ../hdinsight-administer-use-command-line/
  [구매 옵션]: http://azure.microsoft.com/ko-kr/pricing/purchase-options/
  [구성원 제공 항목]: http://azure.microsoft.com/ko-kr/pricing/member-offers/
  [무료 평가판]: http://azure.microsoft.com/ko-kr/pricing/free-trial/
  [HDInsight 클러스터 프로비전]: #create
  [HDInsight 클러스터 사용자 지정]: #customize
  [HDInsight 클러스터 사용자 이름 및 암호 변경]: #password
  [RDP를 사용하여 HDInsight 클러스터에 연결]: #rdp
  [자체 서명된 인증서 만들기]: #cert
  [HTTP 서비스 액세스 권한 부여/해지]: #httpservice
  [Hadoop 명령 콘솔 열기]: #hadoopcmd
  [다음 단계]: #nextsteps
  [1]: ../hdinsight-provision-clusters/
  [HDInsight에서 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage/
  [저장소 계정을 만드는 방법]: ../storage-create-storage-account/
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [HDI.QuickCreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
  [HDI.ClusterLanding]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "클러스터 방문 페이지"
  [Azure HDInsight에 포함된 Hadoop 버전]: ../hdinsight-component-versioning/
  [프로그래밍 방식으로 Hadoop 작업 제출]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Microsoft 지원 센터]: http://azure.microsoft.com/ko-kr/support/options/
  [원격 데스크톱 사용]: #enablerdp
  [HDI.CreateRDPUser]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
  [makecert]: http://msdn.microsoft.com/ko-kr/library/bfsktky3(v=vs.110).aspx
  [HDI.ClusterCreate.UploadCert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png
  [HDI.HadoopCommandLine]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop 명령줄"
  [Hadoop 명령 참조]: http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html
  [Azure HDInsight 시작]: ../hdinsight-get-started/
