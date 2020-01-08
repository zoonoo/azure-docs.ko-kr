---
title: Azure 파일 공유 만들기
titleSuffix: Azure Files
description: Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 Azure 파일 공유를 만드는 방법입니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452885"
---
# <a name="create-a-file-share"></a>파일 공유 만들기
 [Azure Portal](https://portal.azure.com/), Azure Storage PowerShell cmdlet, Azure Storage 클라이언트 라이브러리 또는 Azure Storage REST API를 사용 하 여 Azure Files에서 공유를 만들 수 있습니다. 이 문서에서는 다음을 수행하는 방법을 알아봅니다.
- Azure Portal를 사용 하 여 Azure 파일 공유 만들기
- PowerShell을 사용 하 여 Azure 파일 공유 만들기
- Azure CLI를 사용 하 여 Azure 파일 공유 만들기

## <a name="prerequisites"></a>필수 조건
Azure 파일 공유를 만들려면 이미 존재 하는 저장소 계정을 사용 하거나 [새 azure storage 계정을 만들](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)수 있습니다. PowerShell을 사용 하 여 Azure 파일 공유를 만들려면 저장소 계정의 계정 키와 이름이 필요 합니다. PowerShell 또는 CLI를 사용 하려는 경우 저장소 계정 키가 필요 합니다.

> [!NOTE]
> 5 TiB 보다 큰 파일 공유를 만들려면 [큰 파일 공유 사용](storage-files-how-to-create-large-file-share.md)을 참조 하세요.

## <a name="create-a-file-share-through-the-azure-portal"></a>Azure Portal을 통해 파일 공유 만들기

1. Azure Portal의 **저장소 계정** 창으로 이동 합니다.
    저장소 계정 창 ![](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. **+ 파일 공유** 단추를 선택 합니다.
    파일 공유 추가 단추를 ![](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. **이름** 및 **할당량**에 대 한 정보를 입력 합니다.
    새 파일 공유의 이름과 할당량을 ![](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. 새 파일 공유를 봅니다.
    UI에서 새 파일 공유를 ![](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. 파일을 업로드합니다.
    탐색 모음에서 업로드 단추를 ![](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. 파일 공유로 이동 하 여 디렉터리와 파일을 관리 합니다.
    파일 공유의 폴더 보기를 ![](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>PowerShell을 통해 파일 공유 만들기

Azure PowerShell cmdlet을 다운로드 하 여 설치 합니다. 설치 포인트와 설치 지침에 대해서는  [Azure PowerShell 설치 및 구성 방법](https://docs.microsoft.com/powershell/azure/overview) 을 참조하세요.

> [!Note]  
> 최신 Azure PowerShell 모듈을 다운로드 하 고 설치 하거나 업그레이드 하는 것이 좋습니다.

1. 새 스토리지 계정을 만듭니다.
    저장소 계정은 blob 또는 큐와 같은 Azure 파일 공유 및 저장소 리소스를 배포할 수 있는 저장소의 공유 풀입니다.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. 새 파일 공유를 만듭니다.        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> 파일 공유의 이름은 모두 소문자여야 합니다. 파일 공유 및 파일 이름 지정에 대 한 자세한 내용은 [공유, 디렉터리, 파일 및 메타 데이터 이름 지정 및](https://msdn.microsoft.com/library/azure/dn167011.aspx)참조를 참조 하세요.

## <a name="create-a-file-share-through-the-cli"></a>CLI를 통해 파일 공유 만들기

1. Azure CLI를 다운로드 하 여 설치 합니다.
     [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli) 및 [Azure CLI 시작](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)을 참조 하세요.

1. 공유를 만들 저장소 계정에 대 한 연결 문자열을 만듭니다.
    다음 예제에서는 ```<storage-account>``` 및 ```<resource_group>```을 사용자의 스토리지 계정 이름 및 리소스 그룹으로 바꿉니다.

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. 파일 공유를 만듭니다.
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>다음 단계

* [Windows에서 파일 공유 연결 및 탑재](storage-how-to-use-files-windows.md)
* [Linux에서 파일 공유 연결 및 탑재](storage-how-to-use-files-linux.md)
* [MacOS에서 파일 공유 연결 및 탑재](storage-how-to-use-files-mac.md)

Azure Files에 대한 자세한 내용은 다음 링크를 참조하세요.

* [저장소 파일 FAQ](storage-files-faq.md)
* [Windows에서 Azure Files 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux에서 Azure Files 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
