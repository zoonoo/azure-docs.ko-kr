---
title: "클라우드 서비스를 모니터링하는 방법 | Microsoft Docs"
description: "Azure 클래식 포털을 사용하여 Cloud Services를 모니터링하는 방법에 대해 알아봅니다."
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: 5c48d2fb-b8ea-420f-80df-7aebe2b66b1b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2015
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: ab97962175f4498200db428736a1cbd124fac285
ms.openlocfilehash: aab8f3233191c9b3f839e3358902f72499d93ee5

---
# <a name="how-to-monitor-cloud-services"></a>Cloud Services를 모니터링하는 방법
[!INCLUDE [disclaimer](../../includes/disclaimer.md)]

Azure 클래식 포털에서 Cloud Services의 `key` 성능 메트릭을 모니터링할 수 있습니다. 각 서비스 역할에 대해 모니터링 수준을 최소 및 자세히로 설정하고 모니터링 표시를 사용자 지정할 수 있습니다. 자세한 모니터링 데이터는 저장소 계정에 저장되며, 포털 외부에서 액세스할 수 있습니다. 

Azure 클래식 포털의 모니터링 표시는 매우 다양하게 구성할 수 있습니다. **모니터** 페이지의 메트릭 목록에서 모니터링할 메트릭을 선택하고 **모니터** 페이지 및 대시보드에서 메트릭 차트에 넣을 메트릭을 선택할 수 있습니다. 

## <a name="concepts"></a>개념
기본적으로 최소 모니터링은 역할 인스턴스(가상 컴퓨터)에 대해 호스트 운영 체제에서 수집된 성능 카운터를 사용하여 새 클라우드 서비스에 제공됩니다. 최소 메트릭은 CPU 비율, 데이터 입력, 데이터 출력, 디스크 읽기 처리량 및 디스크 쓰기 처리량으로 제한됩니다. 자세한 모니터링을 구성하면 가상 컴퓨터(역할 인스턴스) 내의 성능 데이터를 기반으로 추가 메트릭을 받을 수 있습니다. 자세한 정보 표시 메트릭을 사용하면 응용 프로그램 작동 중 발생하는 문제를 보다 자세히 분석할 수 있습니다.

기본적으로 역할 인스턴스의 성능 카운터 데이터는 역할 인스턴스에서 3분 간격으로 샘플링되고 전송됩니다. 자세한 모니터링을 사용하는 경우 원시 성능 카운터 데이터가 각 역할 인스턴스 및 각 역할의 전체 역할 인스턴스에 대해 5분, 1시간 및 12시간 간격으로 집계됩니다. 집계된 데이터는 10일 후에 삭제됩니다.

자세한 모니터링을 사용하면 집계된 모니터링 데이터가 저장소 계정에 테이블로 저장됩니다. 역할에 자세한 모니터링을 사용하려면 저장소 계정에 연결되는 진단 연결 문자열을 구성해야 합니다. 역할에 따라 다른 저장소 계정을 사용할 수 있습니다.

자세한 모니터링을 사용하면 데이터 저장소, 데이터 전송 및 저장소 트랜잭션과 관련된 저장소 비용이 증가합니다. 최소 모니터링에는 저장소 계정이 필요하지 않습니다. 최소 모니터링 수준에서 노출되는 메트릭에 대한 데이터는 모니터링 수준을 자세히로 설정하는 경우에도 저장소 계정에 저장되지 않습니다.

## <a name="how-to-configure-monitoring-for-cloud-services"></a>방법: Cloud Services에 대한 모니터링 구성
다음 절차를 사용하여 Azure 클래식 포털에서 자세한 모니터링 또는 최소 모니터링을 구성합니다. 

