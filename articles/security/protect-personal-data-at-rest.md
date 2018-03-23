---
title: 암호화를 사용하는 Azure Protect 미사용 개인 데이터 | Microsoft Docs
description: 이 문서는 GDPR(일반 데이터 보호 규정)과 같은 업계 및 정부 요구 사항을 준수하기 위해 사용될 수 있습니다.
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: ''
ms.openlocfilehash: 37da3d91df4be871d6647b6290768ea4999a5010
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-encryption-technologies-protect-personal-data-at-rest-with-encryption"></a>Azure 암호화 기술: 암호화로 미사용 개인 데이터 보호

이 문서는 Azure 암호화 기술을 이해하고 사용하여 미사용 데이터를 보호하는 데 도움이 됩니다.

중요하거나 개인적인 데이터를 보호하고 규정 준수 및 데이터 개인 정보 보호 요구 사항을 충족하려면 미사용 데이터를 암호화하여 보관하는 것이 가장 좋습니다. 미사용 데이터 암호화는 디스크에 있을 때 데이터를 암호화하여 공격자가 암호화되지 않은 데이터에 액세스하지 못하도록 설계되었습니다. 이 문서에 포함된 정보는 GDPR(일반 데이터 보호 규정)을 준수하기 위해 사용할 수 있습니다.

## <a name="scenario"></a>시나리오 

미국에 본사를 둔 대형 크루즈 회사는 영국 제도뿐만 아니라 지중해 및 발트해 연안의 여정도 제공하도록 사업을 확장하고 있습니다. 이러한 노력을 지원하기 위해 이탈리아, 독일, 덴마크 및 영국에 기반을 둔 몇 개의 소형 크루즈 라인을 인수했습니다.

회사에서 Microsoft Azure를 사용하여 클라우드에 회사 데이터를 저장합니다. 여기에는 다음과 같은 고객 및/또는 직원 정보가 포함될 수 있습니다.

- 주소
- 전화 번호
- 납세자 번호
- 신용 카드 정보

회사에서 고객 및 직원 데이터의 개인 정보를 보호해야 하지만, 필요로 하는 부서(예: 급여 및 예약 부서)에서 해당 데이터에 액세스할 수 있어야 합니다.

또한 크루즈 라인에서 현재 및 과거 고객과의 관계를 추적하기 위해 개인 정보가 포함된 보상 및 충성도 프로그램 구성원에 대한 대규모 데이터베이스를 유지하고 있습니다.

### <a name="problem-statement"></a>문제 설명

회사에서 고객 및 직원 개인 데이터의 개인 정보를 보호해야 하지만, 필요로 하는 부서(예: 급여 및 예약 부서)에서 해당 데이터에 액세스할 수 있어야 합니다. 이러한 개인 데이터는 회사에서 제어하는 데이터 센터 외부에 저장되며 회사의 물리적 통제하에 있지 않습니다.

### <a name="company-goal"></a>회사 목표

다계층 심층 방어 보안 전략의 일부로 클라우드 저장소에 있는 데이터를 포함하여 개인 데이터가 포함된 모든 데이터 원본을 암호화하는 것이 회사의 목표입니다. 권한 없는 사람이 개인 데이터에 액세스하는 경우 읽을 수 없는 형식으로 렌더링해야 합니다. 암호화를 적용하는 경우 사용자 또는 관리자에게 쉽고 투명해야 합니다.

## <a name="solutions"></a>솔루션

Azure 서비스는 미사용 개인 데이터를 암호화하여 보호할 수 있는 여러 도구와 기술을 제공합니다.

### <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)는 Azure 서비스에서 미사용 데이터를 암호화하는 데 사용되는 키에 안전한 저장소를 제공하며 권장되는 키 저장소 및 관리 솔루션입니다. 암호화 키 관리는 저장된 데이터를 보호하는 데 필수적입니다.

#### <a name="how-do-i-use-azure-key-vault-to-protect-keys-that-encrypt-personal-data"></a>Azure Key Vault를 사용하여 개인 데이터를 암호화하는 키를 보호하려면 어떻게 할까요?

Azure Key Vault를 사용하려면 Azure 계정에 대한 구독이 필요합니다. Azure PowerShell도 설치해야 합니다. PowerShell cmdlet을 사용하여 다음을 수행하는 단계가 포함됩니다.

1. 구독에 연결

2. 키 자격 증명 모음 만들기

3. 키 또는 암호를 키 자격 증명 모음에 추가

4. 키 자격 증명 모음을 사용할 응용 프로그램을 Azure Active Directory에 등록

5. 키 또는 비밀을 사용하여 응용 프로그램에 권한 부여

