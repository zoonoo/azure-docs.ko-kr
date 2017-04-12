---
title: "Azure Government 규정 준수 | Microsoft 문서"
description: "Azure Government에 제공되는 규정 준수 서비스의 개요 설명"
services: azure-government
cloud: gov
documentationcenter: 
author: jomolesk
manager: zakramer
ms.assetid: 1d2e0938-482f-4f43-bdf6-0a5da2e9a185
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/07/2016
ms.author: jomolesk
translationtype: Human Translation
ms.sourcegitcommit: 474ee83dfc8532f8410b79c6426af3cad6f3d320
ms.openlocfilehash: cd54456c8f9e0f37f53973fca80b8bd3514b62d1


---
# <a name="azure-government-compliance"></a>Azure Government 규정 준수

## <a name="azure-blueprint"></a>Azure Blueprint

Azure Blueprint 프로그램은 정부 기관 및 정부를 대신하여 구축하는 타사 공급자를 위해 Azure의 보안을 강화하고 규정에 따른 사용을 촉진하기 위해 설계되었습니다. Azure Government 고객은 Azure Government의 P-ATO(FedRAMP JAB Provisional Authority to Operate) 또는 DoD PA(Provisional Authorization)를 활용하여 Azure 기반 시스템에서 고객 책임 보안 제어의 범위를 줄일 수 있습니다. Azure Government에서 보안 제어 구현이 상속되기 때문에 고객은 Azure에서 구축된 IaaS, PaaS 또는 SaaS 환경에 대한 제어 구현에 집중할 수 있습니다.

> [!NOTE]
> Azure Blueprint에서 "고객"이란 Azure 내에서 직접 구축하는 조직을 말합니다. Azure 고객으로는 정부 또는 정부 기관을 대신하여 Azure에 직접 구축하는 타사 ISV가 포함될 수 있습니다.

## <a name="blueprint-customer-responsibilities-matrix"></a>Blueprint 고객 책임 매트릭스

Azure Blueprint CRM(고객 책임 매트릭스)은 Azure Government 고객이 Azure 내에 구현된 시스템별 보안 제어를 구현하고 문서화하는 것을 지원하기 위해 설계되었습니다. CRM에는 고객 구현 요구 사항을 포함하여 FedRAMP 및 DISA 기준에 대한 모든 [NIST SP 800-53](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r4.pdf) 보안 제어 요구 사항이 명시적으로 나열됩니다. 여기에는 Azure와 Azure 고객이 책임을 공유하는 제어와 전적으로 Azure 고객이 구현해야 하는 제어가 포함됩니다. 해당하는 경우 보다 구체적인 지침을 제공하기 위해 제어 하위 요구 사항 수준에서 제어에 대해 구체적으로 기술됩니다.

CRM 형식은 유용성을 위해 설계되었으며 구현된 보안 제어에서 고객 부분만 집중적으로 문서화하는 데 도움이 됩니다.

예를 들어 제어 AC-1은 ATO를 검색하는 시스템에 대한 문서화된 액세스 제어 정책 및 절차가 필요합니다. 이 제어의 경우 Microsoft는 Azure 인프라 및 플랫폼을 관리하는 데 사용되는 액세스 제어 메커니즘과 관련된 내부 Azure 정책 및 절차를 갖고 있습니다. 또한 고객은 Azure에 구축한 고객 시스템 내부에서 사용할 고유의 액세스 제어 정책과 절차를 만들어야 합니다. CRM 문서는 특정 콘텐츠를 포함하는 정책과 절차가 필요한 AC-1a 파트뿐 아니라 고객이 연&1;회 이러한 문서를 검토하고 업데이트해야 하는 AC-1b까지 제어합니다.

Blueprint CRM은 FedRAMP Moderate 및 High 기준을 위한 Microsoft Excel 통합 문서로, 그리고 DISA 클라우드 컴퓨팅 SRG L4 및 L5 기준으로 제공됩니다.

Azure Blueprint CRM의 복사본을 요청하거나 사용자 의견을 보내려면 [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com)으로 전자 메일을 보내주세요.

## <a name="blueprint-system-security-plan"></a>Blueprint 시스템 보안 계획

Azure Blueprint SSP(시스템 보안 계획) 템플릿은 고객 보안 제어 구현과 Azure에서 상속되는 컨트롤을 설명하는 SSP를 개발하는 데 사용하도록 설계된 고객 중심 템플릿입니다. 고객 책임을 포함하는 컨트롤에는 철저하고 규정에 따라 응답하는 제어 구현을 문서화하는 방법에 대한 지침이 포함됩니다. Azure 상속 섹션에서는 Azure가 고객을 대신하여 보안 컨트롤을 구현하는 방식을 설명합니다.

Azure Blueprint SSP는 FedRAMP Moderate 및 High 기준과 DISA 클라우드 컴퓨팅 SRG L4 및 L5 기준에 제공됩니다. 

Azure Blueprint SSP의 복사본을 요청하거나 사용자 의견을 보내려면 [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com)으로 전자 메일을 보내주세요.

## <a name="azure-blueprint-implementation-guidance"></a>Azure Blueprint 구현 지침

Azure Blueprint 구현 지침은 클라우드 솔루션 설계자 및 보안 담당자가 Azure Government 서비스 및 기능을 배포하여 고객 책임 FedRAMP 및 DoD 보안 컨트롤의 하위 집합을 구현하는 방법을 이해하도록 도와주기 위해 설계되었습니다. Azure 서비스 및 기능을 안전하게 배포하는 방법을 안내하는 다양한 설명서, 도구, 템플릿 및 기타 리소스가 제공됩니다. Azure 리소스는 Azure Resource Manager 템플릿 [빌딩 블록](https://github.com/mspnp/template-building-blocks), 커뮤니티에서 제공하는 Azure [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/) 또는 [고객이 작성한](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) JSON 기반 Resource Manager 템플릿을 통해 배포할 수 있습니다. Azure에서 기본적인 배포의 아키텍처에는 계산, 네트워킹 및 저장소 리소스가 포함됩니다. 이 구현 지침은 보안 제어 구현 요구 사항을 충족하는 데 도움이 되는 방법으로 이러한 리소스를 배포하도록 도와줍니다.

### <a name="virtual-machines"></a>가상 컴퓨터

Azure Resource Manager 템플릿을 사용하여 미국 정부 및 산업 STIG(보안 기술 구현 지침)와 보안 벤치마크를 충족하는 미리 구성된 가상 컴퓨터를 배포할 수 있습니다. 미리 구성된 기존 컴퓨터를 사용하여 사용자 지정 Azure 가상 컴퓨터를 만들 수 있습니다. 또는 도메인에 가입된 컴퓨터의 경우 Active Directory를 사용하여, 독립 실행형 컴퓨터의 경우 로컬 그룹 정책 개체 유틸리티를 사용하여 새 가상 컴퓨터를 만들고 보안 정책을 적용할 수 있습니다. Azure 가상 컴퓨터 사용자 지정 스크립트 확장을 사용하여 이러한 배포 후 구성 작업을 관리할 수 있습니다.

Windows 가상 컴퓨터 구성에 다음에 대한 보안 제어 구현을 포함할 수 있습니다.

- 시스템 사용 알림 및 승인 [NIST SP 800-53 제어 AC-8]

  > AC-8 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)가 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 Windows Server 2012 R2에 대한 CIS 벤치마크에 따라 구성되고 사용자가 로그온하기 전에 승인해야 하는 적절한 시스템 사용 알림을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다( CIS 벤치마크 섹션 2.3.7.4, 2.3.7.5 참조).*

