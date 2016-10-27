<properties
    pageTitle="Log Analytics의 Azure Key Vault 솔루션 | Microsoft Azure"
    description="Log Analytics에서 Azure Key Vault 솔루션을 사용하여 Azure Key Vault 로그를 검토할 수 있습니다."
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
    ms.date="07/12/2016"
    ms.author="richrund"/>


# <a name="azure-key-vault-(preview)-solution-in-log-analytics"></a>Log Analytics의 Azure Key Vault(Preview) 솔루션

>[AZURE.NOTE] [Preview 솔루션](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features)입니다.

Log Analytics에서 Azure Key Vault 솔루션을 사용하여 Azure Key Vault AuditEvent 로그를 검토할 수 있습니다.

Azure Key Vault에 대해 감사 이벤트의 로깅을 사용할 수 있습니다. 이 로그는 Azure Blob Storage에 기록되며 이후 검색과 분석을 위해 Log Analytics로 인덱싱할 수 있습니다.

## <a name="install-and-configure-the-solution"></a>솔루션 설치 및 구성

다음 지침을 사용하여 Azure Key Vault 솔루션을 설치 및 구성합니다.

1.  모니터링할 [Key Vault](../key-vault/key-vault-logging.md) 리소스에 대해 진단 로깅을 사용하도록 설정합니다.
2.  [Blob Storage의 JSON 파일](../log-analytics/log-analytics-azure-storage-json.md)에서 설명한 프로세스를 사용하여 Blob Storage에서 로그를 읽도록 Log Analytics를 구성합니다.
3.  [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 설명한 프로세스를 사용하여 Azure Key Vault 솔루션을 사용하도록 설정합니다.  

## <a name="review-azure-key-vault-data-collection-details"></a>Azure Key Vault 데이터 수집 상세 정보를 검토합니다.

Azure Key Vault 솔루션은 Azure Key Vault에 대해 Azure Blob Storage에서 진단 로그를 수집합니다.
데이터 수집에 에이전트가 필요하지 않습니다. 

다음 표에서는 데이터 수집 방법 및 Azure Key Vault에 대해 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 플랫폼 | 직접 에이전트 | SCOM(Systems Center Operations Manager) 에이전트 | Azure 저장소 | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
|---|---|---|---|---|---|---|
|Azure|![아니요](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![아니요](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![예](./media/log-analytics-azure-keyvault/oms-bullet-green.png)|            ![아니요](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![아니요](./media/log-analytics-azure-keyvault/oms-bullet-red.png)| 10분|

## <a name="use-azure-key-vault"></a>Azure Key Vault 사용

Log Analytics **개요** 페이지의 **Azure Key Vault** 타일을 사용하여 솔루션을 설치한 후에는 모니터링되는 Key Vault에 대해 시간 경과에 따른 요청 상태의 요약을 확인할 수 있습니다.  

![Azure Key Vault 타일 이미지](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

**개요** 타일을 클릭한 후 로그의 요약을 확인하고 다음 범주에 대한 상세 정보를 파악할 수 있습니다.

- 시간 경과에 따른 모든 Key Vault 작업 크기
- 시간 경과에 따른 실패 작업
- 작업별 평균 작업 대기 시간
- 1000ms 이상이 소요되는 작업 수 및 1000ms 이상이 소요되는 작업 목록을 포함한 작업 서비스 품질

![Azure Key Vault 대시보드 이미지](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Azure Key Vault 대시보드 이미지](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>모든 작업에 대한 세부 사항을 보려면

1. **개요** 페이지에서 **Azure Key Vault** 타일을 클릭합니다.
2. **Azure Key Vault** 대시보드에서 블레이드 중 하나에서 요약 정보를 검토한 다음 하나를 클릭하여 로그 검색 페이지에서 항목에 대한 세부 정보를 봅니다.

    로그 검색 페이지에서, 시간별 결과, 자세한 결과 및 로그 검색 기록을 볼 수 있습니다. 패싯으로 필터링하여 결과 범위를 좁힐 수 있습니다.

## <a name="log-analytics-records"></a>Log Analytics 레코드

Azure Key Vault 솔루션은 Azure Diagnostics에서 [AuditEvent logs](..\key-vault\key-vault-logging.md)에서 수집된 **KeyVaults** 형식의 레코드를 분석합니다.  이러한 레코드의 속성은 다음 표에 있습니다.  

| 속성 | 설명 |
|:--|:--|
| 형식 | *KeyVaults* |
| SourceSystem | *AzureStorage* |
| callerIpAddress | 요청한 클라이언트의 IP 주소입니다. |
| Category      | 키 자격 증명 모음 로그의 경우 AuditEvent는 단일의 사용 가능한 값입니다.|
| CorrelationId | 클라이언트가 서비스 쪽(키 자격 증명 모음) 로그를 사용하여 클라이언트 쪽 로그 상관 관계를 지정하도록 전달할 수 있는 선택적 GUID입니다. |
| DurationMs | 밀리초 단위로 REST API 요청을 처리하는 데 걸린 시간입니다. 이 항목은 네트워크 대기 시간을 포함하지 않으므로 클라이언트 쪽에서 측정한 시간은 이 시간과 일치하지 않을 수 있습니다. |
| HttpStatusCode_d | 요청에서 반환한 HTTP 상태 코드 |
| Id_s       | 요청의 고유 ID |
| Identity_o | REST API 요청을 수행할 때 제공된 토큰의 ID입니다. Azure PowerShell cmdlet에서 발생하는 요청의 경우처럼 이는 보통 "사용자", "서비스 주체" 또는 "사용자+appId"의 조합입니다. |
| OperationName      | [Azure Key Vault 로깅](..\key-vault\key-vault-logging.md)에 설명된 대로 작업의 이름입니다.|
| OperationVersion      | 클라이언트에서 요청한 REST API 버전입니다.|
| RemoteIPLatitude | 요청한 클라이언트의 위도입니다. |
| RemoteIPLongitude | 요청한 클라이언트의 경도입니다. |
| RemoteIPCountry | 요청한 클라이언트의 국가입니다.  |
| RequestUri_s | 요청의 URI입니다. |
| 리소스   | Key Vault의 이름입니다. |
| ResourceGroup | Key Vault의 리소스 그룹입니다. |
| ResourceId | Azure 리소스 관리자 리소스 ID. 키 자격 증명 모음 로그의 경우 이는 항상 키 자격 증명 모음 리소스 ID입니다. |
| ResourceProvider | *MICROSOFT.KEYVAULT* |
| ResultSignature  | HTTP 상태입니다.|
| ResultType      | REST API 요청의 결과입니다.|
| SubscriptionId | Key Vault를 포함하는 구독의 Azure 구독 ID입니다. |


## <a name="next-steps"></a>다음 단계

- [Log Analytics의 로그 검색](log-analytics-log-searches.md)을 사용하여 자세한 Azure Key Vault 데이터를 확인합니다.



<!--HONumber=Oct16_HO2-->


