<properties
   pageTitle="Azure 백업- 암호화된 디스크로 Azure IaaS VM 백업 | Microsoft Azure"
   description="Azure 백업이 IaaS VM 백업 과정에서 BitLocker 또는 dmcrypt을 사용하여 암호화된 데이터를 처리하는 방법을 알아봅니다. 이 문서는 암호화된 데이터를 다루는 경우 백업 및 복원 환경의 차이에 대비할 수 있도록 합니다."
   services="backup"
   documentationCenter=""
   authors="pallavijoshi"
   manager="vijayts"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="07/01/2016"
   ms.author="markgal; jimpark; trinadhk"/>

# VM을 백업하는 동안 암호화된 디스크 처리

Azure의 VM 데이터를 암호화하려는 엔터프라이즈가 사용할만한 솔루션은 Windows의 Bitlocker 또는 Linux 컴퓨터의 dmcrypt입니다. 두 가지 모두 볼륨 수준 암호화 솔루션입니다. 이 문서에서는 Azure VM에 대한 백업 설정의 구체적인 정보와 암호화된 데이터가 복원 워크플로에 미치는 영향을 다룹니다.

## 백업 작동 원리

전반적인 솔루션은 VM 계층과 저장소 계층이라는 두 가지 계층으로 구성됩니다.

1. VM 계층은 게스트 운영 체제에 표시되는 데이터와 가상 컴퓨터에서 실행되는 응용 프로그램을 처리합니다. 데이터를 디스크에 쓰기 전에 볼륨의 데이터를 투명하게 암호화하는 암호화 소프트웨어(Bitlocker 또는 dmcrypt)를 실행하는 계층이기도 합니다.
2. 저장소 계층은 VM에 연결된 디스크 및 페이지 Blob을 처리합니다. 디스크에 기록되는 데이터에 대한 정보 및 데이터의 암호화 여부에 대한 정보를 포함하지 않습니다. 이 계층에서 VM 백업 기능이 작동합니다.

![Bitlocker 암호화 및 Azure VM 백업이 공존하는 원리](./media/backup-azure-vms-encryption/how-it-works.png)

데이터에 대한 전체 암호화는 VM 계층에서 투명하고 원활하게 일어납니다. 따라서 VM에 연결된 페이지 Blob에 기록되는 데이터는 암호화된 데이터입니다. [Azure 백업이 VM 디스크의 스냅숏을 만들고 데이터를 전송](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines)할 때, 페이지 Blob에 있는 암호화된 데이터를 복사합니다.

## 솔루션 구성 요소

이 솔루션에는 작업이 수행되도록 하기 위하여 올바르게 구성하고 관리해야 하는 부분이 많이 있습니다.

| 함수 | 사용되는 소프트웨어 | 추가적인 참고 사항 |
| -------- | ------------- | ------- |
| 암호화 | Bitlocker 또는 dmcrypt | 암호화는 Azure Backup에 비해 *다른* 계층에서 이루어지기 때문에 어떤 암호화 소프트웨어를 사용하든 상관이 없습니다. 그렇긴 하지만, 이 환경은 Bitlocker를 사용한 CloudLink 및 dmcrypt에 대해서만 유효성이 검사되었습니다.<br><br> 데이터를 암호화하려면 키가 필요합니다. 데이터에 권한이 있는 액세스만 가능하도록 하려면, 키 역시 안전하게 보관되어야 합니다. |
| 키 관리 | CloudLink SecureVM | 키는 데이터 암호화 또는 암호 해독에 필수적입니다. 올바른 키가 없으면 데이터를 복구할 수 없습니다. 키는 다음과 같은 상황에서 *매우* 중요합니다.<br><li>키 롤오버<li>장기간 보존<br><br>예를 들어, 7년 전 데이터 백업에 사용된 키가 현재 사용되는 키와 동일하지 않을 수 있습니다. 7년 전의 키가 없으면, 그 당시의 데이터를 복원하여 사용하는 것이 불가능합니다.|
| 데이터 백업 | Azure 백업 | Azure 백업을 사용하여 PowerShell을 사용하거나 [Azure 관리 포털](http://manage.windowsazure.com)을 사용하는 Azure IaaS VM을 백업합니다. |
| 데이터 복원 | Azure 백업 | Azure 백업을 사용하여 복구 지점에서 디스크 또는 전체 VM을 복원합니다. 데이터는 복원 작업의 일환으로 Azure 백업에서 암호가 해독되지 않습니다.|
| 암호 해독 | Bitlocker 또는 dmcrypt | 복원된 데이터 디스크 또는 복원된 VM으로부터 데이터를 읽어오려면, 소프트웨어에 키 관리 소프트웨어의 키가 필요합니다. 올바른 키가 없으면 데이터 암호를 해독할 수 없습니다. |

> [AZURE.IMPORTANT]  키 관리는(키 롤오버 포함) Azure 백업에 속하지 않습니다. 이러한 측면은 독립적으로 관리되어야 하지만 전반적인 백업/복원 작업에 매우 중요합니다.

### 지원되는 시나리오


| &nbsp; | 백업 저장소 | 복구 서비스 자격 증명 모음 |
| :-- | :-- | :-- |
| Azure IaaS V1 VM | 예 | 아니요 |
| Azure IaaS V2 VM | 해당 없음 | 아니요 |


## CloudLink SecureVM

[CloudLink SecureVM](http://www.cloudlinktech.com/choose-your-cloud/microsoft-azure/)은 Azure IaaS VM 데이터 보호에 사용될 수 있는 VM 암호화 솔루션입니다. CloudLink SecureVM은 Azure 백업과 사용하도록 지원됩니다.

### 지원 정보

- CloudLink SecureVM 버전 4.0(빌드 21536.121 이상)
- Azure PowerShell 버전 0.9.8 이상

### 키 관리

기존 백업을 포함하는 VM에 대한 키를 롤오버하거나 변경해야 하는 경우에는 백업 당시 사용된 키를 나중에 사용할 수 있도록 해야 합니다. keystore 또는 전체 SecureVM 시스템을 백업해 두는 것도 한 가지 방법입니다.

### 설명서 및 리소스

- [배포 가이드 - PDF](http://www.cloudlinktech.com/Azure/CL_SecureVM_4_0_DG_EMC_Azure_R2.pdf)
- [SecureVM 배포 및 사용 - 비디오](https://www.youtube.com/watch?v=8AIRe92UDNg)

<!---HONumber=AcomDC_0803_2016-->