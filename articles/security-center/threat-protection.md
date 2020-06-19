---
title: 위협 보호 및 Azure Security Center
description: 이 항목에서는 Azure Security Center의 위협 방지 기능으로 보호되는 리소스에 대해 설명합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 850b06153a25020f36a4c7df1863e5a576495f3b
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744168"
---
# <a name="threat-protection-in-azure-security-center"></a>위협 보호 및 Azure Security Center

Security Center는 사용자 환경의 모든 영역에서 위협을 탐지하면 경고를 생성합니다. 이러한 경고는 영향을 받는 리소스의 세부 정보, 제안된 수정 단계 및 경우에 따라 응답으로 논리 앱을 트리거하는 옵션을 설명합니다.

Azure Security Center의 위협 방지는 사용자 환경에 대한 포괄적인 방어 기능을 제공합니다.

* **Azure 컴퓨팅 리소스에 대한 위협 방지**: Windows 컴퓨터, Linux 컴퓨터, Azure App Service 및 Azure 컨테이너

* **Azure 데이터 리소스에 대한 위협 방지**: SQL Database/SQL Data Warehouse, Azure Storage 및 Azure Cosmos DB

* **Azure 서비스 계층에 대한 위협 방지**: Azure 네트워크 계층, Azure 관리 계층(Azure Resource Manager)(미리 보기) 및 Azure Key Vault(미리 보기)

Security Center에서 경고를 생성하든 Security Center에서 다른 보안 제품으로부터 경고를 받든 이를 내보낼 수 있습니다. 경고를 Azure Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM으로 경고 내보내기](continuous-export.md)의 지침을 따릅니다. 

> [!NOTE]
> 서로 다른 원본의 경고가 표시되는 데 각각 다른 시간이 걸릴 수 있습니다. 예를 들어 네트워크 트래픽을 분석해야 하는 경고를 표시하는 경우 가상 머신에서 실행되는 의심스러운 프로세스와 관련된 경고보다 더 오래 걸릴 수 있습니다.

> [!TIP]
> Security Center의 위협 방지 기능을 사용하도록 설정하려면 표준 가격 책정 계층을 해당 워크로드가 포함된 구독에 적용해야 합니다.
>
> **Azure Storage 계정**에 대한 위협 방지는 구독 수준 또는 리소스 수준에서 사용하도록 설정할 수 있습니다.
> **Azure SQL Database SQL 서버**에 대한 위협 방지는 구독 수준 또는 리소스 수준에서 사용하도록 설정할 수 있습니다.
> **Azure Database for MariaDB/ MySQL/ PostgreSQL**에 대한 위협 방지는 리소스 수준에서만 사용하도록 설정할 수 있습니다.



## <a name="threat-protection-for-windows-machines"></a>Windows 컴퓨터에 대한 위협 방지 <a name="windows-machines"></a>

Azure Security Center는 Azure 서비스와 통합하여 Windows 기반 컴퓨터를 모니터링하고 보호합니다. Security Center는 이러한 모든 서비스의 경고 및 수정 제안을 사용하기 쉬운 형식으로 제공합니다.

* **Microsoft Defender ATP(Advanced Threat Protection)** <a name="windows-atp"></a> - Security Center는 Microsoft Defender ATP(Advanced Threat Protection)와 통합하여 클라우드 워크로드 보호 플랫폼을 확장합니다. 또한 포괄적인 EDR(엔드포인트 검색 및 응답) 기능을 제공합니다.

    > [!IMPORTANT]
    > Microsoft Defender ATP 센서는 Security Center를 사용하는 Windows 서버에서 자동으로 사용하도록 설정됩니다.

    Microsoft Defender ATP는 위협을 탐지하면 경고를 트리거합니다. 이 경고는 Security Center 대시보드에 표시됩니다. 대시보드에서 Microsoft Defender ATP 콘솔로 피벗하고 자세히 조사하여 공격 범위를 확인할 수 있습니다. Microsoft Defender ATP에 대한 자세한 내용은 [Microsoft Defender ATP 서비스에 서버 온보딩](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)을 참조하세요.

