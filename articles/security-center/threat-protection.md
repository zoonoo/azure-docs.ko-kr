---
title: 위협 보호 및 Azure Security Center
description: 이 항목에서는 Azure Security Center의 위협 방지 기능으로 보호 되는 리소스에 대해 설명 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 79638f584f1c65b33f23a68f01dbe82878460cc2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234080"
---
# <a name="threat-protection-in-azure-security-center"></a>위협 보호 및 Azure Security Center

Security Center 사용자 환경의 모든 영역에서 위협을 발견 하면 경고를 생성 합니다. 이러한 경고는 영향을 받는 리소스, 제안 된 수정 단계에 대 한 세부 정보를 설명 하 고, 경우에 따라 논리 앱을 트리거하는 옵션을 설명 합니다.

Azure Security Center의 위협 방지는 사용자 환경에 대 한 포괄적인 방어 기능을 제공 합니다.

* **Azure 계산 리소스에 대 한 위협 방지**: Windows 컴퓨터, Linux 컴퓨터, Azure App Service 및 azure 컨테이너

* **Azure 데이터 리소스에 대 한 위협 방지**: SQL Database 및 SQL Data Warehouse, Azure Storage 및 Azure Cosmos DB

* **Azure 서비스 계층에 대 한 위협 방지**: azure 네트워크 계층, azure 관리 계층 (Azure Resource Manager) (미리 보기) 및 Azure Key Vault (미리 보기)

경고가 Security Center에 의해 생성 되거나 다른 보안 제품의 Security Center에서 수신 되는지 여부를 내보낼 수 있습니다. Azure 센티널 (또는 타사 SIEM) 또는 기타 외부 도구로 경고를 내보내려면 [SIEM으로 경고 내보내기](continuous-export.md)의 지침을 따르세요. 

> [!TIP]
> Security Center의 위협 방지 기능을 사용 하려면 해당 하는 작업을 포함 하는 구독에 표준 가격 책정 계층을 적용 해야 합니다.
>
> 저장소 계정 및 PaaS SQL/MySQL/PG 서버에 대 한 Security Center의 위협 방지는 현재 리소스 수준에서 사용 하도록 설정 하 여 개별 저장소/s q m 작업을 보호할 수 있는 유일한 작업입니다.



## <a name="threat-protection-for-windows-machines"></a>Windows 컴퓨터에 대 한 위협 방지<a name="windows-machines"></a>

Azure Security Center는 Azure 서비스와 통합 되어 Windows 기반 컴퓨터를 모니터링 하 고 보호 합니다. Security Center은 이러한 모든 서비스의 경고 및 수정 제안을 사용 하기 쉬운 형식으로 제공 합니다.

* **Microsoft defender atp** <a name="windows-atp"></a> -Security Center microsoft defender atp (Advanced Threat protection)와 통합 하 여 클라우드 워크 로드 보호 플랫폼을 확장 합니다. 포괄적인 EDR (끝점 검색 및 응답) 기능을 제공 합니다.

    > [!IMPORTANT]
    > Microsoft Defender ATP 센서는 Security Center를 사용 하는 Windows server에서 자동으로 사용 하도록 설정 됩니다.

    Microsoft Defender ATP가 위협을 감지 하면 경고를 트리거합니다. 경고는 Security Center 대시보드에 표시 됩니다. 대시보드에서 Microsoft Defender ATP 콘솔로 피벗을 수행 하 고 자세한 조사를 수행 하 여 공격의 범위를 확인할 수 있습니다. Microsoft Defender ATP에 대 한 자세한 내용은 [Microsoft DEFENDER atp 서비스에 서버](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)등록을 참조 하세요.

