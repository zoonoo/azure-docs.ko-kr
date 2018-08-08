---
title: 미사용 데이터에 대한 Azure Storage 서비스 암호화 | Microsoft Docs
description: Azure Storage 서비스 암호화 기능을 사용하여 데이터를 저장할 때 서비스 쪽에서 Azure Blob Storage를 암호화하고 데이터를 검색할 때 암호 해독합니다.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 08/01/2018
ms.author: lakasa
ms.openlocfilehash: f35697139a4be49be8a645cfd4d451ad8e3c8094
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412358"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>미사용 데이터에 대한 Azure Storage 서비스 암호화
미사용 데이터에 대한 Azure Storage 서비스 암호화를 사용하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. 이 기능을 통해 Azure 저장소 플랫폼은 데이터를 자동으로 암호화한 후 Azure Blob Storage, Azure Files 또는 Azure Queue Storage에 보관하고, 검색 전에 암호를 해독합니다. Storage 서비스 암호화의 암호화, 미사용 데이터의 암호화, 암호 해독, 키 관리는 완전히 투명한 방식으로 처리됩니다. Azure 저장소 플랫폼에 쓴 모든 데이터는 가장 강력한 블록 암호화 기술 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 통해 암호화됩니다.

Storage 서비스 암호화는 모든 신규 및 기존 저장소 계정에 대해 사용되도록 설정되며 비활성화할 수 없습니다. 사용자 데이터는 기본적으로 보호되므로 Storage 서비스 암호화를 활용하기 위해 코드 또는 응용 프로그램을 수정할 필요가 없습니다.

이 기능은 다음의 데이터를 자동으로 암호화합니다.

- Azure Blob Storage, Azure Files, Azure Queue Storage, Azure Table Storage.  
- 두 성능 계층(Standard 및 Premium)
- 두 배포 모델(Azure Resource Manager 및 클래식)

> [!Note]  
> [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md)에는 저장소 서비스 암호화를 사용할 수 있습니다. OS 수준에서 암호화를 사용하는 것이 좋습니다. 예를 들어 [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md)은 Windows에서 업계 표준 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)를 사용하고 Linux에서 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)를 사용하여 KeyVault와 통합된 암호화를 제공합니다.

저장소 서비스 암호화는 Azure 저장소 서비스의 성능에 영향을 주지 않습니다.

Storage 서비스 암호화를 사용하는 Microsoft 관리 암호화 키를 사용하거나 사용자 고유의 암호화 키를 사용할 수 있습니다. 자체 키 사용에 대한 자세한 내용은 [Azure Key Vault의 고객 관리 키를 사용하는 Storage 서비스 암호화](storage-service-encryption-customer-managed-keys.md)를 참조하세요.

