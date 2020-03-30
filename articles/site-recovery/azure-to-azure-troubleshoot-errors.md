---
title: Azure 사이트 복구에서 Azure VM 복제 문제 해결
description: 재해 복구를 위해 Azure 가상 컴퓨터를 복제할 때 오류를 해결합니다.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: rochakm
ms.openlocfilehash: c131609a5622061e2ea49db422bc4e9da96666d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276685"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Azure-Azure VM 복제 오류 문제 해결

이 문서에서는 한 지역에서 다른 지역으로 Azure 가상 시스템(VM)을 복제하고 복구하는 동안 Azure 사이트 복구에서 일반적인 오류를 해결하는 방법을 설명합니다. 지원되는 구성에 대한 자세한 내용은 [Azure VM을 복제하기 위한 지원 매트릭스](site-recovery-support-matrix-azure-to-azure.md)를 참조하세요.

## <a name="azure-resource-quota-issues-error-code-150097"></a><a name="azure-resource-quota-issues-error-code-150097"></a>Azure 리소스 할당량 문제(오류 코드 150097)

구독이 재해 복구 지역으로 사용할 대상 지역에 Azure VM을 만들 수 있는지 확인합니다. 또한 구독에 필요한 크기의 VM을 만들기에 충분한 할당량이 있는지 확인합니다. 기본적으로 사이트 복구는 원본 VM 크기와 동일한 대상 VM 크기를 선택합니다. 일치하는 크기를 사용할 수 없는 경우 사이트 복구에서 사용 가능한 가장 가까운 크기를 자동으로 선택합니다.

원본 VM 구성을 지원하는 크기가 없는 경우 다음 메시지가 나타납니다.

> "가상 컴퓨터 *VmName에*대 한 복제를 사용할 수 없습니다."

### <a name="possible-causes"></a>가능한 원인

- 구독 ID는 대상 지역 위치에서 VM을 만들 수 없습니다.
- 구독 ID가 활성화되지 않았거나 할당량이 충분하지 않아 대상 지역 위치에서 특정 VM 크기를 만들 수 없습니다.
- 대상 영역 위치의 구독 ID에 대해 소스 VM의 네트워크 인터페이스 카드(NIC) 개수(2)와 일치하는 적절한 대상 VM 크기를 찾을 수 없습니다.

### <a name="fix-the-problem"></a>문제 해결

[Azure 청구 지원에](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) 문의하여 구독이 대상 위치에 필요한 크기의 VM을 만들 수 있도록 설정합니다. 그런 다음 실패한 작업을 다시 시도합니다.

대상 위치에 용량 제약 조건이 있는 경우 복제를 사용하지 않도록 설정합니다. 그런 다음 구독에 필요한 크기의 VM을 만들기에 충분한 할당량이 있는 다른 위치로 복제를 사용하도록 설정합니다.

## <a name="trusted-root-certificates-error-code-151066"></a><a name="trusted-root-certificates-error-code-151066"></a>신뢰할 수 있는 루트 인증서(오류 코드 151066)

VM에 신뢰할 수 있는 최신 루트 인증서가 모두 없는 경우 "복제 사용" 사이트 복구 작업이 실패할 수 있습니다. VM에서 사이트 복구 서비스 호출의 인증 및 권한 부여는 이러한 인증서 없이 실패합니다.

"복제 사용" 작업이 실패하면 다음 메시지가 나타납니다.

> "사이트 복구 구성이 실패했습니다."

### <a name="possible-cause"></a>가능한 원인

권한 부여 및 인증에 필요한 신뢰할 수 있는 루트 인증서가 가상 시스템에 없습니다.

### <a name="fix-the-problem"></a>문제 해결

#### <a name="windows"></a>Windows

Windows 운영 체제를 실행하는 VM의 경우 모든 신뢰할 수 있는 루트 인증서가 컴퓨터에 표시되도록 VM에 최신 Windows 업데이트를 설치합니다. 조직의 일반적인 Windows 업데이트 관리 또는 인증서 업데이트 관리 프로세스를 따라 VM에서 최신 루트 인증서 및 업데이트된 인증서 해지 목록을 가져옵니다.

연결이 끊어진 환경에 있는 경우 인증서를 받는 조직에서 표준 Windows 업데이트 프로세스를 따릅니다. 필요한 인증서가 VM에 없는 경우 보안상의 이유로 Site Recovery 서비스에 대한 호출이 실패합니다.

