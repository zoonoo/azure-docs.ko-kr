---
title: "VM 및 역할 인스턴스 이름 확인"
description: "Azure IaaS, 하이브리드 솔루션, 서로 다른 클라우드 서비스, Active Directory, 자체 DNS 서버 사용 시의 이름 확인 시나리오  "
services: virtual-network
documentationcenter: na
author: GarethBradshawMSFT
manager: carmonm
editor: tysonn
ms.assetid: 5d73edde-979a-470a-b28c-e103fcf07e3e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2016
ms.author: telmos
translationtype: Human Translation
ms.sourcegitcommit: b574360cce92350a9bf52c21678bf0e91ceb270e
ms.openlocfilehash: 479cf8cf358d0b242d8ce030d8639b493e4767d8


---
# <a name="name-resolution-for-vms-and-role-instances"></a>VM 및 역할 인스턴스에 대한 이름 확인
IaaS, PaaS, 하이브리드 솔루션 호스팅에 Azure를 어떻게 사용할지에 따라 귀하가 만든 VM 및 역할 인스턴스가 서로 통신하도록 설정해야 할 수도 있습니다. IP 주소를 이용해 통신할 수도 있지만, 기억하기 쉽고 변경되지 않는 이름을 사용하면 보다 간편합니다. 

역할 인스턴스와 VM을 Azure에 호스트할 때 도메인 이름으로 내부 IP 주소를 확인하는 방법은 다음의 두 가지가 있습니다.

