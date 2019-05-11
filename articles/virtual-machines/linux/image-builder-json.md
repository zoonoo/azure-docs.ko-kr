---
title: Azure 이미지 작성기 템플릿 (미리 보기) 만들기
description: Azure 이미지 작성기를 사용 하는 템플릿을 만드는 방법에 알아봅니다.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: b4646879eb7eeecf41852baab7ab64e4053b05e1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159602"
---
# <a name="preview-create-an-azure-image-builder-template"></a>미리 보기: Azure 이미지 작성기 템플릿 만들기 

Azure 이미지 작성기 이미지 작성기 서비스로 정보를 전달 하는.json 파일을 사용 합니다. 이 문서의 살펴보겠습니다 json 파일의 섹션을 직접 빌드할 수 있도록 합니다. 전체.json 파일의 예제를 보려면 참조는 [Azure 이미지 작성기 GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)합니다.

다음은 기본 템플릿 형식입니다.

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
        "<build timeout in minutes>": {}, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>형식 및 API 버전

합니다 `type` 이어야 하는 리소스 유형이 며 `"Microsoft.VirtualMachineImages/imageTemplates"`합니다. 합니다 `apiVersion` API 변경 내용으로 시간이 지남에 따라 변경 됩니다 있지만 있어야 `"2019-05-01-preview"` 미리 보기에 대 한 합니다.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

위치는 사용자 지정 이미지 만들어지는 지역입니다. 이미지 작성기 미리 보기의 경우에 다음 지역만 지원 됩니다.

- 미국 동부
- 미국 동부 2
- 미국 중서부
- 미국 서부
- 미국 서부 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>(선택 사항)에 따라 달라 집니다.

종속성은 계속 하기 전에 완료 되도록 하려면이 선택적 섹션을 사용할 수 있습니다. 

```json
    "dependsOn": [],
```

자세한 내용은 [리소스 종속성을 정의](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)합니다.

## <a name="identity"></a>ID
기본적으로 이미지 작성기 지원 스크립트를 사용 하 여 또는 GitHub와 Azure storage와 같은 여러 위치에서 파일을 복사 합니다. 이 사용 하려면 공개적으로 액세스 가능한 같아야 합니다.

또한 id ' Storage Blob 데이터 판독기 ' 최소 Azure 저장소 계정에 부여 된으로 Azure Storage에 이미지 작성기 액세스할 수 있도록 Azure User-Assigned 관리 Id가 정의 사용할 수 있습니다. 즉, 설치 SAS 토큰 또는 외부에서 액세스할 수 있는 저장소 blob을 만들 필요가 없습니다.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

전체 예제를 참조 하세요 [ Azure User-Assigned 관리 Id를 사용 하 여 Azure Storage의 파일을 액세스할](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)합니다.

이미지를 사용자 할당 Id에 대 한 작성기 지원 합니다. • • 사용자 지정 도메인 이름을 지원 하지 않습니다만 단일 id를 지원

자세한 내용은 참조 하세요 [Azure 리소스에 대 한 관리 되는 id 란?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)합니다.
이 기능을 배포 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure CLI를 사용 하 여 Azure VM에서 Azure 리소스에 대 한 id를 관리 하는 구성](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)합니다.

## <a name="properties-source"></a>속성: 원본

`source` 섹션 이미지 작성기에서 사용할 원본 이미지에 대 한 정보를 포함 합니다.

API의 소스 이미지 빌드를 정의 하는 'SourceType' 필요, 현재 세 가지 유형이 있습니다.
- ISO-이 소스는 RHEL ISO 옵션을 사용 합니다.
- PlatformImage-원본 이미지는 Marketplace 이미지를 표시 합니다.
- ManagedImage-관리 되는 일반 이미지에서 시작 하는 경우이 설정을 사용 합니다.
- SharedImageVersion-소스로 공유 이미지 갤러리에서 이미지 버전을 사용할 때 사용 됩니다.

### <a name="iso-source"></a>ISO 원본