* **파일리스 공격 탐지** <a name="windows-fileless"></a> - 일반적으로 엔드포인트를 대상으로 하는 파일리스 공격입니다. 파일리스 공격은 탐지를 피하기 위해 악성 페이로드를 메모리에 삽입합니다. 공격자 페이로드는 손상된 프로세스의 메모리 내에서 유지되며 광범위한 악의적인 활동을 수행합니다.

    파일리스 공격 탐지를 통해 자동화된 메모리 포렌식 기술은 파일리스 공격 도구 키트, 기술 및 동작을 식별합니다. 이 솔루션은 런타임에 컴퓨터를 정기적으로 검사하고, 보안에 중요한 프로세스의 메모리에서 직접 인사이트를 추출합니다.

    악성 페이로드의 악용, 코드 삽입 및 실행에 대한 증거를 찾습니다. 파일리스 공격 탐지는 자세한 보안 경고를 생성하여 경고 심사, 상관 관계 및 다운스트림 응답 시간을 가속화합니다. 이 방법은 이벤트 기반 EDR 솔루션을 보완하여 탐지 범위를 더 확장합니다.

    파일리스 공격 탐지 경고에 대한 자세한 내용은 [경고 참조 표](alerts-reference.md#alerts-windows)를 참조하세요.

> [!TIP]
> [Azure Security Center 플레이북: 보안 경고](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)를 다운로드하여 Windows 경고를 시뮬레이션할 수 있습니다.






## <a name="threat-protection-for-linux-machines"></a>Linux 컴퓨터에 대한 위협 방지 <a name="linux-machines"></a>

Security Center는 가장 일반적인 Linux 감사 프레임워크 중 하나인 **auditd**를 사용하여 Linux 컴퓨터에서 감사 레코드를 수집합니다. auditd는 주요 커널에 상주합니다. 

* **Linux auditd 경고 및 Log Analytics 에이전트 통합** <a name="linux-auditd"></a> - auditd 시스템은 시스템 호출을 모니터링해야 하는 커널 수준 하위 시스템으로 구성됩니다. 지정된 규칙 세트를 기준으로 필터링하고 이에 대한 메시지를 소켓에 씁니다. Security Center는 Log Analytics 에이전트 내에서 auditd 패키지의 기능을 통합합니다. 이러한 통합을 통해 사전 요구 사항 없이 지원되는 모든 Linux 배포에서 auditd 이벤트를 수집할 수 있습니다.

    auditd 레코드는 Linux 에이전트용 Log Analytics 에이전트를 사용하여 이벤트에 수집, 보강 및 집계됩니다. Security Center는 Linux 신호를 사용하여 클라우드 및 온-프레미스 Linux 컴퓨터에서 악의적인 동작을 탐지하는 새 분석을 지속적으로 추가합니다. Windows 기능과 비슷하게 이러한 분석은 의심스러운 프로세스, 의심스러운 로그인 시도, 커널 모듈 로드 및 기타 활동 전반에 걸쳐 있습니다. 이러한 활동은 컴퓨터가 공격을 받고 있거나 위반되었음을 나타낼 수 있습니다.  

    Linux 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-linux)를 참조하세요.

> [!TIP]
> [Azure Security Center 플레이북: Linux 탐지](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)를 다운로드하여 Linux 경고를 시뮬레이션할 수 있습니다.





## <a name="threat-protection-for-azure-app-service"></a>Azure App Service에 대한 위협 방지 <a name="app-services"></a>

> [!NOTE]
> 이 서비스는 현재 Azure 정부 및 소버린 클라우드 지역에서 사용할 수 없습니다.

Security Center는 클라우드 규모를 사용하여 App Service를 통해 실행되는 애플리케이션을 대상으로 하는 공격을 식별합니다. 공격자는 웹 애플리케이션을 검색하여 약점을 찾아 악용합니다. 특정 환경으로 라우팅되기 전에 Azure에서 실행되는 애플리케이션에 대한 요청은 여러 게이트웨이를 통과하면서 검사 및 기록됩니다. 그런 다음, 이 데이터는 악용 및 공격자를 식별하고, 나중에 사용할 새 패턴을 학습하는 데 사용됩니다.

Azure에서 클라우드 공급자로 제공하는 표시 유형을 사용하면 Security Center에서 App Service 내부 로그를 분석하여 여러 대상에 대한 공격 방법론을 식별합니다. 예를 들어 방법론에는 광범위한 검사 및 분산 공격이 포함됩니다. 이러한 유형의 공격은 일반적으로 작은 IP 하위 집합에서 발생하며, 여러 호스트에서 비슷한 엔드포인트로 크롤링하는 패턴을 보여 줍니다. 공격은 취약한 페이지 또는 플러그 인을 검색하며, 단일 호스트의 관점에서 식별할 수 없습니다.

