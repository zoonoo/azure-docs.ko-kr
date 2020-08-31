---
title: Azure 이미지 작성기 서비스 DevOps 작업
description: 응용 프로그램 및 OS를 설치 하 고 구성할 수 있도록 VM 이미지에 빌드 아티팩트를 삽입 하는 Azure DevOps 작업
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 9f948fcc8ad36f8bef8b1ab6a1b74131faea9bd3
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068274"
---
# <a name="azure-image-builder-service-devops-task"></a>Azure 이미지 작성기 서비스 DevOps 작업

이 문서에서는 Azure DevOps 작업을 사용 하 여 응용 프로그램 및 OS를 설치 하 고 구성할 수 있도록 VM 이미지에 빌드 아티팩트를 삽입 하는 방법을 보여 줍니다.

## <a name="devops-task-versions"></a>DevOps 태스크 버전
두 개의 Azure VM AIB (이미지 빌더) DevOps 태스크가 있습니다.

* [' 안정 ' AIB 작업](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)을 통해 최신 업데이트 및 기능을 사용할 수 있으며,이를 통해 고객은 나중에 약 1 주일 후에 ' 안정적인 ' 작업으로 수준을 올릴 수 있습니다. 


* [' 불안정 ' AIB 작업](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary)으로,이를 통해 최신 업데이트 및 기능을 사용 하 여 고객을 ' 안정적인 ' 작업으로 승격 하기 전에 테스트할 수 있습니다. 보고 된 문제가 없고 원격 분석에서 약 1 주일 후에 문제를 표시 하지 않으면 작업 코드를 ' 안정 '로 승격 합니다. 

## <a name="prerequisites"></a>필수 조건

