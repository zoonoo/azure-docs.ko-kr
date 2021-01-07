---
title: 연결 모드 및 요구 사항
description: 사용자 환경에서 Azure로의 Azure Arc 사용 데이터 서비스 연결 옵션을 설명 합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: c7bff21a17af3c908caeed6a1e60de8e2fe4efc9
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287583"
---
# <a name="connectivity-modes-and-requirements"></a>연결 모드 및 요구 사항

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>연결 모드

Azure Arc 사용 데이터 서비스 환경에서 Azure로의 연결 수준에 대 한 여러 옵션이 있습니다. 요구 사항이 비즈니스 정책, 정부 규제 또는 Azure에 대 한 네트워크 연결의 가용성에 따라 달라 지기 때문에 다음 연결 모드 중에서 선택할 수 있습니다.

Azure Arc 사용 데이터 서비스는 두 가지 *연결 모드* 에서 azure에 연결 하는 옵션을 제공 합니다. 

- 직접 연결 됨 
- 간접적으로 연결 됨

연결 모드에서는 Azure로 전송 되는 데이터의 양과 사용자가 Arc 데이터 컨트롤러와 상호 작용 하는 방법을 유연 하 게 선택할 수 있습니다. 선택한 연결 모드에 따라 Azure Arc 사용 데이터 서비스의 일부 기능을 사용 하거나 사용 하지 못할 수도 있습니다.

중요 한 점은 azure Arc 사용 데이터 서비스를 azure에 직접 연결 하는 경우 사용자는 [Azure Resource Manager api](/rest/api/resources/), Azure CLI 및 Azure Portal를 사용 하 여 azure arc data services를 운영할 수 있습니다. 직접 연결 모드의 환경은 모든 Azure Portal에서 프로 비전/프로 비전 해제, 크기 조정, 구성 등의 다른 Azure 서비스를 사용 하는 방법과 매우 비슷합니다.  Azure Arc 사용 데이터 서비스를 Azure에 간접적으로 연결 하는 경우 Azure Portal은 읽기 전용 뷰입니다. 배포한 SQL 관리 되는 인스턴스 및 Postgres Hyperscale 인스턴스와이 인스턴스에 대 한 세부 정보를 볼 수 있지만 Azure Portal에서는 작업을 수행할 수 없습니다.  간접적으로 연결 된 모드에서는 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] kubectl 같은 Azure Data Studio, 또는 Kubernetes 기본 도구를 사용 하 여 모든 작업을 로컬로 수행 해야 합니다.

또한이 기능을 제공 하기 위해 Azure에 대 한 연속 연결 및 직접 연결에 대 한 종속성이 있기 때문에 Azure Active Directory 및 Azure Role-Based Access Control을 직접 연결 모드에서 사용할 수 있습니다.

일부 Azure 연결 서비스는 Azure Defender 보안 서비스, 컨테이너 정보 및 blob 저장소에 대 한 Azure Backup와 같이 직접 연결할 수 있는 경우에만 사용할 수 있습니다.

현재 미리 보기에서는 간접적으로 연결 된 모드만 지원 됩니다. 