- 계정 잠금 [NIST SP 800-53 제어 AC-7] 동시 세션 제어 [NIST SP 800-53 제어 AC 10], 세션 잠금 [NIST SP-800-53 제어 AC-11], 인증자 관리 [NIST SP 800-53 제어 IA-5] 및 기타를 포함한 로컬 컴퓨터 계정 제한 사항

  > AC-7 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 Windows Server 2012 R2에 대한 CIS 벤치마크에 따라 구성되고 연속으로 로그인이 실패하면 계정 잠금을 시작하는 것을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다( CIS 벤치마크 섹션 1.2.1, 1.2.2, 1.2.3 참조. 참고: FedRAMP 및 DoD 매개 변수 요구 사항을 충족하도록 CIS 벤치마크 기본값을 변경해야 함).*

  > AC-10 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 Windows Server 2012 R2에 대한 CIS 벤치마크에 따라 구성되고 FedRAMP 및 DoD 요구 사항을 충족하도록 사용자를 단일 원격 데스크톱 서비스 세션으로 제한하는 것을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다( CIS 벤치마크 섹션 18.9.48.3.2 참조).*

  > AC-11 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 Windows Server 2012 R2에 대한 CIS 벤치마크에 따라 구성되고 사용자가 다시 인증될 때까지 900초(15분) 동안 활동이 없으면 세션 잠금을 시작하는 것을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다( CIS 벤치마크 섹션 2.3.7.3, 19.1.3.1, 19.1.3.3 참조).*

  > IA-5 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 Windows Server 2012 R2에 대한 CIS 벤치마크에 따라 구성되고 FedRAMP 요구 사항을 충족하도록 최소 1일부터 최대 60일의 수명 제한이 적용되는 로컬 계정 암호, 재사용 조건(24개 암호), 길이(14자), 복잡성 요구 사항을 제한하는 것을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다( CIS 벤치마크 섹션 1.1.1, 1.1.2., 1.1.3., 1.1.4, 1.1.5 참조. 참고: DoD 매개 변수 요구 사항을 충족하도록 CIS 벤치마크 기본값을 변경해야 함).*

