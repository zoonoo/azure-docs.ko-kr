---
title: Azure Storage 인벤토리를 사용 하 여 blob 수 및 크기 계산
description: 컨테이너 당 blob의 개수 및 전체 크기를 계산 하는 방법에 대해 알아봅니다.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 92e5b00cd655677cdc3096bc2142dfe1b704adf2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102638167"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>Azure Storage 인벤토리를 사용 하 여 컨테이너 당 blob 수 및 총 크기 계산

이 문서에서는 Azure Blob Storage 인벤토리 기능 및 Azure Synapse를 사용 하 여 컨테이너 당 blob의 blob 수와 총 크기를 계산 합니다. 이러한 값은 컨테이너 당 blob 사용을 최적화 하는 경우에 유용 합니다.

Blob 메타 데이터는이 메서드에 포함 되지 않습니다. Azure Blob Storage inventory 기능은 기본 매개 변수와 함께 [목록 blob](/rest/api/storageservices/list-blobs) REST API를 사용 합니다. 따라서이 예제에서는 스냅숏, ' $ ' 컨테이너 등을 지원 하지 않습니다.

> [!IMPORTANT]
> Blob 인벤토리는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 약관에 대 한 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 을 참조 하세요.

## <a name="enable-inventory-reports"></a>인벤토리 보고서 사용

이 메서드의 첫 번째 단계는 저장소 계정에서 [인벤토리 보고서를 사용 하도록 설정](blob-inventory.md#enable-inventory-reports) 하는 것입니다. 첫 번째 보고서를 생성 하기 위해 인벤토리 보고서를 설정한 후 최대 24 시간 동안 기다려야 할 수 있습니다.

분석할 인벤토리 보고서가 있는 경우 보고서 CSV 파일이 있는 컨테이너에 대 한 blob 읽기 권한을 사용자에 게 부여 합니다.

1. 인벤토리 CSV 보고서 파일을 사용 하 여 컨테이너로 이동 합니다.
1. **Access Control (IAM)**, **역할 할당 추가** 를 차례로 선택 합니다.

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="역할 할당 추가 선택":::

1. **역할** 드롭다운 목록에서 **저장소 Blob 데이터 판독기** 를 선택 합니다.

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="드롭다운에서 저장소 Blob 데이터 판독기 역할 추가":::

1. **Select** 필드에서 보고서를 실행 하는 데 사용 하 고 있는 계정의 전자 메일 주소를 입력 합니다.

## <a name="create-an-azure-synapse-workspace"></a>Azure Synapse 작업 영역 만들기

그런 다음 SQL 쿼리를 실행 하 여 인벤토리 결과를 보고 하는 [Azure Synapse 작업 영역을 만듭니다](/azure/synapse-analytics/get-started-create-workspace) .

## <a name="create-the-sql-query"></a>SQL 쿼리 만들기

Azure Synapse 작업 영역을 만든 후 다음 단계를 수행 합니다.

1. [https://web.azuresynapse.net](https://web.azuresynapse.net)으로 이동합니다.
1. 왼쪽 가장자리에서 **개발** 탭을 선택 합니다.
1. 항목을 추가 하려면 큼 더하기 기호 (+)를 선택 합니다.
1. **SQL 스크립트** 를 선택 합니다.

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="SQL 스크립트를 선택 하 여 새 쿼리 만들기":::

## <a name="run-the-sql-query"></a>SQL 쿼리 실행

1. Azure Synapse 작업 영역에서 다음 SQL 쿼리를 추가 하 여 [인벤토리 CSV 파일을 읽습니다](/azure/synapse-analytics/sql/query-single-csv-file#read-a-csv-file).

    `bulk`매개 변수의 경우 분석 하려는 인벤토리 보고서 CSV 파일의 URL을 사용 합니다.

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

1. 오른쪽의 속성 창에서 SQL 쿼리 이름을로 설정 합니다.

1. CTRL + S를 누르거나 **모두 게시** 단추를 선택 하 여 SQL 쿼리를 게시 합니다.

1. **실행** 단추를 선택 하 여 SQL 쿼리를 실행 합니다. 컨테이너 당 blob 수 및 총 크기는 **결과** 창에 보고 됩니다.

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="스크립트를 실행 하 여 blob 수 및 총 크기를 계산 하는 출력입니다.":::

## <a name="next-steps"></a>다음 단계

- [Azure Storage blob 인벤토리를 사용 하 여 blob 데이터 관리](blob-inventory.md)
- [Blob 컨테이너의 총 청구 크기 계산](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)
