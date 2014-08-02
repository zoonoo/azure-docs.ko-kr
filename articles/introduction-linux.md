<properties linkid="manage-linux-fundamentals-intro-to-linux" urlDisplayName="Intro to Linux" pageTitle="Introduction to Linux in Azure - Azure Tutorial" metaKeywords="Azure Linux vm, Linux vm" description="Learn about using Linux virtual machines on Azure." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Introduction to Linux on Azure" authors="" solutions="" manager="" editor="" />

Azure의 Linux 소개
==================

이 항목에서는 Azure 클라우드에서 Linux 가상 컴퓨터를 사용하는 몇 가지 측면을 간략하게 설명합니다. 갤러리의 기존 이미지를 사용할 경우 Linux 가상 컴퓨터 배포는 간단한 프로세스입니다.

목차
----

-   [인증: 사용자 이름, 암호 및 SSH 키](#authentication)
-   [Linux 가상 컴퓨터 로그인용 SSH 키 생성 및 사용](#keygeneration)
-   [sudo를 사용하여 superuser 권한 얻기](#superuserprivileges)
-   [방화벽 구성](#firewallconfiguration)
-   [호스트 이름 변경](#hostnamechanges)
-   [가상 컴퓨터 이미지 캡처](#virtualmachine)
-   [디스크 연결](#attachingdisks)

인증: 사용자 이름, 암호 및 SSH 키
---------------------------------

Azure 관리 포털에서 Linux 가상 컴퓨터를 만들 경우 사용자 이름, 암호 및 SSH 공개 키(선택적으로)를 지정해야 합니다. Azure에서 Linux 가상 컴퓨터를 배포하기 위해 사용자 이름을 선택할 경우 제약 조건이 적용됩니다. 즉, 가상 컴퓨터에 이미 존재하는 시스템 계정의 이름(예: 루트)은 사용할 수 없습니다. SSH 공개 키를 지정하지 않은 경우 지정된 사용자 이름 및 암호를 사용하여 가상 컴퓨터에 로그인할 수 있습니다. 관리 포털에서 SSH 공개 키를 업로드하기로 선택한 경우 암호 기반 인증을 사용할 수 없으며 해당 SSH 개인 키를 사용하여 가상 컴퓨터에 인증하고 로그인해야 합니다.

SSH 키 생성
-----------

현재 버전의 관리 포털은 X509 인증서에 캡슐화된 SSH 공개 키만 허용합니다. SSH 키를 생성하고 Azure에 사용하려면 아래 단계를 따르십시오.

1.  openssl을 사용하여 2048비트 RSA 키 쌍으로 X509 인증서를 생성합니다.

         openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

    openssl에서 확인하는 몇 가지 질문에 대답합니다(비워 둘 수도 있음). 이러한 필드의 콘텐츠는 플랫폼에서 사용되지 않습니다.

2.  개인 키의 사용 권한을 변경하여 보안을 설정합니다.

         chmod 600 myPrivateKey.key

3.  myCert.pem을 myCert.cer(DER 인코딩된 X509 인증서)로 변환합니다.

         openssl  x509 -outform der -in myCert.pem -out myCert.cer

4.  Linux 가상 컴퓨터를 만드는 동안 myCert.cer을 업로드합니다. 프로비저닝 프로세스에서 가상 컴퓨터의 지정된 사용자에 대한 authorized\_keys 파일에 이 인증서의 공개 키를 자동으로 설치합니다.

5.  ssh를 사용하여 Linux 가상 컴퓨터에 연결합니다.

         ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

    처음 로그인할 때 호스트의 공개 키 지문을 허용하라는 메시지가 표시됩니다.

6.  선택적으로 myPrivateKey.key를 \~/.ssh/id\_rsa로 복사할 수 있습니다. 그러면 -i 옵션을 사용하지 않아도 openssh 클라이언트가 이 파일을 자동으로 선택할 수 있습니다.

Sudo를 사용하여 Superuser 권한 얻기
-----------------------------------

Azure에서 가상 컴퓨터 인스턴스를 배포하는 동안 지정한 사용자 계정이 권한 있는 계정입니다. Azure Linux 에이전트에서 sudo 도구를 사용하여 루트(superuser 계정)로 권한을 상승하도록 이 계정을 구성할 수 있습니다. 이 사용자 계정을 사용하여 로그인한 후 "sudo 명령"을 사용하여 루트로 명령을 실행할 수 있습니다. 선택적으로 **sudo -s**를 사용하여 루트 셸을 얻을 수 있습니다.

방화벽 구성
-----------

Azure는 관리 포털에서 지정한 포트에 대한 연결을 제한하는 인바운드 패킷 필터를 제공합니다. 기본적으로 허용되는 유일한 포트는 SSH입니다. 관리 포털에서 규칙을 추가하여 Linux 가상 컴퓨터의 추가 포트에 액세스할 수 있습니다.

갤러리의 Linux 이미지는 Linux 가상 컴퓨터에서 iptables 방화벽을 사용하도록 설정할 수 없습니다. 원하는 경우 추가 기능을 제공하도록 IPtables 방화벽을 구성할 수 있습니다.

호스트 이름 변경
----------------

Linux 이미지의 인스턴스를 처음 배포할 때 가상 컴퓨터의 호스트 이름을 지정해야 합니다. 가상 컴퓨터를 실행하면 이 호스트 이름이 플랫폼 DNS 서버에 게시되므로 서로 연결된 여러 가상 컴퓨터에서 호스트 이름을 사용하여 IP 주소를 조회할 수 있습니다. 가상 컴퓨터를 배포한 후 호스트 이름을 변경해야 하는 경우 **hostname newname** 명령을 사용하십시오. Azure Linux 에이전트에는 이 이름 변경을 자동으로 검색하여 이 변경 내용을 기억하도록 가상 컴퓨터를 적절히 구성하고 플랫폼 DNS 서버에 이 변경 내용을 게시하는 기능이 포함되어 있습니다. 이 기능과 관련된 추가 세부 정보 및 구성 옵션은 Azure Linux 에이전트의 추가 정보 파일을 참조하십시오.

가상 컴퓨터 이미지 캡처
-----------------------

Azure는 기존 가상 컴퓨터의 상태를 이미지로 캡처하는 기능을 제공합니다. 이 이미지는 이후에 추가 가상 컴퓨터 인스턴스를 배포하는 데 사용할 수 있습니다. Azure Linux 에이전트는 프로비전 프로세스 중 수행된 일부 사용자 지정을 롤백하는 데 사용할 수 있습니다. 아래 단계를 따르면 가상 컴퓨터를 이미지로 캡처할 수 있습니다.

1.  **waagent -deprovision**을 실행하여 사용자 지정 프로비전을 실행 취소합니다. 또는 선택적으로 프로비전 중 지정한 사용자 계정 및 연결된 모든 데이터를 삭제하려면 **waagent -deprovision+user**를 실행합니다.

2.  관리 포털/도구를 사용하여 가상 컴퓨터를 종료합니다.

3.  관리 포털에서 캡처를 클릭하거나 가상 컴퓨터를 이미지로 캡처하는 도구를 사용합니다.

디스크 연결
-----------

이 섹션에서는 [Linux 가상 컴퓨터를 만드는](../virtual-machine-from-gallery/) 방법에 대한 자습서의 단계를 다루었습니다.

