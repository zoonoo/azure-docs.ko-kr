---
title: Azure 보증 Linux 배포판 | Microsoft Docs
description: Ubuntu, CentOS, Oracle 및 SUSE 관련 지침을 포함하여 Azure에서 Linux의 인증 배포를 수행하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: szark
ms.openlocfilehash: a1be0b6870882d3c7b0281dec7933e87c50e49de
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834563"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Azure의 Linux 배포판을 보증
파트너는 Azure Marketplace에서 Linux 이미지를 제공합니다. 다양한 Linux 커뮤니티와 작업하여 보증 배포 목록에 다양한 옵션을 추가합니다. Marketplace에서 사용할 수 없는 배포판의 경우 [Linux 운영 체제가 포함된 가상 하드 디스크 만들기 및 업로드](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)의 지침에 따라 항상 고유한 Linux를 가져올 수 있습니다.

## <a name="supported-distributions-and-versions"></a>지원되는 배포판 및 버전
다음 표에서 Linux 배포판 및 Azure에서 지원 되는 버전을 나열합니다. 가리킵니다 [Microsoft Azure의 Linux 이미지 지원](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) Linux 및 Azure에서 오픈 소스 기술에 대 한 지원에 대 한 자세한 내용에 대 한 합니다.

Hyper-V 및 Azure용 LIS(Linux 통합 서비스) 드라이버는 Microsoft가 업스트림 Linux 커널로 직접 제공되는 커널 모듈입니다.  일부 LIS 드라이버는 기본적으로 배포 커널에 빌드됩니다. RHEL(Red Hat Enterprise)/CentOS를 기반으로 둔 이전 배포는 [Hyper-V 및 Azure용 Linux Integration Services 버전 4.2](https://www.microsoft.com/download/details.aspx?id=55106)에서 별도의 다운로드로 제공됩니다. LIS 드라이버에 대한 자세한 내용은 [Linux 커널 요구 사항](create-upload-generic.md#linux-kernel-requirements)을 참조하세요.

Azure Linux 에이전트는 Azure Marketplace 이미지에 이미 사전 설치되어 있으며 일반적으로 배포판의 패키지 리포지토리에서 사용할 수 있습니다. 소스 코드는 [GitHub](https://github.com/azure/walinuxagent)에서 찾을 수 있습니다.


| 배포 | Version | 드라이버 | 에이전트 |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 이상, 7.0 이상 |CentOS 6.3: [LIS 다운로드](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+: 커널에 있음 |패키지: "WALinuxAgent"의 [리포지토리](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/)에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0 이상 |커널에 있음 |소스 코드: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9 이상, 8.2 이상 |커널에 있음 |패키지: "waagent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4 이상, 7.0 이상 |커널에 있음 |패키지: "WALinuxAgent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7+, 7.1+, 8.0+ |커널에 있음 |패키지: "WALinuxAgent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SAP용 SLES<br>11 SP4<br>12 SP1+<br>15|커널에 있음 |패키지:<p> 11의 경우 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 리포지토리에 있음<br>12의 경우 "python-azure-agent" 아래의 "공용 클라우드" 모듈에 포함됨<br/>소스 코드: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |커널에 있음 |패키지: "python-azure-agent"의 [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 이상 **<sup>1</sup>** |커널에 있음 |패키지: "walinuxagent"의 리포지토리에 있음 <br/>소스 코드: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>**  Ubuntu 12.04 및 14.04에 대 한 추가 지원에 대 한 내용은 여기에서 찾을 수 있습니다. [보안 유지 관리를 확장 하는 Ubuntu](https://www.ubuntu.com/esm)합니다.


## <a name="image-update-cadence"></a>이미지 업데이트 주기
Azure 보증된 Linux 배포판의 게시자 최신 패치 및 분기별 또는 더 빠른 주기로 보안 픽스를 사용 하 여 Azure Marketplace에서 해당 이미지를 정기적으로 업데이트는 필요 합니다. Azure Marketplace에서 업데이트 된 이미지는 이미지 SKU의 새 버전으로 고객에 게 자동으로 사용할 수 있습니다. Linux 이미지를 찾는 방법에 대 한 자세한 정보: [Azure Marketplace에서 Linux VM 이미지 찾기](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage)합니다.

### <a name="additional-links"></a>추가 링크
 - [SUSE 공용 클라우드 이미지 수명 주기](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>커널 azure 조정

Azure는 다양 한 Linux 보증된 배포판 이러한 Azure Marketplace에 게시 되는 이미지를 최적화 하기 위해 밀접 하 게 합니다. 이 공동 작업의 측면 중 하나는 Azure 플랫폼에 최적화 되며 Linux 배포판의 완전히 지원 되는 구성 요소로 전달 하는 "조정 된" Linux 커널의 개발 합니다. Azure 조정 커널 새로운 기능과 성능 향상 및 기본 또는 배포에서 사용할 수 있는 제네릭 커널에 비해 (일반적으로 분기별)는 더 빠른 주기로 합니다.

대부분의 경우 Azure Marketplace에서 기본 이미지에 미리 설치 된 이러한 커널을 찾을 수 있습니다 하 고 있으므로 Azure 고객에 게 즉시 최적화 된 이러한 커널의 혜택 받습니다. 이러한 Azure 조정 커널에 대 한 자세한 내용은 다음 링크에 있습니다.

 - CentOS Azure 조정 커널-SIG-CentOS 가상화를 통해 사용할 수 있는 [자세한 정보](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian 클라우드 커널-Debian 10 및 azure에서 Debian 9 "backports" 이미지를 사용할 수 있는 [추가 정보](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - SLES Azure 조정 커널- [자세한 정보](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu Azure 조정 커널- [자세한 정보](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>파트너

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

CoreOS 웹 사이트:

*CoreOS는 보안, 일관성 및 안정성을 위해 설계되었습니다. yum 또는 apt를 통한 패키지를 설치하는 대신 CoreOS는 더 높은 추상화 수준에서 서비스를 관리하려면 Linux 컨테이너를 사용합니다. 단일 서비스 코드 및 모든 종속성이 하나 또는 여러 CoreOS 컴퓨터에서 실행할 수 있는 컨테이너 내에서 패키지됩니다.*

### <a name="credativ"></a>Credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ는 독립적인 컨설팅 및 서비스 회사로, 주요 사업 분야는 무료 소프트웨어를 사용하여 전문 솔루션을 개발하고 구현하는 것입니다. 시장을 선도하는 오픈 소스 전문 회사인 Credative는 지원을 받는 많은 IT 부서들로부터 국제적인 명성을 얻고 있습니다. 현재 Credativ는 Microsoft와 협력 관계를 맺고 Debian 8(Jessie) 및 Debian 7(Wheezy) 이전 버전에 해당하는 Debian 이미지를 준비 중입니다. 두 이미지 모두 Azure에서 실행하도록 특별히 설계되었으며 플랫폼을 통해 쉽게 관리할 수 있습니다. Credativ는 또한 자체적인 오픈 소스 지원 센터를 통해 장기적인 유지 및 Azure의 Debian 이미지 업데이트를 지원합니다.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle의 전략은 공용 및 프라이빗 클라우드에 대해 광범위한 솔루션 포트폴리오를 제공하는 것입니다. 이 전략을 통해 고객은 Oracle 클라우드 및 다른 클라우드에 Oracle 소프트웨어를 배포하는 방법을 유연하게 선택할 수 있습니다. Oracle과 Microsoft의 파트너 관계를 통해 고객은 Oracle의 확실한 인증 및 지원으로 Microsoft 공용 및 프라이빗 클라우드에서 Oracle 소프트웨어를 배포할 수 있습니다.  Oracle 공용 및 프라이빗 클라우드 솔루션에 대한 Oracle의 이행 및 투자는 변하지 않습니다.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat은 전 세계의 오픈 소스 솔루션 시장을 선도하는 기업입니다. Fortune지 선정 500대 기업의 90% 이상이 Red Hat의 도움을 받아 비즈니스 과제를 해결하고, IT와 비즈니스 전략을 연결하고, 기술의 미래를 준비하고 있습니다. 이를 위해 Red Hat은 개방형 비즈니스 모델과 경제적이고 예측 가능한 구독 모델을 통해 안전한 솔루션을 제공합니다.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server on Azure는 클라우드 컴퓨팅에 대해 우수한 안정성과 보안을 제공하는 검증된 플랫폼입니다. SUSE의 다양한 Linux 플랫폼은 Azure 클라우드 서비스와 자연스럽게 통합되어 쉽게 관리할 수 있는 클라우드 환경을 제공합니다. 1,800곳이 넘는 SUSE Linux Enterprise Server의 독립 소프트웨어 공급업체에서 9,200개 이상의 인증된 애플리케이션을 통해 SUSE는 데이터 센터에서 지원되는 실행 중인 작업이 확실히 Azure에 배포될 수 있도록 합니다.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Canonical 엔지니어링과 개방형 커뮤니티 거버넌스로 인해 소비자용 개인 클라우드 서비스를 비롯한 클라이언트, 서버 및 클라우드 컴퓨팅에서 Ubuntu가 성공할 수 있었습니다. Canonical은 전화에서 클라우드까지 적용되는 Ubuntu의 무료 통합 플랫폼에 대한 비전이 실현되도록 전화, 태블릿, TV 및 데스크톱에 대해 일관된 인터페이스 제품군을 제공합니다. 이러한 비전으로 인해 공용 클라우드 공급자에서 가전제품 제조업체에 이르는 다양한 기관에서 Ubuntu를 우선적으로 선택하고 있으며 개별 기술자 사이에서도 Ubuntu를 선호하게 되었습니다.

Canonical은 전 세계의 개발자 및 엔지니어링 센터와 함께 PC, 서버 및 핸드헬드 디바이스 업계에 Ubuntu 솔루션을 제공하기 위해 하드웨어 제조업체, 콘텐츠 공급자 및 소프트웨어 개발자와 유일하게 파트너 입장에 있습니다.
