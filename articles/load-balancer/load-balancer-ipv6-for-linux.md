<properties
    pageTitle="Linux VM에 대한 DHCPv6 구성 | Microsoft Azure"
    description="Linux VM에 대한 DHCPv6를 구성하는 방법"
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="ipv6, Azure Load Balancer, 이중 스택, 공용 IP, 기본 ipv6, 모바일, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>


# <a name="configuring-dhcpv6-for-linux-vms"></a>Linux VM에 대한 DHCPv6 구성

Azure Marketplace의 Linux 가상 컴퓨터 이미지 중 일부에는 기본적으로 구성된 DHCPv6가 없습니다. IPv6를 지원하려면 DHCPv6가 사용 중인 Linux OS 배포 내에 구성되어야 합니다. 다른 Linux 배포는 다른 패키지를 사용하기 때문에 DHCPv6는 다른 방법으로 구성됩니다.

>[AZURE.NOTE] Azure Marketplace의 최근 SUSE Linux 및 CoreOS 이미지는 DHCPv6를 사용해 미리 구성되었습니다. 이러한 이미지를 사용할 경우 추가 변경이 필요하지 않습니다.

이 문서에서는 Linux 가상 컴퓨터가 IPv6 주소를 확보할 수 있게 DHCPv6를 사용하도록 설정하는 방법을 설명합니다.

>[AZURE.WARNING] 네트워크 구성 파일을 부적절하게 편집한다면 VM에 대한 네트워크 액세스를 하지 못하게 될 수도 있습니다. 구성 변경 테스트는 비프로덕션 시스템에서 하는 것이 좋습니다. 이 문서의 지침에서는 Linux 이미지는 Azure Marketplace의 최신 버전에서 테스트 되었습니다. 더 자세한 지침은 특정 버전의 Linux 설명서를 참조하세요.

## <a name="ubuntu"></a>Ubuntu

1. 파일 `/etc/dhcp/dhclient6.conf` 을 편집하고 다음 줄을 추가합니다.

        timeout 10;

2. 다음 구성을 사용하여 eth0 인터페이스에 대한 네트워크 구성을 편집합니다.

    * **Ubuntu 12.04 and 14.04**에서 `/etc/network/interfaces.d/eth0.cfg` 파일을 편집합니다.
    * **Ubuntu 16.04**에서 `/etc/network/interfaces.d/50-cloud-init.cfg` 파일을 편집합니다.

    ```bash
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. IPv6 주소를 갱신합니다.

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. 파일 `/etc/dhcp/dhclient6.conf` 을 편집하고 다음 줄을 추가합니다.

        timeout 10;

2. 파일 `/etc/network/interfaces` 을 편집하고 다음 구성을 추가합니다.

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. IPv6 주소를 갱신합니다.

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-/-centos-/-oracle-linux"></a>RHEL / CentOS / Oracle Linux

1. 파일 `/etc/sysconfig/network` 을 편집하고 다음 매개 변수를 추가합니다.

        NETWORKING_IPV6=yes

2. 파일 `/etc/sysconfig/network-scripts/ifcfg-eth0` 을 편집하고 다음 두 매개 변수를 추가합니다.

        IPV6INIT=yes
        DHCPV6C=yes

3. IPv6 주소를 갱신합니다.

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-&-opensuse-13"></a>SLES 11 및 openSUSE 13

Azure의 최근 SLES 및 openSUSE 이미지는 DHCPv6를 사용해 미리 구성되었습니다. 이러한 이미지를 사용할 경우 추가 변경이 필요하지 않습니다. 이전 또는 사용자 지정 SUSE 이미지를 기반으로 하는 VM인 경우 다음 단계를 사용합니다.

1. 필요하다면 `dhcp-client` 패키지를 설치합니다.

    ```bash
    # sudo zypper install dhcp-client
    ```

2. 파일 `/etc/sysconfig/network/ifcfg-eth0` 을 편집하고 다음 매개 변수를 추가합니다.

        DHCLIENT6_MODE='managed'

3. IPv6 주소를 갱신합니다.

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 및 openSUSE Leap

Azure의 최근 SLES 및 openSUSE 이미지는 DHCPv6를 사용해 미리 구성되었습니다. 이러한 이미지를 사용할 경우 추가 변경이 필요하지 않습니다. 이전 또는 사용자 지정 SUSE 이미지를 기반으로 하는 VM인 경우 다음 단계를 사용합니다.

1. 파일 `/etc/sysconfig/network/ifcfg-eth0` 을 편집하고 이 매개 변수를 교체합니다.

        #BOOTPROTO='dhcp4'

    다음 값으로.

        BOOTPROTO='dhcp'

2. 다음 매개 변수를 `/etc/sysconfig/network/ifcfg-eth0`에 추가합니다.

        DHCLIENT6_MODE='managed'

3. IPv6 주소를 갱신합니다.

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Azure의 최근 CoreOS 이미지는 DHCPv6를 사용해 미리 구성되었습니다. 이러한 이미지를 사용할 경우 추가 변경이 필요하지 않습니다. 이전 또는 사용자 지정 CoreOS 이미지를 기반으로 하는 VM인 경우 다음 단계를 사용합니다.

1. 파일 `/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. IPv6 주소를 갱신합니다.

    ```bash
    # sudo systemctl restart systemd-networkd
    ```



<!--HONumber=Oct16_HO2-->


