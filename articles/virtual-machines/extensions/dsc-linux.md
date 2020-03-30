---
title: 리눅스에 대 한 Azure DSC 확장
description: Desired State Configuration을 사용하여 Azure Linux VM을 구성할 수 있도록 OMI 및 DSC 패키지를 설치합니다.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 2f04b5ecb2019a77bbb38e97c3869cc0a9447955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250622"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>리눅스에 대한 DSC 확장 (마이크로 소프트.OSTCExtensions.DSCForLinux)

원하는 상태 구성(DSC)은 코드로 구성하여 IT 및 개발 인프라를 관리하는 데 사용할 수 있는 관리 플랫폼입니다.

> [!NOTE]
> Linux용 DSC 확장 및 [Linux용 Azure Monitor 가상 머신 확장은](/azure/virtual-machines/extensions/oms-linux) 현재 충돌을 발생시키고 나란히 구성으로 지원되지 않습니다. 동일한 VM에서 두 솔루션을 함께 사용하지 마십시오.

DSCForLinux 확장은 게시 및 마이크로 소프트에 의해 지원됩니다. 확장은 OMI 및 DSC 에이전트를 Azure 가상 머신에 설치합니다. DSC 확장은 다음과 같은 작업을 수행할 수도 있습니다.


- Linux VM을 Azure 자동화 계정에 등록하여 Azure 자동화 서비스(확장 등록 작업)에서 구성을 가져옵니다.
- MOF 구성을 Linux VM으로 푸시합니다(푸시 확장작업).
- 리눅스 VM에 메타 MOF 구성을 적용하여 노드 구성을 끌어오기 위해 끌어오기 서버를 구성합니다(끌어오기 확장 작업).
- 리눅스 VM에 사용자 정의 DSC 모듈을 설치 (확장 작업을 설치).
- 리눅스 VM에서 사용자 지정 DSC 모듈을 제거 (확장 제거작업).

 

## <a name="prerequisites"></a>사전 요구 사항

### <a name="operating-system"></a>운영 체제

DSC Linux 확장은 다음을 제외한 모든 [Azure 보증 Linux 배포판](/azure/virtual-machines/linux/endorsed-distros)을 지원합니다.

| 배포 | 버전 |
|---|---|
| Debian | 모든 버전 |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>인터넷 연결

DSCForLinux 확장프로그램은 대상 가상 머신을 인터넷에 연결해야 합니다. 예를 들어 레지스터 확장은 자동화 서비스에 대한 연결이 필요합니다. 끌어오기, 끌어오기, 설치와 같은 다른 작업의 경우 Azure 저장소 및 GitHub에 대한 연결이 필요합니다. 고객이 제공하는 설정에 따라 다릅니다.

## <a name="extension-schema"></a>확장 스키마

### <a name="public-configuration"></a>공용 구성

지원되는 모든 공용 구성 매개 변수는 다음과 같습니다.

* `FileUri`: (선택 사항, 문자열) MOF 파일, 메타 MOF 파일 또는 사용자 지정 리소스 zip 파일의 uri입니다.
* `ResourceName`: (선택 사항, 문자열) 사용자 지정 리소스 모듈의 이름입니다.
* `ExtensionAction`: (선택 사항, 문자열) 확장에서 수행할 작업을 지정합니다. 유효한 값은 레지스터, 푸시, 당기기, 설치 및 제거입니다. 지정하지 않으면 기본적으로 푸시 작업으로 간주됩니다.
* `NodeConfigurationName`: (선택 사항, 문자열) 적용할 노드 구성의 이름입니다.
* `RefreshFrequencyMins`: (선택 사항, int) DSC가 끌어오기 서버에서 구성을 얻으려고 시도하는 빈도(분)를 지정합니다. 
       끌어오기 서버의 구성이 대상 노드의 현재 구성과 다른 경우 보류 중인 저장소로 복사되어 적용됩니다.
* `ConfigurationMode`: (선택 사항, 문자열) DSC에서 구성을 적용해야 하는 방법을 지정합니다. 유효한 값은 ApplyOnly, ApplyAndMonitor 및 ApplyAndAutoCorrect입니다.
* `ConfigurationModeFrequencyMins`: (선택 사항, 정수) DSC에서 구성이 원하는 상태에 있는지 확인하는 빈도(분)를 지정합니다.

> [!NOTE]
> 2.3 이전 버전을 사용하는 경우 모드 매개 변수는 ExtensionAction과 동일합니다. 모드는 오버로드 된 용어인 것 같습니다. 혼동을 피하기 위해 확장 작업은 버전 2.3 이후부터 사용됩니다. 이전 버전과의 호환성을 위해 확장은 mode와 ExtensionAction을 모두 지원합니다. 
>

### <a name="protected-configuration"></a>보호된 구성

지원되는 모든 보호된 구성 매개 변수는 다음과 같습니다.

