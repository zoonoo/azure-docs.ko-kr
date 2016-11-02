<properties
 pageTitle="HPC 팩 클러스터 계산 노드 관리 | Microsoft Azure"
 description="Azure에서 HPC 팩 클러스터 계산 노드를 추가, 제거, 시작, 중지하는 PowerShell 스크립트 도구에 대해 알아보세요."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# Azure의 HPC 팩 클러스터에 있는 계산 노드의 수 및 가용성 관리

Azure VM에 HPC 팩 클러스터를 만든 경우 클러스터에서 여러 계산 노드 VM을 손쉽게 추가, 제거, 시작(프로비전), 중지(프로비전 해제)할 수 있어야 합니다. 이러한 작업을 하려면 헤드 노드 VM에 설치된 Azure PowerShell 스크립트를 실행합니다. 이러한 스크립트로 HPC 팩 클러스터 리소스의 수와 가용성을 관리하여 비용을 관리할 수 있습니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## 필수 조건

* **Azure VM의 HPC Pack 클러스터** - HPC Pack 2012 R2 업데이트 1 이상을 사용하여 클래식 배포 모델로 HPC Pack 클러스터를 만듭니다. 예를 들어 Azure 마켓플레이스 및 Azure PowerShell 스크립트에서 현재 HPC Pack VM 이미지를 사용하여 배포를 자동화할 수 있습니다. 자세한 내용 및 필수 구성 요소는 [HPC 팩 IaaS 배포 스크립트를 사용하여 HPC 클러스터 만들기](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)를 참조하세요.

    배포 후 헤드 노드의 %CCP\_HOME%bin 폴더에서 노드 관리 스크립트를 찾습니다. 각 스크립트는 관리자 권한으로 실행해야 합니다.

* **Azure 게시 설정 파일 또는 관리 인증서** - 헤드 노드에서 다음 중 하나를 수행해야 합니다.

    * **Azure 게시 설정 파일을 가져옵니다**. 이렇게 하려면 헤드 노드에서 다음 Azure PowerShell cmdlet을 실행합니다.

    ```
    Get-AzurePublishSettingsFile

    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```

    * **헤드 노드에서 Azure 관리 인증서를 구성합니다**. .cer 파일이 있는 경우 CurrentUser\\My certificate 저장소로 가져온 다음 Azure 환경(AzureCloud 또는 AzureChinaCloud)에 대해 다음 Azure PowerShell cmdlet을 실행합니다.

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## 계산 노드 VM 추가

**Add-HpcIaaSNode.ps1** 스크립트를 사용하여 계산 노드를 추가합니다.

### 구문
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### 매개 변수

* **ServiceName** - 새 계산 노드 VM이 추가될 클라우드 서비스의 이름입니다.

* **ImageName** - Azure VM 이미지 이름으로, Azure 클래식 포털 또는 Azure PowerShell cmdlet **Get-AzureVMImage**를 통해 가져올 수 있습니다. 이 이미지는 다음 요구 사항을 충족해야 합니다.

    1. Windows 운영 체제를 설치해야 합니다.

    2. HPC 팩을 계산 노드 역할로 설치해야 합니다.

    3. 이 이미지는 공용 Azure VM 이미지가 아닌 사용자 범주의 개인 이미지여야 합니다.

* **수량** - 추가할 계산 노드 VM의 수입니다.

* **InstanceSize** - 계산 노드 VM의 크기입니다.

* **DomainUserName** - 도메인 사용자 이름으로 새 VM을 도메인에 연결하는 데 사용합니다.

* **DomainUserPassword** - 도메인 사용자의 암호입니다.

* **NodeNameSeries**(선택 사항) - 계산 노드의 명명 패턴입니다. 형식은 &lt;*Root\_Name*&gt;&lt;*Start\_Number*&gt;%여야 합니다. 예를 들어 MyCN%10%은 MyCN11부터 시작하는 계산 노드 이름의 시리즈를 의미합니다. 이 매개 변수를 지정하지 않을 경우 스크립트는 HPC 클러스터에서 구성된 노드 명명 시리즈를 사용합니다.

