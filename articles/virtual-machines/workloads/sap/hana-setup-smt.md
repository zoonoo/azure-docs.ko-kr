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
ms.openlocfilehash: 690f41e941f2d1db8fc92d225a54d07570299222
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313138"
---
# <a name="set-up-smt-server-for-suse-linux"></a>SUSE Linux용 SMT 서버 설정
SAP HANA 대규모 인스턴스는 인터넷에 직접 연결되지 않습니다. 이러한 유닛을 운영 체제 공급자에 등록하고 업데이트를 다운로드하여 적용하는 것은 간단한 프로세스가 아닙니다. SUSE Linux용 솔루션은 Azure 가상 머신에 SMT 서버를 설정하는 것입니다. HANA 큰 인스턴스에 연결되는 Azure 가상 네트워크에서 가상 머신을 호스팅합니다. 이러한 SMT 서버를 사용하면 HANA 큰 인스턴스 유닛이 업데이트를 등록하고 다운로드할 수 있습니다. 

SUSE에 대한 자세한 설명서는 [SLES 12 SP2용 구독 관리 도구](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf)를 참조하세요. 

HANA 큰 인스턴스에 대한 작업을 수행하는 SMT 서버를 설치하는 데 필수 구성 요소는 다음과 같습니다.

- HANA 큰 인스턴스 ExpressRoute 회로에 연결된 Azure 가상 네트워크.
- 조직과 연결된 SUSE 계정. 조직에는 유효한 SUSE 구독이 있어야 합니다.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Azure 가상 머신에 SMT 서버 설치

