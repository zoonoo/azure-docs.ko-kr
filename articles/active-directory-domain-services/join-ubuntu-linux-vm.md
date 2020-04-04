---
title: 우분투 VM에 가입하여 Azure AD 도메인 서비스에 | 마이크로 소프트 문서
description: Azure AD 도메인 서비스 관리 도메인에 우분투 리눅스 가상 컴퓨터를 구성 하고 가입 하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 95373ab8ff78c5bcb856e6d7e6d67d8525cd3f7e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655128"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Azure AD 도메인 서비스 관리 도메인에 우분투 리눅스 가상 머신에 가입

사용자가 단일 자격 증명 집합을 사용하여 Azure의 VM(가상 시스템)에 로그인할 수 있도록 하려면 VM을 Azure Active Directory 도메인 서비스(AD DS) 관리 도메인에 조인할 수 있습니다. VM을 Azure AD DS 관리 도메인에 가입하면 도메인의 사용자 계정 및 자격 증명을 사용하여 서버에 로그인하고 관리할 수 있습니다. Azure AD DS 관리 도메인의 그룹 구성원 자격도 적용되어 VM의 파일 또는 서비스에 대한 액세스를 제어할 수 있습니다.

이 문서에서는 Azure AD DS 관리 도메인에 우분투 리눅스 VM을 조인하는 방법을 보여 주며 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure AD DS 관리 도메인의 일부인 사용자 계정입니다.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>만들고 우분투 리눅스 VM에 연결

