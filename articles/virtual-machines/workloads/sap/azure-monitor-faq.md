---
title: FAQ - SAP 솔루션을 위한 Azure Monitor | Microsoft Docs
description: 이 문서에서는 SAP 솔루션을 위한 Azure Monitor의 FAQ(자주 묻는 질문)에 대한 답변을 알아봅니다.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 4b84f07f637b0a8925dec96c8c609101247ffd64
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377127"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>SAP 솔루션을 위한 Azure Monitor FAQ(미리 보기)
## <a name="frequently-asked-questions"></a>질문과 대답

이 문서에서는 SAP 솔루션을 위한 Azure Monitor의 FAQ(자주 묻는 질문)에 대한 답변을 제공합니다.  

 - **SAP 솔루션을 위한 Azure Monitor에 대해 요금을 지불해야 하나요?**  
SAP 솔루션을 위한 Azure Monitor에 대한 라이선스 요금은 없습니다.  
하지만 관리되는 리소스 그룹 구성 요소에 대한 비용 부담의 책임은 고객에게 있습니다.  

 - **이 서비스에 대한 공개 미리 보기는 어느 지역에서 사용할 수 있나요?**  
이 서비스의 공개 미리 보기는 미국 동부 2, 미국 서부 2, 미국 동부 및 유럽 서부에서 사용할 수 있습니다.  

 - **내 구독에서 관리되는 리소스 그룹의 배포를 허용하려면 사용 권한을 제공해야 하나요?**  
아니요, 명시적 사용 권한은 필요하지 않습니다.  

 - **수집기 VM은 어디에 두나요?**  
SAP 솔루션을 위한 Azure Monitor 리소스를 배포하는 시점에 고객이 모니터링 리소스에 대해 SAP HANA 서버와 동일한 VNET을 선택하는 것이 좋습니다. 따라서 수집기 VM은 SAP HANA 서버와 동일한 VNET에 두는 것이 좋습니다. 고객이 HANA가 아닌 데이터베이스를 사용하고 있는 경우 수집기 VM은 HANA가 아닌 데이터베이스와 동일한 VNET에 두게 됩니다.  

 - **지원되는 HANA 버전에는 어떤 것이 있나요?**  
HANA 1.0 SPS 12(수정 버전 120 이상) 및 HANA 2.0 SPS03 이상  

 - **지원되는 HANA 배포 구성에는 어떤 것이 있나요?**  
현재 지원되는 구성은 다음과 같습니다.
   - 단일 노드(스케일 업) 및 다중 노드(스케일 아웃)  
   - 단일 데이터베이스 컨테이너(HANA 1.0 SPS 12) 및 다중 데이터베이스 컨테이너(HANA 1.0 SPS 12 또는 HANA 2.0)  
   - 자동 호스트 장애 조치(failover)(n+1) 및 HSR  

 - **지원되는 SQL Server 버전에는 어떤 것이 있나요?**  
SQL Server 2012 SP4 이상  

 - **지원되는 SQL Server 구성에는 어떤 것이 있나요?**  
현재 지원되는 구성은 다음과 같습니다.
   - 가상 머신의 기본 또는 명명된 독립 실행형 인스턴스  
   - 클러스터형 리소스의 가상 이름이나 AlwaysOn 수신기 이름을 사용할 때 AlwaysOn 구성의 클러스터형 인스턴스 또는 인스턴스. 현재 클러스터 또는 AlwaysOn 특정 메트릭은 수집되지 않음    
   - Azure SQL Database(PAAS)는 현재 지원되지 않음  

 - **관리되는 리소스 그룹을 실수로 삭제하면 어떻게 되나요?**  
관리되는 리소스 그룹은 기본적으로 잠겨 있습니다. 따라서 고객에 의해 관리되는 리소스 그룹이 실수로 삭제될 가능성은 아주 작습니다.  
고객이 관리되는 리소스 그룹을 삭제하는 경우 SAP 솔루션을 위한 Azure Monitor 작동이 중지됩니다. 고객은 SAP 솔루션을 위한 Azure Monitor 리소스를 새로 배포하고 다시 시작해야 합니다.  

 - **SAP 솔루션을 위한 Azure Monitor 리소스를 배포하려면 Azure 구독에 어떤 역할이 필요한가요?**  
기여자 역할입니다.  

 - **이 제품에 대한 SLA는 무엇인가요?**  
미리 보기는 서비스 수준 약정에서 제외되어 있습니다. SAP 솔루션을 위한 Azure Monitor 마켓플레이스 이미지를 통해 전체 사용 조건을 읽어보세요.  

 - **이 솔루션을 통해 내 전체 환경을 모니터링할 수 있나요?**  
현재 HANA 데이터베이스, 기본 인프라, 고가용성 클러스터, Microsoft SQL Server, SAP Netweaver 가용성 및 SAP 애플리케이션 인스턴스 가용성 메트릭을 공개 미리 보기에서 모니터링할 수 있습니다.  

 - **이 서비스가 SAP 솔루션 관리자를 대체하나요?**  
아니요. 고객은 비즈니스 프로세스 모니터링을 위해 SAP 솔루션 관리자를 계속 사용할 수 있습니다.  

 - **SAP HANA Cockpit/Studio와 같은 일반적인 솔루션에 비해 이 서비스는 어떤 가치가 있나요?**  
SAP 솔루션을 위한 Azure Monitor는 HANA 데이터베이스에만 한정된 것이 아닙니다. SAP 솔루션을 위한 Azure Monitor는 AnyDB도 지원합니다.  

- **지원되는 SAP NetWeaver 버전은 무엇인가요?**  
SAP NetWeaver 7.0 이상입니다.  

- **지원되는 SAP NetWeaver 구성은 무엇인가요?**  
ABAP, Java 및 이중 스택 SAP NetWeaver 애플리케이션 서버 구성을 지원합니다.

- **SAP NetWeaver 애플리케이션 모니터링에 대해 메서드를 보호 해제해야 하는 이유는 무엇인가요?**  
SAP 릴리스 7.3 이상에서는 대부분의 웹 서비스 메서드가 기본적으로 보호되어 있습니다. 이러한 메서드를 호출하여 가용성 및 성능 메트릭을 페치하기 위해서는 다음 메서드를 보호 해제해야 합니다. GetQueueStatistic, ABAPGetWPTable, GetProcessList, EnqGetStatistic and GetSystemInstancelist

- **SAPCONTROL webmethods를 보호 해제하는 데 위험이 있나요?**  
일반적으로 SAPCONTROL webmethods를 보호 해제하는 것은 [이러한](https://launchpad.support.sap.com/#/notes/1439348) 보안 위험이 발생하지 않지만 고객이 sapstartsrv의 서버 포트(5XX13 / 5XX14)를 통해 보호되지 않은 webmethods에 대한 액세스를 제한/금지하려는 경우 SAP ACL(액세스 제어 목록)에서 필터를 추가하여 수행할 수 있습니다. [OSS 참고](https://service.sap.com/sap/support/notes/1495075)에서 이 작업을 수행하는 데 필요한 구성을 설명합니다. 

- **SAP NetWeaver 공급자를 설정하기 위해 시스템 구성을 수행한 후에는 SAP 인스턴스를 다시 시작해야 하나요?**  
예, SAP 구성 변경을 통해 보호되지 않은 메서드가 생기고 나면 해당되는 SAP 시스템을 다시 시작하여 구성 변경 내용이 업데이트되도록 해야 합니다.  

## <a name="next-steps"></a>다음 단계

- 첫 번째 SAP 솔루션을 위한 Azure Monitor 공급자 리소스를 만듭니다.
