<properties linkid="manage-services-how-to-scale-a-sqldb" urlDisplayName="How to scale" pageTitle="How to scale a SQL Database - Azure" metaKeywords="" description="Learn about options for scaling your SQL Database in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Scale a SQL Database Solution" authors="" solutions="" manager="" editor="" />

SQL 데이터베이스 솔루션을 확장하는 방법
=======================================

Azure에서 데이터베이스 확장성은 규모 확장과 같은 말입니다. 규모 확장에서는 작업이 데이터 센터의 여러 상용 서버로 재분산됩니다. 규모 확장은 데이터 용량 또는 성능 문제를 해결하기 위한 전략입니다. 고성장 궤적을 그리는 초대형 데이터베이스의 경우에는 이러한 데이터베이스에 액세스하는 사용자 수가 많건 적건 관계없이 결국 규모 확장 전략이 필요하게 됩니다.

Azure에서는 페더레이션을 통해 규모 확장이 가장 잘 수행됩니다. SQL 데이터베이스 페더레이션은 행 분할을 기반으로 합니다. 행 분할에서는 하나 이상의 테이블이 행으로 분할되어 여러 페더레이션 멤버에 분배됩니다.

페더레이션이 모든 확장성 문제에 대한 유일한 해결책은 아닙니다. 경우에 따라 데이터 또는 응용 프로그램 요구 사항의 특성으로 인해 보다 간단한 접근 방법이 필요합니다. 다음 목록은 복잡도에 따른 잠재적 솔루션을 보여 줍니다.

데이터베이스 크기 늘리기
------------------------

데이터베이스는 각 버전에서 설정한 최대값에 따라 고정 크기로 만들어집니다. Web Edition의 경우 데이터베이스를 최대 5GB까지 늘릴 수 있습니다. Business Edition의 경우 최대 데이터베이스 크기는 150GB입니다. 데이터 용량을 늘리는 가장 확실한 방법은 버전과 최대 크기를 변경하는 것입니다.

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

여러 데이터베이스 사용 및 사용자 할당
-------------------------------------

제한된 시나리오에서는 데이터베이스의 복사본을 만든 후 각 데이터베이스에 로그인 및 사용자를 할당할 수 있습니다. 페더레이션이 옵션으로 가능하기 이전에는 이러한 방법이 작업을 재분산하는 일반적인 접근 방법이었습니다. 이 접근 방법은 단기간 사용한 후 나중에 온-프레미스에 유지하는 주 데이터베이스에 병합하는 데이터베이스의 경우와 읽기 전용 데이터를 제공하는 솔루션의 경우 실행 가능합니다.

페더레이션 사용
---------------

SQL 데이터베이스에서 페더레이션은 확장성과 성능을 향상시키는 데 사용됩니다. 한 데이터베이스 내의 하나 이상의 테이블이 행으로 분할되어 여러 데이터베이스(페더레이션 멤버)에 분배됩니다. 이러한 유형의 행 분할을 흔히 '분할'이라고 합니다. 이러한 분할이 유용한 기본 시나리오는 확장성과 성능을 향상시키거나 용량을 관리해야 하는 경우입니다.

페더레이션은 Business Edition에서 지원됩니다. 자세한 내용은 [SQL 데이터베이스의 페더레이션](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh597452.aspx) 및 [SQL 데이터베이스 페더레이션 자습서 - DBA](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh778416.aspx)를 참조하십시오.

다른 형식의 저장소 고려
-----------------------

Azure는 테이블 저장소 및 Blob 저장소를 비롯한 여러 형식의 데이터 저장소를 지원합니다. 관계형 기능이 필요하지 않은 경우 테이블 또는 Blob 저장소가 더 경제적일 수 있습니다.

[Federations in SQL Database]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh597452.aspx
[SQL Database Federations Tutorial - DBA]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh778416.aspx
