---
title: Azure Blob Storage 및 Azure Data Lake Storage에서 데이터 공유 및 수신
description: Azure Blob Storage 및 Azure Data Lake Storage에서 데이터를 공유 하 고 수신 하는 방법에 대해 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/23/2021
ms.openlocfilehash: c94d6f7d4d06d7dbaa6a2ad54f94575a6cc85d33
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644655"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Azure Blob Storage 및 Azure Data Lake Storage에서 데이터 공유 및 수신

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure 데이터 공유는 저장소 계정에서의 스냅숏 기반 공유를 지원 합니다. 이 문서에서는 Azure Blob Storage, Azure Data Lake Storage Gen1 및 Azure Data Lake Storage Gen2에서 데이터를 공유 하 고 받는 방법을 설명 합니다.

Azure 데이터 공유는 Azure Data Lake Gen1 및 Azure Data Lake Gen2에서 파일, 폴더 및 파일 시스템을 공유 하는 것을 지원 합니다. 또한 Azure Blob Storage에서 blob, 폴더 및 컨테이너를 공유할 수 있도록 지원 합니다. 블록 blob만 현재 지원 됩니다. 이러한 원본에서 공유 하는 데이터 Azure Data Lake Gen2 또는 Azure Blob Storage에서 받을 수 있습니다.

스냅숏 기반 공유에서 파일 시스템, 컨테이너 또는 폴더가 공유 되는 경우 데이터 소비자는 공유 데이터의 전체 복사본을 만들도록 선택할 수 있습니다. 또는 증분 스냅숏 기능을 사용 하 여 새 파일이 나 업데이트 된 파일만 복사할 수 있습니다. 증분 스냅숏 기능은 파일의 마지막 수정 시간을 기반으로 합니다. 

스냅숏을 생성 하는 동안 동일한 이름의 기존 파일을 덮어씁니다. 원본에서 삭제 된 파일은 대상에서 삭제 되지 않습니다. 원본에서 비어 있는 하위 폴더가 대상에 복사 되지 않습니다. 

## <a name="share-data"></a>데이터 공유