## <a name="view-encryption-settings-in-the-azure-portal"></a>Azure Portal에서 암호화 설정 보기
Storage 서비스 암호화의 설정을 보려면 [Azure Portal](https://portal.azure.com)에 로그인한 후 저장소 계정을 선택합니다. **설정** 창에서 **암호화** 설정을 선택합니다.

![암호화 설정을 보여 주는 포털 스크린샷](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Storage 서비스 암호화에 대한 FAQ
**Resource Manager 저장소 계정에서 데이터를 암호화하려면 어떻게 해야 하나요?**  
저장소 서비스 암호화는 모든 저장소 계정(클래식 및 Resource Manager)에 대해 사용하도록 설정되고, 암호화가 활성화되기 전에 만들어진 저장소 계정의 모든 기존 파일이 백그라운드 암호화 프로세스를 통해 소급하여 암호화됩니다.

**저장소 계정을 만들 경우 저장소 서비스 암호화가 기본적으로 사용되나요?**  
예, 저장소 서비스 암호화는 모든 저장소 계정 및 모든 Azure 저장소 서비스에 대해 사용하도록 설정됩니다.

**Resource Manager 저장소 계정이 있습니다. 해당 계정에 대해 Storage 서비스 암호화를 사용하도록 설정할 수 있나요?**  
저장소 서비스 암호화는 기존의 모든 Resource Manager 저장소 계정에서 기본적으로 활성화됩니다. 이 기능은 Azure Blob Storage, Azure Files, Azure Queue Storage, Azure Table Storage에서 지원됩니다. 

**내 저장소 계정에서 암호화를 사용하지 않도록 설정할 수 있나요?**  
암호화는 기본적으로 사용하도록 설정되며 저장소 계정에 대한 암호화를 사용하지 않도록 설정하는 옵션은 제공되지 않습니다. 

**저장소 서비스 암호화를 사용하는 경우 Azure Storage 비용은 얼마나 늘어나나요?**  
추가 비용은 없습니다.

**나만의 암호화 키를 사용할 수 있나요?**  
예. 사용자 고유의 암호화 키를 사용할 수 있습니다. 자세한 내용은 [Azure Key Vault의 고객 관리 키를 사용하는 Storage 서비스 암호화](storage-service-encryption-customer-managed-keys.md)를 참조하세요.

**암호화 키에 대한 액세스를 해지할 수 있나요?**  
예, Azure Key Vault에서 [자신만의 암호화 키를 사용](storage-service-encryption-customer-managed-keys.md)하는 경우 가능합니다.

**Azure Managed Disks에 저장소 서비스 암호화를 사용할 수 있나요?**  
아니요, [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md)에는 저장소 서비스 암호화를 사용할 수 없습니다. OS 수준에서 암호화를 사용하는 것이 좋습니다. 예를 들어 [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md)은 Windows에서 업계 표준 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)를 사용하고 Linux에서 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)를 사용하여 KeyVault와 통합된 암호화를 제공합니다.

**저장소 서비스 암호화가 Azure Disk Encryption과 어떻게 다른가요?**  
Azure Disk Encryption은 BitLocker, DM-Crypt와 같은 OS 기반 솔루션과 Azure KeyVault 간의 통합을 제공합니다. 저장소 서비스 암호화는 기본적으로 가상 머신 아래 Azure 저장소 플랫폼 계층에서 암호화를 제공합니다.

**클래식 저장소 계정이 있습니다. 해당 계정에 대해 Storage 서비스 암호화를 사용하도록 설정할 수 있나요?**  
저장소 서비스 암호화는 모든 저장소 계정(클래식 및 Resource Manager)에 대해 사용하도록 설정됩니다.

**내 클래식 저장소 계정에서 데이터를 암호화하려면 어떻게 해야 하나요?**  
기본적으로 암호화를 사용하도록 설정되어 있으며, Azure 저장소 서비스에 저장된 모든 데이터가 자동으로 암호화됩니다. 

**Azure PowerShell 및 Azure CLI를 사용하여 저장소 서비스 암호화가 사용되도록 설정된 저장소 계정을 만들 수 있나요?**  
저장소 서비스 암호화는 기본적으로 모든 저장소 계정(클래식 또는 Resource Manager)을 만들 때 활성화됩니다. Azure PowerShell과 Azure CLI를 사용하여 계정 속성을 확인할 수 있습니다.

**내 저장소 계정이 지역 중복되어 복제되도록 설정되어 있습니다. Storage 서비스 암호화를 사용할 경우 중복 복사본도 암호화되나요?**  
예. 저장소 계정의 모든 복사본이 암호화됩니다. 로컬 중복 저장소, 영역 중복 저장소, 지역 중복 저장소 및 읽기 액세스 지역 중복 저장소를 비롯한 모든 중복 옵션이 지원됩니다.

**저장소 서비스 암호화는 특정 지역에서만 허용되나요?**  
저장소 서비스 암호화는 모든 지역에서 사용할 수 있습니다.

**저장소 서비스 암호화는 FIPS 140-2를 준수하나요?**  
예, 저장소 서비스 암호화는 FIPS 140-2를 준수합니다.

**문제가 있거나 피드백을 제공하고 싶은 경우 어떻게 연락하나요?**  
저장소 서비스 암호화에 대한 문제나 의견이 있으면 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)으로 문의하세요.

## <a name="next-steps"></a>다음 단계
Azure Storage는 여러 개발자가 보안 응용 프로그램을 빌드하도록 지원하는 포괄적인 보안 기능을 제공합니다. 자세한 내용은 [저장소 보안 가이드](../storage-security-guide.md)를 참조하세요.