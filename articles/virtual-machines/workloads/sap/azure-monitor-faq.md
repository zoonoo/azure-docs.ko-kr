---
title: FAQ-SAP 솔루션에 대 한 Azure Monitor | Microsoft Docs
description: 이 문서에서는 SAP 솔루션에 대 한 Azure Monitor FAQ (질문과 대답)에 대해 알아봅니다.
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 204b809966fbf63a48f6d1ce1d80f87f706c9a56
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968573"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>SAP 솔루션에 대 한 Azure Monitor FAQ (미리 보기)
## <a name="frequently-asked-questions"></a>질문과 대답

이 문서에서는 SAP 솔루션에 대 한 Azure Monitor FAQ (질문과 대답)를 제공 합니다.  

 - **SAP 솔루션에 대 한 Azure Monitor 요금을 지불 해야 하나요?**  
SAP 솔루션에 대 한 Azure Monitor에 대 한 라이선스 요금은 없습니다.  
그러나 고객은 관리 되는 리소스 그룹 구성 요소에 대 한 비용을 지불 해야 합니다.  

 - **공개 미리 보기에서이 서비스를 사용할 수 있는 지역은 어디 인가요?**  
공개 미리 보기의 경우이 서비스는 미국 동부 2, 미국 서 부 2, 미국 동부 및 유럽 서부에서 사용할 수 있습니다.  

 - **내 구독에서 관리 되는 리소스 그룹의 배포를 허용 하는 권한을 제공 해야 하나요?**  
아니요, 명시적 사용 권한이 필요 하지 않습니다.  

 - **수집기 VM은 어디에 있나요?**  
SAP Solutions 리소스에 대 한 Azure Monitor를 배포 하는 시점에 고객이 모니터링 리소스에 대해 SAP HANA 서버로 동일한 VNET을 선택 하는 것이 좋습니다. 따라서 수집기 VM은 SAP HANA server와 동일한 VNET에 배치 하는 것이 좋습니다. 고객이 HANA를 사용 하지 않는 데이터베이스를 사용 하는 경우 수집기 VM은 비 HANA 데이터베이스와 동일한 VNET에 상주 하 게 됩니다.  

 - **지원 되는 HANA 버전은 무엇 인가요?**  
HANA 1.0 SPS 12 120 (이상) 및 HANA 2.0 SPS03 이상  

 - **어떤 HANA 배포 구성이 지원 되나요?**  
현재 지원되는 구성은 다음과 같습니다.
   - 단일 노드 (수직 확장) 및 다중 노드 (스케일 아웃)  
   - 단일 데이터베이스 컨테이너 (HANA 1.0 SPS 12) 및 여러 데이터베이스 컨테이너 (HANA 1.0 SPS 12 또는 HANA 2.0)  
   - 자동 호스트 장애 조치 (failover) (n + 1) 및 HSR  

 - **지원 되는 SQL Server 버전은 무엇 인가요?**  
SQL Server 2012 SP4 이상  

 - **어떤 SQL Server 구성이 지원 되나요?**  
현재 지원되는 구성은 다음과 같습니다.
   - 가상 컴퓨터의 기본 또는 명명 된 독립 실행형 인스턴스  
   - 클러스터형 리소스의 가상 이름 또는 AlwaysOn 수신기 이름을 사용 하는 경우 AlwaysOn 구성의 클러스터형 인스턴스 또는 인스턴스 현재 클러스터 또는 AlwaysOn 특정 메트릭이 수집 되지 않습니다.    
   - PAAS (Azure SQL Database)는 현재 지원 되지 않습니다.  

 - **관리 되는 리소스 그룹을 실수로 삭제 하면 어떻게 되나요?**  
관리 되는 리소스 그룹은 기본적으로 잠깁니다. 따라서 고객에 의해 관리 되는 리소스 그룹을 실수로 삭제할 가능성이 minuscule 됩니다.  
고객이 관리 되는 리소스 그룹을 삭제 하는 경우 SAP 솔루션에 대 한 Azure Monitor 작동이 중지 됩니다. 고객은 SAP 솔루션 리소스에 대 한 새 Azure Monitor을 배포 하 고 시작 해야 합니다.  

 - **Azure 구독에서 SAP 솔루션 리소스에 대 한 Azure Monitor를 배포 하는 데 필요한 역할은 무엇 인가요?**  
참가자 역할.  

 - **이 제품에 대 한 SLA는 무엇 인가요?**  
미리 보기는 서비스 수준 계약에서 제외 됩니다. SAP Solutions marketplace 이미지에 대 한 Azure Monitor 전체 사용 조건을 읽어 보세요.  

 - **이 솔루션을 통해 전체 가로를 모니터링할 수 있나요?**  
현재 공개 미리 보기로 제공 되는 HANA 데이터베이스, 기본 인프라, 고가용성 클러스터 및 Microsoft SQL server를 모니터링할 수 있습니다.  

 - **이 서비스는 SAP 솔루션 관리자를 대체 하나요?**  
아니요. 고객은 계속 해 서 SAP 솔루션 관리자를 사용 하 여 비즈니스 프로세스를 모니터링할 수 있습니다.  

 - **SAP HANA 환경/스튜디오와 같은 기존 솔루션에 대 한이 서비스의 가치는 무엇 인가요?**  
SAP 솔루션에 대 한 Azure Monitor는 HANA 데이터베이스와 관련이 없습니다. SAP 솔루션에 대 한 Azure Monitor는 AnyDB도 지원 합니다.  

## <a name="next-steps"></a>다음 단계

- SAP solutions 리소스에 대 한 첫 번째 Azure Monitor를 만듭니다.
