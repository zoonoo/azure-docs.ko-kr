---
title: Linux용 Azure DSC 확장
description: Desired State Configuration을 사용하여 Azure Linux VM을 구성할 수 있도록 OMI 및 DSC 패키지를 설치합니다.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 67f72c5b396bc935f7bec34bc8a52f63131649b1
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904477"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Linux용 DSC 확장(Microsoft.OSTCExtensions.DSCForLinux)

DSC(Desired State Configuration)는 구성을 코드로 사용하여 IT 및 개발 인프라를 관리할 수 있게 하는 관리 플랫폼입니다.

DSCForLinux 확장은 Microsoft에서 게시하고 지원합니다. 확장은 OMI 및 DSC 에이전트를 Azure 가상 머신에 설치합니다. DSC 확장에서는 다음 작업도 수행할 수 있습니다.


- Linux VM을 Azure Automation 계정에 등록하여 Azure Automation 서비스에서 구성을 가져옵니다(ExtensionAction 등록).
- MOF 구성을 Linux VM에 푸시합니다(ExtensionAction 푸시).
- Meta MOF 구성을 Linux VM에 적용하여 노드 구성을 끌어오도록 끌어오기 서버를 구성합니다(ExtensionAction 끌어오기).
- Linux VM에 사용자 지정 DSC 모듈을 설치합니다(ExtensionAction 설치).
- Linux VM에서 사용자 지정 DSC 모듈을 제거합니다(ExtensionAction 제거).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>필수 조건

### <a name="operating-system"></a>운영 체제

DSC Linux 확장은 다음을 제외한 모든 [Azure 보증 Linux 배포판](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)을 지원합니다.

| 배포 | 버전 |
|---|---|
| Debian | 모든 버전 |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>인터넷 연결

DSCForLinux 확장을 사용하려면 대상 가상 머신이 인터넷에 연결되어 있어야 합니다. 예를 들어 등록 확장에는 Automation 서비스에 대한 연결이 필요합니다. Push, Pull, Install과 같은 다른 작업의 경우 Azure 스토리지/Github에 연결해야 합니다. 이러한 연결은 고객이 제공하는 설정에 따라 달라집니다.

## <a name="extension-schema"></a>확장 스키마

### <a name="11-public-configuration"></a>1.1 공용 구성

지원되는 모든 공용 구성 매개 변수는 다음과 같습니다.

* `FileUri`: (옵션, 문자열)는 MOF 파일/메타 MOF 파일/사용자 지정 리소스 ZIP 파일의 uri입니다.
* `ResourceName`: (옵션, 문자열) 사용자 지정 리소스 모듈의 이름
* `ExtensionAction`: (옵션, 문자열) 확장의 용도 지정 합니다. 유효한 값은 Register, Push, Pull, Install, Remove입니다. 지정하지 않으면 기본적으로 푸시 작업으로 간주됩니다.
* `NodeConfigurationName`: (옵션, 문자열) 적용 하는 노드 구성의 이름입니다.
* `RefreshFrequencyMins`: (선택 사항, int) 빈도 (분 단위로 지정) DSC 끌어오기 서버에서 구성을 가져오려고 시도 합니다. 
       끌어오기 서버의 구성이 대상 노드의 현재 구성과 다르면 보류 중인 저장소에 복사되고 적용됩니다.
* `ConfigurationMode`: (옵션, 문자열) DSC 구성을 적용 해야 하는 방법을 지정 합니다. 유효한 값은 다음과 같습니다. ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect입니다.
* `ConfigurationModeFrequencyMins`: (선택 사항, int) 빈도 (분 단위로 지정) DSC 원하는 상태로 구성 되었는지 확인 합니다.

> [!NOTE]
> 2.3 버전 미만을 사용하는 경우 mode 매개 변수는 ExtensionAction과 동일합니다. 모드는 오버 로드 된 용어 것 같습니다. 따라서 혼동을 방지하기 위해 ExtensionAction이 2.3 버전 이후부터 사용되고 있습니다. 이전 버전과의 호환성을 위해 확장은 mode와 ExtensionAction을 모두 지원합니다. 
>

### <a name="12-protected-configuration"></a>1.2 보호된 구성

지원되는 모든 보호된 구성 매개 변수는 다음과 같습니다.

