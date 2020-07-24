---
title: Azure Data Factory 커넥터 개요
description: Data Factory에서 지원 되는 커넥터에 대해 알아봅니다.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 334d5b5113dba17c5abc2b4f2520bde0d16e4c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87007449"
---
# <a name="azure-data-factory-connector-overview"></a>Azure Data Factory 커넥터 개요

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory는 복사, 데이터 흐름, 조회, 메타 데이터 가져오기 및 삭제 작업을 통해 다음과 같은 데이터 저장소와 형식을 지원 합니다. 각 데이터 저장소를 클릭 하 여 지원 되는 기능 및 해당 구성에 대해 자세히 알아보세요.

## <a name="supported-data-stores"></a>지원되는 데이터 저장소

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="supported-file-formats"></a>지원되는 파일 형식

Azure Data Factory은 다음과 같은 파일 형식을 지원 합니다. 서식 기반 설정에 대 한 각 문서를 참조 하세요.

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

- [복사 활동](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [활동 삭제](delete-activity.md)