* `StorageAccountName`: (선택 사항, 문자열) 파일이 포함된 저장소 계정의 이름
* `StorageAccountKey`: (선택 사항, 문자열) 파일이 포함된 저장소 계정의 키
* `RegistrationUrl`: (선택 사항, 문자열) Azure 자동화 계정의 URL
* `RegistrationKey`: (선택 사항, 문자열) Azure 자동화 계정의 액세스 키


## <a name="scenarios"></a>시나리오

### <a name="register-an-azure-automation-account"></a>Azure 자동화 계정 등록
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

파워쉘 포맷
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>VM에 MOF 구성 파일(Azure 저장소 계정)을 적용합니다.

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

파워쉘 포맷
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>VM에 MOF 구성 파일(공용 저장소)을 적용합니다.

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

파워쉘 포맷
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>VM에 메타 MOF 구성 파일(Azure 저장소 계정)을 적용합니다.

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

파워쉘 포맷
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>VM에 메타 MOF 구성 파일(공용 스토리지에 있음) 적용
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
파워쉘 포맷
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>VM에 사용자 지정 리소스 모듈(Azure 저장소 계정의 zip 파일)을 설치합니다.
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

파워쉘 포맷
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>VM에 사용자 지정 리소스 모듈(공용 저장소의 zip 파일) 설치
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
파워쉘 포맷
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>VM에서 사용자 지정 리소스 모듈 제거
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
파워쉘 포맷
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 템플릿은 Azure Automation에 온보딩하는 등 배포 후 구성이 필요한 하나 이상의 가상 컴퓨터를 배포할 때 이상적입니다. 

Resource Manager 템플릿 샘플은 [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) 및 [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu)입니다.

Azure 리소스 관리자 템플릿에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성을](../../azure-resource-manager/templates/template-syntax.md)참조하십시오.


## <a name="azure-cli-deployment"></a>Azure CLI 배포

### <a name="use-azure-cliazure-cli"></a>[Azure CLI][azure-cli] 사용
DSCForLinux 확장을 배포하기 전에 `public.json` 섹션 `protected.json` 3의 다양한 시나리오에 따라 구성하십시오.

#### <a name="classic"></a>클래식

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

클래식 배포 모드를 Azure 서비스 관리 모드라고도 합니다. 다음을 실행하여 클래식 모드로 전환할 수 있습니다.
```
$ azure config mode asm
```

다음을 실행하여 DSCForLinux 확장을 배포할 수 있습니다.
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

사용 가능한 최신 확장 버전을 확인하려면 다음을 실행합니다.
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>리소스 관리자
다음을 실행하여 Azure Resource Manager 모드로 전환할 수 있습니다.
```
$ azure config mode arm
```

다음을 실행하여 DSCForLinux 확장을 배포할 수 있습니다.
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> Azure 리소스 관리자 `azure vm extension list` 모드에서는 현재 사용할 수 없습니다.
>

### <a name="use-azure-powershellazure-powershell"></a>[Azure PowerShell][Azure-전원 셸] 사용

#### <a name="classic"></a>클래식

다음을 실행하여 Azure 서비스 관리 모드에서 Azure 계정에 로그인할 수 있습니다.

```powershell>
Add-AzureAccount
```

그리고 실행하여 DSCForLinux 확장을 배포 :

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

이전 섹션의 여러 시나리오에 따라 $privateConfig 및 $publicConfig 내용을 변경합니다.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>리소스 관리자

다음을 실행하여 Azure 리소스 관리자 모드에서 Azure 계정에 로그인할 수 있습니다.

```powershell>
Login-AzAccount
```

Azure 리소스 관리자에서 Azure PowerShell을 사용하는 방법에 대해 자세히 알아보려면 [Azure PowerShell을 사용하여 Azure 리소스 관리를](../../azure-resource-manager/management/manage-resources-powershell.md)참조하세요.

다음을 실행하여 DSCForLinux 확장을 배포할 수 있습니다.

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

이전 섹션의 여러 시나리오에 따라 $privateConfig 및 $publicConfig 내용을 변경합니다.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure 포털에서 Azure CLI를 사용하여 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

확장 실행 출력은 다음 파일에 기록됩니다.

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

오류 코드: 51은 지원되지 않는 배포 또는 지원되지 않는 확장 작업을 나타냅니다.
경우에 따라, DSC 리눅스 확장 오미의 높은 버전이 컴퓨터에 이미 있을 때 OMI를 설치 하지 못합니다. [오류 응답: (000003) 다운그레이드가 허용되지 않음]



### <a name="support"></a>고객 지원팀

이 문서의 어느 시점에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼의 Azure 전문가에게 문의하십시오.](https://azure.microsoft.com/support/community/) 또는 Azure 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/)이동하여 **지원 받기를**선택합니다. Azure 지원 사용에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ를](https://azure.microsoft.com/support/faq/)참조하십시오.

## <a name="next-steps"></a>다음 단계
확장에 대한 자세한 내용은 [Linux용 가상 머신 확장 및 기능](features-linux.md)을 참조하세요.