||**간접적으로 연결 됨**|**직접 연결 됨**|**연결 되지 않음**|
|---|---|---|---|
|**설명**|간접적으로 연결 된 모드는 Azure에 직접 연결 하지 않고 사용자 환경에서 로컬로 대부분의 관리 서비스를 제공 합니다.  적은 양의 데이터를 인벤토리 및 청구 목적 _으로만_ Azure에 전송 해야 합니다. 파일에 내보내고 한 달에 한 번 이상 Azure에 업로드 됩니다.  Azure에 대 한 직접 또는 연속 연결이 필요 하지 않습니다.  Azure에 연결 해야 하는 일부 기능 및 서비스는 사용할 수 없습니다.|직접 연결 모드에서는 Azure를 사용 하 여 직접 연결을 설정할 수 있는 경우 사용할 수 있는 모든 서비스를 제공 합니다. 연결은 항상 사용자 환경 _에서_ Azure로 시작 되며, 표준 포트 및 프로토콜 (예: HTTPS/443)을 사용 합니다.|어떤 방식으로든 Azure로 또는 데이터를 보낼 수 없습니다.|
|**현재 가용성**| 미리 보기로 제공됩니다.|향후 미리 보기에 대해 계획 되었습니다.|현재 지원되지 않습니다.|
|**일반적인 사용 사례**|비즈니스 또는 규정 준수 정책으로 인 한 데이터 센터의 데이터 영역 내부 또는 외부의 연결을 허용 하지 않는 온-프레미스 데이터 센터 또는 외부 공격 또는 데이터 exfiltration의 문제를 방지 합니다.  일반적인 예: 금융 기관, 의료 보험, 정부. <br/><br/>에 지 사이트가 일반적으로 인터넷에 연결 되지 않는에 지 사이트 위치입니다.  일반적인 예: 석유/가스 또는 군사 필드 응용 프로그램입니다.  <br/><br/>오랜 기간 동안 간헐적으로 연결 되는에 지 사이트 위치입니다.  일반적인 예: 경기장, 크루즈 제공. | 공용 클라우드를 사용 하는 조직  일반적인 예: Azure, AWS 또는 Google Cloud.<br/><br/>인터넷 연결이 일반적으로 제공 되 고 허용 되는에 지 사이트 위치입니다.  일반적인 예: 소매 매장, 제조.<br/><br/>데이터 센터의 데이터 영역에서 인터넷으로의 연결에 대 한 보다 관대 한 정책을 포함 하는 회사 데이터 센터.  일반적인 예: 규제 되지 않은 비즈니스, 중소 규모 기업|어떤 환경에서 든 데이터 환경에서 데이터를 가져올 수 없는 "gapped" 환경 일반적인 예: 상위 비밀 정부 시설.|
|**Azure에 데이터를 전송 하는 방법**|청구 및 인벤토리 데이터를 Azure로 전송 하는 방법에는 세 가지 옵션이 있습니다.<br><br> 1) 데이터는 보안 데이터 영역과 Azure 모두에 연결 된 자동화 된 프로세스에 의해 데이터 영역 외부로 내보내집니다.<br><br>2) 데이터 영역 내에서 자동화 된 프로세스를 통해 데이터 영역 외부로 데이터를 내보내고, 보안 수준이 낮은 지역에 자동으로 복사 하 고, 보안 수준이 낮은 지역에서 자동화 된 프로세스를 통해 데이터를 Azure에 업로드 합니다.<br><br>3) 데이터는 보안 지역 내의 사용자가 수동으로 내보내고, 안전 지역에서 수동으로 가져와서 Azure에 수동으로 업로드 합니다. <br><br>처음 두 옵션은 자동으로 실행 되도록 예약할 수 있는 자동화 된 연속 프로세스로, azure에 대 한 사용 가능한 연결에 대해서만 Azure에 데이터를 전송 하는 데 최소한의 지연이 있습니다.|데이터는 자동으로 Azure에 전송 됩니다.|데이터는 Azure로 전송 되지 않습니다.|

## <a name="feature-availability-by-connectivity-mode"></a>연결 모드의 기능 가용성

