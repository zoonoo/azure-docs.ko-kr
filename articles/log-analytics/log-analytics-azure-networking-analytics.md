<properties
    pageTitle="Log Analytics의 Azure Networking Analytics 솔루션 | Microsoft Azure"
    description="Log Analytics의 Azure Networking Analytics 솔루션을 사용하여 Azure 네트워크 보안 그룹 로그와 Azure Application Gateway 로그를 검토할 수 있습니다."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="richrund"/>


# <a name="azure-networking-analytics-(preview)-solution-in-log-analytics"></a>Log Analytics의 Azure Networking Analytics 솔루션(Preview) 

>[AZURE.NOTE] [Preview 솔루션](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features)입니다.

Log Analytics의 Azure Networking Analytics 솔루션을 사용하여 Azure Application Gateway 로그와 Azure 네트워크 보안 그룹 로그를 검토할 수 있습니다.

Azure Application Gateway 로그와 Azure 네트워크 보안 그룹에 대해 로깅을 사용하도록 설정할 수 있습니다. 이 로그는 Blob Storage에 기록되며 이후 검색 및 분석을 위해 Log Analytics로 인덱싱할 수 있습니다.

Application Gateway에는 다음 로그가 지원됩니다.

+ ApplicationGatewayAccessLog
+ ApplicationGatewayPerformanceLog

네트워크 보안 그룹에는 다음 로그가 지원됩니다.

+ NetworkSecurityGroupEvent
+ NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>솔루션 설치 및 구성

다음 지침을 사용하여 Azure Networking Analytics 솔루션을 설치 및 구성합니다.

1.  모니터링할 리소스에 대해 진단 로깅을 사용하도록 설정합니다.
  + [응용 프로그램 게이트웨이](../application-gateway/application-gateway-diagnostics.md)
  + [네트워크 보안 그룹](../virtual-network/virtual-network-nsg-manage-log.md)
2.  [Blob Storage의 JSON 파일](../log-analytics/log-analytics-azure-storage-json.md)에서 설명한 프로세스를 사용하여 Blob Storage에서 로그를 읽도록 Log Analytics를 구성합니다.
3.  [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 설명한 프로세스를 사용하여 Azure Networking Analytics 솔루션을 사용하도록 설정합니다.  

특정 리소스 형식에 대해 진단 로깅을 사용하도록 설정하지 않은 경우 해당 리소스에 대한 대시보드 블레이드가 비어 있습니다.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Azure Networking Analytics 데이터 수집 상세 정보 검토

Azure Networking Analytics 솔루션은 Azure Application Gateway 및 네트워크 보안 그룹에 대해 Azure Blob Storage로부터 진단 로그를 수집합니다.
데이터 수집에 에이전트가 필요하지 않습니다. 

다음 표에서는 데이터 수집 방법 및 Azure Networking Analytics에 대해 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 플랫폼 | 직접 에이전트 | SCOM(Systems Center Operations Manager) 에이전트 | Azure 저장소 | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
|---|---|---|---|---|---|---|
|Azure|![아니요](./media/log-analytics-azure-networking/oms-bullet-red.png)|![아니요](./media/log-analytics-azure-networking/oms-bullet-red.png)|![예](./media/log-analytics-azure-networking/oms-bullet-green.png)|            ![아니요](./media/log-analytics-azure-networking/oms-bullet-red.png)|![아니요](./media/log-analytics-azure-networking/oms-bullet-red.png)| 10분|

## <a name="use-azure-networking-analytics"></a>Azure Networking Analytics 사용

솔루션을 설치한 후에는 Log Analytics의 **개요** 페이지에서 **Azure Networking Analytics** 타일을 사용하여 모니터링되는 Application Gateway의 클라이언트 및 서버 요약을 볼 수 있습니다.

![Azure Networking Analytics 타일 이미지](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

**개요** 타일을 클릭한 후 로그의 요약을 확인하고 다음 범주에 대한 상세 정보를 파악할 수 있습니다.

+ Application Gateway 액세스 로그
  - Application Gateway 액세스 로그에 대한 클라이언트 및 서버 오류
  - 각 Application Gateway의 시간당 요청
  - 각 Application Gateway의 시간당 실패한 요청
  - Application Gateway에 대한 사용자 에이전트별 오류
+ Application Gateway 성능 
  - Application Gateway에 대한 호스트 상태
  - Application Gateway 실패한 요청에 대해 최대값 및 95번째 백분위수
+ 네트워크 보안 그룹 차단 흐름
  - 차단된 흐름이 있는 네트워크 보안 그룹 규칙
  - 차단된 흐름이 있는 MAC 주소 
+ 네트워크 보안 그룹 허용 흐름
  - 허용된 흐름이 있는 네트워크 보안 그룹 규칙
  - 허용된 흐름이 있는 MAC 주소 


![Azure Networking Analytics 대시보드 이미지](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![Azure Networking Analytics 대시보드 이미지](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![Azure Networking Analytics 대시보드 이미지](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![Azure Networking Analytics 대시보드 이미지](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>모든 로그 요약의 세부 정보를 보려면

1. **개요** 페이지에서 **Azure Networking Analytics** 타일을 클릭합니다.
2. **Azure Networking Analytics** 대시보드에서 블레이드 중 하나에서 요약 정보를 검토한 다음 하나를 클릭하여 로그 검색 페이지에서 항목에 대한 세부 정보를 봅니다.

    로그 검색 페이지에서, 시간별 결과, 자세한 결과 및 로그 검색 기록을 볼 수 있습니다. 패싯으로 필터링하여 결과 범위를 좁힐 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Log Analytics의 로그 검색](log-analytics-log-searches.md)을 사용하여 자세한 Azure Networking Analytics 데이터를 확인합니다.



<!--HONumber=Oct16_HO2-->


