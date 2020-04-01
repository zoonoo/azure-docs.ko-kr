---
title: Azure Security Center에서 디스크 암호화 적용 | Microsoft Docs
description: 이 문서에서는 Azure 보안 센터 권장 사항 **디스크 암호화 적용을**구현하는 방법을 보여 주며 있습니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: e0677ecc63573d04e384a0104ee0e4e28b4d5e3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473282"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Azure Security Center에서 디스크 암호화 적용

Azure 보안 센터는 Windows 및 Linux VM 모두에서 암호화되지 않은 디스크에서 Azure 디스크 암호화를 사용하는 것이 좋습니다. 디스크 암호화를 사용하면 Windows 및 Linux IaaS VM 디스크를 암호화할 수 있습니다.  VM에서 OS 및 데이터 볼륨에 암호화를 사용하는 것이 좋습니다.

디스크 암호화는 업계 표준인 Windows의 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 기능과 Linux의 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능을 사용합니다. 이러한 기능은 데이터를 보호하고 조직의 보안 및 규정 준수 요구 사항을 충족하는 데 도움이 되는 OS 및 데이터 암호화를 제공합니다. 디스크 암호화는 [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/)와 함께 통합되어 주요 자격 증명 모음 구독에서 디스크 암호화 키 및 암호를 제어하고 관리할 수 있도록 하며 VM 디스크의 모든 휴지 상태 데이터가 [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)에서 암호화되도록 보장합니다.

지원되는 버전의 Windows 및 Linux 목록은 Azure 디스크 암호화 설명서의 [지원되는 VM 및 운영 체제를](../virtual-machines/windows/disk-encryption-overview.md#supported-vms-and-operating-systems) 참조하십시오.

## <a name="implement-the-recommendation"></a>권장 사항 구현
1. 권장 **사항** 페이지에서 **가상 컴퓨터에 디스크 암호화를**선택해야 합니다.
2. **비정상 리소스에서**디스크 암호화를 권장하는 VM을 선택합니다.
3. 지시를 따라서 이러한 VM에 암호화를 적용합니다.

![디스크 암호화 적용](./media/security-center-apply-disk-encryption/apply-disk-encryption.png)

보안 센터에 의해 암호화가 필요하다고 식별된 Azure Virtual Machines를 암호화하려면 다음 단계를 권장합니다.

* Azure PowerShell을 설치하고 구성합니다. 이렇게 하면 Azure Virtual Machines를 암호화하는 데 필요한 필수 구성 요소를 설정하는 데 필요한 PowerShell 명령을 실행할 수 있습니다.
* Azure 디스크 암호화 필수 구성 요소 Azure PowerShell 스크립트를 가져오고 실행합니다.
* 가상 머신 암호화

[Azure PowerShell을 사용하여 Windows IaaS VM 암호화](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) - 이러한 단계를 안내하고 디스크 암호화를 구성할 수 있는 Windows 클라이언트 컴퓨터를 사용하고 있다고 가정합니다.

Azure Virtual Machines에 대해 사용할 수 있는 방법은 여러 가지가 있습니다. Azure PowerShell 또는 Azure CLI에 이미 정통한 경우 대체 방법을 사용하는 것이 좋습니다. 이러한 다른 방법에 대한 자세한 내용은 [Azure 디스크 암호화](../security/fundamentals/encryption-overview.md)를 참조하십시오.

## <a name="see-also"></a>참조
이 문서에서는 보안 센터 권장 사항 "디스크 암호화 적용"을 구현하는 방법을 보여주었습니다. 디스크 암호화에 대한 자세한 내용은 다음을 참조하십시오.

* [Azure Key Vault를 사용한 암호화 및 키](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) 관리(비디오, 36분 39초)-IaaS VM 및 Azure Key Vault에 대한 디스크 암호화 관리를 사용하여 데이터를 보호하고 보호하는 방법을 알아봅니다.
* [Azure 디스크](../security/fundamentals/encryption-overview.md) 암호화(문서)--Windows 및 Linux VM용 디스크 암호화를 사용하도록 설정하는 방법에 대해 알아봅니다.