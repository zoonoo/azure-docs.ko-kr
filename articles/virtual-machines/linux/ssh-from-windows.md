---
title: Linux VM용 Windows를 통한 SSH 키 사용 | Microsoft Docs
description: Windows 컴퓨터에서 SSH 키를 생성하고 사용하여 Azure에서 Linux 가상 머신에 연결하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: danlep
ms.openlocfilehash: fcc2365c3b41fb69492aa68bf7c48c2d3b8ee5f3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Azure에서 Windows를 통해 SSH 키를 사용하는 방법
> [!div class="op_single_selector"]
> * [Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
>
>

Azure에서 Linux VM(가상 머신)에 연결할 때 [public-key cryptography](https://wikipedia.org/wiki/Public-key_cryptography)를 사용하면 더욱 안전한 방법으로 Linux VM에 로그인할 수 있습니다. 이 프로세스는 사용자를 인증하는 데 있어 사용자 이름과 암호가 아니라 SSH(보안 셸) 명령을 사용하는 공용 및 개인 키의 교환과 관계됩니다. 암호는 무차별 암호 대입 공격, 특히 웹 서버와 같은 인터넷 연결 VM에 대한 공격에 취약합니다. 이 문서에서는 SSH 키 개요 및 Windows 컴퓨터에 적절한 키를 생성하는 방법에 대해 설명합니다.

## <a name="overview-of-ssh-and-keys"></a>SSH 및 키에 대한 개요
다음과 같은 공개 및 개인 키를 사용하면 Linux VM에 안전하게 로그인할 수 있습니다.

* **공개 키**는 public-key 암호화를 사용하려는 Linux VM 또는 다른 서비스에 배치됩니다.
* **개인 키**는 사용자가 로그인할 때 자신의 ID를 확인하도록 Linux VM에 제출하는 키입니다. 이 개인 키는 보호해야 하는 한편, 공유하면 안됩니다.

이러한 공용 및 개인 키는 여러 VM 및 서비스에서 사용할 수 있습니다. 각 VM 또는 서비스에 액세스하기 위해 키 쌍이 반드시 필요한 것은 아닙니다. 자세한 개요에 대해서는 [public-key 암호화](https://wikipedia.org/wiki/Public-key_cryptography)를 참조하세요.

SSH는 안전하지 않은 연결에서 안전하게 로그인할 수 있도록 하는 암호화된 연결 프로토콜입니다. Azure에서 호스팅되는 Linux VM에 대한 기본 연결 프로토콜입니다. SSH 자체에서 암호화된 연결을 제공하지만 SSH 연결과 함께 암호를 하더라도 VM은 여전히 무차별 암호 대입 공격이나 암호 추측에 취약합니다. SSH를 사용하여 VM에 연결하는 데 있어 더 안전하고 선호하는 방법은 이러한 공개 및 개인 키, 즉 SSH 키를 사용하는 것입니다.

SSH 키를 사용하지 않지 않더라도 여전히 암호를 사용하여 Linux VM에 로그인할 수 있습니다. VM이 인터넷에 노출되지 않는 경우에는 암호만 사용 하는 것으로도 충분할 수 있습니다. 그렇지만 지속적으로 각 Linux VM에 대한 암호를 관리하고, 최소 암호 길이, 정기 업데이트 등 정상적 암호 정책 및 사례들을 유지 관리해야 합니다. SSH 키를 사용하면 여러 VM에 대한 개별적인 자격 증명 관리의 복잡성을 줄입니다.

## <a name="windows-packages-and-ssh-clients"></a>Windows 패키지 및 SSH 클라이언트
Azure에서 **SSH 클라이언트**를 통해 Linux VM에 연결하고 관리합니다. 일반적으로 Windows 컴퓨터에는 SSH 클라이언트를 설치하지 않습니다. Windows 10 1주년 업데이트에 Windows용 Bash가 추가되었으며 최신 Windows 10 크리에이터 업데이트에는 추가 업데이트가 제공됩니다. Linux용 Windows 하위 시스템에서는 Bash 셸 내에 기본적으로 포함된 SSH 클라이언트와 같은 유틸리티를 실행하고 액세스할 수 있습니다. 그런 다음 [Linux용 SSH 키 쌍을 생성하는 방법](mac-create-ssh-keys.md)과 같은 Linux 문서를 참조할 수 있습니다. Windows용 Bash는 아직 개발 중이며 베타 릴리스 버전으로 간주됩니다. Windows용 Bash에 대한 자세한 내용은 [Ubuntu on Windows의 Bash](https://msdn.microsoft.com/commandline/wsl/about)를 참조하세요.

Windows용 Bash 이외의 것을 사용하려는 경우 설치할 수 있는 공용 Windows SSH 클라이언트는 다음과 같은 패키지에 포함되어 있습니다.

* [Windows 용 Git](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)


## <a name="which-key-files-do-you-need-to-create"></a>어떤 키 파일을 만들어야 하나요?
Azure는 최소한 2048비트, **ssh-rsa** 형식 공개 및 개인 키 서식이 필요합니다. 클래식 배포 모델을 사용하여 Azure 리소스를 관리하는 경우 PEM(`.pem` 파일)도 생성해야 합니다.

다음은 배포 시나리오 및 각각에 사용되는 파일 형식입니다.

1. **ssh-rsa** 키는 [Azure 포털](https://portal.azure.com)을 사용하는 모든 배포 및 [Azure CLI](../../cli-install-nodejs.md)를 사용하는 Resource Manager 배포에 필요합니다.
   * 대개 이러한 키는 대부분의 모든 사용자에게 필요한 것입니다.
2. `.pem` 파일은 클래식 배포를 사용하여 VM을 만드는 데 필요합니다. 이러한 키는 [Azure Portal](https://portal.azure.com) 또는 [Azure CLI](../../cli-install-nodejs.md)를 사용하는 클래식 배포에서 지원됩니다.
   * 클래식 배포 모델을 사용하여 만든 리소스를 관리하는 경우 이러한 추가 키와 인증서를 만들기만 하면 됩니다.

## <a name="install-git-for-windows"></a>Windows용 Git 설치
이전 섹션에서는 Windows용 `openssl` 도구가 포함된 다수의 패키지를 나열했습니다. 이 도구는 공용 및 개인 키를 만드는 데 필요합니다. 다음 예제에서는 선호하는 패키지를 선택할 수도 있겠지만 **Windows 용 Git**를 설치하고 사용하는 방법에 대해 자세히 설명합니다. **Windows 용 Git**를 사용하면 Linux VM을 사용할 때 유용할 수도 있는 몇 가지 추가 오픈 소스 소프트웨어([OSS](https://en.wikipedia.org/wiki/Open-source_software)) 도구와 유틸리티에 액세스할 수 있습니다.

1. [https://git-for-windows.github.io/](https://git-for-windows.github.io/)에서 **Windows용 Git**을 다운로드하고 설치합니다.
2. 자격 증명을 별도로 변경할 필요가 없으면 설치 과정에서 기본 옵션을 적용합니다.
3. **시작 메뉴** > **Git** > **Git Bash**에서 **Git Bash**를 실행합니다. 콘솔에서 다음 예와 비슷한 모양으로 보여 줍니다.

    ![Windows용 Git의 Bash 셸](./media/ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>개인 키 만들기
1. **Git Bash** 창에서 `openssl.exe`를 사용하여 개인 키를 만듭니다. 다음 예제에서는 `myPrivateKey` 키 및 `myCert.pem` 인증서를 만듭니다.

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    출력은 다음 예제와 유사합니다.

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

   Bash가 오류를 보고하는 경우 상승된 권한으로 새 **Git Bash** 창을 엽니다. 그런 후 `openssl` 명령을 실행합니다.

2. 국가 이름, 위치, 조직 이름 등을 묻는 메시지에 응답합니다.
3. 새 개인 키와 인증서가 현재 작업 중인 디렉터리에 만들어집니다. 보안 방안으로 사용자의 개인 키에만 액세스할 수 있도록 해당 키의 사용 권한을 설정해야 합니다.

    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. [다음 섹션](#create-a-private-key-for-putty)에서는 PuTTY를 사용하여 공개 키를 확인하고 사용하는 방법에 대해 설명하고 SSH에 PuTTY를 사용하기 위해 특정된 개인 키를 Linux VM에 만듭니다. 다음 명령은 지금 바로 사용할 수 있는 `myPublicKey.key`라는 공개 키 파일을 생성합니다.

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. 클래식 리소스도 관리해야 하는 경우 `myCert.pem`을 `myCert.cer`(DER 인코딩된 X509 인증서)로 변환합니다. 특히 이전 클래식 리소스를 관리해야 하는 경우에만 선택적으로 이 단계를 수행합니다.

    다음 명령을 사용하여 인증서를 변환합니다.

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>PuTTY용 개인 키 만들기
PuTTY는 Windows용 공용 SSH 클라이언트입니다. 원하는 SSH 클라이언트를 무료로 사용할 수 있습니다. PuTTY를 사용하려면 추가 형식의 키인 PuTTY 개인 키(PPK)를 만들어야 합니다. PuTTY를 사용하지 않으려면 이 섹션을 건너뜁니다.

다음 예제에서는 특별히 PuTTY용 추가 개인 키를 만들어 사용합니다.

1. **Git Bash**를 사용하여 개인 키를 PuTTYgen에서 인식할 수 있는 RSA 개인 키로 변환합니다. 다음 예제에서는 기존의 `myPrivateKey` 키에서 `myPrivateKey_rsa` 키를 만듭니다.

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    보안 방안으로 사용자의 개인 키에만 액세스할 수 있도록 해당 키의 사용 권한을 설정해야 합니다.

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 PuTTYgen을 다운로드하고 실행합니다.
3. 메뉴에서 **파일** > **개인 키 로드**를 클릭합니다.
4. 개인 키(이전 예제의 `myPrivateKey_rsa`)를 찾습니다. **Git Bash**를 시작할 때의 기본 디렉터리는 `C:\Users\%username%`입니다. **모든 파일 (\*.\*)**을 표시하도록 파일 필터를 변경합니다.

    ![PuTTYgen에 기존 개인 키 로드](./media/ssh-from-windows/load-private-key.png)
5. **열기**를 클릭합니다. 다음과 같이 키를 성공적으로 가져왔다는 메시지를 표시합니다.

    ![성공적으로 PuTTYgen으로 가져온 키](./media/ssh-from-windows/successfully-imported-key.png)
6. **확인**을 클릭하여 창을 닫습니다.
7. 공개 키는 **PuTTYgen** 창의 맨 위에 표시됩니다. Linux VM을 만들 때 이 공개 키를 복사하여 Azure 포털 또는 Azure Resource Manager 템플릿에 붙여넣습니다. 다음과 같이 **공개 키 저장**을 클릭하면 컴퓨터에 복사본을 저장할 수도 있습니다.

    ![PuTTY 공개 키 파일 저장](./media/ssh-from-windows/save-public-key.png)

    다음 예제에서는 Linux VM을 만들 때 이 공개 키를 복사하여 Azure 포털에 붙여넣는 방법을 보여 줍니다. 그런 다음 공개 키는 대개 새 VM의 `~/.ssh/authorized_keys`에 저장됩니다.

    ![Azure 포털에서 VM을 만들 때의 공개 키 사용](./media/ssh-from-windows/use-public-key-azure-portal.png)
8. 다음과 같이 다시 **PuTTYgen**에서 **개인 키 저장**을 클릭합니다.

    ![PuTTY 개인 키 파일 저장](./media/ssh-from-windows/save-ppk-file.png)

   > [!WARNING]
   > 키에 대한 전달 구를 입력하지 않고 계속하기를 원하는지 묻는 메시지가 표시됩니다. 전달 구는 개인 키에 연결되는 암호와 비슷합니다. 다른 사용자가 개인 키를 가져오더라도 키만으로는 인증할 수 없기 때문에 전달 구가 필요합니다. 전달 구를 사용하지 않을 때 개인 키를 가져오면 해당 키를 사용하는 모든 VM 또는 서비스에 로그인 할 수 있습니다. 따라서 전달 구를 만드는 것이 좋습니다. 하지만 전달 구를 잊어버린 경우에는 복구할 수 있는 방법이 없으므로 주의해야 합니다.
   >
   >

    전달 구를 입력하려면 **아니요**를 클릭하고, 주 PuTTYgen 창에서 전달 구를 입력한 다음 **개인 키 저장**을 다시 클릭합니다. 그렇지 않을 경우 **예**를 클릭하여 선택적인 전달 구를 암호를 제공하지 않고서 계속합니다.
9. 이름과 위치를 입력하여 PPK 파일을 저장합니다.

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Linux 컴퓨터의 SSH에 Putty 사용
다시금 말하지만 PuTTY는 Windows용 공용 SSH 클라이언트입니다. 원하는 SSH 클라이언트를 무료로 사용할 수 있습니다. 다음 단계에서는 개인 키를 사용하여 SSH 사용 Azure VM에서 인증하는 방법에 대해 자세히 설명합니다. 이러한 단계는 SSH 연결을 인증하기 위해 개인 키를 로드해야 하는 점에서 다른 SSH 키 클라이언트에서 수행하는 단계들과 비슷합니다.

1. [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 PuTTY를 다운로드하고 실행합니다.
2. 다음과 같이 Azure 포털에서 VM의 호스트 이름 또는 IP 주소를 입력합니다.

    ![새 PuTTY 연결 열기](./media/ssh-from-windows/putty-new-connection.png)
3. **열기**를 선택하기 전에 **연결** > **SSH** > **인증** 탭을 클릭합니다. 개인 키 찾기 및 선택:

    ![인증용 PuTTY 개인 키 선택](./media/ssh-from-windows/putty-auth-dialog.png)
4. **열기** 를 클릭하여 가상 머신에 연결합니다.

## <a name="next-steps"></a>다음 단계
[OS X 및 Linux를 사용](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)하여 공개 및 개인 키를 생성할 수도 있습니다.

Windows용 Bash 및 Windows 컴퓨터에서 쉽게 사용할 수 있는 OSS 도구의 이점에 대한 자세한 내용은 [Ubuntu on Windows의 Bash](https://msdn.microsoft.com/commandline/wsl/about)를 참조하세요.

SSH를 사용하여 Linux VM을 연결하는 데 문제가 있으면 [Azure Linux VM에 대한 SSH 연결 문제 해결](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
