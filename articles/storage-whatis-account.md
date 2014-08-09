<properties linkid="manage-services-what-is-a-storage-account" urlDisplayName="What is a Storage Account" pageTitle="What is a storage account? | Microsoft Azure" metaKeywords="" description="Learn about the different types of storage accounts available in Azure, and get definitions for key storage terms." metaCanonical="" services="storage" documentationCenter="" title="What is a Storage Account?" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

저장소 계정 정의
================

Azure 저장소에는 세 가지 서비스 Blob 저장소, 테이블 저장소, 큐 저장소가 있습니다. 이러한 서비스는 모든 저장소 계정에 포함되어 있습니다. 저장소 계정은 Blob, 큐 및 테이블 작업에 필요한 고유 네임스페이스를 제공합니다.

저장소 계정이 2012년 6월 8일 이후에 만들어진 경우 최대 200TB의 Blob, 큐 및 테이블 데이터가 포함될 수 있습니다. 이 날짜 이전에 만들어진 저장소 계정의 경우 총 용량은 100TB입니다. 단일 구독에서 최대 20개의 고유하게 명명된 저장소 계정을 만들 수 있습니다. 저장소 계정에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표](http://msdn.microsoft.com/ko-kr/library/dn249410.aspx)(영문)를 참조하십시오.

만든 시기를 포함하여 저장소 계정에 대한 모든 정보는 관리 포털의 **저장소** **대시보드** 페이지에 제공되어 있습니다.

저장소 비용은 네 가지 요소인 저장소 용량, 복제 체계, 저장소 트랜잭션 및 데이터 송신을 기반으로 합니다. 저장소 용량은 데이터를 저장하는 데 사용하는 저장소 계정 서비스 단위가 어느 정도인지를 나타냅니다. 단순히 데이터를 저장하는 비용은 저장하는 데이터의 양과 복제 방법에 따라 결정됩니다. 트랜잭션은 Azure 저장소에 대한 모든 읽기 및 쓰기 작업을 나타냅니다. 데이터 송신은 Azure 지역 외부에서 전송되는 데이터를 나타냅니다. 저장소 계정의 데이터에 동일한 지역에서 실행되지 않는 응용 프로그램이 액세스하는 경우 해당 응용 프로그램이 클라우드 서비스인지 다른 유형의 응용 프로그램인지 여부에 상관없이 데이터 송신 요금이 부과됩니다. Azure 서비스의 경우 데이터 송신 요금을 줄이거나 제거하기 위해 동일한 데이터 센터의 데이터와 서비스를 그룹화하는 조치를 취할 수 있습니다.

[저장소 가격 정보](http://www.windowsazure.com/ko-kr/pricing/details/#storage) 페이지에서는 저장소 용량, 복제 및 트랜잭션에 대한 자세한 가격 정보를 제공합니다. [데이터 전송 가격 정보](http://www.windowsazure.com/ko-kr/pricing/details/data-transfers/)에서는 데이터 송신에 대한 자세한 가격 정보를 제공합니다.

개념
----

-   **GRS(지역 중복 저장소)** 지역 중복 저장소는 동일한 지역의 보조 위치로 원활하게 데이터를 복제하여 가장 높은 수준의 저장소 내구성을 제공합니다. 따라서 기본 위치에 심각한 장애가 발생하면 장애 조치(Failover)를 수행할 수 있습니다. 보조 위치는 기본 위치에서 수백 마일 떨어져 있습니다. GRS는 *지역에서 복제* 기능을 통해 구현됩니다. 이 기능은 저장소 계정에 대해 기본적으로 설정되지만 사용하지 않으려는 경우(예: 회사 정책으로 인해 사용할 수 없는 경우) 해제될 수 있습니다. 자세한 내용은 [Azure 저장소의 지역에서 복제 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx)(영문)를 참조하십시오.

-   **LRS(로컬 중복 저장소)** 로컬 중복 저장소는 단일 위치에서 영속성 및 가용성이 뛰어난 저장소를 제공합니다. 로컬 중복 저장소의 경우 계정 데이터가 동일한 데이터 센터 내에서 세 번 복제됩니다. Azure의 모든 저장소는 로컬 중복 저장소입니다. 내구성을 높이기 위해 지역에서 복제를 설정할 수 있습니다. 로컬 중복 저장소는 할인된 가격으로 제공됩니다. 가격 정보는 [가격 정보](http://www.windowsazure.com/ko-kr/pricing/details/#storage)를 참조하십시오.

-   **선호도 그룹** *선호도 그룹*은 Azure 내 클라우드 서비스 배포 및 저장소 계정의 지리적 그룹화입니다. 선호도 그룹은 동일한 데이터 센터 또는 대상 사용자 그룹 인근의 컴퓨터 작업을 찾아 서비스 성능을 향상시킬 수 있습니다. 또한 저장소 계정의 데이터에 동일한 선호도 그룹에서 실행되는 서비스가 액세스하는 경우 데이터 송신 요금도 발생하지 않습니다.

-   **저장소 계정 끝점** 저장소 계정 *끝점*은 Blob, 테이블 또는 큐에 액세스하기 위한 가장 높은 수준의 네임스페이스를 나타냅니다. 저장소 계정의 기본 끝점 형식은 다음과 같습니다.

    -   Blob 서비스: http://*mystorageaccount*.blob.core.windows.net

    -   테이블 서비스: http://*mystorageaccount*.table.core.windows.net

    -   큐 서비스: http://*mystorageaccount*.queue.core.windows.net

-   **저장소 계정 URL** 저장소 계정의 개체에 액세스하기 위한 URL은 저장소 계정의 개체 위치를 끝점에 추가하여 구성됩니다. 예를 들어, Blob 주소의 형식은 다음과 같습니다. http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

-   **저장소 액세스 키** 저장소 계정을 만들면 Azure에서 두 개의 512비트 저장소 액세스 키를 생성합니다. 이 키는 저장소 계정에 액세스하는 경우 인증에 사용됩니다. Azure에서는 두 개의 저장소 액세스 키를 제공하므로 저장소 서비스나 해당 서비스에 대한 액세스 중단 없이 키를 다시 생성할 수 있습니다.

-   **최소 및 상세 메트릭** 저장소 계정에 대해 모니터링 설정에서 최소 또는 상세 메트릭을 구성할 수 있습니다. *최소 메트릭*은 수신/송신, 가용성, 대기 시간, 성공률 등 Blob, 테이블 및 큐 서비스에 대해 집계되는 데이터에 대한 메트릭을 수집합니다. *상세 메트릭*은 동일한 메트릭에 대해 서비스 수준 집계뿐만 아니라 작업 수준 세부 정보를 수집합니다. 상세 메트릭을 사용하여 응용 프로그램 작업 중 발생하는 문제를 더 면밀하게 분석할 수 있습니다. 사용 가능한 메트릭의 전체 목록은 [저장소 분석 메트릭 테이블 스키마](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh343264.aspx)(영문)를 참조하십시오. 저장소 모니터링에 대한 자세한 내용은 [저장소 분석 메트릭 정보](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh343258.aspx)를 참조하십시오.

-   **로깅** 로깅은 Blob, 테이블, 큐에 대한 읽기, 쓰기, 삭제 요청 로깅을 사용할 수 있는 저장소 계정의 구성 가능한 기능입니다. 로깅은 Azure 관리 포털에서 구성하지만 로그를 관리 포털에서 볼 수는 없습니다. 로그는 저장소 계정, $logs 컨테이너에서 저장되고 액세스됩니다. 자세한 정보는 [저장소 분석 개요](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh343268.aspx)를 참조하십시오.


