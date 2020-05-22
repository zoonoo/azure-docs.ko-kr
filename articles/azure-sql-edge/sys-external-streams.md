---
title: sys.external_streams(Transact-SQL) - Azure SQL Edge(미리 보기)
description: Azure SQL Edge(미리 보기)에서 sys.external_streams를 사용하는 방법에 대해 알아봅니다.
keywords: sys.external_streams, SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: d47139b0b2a20ecfcf92bfc55a8b47c8e3cf7dab
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594472"
---
# <a name="sysexternal_streams-transact-sql"></a>sys.external_streams(Transact-SQL)

데이터베이스 범위 내에서 만든 각 외부 스트리밍 작업에 대해 하나의 행을 반환합니다.

|열 이름|데이터 형식|Description|  
|-----------------|---------------|-----------------|
|**name**|**sysname**|스트림의 이름입니다. 데이터베이스 내에서 고유합니다.|
|**object_id**|**int**|스트리밍 개체의 개체 ID 번호입니다. 데이터베이스 내에서 고유합니다.|
|**principal_id**|**int**|이 어셈블리를 소유하는 주체의 ID입니다.|
|**schema_id**|**int**| 개체를 포함하는 스키마의 ID입니다.|
|**parent_object_id**|**id**| 이 스트림의 부모 개체에 대한 개체 ID 번호입니다. 현재 구현에서 이 값은 항상 null입니다.|
|**type**|**char(2)**|개체 유형입니다. 스트림 개체의 경우 형식은 항상 ‘ES’입니다.|
|**type_desc**|**nvarchar(60)**| 개체 형식에 대한 설명입니다. 스트림 개체의 경우 형식은 항상 ‘EXTERNAL_STREAM’입니다.|
|**create_date**|**datetime**| 개체를 만든 날짜입니다.|
|**modify_date**|**datetime**| ALTER 문을 사용하여 개체를 마지막으로 수정한 날짜입니다.|
|**is_ms_shipped**|**bit**| 내부 구성 요소에서 만든 개체입니다.|  
|**is_published**|**bit**|개체가 게시됩니다.|  
|**is_schema_published**|**bit**|개체의 스키마만 게시됩니다.|
|**max_column_id_used**|**bit**| 이 열은 내부 용도로 사용되며 나중에 제거될 예정입니다.|  
|**uses_ansi_nulls**|**bit**| 스트림 개체는 SET ANSI_NULLS 데이터베이스 옵션을 ON으로 설정하여 생성되었습니다.|
|**data_source_id**|**int**| 스트림 개체가 나타내는 외부 데이터 원본에 대한 개체 ID입니다. |  
|**file_format_id**|**int**| 스트림 개체에서 사용하는 외부 파일 형식에 대한 개체 ID입니다. 외부 파일 형식에는 외부 스트림에서 참조하는 데이터의 실제 레이아웃을 지정해야 합니다.| 
|**location**|**varchar(max)**| 외부 스트림 개체의 대상입니다. 자세한 내용은 [외부 스트림 만들기](overview.md)를 참조하세요. |
|**input_option**|**varchar(max)**| 외부 스트림을 만드는 동안 사용되는 입력 옵션입니다. 자세한 내용은 [외부 스트림 만들기](overview.md)를 참조하세요. |
|**output_option**|**varchar(max)**| 외부 스트림을 만드는 동안 사용되는 출력 옵션입니다. 자세한 내용은 [외부 스트림 만들기](overview.md)를 참조하세요. | 

## <a name="permissions"></a>사용 권한

사용자가 소유하고 있거나 사용 권한을 부여 받은 보안 개체에 대해서만 카탈로그 뷰의 메타데이터를 볼 수 있습니다. 자세한 내용은 [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/)을 참조하세요.

## <a name="see-also"></a>참고 항목

- [카탈로그 뷰(Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [시스템 뷰(Transact-SQL)](/sql/t-sql/language-reference/)
