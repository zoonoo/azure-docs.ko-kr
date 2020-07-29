---
title: 격리 된 네트워크에서 Azure Disk Encryption
description: 이 문서에서는 Linux Vm 용 Microsoft Azure 디스크 암호화에 대 한 문제 해결 팁을 제공 합니다.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: f0ca50d137ef7e6378d2bf8d0ab03127d49a9a88
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373592"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>격리 된 네트워크에서 Azure Disk Encryption

방화벽, 프록시 요구 사항 또는 NSG(네트워크 보안 그룹) 설정으로 연결이 제한되면 필요한 작업을 수행할 수 있는 확장의 기능이 중단될 수 있습니다. 이로 인해 "VM에서 사용할 수 없는 확장 상태"와 같은 상태 메시지가 표시됩니다.

## <a name="package-management"></a>패키지 관리

Azure Disk Encryption은 일반적으로 ADE를 사용할 때 설치 되는 구성 요소 수에 따라 달라 집니다. 방화벽 뒤에 있거나 인터넷에서 격리 된 경우에는 이러한 패키지를 미리 설치 하거나 로컬로 사용할 수 있어야 합니다.

각 배포에 필요한 패키지는 다음과 같습니다. 지원 되는 배포판 및 볼륨 유형의 전체 목록은 [지원 되는 vm 및 운영 체제](disk-encryption-overview.md#supported-vms-and-operating-systems)를 참조 하세요.

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-6, procps
- **CentOS 7.2-7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-전송, pyparted, procps-, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-전송, pyparted, python-6
- **Redhat 7.2-7.7**: lsscsi, psmisc,, uuid, at, patch, cryptsetup, cryptsetup-전송, procps-기능-linux
- **Redhat 6.8**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup-전송
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

Red Hat에서 프록시가 필요한 경우 subscription-manager와 yum이 올바르게 설정되었는지 확인해야 합니다. 자세한 내용은 [구독 관리자 및 yum 문제를 해결하는 방법](https://access.redhat.com/solutions/189533)을 참조하세요.  

패키지를 수동으로 설치 하는 경우 새 버전이 릴리스되면 수동으로 업그레이드 해야 합니다.

## <a name="network-security-groups"></a>네트워크 보안 그룹
적용되는 모든 네트워크 보안 그룹 설정은 엔드포인트에서도 디스크 암호화에 대해 문서화된 네트워크 구성 필수 조건을 충족해야 합니다.  [Azure Disk Encryption: 네트워킹 요구 사항](disk-encryption-overview.md#networking-requirements) 을 참조 하세요.

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure AD (이전 버전)로 Azure Disk Encryption

[AZURE AD (이전 버전)와 Azure Disk Encryption](disk-encryption-overview-aad.md)를 사용 하는 경우에는 [위에 나와](#package-management)있는 것 처럼 배포판에 적합 한 패키지 외에도 모든 배포판에 대해 [Azure Active Directory 라이브러리](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) 를 수동으로 설치 해야 합니다.

[Azure AD 자격 증명](disk-encryption-linux-aad.md)에 암호화가 사용하도록 설정된 경우 대상 VM은 Azure Active Directory 엔드포인트 및 Key Vault 엔드포인트 모두에 대한 연결을 허용해야 합니다. 현재 Azure Active Directory 인증 엔드포인트는 [Office 365 URL 및 IP 주소 범위](/office365/enterprise/urls-and-ip-address-ranges) 설명서의 56 및 59 섹션에서 유지 관리됩니다. Key Vault 지침은 [방화벽 뒤에 있는 Azure Key Vault에 액세스](../../key-vault/general/access-behind-firewall.md)하는 방법에 관한 설명서에서 제공됩니다.

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 

가상 머신은 VM 내 에서만 액세스할 수 있는 잘 알려진 라우팅할 수 없는 IP 주소 ()를 사용 하는 [Azure 인스턴스 메타 데이터 서비스](instance-metadata-service.md) 끝점에 액세스할 수 있어야 합니다 `169.254.169.254` .  이 주소에 대한 로컬 HTTP 트래픽(예: X-Forwarded-For 헤더)을 변경하는 프록시 구성은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure disk encryption 문제 해결](disk-encryption-troubleshooting.md) 에 대 한 추가 단계를 참조 하세요.
- [휴지 상태의 Azure 데이터 암호화](../../security/fundamentals/encryption-atrest.md)