### 예

다음 예제는 VM 이미지인 *hpccnimage1*을 기준으로 클라우드 서비스 *hpcservice1*에 20개의 대형 크기 컴퓨터 노드 VM을 추가합니다.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## 계산 노드 VM 제거

**Remove-HpcIaaSNode.ps1** 스크립트를 사용하여 계산 노드를 제거합니다.

### 구문

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### 매개 변수

* **Name** - 제거할 클러스터 노드의 이름. 와일드카드가 지원됩니다. 매개 변수 설정 이름은 Name입니다. **Name** 및 **Node** 매개 변수를 모두 지정할 수 없습니다.

* **Node** - 제거할 노드의 HpcNode 개체로 HPC PowerShell cmdlet [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx)를 통해 얻을 수 있습니다. 매개 변수 설정 이름은 Node입니다. **Name** 및 **Node** 매개 변수를 모두 지정할 수 없습니다.

* **DeleteVHD**(선택 사항) - 제거된 VM의 관련 디스크를 삭제하는 설정입니다.

* **강제**(선택 사항) - HPC 노드를 제거하기 전에 HPC 노드를 오프라인으로 강제 전환하는 설정입니다.

* **확인**(선택 사항) - 명령을 실행하기 전 확인 메시지를 표시합니다.

* **WhatIf** - 실제로 명령을 실행하지 않고 명령을 실행할 경우에 상황에 대해 설명하는 설정입니다.

### 예

다음 예제는 이름이 *HPCNode-CN-*으로 시작하는 노드를 오프라인으로 강제 전환한 다음 노드와 관련 디스크를 제거합니다.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## 계산 노드 VM 시작

**Start-HpcIaaSNode.ps1** 스크립트를 사용하여 계산 노드를 시작합니다.

### 구문

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### 매개 변수

* **Name** - 시작할 클러스터 노드의 이름 와일드카드가 지원됩니다. 매개 변수 설정 이름은 Name입니다. **Name** 및 **Node** 매개 변수를 모두 지정할 수 없습니다.

* **Node** - 시작할 노드의 HpcNode 개체로 HPC PowerShell cmdlet [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx)를 통해 얻을 수 있습니다. 매개 변수 설정 이름은 Node입니다. **Name** 및 **Node** 매개 변수를 모두 지정할 수 없습니다.

### 예

다음 예제는 이름이 *HPCNode-CN-*로 시작하는 노드를 시작합니다.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## 계산 노드 VM 중지

**Stop-HpcIaaSNode.ps1** 스크립트를 사용하여 컴퓨터 노드를 중지합니다.

### 구문

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### 매개 변수


* **Name** - 중지할 클러스터 노드의 이름 와일드카드가 지원됩니다. 매개 변수 설정 이름은 Name입니다. **Name** 및 **Node** 매개 변수를 모두 지정할 수 없습니다.

* **Node** - 중지할 노드의 HpcNode 개체로 HPC PowerShell cmdlet [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx)를 통해 얻을 수 있습니다. 매개 변수 설정 이름은 Node입니다. **Name** 및 **Node** 매개 변수를 모두 지정할 수 없습니다.

* **Force**(선택 사항) - HPC 노드를 중지하기 전에 HPC 노드를 오프라인으로 강제 전환하는 설정입니다.

### 예

다음 예제는 이름이 *HPCNode-CN-*로 시작하는 노드를 오프라인으로 강제 전환한 다음 노드를 중지합니다.

Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force

## 다음 단계

* 클러스터 노드를 현재 클러스터 작업의 워크로드에 따라 자동으로 증가 또는 축소하려는 경우 [클러스터 워크로드에 따라 Azure에서 HPC Pack 클러스터를 자동으로 증가 및 축소](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)를 참조하세요.

<!---HONumber=AcomDC_0727_2016-->