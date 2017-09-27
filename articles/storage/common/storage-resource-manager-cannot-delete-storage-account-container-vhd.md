---
title: "Azure Storage 계정, 컨테이너 또는 VHD를 삭제하는 경우 발생하는 오류 문제 해결 | Microsoft Docs"
description: "Azure Storage 계정, 컨테이너 또는 VHD를 삭제하는 경우 발생하는 오류 문제 해결"
services: storage
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 11944dd38b1cc30106c0b76a108480c018ca39d4
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds"></a>Azure Storage 계정, 컨테이너 또는 VHD를 삭제하는 경우 발생하는 오류 문제 해결

[Azure 포털](https://portal.azure.com)에서 Azure 저장소 계정, 컨테이너 또는 VHD(가상 하드 디스크)를 삭제하려고 하면 오류가 발생할 수 있습니다. 이 문서에서는 Azure Resource Manager 배포의 문제 해결에 유용한 문제 해결 지침을 제공합니다.

이 문서에 Azure 문제와 관련된 정보가 없는 경우 [MSDN 및 Stack Overflow](https://azure.microsoft.com/support/forums/)에서 Azure 포럼을 방문합니다. 이러한 포럼이나 Twitter의 @AzureSupport에 문제를 게시할 수 있습니다. 또한 **Azure 지원** 사이트에서 [지원 받기](https://azure.microsoft.com/support/options/) 를 선택하여 Azure 지원 요청을 제출할 수 있습니다.

## <a name="symptoms"></a>증상
### <a name="scenario-1"></a>시나리오 1
Resource Manager 배포의 저장소 계정에서 VHD를 삭제하려고 할 때 다음 오류 메시지가 나타납니다.

**Blob 'vhds/BlobName.vhd'를 삭제하지 못했습니다. 오류: 현재 Blob에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다.**

이 문제는 가상 컴퓨터(VM)에 삭제하려는 VHD에 대한 임대가 있으므로 발생할 수 있습니다.

### <a name="scenario-2"></a>시나리오 2
Resource Manager 배포의 저장소 계정에서 컨테이너를 삭제하려고 할 때 다음 오류 메시지가 나타납니다.

**저장소 컨테이너 'vhds'를 삭제하지 못했습니다. 오류: 현재 컨테이너에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다**.

이 문제는 컨테이너에 임대 상태에서 잠겨 있는 VHD가 있기 때문에 발생할 수 있습니다.

### <a name="scenario-3"></a>시나리오 3
Resource Manager 배포의 저장소 계정을 삭제하려고 할 때 다음 오류 메시지가 나타납니다.

**저장소 계정 'StorageAccountName'을 삭제하지 못했습니다. 오류: 저장소 계정의 아티팩트가 사용 중이므로 저장소 계정을 삭제할 수 없습니다.**

이 문제는 저장소 계정에 임대 상태에 있는 VHD가 포함되어 있기 때문에 발생할 수 있습니다.

## <a name="solution"></a>해결 방법 
이러한 문제를 해결하려면 오류를 일으키는 VHD와 연결된 VM을 식별해야 합니다. 그런 다음 VM(데이터 디스크에 대한)에서 VHD를 분리하거나 VHD(OS 디스크에 대한)를 사용 중인 VM을 삭제합니다. 그러면 VHD에서 임대가 제거되어 삭제할 수 있습니다. 

이 작업을 수행하려면 다음 방법 중 하나를 사용합니다.

### <a name="method-1---use-azure-storage-explorer"></a>방법 1 - Azure Storage 탐색기 사용

### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>1단계 저장소 계정 삭제를 방지하는 VHD 식별

1. 저장소 계정을 삭제하는 경우 다음과 같은 메시지 대화 상자를 수신합니다. 

    ![저장소 계정을 삭제하는 경우의 메시지](././media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. **디스크 URL**을 확인하여 저장소 계정과 저장소 계정 삭제를 방지하는 VHD를 식별합니다. 다음 예제에서는 ".blob.core.windows.net" 앞의 문자열이 저장소 계정 이름이고, "SCCM2012-2015-08-28.vhd"가 VHD 이름입니다.  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

### <a name="step-2-delete-the-vhd-by-using-azure-storage-explorer"></a>2단계 Azure Storage 탐색기를 사용하여 VHD 삭제

1. [Azure Storage 탐색기](http://storageexplorer.com/)의 최신 버전을 다운로드하여 설치합니다. 이 도구는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 쉽게 작업할 수 있도록 해주는 Microsoft의 독립 실행형 앱입니다.
2. Azure Storage 탐색기를 열고 왼쪽 막대에서 ![계정 아이콘](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/account.png) 및 Azure 환경을 차례로 선택한 다음 로그인합니다.

3. 모든 구독을 선택하거나 삭제하려는 저장소 계정이 포함된 구독을 선택합니다.

    ![구독 추가](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/addsub.png)

4. 앞의 디스크 URL에서 얻은 저장소 계정으로 이동하여 **BLOB 컨테이너** > **vhd**를 선택하고 저장소 계정 삭제를 방지하는 VHD를 검색합니다.
5. VHD가 있으면 **VM 이름** 열에서 이 VHD를 사용하는 VM을 찾습니다.

    ![VM 확인](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/check-vm.png)

6. Azure Portal을 사용하여 VHD에서 임대를 제거합니다. 자세한 내용은 [VHD에서 임대 제거](#remove-the-lease-from-the-vhd)를 참조하세요. 

7. Azure Storage 탐색기로 이동하여 VHD를 마우스 오른쪽 단추로 클릭하고 삭제를 선택합니다.

8. 저장소 계정을 삭제합니다.

### <a name="method-2---use-azure-portal"></a>방법 2 - Azure Portal 사용 

#### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>1단계: 저장소 계정 삭제를 방지하는 VHD 식별

1. 저장소 계정을 삭제하는 경우 다음과 같은 메시지 대화 상자를 수신합니다. 

    ![저장소 계정을 삭제하는 경우의 메시지](././media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. **디스크 URL**을 확인하여 저장소 계정과 저장소 계정 삭제를 방지하는 VHD를 식별합니다. 다음 예제에서는 ".blob.core.windows.net" 앞의 문자열이 저장소 계정 이름이고, "SCCM2012-2015-08-28.vhd"가 VHD 이름입니다.  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

2. [Azure 포털](https://portal.azure.com)에 로그인합니다.
3. 허브 메뉴에서 **모든 리소스**를 선택합니다. 저장소 계정으로 이동한 다음 **Blobs** > **vhds**를 선택합니다.

    ![저장소 계정 및 "vhds" 컨테이너가 강조 표시된 포털의 스크린샷](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

4. 앞의 디스크 URL에서 가져온 VHD를 찾습니다. 그런 다음 VHD를 사용 중인 VM을 결정합니다. 일반적으로 VHD의 이름을 확인하여 VHD를 유지하는 VM를 결정할 수 있습니다.

Resource Manager 개발 모델의 VM

   * OS 디스크는 일반적으로 VMName-YYYY-MM-DD-HHMMSS.vhd 명명 규칙을 따릅니다.
   * 데이터 디스크는 일반적으로 VMName-YYYY-MM-DD-HHMMSS.vhd 명명 규칙을 따릅니다.

Classic 개발 모델의 VM

   * OS 디스크는 일반적으로 CloudServiceName-VMName-YYYY-MM-DD-HHMMSS.vhd 명명 규칙을 따릅니다.
   * 데이터 디스크는 일반적으로 CloudServiceName-VMName-YYYY-MM-DD-HHMMSS.vhd 명명 규칙을 따릅니다.

#### <a name="step-2-remove-the-lease-from-the-vhd"></a>2단계: VHD에서 임대 제거

[VHD에서 임대를 제거](#remove-the-lease-from-the-vhd)하고 저장소 계정을 삭제합니다.

## <a name="what-is-a-lease"></a>임대란?
임대는 Blob(예를 들어, VHD)에 대한 액세스를 제어하는 데 사용할 수 있는 잠금입니다. Blob가 임대되면 임대 소유자만 Blob에 액세스할 수 있습니다. 다음과 같은 이유로 임대가 중요합니다.

* 여러 소유자가 Blob의 같은 부분에 동시에 쓰려고 하는 경우 데이터 손상이 방지됩니다.
* 실제로 어떤 항목이 Blob를 사용 중인 경우(예를 들어, VM) Blob가 삭제되지 않도록 합니다.
* 실제로 어떤 항목이 저장소 계정을 사용 중인 경우(예를 들어, VM) 저장소 계정이 삭제되지 않도록 합니다.

### <a name="remove-the-lease-from-the-vhd"></a>VHD에서 임대 제거
VHD가 OS 디스크인 경우 VM을 삭제하여 임대를 제거해야 합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **허브** 메뉴에서 **Virtual Machines**를 선택합니다.
3. VHD에서 임대를 유지하는 VM을 선택합니다.
4. 실제로 가상 컴퓨터를 사용 중인 항목이 없고 가상 컴퓨터가 더 이상 필요하지 않음을 확인합니다.
5. **VM 세부 정보** 블레이드 맨 위에서 **삭제**를 선택한 후 **예**를 클릭하여 확인합니다.
6. VM은 삭제되지만 VHD는 유지될 수 있습니다. 그러나 VHD는 더 이상 임대를 포함하지 않아야 합니다. 임대를 해제하는 데는 몇 분 정도 걸릴 수 있습니다. 임대가 해제되었는지 확인하려면 **모든 리소스** > **저장소 계정 이름** > **Blobs** > **vhds**로 이동합니다. **Blob 속성** 창에서 **임대 상태** 값이 **잠금 해제됨**이 됩니다.

VHD가 데이터 디스크인 경우 VM에서 VHD를 분리하여 임대를 제거합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **허브** 메뉴에서 **Virtual Machines**를 선택합니다.
3. VHD에서 임대를 유지하는 VM을 선택합니다.
4. **VM 세부 정보** 블레이드에서 **디스크**를 선택합니다.
5. VHD에서 임대를 유지하는 데이터 디스크를 선택합니다. VHD의 URL을 확인하여 디스크에 연결된 VHD를 확인할 수 있습니다.
6. 확실히 데이터 디스크를 사용 중인 항목이 없는 것을 확인합니다.
7. **디스크 세부 정보** 블레이드에서 **분리**를 클릭합니다.
8. 이제 VM에서 디스크가 분리되며 VHD는 더 이상 임대를 포함하지 않습니다. 임대를 해제하는 데는 몇 분 정도 걸릴 수 있습니다. 임대가 해제되었는지 확인하려면 **모든 리소스** > **저장소 계정 이름** > **Blobs** > **vhds**로 이동합니다. **Blob 속성** 창에서 **임대 상태** 값이 **잠금 해제됨**이 됩니다.

## <a name="next-steps"></a>다음 단계
* [저장소 계정 삭제](storage-create-storage-account.md#delete-a-storage-account)
* [Microsoft Azure(PowerShell)에서 Blob 저장소의 임대 잠금을 해제하는 방법](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