키 자격 증명 모음을 만들려면 New-AzureRmKeyVault PowerShell cmdlet을 사용합니다. 자격 증명 모음 이름, 리소스 그룹 이름 및 지리적 위치를 할당합니다. 다른 cmdlet을 통해 키를 관리하는 경우 자격 증명 모음 이름을 사용합니다. REST API를 통해 자격 증명 모음을 사용하는 응용 프로그램은 자격 증명 모음 URI를 사용합니다.

Azure Key Vault에서는 소프트웨어로 보호된 키를 제공하거나 .PFX 파일에서 기존 키를 가져올 수 있습니다. 자격 증명 모음에 비밀(암호)을 저장할 수도 있습니다.

또한 로컬 HSM에서 키를 생성하고, 키가 HSM 경계를 벗어나지 않고 키 자격 증명 모음 서비스의 HSM에 전송할 수도 있습니다.

Azure Key Vault 사용에 대한 자세한 지침은 [Azure Key Vault 시작](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)의 단계를 따르세요.

Azure Key Vault와 함께 사용되는 PowerShell cmdlet 목록은 [AzureRM.KeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0)를 참조하세요.

### <a name="azure-disk-encryption-for-windows"></a>Windows용 Azure Disk Encryption

[Windows 및 Linux IaaS VM용 Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)은 Azure 가상 머신에서 미사용 개인 데이터를 보호하고 Azure Key Vault와 통합합니다. Azure Disk Encryption은 Windows에서 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx)를, Linux에서 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)를 사용하여 OS 및 데이터 드라이브를 모두 암호화합니다. Azure Disk Encryption은 Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016, Windows 8 및 Windows 10 클라이언트에서 지원됩니다.

#### <a name="how-do-i-use-azure-disk-encryption-to-protect-personal-data"></a>Azure Disk Encryption을 사용하여 개인 데이터를 보호하려면 어떻게 할까요?

Azure Disk Encryption을 사용하려면 Azure 계정에 대한 구독이 필요합니다. Windows 및 Linux VM용 Azure Disk Encryption을 사용하도록 설정하려면 다음을 수행합니다.

1. Azure Disk Encryption Resource Manager 템플릿, PowerShell 또는 CLI(명령줄 인터페이스)를 사용하여 디스크 암호화를 사용하도록 설정하고 암호화 구성을 지정합니다. 

2. Azure 플랫폼에 대한 액세스 권한을 부여하여 키 자격 증명 모음에서 암호화 자료를 읽습니다.

3. AAD(Azure Active Directory) 응용 프로그램 ID를 제공하여 암호화 키 자료를 키 자격 증명 모음에 씁니다.

Azure에서 VM과 키 자격 증명 모음 구성을 업데이트하고 암호화된 VM을 설정합니다.

Azure Disk Encryption를 지원하도록 키 자격 증명 모음을 설정하면, 보안을 강화하고 암호화된 가상 머신의 백업을 지원하기 위해 KEK(키 암호화 키)를 추가할 수 있습니다.

![](media/protect-personal-data-at-rest/create-key.png)

특정 배포 시나리오 및 사용자 환경에 대한 자세한 지침은 [Windows 및 Linux IaaS VM용 Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)에 포함되어 있습니다.

### <a name="azure-storage-service-encryption"></a>Azure Storage 서비스 암호화

