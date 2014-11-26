<properties linkid="manage-services-how-to-manage-a-storage-account" urlDisplayName="How to manage" pageTitle="How to manage storage accounts | Microsoft Azure" metaKeywords="Azure manage storage accounts, storage account management portal, storage account geo-replication, Azure geo-replication, Azure access keys" description="Learn how to manage storage accounts in Azure by using the Management Portal." metaCanonical="" services="storage" documentationCenter="" title="How To Manage Storage Accounts" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# <span id="managestorageaccounts"></span></a>저장소 계정을 관리하는 방법

## 목차

-   [방법: 저장소 계정 복제 관리][방법: 저장소 계정 복제 관리]
-   [방법: 저장소 액세스 키 보기, 복사 및 다시 생성][방법: 저장소 액세스 키 보기, 복사 및 다시 생성]
-   [방법: 저장소 계정 삭제][방법: 저장소 계정 삭제]

## <span id="georeplication"></span></a>방법: 내구성과 고가용성을 위한 저장소 계정 데이터 복제

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### 저장소 계정에 대한 복제 설정을 지정하려면

1.  [Azure 관리 포털][Azure 관리 포털]에서 **저장소**를 클릭하고 대시보드에 표시할 저장소 계정 이름을 클릭합니다.

2.  **구성**을 클릭합니다.

3.  **복제** 필드에서 저장소 계정에 사용할 복제 유형을 선택합니다.

4.  **저장**을 클릭하고 메시지가 나타나면 선택 내용을 확인합니다.

## <span id="regeneratestoragekeys"></span></a>방법: 저장소 액세스 키 보기, 복사 및 다시 생성

저장소 계정을 만들면 Azure에서 두 개의 512비트 저장소 액세스 키를 생성합니다. 이 키는 저장소 계정에 액세스하는 경우 인증에 사용됩니다. Azure에서는 두 개의 저장소 액세스 키를 제공하므로 저장소 서비스나 해당 서비스에 대한 액세스 중단 없이 키를 다시 생성할 수 있습니다.

[관리 포털][관리 포털]에서 대시보드 또는 **저장소** 페이지의 **키 관리**를 사용하여 Blob, 테이블 및 큐 서비스에 액세스하는 데 사용되는 저장소 액세스 키를 보고, 복사하고, 다시 생성합니다.

### 저장소 액세스 키 복사

**키 관리**를 사용하여 연결 문자열에 사용할 저장소 액세스 키를 복사할 수 있습니다. 연결 문자열에는 인증에 사용할 저장소 계정 이름과 키가 있어야 합니다. Azure 저장소 서비스에 액세스하기 위한 연결 문자열 구성에 대한 자세한 내용은 [연결 문자열 구성][연결 문자열 구성]을 참조하세요.

1.  [관리 포털][관리 포털]에서 **저장소**를 클릭한 후 대시보드를 열 저장소 계정 이름을 클릭합니다.

2.  **키 관리**를 클릭합니다.

    **액세스 키 관리**가 열립니다.

    ![키 관리][키 관리]

3.  저장소 액세스 키를 복사하려면 키 텍스트를 선택합니다. 그런 다음 마우스 오른쪽 단추를 클릭하고 **복사**를 클릭합니다.

### 저장소 액세스 키 다시 생성

저장소 연결을 더욱 안전하게 유지하는 데 도움이 되도록 정기적으로 저장소 계정의 액세스 키를 변경해야 합니다. 두 개의 액세스 키가 할당되므로 하나의 액세스 키를 다시 생성하는 동안 다른 액세스 키를 사용하여 저장소 계정에 대한 연결을 유지할 수 있습니다.

<div class="dev-callout"> 
    <b>경고</b> 
    <p>액세스 키를 다시 생성하면 가상 컴퓨터, 미디어 서비스 및 저장소 계정에 종속된 모든 응용 프로그램에 영향을 줍니다. 액세스 키를 사용하여 저장소 계정에 액세스하는 모든 클라이언트에서 새 키를 사용하도록 업데이트해야 합니다.
    </p> 