* [Visual Studio Marketplace에서 안정적인 DevOps 작업](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)을 설치 합니다.
* VSTS DevOps 계정이 있어야 하 고 빌드 파이프라인이 생성 되어 있어야 합니다.
* 파이프라인에서 사용 하는 구독에서 이미지 작성기 기능 요구 사항을 등록 하 고 사용 하도록 설정 합니다.
    * [Az PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-powershell#register-features)
    * [Az CLI](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder#register-the-features)
    
* 원본 이미지 리소스 그룹에서 표준 Azure Storage 계정을 만들어 다른 리소스 그룹/저장소 계정을 사용할 수 있습니다. 저장소 계정은 DevOps 작업에서 이미지로 빌드 아티팩트를 전송 하는 데 사용 됩니다.

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>릴리스 파이프라인에 작업 추가

**릴리스 파이프라인**  >  **편집** 선택

사용자 에이전트에서 *+* 추가를 선택 하 여 **이미지 작성기**를 검색 합니다. **추가**를 선택합니다.

다음 태스크 속성을 설정 합니다.

### <a name="azure-subscription"></a>Azure 구독

드롭다운 메뉴에서 이미지 작성기를 실행 하는 데 사용할 구독을 선택 합니다. 원본 이미지가 있고 이미지를 배포할 위치와 동일한 구독을 사용 합니다. 구독 또는 리소스 그룹에 대 한 이미지 작성기 참가자 액세스 권한을 부여 해야 합니다.

### <a name="resource-group"></a>리소스 그룹

임시 이미지 템플릿 아티팩트가 저장 될 리소스 그룹을 사용 합니다. 템플릿 아티팩트를 만들 때 추가 임시 이미지 작성기 리소스 그룹이 `IT_<DestinationResourceGroup>_<TemplateName>_guid` 만들어집니다. 임시 리소스 그룹은 스크립트와 같은 이미지 메타 데이터를 저장 합니다. 작업이 끝나면 이미지 템플릿 아티팩트와 임시 이미지 작성기 리소스 그룹이 삭제 됩니다.
 
### <a name="location"></a>위치

위치는 이미지 작성기가 실행 될 지역입니다. 집합 수의 [지역](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#regions) 만 지원 됩니다. 원본 이미지가이 위치에 있어야 합니다. 예를 들어 공유 이미지 갤러리를 사용 하는 경우 복제본이 해당 지역에 있어야 합니다.

### <a name="managed-identity-required"></a>관리 Id (필수)
이미지 작성기를 사용 하려면 원본 사용자 지정 이미지를 읽고, Azure Storage에 연결 하 고, 사용자 지정 이미지를 만드는 데 사용 하는 관리 Id가 필요 합니다. 자세한 내용은 [여기](https://aka.ms/azvmimagebuilder#permissions)를 참조하세요.

### <a name="vnet-support"></a>VNET 지원

현재 DevOps 태스크는 기존 서브넷을 지정 하는 것을 지원 하지 않습니다 .이는 로드맵에 있지만, 기존 VNET을 활용 하려는 경우에는 포함 된 이미지 작성기 템플릿을 사용 하 여 ARM 템플릿을 사용 하거나,이 작업을 수행 하는 방법에 대 한 Windows 이미지 빌더 템플릿 예제를 참조 하거나 [AZ AIB PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-powershell)을 사용 하십시오.

### <a name="source"></a>원본

원본 이미지는 지원 되는 이미지 작성기 OSs 여야 합니다. 이미지 작성기를 실행 하는 것과 동일한 지역에서 기존 사용자 지정 이미지를 선택할 수 있습니다.
* 관리 이미지-resourceId를 전달 해야 합니다. 예를 들면 다음과 같습니다.
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Azure 공유 이미지 갤러리-이미지 버전의 resourceId를 전달 해야 합니다. 예를 들면 다음과 같습니다.
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    최신 공유 이미지 갤러리 버전을 가져와야 하는 경우에는 AZ PowerShell 또는 AZ CLI 작업을 통해 최신 버전을 가져오고 DevOps 변수를 설정할 수 있습니다. AZ VM Image Builder DevOps 태스크에서 변수를 사용 합니다. 자세한 내용은 [예제](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery)를 참조 하세요.

* 마켓플레이스 기본 이미지 인기 있는 이미지의 드롭다운 목록이 있습니다 .이 목록에는 항상 지원 되는 OS의 ' 최신 ' 버전이 사용 됩니다. 

    기본 이미지가 목록에 없는 경우를 사용 하 여 정확한 이미지를 지정할 수 있습니다 `Publisher:Offer:Sku` .

    기본 이미지 버전 (선택 사항)-사용할 이미지의 버전을 제공할 수 있습니다. 기본값은 `latest` 입니다.

### <a name="customize"></a>사용자 지정

#### <a name="provisioner"></a>Provisioner

처음에는 두 가지 방법, 즉 **Shell** 및 **PowerShell**이 지원 됩니다. 인라인이 유일 하 게 지원 됩니다. 스크립트를 다운로드 하려는 경우 인라인 명령을 전달 하 여이 작업을 수행할 수 있습니다.

OS의 경우 PowerShell 또는 Shell을 선택 합니다.

#### <a name="windows-update-task"></a>작업 Windows 업데이트

Windows의 경우에만 작업이 사용자 지정의 끝에 Windows 업데이트 실행 됩니다. 필요한 재부팅을 처리 합니다.

다음 Windows 업데이트 구성이 실행 됩니다.
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
미리 보기가 아닌 중요 하 고 권장 되는 Windows 업데이트를 설치 합니다.

#### <a name="handling-reboots"></a>다시 부팅 처리
현재 DevOps 작업에는 Windows 빌드를 다시 부팅 하는 기능이 지원 되지 않습니다. PowerShell 코드로 다시 부팅 하려고 하면 빌드가 실패 합니다. 그러나 코드를 사용 하 여 Linux 빌드를 다시 부팅할 수 있습니다.

#### <a name="build-path"></a>빌드 경로

작업은 DevOps 빌드 릴리스 아티팩트를 이미지에 삽입할 수 있도록 설계 되었습니다. 이 작업을 수행 하려면 빌드 파이프라인을 설정 해야 합니다. 릴리스 파이프라인을 설치할 때 빌드 아티팩트의 리포지토리를 추가 해야 합니다.

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="릴리스 파이프라인에서 아티팩트 추가를 선택 합니다.":::

**빌드 경로** 단추를 선택 하 여 이미지에 배치할 빌드 폴더를 선택 합니다. 이미지 작성기 태스크는 그 안에 있는 모든 파일과 디렉터리를 복사 합니다. 이미지를 만들 때 이미지 작성기는 OS에 따라 파일 및 디렉터리를 다른 경로에 배포 합니다.

> [!IMPORTANT]
> 리포지토리 아티팩트를 추가 하는 경우 디렉터리에 밑줄 *_* 이 접두사로 표시 될 수 있습니다. 밑줄을 사용 하면 인라인 명령과 관련 된 문제가 발생할 수 있습니다. 명령에 적절 한 따옴표를 사용 합니다.
> 

다음 예제에서는이 작업을 수행 하는 방법을 설명 합니다.

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="계층을 보여 주는 디렉터리 구조입니다.":::


* Windows-파일은에 `C:\` 있습니다. 디렉터리를 포함 하는 라는 디렉터리를 `buildArtifacts` 만듭니다 `webapp` .

* Linux 파일은에 `/tmp` 있습니다. `webapp`모든 파일 및 디렉터리를 포함 하는 디렉터리가 만들어집니다. 이 디렉터리에서 파일을 이동 해야 합니다. 그렇지 않으면 임시 디렉터리에 있기 때문에 삭제 됩니다.

#### <a name="inline-customization-script"></a>인라인 사용자 지정 스크립트

* Windows-쉼표로 구분 된 PowerShell 인라인 명령을 입력할 수 있습니다. 빌드 디렉터리에서 스크립트를 실행 하려는 경우 다음을 사용할 수 있습니다.

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

* Linux 기반 Linux 시스템 빌드 아티팩트가 디렉터리에 배치 됩니다 `/tmp` . 그러나 많은 Linux OSs에서 다시 부팅 하면/tmp 디렉터리 콘텐츠가 삭제 됩니다. 아티팩트가 이미지에 존재 하도록 하려면 다른 디렉터리를 만들고이를 복사 해야 합니다.  예를 들면 다음과 같습니다.

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    "/Tmp" 디렉터리를 사용 하는 것이 양호 하면 아래 코드를 사용 하 여 스크립트를 실행할 수 있습니다.
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>이미지 빌드 후 빌드 아티팩트는 어떻게 되나요?

> [!NOTE]
> 이미지 작성기는 빌드 아티팩트를 자동으로 제거 하지 않으며 항상 빌드 아티팩트를 제거 하는 코드를 포함 하는 것이 좋습니다.
> 

* Windows 이미지 작성기는 디렉터리에 파일을 배포 `c:\buildArtifacts` 합니다. 디렉터리가 지속형 디렉터리를 제거 해야 합니다. 실행 하는 스크립트에서이를 제거할 수 있습니다. 예를 들면 다음과 같습니다.

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux-빌드 아티팩트가 디렉터리에 배치 됩니다 `/tmp` . 그러나 대부분의 Linux OSs에서 다시 부팅 하면 `/tmp` 디렉터리 내용이 삭제 됩니다. 콘텐츠를 제거 하는 코드를 사용 하 고 OS를 사용 하 여 콘텐츠를 제거 하는 것이 좋습니다. 예를 들면 다음과 같습니다.

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>이미지 빌드의 총 길이

DevOps 파이프라인 작업에서 전체 길이를 변경할 수 없습니다. 240 분의 기본값을 사용 합니다. [BuildTimeoutInMinutes](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#properties-buildtimeoutinminutes)를 증가 시키려면 릴리스 파이프라인에서 AZ CLI 작업을 사용할 수 있습니다. 템플릿을 복사 하 고 제출 하도록 태스크를 구성 합니다. 예제는이 [솔루션](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)을 참조 하거나 Az PowerShell을 사용 합니다.


#### <a name="storage-account"></a>스토리지 계정

필수 구성 요소에서 만든 저장소 계정을 선택 합니다. 목록에 표시 되지 않는 경우 이미지 작성기에는이에 대 한 권한이 없습니다.

빌드를 시작 하면 이미지 작성기가 라는 컨테이너를 만듭니다 `imagebuilder-vststask` . 컨테이너는 리포지토리의 빌드 아티팩트가 저장 되는 위치입니다.

> [!NOTE]
> 각 빌드 후 저장소 계정 또는 컨테이너를 수동으로 삭제 해야 합니다.
>

### <a name="distribute"></a>배포

3 가지 배포 유형이 지원 됩니다.

#### <a name="managed-image"></a>관리되는 이미지

* ResourceID
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* 위치

#### <a name="azure-shared-image-gallery"></a>Azure 공유 이미지 갤러리

공유 이미지 갤러리가 이미 **있어야** 합니다.

* ResourceID 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* 지역: 쉼표로 구분 된 지역 목록입니다. 예: westus, eastus, centralus

#### <a name="vhd"></a>VHD

이에 대 한 값을 전달할 수 없습니다. 이미지 작성기는 VHD 컨테이너의 임시 이미지 작성기 리소스 그룹인로 VHD를 내보냅니다 `IT_<DestinationResourceGroup>_<TemplateName>` . *vhds* 릴리스 빌드를 시작 하면 이미지 작성기가 로그를 내보냅니다. 완료 되 면 VHD URL이 생성 됩니다.

### <a name="optional-settings"></a>선택적 설정

* [Vm 크기](image-builder-json.md#vmprofile) -vm 크기를 기본값 *Standard_D1_v2*에서 재정의할 수 있습니다. 를 재정의 하 여 총 사용자 지정 시간을 줄이거나, GPU/HPC 등의 특정 VM 크기에 따라 달라 지는 이미지를 만들 수 있습니다.

## <a name="how-it-works"></a>작동 방법

릴리스를 만들 때 작업은 저장소 계정에 *imagebuilder-vststask*라는 컨테이너를 만듭니다. 빌드 아티팩트를 zips 및 업로드 하 고 zip 파일에 대 한 SAS 토큰을 만듭니다.

작업은 작업에 전달 된 속성을 사용 하 여 이미지 작성기 템플릿 아티팩트를 만듭니다. 작업은 다음을 수행 합니다.
* 빌드 아티팩트 zip 파일 및 기타 관련 스크립트를 다운로드 합니다. 파일은 임시 이미지 작성기 리소스 그룹의 저장소 계정에 저장 됩니다 `IT_<DestinationResourceGroup>_<TemplateName>` .
* *T_* 접두사가 있는 템플릿 및 10 자리 단조 정수를 만듭니다. 템플릿이 선택한 리소스 그룹에 저장 됩니다. 이 템플릿은 리소스 그룹의 빌드 기간 동안 존재 합니다. 

예제 출력:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

이미지 빌드가 시작 되 면 릴리스 로그에 실행 상태가 보고 됩니다.

```text
starting run template...
```

이미지 빌드가 완료 되 면 다음 텍스트와 유사한 출력이 표시 됩니다.

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

및 이미지 템플릿이 `IT_<DestinationResourceGroup>_<TemplateName>` 삭제 됩니다.

' $ (ImageUri) ' VSTS 변수를 가져와서 다음 작업에서 사용 하거나 값을 사용 하 고 VM을 빌드할 수 있습니다.

## <a name="output-devops-variables"></a>DevOps 변수 출력

원본 marketplace 이미지의 Pub/제안의/SKU/버전:
* $ (pirPublisher)
* $ (pirOffer)
* $ (pirSku)
* $ (pirVersion)

이미지 URI-분산 이미지의 ResourceID입니다.
* $ (imageUri)

## <a name="faq"></a>FAQ

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>이미 만든 기존 이미지 템플릿을 DevOps 외부에서 사용할 수 있나요?

현재는 그렇지 않습니다.

### <a name="can-i-specify-the-image-template-name"></a>이미지 템플릿 이름을 지정할 수 있나요?

아니요. 고유 템플릿 이름이 사용 된 후 삭제 됩니다.

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>이미지 작성기가 실패 했습니다. 어떻게 해결할 수 있나요?

빌드 오류가 발생 하는 경우 DevOps 작업은 준비 리소스 그룹을 삭제 하지 않습니다. 빌드 사용자 지정 로그가 포함 된 준비 리소스 그룹에 액세스할 수 있습니다.

VM 이미지 작성기 태스크에 대 한 DevOps 로그에 오류가 표시 되 고 사용자 지정. 로그 위치를 확인 합니다. 예를 들면 다음과 같습니다.

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="오류를 표시 하는 DevOps 태스크의 예입니다.":::

문제 해결에 대 한 자세한 내용은 [Azure 이미지 작성기 서비스 문제 해결](image-builder-troubleshoot.md)을 참조 하세요. 

실패를 조사 한 후에는 준비 리소스 그룹을 삭제할 수 있습니다. 먼저 이미지 템플릿 리소스 아티팩트를 삭제 합니다. 아티팩트는 *t_* 접두사가 붙습니다. devops 작업 빌드 로그에서 찾을 수 있습니다.

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

이미지 템플릿 리소스 아티팩트가 작업에서 처음 지정 된 리소스 그룹에 있습니다. 문제 해결이 완료 되 면 아티팩트를 삭제 합니다. Azure Portal 사용 하 여 삭제 하는 경우 리소스 그룹 내에서 **숨겨진 형식 표시**를 선택 하 여 아티팩트를 봅니다.


## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 이미지 작성기 개요](image-builder-overview.md)를 참조 하세요.
