---
title: Azure의 SAP HANA(대규모 인스턴스)에 SMT 서버를 설정하는 방법 | Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)에 SMT 서버를 설정하는 방법입니다.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f387c1afe88f2bba476309b2e2e01942d2b7ae5b
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982628"
---
# <a name="setting-up-smt-server-for-suse-linux"></a>SUSE Linux용 SMT 서버 설정
SAP HANA 큰 인스턴스는 인터넷에 직접 연결되지 않습니다. 따라서 이러한 유닛을 OS 제공자에 등록하고 패치를 다운로드하여 적용하는 것은 간단한 프로세스가 아닙니다. SUSE Linux의 경우 Azure VM에 SMT 서버를 설정하는 것이 하나의 솔루션이 될 수 있습니다. 반면에 Azure VM은 HANA 큰 인스턴스에 연결된 Azure VNet에서 호스트되어야 합니다. 이러한 SMT 서버를 사용하면 HANA Large Instance 유닛이 패치를 등록하고 다운로드할 수 있습니다. 

SUSE의 [Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf)(SLES 12 SP2용 구독 관리 도구) 사이트에 자세한 지침이 제공되어 있습니다. 

HANA Large Instance에 대한 작업을 수행하는 SMT 서버를 설치하기 위한 전제 조건은 다음과 같습니다.

- HANA Large Instance ER 회로에 연결된 Azure VNet.
- 조직과 연결된 SUSE 계정. 조직에는 유효한 SUSE 구독이 있어야 합니다.

## <a name="installation-of-smt-server-on-azure-vm"></a>Azure VM에 SMT 서버 설치