### <a name="before-you-begin"></a>시작하기 전에
* 모니터링 데이터를 저장할 *클래식* 저장소 계정을 만듭니다. 역할에 따라 다른 저장소 계정을 사용할 수 있습니다. 자세한 내용은 [저장소 계정을 만드는 방법](../storage/storage-create-storage-account.md#create-a-storage-account)을 참조하세요.
* 클라우드 서비스 역할에 대해 Azure 진단을 사용하도록 설정합니다. [Cloud Services에 대한 진단 구성](cloud-services-dotnet-diagnostics.md)을 참조하십시오.

역할 구성에서 진단 연결 문자열이 있는지 확인하십시오. Azure 진단을 사용하도록 설정하고 역할 구성에 진단 연결 문자열을 포함할 때까지 자세한 정보 표시를 켤 수 없습니다.   

> [!NOTE]
> Azure SDK 2.5 대상 프로젝트는 프로젝트 템플릿에 진단 연결 문자열을 자동으로 포함하지 않습니다. 이러한 프로젝트에 대해서는 역할 구성에 진단 연결 문자열을 수동으로 추가해야 합니다.
> 
> 

**역할 구성에 수동으로 진단 연결 문자열을 추가하려면**

1. Visual Studio에서 클라우드 서비스 프로젝트를 엽니다.
2. **역할**을 두 번 클릭하여 역할 디자이너를 열고 **설정** 탭을 선택합니다.
3. **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**이라는 설정을 찾습니다. 
4. 이 설정이 없다면 **설정 추가** 단추를 클릭하여 구성에 추가한 다음 새로운 설정의 유형을 **ConnectionString**으로 변경합니다.
5. **...** 단추를 클릭하여 연결 문자열에 대한 값을 설정합니다. 그러면 저장소 계정을 선택할 수 있는 대화 상자가 열립니다.
   
    ![Visual Studio 설정](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

### <a name="to-change-the-monitoring-level-to-verbose-or-minimal"></a>모니터링 수준을 자세히 또는 최소로 변경하려면
1. [Azure 클래식 포털](https://manage.windowsazure.com/)에서 클라우드 서비스 배포를 위한 **구성** 페이지를 엽니다.
2. **수준**에서 **자세히** 또는 **최소**를 클릭합니다. 
3. **저장**을 클릭합니다.

자세한 모니터링을 켜면 해당 시간 내에 Azure 클래식 포털에 모니터링 데이터가 표시되기 시작합니다.

원시 성능 카운터 데이터 및 집계된 모니터링 데이터는 역할에 대한 배포 ID로 한정되는 테이블로 저장소 계정에 저장됩니다. 

## <a name="how-to-receive-alerts-for-cloud-service-metrics"></a>방법: 클라우드 서비스 메트릭에 대한 경고 받기
클라우드 서비스 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다. Azure 클래식 포털의 **관리 서비스** 페이지에서 선택한 메트릭이 지정한 값에 도달하는 경우 경고를 트리거하는 규칙을 만들 수 있습니다. 또한 경고가 트리거되면 메일을 보내도록 선택할 수 있습니다. 자세한 내용은 [방법: Azure에서 경고 알림 받기 및 경고 규칙 관리](http://go.microsoft.com/fwlink/?LinkId=309356)를 참조하세요.

## <a name="how-to-add-metrics-to-the-metrics-table"></a>방법: 메트릭 테이블에 메트릭 추가
1. [Azure 클래식 포털](http://manage.windowsazure.com/)에서 클라우드 서비스에 대한 **모니터** 페이지를 엽니다.
   
    기본적으로 메트릭 테이블에는 사용 가능한 메트릭의 하위 집합이 표시됩니다. 다음 그림에서는 역할 수준에서 집계된 데이터를 사용하여 Memory\Available MBytes 성능 카운터로 제한되는 클라우드 서비스에 대한 기본적인 자세한 정보 표시 메트릭을 보여 줍니다. **메트릭 추가** 를 사용하여 Azure 클래식 포털에서 모니터링할 추가 집계 및 역할 수준 메트릭을 선택합니다.
   
    ![자세한 표시](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
2. 메트릭 테이블에 메트릭을 추가하려면
   
   1. 아래와 같이 **메트릭 추가**를 클릭하여 **메트릭 선택**을 엽니다.
      
       사용 가능한 첫 번째 메트릭이 확장되어 사용 가능한 옵션을 보여 줍니다. 각 메트릭에 대한 맨 위 옵션에는 모든 역할에 대해 집계된 모니터링 데이터가 표시됩니다. 또한 데이터를 표시할 개별 역할을 선택할 수도 있습니다.
      
       ![메트릭 추가](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)
   2. 표시할 메트릭을 선택하려면
      
      * 메트릭 옆에 있는 아래쪽 화살표를 클릭하여 모니터링 옵션을 확장합니다.
      * 표시할 각 모니터링 옵션에 대한 확인란을 선택합니다.
        
        메트릭 테이블에 최대 50개의 메트릭을 표시할 수 있습니다.
        
        > [!TIP]
        > 자세한 모니터링에서 메트릭 목록에는 수십 개의 메트릭이 포함되어 있습니다. 스크롤 막대를 표시하려면 대화 상자의 오른쪽을 가리킵니다. 목록을 필터링하려면 아래와 같이 검색 아이콘을 클릭하고 검색 상자에 텍스트를 입력합니다.
        > 
        > 
        
        ![메트릭 검색 추가](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)
3. 메트릭 선택을 마치면 확인(확인 표시)을 클릭합니다.
   
    아래와 같이 선택한 메트릭이 메트릭 테이블에 추가됩니다.
   
    ![모니터 메트릭](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)
4. 메트릭 테이블에서 메트릭을 삭제하려면 메트릭을 클릭하여 선택한 다음 **메트릭 삭제**를 클릭합니다. 메트릭을 선택한 경우에만 **메트릭 삭제** 이 표시됩니다.

### <a name="to-add-custom-metrics-to-the-metrics-table"></a>메트릭 테이블에 사용자 지정 메트릭을 추가하려면
**자세한 정보 표시** 모니터링 수준은 포털에서 모니터링할 수 있는 기본 메트릭 목록을 제공합니다. 이외에도 포털을 통해 응용 프로그램에 의해 정의되는 모든 사용자 지정 또는 성능 카운터를 모니터링할 수 있습니다.

다음 단계는 **자세한 정보 표시** 모니터링 수준을 켜고 사용자 지정 성능 카운터를 수집 및 전송하도록 응용 프로그램을 구성했다고 가정합니다. 

포털에서 사용자 지정 성능 카운터를 표시하려면 wad-control-container에서 구성을 업데이트 해야 합니다.

1. 진단 저장소 계정의 wad-control-container blob을 엽니다. Visual Studio 또는 다른 저장소 Explorer를 사용할 수 있습니다.
   
    ![Visual Studio 서버 Explorer](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)
2. 패턴 **DeploymentId/RoleName/RoleInstance**을 사용하여 blob 경로로 이동하여 역할 인스턴스에 대한 구성을 찾습니다. 
   
    ![Visual Studio 저장소 Explorer](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. 역할 인스턴스에 대한 구성 파일을 다운로드 하고 모든 사용자 지정 성능 카운터를 포함하도록 업데이트합니다. 예를 들어 *C 드라이브*에 대해 *Disk Write Bytes/sec*를 모니터링하려면 **PerformanceCounters\Subscriptions** 노드 아래에 다음을 추가합니다.
   
    ```xml
    <PerformanceCounterConfiguration>
    <CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
    <SampleRateInSeconds>180</SampleRateInSeconds>
    </PerformanceCounterConfiguration>
    ```
4. 변경 내용을 저장하고 구성 파일을 동일한 위치에 업로드하여 blob 내 기존 파일을 덮어씁니다.
5. Azure 클래식 포털 구성에서 자세한 정보 표시 모드를 설정/해제합니다. 이미 자세한 정보 표시 모드인 경우에는 최소로 전환한 다음 자세한 정보 표시로 돌아가야 합니다.
6. 그러면 사용자 지정 성능 카운터가 **메트릭 추가** 대화 상자에 나타납니다. 

## <a name="how-to-customize-the-metrics-chart"></a>방법: 메트릭 차트 사용자 지정
1. 메트릭 테이블에서 메트릭 차트에 넣을 메트릭을 최대 6개까지 선택합니다. 메트릭을 선택하려면 왼쪽에 있는 확인란을 클릭합니다. 메트릭 차트에서 메트릭을 제거하려면 메트릭 테이블에서 해당 확인란을 선택 취소합니다.
   
    메트릭 테이블에서 메트릭을 선택하면 메트릭이 메트릭 차트에 추가됩니다. 좁은 화면에서는 화면에 맞지 않는 메트릭 헤더가 **n more** 드롭다운 목록에 포함됩니다.
2. 상대 값(각 메트릭에 대한 최종 값만)과 절대 값(Y축에 표시됨) 표시를 전환하려면 차트 맨 위에서 상대 또는 절대를 선택합니다.
   
    ![상대 또는 절대](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)
3. 메트릭 차트가 표시되는 시간 범위를 변경하려면 차트 맨 위에서 1시간, 24시간 또는 7일을 선택합니다.
   
    ![모니터 표시 기간](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)
   
    대시보드 메트릭 차트에서 메트릭을 넣는 방법은 다양합니다. 표준 메트릭 집합을 사용할 수 있으며, 메트릭 헤더를 선택하여 메트릭을 추가하거나 제거합니다.

### <a name="to-customize-the-metrics-chart-on-the-dashboard"></a>대시보드에서 메트릭 차트를 사용자 지정하려면
1. 클라우드 서비스에 대한 대시보드를 엽니다.
2. 차트에서 메트릭을 추가하거나 제거합니다.
   
   * 새 메트릭을 넣으려면 차트 헤더에서 메트릭에 대한 확인란을 선택합니다. 좁은 화면에서는 ***n*??메트릭** 옆에 있는 아래쪽 화살표를 클릭하여 차트 헤더 영역에 표시할 수 없는 메트릭을 넣습니다.
   * 차트에 그려진 메트릭을 삭제하려면 헤더 옆에 있는 확인란을 선택 취소합니다.
   
3. **상대** 및 **절대** 표시를 전환합니다.
4. 1시간, 24시간 또는 7일의 데이터를 선택하여 표시합니다.

## <a name="how-to-access-verbose-monitoring-data-outside-the-azure-classic-portal"></a>방법: Azure 클래식 포털 외부에서 자세한 모니터링 데이터 액세스
자세한 모니터링 데이터는 각 영역에 대해 지정한 저장소 계정에 테이블로 저장됩니다. 각 클라우드 서비스 배포에서 역할에 대해 6개의 테이블이 만들어집니다. 각각(5분, 1시간 및 12시간)에 대해 두 개의 테이블이 만들어집니다. 이러한 테이블 중 하나에는 역할 수준 집계가 저장되고, 다른 테이블에는 역할 인스턴스에 대한 집계가 저장됩니다. 

테이블 이름의 형식은 다음과 같습니다.

```
WAD*deploymentID*PT*aggregation_interval*[R|RI]Table
```

위치:

* *deploymentID*는 클라우드 서비스 배포에 할당된 GUID입니다.
* *aggregation_interval* = 5M, 1H 또는 12H
* 역할 수준 집계 = R
* 역할 인스턴스에 대한 집계 = RI

예를 들어 다음 테이블에는 1시간 간격으로 집계된 자세한 모니터링 데이터가 저장됩니다.

```
WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
```



<!--HONumber=Dec16_HO2-->