문제가 해결되었는지 확인하려면 VM의 브라우저에서 login.microsoftonline.com으로 이동합니다.

자세한 내용은 [신뢰할 수 있는 루트 및 허용되지 않는 인증서 구성을](https://technet.microsoft.com/library/dn265983.aspx)참조하십시오.

#### <a name="linux"></a>Linux

Linux 운영 체제 버전 배포자가 제공한 지침을 따라 최신 신뢰할 수 있는 루트 인증서와 VM의 최신 인증서 해지 목록을 확인합니다.

SUSE Linux는 인증서 목록을 유지하기 위해 기호 링크(또는 *심볼링크)를*사용하므로 다음 단계를 따르십시오.

1. 루트 사용자로 로그인합니다.

1. 이 명령을 실행하여 디렉터리를 변경합니다.

    **# CD /etc/ ssl/인증서**

1. 시만텍 루트 CA 인증서가 있는지 확인합니다.

    **# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. 시만텍 루트 CA 인증서를 찾을 수 없는 경우 다음 명령을 실행하여 파일을 다운로드합니다. 오류가 있는지 확인하고 네트워크 오류에 대한 권장 작업을 따릅니다.

    **# wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem**

1. 볼티모어 루트 CA 인증서가 있는지 확인합니다.

    **# ls Baltimore_CyberTrust_Root.pem**

1. 볼티모어 루트 CA 인증서를 찾을 수 없는 경우 이 명령을 실행하여 인증서를 다운로드합니다.

    **# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem**

1. DigiCert_Global_Root_CA 인증서가 있는지 확인합니다.

    **# ls DigiCert_Global_Root_CA.pem**

1. DigiCert_Global_Root_CA 찾을 수 없는 경우 다음 명령을 실행하여 인증서를 다운로드합니다.

    **# wgethttp://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt**

    **# openssl x509 -에서 DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem**

1. 다시 해시 스크립트를 실행하여 새로 다운로드한 인증서에 대한 인증서 주체 해시를 업데이트합니다.

    **# c_rehash**

1. 다음 명령을 실행하여 인증서에 대해 심볼링크로 주체 해시가 만들어졌는지 확인합니다.

    - 명령:

        **# ls -l | 그렙 볼티모어**

    - 출력:

        `lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem`

        `-rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem`

    - 명령:

        **# ls -l | 그레프 VeriSign_Class_3_Public_Primary_Certification_Authority_G5**

    - 출력:

        `-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

        `lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

    - 명령:

        **# ls -l | 그레프 DigiCert_Global_Root**

    - 출력:

        `lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem`

        `-rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem`

1. 파일 이름 b204d74a.0으로 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem 파일의 복사본을 만듭니다.

    **# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0**

1. 파일 이름 653b494a.0으로 Baltimore_CyberTrust_Root.pem 파일의 복사본을 만듭니다.

    **# cp Baltimore_CyberTrust_Root.pem 653b494a.0**

1. 파일 이름 3513523f.0으로 DigiCert_Global_Root_CA.pem 파일의 복사본을 만듭니다.

    **# cp DigiCert_Global_Root_CA.pem 3513523f.0**

1. 파일이 있는지 확인합니다.

    - 명령:

        **# ls -l 653b494a.0 b204d74a.0 3513523f.0**

    - 출력

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0`

        `-rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0`

        `-rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0`

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 또는 IP 범위에 대한 아웃바운드 연결(오류 코드 151037 또는 151072)

사이트 복구 복제가 작동하려면 VM에서 특정 URL에 대한 아웃바운드 연결이 필요합니다. VM이 방화벽 뒤에 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 이러한 문제 중 하나가 발생할 수 있습니다. URL을 통해 아웃바운드 액세스를 계속 지원하지만 IP 범위 허용 목록을 사용하는 것은 더 이상 지원되지 않습니다.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>문제 1: Site Recovery에 Azure 가상 머신을 등록하지 못했습니다(151195). </br>
- **가능한 원인** </br>
  - DNS 확인 실패로 인해 사이트 복구 끝점에 연결을 설정할 수 없습니다.
  - 가상 머신을 장애 조치(failover)했지만 DR 지역에서 DNS 서버에 도달할 수 없는 경우 재보호 기간에 자주 발생합니다.

- **해상도**
   - 사용자 지정 DNS를 사용하는 경우 재해 복구 지역에서 DNS 서버에 액세스할 수 있는지 확인합니다. 사용자 지정 DNS가 있는지 확인하려면 VM > 재해 복구 네트워크 > DNS 서버로 이동합니다. 가상 머신에서 DNS 서버에 액세스를 시도합니다. 액세스할 수 없는 경우 DNS 서버를 장애 조치(failover)하거나 DR 네트워크와 DNS 사이를 연결하여 액세스 가능하게 만듭니다.

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>문제 2: Site Recovery 구성이 실패했습니다(151196).
- **가능한 원인** </br>
  - Office 365 인증에 연결하여 IP4 엔드포인트를 식별할 수 없습니다.

- **해상도**
  - Azure Site Recovery는 인증을 위해 Office 365 IP 범위에 액세스할 수 있어야 합니다.
    Azure 네트워크 보안 그룹(NSG) 규칙/방화벽 프록시를 사용하여 VM에서 아웃바운드 네트워크 연결을 제어하는 경우 [AAD(Azure Active Directory) 서비스 태그](../virtual-network/security-overview.md#service-tags) 기반 NSG 규칙을 사용하여 AAD에 대한 액세스를 허용하는 지 확인하십시오. 더 이상 IP 주소 기반 NSG 규칙을 지원하지 않습니다.


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>문제 3: Site Recovery 구성이 실패했습니다(151197).
- **가능한 원인** </br>
  - Azure Site Recovery 서비스 엔드포인트에 연결할 수 없습니다.

- **해상도**
  - Azure 네트워크 보안 그룹(NSG) 규칙/방화벽 프록시를 사용하여 VM에서 아웃바운드 네트워크 연결을 제어하는 경우 서비스 태그를 사용해야 합니다. ASR(Azure 사이트 복구)에 대한 NSG를 통해 IP 주소 허용 목록을 사용하는 것은 더 이상 지원되지 않습니다.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>문제 4: 네트워크 트래픽이 온-프레미스 프록시 서버를 통과할 때 A2A 복제가 실패했습니다(151072).
 - **가능한 원인** </br>
   - 사용자 지정 프록시 설정이 유효하지 않으며 ASR Mobility Service 에이전트가 IE에서 프록시 설정을 자동으로 검색하지 않았습니다.


 - **해상도**
   1.    Mobility Service 에이전트는 Windows의 경우 IE에서 Linux의 경우 /etc/environment에서 프록시 설정을 검색합니다.
   2.  ASR Mobility Service에 대해서만 프록시를 설정하려는 경우 다음 위치에 있는 ProxyInfo.conf에서 프록시 세부 정보를 제공할 수 있습니다.</br>
       - ***Linux***에서 ``/usr/local/InMage/config/``
       - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``***윈도우에서***
   3.    ProxyInfo.conf에는 다음 INI 형식의 프록시 설정이 있어야 합니다.</br>
                   *[프록시]*</br>
                   *Address=http://1.2.3.4*</br>
                   *Port=567*</br>
   4. ASR Mobility Service 에이전트는 ***인증되지 않은 프록시***만 지원합니다.


### <a name="fix-the-problem"></a>문제 해결

허용 목록에 [필요한 URL을](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 추가하려면 네트워킹 지침 [문서의](site-recovery-azure-to-azure-networking-guidance.md)단계를 따릅니다.


### <a name="more-information"></a>자세한 정보

필요한 [URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 또는 [필수 IP 범위를](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)지정하려면 [Azure에서 Azure 복제에 대한 네트워킹 정보의](site-recovery-azure-to-azure-networking-guidance.md)지침을 따릅니다.

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>컴퓨터에서 디스크를 찾을 수 없음(오류 코드 150039)

VM에 연결된 새 디스크는 초기화되어야 합니다. 디스크를 찾을 수 없는 경우 다음 메시지가 나타납니다.

> "논리 적 단위 번호 *LUN* *Value를* 가진 Azure 데이터 디스크 *DiskName* *DiskURI는* 동일한 LUN 값을 가진 VM 내에서 보고되는 해당 디스크에 매핑되지 않았습니다.

### <a name="possible-causes"></a>가능한 원인

- 새 데이터 디스크가 VM에 연결되었지만 초기화되지 않았습니다.
- VM 내부의 데이터 디스크가 디스크가 VM에 연결된 LUN(논리 단위 번호) 값을 올바르게 보고하지 않습니다.

### <a name="fix-the-problem"></a>문제 해결

데이터 디스크가 초기화되었는지 확인한 다음 작업을 다시 시도합니다.

- **Windows**: [새 디스크를 연결하고 초기화합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)

- **리눅스**: [리눅스에서 새로운 데이터 디스크를 초기화](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

문제가 계속되면 지원에 문의하세요.

## <a name="one-or-more-disks-are-available-for-protection-error-code-153039"></a>하나 이상의 디스크를 보호할 수 있습니다(오류 코드 153039).

### <a name="possible-causes"></a>가능한 원인

- 보호 후 하나 이상의 디스크가 최근에 가상 시스템에 추가되었습니다.
- 가상 컴퓨터를 보호한 후 하나 이상의 디스크가 초기화되었습니다.

### <a name="fix-the-problem"></a>문제 해결

VM의 복제 상태를 다시 정상 상태로 만들려면 디스크를 보호하거나 경고를 해제하도록 선택할 수 있습니다.

#### <a name="to-protect-the-disks"></a>디스크를 보호하려면

1. **복제된 항목** > *VM 이름* > **디스크로 이동합니다.**
1. 보호되지 않은 디스크를 선택한 다음 **복제 활성화를**선택합니다.

    ![VM 디스크에서 복제 사용](./media/azure-to-azure-troubleshoot-errors/add-disk.png)

#### <a name="to-dismiss-the-warning"></a>경고를 해제하려면

1. 복제된 항목 > *VM 이름으로* **이동합니다.**
1. **개요** 섹션에서 경고를 선택한 다음 **확인을**선택합니다.

    ![새 디스크 경고 해제](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)

## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information-error-code-150225"></a>정보(오류 코드 150225)로 완료된 볼트에서 가상 컴퓨터를 제거합니다.

가상 컴퓨터를 보호하면 사이트 복구는 원본 가상 시스템에 대한 몇 가지 링크를 만듭니다. 보호를 제거하거나 복제를 사용하지 않도록 설정하면 사이트 복구는 정리 작업의 일부로 이러한 링크를 제거합니다. 가상 시스템에 리소스 잠금이 있는 경우 정리 작업이 정보로 완료됩니다. 이 정보는 가상 시스템이 복구 서비스 자격 증명 모음에서 제거되었지만 원본 컴퓨터에서 오래된 링크 중 일부를 정리할 수 없다고 말합니다.

이 가상 컴퓨터를 다시 보호하려는 경우 이 경고를 무시할 수 있습니다. 그러나 나중에 이 가상 컴퓨터를 보호해야 하는 경우 "문제 해결" 단계를 수행하여 링크를 정리합니다.

> [!WARNING]
> 정리를 하지 않는 경우:
>
> - 복구 서비스 자격 증명 모음을 통해 복제를 사용하도록 설정하면 가상 시스템이 나열되지 않습니다.
> - **가상 시스템** > **설정** > **재해 복구를**사용하여 VM을 보호하려고 하면 "VM의 기존 오래된 리소스 링크로 인해 복제를 사용할 수 없습니다"라는 메시지와 함께 작업이 실패합니다.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
> 사이트 복구는 이러한 단계를 수행하는 동안 원본 가상 컴퓨터를 삭제하거나 영향을 주지 않습니다.

1. VM 또는 VM 리소스 그룹에서 잠금을 제거합니다. 예를 들어 다음 이미지에서 "MoveDemo"라는 VM의 리소스 잠금을 삭제해야 합니다.

    ![VM에서 잠금 제거](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 스크립트를 다운로드하여 [오래된 사이트 복구 구성을 제거합니다.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. 정리-부실-asr-config-Azure-VM.ps1이라고 하는 스크립트를 실행합니다. 구독 ID, VM 리소스 그룹 및 VM 이름을 매개 변수로 공급합니다.
1. Azure 자격 증명을 묻는 메시지가 나오면 이를 제공합니다. 그런 다음 오류가 없는 스크립트가 실행되는지 확인합니다.

## <a name="replication-cant-be-enabled-because-of-stale-resource-links-on-the-vm-error-code-150226"></a>VM의 오래된 리소스 링크(오류 코드 150226)로 인해 복제를 사용할 수 없습니다.

### <a name="possible-cause"></a>가능한 원인

가상 시스템에 이전 사이트 복구 보호의 오래된 구성이 있습니다.

사이트 복구를 사용하여 Azure VM에 대한 복제를 사용하도록 설정한 경우 Azure VM에서 오래된 구성이 발생할 수 있습니다.

- 복제를 비활성화했지만 원본 VM에 리소스 잠금이 있었습니다.
- VM에서 복제를 명시적으로 사용하지 않도록 설정하지 않고 사이트 복구 자격 증명 모음을 삭제했습니다.
- VM에서 복제를 명시적으로 사용하지 않도록 설정하지 않고 사이트 복구 자격 증명 모음이 포함된 리소스 그룹을 삭제했습니다.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
> 사이트 복구는 이러한 단계를 수행하는 동안 원본 가상 컴퓨터를 삭제하거나 영향을 주지 않습니다.

1. VM 또는 VM 리소스 그룹에서 잠금을 제거합니다. 예를 들어 다음 이미지에서 "MoveDemo"라는 VM의 리소스 잠금을 삭제해야 합니다.

    ![VM에서 잠금 제거](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 스크립트를 다운로드하여 [오래된 사이트 복구 구성을 제거합니다.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. 정리-부실-asr-config-Azure-VM.ps1이라고 하는 스크립트를 실행합니다. 구독 ID, VM 리소스 그룹 및 VM 이름을 매개 변수로 공급합니다.
1. Azure 자격 증명을 묻는 메시지가 나오면 이를 제공합니다. 그런 다음 오류가 없는 스크립트가 실행되는지 확인합니다.

## <a name="unable-to-see-the-azure-vm-or-resource-group-for-the-selection-in-the-enable-replication-job"></a>"복제 활성화" 작업에서 선택 영역에 대한 Azure VM 또는 리소스 그룹을 볼 수 없습니다.

### <a name="cause-1-the-resource-group-and-source-virtual-machine-are-in-different-locations"></a>원인 1: 리소스 그룹 및 소스 가상 시스템이 서로 다른 위치에 있습니다.

사이트 복구는 현재 원본 지역 리소스 그룹과 가상 시스템이 동일한 위치에 있어야 합니다. 그렇지 않은 경우 보호를 적용하려고 할 때 가상 컴퓨터 또는 리소스 그룹을 찾을 수 없습니다.

해결 방법을 사용하면 복구 서비스 자격 증명 모음 대신 VM에서 복제를 사용하도록 설정할 수 있습니다. 원본 **VM** > **속성** > **재해 복구로** 이동하여 복제를 사용하도록 설정합니다.

### <a name="cause-2-the-resource-group-is-not-part-of-the-selected-subscription"></a>원인 2: 리소스 그룹이 선택한 구독의 일부가 아닙니다.

리소스 그룹이 선택한 구독의 일부가 아닌 경우 보호 시 리소스 그룹을 찾지 못할 수 있습니다. 리소스 그룹이 사용 중 구독에 속하는지 확인합니다.

### <a name="cause-3-stale-configuration"></a>원인 3: 부실 구성

Azure VM에 오래된 사이트 복구 구성이 남아 있는 경우 복제를 위해 사용하도록 설정하려는 VM이 표시되지 않을 수 있습니다. 이 조건은 사이트 복구를 사용하여 Azure VM에 대한 복제를 사용하도록 설정한 다음 다음과 같은 경우에 발생할 수 있습니다.

- VM에서 복제를 명시적으로 사용하지 않도록 설정하지 않고 사이트 복구 자격 증명 모음을 삭제했습니다.
- VM에서 복제를 명시적으로 사용하지 않도록 설정하지 않고 사이트 복구 자격 증명 모음이 포함된 리소스 그룹을 삭제했습니다.
- 복제를 비활성화했지만 원본 VM에 리소스 잠금이 있었습니다.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
> 이 섹션에서 언급한 스크립트를 사용하기 전에 "AzureRM.Resources" 모듈을 업데이트해야 합니다.  사이트 복구는 이러한 단계를 수행하는 동안 원본 가상 컴퓨터를 삭제하거나 영향을 주지 않습니다.

1. VM 또는 VM 리소스 그룹에서 잠금을 제거합니다(있는 경우). 예를 들어 다음 이미지에서 "MoveDemo"라는 VM의 리소스 잠금을 삭제해야 합니다.

    ![VM에서 잠금 제거](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)

1. 스크립트를 다운로드하여 [오래된 사이트 복구 구성을 제거합니다.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. 정리-부실-asr-config-Azure-VM.ps1이라고 하는 스크립트를 실행합니다. 구독 ID, VM 리소스 그룹 및 VM 이름을 매개 변수로 공급합니다.
1. Azure 자격 증명을 묻는 메시지가 나오면 이를 제공합니다. 그런 다음 오류가 없는 스크립트가 실행되는지 확인합니다.

## <a name="unable-to-select-a-virtual-machine-for-protection"></a>보호를 위해 가상 컴퓨터를 선택할 수 없음

### <a name="cause-1-the-virtual-machine-has-an-extension-installed-in-a-failed-or-unresponsive-state"></a>원인 1: 가상 시스템에 장애가 있거나 응답하지 않는 상태에 확장이 설치되어 있습니다.

가상 **컴퓨터** > **설정** > **확장으로** 이동하여 실패한 상태의 확장을 확인합니다. 실패한 확장을 제거한 다음 가상 컴퓨터를 보호하기 위해 다시 시도합니다.

### <a name="cause-2-the-vms-provisioning-state-is-not-valid"></a>원인 2: VM의 프로비저닝 상태가 잘못되었습니다.

이 문서의 후반부에서 [VM의 프로비저닝 상태가 유효하지 않은](#the-vms-provisioning-state-is-not-valid-error-code-150019)문제 해결 단계를 참조하십시오.

## <a name="the-vms-provisioning-state-is-not-valid-error-code-150019"></a>VM의 프로비저닝 상태가 잘못되었습니다(오류 코드 150019).

VM에서 복제를 사용하려면 해당 프로비저닝 상태가 **성공되어야**합니다. 다음 단계에 따라 프로비저닝 상태를 확인합니다.

1. Azure 포털에서 **모든 서비스에서** **리소스 탐색기를** 선택합니다.
1. **구독** 목록을 확장하고 본인의 구독을 선택합니다.
1. **리소스 그룹** 목록을 확장하고 VM의 리소스 그룹을 선택합니다.
1. **리소스** 목록을 확장하고 VM을 선택합니다.
1. 오른쪽에 있는 인스턴스 보기의 **프로비저닝상태** 필드를 확인합니다.

### <a name="fix-the-problem"></a>문제 해결

- **provisioningState**가 **실패**이면 지원 서비스에 문제 해결을 위한 세부 정보를 문의합니다.
- **프로비저닝State가** **업데이트**중인 경우 다른 확장이 배포될 수 있습니다. VM에 진행 중인 작업이 있는지 확인하고 완료될 때까지 기다린 다음 실패한 사이트 복구 "복제 활성화" 작업을 다시 시도합니다.

## <a name="unable-to-select-target-vm-network-selection-tab-is-unavailable"></a>대상 VM을 선택할 수 없음(네트워크 선택 탭을 사용할 수 없음)

### <a name="cause-1-your-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>원인 1: VM이 대상 네트워크에 이미 매핑된 네트워크에 연결됩니다.

원본 VM이 가상 네트워크의 일부이고 동일한 가상 네트워크의 다른 VM이 대상 리소스 그룹의 네트워크와 이미 매핑된 경우 네트워크 선택 드롭다운 목록 상자를 기본적으로 사용할 수 없습니다(흐리게 표시).

![네트워크 선택 목록을 사용할 수 없음](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

### <a name="cause-2-you-previously-protected-the-vm-by-using-site-recovery-and-then-you-disabled-the-replication"></a>원인 2: 사이트 복구를 사용 하 여 이전에 VM을 보호 하 고 복제를 사용 하지 않도록 설정 했습니다.

VM의 복제를 사용하지 않도록 설정해도 네트워크 매핑은 삭제되지 않습니다. VM이 보호된 복구 서비스 자격 증명 모음에서 매핑을 삭제해야 합니다. 복구 *서비스 볼트* > **사이트 복구 인프라** > **네트워크 매핑으로**이동합니다.

![네트워크 매핑 삭제](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)

재해 복구 설정 중에 구성된 대상 네트워크는 VM이 보호된 후 초기 설정 후 변경할 수 있습니다.

![네트워크 매핑 수정](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)

네트워크 매핑을 변경하면 동일한 네트워크 매핑을 사용하는 모든 보호된 VM에 영향을 줍니다.

## <a name="com-or-volume-shadow-copy-service-error-error-code-151025"></a>COM+ 또는 볼륨 섀도우 복사 서비스 오류(오류 코드 151025)

이 오류가 발생하면 다음 메시지가 나타납니다.

> "사이트 복구 확장을 설치하지 못했습니다."

### <a name="possible-causes"></a>가능한 원인

- COM+ 시스템 응용 프로그램 서비스가 비활성화되어 있습니다.
- 볼륨 그림자 복사 서비스가 비활성화되었습니다.

### <a name="fix-the-problem"></a>문제 해결

COM+ 시스템 응용 프로그램 및 볼륨 섀도 복사 서비스를 자동 또는 수동 시작 모드로 설정합니다.

1. Windows에서 서비스 콘솔을 엽니다.
1. COM+ 시스템 응용 프로그램 및 볼륨 섀도 복사본 서비스가 **시작 유형으로** **비활성화되지** 않도록 설정되지 않았는지 확인합니다.

    ![COM+ 시스템 응용 프로그램 및 볼륨 섀도 복사 서비스의 시작 유형 확인](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>지원되지 않는 관리 디스크 크기(오류 코드 150172)

이 오류가 발생하면 다음 메시지가 나타납니다.

> "최소 지원 되는 크기 1024 MB 보다 작은 *DiskSize)*를*가진 *DiskName을* 가지고 있으므로 가상 시스템에 대해 보호를 사용할 수 없습니다."

### <a name="possible-cause"></a>가능한 원인

디스크는 지원되는 크기인 1024MB보다 작습니다.

### <a name="fix-the-problem"></a>문제 해결

디스크 크기가 지원되는 크기 범위 내에 있는지 확인한 다음 작업을 다시 시도합니다.

## <a name="protection-was-not-enabled-because-the-grub-configuration-includes-the-device-name-instead-of-the-uuid-error-code-151126"></a><a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>GRUB 구성에 UUID 대신 장치 이름이 포함되어 있으므로 보호가 활성화되지 않았습니다(오류 코드 151126).

### <a name="possible-cause"></a>가능한 원인

Linux GRUB 구성 파일(/boot/grub/menu.lst"), /boot/grub/grub.cfg, /boot/grub2/grub.cfg 또는 /etc/default/grub)은 *루트* 및 *재개* 매개 변수에 대한 UUID 값 대신 실제 장치 이름을 지정할 수 있습니다. 장치 이름이 변경될 수 있으므로 사이트 복구에는 UUID가 필요합니다. 다시 시작하면 VM이 장애 조치(failover)에서 동일한 이름을 사용하지 않아 문제가 발생할 수 있습니다.

다음 예제는 GRUB 파일에서 필요한 UUID 대신 장치 이름(굵게 표시)이 표시되는 줄입니다.

- 파일 /부팅/grub2/grub.cfg

  > linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts

- 파일 : /boot/grub/menu.lst

  > 커널 /부팅/vmlinuz-3.0.0.101-63-기본 **루트=/dev/sda2** **이력서=/dev/sda1** 스플래시=무음 크래시커넬=256M-:128M 쇼옵트 vga=0x314


### <a name="fix-the-problem"></a>문제 해결

각 장치 이름을 해당 UUID로 바꿉니다.

1. 명령 **blkid** ***장치 이름을***실행 하여 장치의 UUID를 찾을 수 있습니다. 예를 들어:

    ```
    blkid /dev/sda1
    /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
    blkid /dev/sda2
    /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 장치 이름을 UUID 형식으로 UUID로 바꾸고 **uUID**=*UUID* 를 **다시 시작합니다.**=*UUID* 예를 들어 교체 후 /boot/grub/menu.lst(이전 설명)의 줄은 다음과 같습니다.

    > kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314

1. 보호를 다시 시도합니다.

## <a name="enable-protection-failed-because-the-device-mentioned-in-the-grub-configuration-doesnt-exist-error-code-151124"></a>GRUB 구성에 언급된 장치가 존재하지 않기 때문에 보호가 실패했습니다(오류 코드 151124).

### <a name="possible-cause"></a>가능한 원인

GRUB 구성 파일(/boot/grub/menu.lst, /boot/grub/grub.cfg, /boot/grub2/grub.cfg) 또는 /etc/default/grub)에는 *rd.lvm.lv* 또는 *rd_LVM_LV*매개 변수가 포함될 수 있습니다. 이러한 매개 변수는 부팅 시 검색할 LVM(논리 볼륨 관리자) 장치를 식별합니다. 이러한 LVM 장치가 없으면 보호된 시스템 자체가 부팅되지 않고 부팅 프로세스에 갇혀 있습니다. 장애 조치 VM에서도 동일한 문제가 표시됩니다. 다음은 몇 가지 예입니다.