이 단계에서는 Azure VM에 SMT 서버를 설치합니다. 첫 번째 조치는 [SUSE 고객 센터](https://scc.suse.com/)에 로그인하는 것입니다.

로그인한 상태에서 Organization(조직)--> Organization Credentials(조직 자격 증명)로 이동합니다. 이 섹션에서 SMT 서버를 설정하는 데 필요한 자격 증명을 찾아야 합니다.

세 번째 단계는 Azure VNet에 SUSE Linux VM을 설치하는 것입니다. VM을 배포하려면 Azure의 SLES 12 SP2 갤러리 이미지를 사용합니다(BYOS SUSE 이미지 선택). 아래 스크린샷에 표시된 대로 배포 과정에서 DNS 이름을 정의하지 않고 고정 IP 주소를 사용하지 마십시오.

![SMT 서버용 VM 배포](./media/hana-installation/image3_vm_deployment.png)

배포된 VM은 더 작은 VM이고 Azure VNet에서 내부 IP 주소인 10.34.1.4를 가져왔습니다. VM의 이름은 smtserver였습니다. 설치 후 HANA 큰 인스턴스 유닛에 대한 연결이 확인되었습니다. 이름 확인을 구성하는 방식에 따라 Azure VM의 etc/hosts에 HANA 큰 인스턴스 유닛의 확인을 구성해야 할 수 있습니다. 패치를 보관하는 데 사용할 VM에 디스크를 더 추가합니다. 부팅 디스크 자체가 너무 작을 수 있습니다. 데모의 경우 다음 스크린샷처럼 디스크가 /srv/www/htdocs에 탑재되었습니다. 100GB 디스크로 충분합니다.

![SMT 서버용 VM 배포](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

HANA 대규모 인스턴스 단위에 로그인하고, /etc/hosts를 유지 관리하고, 네트워크를 통해 SMT 서버를 실행해야 하는 Azure VM에 연결할 수 있는지 확인합니다.

이 확인이 성공적으로 완료되면 SMT 서버를 실행할 Azure VM에 로그인해야 합니다. putty를 사용하여 VM에 로그인하는 경우 bash 창에서 다음 명령을 순서대로 실행해야 합니다.

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

이 명령을 실행한 후 bash를 다시 시작하여 설정을 활성화합니다. 그런 다음 YAST를 시작합니다.

SUSE 사이트에 VM(smtserver)을 연결합니다.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

VM이 SUSE 사이트에 연결되면 SMT 패키지를 설치합니다. SMT 패키지를 설치하려면 다음 putty 명령을 사용합니다.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


SMT 패키지를 설치하려면 YAST 도구를 사용할 수도 있습니다. YAST에서 Software Maintenance(소프트웨어 유지 관리)로 이동하여 smt를 검색합니다. smt를 선택하면 yast2-smt로 자동으로 전환됩니다(아래 그림 참조).

![yast의 SMT](./media/hana-installation/image5_smt_in_yast.PNG)


smtserver에 설치를 위한 선택 사항을 수락합니다. 설치가 완료되면 SMT 서버 구성으로 이동하여 앞에서 검색한 SUSE Customer Center(SUSE 고객 센터)의 조직 자격 증명을 입력합니다. Azure VM 호스트 이름을 SMT 서버 URL로 입력합니다. 이 데모에서는 https://smtserver입니다(다음 그래픽 참조).

![SMT 서버 구성](./media/hana-installation/image6_configuration_of_smtserver1.png)

다음 단계로, SUSE Customer Center(SUSE 고객 센터)에 대한 연결이 작동하는지 테스트해야 합니다. 다음 그래픽에서 볼 수 있듯이 데모에서는 작동이 되었습니다.

![SUSE Customer Center(SUSE 고객 센터)에 대한 연결 테스트](./media/hana-installation/image7_test_connect.png)

SMT 설정이 시작되면 데이터베이스 암호를 제공해야 합니다. 새로운 설치이므로 다음 그래픽과 같이 암호를 정의해야 합니다.

![데이터베이스에 대한 암호 정의](./media/hana-installation/image8_define_db_passwd.PNG)

다음 인터랙션은 인증서가 만들어 질 때 수행됩니다. 다음과 같이 대화 상자를 통해 단계를 진행합니다.

![SMT 서버의 인증서 만들기](./media/hana-installation/image9_certificate_creation.PNG)

구성 끝 부분의 'Run synchronization check'(동기화 확인 실행) 단계에서 몇 분 정도가 소요될 수 있습니다. SMT 서버를 설치하고 구성한 후에 탑재 지점인 /srv/www/htdocs/ 아래에 디렉터리 리포지토리가 있고, 리포지토리 아래에 하위 디렉터리가 있어야 합니다. 

다음 명령으로 SMT 서버 및 관련 서비스를 다시 시작합니다.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-of-packages-onto-smt-server"></a>SMT 서버에 패키지 다운로드

모든 서비스가 다시 시작된 후 Yast를 사용하여 SMT Management에서 적절한 패키지를 선택합니다. 패키지 선택은 SMT 서버를 실행하는 SLES 릴리스 또는 VM 버전이 아니라 HANA 큰 인스턴스 서버의 OS 이미지에 따라 달라집니다. 선택 화면의 예는 다음과 같습니다.

![패키지 선택](./media/hana-installation/image10_select_packages.PNG)

패키지 선택이 끝나면 설정한 SMT 서버에 선택한 패키지의 초기 복사를 시작해야 합니다. 이 복사는 smt-mirror 명령을 사용하여 셸에서 트리거됩니다(아래 그림 참조).


![SMT 서버에 패키지 다운로드](./media/hana-installation/image11_download_packages.PNG)

위에서 볼 수 있듯이 패키지는 탑재 지점 /srv/www/htdocs 아래 생성된 디렉터리에 복사되어야 합니다. 이 프로세스는 시간이 어느 정도 걸릴 수 있습니다. 선택한 패키지 수에 따라 최대 1시간 이상이 걸릴 수 있습니다.
이 프로세스를 마치면 SMT 클라이언트 설정으로 이동해야 합니다. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>HANA 큰 인스턴스 유닛에 SMT 클라이언트 설정

이 경우 클라이언트는 HANA 큰 인스턴스 유닛입니다. SMT 서버 설정이 clientSetup4SMT.sh 스크립트를 Azure VM에 복사했습니다. 이 스크립트를 SMT 서버에 연결할 HANA 큰 인스턴스 유닛으로 복사합니다. -h 옵션을 사용하여 스크립트를 시작하고 매개 변수로 SMT 서버의 이름을 입력합니다. 이 예제에서는 smtserver입니다.

![SMT 클라이언트 구성](./media/hana-installation/image12_configure_client.PNG)

클라이언트에 의해 서버에서 인증서 로드가 성공했지만 등록이 실패하는 시나리오가 있을 수 있습니다(아래 참조).

![클라이언트 등록 실패](./media/hana-installation/image13_registration_failed.PNG)

등록이 실패하면 [ SUSE 지원 문서](https://www.suse.com/de-de/support/kb/doc/?id=7006024)를 읽고 문서에 설명된 단계를 실행합니다.

> [!IMPORTANT] 
> 서버 이름으로 VM 이름을 제공해야 합니다. 이 경우 smtserver이며 정규화된 도메인 이름은 사용하지 않습니다. VM 이름만 작동합니다. 

이 단계를 실행한 후에는 HANA 큰 인스턴스 유닛에서 다음 명령을 실행해야 합니다

```
SUSEConnect –cleanup
```

> [!Note] 
> 지난 테스트에서는 해당 단계 후 항상 몇 분을 기다려야 했습니다. SUSE 문서에 설명된 시정 조치 후에 즉시 실행되는 clientSetup4SMT.sh는 인증서가 아직 유효하지 않다는 메시지로 종료되었습니다. 대개 5~10분을 기다려서 clientSetup4SMT.sh를 실행하면 클라이언트 구성이 성공적으로 끝납니다.

SUSE 문서의 단계를 기반으로 수정해야 하는 문제가 발생하면 HANA 큰 인스턴스 유닛에서 clientSetup4SMT.sh를 다시 시작해야 합니다. 이제 아래와 같이 성공적으로 완료됩니다.

![클라이언트 등록 성공](./media/hana-installation/image14_finish_client_config.PNG)

이 단계에서 Azure VM에 설치된 SMT 서버에 연결하기 위해 HANA 큰 인스턴스 유닛의 SMT 클라이언트를 구성했습니다. 이제 'zypper up' 또는 'zypper in'을 사용하여 HANA 큰 인스턴스에 OS 패치를 설치하거나 추가 패키지를 설치할 수 있습니다. 이전에 다운로드한 패치만 SMT 서버에서 가져올 수 있는 것으로 간주됩니다.

**다음 단계**
- [HLI에 HANA 설치](hana-example-installation.md)를 참조하세요.