Azure 데이터 공유를 사용 하 여 데이터를 공유 하려면 다음 섹션의 정보를 사용 하세요. 
### <a name="prerequisites-to-share-data"></a>데이터 공유를 위한 필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 수신자의 Azure 로그인 메일 주소를 찾습니다. 받는 사람의 전자 메일 별칭은 사용자의 용도로 작동 하지 않습니다.
* 원본 Azure 데이터 저장소가 데이터 공유 리소스를 만들 Azure 구독에 있는 것과 다른 Azure 구독에 있는 경우 Azure 데이터 저장소가 있는 구독에 [DataShare 리소스 공급자](concepts-roles-permissions.md#resource-provider-registration) 를 등록 합니다. 

### <a name="prerequisites-for-the-source-storage-account"></a>원본 저장소 계정에 대 한 필수 구성 요소

* Azure Storage 계정. 계정이 아직 없는 경우 [새로 만듭니다](../storage/common/storage-account-create.md).
* 저장소 계정에 쓸 수 있는 권한입니다. 쓰기 권한은 *Microsoft. Storage/storageAccounts/write* 입니다. 참가자 역할의 일부입니다.
* 저장소 계정에 역할 할당을 추가할 수 있는 권한입니다. 이 권한은 *Microsoft. 권한 부여/역할 할당/쓰기* 에 있습니다. 소유자 역할의 일부입니다. 

### <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

### <a name="create-a-data-share-account"></a>데이터 공유 계정 만들기

Azure 리소스 그룹에서 Azure Data Share 리소스를 만듭니다.

1. 포털의 왼쪽 위 모서리에서 메뉴를 열고 **리소스 만들기** (+)를 선택 합니다.

1. *Data Share* 를 검색합니다.

1. **데이터 공유** 및 **만들기** 를 선택 합니다.

1. Azure 데이터 공유 리소스의 기본 세부 정보를 제공 합니다. 

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | Subscription | 사용자의 구독 | 데이터 공유 계정에 대 한 Azure 구독을 선택 합니다.|
    | 리소스 그룹 | *test-resource-group* | 기존 리소스 그룹을 사용 하거나 리소스 그룹을 만듭니다. |
    | 위치 | *미국 동부 2* | 데이터 공유 계정에 대한 지역을 선택합니다.
    | 이름 | *datashareaccount* | 데이터 공유 계정의 이름을로 합니다. |
    | | |

1. **검토 +** 만들기 만들기  >   를 선택 하 여 데이터 공유 계정을 프로 비전 합니다. 새 데이터 공유 계정을 프로 비전 하는 작업은 일반적으로 약 2 분이 걸립니다. 

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

### <a name="create-a-share"></a>공유 만들기

1. 데이터 공유 **개요** 페이지로 이동 합니다.

   :::image type="content" source="./media/share-receive-data.png" alt-text="데이터 공유 개요를 보여 주는 스크린샷":::

1. **Start sharing your data**(데이터 공유 시작)를 선택합니다.

1. **만들기** 를 선택합니다.   

1. 공유에 대 한 세부 정보를 제공 합니다. 이름, 공유 유형, 공유 콘텐츠의 설명 및 사용 약관(선택 사항)을 지정합니다. 

    ![데이터 공유 세부 정보를 보여 주는 스크린샷](./media/enter-share-details.png "데이터 공유 세부 정보를 입력 합니다.") 

1. **계속** 을 선택합니다.

1. 공유에 데이터 집합을 추가 하려면 **데이터 집합 추가** 를 선택 합니다. 

    ![공유에 데이터 집합을 추가 하는 방법을 보여 주는 스크린샷](./media/datasets.png "데이터 세트.")

1. 추가할 데이터 집합 형식을 선택 합니다. 데이터 집합 형식 목록은 이전 단계에서 스냅숏 기반 공유 또는 내부 공유를 선택 했는지에 따라 달라 집니다. 

    ![데이터 집합 형식을 선택할 수 있는 위치를 보여 주는 스크린샷](./media/add-datasets.png "데이터 집합을 추가 합니다.")    

1. 공유 하려는 개체로 이동 합니다. 그런 다음 **데이터 집합 추가** 를 선택 합니다. 

    ![공유할 개체를 선택 하는 방법을 보여 주는 스크린샷](./media/select-datasets.png "데이터 집합을 선택 합니다.")    

1. **받는 사람 탭에서** **받는 사람 추가** 를 선택 하 여 데이터 소비자의 전자 메일 주소를 추가 합니다. 

    ![받는 사람 전자 메일 주소를 추가 하는 방법을 보여 주는 스크린샷](./media/add-recipient.png "받는 사람을 추가 합니다.") 

1. **계속** 을 선택합니다.

1. 스냅숏 공유 유형을 선택한 경우 데이터 소비자에 대 한 데이터를 업데이트 하도록 스냅숏 일정을 설정할 수 있습니다. 

    ![스냅숏 일정 설정을 보여 주는 스크린샷](./media/enable-snapshots.png "스냅숏을 사용 하도록 설정 합니다.") 

1. 시작 시간과 되풀이 간격을 선택합니다. 

1. **계속** 을 선택합니다.

1. **검토 + 만들기** 탭에서 패키지 콘텐츠, 설정, 받는 사람 및 동기화 설정을 검토 합니다. 그런 다음 **만들기** 를 선택합니다.

이제 Azure 데이터 공유를 만들었습니다. 데이터 공유를 받는 사람은 초대를 수락할 수 있습니다. 

## <a name="receive-data"></a>데이터 수신

다음 섹션에서는 공유 데이터를 수신 하는 방법을 설명 합니다.
### <a name="prerequisites-to-receive-data"></a>데이터를 받기 위한 필수 구성 요소
데이터 공유 초대를 수락 하기 전에 다음과 같은 필수 구성 요소가 있는지 확인 합니다. 

* Azure 구독 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure의 초대. 전자 메일 제목은 "Azure 데이터 공유 초대" 여야 합니다 *\<yourdataprovider\@domain.com>* .
* 에서 등록 된 [DataShare 리소스 공급자](concepts-roles-permissions.md#resource-provider-registration) 는 다음과 같습니다.
    * 데이터 공유 리소스를 만들 Azure 구독.
    * 대상 Azure 데이터 저장소가 있는 Azure 구독.

### <a name="prerequisites-for-a-target-storage-account"></a>대상 저장소 계정에 대 한 필수 구성 요소

* Azure Storage 계정. 계정이 아직 없는 경우 [계정을 만듭니다](../storage/common/storage-account-create.md). 
* 저장소 계정에 쓸 수 있는 권한입니다. 이 사용 권한은 *Microsoft Storage/storageAccounts/write* 에 있습니다. 참가자 역할의 일부입니다. 
* 저장소 계정에 역할 할당을 추가할 수 있는 권한입니다. 이 할당은 *Microsoft. 권한 부여/역할 할당/쓰기* 에 있습니다. 소유자 역할의 일부입니다.  

### <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

### <a name="open-an-invitation"></a>초대 열기

전자 메일에서 또는 Azure Portal에서 직접 초대를 열 수 있습니다.

1. 전자 메일에서 초대를 열려면 받은 편지함에서 데이터 공급자의 초대를 확인 합니다. Microsoft Azure의 초대는 "Azure 데이터 공유 초대"의 제목입니다 *\<yourdataprovider\@domain.com>* . **초대 보기** 를 선택 하 여 Azure에서 초대를 확인 합니다. 

   Azure Portal에서 초대를 열려면 *데이터 공유 초대* 를 검색 합니다. 데이터 공유 초대 목록이 표시 됩니다.

   ![Azure Portal 초대 목록을 보여 주는 스크린샷](./media/invitations.png "초대 목록입니다.") 

1. 보려는 공유를 선택 합니다. 

### <a name="accept-an-invitation"></a>초대 수락
1. **사용 약관** 를 포함 하 여 모든 필드를 검토 합니다. 조건에 동의 하면 확인란을 선택 합니다. 

   ![사용 약관 영역을 보여 주는 스크린샷](./media/terms-of-use.png "사용 약관.") 

1. **대상 데이터 공유 계정** 에서 데이터 공유를 배포할 구독 및 리소스 그룹을 선택 합니다. 그 후, 다음 필드를 입력합니다.

   * 데이터 공유 **계정 필드에서** 데이터 공유 계정이 없는 경우 **새로 만들기** 를 선택 합니다. 그렇지 않은 경우에는 데이터 공유를 수락할 기존 데이터 공유 계정을 선택 합니다. 

   * **받은 공유 이름** 필드에서 데이터 공급자가 지정한 기본값을 그대로 두거나 수신 된 공유에 대 한 새 이름을 지정 합니다. 

1. **수락 및 구성** 을 선택 합니다. 공유 구독이 생성 됩니다. 

   ![구성 옵션을 허용할 위치를 보여 주는 스크린샷](./media/accept-options.png "수락 옵션") 

    받은 공유가 데이터 공유 계정에 표시 됩니다. 

    초대를 수락 하지 않으려면 **거부** 를 선택 합니다. 

### <a name="configure-a-received-share"></a>받은 공유 구성
데이터를 받을 위치를 구성 하려면이 섹션의 단계를 따르세요.

1. **데이터 집합** 탭에서 대상을 할당 하려는 데이터 집합 옆의 확인란을 선택 합니다. 대상 **에 매핑** 을 선택 하 여 대상 데이터 저장소를 선택 합니다. 

   ![대상에 매핑하는 방법을 보여 주는 스크린샷](./media/dataset-map-target.png "대상에 매핑합니다.") 

1. 데이터에 대 한 대상 데이터 저장소를 선택 합니다. 대상 데이터 저장소에서 수신 된 데이터의 파일과 경로 및 이름이 동일한 파일을 덮어씁니다. 

   ![대상 저장소 계정을 선택할 수 있는 위치를 보여 주는 스크린샷](./media/map-target.png "대상 저장소.") 

1. 스냅숏 기반 공유의 경우 데이터 공급자가 스냅숏 일정을 사용 하 여 정기적으로 데이터를 업데이트 하는 경우 **스냅숏 일정** 탭에서 일정을 사용 하도록 설정할 수 있습니다. 스냅숏 일정 옆의 상자를 선택 합니다. 그런 다음 **사용** 을 선택합니다. 첫 번째 예약 된 스냅숏은 일정 시간 동안 1 분 이내에 시작 되 고 후속 스냅숏은 예약 된 시간 (초) 이내에 시작 됩니다.

   ![스냅숏 일정을 사용 하도록 설정 하는 방법을 보여 주는 스크린샷](./media/enable-snapshot-schedule.png "스냅숏 일정을 사용 하도록 설정 합니다.")

### <a name="trigger-a-snapshot"></a>스냅샷 트리거
이 섹션의 단계는 스냅숏 기반 공유에만 적용 됩니다.

1. **세부 정보** 탭에서 스냅숏을 트리거할 수 있습니다. 탭에서 **트리거 스냅숏** 을 선택 합니다. 데이터의 전체 스냅숏 또는 증분 스냅숏을 트리거하기를 선택할 수 있습니다. 데이터 공급자에서 처음으로 데이터를 수신 하는 경우 **전체 복사** 를 선택 합니다. 스냅숏이 실행 중일 때 후속 스냅숏은 이전 스냅숏이 완료 될 때까지 시작 되지 않습니다.

   ![트리거 스냅숏 선택 항목을 보여 주는 스크린샷](./media/trigger-snapshot.png "스냅숏 트리거") 

1. 마지막 실행 상태가 *성공한* 경우 대상 데이터 저장소로 이동 하 여 받은 데이터를 확인 합니다. **데이터 집합** 을 선택 하 고 대상 경로 링크를 선택 합니다. 

   ![소비자 데이터 집합 매핑을 보여 주는 스크린샷](./media/consumer-datasets.png "소비자 데이터 집합 매핑.") 

### <a name="view-history"></a>기록 보기
스냅숏 기반 공유 에서만 스냅숏의 기록을 볼 수 있습니다. 기록을 보려면 **기록** 탭을 엽니다. 여기에서 지난 30 일 동안 생성 된 모든 스냅숏의 기록을 볼 수 있습니다. 

## <a name="storage-snapshot-performance"></a>저장소 스냅숏 성능
저장소 스냅숏 성능은 공유 데이터의 파일 수와 크기에 대 한 다양 한 요인에 의해 영향을 받습니다. 항상 고유한 성능 테스트를 수행 하는 것이 좋습니다. 다음은 성능에 영향을 주는 몇 가지 예제 요소입니다.

* 원본 및 대상 데이터 저장소에 대 한 동시 액세스.  
* 원본 및 대상 데이터 저장소의 위치입니다. 
* 증분 스냅숏의 경우 공유 데이터 집합의 파일 수는 마지막으로 성공한 스냅숏 이후 마지막으로 수정 된 시간을 사용 하 여 파일 목록을 찾는 데 걸리는 시간에 영향을 줄 수 있습니다. 


## <a name="next-steps"></a>다음 단계
Azure 데이터 공유 서비스를 사용 하 여 저장소 계정에서 데이터를 공유 하 고 수신 하는 방법을 배웠습니다. 다른 데이터 원본에서 공유 하는 방법에 대 한 자세한 내용은 [지원 되는 데이터 저장소](supported-data-stores.md)를 참조 하세요.