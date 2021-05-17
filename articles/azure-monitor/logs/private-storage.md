---
title: Azure Monitor Log Analytics에서 고객 관리형 스토리지 계정 사용
description: Log Analytics 시나리오에 고유한 스토리지 계정 사용
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: a6d4c5811c08aa8c4de2eeea5f5f53967c3006b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025359"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics에서 고객 관리형 스토리지 계정 사용

Log Analytics는 다양한 시나리오에서 Azure Storage를 사용합니다. 이 사용은 일반적으로 자동으로 관리됩니다. 그러나 경우에 따라 고객 관리형 스토리지 계정이라고도 하는 고유한 스토리지 계정을 제공하고 관리해야 하는 경우도 있습니다. 이 문서에서는 WAD/LAD 로그, Private Link 및 CMK(고객 관리형 키) 암호화를 위해 고객 관리형 스토리지를 사용하는 방법을 설명합니다. 

> [!NOTE]
> 서식 및 콘텐츠가 변경될 수 있는 경우 고객 관리형 스토리지에 대한 콘텐츠 Log Analytics 업로드에 의존하지 않는 것이 좋습니다.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Azure Diagnostics 확장 로그(WAD/LAD) 수집
Azure Diagnostics 확장 에이전트(Windows용 WAD, Linux 에이전트용 LAD라고도 함)는 다양한 운영 체제 로그를 수집하고 고객 관리형 스토리지 계정에 저장합니다. 그런 다음 이러한 로그를 Log Analytics에 수집하고 검토 및 분석할 수 있습니다.
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>스토리지 계정에서 Azure Diagnostics 확장 로그를 수집하는 방법
[Azure Portal](../agents/diagnostics-extension-logs.md#collect-logs-from-azure-storage)을 사용하거나 [Storage Insights API](/rest/api/loganalytics/storage%20insights/createorupdate)를 호출하여 스토리지 계정을 스토리지 데이터 원본으로서 Log Analytics 작업 영역에 연결합니다.

지원되는 데이터 형식은
* Syslog
* Windows 이벤트
* Service Fabric
* ETW 이벤트
* IIS 로그

## <a name="using-private-links"></a>프라이빗 사용
고객 관리형 스토리지 계정은 프라이빗 링크를 사용하여 Azure Monitor 리소스에 연결할 때 사용자 지정 로그 또는 IIS 로그를 수집하는 데 사용됩니다. 이러한 데이터 형식의 수집 프로세스는 먼저 로그를 중간 Azure Storage 계정에 업로드한 다음, 작업 영역에 수집하기만 합니다. 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>프라이빗 링크를 통해 고객 관리형 스토리지 계정 사용
#### <a name="workspace-requirements"></a>작업 영역 요구 사항
프라이빗 링크를 통해 Azure Monitor에 연결할 때 Log Analytics 에이전트는 프라이빗 링크를 통해 액세스할 수 있는 작업 영역에만 로그를 보낼 수 있습니다. 이 요구 사항은 다음을 수행해야 한다는 것을 의미합니다.
* AMPLS(Azure Monitor Private Link Scope) 개체 구성
* 작업 영역에 연결
* 프라이빗 링크를 통해 AMPLS를 네트워크에 연결. 

AMPLS 구성 절차에 대한 자세한 내용은 [Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결](./private-link-security.md)을 참조하세요. 

#### <a name="storage-account-requirements"></a>스토리지 계정 요구 사항
스토리지 계정이 프라이빗 링크에 성공적으로 연결되려면 다음을 수행해야 합니다.
* VNet 또는 피어링 네트워크에 있고 프라이빗 링크를 통해 VNet에 연결되어야 합니다.
* 연결된 작업 영역과 동일한 지역에 있어야 합니다.
* Azure Monitor에서 스토리지 계정에 액세스할 수 있도록 허용합니다. 선택한 네트워크만 스토리지 계정에 액세스할 수 있도록 선택한 경우 "신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용" 예외를 선택해야 합니다.
![스토리지 계정 신뢰 MS 서비스 이미지](./media/private-storage/storage-trust.png)
* 작업 영역에서 다른 네트워크의 트래픽만 처리하는 경우 관련 네트워크/인터넷에서 들어오는 트래픽을 허용하도록 스토리지 계정을 구성해야 합니다.
* 에이전트와 스토리지 계정 간의 TLS 버전을 조정합니다. TLS 1.2 이상을 사용하여 Log Analytics에 데이터를 전송하는 것이 좋습니다. [플랫폼별 지침](./data-security.md#sending-data-securely-using-tls-12)을 검토하고 필요한 경우 [TLS 1.2를 사용하도록 에이전트를 구성](../agents/agent-windows.md#configure-agent-to-use-tls-12)합니다. 몇 가지 이유로 인해 이렇게 할 수 없는 경우 TLS 1.0을 허용하도록 스토리지 계정을 구성합니다.

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>CMK 데이터 암호화에 고객 관리형 스토리지 계정 사용
Azure Storage는 스토리지 계정의 모든 미사용 데이터를 암호화합니다. 기본적으로 MMK(Microsoft 관리 키)를 사용하여 데이터를 암호화합니다. 그러나 Azure Storage를 사용하면 Azure Key Vault의 CMK를 사용하여 스토리지 데이터를 암호화할 수도 있습니다. 사용자 고유의 키를 Azure Key Vault로 가져올 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다.
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>고객 관리형 스토리지 계정이 필요한 CMK 시나리오
* 로그 암호화 - CMK를 사용하여 경고 쿼리
* CMK를 사용하여 저장된 쿼리 암호화

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>고객 관리형 스토리지 계정에 CMK를 적용하는 방법
##### <a name="storage-account-requirements"></a>스토리지 계정 요구 사항
스토리지 계정 및 키 자격 증명 모음은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수도 있습니다. Azure Storage 암호화 및 키 관리에 대한 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](../../storage/common/storage-service-encryption.md)를 참조하세요.

##### <a name="apply-cmk-to-your-storage-accounts"></a>스토리지 계정에 CMK 적용
Azure Key Vault에서 CMK를 사용하도록 Azure Storage 계정을 구성하려면 [Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) 또는 [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)를 사용합니다. 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>Log Analytics 작업 영역에 스토리지 계정 연결
### <a name="using-the-azure-portal"></a>Azure Portal 사용
Azure Portal에서 작업 영역 메뉴를 열고 *연결된 스토리지 계정* 을 선택합니다. 블레이드가 열리고 위에서 언급한 사용 사례(Private Link를 통한 수집, 저장된 쿼리에 또는 경고에 CMK 적용)에 따라 연결된 스토리지 계정이 표시됩니다.
![연결된 스토리지 계정 블레이드 이미지](./media/private-storage/all-linked-storage-accounts.png) 테이블에서 항목을 선택 하면 해당 스토리지 계정 세부 정보가 열립니다. 여기서 이 형식에 대해 연결된 스토리지 계정을 설정하거나 업데이트할 수 있습니다. 
![스토리지 계정 블레이드 이미지 링크](./media/private-storage/link-a-storage-account-blade.png) 원할 경우 다른 사용 사례에 대해 동일한 계정을 사용할 수 있습니다.

### <a name="using-the-azure-cli-or-rest-api"></a>Azure CLI 또는 REST API 사용
[Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) 또는 [REST API](/rest/api/loganalytics/linkedstorageaccounts)를 통해 스토리지 계정을 작업 영역에 연결할 수도 있습니다.

적용 가능한 dataSourceType 값은 다음과 같습니다.
* CustomLogs – 사용자 지정 로그 및 IIS 로그 수집에 스토리지 계정을 사용합니다.
* Query - 스토리지 계정을 사용하여 저장된 쿼리를 저장합니다(CMK 암호화에 필요).
* Alerts - 스토리지 계정을 사용하여 로그 기반 경고를 저장합니다(CMK 암호화에 필요).


## <a name="managing-linked-storage-accounts"></a>연결된 스토리지 계정 관리

### <a name="create-or-modify-a-link"></a>링크 만들기 또는 수정
스토리지 계정을 작업 영역에 연결하면 Log Analytics는 서비스에서 소유하는 스토리지 계정 대신 해당 계정을 사용합니다. 이 문서의 설명에 따라 Azure Automation Hybrid Runbook Worker를 제거할 수 있습니다. 
* 여러 스토리지 계정을 등록하여 로그의 부하 분산
* 여러 작업 영역에 동일한 스토리지 계정 다시 사용

### <a name="unlink-a-storage-account"></a>스토리지 계정 연결 해제
스토리지 계정 사용을 중지하려면 작업 영역에서 스토리지의 연결을 해제합니다. 작업 영역에서 모든 스토리지 계정의 연결을 해제하면 Log Analytics가 서비스 관리 스토리지 계정을 사용하려고 시도합니다. 네트워크에서 인터넷에 대한 액세스가 제한된 경우 이러한 스토리지를 사용하지 못할 수 있으며 스토리지에 의존하는 모든 시나리오가 실패합니다.

### <a name="replace-a-storage-account"></a>스토리지 계정 교체
수집에 사용되는 스토리지 계정을 바꾸려면
1.  **새 스토리지 계정에 대한 링크를 만듭니다.** 로깅 에이전트는 업데이트된 구성을 가져오고 새 스토리지로 데이터 전송도 시작합니다. 이 프로세스에 몇 분 정도 걸릴 수 있습니다.
2.  **에이전트가 제거된 계정에 쓰기를 중지하도록 이전 스토리지 계정의 연결을 해제합니다.** 수집 프로세스는 모두 수집될 때까지 이 계정의 데이터를 계속 읽습니다. 모든 로그가 수집될 때까지 스토리지 계정을 삭제하지 마세요.

### <a name="maintaining-storage-accounts"></a>스토리지 계정 유지 관리
#### <a name="manage-log-retention"></a>로그 보존 관리
사용자 고유의 스토리지 계정을 사용하는 경우에는 사용자가 보존 여부를 결정합니다. Log Analytics는 프라이빗 스토리지에 저장된 로그를 삭제하지 않습니다. 대신, 기본 설정에 따라 부하를 처리하는 정책을 설정해야 합니다.

#### <a name="consider-load"></a>부하 고려
스토리지 계정은 제한 요청을 시작하기 전에 특정 읽기 및 쓰기 요청 부하를 처리할 수 있습니다. 자세한 내용은 [Blob 스토리지의 스케일링 기능 및 성능 목표](../../storage/common/scalability-targets-standard-account.md)를 참조하세요. 제한은 로그를 수집하는 데 걸리는 시간에 영향을 줍니다. 스토리지 계정이 오버로드된 경우 추가 스토리지 계정을 등록하여 부하를 분산합니다. 스토리지 계정의 용량 및 성능을 모니터링하려면 [Azure Portal의 인사이트]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview)를 검토하세요.

### <a name="related-charges"></a>관련 요금
스토리지 계정은 저장된 데이터의 볼륨, 스토리지 유형 및 중복 유형에 따라 요금이 청구됩니다. 자세한 내용은 [블록 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs) 및 [Table Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/tables)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결](private-link-security.md)에 대해 자세히 알아보기
- [Azure Monitor 고객 관리형 키](../logs/customer-managed-keys.md)에 대해 자세히 알아보기