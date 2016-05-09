
<!--
includes/sql-data-warehouse-include-pause-description.md

Latest Freshness check:  2016-04-22 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-powershell.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-rest-api.md

-->
비용을 절약하기 위해 필요에 따라 계산 리소스를 일지 중지 및 다시 시작할 수 있습니다. 예를 들어, 밤 시간과 주말에 데이터베이스를 사용하지 않으려면 해당 시간에 일시 중지했다가 주간에 다시 시작할 수 있습니다. 데이터베이스 일시 중지 중에는 DWU 요금이 부과되지 않습니다.

데이터베이스를 일시 중지하면

- 계산 및 메모리 리소스가 데이터 센터에서 사용 가능한 리소스의 풀에 반환됩니다.
- 일시 중지 중에는 DWU 비용이 0입니다.
- 데이터 저장소에는 영향이 없으며 데이터는 그대로 유지됩니다. 
- SQL 데이터 웨어하우스가 실행 중이거나 큐에 있는 모든 작업을 취소합니다.

<!---HONumber=AcomDC_0427_2016-->