---
title: Azure Data Factory를 사용하여 데이터 증분 복사 | Microsoft Docs
description: 이 자습서는 원본 데이터 저장소에서 대상 데이터 저장소로 데이터를 증분 복사하는 방법을 보여줍니다. 첫 번째 복사는 한 테이블에서 데이터를 복사합니다.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 1d8b31e55a2a230385730c924d3e6bcc6072e7ea
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520434"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>원본 데이터 저장소에서 대상 데이터 저장소로 데이터 증분 로드

데이터 통합 솔루션에서 초기 전체 데이터 로드 후 증분 방식(또는 델타)으로 데이터를 로드하는 것은 널리 사용되는 시나리오입니다. 이 섹션의 자습서에서는 Azure Data Factory를 사용하여 증분 방식으로 데이터를 로드하는 다양한 방법을 보여줍니다.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>워터마크를 사용하여 데이터베이스에서 델타 데이터 로드
이 경우 워터마크는 원본 데이터베이스에 정의합니다. 워터마크는 마지막으로 업데이트된 타임스탬프 또는 증분 키가 있는 열입니다. 델타 로딩 솔루션은 이전 워터마크와 새 워터마크 사이에 변경된 데이터를 로드합니다. 이 방식에 대한 워크플로는 다음 다이어그램과 같습니다. 

![워터마크 사용을 위한 워크플로](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

단계별 지침은 다음 자습서를 참조하세요. 

- [Azure SQL Database의 한 테이블에서 Azure Blob 스토리지로 데이터 증분 복사](tutorial-incremental-copy-powershell.md)
- [온-프레미스 SQL Server의 여러 테이블에서 Azure SQL Database로 데이터 증분 복사](tutorial-incremental-copy-multiple-tables-powershell.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>변경 내용 추적 기술을 사용하여 SQL DB에서 델타 데이터 로드
변경 내용 추적 기술은 애플리케이션에 효율적인 변경 내용 추적 메커니즘을 제공하는 SQL Server 및 Azure SQL Database의 간단한 솔루션입니다. 이를 통해 애플리케이션에서 삽입되거나 업데이트되거나 삭제된 데이터를 쉽게 식별할 수 있습니다. 

이 방식에 대한 워크플로는 다음 다이어그램과 같습니다.

![변경 내용 추적 사용을 위한 워크플로](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

단계별 지침은 다음 자습서를 참조하세요. <br/>
[변경 내용 추적 기술을 사용하여 Azure SQL Database에서 Azure Blob Storage로 데이터 증분 복사](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>LastModifiedDate를 사용하여 새 파일과 변경된 파일 로드
LastModifiedDate를 사용하여 새 파일과 변경된 파일만 대상 저장소에 복사할 수 있습니다. ADF는 원본 저장소의 모든 파일을 검색하고, LastModifiedDate별로 파일 필터를 적용하고, 마지막 시간 이후의 새 파일과 변경된 파일만 대상 저장소에 복사합니다.  ADF가 대량의 파일을 검색하되, 몇 가지 파일만 대상에 복사하도록 해도 파일 검색에 시간이 오래 걸려서 대기 시간이 길어집니다.   

단계별 지침은 다음 자습서를 참조하세요. <br/>
[LastModifiedDate를 기반으로 Azure Blob Storage에서 Azure Blob Storage로 새 파일과 변경된 파일 증분 복사](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>시간 분할 폴더 또는 파일 이름을 사용하여 새 파일만 로드합니다.
파일 또는 폴더가 timeslice 정보와 함께 파일 또는 폴더 이름(예: /yyyy/mm/dd/file.csv)의 일부로 이미 시간 분할된 새 파일만 복사할 수 있습니다. 이는 새 파일의 증분 로드 시 가장 높은 성능을 제공하는 방법입니다. 

단계별 지침은 다음 자습서를 참조하세요. <br/>
[시간 분할된 폴더나 파일 이름을 기반으로 Azure Blob Storage에서 Azure Blob Storage로 새 파일 증분 복사](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>다음 단계
다음 자습서를 진행합니다. 

> [!div class="nextstepaction"]
>[Azure SQL Database의 한 테이블에서 Azure Blob 스토리지로 데이터 증분 복사](tutorial-incremental-copy-powershell.md)
