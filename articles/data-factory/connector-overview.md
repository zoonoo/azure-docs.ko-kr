---
title: Azure Data Factory 커넥터 개요
description: Data Factory에서 지원되는 커넥터에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: jianleishen
ms.openlocfilehash: 1e25ba74eddbb39467fabbd6bfb60d4a2c7d049b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110793686"
---
# <a name="azure-data-factory-connector-overview"></a>Azure Data Factory 커넥터 개요

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory는 복사, 데이터 흐름, 조회, 메타데이터 가져오기 및 삭제 작업을 통해 다음 데이터 저장소와 형식을 지원합니다. 각 데이터 저장소를 클릭하여 지원되는 기능 및 해당 구성에 대해 알아보세요.

## <a name="supported-data-stores"></a>지원되는 데이터 저장소

[!INCLUDE [Connector overview](includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>추가 데이터 저장소와 통합

Azure Data Factory는 위에서 언급한 목록보다 더 광범위한 데이터 저장소 세트에 도달할 수 있습니다. Azure Data Factory 기본 제공 커넥터 목록에 없는 데이터 저장소로/에서 데이터를 이동해야 하는 경우 다음과 같은 몇 가지 확장 가능한 옵션이 있습니다.
- 데이터베이스 및 데이터 웨어하우스의 경우 일반적으로 [일반 ODBC 커넥터](connector-odbc.md)를 사용할 수 있는 해당 ODBC 드라이버를 찾을 수 있습니다.
- SaaS 애플리케이션의 경우:
    - RESTful API를 제공하는 경우 [일반 REST 커넥터](connector-rest.md)를 사용할 수 있습니다.
    - OData 피드가 있는 경우 [일반 OData 커넥터](connector-odata.md)를 사용할 수 있습니다.
    - SOAP API를 제공하는 경우 [일반 HTTP 커넥터](connector-http.md)를 사용할 수 있습니다.
    - ODBC 드라이버가 있는 경우 [일반 ODBC 커넥터](connector-odbc.md)를 사용할 수 있습니다.
- 다른 경우에는 데이터를 ADF 지원 데이터 저장소(예: Azure Blob/File/FTP/SFTP/등)로 로드하거나 표시할 수 있는지 확인한 다음, 여기에서 ADF를 선택합니다. [Azure Function](control-flow-azure-function-activity.md), [사용자 지정 작업](transform-data-using-dotnet-custom-activity.md), [Databricks](transform-data-databricks-notebook.md)/[HDInsight](transform-data-using-hadoop-hive.md), [웹 작업](control-flow-web-activity.md)등을 통해 사용자 지정 데이터 로드 메커니즘을 호출할 수 있습니다.

## <a name="supported-file-formats"></a>지원되는 파일 형식

Azure Data Factory는 다음과 같은 파일 형식을 지원합니다. 형식 기반 설정에 대한 각 문서를 참조하세요.

- [Avro 형식](format-avro.md)
- [이진 형식](format-binary.md)
- [Common Data Model 형식](format-common-data-model.md)
- [구분된 텍스트 형식](format-delimited-text.md)
- [델타 형식](format-delta.md)
- [Excel 형식](format-excel.md)
- [JSON 형식](format-json.md)
- [ORC 형식](format-orc.md)
- [Parquet 형식](format-parquet.md)
- [XML 형식](format-xml.md)

## <a name="next-steps"></a>다음 단계

- [복사 작업](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [활동 삭제](delete-activity.md)
