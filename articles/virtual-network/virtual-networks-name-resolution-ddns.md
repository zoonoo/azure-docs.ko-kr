<properties 
   pageTitle="동적 DNS를 사용하여 호스트 이름 등록"
   description="이 페이지에서는 고유의 DNS 서버에서 호스트 이름을 등록하기 위해 동적 DNS를 설치하는 방법에 대한 몇 가지 세부 정보를 제공합니다."
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# 동적 DNS를 사용하여 호스트 이름 등록
VM 및 역할 인스턴스에 대해 [Azure가 이름 확인을 제공](virtual-networks-name-resolution-for-vms-and-role-instances.md)합니다. 이름 확인을 위해 Azure가 제공하는 기능 이상이 필요한 경우 자체 DNS 서버를 제공할 수 있습니다. 그러면 특정한 자체 요구에 맞게 DNS 솔루션을 맞춤식 구성을 할 수 있게 됩니다. 예를 들어 온-프레미스 AD 도메인 컨트롤러에 액세스합니다.

Azure에는 DNS 서버에 레코드를 직접 등록하는 기능(예: 자격 증명)이 없으므로 종종 대체 정렬이 필요합니다. 다음에서는 좀 더 일반적인 몇 가지 시나리오에 대한 상위 수준의 세부 정보를 제공합니다.

## Windows 클라이언트 ##
비 도메인에 연결된 Windows 클라이언트는 부팅하거나 해당 IP 주소가 변경될 때 보안되지 않은 DDNS 업데이트를 시도합니다. DNS 이름은 호스트 이름과 주 DNS 접미사입니다. Azure는 주 DNS 접미사를 비워 두지만 [UI](https://technet.microsoft.com/library/cc794784.aspx)를 통하거나 [자동으로](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix) VM에서 재정의될 수 있습니다.

도메인에 연결된 Windows 클라이언트는 보안 DDNS를 사용하여 해당 IP를 도메인 컨트롤러에 등록합니다. 도메인 연결 프로세스는 클라이언트에 대한 주 DNS 접미사를 설정하고 트러스트 관계를 관리합니다.

## Linux 클라이언트 ##
Linux 클라이언트는 시작할 때 DNS 서버를 사용하여 일반적으로 등록하지 않고 DHCP 서버가 수행한다고 가정합니다. 바인드 패키지가 DDNS 업데이트를 보내는 데 사용할 수 있는 *nsupdate*라는 도구와 함께 제공됩니다. DDNS 프로토콜이 표준화되어 있으므로 DNS 서버에서 바인드를 사용하지 않을 때에도 *nsupdate*를 사용할 수 있습니다.

Linux DHCP 클라이언트는 IP 주소가 할당되거나 변경될 때 스크립트를 실행할 수 있도록 하는 후크를 제공합니다. 예를 들어 */etc/dhcp/dhclient-exit-hooks.d/*에 있습니다. 이는 DNS의 호스트 이름을 등록하는 데 사용할 수 있습니다. 예:
    
    	#!/bin/sh
    	requireddomain=mydomain.local
    
    	# only execute on the primary nic
    	if [ "$interface" != "eth0" ]
    	then
    		return
    	fi
    
		# when we have a new IP, perform nsupdate
		if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] || 
		   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
		then
    		host=`hostname`
	    	nsupdatecmds=/var/tmp/nsupdatecmds
  			echo "update delete $host.$requireddomain a" > $nsupdatecmds
  			echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
  			echo "send" >> $nsupdatecmds

  			nsupdate $nsupdatecmds
		fi

		#done
		exit 0;

*nsupdate* 명령은 보안 DDNS 업데이트를 수행할 수도 있습니다. 예를 들어 바인드 DNS 서버를 사용하는 경우 공개-개인 키 쌍이 [생성되고](http://linux.yyz.us/nsupdate/) DNS 서버가 키의 공용 부분으로 [구성되어](http://linux.yyz.us/dns/ddns-server.html) 있으므로 요청의 서명을 확인할 수 있습니다. DDNS 업데이트 요청을 서명하려면 *nsupdate*에 *-k* 옵션으로 키 쌍을 제공해야 합니다.

Windows DNS 서버를 사용하는 경우 Kerberos 인증은 nsupdate의 *-g* 스위치와 함께 사용할 수 있습니다(Windows 버전의 *nsupdate*에서 사용할 수 없음). 이를 사용하려면 *kinit*를 사용하여 [keytab 파일](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html) 등에서 자격 증명을 로드하면 *nsupdate -g*가 캐시에서 자격 증명을 선택합니다.

필요한 경우 DNS 검색 접미사를 VM에 추가할 수 있습니다. DNS 접미사는 */etc/resolv.conf* 파일에 지정됩니다. 대부분의 Linux 배포판은 자동으로 이 파일의 콘텐츠를 관리하므로 일반적으로 편집할 수 없습니다. 그러나 DHCP 클라이언트의 *supersede* 명령을 사용하여 재정의할 수 있습니다. */etc/dhcp/dhclient.conf*에 다음을 추가합니다.

		supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0204_2016-->