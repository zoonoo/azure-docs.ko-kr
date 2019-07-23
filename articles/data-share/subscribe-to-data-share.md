---
title: 자습서 - Azure Data Share 미리 보기를 사용하여 데이터 수락 및 받기
description: 자습서 - Azure Data Share 미리 보기를 사용하여 데이터 수락 및 받기
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: fc63c1a0b3b496de8e5ecea58f79f1db9d872e80
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838437"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>자습서: Azure Data Share 미리 보기를 사용하여 데이터 수락 및 받기

이 자습서에서는 Azure Data Share 미리 보기를 사용하여 데이터 공유 초대를 수락하는 방법에 대해 알아봅니다. 공유되는 데이터를 받는 방법과 정기적인 새로 고침 간격을 사용하도록 설정하여 공유되는 데이터의 최신 스냅샷을 항상 유지하는 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * Azure Data Share 미리 보기 초대를 수락하는 방법
> * Azure Data Share 미리 보기 계정 만들기
> * 데이터 대상 지정
> * 예약된 새로 고침을 위해 데이터 공유에 대한 구독 만들기

## <a name="prerequisites"></a>필수 조건
데이터 공유 초대를 수락하려면 먼저 아래에 나열된 여러 Azure 리소스를 프로비저닝해야 합니다. 

모든 필수 조건이 충족되었는지 확인한 후에 데이터 공유 초대를 수락합니다. 

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure Storage 계정: 아직 없는 경우 [Azure Storage 계정](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)을 만들 수 있습니다. 
* 데이터 공유 초대: " **<yourdataprovider@domain.com>** 이(가) 보낸 Azure Data Share 초대"라는 제목이 있는 Microsoft Azure의 초대입니다.
* 스토리지 계정에 역할 할당을 추가할 수 있는 권한. 이 권한은 *Microsoft.Authorization/role assignments/write* 권한에 있습니다. 이 권한은 소유자 역할에 있습니다. 
* Microsoft.DataShare에 대한 리소스 공급자 등록. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Azure 리소스 공급자](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) 설명서를 참조하세요. 

> [!IMPORTANT]
> Azure Data Share를 수락하고 받으려면 먼저 Microsoft.DataShare 리소스 공급자를 등록해야 하며 데이터를 수락하는 스토리지 계정의 소유자여야 합니다. [Azure Data Share 미리 보기의 문제 해결](data-share-troubleshoot.md)에서 설명하는 지침에 따라 데이터 공유 리소스 공급자를 등록하고 자신을 스토리지 계정의 소유자로 추가합니다. 

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="open-invitation"></a>초대 열기

받은 편지함에서 데이터 공급자의 초대를 확인합니다. 이 초대는 Microsoft Azure에서 **<yourdataprovider@domain.com>이(가) 보낸 Azure Data Share 초대**라는 제목으로 보낸 것입니다. 여러 개의 초대가 있는 경우 올바른 공유를 수락할 수 있도록 공유 이름을 적어둡니다. 

**초대 보기**를 선택하여 Azure에서 초대를 확인합니다. 그러면 [받은 공유] 보기로 이동합니다.

![초대](./media/invitations.png "초대 목록") 

확인하려는 공유를 선택합니다. 

## <a name="accept-invitation"></a>초대 수락
**사용 약관**을 포함하여 모든 필드를 검토해야 합니다. 사용 약관에 동의하는 경우 해당 확인란을 선택하여 동의함을 표시해야 합니다. 

![사용 약관](./media/terms-of-use.png "사용 약관") 

*대상 데이터 공유 계정*에서 Data Share를 배포할 대상의 구독 및 리소스 그룹을 선택합니다. 

기존 Data Share 계정이 없는 경우 **데이터 공유 계정** 필드에서 **새로 만들기**를 선택합니다. 그렇지 않으면 데이터 공유를 수락할 기존 Data Share 계정을 선택합니다. 

*받은 공유 이름* 필드에 대해 데이터 공급자에서 지정한 기본값을 그대로 두거나 받은 공유에 대한 새 이름을 지정할 수 있습니다. 

![대상 데이터 공유 계정](./media/target-data-share.png "대상 데이터 공유 계정") 

사용 약관에 동의하고 공유 위치를 지정했으면 *수락 및 구성*을 선택합니다. 이 옵션을 선택하면 공유 구독이 만들어지고 다음 화면에서 복사할 데이터에 대한 대상 스토리지 계정을 선택하라는 메시지가 표시됩니다. 

![수락 옵션](./media/accept-options.png "수락 옵션") 

지금 초대를 수락하지만 나중에 스토리지를 구성하려면 *수락 및 나중에 구성*을 선택합니다. 이 옵션을 사용하면 나중에 대상 스토리지 계정을 구성할 수 있습니다. 나중에 스토리지를 계속 구성하기 위해 데이터 공유 구성을 다시 시작하는 방법에 대한 자세한 단계는 [스토리지 계정을 구성하는 방법](how-to-configure-mapping.md) 페이지를 참조하세요. 

초대를 수락하지 않으려면 *거부*를 선택합니다. 

## <a name="configure-storage"></a>저장소 구성
*대상 스토리지 설정* 아래에서 데이터를 받도록 하려는 구독, 리소스 그룹 및 스토리지 계정을 선택합니다. 

![대상 스토리지 설정](./media/target-storage-settings.png "대상 스토리지") 

데이터를 정기적으로 새로 고치려면 스냅샷 설정을 사용하도록 설정해야 합니다. 데이터 공급자가 스냅샷 설정 일정을 데이터 공유에 포함시킨 경우에만 해당 일정이 표시됩니다. 

![스냅샷 설정](./media/snapshot-settings.png "스냅샷 설정") 

*저장*을 선택합니다. 

## <a name="trigger-a-snapshot"></a>스냅샷 트리거

**스냅샷 트리거**를 선택하여 받은 공유 - > 세부 정보 탭에서 스냅샷을 트리거할 수 있습니다. 여기서는 데이터의 전체 또는 증분 스냅샷을 트리거할 수 있습니다. 데이터를 데이터 공급자로부터 처음 받는 경우 전체 복사본을 선택합니다. 

![스냅샷 트리거](./media/trigger-snapshot.png "스냅샷 트리거") 

마지막 실행 상태가 *성공*인 경우 스토리지 계정을 열어 받은 데이터를 확인합니다. 

사용한 스토리지 계정을 확인하려면 **데이터 세트**를 선택합니다. 

![소비자 데이터 세트](./media/consumer-datasets.png "소비자 데이터 세트 매핑") 

## <a name="view-history"></a>기록 보기
스냅샷 기록을 확인하려면 받은 공유 -> 기록으로 이동합니다. 여기서는 지난 60일 동안 생성된 모든 스냅샷의 기록을 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Data Share를 수락하고 받는 방법을 알아보았습니다. Azure Data Share 개념에 대해 자세히 알아보려면 [개념: Azure Data Share 용어](terminology.md)로 계속 진행하세요.