Azure 이미지 작성기는 게시 된 Red Hat Enterprise Linux 7.x 이진 DVD Iso, 미리 보기에 대 한 사용만 지원 합니다. 이미지 작성기를 지원합니다.
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

가져오려는 합니다 `sourceURI` 및 `sha256Checksum` 값으로 이동 `https://access.redhat.com/downloads` 제품 선택 합니다 **Red Hat Enterprise Linux**, 및 지원 되는 버전. 

목록의 **설치 관리자와 Red Hat Enterprise Linux Server에 대 한 이미지**, Red Hat Enterprise Linux 7.x 이진 DVD 및 체크섬에 대 한 링크를 복사 해야 합니다.

> [!NOTE]
> 빈번한 간격 링크의 액세스 토큰 새로 고침, 주소가 변경 된 있으므로 템플릿 제출 하려는 때마다는 RH 연결 되는 경우 확인 해야 합니다.
 
### <a name="platformimage-source"></a>PlatformImage 원본 
Azure 이미지 작성기는 다음 Azure Marketplace 이미지를 지원합니다.
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7.6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


속성은 실행 AZ CLI를 사용 하 여 VM을 만드는 데 사용 되는 동일 합니다 속성을 가져오려면 아래: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> 버전 '최신' 일 수 없습니다, 그리고 버전 번호를 가져오려면 위의 명령을 사용 해야 합니다. 

### <a name="managedimage-source"></a>ManagedImage 원본

