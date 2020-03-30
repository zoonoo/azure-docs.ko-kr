---
title: Azure 사이트 복구를 사용하여 Azure에서 Azure 재해 복구에 대한 연결 문제 해결
description: Azure VM 재해 복구에서 연결 문제 해결
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 08/05/2019
ms.openlocfilehash: b082e1aca094dcb335a7268e4c116376d756fd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292021"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Azure-Azure VM 네트워크 연결 문제 해결

이 문서에서는 한 리전에서 다른 리전으로 Azure 가상 컴퓨터를 복제하고 복구할 때 네트워크 연결과 관련된 일반적인 문제에 대해 설명합니다. 네트워킹 요구 사항에 대한 자세한 내용은 [Azure VM 복제에 대한 연결 요구 사항을](azure-to-azure-about-networking.md)참조하세요.

Site Recovery 복제가 작동하려면 VM에서 특정 URL 또는 IP 범위에 대한 아웃바운드 연결이 필요합니다. VM이 방화벽 뒤에 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 이러한 문제 중 하나가 발생할 수 있습니다.

**Url** | **세부 정보**  
--- | ---
\*.blob.core.windows.net | VM에서 원본 지역의 캐시 스토리지 계정에 데이터를 쓸 수 있도록 하는 데 필요합니다. VM의 모든 캐시 저장소 계정을 알고 있는 경우 *.blob.core.windows.net 대신 특정 저장소 계정 URL(예: cache1.blob.core.windows.net 및 cache2.blob.core.windows.net)을 나열할 수 있습니다.
login.microsoftonline.com | Site Recovery 서비스 URL에 대한 권한 부여 및 인증에 필요합니다.
\*.hypervrecoverymanager.windowsazure.com | VM에서 Site Recovery 서비스 통신이 발생할 수 있도록 하는 데 필요합니다. 방화벽 프록시가 IP를 지원하는 경우 해당 '사이트 복구 IP'를 사용할 수 있습니다.
\*.servicebus.windows.net | VM에서 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 하는 데 필요합니다. 방화벽 프록시가 IP를 지원하는 경우 해당 '사이트 복구 모니터링 IP'를 사용할 수 있습니다.

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 또는 IP 범위에 대한 아웃바운드 연결(오류 코드 151037 또는 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>문제 1: Site Recovery에 Azure 가상 머신을 등록하지 못했습니다(151195). </br>
- **가능한 원인** </br>
  - DNS 확인 실패로 인해 사이트 복구 끝점에 연결을 설정할 수 없습니다.
  - 가상 머신을 장애 조치(failover)했지만 DR 지역에서 DNS 서버에 도달할 수 없는 경우 재보호 기간에 자주 발생합니다.

- **해상도**
   - 사용자 지정 DNS를 사용하는 경우 재해 복구 지역에서 DNS 서버에 액세스할 수 있는지 확인합니다. 사용자 지정 DNS가 있는지 확인하려면 VM > 재해 복구 네트워크 > DNS 서버로 이동합니다. 가상 머신에서 DNS 서버에 액세스를 시도합니다. 액세스할 수 없는 경우 DNS 서버에서 장애 또는 DR 네트워크와 DNS 간의 사이트 라인을 만들어 액세스할 수 있도록 합니다.

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>문제 2: Site Recovery 구성이 실패했습니다(151196).

> [!NOTE]
> 가상 시스템이 **표준** 내부 부하 분산 검사기 뒤에 있으면 기본적으로 O365 IP(즉, login.microsoftonline.com)에 액세스할 수 없습니다. **기본** 내부 부하 분산 기 유형으로 변경하거나 [문서에서](https://aka.ms/lboutboundrulescli)설명한 대로 아웃바운드 액세스를 만듭니다.

- **가능한 원인** </br>
  - Office 365 인증에 연결하여 IP4 엔드포인트를 식별할 수 없습니다.

- **해상도**
  - Azure Site Recovery는 인증을 위해 Office 365 IP 범위에 액세스할 수 있어야 합니다.
    Azure NSG(네트워크 보안 그룹) 규칙/방화벽 프록시를 사용하여 VM의 아웃바운드 네트워크 연결을 제어하는 경우 O365 IP 범위로의 통신을 허용해야 합니다. Azure AD에 해당하는 모든 IP 주소에 대한 액세스를 허용하기 위한 [Azure Active Directory(Azure AD) 서비스](../virtual-network/security-overview.md#service-tags) 태그 기반 NSG 규칙을 만듭니다.
      - 나중에 새 주소가 Azure AD에 추가되면 새 NSG 규칙을 만들어야 합니다.

### <a name="example-nsg-configuration"></a>NSG 구성 예제

이 예제에서는 복제할 VM에 대한 NSG 규칙을 구성하는 방법을 보여 줍니다.

- NSG 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 필요한 모든 IP 주소 범위에 대한 포트:443에 "HTTPS 아웃바운드 허용" 규칙을 사용합니다.
- 이 예제에서는 VM 원본 위치가 "미국 동부"이고 대상 위치가 "미국 중부"라고 가정합니다.

### <a name="nsg-rules---east-us"></a>NSG 규칙 - 미국 동부

1. 아래 스크린샷에 표시된 것처럼 NSG에서 "Storage.EastUS"에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. 아래 스크린샷에 표시된 것처럼 NSG에서 “AzureActiveDirectory”에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. 대상 위치에 해당하는 Site Recovery IP에 대한 아웃바운드 HTTPS(443) 규칙을 만듭니다.

   **위치** | **Site Recovery IP 주소** |  **Site Recovery 모니터링 IP 주소**
    --- | --- | ---
   미국 중부 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG 규칙 - 미국 중부

이러한 규칙은 장애 조치 후 대상 지역에서 원본 지역으로의 복제를 활성화하는 데 필요합니다.

1. NSG에서 "Storage.CentralUS"에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다.

2. NSG에서 “AzureActiveDirectory”에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다.

3. 원본 위치에 해당하는 Site Recovery IP에 대한 아웃바운드 HTTPS(443) 규칙을 만듭니다.

   **위치** | **Site Recovery IP 주소** |  **Site Recovery 모니터링 IP 주소**
    --- | --- | ---
   미국 중부 | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>문제 3: Site Recovery 구성이 실패했습니다(151197).
- **가능한 원인** </br>
  - Azure Site Recovery 서비스 엔드포인트에 연결할 수 없습니다.

- **해상도**
  - Azure Site Recovery는 지역에 따라 [Site Recovery IP 범위](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags)에 액세스할 수 있어야 합니다. 가상 머신에서 필요한 ip 범위에 액세스할 수 있는지 확인합니다.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>문제 4: 네트워크 트래픽이 온-프레미스 프록시 서버(151072)를 통과할 때 A2A 복제에 실패했습니다.
- **가능한 원인** </br>
  - 사용자 지정 프록시 설정이 잘못되었으며 Azure 사이트 복구 이동성 서비스 에이전트가 IE에서 프록시 설정을 자동으로 검색하지 않았습니다.


- **해상도**
  1. Mobility Service 에이전트는 Windows의 경우 IE에서 Linux의 경우 /etc/environment에서 프록시 설정을 검색합니다.
  2. Azure 사이트 복구 이동성 서비스에 대해서만 프록시를 설정하려는 경우 다음 위치에 있는 ProxyInfo.conf에서 프록시 세부 정보를 제공할 수 있습니다.</br>
     - ***Linux***에서 ``/usr/local/InMage/config/``
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``***윈도우에서***
  3. ProxyInfo.conf에는 다음 INI 형식의 프록시 설정이 있어야 합니다.</br>
                *[프록시]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Azure 사이트 복구 이동성 서비스 에이전트는 ***인증되지 않은 프록시만***지원합니다.

### <a name="fix-the-problem"></a>문제 해결
필요한 [URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 또는 필요한 [IP 범위를](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)허용하려면 [네트워킹 지침 문서의](site-recovery-azure-to-azure-networking-guidance.md)단계를 따르십시오.


## <a name="next-steps"></a>다음 단계
[Azure 가상 머신 복제](site-recovery-replicate-azure-to-azure.md)
