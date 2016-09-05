<properties
	pageTitle="PowerShell 스크립트를 사용하여 클래식 가상 컴퓨터를 Azure Resource Manager에 복제"
	description="이 문서는 PowerShell 스크립트를 사용하여 단일 클래식 가상 컴퓨터를 Azure Resource Manager에 복제하는 방법을 보여줍니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="singhkay"/>

# PowerShell 스크립트를 사용하여 클래식 가상 컴퓨터를 Azure Resource Manager에 복제

이 문서는 [Azure/classic-iaas-resourcemanager-migration](https://github.com/Azure/classic-iaas-resourcemanager-migration)에 있는 스크립트를 사용하여 **단일** 클래식 가상 컴퓨터를 Azure Resource Manager 배포 모델로 복제하는 방법을 보여줍니다.

>[AZURE.IMPORTANT]이 스크립트를 사용하여 복제하면 클래식 가상 컴퓨터에 가동 중지 시간이 발생합니다. 플랫폼 지원 마이그레이션을 찾는다면, 다음 페이지를 방문하세요.
- [클래식에서 Azure Resource Manager 스택으로 IaaS 리소스의 플랫폼 지원 마이그레이션](./virtual-machines-windows-migration-classic-resource-manager.md)
- [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Azure PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## 스크립트 가져오기

>[AZURE.NOTE]PowerShell 스크립트는 Microsoft 지원에서 공식적으로 지원하지 않습니다. 따라서 Github에 오픈 소스로 제공되며 수정 또는 추가적인 시나리오를 위한 PR을 기꺼이 받아들입니다.

위에 명시된 리포지토리에서 zip 파일을 다운로드하거나 아래 명령 중 **어느 쪽**이든 사용하여 리포지토리를 복제하면 스크립트를 가져올 수 있습니다.

```
git clone https://github.com/Azure/classic-iaas-resourcemanager-migration.git
```

**또는**

```
git clone git@github.com:Azure/classic-iaas-resourcemanager-migration.git
```

## 마이그레이션 모듈 가져오기

다음 단계는 모듈을 PowerShell 세션으로 가져오는 것입니다. 이 작업은 아래 명령을 사용하여 수행할 수 있습니다.

```
Import-Module .\asm2arm.psd1
```

## 클래식 배포 모델 및 Azure Resource Manager 구독을 사용하여 인증

클래식 가상 컴퓨터를 복제할 수 있으려면, 클래식 배포 모델 및 Azure Resource Manager 스택 모두에 대해 PowerShell 세션을 인증해야 합니다. 이 작업은 다음 cmdlet을 사용하여 수행할 수 있습니다.

```
Add-AzureAccount
Login-AzureRmAccount
```

>[AZURE.IMPORTANT]클래식 배포 모델에 `Select-AzureSubscription`을 사용하고 Azure Resource Manager에는 Set-AzureRmContext를 사용하여 기본 구독이 선택되도록 합니다.

## 스크립트 사용

### cmdlet

모듈을 가져오면 아래 두 가지 cmdlet이 세션에 추가됩니다.

```
Add-AzureSMVmToRM
New-AzureSmToRMDeployment
```

이 cmdlet은 아래에 지정된 기능을 수행합니다.

#### Add-AzureSMVmToRM
- 가상 컴퓨터에 제공되는, Azure Resource Manager 템플릿 및 명령적 PowerShell 스크립트(VM에 VM 에이전트 확장이 있는 경우 디스크 BLOB을 복사하기 위해)를 생성합니다.

>[AZURE.IMPORTANT]`-Deploy` 스위치를 지정하면, 이 cmdlet은 `New-AzureSmToRMDeployment` cmdlet을 호출하여 위에서 생성된 Azure Resource Manager 템플릿과 명령적 PowerShell 스크립트를 배포합니다.

#### New-AzureSmToRMDeployment
- `Add-AzureSMVmToRM` cmdlet에 의해 생성된 템플릿과 명령적 PowerShell 스크립트를 배포하여 마이그레이션을 시작합니다.

### 복제할 클래식 가상 컴퓨터 식별

이 작업은 클래식 가상 컴퓨터 상태를 변수에 저장하여 `Add-AzureSMVmToRM` cmdlet에 전달하거나, `ServiceName` 및 VM `Name` 매개변수를 직접 사용하여 수행할 수 있습니다. 클래식 가상 컴퓨터를 식별하고 나면, `Add-AzureSMVmToRM` cmdlet을 사용하여 복제할 수 있습니다.

```
$vm = Get-AzureVm -ServiceName acloudservice -Name atestvm
Add-AzureSMVmToRM -VM $vm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

**또는**

```
Add-AzureSMVmToRM -ServiceName acloudservice -Name atestvm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

>[AZURE.IMPORTANT]위의 예제에서 `-Deploy` 스위치를 사용하고 있기 때문에, `New-AzureSmToRMDeployment` cmdlet을 사용할 필요가 없습니다.

## 이러한 스크립트를 사용하면 마이그레이션이 어떻게 수행되나요?

cmdlet은 classVM을 복제하고, 저장소, 네트워크 및 계산 리소스 공급자에 대한 사용자 지정 PowerShell 해시 테이블로 리소스를 생성합니다. 리소스를 나타내는 이 해시 테이블은 배열에 추가되고, 나중에 JSON으로 직렬화하여 템플릿이 되고 파일에 기록됩니다.

템플릿은 클래식 가상 컴퓨터 에이전트 확장 및 DiskAction 옵션 값의 존재에 따라 파일을 만듭니다. 이 파일은 OutputFileFolder 매개 변수에 의해 지정된 디렉터리에 배치됩니다. 해당 파일은 다음과 같습니다.

1. `<ServiceName>-<VMName>-setup<optional timestamp>.json`: 이 파일은 클래식 가상 컴퓨터가 복제되기 전에 준비되어야 하는 리소스를 나타내며 잠재적으로 모든 후속 VM에 대해 동일합니다.(후속 실행 사이의 상태를 유지하지 않지만, BLOB 복사 작업이 발생하기 전에 저장소 계정이 프로비전되어야 하기 때문에(강제적으로 수행됨), 그룹과 같은 리소스를 하나로 그룹화하는 것이 타당합니다.)

2.  `<ServiceName>-<VMName>-deploy<optional timestamp>.json`: Resource Manager 가상 컴퓨터에 대한 템플릿을 포함합니다.
3.  `<ServiceName>-<VMName>-parameters<optional timestamp>.json`: 템플릿에 전달되는 실제 매개 변수를 포함합니다.
4.  `<ServiceName>-<VMName>-setextensions<optional timestamp>.json`: 가상 컴퓨터 에이전트 확장 설정을 위해 실행되는 PowerShell cmdlet 집합입니다.
4.  `<ServiceName>-<VMName>-copydisks<optional timestamp>.json`: CopyDisks 옵션이 지정된 경우, 디스크 BLOB 복사를 위해 실행되는 PowerShell cmdlet 집합입니다.

-Deploy 플래그가 설정되면, 파일을 생성한 후에, cmdlet이 <ServiceName>-<VMName>-setup.json 템플릿을 배포하고, DiskAction 매개 변수가 CopyDisks로 설정되어 있으면 원본 가상 컴퓨터 디스크 Blob을 복사한 다음, 매개 변수에 대한 <ServiceName>-<VMName>-parameters.json 파일을 사용하여 <ServiceName>-<VMName>-deploy.json 템플릿을 배포합니다. 가상 컴퓨터 배포가 완료되고 나서, 명령적 스크립트(가상 컴퓨터 에이전트 확장에 대해) 또는 디스크 복사를 위한 스크립트가 있으면, 실행됩니다.

### 네트워크 세부 정보
cmdlet은 클래식 네트워크 설정을 Resource Manager에 복제하기 위한 것이 아닙니다. 가상 컴퓨터 자체를 복제하기에 가장 편리한 방식으로 네트워킹 설비를 활용합니다. 다른 조건에서 발생하는 상황은 다음과 같습니다.

1.  대상 리소스 그룹에 가상 네트워크가 없는 경우
    - 원본 가상 컴퓨터가 서브넷에 없습니다. 10.0.0.0/16의 기본 가상 네트워크가 10.0.0.0/22 주소 공간으로 서브넷과 함께 생성됩니다.
    - 원본 가상 컴퓨터가 서브넷에 있습니다. 가상 컴퓨터가 있는 가상 네트워크가 발견되고 가상 네트워크의 사양이 서브넷과 함께 복사됩니다.
2.  대상 리소스 그룹에 `<VM virtual network>arm`이라는 이름의 가상 네트워크가 있습니다(‘arm’ 문자열이 추가됨).
    - 가상 네트워크에 이름과 주소 공간이 같은 서브넷이 있으면, 그것을 사용합니다.
    - 적합한 서브넷이 없으면, 22비트 마스크로 기존 서브넷 밖에서 주소 블록을 찾아서, 그것을 사용합니다.

## 복제 및 플랫폼 마이그레이션

현재 복제 방식과 플랫폼 지원 마이그레이션 사이에는 몇 가지 차이가 있습니다.

### 복제


| 장점 | 단점 |
|--------------------------------------------------------|:----------------------------------------------------------------:|
| 클라우드 서비스 내의 모든 가상 컴퓨터 복제 | VM을 종료해야 하기 때문에 가동 중지 시간 발생 |
| VNET 내부 또는 VNET 외부에 가상 컴퓨터 복제 | 디스크 BLOB 복사가 필요한 경우 시나리오가 오래 걸림 |
| | VM에 대한 네트워크 구성 변경(내부 IP, VIP) |


### 플랫폼 마이그레이션


| 장점 | 단점 |
|----------------------------------|:-------------------------------------------:|
| 가상 네트워크 내 대부분의 VM 구성에 가동 중지 시간이 발생하지 않습니다. | 모든 리소스가 배포되어 있는 VNET이나 클라우드 서비스의 모든 VM이 함께 마이그레이션되어야 합니다. |
 
 
## 지원되지 않는 시나리오

**다음은 복제 스크립트의 범위에 포함되지 않습니다.**

 1. 실행 중인 가상 컴퓨터 중지
 2. 데이터/디스크 변경
 3. 실행 중인 VM 복제
 4. 복잡한 시나리오에서 여러 VM을 힘들이지 않고 재빨리 복제
 5. 전체 ASM 네트워크 구성 복제
 6. 부하 분산된 VM 생성. 이 구성은 당연히 Azure 전문가가 처리해야 한다고 생각됩니다.
 
 
## 테스트를 거친 구성

_Add-AzureSMVmToRM_ cmdlet은 다음 테스트 사례를 사용하여 유효성이 검사되었습니다.

| # | 설명 |
|:---|:---|
| 1 | 기존 OS 디스크와 Windows 가상 컴퓨터 |
| 2 | 기존 OS 디스크와 Linux 가상 컴퓨터 |
| 3 | 기존 OS 및 데이터 디스크와 Windows 가상 컴퓨터 |
| 4 | 기존 OS 및 데이터 디스크와 Linux 가상 컴퓨터 |
| 5 | 이미지 갤러리와 일치하는 새 OS 디스크와 Windows 가상 컴퓨터 |
| 6 | 이미지 갤러리와 일치하는 새 OS 디스크와 Linux 가상 컴퓨터 |
| 7 | 새 OS 디스크 및 빈 데이터 디스크와 Windows 가상 컴퓨터 |
| 8 | 새 OS 디스크 및 빈 데이터 디스크와 Linux 가상 컴퓨터 |
| 9 | 공용 끝점과 Windows 가상 컴퓨터 |
| 10 | 공용 끝점과 Linux 가상 컴퓨터 |
| 11 | WinRM 인증서와 Windows 가상 컴퓨터 |
| 12 | Vnet 및 서브넷의 Windows 가상 컴퓨터 |
| 13 | Vnet 및 서브넷의 Linux 가상 컴퓨터 |
| 14 | 사용자 지정 확장이 있는 Windows 가상 컴퓨터 |
| 15 | 가용성 집합의 Windows 가상 컴퓨터 |
| 16 | Vnet과 서브넷에, 여러 데이터 디스크, 공용 끝점이 있고 사용자 지정 확장이 있는 가용성 집합의 Windows 가상 컴퓨터 |
| 17 | Vnet과 서브넷에, 여러 데이터 디스크, 공용 끝점이 있고 사용자 지정 확장이 있는 가용성 집합의 Linux 가상 컴퓨터 |

## 참고 사항
1. 여러 개의 VM이 짧은 간격으로 번갈아 복제되면, DNS 캐시 새로 고침 시간으로 인해 공용 IP 주소에 대해 DNS 이름 충돌이 발생할 수 있습니다.

<!---HONumber=AcomDC_0824_2016-->