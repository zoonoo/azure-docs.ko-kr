---
title: Azure 사이트 복구에서 Azure VM 복제 문제 해결
description: 재해 복구를 위해 Azure 가상 컴퓨터를 복제할 때 오류를 해결합니다.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: 243fea8fae071368a91bf482190442f15c372fc1
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271304"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Azure-Azure VM 복제 오류 문제 해결

이 문서에서는 VM(Azure 가상 시스템)을 한 리전에서 다른 리전으로 복제하고 복구하는 동안 Azure 사이트 복구에서 일반적인 오류를 해결하는 방법을 설명합니다. 지원되는 구성에 대한 자세한 내용은 [Azure VM을 복제하기 위한 지원 매트릭스](azure-to-azure-support-matrix.md)를 참조하세요.

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 리소스 할당량 문제(오류 코드 150097)

DR(재해 복구) 지역으로 사용할 대상 지역에 Azure VM을 만들 수 있도록 구독이 활성화되어 있는지 확인합니다. 구독에는 필요한 크기의 VM을 만들기 에 충분한 할당량이 필요합니다. 기본적으로 사이트 복구는 원본 VM 크기와 동일한 대상 VM 크기를 선택합니다. 일치하는 크기를 사용할 수 없는 경우 사이트 복구에서 사용 가능한 가장 가까운 크기를 자동으로 선택합니다.

원본 VM 구성을 지원하는 크기가 없는 경우 다음 메시지가 표시됩니다.

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>가능한 원인

- 구독 ID는 대상 지역 위치에서 VM을 만들 수 없습니다.
- 구독 ID가 활성화되지 않았거나 할당량이 충분하지 않아 대상 지역 위치에서 특정 VM 크기를 만들 수 없습니다.
- 대상 영역 위치의 구독 ID에 대해 소스 VM의 네트워크 인터페이스 카드(NIC) 개수(2)와 일치하는 적절한 대상 VM 크기를 찾을 수 없습니다.

### <a name="fix-the-problem"></a>문제 해결

[Azure 청구 지원에](/azure/azure-portal/supportability/resource-manager-core-quotas-request) 문의하여 구독이 대상 위치에 필요한 크기의 VM을 만들 수 있도록 설정합니다. 그런 다음 실패한 작업을 다시 시도합니다.

대상 위치에 용량 제약 조건이 있는 경우 해당 위치에 대한 복제를 사용하지 않도록 설정합니다. 그런 다음 구독에 필요한 크기의 VM을 만들기에 충분한 할당량이 있는 다른 위치로 복제를 사용하도록 설정합니다.

## <a name="trusted-root-certificates-error-code-151066"></a>신뢰할 수 있는 루트 인증서(오류 코드 151066)

VM에 신뢰할 수 있는 최신 루트 인증서가 모두 없는 경우 사이트 복구에 대한 복제를 사용하도록 설정하는 작업이 실패할 수 있습니다. VM에서 사이트 복구 서비스 호출의 인증 및 권한 부여는 이러한 인증서 없이 실패합니다.

사용 복제 작업이 실패하면 다음 메시지가 표시됩니다.

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>가능한 원인

권한 부여 및 인증에 필요한 신뢰할 수 있는 루트 인증서가 가상 시스템에 없습니다.

### <a name="fix-the-problem"></a>문제 해결

#### <a name="windows"></a>Windows

Windows 운영 체제를 실행하는 VM의 경우 모든 신뢰할 수 있는 루트 인증서가 VM에 표시되도록 최신 Windows 업데이트를 설치합니다. 조직의 일반적인 Windows 업데이트 관리 또는 인증서 업데이트 관리 프로세스를 따라 VM에서 최신 루트 인증서 및 업데이트된 인증서 해지 목록을 가져옵니다.

- 연결이 끊어진 환경에 있는 경우 인증서를 받는 조직에서 표준 Windows 업데이트 프로세스를 따릅니다.
- 필요한 인증서가 VM에 없는 경우 보안상의 이유로 Site Recovery 서비스에 대한 호출이 실패합니다.

문제가 해결되었는지 확인하려면 VM의 `login.microsoftonline.com` 브라우저로 이동하십시오.

자세한 내용은 [신뢰할 수 있는 루트 및 허용되지 않는 인증서 구성을](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11))참조하십시오.

#### <a name="linux"></a>Linux

