---
title: Azure Storage 인벤토리를 사용하여 Blob 수 및 크기 계산
description: 컨테이너당 Blob의 수 및 전체 크기를 계산하는 방법을 알아봅니다.
services: storage
author: twooley
ms.author: twooley
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: e752d40ce2f237c2ab08bac2e71133cd06ec40e4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277184"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Azure Storage 인벤토리를 사용하여 컨테이너당 Blob 수 및 총 크기 계산

이 문서에서는 Azure Blob Storage 인벤토리 기능과 Azure Synapse를 사용하여 컨테이너당 Blob의 수 및 총 크기를 계산합니다. 이 값은 컨테이너당 Blob 사용량을 최적화하는 경우에 유용합니다.

Blob 메타데이터는 이 메서드에 포함되지 않습니다. Azure Blob Storage 인벤토리 기능은 기본 매개 변수와 함께 [목록 Blob](/rest/api/storageservices/list-blobs) REST API를 사용합니다. 따라서 예제에서는 스냅샷, ‘$’ 컨테이너 등을 지원하지 않습니다.

> [!IMPORTANT]
> Blob 인벤토리는 현재 **미리 보기** 로 제공됩니다. 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="enable-inventory-reports"></a>인벤토리 보고서 사용

이 메서드의 첫 번째 단계는 스토리지 계정에서 [인벤토리 보고서를 사용하도록 설정](blob-inventory.md#enable-inventory-reports)하는 것입니다. 첫 번째 보고서를 생성하기 위해 인벤토리 보고서를 사용하도록 설정한 후 최대 24시간을 기다려야 할 수 있습니다.

분석할 인벤토리 보고서가 있는 경우 보고서 CSV 파일이 있는 컨테이너에 대한 Blob 읽기 권한을 자신에게 부여합니다.

1. 인벤토리 CSV 보고서 파일이 있는 컨테이너로 이동합니다.
1. **액세스 제어(IAM)** , **역할 할당 추가** 를 차례로 선택합니다.

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="역할 할당 추가 선택":::

1. **역할** 드롭다운 목록에서 **Storage Blob 데이터 읽기 권한자** 를 선택합니다.

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="드롭다운에서 Storage Blob 데이터 읽기 권한자 역할 추가":::

1. **선택** 필드에서 보고서를 실행하는 데 사용하고 있는 계정의 메일 주소를 입력합니다.

## <a name="create-an-azure-synapse-workspace"></a>Azure Synapse 작업 영역 만들기

그런 다음, SQL 쿼리를 실행하여 인벤토리 결과를 보고할 [Azure Synapse 작업 영역을 만듭니다](../../synapse-analytics/get-started-create-workspace.md).

## <a name="create-the-sql-query"></a>SQL 쿼리 만들기

Azure Synapse 작업 영역을 만든 후 다음 단계를 수행합니다.

1. [https://web.azuresynapse.net](https://web.azuresynapse.net)으로 이동합니다.
1. 왼쪽 페이지에서 **개발** 탭을 선택합니다.
1. 큰 더하기 기호(+)를 선택하여 항목을 추가합니다.
1. **SQL 스크립트** 를 선택합니다.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="SQL 스크립트를 선택하여 새 쿼리 만들기":::

## <a name="run-the-sql-query"></a>SQL 쿼리 실행

1. Azure Synapse 작업 영역에 다음 SQL 쿼리를 추가하여 [인벤토리 CSV 파일을 읽습니다](../../synapse-analytics/sql/query-single-csv-file.md#read-a-csv-file).

    `bulk` 매개 변수의 경우 분석하려는 인벤토리 보고서 CSV 파일의 URL을 사용합니다.

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. 오른쪽 속성 창에서 SQL 쿼리의 이름을 지정합니다.

1. Ctrl+S를 누르거나 **모두 게시** 단추를 선택하여 SQL 쿼리를 게시합니다.

1. **실행** 단추를 선택하여 SQL 쿼리를 실행합니다. 컨테이너당 Blob 수 및 총 크기는 **결과** 창에 보고됩니다.

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="스크립트를 실행하여 Blob 수 및 총 크기를 계산한 출력":::

## <a name="next-steps"></a>다음 단계

- [Azure Storage Blob 인벤토리를 사용하여 Blob 데이터 관리](blob-inventory.md)
- [Blob 컨테이너의 총 청구 크기 계산](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)