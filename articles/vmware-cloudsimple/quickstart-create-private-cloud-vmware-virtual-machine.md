---
title: 빠른 시작 - 프라이빗 클라우드에서 Azure VMware VM 만들기 - 클라우드간편으로 Azure VMware 솔루션
description: 클라우드단순 프라이빗 클라우드에서 Azure VMware VM을 만드는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ac818cfd267b781366c0e32c9f93cc885dff99c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77566151"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>프라이빗 클라우드에서 VMware 가상 머신 만들기

프라이빗 클라우드에서 가상 컴퓨터를 만들려면 Azure 포털에서 CloudSimple 포털에 액세스하여 먼저 합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

1. **모든 서비스를**선택합니다.
2. 클라우드 **간단한 서비스를**검색합니다.
3. 프라이빗 클라우드를 만들려는 CloudSimple 서비스를 선택합니다.
4. **개요** 페이지에서 **CloudSimple 포털로 이동을** 클릭하여 CloudSimple 포털에 대한 새 브라우저 탭을 엽니다.  메시지가 표시되면 Azure 로그인 자격 증명으로 로그인합니다.  

    ![클라우드심플 포털 출시](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>시작 vCenter 웹 ui

이제 vCenter를 시작하여 가상 컴퓨터 및 정책을 설정할 수 있습니다.

vCenter에 액세스하려면 CloudSimple 포털에서 시작합니다. 홈 페이지에서 일반적인 **작업**에서 **vSphere 클라이언트 를 클릭합니다.**  프라이빗 클라우드를 선택한 다음 프라이빗 클라우드에서 **vSphere 클라이언트 시작을** 클릭합니다.

   ![vSphere 클라이언트 시작](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>ISO 또는 vSphere 템플릿 업로드

  > [!WARNING]
  > ISO 업로드의 경우 vSphere HTML5 클라이언트를 사용합니다.  Flash 클라이언트를 사용하면 오류가 발생할 수 있습니다.

1. vCenter에 업로드할 ISO 또는 vSphere 템플릿을 가져와 VM을 만들고 로컬 시스템에서 사용할 수 있도록 합니다.
2. vCenter에서 **디스크** 아이콘을 클릭하고 **vsanDatastore**를 선택합니다. **파일을** 클릭한 다음 **새 폴더**를 클릭합니다.
    ![v센터 ISO](media/vciso00.png)

3. 'ISO 및 템플릿'이라는 제목의 폴더를 만듭니다.

4. IsSo 및 템플릿의 IsSo 폴더로 이동하여 **파일 업로드**를 클릭합니다. 화면의 지침에 따라 ISO를 업로드합니다.

## <a name="create-a-virtual-machine-in-vcenter"></a>vCenter에서 가상 컴퓨터 만들기

1. vCenter에서 호스트 **및 클러스터 아이콘을 클릭합니다.**

2. **워크로드를** 마우스 오른쪽 단추로 클릭하고 **새 가상 컴퓨터를**선택합니다.
    ![새 VM](media/vcvm01.png)

3. **새 가상 컴퓨터 만들기를** 선택하고 다음 을 **클릭합니다.**
    ![새 VM](media/vcvm02.png)

4. 컴퓨터 이름을 지정하고 **워크로드 VM의** 위치를 선택한 다음 을 **클릭합니다.**
    ![새 VM](media/vcvm03.png)

5. **워크로드** 계산 리소스를 선택하고 **다음**을 클릭합니다.
    ![새 VM](media/vcvm04.png)

6. **vsanDatastore를** 선택하고 **다음을**클릭합니다.
    ![새 VM](media/vcvm05.png)

7. 기본 ESXi 6.5 호환성 선택을 유지하고 **다음을**클릭합니다.
    ![새 VM](media/vcvm06.png)

8. 만드는 VM에 대한 ISO의 게스트 OS를 선택하고 **다음을**클릭합니다.
    ![새 VM](media/vcvm07.png)

9. 하드 디스크 및 네트워크 옵션을 선택합니다. 새 CD/DVD 드라이브의 경우 **데이터스토어 ISO 파일을**선택합니다.  공용 IP 주소에서 이 VM으로의 트래픽을 허용하려면 네트워크를 **vm-1로**선택합니다.
    ![새 VM](media/vcvm08.png)

10. 선택 창이 열립니다. 이전에 ISO 및 템플릿 폴더에 업로드한 파일을 선택하고 **확인을**클릭합니다.
    ![새 VM](media/vcvm10.png)

11. 설정을 검토하고 **확인을** 클릭하여 VM을 만듭니다.
    ![새 VM](media/vcvm11.png)

이제 VM이 워크로드 계산 리소스에 추가되고 사용할 준비가 되었습니다. 
![새 VM](media/vcvm12.png)

이제 기본 설정이 완료되었습니다. 온-프레미스 VM 인프라를 사용하는 방법과 유사한 프라이빗 클라우드를 사용할 수 있습니다.

다음 섹션에는 프라이빗 클라우드 워크로드에 대한 DNS 및 DHCP 서버를 설정하고 기본 네트워킹 구성을 수정하는 것에 대한 선택적 정보가 포함되어 있습니다.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>vCenter에 사용자 및 ID 원본 추가(선택 사항)

CloudSimple 은 기본 vCenter 사용자 계정에 `cloudowner@cloudsimple.local`사용자 이름을 할당합니다. 시작하기 위해 추가 계정 설정이 필요하지 않습니다.  CloudSimple은 일반적으로 관리자에게 정상적인 작업을 수행하는 데 필요한 권한을 할당합니다.  온-프레미스 활성 디렉터리 또는 Azure AD를 프라이빗 클라우드의 [추가 ID 원본으로](set-vcenter-identity.md) 설정합니다.

## <a name="create-a-dns-and-dhcp-server-optional"></a>DNS 및 DHCP 서버 만들기(선택 사항)

프라이빗 클라우드 환경에서 실행되는 응용 프로그램 및 워크로드에는 조회 및 IP 주소 할당을 위한 이름 확인 및 DHCP 서비스가 필요합니다. 이러한 서비스를 제공하려면 적절한 DHCP 및 DNS 인프라가 필요합니다. vCenter에서 가상 컴퓨터를 구성하여 프라이빗 클라우드 환경에서 이러한 서비스를 제공할 수 있습니다.

사전 요구 사항

* VLAN이 구성된 분산 포트 그룹

* 온-프레미스 또는 인터넷 기반 DNS 서버로 설정 라우팅

* 가상 컴퓨터 템플릿 또는 ISO를 사용하여 가상 컴퓨터를 만듭니다.

다음 링크는 Linux 및 Windows에서 DHCP 및 DNS 서버를 설정하는 방법에 대한 지침을 제공합니다.

#### <a name="linux-based-dns-server-setup"></a>리눅스 기반 DNS 서버 설정

리눅스는 DNS 서버를 설정하기위한 다양한 패키지를 제공합니다.  다음은 오픈 소스 BIND DNS 서버를 설정하기 위한 지침링크입니다.

[예제 설정](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>윈도우 기반 설정

이러한 Microsoft 항목에서는 Windows 서버를 DNS 서버와 DHCP 서버로 설정하는 방법을 설명합니다.

[DNS 서버로 윈도우 서버](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[DHCP 서버로 윈도우 서버](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>네트워킹 구성 사용자 지정(선택 사항)

CloudSimple 포털의 네트워크 페이지를 사용하면 VM에 대한 방화벽 테이블 및 공용 IP 주소에 대한 구성을 지정할 수 있습니다.

### <a name="allocate-public-ips"></a>공용 IP 할당

1. CloudSimple 포털에서 **공용 IP를 > 네트워크로** 이동합니다.
2. **공용 IP 할당을**클릭합니다.
3. IP 주소 항목을 식별하기 위해 이름을 입력합니다.
4. 기본 위치를 유지합니다.
5. 원하는 경우 슬라이더를 사용하여 유휴 시간 지정을 변경합니다.
6. 공용 IP 주소를 할당할 로컬 IP 주소를 입력합니다.
7. 원하는 경우 연결된 DNS 이름을 입력합니다.
8. **완료**를 클릭합니다.

    ![공용 IP](media/quick-create-pc-public-ip.png)

공용 IP 주소를 할당하는 작업이 시작됩니다. 활동 > 작업 페이지에서 작업 상태를 확인할 수 **있습니다.** 할당이 완료되면 새 항목이 공용 IP 페이지에 표시됩니다.

이 IP 주소를 매핑해야 하는 VM은 위에 지정된 로컬 주소로 구성해야 합니다. IP 주소를 구성하는 절차는 VM 운영 체제에 따라 다릅니다. 올바른 절차는 VM 운영 체제에 대한 설명서를 참조하십시오.

#### <a name="example"></a>예제

예를 들어, 여기에 우분투에 대 한 세부 정보 16.04.

파일 /etc/네트워크/인터페이스의 inet 주소 패밀리 구성에 정적 메서드를 추가합니다. 주소, 넷마스크 및 게이트웨이 값을 변경합니다. 이 예제에서는 eth0 인터페이스, 내부 IP 주소 192.168.24.10, 게이트웨이 주소 192.168.24.1 및 netmask 255.255.255.0을 사용합니다. 사용자 환경의 경우 사용 가능한 서브넷 정보가 환영 전자 메일에 제공됩니다.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
인터페이스를 수동으로 사용하지 않도록 설정합니다.

```
sudo ifdown eth0
```
인터페이스를 수동으로 다시 활성화합니다.

```
sudo ifup eth0
```

기본적으로 인터넷에서 들어오는 모든 트래픽은 **거부됩니다.** 다른 포트를 열려면 [방화벽 테이블을](firewall.md)만듭니다.

내부 IP 주소를 정적 IP 주소로 구성한 후 VM 내에서 인터넷에 연결할 수 있는지 확인합니다.

```
ping 8.8.8.8
```
또한 공용 IP 주소를 사용하여 인터넷에서 VM에 연결할 수 있는지 확인합니다.

VM의 iptable 규칙이 포트 80 인바운드를 차단하지 않는지 확인합니다.
        
```
netstat -an | grep 80
```

포트 80에서 수신 시작되는 http 서버를 시작합니다.
       
```
python2.7 -m SimpleHTTPServer 80
```

또는

```
python3 -m http.server 80
```
데스크톱에서 브라우저를 시작하고 공용 IP 주소에 대한 포트 80을 가리키면 VM의 파일을 찾아볼 수 있습니다.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>공용 IP에 대한 기본 CloudSimple 방화벽 규칙

* VPN 트래픽: VPN과 모든 워크로드 네트워크 및 관리 네트워크 간의 모든 트래픽이 허용됩니다.
* 프라이빗 클라우드 내부 트래픽: 워크로드 네트워크와 관리 네트워크(위에 표시된) 사이의 모든 동서 트래픽이 허용됩니다.
* 인터넷 트래픽:
  * 인터넷에서 들어오는 모든 트래픽은 워크로드 네트워크 및 관리 네트워크에 거부됩니다.
  * 워크로드 네트워크 또는 관리 네트워크에서 인터넷으로 나가는 모든 트래픽이 허용됩니다.

방화벽 규칙 기능을 사용하여 트래픽 이 보호되는 방식을 수정할 수도 있습니다. 자세한 내용은 [방화벽 테이블 및 규칙 설정을](firewall.md)참조하십시오.

## <a name="install-solutions-optional"></a>솔루션 설치(선택 사항)

클라우드심플 프라이빗 클라우드에 솔루션을 설치하여 프라이빗 클라우드 vCenter 환경을 최대한 활용할 수 있습니다. 백업, 재해 복구, 복제 및 기타 기능을 설정하여 가상 컴퓨터를 보호할 수 있습니다. 예를 들어 VMware 사이트 복구 관리자(VMware SRM) 및 Veeam 백업 & 복제가 있습니다.

솔루션을 설치하려면 제한된 기간 동안 추가 권한을 요청해야 합니다. [권한 에스컬레이션을](escalate-private-cloud-privileges.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware VM 사용](quickstart-create-vmware-virtual-machine.md)
* [Azure ExpressRoute를 사용하여 온-프레미스 네트워크에 연결](on-premises-connection.md)
* [CloudSimple 네트워크에서 VPN 게이트웨이 설정](vpn-gateway.md)
