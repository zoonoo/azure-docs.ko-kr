<properties linkid="manage-scenarios-how-to-scale-websites" urlDisplayName="How to scale" pageTitle="How to scale web sites - Microsoft Azure service management" metaKeywords="Azure scaling web sites" description="Learn how to scale web sites in Azure to use Free, Shared, Basic and Standard web hosting plans." metaCanonical="" services="web-sites" documentationCenter="" title="How to Scale Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

웹 사이트 크기를 조정하는 방법
==============================

Microsoft Azure에서 웹 사이트의 성능 및 처리량 증가를 위해 Azure 관리 포털을 사용하여 웹 호스팅 계획 모드를 무료에서 공유, 기본 또는 표준으로 변경할 수 있습니다.

Azure 웹 사이트 크기를 확장하려면 두 가지 관련 작업을 수행해야 합니다. 먼저 웹 호스팅 계획 모드를 더 높은 수준의 서비스로 변경하고, 더 높은 수준의 서비스로 전환한 후에는 특정 설정을 구성합니다. 이 문서에서는 두 가지 작업을 모두 다룹니다. 표준 모드와 같이 더 높은 서비스 계층은 Azure의 리소스 사용 방법을 결정하는 데 보다 뛰어난 유연성과 견고성을 제공합니다.

관리 포털의 크기 조정 탭에서 모드를 쉽게 변경하고 구성할 수 있습니다. 필요에 따라 크기를 확장하거나 축소할 수 있습니다. 이러한 변경은 적용하는 데 몇 초밖에 걸리지 않으며 웹 호스팅 계획의 모든 웹 사이트에 영향을 줍니다. 또한 변경을 적용하기 위해 코드를 변경하거나 응용 프로그램을 다시 배포할 필요도 없습니다.

