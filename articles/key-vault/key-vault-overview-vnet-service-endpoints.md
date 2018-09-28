---
ms.assetid: ''
title: Azure Key Vault에 대한 VNET 서비스 엔드포인트 | Microsoft Docs
description: Key Vault의 가상 네트워크 서비스 엔드포인트 개요
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 08/31/2018
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: c2696d5eb22443b565c48ef4f96d6e4a25827606
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295007"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Azure Key Vault의 가상 네트워크 서비스 엔드포인트

Key Vault에 대한 가상 네트워크 서비스 엔드포인트를 사용하여 액세스 권한을 지정된 가상 네트워크 및/또는 IPv4(인터넷 프로토콜 버전 4) 주소 범위 목록으로 제한할 수 있습니다. 이러한 소스 외부에서 Key Vault에 연결하는 모든 호출자는 액세스가 거부됩니다. 고객이 Office 365 Exchange Online, Office 365 SharePoint Online, Azure Compute, Azure Resource Manager, Azure Backup 등과 같은 “신뢰할 수 있는 Microsoft 서비스”를 허용하도록 옵트인(Opt-In)한 경우 해당 서비스에서의 연결은 방화벽을 통과합니다. 물론 이러한 호출자는 유효한 AAD 토큰을 제공해야 하며, 요청된 작업을 수행하기 위해 (액세스 정책으로 구성된) 사용 권한이 있어야 합니다. [가상 네트워크 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)에 대한 추가적인 기술 세부 정보를 읽어보세요.

## <a name="usage-scenarios"></a>사용 시나리오

기본적으로 모든 네트워크(인터넷 트래픽 포함)의 트래픽에 대한 액세스를 거부하도록 [Key Vault 방화벽 및 가상 네트워크](key-vault-network-security.md)를 구성할 수 있습니다. 특정 Azure Virtual Network 및/또는 공용 인터넷 IP 주소의 트래픽에 대한 액세스 권한을 부여하여 응용 프로그램에 대한 네트워크 보안 경계를 구축하도록 할 수 있습니다.

> [!NOTE]
> Key Vault 방화벽 및 가상 네트워크 규칙은 Key Vault [데이터 평면](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control)에만 적용됩니다. Key Vault 제어 평면 작업(예: Key Vault 만들기, 삭제, 수정 작업, 액세스 정책 설정, 방화벽 및 가상 네트워크 규칙 설정)은 방화벽 및 가상 네트워크 규칙의 영향을 받지 않습니다.

예를 들면 다음과 같습니다.
* Key Vault를 사용하여 암호화 키, 응용 프로그램 비밀, 인증서를 저장하며 공용 인터넷에서 Key Vault에 대한 액세스를 차단하려고 한다고 가정합니다.
* 사용자 응용 프로그램 또는 지정된 일부 호스트만 Key Vault에 연결할 수 있도록 Key Vault에 대한 액세스를 잠그려고 합니다.
* Azure VNET(Virtual Network)에서 실행되는 응용 프로그램 및 이 VNET은 모든 인바운드 및 아웃바운드 트래픽에 대해 잠겨 있습니다. 응용 프로그램은 여전히 비밀 또는 인증서를 가져오거나 암호화 키를 사용하 기 위해 Key Vault에 연결해야 합니다.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Key Vault 방화벽 및 가상 네트워크 구성

방화벽 및 가상 네트워크를 구성하는 데 필요한 단계는 다음과 같습니다. 이러한 단계는 방화벽 및 가상 네트워크 규칙을 설정하기 위해 사용하는 인터페이스(PowerShell, CLI, Azure Portal)에 관계없이 동일합니다.
1. 선택 사항이지만 강력히 권장되는 작업: [Key Vault 로깅](key-vault-logging.md)을 사용하도록 설정하여 자세한 액세스 로그를 확인합니다. 이렇게 하면 방화벽 및 가상 네트워크 규칙이 Key Vault에 대한 액세스를 막는 경우를 진단하는 데 도움이 됩니다.
2. 대상 가상 네트워크 및 서브넷에 대한 'Key Vault의 서비스 엔드포인트’를 사용하도록 설정
3. 특정 가상 네트워크, 서브넷 및 IPv4 주소 범위에서 해당 Key Vault로의 액세스를 제한하도록 Key Vault에 대한 방화벽 및 가상 네트워크 규칙을 설정합니다.
4. 이 Key Vault를 신뢰할 수 있는 모든 Microsoft 서비스에서 액세스할 수 있도록 하려면 ‘신뢰할 수 있는 Azure 서비스’가 Key Vault에 연결하도록 허용하는 옵션을 설정합니다.

