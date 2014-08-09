<properties umbracoNaviHide="0" pageTitle="Storage Account Concepts | Azure" metaKeywords="Azure storage, storage service, service, storage account, account, create storage account, create account" description="Learn about storage account concepts." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Storage Account Concepts" authors="" />

저장소 계정 개념
================

-   **GRS(지역 중복 저장소)** 지역 중복 저장소는 동일한 지역의 보조 위치로 원활하게 데이터를 복제하여 가장 높은 수준의 저장소 내구성을 제공합니다. 따라서 기본 위치에 심각한 장애가 발생하면 장애 조치(Failover)를 수행할 수 있습니다. 보조 위치는 기본 위치에서 수백 마일 떨어져 있습니다. GRS는 *지역에서 복제* 기능을 통해 구현됩니다. 이 기능은 저장소 계정에 대해 기본적으로 설정되지만 사용하지 않으려는 경우(예: 회사 정책으로 인해 사용할 수 없는 경우) 해제될 수 있습니다. 자세한 내용은 [Windows Azure의 지역에서 복제 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx)(영문)를 참조하십시오.

-   **LRS(로컬 중복 저장소)** 로컬 중복 저장소는 단일 위치에서 영속성 및 가용성이 뛰어난 저장소를 제공합니다. 로컬 중복 저장소의 경우 계정 데이터가 동일한 데이터 센터 내에서 세 번 복제됩니다. Azure의 모든 저장소는 로컬 중복 저장소입니다. 내구성을 높이기 위해 지역에서 복제를 설정할 수 있습니다. 로컬 중복 저장소는 할인된 가격으로 제공됩니다. 자세한 내용은 [Azure 가격 책정 개요](http://www.windowsazure.com/ko-kr/pricing/details/)를 참조하십시오.

-   **선호도 그룹** *선호도 그룹*은 Azure 내 클라우드 서비스 배포 및 저장소 계정의 지리적 그룹화입니다. 선호도 그룹은 동일한 데이터 센터 또는 대상 사용자 그룹 인근의 컴퓨터 작업을 찾아 서비스 성능을 향상시킬 수 있습니다. 또한 송신에 대해 청구 요금이 발생하지 않습니다.

-   **저장소 계정 끝점** 저장소 계정 *끝점*은 Blob, 테이블 또는 큐에 액세스하기 위한 가장 높은 수준의 네임스페이스를 나타냅니다. 저장소 계정의 기본 끝점 형식은 다음과 같습니다.

    -   Blob 서비스: http://*mystorageaccount*.blob.core.windows.net

    -   테이블 서비스: http://*mystorageaccount*.table.core.windows.net

    -   큐 서비스: http://*mystorageaccount*.queue.core.windows.net

-   **저장소 계정 URL** 저장소 계정의 개체에 액세스하기 위한 URL은 저장소 계정의 개체 위치를 끝점에 추가하여 작성됩니다. 예를 들어, Blob 주소의 형식은 다음과 같습니다. http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

-   **저장소 액세스 키** 저장소 계정을 만들면 Azure에서 두 개의 512비트 저장소 액세스 키를 생성합니다. 이 키는 저장소 계정에 액세스하는 경우 인증에 사용됩니다. Azure에서는 두 개의 저장소 액세스 키를 제공하므로 저장소 서비스나 해당 서비스에 대한 액세스 중단 없이 키를 다시 생성할 수 있습니다.

-   **최소 및 상세 메트릭** 저장소 계정에 대해 모니터링 설정에서 최소 또는 상세 메트릭을 구성할 수 있습니다. *최소 메트릭*은 수신/송신, 가용성, 대기 시간, 성공률 등 Blob, 테이블 및 큐 서비스에 대해 집계되는 데이터에 대한 메트릭을 수집합니다. *상세 메트릭*은 동일한 메트릭에 대해 서비스 수준 집계뿐만 아니라 작업 수준 세부 정보를 수집합니다. 세부 정보 표시 메트릭을 사용하면 응용 프로그램 작업 중 발생하는 문제를 더 면밀하게 분석할 수 있습니다. 사용 가능한 메트릭의 전체 목록은 [저장소 분석 메트릭 테이블 스키마](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh343264.aspx)(영문)를 참조하십시오. 저장소 모니터링에 대한 자세한 내용은 [저장소 분석 메트릭 정보](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh343258.aspx)를 참조하십시오.

-   **로깅** 로깅은 Blob, 테이블, 큐에 대한 읽기, 쓰기, 삭제 요청 로깅을 사용할 수 있는 저장소 계정의 구성 가능한 기능입니다. 로깅은 Azure 관리 포털에서 구성하지만 로그를 관리 포털에서 볼 수는 없습니다. 로그는 저장소 계정, \$logs 컨테이너에서 저장되고 액세스됩니다. 자세한 정보는 [저장소 분석 개요](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh343268.aspx)를 참조하십시오.