- 파일 : RHEL7에서 /boot/grub2/grub.cfg:

    > linux16 /vmlinuz-3.10.0-957.el7.x86_64 루트=/dev/mapper/rhel_mup-rhel7u6-루트 ro 크래커넬=128M\@64M **rd.lvm.lvm.lvg/루트 rd.lvm.lvm=rootvg/rootvg/rootrhg/sgb** 조용한 LANG=en_US. UTF-8

- 파일 : RHEL7에 /etc / 기본값 / grub :

    > GRUB_CMDLINE_LINUX="크래시커넬=자동 **rd.lvm.lvm=rootvg/root rd.lvm.lvm.lv=rootvg/스왑** rhgb 조용한"

- 파일 : RHEL6의 /boot/grub/menu.lst:

    > 커널 /vmlinuz-2.6.32-754.el6.x86_64 ro 루트=UUID=36d8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US. UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 크래셔커널=자동 **rd_LVM_LV =rootvg/lv_root** 키보드유형=PC KEYTABLE=우리 **rd_LVM_LV=rootvg/lv_swap** rd_NO_DM rhgb 조용한

각 예제에서 굵게 표시된 부분은 GRUB이 볼륨 그룹 "rootvg"에서 "루트" 및 "스왑"이라는 이름으로 두 개의 LVM 장치를 검색해야 한다는 것을 보여 주었습니다.

