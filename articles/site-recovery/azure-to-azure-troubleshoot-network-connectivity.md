---
title: Azure 사이트 복구를 사용하여 Azure에서 Azure 재해 복구에 대한 연결 문제 해결
description: Azure VM 재해 복구에서 연결 문제 해결
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 49d2d3d3e8829198a57aaf2feb40e89f105667bd
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804863"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Azure-Azure VM 네트워크 연결 문제 해결

이 문서에서는 한 리전에서 다른 리전으로 Azure 가상 시스템(VM)을 복제하고 복구할 때 네트워크 연결과 관련된 일반적인 문제에 대해 설명합니다. 네트워킹 요구 사항에 대한 자세한 내용은 [Azure VM 복제에 대한 연결 요구 사항을](azure-to-azure-about-networking.md)참조하세요.

Site Recovery 복제가 작동하려면 VM에서 특정 URL 또는 IP 범위에 대한 아웃바운드 연결이 필요합니다. VM이 방화벽 뒤에 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 이러한 문제 중 하나가 발생할 수 있습니다.

| **URL** | **세부 정보** |
| --- | --- |
| `*.blob.core.windows.net` | VM에서 원본 지역의 캐시 스토리지 계정에 데이터를 쓸 수 있도록 하는 데 필요합니다. VM의 모든 캐시 저장소 계정을 알고 있는 경우 특정 저장소 계정 URL에 대한 허용 목록을 사용할 수 있습니다. 예를 `cache1.blob.core.windows.net` 들어, `cache2.blob.core.windows.net` 대신 `*.blob.core.windows.net`. |
| `login.microsoftonline.com` | Site Recovery 서비스 URL에 대한 권한 부여 및 인증에 필요합니다. |
| `*.hypervrecoverymanager.windowsazure.com` | VM에서 Site Recovery 서비스 통신이 발생할 수 있도록 하는 데 필요합니다. 방화벽 프록시가 IP를 지원하는 경우 해당 _사이트 복구 IP를_ 사용할 수 있습니다. |
| `*.servicebus.windows.net` | VM에서 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 하는 데 필요합니다. 방화벽 프록시가 IP를 지원하는 경우 해당 _사이트 복구 모니터링 IP를_ 사용할 수 있습니다. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 또는 IP 범위에 대한 아웃바운드 연결(오류 코드 151037 또는 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>문제 1: Site Recovery에 Azure 가상 머신을 등록하지 못했습니다(151195).

#### <a name="possible-cause"></a>가능한 원인

도메인 이름 시스템(DNS) 해결 실패로 인해 사이트 복구 끝점에 대한 연결을 시작할 수 없습니다. 이 문제는 VM을 통해 실패했지만 DNS 서버가 DR(재해 복구) 지역에서 연결할 수 없는 경우 다시 보호하는 동안 더 일반적입니다.

#### <a name="resolution"></a>해결 방법

사용자 지정 DNS를 사용하는 경우 재해 복구 지역에서 DNS 서버에 액세스할 수 있는지 확인합니다.

VM이 사용자 지정 DNS 설정을 사용하는지 확인하려면 다음을 수행하십시오.

1. **가상 컴퓨터를** 열고 VM을 선택합니다.
1. VM **설정으로** 이동하여 **네트워킹을 선택합니다.**
1. **가상 네트워크/서브넷에서**링크를 선택하여 가상 네트워크의 리소스 페이지를 엽니다.
1. **설정으로** 이동하여 **DNS 서버를 선택합니다.**

가상 컴퓨터에서 DNS 서버에 액세스해 봅을 사용해 보십시오. DNS 서버에 액세스할 수 없는 경우 DNS 서버에서 장애 또는 DR 네트워크와 DNS 간의 사이트 라인을 만들어 액세스할 수 있도록 합니다.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>문제 2: Site Recovery 구성이 실패했습니다(151196).

> [!NOTE]
> VM이 **표준** 내부 부하 분산기 뒤에 있는 경우 기본적으로 Office 365 IP와 같은 `login.microsoftonline.com`액세스 권한이 없습니다. Azure [CLI를 사용하여 표준 로드 밸런서에서 로드 분산 및 아웃바운드 규칙을](/azure/load-balancer/configure-load-balancer-outbound-cli)구성문서에서 설명한 대로 **기본** 내부 로드 밸런서 유형으로 변경하거나 아웃바운드 액세스를 만듭니다.

#### <a name="possible-cause"></a>가능한 원인

Office 365 인증 및 ID IP4 끝점에 연결을 설정할 수 없습니다.

#### <a name="resolution"></a>해결 방법

- Azure 사이트 복구인증을 위해 Office 365 IP 범위에 액세스해야 합니다.
- Azure Network 보안 그룹(NSG) 규칙/방화벽 프록시를 사용하여 VM에서 아웃바운드 네트워크 연결을 제어하는 경우 Office 365 IP 범위에 대한 통신을 허용해야 합니다. Azure AD에 해당하는 모든 IP 주소에 대한 액세스를 허용하는 [Azure Active Directory(Azure AD) 서비스](/azure/virtual-network/security-overview#service-tags) 기반 NSG 규칙을 만듭니다.
- 나중에 새 주소가 Azure AD에 추가되면 새 NSG 규칙을 만들어야 합니다.

### <a name="example-nsg-configuration"></a>NSG 구성 예제

이 예제에서는 복제할 VM에 대한 NSG 규칙을 구성하는 방법을 보여 줍니다.

- NSG 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 **HTTPS 아웃바운드** 허용 규칙을 사용하여 필요한 모든 IP 주소 범위에 대해 443을 포팅합니다.
- 이 예제는 VM 소스 위치가 **미국 동부이고** 대상 위치가 **미국 중부라고 가정합니다.**

#### <a name="nsg-rules---east-us"></a>NSG 규칙 - 미국 동부

1. 다음 스크린샷과 같이 NSG에 대한 HTTPS 아웃바운드 보안 규칙을 만듭니다. 이 예제에서는 **대상 서비스 태그**: _Storage.EastUS_ 및 **대상 포트 범위**: _443을_사용합니다.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="storage-tag":::

1. 다음 스크린샷과 같이 NSG에 대한 HTTPS 아웃바운드 보안 규칙을 만듭니다. 이 예제에서는 **대상 서비스 태그**: _AzureActiveDirector_ 및 **대상 포트 범위**: _443을_사용합니다.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. 대상 위치에 해당하는 사이트 복구 IP에 대한 HTTPS 포트 443 아웃바운드 규칙을 만듭니다.

   | **위치** | **Site Recovery IP 주소** |  **Site Recovery 모니터링 IP 주소** |
   | --- | --- | --- |
   | 미국 중부 | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>NSG 규칙 - 미국 중부

이 예제에서는 대상 지역에서 장애 조치 후 소스 영역으로 복제를 사용할 수 있도록 이러한 NSG 규칙이 필요합니다.

1. _Storage.CentralUS에_대한 HTTPS 아웃바운드 보안 규칙을 만듭니다.

   - **대상 서비스 태그**: _스토리지.CentralUS_
   - **대상 포트 범위**: _443_

1. _AzureActiveDirector._

   - **대상 서비스 태그**: _AzureActiveDirectory_
   - **대상 포트 범위**: _443_

1. 소스 위치에 해당하는 사이트 복구 IP에 대한 HTTPS 포트 443 아웃바운드 규칙을 만듭니다.

   |**위치** | **Site Recovery IP 주소** |  **Site Recovery 모니터링 IP 주소** |
   | --- | --- | --- |
   | 미국 동부 | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>문제 3: Site Recovery 구성이 실패했습니다(151197).

#### <a name="possible-cause"></a>가능한 원인

Azure 사이트 복구 서비스 끝점에 대한 연결을 설정할 수 없습니다.

#### <a name="resolution"></a>해결 방법

Azure Site Recovery는 지역에 따라 [Site Recovery IP 범위](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)에 액세스할 수 있어야 합니다. 필요한 IP 범위에 VM에서 액세스할 수 있는지 확인합니다.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>문제 4: 네트워크 트래픽이 온-프레미스 프록시 서버(151072)를 통과할 때 Azure-Azure 복제에 실패했습니다.

#### <a name="possible-cause"></a>가능한 원인

사용자 지정 프록시 설정이 잘못되었으며 Azure 사이트 복구 이동성 서비스 에이전트가 IE(인터넷 탐색기)에서 프록시 설정을 자동으로 검색하지 않았습니다.

#### <a name="resolution"></a>해결 방법

1. 모빌리티 서비스 에이전트는 Windows 및 `/etc/environment` Linux에서 IE의 프록시 설정을 검색합니다.
1. Azure 사이트 복구 이동성 서비스에 대해서만 프록시를 설정하려는 경우 다음 위치에 있는 _ProxyInfo.conf에서_ 프록시 세부 정보를 제공할 수 있습니다.

   - **리눅스**:`/usr/local/InMage/config/`
   - **윈도우**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo.conf는_ 다음과 같은 _INI_ 형식의 프록시 설정이 있어야 합니다.

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

1. Azure 사이트 복구 이동성 서비스 에이전트는 **인증되지 않은 프록시만**지원합니다.

### <a name="fix-the-problem"></a>문제 해결

필요한 [URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 또는 필요한 [IP 범위를](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)허용하려면 [네트워킹 지침 문서의](site-recovery-azure-to-azure-networking-guidance.md)단계를 따르십시오.

## <a name="next-steps"></a>다음 단계

[Azure 가상 머신 복제](site-recovery-replicate-azure-to-azure.md)
