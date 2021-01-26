---
title: Azure Stack Edge에 대 한 Azure 보안 기준
description: Azure Stack Edge 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1d7cd07ee8ae8b35d51fbdd25b34602c2e799c75
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787267"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Azure Stack Edge에 대 한 Azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 2.0](../security/benchmarks/overview.md) 의 지침을 Microsoft Azure Stack Edge에 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Azure Stack Edge에 적용 되는 관련 지침에 따라 그룹화 됩니다. Azure Stack Edge에 적용할 수 없는 **컨트롤이** 제외 되었습니다.

Azure Stack Edge가 Azure Security 벤치 마크에 완전히 매핑되는 방식을 확인 하려면 [전체 Azure Stack Edge 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-controls-v2-network-security.md)을 참조하세요.

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: 내부 트래픽에 대 한 보안 구현

**지침**: 고객이 내부 액세스를 위해 Microsoft에서 제공 하는 물리적 Azure Stack Edge 장치를 개인 네트워크에 배포 하 고 더 안전한 옵션을 제공 합니다. 예를 들어, 고객의 내부 네트워크를 통해 Azure Stack Edge 장치에 액세스할 수 있으며 고객이 구성한 IP가 필요 합니다. 또한 고객은 장치의 사용자 인터페이스에 액세스 하도록 액세스 암호를 선택 합니다. 

내부 트래픽은 다음을 통해 더욱 안전 하 게 보호 됩니다.

- TLS (전송 계층 보안) 버전 1.2은 Azure Stack에 지 장치의 Azure Portal 및 SDK 관리에 필요 합니다.

- 장치에 대 한 모든 클라이언트 액세스는 기본 보안 프로토콜로 표준 TLS 1.2를 사용 하는 로컬 웹 UI를 통해 실행 됩니다.

- 권한 있는 Azure Stack Edge Pro 장치만 고객이 Azure 구독에서 만든 Azure Stack Edge 서비스에 가입할 수 있습니다.

참조 된 링크에서 추가 정보를 확인할 수 있습니다.
 
- [Azure Stack Edge Pro GPU 장치에 액세스 하는 Windows 클라이언트에서 TLS 1.2 구성](azure-stack-edge-j-series-configure-tls-settings.md)

