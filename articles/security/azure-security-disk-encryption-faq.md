---
title: Azure Disk Encryption FAQ | Microsoft Docs
description: "이 문서에서는 Windows 및 Linux IaaS VM용 Microsoft Azure Disk Encryption에 대한 질문과 대답을 제공합니다."
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 2a81a88750ae40cd75e5d394f97c8310416546e4
ms.contentlocale: ko-kr
ms.lasthandoff: 07/28/2017

---
# <a name="azure-disk-encryption-frequently-asked-questions-faq"></a>Azure Disk Encryption FAQ(질문과 대답)

이 FAQ는 Windows 및 Linux IaaS VM용 Azure Disk Encryption에 대한 질문에 답변합니다. 이 서비스에 대한 자세한 내용은 [Windows 및 Linux IaaS VM용 Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)을 참조하세요.

## <a name="general-questions"></a>일반적인 질문
Q. Azure Disk Encryption은 어느 지역의 GA에서 사용할 수 있나요?
A: Windows 및 Linux IaaS VM용 Azure Disk Encryption은 모든 Azure 공용 지역의 GA에서 사용할 수 있습니다.

Q: Azure Disk Encryption에서 사용할 수 있는 사용자 환경은 무엇인가요?
A: Azure Disk Encryption GA에서는 Azure Resource Manager 템플릿, Azure PowerShell, Azure CLI를 지원합니다. 이렇게 하면 IaaS VM에 대한 디스크 암호화를 사용하기 위한 세 가지 옵션이 있다는 점에서 많은 유연성을 제공합니다. 사용자 환경 및 단계별 지침에 대한 자세한 내용은 Azure Disk Encryption 배포 시나리오 및 환경을 참조하세요.

Q: Azure Disk Encryption 비용은 얼마인가요?
A: Azure Disk Encryption을 사용하여 VM 디스크를 암호화하는 데 부과되는 요금은 없습니다.

