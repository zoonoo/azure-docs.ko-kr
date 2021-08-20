---
title: Azure Data Factory의 새로운 기능
description: 이 새로운 기능 페이지에서는 Azure Data Factory의 새로운 기능과 개선 사항을 소개합니다.
author: pennyzhou-msft
ms.author: xupzhou
ms.reviewer: xupzhou
ms.service: data-factory
ms.topic: overview
ms.date: 07/14/2021
ms.openlocfilehash: fe4c5fb72ce3cd32e14bad211683e01ac6edf3ab
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342627"
---
# <a name="whats-new-in-azure-data-factory"></a>Azure Data Factory의 새로운 기능

Azure Data Factory는 지속적으로 개선되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 

## <a name="june-2021"></a>2021년 6월
<br>
<table>
<tr><td><b>서비스 범주</b></td><td><b>서비스 개선 사항</b></td><td><b>세부 정보</b></td></tr>
<tr><td rowspan=4 valign="middle"><b>데이터 이동</b></td><td>Azure Data Factory 데이터 복사 도구가 포함된 새로운 사용자 환경</td><td>이제 향상된 데이터 수집 환경에서 다시 디자인된 데이터 복사 도구를 사용할 수 있습니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/a-re-designed-copy-data-tool-experience/ba-p/2380634">자세한 정보</a></td></tr>
<tr><td>MongoDB와 MongoDB Atlas는 원본과 싱크 둘 다로 지원됨</td><td>이 개선 사항은 지원되는 모든 데이터 저장소와 MongoDB 또는 MongoDB Atlas 데이터베이스 간 데이터 복사를 지원합니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-connectors-available-in-adf-mongodb-and-mongodb-atlas-are/ba-p/2441482">자세한 정보</a></td></tr>
<tr><td>Always Encrypted는 Azure SQL Database, Azure SQL Managed Instance, SQL Server 커넥터에서 원본과 싱크 둘 다로 지원됩니다.</td><td>Azure Data Factory에서 Azure SQL Database, Azure SQL Managed Instance, SQL Server 커넥터의 복사 작업을 위해 Always Encrypted를 사용할 수 있습니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/azure-data-factory-copy-now-supports-always-encrypted-for-both/ba-p/2461346">자세한 정보</a></td></tr>
<tr><td>ADLS Gen2 또는 Azure Blob에 싱크할 때 복사 작업에서 사용자 지정 메타데이터를 설정할 수 있습니다.</td><td>ADLS Gen2 또는 Azure Blob에 쓸 때 복사 작업에서 사용자 지정 메타데이터를 설정하거나 원본 파일의 마지막으로 수정된 정보를 메타데이터로 저장할 수 있습니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/support-setting-custom-metadata-when-writing-to-blob-adls-gen2/ba-p/2545506#M490">자세한 정보</a></td></tr>
<tr><td rowspan=4 valign="middle"><b>데이터 흐름</b></td><td>이제 SQL Server가 데이터 흐름의 원본 및 싱크로 지원됨</td><td>이제 SQL Server가 데이터 흐름의 원본 및 싱크로 지원됩니다. Azure Integration Runtime 관리형 VNET 기능을 사용하여 온-프레미스 SQL Server와 클라우드 VM 기반 인스턴스에 통신하도록 네트워킹을 구성하는 방법에 대한 지침을 보려면 링크를 따르세요.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-data-flow-connector-sql-server-as-source-and-sink/ba-p/2406213">자세한 정보</a></td></tr>
<tr><td>이제 모든 새 Azure Integration Runtime에서 데이터 흐름 클러스터 빠른 시작 재사용이 기본적으로 사용하도록 설정됨</td><td>ADF에서 인기 있는 데이터 흐름 빠른 시작 재사용 기능이 일반 공급됨을 알려 드립니다. 이제 모든 새 Azure Integration Runtime에서 빠른 시작 재사용이 기본적으로 사용하도록 설정됩니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/how-to-startup-your-data-flows-execution-in-less-than-5-seconds/ba-p/2267365">자세한 정보</a></td></tr>
<tr><td>ADF 퍼블릭 미리 보기의 파워 쿼리 작업</td><td>이제 Azure Data Factory 데이터 랭글링을 사용하여 파워 쿼리 싱크에 대한 복합 필드 매핑을 빌드할 수 있습니다. 이 업데이트를 수용하기 위해 이제 싱크가 파워 쿼리(미리 보기) 작업의 파이프라인에서 구성됩니다.<br><a href="wrangling-tutorial.md">자세한 정보</a></td></tr>
<tr><td>Azure Data Factory의 업데이트된 데이터 흐름 모니터링 UI</td><td>Azure Data Factory의 모니터링 UI가 데이터 흐름 ETL 작업 실행을 보면서 성능 튜닝 영역을 빠르게 식별하기 쉽도록 새롭게 업데이트되었습니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/updated-data-flows-monitoring-ui-in-adf-amp-synapse/ba-p/2432199">자세한 정보</a></td></tr>
<tr><td><b>SSIS(SQL Server Integration Services)</b></td><td>Azure Data Factory에서 SSIS를 사용하여 간단한 3단계로 어디서나 SQL 실행</td><td>이 게시물에서는 Azure Data Factory에서 SSIS를 사용하여 어디서나 SQL 문/스크립트를 실행하는 간단한 3단계를 제공합니다.<ol><li>자체 호스팅 통합 런타임/SSIS Integration Runtime을 준비합니다.</li><li>Azure Data Factory 파이프라인에서 SSIS 패키지 실행 작업을 준비합니다.</li><li>자체 호스팅 통합 런타임/SSIS Integration Runtime에서 SSIS 패키지 실행 작업을 실행합니다.</li></ol><a href="https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244">자세한 정보</a></td></tr>
</table>

## <a name="more-information"></a>자세한 정보

- [블로그 - Azure Data Factory](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
- [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter](https://twitter.com/AzDataFactory?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)
- [비디오](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/featured)