- [빠른 시작-GPU를 사용 하 여 Azure Stack Edge Pro 시작](azure-stack-edge-gpu-quickstart.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: 개인 네트워크를 함께 연결

**지침**: 고객은 지점 및 사이트 간 VPN (가상 사설망)을 선택 하 여 온-프레미스 개인 네트워크에서 Azure 네트워크에 Azure Stack Edge 장치를 연결할 수 있습니다. VPN은 고객의 장치에서 Azure로의 전송 계층 보안을 통해 이동 하는 데이터에 대 한 두 번째 암호화 계층을 제공 합니다. 

고객은 Azure Portal 또는 Azure PowerShell를 통해 Azure Stack Edge 장치에서 가상 개인 네트워크를 구성할 수 있습니다.

- [Azure Stack Edge Pro R 및 Azure Stack Edge 미니 R에 대 한 Azure PowerShell 스크립트를 통해 Azure VPN 구성](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Azure Stack Edge Pro GPU 장치에 액세스 하는 Windows 클라이언트에서 TLS 1.2 구성](azure-stack-edge-j-series-configure-tls-settings.md)

- [자습서: Azure Stack Edge Pro R을 위한 인증서 구성](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Azure 서비스에 대한 프라이빗 네트워크 액세스 설정

**지침**: 고객은 지점 및 사이트 간 VPN (가상 사설망)을 선택 하 여 온-프레미스 개인 네트워크에서 Azure 네트워크에 Azure Stack Edge 장치를 연결할 수 있습니다. VPN은 고객의 장치에서 Azure로의 전송 계층 보안을 통해 이동 하는 데이터에 대 한 두 번째 암호화 계층을 제공 합니다. 

- [Azure Stack Edge Pro R 및 Azure Stack Edge 미니 R에 대 한 Azure PowerShell 스크립트를 통해 Azure VPN 구성](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Azure Stack Edge Pro GPU 장치에 액세스 하는 Windows 클라이언트에서 TLS 1.2 구성](azure-stack-edge-j-series-configure-tls-settings.md)

- [자습서: Azure Stack Edge Pro R을 위한 인증서 구성](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: 외부 네트워크 공격 으로부터 응용 프로그램 및 서비스 보호

**지침**: Azure Stack Edge 장치는 고객이 구성할 수 없는 표준 Windows Server 네트워크 보호 기능을 통합 합니다.

고객은 DDoS (고급 배포 된 서비스 거부) 보호를 사용 하는 방화벽과 같은 네트워크 가상 어플라이언스를 사용 하 여 외부 공격 으로부터 Azure Stack Edge 장치와 연결 된 개인 네트워크를 보호 하도록 선택할 수 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: Azure Stack Edge 장치에서 사용 하는 끝점은 모두 Microsoft에서 관리 됩니다. 고객은 온-프레미스 시스템에 배포 하려는 추가 컨트롤을 담당 합니다.

Azure Stack Edge 장치는 자체 침입 감지 기능을 사용 하 여 서비스를 보호 합니다. 

- [Azure Stack에 지 보안 이해](azure-stack-edge-security.md)

- [Azure Stack Edge에 대 한 포트 정보](azure-stack-edge-gpu-system-requirements.md)

- [하드웨어 및 소프트웨어 침입 감지 로그 가져오기](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="identity-management"></a>ID 관리

자세한 내용은 [Azure Security Benchmark: ID 관리](../security/benchmarks/security-controls-v2-identity-management.md)를 참조하세요.

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: 애플리케이션 ID를 안전하게 자동으로 관리

**지침**: 모든 Azure Stack Edge 장치는 Azure Active Directory (Azure AD)에서 자동으로 시스템 할당 관리 id를 가집니다. 현재 관리 id는 Azure Stack Edge에서 호스트 되는 가상 컴퓨터의 클라우드 관리에 사용 됩니다.

Azure Stack Edge 장치는 로컬 액세스를 위해 잠긴 상태로 부팅 됩니다. 로컬 장치 관리자 계정의 경우 로컬 웹 사용자 인터페이스 또는 PowerShell 인터페이스를 통해 장치에 연결 하 고 강력한 암호를 설정 해야 합니다. Azure Key Vault와 같은 안전한 위치에 장치 관리자 자격 증명을 저장 하 고 관리 하 고 조직의 표준에 따라 관리자 암호를 회전 합니다.

- [암호를 통해 Azure Stack Edge 장치 보호](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: 애플리케이션 액세스에 Azure AD SSO(Single Sign-On) 사용

**지침**: Single sign-on은 Azure Stack Edge 끝점 장치에 대해 지원 되지 않습니다. 그러나 Azure AD (표준 Azure Active Directory) 기반 Single Sign-On를 사용 하 여 Azure 클라우드 리소스에 대 한 액세스를 보호 하도록 선택할 수 있습니다.

- [Azure AD를 사용 하 여 응용 프로그램 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: 모든 Azure Active Directory 기반 액세스에 강력한 인증 제어 사용

**지침**: 다단계 인증은 강력한 인증 제어 이지만 Azure Stack Edge 서비스 사용자를 위한 옵트인 기능입니다. Azure Portal에서 Azure Stack Edge 장치의 가장자리 관리와 같은 지원 되는 시나리오에 활용할 수 있습니다. Azure Stack Edge 장치에 대 한 로컬 액세스는 다단계 인증을 지원 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: 계정 변칙 모니터링 및 경고

**지침**: Azure Monitor을 사용 하 여 Azure Stack에 지 서비스에 대 한 장치 또는 컨테이너 로그를 수집 합니다. 장치의 Kubernetes 클러스터에서 여러 계산 응용 프로그램을 실행 하는 것과 같은 컨테이너를 모니터링 합니다.

또한 감사 로그를 포함 하는 지원 패키지는 Azure Stack Edge 장치의 로컬 웹 사용자 인터페이스에서 수집할 수 있습니다. 지원 패키지는 Azure Portal에서 사용할 수 없습니다.
 
- [Azure Stack Edge Pro 장치에서 Azure Monitor 사용](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [지원 패키지 수집](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: 조건에 따라 Azure 리소스 액세스 제한

**지침**: Azure Active Directory (Azure AD) 조건부 액세스는 Edge 서비스 Azure Stack 인증을 위한 옵트인 기능입니다. Azure Stack Edge 서비스는 여러 지리적 위치에서 Azure Stack Edge Pro 장치를 관리할 수 있는 Azure Portal의 리소스입니다. 

- [조건부 액세스 정의](../active-directory/conditional-access/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: 의도하지 않은 자격 증명 노출 제거

**지침**: 모범 사례에 따라 다음과 같은 자격 증명을 보호 합니다.

- Azure에서 Azure Stack Edge 리소스를 사용 하 여 장치를 활성화 하는 데 사용 되는 정품 인증 키입니다.
- 자격 증명을 로그인 하 여 Azure Stack Edge 장치에 액세스 합니다.
- Azure Stack Edge 장치의 복구를 용이 하 게 할 수 있는 주요 파일입니다.
- 채널 암호화 키

저장소 계정 키를 회전 한 다음 정기적으로 동기화 하 여 권한 없는 사용자 로부터 저장소 계정을 보호 합니다.

- [Azure Stack Edge Pro 보안 및 데이터 보호](azure-stack-edge-security.md)

- [스토리지 키 동기화](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="privileged-access"></a>권한 있는 액세스

자세한 내용은 [Azure Security Benchmark: 권한 있는 액세스](../security/benchmarks/security-controls-v2-privileged-access.md)를 참조하세요.

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: 중요 비즈니스용 시스템에 대한 관리 액세스 제한

**지침** Azure Stack: Edge 솔루션에는 업무상 중요 한 장치에 대 한 액세스를 제한 하는 강력한 액세스 제어를 포함 하는 여러 구성 요소가 있습니다. 조직에서 장치를 구성 하 고 관리 하려면 EA (기업계약) 또는 CSP (클라우드 솔루션 공급자) 또는 Microsoft Azure 스폰서쉽 구독이 필요 합니다. 

Azure Stack Edge 서비스는 azure에서 호스트 되는 관리 서비스로 Azure 보안 기능으로 보호 됩니다. 소프트웨어 개발 키트 관리 작업의 장치 속성에서 리소스에 대 한 암호화 키를 가져올 수 있지만 Graph API 리소스에 대 한 적절 한 권한이 있는 경우에만 암호화 키를 볼 수 있습니다.

Azure Stack Edge Pro 장치는 데이터를 로컬로 처리 한 다음 Azure에 전송 하 여 데이터를 변환 하는 데 도움이 되는 온-프레미스 장치입니다. 장치:

- Azure Stack Edge 서비스에 액세스 하려면 정품 인증 키가 필요 합니다.
- 는 장치 암호로 항상 보호 됩니다.
- 보안 부팅이 사용 하도록 설정 되어 있습니다.

- 는 잠긴 장치입니다. 장치 베이스 보드 관리 컨트롤러 (BMC) 및 BIOS는 암호로 보호 됩니다. BIOS는 제한 된 사용자 액세스를 통해 보호 됩니다.
- Windows Defender Device Guard를 실행 합니다. Device Guard를 사용 하면 코드 무결성 정책에서 정의한 신뢰할 수 있는 응용 프로그램만 실행할 수 있습니다.

모범 사례에 따라 Edge Azure Stack 액세스 하는 데 사용 되는 모든 자격 증명과 암호를 보호 합니다. 

- [암호 모범 사례](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: 정기적으로 사용자 액세스 권한 검토 및 조정

**지침**: Edge Azure Stack 장치를 구성할 수 있는 ' EdgeUser ' 라는 사용자가 있습니다. 또한 장치의 로컬 Azure Resource Manager 기능에 대 한 사용자 ' EdgeArmUser ' Azure Resource Manager 있습니다. 

다음을 보호 하려면 모범 사례를 따라야 합니다.

- 온-프레미스 장치에 액세스 하는 데 사용 되는 자격 증명

- SMB 공유 자격 증명.

- NFS 공유를 사용 하도록 구성 된 클라이언트 시스템에 액세스 합니다.

- Blob REST API를 사용 하는 경우 로컬 저장소 계정에 액세스 하는 데 사용 되는 로컬 저장소 계정 키입니다.

참조 된 링크에서 추가 정보를 확인할 수 있습니다.

- [Azure Stack Edge 장치의 보안에 대 한 정보](azure-stack-edge-security.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: 권한 있는 액세스 워크스테이션 사용

**지침**: 보안 및 격리 된 워크스테이션은 관리자, 개발자 및 중요 서비스 운영자와 같은 중요 한 역할의 보안에 매우 중요 합니다. 관리 작업에 대해 Azure 방호를 사용 하거나 사용 하지 않고 매우 안전한 사용자 워크스테이션을 사용 합니다. Azure Active Directory (Azure AD), Microsoft Defender ATP (Advanced Threat Protection) 및 Microsoft Intune를 사용 하 여 관리 작업을 위한 안전 하 고 관리 되는 사용자 워크스테이션을 배포할 수 있습니다. 

보안 워크스테이션을 중앙에서 관리하여 강력한 인증, 소프트웨어 및 하드웨어 기준, 제한된 논리적 액세스 및 네트워크 액세스를 비롯한 보안 구성을 적용할 수 있습니다.

- [권한 있는 액세스 워크스테이션 이해](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [권한 있는 액세스 워크스테이션 배포](/security/compass/privileged-access-deployment)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-controls-v2-data-protection.md)를 참조하세요.

### <a name="dp-2-protect-sensitive-data"></a>DP-2: 중요한 데이터 보호

**지침**: Azure Stack Edge는 모든 상호 작용 된 데이터를 권한이 있는 사용자만이 데이터에 액세스할 수 있는 중요 한 데이터를 처리 합니다. Azure Stack Edge 서비스에 액세스 하는 데 사용 되는 자격 증명을 보호 하려면 모범 사례를 따라야 합니다.

- [Azure Stack Edge Pro 보안 및 데이터 보호](azure-stack-edge-security.md#protect-the-device-via-password)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: 전송 중인 중요한 정보 암호화

**지침**: Edge에서 데이터를 비행 하는 데 보안 채널을 사용 하 Azure Stack 합니다. 해당 경고는 다음과 같습니다.

- 표준 TLS 1.2은 장치와 Azure 클라우드 간에 이동 하는 데이터에 사용 됩니다. TLS 1.1 이전 버전에는 대체가 없습니다. TLS 1.2이 지원 되지 않는 경우 에이전트 통신이 차단 됩니다. TLS 1.2는 Azure Portal 및 SDK (소프트웨어 개발 키트) 관리에도 필요 합니다.

- 클라이언트가 브라우저의 로컬 웹 사용자 인터페이스를 통해 장치에 액세스 하는 경우 표준 TLS 1.2가 기본 보안 프로토콜로 사용 됩니다.

가장 좋은 방법은 TLS 1.2를 사용 하도록 브라우저를 구성 하는 것입니다. 데이터 서버에서 데이터를 복사할 때 데이터를 보호 하려면 암호화와 함께 SMB 3.0을 사용 합니다.

- [비행 데이터를 보호 하기 위한 모범 사례](azure-stack-edge-security.md#protect-data-in-flight)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="asset-management"></a>자산 관리

자세한 내용은 [Azure Security Benchmark: 자산 관리](../security/benchmarks/security-controls-v2-asset-management.md)를 참조하세요.

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: 보안 팀에서 자산 위험에 대한 가시성을 확보하도록 보장

**지침**: Azure Security Center를 사용하여 보안 위험을 모니터링할 수 있도록 Azure 테넌트 및 구독에서 보안 읽기 권한자 권한을 보안 팀에 부여합니다. 

보안 팀의 책임이 구성된 방식에 따라 보안 위험 모니터링은 중앙 보안 팀 또는 로컬 팀의 책임이 될 수 있습니다. 즉, 보안 인사이트 및 위험이 항상 조직 내의 중앙에서 집계되어야 합니다. 

보안 읽기 권한자 권한은 전체 테넌트(루트 관리 그룹)에 광범위하게 적용하거나 범위를 관리 그룹 또는 특정 구독으로 지정할 수 있습니다. 

작업 및 서비스에 대 한 가시성을 확보 하려면 추가 권한이 필요할 수 있습니다. 

- [보안 읽기 권한자 역할 개요](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 관리 그룹 개요](../governance/management-groups/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>오전 5: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: 권한이 부여 된 사용자 (예: ' EdgeArmUser ')만 로컬 Azure Resource Manager를 통해 Azure Stack Edge 장치 api에 액세스할 수 있습니다. 사용자 계정 암호는 Azure Portal 에서만 관리할 수 있습니다. 

- [Azure Resource Manager 암호 설정](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: 계산 리소스에서 승인 된 응용 프로그램만 사용

**지침**: 로컬로 만든 가상 머신에서 실행할 수 있도록 응용 프로그램을 가져올 수 있습니다. PowerShell 스크립트를 사용 하 여 스택 Edge 장치에서 로컬 계산 가상 컴퓨터를 만듭니다. 로컬 가상 머신에서 실행할 수 있는 신뢰할 수 있는 응용 프로그램만 가져오는 것이 좋습니다. 

- [Windows 환경에서 PowerShell 스크립트 실행을 제어 하는 방법](/powershell/module/microsoft.powershell.security/set-executionpolicy?preserve-view=true&amp;viewFallbackFrom=powershell-6&view=powershell-7.1)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-threat-detection"></a>로깅 및 위협 탐지

자세한 내용은 [Azure Security Benchmark: 로깅 및 위협 탐지](../security/benchmarks/security-controls-v2-logging-threat-detection.md)를 참조하세요.

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure 리소스에 대 한 위협 감지 사용

**지침**: Azure Stack Edge는 위협 검색 기능을 제공 하지 않습니다. 그러나 고객은 지원 패키지의 감사 로그를 수집 하 여 오프 라인 위협 검색 및 분석을 수행할 수 있습니다. 

- [Azure Stack Edge 장치에 대 한 지원 패키지 수집](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure 네트워크 활동에 대한 로깅 사용

**지침**: Edge Azure Stack 다운로드 가능한 지원 패키지의 일부로 네트워크 감사 로그를 사용할 수 있습니다. 이러한 로그를 구문 분석 하 여 Azure Stack에 지 장치에 대 한 반자동 시간 모니터링을 구현할 수 있습니다.

- [지원 패키지 수집 Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure 리소스에 대한 로깅 사용

**지침**: 로그를 사용한 실시간 모니터링은 현재 Azure Stack Edge에 대해 지원 되지 않습니다. 지원 패키지를 수집 하는 기능이 있습니다. 여기에 포함 된 다양 한 로그 (예: 방화벽, 소프트웨어, 하드웨어 침입 및 Azure Stack Edge Pro 장치에 대 한 시스템 이벤트 로그)를 분석할 수 있습니다. 인바운드 및 아웃 바운드 트래픽에 대해 소프트웨어 침입이 나 기본 방화벽 로그가 수집 됩니다.

- [Azure Stack Edge에 대 한 지원 패키지 수집](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: 보안 로그 관리 및 분석 중앙 집중화

**지침**: 로그를 사용한 실시간 모니터링은 현재 Azure Stack Edge에 대해 지원 되지 않습니다. 그러나 포함 된 다양 한 로그를 분석 하는 데 사용할 수 있는 지원 패키지를 수집할 수 있습니다.  지원 패키지는 압축 되며 선택한 경로에 다운로드 됩니다. 패키지의 압축을 풀고 여기에 포함 된 시스템 로그 파일을 확인 합니다. 이러한 로그를 보안 정보 이벤트 관리 도구나 분석을 위해 다른 중앙 저장소 위치로 보낼 수도 있습니다.

- [Azure Stack Edge에 대 한 지원 패키지 수집](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: 로그 스토리지 보존 구성

**지침**: Azure Stack Edge 장치에서 로그 저장소 보존 기간을 변경할 수 없습니다. 필요에 따라 이전 로그를 제거 합니다. 오랜 시간 동안 로그를 유지 하기 위한 요구 사항에 대 한 일정 한 간격으로 장치에서 지원 패키지를 수집할 수 있습니다. 

- [Azure Stack Edge에 대 한 지원 패키지 수집](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: 승인 된 시간 동기화 원본 사용

**지침**: Edge Azure Stack Microsoft의 네트워크 시간 공급자 서버인 time.windows.com를 사용 합니다.  Azure Stack Edge를 사용 하면 고객이 선택한 네트워크 시간 프로토콜 서버를 구성할 수도 있습니다.

- [Azure Stack Edge 장치 시간 설정 구성](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)을 참조하세요.

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 준비 - Azure에 대한 인시던트 응답 프로세스 업데이트

**지침**: 조직 인시던트 대응 계획에 프로세스가 포함 되는지 확인 합니다. 

- 보안 인시던트에 대응 하려면

- Azure cloud 용으로 업데이트 되었습니다.
 
- 준비 상태를 확인 하기 위해 정기적으로 수행 됩니다.

엔터프라이즈 환경에서 표준화 된 인시던트 응답 프로세스를 사용 하 여 보안을 구현 하는 것이 좋습니다.

- [엔터프라이즈 환경에서 보안 구현](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [인시던트 응답 참조 가이드](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 준비 - 인시던트 알림 설정

**지침**: Azure Security Center에서 보안 인시던트 연락처 정보를 설정합니다. 이 연락처 정보는 MSRC(Microsoft 보안 대응 센터)가 불법적인 당사자나 권한 없는 당사자가 데이터에 액세스한 것을 발견한 경우 Microsoft가 연락하는 데 사용됩니다. 또한 인시던트 응답 요구 사항에 따라 다양한 Azure 서비스에서 인시던트 경고 및 알림을 사용자 지정하는 옵션도 있습니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 검색 및 분석 – 고품질 경고를 기반으로 인시던트 만들기 

**지침**: 고품질 경고를 만들고 품질을 측정 하는 프로세스가 있는지 확인 합니다. 이를 통해 과거 인시던트의 문제를 파악 하 고 분석에 대 한 경고의 우선 순위를 지정 하 여 처리 되지 않은 시간 처리를 방지할 수 있습니다. 다양 한 경고 소스에 대 한 로깅 및 상관 관계를 사용 하 여 경고를 생성 하 고 정리 하기 위해 설계 된 이전 인시던트, 유효성 검사 된 커뮤니티 원본 및 도구에서 사용자의 경험을 기반으로 고품질 경고를 작성 

Azure Security Center는 여러 Azure 자산에서 고품질의 경고를 제공 합니다. Security Center data connector를 사용 하 여 경고를 Azure 센티널로 스트리밍할 수 있습니다. Azure 센티널로 고급 경고 규칙을 만들어 조사를 위한 자동 인시던트를 생성 합니다. 

Azure 리소스의 위험을 식별 하는 데 도움이 되도록 내보내기 기능을 사용 하 여 Security Center 경고 및 권장 사항을 Azure 센티널로 내보냅니다. 지속적인 보안을 위해 자동화 된 방식으로 경고 및 권장 사항을 내보내는 프로세스를 만드는 것이 좋습니다.

- [내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 탐지 및 분석 – 인시던트 조사

**지침**: 분석가가 잠재적 인시던트를 조사 하는 동안 발생 한 활동에 대 한 전체 보기를 작성 하기 위해 다양 한 데이터 원본을 쿼리하고 사용할 수 있도록 합니다. 블라인드 지점을 방지 하려면 다양 한 로그 유형을 수집 하 여 kill 체인에서 잠재적인 공격자의 활동을 추적 해야 합니다.  또한 기록 참조를 위해 정보 및 학습을 캡처해야 합니다.  

조사할 데이터 원본에는 범위 내 서비스 및 실행 중인 시스템에서 이미 수집되고 있는 중앙 집중식 로깅 원본이 포함되지만 다음과 같은 원본도 포함될 수 있습니다.

- 네트워크 데이터 – 네트워크 보안 그룹의 흐름 로그, Azure Network Watcher 및 Azure Monitor를 사용하여 네트워크 흐름 로그 및 기타 분석 정보를 캡처합니다. 

- 실행 중인 시스템의 스냅샷 

    - Azure 가상 머신의 스냅샷 기능을 사용하여 실행 중인 시스템의 디스크에 대한 스냅샷을 만듭니다. 

    - 운영 체제의 네이티브 메모리 덤프 기능을 선택 하 여 실행 중인 시스템의 메모리에 대 한 스냅숏을 만듭니다.

    - Azure 서비스 또는 타사 소프트웨어 기능의 스냅숏 기능을 사용 하 여 실행 중인 시스템의 스냅숏을 만듭니다.

Azure Sentinel은 거의 모든 로그 원본 및 사례 관리 포털에서 광범위한 데이터 분석을 제공하여 인시던트의 전체 수명 주기를 관리합니다. 조사 중에 인텔리전스 정보는 추적 및 보고를 위해 인시던트에 연결할 수 있습니다. 

- [Windows 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 지원 진단 정보 및 메모리 덤프 수집](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel을 사용하여 인시던트 조사](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 탐지 및 분석 – 인시던트 우선 순위 지정

**지침**: 경고 심각도 및 자산 민감도에 따라 처음에 집중할 인시던트에 대해 분석가에게 컨텍스트를 제공합니다. Azure Security Center의 각 경고에 할당 된 심각도를 사용 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있습니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

또한 태그를 사용하여 리소스를 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 차단, 제거, 복구 - 인시던트 처리 자동화

**지침**: 수동 반복 작업을 자동화하여 응답 시간을 단축하고 분석가의 부담을 줄입니다. 수동 작업은 실행하는 데 더 오래 걸려 각 인시던트의 속도를 늦추고 분석가가 처리할 수 있는 인시던트 수를 줄입니다. 또한 수동 작업은 분석가를 지치게 하여 지연을 유발하는 인간 오류의 위험을 높이고, 복잡한 작업에 효과적으로 집중할 수 있는 분석가의 능력을 저하시킵니다. Azure Security Center 및 Azure Sentinel의 워크플로 자동화 기능을 사용하여 작업을 자동으로 트리거하거나 플레이북을 실행하여 들어오는 보안 경고에 대응합니다. 플레이북은 알림 보내기, 계정 사용 안 함 및 문제가 있는 네트워크 격리와 같은 작업을 수행합니다. 

- [Security Center에서 워크플로 자동화 구성](../security-center/workflow-automation.md)

- [Azure Security Center에서 자동화된 위협 대응 설정](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="posture-and-vulnerability-management"></a>태세 및 취약성 관리

자세한 내용은 [Azure Security Benchmark: 태세 및 취약성 관리](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)를 참조하세요.

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV 3: 계산 리소스에 대 한 보안 구성 설정

**지침**: Azure Stack Edge는 고객이 만든 로컬 가상 컴퓨터에 대 한 보안 구성을 만들기 위한 지원을 제공 합니다. 로컬 가상 컴퓨터를 만드는 동안 Microsoft에서 제공 하는 지침을 사용 하 여 보안 기준을 설정 하는 것이 좋습니다.

- [보안 준수 도구 키트에 포함 된 보안 기준을 다운로드 합니다.](/windows/security/threat-protection/windows-security-baselines)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: 계산 리소스에 대 한 보안 구성 유지

**지침** Azure Stack: Edge는 고객이 만든 로컬 가상 컴퓨터에 대 한 보안 구성을 유지할 수 있는 지원을 제공 하지 않습니다. 고객은 계산 리소스에 대 한 구성을 안전 하 게 유지 하기 위해 SCT (보안 준수 도구 키트)를 사용 하는 것이 좋습니다.

Azure Stack Edge에서 관리 하는 호스트 운영 체제 및 가상 컴퓨터는 보안 구성을 유지 합니다. 

- [Windows 보안 기준](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV 5: 사용자 지정 운영 체제 및 컨테이너 이미지를 안전 하 게 저장

**지침**: Azure Stack Edge에서 관리 하는 호스트 운영 체제 및 가상 머신은 안전 하 게 저장 됩니다. 

고객은 자신의 가상 컴퓨터 및 컨테이너 이미지를 가져올 수 있으며 보안 관리를 담당 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: 자동으로 신속하게 소프트웨어 취약성 수정

**지침**: Azure Stack Edge는 이러한 업데이트를 통해 취약점을 해결 하는 데 사용할 수 있는 정기적인 패치 업데이트 및 경고 고객을 제공 합니다. 최신 패치를 사용 하 여 장치 및 가상 머신을 최신 상태로 유지 하는 것은 고객의 책임입니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 정기적인 공격 시뮬레이션 수행

**지침**: 필요에 따라 Azure 리소스에 대한 침투 테스트 또는 레드 팀 활동을 수행하고 모든 중요한 보안 결과를 수정해야 합니다.
Microsoft Cloud 침투 테스트 시행 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다.

- [Azure의 침투 테스트](../security/fundamentals/pen-testing.md)

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="endpoint-security"></a>끝점 보안

*자세한 내용은 [Azure 보안 벤치 마크: 끝점 보안](../security/benchmarks/security-controls-v2-endpoint-security.md)을 참조 하세요.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: EDR (끝점 검색 및 응답) 사용

**지침**: Azure Stack EDGE는 edr (끝점 검색 및 응답)을 직접 지원 하지 않습니다. 그러나 지원 패키지를 수집 하 고 감사 로그를 검색할 수 있습니다. 그런 다음 이러한 로그를 분석 하 여 비정상적인 활동을 확인할 수 있습니다. 

- [Azure Stack Edge 장치에 대 한 지원 패키지 수집](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: 중앙에서 관리 되는 최신 맬웨어 방지 소프트웨어 사용

**지침**: Azure Stack Edge는 미리 결정 된 응용 프로그램 및 스크립트를 실행 하도록 허용 하는 CI (엄격한 코드 무결성) 정책과 함께 WDAC (Windows Defender 응용 프로그램 제어)를 사용 합니다. Windows Defender RTP (실시간 보호) 맬웨어 방지도 사용할 수 있습니다. 고객이 Azure Stack Edge 장치에서 로컬로 실행 하기 위해 만든 계산 Vm에서 맬웨어 방지 프로그램을 실행 하도록 선택할 수 있습니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="backup-and-recovery"></a>백업 및 복구

자세한 내용은 [Azure Security Benchmark: 백업 및 복구](../security/benchmarks/security-controls-v2-backup-recovery.md)를 참조하세요.

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: 정기적으로 자동 백업 확인

**지침**: Azure Stack Edge 장치의 정기적 백업은 권장 되며, 장치에 배포 된 가상 컴퓨터에 포함 된 데이터를 보호 하기 위해 Azure Backup 및 기타 타사 데이터 보호 솔루션을 통해 수행할 수 있습니다. 

Cohesity, Commvault 및 Veritas와 같은 타사 데이터 보호 솔루션은 로컬 SMB 또는 NFS 공유에 있는 데이터에 대 한 백업 솔루션을 제공할 수도 있습니다. 

참조 된 링크에서 추가 정보를 확인할 수 있습니다.

- [장치 오류에 대 한 준비](azure-stack-edge-gpu-prepare-device-failure.md)

- [Vm에서 파일 및 폴더 보호](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="br-2-encrypt-backup-data"></a>BR-2: 백업 데이터 암호화

**지침**: 백업이 공격 으로부터 보호 되는지 확인 합니다. 여기에는 기밀성의 손실을 방지 하기 위해 백업의 암호화가 포함 됩니다.  자세한 내용은 원하는 백업 솔루션을 참조 하 여 자세한 내용을 확인 하세요.

- [Edge 로컬 공유의 데이터 보호](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [가상 컴퓨터의 파일 및 폴더 보호](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: 고객 관리형 키를 비롯한 모든 백업 유효성 검사

**지침**: 정기적으로 백업 데이터 복원을 수행 합니다. 

- [Azure Stack Edge에 대 한 복구 절차](azure-stack-edge-gpu-recover-device-failure.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: 분실한 키의 위험 완화

**지침**: 고객 관리 키를 비롯 한 모든 Azure Stack Edge 백업은 조직의 모범 사례에 따라 보호 되도록 합니다. Azure Stack Edge 장치는 Azure에서 데이터에 대 한 대상 리포지토리로 사용 되는 Azure Storage 계정과 연결 됩니다. 

Azure Storage 계정에 대 한 액세스는 해당 저장소 계정과 연결 된 Azure 구독 및 2 512 비트 저장소 액세스 키에 의해 제어 됩니다. 액세스 키 중 하나는 Azure Stack Edge 장치가 저장소 계정에 액세스할 때 인증을 위해 사용 됩니다. 다른 키는 정기 키 회전을 위해 예약 된에 보관 되어 있습니다. 보안 모범 사례가 키 회전에 사용 되는지 확인 합니다.

- [Azure Storage 계정에서 Azure Stack Edge 장치 데이터 보호](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="governance-and-strategy"></a>거버넌스 및 전략

자세한 내용은 [Azure Security Benchmark: 거버넌스 및 전략](../security/benchmarks/security-controls-v2-governance-strategy.md)을 참조하세요.

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 자산 관리 및 데이터 보호 전략 정의 

**지침**: 시스템 및 데이터를 지속적으로 모니터링하고 보호하기 위한 명확한 전략을 문서화하고 전달해야 합니다. 중요 비즈니스용 데이터 및 시스템의 검색, 평가, 보호, 모니터링에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   비즈니스 위험에 따른 데이터 분류 표준

-   위험 및 자산 인벤토리에 대한 보안 조직의 가시성 

-   사용할 Azure 서비스에 대한 보안 조직의 승인 

-   수명 주기 전체에서 자산 보안

-   조직 데이터 분류에 따라 필요한 액세스 제어 전략

-   Azure 기본 및 타사 데이터 보호 기능 사용

-   전송 중 및 미사용 사용 사례에 대한 데이터 암호화 요구 사항

-   적절한 암호화 표준

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 아키텍처 권장 사항 - 스토리지, 데이터, 암호화](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 보안 기본 사항 - Azure 데이터 보안, 암호화, 스토리지](../security/fundamentals/encryption-overview.md)

- [클라우드 채택 프레임워크 - Azure 데이터 보안 및 암호화 모범 사례](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - 자산 관리](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark - 데이터 보호](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 엔터프라이즈 구분 전략 정의 

**지침**: ID, 네트워크, 애플리케이션, 구독, 관리 그룹 및 기타 컨트롤의 조합을 사용하여 자산에 대한 액세스를 구분하는 엔터프라이즈 전체 전략을 수립합니다.

서로 통신하고 데이터에 액세스해야 하는 시스템의 일상 작업을 사용하도록 설정해야 할 필요성과 보안 분리의 필요성을 신중하게 조정해야 합니다.

구분 전략이 네트워크 보안, ID 및 액세스 모델, 애플리케이션 권한/액세스 모델 및 사용자 프로세스 컨트롤을 비롯하여 컨트롤 형식 간에 일관되게 구현되는지 확인합니다.

- [Azure의 구분 전략에 대한 지침(동영상)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure의 구분 전략에 대한 지침(문서)](/security/compass/governance#enterprise-segmentation-strategy)

- [엔터프라이즈 구분 전략에 맞춰 네트워크 구분 조정](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: 보안 태세 관리 전략 정의

**지침**: 개별 자산과 해당 자산이 호스트되는 환경에 대한 위험을 지속적으로 측정하고 완화합니다. 게시된 애플리케이션, 네트워크 수신 및 송신 지점, 사용자 및 관리자 엔드포인트 등과 같은 고가치 자산과 노출이 많은 공격 노출 영역에 우선 순위를 지정합니다.

- [Azure Security Benchmark - 태세 및 취약성 관리](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 조직 역할, 책임, 의무 조정

**지침**: 보안 조직의 역할 및 책임에 대한 명확한 전략을 문서화하고 전달하는지 확인합니다. 보안 의사 결정에 대한 명확한 책임을 제시하고, 공유 책임 모델을 모두에게 교육하고, 클라우드를 보호하는 기술에 관해 기술 팀을 교육하는 일에 우선 순위를 지정합니다.

- [Azure 보안 모범 사례 1 – 사용자: 클라우드 보안 경험에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 보안 모범 사례 2 – 사용자: 클라우드 보안 기술에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 보안 모범 사례 3 – 프로세스: 클라우드 보안 결정에 대한 책임 할당](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: 네트워크 보안 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure 네트워크 보안 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   중앙 집중식 네트워크 관리 및 보안 책임

-   엔터프라이즈 구분 전략에 맞춰 조정된 가상 네트워크 구분 모델

-   다양한 위협 및 공격 시나리오에서 수정 전략

-   인터넷 에지 및 수신/송신 전략

-   하이브리드 클라우드 및 온-프레미스 상호 연결 전략

-   최신 네트워크 보안 아티팩트(예: 네트워크 다이어그램, 참조 네트워크 아키텍처)

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - 네트워크 보안](../security/benchmarks/index.yml)

- [Azure 네트워크 보안 개요](../security/fundamentals/network-overview.md)

- [엔터프라이즈 네트워크 아키텍처 전략](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID 및 권한 있는 액세스 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure ID 및 권한 있는 액세스 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   중앙 집중식 ID 및 인증 시스템과 다른 내부 및 외부 ID 시스템 간의 상호 연결

-   다양한 사용 사례 및 조건에서 강력한 인증 방법

-   권한이 높은 사용자 보호

-   변칙 사용자 활동 모니터링 및 처리  

-   사용자 ID 및 액세스 검토와 조정 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - ID 관리](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark - 권한 있는 액세스](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 관리 보안 개요](../security/fundamentals/identity-management-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: 로깅 및 위협 대응 전략 정의

**지침**: 규정 준수 요구 사항을 충족하면서 신속하게 위협을 탐지하고 수정할 수 있는 로깅 및 위협 대응 전략을 수립합니다. 통합 및 수동 단계가 아닌 위협에 집중할 수 있도록 분석가에게 고품질 경고 및 원활한 환경을 제공하는 것에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   보안 운영(SecOps) 조직의 역할 및 책임 

-   NIST 또는 다른 업계 프레임워크를 사용하여 잘 정의된 인시던트 응답 프로세스 

-   위협 탐지, 인시던트 응답 및 규정 준수 요구 사항을 지원하는 로그 캡처 및 보존

-   SIEM, 네이티브 Azure 기능, 기타 소스를 사용하여 위협에 대한 중앙 집중식 가시성 및 상관 관계 정보 

-   고객, 공급자 및 공공 당사자와의 통신 및 알림 계획

-   로깅 및 위협 탐지, 포렌식, 공격 수정, 제거와 같은 인시던트 처리를 위해 Azure 네이티브 및 타사 플랫폼 사용

-   확인한 상황 및 증거 보존을 포함하여 인시던트 및 인시던트 후 활동을 처리하는 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - 로깅 및 위협 탐지](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark - 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 보안 모범 사례 4 - 프로세스 클라우드에 대한 인시던트 응답 프로세스 업데이트](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 채택 프레임워크, 로깅, 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.
