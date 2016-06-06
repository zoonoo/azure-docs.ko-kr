
<!--
includes/sql-data-warehouse-backup-retention-policies.md

Latest Freshness check:  2016-05-05 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-overview-expectations.md
articles/sql-data-warehouse/sql-data-warehouse-overview-backup-and-restore.md
-->
SQL 데이터 웨어하우스는 Azure 저장소 스냅숏을 사용하여 적어도 8시간마다 모든 라이브 데이터의 스냅숏을 만듭니다. 이러한 스냅숏은 7일 동안 유지됩니다. 이를 통해 지난 7일 내에 적어도 21개의 특정 시점 중 하나로 마지막 스냅숏 작업 시까지 데이터를 복원할 수 있습니다.

SQL 데이터 웨어하우스는 데이터베이스가 삭제되기 전에 데이터베이스 스냅숏을 생성하여 7일 동안 유지합니다. 이 경우가 발생할 때 라이브 데이터베이스에서 스냅숏을 더 이상 유지하지 않습니다. 이렇게 하면 삭제된 지점으로 삭제된 데이터베이스를 복원할 수 있습니다.

SQL 데이터 웨어하우스는 지역별 오류 시 추가된 복구 기능을 위해 스냅숏을 서로 다른 지리적 Azure 지역에 비동기적으로 복사합니다. Azure 지역에서 오류로 인해 데이터베이스에 액세스할 수 없는 경우 지역 중복 스냅숏 중 하나에 데이터베이스를 복원할 수 있습니다.

<!---HONumber=AcomDC_0525_2016-->