* [Azure에서 제공하는 이름 확인](#azure-provided-name-resolution)
* [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server) (Azure에서 제공하는 DNS 서버에 쿼리를 전달할 수 있음) 

어떤 방법으로 이름을 확인할지는 사용하는 VM 및 역할 인스턴스가 서로 어떻게 통신해야 하는지에 따라 다릅니다.

**다음 표에 각 시나리오 별로 해당하는 이름 확인 방법이 나와 있습니다.**

| **시나리오** | **솔루션** | **접미사** |
| --- | --- | --- |
| 동일한 클라우드 서비스 또는 가상 네트워크에 위치한 역할 인스턴스 또는 VM 간 이름 확인 |[Azure에서 제공하는 이름 확인](#azure-provided-name-resolution) |호스트 이름 또는 FQDN |
| 서로 다른 네트워크에 위치한 역할 인스턴스 또는 VM 간 이름 확인 |고객이 관리하는 DNS 서버가 Azure(DNS 프록시)에서 확인을 위해 vnet 간의 쿼리를 전달합니다.  [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server) |FQDN만 |
| Azure의 VM 또는 역할 인스턴스에서 온-프레미스 컴퓨터와 서비스 이름 확인 |고객이 관리하는 DNS 서버(예: 온-프레미스 도메인 컨트롤러, 로컬 읽기 전용 도메인 컨트롤러 또는 영역 전송을 사용하여 동기화된 DNS 보조)입니다.  See [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server) |FQDN만 |
| 온-프레미스 컴퓨터에서 Azure 호스트 이름 확인 |해당 vnet에서 고객이 관리하는 DNS 프록시 서버에 쿼리를 전달하면 프록시 서버는 이름 확인을 위해 Azure에 쿼리를 전달합니다. See [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server) |FQDN만 |
| 내부 IP에 대한 역방향 DNS |[자체 DNS 서버를 사용한 이름 확인](#name-resolution-using-your-own-dns-server) |해당 없음 |
| 서로 다른 클라우드 서비스에 위치하며 가상 네트워크에 존재하지 않는 VM 또는 역할 인스턴스 간 이름 확인 |사용할 수 없습니다. 가상 네트워크 외부에 있는 VM과 역할 인스턴스가 서로 다른 클라우드 서비스에 위치한 경우에는 연결을 지원하지 않습니다. |해당 없음 |

## <a name="azure-provided-name-resolution"></a>Azure에서 제공하는 이름 확인
Azure에서는 공용 DNS 이름 확인과 함께, 동일한 가상 네트워크 또는 클라우드 서비스 내에 있는 VM 및 역할 인스턴스에 대한 내부 이름 확인을 제공합니다.  클라우드 서비스의 VM/인스턴스는 동일한 DNS 접미사를 공유하므로 호스트 이름만으로도 충분하지만 클래식 가상 네트워크에서는 여러 클라우드 서비스에 여러 DNS 접미사가 있으므로 여러 클라우드 서비스에서 이름을 확인하려면 FQDN이 필요합니다.  리소스 관리자 배포 모델 내의 가상 네트워크에 DNS 접미사가 가상 네트워크에 걸쳐 일관적이므로(따라서 FQDN이 필요 없음) NIC 및 VM 모두에 DNS 이름을 지정할 수 있습니다. Azure에서 제공한 이름 확인은 별도로 구성할 필요가 없으나, 위의 표에 나온 바와 같이 모든 배포 서비스에서 선택할 수 있는 것은 아닙니다.

> [!NOTE]
> 웹 역할 및 작업자 역할의 경우, Azure 서비스 관리 REST API를 사용하면 역할 이름과 인스턴스 번호를 통해 역할 인스턴스의 내부 IP 주소를 접속할 수도 있습니다. 자세한 내용은 [서비스 관리 REST API 참조](https://msdn.microsoft.com/library/azure/ee460799.aspx)를 참조하세요.
> 
> 

### <a name="features-and-considerations"></a>기능 및 고려 사항
**기능:**

* 사용 편의성: Azure에서 제공하는 이름 확인을 사용하면 별도로 구성할 필요가 없습니다.
* Azure에서 제공하는 이름 확인 서비스는 항상 사용 가능하며 사용자 고유 DNS 서버의 클러스터를 만들고 관리해야 하는 필요성을 줄여줍니다.
* 온-프레미스와 Azure 호스트 이름을 모두 확인하기 위해 자체 DNS 서버와 함께 사용할 수 있습니다.
* 역할 인스턴스/VM이 동일한 클라우드 서비스에 위치할 경우에 이름 확인이 제공되며 FQDN이 필요하지 않습니다.
* 리소스 관리자 배포 모델을 사용하는 가상 네트워크에서 VM 사이에 이름 확인이 제공됩니다. FQDN은 필요 없습니다. 다른 클라우드 서비스에서 이름을 확인할 때 클래식 배포 모델에서 가상 네트워크는 FQDN을 필요로 합니다. 
* 자동 생성되는 이름 대신에 배포를 가장 잘 설명해주는 호스트 이름을 사용할 수 있습니다.

**고려 사항:**

* Azure에서 만든 DNS 접미사는 수정할 수 없습니다.
* 사용자 고유의 레코드를 수동으로 등록할 수 없습니다.
* WINS 및 NetBIOS는 지원되지 않습니다. (Windows 탐색기에 VM은 표시되지 않습니다.)
* 호스트 이름이 DNS와 호환될 수 있어야 합니다. (0-9, a-z 및 '-'만 사용이 가능하며, '-'로 시작하거나 끝날 수 없습니다. RFC 3696 섹션을 2를 참조하세요.)
* DNS 쿼리 트래픽은 각 VM에 대해 제한됩니다. 이 대부분의 응용 프로그램에 영향을 주지 않아야 합니다.  요청 제한이 확인되는 경우 클라이언트쪽 캐싱이 사용하도록 설정되었는지 확인합니다.  자세한 내용은 참조 [Azure에서 제공하는 이름 확인 활용](#Getting-the-most-from-Azure-provided-name-resolution)을 참조하세요.
* 처음 180개의 클라우드 서비스 내에서 VM만 클래식 배포 모델 내의 가상 네트워크에 대해 등록됩니다. 이것은 리소스 관리자 배포 모델에서 가상 네트워크에는 적용되지 않습니다.

### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Azure에서 제공하는 이름 확인 활용
**클라이언트쪽 캐싱:**

모든 DNS 쿼리를 네트워크를 통해 전송해야 하는 것은 아닙니다.  클라이언트쪽 캐싱을 사용하면 대기 시간을 줄이고 로컬 캐시에서 되풀이되는 DNS 쿼리를 확인하여 네트워크 블립에 대한 복원력을 개선하는 데 도움이 됩니다.  DNS 레코드는 레코드 새로 고침에 영향을 주지 않으면서 캐시가 가능한 오랫동안 레코드를 저장할 수 있도록 하는 TTL(Time-To-Live)을 포함하므로 클라이언트쪽 캐싱은 대부분의 상황에 적합합니다.

기본 Windows DNS 클라이언트에는 기본 제공되는 DNS 캐시가 있습니다.  일부 Linux 배포판은 기본적으로 캐싱을 포함하지 않으므로 각 Linux VM에 캐싱을 추가하는 것이 좋습니다(로컬 캐시가 아직 없다는 것을 확인한 후).

dnsmasq와 같은 다양한 여러 DNS 캐싱이 제공되며 여기서는 가장 일반적인 배포판에 dnsmasq를 설치하는 단계를 설명합니다.

* **Ubuntu(resolvconf 사용)**:
  * dnsmasq 패키지를 설치합니다("sudo apt-get install dnsmasq").
* **SUSE(netconf 사용)**:
  * dnsmasq 패키지를 설치합니다("sudo zypper install dnsmasq"). 
  * dnsmasq 서비스를 사용하도록 설정합니다("systemctl enable dnsmasq.service"). 
  * dnsmasq 서비스를 시작합니다("systemctl start dnsmasq.service"). 
  * "/etc/sysconfig/network/config"를 편집하고 NETCONFIG_DNS_FORWARDER=""를 "dnsmasq"로 변경합니다.
  * 캐시를 로컬 DNS 확인자로 설정하기 위해 resolv.conf("netconfig update")를 업데이트합니다.
* **OpenLogic(NetworkManager 사용)**:
  * dnsmasq 패키지를 설치합니다("sudo yum install dnsmasq").
  * dnsmasq 서비스를 사용하도록 설정합니다("systemctl enable dnsmasq.service").
  * dnsmasq 서비스를 시작합니다("systemctl start dnsmasq.service").
  * "prepend domain-name-servers 127.0.0.1;"을 "/etc/dhclient-eth0.conf"에 추가합니다.
  * 캐시를 로컬 DNS 확인자로 설정하기 위해 네트워크 서비스("service network restart")를 다시 시작합니다.

> [!NOTE]
> 'dnsmasq' 패키지는 여러 DNS 캐시 중에 Linux에 사용할 수 있는 유일한 캐시입니다.  사용하기 전에 특정 요구 사항에 대한 적합성을 확인하고 다른 캐시가 설치되어 있지 않은지 확인합니다.
> 
> 

**클라이언트쪽 재시도:**

DNS는 주로 UDP 프로토콜입니다.  UDP 프로토콜은 메시지 배달을 보장하지 않으므로 DNS 프로토콜 자체에서 재시도 논리가 처리됩니다.  각 DNS 클라이언트(운영 체제)는 작성자의 기본 설정에 따라 서로 다른 재시도 논리를 나타낼 수 있습니다.

* Windows 운영 체제는 1초 후 재시도한 후 2초, 4초 후 다시 재시도하고 또 다시 4초 후 재시도합니다. 
* 기본 Linux 설정에서는 5초 후 재시도합니다.  1초 간격으로 5번 재시도하도록 설정을 변경하는 것이 좋습니다.  

Linux VM에서 현재 설정을 확인하려면 'cat /etc/resolv.conf'에서 'options' 줄을 확인합니다. 예를 들면 다음과 같습니다.

    options timeout:1 attempts:5

resolv.conf 파일은 일반적으로 자동으로 생성되며 편집할 수 없습니다.  'options' 줄을 추가하는 구체적인 단계는 배포판마다 다릅니다.

* **Ubuntu** (resolvconf 사용):
  * options 줄을 '/etc/resolveconf/resolv.conf.d/head'에 추가합니다. 
  * 'resolvconf -u'를 실행하여 업데이트합니다.
* **SUSE** (netconf 사용):
  * 'timeout:1 attempts:5'를 '/etc/sysconfig/network/config'의 NETCONFIG_DNS_RESOLVER_OPTIONS="" 매개 변수에 추가합니다. 
  * 'netconfig update'를 실행하여 업데이트합니다.
* **OpenLogic** (NetworkManager 사용):
  * 'echo "options timeout:1 attempts:5"'를 '/etc/NetworkManager/dispatcher.d/11-dhclient'에 추가합니다. 
  * 'service network restart'를 실행하여 업데이트합니다.

## <a name="name-resolution-using-your-own-dns-server"></a>자체 DNS 서버를 이용한 이름 확인
이름 확인 요구 사항이 Azure에서 제공하는 기능을 벗어날 수 있는 여러 상황이 있습니다. 예를 들어 Active Directory 도메인을 사용할 때나 vnet(가상 네트워크) 간의 DNS 확인이 요구될 때와 같은 상황입니다.  이러한 시나리오를 해결하기 위해 Azure는 자체 DNS 서버를 사용할 수 있는 기능을 제공합니다.  

가상 네트워크 내에서 DNS 서버는 해당 가상 네트워크 내에서 호스트 이름을 확인하기 위해 Azure의 재귀 확인자에게 DNS 쿼리를 전달할 수 있습니다.  예를 들어 Azure에서 실행 중인 DC(도메인 컨트롤러)는 해당 도메인에 대한 DNS 쿼리에 응답하고 Azure에 다른 모든 쿼리를 전달할 수 있습니다.  이렇게 하면 VM은 온-프레미스 리소스(DC를 통해)와 Azure에서 제공하는 호스트 이름(전달자를 통해)을 확인할 수 있습니다.  Azure의 재귀 확인자에 대한 액세스는 가상 IP 168.63.129.16을 통해 제공됩니다.

또한 DNS 전달로 vnet 간 DNS 확인이 가능하며 온-프레미스 컴퓨터는 Azure에서 제공하는 호스트 이름을 확인할 수 있습니다.  VM의 호스트 이름을 확인하려면 DNS 서버 VM이 동일한 가상 네트워크에 있어야 하며 Azure에 호스트 이름 쿼리를 전달하도록 구성되어야 합니다.  DNS 접미사는 각 vnet마다 다르기 때문에 확인을 위해 올바른 vnet에 DNS 쿼리를 보내도록 조건부 전달 규칙을 사용할 수 있습니다.  다음 이미지에서는 두 vnet과 온-프레미스 네트워크가 이 메서드를 사용하여 vnet 간 DNS 확인을 수행하는 것을 보여 줍니다.  예제 DNS 전달자는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) 및 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)에서 사용할 수 있습니다.

![Vnet 간 DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Azure에서 제공하는 이름 확인을 사용하는 경우 DHCP를 사용하여 각 VM에 내부 DNS 접미사(*.internal.cloudapp.net)를 제공합니다.  그러면 호스트 이름 레코드가 internal.cloudapp.net 영역에 있으므로 호스트 이름 확인을 수행할 수 있습니다.  자체 이름 확인 솔루션을 사용하는 경우 이 IDNS 접미사는 다른 DNS 아키텍처에 방해가 되기 때문에 VM에 제공되지 않습니다(예: 도메인 가입 시나리오).  대신 작동하지 않는 자리 표시자(reddog.microsoft.com)가 제공됩니다.  

필요한 경우 PowerShell 또는 API를 사용하여 내부 DNS 접미사를 확인할 수 있습니다.

* 리소스 관리자 배포 모델에서 가상 네트워크의 경우, [네트워크 인터페이스 카드](https://msdn.microsoft.com/library/azure/mt163668.aspx) 리소스 또는 [Get AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) cmdlet을 통해 접미사를 사용할 수 있습니다.    
* 클래식 배포 모델에서 접미사는 [배포 API 가져오기](https://msdn.microsoft.com/library/azure/ee460804.aspx) 호출 또는 [Get-AzureVM -Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx) cmdlet을 통해 사용할 수 있습니다.

Azure에 전달하는 쿼리가 사용자 요구에 적합하지 않은 경우 자체 DNS 솔루션을 제공해야 합니다.  DNS 솔루션은 다음을 수행해야 합니다:

* 예를 들어 [DDNS](virtual-networks-name-resolution-ddns.md)를 통해 적절한 호스트 이름 확인을 제공해야 합니다.  DDNS를 사용하는 경우 Azure의 DHCP 임대는 매우 길고 청소는 DNS 레코드를 중간에 제거할 수 있기 때문에 DNS 레코드 청소를 사용하지 않도록 설정해야 합니다. 
* 외부 도메인 이름을 확인할 수 있도록 적절한 재귀 확인을 제공해야 합니다.
* 제공하는 클라이언트에서 액세스 가능해야 하고(포트 53에서 TCP 및 UDP) 인터넷에 액세스할 수 있어야 합니다.
* 외부 에이전트로 인해 나타나는 위험을 완화하기 위해 인터넷의 액세스로부터 보호되어야 합니다.

> [!NOTE]
> 최상의 성능을 위해 DNS 서버로 Azure VM을 사용할 때는 IPv6을 사용하지 않도록 설정하고 [인스턴스 수준 공용 IP](virtual-networks-instance-level-public-ip.md) 를 각 DNS 서버 VM에 할당해야 합니다.  Windows Server를 DNS 서버로 사용하려고 선택한 경우 [이 문서](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) 에서 추가로 성능을 분석하고 최적화하는 방법을 참조하세요.
> 
> 

### <a name="specifying-dns-servers"></a>DNS 서버 지정
사용자 고유의 DNS 서버를 사용할 때 Azure는 가상 네트워크당 또는 네트워크 인터페이스(리소스 관리자)/클라우드 서비스(클래식)당 여러 DNS 서버를 지정할 수 있는 기능을 제공합니다.  클라우드 서비스/네트워크 인터페이스에 대해 지정된 DNS 서버가 가상 네트워크에 대해 지정된 서버보다 우선적으로 사용됩니다.

> [!NOTE]
> DNS 서버 IP와 같은 네트워크 연결 속성은 Windows VM 내에서 직접 편집할 수 없습니다. 이들은 가상 네트워크 어댑터가 교체될 때 서비스 치료 중에 지워질 수도 있기 때문입니다. 
> 
> 

리소스 관리자 배포 모델을 사용할 때는 DNS 서버를 포털, API/템플릿([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) 또는 PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [nic](https://msdn.microsoft.com/library/mt619370.aspx))에서 지정할 수 있습니다.

클래식 배포 모델을 사용할 때 가상 네트워크에 대한 DNS 서버를 포털 또는 [*네트워크 구성* 파일](https://msdn.microsoft.com/library/azure/jj157100)에서 지정할 수 있습니다.  클라우드 서비스의 경우 DNS 서버는 [ *서비스 구성* 파일](https://msdn.microsoft.com/library/azure/ee758710)을 통해 또는 Powershell ([New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)) 내에서 지정됩니다.

> [!NOTE]
> 이미 배포된 가상 네트워크/가상 컴퓨터에 대한 DNS 설정을 변경하면 영향을 받는 각 VM을 다시 시작해야 변경사항이 적용됩니다.
> 
> 

## <a name="next-steps"></a>다음 단계
리소스 관리자 배포 모델:

* [가상 네트워크 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [네트워크 인터페이스 카드 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [새-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
* [새-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

클래식 배포 모델:

* [Azure 서비스 구성 스키마](https://msdn.microsoft.com/library/azure/ee758710)
* [가상 네트워크 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100)
* [네트워크 구성 파일을 사용하여 가상 네트워크 구성](virtual-networks-using-network-configuration-file.md) 




<!--HONumber=Dec16_HO2-->


