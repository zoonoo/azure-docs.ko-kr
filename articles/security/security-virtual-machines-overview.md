---
title: "Azure Virtual Machines에 사용되는 Azure 보안 기능 | Microsoft Docs"
description: " Azure Virtual Machines에서 사용될 수 있는 핵심 Azure 보안 기능에 대한 개요입니다. Azure VM은 VM을 실행하는 실제 하드웨어를 구입 및 유지 관리할 필요가 없는 가상화의 유연성을 제공합니다. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 70d22ae8d7f67f1e12a233e004f1eb749927e7d8
ms.lasthandoff: 03/25/2017


---
# <a name="azure-virtual-machines-security-overview"></a>Azure 가상 컴퓨터 보안 개요
Azure 가상 컴퓨터를 사용하면 다양한 컴퓨팅 솔루션을 민첩하게 배포할 수 있습니다. Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP 및 Azure BizTalk 서비스 지원을 통해 거의 모든 운영 체제에 모든 작업과 언어를 배포할 수 있습니다.

Azure 가상 컴퓨터는 가상 컴퓨터를 실행하는 실제 하드웨어를 구입 및 유지 관리하지 않고도 가상화의 유연성을 제공합니다.  보안이 강화된 데이터 센터에서 데이터가 안전하게 보호된다는 확인을 갖고 응용 프로그램을 빌드 및 배포할 수 있습니다.

Azure를 사용하여 다음과 같은 보안이 강화된 규정 준수 솔루션을 빌드할 수 있습니다.

* 바이러스 및 맬웨어로부터 가상 컴퓨터 보호
* 중요한 데이터 암호화
* 네트워크 트래픽 보안
* 위협 식별 및 감지
* 규정 준수 요구 사항 충족

이 문서의 목표는 가상 컴퓨터와 함께 사용할 수 있는 핵심 Azure 보안 기능의 개요를 제공하는 것입니다. 문서에 각 기능에 대한 세부 정보를 제공하는 링크를 제공하므로 자세히 알아볼 수 있습니다.  

이 문서에서 다룰 핵심 Azure 가상 컴퓨터 보안 기능:

* 맬웨어 방지
* 하드웨어 보안 모듈
* 가상 컴퓨터 디스크 암호화
* 가상 컴퓨터 백업
* Azure Site Recovery
* 가상 네트워킹
* 보안 정책 관리 및 보고
* 규정 준수

## <a name="antimalware"></a>맬웨어 방지
Azure를 통해 가상 컴퓨터를 악성 파일, 애드웨어 및 기타 위협으로부터 보호할 수 있도록 Microsoft, Symantec, Trend Micro, McAfee 및 Kaspersky 등의 보안 공급업체의 맬웨어 방지 소프트웨어를 사용할 수 있습니다. 아래 추가 정보 섹션을 참조하여 파트너 솔루션에 대한 문서를 검색하세요.

Azure 클라우드 서비스 및 가상 컴퓨터를 위한 Microsoft 맬웨어 방지 프로그램은 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별 및 제거하는 데 도움이 되는 실시간 보호 기능입니다.  Microsoft 맬웨어 방지 프로그램은 알려진 악성 또는 원치 않는 소프트웨어가 Azure 시스템에서 스스로의 설치나 실행을 시도할 때 구성 가능한 경고를 제공합니다.

Microsoft 맬웨어 방지 프로그램은 응용 프로그램 및 테넌트 환경에 대한 단일 에이전트 솔루션이며 사용자의 개입 없이 백그라운드에서 실행되도록 설계됩니다. 맬웨어 방지 프로그램 모니터링을 포함하여 기본 보안 또는 고급 사용자 지정 구성을 사용하여 응용 프로그램 워크로드의 필요에 따라 보호를 배포할 수 있습니다.

Microsoft 맬웨어 방지 프로그램을 배포하고 사용할 때 다음과 같은 핵심 기능을 사용할 수 있습니다.

* 실시간 보호 - 클라우드 서비스 및 가상 컴퓨터에서 작업을 모니터링하여 악성 프로그램 실행을 검색 및 차단합니다.
* 예약된 검색 - 주기적으로 대상으로 지정된 검색을 수행하여 적극적으로 실행 중인 프로그램을 포함하여 맬웨어를 검색합니다.
* 맬웨어 치료 - 자동으로 검색된 맬웨어에서 악성 파일을 삭제 또는 격리하고 악성 레지스트리 항목을 정리하는 것과 같은 작업을 수행합니다.
* 서명 업데이트 - 자동으로 최신 보호 시그니처(바이러스 정의)를 설치하여 보호가 미리 결정된 빈도에 최신 상태가 되도록 합니다.
* 맬웨어 방지 엔진 업데이트 – 자동으로 Microsoft 맬웨어 방지 엔진을 업데이트합니다.
* 맬웨어 방지 플랫폼 업데이트 – 자동으로 Microsoft 맬웨어 방지 플랫폼을 업데이트합니다.
* 활성 보호 - 신속한 응답을 확인하기 위해 검색된 위협 및 의심스러운 리소스에 대한 Azure 원격 분석 메타데이터를 보고하며 Microsoft 활성 보호 시스템(MAPS)을 통해 실시간 동기 서명 전달을 사용하도록 설정합니다.
* 샘플 보고 - Microsoft 맬웨어 방지 서비스에 샘플을 제공하고 보고하여 서비스를 구체화하고 문제를 해결하는 데 사용합니다.
* 제외 – 응용 프로그램 및 서비스 관리자로 특정 파일, 프로세스, 드라이브를 구성하여 성능 및 기타 이유로 보호 및 검색에서 제외합니다.
* 맬웨어 방지 이벤트 수집 - 운영 체제 이벤트 로그에서 수행된 맬웨어 방지 서비스 상태, 의심스러운 활동 및 수정 작업을 기록하고 고객의 Azure 저장소 계정에 수집합니다.

