---
title: "Azure Virtual Machine 보안 모범 사례 | Microsoft Docs"
description: "이 문서는 Azure의 가상 컴퓨터에 사용할 보안 모범 사례 모음을 제공합니다."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: d6c5ea3e44b6121377d7d51f2bb9c878f9f933c5
ms.lasthandoff: 03/03/2017


---
# <a name="azure-virtual-machine-security-best-practices"></a>Azure Virtual Machine 보안 모범 사례

대부분 IaaS(Infrastructure as a Service) 시나리오에서 [Virtual Machines](https://docs.microsoft.com/en-us/azure/virtual-machines/)는 클라우드 컴퓨팅을 사용하는 조직에 주요 워크로드입니다. 조직이 워크로드를 클라우드에 천천히 마이그레이션하고 싶은 [하이브리드 시나리오](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx)에서 지배적입니다. [IaaS에 대한 일반적인 보안 고려 사항](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx) 시나리오를 따르고 Azure에 있는 모든 VM에 보안 모범 사례를 적용했는지 확인해야 합니다.

이 문서에서는 Azure VM 보안 모범 사례 컬렉션에 대해 설명합니다. 이러한 모범 사례는 Azure VM에 대한 Microsoft의 경험 그리고 여러분 같은 고객의 경험에서 얻은 것입니다. 

각 모범 사례에 대해 다음 사항을 설명하겠습니다.

- 각 모범 사례
- 해당 모범 사례를 사용해야 하는 이유
- 해당 모범 사례를 사용하지 않을 경우에 발생할 수 있는 결과
- 해당 모범 사례를 대체할 수 있는 대안
- 해당 모범 사례를 사용하는 방법을 알아보는 방법

이 Azure Virtual Machine 보안 모범 사례 문서는 이 문서가 작성될 당시의 합의된 의견과 Azure 플랫폼 기능 및 특징 집합을 기반으로 합니다. 이 문서는 시간이 지남에 따라 변화하는 의견 및 기술을 반영하도록 주기적으로 업데이트 됩니다.

이 문서에서 다루는 Azure VM 보안 모범 사례에는 다음이 포함됩니다.

- 가상 컴퓨터 인증 및 액세스 제어
- 가상 컴퓨터 가용성 및 네트워크 액세스
- 암호화를 적용하여 Azure VM에서 미사용 데이터 보호
- 가상 컴퓨터 업데이트 관리
- 가상 컴퓨터의 보안 태세 관리
- 가상 컴퓨터 성능 모니터링

## <a name="virtual-machine-authentication-and-access-control"></a>가상 컴퓨터 인증 및 액세스 제어

가상 컴퓨터를 보호하는 첫 번째 단계는 승인된 사용자만 새 VM을 프로비저닝할 수 있게 하는 것입니다. [Resource Manager 정책](../azure-resource-manager/resource-manager-policy.md)을 사용하여 조직의 리소스 표기 규칙을 정하고 사용자 지정된 정책을 생성하며 이러한 규칙을 [리소스 그룹](../azure-resource-manager/resource-group-overview.md) 등의 리소스에 적용할 수 있습니다. 해당 리소스 그룹에 속한 VM은 이러한 정책을 상속합니다. 요구 사항이 다르고, 다른 리소스 그룹에 있는 리소스(VM 포함)를 관리하기에 좋은 접근 방식이며 [역할 기반 액세스 제어(RBAC)](../active-directory/role-based-access-control-configure.md)를 사용하여 VM에 대한 개별 액세스를 제어할 수도 있습니다.

Azure Resource Manager 정책 및 RBAC가 VM 액세스를 제어하도록 사용 설정하면 VM의 전반적인 보안을 개선할 수 있습니다. 동일한 수명 주기를 공유하는 VM을 동일한 리소스 그룹에 긴밀하게 결합하는 것이 좋습니다. 리소스 그룹을 사용하여 리소스를 그룹 단위로 배포 및 모니터링하고, 리소스 그룹별로 비용을 청구할 수 있습니다. [최소 권한](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) 접근 방식을 사용하여 VM을 프로비전하도록 사용자에게 액세스를 활성화하고 사용자에게 권한 할당 시 Azure에서 다음 기본 제공 역할을 사용하도록 계획합니다.

- [가상 컴퓨터 참여자](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor): 가상 컴퓨터를 관리할 수 있으나 해당 컴퓨터가 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없습니다.
- [클래식 가상 컴퓨터 참여자](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor): 가상 컴퓨터를 관리할 수 있으나 해당 컴퓨터가 연결된 가상 네트워크 또는 저장소 계정은 관리할 수 없습니다.
- [보안 관리자](../active-directory/role-based-access-built-in-roles.md#security-manager): 보안 구성 요소, 보안 정책 및 가상 컴퓨터를 관리할 수 있습니다.
- [DevTest 실습 사용자](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user): 모든 항목을 볼 수 있으며 가상 컴퓨터를 연결, 시작, 다시 시작 및 종료할 수 있습니다.

관리자 간 계정 및 암호를 공유하거나, 여러 사용자 계정 또는 서비스, 특히 소셜 미디어 또는 기타 비 관리 작업에 암호를 다시 사용하지 마세요. 이상적으로는, VM 프로비전을 확보하기 위해 [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) 템플릿을 사용해야 합니다. 이 접근 방식을 사용하면 배포 선택을 확정하고 배포 전체에서 보안 설정을 적용할 수 있습니다.

RBAC 같은 기능을 활용하여 데이터 액세스 제어를 적용하지 않는 조직은 사용자에게 필요 이상으로 많은 권한을 부여하게 될 수 있습니다. 그로 인해 원래는 액세스할 수 없어야 하는 데이터에 액세스할 수 있게 되어 데이터 손상이 발생할 수 있습니다.
 

## <a name="virtual-machine-availability-and-network-access"></a>가상 컴퓨터 가용성 및 네트워크 액세스

VM에서 고가용성의 중요한 응용 프로그램을 실행할 경우에는 여러 VM을 사용하는 것이 좋습니다.  가용성 향상을 위해 [가용성 집합](../virtual-machines/virtual-machines-windows-infrastructure-availability-sets-guidelines.md)에서 최소&2;개 이상의 VM을 생성하세요. 또한 Azure [Load Balancer](../load-balancer/load-balancer-overview.md)에서는 부하 분산된 VM이 동일한 가용성 집합에 속해야 합니다. 이러한 VM을 인터넷에서 액세스해야 할 경우 [인터넷 페이싱 부하 분산 장치](../load-balancer/load-balancer-internet-overview.md)를 구성해야 합니다.

VM이 인터넷에 노출되어 있으면 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 흐름을 제어](../virtual-network/virtual-networks-nsg.md)하는 것이 중요합니다.  NSG가 서브넷에 적용될 수 있기 때문에, 서브넷에 따라서 리소스를 그룹화고 서브넷에 NSG를 적용하여 NSG의 수를 최소화할 수 있습니다. Azure에서 [네트워크 보안](../best-practices-network-security.md) 기능을 적절하게 구성하여 달성할 수 있는 네트워크 격리 계층을 생성하기 위한 것입니다.  

특정 VM에 원격 액세스할 수 있는 대상 및 시간을 제어하기 위해 Azure Security Center에서 VM 액세스 기능을 시기적절하게 사용할 수도 있습니다. 이 기능을 사용하는 방법에 대한 자세한 내용은 아래 동영상을 보세요.


<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-Just-in-Time-VM-Access/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

인터넷 페이싱 VM에 대한 네트워크 액세스 제한을 적용하지 않고 있는 조직은 RDP Brute Force 공격 등의 보안 위험에 노출되어 있습니다. 

## <a name="protect-data-at-rest-in-azure-vms-by-enforcing-encryption"></a>암호화를 적용하여 Azure VM에서 미사용 데이터 보호

요즘 [미사용 데이터 암호화](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)는 데이터 프라이버시, 규정 준수 및 데이터 주권을 위한 필수 단계입니다. [Azure Disk Encryption](../security/azure-security-disk-encryption.md)을 사용하면 IT 관리자가 Windows 및 Linux IaaS 가상 컴퓨터(VM) 디스크를 암호화할 수 있습니다. Azure 디스크 암호화는 업계 표준인 Windows의 BitLocker 기능과 Linux의 DM-Crypt 기능을 활용하여 OS 및 데이터 디스크를 위한 볼륨 암호화를 제공합니다.

Azure 디스크 암호화를 활용하여 데이터를 보호하면 조직의 보안 및 규정 준수 요구 사항을 충족하는 데 도움이 됩니다. 조직에서는 암호화를 사용하여 데이터 무단 액세스와 관련된 위험을 완화하는 방법도 고려해야 합니다. 또한 민감한 데이터를 드라이브에 기록하기 전에 드라이브를 암호화하는 것이 좋습니다. 

Azure Storage 계정의 미사용 데이터를 보호할 수 있도록 VM의 데이터 볼륨을 암호화해야 합니다. [Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/)를 활용하여 암호화 키 및 암호를 보호하세요. 

데이터 암호화를 적용하지 않는 조직은 데이터 무결성 문제에 노출될 가능성이 높습니다. 예를 들어 악의적인 사용자가 데이터 및 손상된 계정을 가로채서 일반 형식 데이터에 무단으로 액세스하게 될 수 있습니다. 이러한 위험 요소 외에도 업계 규정을 준수해야 하는 회사는 데이터 보안에 충실하고 있으며 올바른 보안 컨트롤을 사용하여 데이터 보안을 강화하고 있다는 사실을 증명해야 합니다.

Azure 디스크 암호화에 대한 자세한 내용은 [Windows 및 Linux IaaS VM용 Azure 디스크 암호화](azure-security-disk-encryption.md) 문서를 참조하세요.


## <a name="manage-virtual-machine-updates"></a>가상 컴퓨터 업데이트 관리

사용자에서 관리하므로 Azure는 Windows Azure Virtual Machines로 Windows 업데이트를 푸시하지 않습니다. 온-프레미스 컴퓨터와 마찬가지입니다. 그러나 고객은 자동 Windows 업데이트 설정을 활성화 상태로 유지하는 것이 좋습니다. [Windows Server Update Services(WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) 서버 또는 다른 적합한 업데이트 관리 제품을 다른 Azure Virtual Machine 또는 온-프레미스에 배포하는 옵션도 있습니다. WSUS와 Windows 업데이트 모두 VM을 최신 상태로 유지합니다. 스캔 제품을 활용하여 모든 IaaS 가상 컴퓨터가 최신 상태를 유지하는지 확인하는 것도 좋습니다.

Azure에서 제공하는 스톡 이미지가 정기적으로 업데이트되어 최신 회의 Windows 업데이트를 포함합니다. 그러나 이미지가 배포 당시에 최신 상태라는 보장되지 않습니다. 공용 버전에서 약간의 지연(몇 주 이내)이 있을 수 있습니다. 모든 Windows 업데이트를 확인하고 설치하는 것은 모든 배포의 첫 번째 단계입니다. 자체 라이브러리에서 제공하는 이미지 배포 시 특히 기억해야 합니다. Windows Azure 갤러리의 일부로 제공된 이미지는 자동 Windows 업데이트가 기본적으로 활성화되어 있습니다.

소프트웨어 업데이트 정책을 적용하고 있지 않은 조직은 이미 해결된 알려져 있는 취약점을 악용하는 위협에 더 노출되어 있습니다. 이러한 위험 요소 외에도 업계 규정을 준수해야 하는 회사는 보안에 충실하고 있으며 올바른 보안 컨트롤을 사용하여클라우드에 있는 워크로드의 보안을 강화하고 있다는 사실을 증명해야 합니다.
기존 데이터 센터 및 Azure IaaS에서 소프트웨어 업데이트 모범 사례 간의 많은 공통점이 있다는 점을 강조하는 것이 중요합니다. 따라서 현재 소프트웨어 업데이트 정책을 평가하여 Azure VM을 포함하는 것이 좋습니다.

## <a name="manage-virtual-machine-secure-posture"></a>가상 컴퓨터의 보안 태세 관리

사이버 위협은 발전하고 있으며 VM을 보호하는 데 신속하게 위협을 감지하고 경고를 트리거하며 거짓 오류를 줄일 수 있는 더 다양한 모니터링 기능이 필요합니다. 이러한 유형의 워크로드에 대한 보안 태세는 리소스에 무단으로 액세스하려는 위협을 실시간으로 모니터링하면서 업데이트 관리에서 보안 네트워크 액세스까지 VM의 모든 보안 측면에서 구성되어 있습니다.

[Azure Security Center](../security-center/security-center-intro.md)를 사용하여 [Windows](../security-center/security-center-virtual-machine.md) 및 [Linux VM](../security-center/security-center-linux-virtual-machine.md)의 보안 태세를 모니터링할 수 있습니다. Azure Security Center에서 다음 기능을 활용하여 VM을 모니터링할 수 있습니다.

- 권장된 구성 규칙으로 운영 체제(OS) 보안 설정
- 시스템 보안 및 누락된 중요 업데이트
- 끝점 보호 권장 사항(맬웨어 방지)
- 디스크 암호화 유효성 검사
- 취약점 평가 및 재구성
- 위협 감지

Security Center는 위협을 적극적으로 모니터링할 수 있으며 이러한 위협은 보안 경고에서 공개됩니다. 상호 관련이 있는 위협은 *보안 인시던트*라고 하는 단일 보기로 집계됩니다. 아래의 동영상을 통해 Security Center에서 어떻게 Azure에 있는 사용자의 VM에서 잠재적인 위협을 식별할 수 있게 지원하는지 이해할 수 있습니다.

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

VM에 보안 태세를 적용하지 않고 있는 조직은 준비되어 있는 보안 컨트롤을 우회하기 위한 잠재적인 시도를 인식하지 못합니다.

## <a name="monitoring-virtual-machine-performance"></a>가상 컴퓨터 성능 모니터링

또한 리소스 남용은 VM에 더 많은 리소스를 소비하는 프로세스가 있을 경우에도 문제가 될 수 있습니다. 성능 문제가 있는 가상 컴퓨터는 서비스 중단을 유발할 수 있으며, 이는 보안 원칙 중 하나인 가용성을 저해합니다. 이러한 VM 액세스를 적극적으로(문제가 발생하는 동안) 모니터링할 뿐만 아니라 정상적인 작업 시간 중 기준을 완료해야 합니다.

[Azure Diagnostic Logs](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/)는 사용자의 가상 컴퓨터의 리소스를 모니터링하고 성능 및 가용성을 훼손하는 잠재적인 문제를 식별하는 데 도움이 됩니다. Azure Diagnostics Extension은 Windows 기반 Azure 가상 컴퓨터에 모니터링 및 진단 기능을 제공합니다. 확장을 Azure Resource Manager [템플릿](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)에 속하도록 포함시켜서 가상 컴퓨터에서 이러한 기능을 사용하도록 설정할 수 있습니다. 또한 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 사용하여 리소스 상태에 대한 가시성도 얻을 수 있습니다.

가상 컴퓨터의 성능을 모니터링하지 않는 고직은 성능 패턴의 일부 변경 사항이 정상 활용의 일부인지 또는 정상보다 더 많은 리소스를 소비하는 비정상적인 작업이 발생하고 있는지 판단할 수 없게 됩니다. 이러한 비정상은 외부 리소스에서 잠재적인 공격이 유입됙 있거나 가상 컴퓨터에서 손상된 프로세스가 실행 중임을 나타낼 수 있습니다. 
