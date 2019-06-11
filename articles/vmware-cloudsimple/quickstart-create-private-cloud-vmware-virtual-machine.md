---
title: 빠른 시작-사설 클라우드에서 VMware VM 만들기
description: CloudSimple 사설 클라우드를 만드는 방법 및 VMware VM을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5837fd615b8dbf32ff289a2e0d09da9db51a908d
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481506"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>사설 클라우드에 VMware 가상 머신 만들기

사설 클라우드에 가상 컴퓨터를 만들려면 Azure portal에서 CloudSimple 포털에 액세스 하 여 시작 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

1. **모든 서비스**를 선택합니다.
2. 검색할 **CloudSimple 서비스**합니다.
3. 사설 클라우드를 만들려는 CloudSimple 서비스를 선택 합니다.
4. **개요** 페이지에서 클릭 **CloudSimple 포털로 이동** CloudSimple 포털에 대 한 새 브라우저 탭을 열려면 합니다.  메시지가 표시 되 면 Azure 로그인 자격 증명에 로그인 합니다.  

    ![CloudSimple 포털 시작](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>VCenter 웹 ui를 시작 합니다.

이제 가상 머신과 정책을 설정 하는 vCenter를 시작할 수 있습니다.

VCenter에 액세스 하려면 CloudSimple 포털에서 시작 합니다. 홈 페이지에서 아래 **일반적인**, 클릭 **vSphere 클라이언트를 시작**합니다.  사설 클라우드를 선택 하 고 클릭 **vSphere 클라이언트를 시작** 사설 클라우드에서 합니다.

   ![VSphere 클라이언트를 시작 합니다.](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>ISO 또는 vSphere 서식 파일 업로드

> [!WARNING]
> ISO 업로드 HTML5 vSphere 클라이언트를 사용 합니다.  플래시 클라이언트를 사용 하 여 오류가 발생할 수 있습니다.

1. 업로드 하려는 vcenter 가상 컴퓨터를 만드는 것을 사용할 수 있는 로컬 시스템에서 ISO 또는 vSphere 템플릿을 가져옵니다.

2. Vcenter에서 클릭 합니다 **디스크** 아이콘을 선택 **vsanDatastore**합니다. 클릭 **파일** 을 클릭 한 다음 **새 폴더**합니다.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. ISO 파일을 저장할 폴더를 만듭니다.

4. 만들어지면 새 폴더로 이동 하 고 클릭 **파일 업로드**합니다. 에 따라는 화면의 ISO를 업로드 하는 지침입니다.

## <a name="create-a-virtual-machine-in-vcenter"></a>VCenter의 가상 머신 만들기

1. Vcenter에서 클릭 합니다 **호스트 및 클러스터** 아이콘입니다.

2. 마우스 오른쪽 단추로 클릭 **워크 로드** 선택한 **새 가상 머신을**합니다.
    
    ![가상 머신 만들기](media/create-vcenter-virtual-machine-01.png)

3. 선택 **새 가상 머신 만들기** 누릅니다 **다음**합니다.

    ![새 가상 컴퓨터 마법사](media/create-vcenter-virtual-machine-02.png)

4. 이름에서 컴퓨터를 선택 합니다 **워크 로드 VM** 폴더를 마우스 클릭 **다음**합니다.

    ![이름 및 폴더를 선택 합니다.](media/create-vcenter-virtual-machine-03.png)

5. 선택 된 **워크 로드** 계산 리소스 및 클릭 **다음**합니다.

    ![계산 리소스를 선택 합니다.](media/create-vcenter-virtual-machine-04.png)

6. 선택 **vsanDatastore** 누릅니다 **다음**합니다.

    ![저장소 선택](media/create-vcenter-virtual-machine-05.png)

7. 기본 ESXi 6.5 호환성 선택 항목을 유지 하 고 클릭 **다음**합니다.

    ![호환성을 선택 합니다.](media/create-vcenter-virtual-machine-06.png)

8. 가상 컴퓨터에 대 한 ISO의 게스트 OS를 선택 하 고 클릭 **다음**합니다.

    ![게스트 OS를 사용자 지정](media/create-vcenter-virtual-machine-07.png)

9. 하드 디스크 및 네트워크 옵션을 선택 합니다. 새 CD/DVD 드라이브에 대 한 선택 **데이터 저장소 ISO 파일**합니다.  이 가상 머신에 공용 IP 주소에서 트래픽을 허용 하려는 경우로 네트워크를 선택 **v m-1**합니다.

    ![사용자 지정 하드웨어 선택](media/create-vcenter-virtual-machine-08.png)

10. 선택 창이 열립니다. Iso 및 템플릿 폴더에 이전에 업로드 한 파일을 선택 하 고 클릭 **확인**합니다.

    ![ISO를 선택 합니다.](media/create-vcenter-virtual-machine-10.png)

11. 설정을 검토 하 고 클릭 **확인** 가상 머신을 만듭니다.

    ![옵션 검토](media/create-vcenter-virtual-machine-11.png)

가상 컴퓨터 작업 계산 리소스에 추가 됩니다 하 고 사용할 준비가 되었습니다. 

![VCenter에 새 가상 컴퓨터](media/create-vcenter-virtual-machine-12.png)

기본 설정 완료 되었습니다. 온-프레미스 가상 컴퓨터 인프라 사용 하는 방법 비슷합니다 사설 클라우드를 사용 하 여 시작할 수 있습니다.

다음 섹션에는 DNS 및 DHCP 서버에 대 한 설정 사설 클라우드 워크 로드 및 기본 네트워킹 구성을 수정 하는 방법에 대 한 선택적 정보가 포함 되어 있습니다.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>(선택 사항) vcenter 사용자 및 Id 원본 추가

CloudSimple 할당 username 사용 하 여 기본 vCenter 사용자 계정은 **cloudowner@cloudsimple.local** 합니다. 추가 계정 설치가 필요 하지 않습니다 시작할 수 있습니다.  CloudSimple는 일반적으로 관리자가 정상적인 작업 수행에 필요한 권한을 할당 합니다.  온-프레미스 active directory 또는 Azure AD 설정를 [추가 id 원본](https://docs.azure.cloudsimple.com/set-vcenter-identity/) 사설 클라우드에 있습니다.

## <a name="create-a-dns-and-dhcp-server-optional"></a>DNS 및 DHCP 서버 (선택 사항) 만들기

응용 프로그램 및 사설 클라우드 환경에서 실행 중인 워크 로드에는 이름 확인 및 조회 및 IP 주소 할당에 DHCP 서비스를 필요 합니다. 적절 한 DHCP 및 DNS 인프라를 이러한 서비스를 제공 해야 합니다. 사설 클라우드 환경에서 이러한 서비스를 제공 하는 vCenter의 가상 컴퓨터를 구성할 수 있습니다.

### <a name="prerequisites"></a>필수 조건

* 구성 된 VLAN 사용 하 여 분산된 포트 그룹

* 온-프레미스 또는 인터넷 기반 DNS 서버를 설정 하는 경로

* 가상 머신 템플릿 또는 가상 머신을 만들려면 ISO

다음 링크는 Linux 및 Windows에서 DHCP 및 DNS 서버 설정 지침을 제공 합니다.

### <a name="linux-based-dns-server-setup"></a>Linux 기반 DNS 서버 설정

Linux는 DNS 서버를 설정 하기 위한 다양 한 패키지를 제공 합니다.  오픈 소스 BIND DNS 서버를 설정 하는 것에 대 한 지침에 대 한 링크는 다음과 같습니다.

[예제 설정](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Windows 기반 설치

다음 Microsoft 문서는 Windows server DNS 서버 및 DHCP 서버로 설정 하는 방법에 설명 합니다.
<br>
[Windows Server DNS 서버로](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[DHCP 서버와 Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>네트워킹 구성 (선택 사항) 사용자 지정

CloudSimple 포털의 네트워크 페이지를 사용 하면 방화벽 테이블에 대 한 구성 및 가상 머신에 대 한 공용 IP 주소를 지정할 수 있습니다.

### <a name="allocate-public-ips"></a>공용 Ip를 할당 합니다.

1. 이동할 **네트워크 > 공용 IP** CloudSimple 포털에서 합니다.
2. 클릭 **공용 IP 할당**합니다.
3. IP 주소 항목을 식별 하는 이름을 입력 합니다.
4. 사설 클라우드의 위치를 선택 합니다.
5. 원하는 경우 유휴 시간 제한을 변경 하려면 슬라이더를 사용 합니다.
6. 공용 IP 주소를 할당 하려는 로컬 IP 주소를 입력 합니다.
7. 원하는 경우에 연결 된 DNS 이름을 입력 합니다.
8. **Done**을 클릭합니다.

    ![공용 IP](media/quick-create-pc-public-ip.png)

공용 IP 주소를 할당 하는 작업을 시작 합니다. 작업의 상태를 확인할 수 있습니다 합니다 **활동 > 작업** 페이지입니다. 할당이 완료 되 면 공용 Ip 페이지에 새 항목이 표시 됩니다.

이 IP 주소를 매핑할 수 해야 하는 가상 컴퓨터 위에 지정 된 로컬 주소를 사용 하 여 구성 해야 합니다. IP 주소를 구성 하는 절차는 가상 머신 운영 체제와 관련이 있습니다. 가상 머신 운영 체제에서 올바른 프로시저에 대 한 설명서를 참조 하세요.

#### <a name="example"></a>예

예를 들어, Ubuntu 16.04에 대 한 세부 정보 같습니다.

정적 메서드를 inet 주소 패밀리 구성을 파일에 추가할 ```/etc/network/interfaces```합니다. 주소, netmask 및 게이트웨이 값을 변경 합니다. 예를 들어 eth0 인터페이스, 내부 IP 주소 192.168.24.10, 192.168.24.1, 게이트웨이 주소 및 네트워크 마스크 255.255.255.0 사용 하는 것입니다. 

```interfaces``` 파일을 편집합니다.

```
sudo vi /etc/network/interfaces
```

다음 섹션에서는 업데이트 ```interfaces``` 파일입니다.

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

인터페이스를 사용 하지 않도록 설정 합니다.

```
sudo ifdown eth0
```

인터페이스를 다시 사용 하도록 설정 합니다.

```
sudo ifup eth0
```

기본적으로 인터넷에서 들어오는 모든 트래픽이 됩니다 **거부**합니다. 다른 포트를 열려는 싶다면 만듭니다는 [방화벽 테이블](https://docs.azure.cloudsimple.com/firewall/)합니다.

내부 IP 주소를 고정 IP 주소를 구성한 후 가상 머신 내에서 인터넷에 연결할 수 있는지를 확인 합니다.

```
ping 8.8.8.8
```

연결할 수 있는지 가상 컴퓨터를 인터넷에서 공용 IP 주소를 사용 하 여 확인 합니다.

가상 컴퓨터에서 모든 (iptable) 방화벽 규칙은 포트 80을 차단 하지 않는지 확인 하십시오. 인바운드 합니다.

```
netstat -an | grep 80
```

포트 80에서 수신 하는 http 서버를 시작 합니다.
       
```
python2.7 -m SimpleHTTPServer 80
```

또는

```
python3 -m http.server 80
```

바탕 화면에 브라우저를 시작 하 고 가상 컴퓨터에 있는 파일을 찾으려면 공용 IP 주소의 80 포트를 가리킵니다. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>공용 IP에 대 한 기본 CloudSimple 방화벽 규칙

* VPN 트래픽이: (를) VPN 및 모든 워크 로드 네트워크 및 관리 네트워크 간의 모든 트래픽이 허용 됩니다.
* 내부 트래픽 사설 클라우드: (를) 워크 로드 네트워크 및 관리 네트워크 (위에 표시 됨) 간의 모든 동-서 트래픽이 허용 됩니다.
* 인터넷 트래픽:
    * 워크 로드 네트워크 및 관리 네트워크에 인터넷에서 들어오는 모든 트래픽을 거부 됩니다.
    * 워크 로드 네트워크 또는 관리 네트워크에서 인터넷으로 나가는 모든 트래픽이 허용 됩니다.

또한 방화벽 규칙 기능을 사용 하 여 트래픽이 보호 하는 방법은 수정할 수 있습니다. 자세한 내용은 [방화벽 테이블 및 규칙 설정](https://docs.azure.cloudsimple.com/firewall/)합니다.

## <a name="install-solutions-optional"></a>(선택 사항) 솔루션 설치
활용 하기 위해 전체 사설 클라우드 vCenter 환경을 CloudSimple 사설 클라우드 솔루션을 설치할 수 있습니다. 가상 컴퓨터를 보호 하기 위해 백업, 재해 복구, 복제 및 다른 함수를 설정할 수 있습니다. VMware 사이트 복구 관리자 (VMware SRM) 및 Veeam Backup 및 복제를 예로 들 수 있습니다.

솔루션을 설치 하려면 제한 된 기간에 대 한 추가 권한을 요청 해야 합니다. 참조 [권한 상승을](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges)합니다.

## <a name="next-steps"></a>다음 단계

* [Azure의 VMware 가상 컴퓨터를 사용 합니다.](quickstart-create-vmware-virtual-machine.md)
* [Azure ExpressRoute를 사용 하 여 온-프레미스 네트워크에 연결](https://docs.azure.cloudsimple.com/on-premises-connection)
* [CloudSimple 네트워크에서 VPN gateway 설정](https://docs.azure.cloudsimple.com/vpn-gateway)