* **크래시 덤프 분석** <a name="windows-dump"></a> -소프트웨어가 충돌 하는 경우 크래시 덤프는 충돌 시 메모리의 일부를 캡처합니다.

    충돌로 인해 맬웨어가 발생 했거나 맬웨어가 있을 수 있습니다. 보안 제품에서 검색 되지 않도록 하기 위해 다양 한 형태의 맬웨어가 fileless 공격을 사용 하 여 디스크에 기록 하거나 디스크에 기록 하는 소프트웨어 구성 요소를 암호화 하는 것을 방지 합니다. 이러한 유형의 공격은 기존의 디스크 기반 방법을 사용 하 여 감지 하기 어렵습니다.

    그러나 메모리 분석을 사용 하면 이러한 종류의 공격을 감지할 수 있습니다. 크래시 덤프의 메모리를 분석 하 여 공격에 사용 되는 기술을 감지할 수 Security Center. 예를 들어 공격에서 소프트웨어의 취약성을 악용 하 고 기밀 데이터에 액세스 하 고 은밀가 손상 된 컴퓨터 내에서 지속 될 수 있습니다. 이 작업은 호스트에 대 한 성능에 최소한의 영향을 주므로 Security Center 합니다.

    크래시 덤프 분석 경고에 대 한 자세한 내용은 [경고의 참조 테이블](alerts-reference.md#alerts-windows)을 참조 하세요.

* **Fileless 공격 감지** <a name="windows-fileless"></a> -끝점을 대상으로 하는 Fileless 공격이 일반적입니다. 검색을 방지 하기 위해 fileless 공격은 악성 페이로드를 메모리에 삽입 합니다. 공격자 페이로드는 손상 된 프로세스의 메모리 내에 유지 되며 다양 한 악의적인 작업을 수행 합니다.

    Fileless 공격 감지를 통해 자동화 된 메모리 법적 기술은 fileless 공격 도구 키트, 기술 및 동작을 식별 합니다. 이 솔루션은 런타임에 컴퓨터를 정기적으로 검색 하 고 보안에 중요 한 프로세스의 메모리에서 직접 정보를 추출 합니다.

    악용에 대 한 증거, 코드 삽입 및 악성 페이로드의 실행을 찾습니다. Fileless 공격 감지는 경고 심사, 상관 관계 및 다운스트림 응답 시간을 가속화 하기 위한 자세한 보안 경고를 생성 합니다. 이 접근 방식은 이벤트 기반 EDR 솔루션을 보완 하 여 더 큰 검색 검사를 제공 합니다.

    Fileless 공격 감지 경고에 대 한 자세한 내용은 [경고의 참조 테이블](alerts-reference.md#alerts-windows)을 참조 하세요.

> [!TIP]
> [Azure Security Center 플레이 북: 보안 경고](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)를 다운로드 하 여 Windows 경고를 시뮬레이션할 수 있습니다.






## <a name="threat-protection-for-linux-machines"></a>Linux 컴퓨터에 대 한 위협 방지<a name="linux-machines"></a>

Security Center는 가장 일반적인 Linux 감사 프레임 워크 중 하나인 **auditd**를 사용 하 여 linux 컴퓨터에서 감사 레코드를 수집 합니다. auditd는 주요 커널에 상주 합니다. 

* **Linux auditd 경고 및 Log Analytics 에이전트 통합** <a name="linux-auditd"></a> -auditd 시스템은 시스템 호출 모니터링을 담당 하는 커널 수준 하위 시스템으로 구성 됩니다. 지정 된 규칙 집합을 기준으로 필터링 하 고이에 대 한 메시지를 소켓에 씁니다. Security Center는 Log Analytics 에이전트 내에서 auditd 패키지의 기능을 통합 합니다. 이러한 통합을 통해 모든 지원 되는 Linux 배포판에서 사전 요구 사항 없이 auditd 이벤트를 수집할 수 있습니다.

    auditd 레코드는 Linux 에이전트에 대 한 Log Analytics agent를 사용 하 여 수집 하 고, 보강, 이벤트에 집계 됩니다. Security Center은 Linux 신호를 사용 하 여 클라우드 및 온-프레미스 Linux 컴퓨터에서 악성 동작을 검색 하는 새로운 분석을 지속적으로 추가 합니다. Windows 기능과 유사 하 게 이러한 분석은 의심 스러운 프로세스, 수상한 로그인 시도, 커널 모듈 로드 및 기타 작업에 걸쳐 있습니다. 이러한 활동은 컴퓨터가 공격을 받고 있거나 위반 되었음을 나타낼 수 있습니다.  

    Linux 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-linux)을 참조 하세요.

> [!TIP]
> [Azure Security Center 플레이 북: linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)검색을 다운로드 하 여 linux 경고를 시뮬레이션할 수 있습니다.





## <a name="threat-protection-for-azure-app-service"></a>Azure App Service에 대 한 위협 방지<a name="app-services"></a>

> [!NOTE]
> 이 서비스는 현재 Azure 정부 및 소 버린 클라우드 지역에서 사용할 수 없습니다.

Security Center는 클라우드의 규모를 사용 하 여 App Service에서 실행 되는 응용 프로그램을 대상으로 하는 공격을 식별 합니다. 공격자는 웹 응용 프로그램을 검색 하 여 약점을 찾아 활용 합니다. 특정 환경으로 라우팅되기 전에 Azure에서 실행 중인 응용 프로그램에 대 한 요청은 여러 게이트웨이를 통해 수행 되며,이를 검사 하 고 기록 합니다. 그런 다음이 데이터를 사용 하 여 악용 및 공격자를 식별 하 고 나중에 사용 될 새 패턴을 알아봅니다.

Azure가 클라우드 공급자 인 표시 유형을 사용 하 여 Security Center App Service 내부 로그를 분석 하 여 여러 대상에 대 한 공격 방법론을 식별 합니다. 예를 들어, 방법론에는 광범위 한 검색 및 분산 공격이 포함 됩니다. 이러한 유형의 공격은 일반적으로 Ip의 작은 하위 집합에서 제공 되며 여러 호스트에서 비슷한 끝점에 대 한 탐색 패턴을 보여 줍니다. 공격에 취약 한 페이지 또는 플러그 인을 검색 하 고 있으며 단일 호스트의 관점에서 식별할 수 없습니다.

Windows 기반 App Service 계획을 실행 하는 경우에는 Security Center 기본 샌드박스 및 Vm에도 액세스할 수 있습니다. 위에서 언급 한 로그 데이터와 함께 인프라는 circulating의 새로운 공격에서 고객 시스템의 손상에 이르기까지 이야기를 알려줄 수 있습니다. 따라서 웹 앱이 악용 된 후 Security Center 배포 된 경우에도 지속적인 공격을 검색할 수 있습니다.

Azure App Service 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-azureappserv)을 참조 하세요.

