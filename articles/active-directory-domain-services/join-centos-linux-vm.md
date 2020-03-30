---
title: 센트OS VM을 Azure AD 도메인 서비스에 가입 | 마이크로 소프트 문서
description: Azure AD 도메인 서비스 관리 도메인에 CentOS Linux 가상 컴퓨터를 구성하고 가입하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: c634b1288727ae045d1fb8b6f6cdff4a80e757ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298908"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Azure AD 도메인 서비스 관리 도메인에 CentOS Linux 가상 머신 가입

사용자가 단일 자격 증명 집합을 사용하여 Azure의 VM(가상 시스템)에 로그인할 수 있도록 하려면 VM을 Azure Active Directory 도메인 서비스(AD DS) 관리 도메인에 조인할 수 있습니다. VM을 Azure AD DS 관리 도메인에 가입하면 도메인의 사용자 계정 및 자격 증명을 사용하여 서버에 로그인하고 관리할 수 있습니다. Azure AD DS 관리 도메인의 그룹 구성원 자격도 적용되어 VM의 파일 또는 서비스에 대한 액세스를 제어할 수 있습니다.

이 문서에서는 CentOS Linux VM을 Azure AD DS 관리 도메인에 가입하는 방법을 보여 주며 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure AD DS 관리 도메인의 일부인 사용자 계정입니다.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>생성 및 센트로스 리눅스 VM에 연결

Azure에 기존 CentOS Linux VM이 있는 경우 SSH를 사용하여 연결한 다음 다음 단계로 계속하여 [VM 구성을 시작합니다.](#configure-the-hosts-file)

CentOS Linux VM을 만들어야 하거나 이 문서에서 사용할 테스트 VM을 만들려면 다음 방법 중 하나를 사용할 수 있습니다.

* [Azure 포털](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure 파워쉘](../virtual-machines/linux/quick-create-powershell.md)

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
* *센토스는* 관리되는 도메인에 가입하는 CentOS VM의 호스트 이름입니다.

이러한 이름을 사용자 고유의 값으로 업데이트합니다.

```console
127.0.0.1 centos.aaddscontoso.com centos
```

작업이 완료되면 편집기의 *hosts* `:wq` 명령을 사용하여 호스트 파일을 저장하고 종료합니다.

## <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

VM은 Azure AD DS 관리 도메인에 VM을 조인하기 위해 몇 가지 추가 패키지가 필요합니다. 이러한 패키지를 설치하고 구성하려면 다음을 사용하여 `yum`도메인 조인 도구를 업데이트하고 설치합니다.

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>관리되는 도메인에 VM 가입

필요한 패키지가 VM에 설치되었으므로 VM을 Azure AD DS 관리 도메인에 조인합니다.

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
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

VM을 Azure AD DS 관리 도메인에 가입하는 데 몇 분 정도 걸립니다. 다음 예제 출력은 VM이 Azure AD DS 관리 도메인에 성공적으로 가입되었음을 보여 주며 다음과 같은

```output
Successfully enrolled machine in realm
```

VM이 도메인 조인 프로세스를 성공적으로 완료할 수 없는 경우 VM의 네트워크 보안 그룹이 TCP + UDP 포트 464의 아웃바운드 Kerberos 트래픽을 Azure AD DS 관리 도메인의 가상 네트워크 서브넷에 허용해야 합니다.

## <a name="allow-password-authentication-for-ssh"></a>SSH에 대한 암호 인증 허용

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
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>'AAD DC Administrators' 그룹 sudo 권한 부여

CentOS VM에서 *AAD DC Administrators* 그룹 관리 권한의 구성원에게 권한을 부여하려면 */etc/sudoers*에 항목을 추가합니다. 추가되면 *AAD DC 관리자* 그룹의 구성원은 `sudo` CentOS VM에서 명령을 사용할 수 있습니다.

1. 편집을 위해 *sudoers* 파일을 엽니다.

    ```console
    sudo visudo
    ```

1. */etc/sudoers* 파일의 끝에 다음 항목을 추가합니다. *AAD DC Administrators* 그룹에는 이름에 공백이 있으므로 그룹 이름에 백슬래시 이스케이프 문자를 포함합니다. *aaddscontoso.com*같은 고유한 도메인 이름 추가:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    작업이 완료되면 편집기의 명령을 `:wq` 사용하여 편집기에서 저장하고 종료합니다.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>도메인 계정을 사용하여 VM에 로그인

VM이 Azure AD DS 관리 도메인에 성공적으로 가입되었는지 확인하려면 도메인 사용자 계정을 사용하여 새 SSH 연결을 시작합니다. 홈 디렉터리가 만들어졌고 도메인의 그룹 구성원이 적용되었는지 확인합니다.

1. 본체에서 새 SSH 연결을 만듭니다. centos.aaddscontoso.com 와 같은 VM의 주소를 `ssh -l` 입력하는 등의 `contosoadmin@aaddscontoso.com` 명령을 사용하여 관리되는 도메인에 속한 도메인 계정을 *사용합니다.* Azure 클라우드 셸을 사용하는 경우 내부 DNS 이름이 아닌 VM의 공용 IP 주소를 사용합니다.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com centos.aaddscontoso.com
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
    sudo yum update
    ```

## <a name="next-steps"></a>다음 단계

VM을 Azure AD DS 관리 도메인에 연결하거나 도메인 계정으로 로그인하는 데 문제가 있는 경우 [도메인 가입 문제 해결 을](join-windows-vm.md#troubleshoot-domain-join-issues)참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