Windows 기반 App Service 계획을 실행하는 경우 Security Center에서도 기본 샌드박스 및 VM에 액세스할 수 있습니다. 위에서 언급한 로그 데이터와 함께 인프라는 악순환되는 새 공격에서 고객 컴퓨터의 손상에 이르기까지 모든 것을 알려줄 수 있습니다. 따라서 웹앱이 악용된 후 Security Center를 배포하더라도 지속적인 공격을 탐지할 수 있습니다.

Azure App Service 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-azureappserv)를 참조하세요.

App Service 계획에 대한 자세한 내용은 [App Service 계획](https://azure.microsoft.com/pricing/details/app-service/plans/)을 참조하세요.





## <a name="threat-protection-for-azure-containers"></a>Azure 컨테이너에 대한 위협 방지 <a name="azure-containers"></a>

> [!NOTE]
> 이 서비스는 현재 Azure 정부 및 소버린 클라우드 지역에서 사용할 수 없습니다.

Security Center는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

Security Center에서 위협 방지를 제공하는 다양한 수준은 다음과 같습니다. 

* **호스트 수준** - Security Center의 에이전트(표준 계층에서 사용 가능, 자세한 내용은 [가격 책정](security-center-pricing.md) 참조)에서 Linux를 모니터링하여 의심스러운 활동을 탐지합니다. 에이전트는 노드 또는 노드에서 실행되는 컨테이너에서 시작된 의심스러운 활동에 대한 경고를 트리거합니다. 이러한 활동의 예로 웹 셸 탐지 및 알려진 의심스러운 IP 주소와의 연결이 있습니다.

    에이전트는 컨테이너화된 환경의 보안에 대한 더 심층적인 인사이트를 파악하기 위해 컨테이너 관련 분석을 모니터링합니다. 권한 있는 컨테이너 만들기, API 서버에 대한 의심스러운 액세스 및 Docker 컨테이너 내에서 실행되는 SSH(Secure Shell) 서버와 같은 이벤트에 대한 경고를 트리거합니다.

    >[!IMPORTANT]
    > 에이전트를 호스트에 설치하지 않도록 선택하는 경우 위협 방지 혜택 및 보안 경고의 하위 집합만 받게 됩니다. 네트워크 분석 및 악의적인 서버와의 통신과 관련된 경고는 계속 받을 수 있습니다.

    호스트 수준 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-containerhost)를 참조하세요.


* **AKS 클러스터 수준**의 위협 방지는 Kubernetes의 감사 로그 분석을 기반으로 합니다. 이 **에이전트 없는** 모니터링을 사용하도록 설정하려면 **가격 책정 및 설정** 페이지에서 Kubernetes 옵션을 구독에 추가합니다([가격 책정](security-center-pricing.md) 참조). 이 수준에서 경고를 생성하기 위해 Security Center는 AKS에서 검색한 로그를 사용하여 AKS 관리형 서비스를 모니터링합니다. 이 수준의 이벤트 예로 노출된 Kubernetes 대시보드, 높은 권한이 있는 역할 만들기 및 중요한 탑재 만들기가 있습니다.

    >[!NOTE]
    > Security Center는 구독 설정에서 Kubernetes 옵션을 사용하도록 설정한 후에 발생하는 Azure Kubernetes Service 작업 및 배포에 대한 보안 경고를 생성합니다. 

    AKS 클러스터 수준 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-akscluster)를 참조하세요.

또한 글로벌 보안 연구 팀에서는 위협 환경을 지속적으로 모니터링합니다. 검색된 컨테이너 관련 경고 및 취약성을 추가합니다.

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)의 지침에 따라 컨테이너 경고를 시뮬레이션할 수 있습니다.








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>SQL Database 및 SQL Data Warehouse에 대한 위협 방지 <a name="data-sql"></a>

Azure SQL Database용 Advanced Threat Protection은 비정상적이며 잠재적으로 유해한 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상 활동을 탐지합니다.

의심스러운 데이터베이스 활동, 잠재적 취약성 또는 SQL 삽입 공격, 비정상 데이터베이스 액세스 및 쿼리 패턴이 있으면 경고가 표시됩니다.

