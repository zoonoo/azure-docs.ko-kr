<properties
   pageTitle="서비스 복원력 지침 | Microsoft Azure"
   description="Microsoft Azure 서비스에 대한 재해 복구 및 사전 복원력 및 가용성 지침에 연결합니다."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="aglick"/>

# Microsoft Azure 서비스 복원력 지침
Microsoft Azure는 필요할 때 리소스를 제공하도록 설계되었습니다. 좋은 설계와 작업 방법의 일부로, 응용 프로그램이 서비스 중단에 영향을 받는 경우 수행할 작업 뿐만 아니라 고가용성을 달성하기 위해 Azure 서비스를 사용하는 방법을 알고 있어야 합니다. 이 프로세스를 지원하기 위해 이 문서는 재해 복구 지침 뿐만 아니라 다양한 Azure 서비스를 위한 설계 지침에 대한 링크를 포함합니다.

##재해 복구 지침
아래 재해 복구 지침 링크는 Azure 서비스 중단의 영향을 받는 경우 응용 프로그램이 다시 신속하게 온라인 상태가 될 수 있도록 필요한 정보를 제공할 수 있습니다. 다음 질문에 대해 다음 링크가 생성되었습니다. "Azure 서비스 중단의 영향을 받고 있습니다. 어떻게 해야 할까요?"

##디자인 지침
아래의 설계 지침 링크는 응용 프로그램의 가동 시간을 최대화하는 방식으로 각 Azure 서비스를 사용하는 최상의 방법을 이해할 수 있도록 만들어진 설계 및 아키텍처 지침입니다. 이러한 링크는 다음 질문을 위해 생성되었습니다. "버그, 하드웨어 오류, 서비스 중단 또는 다른 실패가 응용 프로그램의 전반적인 가용성에 영향을 주지 않도록 하려면 어떻게 해야 하나요?" 현재 찾으려고 하는 서비스에 대한 특정 지침이 없는 경우 [Microsoft Azure에 빌드된 응용 프로그램에 대한 고가용성](./resiliency-high-availability-azure-applications.md) 문서에서 설계에 도움이 되는 추가 정보를 찾을 수 있습니다.

##서비스 지침
| 부여 | 재해 복구 지침 | 디자인 지침 |
|:---------|:--------------------------:|:------------------:|
| [클라우드 서비스](https://azure.microsoft.com/services/cloud-services/ "Azure 클라우드 서비스") | [link](../cloud-services/cloud-services-disaster-recovery-guidance.md "Azure 클라우드 서비스 재해 복구 지침") | 사용할 수 없음 |
| [키 자격 증명 모음](https://azure.microsoft.com/services/key-vault/ "Azure 키 자격 증명 모음") | [link](../key-vault/key-vault-disaster-recovery-guidance.md "Azure 주요 자격 증명 모음 재해 복구 지침") | 사용할 수 없음 |
| [저장소](https://azure.microsoft.com/services/storage/ "Azure 저장소") | [link](../storage/storage-disaster-recovery-guidance.md "Azure 저장소 재해 복구 지침") | 사용할 수 없음 |
| [SQL 데이터베이스](https://azure.microsoft.com/services/sql-database/ "Azure SQL 데이터베이스") | [link](../sql-database/sql-database-disaster-recovery.md "Azure SQL 데이터베이스 재해 복구 지침") | [link](../sql-database/sql-database-business-continuity-design.md "Azure SQL 데이터베이스 설계 지침") |
| [가상 컴퓨터](https://azure.microsoft.com/services/virtual-machines/ "Azure 가상 컴퓨터") | [link](../virtual-machines/virtual-machines-disaster-recovery-guidance.md "Azure 가상 컴퓨터 재해 복구 지침") | 사용할 수 없음 |
| [가상 네트워크](https://azure.microsoft.com/services/virtual-network/ "Azure 가상 네트워크") | [link](../virtual-network/virtual-network-disaster-recovery-guidance.md "Azure 가상 네트워크 재해 복구 지침") | 사용할 수 없음 |

##다음 단계
시스템 및 솔루션에 대해 더 중점을 둔 지침을 찾고 있다면 [Microsoft Azure에서 빌드된 응용 프로그램에 대한 재해 복구 및 고가용성](https://aka.ms/drtechguide)을 참고하세요.

<!---HONumber=AcomDC_0601_2016-->