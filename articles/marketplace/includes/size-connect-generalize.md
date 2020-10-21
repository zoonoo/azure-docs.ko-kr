---
title: 포함 파일
description: 파일
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 24adbfe38a3d43a83307fb8726849f7c73def3f3
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284301"
---
## <a name="generalize-the-image"></a>이미지 일반화

Azure Marketplace의 모든 이미지는 일반적으로 다시 사용할 수 있어야 합니다. 이를 달성하려면 운영 체제 VHD가 일반화되어야 합니다. 이 작업은 VM에서 인스턴스 관련 식별자와 소프트웨어 드라이버를 모두 제거합니다.

### <a name="for-windows"></a>Windows의 경우

Windows OS 디스크는 [sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) 도구를 사용 하 여 일반화 됩니다. 나중에 OS를 업데이트 하거나 다시 구성 하는 경우 sysprep을 다시 실행 해야 합니다.

> [!WARNING]
> Sysprep을 실행 한 후 업데이트가 자동으로 실행 될 수 있기 때문에 배포 될 때까지 VM을 해제 합니다. 이렇게 종료하면 이후 업데이트에서 운영 체제 또는 설치된 서비스에 대한 인스턴스 관련 변경이 수행되지 않습니다. sysprep 실행에 대한 자세한 내용은 [VHD를 일반화하는 단계](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)를 참조하세요.

### <a name="for-linux"></a>Linux의 경우

다음 프로세스에서 Linux VM을 일반화하여 별도의 VM으로 다시 배포합니다. 자세한 내용은 [가상 머신 또는 VHD의 이미지를 만드는 방법](../../virtual-machines/linux/capture-image.md)을 참조하세요. "캡처된 이미지에서 VM 만들기" 라는 섹션에 도달 하면를 중지할 수 있습니다.

1. Azure Linux 에이전트를 제거 합니다.
    1. SSH 클라이언트를 사용하여 Linux VM에 연결합니다.
    2. SSH 창에서 다음 명령을 입력 `sudo waagent –deprovision+user` 합니다.
    3. Y를 입력하여 계속합니다(확인 단계를 방지하려면 이전 명령에 -force 매개 변수를 추가할 수 있음).
    4. 명령이 완료 되 면 **Exit** 를 입력 하 여 SSH 클라이언트를 닫습니다.
2. 가상 컴퓨터를 중지 합니다.
    1. Azure Portal에서 RG(리소스 그룹)를 선택하고, VM을 할당 취소합니다.
    2. 이제 VM이 일반화 되어이 VM 디스크를 사용 하 여 새 VM을 만들 수 있습니다.

### <a name="take-a-snapshot-of-the-vm-disk"></a>VM 디스크의 스냅숏 만들기

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.
2. 왼쪽 위에서 시작 하 여 **리소스 만들기**를 선택한 다음,를 검색 하 고 **스냅숏**을 선택 합니다.
3. 스냅숏 블레이드에서  **만들기**를 선택 합니다.
4. 스냅샷의 **이름**을 입력합니다.
5. 기존 리소스 그룹을 선택 하거나 새 리소스 그룹의 이름을 입력 합니다.
6. **원본 디스크**에서 스냅샷을 만들 관리 디스크를 선택합니다.
7. 스냅샷 저장에 사용할 **계정 유형**을 선택합니다. 고성능 SSD에 저장할 필요가 없다면 **표준 HDD**를 사용합니다.
8. **만들기**를 선택합니다.

#### <a name="extract-the-vhd"></a>VHD 추출

다음 스크립트를 사용 하 여 스냅숏을 저장소 계정의 VHD로 내보냅니다.

```JSON
#Provide the subscription Id where the snapshot is created
subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
resourceGroupName=myResourceGroupName

#Provide the snapshot name
snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $ snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용 하 여 스냅숏에 대 한 SAS URI를 생성 하 고 SAS URI를 사용 하 여 기본 VHD를 저장소 계정에 복사 합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
| --- | --- |
| az disk grant-access | 기본 VHD 파일을 스토리지 계정으로 복사하거나 온-프레미스로 다운로드하는 데 사용되는 읽기 전용 SAS를 생성합니다.
| az storage blob copy start | 한 저장소 계정에서 다른 저장소 계정으로 blob을 비동기적으로 복사 합니다. `az storage blob show`새 blob의 상태를 확인 하는 데 사용 합니다. |
|
