<properties 
	pageTitle="Azure 데이터 팩터리 - 질문과 대답" 
	description="Azure 데이터 팩터리에 대한 질문과 대답입니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 - 질문과 대답

**Q: Azure 데이터 팩터리란 무엇인가요?**

데이터 팩터리는 개발자가 데이터 저장소, 이동 및 처리 서비스를 고가용성 및 내결함성이 있는 데이터 파이프라인으로 구성하도록 지원하는 완전히 관리되는 서비스입니다. 데이터 팩터리는 온-프레미스 및 클라우드 데이터 저장소 모두에서 작동합니다. 파이프라인은 데이터 입력, 처리 작업 및 데이터 출력으로 이루어지며, 간단한 JSON 스크립트를 사용하여 정의되고 PowerShell 명령을 통해 활성화됩니다. 활성화되면 데이터 팩터리는 사용자 대신 자동 클러스터 관리를 위한 옵션을 사용하여 HDInsight (Hadoop)에서 실행할 파이프라인을 오케스트레이션하고 예약합니다. 또한 데이터 팩터리는 하나의 대시보드에서 다양한 작동 및 서비스 상태 정보를 사용하여 모든 파이프라인을 모니터링하는 시각적 관리 및 모니터링 환경을 Azure 미리 보기 포털을 통해 제공합니다.
 
**Q: 데이터 팩터리로 해결되는 고객 문제는 무엇인가요?**

Azure 데이터 팩터리는 구조화되지 않은 데이터와 함께 기존 관계형 저장소에서 다양한 데이터 저장소, 처리 및 이동 서비스를 활용하는 민첩성과 완전히 관리되는 서비스의 제어 및 모니터링 기능 사이의 균형을 유지합니다.

**Q: 데이터 팩터리의 대상 고객층은 누구인가요?**


- Hadoop과 다른 시스템 간의 통합 서비스를 구축하는 데이터 개발자
	- 지속적으로 변경되고 확장되는 데이터 환경에 맞춰 확장하고 통합해야 합니다.
	- 정보 생산을 위한 사용자 지정 코드를 작성해야 하며, 이 경우 비용이 많이 들고, 유지 관리하기 어렵고, 가용성이나 내결함성이 높지 않습니다.

- IT 인프라 내에서 더 다양한 데이터를 통합하는 방법을 찾고 있는 IT 전문가
	- 조직의 모든 데이터를 살펴서 다양한 비즈니스 통찰력을 도출해야 합니다.
	- 온-프레미스와 클라우드 전반에서 비용과 규모에 맞춰 계산 및 저장소 리소스를 관리해야 합니다.
	- 다양한 원본 및 처리를 빠르게 추가하여 새로운 비즈니스 요구를 해결하고 모든 계산 및 저장소 자산의 상태를 지속적으로 파악해야 합니다.

**Q: Azure 데이터 팩터리에 대한 가격 정보는 어디서 찾을 수 있나요?**

Azure 데이터 팩터리에 대한 가격 정보는 [데이터 팩터리 가격 정보 페이지][adf-pricing-details]를 참조하세요.  

**Q: Azure 데이터 팩터리를 시작하려면 어떻게 해야 하나요?**

- Azure 데이터 팩터리에 대한 개요는 [Azure 데이터 팩터리 소개][adf-introduction]를 참조하세요.
- 간략한 자습서는 [Azure 데이터 팩터리 시작][adfgetstarted]을 참조하세요.
- 자세한 설명서는 [Azure 데이터 팩터리 설명서][adf-documentation-landingpage]를 참조하세요.
 
**Q: 지원되는 데이터 원본 및 작업은 무엇인가요?**

- **지원되는 데이터 원본:** Azure 저장소(Blob 및 테이블), SQL Server, Azure SQL 데이터베이스
- **지원되는 작업:** 복사 작업(온-프레미스에서 클라우드로 복사, 클라우드에서 온-프레미스로 복사), HDInsight 작업(Pig 및 Hive 변환) 및 사용자 지정 C# 작업
  
**Q: 고객이 데이터 팩터리에 액세스하려면 어떻게 해야 하나요?**

고객은 [Azure 미리 보기 포털][azure-preview-portal]을 통해 데이터 팩터리에 액세스할 수 있습니다.

**Q: 데이터 팩터리의 지역 가용성은 얼마나 되나요?**

공개 미리 보기에서 데이터 팩터리는 미국 서부에서만 제공됩니다.  데이터 팩터리에서 사용되는 계산 및 저장소 서비스는 다른 지역에 있을 수 있습니다.
 
**Q: 데이터 팩터리/파이프라인/작업/데이터 집합의 수에 대한 제한은 어떻게 되나요?** 


- 구독 내의 데이터 팩터리 수: 50
- 데이터 팩터리 내의 파이프라인 수: 100
- 파이프라인 내의 작업 수: 10
- 데이터 팩터리 내의 데이터 집합 수: 100

**Q: 복사 작업에서 지원하는 지역은 어디인가요?**

복사 작업에서 데이터를 복사할 수 있는 지역은 미국 동부 2, 미국 서부, 북유럽, 서유럽 및 동남아시아입니다.

위의 다섯 개 지역 중 하나를 사용하여 데이터를 라우팅하면 다른 지역으로도 데이터를 복사할 수 있습니다.  복사 작업에 대한 요금은 어느 지역을 통해 데이터가 라우팅되는지를 기준으로 청구됩니다.

복사 대상의 지역 | 라우팅에 사용되는 지역
-------------------------- | -----------------------
미국 동부 | 미국 동부 2
미국 중부 | 미국 동부 2
미국 중북부 | 미국 동부 2
미국 중남부 | 미국 서부
동아시아 | 동남아시아
일본 동부 | 미국 서부
일본 서부 | 미국 서부
브라질 남부 | 미국 동부 2

**Q: HDInsight 클러스터를 사용할 경우 HDInsight에서 지원하는 지역은 어디인가요?**

다음 문서의 지리적 가용성 섹션 또는 [HDInsight 가격 정보][hdinsight-supported-regions]를 참조하세요.

**Q: 주문형 HDInsight 클러스터가 사용되는 지역은 어디인가요?**

주문형 HDInsight 클러스터는 클러스터에서 사용하도록 지정한 저장소가 있는 지역과 동일한 지역에 생성됩니다.    

## 참고 항목
[Azure 데이터 팩터리 소개][adf-introduction]
[Azure 데이터 팩터리 시작][adfgetstarted]
[데이터 팩터리 문제 해결 가이드][adf-troubleshoot](영문)


[adfgetstarted]: ../data-factory-get-started
[adf-introduction]: ../data-factory-introduction
[adf-troubleshoot]: ../data-factory-troubleshoot
[adf-documentation-landingpage]: http://go.microsoft.com/fwlink/?LinkId=516909
[azure-preview-portal]: http://portal.azure.com

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/

<!--HONumber=35.2-->

<!--HONumber=46--> 