Linux 운영 체제 버전 배포자가 제공한 지침을 따라 최신 신뢰할 수 있는 루트 인증서와 VM의 최신 인증서 해지 목록을 확인합니다.

SUSE Linux는 인증서 목록을 유지하기 위해 기호 링크 또는 심볼링크를 사용하므로 다음 단계를 따르십시오.

1. **루트** 사용자로 로그인합니다. 해시 기호 ()는`#`기본 명령 프롬프트입니다.

1. 디렉터리를 변경하려면 다음 명령을 실행합니다.

   `cd /etc/ssl/certs`

1. 시만텍 루트 CA 인증서가 있는지 확인합니다.

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - 시만텍 루트 CA 인증서를 찾을 수 없는 경우 다음 명령을 실행하여 파일을 다운로드합니다. 오류가 있는지 확인하고 네트워크 오류에 대한 권장 작업을 따릅니다.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. 볼티모어 루트 CA 인증서가 있는지 확인합니다.

   `ls Baltimore_CyberTrust_Root.pem`

   - 볼티모어 루트 CA 인증서를 찾을 수 없는 경우 이 명령을 실행하여 인증서를 다운로드합니다.

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. DigiCert_Global_Root_CA 인증서가 있는지 확인합니다.

   `ls DigiCert_Global_Root_CA.pem`

    - DigiCert_Global_Root_CA 찾을 수 없는 경우 다음 명령을 실행하여 인증서를 다운로드합니다.

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. 새로 다운로드한 인증서에 대한 인증서 주체 해시를 업데이트하려면 다시 해시 스크립트를 실행합니다.

   `c_rehash`

1. 인증서에 대해 심볼링크로 주체 해시가 만들어졌는지 확인하려면 다음 명령을 실행합니다.

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. 파일 이름 _b204d74a.0으로_ _VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_ 파일의 복사본을 만듭니다.

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. 파일 이름 _653b494a.0으로_ _Baltimore_CyberTrust_Root.pem_ 파일의 복사본을 만듭니다.

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. 파일 이름 _3513523f.0으로_ _파일 DigiCert_Global_Root_CA.pem의_ 복사본을 만듭니다.

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. 파일이 있는지 확인합니다.

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>아웃바운드 URL 또는 IP 범위(오류 코드 151037 또는 151072)

사이트 복구 복제가 작동하려면 VM에서 특정 URL에 대한 아웃바운드 연결이 필요합니다. VM이 방화벽 뒤에 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 이러한 문제 중 하나가 발생할 수 있습니다. URL을 통해 아웃바운드 액세스를 계속 지원하지만 IP 범위 허용 목록을 사용하는 것은 더 이상 지원되지 않습니다.

### <a name="issue-1-failed-to-register-azure-vm-with-site-recovery-151195"></a>문제 1: 사이트 복구에 Azure VM을 등록하지 못했습니다(151195)

#### <a name="possible-causes"></a>가능한 원인

- 도메인 이름 시스템(DNS) 해결 실패로 인해 사이트 복구 끝점에 대한 연결을 시작할 수 없습니다.
- 이 문제는 가상 시스템에 장애가 있지만 DNS 서버가 DR(재해 복구) 지역에서 연결할 수 없는 경우 다시 보호하는 동안 더 일반적입니다.

#### <a name="fix-the-problem"></a>문제 해결

사용자 지정 DNS를 사용하는 경우 재해 복구 지역에서 DNS 서버에 액세스할 수 있는지 확인합니다.

VM이 사용자 지정 DNS 설정을 사용하는지 확인하려면 다음을 수행하십시오.

1. **가상 컴퓨터를** 열고 VM을 선택합니다.
1. VM **설정으로** 이동하여 **네트워킹을 선택합니다.**
1. **가상 네트워크/서브넷에서**링크를 선택하여 가상 네트워크의 리소스 페이지를 엽니다.
1. **설정으로** 이동하여 **DNS 서버를 선택합니다.**

가상 컴퓨터에서 DNS 서버에 액세스해 봅을 사용해 보십시오. DNS 서버에 액세스할 수 없는 경우 DNS 서버에서 장애 또는 DR 네트워크와 DNS 간의 사이트 라인을 만들어 액세스할 수 있도록 합니다.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-오류.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>문제 2: Site Recovery 구성이 실패했습니다(151196).