Azure SQL Database 및 SQL용 Advanced Threat Protection은 Azure SQL Database, Azure SQL Database 관리형 인스턴스, Azure SQL Data Warehouse 데이터베이스 및 Azure Virtual Machines의 SQL 서버를 포함하여 고급 SQL 보안 기능을 위한 [ADS(Advanced Data Security)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 통합 패키지의 일부입니다.

자세한 내용은 다음을 참조하세요.

* [Azure SQL Database용 Advanced Threat Protection을 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Azure Virtual Machines에서 SQL 서버용 Advanced Threat Protection을 사용하도록 설정하는 방법](security-center-iaas-advanced-data.md)
* [SQL Database 및 SQL Data Warehouse에 대한 위협 방지 경고 목록](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Azure Storage에 대한 위협 방지 <a name="azure-storage"></a>

Storage용 Advanced Threat Protection은 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. 이 보호 계층을 사용하면 보안 전문가가 아니더라도 위협을 처리할 수 있으며, 보안 모니터링 시스템을 관리할 수 있습니다.

Azure Storage용 Advanced Threat Protection은 현재 [Blob Storage](https://azure.microsoft.com/services/storage/blobs/)에만 사용할 수 있습니다. 

이 서비스는 모든 퍼블릭 클라우드 및 미국 정부 클라우드에서 사용할 수 있지만, 다른 소버린 또는 Azure Government 클라우드 지역에서는 사용할 수 없습니다.

30일 평가판을 포함한 자세한 가격 책정 정보는 [Azure Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

자세한 내용은 다음을 참조하세요.

* [Azure Storage용 Advanced Threat Protection을 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure Storage에 대한 위협 방지 경고 목록](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)의 지침에 따라 Azure Storage 경고를 시뮬레이션할 수 있습니다.




## <a name="threat-protection-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 위협 방지 <a name="cosmos-db"></a>

Azure Cosmos DB 경고는 Azure Cosmos DB 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도로 인해 생성됩니다.

자세한 내용은 다음을 참조하세요.

* [Azure Cosmos DB용 Advanced Threat Protection(미리 보기)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB에 대한 위협 방지 경고 목록(미리 보기)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Azure 네트워크 계층에 대한 위협 방지 <a name="network-layer"></a>

Security Center 네트워크 계층 분석은 Azure 핵심 라우터에서 수집하는 패킷 헤더인 [IPFIX 데이터](https://en.wikipedia.org/wiki/IP_Flow_Information_Export) 샘플을 기반으로 합니다. 이 데이터 피드를 기반으로 하여 Security Center에서 기계 학습 모델을 사용하여 악성 트래픽 활동을 식별하고 플래그를 지정합니다. 또한 Security Center에서 Microsoft 위협 인텔리전스 데이터베이스를 사용하여 IP 주소를 보강합니다.

일부 네트워크 구성에서는 Security Center에서 의심스러운 네트워크 활동에 대한 경고를 생성하지 않도록 제한할 수 있습니다. Security Center에서 네트워크 경고를 생성하려면 다음을 확인합니다.

- 가상 머신에서 공용 IP 주소를 사용하거나 가상 머신이 공용 IP 주소가 있는 부하 분산 장치에 있습니다.

- 가상 머신의 네트워크 송신 트래픽은 외부 IDS 솔루션에서 차단되지 않습니다.

- 의심스러운 통신이 발생한 전체 시간 동안 동일한 IP 주소가 가상 머신에 할당되었습니다. 이는 관리형 서비스(예: AKS, Databricks)의 일부로 만든 VM에도 적용됩니다.

Azure 네트워크 계층 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-azurenetlayer)를 참조하세요.

Security Center에서 네트워크 관련 신호를 사용하여 위협 방지를 적용하는 방법에 대한 자세한 내용은 [Security Center의 추론 DNS 탐지](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)를 참조하세요.



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a><a name ="management-layer">Azure 관리 계층(Azure Resource Manager)에 대한 위협 방지(미리 보기)</a>

Azure Resource Manager를 기반으로 하는 Security Center의 보호 계층은 현재 미리 보기에 있습니다.

Security Center는 Azure Resource Manager 이벤트를 사용하여 추가 보호 계층을 제공하며, 이는 Azure의 제어 평면으로 간주됩니다. Security Center는 Azure Resource Manager 레코드를 분석하여 Azure 구독 환경에서 비정상적이거나 잠재적으로 유해한 작업을 탐지합니다.

Azure Resource Manager(미리 보기) 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-azureresourceman)를 참조하세요.



>[!NOTE]
> 이전 분석의 일부는 Microsoft Cloud App Security에서 작동됩니다. 이러한 분석을 활용하려면 Cloud App Security 라이선스에 대한 정품 인증을 수행해야 합니다. Cloud App Security 라이선스가 있는 경우 이러한 경고는 기본적으로 사용하도록 설정됩니다. 경고를 사용하지 않도록 설정하려면 다음을 수행합니다.
>
> 1. **Security Center** 블레이드에서 **보안 정책**을 선택합니다. 변경하려는 구독에 대한 **설정 편집**을 선택합니다.
> 2. **위협 탐지**를 선택합니다.
> 3. **통합 사용** 아래에서 **Microsoft Cloud App Security에서 내 데이터에 액세스하도록 허용합니다.** 를 선택 취소한 다음, **저장**을 선택합니다.

>[!NOTE]
>Security Center는 보안과 관련된 고객 데이터를 리소스와 동일한 지역에 저장합니다. Microsoft에서 Security Center를 해당 리소스의 지역에 아직 배포하지 않은 경우 데이터는 미국에 저장됩니다. Cloud App Security를 사용하도록 설정되는 경우 이 정보는 Cloud App Security의 지리적 위치 규칙에 따라 저장됩니다. 자세한 내용은 [비지역 서비스에 대한 데이터 저장](https://azuredatacentermap.azurewebsites.net/)을 참조하세요.








## <a name="threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault에 대한 위협 방지(미리 보기)<a name="azure-keyvault"></a>

> [!NOTE]
> 이 서비스는 현재 Azure 정부 및 소버린 클라우드 지역에서 사용할 수 없습니다.

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 

Azure Security Center에는 Azure Key Vault에 대한 Azure 네이티브 고급 위협 방지 기능이 포함되어 있으며, 추가 보안 인텔리전스를 계층에 제공합니다. Security Center는 Key Vault 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. 이 보호 계층을 사용하면 보안 전문가가 아니거나 타사 보안 모니터링 시스템을 관리할 필요 없이 위협을 처리할 수 있습니다.  

비정상 활동이 발생하면 Security Center에서 경고를 표시하고 필요에 따라 이메일을 통해 구독 관리자에게 보냅니다. 이러한 경고에는 의심스러운 활동에 대한 세부 정보 및 위협을 조사하고 완화하는 방법에 대한 추천 사항이 포함됩니다. 

Azure Key Vault 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-azurekv)를 참조하세요.





## <a name="threat-protection-for-other-microsoft-services"></a>기타 Microsoft 서비스에 대한 위협 방지 <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF에 대한 위협 방지 <a name="azure-waf"></a>

Azure Application Gateway는 일반적인 악용 및 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호하는 WAF(웹 애플리케이션 방화벽)를 제공합니다.

일반적으로 알려진 취약성을 악용하여 웹 애플리케이션을 공격하는 악의적인 사례가 점점 늘어나고 있습니다. Application Gateway WAF는 Open Web Application Security Project의 핵심 규칙 집합 3.0 또는 2.2.9를 기반으로 합니다. WAF는 새로운 취약성으로부터 보호하기 위해 자동으로 업데이트됩니다. 

Azure WAF에 대한 라이선스가 있는 경우 추가 구성 없이 WAF 경고가 Security Center로 스트림됩니다. WAF에서 생성되는 경고에 대한 자세한 내용은 [웹 애플리케이션 방화벽 CRS 규칙 그룹 및 규칙](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)을 참조하세요.


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS Protection에 대한 위협 방지 <a name="azure-ddos"></a>

DDoS(분산 서비스 거부) 공격은 쉽게 실행되는 것으로 알려져 있습니다. 특히 애플리케이션을 클라우드로 이동하는 경우 매우 중요한 보안 문제가 됩니다. 

DDoS 공격은 애플리케이션의 리소스를 소진시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 만듭니다. DDoS 공격은 인터넷을 통해 연결할 수 있는 모든 엔드포인트를 대상으로 할 수 있습니다.

DDoS 공격을 방어하려면 Azure DDoS Protection 라이선스를 구매하고 애플리케이션 디자인 모범 사례를 따릅니다. DDoS Protection은 다양한 서비스 계층을 제공합니다. 자세한 내용은 [Azure DDoS Protection 개요](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)를 참조하세요.

Azure DDoS Protection 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-azureddos)를 참조하세요.


## <a name="next-steps"></a>다음 단계
이러한 위협 방지 기능의 보안 경고에 대한 자세한 내용은 다음 문서를 참조하세요.

* [모든 Azure Security Center 경고 참조 표](alerts-reference.md)
* [Azure Security Center의 보안 경고](security-center-alerts-overview.md)
* [Azure Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md)
* [보안 경고 및 추천 사항 내보내기(미리 보기)](continuous-export.md)