|**기능**|**간접적으로 연결 됨**|**직접 연결 됨**|
|---|---|---|
|**자동 고가용성**|지원됨|지원됨|
|**셀프서비스 프로비저닝**|지원됨<br/>만들기는 Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 또는 Kubernetes 네이티브 도구 (투구, kubectl, oc 등)를 사용 하거나 Azure Arc Enabled Kubernetes GitOps 프로 비전을 사용 하 여 수행할 수 있습니다.|지원됨<br/>간접적으로 연결 된 모드 만들기 옵션 외에도 Azure Portal, Azure Resource Manager Api, Azure CLI 또는 ARM 템플릿을 통해 만들 수 있습니다. **직접 연결 모드의 보류 중인 가용성**
|**탄력적 확장성**|지원됨|지원됨<br/>**직접 연결 모드의 보류 중인 가용성**|
|**Billing**|지원됨<br/>청구 데이터는 주기적으로 내보내고 Azure로 전송 됩니다.|지원됨<br/>청구 데이터는 자동으로 Azure로 전송 되 고 거의 실시간으로 반영 됩니다. **직접 연결 모드의 보류 중인 가용성**|
|**재고 관리**|지원됨<br/>인벤토리 데이터는 주기적으로 내보내고 Azure로 전송 됩니다.<br/><br/>Azure Data Studio, Azure Data CLI와 같은 클라이언트 도구를 사용 하거나 `kubectl` 로컬에서 인벤토리를 보고 관리 합니다.|지원됨<br/>인벤토리 데이터는 자동으로 Azure로 전송 되 고 거의 실시간으로 반영 됩니다. 따라서 Azure Portal에서 직접 인벤토리를 관리할 수 있습니다. **직접 연결 모드의 보류 중인 가용성**|
|**자동 업그레이드 및 패치**|지원됨<br/>데이터 컨트롤러는 MCR (Microsoft Container Registry)에 직접 액세스 하거나, MCR에서 컨테이너 이미지를 가져와서 데이터 컨트롤러에서 액세스할 수 있는 로컬 개인 컨테이너 레지스트리로 푸시 해야 합니다.|지원됨<br/>**직접 연결 모드의 보류 중인 가용성**|
|**자동 백업 및 복원**|지원됨<br/>자동 로컬 백업 및 복원|지원됨<br/>자동 로컬 백업 및 복원 외에도 장기 오프 사이트 보존을 위해 _필요에 따라_ Azure Backup에 백업을 보낼 수 있습니다. **직접 연결 모드의 보류 중인 가용성**|
|**Monitoring**|지원됨<br/>Grafana 및 Kibana 대시보드를 사용 하 여 로컬 모니터링|지원됨<br/>로컬 모니터링 대시보드 외에도 _선택적_ 으로 모니터링 데이터와 로그를 전송 하 여 한 곳에서 여러 사이트에 대 한 규모 규모 모니터링을 Azure Monitor 수 있습니다. **직접 연결 모드의 보류 중인 가용성**|
|**인증**|데이터 컨트롤러 및 대시보드 인증에 로컬 사용자 이름/암호를 사용 합니다. SQL 및 Postgres 로그인 또는 Active Directory를 사용 하 여 데이터베이스 인스턴스에 연결 합니다.  Kubernetes API에 대 한 인증에 K8s 인증 공급자를 사용 합니다.|간접적으로 연결 된 모드에 대 한 인증 방법 외에도 _선택적_ 으로 Azure Active Directory를 사용할 수 있습니다. **직접 연결 모드의 보류 중인 가용성**|
|**RBAC(역할 기반 액세스 제어)**|Kubernetes API에서 Kubernetes RBAC를 사용 합니다. 데이터베이스 인스턴스에 SQL 및 Postgres RBAC를 사용 합니다.|필요에 따라 Azure Active Directory 및 Azure RBAC와 통합할 수 있습니다. **직접 연결 모드의 보류 중인 가용성**|
|**Azure Defender**|지원되지 않음|미래 예정|

## <a name="connectivity-requirements"></a>연결 요구 사항

**일부 기능에는 Azure에 대 한 연결이 필요 합니다.**

**Azure와의 모든 통신은 항상 사용자 환경에서 시작 됩니다.** 이는 Azure Portal 사용자가 시작 하는 작업의 경우에도 마찬가지입니다.  이 경우 Azure에서 큐에 대기 중인 작업이 효과적입니다.  사용자 환경의 에이전트는 Azure와의 통신을 시작 하 여 큐에 있는 작업을 확인 하 고, 작업을 실행 하 고, 상태/완료/실패를 Azure에 다시 보고 합니다.

