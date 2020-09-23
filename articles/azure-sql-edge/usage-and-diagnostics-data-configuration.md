---
title: Azure SQL Edge 사용량 및 진단 데이터 구성
description: Azure SQL Edge에서 사용 현황 및 진단 데이터를 구성 하는 방법에 대해 알아봅니다.
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: 130e23c290ce493d3fb92f6dd0be4cd7c61a86fd
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888041"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Azure SQL Edge 사용량 및 진단 데이터 구성

기본적으로 Azure SQL Edge는 고객이 응용 프로그램을 사용 하는 방법에 대 한 정보를 수집 합니다. 특히 Azure SQL Edge는 배포 환경, 사용 및 성능에 대 한 정보를 수집 합니다. 이 정보는 Microsoft에서 고객의 요구에 맞게 제품을 향상시키는 데 도움이 됩니다. 예를 들어 Microsoft는 관련 버그를 수정 하 고, Azure SQL Edge를 사용 하는 방법에 대 한 설명서를 개선 하 고, 제품에 기능을 추가 하 여 고객에 게 더 나은 서비스를 제공할 수 있도록 고객이 직면 하는 오류 코드의 종류에 대 한 정보를 수집 합니다.

특히 Microsoft는 이러한 메커니즘을 통해 다음과 같은 유형의 정보는 전송하지 않습니다.

- 사용자 테이블 내에 있는 값입니다.
- 모든 로그온 자격 증명 또는 기타 인증 정보
- 개인 또는 고객 데이터.

다음 예제 시나리오는 제품을 개선하는 데 도움이 되는 기능 사용 정보를 포함합니다.

사용 및 진단 데이터 수집에 사용 되는 쿼리의 예제 쿼리는 아래에 제공 되어 있습니다. 이 쿼리는 Azure SQL Edge에서 사용 되는 다양 한 스트리밍 데이터 원본의 개수와 유형을 식별 합니다. 이 데이터는 microsoft가 이러한 데이터 원본에 연결 된 성능 및 사용자 환경을 향상 시킬 수 있도록 일반적으로 사용 되는 스트리밍 데이터 원본을 식별 하는 데 도움이 됩니다. 

```sql
select 
count(*) as [count], sum(inputs) as inputs, sum(outputs) as outputs, sum(linked_to_job) 
as linked_to_job, data_source_type  
from ( 
select isnull(value,'unknown') as data_source_type, inputs, outputs, linked_to_job  
from 
    ( 
        select 
        convert(sysname, lower(substring(ds.location, 0, charindex('://', ds.location))), 1) as data_source_type, 
        isnull(inputs, 0) as inputs, isnull(outputs, 0) as outputs, isnull(js.stream_id/js.stream_id, 0) as linked_to_job 
        from sys.external_streams es              
        join sys.external_data_sources ds 
             on es.data_source_id = ds.data_source_id             
        left join 
            ( 
            select stream_id, max(cast(is_input as int)) inputs, max(cast(is_output as int)) outputs 
            from sys.external_job_streams group by stream_id 
            ) js                
             on js.stream_id = es.object_id 
    ) ds            
left join 
    (
        select value from string_split('edgehub,sqlserver,kafka', ',')) as known_ep on data_source_type = value 
    ) known_ds        
group by data_source_type
```

## <a name="disable-usage-and-diagnostic-data-collection"></a>사용 및 진단 데이터 수집 사용 안 함

다음 방법 중 하나를 사용 하 여 Azure SQL Edge에서 사용 및 진단 데이터 수집을 사용 하지 않도록 설정할 수 있습니다.

> [!NOTE]
> 개발자 버전에서는 사용 현황 및 진단 데이터를 사용 하지 않도록 설정할 수 없습니다.

### <a name="disable-usage-and-diagnostics-using-environment-variables"></a>환경 변수를 사용 하 여 사용 및 진단 사용 안 함

Azure SQL Edge에서 사용 및 진단 데이터 수집을 사용 하지 않도록 설정 하려면 다음 환경 변수를 추가 하 고 해당 값을로 설정 `*False*` 합니다. 환경 변수를 사용 하 여 Azure SQL Edge를 구성 하는 방법에 대 한 자세한 내용은 [환경 변수를 사용한 구성](configure.md#configure-by-using-environment-variables)을 참조 하세요.

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE-사용 및 진단 데이터 수집을 사용 하도록 설정 합니다. 이 값이 기본 구성입니다.
- FALSE-사용 및 진단 데이터 수집을 사용 하지 않도록 설정 합니다.

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>Mssql 파일을 사용 하 여 사용 및 진단 사용 안 함

Azure SQL Edge에서 사용 및 진단 데이터 수집을 사용 하지 않도록 설정 하려면 SQL Edge 모듈의/var/opt/mssql/폴더에 매핑되는 영구 저장소 드라이브의 mssql 파일에 다음 줄을 추가 합니다. Mssql 파일을 사용 하 여 Azure SQL Edge를 구성 하는 방법에 대 한 자세한 내용은 [mssql 파일을 사용 하 여 구성](configure.md#configure-by-using-an-mssqlconf-file)을 참조 하세요.

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>사용 현황 및 진단 데이터 수집에 대 한 로컬 감사

Azure SQL Edge 사용량 및 진단 데이터 수집의 로컬 감사 구성 요소는 서비스에서 수집 된 데이터를 지정 된 폴더에 기록 하 여 Microsoft에 전송 되는 데이터 (로그)를 나타낼 수 있습니다. 로컬 감사의 목적은 고객들이 규정 준수 또는 개인 정보 유효성 검사의 이유로 이 기능을 사용하여 Microsoft에서 수집하는 모든 데이터를 확인하기 위함입니다.

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>사용 현황 및 진단 데이터의 로컬 감사 사용

Azure SQL Edge에서 로컬 감사 사용 및 진단 데이터를 사용 하도록 설정 하려면

1. 새 로컬 감사 로그 저장소에 대 한 대상 디렉터리를 만듭니다. 이 대상 디렉터리는 호스트에 있거나 컨테이너 내에 있을 수 있습니다. 아래 예제에서 대상 디렉터리는 SQL Edge의/var/opt/mssql/path에 매핑되는 동일한 탑재 볼륨에 생성 됩니다.

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. 환경 변수 또는 mssql 파일을 사용 하 여 사용 현황 및 진단 데이터 감사를 구성 합니다.

   - 환경 변수 사용-SQL Edge 배포에 다음 환경 변수를 추가 하 고 감사 파일의 대상 디렉터리를 지정 합니다.
   
     `*MSSQL_TELEMETRY_DIR = <host mount path>/audit*`
   
   - Mssql 파일 사용-mssql 파일에 다음 줄을 추가 하 고 감사 파일의 대상 디렉터리를 지정 합니다.
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = <host mount path>/audit
       ```  

## <a name="next-steps"></a>다음 단계

- [Azure SQL Edge에 연결](connect.md)
- [SQL Edge로 엔드투엔드 IoT 솔루션 빌드](tutorial-deploy-azure-resources.md)