자세한 단계별 지침을 보려면 [Azure Key Vault 방화벽 및 가상 네트워크 구성](key-vault-network-security.md)을 참조하세요.

> [!IMPORTANT]
> 일단 방화벽 규칙이 적용되면 모든 Key Vault [데이터 평면](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) 작업은 호출자 요청이 허용되는 가상 네트워크 또는 IPV4 주소 범위에서 시작된 경우에만 수행될 수 있습니다. Azure Portal에서 Key Vault에 액세스하는 경우도 마찬가지입니다. 사용자가 Azure Portal에서 Key Vault를 찾아볼 수 있지만 해당 클라이언트 컴퓨터가 허용 목록에 없는 경우 키/암호/인증서를 나열하지 못할 수 있습니다. 다른 Azure 서비스의 ‘Key Vault 선택기’도 마찬가지입니다. 방화벽 규칙이 해당 사용자의 클라이언트 컴퓨터를 금지하는 경우 사용자는 Key Vault 목록을 볼 수 있지만 키를 나열할 수는 없습니다.


> [!NOTE]
> * 최대 127개의 VNET 규칙 및 127개의 IPv4 규칙이 허용됩니다. 
> * "/31" 또는 "/32" 접두사 크기를 사용하는 작은 주소 범위는 지원되지 않습니다. 이러한 범위는 개별 IP 주소 규칙을 사용하여 구성해야 합니다.
> * IP 네트워크 규칙은 공용 IP 주소에 대해서만 허용됩니다. 개인 네트워크용으로 예약된 IP 주소 범위(RFC 1918에 정의)는 IP 규칙에서 허용되지 않습니다. 개인 네트워크는 *10.*\*, *172.16.*\* 및 *192.168.*\*로 시작하는 주소를 포함합니다. 
> * 현재 IPv4 주소만 지원됩니다.

## <a name="trusted-services"></a>신뢰할 수 있는 서비스
‘신뢰할 수 있는 서비스 허용’ 옵션이 사용되도록 설정된 경우 Key Vault에 액세스하도록 허용되는 신뢰할 수 있는 서비스 목록은 다음과 같습니다.

|신뢰할 수 있는 서비스|사용 시나리오|
| --- | --- |
|Azure Virtual Machines 배포 서비스|[고객 관리 Key Vault에서 VM으로 인증서 배포](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Azure Resource Manager 템플릿 배포 서비스|[배포 중 보안 값 전달](../azure-resource-manager/resource-manager-keyvault-parameter.md)|
|Azure Disk Encryption 볼륨 암호화 서비스|VM 배포 동안 BitLocker 키(Windows VM) 또는 DM 암호(Linux VM)와 키 암호화에 대한 액세스를 허용하여 [Azure Disk Encryption](../security/azure-security-disk-encryption.md)를 사용하도록 설정|
|Azure Backup|Azure VM 백업 동안 [Azure Backup](../backup/backup-introduction-to-azure-backup.md)을 사용하여 관련 키 및 암호의 백업 및 복원 허용|
|Exchange Online 및 SharePoint Online|[고객 키](https://support.office.com/en-us/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697)를 사용하여 서비스 암호화를 위한 고객 키 액세스 허용|
|Azure Information Protection|[Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)을 위해 테넌트 키 액세스 허용|
|App Services|[Key Vault를 통해 Azure Web App 인증서 배포](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)|
|Azure SQL|[Azure SQL Database 및 Data Warehouse에서 BYOK(Bring Your Own Key) 지원을 통한 투명한 데이터 암호화](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)|
|Azure Storage|[Azure Key Vault의 고객 관리 키를 사용하는 Storage 서비스 암호화](../storage/common/storage-service-encryption-customer-managed-keys.md)|
|Azure Data Lake Store|고객 관리 키를 사용하여 [Azure Data Lake Store의 데이터 암호화](../data-lake-store/data-lake-store-encryption.md)|



> [!NOTE]
> 해당 서비스가 Key Vault에 액세스할 수 있도록 하려면 관련 Key Vault 액세스 정책을 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Key Vault 보안 설정](key-vault-secure-your-key-vault.md)
* [Azure Key Vault 방화벽 및 가상 네트워크 구성](key-vault-network-security.md)