Azure에 기존 우분투 리눅스 VM이 있는 경우 SSH를 사용하여 연결한 다음 다음 단계로 계속하여 [VM 구성을 시작합니다.](#configure-the-hosts-file)

우분투 리눅스 VM을 만들 필요가 있거나이 문서와 함께 사용할 테스트 VM을 만들려면 다음 방법 중 하나를 사용할 수 있습니다.

* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

VM을 만들 때 가상 네트워크 설정에 주의를 기울여 VM이 Azure AD DS 관리 도메인과 통신할 수 있는지 확인합니다.

* VM을 Azure AD 도메인 서비스를 사용하도록 설정한 동일한 피어식 가상 네트워크에 배포합니다.
* Azure AD 도메인 서비스 인스턴스와 다른 서브넷에 VM을 배포합니다.

VM이 배포되면 SSH를 사용하여 VM에 연결하는 단계를 따릅니다.

## <a name="configure-the-hosts-file"></a>hosts 파일 구성

관리되는 도메인에 대해 VM 호스트 이름이 올바르게 구성되었는지 확인하려면 */etc/hosts* 파일을 편집하고 호스트 이름을 설정합니다.

```console
sudo vi /etc/hosts
```

*호스트* 파일에서 로컬 *호스트* 주소를 업데이트합니다. 다음 예제에서,

* *aaddscontoso.com* Azure AD DS 관리 도메인의 DNS 도메인 이름입니다.
* *우분투는* 관리 되는 도메인에 가입 하는 우분투 VM의 호스트 이름입니다.

이러한 이름을 사용자 고유의 값으로 업데이트합니다.

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

작업이 완료되면 편집기의 *hosts* `:wq` 명령을 사용하여 호스트 파일을 저장하고 종료합니다.

## <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

VM은 Azure AD DS 관리 도메인에 VM을 조인하기 위해 몇 가지 추가 패키지가 필요합니다. 이러한 패키지를 설치하고 구성하려면 다음을 사용하여 도메인 조인 도구를 업데이트하고 설치합니다.`apt-get`

Kerberos 설치 하는 동안 *krb5 사용자* 패키지 는 모든 대문자의 영역 이름에 대 한 메시지를 표시 합니다. 예를 들어 Azure AD DS 관리 도메인의 이름이 *aaddscontoso.com*경우 *AADDSCONTOSO.COM* 영역으로 입력합니다. 설치는 `[realm]` */etc/krb5.conf* 구성 파일에 및 `[domain_realm]` 섹션을 씁니다. 영역을 ALL 대문자로 지정해야 합니다.

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>NTP(네트워크 시간 프로토콜 구성)

도메인 통신이 올바르게 작동하려면 우분투 VM의 날짜와 시간이 Azure AD DS 관리 도메인과 동기화되어야 합니다. Azure AD DS 관리 도메인의 NTP 호스트 이름을 */etc/ntp.conf* 파일에 추가합니다.

1. 편집기와 *ntp.conf* 파일을 엽니다.

    ```console
    sudo vi /etc/ntp.conf
    ```

1. *ntp.conf* 파일에서 Azure AD DS 관리 도메인의 DNS 이름을 추가할 줄을 만듭니다. 다음 예제에서는 *aaddscontoso.com* 대한 항목이 추가됩니다. 사용자 고유의 DNS 이름을 사용합니다.

    ```console
    server aaddscontoso.com
    ```

    완료되면 편집기의 `:wq` 명령을 사용하여 *ntp.conf* 파일을 저장하고 종료합니다.

1. VM이 Azure AD DS 관리 도메인과 동기화되도록 하려면 다음 단계가 필요합니다.

    * NTP 서버 중지
    * 관리되는 도메인의 날짜 및 시간 업데이트
    * NTP 서비스 시작

    다음 명령을 실행하여 이러한 단계를 완료합니다. `ntpdate` 명령과 함께 자신의 DNS 이름을 사용합니다.

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>관리되는 도메인에 VM 가입

필요한 패키지가 VM에 설치되고 NTP가 구성되었으므로 VM을 Azure AD DS 관리 도메인에 조인합니다.

1. `realm discover` 명령을 사용하여 Azure AD DS 관리 도메인을 검색합니다. 다음 예제는 *AADDSCONTOSO.COM*영역을 검색합니다. 모든 대문자에서 고유한 Azure AD DS 관리 도메인 이름을 지정합니다.

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   명령에서 `realm discover` Azure AD DS 관리 도메인을 찾을 수 없는 경우 다음 문제 해결 단계를 검토합니다.

    * VM에서 도메인에 연결할 수 있는지 확인합니다. 긍정적인 `ping aaddscontoso.com` 답장이 반환되는지 확인합니다.
    * VM이 Azure AD DS 관리 도메인을 사용할 수 있는 동일한 피어있는 가상 네트워크에 배포되어 있는지 확인합니다.
    * Azure AD DS 관리 도메인의 도메인 컨트롤러를 가리키도록 가상 네트워크에 대한 DNS 서버 설정이 업데이트되었는지 확인합니다.

1. 이제 명령을 사용하여 Kerberos를 `kinit` 초기화합니다. Azure AD DS 관리 도메인의 일부인 사용자를 지정합니다. 필요한 경우 [Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)의 그룹에 사용자 계정을 추가합니다.

    다시 말하지만 Azure AD DS 관리 되는 도메인 이름은 모든 대문자에 입력 해야 합니다. 다음 예제에서는 Kerberos를 초기화하는 데 명명된 `contosoadmin@aaddscontoso.com` 계정이 사용됩니다. Azure AD DS 관리 도메인의 일부인 사용자 계정을 입력합니다.

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. 마지막으로 명령을 사용하여 컴퓨터를 Azure AD DS `realm join` 관리 도메인에 조인합니다. 다음과 같이 `kinit` `contosoadmin@AADDSCONTOSO.COM`이전 명령에서 지정한 Azure AD DS 관리 도메인의 일부인 동일한 사용자 계정을 사용합니다.

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

VM을 Azure AD DS 관리 도메인에 가입하는 데 몇 분 정도 걸립니다. 다음 예제 출력은 VM이 Azure AD DS 관리 도메인에 성공적으로 가입되었음을 보여 주며 다음과 같은

```output
Successfully enrolled machine in realm
```

VM이 도메인 조인 프로세스를 성공적으로 완료할 수 없는 경우 VM의 네트워크 보안 그룹이 TCP + UDP 포트 464의 아웃바운드 Kerberos 트래픽을 Azure AD DS 관리 도메인의 가상 네트워크 서브넷에 허용해야 합니다.

## <a name="update-the-sssd-configuration"></a>SSSD 구성 업데이트

이전 단계에 설치된 패키지 중 하나는 시스템 보안 서비스 데몬(SSSD)이었습니다. 사용자가 도메인 자격 증명을 사용하여 VM에 로그인하려고 하면 SSSD는 요청을 인증 공급자에게 전달합니다. 이 시나리오에서 SSSD는 Azure AD DS를 사용하여 요청을 인증합니다.

1. 편집기와 *sssd.conf* 파일을 엽니다.

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. 다음과 같이 *use_fully_qualified_names* 줄을 주석지:

    ```console
    # use_fully_qualified_names = True
    ```

    완료되면 편집기의 `:wq` 명령을 사용하여 *sssd.conf* 파일을 저장하고 종료합니다.

1. 변경 을 적용하려면 SSSD 서비스를 다시 시작합니다.

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>사용자 계정 및 그룹 설정 구성

VM이 Azure AD DS 관리 도메인에 가입되어 인증을 위해 구성되면 완료할 몇 가지 사용자 구성 옵션이 있습니다. 이러한 구성 변경 에는 암호 기반 인증을 허용하고 도메인 사용자가 처음 로그인할 때 로컬 VM에서 홈 디렉터리를 자동으로 만드는 것이 포함됩니다.

### <a name="allow-password-authentication-for-ssh"></a>SSH에 대한 암호 인증 허용

기본적으로 사용자는 SSH 공개 키 기반 인증을 사용하여 VM에만 로그인할 수 있습니다. 암호 기반 인증에 실패합니다. VM을 Azure AD DS 관리 도메인에 가입할 때 해당 도메인 계정은 암호 기반 인증을 사용해야 합니다. 다음과 같이 암호 기반 인증을 허용하도록 SSH 구성을 업데이트합니다.

1. 편집기와 함께 *sshd_conf* 파일을 엽니다.

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. *암호 인증에* 대한 줄을 *예로*업데이트합니다.

    ```console
    PasswordAuthentication yes
    ```

    작업이 완료되면 편집기의 *sshd_conf* `:wq` 명령을 사용하여 sshd_conf 파일을 저장하고 종료합니다.

1. 변경 내용을 적용하고 사용자가 암호를 사용하여 로그인하도록 하려면 SSH 서비스를 다시 시작하십시오.

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>자동 홈 디렉터리 생성 구성

사용자가 처음 에 서명할 때 홈 디렉터리를 자동으로 만들수 있도록 하려면 다음 단계를 완료하십시오.

1. 편집기에서 */etc/pam.d/common-session* 파일을 엽니다.

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. 이 파일의 다음 줄을 줄 `session optional pam_sss.so`아래에 추가합니다.

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    작업이 완료되면 편집기의 *common-session* `:wq` 명령을 사용하여 공통 세션 파일을 저장하고 종료합니다.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>'AAD DC Administrators' 그룹 sudo 권한 부여

우분투 VM에서 *AAD DC Administrators* 그룹 관리 권한의 구성원에게 권한을 부여하려면 */etc/sudoers*에 항목을 추가합니다. 추가되면 *AAD DC 관리자* 그룹의 구성원은 `sudo` 우분투 VM에서 명령을 사용할 수 있습니다.

1. 편집을 위해 *sudoers* 파일을 엽니다.

    ```console
    sudo visudo
    ```

1. */etc/sudoers* 파일의 끝에 다음 항목을 추가합니다.

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    완료되면 명령을 사용하여 편집기에서 `Ctrl-X` 저장하고 종료합니다.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>도메인 계정을 사용하여 VM에 로그인

VM이 Azure AD DS 관리 도메인에 성공적으로 가입되었는지 확인하려면 도메인 사용자 계정을 사용하여 새 SSH 연결을 시작합니다. 홈 디렉터리가 만들어졌고 도메인의 그룹 구성원이 적용되었는지 확인합니다.

1. 본체에서 새 SSH 연결을 만듭니다. ubuntu.aaddscontoso.com 와 같은 VM의 주소를 `ssh -l` 입력하는 등의 `contosoadmin@aaddscontoso.com` 명령을 사용하여 관리되는 도메인에 속한 도메인 계정을 *사용합니다.* Azure 클라우드 셸을 사용하는 경우 내부 DNS 이름이 아닌 VM의 공용 IP 주소를 사용합니다.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
    ```

1. VM에 성공적으로 연결한 경우 홈 디렉터리가 올바르게 초기화되었는지 확인합니다.

    ```console
    pwd
    ```

    사용자 계정과 일치하는 고유한 디렉터리로 */home* 디렉터리에 있어야 합니다.

1. 이제 그룹 멤버 자격이 올바르게 확인되고 있는지 확인합니다.

    ```console
    id
    ```

    Azure AD DS 관리 도메인의 그룹 구성원 자격이 표시됩니다.

1. *AAD DC Administrators* 그룹의 구성원으로 VM에 로그인한 경우 `sudo` 명령을 올바르게 사용할 수 있는지 확인합니다.

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>다음 단계

VM을 Azure AD DS 관리 도메인에 연결하거나 도메인 계정으로 로그인하는 데 문제가 있는 경우 [도메인 가입 문제 해결 을](join-windows-vm.md#troubleshoot-domain-join-issues)참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