* `StorageAccountName`: (옵션, 문자열) 파일이 포함 된 저장소 계정의 이름
* `StorageAccountKey`: (옵션, 문자열) 파일이 포함 된 저장소 계정 키
* `RegistrationUrl`: (옵션, 문자열)는 Azure Automation 계정의 URL
* `RegistrationKey`: (옵션, 문자열)는 Azure Automation 계정의 액세스 키


## <a name="scenarios"></a>시나리오

### <a name="register-to-azure-automation-account"></a>Azure Automation 계정에 등록
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

PowerShell 형식
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>VM에 MOF 구성 파일(Azure Storage 계정에 있음) 적용

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

PowerShell 형식
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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>VM에 MOF 구성 파일(공용 저장소에 있음) 적용

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

PowerShell 형식
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>VM에 메타 MOF 구성 파일(Azure Storage 계정에 있음) 적용

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

PowerShell 형식
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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>VM에 메타 MOF 구성 파일(공용 저장소에 있음) 적용
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
PowerShell 형식
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>VM에 사용자 지정 리소스 모듈(Azure Storage 계정에 있는 ZIP 파일) 설치
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

PowerShell 형식
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>VM에 사용자 지정 리소스 모듈(공용 저장소에 있는 ZIP 파일) 설치
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
PowerShell 형식
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
PowerShell 형식
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. Azure Automation에 등록하는 것처럼 배포 후 구성이 필요한 하나 이상의 가상 머신을 배포하는 경우 템플릿을 사용하는 것이 좋습니다. 

Resource Manager 템플릿 샘플은 [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) 및 [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu)입니다.

Azure Resource Manager 템플릿에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](../../azure-resource-manager/resource-group-authoring-templates.md)을 참조하세요.


## <a name="azure-cli-deployment"></a>Azure CLI 배포

### <a name="21-using-azure-cliazure-cli"></a>2.1. [**Azure CLI**][azure-cli] 사용
DSCForLinux 확장을 배포하기 전에 섹션 3의 다양한 시나리오에 따라 `public.json`과 `protected.json`을 구성해야 합니다.

#### <a name="211-classic"></a>2.1.1. 클래식
클래식 모드는 Azure 서비스 관리 모드라고도 합니다. 다음을 실행하여 클래식 모드로 전환할 수 있습니다.
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

#### <a name="212-resource-manager"></a>2.1.2. 리소스 관리자
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
> `azure vm extension list`는 현재 Azure Resource Manager 모드에서 사용할 수 없습니다.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. [**Azure PowerShell**][azure-powershell] 사용

#### <a name="221-classic"></a>2.2.1 클래식

다음을 실행하여 Azure 계정(Azure 서비스 관리 모드)에 로그인할 수 있습니다.

```powershell>
Add-AzureAccount
```

다음을 실행하여 DSCForLinux 확장을 배포합니다.

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

위의 섹션에서 다양한 시나리오에 따라 $privateConfig 및 $publicConfig의 내용을 변경해야 합니다 
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

#### <a name="222resource-manager"></a>2.2.2. Resource Manager

다음을 실행하여 Azure 계정(Azure Resource Manager 모드)에 로그인할 수 있습니다.

```powershell>
Login-AzAccount
```

Azure Resource Manager에서 Azure PowerShell을 사용하는 방법에 대한 자세한 내용은 [**여기**](../../azure-resource-manager/manage-resources-powershell.md)를 클릭하세요.

다음을 실행하여 DSCForLinux 확장을 배포할 수 있습니다.

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

위의 섹션에서 다양한 시나리오에 따라 $privateConfig 및 $publicConfig의 내용을 변경해야 합니다 
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

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

확장 실행 출력은 다음 파일에 기록됩니다.

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

오류 코드: 51은 지원되지 않는 배포판 또는 지원되지 않는 확장 작업을 나타냅니다.
OMI의 상위 버전이 이미 컴퓨터에 있으면 경우에 따라 DSC Linux 확장에서 OMI를 설치하지 못합니다. [오류 응답: (000003) 다운그레이드가 허용되지 않음]



### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="next-steps"></a>다음 단계
확장에 대한 자세한 내용은 [Linux용 가상 머신 확장 및 기능](features-linux.md)을 참조하세요.
