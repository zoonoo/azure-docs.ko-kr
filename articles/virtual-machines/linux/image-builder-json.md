---
title: Azure 이미지 빌더 템플릿 만들기(미리 보기)
description: Azure 이미지 빌더에서 사용할 템플릿을 만드는 방법에 대해 알아봅니다.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: e1f1bc09406c34836c13deb805fa399ab4751d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246792"
---
# <a name="preview-create-an-azure-image-builder-template"></a>미리 보기: Azure 이미지 빌더 템플릿 만들기 

Azure 이미지 빌더는 .json 파일을 사용하여 이미지 빌더 서비스에 정보를 전달합니다. 이 문서에서는 json 파일의 섹션을 통해 이동 합니다., 그래서 당신은 당신의 자신의 구축할 수 있습니다. 전체 .json 파일의 예제를 보려면 [Azure 이미지 빌더 GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)를 참조하십시오.

기본 템플릿 형식은 다음과 입니다.

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>형식 및 API 버전

은 `type` 리소스 유형이며, 이 `"Microsoft.VirtualMachineImages/imageTemplates"`형식은 입니다. API가 `apiVersion` 변경되면 시간이 지남에 따라 `"2019-05-01-preview"` 변경되지만 미리 보기여야 합니다.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>위치

위치는 사용자 지정 이미지가 생성되는 영역입니다. 이미지 빌더 미리 보기의 경우 다음 지역이 지원됩니다.

- 미국 동부
- 미국 동부 2
- 미국 중서부
- 미국 서부
- 미국 서부 2
- 북유럽
- 서유럽


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
기본적으로 이미지 빌더는 "Standard_D1_v2" 빌드 VM을 사용하며, 예를 들어 GPU VM에 대한 이미지를 사용자 지정하려는 경우 GPU VM 크기가 필요합니다. 이 구성 요소는 선택 사항입니다.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

기본적으로 이미지 빌더는 이미지의 크기를 변경하지 않으며 소스 이미지의 크기를 사용합니다. OS 디스크(Win 및 Linux)의 크기를 늘릴 수 있으며, 이는 선택 사항이며 값이 0이면 소스 이미지와 동일한 크기를 유지합니다. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
VNET 속성을 지정하지 않으면 이미지 빌더가 자체 VNET, 공용 IP 및 NSG를 만듭니다. 공용 IP는 공용 IP를 원하지 않거나 이미지 빌더가 구성 서버(DSC, Chef, Puppet, Ansible), 파일 공유 등과 같은 기존 VNET 리소스에 액세스하도록 하려는 경우 빌드 VM과 통신하는 데 사용됩니다. 을 하면 VNET을 지정할 수 있습니다. 자세한 내용은 네트워킹 [설명서를](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)검토하는 경우 선택 사항입니다.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>태그들

생성된 이미지에 대해 지정할 수 있는 키/값 쌍입니다.

## <a name="depends-on-optional"></a>에 따라 달라 집니다 (선택 사항)

이 선택적 섹션을 사용하여 계속하기 전에 종속성이 완료되도록 할 수 있습니다. 

```json
    "dependsOn": [],
```

자세한 내용은 [리소스 종속성 정의를](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)참조하십시오.

## <a name="identity"></a>Identity
기본적으로 Image Builder는 스크립트를 사용하거나 GitHub 및 Azure 저장소와 같은 여러 위치에서 파일을 복사하는 것을 지원합니다. 이러한 파일을 사용하려면 공개적으로 액세스할 수 있어야 합니다.

또한 사용자가 정의한 Azure 사용자 할당관리 ID를 사용하여 Azure 저장소 계정에 최소 '저장소 Blob 데이터 판독기'가 부여된 경우 이미지 빌더가 Azure 저장소에 액세스할 수 있도록 허용할 수도 있습니다. 즉, 저장소 Blob을 외부에서 액세스할 수 있도록 하거나 SAS 토큰을 설정할 필요가 없습니다.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

전체 예제에서는 [Azure 사용자 할당된 관리되는 ID를 사용하여 Azure 저장소의 파일에 액세스합니다.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)

사용자 할당 ID에 대한 이미지 빌더 지원: • 단일 ID만 지원 • 사용자 지정 도메인 이름을 지원하지 않음

