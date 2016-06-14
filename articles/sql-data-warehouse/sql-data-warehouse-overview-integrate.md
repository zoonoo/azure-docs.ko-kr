<properties
   pageTitle="SQL 데이터 웨어하우스와 통합된 솔루션 구축 | Microsoft Azure"
   description="SQL 데이터 웨어하우스와 통합된 솔루션과 파트너 및 도구"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#SQL 데이터 웨어하우스와 함께 기타 서비스 활용
사용자는 SQL 데이터 웨어하우스를 통해 핵심 기능 외에도, Azure에서 다양한 기타 서비스를 함께 활용할 수 있습니다. 특히 다음과 긴밀하게 통합하는 단계를 현재 진행했습니다.

+ Power BI
+ Azure 데이터 팩터리
+ Azure 기계 학습
+ Azure 스트림 분석

Azure 에코시스템에서 더 많은 서비스가 연결되도록 노력하고 있습니다.

##Power BI
Power BI 통합을 통해 Power BI의 동적 보고 및 시각화와 더불어 SQL 데이터 웨어하우스의 계산 능력의 활용도를 높일 수 있습니다. Power BI 통합에는 현재 다음이 포함됩니다.

+ **직접 연결**: SQL 데이터 웨어하우스에 대해 논리적 푸시다운을 통한 보다 고급 연결. 더 큰 규모를 더욱 빠르게 분석합니다.
+ **Power BI에서 열기**: 'Power BI에서 열기' 단추를 통해 인스턴스 정보를 Power BI에 전달하여 보다 원활한 연결이 가능합니다.

자세한 내용은 [Power BI와 통합](./sql-data-warehouse-integrate-power-bi.md) 또는 [Power BI 설명서](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx)를 참조하세요.

##Azure 데이터 팩터리
Azure 데이터 팩터리는 복잡한 추출-로드 파이프라인을 만들 수 있는 관리되는 플랫폼을 사용자에게 제공합니다. Azure 데이터 팩터리와 SQL 데이터 웨어하우스의 통합에는 다음이 포함됩니다.

+ **저장 프로시저**: SQL 데이터 웨어하우스에서 저장 프로시저의 실행을 오케스트레이션합니다.
+ **복사**: ADF를 사용하여 SQL 데이터 웨어하우스로 데이터를 이동합니다. 이 작업에서는 백그라운드에서 ADF의 표준 데이터 이동 메커니즘 또는 PolyBase가 사용될 수 있습니다. 

자세한 내용은 [Azure 데이터 팩터리와 통합](./sql-data-warehouse-integrate-azure-data-factory.md) 또는 [Azure 데이터 팩터리 설명서](https://azure.microsoft.com/documentation/services/data-factory/)를 참조하세요.

##Azure 기계 학습
Azure 기계 학습은 사용자가 다양한 예측 도구를 활용하여 복잡한 모델을 만들 수 있는 완전히 관리되는 분석 서비스입니다. SQL 데이터 웨어하우스는 다음 기능을 포함하는 이러한 모델에 대한 소스 및 대상으로 지원됩니다.

+ **데이터 읽기:** SQL 데이터 웨어하우스에 대해 T-SQL을 사용하는 규모에 따른 드라이브 모델입니다.
+ **데이터 쓰기:** 모델에서 SQL 데이터 웨어하우스로 변경 사항을 뒤로 커밋합니다.

자세한 내용은 [Azure 기계 학습과 통합](./sql-data-warehouse-integrate-azure-machine-learning.md) 또는 [Azure 기계 학습 설명서](https://azure.microsoft.com/services/machine-learning/)를 참조하세요.

##Azure 스트림 분석
Azure 스트림 분석은 Azure 이벤트 허브에서 생성된 이벤트 데이터를 처리 및 사용하기 위한 복잡하고 완전히 관리되는 인프라입니다. SQL 데이터 웨어하우스와 통합을 통해 스트리밍 데이터를 효과적으로 처리하고 관계형 데이터와 함께 저장하여 보다 심도있는 고급 분석이 가능합니다.

+ **작업 출력:** 스트림 분석 작업에서 SQL 데이터 웨어하우스로 출력을 직접 보냅니다.

자세한 내용은 [Azure 스트림 분석과 통합](./sql-data-warehouse-integrate-azure-stream-analytics.md) 또는 [Azure 스트림 분석 설명서](https://azure.microsoft.com/documentation/services/stream-analytics/)를 참조하세요.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-integrate-solution-partners.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0601_2016-->