### <a name="fix-the-problem"></a>문제 해결

LVM 장치가 없는 경우 GRUB 구성 파일에서 해당 매개 변수를 만들거나 제거합니다. 그런 다음 다시 시도하여 보호를 사용하도록 설정합니다.

## <a name="a-site-recovery-mobility-service-update-finished-with-warnings-error-code-151083"></a>경고로 완료된 사이트 복구 모빌리티 서비스 업데이트(오류 코드 151083)

사이트 복구 이동성 서비스에는 필터 드라이버라고 하는 많은 구성 요소가 있습니다. 필터 드라이버는 시스템을 다시 시작하는 동안에만 시스템 메모리에 로드됩니다. 모빌리티 서비스 업데이트에 필터 드라이버 변경 사항이 포함될 때마다 컴퓨터가 업데이트되지만 일부 수정 프로그램에 다시 시작해야 한다는 경고가 계속 표시됩니다. 필터 드라이버 수정은 새 필터 드라이버가 로드된 경우에만 적용될 수 있으며, 이는 다시 시작할 때만 발생하기 때문에 경고가 나타납니다.

> [!NOTE]
> 단지 경고일 뿐입니다. 기존 복제는 새 에이전트 업데이트 후에도 계속 작동합니다. 새 필터 드라이버의 이점을 원할 때마다 다시 시작하도록 선택할 수 있지만 다시 시작하지 않으면 이전 필터 드라이버가 계속 작동합니다.
>
> 필터 드라이버 외에도 모빌리티 서비스 업데이트의 다른 개선 사항 및 수정 사항의 이점은 다시 시작하지 않고도 적용됩니다.

## <a name="protection-couldnt-be-enabled-because-the-replica-managed-disk-already-exists-without-expected-tags-in-the-target-resource-group-error-code-150161"></a>대상 리소스 그룹(오류 코드 150161)에 예상 태그가 없는 복제본 관리 디스크가 이미 있으므로 보호를 사용할 수 없습니다.

### <a name="possible-cause"></a>가능한 원인

이 문제는 가상 컴퓨터를 이전에 보호 하 고 복제를 사용 하지 않도록 설정 된 경우 복제 디스크를 청소 하지 않은 경우에 발생할 수 있습니다.

### <a name="fix-the-problem"></a>문제 해결

오류 메시지에서 식별된 복제본 디스크를 삭제하고 실패한 보호 작업을 다시 시도합니다.

## <a name="next-steps"></a>다음 단계

[Azure 가상 머신 복제](site-recovery-replicate-azure-to-azure.md)
