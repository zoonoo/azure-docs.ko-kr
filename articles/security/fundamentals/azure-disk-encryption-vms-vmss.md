---
title: 가상 컴퓨터 및 가상 머신 확장 집합에 대 한 Azure Disk Encryption
description: 이 문서에서는 Azure Disk Encryption에 대 한 개요를 제공 합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 239bfb4f5a0e9115c113bb2d5ddbc931f41c0f3d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72599956"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>가상 컴퓨터 및 가상 머신 확장 집합에 대 한 Azure Disk Encryption

Azure 디스크 암호화는 Linux 및 Windows 가상 머신과 가상 머신 확장 집합에 모두 적용할 수 있습니다. 

## <a name="linux-virtual-machines"></a>Linux 가상 머신

다음 문서에서는 Linux 가상 머신을 암호화 하기 위한 지침을 제공 합니다.

### <a name="current-version-of-azure-disk-encryption"></a>현재 버전 Azure Disk Encryption

- [Linux 가상 머신에 대 한 Azure Disk Encryption 개요](../../virtual-machines/linux/disk-encryption-overview.md)
- [Linux Vm에 대 한 Azure Disk Encryption 시나리오](../../virtual-machines/linux/disk-encryption-linux.md)
- [Azure CLI를 사용 하 여 Linux VM 만들기 및 암호화](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Azure PowerShell를 사용 하 여 Linux VM 만들기 및 암호화](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Azure Portal를 사용 하 여 Linux VM 만들기 및 암호화](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure Disk Encryption 샘플 스크립트](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Azure Disk Encryption 문제 해결](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption 질문과 대답](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure AD를 사용 하는 azure disk encryption (이전 버전)

- [Linux 가상 머신에 대 한 Azure AD Azure Disk Encryption 개요](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Linux Vm에서 Azure AD 시나리오를 사용 하 여 Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-linux.md)
- [Azure AD (이전 릴리스)를 사용 하 여 Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Windows 가상 머신

다음 문서는 Windows 가상 머신 암호화에 대 한 지침을 제공 합니다.

### <a name="current-version-of-azure-disk-encryption"></a>현재 버전 Azure Disk Encryption

- [Windows 가상 머신에 대 한 Azure Disk Encryption 개요](../../virtual-machines/windows/disk-encryption-overview.md)
- [Windows Vm에 대 한 Azure Disk Encryption 시나리오](../../virtual-machines/windows/disk-encryption-windows.md)
- [Azure CLI를 사용 하 여 Windows VM 만들기 및 암호화](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Azure PowerShell를 사용 하 여 Windows VM 만들기 및 암호화](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Azure Portal를 사용 하 여 Windows VM 만들기 및 암호화](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure Disk Encryption 샘플 스크립트](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Azure Disk Encryption 문제 해결](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption 질문과 대답](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure AD를 사용 하는 azure disk encryption (이전 버전)

- [Windows 가상 머신에 대 한 Azure AD Azure Disk Encryption 개요](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Windows Vm에서 Azure AD 시나리오로 Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-windows.md)
- [Azure AD (이전 릴리스)를 사용 하 여 Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>가상 머신 크기 집합

다음 문서에서는 가상 머신 확장 집합을 암호화 하는 방법에 대 한 지침을 제공 합니다.

- [가상 머신 확장 집합에 대 한 Azure Disk Encryption 개요](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Azure CLI를 사용 하 여 가상 머신 확장 집합 암호화](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Azure Powershell을 사용 하 여 가상 머신 확장 집합을 암호화](../../virtual-machine-scale-sets/disk-encryption-powershell.md)합니다.
- [Azure Resource Manager를 사용 하 여 가상 머신 확장 집합 암호화](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Azure Disk Encryption에 대 한 키 자격 증명 모음 만들기 및 구성](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [가상 머신 확장 집합 확장 시퀀싱으로 Azure Disk Encryption 사용](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>다음 단계

- [Azure 암호화 개요](encryption-overview.md)
- [저장 데이터 암호화](encryption-atrest.md)
- [데이터 보안 및 암호화 모범 사례](data-encryption-best-practices.md)
