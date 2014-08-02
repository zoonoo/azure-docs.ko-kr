<properties linkid="manage-services-how-to-create-a-storage-account" urlDisplayName="How to create" pageTitle="How to create a storage account | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" authors="tysonn" solutions="" manager="paulettm" editor="cgronlun" />

저장소 계정을 만드는 방법
=========================

Azure에서 Blob, 테이블 및 큐 서비스에 파일과 데이터를 저장하려면 데이터를 저장하려는 지리적 지역에 저장소 계정을 만들어야 합니다. 하나의 저장소 계정은 최대 200TB의 데이터를 포함할 수 있으며 개별 Azure 구독마다 최대 20개의 저장소 계정을 만들 수 있습니다. 자세한 내용은 [Azure 저장소 확장성 및 성능 목표](http://msdn.microsoft.com/en-us/library/dn249410.aspx)(영문)를 참조하십시오.

이 항목에서는 Azure 관리 포털에서 저장소 계정을 만드는 방법에 대해 설명합니다.
**참고**

Azure 가상 컴퓨터의 경우 배포 위치에 아직 저장소 계정이 없으면 해당 위치에서 자동으로 저장소 계정이 작성됩니다. 저장소 계정 이름은 가상 컴퓨터 이름을 기반으로 합니다.

목차
----

-   [방법: 저장소 계정 만들기](#create)
-   [다음 단계](#next)

방법: 저장소 계정 만들기
------------------------

1.  [관리 포털](https://manage.windowsazure.com)에 로그인합니다.

2.  **새로 만들기**, **저장소**, **빠른 생성**을 차례로 클릭합니다.

    ![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3.  **URL**에서 저장소 계정의 URL에 사용할 하위 도메인 이름을 입력합니다. 저장소의 개체에 액세스하려면 끝점에 개체 위치를 추가합니다. 예를 들어, Blob에 액세스하는 URL은 http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob*이 될 수 있습니다.

4.  **지역/선호도 그룹**에서 저장소의 지역 또는 선호도 그룹을 선택합니다. 사용 중인 다른 Azure 서비스와 동일한 데이터 센터에 저장소 서비스가 있도록 하려면 지역 대신 선호도 그룹을 선택합니다. 이렇게 하면 성능이 향상될 수 있고 송신 요금이 발생하지 않습니다.

    > [WACOM.NOTE]
    > 선호도 그룹을 만들려면 관리 포털의 **네트워크** 영역을 열고, **선호도 그룹**을 클릭한 다음 **새 선호도 그룹 만들기** 또는 **만들기**를 클릭합니다. 이전 관리 포털에서 만든 선호도 그룹을 사용할 수 있습니다. 이전 포털을 열려면 제목 표시줄에서 **미리 보기**를 클릭하고 **이전 포털로 돌아가기**를 클릭합니다. 현재 포털로 돌아오려면 포털 아래쪽의 **미리 보기 포털 보기**를 클릭합니다. Azure 서비스 관리 API를 사용하여 선호도 그룹을 만들고 관리할 수도 있습니다. 자세한 내용은 [선호도 그룹 작업](http://msdn.microsoft.com/en-us/library/windowsazure/ee460798.aspx)을 참조하십시오.

5.  Azure 구독이 두 개 이상인 경우 **구독** 필드가 표시됩니다. **구독**에서 저장소 계정을 사용할 Azure 구독을 입력합니다. 하나의 구독에 대해 최대 다섯 개의 저장소 계정을 만들 수 있습니다.

6.  **복제**에서 저장소 계정에 사용할 복제 수준을 선택합니다.

    기본적으로 복제는 **지역 중복**으로 설정됩니다. 지역 중복 복제를 사용하면 기본 위치에 심각한 재해가 발생하는 경우 저장소 계정 및 포함된 모든 데이터를 보조 위치로 장애 조치(Failover)합니다. Azure는 동일한 지역에서 보조 위치를 할당하며 이 설정은 변경될 수 없습니다. 장애 조치(Failover) 후 보조 위치는 저장소 계정의 기본 위치가 되며 데이터는 새 보조 위치로 복제됩니다.

    보조 위치에서 데이터를 읽을 수 있도록 하려면 **읽기 액세스 지역 중복** 복제를 선택할 수 있습니다. 이 옵션을 사용하면 지역 중복 복제가 제공되고 보조 위치의 복제된 데이터에 읽기 전용으로 액세스할 수 있습니다. 읽기 액세스 지역 중복 복제에서는 기본 또는 보조 위치 중 하나가 사용 불가능하게 되는 경우 나머지 사용 가능한 기본 또는 보조 위치에서 데이터에 액세스할 수 있습니다.

    세 번째 복제 옵션, **읽기 액세스 지역 중복**은 현재 미리 보기에 있습니다. 이 옵션을 사용하면 보조 위치의 복제된 데이터에 읽기 전용으로 액세스할 수 있습니다. 읽기 액세스 지역 중복 복제에서는 기본 또는 보조 위치 중 하나가 사용 불가능하게 되는 경우 나머지 사용 가능한 기본 또는 보조 위치에서 데이터에 액세스할 수 있습니다.

    > [WACOM.NOTE]
    > 미리 보기에 있는 동안 읽기 액세스 지역 중복 복제를 사용하려면 사용자 구독에서 이 기능을 사용할 수 있게 설정하도록 수동으로 요청해야 합니다. [Azure 미리 보기 기능](https://account.windowsazure.com/PreviewFeatures) 페이지를 방문하여 사용자 구독에 대해 읽기 액세스 지역 중복 복제를 요청하십시오. 읽기 액세스 지역 중복 복제에 대한 자세한 내용은 [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)(영문)를 참조하십시오. 사용자 구독에 읽기 액세스 지역 중복 복제가 미리 보기 기능으로 사용하도록 설정되어 있지 않으면 저장소 계정에 대해 선택하는 옵션이 사용하지 않도록 설정됩니다.

    저장소 계정 복제에 대한 가격 정보는 [저장소 가격 정보](http://www.windowsazure.com/en-us/pricing/details/storage/)를 참조하십시오.

7.  **저장소 계정 만들기**를 클릭합니다.

    저장소 계정을 만드는 데 몇 분 정도 걸릴 수 있습니다. 상태를 확인하려면 포털 화면의 아래쪽에서 알림을 모니터링할 수 있습니다. 저장소 계정이 만들어지면 새 저장소 계정이 **온라인** 상태가 되고 새 저장소 계정을 사용할 준비가 됩니다.

    ![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)

다음 단계
---------

-   Azure 저장소 서비스에 대한 자세한 내용은 [클라우드 저장소 이해](http://www.windowsazure.com/en-us/develop/net/fundamentals/cloud-storage/)(영문) 및 [Blob, 큐 및 테이블](http://msdn.microsoft.com/en-us/library/gg433040.aspx)(영문)을 참조하십시오.

-   [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)(영문)를 방문하십시오.

-   앱에서 Azure Blob, 테이블 및 큐 서비스를 사용하도록 구성하십시오. [Azure 개발자 센터](http://www.windowsazure.com/en-us/develop/overview/)에서는 .NET, Node.js, Java 및 PHP 응용 프로그램에서 Blob, 테이블 및 큐 저장소 서비스를 사용하기 위한 방법 가이드를 제공합니다. 프로그래밍 언어별 지침은 해당 언어의 방법 가이드를 참조하십시오.