자세한 정보: 가상 컴퓨터를 보호하는 맬웨어 방지 소프트웨어에 대해 자세히 알아보려면 다음을 참조하세요.

* [Azure Cloud Services 및 가상 컴퓨터용 Microsoft 맬웨어 방지 프로그램](azure-security-antimalware.md)
* [Azure 가상 컴퓨터에 맬웨어 방지 솔루션 배포](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Windows VM에 Trend Micro Deep Security as a Service를 설치하고 구성하는 방법](../virtual-machines/windows/classic/install-trend.md)
* [Windows VM에서 Symantec Endpoint Protection을 설치하고 구성하는 방법](../virtual-machines/windows/classic/install-symantec.md)
* [Azure 가상 컴퓨터에 대한 새로운 맬웨어 방지 옵션 - McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)
* [Azure 마켓플레이스의 보안 솔루션](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>하드웨어 보안 모듈
암호화 및 인증은 키 자체가 보호되지 않는 한 보안을 향상시키지 않습니다. 중요한 키와 암호 정보를 Azure 주요 자격 증명 모음에 보관함으로써 관리와 보안을 단순화할 수 있습니다. 주요 자격 증명 모음은 FIPS 140-2 Level 2 기준에 따라 인증된 HSM(하드웨어 보안 모듈)에 키를 보관할 수 있는 옵션을 제공합니다. 백업 또는 [투명한 데이터 암호화](https://msdn.microsoft.com/library/bb934049.aspx) 를 위한 SQL Server 암호화 키는 응용 프로그램의 키 또는 암호와 함께 주요 자격 증명 모음에 저장됩니다. 이러한 보호된 항목에 대한 사용 권한 및 액세스는 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)를 통해 관리됩니다.

자세한 정보:

* [Azure 주요 자격 증명 모음이란?](../key-vault/key-vault-whatis.md)
* [Azure 주요 자격 증명 모음 시작](../key-vault/key-vault-get-started.md)
* [Azure 주요 자격 증명 모음 블로그](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>가상 컴퓨터 디스크 암호화
Azure 디스크 암호화는 Windows 및 Linux Azure 가상 컴퓨터 디스크를 암호화할 수 있도록 하는 새로운 기능입니다. Azure Disk Encryption는 업계 표준인 Windows의 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 기능과 Linux의 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능을 사용하여 OS 및 데이터 디스크를 위한 볼륨 암호화를 제공합니다.

이 솔루션은 Azure 주요 자격 증명 모음과 함께 통합되어 주요 자격 증명 모음 구독에서 디스크 암호화 키 및 암호를 제어하고 관리할 수 있도록 하며 가상 컴퓨터 디스크의 모든 휴지 상태 데이터가 Azure 저장소에서 암호화되도록 보장합니다.

자세한 정보:

* [Windows 및 Linux IaaS VM용 Azure 디스크 암호화](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
* [Linux 및 Windows 가상 컴퓨터에 대한 Azure 디스크 암호화](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
* [가상 컴퓨터 암호화](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>가상 컴퓨터 백업
Azure 백업은 자본 투자 없이 최소의 비용으로 응용 프로그램 데이터를 보호하는 확장성이 뛰어난 솔루션입니다. 응용 프로그램 오류로 인해 데이터가 손상되고 사용자 오류로 인해 응용 프로그램에 버그가 발생할 수 있습니다. Azure 백업은 Windows 및 Linux를 실행하는 가상 컴퓨터의 보호에 도움이 됩니다.

자세한 정보:

* [Azure 백업이란?](../backup/backup-introduction-to-azure-backup.md)
* [Azure 백업 학습 경로](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Azure 백업 서비스 - FAQ](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
계획되거나 계획되지 않은 중단 상태가 발생하는 경우, 조직 BCDR 전략의 중요한 부분은 회사 워크로드 및 앱을 실행하도록 유지하는 방법을 알아내는 것입니다. Azure Site Recovery는 기본 위치가 중단되는 경우 보조 위치에서 사용할 수 있도록 워크로드 및 앱의 복제, 장애 조치 및 복구를 오케스트레이션하여 해당 작업을 수행합니다.

사이트 복구:

* **BCDR 전략 간소화** - 사이트 복구를 사용하면 단일 위치에서 여러 비즈니스 워크로드 및 앱의 복제, 장애 조치 및 복구를 쉽게 처리할 수 있습니다. 사이트 복구는 복제 및 장애 조치를 오케스트레이션하지만 응용 프로그램 데이터를 가로채거나 이에 대한 정보를 포함하지 않습니다.
* **유연한 복제 제공** - 사이트 복구를 사용하여 Hyper-V 가상 컴퓨터, VMware 가상 컴퓨터 및 Windows/Linux 물리적 서버에서 실행 중인 워크로드를 복제할 수 있습니다.
* **장애 조치 및 복구 지원** - 사이트 복구는 프로덕션 환경에 영향을 주지 않고 재해 복구 훈련을 지원하는 테스트 장애 조치를 제공합니다. 또한 예상된 중단에 대한 데이터 손실을 제거하고 계획된 장애 조치를 실행하거나 예기치 않은 재해에 대한 데이터 손상(복제 빈도에 따라 다름)을 최소화하고 계획되지 않은 장애 조치를 실행할 수 있습니다. 장애 조치 후에 기본 사이트를 장애 복구할 수 있습니다. 사이트 복구는 다중 계층 응용 프로그램의 장애 조치 및 복구를 사용자 지정할 수 있도록 스크립트와 Azure 자동화 통합 문서를 포함할 수 있는 복구 계획을 제공합니다.
* **보조 데이터 센터 제거** - 보조 온-프레미스 사이트 또는 Azure에 복제할 수 있습니다. 재해 복구에 대한 대상으로 Azure를 사용하면 보조 사이트를 유지 관리하는 비용 및 복잡성이 제거됩니다. 복제된 데이터는 Azure Storage에 저장됩니다.
* **기존 BCDR 기술과 통합** - 사이트 복구는 다른 응용 프로그램 BCDR 기능과 협력합니다. 예를 들어 사이트 복구를 사용하여 회사 워크로드의 SQL Server 백 엔드를 보호할 수 있습니다. 여기에는 가용성 그룹의 장애 조치를 관리하는 SQL Server AlwaysOn의 기본 지원이 포함됩니다.

자세한 정보:

* [Azure Site Recovery란?](../site-recovery/site-recovery-overview.md)
* [Azure Site Recovery 작동 방식](../site-recovery/site-recovery-components.md)
* [Azure Site Recovery로 보호되는 작업은 무엇입니까?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>가상 네트워킹
가상 컴퓨터는 네트워크 연결이 필요합니다. 이 요구 사항을 지원하기 위해 Azure에서는 가상 컴퓨터를 Azure 가상 네트워크에 연결해야 합니다. Azure 가상 네트워크는 물리적 Azure 네트워크 패브릭 위에 구축되는 논리적 구조체입니다. 각 논리적 Azure 가상 네트워크는 다른 모든 Azure 가상 네트워크와 격리됩니다. 이 격리를 통해 사용자 배포의 네트워크 트래픽이 다른 Microsoft Azure 고객에게 액세스되지 않게 해줍니다.

자세한 정보:

* [Azure 네트워크 보안 개요](security-network-overview.md)
* [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)
* [네트워킹 기능 및 엔터프라이즈 시나리오에 대한 파트너 관계](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>보안 정책 관리 및 보고
Azure 보안 센터는 위협을 예방, 감지 및 대응하는 데 도움이 되며 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 제공합니다. 이는 Azure 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

Azure 보안 센터는 다음과 같은 방법을 통해 가상 컴퓨터 보안을 최적화하고 모니터링하는 데 도움을 줍니다.

* 시스템 업데이트 적용, ACL 끝점 구성, 맬웨어 방지 프로그램 활성화, 네트워크 보안 그룹 활성화 및 디스크 암호화 적용과 같은 가상 컴퓨터 [보안 권장 사항](../security-center/security-center-recommendations.md) 을 제공합니다.
* 가상 컴퓨터의 상태 모니터링

자세한 정보:

* [Azure 보안 센터 소개](../security-center/security-center-intro.md)
* [Azure 보안 센터 질문과 대답](../security-center/security-center-faq.md)
* [Azure 보안 센터 계획 및 작업](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>규정 준수
Azure 가상 컴퓨터는 FISMA, FedRAMP, HIPAA, PCI DSS Level 1 및 기타 주요 규정 준수 프로그램 인증을 받았습니다. 이러한 인증을 통해 여러분이 직접 만드는 Azure 응용 프로그램도 규정 준수 요구 사항을 충족하기 쉽고 비즈니스가 다양한 국내 및 국제 규정 요구 사항을 충족하기도 쉽습니다.

자세한 정보:

* [Microsoft 보안 센터: 규정 준수](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
* [신뢰할 수 있는 클라우드: Microsoft Azure 보안, 개인 정보 보호 정책 및 규정 준수](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

