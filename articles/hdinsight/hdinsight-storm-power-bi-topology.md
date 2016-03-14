<properties
 pageTitle="Apache Storm에서 Power BI로 데이터 기록 | Microsoft Azure"
 description="HDInsight 내 Apache Storm 클러스터에서 실행되는 C# 토폴로지로부터 Power BI로 데이터를 기록합니다. 또한, Power BI를 사용하여 보고서 및 실시간 대시보드를 만듭니다."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="03/01/2016"
 ms.author="larryfr"/>

# Power BI를 사용하여 Apache Storm 토폴로지에서 데이터 시각화

Power BI를 사용하여 데이터를 보고서 또는 대시보드로 시각적으로 표시할 수 있습니다. Power BI REST API를 사용하면 HDInsight 클러스터의 Apache Storm에서 실행되는 토폴로지의 데이터를 Power BI에서 손쉽게 사용할 수 있습니다.

이 문서에는 Power BI를 사용하여 Storm 토폴로지에서 생성된 데이터로 보고서 및 대시보드를 만드는 방법에 대해 알아봅니다.

## 필수 조건

- Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* [Power BI](https://powerbi.com) 액세스 권한이 있는 Azure Active Directory 사용자

* Visual Studio(다음 버전 중 하나)

    * Visual Studio 2012 [업데이트 4](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 [업데이트 4](http://www.microsoft.com/download/details.aspx?id=44921) 또는 [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * Visual Studio 2015 [CTP6](http://visualstudio.com/downloads/visual-studio-2015-ctp-vs)

* HDInsight Tools for Visual Studio: 설치 정보는 [HDInsight Tools for Visual Studio 사용 시작](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

## 작동 방법

이 예제에는 임의로 문장을 생성하고, 문장을 단어로 분할하고, 단어 수를 계산하고, 단어 및 개수를 Power BI REST API로 보내는 C# Storm 토폴로지가 포함되어 있습니다. [PowerBi.Api.Client](https://github.com/Vtek/PowerBI.Api.Client) Nuget 패키지는 Power BI와 통신하는 데 사용됩니다.

이 프로젝트의 다음 파일은 Power BI 관련 기능을 구현합니다.

* **PowerBiBolt.cs**: 데이터를 Power BI로 보내는 Storm bolt를 구현합니다.

* **Data.cs**: Power BI로 보낼 데이터 개체/행을 설명합니다.

> [AZURE.WARNING] Power BI는 이름이 같은 여러 데이터 집합의 만들기를 허용하는 것처럼 보입니다. 이는 데이터 집합이 존재하지 않고 토폴로지에서 Power BI Bolt의 여러 인스턴스를 만든 경우에 발생할 수 있습니다. 이를 방지하려면 bolt의 병렬 처리 힌트를 1로 설정(이 예제와 같이)하거나 토폴로지를 배포하기 전에 데이터 집합을 만듭니다.
>
> 이 솔루션에 포함된 **CreateDataset** 콘솔 응용 프로그램은 토폴로지 외부에서 데이터 집합을 만드는 방법의 예로 제공됩니다.

## Power BI 응용 프로그램 등록

1. [Power BI 빠른 시작](https://msdn.microsoft.com/library/dn931989.aspx) 단계에 따라 Power BI에 등록합니다.

2. [앱 등록](https://msdn.microsoft.com/library/dn877542.aspx) 단계에 따라 응용 프로그램 등록을 만듭니다. 이는 Power BI REST API에 액세스할 때 사용됩니다.

    > [AZURE.IMPORTANT] 응용 프로그램 등록을 위한 **클라이언트 ID**를 저장합니다.

## 예제 다운로드

[HDInsight C# Storm Power BI 예제](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi)를 다운로드합니다. 다운로드하려면 [git](http://git-scm.com/)를 사용하여 포크/복제하거나, **다운로드** 링크를 사용하여 .zip 보관 파일을 다운로드합니다.

## 샘플 구성

1. Visual Studio에서 샘플을 엽니다. **솔루션 탐색기**에서 **App.config** 파일을 열고 **<OAuth .../>** 요소를 찾습니다. 이 요소의 다음 속성 값을 입력합니다.

    * **Client**: 이전에 만든 응용 프로그램 등록을 위한 클라이언트 ID입니다.

    * **User**: Power BI에 액세스할 수 있는 Azure Active Directory 계정입니다.

    * **Password**: Azure Active Directory 계정의 암호입니다.

2. (선택 사항). 이 프로젝트에서 사용되는 기본 데이터 집합 이름은 **Words**입니다. 이를 변경하려면 **솔루션 탐색기**에서 **WordCount** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**, **설정**을 차례로 선택합니다. **DatasetName** 항목을 원하는 값으로 변경합니다.

2. 파일을 저장하고 닫습니다.

## 샘플 배포

1. **솔루션 탐색기**에서 **WordCount** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출**을 선택합니다. **Storm 클러스터** 드롭다운 대화 상자에서 HDInsight 클러스터를 선택합니다.

    > [AZURE.NOTE] **Storm 클러스터** 드롭다운을 서버 이름으로 채우는 데 몇 초 정도 걸릴 수 있습니다.
    >
    > 메시지가 표시되면 Azure 구독에 대한 로그인 자격 증명을 입력합니다. 하나 이상의 구독이 있는 경우 HDInsight 클러스터의 Storm을 포함하는 자격 증명으로 로그인합니다.

2. 토폴로지 제출에 성공하면 클러스터에 대한 Storm 토폴로지가 나타납니다. 실행 중인 토폴로지에 대한 정보를 보려면 목록에서 WordCount 토폴로지를 선택합니다.

    ![WordCount 토폴로지가 선택된 토폴로지](./media/hdinsight-storm-power-bi-topology/topologysummary.png)

    > [AZURE.NOTE] Azure, HDInsight를 확장한 다음 HDInsight 클러스터의 Storm을 마우스 오른쪽 단추로 클릭하고 Storm 토폴로지 보기를 선택하여 서버 탐색기에서 Storm 토폴로지를 볼 수도 있습니다.

3. **토폴로지 요약**을 볼 때 **Bolts** 섹션이 나타날 때까지 스크롤합니다. 이 섹션에서 **PowerBI** bolt에 대한 **Executed** 열을 확인합니다. 페이지 위쪽의 새로 고침 단추를 사용하여 값이 0이 아닌 값으로 변경될 때까지 새로 고칩니다. 이 숫자가 증가하기 시작하면 항목이 Power BI에 기록되고 있는 것입니다.

## 보고서 만들기

1. 브라우저에서 [https://PowerBI.com](https://powerbi.com)을 방문합니다. 사용자 계정으로 로그인합니다.

2. 페이지의 왼쪽에서 **Datasets**를 확장합니다. **Words** 항목을 선택합니다. 이는 예제 토폴로지에서 생성된 데이터 집합입니다.

    ![Words 데이터 집합 항목](./media/hdinsight-storm-power-bi-topology/words.png)

3. **Fields** 영역에서 **WordCount**를 확장합니다. **Count** 및 **Word** 항목을 페이지의 가운데 부분으로 끌어옵니다. 그러면 각 단어에 대해 해당 단어가 발생한 횟수를 나타내는 막대가 표시된 새 차트가 만들어집니다.

    ![WordCount 차트](./media/hdinsight-storm-power-bi-topology/wordcountchart.png)

4. 페이지의 왼쪽 위에서 **Save**를 선택하여 새 보고서를 만듭니다. **Word Count**를 보고서 이름으로 입력합니다.

5. Power BI 로고를 선택하여 대시보드 돌아갑니다. 이제 **Word Count** 보고서가 **Reports** 아래에 표시됩니다.

## 라이브 대시보드 만들기

1. **Dashboard** 옆에서 **+** 아이콘을 선택하여 새 대시보드를 만듭니다. 새 대시보드의 이름을 **Live Word Count**로 지정합니다.

2. 이전에 만든 **Word Count** 보고서를 선택합니다. 보고서가 표시되면 차트를 선택한 후 차트의 오른쪽 위에 있는 압정 아이콘을 선택합니다. 대시보드에 고정되었음을 나타내는 알림이 제공됩니다.

    ![압정이 표시된 차트](./media/hdinsight-storm-power-bi-topology/pushpin.png)

2. Power BI 로고를 선택하여 대시보드 돌아갑니다. **Live Word Count** 대시보드를 선택합니다. 이제 Word Count 차트가 포함되어 있으며, HDInsight에서 실행되는 WordCount 토폴로지에서 Power BI로 새 항목이 전송되면 차트가 업데이트됩니다.

    ![라이브 대시보드](./media/hdinsight-storm-power-bi-topology/dashboard.png)

## WordCount 토폴로지 중지

토폴로지는 사용자가 중지하거나 HDInsight 클러스터에서 Storm을 삭제할 때까지 계속 실행됩니다. 토폴로지를 중지하려면 다음 단계를 수행합니다.

1. Visual Studio에서 WordCount 토폴로지에 대한 **토폴로지 요약** 창을 엽니다. 토폴로지 요약이 이미 열려 있지 않은 경우 **서버 탐색기**로 이동하여 **Azure** 및 **HDInsight** 항목을 확장하고 HDInsight 클러스터의 Storm을 마우스 오른쪽 단추로 클릭한 후 **Storm 토폴로지 보기**를 선택합니다. 마지막으로 **WordCount** 토폴로지를 선택합니다.

2. **Kill** 단추를 선택하여 **WordCount** 토폴로지를 중지합니다.

    ![토폴로지 요약의 Kill 단추](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## 다음 단계

이 문서에서는 REST를 사용하여 Storm 토폴로지에서 Power BI로 데이터를 보내는 방법을 알아보았습니다. 다른 Azure 기술을 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0302_2016-->