- 최소 기능을 포함한 구성 설정 [NIST SP 800-53 제어 CM-6, CM-7]

  > CM-6 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 대해 유지됩니다. 그룹 정책 개체는 Windows Server 2012 R2에 대한 CIS 벤치마크에 따라 구성되고 시스템 운영 요구에 대한 조직의 요구 사항을 충족하도록 사용자 지정됩니다. 다른 구성 리소스와 함께 이 그룹 정책 개체를 설정하여 일관적인 구성 설정 구현을 문서화하고 보장합니다.*

  > CM-7 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 대해 유지됩니다. 그룹 정책 개체는 Windows Server 2012 R2에 대한 CIS 벤치마크에 따라 구성되고 시스템 운영 요구에 대한 조직의 요구 사항을 충족하도록 사용자 지정됩니다. Azure 내의 모든 Windows 컴퓨터에 대해 유지되며 Windows 컴퓨터의 구성을 반영하여 시스템 운영에 필수적인 기능만 제공하는 그룹 정책 개체는 기준 가상 컴퓨터 이미지를 만드는 데 사용됩니다.*

  Windows 컴퓨터의 경우 미리 구성된 GPO(그룹 정책 개체), Linux 컴퓨터의 경우 셸 스크립트를 포함하는 보안 기준 업데이트 관리 도구는 [Microsoft](https://technet.microsoft.com/itpro/windows/keep-secure/windows-security-baselines)(Windows 컴퓨터의 경우)와 [인터넷 보안 센터](https://benchmarks.cisecurity.org/) 및 [DISA(Defense Information Systems Agency)](http://iase.disa.mil/stigs)(Windows 및 Linux 컴퓨터 모두)를 통해 제공됩니다.  

보안 제어 요구 사항을 충족하도록 Azure 가상 컴퓨터를 암호화하여 휴지 정보를 보호할 수 있습니다[NIST SP 800-53 제어 SC-28]. Azure 빠른 시작 템플릿을 사용하여 [신규](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image) 및 기존 [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)와 [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm) 가상 컴퓨터 모두에 암호화를 배포할 수 있습니다. 

> SC-28 제어 구현 문 예제: *Azure 내의 고객 제어 가상 컴퓨터는 디스크 암호화를 구현하여 휴지 상태인 정보의 기밀성과 무결성을 보호합니다. Windows용 Azure Disk Encryption은 Windows의 BitLocker 기능을 사용하여 구현되고 Linux 가상 컴퓨터의 디스크 암호화는 Linux의 DM 암호화 기능을 사용하여 구현됩니다.

보안 제어 요구 사항을 충족하도록 여러 Azure 가상 컴퓨터 확장을 배포할 수 있습니다. 신규 및 기존 가상 컴퓨터에 [Microsoft 맬웨어 방지 가상 컴퓨터 확장](https://docs.microsoft.com/azure/security/azure-security-antimalware)을 배포할 수 있습니다. 맬웨어 방지 확장은 주기적인 예약 검사 [NIST SP 800-53 제어 SI-3]뿐 아니라 실시간 보호를 사용하도록 설정할 수 있습니다.

> SI-3 제어 구현 문 예제: *Azure의 고객 제어 Windows 가상 컴퓨터에 대한 호스트 기반 맬웨어 방지 보호는 Microsoft 맬웨어 방지 가상 컴퓨터 확장을 사용하여 구현됩니다. 이 확장은 실시간 검사 및 주기적 검사(매주)를 수행하고, 맬웨어 방지 엔진 및 보호 서명을 자동으로 업데이트하고, 자동 문제 해결 작업을 수행하고, Azure 진단을 통해 알림을 제공하도록 구성됩니다.*

*추가 도구 및 리소스*

사용자 지정이 가능하며 [가상 컴퓨터](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/multi-vm-n-nic-m-storage) 및 [가상 컴퓨터 확장](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/virtualMachine-extensions) 배포를 포함하여 Azure 리소스 배포에 사용할 수 있는 Azure Resource Manager 템플릿 [빌딩 블록](https://github.com/mspnp/template-building-blocks)입니다. 

### <a name="virtual-network"></a>가상 네트워크

Azure VNet(Virtual Network)를 사용하면 서브넷, 네트워크 보안 그룹, 사용자 정의 경로를 배포하고 구성하여 가상 네트워크 아키텍처 내에서 보안 정책 및 라우팅을 철저하게 제어할 수 있습니다. 네트워크 보안 그룹을 서브넷 또는 개별 컴퓨터에 적용하고, 다른 용도를 위해 리소스를 다중 계층 아키텍처에 따라 워크로드별로 논리적으로 분리할 수 있습니다. 아래의 참조 아키텍처에서는 리소스가 웹, 비즈니스 및 데이터 계층에 대한 별도의 서브넷과 Active Directory 리소스 및 관리용 서브넷에 그룹화됩니다. 가상 네트워크 내의 네트워크 트래픽을 제한하기 위해 각 서브넷에 네트워크 보안 그룹이 적용됩니다. 
 
![Microsoft Azure를 사용하는 N계층 아키텍처](./media/documentation-government-plan-compliance/compute-n-tier.png)

네트워크 보안 그룹은 리소스 간의 통신 경로를 철저하게 제어할 수 있습니다[NIST SP 800-53 제어 AC-4].

> AC-4 제어 구현 문 예제: *웹, 비즈니스 및 데이터 계층 기능을 구현하는 Azure의 고객 제어 가상 컴퓨터는 서브넷을 통해 분리됩니다. 네트워크 계층의 정보 흐름을 정보 시스템 작동에 필요한 최소한으로 제한하는 네트워크 보안 그룹이 정의되어 각 서브넷에 적용됩니다.*

서브넷 및 가상 컴퓨터에서 나가는 통신에 네트워크 보안 그룹을 적용할 수 있습니다. 따라서 Azure의 정보 시스템 구성 요소와 외부 정보 시스템 간의 통신을 철저하게 제어할 수 있습니다[NIST SP 800-53 제어 CA-3 (5)]. 네트워크 보안 그룹 규칙 처리는 deny-all, permit-by-exception 함수로 구현됩니다. 뿐만 아니라 방화벽이나 IDS/IPS 같은 가상 어플라이언스를 통해 특정 서브넷 및 가상 컴퓨터로 들어오고 나가는 통신을 라우팅하도록 사용자 정의 경로를 구성하여 시스템 통신을 보다 철저하게 관리할 수 있습니다.

> CA-3 (5) 제어 구현 문 예제: *Azure의 고객 제어 리소스에서 발생하는 모든 아웃바운드 통신은 네트워크 보안 그룹 구현을 통해 제한됩니다. 이러한 보안 그룹은 정보 시스템 기능을 지원하기 위해 명시적으로 허용되는 모든 트래픽을 거부하며 FedRAMP 및 DoD L4 요구 사항을 충족하는 아웃바운드 규칙 집합으로 구성됩니다. 또한 사용자 정의 경로는 가상 방화벽 어플라이언스를 통해 모든 아웃바운드 트래픽을 라우팅하도록 구성되며, 가상 방화벽 어플라이언스는 승인된 외부 정보 시스템과만 통신을 허용하는 규칙 집합으로 구성됩니다.*

위의 참조 아키텍처는 보안 함수와 비보안 함수를 격리하기 위해 적용되는 네트워크 보안 그룹 규칙 집합을 통해 Azure 리소스를 별도의 서브넷으로 논리적으로 그룹화하는 방법을 보여줍니다. 이 예에서는 세 개의 웹 응용 프로그램 계층이 Active Directory 서브넷과 관리 서브넷으로부터 격리되며, 정보 시스템과 보안 관리 도구 및 리소스를 호스팅할 수 있습니다[NIST SP 800-53 제어 SC-3].

> SC-3 제어 구현 문 예제: *서브넷 및 이러한 서브넷에 적용되는 네트워크 보안 그룹을 구현하여 고객이 제어하는 Azure 환경 내 비보안 함수로부터 보안 함수를 격리합니다. 웹, 비즈니스 및 웹 응용 프로그램의 데이터 계층에 전용되도록 지정된 정보 시스템 리소스는 관리 서브넷으로부터 논리적으로 격리되고, 정보 시스템 보안 관련 작업이 수행됩니다.*

또한 참조 아키텍처는 정보 시스템에 대한 원격 액세스가 가능하도록 관리되는 액세스 제어 지점을 구현합니다[NIST SP 800-53 제어 AC-17 (3)]. 인터넷 연결 부하 분산 장치는 배포되어 들어오는 인터넷 트래픽을 웹 응용 프로그램에 분산하고, 관리 서브넷은 시스템에 대한 모든 관리 관련 원격 액세스를 제어하는 jumpbox 또는 요새 호스트를 포함합니다. 네트워크 보안 그룹은 가상 네트워크 내부 트래픽을 제한하여 외부 트래픽이 지정된 공용 리소스에만 라우팅되도록 보장합니다.

> AC-17 (3) 제어 구현 문 예제: *Azure 내 정보 시스템의 고객 제어 구성 요소에 대한 원격 액세스는 두 개의 관리되는 네트워크 액세스 제어 지점으로 제한됩니다. 1) 웹 응용 프로그램에 대해 지정된 인터넷 트래픽은 인터넷 연결 부하 분산 장치를 통해 관리되고, 인터넷 연결 부하 분산 장치는 트래픽을 웹 계층 리소스에 분산합니다. 2) 관리 원격 액세스는 환경 내에서 분리된 서브넷의 요새 호스트를 통해 관리됩니다. 관리 서브넷에 적용되며 jumpbox가 상주하는 네트워크 보안 그룹은 허용 목록에 있는 공용 IP 주소의 연결만 허용하며 원격 데스크톱 트래픽 전용으로 제한됩니다.*

네트워크 보안 그룹은 내부 서브넷과 호스트 간의 통신뿐 아니라 Azure 리소스와 외부 호스트 및 시스템 간의 통신에 대한 완전한 제어를 허용하여 공개적으로 액세스할 수 있도록 지정된 정보 시스템 구성 요소와 그렇지 않은 정보 시스템 구성 요소를 분리합니다. 위의 참조 아키텍처에 나오는 솔루션 외에도 Azure는 사용자 정의 경로와 함께 사용하면 Azure 리소스와 외부 네트워크 및 정보 시스템 간의 연결을 더욱 철저하게 보호하고 관리할 수 있는 방화벽 및 IDS/IPS 솔루션 같은 가상 어플라이언스의 배포가 가능합니다[NIST SP 800-53 제어 SC-7].

> SC-7 제어 구현 문 예제: *Azure 내 고객 제어 리소스는 배포된 여러 경계 보호 메커니즘을 통해 보호됩니다. 네트워크 보안 그룹이 정의되고 정보 시스템 경계와 가상 네트워크 내에서 트래픽을 제한하는 네트워크 서브넷에 적용됩니다. 네트워크 보안 그룹은 서브넷 및 가상 컴퓨터와 주고 받는 트래픽을 제어하는 ACL(액세스 제어 목록) 규칙을 포함합니다. 부하 분산 장치가 배포되어 특정 리소스에 들어오는 인터넷 트래픽을 분산합니다. 서브넷은 공개적으로 액세스할 수 있는 정보 시스템 구성 요소를 비공개 리소스로부터 논리적으로 분리합니다.*

네트워크 보안 그룹에 대한 규칙 집합은 특정 네트워크 포트 및 프로토콜에 대한 제한이 가능하며, 필수 기능만 제공하는 방식으로 정보 시스템이 구현되도록 보장하는 핵심 구성 요소입니다[NIST SP 800-53 제어 CM-7].

> CM-7 제어 구현 문 예제: *고객 제어 Azure 환경을 통해 서브넷 및 가상 컴퓨터에 적용되는 네트워크 보안 그룹은 deny-all, permit-by-exception 방식을 사용하여 구현됩니다. 이렇게 하면 명시적으로 승인된 포트 및 프로토콜을 통해서만 네트워크 트래픽이 허용되므로 최소 기능의 개념을 지원합니다.*

*추가 도구 및 리소스*

위의 참조 아키텍처에 대한 설명서는 Azure [설명서 사이트](https://docs.microsoft.com/azure/guidance/guidance-compute-n-tier-vm)에서 제공합니다. 참조 아키텍처를 배포하기 위한 Azure Resource Manager 템플릿이 같은 페이지에 포함되어 있습니다. 또한 Azure 설명서 사이트에는 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 및 [사용자 정의 경로](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)와 관련된 구체적인 정보가 포함되어 있습니다.

### <a name="storage"></a>저장소

Azure 저장소에 저장된 데이터는 고가용성을 보장하기 위해 복제됩니다. 여러 복제 옵션을 사용할 수 있지만 GRS(지역 중복 저장소) 및 RA-GRS(읽기 액세스 지역 중복 저장소)는 데이터가 보조 지역에 복제되도록 보장합니다. 지역 차원의 정전이나 재해 발생 시에도 가용성을 보장기 위해 기본 지역과 보조 지역이 페어링되어 데이터 센터 간에 필요한 거리를 확보합니다[NIST SP 800-53 제어 CP-9]. 지역 중복 고가용성 저장소의 경우 새 저장소 계정을 만들 때 GRS(지역 중복 저장소) 또는 RA-GRS(읽기 액세스 지역 중복 저장소)를 선택합니다. 

> CP-9 제어 구현 문 예제: *Azure 내의 모든 고객 제어 저장소 계정은 지역 중복 저장소를 구현하고, 모든 데이터의 사본&6;개를 두 데이터 센터에 있는 별도의 노드에 유지합니다.*

Azure SSE(저장소 서비스 암호화)는 Azure 저장소 계정 내에 있는 미사용 데이터를 보호합니다[NIST SP 800-53 제어 SC-28, SC-28 (1)]. 사용하도록 설정하면 저장소에 유지하기 전에 Azure가 데이터를 자동으로 암호화합니다. 데이터는 256비트 AES 암호화를 사용하여 암호화됩니다. SSE는 블록 Blob, 추가 Blob 및 페이지 Blob의 암호화를 지원합니다.

> SC-28, SC-28 (1) 제어 구현 문 예제: *고객 제어 Azure 환경 내 모든 저장소 blob의 기밀성과 무결성은 미사용 데이터에 256비트 AES 암호화를 사용하는 Azure SSE를 통해 보호됩니다.*

*추가 도구 및 리소스*

Azure 설명서 사이트에는 [저장소 서비스 암호화](https://docs.microsoft.com/azure/storage/storage-service-encryption) 및 [저장소 복제](https://docs.microsoft.com/azure/storage/storage-redundancy)와 관련된 구체적인 정보가 포함되어 있습니다. 

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory는 Microsoft Azure에서 실행되는 정보 시스템에 대한 ID 및 액세스 기능을 제공합니다. 디렉터리 서비스, 보안 그룹 및 그룹 정책을 통해 고객은 Azure Active Directory를 사용하는 컴퓨터의 액세스 및 보안 정책을 용이하게 제어할 수 있습니다. 계정 및 보안 그룹을 사용하여 정보 시스템에 대한 액세스를 용이하게 관리할 수 있습니다. 그룹 정책을 통해 구성 요구 사항을 충족할 수 있습니다.

Azure Active Directory 보안 그룹 및 디렉터리 서비스를 사용하여 RBAC(역할 기반 액세스 제어) 액세스 제어 체계를 구현하고 정보 시스템에 대한 액세스를 제어할 수 있습니다. 여기에는 다음에 대한 보안 제어 구현이 포함될 수 있습니다.

- 계정 관리 [NIST SP 800-53 제어 AC-2]

  > AC-2.a 제어 구현 문 예제: *Azure Active Directory는 Active Directory 그룹을 사용하여 역할 기반 액세스 제어를 구현함으로써 액세스를 관리하는 데 사용됩니다. 계정 관리 및 AAD에서 지원하는 도메인의 계정 액세스에 대해 설정된 요구 사항이 있습니다. 도메인의 구성원 서버에 대한 액세스는 오직 보안 그룹을 통해서만 지원됩니다. 각 그룹에는 기본 및 보조 소유자가 있습니다. 이러한 소유자는 그룹 구성원 자격, 권한 및 설명의 정확도를 유지할 책임이 있습니다.*

  > AC-2.c 제어 구현 문 예제: *사용자가 보안 그룹에 대한 액세스를 요청하면 해당 그룹 소유자는 구성원 자격에 대해 정의된 기준에 따라 요청을 승인해야 합니다. 계정 조건은 보안 그룹 소유자가 결정합니다.*

  > AC-2.f 제어 구현 문 예제: *Azure Active Directory는 정보 시스템에 대한 액세스 제어를 관리하는 데 사용됩니다. 계정 관리자는 계정 관리 정책에 따라 필요한 대로 정보 시스템 계정을 만들고, 사용하도록 설정하고, 수정하고, 사용하지 않도록 설정하고, 제거할 수 있습니다.*

  > AC-2 (1) 제어 구현 문 예제: *Azure Active Directory는 Active Directory 그룹을 사용하여 역할 기반 액세스 제어를 구현함으로써 액세스를 관리하는 데 사용됩니다. 계정 관리 및 AAD에서 지원하는 도메인의 계정 액세스에 대해 설정된 요구 사항이 있습니다. 도메인의 구성원 서버에 대한 액세스는 오직 보안 그룹을 통해서만 지원됩니다. 각 그룹에는 기본 및 보조 소유자가 있습니다. 이러한 소유자는 그룹 구성원 자격, 권한 및 설명의 정확도를 유지할 책임이 있습니다.*

  > AC-2 (2) 제어 구현 문 예제: *Azure Active Directory는 정보 시스템에 대한 액세스 제어를 관리하는 데 사용됩니다. 계정 관리자는 계정 관리 정책에 따라 임시 계정을 만듭니다. 이러한 임시 계정은 정책 요구 사항에 따라 만료되도록 설정해야 합니다.*

  > AC-2 (3) 제어 구현 문 예제: *Azure Active Directory는 정보 시스템에 대한 액세스 제어를 관리합니다. 계정 관리자는 계정 관리 정책에 따라 임시 계정을 만듭니다. 이러한 임시 계정은 정책 요구 사항에 따라 만료되도록 설정해야 합니다.*

  >AC-2 (7) 제어 구현 문 예제: *Azure Active Directory는 정보 시스템 권한을 보안 그룹에 할당되는 역할로 구성하는 역할 기반 액세스 체계에 따라 정보 시스템에 대한 액세스 제어를 관리합니다. 보안 그룹 관리자는 사용자를 올바른 보안 그룹에 할당할 수 있도록 사용자에게 액세스 권한을 부여하는 일을 담당합니다. 각 보안 그룹에는 작업을 제대로 수행하기 위한 최소한의 적절한 액세스 권한과 함께 사용 권한이 할당됩니다.*

  > AC-2 (11) 제어 구현 문 예제: *Azure Active Directory는 Azure 내 정보 시스템에 대한 액세스 제어를 관리합니다. 보안 정책에 필요한 사용 제한은 계정 관리자에 의해 정의되어 Azure Active Directory 내에 적용됩니다.*

- 액세스 적용 [NIST SP 800-53 제어 AC-3]

  > AC-3 제어 구현 문 예제: *Azure Active Directory는 역할 기반 액세스 제어를 사용하여 고객 환경에 승인된 권한 부여를 적용합니다. Azure Active Directory 보안 그룹에 대한 액세스는 보안 그룹 관리자가 관리합니다. 사용자는 최소 권한의 원칙을 사용하여 역할에 따라 적절한 보안 그룹에 배치됩니다.*

- 최소 권한 [NIST SP 800-53 제어 AC-6]

  > AC-6 (10) 제어 구현 문 예제: *Azure Active Directory는 역할 기반 액세스 제어를 사용하여 고객 환경에 승인된 권한 부여를 적용합니다. Azure Active Directory 보안 그룹에 대한 액세스는 보안 그룹 관리자가 관리합니다. 권한 없는 사용자는 보안 대책을 비활성화하거나 우회하거나 변경할 수 있는 사용 권한을 포함하여 권한 있는 기능에 액세스할 수 있는 보안 그룹 액세스 권한이 부여되지 않습니다.*

- 원격 액세스 [NIST SP 800-53 제어 AC-17]

  > AC-17 (1) 제어 구현 문 예제: *Azure Active Directory는 모든 원격 액세스를 모니터링하고 제어하는 데 사용됩니다. Azure Active Directory에는 디렉터리에 대한 보안, 작업 및 감사 보고서가 포함되어 있습니다.*

- 감사 정보 보호 [NIST SP 800-53 제어 AU-9]

  > AU-9 제어 구현 문 예제: *철저하게 관리되는 액세스 컨트롤은 무단 액세스, 무단 수정, 무단 삭제로부터 감사 정보 및 도구를 보호하는 데 사용됩니다. Azure Active Directory는 Active Directory 정책을 사용하는 계층 방식의 액세스 권한 부여 및 논리 제어와 역할 기반 그룹 구성원 자격을 통해 사용자에게 승인된 논리 액세스를 적용합니다. 감사 정보를 보고 감사 도구를 사용하는 기능은 이러한 권한이 필요한 사용자로 제한됩니다.*

  > AU-9 (4) 제어 구현 문 예제: *Azure Active Directory는 감사 기능 관리를 적절한 보안 그룹의 구성원으로 제한합니다. 관리 감사 기능에 액세스할 필요가 있는 사람에게만 이러한 권한이 부여됩니다.*

- 변경을 위한 액세스 제한 [NIST SP 800-53 제어 CM-5]

  > CM-5 제어 구현 문 예제: *Azure Active Directory에 의해 적용되는 RBAC는 변경과 관련된 논리 액세스 제한을 정의, 문서화, 승인 및 적용하는 데 사용됩니다. 시스템 내에서 생성되는 모든 계정은 역할 기반입니다. 직원이 계정 관리자로부터 액세스를 요청하고 그 요청이 승인되면 역할에 따라 적절한 보안 그룹에 배치됩니다. 프로덕션 환경에 대한 액세스는 승인 후 특정 보안 그룹의 구성원에게만 허용됩니다.*

  > CM-5 (1) 제어 구현 문 예제: *Azure Active Directory는 보안 그룹 구성원 자격을 통해 논리 액세스 제한을 적용합니다. 그러려면 보안 그룹 소유자가 특정 보안 그룹에 대한 액세스를 부여해야 합니다.*

- 사용자 ID 및 인증 [NIST SP 800-53 제어 IA-2]

  > IA-2 제어 구현 문 예제: *정보 시스템 환경에 액세스하는 직원은 사용자 이름을 통해 고유하게 식별되고 Azure Active Directory를 사용하여 인증됩니다. 정보 시스템에 액세스하려면 Azure Active Directory에 인증해야 합니다.*

  > IA-2 (8) 제어 구현 문 예제: *프로덕션 환경에 대한 액세스는 Azure Active Directory에 기본 제공되는 Kerberos 기능을 통해 재생 공격으로부터 보호됩니다. Kerberos 인증에서, 클라이언트가 보내는 인증자에는 암호화된 IP 목록, 클라이언트 타임스탬프, 티켓 수명 등의 추가 데이터가 포함됩니다. 패킷이 재생되면 타임스탬프가 검사됩니다. 타임스탬프가 이전 인증자보다 빠르거나 동일하면 해당 패킷이 거부됩니다.*

  > IA-2 (9) 제어 구현 문 예제: *모든 계정은 Azure Active Directory에 기본 제공되는 Kerberos 기능을 통해 재생 공격으로부터 보호됩니다. Kerberos 인증에서, 클라이언트가 보내는 인증자에는 암호화된 IP 목록, 클라이언트 타임스탬프, 티켓 수명 등의 추가 데이터가 포함됩니다. 패킷이 재생되면 타임스탬프가 검사됩니다. 타임스탬프가 이전 인증자보다 빠르거나 동일하면 해당 패킷이 거부됩니다.*

- 식별자 관리 [NIST SP 800-53 제어 IA-4]

  > IA-4.b 제어 구현 문 예제: *Azure Active Directory 계정 식별자는 사용자를 식별하는 데 사용됩니다. 이러한 고유 식별자는 다시 사용되지 않습니다.*

  > IA-4.c 제어 구현 문 예제: *Azure Active Directory는 서비스 환경에 대한 액세스를 제공하는 데 사용되는 중앙 계정 리포지토리입니다. Azure Active Directory에서 계정이 생성되면 사용자의 고유 식별자가 개인에게 할당됩니다.*

  > IA-4.d 제어 구현 문 예제: *사용자의 고유 식별자는 절대로 다시 사용되지 않습니다. 이는 Azure Active Directory에 의해 적용됩니다.*

  > IA-4.e 제어 구현 문 예제: *Azure Active Directory 내 모든 계정은 35일 동안 활동이 없으면 자동으로 비활성화되도록 구성됩니다.*

  > IA-4 (4) 제어 구현 문 예제: *Azure Active Directory는 고유한 Azure Active Directory 자격 증명에 적용되는 명명 규칙을 사용하여 계약자 및 공급자를 나타냅니다.*

- 인증자 관리 [NIST SP 800-53 제어 IA-5]

  > IA-5.b 제어 구현 문 예제: *Azure Active Directory는 계정 생성 초기에 정책 요구 사항을 충족하는 고유 ID와 임의의 임시 암호를 할당합니다. Azure Active Directory는 계정의 수명이 끝날 때까지 해당 계정과 연결된 고유 ID를 유지합니다. 계정 ID는 Azure Active Directory와 함께 반복되지 않습니다.*

  > IA-5.c 제어 구현 문 예제: *Azure Active Directory는 발급된 암호가 암호 복잡성에 대한 정책 요구 사항을 준수하도록 보장합니다.*

  > IA-5.d 제어 구현 문 예제: *초기 인증자 배포 절차는 계정 관리자가 처리합니다. 사용자 계정에 대한 액세스 수정은 AC-2에 정의된 계정 관리 프로세스를 통해 이루어집니다. 인증자가 손실 또는 손상되면 계정 관리자는 인증자 재설정, 재발급 또는 해지에 대해 정의된 프로세스를 따릅니다.*

  > IA-5 (1).c 제어 구현 문 예제: *Azure Active Directory는 모든 암호가 저장 및 전송되는 동안 암호화를 사용하여 암호를 보호하는 데 사용됩니다. Azure Active Directory에서 저장한 암호는 Azure Active Directory 기능의 일부로 자동 해시됩니다.*

  > IA-5 (1).f 제어 구현 문 예제: *Azure Active Directory는 정보 시스템에 대한 액세스 제어를 관리하는 데 사용됩니다. 계정이 처음으로 생성되거나 임시 암호가 생성되면 Azure Active Directory를 사용하여 사용자가 다음에 로그인할 때 암호를 변경하도록 요청합니다.*

  > IA-5 (4) 제어 구현 문 예제: *Azure Active Directory는 암호 인증자가 IA-5 (1)에 설정된 암호 길이, 복잡성, 회전 및 수명 제한을 만족할 만큼 충분히 강력한지 확인하는 데 사용되는 자동화 도구입니다. Azure Active Directory는 암호 생성 시 이러한 표준을 충족하도록 암호 인증자 강도를 보장합니다.*

Azure Active Directory 그룹 정책 구성을 사용하여 특정 보안 요구 사항을 충족하는 고객 보안 정책을 배포할 수 있습니다. 고객의 Azure 가상 컴퓨터에서 Azure Active Directory를 통해 이러한 정책을 적용할 수 있습니다.
Azure Active Directory 그룹 정책 구성에 다음에 대한 보안 제어 구현을 포함할 수 있습니다.

- 최소 권한 [NIST SP 800-53 제어 AC-6]

  > AC-6 (9) 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 권한 있는 함수의 실행 감사를 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다.*

- 실패한 로그인 시도 [NIST SP 800-53 제어 AC-7]

  > AC-7 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 연속 로그인 실패 후 계정 잠금 시작을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다.*

- 동시 세션 제어 [NIST SP 800-53 제어 AC-10], 세션 잠금 [NIST SP 800-53 제어 AC-11], 세션 종료 [NIST SP 800-53 제어 AC-12]

  > AC-10 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 FedRAMP 및 DoD 요구 사항을 충족하도록 사용자를 단일 원격 데스크톱 서비스 세션으로 제한하는 것을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다.*

  > AC-11.a 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 사용자가 다시 인증될 때까지 900초(15분) 동안 활동이 없으면 세션 잠금을 시작하는 것을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다.*

  > AC-12 제어 구현 문 예제: *Windows는 고객 사용자로부터 로그아웃 요청을 받는 즉시 원격 데스크톱 세션을 자동으로 종료합니다. 또한 구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 900초(15분) 동안 활동이 없으면 원격 데스크톱 세션을 종료하는 것을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다.*

- 인증자 관리 [NIST SP 800-53 제어 IA-5]

  > IA-5.f 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 FedRAMP 요구 사항을 충족하도록 최소&1;일부터 최대&60;일의 수명 제한이 적용되는 로컬 계정 암호, 재사용 조건(24개 암호), 길이(14자), 복잡성 요구 사항을 제한하는 것을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다.*

  > IA-5.g 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 FedRAMP 요구 사항을 충족하도록 최대&60;일의 수명 제한이 적용되는 로컬 계정 암호를 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다.*

  > IA-5 (1).a 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 FedRAMP 요구 사항을 충족하는 복잡성 요구 사항을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다.*

  > IA-5 (1).b 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 FedRAMP 요구 사항을 충족하는 복잡성 요구 사항을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다.*

  > IA-5 (1).d 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 FedRAMP 요구 사항을 충족하도록 최소&1;일부터 최대&60;일의 수명 제한이 적용되는 로컬 계정 암호를 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다."

  > IA-5 (1).e 제어 구현 문 예제: *구성을 통해 제어되는 GPO(그룹 정책 개체)는 Azure 내의 모든 고객 제어 Windows 컴퓨터에 적용됩니다. 그룹 정책 개체는 FedRAMP 요구 사항을 충족하는 재사용 조건(24개 암호)을 포함하여 조직의 요구 사항을 충족하도록 사용자 지정됩니다.*

*추가 도구 및 리소스*

Azure 서비스 및 기능을 안전하게 배포하는 방법을 안내하는 설명서, 도구, 템플릿 및 기타 리소스가 제공됩니다. [Microsoft Azure Docs](https://docs.microsoft.com/azure/active-directory/)를 방문하여 Azure Active Directory를 시작하세요.

### <a name="key-vault"></a>키 자격 증명 모음

Azure Key Vault는 클라우드 응용 프로그램 및 서비스에서 사용되는 암호화 키와 비밀을 보호합니다. 고객은 Azure Key Vault를 사용하여 키와 비밀을 만들고, 관리하고, 보호할 수 있습니다. 보안 컨테이너(자격 증명 모음)를 사용하여 암호화 키 및 비밀을 안전하게 저장하고 관리할 수 있습니다. Azure Key Vault를 사용하여 FIPS 140-2 Level 2 유효성 검사를 통과한 HSM을 사용하는 암호화 키를 생성할 수 있습니다.

Azure Key Vault 컨테이너는 암호화 키를 안전하게 저장하면서도 고가용성을 유지하는 데 도움이 됩니다. 여기에는 다음에 대한 보안 제어 구현이 포함될 수 있습니다.

-   인증자 보호 [NIST SP 800-53 제어 IA-5 (7)]

  > IA-5 (7) 제어 구현 문 예제: *응용 프로그램, 액세스 스크립트 또는 기능 키에 포함된 암호화되지 않은 정적 인증자의 사용은 명시적으로 금지됩니다. 인증자를 사용하는 스크립트 또는 응용 프로그램은 매번 사용하기 전에 Azure Key Vault 컨테이너를 호출합니다. Azure Key Vault 컨테이너에 대한 액세스를 감사하며, 상응하는 Azure Key Vault 컨테이너 호출 없이 서비스 계정을 사용하여 시스템에 액세스하는 경우 이 금지 사항 위반을 발견할 수 있습니다.*

- 암호화 키 설정 및 관리 [NIST SP 800-53 제어 SC-12 (1)]

  > SC-12 (1) 제어 구현 문 예제: *Azure Key Vault는 암호화 키 및 비밀을 저장하는 데 사용됩니다. 이 서비스는 비밀에 대한 액세스를 추적하고 모니터링합니다. 이 서비스는 비밀이 손실되지 않도록 보장하는 데 사용됩니다.*

Azure Key Vault를 사용하여 FIPS 140-2 Level 2 유효성 검사를 충족하는 HSM을 사용하는 암호화 키를 만들 수 있습니다. Azure Active Directory 그룹 정책 구성에 다음에 대한 보안 제어 구현을 포함할 수 있습니다.

- 암호화 키 설정 및 관리 [NIST SP 800-53 제어 SC-12 (2)]

  > SC-12 (2) 제어 구현 문 예제: *Azure Key Vault는 암호화를 만들고, 제어하고, 배포하는 데 사용됩니다. 이러한 키는 FIPS 140-2 Level 2 유효성 검사를 통과한 HSM을 사용하여 생성됩니다. 이러한 키는 Azure Key Vault에 안전하게 저장 및 암호화되는 컨테이너 내에 저장되고 관리됩니다.*

*추가 도구 및 리소스*

Azure 서비스 및 기능을 안전하게 배포하는 방법을 안내하는 설명서, 도구, 템플릿 및 기타 리소스가 제공됩니다. [Microsoft Azure Docs](https://docs.microsoft.com/azure/key-vault/)를 방문하여 Azure Key Vault를 시작하세요.

### <a name="operations-management-suite"></a>Operations Management Suite

OMS(Microsoft Operations Management Suite)는 온-프레미스 및 클라우드 인프라를 관리 및 보호하도록 도와주는 Microsoft의 클라우드 기반 IT 관리 솔루션입니다. 보안 및 규정 준수는 인프라의 보안 위험을 식별, 평가, 완화합니다. 이러한 OMS 기능은 로그 데이터를 분석하고 보안 구성을 모니터링하는 Log Analytics를 비롯한 여러 솔루션을 통해 구현됩니다.

OMS를 지원하는 보안 및 감사 서비스는 계정 모니터링 및 로깅을 구현하는 데 도움이 될 수 있습니다. 여기에는 다음에 대한 보안 제어 구현이 포함될 수 있습니다.

- 계정 관리 [NIST SP 800-53 제어 AC-2]

  > AC-2.g 제어 구현 문 예제: *Microsoft의 OMS(Operation Management)는 시스템 계정 사용을 모니터링하는 데 사용됩니다. OMS는 시스템 계정에 대한 감사 로그를 만든 후 OMS의 분석 기능을 사용하여 로그를 분석하고 정의된 기준에 따라 경고합니다.*

  > AC-2 (4) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 계정을 만들고, 수정하고, 활성화하고, 비활성화하고, 제거하는 작업처럼 계정 관리 기능을 위한 감사 로그를 생성하는 데 사용됩니다. OMS는 위의 조건이 실행되면 시스템 소유자에게 경고하는 데 사용됩니다.*

  > AC-2 (12) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management)는 시스템 계정의 일반적인 사용을 모니터링하는 데 사용됩니다. OMS는 시스템 계정에 대한 감사 로그를 만든 후 OMS의 분석 기능을 사용하여 로그를 분석하고 정의된 기준에 따라 해당 직원에게 경고합니다.*

- 최소 권한 [NIST SP 800-53 제어 AC-6]

  > AC-6 (9) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management)는 권한 있는 기능의 실행을 모니터링하는 데 사용됩니다. OMS는 지정된 기능 목록에 대한 감사 로그를 만든 후 OMS의 분석 기능을 사용하여 로그를 분석하고 정의된 기준에 따라 경고합니다.*

 - 원격 액세스 [NIST SP 800-53 제어 AC-17]

  > AC-17 (1) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management)는 원격 액세스를 모니터링 및 제어하는 데 사용됩니다. OMS에는 디렉터리에 대한 보안, 작업 및 감사 보고서가 포함되어 있습니다.*

- 감사 이벤트 [NIST SP 800-53 제어 AU-2]

  > AU-2 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 성공 및 실패한 계정 로그온 이벤트, 계정 관리 이벤트, 개체 액세스, 정책 변경, 권한 기능, 프로세스 추적 및 시스템 이벤트를 감사하는 데 사용됩니다. 웹 응용 프로그램의 경우 모든 관리자 작업, 인증 검사, 권한 부여 검사, 데이터 삭제, 데이터 액세스, 데이터 변경 및 사용 권한 변경을 감사합니다. 그 밖에 고객이 정의한 이벤트 집합도 감사합니다.*

- 감사 레코드 콘텐츠 [NIST SP 800-53 제어 AU-3]

  > AU-3 (2) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 네트워크, 저장소 및 컴퓨팅 장비에서 생성하는 모든 감사 레코드를 중앙 집중식으로 관리하고 구성하는 데 사용됩니다.*

- 감사 저장소 용량 [NIST SP 800-53 제어 AU-4]

  > AU-4 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 네트워크, 저장소 및 컴퓨팅 장비에서 생성하는 모든 감사 레코드를 중앙 집중식으로 관리하고 구성하는 데 사용됩니다. 중앙 집중식 관리 도구는 감사 레코드 저장소 용량을 구성하는 데 사용됩니다.*

- 감사 처리 실패에 대한 응답 [NIST SP 800-53 제어 AU-5]

  > AU-5 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션과 함께 조직에서 정의한 담당자에게 감사 처리 실패를 알리는 데 사용됩니다.*

  > AU-5 (1) 제어 구현 문: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션과 함께 조직에서 정의한 기간 내에 조직에서 정의한 담당자에게 감사 레코드 저장소가 조직에서 정의한 최대 리포지토리 볼륨 비율에 도달했음을 알리는 데 사용됩니다.*

  > AU-5 (2) 제어 구현 문: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션과 함께 조직 정책에 정의된 감사 가능 이벤트가 실패하면 조직에서 정의한 담당자에게 실시간으로 실패 소식을 알리는 데 사용됩니다.*

- 감사 검토, 분석 및 보고 [NIST SP 800-53 제어 AU-6]

  > AU-6 (3) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션 그리고 Log Analytics 솔루션과 함께 여러 감사 로그 리포지토리를 분석하여 조직 차원의 상황 정보를 제공하는 데 사용됩니다. 분석 도구는 감사 로그에 대해 보고하여 상황 정보를 제공하는 데 사용됩니다.*

  > AU-6 (4) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 모든 감사 레코드를 중앙 집중식으로 관리하고 구성하는 데 사용됩니다. 중앙 집중식 관리 도구를 사용하여 모든 소스의 감사 레코드를 검토 및 분석할 수 있습니다.*

  > AU-6 (5) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션 그리고 Log Analytics 솔루션과 함께 취약점 검사, 정보 시스템 모니터링, 성능 관련 데이터를 통해 생성된 여러 보안 관련 데이터를 분석하는 데 사용됩니다. 이처럼 다양한 리소스를 분석하여 의심스러운 활동을 식별하는 향상된 기능을 제공합니다.*

- 감사 감소 및 보고서 생성 [NIST SP 800-53 제어 AU-7]

  > AU-7 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션 그리고 Log Analytics 솔루션과 함께 보안 사건을 사후 조사할 수 있도록 사람이 읽을 수 있는 주문형 보고서를 생성하는 데 사용됩니다. Microsoft Log Analytics 도구는 원래 감사 레코드의 내용이나 시간 순서를 영구적으로 또는 불가역적으로 변경하지 않습니다.*

  > AU-7 (1) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션과 함께 조직에서 정의한 감사 가능 이벤트를 쿼리하는 데 사용됩니다.*

- 감사 정보 보호 [NIST SP 800-53 제어 AU-9]

  > AU-9 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 그룹과 함께 감사 정보 및 도구에 대한 액세스를 관리하여 감사 레코드에 대한 무단 액세스, 수정 및 삭제를 방지하는 데 사용됩니다. 감사 정보를 보고 감사 도구를 사용하는 기능은 이러한 권한이 필요한 사용자로 제한됩니다.*

  > AU-9 (2) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션 그리고 Azure Backup과 함께 감사 로그를 Azure에 백업한 다음 데이터를 복제하여 데이터의 안정성과 가용성을 높이는 데 사용됩니다. Azure Backup은 감사되는 장소가 아니라 시스템에서 감사 로그를 안전하게 저장할 수 있는 장소를 제공합니다.* 

  > AU-9 (4) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 그룹과 함께 감사 정보 및 도구에 대한 액세스를 관리하는 데 사용됩니다. 관리 감사 기능에 액세스할 필요가 있는 사람에게만 이러한 권한이 부여됩니다.*

- 감사 레코드 보관 [NIST SP 800-53 제어 AU-11]

  > AU-11 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션과 함께 감사 로그 보관을 구성하는 데 사용됩니다. 감사 로그 보관은 감사 데이터를 최소 90일 이상 보관하도록 구성됩니다.*

- 감사 생성 [NIST SP 800-53 제어 AU-12]

  > AU-12 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션과 함께 정보 시스템 구성 요소에서 AU-02에 정의된 감사 가능 이벤트를 수집하는 데 사용됩니다. OMS의 보안 및 감사 솔루션은 조직에서 정의한 담당자가 특정 장치에서 수집할 감사 가능 이벤트를 정의하는 데 사용됩니다.*

  > AU-12 (1) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션 그리고 Log Analytics 솔루션과 함께 시스템 구성 요소의 감사 레코드를 컴파일하는 데 사용됩니다. 그 후 컴파일된 감사 레코드를 타임스탬프를 기준으로 정렬하여 시스템 차원의 감사 내역을 만들 수 있습니다.*

- 변경을 위한 액세스 제한 [NIST SP 800-53 제어 CM-5]

  > CM-5 (1) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션 그리고 Active Directory와 함께 보안 그룹 구성원 자격을 통해 논리 액세스 제한을 적용하는 데 사용됩니다. 이러한 보안 그룹에서 수행한 작업은 OMS를 통해 감사됩니다.*

- 정보 시스템 구성 요소 인벤토리 [NIST SP 800-53 제어 CM-8]

  > CM-8 (3) 제어 구현 문 예제: *Microsoft의 OMS(Operation Management Suite)는 보안 및 감사 솔루션 그리고 맬웨어 방지 솔루션과 함께 승인되지 않은 소프트웨어를 발견하는 데 사용됩니다. 발견 즉시 OMS가 감염된 구성 요소를 비활성화하고 조직에서 정의한 담당자에게 경고를 보냅니다.*

Azure 서비스 및 기능을 안전하게 배포하는 방법을 안내하는 설명서, 도구, 템플릿 및 기타 리소스가 제공됩니다. [Microsoft Azure Docs](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview/)를 방문하여 Operation Management Suite를 시작하세요.

### <a name="additional-implementation-guidance"></a>추가 구현 지침

위에서 설명한 핵심 Azure 서비스 외에도 조직에서 여러 기본 제공 기능을 사용하여 보안 제어 준수 요구 사항을 충족할 수 있습니다. 모든 Azure 리소스는 리소스 그룹으로 분류됩니다. 태그를 적용하여 자산을 더욱 구체적으로 분류할 수 있습니다. 태그는 고객이 범주 또는 다른 속성을 통해 리소스를 식별하기 위해 선택할 수 있는 키-값 쌍입니다. Azure 리소스 그룹을 사용하면 Azure 내에 배포된 모든 고객 리소스를 완벽하게 식별하고 추적할 수 있습니다[NIST SP 800-53 제어 CM-8]. 

> CM-8 제어 구현 문 예제: *Azure 내에 배포된 모든 고객 제어 리소스는 "고객 리소스" 리소스 그룹에 소속됩니다. 배포된 모든 자산은 Azure Portal에서 리소스 그룹 블레이드 내에서 식별되어 정확한 최신 인벤토리를 보장합니다. 필요에 따라 추적 및 보고 요구 사항에 맞게 태그가 적용됩니다.*

고객은 Azure Resource Manager 템플릿을 사용하여 Azure 배포 내의 리소스 구성을 정의할 수 있습니다. Resource Manager 템플릿은 JSON 기반이며 선언적 구문을 사용하여 리소스, 구성 매개 변수 및 Azure 환경의 기타 측면을 기록합니다. Resource Manager 템플릿은 Azure Portal에서 Azure 솔루션이 배포될 때 자동으로 생성되며 고객의 특정 요구 사항을 충족하도록 수동으로 작성하고 편집할 수도 있습니다. 이러한 Resource Manager 템플릿은 정보 시스템에 대한 기본 구성의 표현으로 사용할 수 있습니다[NIST SP 800-53 제어 CM-2]. 위에서 참조한 구성 요소와 같은 기존 Resource Manager 템플릿은 배포 요구 사항을 충족하도록 사용자 지정할 수 있습니다.

> CM-2 제어 구현 문 예제: *정보 시스템 기본 구성의 구성 요소인 Azure Resource Manager 템플릿은 구성 제어에 따라 유지되며 Azure 내에 배포된 고객 제어 리소스 및 정보 시스템 구성 요소의 구성을 나타냅니다. 이러한 템플릿은 가상 컴퓨터, 저장소 및 네트워크 리소스(네트워크 트래픽의 반입 및 반출과 고객 Azure 환경 내의 트래픽을 제어하는 네트워크 리소스의 구성 포함)를 포함하여 배포된 리소스를 캡처합니다.*

## <a name="next-steps"></a>다음 단계

Azure Blueprint, FedRAMP, DoD, Agency ATO 프로세스에 대해 궁금한 점이 있거나 규정 준수와 관련하여 도움이 필요하거나 Blueprint 피드백을 제공하려는 경우 [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com)에 전자 메일을 보내주세요.

[Microsoft 보안 센터](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

[Microsoft Azure Government 블로그](https://blogs.msdn.microsoft.com/azuregov/)



<!--HONumber=Jan17_HO4-->


