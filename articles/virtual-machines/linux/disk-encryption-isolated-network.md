---
title: 격리된 네트워크에서 Azure 디스크 암호화
description: 이 문서에서는 Linux VM용 Microsoft Azure 디스크 암호화에 대한 문제 해결 팁을 제공합니다.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: aa0dc204a017e2d40eb3952a9ede0755127f8de2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970669"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>격리된 네트워크에서 Azure 디스크 암호화

방화벽, 프록시 요구 사항 또는 NSG(네트워크 보안 그룹) 설정으로 연결이 제한되면 필요한 작업을 수행할 수 있는 확장의 기능이 중단될 수 있습니다. 이로 인해 "VM에서 사용할 수 없는 확장 상태"와 같은 상태 메시지가 표시됩니다.

## <a name="package-management"></a>패키지 관리

Azure 디스크 암호화는 여러 구성 요소에 따라 달라지며, 아직 존재하지 않는 경우 일반적으로 ADE 사용 프로그램의 일부로 설치됩니다. 방화벽 뒤에 있거나 인터넷에서 격리된 경우 이러한 패키지를 로컬로 사전 설치하거나 사용할 수 있어야 합니다.

각 배포에 필요한 패키지는 다음과 같습니다. 지원되는 배포판 및 볼륨 유형의 전체 목록은 [지원되는 VM 및 운영 체제를](disk-encryption-overview.md#supported-vms-and-operating-systems)참조하십시오.

- **우분투 14.04, 16.04, 18.04**: lsscsi, psmisc, 에서, 크립토 셋업 빈, 파이썬 분할, 파이썬 식스, procps
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, 에서, 패치, 암호 해독, 암호 해독, 파이트, procps-ng, 유틸리티 리눅스
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypt, pyparted, 파이썬 식스
- **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, 에서, 패치, 암호 해독, 암호 해독, procps-ng, 유틸리티 리눅스
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuid, at, 패치, 암호 해독 - 재암호해독
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

Red Hat에서 프록시가 필요한 경우 subscription-manager와 yum이 올바르게 설정되었는지 확인해야 합니다. 자세한 내용은 [구독 관리자 및 yum 문제를 해결하는 방법](https://access.redhat.com/solutions/189533)을 참조하세요.  

패키지를 수동으로 설치하는 경우 새 버전이 출시될 때 수동으로 업그레이드해야 합니다.

## <a name="network-security-groups"></a>네트워크 보안 그룹
적용되는 모든 네트워크 보안 그룹 설정은 엔드포인트에서도 디스크 암호화에 대해 문서화된 네트워크 구성 필수 조건을 충족해야 합니다.  [Azure 디스크 암호화: 네트워킹 요구 사항](disk-encryption-overview.md#networking-requirements) 참조

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure AD를 사용한 Azure 디스크 암호화(이전 버전)

Azure [AD(이전 버전)와 함께 Azure 디스크 암호화를](disk-encryption-overview-aad.md)사용하는 경우 [Azure Active Directory 라이브러리를](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) 모든 배포판에 대해 수동으로 설치해야 합니다(위에 [나열된](#package-management)대로 배포판에 적합한 패키지 이외에).

[Azure AD 자격 증명](disk-encryption-linux-aad.md)에 암호화가 사용하도록 설정된 경우 대상 VM은 Azure Active Directory 엔드포인트 및 Key Vault 엔드포인트 모두에 대한 연결을 허용해야 합니다. 현재 Azure Active Directory 인증 엔드포인트는 [Office 365 URL 및 IP 주소 범위](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) 설명서의 56 및 59 섹션에서 유지 관리됩니다. Key Vault 지침은 [방화벽 뒤에 있는 Azure Key Vault에 액세스](../../key-vault/key-vault-access-behind-firewall.md)하는 방법에 관한 설명서에서 제공됩니다.

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 

가상 시스템은 VM 내에서만 액세스할 수 있는 잘 알려진 비라우팅 IP 주소()를`169.254.169.254`사용하는 [Azure 인스턴스 메타데이터 서비스](instance-metadata-service.md) 끝점에 액세스할 수 있어야 합니다.  이 주소로 로컬 HTTP 트래픽을 변경하는 프록시 구성(예: X-Forwarded-For 헤더 추가)은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- Azure 디스크 [암호화 문제 해결을](disk-encryption-troubleshooting.md) 위한 추가 단계 보기
- [휴지 상태의 Azure 데이터 암호화](../../security/fundamentals/encryption-atrest.md)