App Service 계획에 대 한 자세한 내용은 [App Service 요금제](https://azure.microsoft.com/pricing/details/app-service/plans/)를 참조 하세요.





## <a name="threat-protection-for-azure-containers"></a>Azure 컨테이너에 대 한 위협 방지<a name="azure-containers"></a>

> [!NOTE]
> 이 서비스는 현재 Azure 정부 및 소 버린 클라우드 지역에서 사용할 수 없습니다.

Security Center는 컨테이너 화 된 환경에 대 한 실시간 위협 방지 기능을 제공 하 고 의심 스러운 활동에 대 한 경고를 생성 합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

Security Center는 다음과 같은 다양 한 수준에서 위협 방지 기능을 제공 합니다. 

* **호스트 수준** -Security Center의 에이전트 (표준 계층에서 사용 가능, 자세한 내용은 [가격 책정](security-center-pricing.md) 참조)에서 의심 스러운 활동에 대 한 Linux를 모니터링 합니다. 에이전트는 노드 또는 해당 노드에서 실행 중인 컨테이너에서 시작 된 의심 스러운 활동에 대 한 경고를 트리거합니다. 이러한 작업의 예로는 알려진 의심 스러운 IP 주소에 대 한 웹 셸 검색 및 연결이 있습니다.

    컨테이너 화 된 환경의 보안을 보다 심층적으로 파악 하기 위해 에이전트는 컨테이너 관련 분석을 모니터링 합니다. 권한 있는 컨테이너 만들기, API 서버에 대 한 의심 스러운 액세스 및 Docker 컨테이너 내에서 실행 되는 SSH (Secure Shell) 서버와 같은 이벤트에 대 한 경고를 트리거합니다.

    >[!IMPORTANT]
    > 호스트에 에이전트를 설치 하지 않도록 선택 하는 경우 위협 방지 혜택 및 보안 경고의 하위 집합만 받게 됩니다. 네트워크 분석과 악의적인 서버와의 통신과 관련 된 경고를 계속 받을 수 있습니다.

    호스트 수준 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-containerhost)을 참조 하세요.


