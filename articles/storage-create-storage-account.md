<properties linkid="manage-services-how-to-create-a-storage-account" urlDisplayName="How to create" pageTitle="How to create a storage account | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# <span id="createstorageaccount"></span></a>저장소 계정을 만드는 방법

Azure에서 Blob, 테이블, 큐 및 파일 서비스에 파일과 데이터를 저장하려면 데이터를 저장하려는 지리적 지역에 저장소 계정을 만들어야 합니다. 이 항목에서는 Azure 관리 포털에서 저장소 계정을 만드는 방법에 대해 설명합니다.

저장소 계정 용량 및 처리량에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표][Azure 저장소 확장성 및 성능 목표](영문)를 참조하세요.

> [WACOM.NOTE] Azure 가상 컴퓨터의 경우 배포 위치에 아직 저장소 계정이 없으면 해당 위치에서 자동으로 저장소 계정이 작성됩니다. 저장소 계정 이름은 가상 컴퓨터 이름을 기반으로 합니다.

## 목차

-   [방법: 저장소 계정 만들기][방법: 저장소 계정 만들기]
-   [다음 단계][다음 단계]

## <span id="create"></span></a>방법: 저장소 계정 만들기

1.  [관리 포털][관리 포털]에 로그인합니다.

2.  **새로 만들기**, **저장소**, **빠른 생성**을 차례로 클릭합니다.

    ![NewStorageAccount][NewStorageAccount]

3.  **URL**에서 저장소 계정의 URL에 사용할 하위 도메인 이름을 입력합니다. 저장소의 개체에 액세스하려면 끝점에 개체 위치를 추가합니다. 예를 들어, Blob에 액세스하는 URL은 http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob\*이 될 수 있습니다.

4.  **지역/선호도 그룹**에서 저장소의 지역 또는 선호도 그룹을 선택합니다. 사용 중인 다른 Azure 서비스와 동일한 데이터 센터에 저장소 서비스가 있도록 하려면 지역 대신 선호도 그룹을 선택합니다. 이렇게 하면 성능이 향상될 수 있고 송신 요금이 발생하지 않습니다.

    > [WACOM.NOTE] 선호도 그룹을 만들려면 관리 포털의 **설정** 영역을 열고 **선호도 그룹**을 클릭한 다음 **선호도 그룹 추가** 또는 **추가** 단추를 클릭합니다. Azure 서비스 관리 API를 사용하여 선호도 그룹을 만들고 관리할 수도 있습니다. 자세한 내용은 [선호도 그룹 작업][선호도 그룹 작업]을 참조하세요.

5.  Azure 구독이 두 개 이상인 경우 **구독** 필드가 표시됩니다. **구독**에서 저장소 계정을 사용할 Azure 구독을 입력합니다. 하나의 구독에 대해 최대 다섯 개의 저장소 계정을 만들 수 있습니다.

6.  **복제**에서 저장소 계정에 사용할 복제 수준을 선택합니다.

    [WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

7.  **저장소 계정 만들기**를 클릭합니다.

    저장소 계정을 만드는 데 몇 분 정도 걸릴 수 있습니다. 상태를 확인하려면 포털 화면의 아래쪽에서 알림을 모니터링할 수 있습니다. 저장소 계정이 만들어지면 새 저장소 계정이 **온라인** 상태가 되고 새 저장소 계정을 사용할 준비가 됩니다.

    ![StoragePage][StoragePage]

## <span id="next"></span></a>다음 단계

-   Azure 저장소에 대한 자세한 내용은 [azure.com][azure.com](영문) 및 [MSDN][MSDN]의 Azure 저장소 설명서를 참조하세요.

-   [Azure 저장소 팀 블로그][Azure 저장소 팀 블로그](영문)를 방문하세요.

  [Azure 저장소 확장성 및 성능 목표]: http://msdn.microsoft.com/ko-kr/library/dn249410.aspx
  [방법: 저장소 계정 만들기]: #create
  [다음 단계]: #next
  [관리 포털]: https://manage.windowsazure.com
  [NewStorageAccount]: ./media/storage-create-storage-account/storage_NewStorageAccount.png
  [선호도 그룹 작업]: http://msdn.microsoft.com/ko-kr/library/windowsazure/ee460798.aspx
  [storage-replication-options]: ../includes/storage-replication-options.md
  [StoragePage]: ./media/storage-create-storage-account/Storage_StoragePage.png
  [azure.com]: http://azure.microsoft.com/ko-kr/documentation/services/storage/
  [MSDN]: http://msdn.microsoft.com/ko-kr/library/gg433040.aspx
  [Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
