---
title: Azure Data Factory에 대 한 Azure PowerShell 샘플
description: Azure PowerShell 샘플 - 데이터 팩터리를 만들고 관리할 수 있도록 하는 스크립트입니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 6df400e7ce4b66a1589a0c44089148b33ab88638
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89439064"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Azure Data Factory에 대한 Azure PowerShell 샘플

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

다음 표에는 Azure Data Factory의 Azure PowerShell 샘플 스크립트에 대한 링크가 나와 있습니다.

| 스크립트 | Description  |
|---|---|
|**데이터 복사**||
|[Azure Blob Storage의 폴더에서 다른 폴더로 Blob 복사](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 Azure Blob Storage의 폴더에서 동일한 Blob Storage에 있는 다른 폴더로 Blob을 복사합니다. |
|[SQL Server에서 Azure Blob Storage로 데이터 복사](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 SQL Server 데이터베이스에서 Azure blob 저장소로 데이터를 복사 합니다. |
|[대량 복사](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 샘플 PowerShell 스크립트는 Azure SQL Database의 데이터베이스에 있는 여러 테이블의 데이터를 Azure Synapse Analytics (이전의 SQL Data Warehouse)로 복사 합니다. |
|[증분 복사](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 샘플 PowerShell 스크립트는 원본에서 싱크로 데이터를 초기 전체 복사한 후에 원본 데이터 저장소에서 싱크 데이터 저장소로 새롭거나 업데이트된 레코드만을 로드합니다. |
|**데이터 변환**||
|[Spark 클러스터를 사용하여 데이터 변환](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 Spark 클러스터에서 프로그램을 실행하여 데이터를 변환합니다. |
|**Azure로 SSIS 패키지 이동**||
|[Azure SSIS 통합 런타임 만들기](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 이 PowerShell 스크립트는 Azure에서 SSIS(SQL Server Integration Services) 패키지를 실행하는 Azure-SSIS 통합 런타임을 프로비전합니다. |