웹 호스팅 계획에 대한 자세한 내용은 [Azure 웹 사이트 웹 호스팅 계획](http://go.microsoft.com/fwlink/?LinkId=9845584)을 참조하십시오. 개별 웹 호스팅 계획의 가격 및 기능에 대한 자세한 내용은 [웹 사이트 가격 정보](http://www.windowsazure.com/en-us/pricing/details/web-sites/)를 참조하십시오.

> [WACOM.NOTE] 웹 사이트를 **무료** 웹 호스팅 계획 모드에서 **기본** 또는 **표준** 웹 호스팅 계획 모드로 전환하기 전에 웹 사이트 구독에 대한 지출 한도를 제거해야 합니다. Microsoft Azure 웹 사이트 구독에 대한 옵션을 보거나 변경하려면 [Microsoft Azure 구독](http://go.microsoft.com/fwlink/?LinkID=235288)을 참조하십시오.

이 문서에서는 다음을 수행합니다.

-   [공유 또는 기본 계획 모드로 크기 조정](#scalingsharedorbasic)
-   [표준 계획 모드로 크기 조정](#scalingstandard)
-   [사이트에 연결된 SQL Server 데이터베이스 크기 조정](#ScalingSQLServer)
-   [개발자 기능](#devfeatures)
-   [기타 기능](#OtherFeatures)

## 공유 또는 기본 계획 모드로 크기 조정

1.  브라우저에서 [관리 포털](https://manage.windowsazure.com/)을 엽니다.

2.  **웹 사이트** 탭에서 웹 사이트를 선택합니다.

    ![웹 사이트 선택](./media/web-sites-scale/01SelectWebSite.png)

3.  **크기 조정** 탭을 클릭합니다.

    ![크기 조정 탭](./media/web-sites-scale/02SelectScaleTab.png)

4.  **웹 호스팅 계획 모드** 섹션에서 **공유** 또는 **기본**을 선택합니다. 이미지의 예에서는 기본을 선택합니다.

    ![웹 호스팅 계획 선택](./media/web-sites-scale/03aChooseWHP.png)

    **웹 호스팅 계획 사이트** 섹션에서는 현재 계획에 대한 짧은 사이트 목록을 보여 줍니다. 현재 계획의 모든 사이트가 선택한 웹 호스팅 계획 모드로 변경됩니다.

5.  **용량** 섹션에서 **인스턴스 크기**를 선택합니다. **작음**, **중간** 또는 **큼** 옵션을 사용할 수 있습니다. 공유 모드에서는 인스턴스 크기 옵션을 사용할 수 없습니다. 인스턴스 크기에 대한 자세한 내용은 [Microsoft Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](http://go.microsoft.com/fwlink/?LinkId=309169)를 참조하십시오.

    ![기본 모드의 인스턴스 크기](./media/web-sites-scale/03bChooseBasicInstanceSize.png)

6.  슬라이더를 사용하여 원하는 **인스턴스 수**를 선택합니다.

    ![기본 모드의 인스턴스 수](./media/web-sites-scale/04ChooseBasicInstanceCount.png)

7.  명령 모음에서 **저장**을 선택합니다.

    ![저장 단추](./media/web-sites-scale/05SaveButton.png)

    > [WACOM.NOTE] 원하는 경우 **웹 호스팅 계획**, **인스턴스 크기** 및 **인스턴스 수** 설정을 개별적으로 구성하고 저장할 수 있습니다.

8.  확인 메시지가 표시되어 현재 웹 사이트와 동일한 웹 호스팅 계획의 사이트도 새 모드로 변경된다는 사실을 알려 줍니다. **예**를 클릭하여 변경을 완료합니다.

    이 예에서는 계획 모드가 **기본**으로 변경되었습니다.

    ![계획 변경 완료](./media/web-sites-scale/06BasicComplete.png)

## 표준 계획 모드로 크기 조정

> [WACOM.NOTE] 웹 호스팅 계획을 표준 모드로 전환하기 전에 Microsoft Azure 웹 사이트 구독에 대한 지출 한도를 제거해야 합니다. 그렇지 않으면 대금 청구 기간이 끝나기 전에 한도에 도달할 경우 사이트를 사용할 수 없게 됩니다. Microsoft Azure 웹 사이트 구독에 대한 옵션을 보거나 변경하려면 [Microsoft Azure 구독](http://go.microsoft.com/fwlink/?LinkID=235288)을 참조하십시오.

1.  표준으로 크기를 조정하려면 공유 또는 기본으로 크기를 조정할 때와 동일한 초기 단계를 따른 후 **웹 호스팅 계획 모드**에서 **표준**을 선택합니다.

    ![표준 계획 선택](./media/web-sites-scale/07ChooseStandard.png)

    이전 단계와 마찬가지로 **웹 호스팅 계획 사이트** 섹션에서는 현재 계획에 대한 짧은 사이트 목록을 보여 줍니다. 이 경우 현재 계획의 모든 사이트가 표준 모드로 변경됩니다.

2.  **표준**을 선택하면 **용량** 섹션이 확장되어 **인스턴스 크기** 및 **인스턴스 수** 옵션이 표시됩니다. 이 옵션은 기본 모드에서도 사용할 수 있습니다. **예약할 크기 조정 설정 편집** 및 **메트릭별 크기 조정** 옵션은 표준 모드에서만 사용할 수 있습니다.

    ![표준의 용량 섹션](./media/web-sites-scale/08CapacitySectionStandard.png)

3.  **인스턴스 크기**를 구성합니다. **작음**, **중간** 또는 **큼** 옵션을 사용할 수 있습니다.

    ![인스턴스 크기 선택](./media/web-sites-scale/09ChooseInstanceSize.png)

    인스턴스 크기에 대한 자세한 내용은 [Microsoft Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](http://go.microsoft.com/fwlink/?LinkId=309169)를 참조하십시오.

4.  주간 또는 야간, 주중 또는 주말 및/또는 지정된 날짜 및 시간에 따라 리소스 크기를 자동으로 조정(자동 크기 조정)하려는 경우 **예약할 크기 조정 설정 편집** 옵션에서 **예약 시간 설정**을 선택합니다.

    ![예약 시간 설정](./media/web-sites-scale/10SetUpScheduleTimesButton.png)

5.  **예약 시간 설정** 대화 상자는 몇 가지 유용한 구성 선택 항목을 제공합니다.

    ![SetUpScheduleTimesDialog](./media/web-sites-scale/11SetUpScheduleTimesDialog.png)

6.  **되풀이 예약**에서 **주간과 야간에 대해 다른 크기 조정 설정** 및/또는 **평일과 주말에 대해 다른 크기 조정 설정**을 선택하여 개별 주간 및 야간 예약 및/또는 개별 주중 및 주말 예약에 따라 리소스 크기를 조정합니다.

    > [WACOM.NOTE] 이 기능에서, 주말은 금요일의 일 종료 시간(기본적으로 오후 8시)에 시작되고 월요일의 일 시작 시간(기본적으로 오전 8시)에 종료됩니다. 주말 프로필에서는 **시간** 설정에서 정의하는 동일 날짜의 시작 및 종료를 사용합니다.

7.  **시간**에서, 30분 증분 단위로 일 시작 및 일 종료 시간과 표준 시간대를 선택합니다. 기본적으로 일 시작은 오전 8시에 시작하고 오후 8시에 종료됩니다. 일광 절약 시간제는 선택된 표준 시간대에 따라 적용됩니다.

8.  **특정 날짜**에서, 리소스 크기를 조정할 명명된 시간 프레임을 하나 이상 만들 수 있습니다. 예를 들어 웹 트래픽의 양이 많은 시간 동안 판매 또는 출시 이벤트를 위해 추가 리소스를 제공할 수 있습니다.

9.  선택한 후에는 **확인**을 클릭하여 **예약 시간** 대화 상자를 닫습니다.

10. **예약할 크기 조정 설정 편집** 상자에서 이제 변경된 내용에 따라 구성 가능한 예약 또는 이벤트를 표시합니다. 되풀이 예약 중 하나 또는 특정 날짜를 선택하여 구성합니다.
	
	![Edit scale settings for schedule](./media/web-sites-scale/12EditScaleSettingsForSchedule.png) 
	
	You can now use the **Scale by Metric** and the **Instance Count** features to fine tune the scaling of resources for each schedule that you choose. 
	
1.  로드가 변경되는 경우 웹 사이트에서 사용하는 인스턴스 수를 동적으로 조정하려면 **CPU**를 선택하여 **메트릭별 크기 조정**을 사용하도록 설정합니다.
	
	![Scale By Metric](./media/web-sites-scale/13ScaleByMetric.png)
	
	The graph shows the number of instances that have been used over the past week. You can use the graph to monitor scaling activity.
	
1.  **메트릭별 크기 조정**은 자동 크기 조정에 사용할 가상 컴퓨터의 최소 및 최대 수를 설정할 수 있도록 **인스턴스 수** 기능을 수정합니다. Azure의 리소스는 설정된 제한을 넘거나 제한보다 낮아지지 않습니다.

    ![인스턴스 수](./media/web-sites-scale/14InstanceCount.png)

2.  또한 **메트릭별 크기 조정**은 CPU 사용량의 대상 범위를 지정할 수 있도록 **대상 CPU** 옵션을 사용하도록 설정합니다. 이 범위는 웹 사이트의 평균 CPU 사용량을 나타냅니다. Azure는 웹 사이트를 이 범위로 유지하도록 표준 인스턴스를 추가하거나 제거합니다.

    ![대상 CPU](./media/web-sites-scale/15TargetCPU.png)

    **참고**: **메트릭별 크기 조정**을 사용하면 Microsoft Azure에서 5분마다 한 번씩 웹 사이트의 CPU를 확인하고 해당 시점에 필요한 만큼 인스턴스를 추가합니다. CPU 사용량이 적으면 Microsoft Azure에서 두 시간마다 한 번씩 인스턴스를 제거하여 웹 사이트의 성능을 유지합니다. 일반적으로 최소 인스턴스 수를 1로 설정하는 것이 적절합니다. 그러나 웹 사이트에서 갑자기 사용량이 많아지는 경우 로드를 처리하기에 충분한 정도로 최소 인스턴스 수를 유지해야 합니다. 예를 들어 Microsoft Azure에서 CPU 사용량을 확인하기 전 5분 동안 트래픽 양이 갑자기 높아지는 경우 해당 시간 동안 사이트가 응답하지 않을 수 있습니다. 트래픽 양이 갑작스럽게 많아질 것으로 예상되는 경우 이러한 버스트를 지원하도록 최소 인스턴스 수를 더 높게 설정하십시오.

3.  **예약할 크기 조정 설정 편집** 목록에서 항목을 변경한 후에는 페이지 맨 아래에 있는 명령 모음에서 **저장** 아이콘을 클릭하여 예약된 모든 설정을 한 번에 저장합니다(각 예약을 개별적으로 저장할 필요가 없음).

## 사이트에 연결된 SQL Server 데이터베이스 크기 조정 
웹 사이트에 연결된 SQL Server 데이터베이스가 하나 이상인 경우(웹 호스팅 계획 모드와 상관없이) 크기 조정 페이지의 아래에 있는 **연결된 리소스**에 나열됩니다.

1.  데이터베이스 중 하나를 크기 조정하려면 **연결된 리소스** 섹션에서 데이터베이스 이름 옆에 있는 **이 데이터베이스에 대한 크기 조정 관리** 링크를 클릭합니다.

    ![연결된 데이터베이스](./media/web-sites-scale/16LinkedResources.png)

2.  이 링크를 클릭하면 데이터베이스의 **버전** 및 **최대 크기**를 구성할 수 있는 Azure 관리 포털의 SQL Server 탭으로 이동됩니다.

    ![SQL Server 데이터베이스 크기 조정](./media/web-sites-scale/17ScaleDatabase.png)

    **버전**으로, 필요한 저장소 용량에 따라 **Web** 또는 **Business**를 선택합니다. **Web** 버전은 적은 용량 범위를 제공하는 반면 **Business**는 더 넓은 범위의 용량을 제공합니다.

    **최대 크기**에서 선택하는 값은 데이터베이스의 상한을 지정합니다. 데이터베이스 요금은 실제로 저장하는 데이터의 양에 따라 달라지므로, **최대 크기** 속성을 변경해도 데이터베이스 요금에 자동으로 영향을 주지는 않습니다. 자세한 내용은 [Azure SQL 데이터베이스에서 계정 및 요금 청구](http://go.microsoft.com/fwlink/?LinkId=234930)를 참조하십시오.

## 개발자 기능 
웹 호스팅 계획 모드에 따라 다음과 같은 개발자 지향 기능을 사용할 수 있습니다.

**비트 수**

-   기본 및 표준 계획 모드는 64비트 및 32비트 응용 프로그램을 지원합니다.
-   무료 및 표준 모드는 32비트 응용 프로그램만 지원합니다.

**디버거 지원**

-   무료, 공유 및 기본 웹 호스팅 계획 모드의 디버거 지원은 응용 프로그램당 1개의 동시 연결로 사용할 수 있습니다.
-   표준 모드의 디버거 지원은 응용 프로그램당 5개의 동시 연결로 사용할 수 있습니다.

## 기타 기능

**웹 끝점 모니터링**

-   웹 끝점 모니터링은 기본 및 표준 웹 호스팅 계획 모드에서 사용할 수 있습니다. 웹 끝점 모니터링에 대한 자세한 내용은 [웹 사이트를 모니터링하는 방법](http://www.windowsazure.com/en-us/documentation/articles/web-sites-monitor/)을 참조하십시오.

-   개발자를 비롯하여 모든 사용자에게 유용한 가격 및 기능을 포함하여 웹 호스팅 계획의 나머지 모든 기능에 대한 자세한 내용은 [웹 사이트 가격 정보](http://www.windowsazure.com/en-us/pricing/details/web-sites/)를 참조하십시오.

## 다음 단계 
- 확장 가능하며 복원력이 뛰어난 아키텍처 빌드를 포함하여 Azure 웹 사이트 모범 사례에 대해서는 [모범 사례: Azure 웹 사이트(WAWS)](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)(영문)를 참조하십시오.

-   가격, 지원 및 SLA에 대한 자세한 내용은 다음 링크를 방문하십시오.

    [데이터 전송 가격 정보](http://www.windowsazure.com/en-us/pricing/details/data-transfers/)

    [Microsoft Azure 지원 계획](http://www.windowsazure.com/en-us/support/plans/)

    [서비스 수준 계약](http://www.windowsazure.com/en-us/support/legal/sla/)

    [SQL 데이터베이스 가격 정보](http://www.windowsazure.com/en-us/pricing/details/sql-database/)

    [Microsoft Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](http://go.microsoft.com/fwlink/?LinkId=309169)

    [웹 사이트 가격 정보](http://www.windowsazure.com/en-us/pricing/details/web-sites/)

    [웹 사이트 가격 정보 - SSL 연결](http://www.windowsazure.com/en-us/pricing/details/web-sites/#ssl-connections)

-   다음 링크는 Azure 웹 사이트 크기 조정에 대한 비디오입니다.

    [Azure 웹 사이트를 크기 조정하는 방법 - 스테판 스차코우(Stefan Schackow)(영문)](http://www.windowsazure.com/en-us/documentation/videos/azure-web-sites-free-vs-standard-scaling/)

    [Azure 웹 사이트, CPU 또는 예약 리소스 크기 자동 조정 - 스테판 스차코우(Stefan Schackow)(영문)](http://www.windowsazure.com/en-us/documentation/videos/auto-scaling-azure-web-sites/)

    [Azure 웹 사이트 크기를 조정하는 방법 - 스테판 스차코우(Stefan Schackow)(영문)](http://www.windowsazure.com/en-us/documentation/videos/how-azure-web-sites-scale/)

