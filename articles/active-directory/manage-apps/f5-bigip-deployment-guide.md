---
title: F5 키를 사용 하 여 Azure AD 보안 하이브리드 액세스 배포 가이드 | Microsoft Docs
description: 보안 하이브리드 액세스를 위해 Azure IaaS에서 F5 빅 IP 가상 버전 (VE) VM을 배포 하는 방법에 대 한 자습서
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: f962bf131b87f17712186145b8c8b8e6090f7002
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730660"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>안전한 하이브리드 액세스를 위해 Azure IaaS에서 F5 BIG-IP Virtual Edition VM을 배포하는 방법에 대한 자습서

이 자습서에서는 Azure IaaS에서 빅 IP Vitural Edition (VE)을 배포 하는 종단 간 프로세스를 안내 합니다. 이 자습서의 끝 부분에서는 다음을 수행 해야 합니다.

- SHA (Secure Hybrid Access) 개념 증명을 모델링 하기 위한 완전히 준비 된 빅 IP VM (가상 머신)

- 새 대규모 IP 시스템 업데이트 및 핫픽스를 테스트 하는 데 사용할 준비 인스턴스입니다.

## <a name="prerequisites"></a>필수 구성 요소

이전에는 F5 큰 IP 환경이 나 지식이 필요 하지 않지만, [F5 큰 ip 용어로](https://www.f5.com/services/resources/glossary)익숙해질 하는 것이 좋습니다. SHA에 대해 Azure에서 큰 IP를 배포 하려면 다음이 필요 합니다.

- 유료 Azure 구독 또는 12 개월 무료 [평가판 구독](https://azure.microsoft.com/free/).

- 다음 F5 큰 IP 라이선스 Sku 중 하나

  - F5 빅 IP® 최상의 번들

  - F5 BIG-IP APM(Access Policy Manager)™ 독립 실행형 라이선스

  - 기존 빅 IP F5 빅 ip® 로컬 Traffic Manager™ (LTM)에 대 한 F5 빅 IP 액세스 정책 관리자™ (APM) 추가 기능 라이선스
  
  - 90-day BIG IP 전체 기능 [평가판 라이선스](https://www.f5.com/trial/big-ip-trial.php).

- SSL (Secure Socket Layer)을 통해 웹 응용 프로그램을 게시 하는 와일드 카드 또는 SAN (주체 대체 이름) 인증서. [암호화](https://letsencrypt.org/) 는 테스트를 위해 무료 90 일 인증서를 제공 합니다.

- BIG-IPs 관리 인터페이스를 보호 하기 위한 SSL 인증서입니다. 웹 앱을 게시 하는 데 사용 되는 인증서의 제목이 큰 IP의 FQDN (정규화 된 도메인 이름)에 해당 하는 경우 사용할 수 있습니다. 예를 들어 subject *. contoso.com로 정의 된 와일드 카드 인증서는 다음과 같은 경우에 적합 합니다. `https://big-ip-vm.contoso.com:8443`

VM 배포 및 기본 시스템 configs 30 분 정도 걸립니다 .이 시점에서 [여기](f5-aad-integration.md)에 나열 된 SHA 시나리오를 구현할 준비가 된 것입니다.

시나리오를 테스트 하기 위해이 자습서에서는 Active Directory (AD) 환경을 포함 하는 Azure 리소스 그룹에 큰 IP가 배포 되어 있다고 가정 합니다. 환경을 DC (도메인 컨트롤러) 및 웹 호스트 (IIS) Vm으로 구성 해야 합니다. 이러한 서버를 다른 위치에 있는 대용량 IP VM에 두는 것도 좋습니다. 이러한 서버는 지정 된 시나리오를 지 원하는 데 필요한 각 역할에 대 한 시야를 제공 합니다. VPN 연결을 통해 큰 IP VM을 다른 환경에 연결 하는 시나리오도 지원 됩니다.

테스트를 위해 여기에 언급 된 항목이 없는 경우이 [스크립트](https://github.com/Rainier-MSFT/Cloud_Identity_Lab)를 사용 하 여 전체 AD 도메인 환경을 Azure에 배포할 수 있습니다. 이 [스크립팅된 자동화](https://github.com/jeevanbisht/DemoSuite)를 사용 하 여 샘플 테스트 응용 프로그램의 컬렉션을 프로그래밍 방식으로 IIS 웹 호스트에 배포할 수도 있습니다.

>[!NOTE]
>[Azure Portal](https://portal.azure.com/#home) 지속적으로 진화 하 고 있으므로이 자습서의 일부 단계는 Azure Portal에서 관찰 된 실제 레이아웃과 다를 수 있습니다.

## <a name="azure-deployment"></a>Azure 배포

큰 IP를 다양 한 토폴로지에 배포할 수 있습니다. 이 가이드에서는 단일 NIC (네트워크 인터페이스) 배포에 대해 집중적으로 설명 합니다. 그러나 대규모 IP 배포에 고가용성, 네트워크 분리 또는 1gb 이상의 처리량을 위한 여러 네트워크 인터페이스가 필요한 경우 [ARM (Azure Resource Manager f 5 bigip) 템플릿을](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)사용 하는 것이 좋습니다.

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps)에서 큰 IP VE를 배포 하려면 다음 작업을 완료 합니다.

1. Vm을 만들 수 있는 권한이 있는 계정으로 [Azure Portal](https://portal.azure.com/#home) 에 로그인 합니다. 예: 기여자

2. 위쪽 리본 검색 상자에 **marketplace** 를 입력 한 다음 **Enter 키를 누르십시오** .

3. Marketplace 필터에 **F5 키** 를 입력 하 고 다음을 **입력** 합니다.

4. 위쪽 리본에서 **+ 추가** 를 선택 하 고 marketplace 필터에 **F5 키** 를 입력 한 다음 **Enter** 키를 눌러

5. **F5 빅 ip 가상 버전 (byol)** 선택  >  **소프트웨어 계획 선택**  >  **f5 빅 IP VE (byol, 2 개의 부팅 위치)**

6. **만들기** 를 선택합니다.

![이미지에 소프트웨어 요금제를 선택 하는 단계가 표시 됩니다.](./media/f5ve-deployment-plan/software-plan.png)

7. **기본** 메뉴를 단계별로 실행 하 고 다음 설정을 사용 합니다.

 |  프로젝트 세부 정보     |  값     |
 |:-------|:--------|
 |Subscription|큰 IP VM 배포에 대 한 대상 구독|
 |Resource group | 기존 Azure 리소스 그룹 대규모 IP VM을 배포 하거나 하나를 만듭니다. DC 및 IIS Vm의 동일한 리소스 그룹 이어야 합니다.|
 | **인스턴스 세부 정보**|  |
 |VM 이름| 예제 큰 IP-VM |
 |지역 | 빅 IP VM에 대 한 대상 Azure 지역 |
 |가용성 옵션| 프로덕션에서 VM을 사용 하는 경우에만 사용|
 |이미지| F5 빅 IP VE-모두 (BYOL, 2 개의 부팅 위치)|
 |Azure Spot 인스턴스| 아니요, 적절 한 경우 자유롭게 사용 하도록 설정 합니다. |
 |크기| 최소 사양은 2 개의 vCPUs와 8gb 메모리 여야 합니다.|
 |**관리자 계정**|  |
 |인증 유형|지금은 암호를 선택 합니다. 나중에 키 쌍으로 전환할 수 있습니다. |
 |사용자 이름|관리 인터페이스에 액세스 하기 위한 큰 IP 로컬 계정으로 만들어지는 id입니다. 사용자 이름은 대/소문자를 구분 합니다.|
 |암호|강력한 암호로 관리자 액세스 보안|
 |**인바운드 포트 규칙**|  |
 |공용 인바운드 포트|없음|

8. 모든 기본값 **을 그대로 유지** 하 고 **다음: 네트워킹** 을 선택 합니다.

9. **네트워킹** 메뉴에서 다음 설정을 완료 합니다.

 |네트워크 인터페이스|      값 |
 |:--------------|:----------------|
 |가상 네트워크|DC 및 IIS Vm에서 사용 하는 것과 동일한 Azure VNet 또는 하나 만들기|
 |서브넷| DC 및 IIS Vm과 동일한 Azure 내부 서브넷 또는 하나 만들기|
 |공용 IP |  없음|
 |NIC 네트워크 보안 그룹| 이전 단계에서 선택한 Azure 서브넷이 NSG (네트워크 보안 그룹)에 이미 연결 되어 있는 경우 없음을 선택 합니다. 그렇지 않으면 기본 선택|
 |네트워킹 가속화| 끄기 |
 |**부하 분산**.|     |
 |VM 부하 분산| 아니요|

10. **다음: 관리** 를 선택 하 고이 설정을 완료 합니다.

 |모니터링|    값 |
 |:---------|:-----|
 |자세한 모니터링| 끄기|
 |부트 진단|사용자 지정 저장소 계정을 사용 하 여 사용 합니다. Azure Portal의 직렬 콘솔 옵션을 통해 SSH (빅 IP Secure Shell) 인터페이스에 연결할 수 있습니다. 사용 가능한 Azure storage 계정을 선택 합니다.|
 |**ID**|  |
 |시스템 할당 관리 ID|끄기|
 |Azure Active Directory|이 옵션은 현재 큰 IP에서 지원 되지 않습니다.|
 |**자동 종료**|    |
 |자동 종료 사용| 테스트 하는 경우 매일 VM을 종료 하도록 설정 하는 것이 좋습니다.|

11. **다음: 고급** 을 선택 하 고 모든 기본값을 그대로 두고 **다음: 태그** 를 선택 합니다.

12. 배포를 시작 하기 전에 **만들기** 를 선택 하기 전에 **다음: 검토 + 만들기** 를 선택 하 여 대규모 IP VM 구성을 검토 합니다.

13. 큰 IP VM을 완벽 하 게 배포 하는 데 걸리는 시간은 일반적으로 5 분입니다. 완료 되 면 **리소스로 이동** 을 선택 하지 않고 Azure Portal의 왼쪽 메뉴를 확장 하 고 **리소스 그룹** 을 선택 하 여 새 빅 IP VM으로 이동 합니다. VM을 만들지 못한 경우 **뒤로** 및 **다음** 을 선택 합니다.

## <a name="network-configuration"></a>네트워크 구성

대규모 IP VM이 처음 부팅 될 때 해당 NIC는 연결 된 Azure 서브넷의 DHCP (Dynamic Host Configuration Protocol) 서비스에서 발급 한 **기본** 개인 IP를 사용 하 여 프로 비전 됩니다. 이 IP는 다음과 통신 하기 위해 TMOS (Traffic Management 운영 체제)에서 사용 됩니다.

- 다른 호스트 및 서비스와 통신

- 공용 인터넷에 대 한 아웃 바운드 액세스

- BIG-IPs 웹 구성 및 SSH 관리 인터페이스에 대 한 인바운드 액세스

이러한 관리 인터페이스 중 하나를 인터넷에 노출 하면 BIG-IPs 공격 노출 영역이 늘어나지만 배포 중에 BIG-IPs 기본 IP가 공용 IP로 프로 비전 되지 않습니다. 대신, 게시 서비스를 위해 보조 내부 IP 및 연결 된 공용 IP가 프로 비전 됩니다.
VM 공용 IP와 개인 IP 간의이 1-1을 매핑하면 외부 트래픽이 VM에 도달할 수 있습니다. 그러나 방화벽과 동일한 방식으로 트래픽을 허용 하는 Azure NSG 규칙도 필요 합니다.

이 다이어그램에서는 일반적인 운영 및 관리를 위해 기본 IP를 사용 하 여 구성 되 고 서비스 게시를 위한 별도의 가상 서버 Ip를 사용 하 여 Azure에서 대규모 IP VE의 단일 NIC 배포를 보여 줍니다.
이 정렬에서는 NSG 규칙을 사용 하 여에 대해 향하는 원격 트래픽이 `intranet.contoso.com` 게시 된 서비스의 공용 ip로 라우팅됩니다.

![이미지는 기본적으로 단일 nic 배포 ](./media/f5ve-deployment-plan/single-nic-deployment.png) 를 보여 줍니다. Azure vm에 발급 된 개인 및 공용 ip는 항상 동적 이므로 VM을 다시 시작할 때마다 변경 될 가능성이 있습니다. BIG-IPs 관리 IP를 고정으로 변경 하 고 서비스 게시에 사용 되는 보조 Ip와 동일 하 게 수행 하 여 예측할 수 없는 연결 문제를 방지 합니다.

1. 큰 IP VM의 메뉴에서 **설정**  >  **네트워킹** 으로 이동 합니다.

2. 네트워킹 보기에서 **네트워크 인터페이스** 의 오른쪽에 있는 링크를 선택 합니다.

![이미지에 네트워크 구성이 표시 됩니다.](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>VM 이름은 배포 중에 임의로 생성 됩니다.

3. 왼쪽 창에서 **IP 구성** 을 선택 하 고 **ipconfig1** row를 선택 합니다.

4. **IP 할당** 옵션을 정적으로 설정 하 고 필요한 경우 큰 ip VM 기본 IP 주소를 변경 합니다. 그런 다음 **저장** 을 선택 하 고 ipconfig1 메뉴를 닫습니다.

>[!NOTE]
>이 기본 IP를 사용 하 여 빅 IP VM을 연결 하 고 관리 합니다.

5. 위쪽 리본에서 **+ 추가** 를 선택 하 고 보조 개인 IP (예: vm1nic2-ipconfig2)의 이름을 제공 합니다.

6. 개인 IP 주소 설정의 **할당** 옵션을 **고정** 으로 설정 합니다. 다음으로 높거나 낮은 연속 IP를 제공 하면 정보를 계속 유지할 수 있습니다.

7. **연결할** 공용 IP 주소를 설정 하 고 **만들기** 를 선택 합니다.

8. 새 공용 IP 주소의 이름을 입력 합니다 (예: 큰 IP-VM_ipconfig2_Public

9. 메시지가 표시 되 면 **SKU** 를 표준으로 설정 합니다.

10. 메시지가 표시 되 면 **계층** 을 **전역** 으로 설정 하 고

11. **할당** 옵션을 **정적** 으로 설정 하 고 **확인** 을 두 번 선택 합니다.

이제 다음을 사용 하 여 빅 IP VM을 설정할 수 있습니다.

- **기본 개인 IP**: 웹 구성 유틸리티 및 SSH를 통해 빅 ip VM을 관리 하는 전용입니다. 이는 단일 IP 시스템에서 **자체 ip** 로 게시 된 백 엔드 서비스에 연결 하는 데 사용 됩니다. 또한 NTP, AD, LDAP 등의 외부 서비스에 연결 합니다.

- **보조 개인 IP**: 게시 된 서비스에 대 한 인바운드 요청을 수신 대기 하는 큰 IP APM 가상 서버를 만들 때 사용 됩니다.

- **공용 IP**: 보조 개인 IP와 연결 되어 공용 인터넷의 클라이언트 트래픽이 게시 된 서비스에 대 한 큰 IP 가상 서버에 연결할 수 있습니다.

이 예제에서는 VM 공용 및 개인 Ip 간의 일대일 관계를 보여 줍니다. Azure VM NIC에는 기본 IP가 하나만 있을 수 있으며, 추가로 생성 된 ip는 항상 보조로 참조 됩니다.

>[!NOTE]
>빅 IP 서비스를 게시 하려면 보조 IP 매핑이 필요 합니다.

![이 이미지는 모든 보조 Ip를 표시 합니다.](./media/f5ve-deployment-plan/secondary-ips.png)

대규모 IP **액세스 기반 구성을** 사용 하 여 SHA를 구현 하려면 5-11 단계를 반복 하 여 대규모 ip APM을 통해 게시할 모든 추가 서비스에 대 한 개인 및 공용 IP 쌍을 추가로 만듭니다. BIG-IPs **고급 구성을** 사용 하 여 서비스를 게시 하는 경우에도 동일한 방법을 사용할 수 있습니다. 그러나이 시나리오에서는 [SNI (서버 이름 표시기)](https://support.f5.com/csp/#/article/K13452) 구성을 사용 하 여 공용 IP 오버 헤드를 방지할 수 있는 옵션이 있습니다. 이 구성에서 대규모 IP 가상 서버는 수신 하는 모든 클라이언트 트래픽을 수락 하 고 대상으로 라우팅합니다.

## <a name="dns-configuration"></a>DNS 구성

클라이언트에서 게시 된 SHA 서비스를 빅 IP VM의 공용 IP로 확인 하도록 DNS를 구성 하는 것이 중요 합니다.

다음 단계는 SHA 서비스에 사용 되는 공용 도메인의 DNS 영역을 Azure에서 관리 한다고 가정 합니다. 그러나 로케이터 레코드를 만드는 동일한 DNS 원칙은 DNS 영역을 관리 하는 위치에 관계 없이 계속 적용 됩니다.

1. 아직 열려 있지 않은 경우 포털의 왼쪽 메뉴를 확장 하 고 **리소스 그룹** 옵션을 통해 빅 IP VM으로 이동 합니다.

2. 큰 IP VM의 메뉴에서 **설정**  >  **네트워킹** 으로 이동 합니다.

3. 큰 IP Vm 네트워킹 보기의 드롭다운 IP 구성 목록에서 첫 번째 보조 IP를 선택 하 고 해당 **NIC 공용 ip** 에 대 한 링크를 선택 합니다.

![NIC 공용 IP를 표시 하는 스크린샷](./media/f5ve-deployment-plan/networking.png)

4. 왼쪽 창의 **설정** 섹션 아래에서 **구성** 을 선택 하 여 선택한 보조 IP에 대 한 공용 ip 및 DNS 속성 메뉴를 엽니다.

5. 별칭 레코드를 선택 하 고 **만들고** 드롭다운 목록에서 **DNS 영역** 을 선택 합니다. DNS 영역이 아직 없는 경우 Azure 외부에서 관리 되거나 Azure AD에서 확인 된 도메인 접미사에 대해 만들 수 있습니다.

6. 다음 세부 정보를 사용 하 여 첫 번째 DNS 별칭 레코드를 만듭니다.

 | 필드 | 값 |
 |:-------|:-----------|
 |Subscription| 큰 IP VM과 동일한 구독|
 |DNS 영역| 게시 된 웹 사이트에서 사용 하는 확인 된 도메인 접미사에 대 한 권한이 있는 DNS 영역 (예: www.contoso.com) |
 |Name | 지정한 호스트 이름이 선택한 보조 IP와 연결 된 공용 IP로 확인 됩니다. IP 매핑에 대 한 올바른 DNS를 정의 해야 합니다. Intranet.contoso.com > 13.77.148.215와 같은 네트워킹에서 마지막 이미지 configs 섹션을 참조 하세요.|
 | TTL | 1 |
 |TTL 단위 | 시간 |

7. Azure에 대 한 **만들기** 를 선택 하 여 해당 설정을 공용 DNS에 저장 합니다.

8. **DNS 이름 레이블 (선택 사항)** 을 그대로 유지 하 고 **저장** 을 선택 하 여 공용 IP 메뉴를 닫습니다.

9. 1 ~ 6 단계를 반복 하 여 대규모 IP의 단계별 구성을 사용 하 여 게시할 모든 서비스에 대해 추가 DNS 레코드를 만듭니다.

  DNS 레코드를 사용 하는 경우 [dns 검사기](https://dnschecker.org/) 와 같은 온라인 도구를 사용 하 여 생성 된 레코드가 모든 전역 공용 DNS 서버에서 성공적으로 전파 되었는지 확인할 수 있습니다.

  [Godaddy](https://www.godaddy.com/)와 같은 외부 공급자를 사용 하 여 dns 도메인 네임 스페이스를 관리 하는 경우 자체 dns 관리 기능을 사용 하 여 레코드를 만들어야 합니다.

>[!NOTE]
>또한 테스트 하 고 자주 DNS 레코드를 전환 하는 경우 PC의 로컬 호스트 파일을 사용할 수 있습니다. Windows PC의 localhosts 파일은 키보드에서 Win + R을 누르고 실행 상자에 word **드라이버** 를 제출 하 여 액세스할 수 있습니다. Localhost 레코드는 다른 클라이언트가 아닌 로컬 PC에 대 한 DNS 확인만 제공 한다는 것에 유의 해야 합니다.

## <a name="client-traffic"></a>클라이언트 트래픽

기본적으로 Azure Vnet 및 연결 된 서브넷은 인터넷 트래픽을 수신할 수 없는 개인 네트워크입니다. 대규모 IP VM의 NIC는 배포 중에 지정 된 NSG에 연결 되어야 합니다. 외부 웹 트래픽이 큰 IP VM에 도달 하려면 공용 인터넷에서 포트 443 (HTTPS) 및 80 (HTTP)를 허용 하도록 인바운드 NSG 규칙을 정의 해야 합니다.

1. 큰 IP VM의 기본 **개요** 메뉴에서 **네트워킹** 을 선택 합니다.

2. 인바운드 규칙 **추가** 를 선택 하 여 다음 nsg 규칙 속성을 입력 합니다.

 |     필드   |   값        |
 |:------------|:------------|
 |원본| 모두|
 |원본 포트 범위| *|
 |대상 IP 주소|모든 큰 IP VM 보조 개인 Ip를 쉼표로 구분한 목록입니다.|
 |대상 포트| 80,443|
 |프로토콜| TCP |
 |작업| 허용|
 |우선 순위|100-4096 사이의 사용 가능한 최소값|
 |Name | 설명이 포함 된 이름입니다. 예를 들면 다음과 같습니다. `BIG-IP-VM_Web_Services_80_443`|

3. **추가** 를 선택 하 여 변경 내용을 커밋하고 **네트워킹** 메뉴를 닫습니다.

모든 위치의 HTTP 및 HTTPS 트래픽이 이제 모든 빅 IP Vm 보조 인터페이스에 연결할 수 있습니다. 포트 80을 허용 하는 것은 사용자가 HTTP에서 HTTPS로 사용자를 자동으로 리디렉션할 수 있도록 하는 데 유용 합니다. 필요할 때마다이 규칙을 편집 하 여 대상 Ip를 추가 하거나 제거할 수 있습니다.

## <a name="manage-big-ip"></a>큰 IP 관리

큰 IP 시스템은 다음과 같은 권장 방법 중 하나를 사용 하 여 액세스할 수 있는 웹 구성 UI를 통해 관리 됩니다.

- 큰 IP의 내부 네트워크 내 컴퓨터에서

- 큰 IP VM의 내부 네트워크에 연결 된 VPN 클라이언트에서

- [Azure AD 응용 프로그램 프록시](./application-proxy-add-on-premises-application.md) 를 통해 게시 됨

나머지 구성으로 진행 하기 전에 가장 적합 한 방법을 결정 해야 합니다. 필요한 경우 공용 IP를 사용 하 여 큰 IP의 기본 IP를 구성 하 여 인터넷에서 웹 구성에 직접 연결할 수 있습니다. 그런 다음 해당 주 IP에 대 한 8443 트래픽을 허용 하는 NSG 규칙을 추가 합니다. 원본을 신뢰할 수 있는 IP로 제한 해야 합니다. 그렇지 않으면 모든 사용자가 연결할 수 있습니다.

준비가 되 면 큰 IP VM의 웹 구성에 연결할 수 있는지 확인 하 고 VM 배포 중에 지정 된 자격 증명으로 로그인 합니다.

- 내부 네트워크의 VM 또는 VPN을 통해 연결 하는 경우 BIG-IPs 기본 IP 및 웹 구성 포트에 직접 연결 합니다. 예: `https://<BIG-IP-VM_Primary_IP:8443` 브라우저에 안전 하지 않은 연결에 대 한 메시지가 표시 되지만, 큰 IP가 구성 될 때까지 확인 메시지는 무시 해도 됩니다. 브라우저에서 액세스를 차단 하는 경우 해당 캐시를 지운 후 다시 시도 insists.

- 응용 프로그램 프록시를 통해 웹 구성을 게시 한 경우 포트를 추가 하지 않고 외부에서 웹 구성에 액세스 하기 위해 정의 된 URL을 사용 합니다 (예:) `https://big-ip-vm.contoso.com` . 웹 구성 포트를 사용 하 여 내부 URL을 정의 해야 합니다 (예:). `https://big-ip-vm.contoso.com:8443` 

일반적으로 CLI (명령줄) 작업 및 루트 수준 액세스에 사용 되는 기본 SSH 환경을 통해 큰 IP 시스템을 관리할 수도 있습니다. 다음을 포함 하 여 CLI에 연결 하기 위한 몇 가지 옵션이 있습니다.

- [Azure 방호 서비스](../../bastion/bastion-overview.md): 모든 위치에서 vNET 내의 모든 VM에 대 한 빠르고 안전한 연결을 허용 합니다.

- JIT 접근 방식을 통해 PuTTY와 같은 SSH 클라이언트를 통해 직접 연결

- 직렬 콘솔: 포털에서 Vm 메뉴의 지원 및 문제 해결 섹션 아래에 제공 됩니다. 파일 전송은 지원 하지 않습니다.

- 웹 구성과 마찬가지로 공용 IP를 사용 하 여 빅 IP의 기본 IP를 구성 하 고 SSH 트래픽을 허용 하는 NSG 규칙을 추가 하 여 인터넷에서 CLI에 직접 연결할 수도 있습니다. 이 방법을 사용 하는 경우에는 원본을 신뢰할 수 있는 IP로 제한 해야 합니다.  

## <a name="big-ip-license"></a>큰 IP 라이선스

서비스 및 SHA 게시를 위해 구성 하려면 먼저, APM 모듈을 사용 하 여 큰 IP 시스템을 활성화 하 고 프로 비전 해야 합니다.

1. 웹 구성으로 다시 로그인 하 고 **일반 속성** 페이지에서 **활성화** 를 선택 합니다.

2. **기본 등록 키** 필드에 F5 키로 제공 되는 대/소문자를 구분 하는 키를 입력 합니다.

3. **활성화 방법** 을 **자동** 으로 설정 된 채로 두고 **다음** 을 선택 하면, 빅 IP가 라이선스의 유효성을 검사 하 고 EULA (최종 사용자 사용권 계약)를 표시 합니다.

4. **수락** 을 선택 하 고 활성화가 완료 될 때까지 기다린 후 **계속** 을 선택 합니다.

5. 다시 로그인 하 고 라이선스 요약 페이지의 아래쪽에서 **다음** 을 선택 합니다. 이제 큰 IP에는 SHA에 필요한 다양 한 기능을 제공 하는 모듈 목록이 표시 됩니다. 이 메시지가 표시 되지 않으면 주 탭에서 **시스템**  >  **리소스 프로 비전** 으로 이동 합니다.

6. 액세스 정책 (APM)의 프로 비전 열을 확인 합니다.

![이미지는 액세스 프로 비전을 보여 줍니다.](./media/f5ve-deployment-plan/access-provisioning.png)

7. **제출** 을 선택 하 고 경고를 수락 합니다.

8. 환자를 확보 하 고 큰 IP가 새 기능을 완료 하기를 기다립니다. 완전히 초기화 되기 전에 여러 번 순환할 수 있습니다. 

9. 준비가 되 면 **계속** 을 선택 하 고 **정보** 탭에서 **설치 유틸리티 실행** 을 선택 합니다.

>[!IMPORTANT]
>F5 라이선스는 한 번에 하나의 큰 IP VE 인스턴스에서 사용 되는 것으로 제한 됩니다. 한 인스턴스에서 다른 인스턴스로 라이선스를 마이그레이션해야 하는 이유가 있을 수 있습니다 .이 경우 서비스를 해제 하기 전에 활성 인스턴스에서 평가판 라이선스를 [취소](https://support.f5.com/csp/article/K41458656) 해야 합니다. 그렇지 않으면 라이선스가 영구적으로 손실 됩니다.

## <a name="provision-big-ip"></a>빅 IP 프로 비전

BIG-IPs 웹 구성에 대 한 관리 트래픽을 보호 하는 것이 가장 중요 합니다. 웹 구성 채널이 손상 되지 않도록 보호할 수 있도록 장치 관리 인증서를 구성 합니다.

1. 왼쪽 탐색 모음에서 **시스템**  >  **인증서 관리**  >  **트래픽 인증서 관리**  >  **SSL 인증서 목록**  >  **가져오기** 로 이동 합니다.

2. **가져오기 유형** 드롭다운 목록에서 **PKCS 12 (IIS)** 를 선택 하 고 **파일을 선택** 합니다. FQDN을 포함 하는 주체 이름 또는 SAN이 있는 SSL 웹 인증서를 찾습니다. 다음 몇 단계에서 큰 IP VM을 할당 합니다.

3. 인증서에 대 한 암호를 제공한 다음 **가져오기** 를 선택 합니다.

4. 왼쪽 탐색 모음에서 **시스템**  >  **플랫폼** 으로 이동 합니다.

5. 해당 환경에 대 한 정규화 된 호스트 이름 및 표준 시간대를 사용 하 여 빅 IP VM을 구성 하 고 다음 **업데이트** 를 수행 합니다.

![이미지가 일반 속성을 표시 합니다.](./media/f5ve-deployment-plan/general-properties.png)

6. 왼쪽 탐색 모음에서 **시스템**  >  **구성**  >  **장치**  >  **NTP** 로 이동 합니다.

7. 안정적인 NTP 원본을 지정 하 고 **추가**, **업데이트** 를 차례로 선택 합니다. 예를 들어 `time.windows.com`

이제 이전 단계에서 지정 된 BIG-IPs FQDN을 기본 개인 IP로 확인 하는 데 DNS 레코드가 필요 합니다. 사용자 환경의 내부 DNS 또는 큰 IP의 웹 구성에 연결 하는 데 사용 되는 PC의 localhost 파일에 레코드를 추가 해야 합니다. 어느 쪽이 든 웹 구성에 직접 연결할 때 브라우저 경고가 더 이상 표시 되지 않습니다. 즉, 응용 프로그램 프록시 또는 다른 역방향 프록시를 통해 서는 안 됩니다.

## <a name="ssl-profile"></a>SSL 프로필

역방향 프록시는 빅 IP 시스템이 단순 전달 서비스 (투명 프록시 또는 클라이언트와 서버 간 교환에 적극적으로 참여 하는 전체 프록시)로 동작할 수 있습니다.
전체 프록시는 두 개의 고유 연결을 만듭니다. 즉, 중간에 소프트 간격이 결합 된 별도의 백 엔드 TCP 서버 연결과 함께 프런트 엔드 TCP 클라이언트 연결을 만듭니다. 클라이언트는 **가상 서버** 라고도 하는 한쪽 끝에서 프록시 수신기에 연결 하 고, 프록시는 백엔드 서버와 별도의 독립적인 연결을 설정 합니다. 양쪽에서 양방향입니다.
이 전체 프록시 모드에서 F5 빅 IP 시스템은 트래픽을 검사할 수 있으므로 요청 및 응답과 상호 작용 하기도 가능 합니다. 부하 분산 및 웹 성능 최적화와 같은 특정 기능 및 응용 프로그램 계층 보안, 웹 가속, 페이지 라우팅 및 보안 원격 액세스와 같은 고급 트래픽 관리 서비스는이 기능을 사용 합니다.
SSL 기반 서비스를 게시할 때 클라이언트와 백 엔드 서비스 간의 트래픽 암호 해독 및 암호화 프로세스는 빅 IP SSL 프로필에 의해 처리 됩니다.

다음과 같은 두 가지 유형의 프로필이 있습니다.

- **클라이언트 ssl**: ssl을 사용 하 여 내부 서비스를 게시 하는 큰 IP 시스템을 설정 하는 가장 일반적인 방법은 클라이언트 ssl 프로필을 만드는 것입니다. 클라이언트 SSL 프로필을 사용 하면 대규모 IP 시스템에서 인바운드 클라이언트 요청을 다운스트림 서비스로 보내기 전에 암호를 해독할 수 있습니다. 그런 다음 클라이언트에 전송 하기 전에 아웃 바운드 백 엔드 응답을 암호화 합니다.

- **서버 ssl**: HTTPS에 대해 구성 된 백 엔드 서비스의 경우 서버 SSL 프로필을 사용 하도록 빅 IP를 구성할 수 있습니다. 서버 SSL 프로필을 사용 하는 경우,이를 대상 백 엔드 서비스로 보내기 전에 빅 IP가 클라이언트 요청을 다시 암호화 합니다. 서버에서 암호화 된 응답을 반환 하는 경우에는 구성 된 클라이언트 SSL 프로필을 통해 클라이언트에 전송 하기 전에 큰 IP 시스템에서 응답을 해독 하 고 다시 암호화 합니다.

클라이언트 및 서버 SSL 프로필 둘 다의 프로 비전에는 모든 SHA 시나리오에서 사용할 수 있는 큰 IP가 미리 구성 되어 있습니다.

1. 왼쪽 탐색 모음에서 **시스템**  >  **인증서 관리**  >  **트래픽 인증서 관리**  >  **SSL 인증서 목록**  >  **가져오기** 로 이동 합니다.

2. **가져오기 유형** 드롭다운 목록에서 **PKCS 12 (IIS)** 를 선택 합니다.

3. 가져온 인증서의 이름 (예:)을 입력 합니다.  `ContosoWilcardCert`

4. **파일 선택** 을 선택 하 여 게시 된 서비스에 대해 사용 하려는 도메인 접미사에 해당 하는 주체 이름을 가진 SSL 웹 인증서를 찾습니다.

5. 가져온 인증서의 **암호** 를 제공한 다음 **가져오기** 를 선택 합니다.

6. 왼쪽 탐색 모음에서 **로컬 트래픽**  >  **프로필**  >  **SSL**  >  **클라이언트** 로 이동한 후 **만들기** 를 선택 합니다.

7. **새 클라이언트 Ssl 프로필** 페이지에서 새 클라이언트 ssl 프로필에 대 한 고유 이름을 제공 하 고 부모 프로필이 **clientssl** 로 설정 되었는지 확인 합니다.

![이미지에 업데이트 큰 ip가 표시 됩니다.](./media/f5ve-deployment-plan/client-ssl.png)

8. **인증서 키 체인** 행에서 오른쪽 끝 확인란을 선택 하 고 **추가** 를 선택 합니다.

9. 세 개의 드롭다운 목록에서 암호 없이 가져온 와일드 카드 인증서를 선택 하 고 완료 **추가** 를 선택  >  합니다.

![이 이미지는 업데이트 빅 ip contoso 와일드 카드를 표시 합니다.](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. 6-9 단계를 반복 하 여 **SSL 서버 인증서 프로필** 을 만듭니다. 위쪽 리본에서 **SSL**  >  **서버**  >  **만들기** 를 선택 합니다.

11. **새 서버 Ssl 프로필** 페이지에서 새 서버 ssl 프로필에 대 한 고유 이름을 제공 하 고 부모 프로필이 **serverssl** 로 설정 되었는지 확인 합니다.

12. 인증서 및 키 행의 오른쪽 위 확인란을 선택 하 고 드롭다운 목록에서 가져온 인증서를 선택한 다음 **완료** 를 선택 합니다.

![이 이미지는 업데이트 큰 ip 서버 모든 프로필을 보여 줍니다.](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>실망 하지 마세요. SSL 인증서를 사용할 수 없는 경우 자체 서명 된 통합 된 IP 서버 및 클라이언트 SSL 인증서를 사용할 수 있습니다. 브라우저에 인증서 오류가 표시 됩니다.

SHA 용 빅 IP를 준비 하는 마지막 단계는 게시 된 리소스와 SSO에 사용 되는 디렉터리 서비스를 찾을 수 있도록 하는 것입니다. 큰 IP에는 두 가지 이름 확인 원본이 있습니다. 로컬/.../hosts 파일에서 시작 하거나, 레코드를 찾을 수 없는 경우 큰 IP 시스템은 구성 된 모든 DNS 서비스를 사용 합니다. Hosts 파일 메서드는 FQDN을 사용 하는 APM 노드 및 풀에는 적용 되지 않습니다.

1. 웹 구성에서 **시스템**  >  **구성**  >  **장치**  >  **DNS** 로 이동 합니다.

2. **Dns 조회 서버 목록** 에서 환경 DNS 서버의 IP 주소를 입력 합니다.

3. 업데이트 **추가**  >   선택

별도의 선택적인 단계로, 로컬의 큰 IP 계정을 관리 하는 대신 AD에 대 한 큰 IP sysadmins를 인증 하는 [LDAP 구성을](https://somoit.net/f5-big-ip/authentication-using-active-directory) 고려할 수 있습니다.

## <a name="update-big-ip"></a>업데이트 빅 IP

[시나리오 기반 지침](f5-aad-integration.md)에서 설명 하는 모든 새 기능의 잠금을 해제 하려면 큰 IP VM을 업데이트 해야 합니다. 주 페이지의 왼쪽 위에 있는 BIG-IPs 호스트 이름 위에 마우스 포인터를 올려 TMOS 버전의 시스템을 확인할 수 있습니다. [F5 다운로드](https://downloads.f5.com/esd/productlines.jsp)를 통해 v15 이상을 실행 하는 것이 좋습니다. [지침](https://support.f5.com/csp/article/K34745165) 을 사용 하 여 주 시스템 OS (TMOS)를 업데이트 합니다.

주 TMOS 업데이트를 수행할 수 없는 경우 다음 단계를 사용 하 여 최소한의 안내 구성만 업그레이드 하는 것이 좋습니다.

1. 큰 IP 웹 구성의 주 탭에서 **액세스**  >  **안내 구성** 으로 이동 합니다.

2. **단계별 구성** 페이지에서 **업그레이드 기반 구성** 을 선택 합니다.

![이미지는 큰 ip를 업데이트 하는 방법을 보여 줍니다.](./media/f5ve-deployment-plan/update-big-ip.png)

3. **업그레이드 단계별 구성** 대화 상자에서 **파일을 선택** 하 여 다운로드 한 사용 사례 팩을 업로드 하 고 **업로드 및 설치** 를 선택 합니다.

4. 업그레이드가 완료 되 면 **계속** 을 선택 합니다.

## <a name="backup-big-ip"></a>백업 빅 IP

이제 큰 IP 시스템이 완전히 프로 비전 되 면 해당 구성의 전체 백업을 수행 하는 것이 좋습니다.

1. **시스템**  >  **보관 파일**  >  **만들기** 로 이동

2. 고유한 **파일 이름을** 제공 하 고 암호를 사용 하 여 **암호화** 를 사용 합니다.

3. 장치 및 SSL 인증서도 백업 하도록 **개인 키** **옵션을 설정** 합니다.

4. **마침** 을 선택 하 고 프로세스가 완료 될 때까지 기다립니다.

5. 백업 결과의 상태를 제공 하는 작업 상태가 표시 됩니다. **확인** 을 선택합니다.

6. 백업 링크를 선택 하 여 사용자 구성 집합 (UCS) 보관 파일을 로컬로 저장 하 고 **다운로드** 를 선택 합니다.

선택적 단계로, [Azure 스냅숏을](../../virtual-machines/windows/snapshot-copy-managed-disk.md)사용 하 여 전체 시스템 디스크의 백업을 수행할 수도 있습니다. 웹 구성 백업과 달리 TMOS 버전 간의 테스트에 대 한 일부 대체 기능을 제공 하거나 새로운 시스템으로 롤백합니다.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>큰 IP 복원

큰 IP를 복원 하는 것은 백업과 유사한 절차를 따르고, 큰 IP Vm 간에 configs를 마이그레이션하는 데에도 사용할 수 있습니다. 백업을 가져오기 전에 지원 되는 업그레이드 경로에 대 한 세부 정보를 확인 해야 합니다.

1. **시스템**  >  **보관** 으로 이동

2. 복원 하려는 백업의 링크를 선택 하거나, **업로드** 단추를 선택 하 여 목록에 없는 이전에 저장 된 UCS 보관 파일을 찾습니다.

3. 백업에 대 한 암호를 입력 하 고 **복원** 을 선택 합니다.

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>작성 시 AzVmSnapshot cmdlet은 날짜를 기준으로 가장 최근의 스냅숏을 복원 하도록 제한 됩니다. 스냅숏은 VM 리소스 그룹의 루트에 저장 됩니다. 스냅숏을 복원 하는 것은 Azure VM을 다시 시작 하기 때문에 주의 해야 합니다.

## <a name="additional-resources"></a>추가 리소스

-   [Azure에서 큰 IP VE 암호 다시 설정](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [포털을 사용 하지 않고 암호 다시 설정](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [대형 IP VE 관리에 사용 되는 NIC 변경](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [단일 NIC 구성의 경로 정보](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>다음 단계

[배포 시나리오](f5-aad-integration.md) 를 선택 하 고 구현을 시작 합니다.