#### <a name="possible-cause"></a>가능한 원인

Office 365 인증 및 ID IP4 끝점에 연결을 설정할 수 없습니다.

#### <a name="fix-the-problem"></a>문제 해결

Azure 사이트 복구인증을 위해 Office 365 IP 범위에 액세스해야 했습니다.
Azure 네트워크 보안 그룹(NSG) 규칙/방화벽 프록시를 사용하여 VM에서 아웃바운드 네트워크 연결을 제어하는 경우 [AAD(Azure Active Directory) 서비스 태그](/azure/virtual-network/security-overview#service-tags) 기반 NSG 규칙을 사용하여 AAD에 대한 액세스를 허용하는 지 확인합니다. 더 이상 IP 주소 기반 NSG 규칙을 지원하지 않습니다.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>문제 3: Site Recovery 구성이 실패했습니다(151197).

#### <a name="possible-cause"></a>가능한 원인

Azure 사이트 복구 서비스 끝점에 대한 연결을 설정할 수 없습니다.

#### <a name="fix-the-problem"></a>문제 해결

Azure 네트워크 보안 그룹(NSG) 규칙/방화벽 프록시를 사용하여 VM에서 아웃바운드 네트워크 연결을 제어하는 경우 서비스 태그를 사용해야 합니다. Azure 사이트 복구에 대한 NSG를 통해 허용 IP 주소 목록을 사용하는 것은 더 이상 지원되지 않습니다.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>문제 4: 네트워크 트래픽이 온-프레미스 프록시 서버(151072)를 사용할 때 복제가 실패합니다.

#### <a name="possible-cause"></a>가능한 원인

사용자 지정 프록시 설정이 잘못되었으며 Mobility 서비스 에이전트가 IE(인터넷 탐색기)에서 프록시 설정을 자동으로 검색하지 않았습니다.

#### <a name="fix-the-problem"></a>문제 해결

1. 모빌리티 서비스 에이전트는 Windows 및 `/etc/environment` Linux에서 IE의 프록시 설정을 검색합니다.
1. 모빌리티 서비스에 대해서만 프록시를 설정하려는 경우 다음 위치에 있는 _ProxyInfo.conf에서_ 프록시 세부 정보를 제공할 수 있습니다.

   - **리눅스**:`/usr/local/InMage/config/`
   - **윈도우**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo.conf는_ 다음 _INI_ 형식의 프록시 설정이 있어야 합니다.

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> 모빌리티 서비스 에이전트는 **인증되지 않은 프록시만**지원합니다.

### <a name="more-information"></a>자세한 정보

[필요한 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 또는 필요한 IP 범위를 지정하려면 [Azure에서 Azure 복제에](azure-to-azure-about-networking.md)대한 네트워킹 정보의 지침을 [따릅니다.](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

## <a name="disk-not-found-in-vm-error-code-150039"></a>VM에서 디스크를 찾을 수 없습니다(오류 코드 150039)

VM에 연결된 새 디스크는 초기화되어야 합니다. 디스크를 찾을 수 없는 경우 다음 메시지가 표시됩니다.

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>가능한 원인

- 새 데이터 디스크가 VM에 연결되었지만 초기화되지 않았습니다.
- VM 내부의 데이터 디스크가 디스크가 VM에 연결된 LUN(논리 단위 번호) 값을 올바르게 보고하지 않습니다.

### <a name="fix-the-problem"></a>문제 해결

데이터 디스크가 초기화되었는지 확인한 다음 작업을 다시 시도합니다.

- **Windows**: [새 디스크를 연결하고 초기화합니다.](/azure/virtual-machines/windows/attach-managed-disk-portal)
- **리눅스**: [리눅스에서 새로운 데이터 디스크를 초기화](/azure/virtual-machines/linux/add-disk).

문제가 계속되면 지원에 문의하세요.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>보호에 사용할 수 있는 여러 디스크(오류 코드 153039)

### <a name="possible-causes"></a>가능한 원인

- 보호 후 하나 이상의 디스크가 최근에 가상 시스템에 추가되었습니다.
- 가상 컴퓨터를 보호한 후 하나 이상의 디스크가 초기화되었습니다.

### <a name="fix-the-problem"></a>문제 해결

VM의 복제 상태를 다시 정상 상태로 만들려면 디스크를 보호하거나 경고를 해제하도록 선택할 수 있습니다.

#### <a name="to-protect-the-disks"></a>디스크를 보호하려면

1. **복제된 항목** > _VM 이름_ > **디스크로 이동합니다.**
1. 보호되지 않은 디스크를 선택한 다음 **복제 활성화를**선택합니다.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="VM 디스크에서 복제를 활성화합니다.":::

#### <a name="to-dismiss-the-warning"></a>경고를 해제하려면

1. 복제된 항목 > _VM 이름으로_ **이동합니다.**
1. **개요** 섹션에서 경고를 선택한 다음 **확인을**선택합니다.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="새 디스크 경고를 해제합니다.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>정보(오류 코드 150225)로 완료된 볼트에서 VM제거됨

사이트 복구가 가상 컴퓨터를 보호하면 원본 가상 시스템에 대한 링크가 만들어집니다. 보호를 제거하거나 복제를 사용하지 않도록 설정하면 사이트 복구는 정리 작업의 일부로 이러한 링크를 제거합니다. 가상 시스템에 리소스 잠금이 있는 경우 정리 작업이 정보로 완료됩니다. 이 정보는 가상 시스템이 복구 서비스 자격 증명 모음에서 제거되었지만 원본 컴퓨터에서 오래된 링크 중 일부를 정리할 수 없다고 말합니다.

이 가상 컴퓨터를 다시 보호하려는 경우 이 경고를 무시할 수 있습니다. 그러나 나중에 이 가상 컴퓨터를 보호해야 하는 경우 이 섹션의 단계를 따라 링크를 정리합니다.

> [!WARNING]
> 정리를 하지 않는 경우:
>
> - 복구 서비스 자격 증명 모음을 통해 복제를 사용하도록 설정하면 가상 시스템이 나열되지 않습니다.
> - **가상 시스템** > **설정** > **재해 복구를**사용 하 여 VM을 보호 하려고 하는 경우 **VM에 기존 부실 리소스 링크 때문에 복제를 사용할 수 없습니다**메시지와 함께 작업이 실패 합니다.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
> 사이트 복구는 이러한 단계를 수행하는 동안 원본 가상 컴퓨터를 삭제하거나 영향을 주지 않습니다.

1. VM 또는 VM 리소스 그룹에서 잠금을 제거합니다. 예를 들어 다음 이미지에서 명명된 `MoveDemo` VM의 리소스 잠금을 삭제해야 합니다.

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="VM에서 잠금을 제거합니다.":::

1. 스크립트를 다운로드하여 [오래된 사이트 복구 구성을 제거합니다.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. 스크립트를 실행, _정리-부실-asr-config-Azure-VM.ps1_. 구독 **ID,** **VM 리소스 그룹**및 **VM 이름을** 매개 변수로 제공합니다.
1. Azure 자격 증명을 묻는 메시지가 표시되면 이를 제공합니다. 그런 다음 오류가 없는 스크립트가 실행되는지 확인합니다.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>부실 리소스가 있는 VM에서 복제를 사용할 수 없습니다(오류 코드 150226).

### <a name="possible-causes"></a>가능한 원인

가상 시스템에 이전 사이트 복구 보호의 오래된 구성이 있습니다.

사이트 복구를 사용하여 Azure VM에 대한 복제를 사용하도록 설정한 경우 Azure VM에서 오래된 구성이 발생할 수 있습니다.

- 복제를 비활성화했지만 원본 VM에 리소스 잠금이 있었습니다.
- VM에서 복제를 명시적으로 사용하지 않도록 설정하지 않고 사이트 복구 자격 증명 모음을 삭제했습니다.
- VM에서 복제를 명시적으로 사용하지 않도록 설정하지 않고 사이트 복구 자격 증명 모음이 포함된 리소스 그룹을 삭제했습니다.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
> 사이트 복구는 이러한 단계를 수행하는 동안 원본 가상 컴퓨터를 삭제하거나 영향을 주지 않습니다.

1. VM 또는 VM 리소스 그룹에서 잠금을 제거합니다. 예를 들어 다음 이미지에서 명명된 `MoveDemo` VM의 리소스 잠금을 삭제해야 합니다.

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="VM에서 잠금을 제거합니다.":::

1. 스크립트를 다운로드하여 [오래된 사이트 복구 구성을 제거합니다.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. 스크립트를 실행, _정리-부실-asr-config-Azure-VM.ps1_. 구독 **ID,** **VM 리소스 그룹**및 **VM 이름을** 매개 변수로 제공합니다.
1. Azure 자격 증명을 묻는 메시지가 표시되면 이를 제공합니다. 그런 다음 오류가 없는 스크립트가 실행되는지 확인합니다.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>복제 작업을 사용하도록 설정에서 VM 또는 리소스 그룹을 선택할 수 없습니다.

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>문제 1: 리소스 그룹 및 소스 VM이 서로 다른 위치에 있습니다.

사이트 복구는 현재 원본 지역 리소스 그룹과 가상 시스템이 동일한 위치에 있어야 합니다. 그렇지 않은 경우 보호를 적용하려고 할 때 가상 컴퓨터 또는 리소스 그룹을 찾을 수 없습니다.

해결 방법을 사용하면 복구 서비스 자격 증명 모음 대신 VM에서 복제를 사용하도록 설정할 수 있습니다. 원본 **VM** > **속성** > **재해 복구로** 이동하여 복제를 사용하도록 설정합니다.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>문제 2: 리소스 그룹이 선택한 구독의 일부가 아닙니다.

리소스 그룹이 선택한 구독의 일부가 아닌 경우 보호 시 리소스 그룹을 찾지 못할 수 있습니다. 리소스 그룹이 사용 중 구독에 속하는지 확인합니다.

### <a name="issue-3-stale-configuration"></a>문제 3: 오래된 구성

Azure VM에 오래된 사이트 복구 구성이 있는 경우 복제를 위해 사용하도록 설정하려는 VM이 표시되지 않을 수 있습니다. 이 조건은 사이트 복구를 사용하여 Azure VM에 대한 복제를 사용하도록 설정한 다음 다음과 같은 경우에 발생할 수 있습니다.

- VM에서 복제를 명시적으로 사용하지 않도록 설정하지 않고 사이트 복구 자격 증명 모음을 삭제했습니다.
- VM에서 복제를 명시적으로 사용하지 않도록 설정하지 않고 사이트 복구 자격 증명 모음이 포함된 리소스 그룹을 삭제했습니다.
- 복제를 비활성화했지만 원본 VM에 리소스 잠금이 있었습니다.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
> 이 섹션에서 언급한 `AzureRM.Resources` 스크립트를 사용하기 전에 모듈을 업데이트해야 합니다. 사이트 복구는 이러한 단계를 수행하는 동안 원본 가상 컴퓨터를 삭제하거나 영향을 주지 않습니다.

1. VM 또는 VM 리소스 그룹에서 잠금을 제거합니다(있는 경우). 예를 들어 다음 이미지에서 명명된 `MoveDemo` VM의 리소스 잠금을 삭제해야 합니다.

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="VM에서 잠금을 제거합니다.":::

1. 스크립트를 다운로드하여 [오래된 사이트 복구 구성을 제거합니다.](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)
1. 스크립트를 실행, _정리-부실-asr-config-Azure-VM.ps1_. 구독 **ID,** **VM 리소스 그룹**및 **VM 이름을** 매개 변수로 제공합니다.
1. Azure 자격 증명을 묻는 메시지가 표시되면 이를 제공합니다. 그런 다음 오류가 없는 스크립트가 실행되는지 확인합니다.

## <a name="unable-to-select-a-vm-for-protection"></a>보호를 위해 VM을 선택할 수 없음

### <a name="possible-cause"></a>가능한 원인

가상 시스템에 장애가 있거나 응답하지 않는 상태에 확장이 설치되어 있습니다.

### <a name="fix-the-problem"></a>문제 해결

가상 **컴퓨터** > **설정** > **확장으로** 이동하여 실패한 상태의 확장을 확인합니다. 실패한 확장을 제거한 다음 가상 컴퓨터를 보호하기 위해 다시 시도합니다.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>VM 프로비저닝 상태가 잘못되었습니다(오류 코드 150019).

VM에서 복제를 사용하려면 해당 프로비저닝 상태가 **성공되어야**합니다. 다음 단계에 따라 프로비저닝 상태를 확인합니다.

1. Azure 포털에서 **모든 서비스에서** **리소스 탐색기를** 선택합니다.
1. **구독** 목록을 확장하고 본인의 구독을 선택합니다.
1. **리소스 그룹** 목록을 확장하고 VM의 리소스 그룹을 선택합니다.
1. **리소스** 목록을 확장하고 VM을 선택합니다.
1. 오른쪽의 인스턴스 뷰에서 **프로비저닝상태** 필드를 확인합니다.

### <a name="fix-the-problem"></a>문제 해결

- **프로비저닝상태가** **실패한**경우 지원팀에 자세한 내용을 문의하여 문제를 해결합니다.
- **프로비저닝State가** **업데이트**중인 경우 다른 확장이 배포될 수 있습니다. VM에 진행 중인 작업이 있는지 확인하고 완료될 때까지 기다린 다음 실패한 사이트 복구 작업을 다시 시도하여 복제를 사용하도록 설정합니다.

## <a name="unable-to-select-target-vm"></a>대상 VM을 선택할 수 없음

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>문제 1: VM이 대상 네트워크에 이미 매핑된 네트워크에 연결됩니다.

원본 VM이 가상 네트워크의 일부이고 동일한 가상 네트워크의 다른 VM이 대상 리소스 그룹의 네트워크와 이미 매핑된 경우 네트워크 선택 드롭다운 목록 상자를 기본적으로 사용할 수 없습니다(흐리게 표시).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="네트워크 선택 목록을 사용할 수 없습니다.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>문제 2: 이전에 VM을 보호한 다음 복제를 사용하지 않도록 설정했습니다.

VM의 복제를 사용하지 않도록 설정해도 네트워크 매핑은 삭제되지 않습니다. VM이 보호된 복구 서비스 자격 증명 모음에서 매핑을 삭제해야 합니다. 복구 **서비스 볼트** > **사이트 복구 인프라** > **네트워크 매핑으로**이동합니다.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="네트워크 매핑을 삭제합니다.":::

재해 복구 설정 중에 구성된 대상 네트워크는 초기 설정 후 VM이 보호된 후 변경할 수 있습니다.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="네트워크 매핑을 수정합니다.":::

네트워크 매핑을 변경하면 동일한 네트워크 매핑을 사용하는 모든 보호된 VM에 영향을 줍니다.

## <a name="com-or-vss-error-code-151025"></a>COM+ 또는 VSS(오류 코드 151025)

COM+ 또는 VSS(볼륨 섀도우 복사 서비스) 오류가 발생하면 다음 메시지가 표시됩니다.

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>가능한 원인

- COM+ 시스템 응용 프로그램 서비스가 비활성화되어 있습니다.
- 볼륨 그림자 복사 서비스가 비활성화되었습니다.

### <a name="fix-the-problem"></a>문제 해결

COM+ 시스템 응용 프로그램 및 볼륨 섀도 복사 서비스를 자동 또는 수동 시작 모드로 설정합니다.

1. Windows에서 서비스 콘솔을 엽니다.
1. COM+ 시스템 응용 프로그램 및 볼륨 섀도 복사본 서비스가 **시작 유형으로** **비활성화되지** 않도록 설정되지 않았는지 확인합니다.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="COM의 시작 유형과 시스템 응용 프로그램 및 볼륨 섀도 복사 서비스를 확인합니다.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>지원되지 않는 관리 디스크 크기(오류 코드 150172)

이 오류가 발생하면 다음 메시지가 표시됩니다.

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>가능한 원인

디스크는 지원되는 크기인 1024MB보다 작습니다.

### <a name="fix-the-problem"></a>문제 해결

디스크 크기가 지원되는 크기 범위 내에 있는지 확인한 다음 작업을 다시 시도합니다.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>GRUB이 장치 이름(오류 코드 151126)을 사용하는 경우 보호가 활성화되지 않음

### <a name="possible-causes"></a>가능한 원인

Linux 그랜드 통합 부트 로더(GRUB) 구성 파일(/boot/grub/menu.lst, _/boot/grub/grub.cfg,_ _/boot/grub2/grub.cfg,_ 또는 _/etc/default/grub)은_ `root` 및 `resume` 매개 변수에 대한 보편적으로 고유한 식별자(UUID) 값 대신 실제 장치 이름을 지정할 수 있습니다._/boot/grub/menu.lst_ 장치 이름이 변경될 수 있으므로 사이트 복구에는 UUID가 필요합니다. 다시 시작하면 VM이 장애 조치(failover)에서 동일한 이름을 사용하지 않아 문제가 발생할 수 있습니다.

다음 예제는 GRUB 파일에서 필요한 UUID 대신 장치 이름이 표시되는 줄입니다.

- 파일 _/부팅 /grub2 / grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- 파일 : _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>문제 해결

각 장치 이름을 해당 UUID로 바꿉니다.

1. 명령을 `blkid <device name>`실행하여 장치의 UUID를 찾습니다. 다음은 그 예입니다.

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 장치 이름을 형식 및 `root=UUID=<UUID>` `resume=UUID=<UUID>`에서 UUID로 바꿉니다. 예를 들어 교체 후 _/boot/grub/menu.lst의_ 줄은 다음과 같습니다.

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 보호를 다시 시도합니다.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>GRUB 장치가 존재하지 않아 보호가 실패했습니다(오류 코드 151124).

### <a name="possible-cause"></a>가능한 원인

GRUB 구성 파일 _(/boot/grub/menu.lst,_ _/boot/grub/grub.cfg,_ _/boot/grub2/grub.cfg,_ 또는 _/etc/default/grub)에는_매개 변수 `rd.lvm.lv` 또는 `rd_LVM_LV`. 이러한 매개 변수는 부팅 시 검색할 LVM(논리 볼륨 관리자) 장치를 식별합니다. 이러한 LVM 장치가 없으면 보호된 시스템 자체가 부팅되지 않고 부팅 프로세스에 갇혀 있습니다. 장애 조치 VM에서도 동일한 문제가 표시됩니다. 다음은 몇 가지 예입니다.

- 파일 : RHEL7에서 _/boot/grub2/grub.cfg:_

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- 파일 : RHEL7에 _/etc / 기본값 / grub_ :

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- 파일 : RHEL6의 _/boot/grub/menu.lst:_

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

각 예제에서 GRUB은 볼륨 그룹과 이름 `root` 및 `swap` 볼륨 그룹이 `rootvg`있는 두 개의 LVM 장치를 검색해야 합니다.

### <a name="fix-the-problem"></a>문제 해결

LVM 장치가 없는 경우 GRUB 구성 파일에서 해당 매개 변수를 만들거나 제거합니다. 그런 다음 다시 시도하여 보호를 사용하도록 설정합니다.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>경고로 완료된 모빌리티 서비스 업데이트(오류 코드 151083)

사이트 복구 이동성 서비스에는 필터 드라이버라고 하는 많은 구성 요소가 있습니다. 필터 드라이버는 시스템을 다시 시작하는 동안에만 시스템 메모리에 로드됩니다. Mobility 서비스 업데이트에 필터 드라이버 변경 내용이 포함될 때마다 컴퓨터가 업데이트되지만 일부 수정 프로그램에 다시 시작해야 한다는 경고가 계속 표시됩니다. 필터 드라이버 수정은 새 필터 드라이버가 로드된 경우에만 적용될 수 있으며, 이는 다시 시작할 때만 발생하기 때문에 경고가 나타납니다.

> [!NOTE]
> 단지 경고일 뿐입니다. 기존 복제는 새 에이전트 업데이트 후에도 계속 작동합니다. 새 필터 드라이버의 이점을 원할 때마다 다시 시작하도록 선택할 수 있지만 다시 시작하지 않으면 이전 필터 드라이버가 계속 작동합니다.
>
> 필터 드라이버 외에도 모빌리티 서비스 업데이트의 다른 개선 사항 및 수정 사항의 이점은 다시 시작하지 않고도 적용됩니다.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>복제본 관리 디스크가 있는 경우 보호가 활성화되지 않음

이 오류는 대상 리소스 그룹에 예상 된 태그 없이 복제본 관리 디스크가 이미 있는 경우에 발생 합니다.

### <a name="possible-cause"></a>가능한 원인

이 문제는 가상 컴퓨터를 이전에 보호하였고 복제를 사용하지 않도록 설정한 경우 복제본 디스크가 제거되지 않은 경우에 발생할 수 있습니다.

### <a name="fix-the-problem"></a>문제 해결

오류 메시지에서 식별된 복제본 디스크를 삭제하고 실패한 보호 작업을 다시 시도합니다.

## <a name="next-steps"></a>다음 단계

[Azure VM을 다른 Azure 지역으로 복제](azure-to-azure-how-to-enable-replication.md)