|**데이터 형식**|**방향**|**필수/선택**|**추가 비용**|**모드 필요**|**참고**|
|---|---|---|---|---|---|
|**컨테이너 이미지**|Microsoft Container Registry-> 고객|필수|아니요|간접 또는 직접|컨테이너 이미지는 소프트웨어를 배포 하는 방법입니다.  인터넷을 통해 MCR (Microsoft Container Registry)에 연결할 수 있는 환경에서는 MCR에서 직접 컨테이너 이미지를 끌어올 수 있습니다.  배포 환경에 직접 연결 되지 않은 경우 MCR에서 이미지를 가져와서 배포 환경의 개인 컨테이너 레지스트리로 푸시할 수 있습니다.  만든 시간에 MCR 대신 개인 컨테이너 레지스트리에서 가져오도록 생성 프로세스를 구성할 수 있습니다. 이는 자동화 된 업데이트에도 적용 됩니다.|
|**리소스 인벤토리**|고객 환경-Azure >|필수|아니요|간접 또는 직접|데이터 컨트롤러, 데이터베이스 인스턴스 (PostgreSQL 및 SQL)의 인벤토리는 청구를 위해 Azure에 유지 되며, 모든 데이터 컨트롤러 및 데이터베이스 인스턴스의 인벤토리를 한 곳에 만드는 목적으로 azure Arc data services를 사용 하는 환경이 둘 이상인 경우에 특히 유용 합니다.  인스턴스가 프로 비전 되 면 프로 비전 해제, 규모 확장/축소, Azure에서 인벤토리가 업데이트 됩니다.|
|**청구 원격 분석 데이터**|고객 환경-Azure >|필수|아니요|간접 또는 직접|청구를 위해 데이터베이스 인스턴스 사용률을 Azure로 보내야 합니다.  미리 보기 기간에는 Azure Arc 사용 데이터 서비스에 대 한 비용이 없습니다.|
|**데이터 및 로그 모니터링**|고객 환경-Azure >|옵션|데이터 볼륨에 따라 달라질 수도 있습니다 ( [Azure Monitor 가격 책정](https://azure.microsoft.com/en-us/pricing/details/monitor/)참조).|간접 또는 직접|여러 환경에서 데이터를 한 곳으로 집계 하는 Azure Monitor에 로컬로 수집 되는 모니터링 데이터와 로그를 전송 하 고 Azure Machine Learning의 데이터를 사용 하 여 경고와 같은 Azure Monitor 서비스를 사용할 수도 있습니다.|
|**Azure 역할 기반 Access Control (Azure RBAC)**|고객 환경-Azure > 고객 환경 >|선택 사항|아니요|직접만|Azure RBAC를 사용 하려는 경우 항상 Azure를 사용 하 여 연결을 설정 해야 합니다.  Azure RBAC를 사용 하지 않으려는 경우 로컬 Kubernetes RBAC를 사용할 수 있습니다.  **직접 연결 모드의 보류 중인 가용성**|
|**Azure AD(Active Directory)**|고객 환경-Azure > 고객 환경 >|옵션|가능 하지만 Azure AD에 대 한 비용을 이미 지불 했을 수 있습니다.|직접만|인증에 Azure AD를 사용 하려는 경우 항상 Azure를 사용 하 여 연결을 설정 해야 합니다. 인증에 Azure AD를 사용 하지 않으려는 경우 Active Directory를 통해 ADFS (Active Directory Federation Services)를 사용할 수 있습니다. **직접 연결 모드의 보류 중인 가용성**|
|**Backup 및 복원**|고객 환경-고객 환경 >|필수|아니요|직접 또는 간접|백업 및 복원 서비스는 로컬 저장소 클래스를 가리키도록 구성할 수 있습니다. |
|**Azure 백업-장기 보존**| 고객 환경-Azure > | 옵션| Azure storage의 경우 예 | 직접만 |장기 오프 사이트 백업의 경우에는 Azure Backup 로컬로 사용 되는 백업을 전송 하 고 복원에 대 한 로컬 환경으로 다시 가져올 수 있습니다. **직접 연결 모드의 보류 중인 가용성**|
|**Azure Defender 보안 서비스**|고객 환경-Azure > 고객 환경 >|옵션|예|직접만|**직접 연결 모드의 보류 중인 가용성**|
|**Azure Portal에서 프로 비전 및 구성 변경**|고객 환경-Azure > 고객 환경 >|선택 사항|아니요|직접만|Azure Data Studio 또는를 사용 하 여 로컬에서 프로 비전 및 구성 변경을 수행할 수 있습니다 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .  직접 연결 모드에서는 Azure Portal를 프로 비전 하 고 구성을 변경할 수도 있습니다. **직접 연결 모드의 보류 중인 가용성**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>인터넷 주소, 포트, 암호화 및 프록시 서버 지원에 대 한 세부 정보

현재 미리 보기 단계에서는 간접적으로 연결 된 모드만 지원 됩니다. 이 모드에는 인터넷에서 사용할 수 있는 서비스에 대 한 세 개의 연결만 필요 합니다. 이러한 연결 항목은 다음과 같습니다.

- [MCR (Microsoft Container Registry)](#microsoft-container-registry-mcr)
- [Azure Resource Manager Api](#azure-resource-manager-apis)
- [Azure monitor Api](#azure-monitor-apis)

Azure에 대 한 모든 HTTPS 연결과 Microsoft Container Registry는 공식적으로 서명 되 고 검증 가능한 인증서를 사용 하 여 SSL/TLS를 사용 하 여 암호화 됩니다.

다음 섹션에서는 이러한 연결에 대 한 세부 정보를 제공 합니다. 

### <a name="microsoft-container-registry-mcr"></a>MCR (Microsoft Container Registry)

Microsoft Container Registry는 Azure Arc 사용 데이터 서비스 컨테이너 이미지를 호스팅합니다.  MCR에서 이러한 이미지를 가져와서 개인 컨테이너 레지스트리에 푸시하고 해당 개인 컨테이너 레지스트리에서 컨테이너 이미지를 가져오도록 데이터 컨트롤러 배포 프로세스를 구성할 수 있습니다.

#### <a name="connection-source"></a>연결 원본

컨테이너 이미지를 끌어오는 각 Kubernetes 노드의 Kubernetes kubelet입니다.

#### <a name="connection-target"></a>연결 대상

`mcr.microsoft.com`

#### <a name="protocol"></a>프로토콜

HTTPS

#### <a name="port"></a>포트

443

#### <a name="can-use-proxy"></a>프록시 사용 가능

예

#### <a name="authentication"></a>인증

None

### <a name="azure-resource-manager-apis"></a>Azure Resource Manager Api
Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 일부 기능을 위해 Azure Resource Manager api에 연결 하 여 Azure에서 데이터를 보내고 검색 Azure CLI.

#### <a name="connection-source"></a>연결 원본

Azure에 연결 하는 Azure CLI Azure Data Studio, 또는를 실행 하는 컴퓨터입니다 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .

#### <a name="connection-target"></a>연결 대상

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>프로토콜

HTTPS

#### <a name="port"></a>포트

443

#### <a name="can-use-proxy"></a>프록시 사용 가능

예

#### <a name="authentication"></a>인증 

Azure Active Directory

### <a name="azure-monitor-apis"></a>Azure monitor Api

Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 일부 기능을 위해 Azure Resource Manager api에 연결 하 여 Azure에서 데이터를 보내고 검색 Azure CLI.

#### <a name="connection-source"></a>연결 원본

또는를 실행 하는 컴퓨터에서 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 모니터링 메트릭 또는 로그를 Azure Monitor에 업로드 하는 Azure CLI.

#### <a name="connection-target"></a>연결 대상

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>프로토콜

HTTPS

#### <a name="port"></a>포트

443

#### <a name="can-use-proxy"></a>프록시 사용 가능

예

#### <a name="authentication"></a>인증 

Azure Active Directory

> [!NOTE]
> 현재 Grafana 및 Kibana 대시보드 및에서 데이터 컨트롤러 API에 대 한 모든 브라우저 HTTPS/443 연결은 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 자체 서명 된 인증서를 사용 하 여 암호화 됩니다.  이러한 SSL 연결의 암호화를 위해 사용자 고유의 인증서를 제공할 수 있는 기능을 나중에 사용할 수 있게 됩니다.

Azure Data Studio와 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] KUBERNETES API 서버 간의 연결은 사용자가 설정한 Kubernetes 인증 및 암호화를 사용 합니다.  Azure Data Studio 및을 사용 하는 각 사용자는 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Azure Arc 사용 데이터 서비스와 관련 된 여러 작업을 수행 하기 위해 KUBERNETES API에 대 한 인증 된 연결이 있어야 합니다.