원본 이미지는 일반화 된 VHD 또는 VM의 기존 관리 되는 이미지 형식으로 설정합니다. 원본 관리 되는 이미지는 지원 되는 OS의 수와 동일한 지역의 Azure 이미지 작성기 템플릿으로 되어야 합니다. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` 관리 되는 이미지의 ResourceId를 이어야 합니다. 사용 하 여 `az image list` 사용 가능한 이미지를 나열 합니다.


### <a name="sharedimageversion-source"></a>SharedImageVersion 원본
공유 이미지 갤러리에서 소스 이미지를 기존 이미지 버전을 설정합니다. 지원 되는 OS 이미지 버전 이어야 합니다 하 고 이미지를 Azure 이미지 작성기 템플릿에와 동일한 지역에 복제 해야 합니다. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` ResourceId는 이미지 버전 이어야 합니다. 사용 하 여 [az sig 이미지 버전 목록](/cli/azure/sig/image-version#az-sig-image-version-list) 목록 이미지 버전입니다.

## <a name="properties-customize"></a>속성: 사용자 지정


이미지 작성기는 여러 '지정자'를 지원합니다. 지정자는 스크립트를 실행 하거나 서버를 다시 부팅 하는 이미지를 사용자 지정 하는 데 사용 되는 함수입니다. 

사용 하는 경우 `customize`: 
- 여러 사용자 지정자를 사용할 수 있지만 고유한 있어야 `name`합니다.
- 커스터마이저가 템플릿에 지정 된 순서로 실행 합니다.
- 한 사용자 지정에 실패 하면 다음 전체 사용자 지정 구성 요소 실패 하 고 오류를 보고 합니다.
- 이미지 빌드를 요구 되며 이미지 작성기는 데 충분 한 시간을 허용 하도록 'buildTimeoutInMinutes' 속성을 조정 얼마나 많은 시간을 고려 합니다.
- 템플릿을 사용 하기 전에 철저 하 게 스크립트를 테스트 것이 좋습니다. VM에서 스크립트 디버깅 쉽게 됩니다.
- 스크립트에 중요 한 데이터를 배치 하지 않습니다. 
- 스크립트 위치를 사용 하는 경우에 공개적으로 액세스할 수 해야 [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)합니다.

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
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

 
사용자 지정 섹션에는 배열입니다. Azure 이미지 작성기는 순서 대로 사용자 지정자를 통해 실행 됩니다. 모든 사용자 지정에서 모든 오류는 빌드 프로세스를 실패 합니다. 
 
 
### <a name="shell-customizer"></a>셸 사용자 지정

셸 스크립트를 실행 중인 셸 사용자 지정 지원, 이어야 합니다 공개적으로 액세스 하는 데 IB에 액세스할 수 있습니다.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
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

OS 지원: Linux 
 
속성을 사용자 지정 합니다.

- **형식** 셸 – 
- **이름** -사용자 지정 추적에 대 한 이름 
- **scriptUri** -파일의 위치에 대 한 URI 
- **인라인** -배열 쉼표로 구분 하 여 셸 명령입니다.
 
> [!NOTE]
> RHEL ISO 소스를 사용 하 여 셸을 사용자 지정을 실행할 때 발생 하는 모든 사용자 지정 하기 전에 Red Hat 자격 서버 등록 첫 번째 사용자 지정 셸 핸들을 확인 해야 합니다. 사용자 지정 완료 되 면 자격 서버를 사용 하 여 스크립트를 등록 취소 해야 합니다.

### <a name="windows-restart-customizer"></a>Windows는 사용자 지정을 다시 시작 
다시 시작 사용자 지정을 사용 하면 Windows VM을 다시 시작을 다시 온라인 상태가 되 고 대기, 다시 부팅 해야 하는 소프트웨어를 설치할 수 있습니다.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

OS 지원: Windows
 
속성을 사용자 지정 합니다.
- **유형**: WindowsRestart
- **restartCommand** -(선택 사항) 다시 시작을 실행할 명령입니다. 기본값은 `'shutdown /r /f /t 0 /c \"packer restart\"'`입니다.
- **restartCheckCommand** – (선택 사항) 다시 시작 성공 여부를 확인 하는 명령입니다. 
- **restartTimeout** -크기 및 단위를 문자열로 지정 된 제한 시간을 다시 시작 합니다. 예를 들어 `5m` (5 분) 또는 `2h` (2 시간). 기본값은: ' 5m '


### <a name="powershell-customizer"></a>PowerShell 사용자 지정 
PowerShell 스크립트를 실행 하는 인라인 명령 셸 사용자 지정 지원, 스크립트를 액세스 하는 데 IB에 공개적으로 액세스할 수 여야 합니다.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

OS 지원: Windows 및 Linux

속성을 사용자 지정 합니다.

- **형식** – PowerShell.
- **scriptUri** -URI의 PowerShell 스크립트 파일의 위치입니다. 
- **인라인** – 쉼표로 구분 된 인라인 명령을 실행할 수 있습니다.
- **valid_exit_codes** – 선택 사항, / 인라인으로 스크립트에서 반환 될 수 있는 유효한 코드 명령, 스크립트/인라인 명령의 보고 된 오류를 피해 야 합니다.

### <a name="file-customizer"></a>사용자 지정 파일

파일 사용자 지정 이미지 작성기를 GitHub 또는 Azure storage에서 파일을 다운로드할 수 있습니다. 빌드 아티팩트에 의존 하는 이미지 빌드 파이프라인에 있는 경우 다음 빌드 공유에서 다운로드 하도록 파일 사용자 지정 설정를 이미지에 아티팩트를 이동 합니다.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

OS 지원: Linux 및 Windows 

파일 사용자 지정 속성:

- **sourceUri** -액세스할 수 있는 저장소 끝점에 GitHub 또는 Azure storage 수 있습니다. 하나의 파일을 전체 디렉터리 없습니다만 다운로드할 수 있습니다. 디렉터리를 다운로드 해야 할 경우 압축된 파일을 사용 하 여 다음 셸 또는 PowerShell 사용자 지정자를 사용 하 여 압축을 해제 합니다. 
- **대상** – 전체 대상 경로 및 파일 이름입니다. 참조 된 모든 경로 하위 디렉터리 존재, 셸 또는 PowerShell 사용자 지정자를 사용 하 여이를 미리 설정 해야 합니다. 경로 만들려면 스크립트 사용자 지정자를 사용할 수 있습니다. 

이 Windows 디렉터리를 Linux 경로 지원 하지만 일부의 차이점이 있습니다. 
- Linux OS의 – 경로만 이미지 작성기에 쓸 수는 /tmp입니다.
- Windows – 경로 제한이 있지만 경로가 존재 해야 합니다.
 
 
오류가 발생 하는 경우 파일을 다운로드 하거나 지정된 된 디렉터리에 배치 하려고 사용자 지정 단계를 실패 하 고는 customization.log이 됩니다.

Azure Storage에서 파일 사용자 지정에서 파일을 다운로드할 수 있습니다 사용 하 여 [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)합니다.

### <a name="generalize"></a>일반화 
Azure 이미지 작성기는 기본적으로 ' 일반화 ' 이미지 각 이미지 사용자 지정 단계 끝에 '프로 비전 해제' 코드를도 실행 됩니다. 일반화 하는 것은 여러 Vm을 만들려면 다시 사용할 수 있도록 이미지를 설정 하는 위치는 프로세스입니다. Windows Vm에 대 한 Azure 이미지 작성기는 Sysprep을 사용합니다. Azure 이미지 작성기가 linux의 경우 다음을 실행 합니다. ' waagent-deprovision'. 

일반화 하는 명령은 이미지 작성기 사용자가 Azure 이미지 작성기를 사용 하면 필요한 경우이 명령은 사용자가 지정할 수 있도록 모든 상황에 적합 한 수 없습니다. 

기존 사용자 지정, 마이그레이션하려는 경우 다른 Sysprep/waagent 명령을 사용 하는 이미지 작성기 일반 명령을 사용 하 여를 VM 만들기에 실패 하면 사용자 고유의 Sysprep 또는 waagent 명령을 사용 합니다.

Windows Server Sysprep 설명서를 검토 하거나 사용 하 여 지원 요청을 발생 해야 Azure 이미지 작성기에서 Windows 사용자 지정 이미지를 성공적으로 만든 후 VM 만드는 실패 하거나 성공적으로 완료 되지 않으면 찾습니다에서 VM을 만들 경우는 Windows Server Sysprep 고객 서비스 지원 팀에 문제를 해결 하 고 올바른 Sysprep 사용에 advise 할 수 있습니다.


#### <a name="default-sysprep-command"></a>Sysprep 명령을 기본합니다
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>기본 Linux 프로 비전 해제 명령

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>명령 재정의
명령을 재정의 하려면 정확한 파일 이름을 사용 하 여 명령 파일을 만들려면는 PowerShell 또는 셸 스크립트 프로 비 저 너의 사용 하 고 올바른 디렉터리에 넣습니다.

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

이미지 작성기는이 명령은 읽기 이러한에 기록 되는 AIB 로그 'customization.log'. 참조 [문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) 로그를 수집 하는 방법에 있습니다.
 
## <a name="properties-distribute"></a>속성: 배포

Azure 이미지 작성기는 세 가지 배포 대상을 지원합니다. 

- **managedImage** -관리 되는 이미지입니다.
- **sharedImage** -이미지 갤러리 공유 합니다.
- **VHD** -저장소 계정에 VHD.

배포 이미지를 모두 동일한 구성의 대상 형식을 참조 하세요 [예제](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)합니다.

이미지 작성기 쿼리를 통해 액세스할 수 있는 모든 배포 대상에 대 한 상태를 유지 관리 하는 둘 이상의 대상에 배포할를 가질 수 있으므로 `runOutputName`합니다.  `runOutputName` 는 해당 배포에 대 한 정보에 대 한 배포를 게시 하는 개체를 쿼리할 수 있습니다. 예를 들어, VHD 또는 이미지 버전을 복제 된 있는 지역 위치를 쿼리할 수 있습니다. 모든 배포 대상의 속성입니다. `runOutputName` 각 배포 대상으로 고유 해야 합니다.
 
### <a name="distribute-managedimage"></a>배포: managedImage

이미지 출력 관리 이미지 리소스 됩니다.

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
 
속성을 배포 합니다.
- **형식** – managedImage 
- **imageId** – 대상 이미지의 리소스 ID 예상 형식: /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **위치** -관리 되는 이미지의 위치입니다.  
- **runOutputName** – 고유한 배포를 식별 하는 것에 대 한 이름입니다.  
- **artifactTags** -선택적 사용자 지정 키 값 쌍의 태그입니다.
 
 
> [!NOTE]
> 대상 리소스 그룹이 있어야 합니다.
> 다른 지역에 배포 이미지를 하려는 경우 배포 시간을 증가 합니다. 

### <a name="distribute-sharedimage"></a>배포: sharedImage 
Azure 공유 이미지 갤러리는 이미지 지역 복제를 관리할 수 있는 새로운 이미지 관리 서비스 버전 관리 및 사용자 지정 이미지를 공유 합니다. Image Builder azure 공유 이미지 갤러리에서 지 원하는 지역에 이미지를 배포할 수 있도록이 서비스를 사용 하 여 배포를 지원 합니다. 
 
공유 이미지 갤러리의 구성 됩니다. 
 
- 갤러리-여러 공유 이미지에 대 한 컨테이너입니다. 갤러리는 한 지역에 배포 됩니다.
- 이미지에 대 한 개념적인 그룹 이미지 정의 합니다. 
- 이미지 버전의 경우 VM 또는 확장 집합을 배포 하는 데는 이미지 형식입니다. 이미지 버전 Vm 배포 해야 하는 다른 지역으로 복제할 수 있습니다.
 
갤러리를 만들고 이미지 정의 참조 해야 이미지 갤러리에 배포 하기 전에 [이미지를 공유](shared-images.md)합니다. 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

공유 이미지 갤러리에 대 한 속성을 배포 합니다.

- **type** - sharedImage  
- **galleryImageId** – 공유 이미지 갤러리의 ID입니다. 형식은: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>합니다.
- **runOutputName** – 고유한 배포를 식별 하는 것에 대 한 이름입니다.  
- **artifactTags** -선택적 사용자 지정 키 값 쌍의 태그입니다.
- **replicationRegions** -배열 복제를 위한 지역입니다. 지역 중 하나는 갤러리를 배포할 지역 이어야 합니다.
 
> [!NOTE]
> Azure 이미지 작성기를 사용 하 여 갤러리를 다른 지역에 있지만 Azure 이미지 작성기 서비스는 데이터 센터 간에 이미지를 전송 해야 합니다. 및이 더 오래 걸립니다. 이미지 작성기가 자동으로 버전 이미지를 기반으로 하는 단조 정수, 현재 지정할 수 없습니다. 

### <a name="distribute-vhd"></a>배포: VHD   
VHD에 출력할 수 있습니다. 그런 다음 VHD를 복사 하 고 Azure MarketPlace에 게시 하거나 Azure Stack을 사용 하는 데 사용할 수 있습니다.  

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
 
OS 지원: Windows 및 Linux

VHD 매개 변수를 배포 합니다.

- **type** - VHD.
- **runOutputName** – 고유한 배포를 식별 하는 것에 대 한 이름입니다.  
- **태그** -선택적 사용자 지정 키 값 쌍의 태그입니다.
 
Azure 이미지 작성기는 저장소 계정 위치를 지정 하는 사용자를 허용 하지 않지만의 상태를 쿼리할 수는 `runOutputs` 위치를 가져옵니다.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> VHD를 만든 후 복사해 다른 위치에 가능한 한 빨리 합니다. VHD는 이미지 템플릿을 Azure 이미지 작성기 서비스에 제출 되 면 생성 된 임시 리소스 그룹에서 저장소 계정에 저장 됩니다. 이미지 서식 파일을 삭제 하면 VHD 손실 됩니다. 
 
## <a name="next-steps"></a>다음 단계

샘플.json 파일이 다른 시나리오에 [Azure 이미지 작성기 GitHub](https://github.com/danielsollondon/azvmimagebuilder)합니다.
 
 
 
 
 
 
 
 
 
 