먼저 [SUSE 고객 센터](https://scc.suse.com/)에 로그인합니다.

**조직** > **조직 자격 증명**으로 이동합니다. 해당 섹션에서 SMT 서버를 설치하는 데 필요한 자격 증명을 찾아야 합니다.

그런 다음, Azure 가상 네트워크에 SUSE Linux VM을 설치합니다. 가상 머신을 배포하려면 Azure의 SLES 12 SP2 갤러리 이미지를 사용합니다(BYOS SUSE 이미지 선택). 배포 과정에서 DNS 이름을 정의하지 않고 고정 IP 주소를 사용하지 않습니다.

![SMT 서버에 대한 가상 머신 배포의 스크린샷](./media/hana-installation/image3_vm_deployment.png)

배포된 가상 머신은 더 작으며, Azure 가상 네트워크에서 내부 IP 주소(10.34.1.4)를 가져왔습니다. 가상 머신 이름은 *smtserver*입니다. 설치 후 HANA 큰 인스턴스 유닛 또는 여러 유닛에 대한 연결이 확인되었습니다. 이름 확인을 구성하는 방법에 따라 Azure 가상 머신의 etc/hosts에 HANA 큰 인스턴스 유닛의 확인을 구성해야 할 수 있습니다. 

가상 머신에 디스크를 추가합니다. 이 디스크를 사용하여 업데이트를 저장하면 부팅 디스크 자체가 너무 작을 수 있습니다. 여기서, 다음 스크린샷처럼 디스크가 /srv/www/htdocs에 탑재되었습니다. 100GB 디스크로 충분합니다.

![SMT 서버에 대한 가상 머신 배포의 스크린샷](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

HANA 큰 인스턴스 유닛 또는 여러 유닛에 로그인하고, /etc/hosts를 유지 관리하고, 네트워크에서 SMT 서버를 실행해야 하는 Azure 가상 머신에 연결할 수 있는지 확인합니다.

이를 확인한 뒤 SMT 서버를 실행해야 하는 Azure 가상 머신에 로그인합니다. putty를 사용하여 가상 머신에 로그인하는 경우 Bash 창에서 다음 명령을 순서대로 실행합니다.

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Bash를 다시 시작하여 설정을 활성화합니다. 그런 다음 YAST를 시작합니다.

SUSE 사이트에 VM(smtserver)을 연결합니다.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

가상 머신이 SUSE 사이트에 연결되면 SMT 패키지를 설치합니다. SMT 패키지를 설치하려면 다음 putty 명령을 사용합니다.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


SMT 패키지를 설치하려면 YAST 도구를 사용할 수도 있습니다. YAST에서 **소프트웨어 유지 관리**로 이동하여 SMT를 검색합니다. **SMT**를 선택하면 자동으로 yast2-smt로 전환됩니다.

![YAST의 SMT 스크린샷](./media/hana-installation/image5_smt_in_yast.PNG)


smtserver에 설치를 위한 선택 사항을 수락합니다. 설치가 완료되면 SMT 서버 구성으로 이동합니다. 앞에서 검색한 SUSE 고객 센터의 조직 자격 증명을 입력합니다. 또한 Azure 가상 머신 호스트 이름을 SMT 서버 URL로 입력합니다. 이 데모에서는 https의:\//smtserver 합니다.

![SMT 서버 구성의 스크린샷](./media/hana-installation/image6_configuration_of_smtserver1.png)

이제 SUSE 고객 센터에 대한 연결이 작동하는지 여부를 테스트합니다. 다음 스크린샷에서 표시된 것처럼 이 데모의 경우 연결이 작동되었습니다.

![SUSE 고객 센터에 대한 연결을 테스트하는 스크린샷](./media/hana-installation/image7_test_connect.png)

SMT 설정이 시작되면 데이터베이스 암호를 제공합니다. 새로 설치하는 것이므로 다음 스크린샷에 표시된 대로 해당 암호를 정의해야 합니다.

![데이터베이스에 대한 암호를 정의하는 스크린샷](./media/hana-installation/image8_define_db_passwd.PNG)

다음 단계에서는 자격 증명을 만듭니다.

![SMT 서버에 대한 인증서를 만드는 스크린샷](./media/hana-installation/image9_certificate_creation.PNG)

구성의 끝에 동기화 확인을 실행하려면 몇 분 정도 걸릴 수 있습니다. SMT 서버를 설치하고 구성한 후에 탑재 지점인 /srv/www/htdocs/ 아래에 디렉터리 리포지토리가 있어야 합니다. 또한 리포지토리 아래 하위 디렉터리도 있습니다. 

다음 명령으로 SMT 서버 및 관련 서비스를 다시 시작합니다.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>SMT 서버에 패키지 다운로드

모든 서비스가 다시 시작된 후 YAST를 사용하여 SMT Management에서 적절한 패키지를 선택합니다. 패키지 선택은 HANA 큰 인스턴스 서버의 운영 체제 이미지에 따라 달라집니다. 패키지 선택은 SLES 릴리스 또는 SMT 서버를 실행하는 가상 머신 버전에 따라 달라지지 않습니다. 다음 스크린샷은 선택 화면에 대한 예를 보여줍니다.

![패키지를 선택하는 스크린샷](./media/hana-installation/image10_select_packages.PNG)

다음으로, 설정한 SMT 서버에서 선택한 패키지의 초기 복사본을 시작합니다. 이 복사는 smt-mirror 명령을 사용하여 셸에서 트리거됩니다.

![SMT 서버에 패키지를 다운로드하는 스크린샷](./media/hana-installation/image11_download_packages.PNG)

패키지는 탑재 지점 /srv/www/htdocs 아래 생성된 디렉터리에 복사되어야 합니다. 이 프로세스는 선택하는 패키지 수에 따라 한 시간 이상 걸릴 수 있습니다. 이 프로세스를 마치면 SMT 클라이언트 설정으로 이동합니다. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>HANA 큰 인스턴스 유닛에 SMT 클라이언트 설정

이 경우 클라이언트는 HANA 큰 인스턴스 유닛입니다. SMT 서버 설정이 clientSetup4SMT.sh 스크립트를 Azure 가상 머신에 복사했습니다. 이 스크립트를 SMT 서버에 연결할 HANA 큰 인스턴스 유닛으로 복사합니다. -h 옵션을 사용하여 스크립트를 시작하고 매개 변수로 SMT 서버의 이름을 지정합니다. 이 예제에서 해당 이름은 *smtserver*입니다.

![SMT 클라이언트를 구성하는 스크린샷](./media/hana-installation/image12_configure_client.PNG)

다음 스크린샷에 표시된 것처럼 클라이언트를 통해 서버에서 인증서 로드가 성공하지만 등록이 실패할 수 있습니다.

![클라이언트 등록 실패의 스크린샷](./media/hana-installation/image13_registration_failed.PNG)

등록이 실패하면 [SUSE 지원 문서](https://www.suse.com/de-de/support/kb/doc/?id=7006024)를 참조하여 문서에 설명된 단계를 실행합니다.

> [!IMPORTANT] 
> 서버 이름의 경우 정규화된 도메인 이름은 사용하지 않고 가상 머신 이름을 제공합니다(이 경우 *smtserver*입니다). 

이러한 단계를 실행한 후 HANA 큰 인스턴스 유닛에서 다음 명령을 실행합니다.

```
SUSEConnect –cleanup
```

> [!Note] 
> 해당 단계를 수행한 후 몇 분 정도 기다립니다. clientSetup4SMT.sh를 즉시 실행하는 경우 오류가 발생할 수 있습니다.

SUSE 문서의 단계에 따라 수정해야 하는 문제가 발생하면 HANA 큰 인스턴스 유닛에서 clientSetup4SMT.sh를 다시 시작합니다. 이제 성공적으로 완료됩니다.

![클라이언트 등록 성공의 스크린샷](./media/hana-installation/image14_finish_client_config.PNG)

HANA 큰 인스턴스 유닛의 SMT 클라이언트를 구성하여 Azure 가상 머신에 설치된 SMT 서버에 연결했습니다. 이제 'zypper up' 또는 'zypper in'을 사용하여 HANA 큰 인스턴스에 운영 체제 업데이트를 설치하거나 추가 패키지를 설치할 수 있습니다. 전에 SMT 서버에 다운로드한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [HLI에 HANA 설치](hana-example-installation.md).