자세한 내용은 [Azure 리소스에 대한 관리ID란 무엇입니까?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
이 기능 배포에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리되는 ID 구성을](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)참조하십시오.

## <a name="properties-source"></a>속성: 소스

섹션에는 `source` 이미지 작성기에서 사용할 소스 이미지에 대한 정보가 포함되어 있습니다.

API에는 이미지 빌드의 소스를 정의하는 'SourceType'이 필요하며 현재 세 가지 유형이 있습니다.
- PlatformImage - 소스 이미지가 마켓플레이스 이미지임을 나타냅니다.
- 관리이미지 - 일반 관리 이미지에서 시작할 때 이 것을 사용합니다.
- SharedImageVersion - 공유 이미지 갤러리에서 이미지 버전을 소스로 사용할 때 사용됩니다.

### <a name="iso-source"></a>ISO 소스
우리는 이미지 빌더에서이 기능을 더 이상 사용되지 않습니다, 지금 [RHEL이](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos)있기 때문에 자신의 가입 이미지를 가지고, 아래의 타임 라인을 검토하시기 바랍니다 :
    * 2020년 3월 31일 - RHEL ISO 소스가 있는 이미지 템플릿은 이제 리소스 공급자가 더 오래 수락할 수 있습니다.
    * 2020년 4월 30일 - RHEL ISO 소스가 포함된 이미지 템플릿은 더 이상 처리되지 않습니다.

### <a name="platformimage-source"></a>플랫폼이미지 소스 
Azure 이미지 빌더는 Windows 서버 및 클라이언트를 지원하며 Linux Azure 마켓플레이스 이미지는 전체 목록은 [여기를](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) 참조하십시오. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


여기에 있는 속성은 AZ CLI를 사용하여 VM을 만드는 데 사용되는 속성과 동일하며 아래를 실행하여 속성을 가져옵니다. 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

버전에서 '최신'을 사용할 수 있으며 템플릿이 제출될 때가 아니라 이미지 빌드가 수행될 때 버전이 평가됩니다. 공유 이미지 갤러리 대상에서 이 기능을 사용하는 경우 템플릿을 다시 제출하지 않도록 하고 이미지 빌드를 간격으로 다시 실행하여 가장 최근 이미지에서 이미지를 다시 만들 수 있습니다.

### <a name="managedimage-source"></a>관리이미지 소스

소스 이미지를 일반화된 VHD 또는 VM의 기존 관리 이미지로 설정합니다. 원본 관리 이미지는 지원되는 OS이어야 하며 Azure 이미지 빌더 템플릿과 동일한 지역에 있어야 합니다. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

관리되는 `imageId` 이미지의 ResourceId여야 합니다. 사용 `az image list` 가능한 이미지를 나열하는 데 사용합니다.


### <a name="sharedimageversion-source"></a>공유 이미지 버전 소스
소스 이미지를 공유 이미지 갤러리에서 기존 이미지 버전으로 설정합니다. 이미지 버전은 지원되는 OS여야 하며 Azure 이미지 빌더 템플릿과 동일한 리전에 이미지를 복제해야 합니다. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

