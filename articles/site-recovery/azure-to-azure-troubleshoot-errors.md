---
title: Azure Site Recovery의 Azure 간 복제 문제 및 오류 해결 | Microsoft Docs
description: 재해 복구를 위해 Azure 가상 머신을 복제할 때 오류 및 문제 해결
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: asgang
ms.openlocfilehash: 1e0450554597d99aa99d6df51f22bfc90c0d92ad
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798575"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Azure 간 VM 복제 문제 해결

이 문서에서는 지역 간에 Azure 가상 머신을 복제 및 복구할 때 Azure Site Recovery에서 발생하는 일반적인 문제와 해결 방법을 설명합니다. 지원되는 구성에 대한 자세한 내용은 [Azure VM을 복제하기 위한 지원 매트릭스](site-recovery-support-matrix-azure-to-azure.md)를 참조하세요.

## <a name="list-of-errors"></a>오류 목록
- **[Azure 리소스 할당량 문제(오류 코드 150097)](#azure-resource-quota-issues-error-code-150097)**
- **[신뢰할 수 있는 루트 인증서(오류 코드 151066)](#trusted-root-certificates-error-code-151066)**
- **[Site Recovery용 아웃바운드 연결(오류 코드 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)**

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 리소스 할당량 문제(오류 코드 150097)
재해 복구 지역으로 사용할 대상 위치에 Azure VM을 만들려면 구독이 사용되도록 설정되어 있어야 합니다. 또한 구독은 특정 크기의 VM을 만들 수 있도록 할당량이 충분해야 합니다. 기본적으로 Site Recovery는 대상 VM에 대해 원본 VM과 동일한 크기를 선택합니다. 일치하는 크기를 사용할 수 없는 경우 가능한 가장 근접한 크기가 자동으로 선택됩니다. 원본 VM 구성을 지원하는 일치하는 크기가 없는 경우 다음 오류 메시지가 나타납니다.

**오류 코드** | **가능한 원인** | **권장 사항**
--- | --- | ---
150097<br></br>**메시지**: 가상 머신 VmName에 대해 복제를 사용하도록 설정할 수 없습니다. | - 대상 영역 위치에 VM을 만드는 구독 ID가 사용하도록 설정되어 있지 않을 수 있습니다.</br></br>- 대상 지역 위치에 특정 VM 크기를 만드는 구독 ID가 사용하도록 설정되어 있지 않거나 할당량이 충분하지 않습니다.</br></br>- 대상 지역 위치에서 구독 ID에 대해 원본 VM NIC 수(2)와 일치하는 적합한 대상 VM 크기를 찾을 수 없습니다.| [Azure 청구 지원](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)에 문의하여 대상 위치에서 구독에 대해 필요한 VM 크기의 VM 만들기를 사용하도록 설정할 수 있습니다. 사용하도록 설정되면 실패한 작업은 다시 시도하세요.

### <a name="fix-the-problem"></a>문제 해결
[Azure 청구 지원](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)에 문의하여 구독을 사용하도록 설정하면 대상 위치에 필요한 크기의 VM을 만들 수 있습니다.

대상 위치에 용량 제약 조건이 있으면 복제를 사용하지 않도록 설정하고 필요한 크기의 VM을 만들기에 충분한 할당량이 구독에 있는 다른 위치에 대해 복제를 사용하도록 설정합니다.

## <a name="trusted-root-certificates-error-code-151066"></a>신뢰할 수 있는 루트 인증서(오류 코드 151066)

최신의 신뢰할 수 있는 루트 인증서 중 일부만 VM에 있는 경우 "복제를 사용하도록 설정"하는 작업이 실패할 수 있습니다. 인증서가 없으면 VM에서 Site Recovery 서비스 호출의 인증 및 권한 부여가 실패합니다. 실패한 "복제를 사용하도록 설정"하는 Site Recovery 작업에 대한 오류 메시지가 나타납니다.

**오류 코드** | **가능한 원인** | **권장 사항**
--- | --- | ---
151066<br></br>**메시지**: Site Recovery 구성이 실패했습니다. | 권한 부여 및 인증에 사용되는 신뢰할 수 있는 필요한 루트 인증서가 시스템에 없습니다. | - Windows 운영 체제를 실행하는 VM의 경우 신뢰할 수 있는 루트 인증서가 컴퓨터에 있는지 확인합니다. 자세한 내용은 [신뢰할 수 있는 루트 및 허용되지 않는 인증서 구성](https://technet.microsoft.com/library/dn265983.aspx)을 참조하세요.<br></br>- Linux 운영 체제를 실행하는 VM의 경우 Linux 운영 체제 버전 배포자가 게시한 신뢰할 수 있는 루트 인증서에 대한 지침을 따릅니다.

### <a name="fix-the-problem"></a>문제 해결
**Windows**

모든 신뢰할 수 있는 루트 인증서가 컴퓨터에 있도록 VM에서 모든 최신 Windows 업데이트를 설치합니다. 연결이 끊어진 환경에 있는 경우 인증서를 받는 조직에서 표준 Windows 업데이트 프로세스를 따릅니다. 필요한 인증서가 VM에 없는 경우 보안상의 이유로 Site Recovery 서비스에 대한 호출이 실패합니다.

조직의 일반적인 Windows 업데이트 관리 또는 인증서 업데이트 관리 프로세스에 따라 VM의 모든 최신 루트 인증서와 업데이트된 인증서 해지 목록을 가져옵니다.

문제가 해결되었는지 확인하려면 VM의 브라우저에서 login.microsoftonline.com으로 이동합니다.

**Linux**

Linux 배포자가 제공한 지침에 따라 VM의 신뢰할 수 있는 최신 루트 인증서 및 최신 인증서 해지 목록을 가져옵니다.

SuSE Linux는 symlink를 사용하여 인증서 목록을 유지 관리하므로 다음 단계를 따릅니다.

1.  루트 사용자로 로그인합니다.

2.  디렉터리를 변경하려면 이 명령을 실행합니다.

      ``# cd /etc/ssl/certs``

1. Symantec 루트 CA 인증서가 있는지 확인합니다.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Symantec 루트 CA 인증서를 찾을 수 없으면 다음 명령을 실행하여 파일을 다운로드합니다. 모든 오류를 확인하고 네트워크 오류에 대한 권장 조치를 수행합니다.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Baltimore 루트 CA 인증서가 있는지 확인합니다.

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Baltimore 루트 CA 인증서를 찾을 수 없으면 인증서를 다운로드합니다.  

    ``# wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. DigiCert_Global_Root_CA 인증서가 있는지 확인합니다.

    ``# ls DigiCert_Global_Root_CA.pem``

6. DigiCert_Global_Root_CA 인증서를 찾을 수 없으면 다음 명령을 실행하여 인증서를 다운로드합니다.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. 다시 해시 스크립트를 실행하여 새로 다운로드한 인증서에 대한 인증서 주체 해시를 업데이트합니다.

    ``# c_rehash``

8.  인증서에 대한 주체 해시가 symlink로 생성되는지 확인합니다.

    - 명령

      ``# ls -l | grep Baltimore``

    - 출력

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - 명령

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - 출력

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - 명령

      ``# ls -l | grep DigiCert_Global_Root``

    - 출력

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  파일 이름을 b204d74a.0으로 하여 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem 파일 복사본 만들기

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. 파일 이름을 653b494a.0으로 하여 Baltimore_CyberTrust_Root.pem 파일 복사본 만들기

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. 파일 이름을 3513523f.0으로 하여 DigiCert_Global_Root_CA.pem 파일 복사본 만들기

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. 파일이 있는지 확인합니다.  

    - 명령

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - 출력

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 또는 IP 범위에 대한 아웃바운드 연결(오류 코드 151037 또는 151072)

Site Recovery 복제가 작동하려면 VM에서 특정 URL 또는 IP 범위에 대한 아웃바운드 연결이 필요합니다. VM이 방화벽 뒤에 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 이러한 문제 중 하나가 발생할 수 있습니다.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>문제 1: Site Recovery에 Azure 가상 머신을 등록하지 못했습니다(151195). </br>
- **가능한 원인** </br>
  - DNS 확인 오류로 인해 Site Recovery 끝점에 연결할 수 없습니다.
  - 가상 머신을 장애 조치(failover)했지만 DR 지역에서 DNS 서버에 도달할 수 없는 경우 재보호 기간에 자주 발생합니다.

- **해결 방법**
   - 사용자 지정 DNS를 사용하는 경우 재해 복구 지역에서 DNS 서버에 액세스할 수 있는지 확인합니다. 사용자 지정 DNS가 있는지 확인하려면 VM > 재해 복구 네트워크 > DNS 서버로 이동합니다. 가상 머신에서 DNS 서버에 액세스를 시도합니다. 액세스할 수 없는 경우 DNS 서버를 장애 조치(failover)하거나 DR 네트워크와 DNS 사이를 연결하여 액세스 가능하게 만듭니다.

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### <a name="issue-2-site-recovery-configuration-failed-151196"></a>문제 2: Site Recovery 구성이 실패했습니다(151196).
- **가능한 원인** </br>
  - Office 365 인증에 연결하여 IP4 엔드포인트를 식별할 수 없습니다.

- **해결 방법**
  - Azure Site Recovery는 인증을 위해 Office 365 IP 범위에 액세스할 수 있어야 합니다.
    Azure NSG(네트워크 보안 그룹) 규칙/방화벽 프록시를 사용하여 VM의 아웃바운드 네트워크 연결을 제어하는 경우 O365 IP 범위로의 통신을 허용해야 합니다. AAD에 해당하는 모든 IP 주소에 대한 액세스를 허용하는 [AAD(Azure Active Directory) 서비스 태그](../virtual-network/security-overview.md#service-tags) 기반 NSG 규칙을 만드세요.
      - 나중에 AAD(Azure Active Directory)에 새 주소가 추가될 때 새 NSG 규칙을 만들어야 합니다.

> [!NOTE]
> 가상 컴퓨터 보호 되는 경우 **표준** 을 내부 부하 분산 장치, 즉 O365 Ip에 액세스할 수 없습니다 기본적으로 login.microsoftonline.com입니다. 되도록 변경 하거나 **기본적인** 내부 부하 분산 장치 유형 또는에 설명 된 대로 아웃 바운드 액세스를 만들기는 [문서](https://aka.ms/lboutboundrulescli)합니다.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>문제 3: Site Recovery 구성이 실패했습니다(151197).
- **가능한 원인** </br>
  - Azure Site Recovery 서비스 엔드포인트에 연결할 수 없습니다.

- **해결 방법**
  - Azure Site Recovery는 지역에 따라 [Site Recovery IP 범위](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)에 액세스할 수 있어야 합니다. 가상 머신에서 필요한 ip 범위에 액세스할 수 있는지 확인합니다.


### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>문제 4: 네트워크 트래픽은 온-프레미스 프록시 서버 (151072)를 통해 A2A 복제가 실패 했습니다.
- **가능한 원인** </br>
  - 사용자 지정 프록시 설정이 올바르지 않습니다 및 Azure Site Recovery 모바일 서비스 에이전트 검색 하지 못했습니다 자동-IE에서 프록시 설정


- **해결 방법**
  1. Mobility Service 에이전트는 Windows의 경우 IE에서 Linux의 경우 /etc/environment에서 프록시 설정을 검색합니다.
  2. Azure Site Recovery 모바일 서비스에 대해서만 프록시를 설정 하려는 경우 위치한 ProxyInfo.conf 프록시 세부 정보를 제공할 수 있습니다.</br>
     - ***Linux***에서 ``/usr/local/InMage/config/``
     - ***Windows***에서 ``C:\ProgramData\Microsoft Azure Site Recovery\Config``
  3. ProxyInfo.conf에는 다음 INI 형식의 프록시 설정이 있어야 합니다.</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Azure Site Recovery 모바일 서비스 에이전트 지원 ***인증 되지 않은 프록시***합니다.


### <a name="fix-the-problem"></a>문제 해결
수 있도록 [필요한 Url](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 또는 [필요한 IP 범위](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)의 단계를 수행 합니다 [네트워킹 지침 문서](site-recovery-azure-to-azure-networking-guidance.md)합니다.

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>컴퓨터에서 디스크를 찾을 수 없음(오류 코드 150039)

VM에 연결된 새 디스크는 초기화되어야 합니다.

**오류 코드** | **가능한 원인** | **권장 사항**
--- | --- | ---
150039<br></br>**메시지**: LUN(논리 단위 번호)이 (LUNValue)인 Azure 데이터 디스크(DiskName) (DiskURI)이(가) LUN 값이 동일한 VM 내에서 보고되는 해당 디스크에 매핑되지 않았습니다. | - 새 데이터 디스크가 VM에 연결되었지만 초기화되지 않았습니다.</br></br>- VM 내의 데이터 디스크는 디스크가 VM에 연결된 LUN 값을 올바르게 보고하지 않습니다.| 데이터 디스크가 초기화되었는지 확인하고 작업을 다시 시도합니다.</br></br>Windows의 경우: [새 디스크를 연결하고 초기화](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)합니다.</br></br>Linux의 경우: [Linux에서 새 데이터 디스크를 초기화](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)합니다.

### <a name="fix-the-problem"></a>문제 해결
데이터 디스크가 초기화되었는지 확인하고 작업을 다시 시도합니다.

- Windows의 경우: [새 디스크를 연결하고 초기화](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)합니다.
- Linux의 경우: [Linux에 새 데이터 디스크를 추가](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk)합니다.

문제가 계속되면 지원에 문의하세요.

## <a name="one-or-more-disks-are-available-for-protectionerror-code-153039"></a>하나 이상의 디스크 (오류 코드 153039) 보호를 위해 사용할 수 있습니다.
- **가능한 원인** </br>
  - 하나 이상의 디스크가 보호 후 가상 컴퓨터에 최근에 추가 되었습니다 하는 경우. 
  - 가상 머신의 보호 한 후 나중에 하나 이상의 디스크 초기화 되었습니다 하는 경우.

### <a name="fix-the-problem"></a>문제 해결
디스크 보호 또는 복제 상태의 VM 다시 정상 되도록 경고를 무시 하거나 선택할 수 있습니다.</br>
1. 디스크를 보호 합니다. 복제 된 항목으로 이동 > VM > 디스크 > 보호 되지 않는 디스크에 클릭 > 복제를 사용 하도록 설정 합니다.
 ![add_disks](./media/azure-to-azure-troubleshoot-errors/add-disk.png)
2. 경고를 해제 합니다. 복제 된 항목으로 이동 > VM > 개요 섹션에서 해제 경고를 클릭 합니다.
![dismiss_warning](./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png)


## <a name="remove-the-virtual-machine-from-the-vault-completed-with-information--error-code-150225"></a>(오류 코드 150225) 정보를 사용 하 여 완료 하는 자격 증명 모음에서 가상 컴퓨터를 제거 합니다.
가상 컴퓨터를 보호 하는 동시에 Azure Site Recovery는 원본 가상 컴퓨터의 일부 링크를 만듭니다. 보호를 제거 하거나 복제를 사용 하지 않도록 설정 하는 경우 Azure Site Recovery는 정리 작업의 일부로 이러한 링크를 제거 합니다. 가상 컴퓨터에 리소스 잠금을 경우 정보를 사용 하 여 작업이 완료를 가져옵니다. 가상 머신을 Recovery services 자격 증명 모음에서 제거 되었지만 오래 된 링크 중 일부를 정리할 수 없습니다 원본 컴퓨터에서 알려 줍니다.

이 가상 컴퓨터를 나중에 다시 보호 하기 위해 의도 하지 않은 경우이 경고를 무시할 수 있습니다. 그러나 나중에이 가상 컴퓨터를 보호 해야 할 경우 다음 정리 해야 링크 아래 단계에 설명 된 대로 합니다. 

**이렇게 하지 않으면 새로 한 후:**

1.  Recovery services 자격 증명 모음을 통해 복제를 사용 하도록 설정 하는 동안 가상 머신 나열 되지 않습니다. 
2.  통해 VM을 보호 하려고 하면 **가상 머신 > 설정 > 재해 복구** 오류로 인해 실패 하는 것이 것 "*VM에서기존부실리소스링크인해복제를사용할수없습니다*".


### <a name="fix-the-problem"></a>문제 해결

>[!NOTE]
>
>Azure Site Recovery는 원본 가상 컴퓨터를 삭제 또는 아래 단계를 수행 하는 동안에 전혀 영향 하지 않습니다.
>

1. VM 또는 VM에서 잠금을 제거 리소스 그룹입니다. 예: VM 아래 "MoveDemo" 이름이 삭제 해야 하는 리소스 잠금.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. 스크립트 다운로드 [오래 된 Azure Site Recovery 구성 제거](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)합니다.
3. 스크립트 실행 *Cleanup-stale-asr-config-Azure-VM.ps1*합니다.
4. 구독 ID, VM 리소스 그룹 및 VM 이름을 매개 변수로 제공 합니다.
5. Azure 자격 증명을 묻는 메시지가 제공 하는 스크립트가 오류 없이 실행 되는 확인 하십시오. 


## <a name="replication-cannot-be-enabled-because-of-the-existing-stale-resource-links-on-the-vm-error-code-150226"></a>(오류 코드 150226) VM에서 기존 부실 리소스 링크 인해 복제를 사용할 수 없습니다.

**원인: 가상 컴퓨터에 이전 Site Recovery 보호를 유지 하는 오래 된 구성**

다음과 같은 경우 Azure VM에 부실 구성이 남겨질 수 있습니다.

- Site Recovery를 사용 하 여 Azure VM에 대 한 복제를 사용 하도록 설정 하 고 다음 복제를 사용 하지 않도록 설정 되었지만 **원본 VM 리소스 잠금을 했습니다**합니다.
- Site Recovery를 사용하여 Azure VM에 대해 복제를 사용하도록 설정한 후 VM에서 명시적으로 복제를 사용하지 않도록 설정하지 않으면서 Site Recovery 자격 증명 모음을 삭제했습니다.
- Site Recovery를 사용하여 Azure VM에 대해 복제를 사용하도록 설정한 후 VM에서 명시적으로 복제를 사용하지 않도록 설정하지 않으면서 Site Recovery 자격 증명 모음이 포함된 리소스 그룹을 삭제했습니다.

### <a name="fix-the-problem"></a>문제 해결

>[!NOTE]
>
>Azure Site Recovery는 원본 가상 컴퓨터를 삭제 또는 아래 단계를 수행 하는 동안에 전혀 영향 하지 않습니다.


1. VM 또는 VM에서 잠금을 제거 리소스 그룹에 있는 경우. *예:* VM 아래 "MoveDemo" 이름이 삭제 해야 하는 리소스 잠금.
   
   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. 스크립트 다운로드 [오래 된 Azure Site Recovery 구성 제거](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)합니다.
3. 스크립트 실행 *Cleanup-stale-asr-config-Azure-VM.ps1*합니다.
4. 구독 ID, VM 리소스 그룹 및 VM 이름을 매개 변수로 제공 합니다.
5. Azure 자격 증명을 묻는 메시지가 제공 하는 스크립트가 오류 없이 실행 되는 확인 하십시오.  

## <a name="unable-to-see-the-azure-vm-or-resource-group--for-selection-in-enable-replication"></a>"복제 사용"에서 선택할 Azure VM 또는 리소스 그룹을 확인할 수 없습니다.

 **원인 1:  리소스 그룹 및 원본 가상 머신이 서로 다른 위치에 있음**
 
Azure Site Recovery 현재 영역 리소스 그룹 및 virtual machines의 소스가 되는 규약 해야 동일한 위치에 있습니다. 경우 하지 않은 경우 다음 있습니다 없게 보호의 시간 동안 가상 머신 또는 리소스 그룹을 찾으려고 합니다. 

**해결 방법으로**, Recovery services 자격 증명 대신 VM에서 복제를 설정할 수 있습니다. 원본 VM으로 이동 > 속성 > 재해 복구 및 복제를 사용 하도록 설정 합니다.

**원인 2: 리소스 그룹이 선택한 구독에 포함되지 않음**

제공된 구독에 포함되지 않은 경우, 보호 시 리소스 그룹을 찾을 수 없습니다. 리소스 그룹이 사용 중인 구독에 속하는지 확인합니다.

 **원인 3: 부실 구성**
 
복제를 사용하려는 VM이 보이지 않는 경우 Azure VM에 남아 있는 부실한 Site Recovery 구성이 그 원인일 수 있습니다. 다음과 같은 경우 Azure VM에 부실 구성이 남겨질 수 있습니다.

- Site Recovery를 사용하여 Azure VM에 대해 복제를 사용하도록 설정한 후 VM에서 명시적으로 복제를 사용하지 않도록 설정하지 않으면서 Site Recovery 자격 증명 모음을 삭제했습니다.
- Site Recovery를 사용하여 Azure VM에 대해 복제를 사용하도록 설정한 후 VM에서 명시적으로 복제를 사용하지 않도록 설정하지 않으면서 Site Recovery 자격 증명 모음이 포함된 리소스 그룹을 삭제했습니다.

- Site Recovery를 사용 하 여 Azure VM에 대 한 복제를 사용 하도록 설정 하 고 복제를 사용 하지 않도록 설정 했지만 원본 VM 리소스 잠금.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
>
> 아래 스크립트를 사용하기 전에 ""AzureRM.Resources"" 모듈을 업데이트해야 합니다. Azure Site Recovery는 원본 가상 컴퓨터를 삭제 또는 아래 단계를 수행 하는 동안에 전혀 영향 하지 않습니다.
>

1. VM 또는 VM에서 잠금을 제거 리소스 그룹에 있는 경우. *예:* VM 아래 "MoveDemo" 이름이 삭제 해야 하는 리소스 잠금.

   ![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png)
2. 스크립트 다운로드 [부실 구성을 제거](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1)합니다.
3. 스크립트 실행 *Cleanup-stale-asr-config-Azure-VM.ps1*합니다.
4. 구독 ID, VM 리소스 그룹 및 VM 이름을 매개 변수로 제공 합니다.
5. Azure 자격 증명을 묻는 메시지가 제공 하는 스크립트가 오류 없이 실행 되는 확인 하십시오.

## <a name="unable-to-select-virtual-machine-for-protection"></a>보호를 위해 가상 머신을 선택할 수 없음
 **원인 1:  가상 머신의 일부 확장이 실패하거나 응답하지 않는 상태로 설치됨** <br>
 [가상 머신] > [설정] > [확장]으로 이동하여 실패한 상태의 확장자가 있는지 확인합니다. 실패한 확장을 제거하고 가상 머신 보호를 다시 시도합니다.<br>
 **원인 2:  [VM의 프로비저닝 상태가 잘못됨](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>VM의 프로비전 상태가 잘못되었습니다(오류 코드 150019).

VM에서 복제를 사용하도록 설정하려면 프로비전 상태가 **성공**이어야 합니다. 다음 단계를 수행하여 VM 상태를 확인할 수 있습니다.

1.  Azure Portal의 **모든 서비스**에서 **리소스 탐색기**를 선택합니다.
2.  **구독** 목록을 확장하고 본인의 구독을 선택합니다.
3.  **리소스 그룹** 목록을 확장하고 VM의 리소스 그룹을 선택합니다.
4.  **리소스** 목록을 확장하고 사용하는 가상 머신을 선택합니다.
5.  오른쪽의 인스턴스 보기에서 **provisioningState** 필드를 선택합니다.

### <a name="fix-the-problem"></a>문제 해결

- **provisioningState**가 **실패**이면 지원 서비스에 문제 해결을 위한 세부 정보를 문의합니다.
- **provisioningState**가 **업데이트 중**이면 또 다른 확장을 배포할 수 있습니다. VM에서 계속 진행되는 작업이 있는지 확인한 후, 해당 작업이 완료될 때까지 기다렸다가 실패한 Site Recovery **복제 사용** 작업을 다시 시도합니다.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>대상 가상 네트워크를 선택할 수 없음 - [네트워크 선택] 탭이 회색으로 표시됩니다.

**원인 1: VM이 '대상 네트워크'에 이미 매핑된 네트워크에 연결된 경우**
- 원본 VM이 가상 네트워크의 일부이고 동일한 가상 네트워크의 다른 VM이 대상 리소스 그룹의 네트워크와 이미 매핑되어 있으면 기본적으로 네트워크 선택 드롭다운이 비활성화됩니다.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**원인 2: 이전에 Azure Site Recovery를 사용하여 VM을 보호했고 복제를 사용하지 않도록 설정한 경우**
 - VM의 복제를 사용하지 않도록 설정하면 네트워크 매핑을 삭제하지 않습니다. VM이 보호되었던 복구 서비스 자격 증명 모음에서 삭제되어야 합니다. </br>
 [복구 서비스 자격 증명 모음] > [Site Recovery 인프라] > [네트워크 매핑]으로 이동합니다. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - 재해 복구 설정 중에 구성된 대상 네트워크는 초기 설정 후에 VM이 보호된 후에 변경할 수 있습니다. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - 네트워크 매핑을 변경하면 특정 네트워크 매핑을 사용하는 모든 보호된 VM에 영향을 줍니다.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM+/볼륨 섀도 복사본 서비스 오류(오류 코드 151025)

**오류 코드** | **가능한 원인** | **권장 사항**
--- | --- | ---
151025<br></br>**메시지**: 사이트 복구 확장 설치 실패 | - 'COM+ 시스템 애플리케이션' 서비스가 비활성화되었습니다.</br></br>- '볼륨 섀도 복사본' 서비스가 비활성화되었습니다.| 'COM+ 시스템 애플리케이션' 및 '볼륨 섀도 복사본' 서비스를 자동 또는 수동 시작 모드로 설정하세요.

### <a name="fix-the-problem"></a>문제 해결

'서비스' 콘솔을 열고, 'COM+ 시스템 애플리케이션' 및 '볼륨 섀도 복사본'이 '시작 유형'에 대해 '사용 안 함'으로 설정되지 않았는지 확인합니다.
  ![com-error](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>지원되지 않는 Managed Disks 크기(오류 코드 150172)


**오류 코드** | **가능한 원인** | **권장 사항**
--- | --- | ---
150172<br></br>**메시지**: 가상 머신에 포함된 (DiskName)의 크기가 (DiskSize)이며 지원되는 최소 크기인 1024MB보다 작기 때문에 가상 머신에 대한 보호를 사용하도록 설정할 수 없습니다. | - 디스크가 지원되는 크기(1024MB)보다 작습니다.| 디스크 크기가 지원되는 크기 범위 내에 있는지 확인하고 작업을 다시 시도하십시오.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>UUID 대신 디바이스 이름이 GRUB 구성에 언급되어 보호를 사용하도록 설정하지 못함(오류 코드 151126)

**가능한 원인:** </br>
GRUB 구성 파일("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" 또는 "/etc/default/grub")에는 **root** 및 **resume** 매개 변수의 값이 UUID가 아닌 실제 디바이스 이름으로 포함되어 있을 수 있습니다. Site Recovery에서는 UUID를 사용해야 합니다. 디바이스 이름은 VM을 다시 부팅하면 변경될 수 있는데, 장애 조치(failover) 시에 VM 이름이 달라지면 문제가 발생하기 때문입니다. 예를 들어: </br>


- 아래에는 이러한 오류의 원인이 되는 GRUB 파일 **/boot/grub2/grub.cfg**에서 발췌한 줄이 나와 있습니다. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- 아래에는 GRUB 파일 **/boot/grub/menu.lst**에서 발췌한 줄이 나와 있습니다.
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

위에서 굵게 표시된 문자열을 살펴보면 GRUB의 "root" 및 "resume" 매개 변수의 값이 UUID가 아닌 실제 디바이스 이름임을 확인할 수 있습니다.

**해결 방법:**<br>
디바이스 이름을 해당 UUID로 바꿔야 합니다.<br>


1. 명령을 실행 하 여 장치의 UUID를 찾으려면 "blkid \<장치 이름 >"입니다. 예를 들어:<br>
   ```
   blkid /dev/sda1
   ```<br>
   ```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
   ```blkid /dev/sda2```<br>
   ```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```<br>



1. Now replace the device name with its UUID in the format like "root=UUID=\<UUID>". For example, if we replace the device names with UUID for root and resume parameter mentioned above in the files "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub: then the lines in the files looks like. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
1. Restart the protection again

## Enable protection failed as device mentioned in the GRUB configuration doesn't exist(error code 151124)
**Possible Cause:** </br>
The GRUB configuration files ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub") may contain the parameters "rd.lvm.lv" or "rd_LVM_LV" to indicate the LVM device that should be discovered at the time of booting. If these LVM devices doesn't exist, then the protected system itself will not boot and stuck in the boot process. Even the same will be observed with the failover VM. Below are few examples:

Few examples: </br>

1. The following line is from the GRUB file **"/boot/grub2/grub.cfg"** on RHEL7. </br>
   *linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet LANG=en_US.UTF-8*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".
1. The following line is from the GRUB file **"/etc/default/grub"** on RHEL7 </br>
   *GRUB_CMDLINE_LINUX="crashkernel=auto **rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap** rhgb quiet"*</br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".
1. The following line is from the GRUB file **"/boot/grub/menu.lst"** on RHEL6 </br>
   *kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet* </br>
   Here the highlighted portion shows that the GRUB has to detect two LVM devices with names **"root"** and **"swap"** from the volume group "rootvg".<br>

**How to Fix:**<br>

If the LVM device doesn't exist, fix either by creating it or remove the parameter for the same from the GRUB configuration files and then retry the enable protection. </br>

## Site Recovery mobility service update completed with warnings ( error code 151083)
Site Recovery mobility service has many components, one of which is called filter driver. Filter driver gets loaded into system memory only at a time of system reboot. Whenever there are  Site Recovery mobility service updates that has filter driver changes, we update the machine but still gives you warning that some fixes require a reboot. It means that the filter driver fixes can only be realized when a new filter driver is loaded which can happen only at the time of system reboot.<br>
**Please note** that this is just a warning and existing replication keeps on working even after the new agent update. You can choose to reboot anytime you want to get the benefits of new filter driver but if you don't reboot than also old filter driver keeps on working. Apart from filter driver, **benefits of  any other enhancements and fixes in mobility service get realized without any reboot when the agent gets updated.**  


## Protection couldn't be enabled as replica managed disk 'diskname-replica' already exists without expected tags in the target resource group( error code 150161

**Cause**: It can occur if the  virtual machine was protected earlier in the past and during disabling the replication, replica disk was not cleaned due to some reason.</br>
**How to fix:**
Delete the mentioned replica disk in the error message and restart the failed protection job again.

## Next steps
[Replicate Azure virtual machines](site-recovery-replicate-azure-to-azure.md)