* **AKS 클러스터 수준**에서 위협 보호는 Kubernetes의 감사 로그 분석을 기반으로 합니다. 이 **에이전트** 없는 모니터링을 사용 하도록 설정 하려면 **가격 책정 & 설정** 페이지에서 구독에 Kubernetes 옵션을 추가 합니다 ( [가격 책정](security-center-pricing.md)참조). 이 수준에서 경고를 생성 하기 위해 Security Center는 AKS에서 검색 된 로그를 사용 하 여 AKS 관리 서비스를 모니터링 합니다. 이 수준에서 발생 하는 이벤트의 예로는 노출 된 Kubernetes 대시보드, 높은 권한이 있는 역할 만들기, 중요 한 탑재 생성 등이 있습니다.

    >[!NOTE]
    > Security Center는 구독 설정에서 Kubernetes 옵션을 사용 하도록 설정한 후 발생 하는 Azure Kubernetes 서비스 작업 및 배포에 대 한 보안 경고를 생성 합니다. 

    AKS 클러스터 수준 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-akscluster)을 참조 하세요.

또한 글로벌 보안 연구원 팀은 지속적으로 위협 환경을 모니터링 합니다. 검색 된 컨테이너 관련 경고 및 취약성을 추가 합니다.

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)의 지침에 따라 컨테이너 경고를 시뮬레이션할 수 있습니다.








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>SQL Database 및 SQL Data Warehouse에 대 한 위협 방지<a name="data-sql"></a>

Azure SQL Database에 대 한 Advanced Threat Protection은 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다.

의심 스러운 데이터베이스 활동, 잠재적 취약성, SQL 삽입 공격 및 비정상적인 데이터베이스 액세스 및 쿼리 패턴이 있는 경우 경고가 표시 됩니다.