[미사용 데이터에 대한 Azure SSE(Storage 서비스 암호화)](https://docs.microsoft.com/azure/storage/storage-service-encryption)를 사용하면 조직의 보안 및 규정 준수 약정을 충족하도록 데이터를 보호할 수 있습니다. Azure Storage에서 저장소에 저장하기 전에 256비트 AES 암호화를 사용하여 데이터를 자동으로 암호화하고 검색하기 전에 암호를 해독합니다. 이 서비스는 Azure Blob 및 파일에서 사용할 수 있습니다.

#### <a name="how-do-i-use-storage-service-encryption-to-protect-personal-data"></a>Storage 서비스 암호화를 사용하여 개인 데이터를 보호하려면 어떻게 할까요?

Storage 서비스 암호화를 사용하도록 설정하려면 다음을 수행합니다.

1. Azure Portal에 로그인합니다.

2. 저장소 계정을 선택합니다.

3. [설정]의 [Blob Service] 섹션에서 [암호화]를 선택합니다.

4. [File Service] 섹션 아래에서 [암호화]를 선택합니다.

암호화 설정을 클릭하면 Storage 서비스 암호화를 사용하거나 사용하지 않도록 설정할 수 있습니다.

![](media/protect-personal-data-at-rest/storage-service-encryption.png)

새 데이터가 암호화됩니다. 이 저장소 계정에 있는 기존 파일의 데이터는 암호화되지 않은 상태로 유지됩니다.

암호화를 사용하도록 설정한 후에는 다음 방법 중 하나를 사용하여 저장소 계정에 데이터를 복사합니다.

1. [AzCopy 명령줄 유틸리티](https://docs.microsoft.com/azure/storage/storage-use-azcopy)를 사용하여 Blob 또는 파일을 복사합니다.

2. [SMB를 사용하여 파일 공유를 탑재](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-windows)하면 Robocopy와 같은 유틸리티를 사용하여 파일을 복사할 수 있습니다.

3. [Storage 클라이언트 라이브러리(예: .NET)](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-blobs)를 사용하여 Blob 저장소 간에 또는 Storage 계정 간에 또는 Blob 또는 파일 데이터를 복사합니다.

4.  [저장소 탐색기](https://docs.microsoft.com/azure/storage/storage-explorers)를 사용하여 암호화를 사용하도록 설정된 Storage 계정에 Blob을 업로드합니다.

### <a name="transparent-data-encryption"></a>투명한 데이터 암호화

TDE(투명한 데이터 암호화)는 데이터베이스 및 서버 수준 모두에서 데이터를 암호화할 수 있는 SQL Azure의 기능입니다. TDE는 이제 기본적으로 새로 만든 모든 데이터베이스에서 사용하도록 설정됩니다. TDE는 데이터 및 로그 파일에 대한 실시간 I/O 암호화 및 암호 해독을 수행합니다.

#### <a name="how-do-i-use-tde-to-protect-personal-data"></a>TDE를 사용하여 개인 데이터를 보호하려면 어떻게 할까요?

Azure Portal, REST API 또는 PowerShell을 사용하여 TDE를 구성할 수 있습니다. Azure Portal을 사용하여 기존 데이터베이스에서 TDE를 사용하도록 설정하려면 다음을 수행합니다.

1. <https://portal.azure.com>의 Azure Portal을 방문하여 Azure 관리자 또는 참가자 계정으로 로그인합니다.

2. 왼쪽 배너에서 [찾아보기]를 클릭한 다음 SQL 데이터베이스를 클릭합니다.

3. 왼쪽 창에서 SQL 데이터베이스를 선택한 상태에서 사용자 데이터베이스를 클릭합니다.

4. 데이터베이스 블레이드에서 [모두] 설정을 클릭합니다.

5. [설정] 블레이드에서 [투명한 데이터 암호화] 부분을 클릭하여 [투명한 데이터 암호화] 블레이드를 엽니다.

6. [데이터 암호화] 블레이드에서 [데이터 암호화] 단추를 [설정]으로 전환한 다음, 페이지 맨 위에 있는 [저장]을 클릭하여 해당 설정을 적용합니다. 암호화 상태는 투명한 데이터 암호화 진행률과 비슷합니다.

![데이터 암호화를 사용하도록 설정](media/protect-personal-data-at-rest/turn-data-encryption-on.png)

TDE를 사용하도록 설정하는 방법에 대한 지침, TDE로 보호되는 데이터베이스의 암호 해독에 대한 내용 및 기타 자세한 내용은 [Azure SQL Database를 사용한 투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) 문서에서 찾을 수 있습니다.

## <a name="summary"></a>요약

회사에서 Azure 클라우드에 저장된 개인 데이터를 암호화하는 목표를 달성할 수 있습니다. 이렇게 하려면 Azure Disk Encryption을 사용하여 전체 볼륨을 보호하면 됩니다. 여기에는 식별 가능한 개인 정보 및 기타 중요한 데이터를 보관하는 운영 체제 파일과 데이터 파일이 모두 포함될 수 있습니다. Azure Storage 서비스 암호화는 Blob 및 파일에 저장된 개인 데이터를 보호하는 데 사용할 수 있습니다. Azure SQL 데이터베이스에 저장된 데이터의 경우 투명한 데이터 암호화는 무단 노출로부터 개인 정보를 보호합니다.

Azure에서 데이터를 암호화하는 데 사용되는 키를 보호하기 위해 회사에서 Azure Key Vault를 사용할 수 있습니다. 이렇게 하면 키 관리 프로세스가 간소화되고 회사에서 개인 데이터를 액세스하고 암호화하는 키를 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 문제 해결 가이드](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-tsg)

- [Azure Virtual Machine 암호화](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption?toc=%2fazure%2fsecurity%2ftoc.json)

- [Azure Data Lake Store의 데이터 암호화](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-encryption)

- [미사용 Azure Cosmos DB 데이터베이스 암호화](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)