Q: Azure Disk Encryption을 사용할 수 있는 가상 컴퓨터 계층은 무엇인가요?
A: Azure Disk Encryption은 프리미엄 저장소가 있는 VM을 포함하여 [A, D, DS, G, GS, F](https://azure.microsoft.com/pricing/details/virtual-machines/) 등의 IaaS VM 시리즈를 포함한 표준 계층 VM에서만 사용할 수 있습니다. 기본 계층 VM에서는 사용할 수 없습니다.

Q: Azure Disk Encryption은 어떤 Linux 배포판을 지원하나요?
A: Azure Disk Encryption에서 지원하는 Linux 서버 배포판 및 버전은 다음과 같습니다.
| Linux 배포 | 버전 | 암호화에 지원되는 볼륨 유형|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | OS 및 데이터 디스크 |
| Ubuntu | 14.04.5-DAILY-LTS | OS 및 데이터 디스크 |
| RHEL | 7.3 | OS 및 데이터 디스크 |
| RHEL | 7.2 | OS 및 데이터 디스크 |
| RHEL | 6.8 | OS 및 데이터 디스크 |
| RHEL | 6.7 | 데이터 디스크  |
| CentOS | 7.3 | OS 및 데이터 디스크 |
| CentOS | 7.2n | OS 및 데이터 디스크 |
| CentOS | 6.8 | OS 및 데이터 디스크 |
| CentOS | 7.1 | 데이터 디스크  |
| CentOS | 7.0 | 데이터 디스크  |
| CentOS | 6.7 | 데이터 디스크  |
| CentOS | 6.6 | 데이터 디스크  |
| CentOS | 6.5 | 데이터 디스크  |
| openSUSE | 13.2 | 데이터 디스크  |
| SLES | 12 SP1 | 데이터 디스크  |
| SLES | 우선 순위: 12-SP1 | 데이터 디스크  |
| SLES | HPC 12 | 데이터 디스크  |
| SLES | 우선 순위: 11-SP4 | 데이터 디스크  |
| SLES | 11 SP4 | 데이터 디스크  |

Q: Azure Disk Encryption 사용을 시작하려면 어떻게 해야 하나요?
A: 고객은 [여기](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)에 있는 Azure Disk Encryption 백서를 참조하여 시작하는 방법을 알아볼 수 있습니다.

Q: Azure Disk Encryption을 사용하여 부팅 볼륨과 데이터 볼륨을 모두 암호화할 수 있나요?
A: 예, Windows 및 Linux IaaS VM용 부팅 볼륨과 데이터 볼륨을 암호화할 수 있습니다. Windows VM의 경우 OS 볼륨을 먼저 암호화해야 데이터 볼륨을 암호화할 수 있습니다. Linux VM의 경우 OS 볼륨을 먼저 암호화하지 않고도 데이터 볼륨을 암호화할 수 있습니다. Linux용 OS 볼륨을 암호화하면 Linux IaaS VM용 OS 볼륨에 대한 암호화 사용 해제가 지원되지 않습니다.

Q: Azure Disk Encryption에서 "BYOK(Bring Your Own Key)" 기능을 사용하나요?
A: 네, 사용자 고유의 키 암호화 키를 제공할 수 있습니다. 이러한 키는 Azure Disk Encryption에 대한 키 저장소인 Azure Key Vault에서 보호됩니다. 키 암호화 키 지원 시나리오에 대한 자세한 내용은 Azure Disk Encryption 배포 시나리오 및 환경을 참조하세요.

Q: Azure에서 만든 키 암호화 키를 사용할 수 있나요?
A: 예, Azure Key Vault를 사용하여 Azure Disk Encryption 사용을 위한 키 암호화 키를 생성할 수 있습니다. 이러한 키는 Azure Disk Encryption에 대한 키 저장소인 Azure Key Vault에서 보호됩니다. 키 암호화 키 지원 시나리오에 대한 자세한 내용은 Azure Disk Encryption 배포 시나리오 및 환경을 참조하세요.

Q: 온-프레미스 키 관리 서비스/HSM을 사용하여 암호화 키를 보호할 수 있나요?
A: 온-프레미스 키 관리 서비스/HSM은 Azure Disk Encryption를 통해 암호화 키를 보호하는 데 사용할 수 없습니다. Azure Key Vault 서비스만 사용하여 암호화 키를 보호할 수 있습니다. 키 암호화 키 지원 시나리오에 대한 자세한 내용은 Azure Disk Encryption 배포 시나리오 및 환경을 참조하세요.

Q: Azure Disk Encryption을 구성하기 위한 필수 구성 요소는 무엇인가요?
A: Azure Disk Encryption 필수 구성 요소인 PowerShell 스크립트를 사용하여 AAD 응용 프로그램을 만들거나, 새 키 자격 증명 모음을 만들거나, 디스크 암호화 액세스에 대한 기존 키 자격 증명 모음을 설정하여 암호화를 사용하도록 설정하고 비밀과 키를 보호합니다.  키 암호화 키 지원 시나리오에 대한 자세한 내용은 Azure Disk Encryption 필수 구성 요소, 배포 시나리오 및 환경을 참조하세요.

Q: PowerShell을 사용하여 Azure Disk Encryption을 구성하는 방법에 대한 자세한 정보는 어디서 얻을 수 있나요?
A: Azure Disk Encryption 기본 작업을 수행하는 방법과 고급 시나리오에 대한 몇 가지 훌륭한 문서가 있습니다. 기본 작업은 [Azure PowerShell를 사용하여 Azure Disk Encryption 탐색 - 1부(영문)](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/)를 확인하세요. 고급 시나리오는 [Azure PowerShell를 사용하여 Azure Disk Encryption 탐색 - 2부(영문)](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/)를 참조하세요.

Q: Azure Disk Encryption에서 지원하는 Azure PowerShell 버전은 무엇인가요?
A: 최신 버전의 Azure PowerShell SDK 버전을 사용하여 Azure Disk Encryption을 구성합니다. 최신 버전의 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)을 다운로드합니다. Azure SDK 버전 1.1.0에서는 Azure Disk Encryption을 지원하지 않습니다.

> [!NOTE]
> Linux Azure Disk Encryption 미리 보기 확장은 더 이상 사용되지 않습니다. 자세한 내용은 [여기](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/)에 있는 설명서를 참조하세요.

Q: 사용자 지정 Linux 이미지에 Azure Disk Encryption을 적용할 수 있나요?
A: 사용자 지정 Linux 이미지에는 Azure Disk Encryption을 적용할 수 없습니다. 위에서 언급한 지원 배포판에 대한 Linux 갤러리 이미지만 지원합니다. 현재 사용자 지정 Linux 이미지는 지원하지 않습니다.

Q: Yum 업데이트를 사용하여 Linux Red Hat VM에 업데이트를 적용할 수 있나요?
A: 예, [여기](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/)서 설명하는 지침에 따라 Red Hat Linux VM을 업데이트하거나 패치할 수 있습니다.

Q: 질문을 하거나 의견을 제출할 수 있는 곳은 어디인가요? A: [여기](https://social.msdn.microsoft.com/Forums/home?forum=AzureDiskEncryption)에 있는 Azure Disk Encryption 포럼에서 질문하거나 피드백을 제출할 수 있습니다.

## <a name="see-also"></a>참고 항목
이 문서에서는 Azure Disk Encryption와 관련하여 가장 자주 묻는 질문에 대해 자세히 알아 보았습니다. 이 서비스 및 기능에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Security Center에서 디스크 암호화 적용](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure 가상 컴퓨터 암호화](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Azure 미사용 데이터 암호화](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

