---
title: Azure Stack 저장소 계정 관리 | Microsoft Docs
description: 찾기, 관리, 복구 및 Azure Stack 저장소 계정을 회수 하는 방법을 알아봅니다
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: af7500d6bf12ef34e6919aa255185a87525310b3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240718"
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Azure Stack의 storage 계정 관리
찾기, 복구 및 비즈니스 요구에 따라 저장소 용량을 회수 하는 Azure Stack에서 저장소 계정을 관리 하는 방법에 알아봅니다.

## <a name="find"></a>저장소 계정 찾기
지역에서 저장소 계정 목록에서 Azure Stack에서 볼 수 있습니다.

1. 에 로그인 합니다 [관리자 포털](https://adminportal.local.azurestack.external)합니다.

2. 선택 **모든 서비스** > **지역 관리** 아래에서 **관리**합니다.

3. 선택 **스토리지** 에서 합니다 **리소스 공급자** 목록입니다.
   
   ![저장소 리소스 공급자](media/azure-stack-manage-storage-accounts/image1.png)

5. 선택 **Storage 계정** 에 **저장소**합니다.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
   
   블레이드에서 해당 지역의 저장소 계정 목록을 표시합니다.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

기본적으로 처음 10 개의 계정이 표시 됩니다. 자세히 인출 하도록 선택할 수 있습니다를 클릭 하 여는 **로드** 목록의 맨 아래에 링크 합니다.

또는

수를 특정 저장소 계정에 관심이 있다면 **필터링 하 고 관련 계정 인출** 만 합니다.


**계정에 대 한 필터링 합니다.**

1. 선택 **필터** 창의 맨 위에 있는 합니다.
2. 필터 창에서 해당 지정할 수 있습니다 **계정 이름**, * * 구독 ID로, 또는 **상태** 표시할 저장소 계정 목록을 세밀 하 게 합니다. 적절 하 게 사용 합니다.
3. **업데이트**를 선택합니다. 목록에 따라 새로 고쳐야 합니다.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. 필터를 다시 설정 하려면: 선택 **필터**, 선택 항목을 지울 및 업데이트 합니다.

(저장소 계정 목록 창) 맨 위에 있는 검색 텍스트 상자에 계정 목록에서 선택한 텍스트를 강조 표시할 수 있습니다. 전체 이름 또는 ID를 쉽게 사용할 수 없을 때이 사용할 수 있습니다.

원하는 계정을 찾을 수 있도록 여기에 자유 텍스트를 사용할 수 있습니다.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>계정 정보를 보면
보기에 관심이 있다면 계정 찾았으면 특정 세부 정보를 보려면 특정 계정을 선택할 수 있습니다. 같은 계정 세부 정보를 사용 하 여 새 창이 열립니다: 계정, 만든 시간, 위치 등의 형식입니다.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>삭제 된 계정 복구
삭제 된 계정 복구 해야 하는 상황에서 수도 있습니다.

Azure Stack에서 작업을 수행 하는 간단한 방법이 있습니다.

1. 저장소 계정 목록으로 이동 합니다. 참조 [저장소 계정의 찾을](#find) 자세한 내용은이 문서의.
2. 목록에서 해당 특정 계정을 찾습니다. 필터링 할 수 있습니다.
3. 확인 합니다 *상태* 계정. 표시 됩니다 **Deleted**합니다.
4. 계정 세부 정보 창에 열리는 계정을 선택 합니다.
5. 이 창 위에 찾을 합니다 **복구** 단추를 선택 합니다.
6. **예**를 선택하여 확인합니다.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. 복구 되었습니다 *처리 대기...* 성공 했는지 확인 합니다.
   진행률 표시를 보려면 포털의 맨 위에 있는 "종 모양" 아이콘을 선택할 수도 있습니다.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   복구 된 계정을 성공적으로 동기화 되 면 다시 사용할 수 있습니다.

### <a name="some-gotchas"></a>몇 가지 알려진 문제
* 삭제 된 계정의 상태를 보여 줍니다 **보존에서**합니다.
  
  보존 의미에서 삭제 된 계정의 보존 기간을 초과 했습니다 하는 그렇지 않을 복구할 수 있습니다.
* 삭제 된 계정의 계정 목록에 표시 되지 않습니다.
  
  계정이 삭제 된 계정의 이미 가비지 수집 되었을 때 계정 목록에 표시 되지 않습니다. 이 경우 복구할 수 없습니다. 참조 [용량을 회수](#reclaim) 이 문서의.

## <a name="set-the-retention-period"></a>보존 기간 설정
보존 기간 설정을 삭제 한 계정의 모든 잠재적으로 하는 동안 복구할 수 (0부터 9999 한 일 후에 시간을 지정 하는 클라우드 연산자 수 있습니다. 기본 보존 기간은 0 일로 설정 됩니다. 값을 모든 삭제 된 계정에서 보존은 바로 고 주기적 가비지 수집에 대 한 표시는 "0"으로 설정 합니다.

**보존 기간을 변경 합니다.**

1. 에 로그인 합니다 [관리자 포털](https://adminportal.local.azurestack.external)합니다.
2. 선택 **모든 서비스** > **지역 관리** 아래에서 **관리**합니다.
3. 선택 **스토리지** 에서 합니다 **리소스 공급자** 목록입니다.
4. 선택 **설정을** 설정 창을 열려면 맨 위에 있는 합니다.
5. 선택 **구성** 다음 보존 기간 값을 편집 합니다.

   일 수를 설정 하 고 저장 합니다.
   
   이 값이 즉시 유효 하 고 전체 지역에 대 한 설정 됩니다.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>용량을 회수
보존 기간의 부작용 중 하나는 삭제 된 계정 계속 보존 기간에 나올 때까지 용량을 소비 하는입니다. 클라우드 운영자로 서 보존 기간 아직 만료 되지 않은 경우에 삭제 한 계정의 공간을 회수 하는 방법을 사용 해야 합니다.

포털 또는 PowerShell을 사용 하 여 용량을 회수할 수 있습니다.

**포털을 사용 하 여 용량을 합니다.**
1. 저장소 계정 창으로 이동 합니다. 참조 [저장소 계정의 찾을](#find)합니다.
2. 선택 **공간을 회수** 창의 맨 위에 있는 합니다.
3. 메시지를 읽고 선택한 **확인**합니다.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. 성공 알림 벨 아이콘은 포털에서 참조 될 때까지 기다립니다.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. 저장소 계정 페이지를 새로 고칩니다. 삭제 된 계정 제거 되었기 때문에 더 이상 목록에 표시 됩니다.

또한 PowerShell을 사용 하 여 명시적으로 보존 기간을 재정의 하 고 용량을 즉시 회수 수 있습니다.

**용량을 회수 하기 위해 PowerShell을 사용 하 여:**   

1. Azure PowerShell 설치 및 구성 했는지 확인 합니다. 그렇지 않은 경우 다음 지침을 따르십시오. 
   * 최신 Azure PowerShell 버전을 설치 하 고 Azure 구독에 연결, 참조 [Azure PowerShell 설치 및 구성 하는 방법을](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)합니다.
   Azure Resource Manager cmdlet에 대 한 자세한 내용은 참조 하세요. [Azure PowerShell를 사용 하 여 Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkId=394767)
2. 다음 cmdlet을 실행합니다.

> [!NOTE]  
> 이러한 cmdlet을 실행 하는 계정 및 해당 콘텐츠가 영구적으로 삭제 합니다. 복구할 수는 없습니다. 이 사용 하 여 주의 해야 합니다.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
````

자세한 내용은 참조 하세요. [Azure Stack PowerShell 설명서.](https://docs.microsoft.com/powershell/module/azurerm.azurestackstorage)
 

## <a name="next-steps"></a>다음 단계

 - 권한 관리에 대 한 내용은 [Access Control](azure-stack-manage-permissions.md)합니다.
 - Azure Stack에 대 한 저장소 용량을 관리 하는 방법은 참조 하세요 [Azure Stack에 대 한 저장소 용량을 관리할](azure-stack-manage-storage-shares.md)합니다.