</div>

**가상 컴퓨터** - 실행 중인 가상 컴퓨터가 저장소 계정에 포함된 경우 액세스 키를 다시 생성한 후 모든 가상 컴퓨터를 다시 배포해야 합니다. 다시 배포하지 않으려면 액세스 키를 다시 생성하기 전에 가상 컴퓨터를 종료합니다.

**미디어 서비스** - 저장소 계정에 종속된 미디어 서비스가 있는 경우 키를 다시 생성한 후 미디어 서비스와 액세스 키를 다시 동기화해야 합니다.

**응용 프로그램** - 저장소 계정을 사용하는 웹 응용 프로그램이나 클라우드 서비스가 있는 경우 키를 롤링하지 않고 키를 다시 생성하면 연결이 끊어집니다. 프로세스는 다음과 같습니다.

1.  저장소 계정의 보조 액세스 키를 참조하도록 응용 프로그램 코드의 연결 문자열을 업데이트합니다.

2.  저장소 계정의 기본 액세스 키를 다시 생성합니다. [관리 포털][관리 포털]의 대시보드 또는 **구성** 페이지에서 **키 관리**를 클릭합니다. 기본 액세스 키 아래의 **다시 생성**을 클릭하고 **예**를 클릭하여 새 키 생성을 확인합니다.

3.  새 기본 액세스 키를 참조하도록 코드의 연결 문자열을 업데이트합니다.

4.  보조 액세스 키를 다시 생성합니다.

## <span id="deletestorageaccount"></span></a>방법: 저장소 계정 삭제

더 이상 사용하지 않는 저장소 계정을 제거하려면 대시보드 또는 **구성** 페이지에서 **삭제**를 사용합니다. **삭제**는 계정의 모든 Blob, 테이블, 큐를 포함한 전체 저장소 계정을 삭제합니다.

<div class="dev-callout">
    <b>경고</b>
    <p>삭제된 저장소 계정의 내용을 복원하는 방법은 없습니다. 계정을 
    삭제하기 전에 저장할 내용을 백업했는지 확인합니다.
    </p>
    <p>
    저장소 계정에 Azure 가상 컴퓨터의 VHD 파일이나 디스크가 포함된 
    경우에는 저장소 계정을 삭제하기 전에 해당 VHD 파일을 사용하는 이미지와 디스크를 
    모두 삭제해야 합니다. 먼저 가상 컴퓨터가 실행 중인 경우 중지한 다음 삭제합니다. 디스크를 삭제하려면 디스크 탭으로 이동하여 저장소 계정에 포함된 모든 디스크를 삭제합니다. 이미지를 삭제하려면 이미지 탭으로 이동하여 계정에 저장된 모든 이미지를 삭제합니다.
    </p>
</div>

1.  [관리 포털][관리 포털]에서 **저장소**를 클릭합니다.

2.  저장소 계정 항목에서 이름을 제외한 임의의 항목을 클릭하고 **삭제**를 클릭합니다.

    -또는-

    대시보드를 열 저장소 계정 이름을 클릭하고 **삭제**를 클릭합니다.

3.  **예**를 클릭하여 저장소 계정 삭제를 확인합니다.

  [방법: 저장소 계정 복제 관리]: #georeplication
  [방법: 저장소 액세스 키 보기, 복사 및 다시 생성]: #regeneratestoragekeys
  [방법: 저장소 계정 삭제]: #deletestorageaccount
  [Azure 관리 포털]: https://manage.windowsazure.com
  [관리 포털]: http://manage.windowsazure.com
  [연결 문자열 구성]: http://msdn.microsoft.com/ko-kr/library/ee758697.aspx
  [키 관리]: ./media/storage-manage-storage-account/Storage_ManageKeys.png