는 `imageVersionId` 이미지 버전의 ResourceId여야 합니다. [az sig 이미지 버전 목록을](/cli/azure/sig/image-version#az-sig-image-version-list) 사용하여 이미지 버전을 나열합니다.

## <a name="properties-buildtimeoutinminutes"></a>속성: 빌드타임아웃인분

기본적으로 이미지 빌더는 240분 동안 실행됩니다. 그런 다음 이미지 빌드가 완료되었는지 여부에 관계없이 시간 시간이 중지되고 중지됩니다. 시간 지정이 적중되면 다음과 유사한 오류가 표시됩니다.

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

buildTimeoutInMinutes 값을 지정하지 않거나 0으로 설정하지 않으면 기본값을 사용합니다. 최대 960분(16시간)까지 값을 늘리거나 줄일 수 있습니다. Windows의 경우 60분 미만으로 설정하지 않는 것이 좋습니다. 시간 설정이 지정되는 경우 [로그를](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)검토하여 사용자 지정 단계가 사용자 입력과 같은 것을 기다리고 있는지 확인합니다. 

사용자 지정을 완료하는 데 더 많은 시간이 필요한 경우 약간의 오버헤드로 필요한 것으로 설정합니다. 그러나 오류를 보기 전에 시간 시간이 지나갈 때까지 기다려야 할 수 있으므로 너무 높게 설정하지 마십시오. 


## <a name="properties-customize"></a>속성: 사용자 지정

이미지 빌더는 여러 '사용자 지정자'를 지원합니다. 사용자 지정자는 스크립트 실행 또는 서버 재부팅과 같이 이미지를 사용자 지정하는 데 사용되는 기능입니다. 

사용 `customize`시 : 
- 여러 사용자 지정자를 사용할 수 있지만 고유한. `name`
- 사용자 지정자는 템플릿에 지정된 순서로 실행됩니다.
- 사용자 지정자가 하나 실패하면 전체 사용자 지정 구성 요소가 실패하고 오류를 다시 보고합니다.
- 템플릿에서 사용하기 전에 스크립트를 철저히 테스트하는 것이 좋습니다. 자체 VM에서 스크립트를 디버깅하는 것이 더 쉬워집니다.
- 스크립트에 중요한 데이터를 넣지 마십시오. 
- [MSI를](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)사용하지 않는 한 스크립트 위치에 공개적으로 액세스할 수 있어야 합니다.

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
사용자 지정 섹션은 배열입니다. Azure 이미지 빌더는 사용자 지정자를 순차적으로 실행합니다. 사용자 지정자에서 오류가 발생하면 빌드 프로세스가 실패합니다. 
 
 
### <a name="shell-customizer"></a>쉘 커스터마이저

셸 사용자 지정자는 실행 중인 셸 스크립트를 지원하며 IB가 셸스크립트에 액세스하려면 공개적으로 액세스할 수 있어야 합니다.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

OS 지원: 리눅스 
 
속성 사용자 지정:

- **유형** – 셸 
- **이름** - 사용자 지정 을 추적하기위한 이름 
- **scriptUri** - 파일의 위치에 URI 
- **인라인** - 쉼표로 구분된 쉘 명령의 배열입니다.
- **sha256Checksum** - 파일의 sha256 체크섬 값은 로컬로 생성한 다음 이미지 빌더가 체크섬및 유효성 검사를 합니다.
    * Mac/Linux 실행에서 터미널을 사용하여 sha256Checksum을 생성하려면 다음을 수행합니다.`sha256sum <fileName>`


명령이 수퍼 사용자 권한으로 실행되려면 에 `sudo`접두사에 붙어 있어야 합니다.

> [!NOTE]
> RHEL ISO 소스를 사용하여 셸 사용자 지정자를 실행할 때 사용자 지정이 발생하기 전에 Red Hat 권한 부여 서버에 등록하는 첫 번째 사용자 지정 셸 핸들을 확인해야 합니다. 사용자 지정이 완료되면 스크립트가 권한 부여 서버에 등록 취소되어야 합니다.

### <a name="windows-restart-customizer"></a>윈도우 다시 시작 사용자 지정 
다시 시작 사용자 지정을 사용하면 Windows VM을 다시 시작하고 다시 온라인 상태가 될 때까지 기다릴 수 있으므로 재부팅이 필요한 소프트웨어를 설치할 수 있습니다.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

OS 지원: 윈도우
 
속성 사용자 지정:
- **유형**: 윈도우 리스타트
- **다시 시작명령** - 다시 시작을 실행하는 명령(선택 사항). 기본값은 `'shutdown /r /f /t 0 /c \"packer restart\"'`입니다.
- **다시 시작체크명령** - 다시 시작이 성공했는지 확인하는 명령(선택 사항). 
- **다시 시작시간 아웃** - 크기 및 단위의 문자열로 지정된 시간 시간을 다시 시작합니다. 예를 들어(5분) `5m` `2h` 또는 (2시간). 기본값은 '5m'입니다.

### <a name="linux-restart"></a>리눅스 다시 시작  
리눅스 다시 시작 사용자 지정자는 없습니다, 그러나, 드라이버를 설치 하는 경우, 또는 다시 시작 해야 하는 구성 요소, 그들을 설치 하 고 쉘 사용자 지정기를 사용 하 여 다시 시작을 호출할 수 있습니다., 빌드 VM에 20 분 SSH 시간 설정.

### <a name="powershell-customizer"></a>파워쉘 커스터마이저 
셸 사용자 지정자는 PowerShell 스크립트 및 인라인 명령 실행을 지원하며 IB가 스크립트에 액세스하려면 스크립트에 공개적으로 액세스할 수 있어야 합니다.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

OS 지원 : 윈도우와 리눅스

속성 사용자 지정:

- **유형** - 파워쉘.
- **scriptUri** - PowerShell 스크립트 파일의 위치에 URI를 제공합니다. 
- **인라인** - 쉼표로 구분하여 실행할 인라인 명령을 실행합니다.
- **validExitCodes** - 스크립트/인라인 명령에서 반환할 수 있는 선택적 유효한 코드는 스크립트/인라인 명령의 보고된 오류를 방지합니다.
- **runElevated** - 선택 사항, 부울, 높은 권한이있는 명령 및 스크립트 실행 지원.
- **sha256Checksum** - 파일의 sha256 체크섬 값은 로컬로 생성한 다음 이미지 빌더가 체크섬및 유효성 검사를 합니다.
    * sha256체크섬을 생성하려면 Windows [Get-해시에서](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6) PowerShell을 사용하여


### <a name="file-customizer"></a>파일 사용자 지정자

파일 사용자 지정기를 사용하면 이미지 작성기가 GitHub 또는 Azure 저장소에서 파일을 다운로드할 수 있습니다. 빌드 아티팩트에 의존하는 이미지 빌드 파이프라인이 있는 경우 빌드 공유에서 다운로드하도록 파일 사용자 지정자를 설정하고 아티팩트를 이미지로 이동할 수 있습니다.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

OS 지원 : 리눅스와 윈도우 

파일 사용자 지정자 속성:

- **sourceUri** - 액세스 가능한 저장소 끝점, 이것은 GitHub 또는 Azure 저장소일 수 있습니다. 전체 디렉토리가 아닌 하나의 파일만 다운로드할 수 있습니다. 디렉토리를 다운로드해야 하는 경우 압축 된 파일을 사용한 다음 Shell 또는 PowerShell 사용자 지정기를 사용하여 압축을 취소합니다. 
- **대상** - 전체 대상 경로 및 파일 이름입니다. 참조된 경로 및 하위 디렉터리도 있어야 하며 Shell 또는 PowerShell 사용자 지정자를 사용하여 미리 설정해야 합니다. 스크립트 사용자 지정자를 사용하여 경로를 만들 수 있습니다. 

이것은 Windows 디렉토리 및 Linux 경로에서 지원되지만 몇 가지 차이점이 있습니다. 
- 리눅스 OS의 – 이미지 빌더가 쓸 수있는 유일한 경로는 /tmp입니다.
- Windows - 경로 제한이 없지만 경로가 있어야 합니다.
 
 
파일을 다운로드하거나 지정된 디렉터리에 넣으려는 오류가 있는 경우 사용자 지정 단계가 실패하고 이 단계가 customization.log에 표시됩니다.

> [!NOTE]
> 파일 사용자 정의는 20MB< 작은 파일 다운로드에만 적합합니다. 대규모 파일 다운로드의 경우 스크립트 또는 인라인 명령을 사용하여 코드를 사용하여 `wget` 리눅스 `curl` `Invoke-WebRequest`또는 Windows 와 같은 파일을 다운로드합니다.

파일 사용자 정의의 파일은 [MSI를](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)사용하여 Azure 저장소에서 다운로드할 수 있습니다.

### <a name="windows-update-customizer"></a>윈도우 업데이트 사용자 지정
이 사용자 지정자는 Packer 커뮤니티에서 유지 관리하는 오픈 소스 프로젝트인 Packer용 [Windows 업데이트 프로비저너](https://packer.io/docs/provisioners/community-supported.html) 커뮤니티에 빌드됩니다. Microsoft는 이미지 빌더 서비스를 사용하여 프로비저작성을 테스트하고 유효성을 검사하며, 이에 대한 조사를 지원하고 문제를 해결하기 위해 노력하지만 오픈 소스 프로젝트는 Microsoft에서 공식적으로 지원하지 않습니다. 자세한 설명서및 Windows 업데이트 프로비저너에 대한 도움말은 프로젝트 리포지토리를 참조하십시오.
 
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
OS 지원: 윈도우

속성 사용자 지정:
- **유형** - 윈도우 업데이트.
- **searchCriteria** - 선택 사항, 설치된 업데이트 유형 (권장, 중요 등),BrowseOnly=0 및 설치됨 =0 (권장)가 기본값입니다.
- **필터** - 선택 사항으로 업데이트를 포함하거나 제외할 필터를 지정할 수 있습니다.
- **updateLimit** - 선택 사항, 설치할 수 있는 업데이트 수를 정의합니다(기본 값 1000).
 
 

### <a name="generalize"></a>일반화 
기본적으로 Azure Image Builder는 각 이미지 사용자 지정 단계가 끝날 때 '프로비저닝 해제' 코드를 실행하여 이미지를 '일반화'합니다. 일반화는 여러 VM을 만드는 데 재사용할 수 있도록 이미지를 설정하는 프로세스입니다. Windows VM의 경우 Azure 이미지 빌더는 Sysprep을 사용합니다. Linux의 경우 Azure 이미지 빌더는 '와에이전트-프로비저닝 해제'를 실행합니다. 

이미지 빌더 사용자가 일반화하는 명령은 모든 상황에 적합하지 않을 수 있으므로 Azure Image Builder를 사용하면 필요한 경우 이 명령을 사용자 지정할 수 있습니다. 

기존 사용자 지정을 마이그레이션하는 경우 다른 Sysprep/waagent 명령을 사용하는 경우 이미지 빌더 일반 명령을 사용할 수 있으며 VM 생성이 실패하면 고유한 Sysprep 또는 waagent 명령을 사용할 수 있습니다.

Azure Image Builder가 Windows 사용자 지정 이미지를 성공적으로 만들고 VM을 만든 다음 VM 생성이 실패하거나 성공적으로 완료되지 않은 경우 Windows Server Sysprep 설명서를 검토하거나 지원 요청을 제기해야 합니다. Windows Server Sysprep 고객 서비스 지원 팀에서 올바른 Sysprep 사용에 대한 문제 해결 및 조언을 제공합니다.


#### <a name="default-sysprep-command"></a>기본 Sysprep 명령
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>기본 Linux 프로비저닝 해제 명령

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>명령 재정의
명령을 재정의하려면 PowerShell 또는 Shell 스크립트 프로비저터를 사용하여 정확한 파일 이름으로 명령 파일을 만들고 올바른 디렉토리에 넣습니다.

* 윈도우: c:\프로비저닝 스크립트.ps1
* 리눅스: /tmp/프로비저닝 스크립트.sh

이미지 빌더는 이러한 명령을 읽고, 이들은 AIB 로그에 기록됩니다, '사용자 정의.log'. 로그를 수집하는 방법에 대한 [문제 해결을](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) 참조하세요.
 
## <a name="properties-distribute"></a>속성: 배포

Azure 이미지 빌더는 세 가지 배포 대상을 지원합니다. 

- **관리이미지** - 관리형 이미지.
- **공유이미지** - 공유 이미지 갤러리.
- **VHD** - 스토리지 계정의 VHD.

동일한 구성의 두 대상 유형에 이미지를 배포할 수 [있습니다.](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)

배포할 대상을 두 개 이상 가질 수 있으므로 Image Builder는 `runOutputName`을 쿼리하여 액세스할 수 있는 모든 배포 대상에 대한 상태를 유지관리합니다.  는 `runOutputName` 해당 배포에 대 한 정보에 대 한 게시물 배포를 쿼리할 수 있는 개체입니다. 예를 들어 VHD의 위치 또는 이미지 버전이 복제된 지역 또는 SIG 이미지 버전이 생성된 영역을 쿼리할 수 있습니다. 모든 배포 대상의 속성입니다. 각 `runOutputName` 배포 대상에 고유해야 합니다. 다음은 공유 이미지 갤러리 배포를 쿼리하는 예제입니다.

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

출력:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>배포: 관리이미지

이미지 출력은 관리되는 이미지 리소스가 됩니다.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
속성 배포:
- **유형** – 관리이미지 
- **imageId** - 대상 이미지의 리소스 ID, 예상\<형식: /구독/\<구독Id>/리소스그룹/대상ResourceGroupName>/공급자/Microsoft.Compute/이미지/이미지\<>
- **위치** - 관리되는 이미지의 위치입니다.  
- **runOutputName** - 분포를 식별하기 위한 고유한 이름입니다.  
- **artifactTags** - 선택적 사용자 지정 키 값 쌍 태그입니다.
 
 
> [!NOTE]
> 대상 리소스 그룹이 있어야 합니다.
> 이미지를 다른 지역에 배포하려면 배포 시간이 늘어남을 수 있습니다. 

### <a name="distribute-sharedimage"></a>배포: 공유이미지 
Azure 공유 이미지 갤러리는 이미지 영역 복제, 버전 관리 및 사용자 지정 이미지 공유를 관리할 수 있는 새로운 이미지 관리 서비스입니다. Azure 이미지 빌더는 이 서비스를 사용하여 배포를 지원하므로 공유 이미지 갤러리에서 지원하는 지역에 이미지를 배포할 수 있습니다. 
 
공유 이미지 갤러리는 다음과 같은 구성으로 구성됩니다. 
 
- 갤러리 - 여러 공유 이미지에 대한 컨테이너입니다. 갤러리는 한 지역에 배포됩니다.
- 이미지 정의 - 이미지에 대한 개념적 그룹화입니다. 
- 이미지 버전 - VM 또는 배율 집합을 배포하는 데 사용되는 이미지 유형입니다. 이미지 버전은 VM을 배포해야 하는 다른 지역으로 복제할 수 있습니다.
 
이미지 갤러리에 배포하려면 먼저 갤러리와 이미지 정의를 만들고 [공유 이미지를](shared-images.md)참조해야 합니다. 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

공유 이미지 갤러리에 대한 속성 배포:

- **유형** - 공유이미지  
- **갤러리이미지Id** – 공유 이미지 갤러리의 ID입니다. 형식은 다음과 같다 :\</ 구독 /\<구독Id> / 리소스그룹 / 리소스그룹>\</ 공급자 / Microsoft.Compute / 갤러리 / 공유이미지 갤러리 이름> / 이미지 /\<이미지GalleryName>.
- **runOutputName** - 분포를 식별하기 위한 고유한 이름입니다.  
- **artifactTags** - 선택적 사용자 지정 키 값 쌍 태그입니다.
- **복제지역** - 복제를 위한 영역 배열입니다. 지역 중 하나는 갤러리가 배포된 영역이어야 합니다.
 
> [!NOTE]
> 다른 지역에서 Azure 이미지 빌더를 갤러리로 사용할 수 있지만 Azure 이미지 빌더 서비스는 데이터 센터 간에 이미지를 전송해야 하며 시간이 더 오래 걸릴 수 있습니다. 이미지 빌더는 단조 정수를 기반으로 이미지를 자동으로 버전화하므로 현재 지정할 수 없습니다. 

### <a name="distribute-vhd"></a>배포: VHD  
VHD로 출력할 수 있습니다. 그런 다음 VHD를 복사하여 Azure MarketPlace에 게시하거나 Azure 스택과 함께 사용할 수 있습니다.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
OS 지원 : 윈도우와 리눅스

VHD 매개 변수 배포:

- **유형** - VHD.
- **runOutputName** - 분포를 식별하기 위한 고유한 이름입니다.  
- **태그** - 선택적 사용자 지정 키 값 쌍 태그입니다.
 
Azure Image Builder는 사용자가 저장소 계정 위치를 지정할 수 있도록 허용하지 `runOutputs` 않지만 위치를 얻기 위해 의 상태를 쿼리할 수 있습니다.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> VHD가 생성되면 가능한 한 빨리 다른 위치로 복사합니다. VHD는 이미지 템플릿이 Azure 이미지 빌더 서비스에 제출될 때 생성된 임시 리소스 그룹의 저장소 계정에 저장됩니다. 이미지 템플릿을 삭제하면 VHD가 손실됩니다. 
 
## <a name="next-steps"></a>다음 단계

[Azure 이미지 빌더 GitHub](https://github.com/danielsollondon/azvmimagebuilder)에는 다양한 시나리오에 대한 샘플 .json 파일이 있습니다.
 