Azure SQL Database 및 SQL에 대 한 advanced Threat Protection은 azure Virtual Machines의 Azure SQL Database Azure SQL Database, 관리 되는 인스턴스, Azure SQL Data Warehouse 데이터베이스 및 SQL server에 대 한 고급 SQL 보안 기능을 제공 하는 [ad (Advanced Data Security)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 통합 패키지의 일부입니다.

자세한 내용은 다음을 참조하세요.

* [Azure SQL Database에 대해 Advanced Threat Protection을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Azure Virtual Machines에서 SQL server에 대 한 Advanced Threat Protection을 사용 하도록 설정 하는 방법](security-center-iaas-advanced-data.md)
* [SQL Database 및 SQL Data Warehouse에 대 한 위협 방지 경고 목록](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Azure Storage에 대 한 위협 방지<a name="azure-storage"></a>

저장소에 대 한 Advanced Threat Protection은 저장소 계정에 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 합니다. 이 보호 계층을 사용 하면 보안 전문가가 아니어도 위협을 해결 하 고 보안 모니터링 시스템을 관리할 수 있습니다.

Azure Storage에 대 한 Advanced threat protection은 현재 [Blob Storage](https://azure.microsoft.com/services/storage/blobs/)에서만 사용할 수 있습니다. 

이 서비스는 모든 공용 클라우드 및 미국 정부 클라우드에서 사용할 수 있지만 다른 소 버린 또는 Azure 정부 클라우드 지역에서는 사용할 수 없습니다.

무료 30 일 평가판을 비롯 한 가격 정보는 [Azure Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조 하세요.

자세한 내용은 다음을 참조하세요.

* [Azure Storage에 대해 Advanced Threat Protection을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure Storage에 대 한 위협 방지 경고 목록](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)의 지침에 따라 Azure Storage 경고를 시뮬레이션할 수 있습니다.




## <a name="threat-protection-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 위협 방지<a name="cosmos-db"></a>

Azure Cosmos DB 경고는 비정상적이 고 잠재적으로 유해한 시도로 인해 Azure Cosmos DB 계정에 액세스 하거나 악용할 때 생성 됩니다.

자세한 내용은 다음을 참조하세요.

* [Azure Cosmos DB에 대 한 Advanced Threat Protection (미리 보기)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB에 대 한 위협 방지 경고 목록 (미리 보기)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Azure 네트워크 계층에 대 한 위협 방지<a name="network-layer"></a>

Security Center 네트워크 계층 분석은 Azure core 라우터가 수집 하는 패킷 헤더 인 샘플 [Ipfix 데이터](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)를 기반으로 합니다. 이 데이터 피드에 따라 Security Center는 machine learning 모델을 사용 하 여 악성 트래픽 활동을 식별 하 고 플래그를 지정 합니다. 또한 Security Center는 Microsoft 위협 인텔리전스 데이터베이스를 사용 하 여 IP 주소를 보강 합니다.

일부 네트워크 구성에서 의심 스러운 네트워크 활동에 대 한 경고를 생성 하는 Security Center 제한할 수 있습니다. 네트워크 경고를 생성 Security Center를 확인 하려면 다음을 확인 하십시오.

- 가상 컴퓨터에 공용 IP 주소가 있거나, 공용 ip 주소를 사용 하는 부하 분산 장치에 있습니다.

- 가상 머신의 네트워크 송신 트래픽이 외부 ID 솔루션에 의해 차단 되지 않습니다.

- 가상 머신이 의심 스러운 통신이 발생 한 전체 시간에 대해 동일한 IP 주소를 할당 받았습니다. 이는 관리 되는 서비스의 일부로 생성 된 Vm (예: AKS, Databricks)에도 적용 됩니다.

Azure 네트워크 계층 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-azurenetlayer)을 참조 하세요.

Security Center 네트워크 관련 신호를 사용 하 여 위협 방지를 적용 하는 방법에 대 한 자세한 내용은 [Security Center에서 추론 DNS](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)검색을 참조 하세요.



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Azure 관리 계층 (Azure Resource Manager)에 대 한 위협 방지 (미리 보기)<a name ="management-layer"></a>

Azure Resource Manager 기반 Security Center의 보호 계층은 현재 미리 보기로 제공 됩니다.

Security Center는 Azure Resource Manager 이벤트를 사용 하 여 추가 보호 계층을 제공 하며,이는 Azure의 제어 평면으로 간주 됩니다. Security Center Azure Resource Manager 레코드를 분석 하 여 Azure 구독 환경에서 비정상 이거나 잠재적으로 유해한 작업을 검색 합니다.

Azure Resource Manager (미리 보기) 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-azureresourceman)을 참조 하세요.



>[!NOTE]
> 이전 분석의 일부는 Microsoft Cloud App Security에 의해 제공 됩니다. 이러한 분석을 활용 하려면 Cloud App Security 라이선스를 활성화 해야 합니다. Cloud App Security 라이선스가 있는 경우 이러한 경고는 기본적으로 사용 하도록 설정 됩니다. 경고를 사용 하지 않도록 설정 하려면:
>
> 1. **Security Center** 블레이드에서 **보안 정책**을 선택 합니다. 변경 하려는 구독에 대해 **설정 편집**을 선택 합니다.
> 2. **위협 검색**을 선택 합니다.
> 3. **통합 사용**에서 Microsoft Cloud App Security에서 **내 데이터에 액세스 하도록 허용**을 선택 취소 하 고 **저장**을 선택 합니다.

>[!NOTE]
>Security Center는 보안과 관련 된 고객 데이터를 해당 리소스와 동일한 지역에 저장 합니다. Microsoft에서 아직 리소스의 지역에 Security Center 배포 하지 않은 경우에는 데이터를 미국에 저장 합니다. Cloud App Security 사용 하도록 설정 된 경우이 정보는 Cloud App Security의 지리적 위치 규칙에 따라 저장 됩니다. 자세한 내용은 [비 지역 서비스에 대 한 데이터 저장소](https://azuredatacentermap.azurewebsites.net/)를 참조 하세요.








## <a name="threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault에 대 한 위협 방지 (미리 보기)<a name="azure-keyvault"></a>

> [!NOTE]
> 이 서비스는 현재 Azure 정부 및 소 버린 클라우드 지역에서 사용할 수 없습니다.

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 

Azure Security Center에는 Azure Key Vault에 대 한 Azure 네이티브 및 고급 위협 방지 기능이 포함 되어 있으며, 추가 보안 인텔리전스 계층을 제공 합니다. Security Center은 Key Vault 계정을 액세스 하거나 악용 하려는 비정상적인 잠재적으로 유해한 시도를 검색 합니다. 이 보호 계층을 사용 하면 보안 전문가가 아니더라도 타사 보안 모니터링 시스템을 관리할 필요 없이 위협을 해결할 수 있습니다.  

비정상적인 활동이 발생 하면 Security Center는 경고를 표시 하 고 필요에 따라 구독 관리자에 게 전자 메일을 통해 보냅니다. 이러한 경고는 의심 스러운 활동의 세부 정보와 위협을 조사 하 고 수정 하는 방법에 대 한 권장 사항을 포함 합니다. 

Azure Key Vault 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-azurekv)을 참조 하세요.





## <a name="threat-protection-for-other-microsoft-services"></a>다른 Microsoft 서비스에 대 한 위협 방지<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF에 대 한 위협 방지<a name="azure-waf"></a>

Azure Application Gateway는 일반적인 악용 및 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호하는 WAF(웹 애플리케이션 방화벽)를 제공합니다.

일반적으로 알려진 취약성을 악용하여 웹 애플리케이션을 공격하는 악의적인 사례가 점점 늘어나고 있습니다. Application Gateway WAF는 공개 웹 응용 프로그램 보안 프로젝트의 핵심 규칙 집합 3.0 또는 2.2.9을 기반으로 합니다. WAF는 새로운 취약점 으로부터 보호 하기 위해 자동으로 업데이트 됩니다. 

Azure WAF에 대 한 라이선스가 있는 경우 WAF 경고는 추가 구성이 필요 하지 않은 Security Center으로 스트리밍됩니다. WAF에 의해 생성 되는 경고에 대 한 자세한 내용은 [웹 응용 프로그램 방화벽 CRS 규칙 그룹 및 규칙](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)을 참조 하세요.


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS Protection에 대 한 위협 방지<a name="azure-ddos"></a>

DDoS (분산 서비스 거부) 공격을 쉽게 실행 하는 것으로 알려져 있습니다. 특히 응용 프로그램을 클라우드로 이동 하는 경우 매우 중요 한 보안 문제가 됩니다. 

DDoS 공격은 애플리케이션의 리소스를 소진시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 만듭니다. DDoS 공격은 인터넷을 통해 도달할 수 있는 모든 끝점을 대상으로 지정할 수 있습니다.

DDoS 공격 으로부터 보호 하려면 Azure DDoS Protection에 대 한 라이선스를 구매 하 고 응용 프로그램 디자인 모범 사례를 준수 하는지 확인 합니다. DDoS Protection는 서로 다른 서비스 계층을 제공 합니다. 자세한 내용은 [Azure DDoS Protection 개요](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)를 참조 하세요.

Azure DDoS Protection 경고 목록은 [경고의 참조 테이블](alerts-reference.md#alerts-azureddos)을 참조 하세요.


## <a name="next-steps"></a>다음 단계
이러한 위협 방지 기능의 보안 경고에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [모든 Azure Security Center 경고에 대 한 참조 테이블](alerts-reference.md)
* [Azure Security Center의 보안 경고](security-center-alerts-overview.md)
* [Azure Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md)
* [보안 경고 및 권장 사항 내보내기 (미리 보기)](continuous-export.md)