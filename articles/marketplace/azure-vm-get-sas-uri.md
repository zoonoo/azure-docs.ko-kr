---
title: VM 이미지에 대 한 SAS URI 생성-Azure Marketplace
description: Azure Marketplace에서 VHD (가상 하드 디스크)에 대 한 SAS (공유 액세스 서명) URI를 생성 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 02/19/2021
ms.openlocfilehash: 870482ca7894c5e260a78270fb036d6a6b22ee41
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630064"
---
# <a name="how-to-generate-a-sas-uri-for-a-vm-image"></a>VM 이미지에 대 한 SAS URI를 생성 하는 방법

> [!NOTE]
> VM을 게시 하는 데 SAS URI가 필요 하지 않습니다. 단지 Parter 센터에서 이미지를 공유할 수 있습니다. [승인 된 기본을 사용 하 여 가상 머신 만들기](https://docs.microsoft.com/azure/marketplace/azure-vm-create-using-approved-base) 또는 [고유한 이미지 지침을 사용 하 여 가상 머신](https://docs.microsoft.com/azure/marketplace/azure-vm-create-using-own-image) 만들기를 참조 하세요.

Vhd에 대 한 SAS Uri 생성에는 다음과 같은 요구 사항이 있습니다.

- 관리 되지 않는 Vhd만 지원 합니다.
- 목록 및 읽기 권한만 필요 합니다. 쓰기 또는 삭제 권한을 제공 하지 마세요.
- 액세스 기간(만료 날짜)은 SAS URI가 생성된 시점에서 3주 이상이어야 합니다.
- UTC 시간 변경을 방지하려면 시작 날짜를 현재 날짜 전으로 설정합니다. 예를 들어 현재 날짜가 2020 년 6 월 16 일 경우 6/15/2020를 선택 합니다.

## <a name="extract-vhd-from-a-vm"></a>VM에서 vhd 추출

> [!NOTE]
> 저장소 계정에 업로드 된 vhd가 이미 있는 경우이 단계를 건너뛸 수 있습니다.

VM에서 vhd를 추출 하려면 VM 디스크의 스냅숏을 만들고 스냅숏에서 vhd를 추출 해야 합니다.

먼저 VM 디스크의 스냅숏을 작성 합니다.

1. Azure Portal에 로그인합니다.
2. 왼쪽 위에서 시작 하 여 리소스 만들기를 선택한 다음,를 검색 하 고 스냅숏을 선택 합니다.
3. 스냅숏 블레이드에서 만들기를 선택 합니다.
4. 스냅샷의 이름을 입력합니다.
5. 기존 리소스 그룹을 선택 하거나 새 리소스 그룹의 이름을 입력 합니다.
6. 원본 디스크에서 스냅샷을 만들 관리 디스크를 선택합니다.
7. 스냅샷 저장에 사용할 계정 유형을 선택합니다. 고성능 SSD에 저장할 필요가 없다면 표준 HDD를 사용합니다.
8. 만들기를 선택합니다.

### <a name="extract-the-vhd"></a>VHD 추출

다음 스크립트를 사용 하 여 스냅숏을 저장소 계정의 VHD로 내보냅니다.

```azurecli
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

### <a name="script-explanation"></a>스크립트 설명
이 스크립트는 다음 명령을 사용 하 여 스냅숏에 대 한 SAS URI를 생성 하 고 SAS URI를 사용 하 여 기본 VHD를 저장소 계정에 복사 합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.


|명령  |메모  |
|---------|---------|
| az disk grant-access    |     기본 VHD 파일을 스토리지 계정으로 복사하거나 온-프레미스로 다운로드하는 데 사용되는 읽기 전용 SAS를 생성합니다.    |
|  az storage blob copy start   |    한 저장소 계정에서 다른 저장소 계정으로 blob을 비동기적으로 복사 합니다. Az storage blob show를 사용 하 여 새 blob의 상태를 확인 합니다.     |
|

## <a name="generate-the-sas-address"></a>SAS 주소 생성

SAS 주소 (URL)를 만드는 데 사용 되는 두 가지 일반적인 도구는 다음과 같습니다.

1. **Azure Storage 탐색기** – Azure Portal에서 사용할 수 있습니다.
2. **Azure CLI** – Windows 이외의 운영 체제와 자동화 된 또는 지속적인 통합 환경에 권장 됩니다.

### <a name="using-tool-1-azure-storage-explorer"></a>도구 1 사용: Azure Storage 탐색기

1. **저장소 계정** 으로 이동 합니다.
1. **Storage 탐색기** 를 엽니다.

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="저장소 계정 창.":::

3. **컨테이너** 에서 VHD 파일을 마우스 오른쪽 단추로 클릭 하 고 **공유 액세스 서명 가져오기** 를 선택 합니다.
4. **공유 액세스 서명** 대화 상자에서 다음 필드를 완료 합니다.

    1. 시작 시간 – VHD 액세스 권한의 시작 날짜입니다. 현재 날짜보다 하루 전의 날짜를 제공합니다.
    2. 만료 시간 – VHD 액세스 권한의 만료 날짜입니다. 현재 날짜보다 최소 3주 후의 날짜를 제공합니다.
    3. 권한 – 읽기 및 목록 사용 권한을 선택합니다.
    4. 컨테이너 수준 – 컨테이너 수준 공유 액세스 서명 URI 생성 확인란을 선택합니다.

    ![공유 액세스 서명 대화 상자](media/vm/create-sas-uri-storage-explorer.png)

5. 이 VHD에 연결되는 SAS URI를 만들려면 **만들기** 를 선택합니다.
6. URI를 복사하고, 안전한 위치에 텍스트 파일로 저장합니다. 이 생성된 SAS URI는 컨테이너 수준 액세스를 위한 것입니다. 이를 특정 하 게 만들려면 텍스트 파일을 편집 하 여 VHD 이름을 추가 합니다.
7. SAS URI의 VHD 문자열 뒤에 VHD 이름을 삽입합니다(앞의 슬래시 포함). 최종 SAS URI는 다음과 같습니다.

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. 게시할 계획의 각 VHD에 대해 이 단계를 반복합니다.

### <a name="using-tool-2-azure-cli"></a>도구 2 사용: Azure CLI

1. [MICROSOFT AZURE CL](/cli/azure/install-azure-cli)I를 다운로드 하 여 설치 합니다. 버전은 Windows, macOS 및 다양한 Linux 배포판에서 사용할 수 있습니다.
2. PowerShell 파일(.ps1 파일 확장명)을 만들고, 다음 코드를 복사한 다음, 로컬로 저장합니다.

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <container-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. 파일을 편집하여 다음 매개 변수 값을 사용합니다. UTC 날짜/시간 형식으로 날짜를 입력 합니다 (예: 2020-04-01T00:00:00Z).

    - 계정-이름 – Azure storage 계정 이름입니다.
    - 계정-키 – Azure storage 계정 키입니다.
    - 시작-날짜 – VHD 액세스를 위한 사용 권한 시작 날짜입니다. 현재 날짜보다 하루 전의 날짜를 제공합니다.
    - 만료 날짜-VHD 액세스를 위한 사용 권한 만료 날짜입니다. 현재 날짜 이후 최소 3주 후의 날짜를 제공합니다.

    다음은이 문서를 작성할 당시에 적절 한 매개 변수 값의 예입니다.

    ```azurecli-interactive
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name <container-name> -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’
    ```

1. 변경 내용을 저장합니다.
2. 다음 방법 중 하나를 사용하여 이 스크립트를 관리자 권한으로 실행해 컨테이너 수준 액세스를 위한 SAS 연결 문자열을 만듭니다.

    - 콘솔에서 스크립트를 실행합니다. Windows에서 스크립트를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행** 을 선택합니다.
    - [Windows PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)와 같은 PowerShell 스크립트 편집기에서 스크립트를 실행합니다. 이 화면은 이 편집기 내에서 SAS 연결 문자열을 만드는 것을 보여 줍니다.

    [![PowerShell 편집기 내에서 SAS 연결 문자열 만들기](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. SAS 연결 문자열을 복사하고, 안전한 위치에 텍스트 파일로 저장합니다. 이 문자열을 편집하여 최종 SAS URI를 만들기 위한 VHD 위치 정보를 추가합니다.
7. Azure Portal에서 새 URI와 연결된 VHD를 포함하는 BLOB 스토리지로 이동합니다.
8. Blob service 끝점의 URL을 복사 합니다.

    ![Blob service 끝점의 URL을 복사 하는 중입니다.](media/vm/create-sas-uri-blob-endpoint.png)

9. 6단계의 SAS 연결 문자열을 사용하여 텍스트 파일을 편집합니다. 다음 형식을 사용하여 전체 SAS URI를 만듭니다.

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>SAS URI 확인

파트너 센터에 게시 하기 전에 SAS URI를 확인 하 여 요청에 대 한 SAS URI 사후 전송과 관련 된 문제를 방지 합니다. 이 프로세스는 선택 사항 이지만 권장 됩니다.

- URI에는 파일 이름 확장명을 포함 하 여 VHD 이미지 파일 이름이 포함 됩니다 `.vhd` .
- `Sp=rl`이 URI의 중간에 표시됩니다. 이 문자열은 읽기 및 나열 액세스를 지정 합니다.
- `sr=c`이 표시되면 컨테이너 수준 액세스가 지정된 것입니다.
- Blob을 테스트 다운로드하려면 URI를 브라우저에 복사하여 붙여넣습니다(다운로드를 완료하기 전에 작업을 취소할 수 있음).

## <a name="next-steps"></a>다음 단계

- 문제가 발생 하는 경우 [VM SAS 오류 메시지](azure-vm-sas-failure-messages.md)를 참조 하세요.
- [파트너 센터에 로그인](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
- [Azure Marketplace에서 가상 컴퓨터 제품 만들기](azure-vm-create.md)
