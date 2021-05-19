---
title: Azure에서 IBM zD&T 개발/테스트 환경 설치 | Microsoft Docs
description: Azure VM(Virtual Machine) IaaS(서비스 제공 인프라)에 IBM zD&T(Z 개발 및 테스트 환경)를 배포합니다.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 32b63256f89d6d051305890c3387140f243e1a70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954329"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Azure에 IBM zD&T 개발/테스트 환경 설치

IBM Z Systems에서 메인프레임 워크로드를 위한 개발/테스트 환경을 만들기 위해 Azure VM(Virtual Machine) IaaS(서비스 제공 인프라)에 IBM Z 개발 및 테스트 환경(zD&T)을 배포할 수 있습니다.

zD&T를 사용하면 덜 중요한 개발 및 테스트 환경에 대한 x86 플랫폼의 비용 절감 효과를 활용한 다음 업데이트를 Z 시스템 프로덕션 환경으로 다시 푸시할 수 있습니다. 자세한 내용은 [IBM ZD&T 설치 지침](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)을 참조하세요.

Azure 및 Azure Stack 지원 버전은 다음과 같습니다.

- zD&T 개인용 버전
- zD&T 병렬 Sysplex
- zD&T Enterprise Edition

ZD&T의 모든 버전은 Windows 서버가 아닌 x86 Linux 시스템에서만 실행됩니다. Enterprise Edition은 RHEL(Red Hat Enterprise Linux) 또는 Ubuntu/Debian에서 지원됩니다. RHEL 및 Debian VM 이미지 모두 Azure에서 사용할 수 있습니다.

이 문서에서는 zD&T Enterprise Edition 웹 서버를 사용하여 환경 만들기 및 관리를 수행할 수 있도록 Azure에서 zD&T Enterprise Edition을 설정하는 방법을 보여 줍니다. ZD&T를 설치해도 환경이 설치되지 않습니다. 설치 패키지로 별도로 만들어야 합니다. 예를 들어 ADCD(애플리케이션 개발자 제어 배포)는 테스트 환경의 볼륨 이미지입니다. IBM에서 제공하는 매체 배포판의 zip 이미지에 포함되어 있습니다. [Azure에서 ADCD 환경을 설정](demo.md)하는 방법을 참조하세요.

자세한 내용은 IBM 지식 센터에서 [zD&T 개요](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html)를 참조하세요.

이 문서에서는 Azure에서 zD&T(Z 개발 및 테스트 환경) Enterprise Edition을 설정하는 방법을 보여 줍니다. 그런 다음 zD&T Enterprise Edition 웹 서버를 사용하여 Azure에서 Z 기반 환경을 만들고 관리할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

> [!NOTE]
> IBM은 zD&T Enterprise Edition을 프로덕션 환경이 *아닌* 개발/테스트 환경에만 설치할 수 있도록 허용합니다.

- Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- IBM 고객 및 파트너만 사용할 수 있는 미디어에 액세스할 수 있어야 합니다. 자세한 내용을 알아보려면 IBM 담당자에게 문의하거나 [zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) 웹 사이트의 연락처 정보를 참조하세요.

- [라이선스 서버](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html) 이는 환경에 액세스하는 데 필요합니다. 이를 만드는 방법은 IBM에서 소프트웨어 라이선스를 부여하는 방법에 따라 다릅니다.

     - **하드웨어 기반 라이선싱 서버** 에는 소프트웨어의 모든 부분에 액세스하는 데 합리적인 토큰이 포함된 USB 하드웨어 디바이스가 필요합니다. 이를 IBM에서 받아야 합니다.

     - **소프트웨어 기반 라이선스 서버** 를 사용하려면 라이선스 키 관리를 위한 중앙 서버를 설정해야 합니다. 이 방법이 선호되며 관리 서버에서 IBM으로부터 받은 키를 설정해야 합니다.

## <a name="create-the-base-image-and-connect"></a>기본 이미지 만들기 및 연결

1. Azure Portal에서 원하는 운영 체제 구성을 사용하여 [VM을 만듭니다](../../../linux/quick-create-portal.md). 이 문서에서는 Ubuntu 16.04를 실행하는 B4ms VM(vCPU 4개 및 16GB 메모리 포함)을 가정합니다.

2. VM을 만든 후 인바운드 포트로 SSH의 경우 22, FTP의 경우 21, 웹 서버의 경우 9443을 엽니다.

3. **연결** 단추를 통해 VM의 **개요** 블레이드에 표시된 SSH 자격 증명을 가져옵니다. **SSH** 탭을 선택한 다음 SSH 명령을 클립보드에 복사합니다.

4. 로컬 PC에서 [Bash 셸](../../../../cloud-shell/quickstart.md)에 로그온하여 명령을 붙여넣습니다. 이는 **ssh\<user id\>\@\<IP Address\>** 형식입니다. 자격 증명을 입력하라는 메시지가 표시되면 입력하여 홈 디렉터리에 대한 연결을 설정합니다.

## <a name="copy-the-installation-file-to-the-server"></a>서버에 설치 파일을 복사합니다.

웹 서버의 설치 파일은 **ZDT\_Install\_EE\_V12.0.0.1.tgz** 입니다. IBM에서 제공하는 미디어에 포함되어 있습니다. Ubuntu VM에 이 파일을 업로드해야 합니다.

1. 명령줄에서 다음 명령을 입력하여 새로 만든 이미지에서 모든 항목이 최신 상태인지 확인합니다.

    ```
    sudo apt-get update
    ```

2. 설치할 디렉터리를 만듭니다.

    ```
    mkdir ZDT
    ```

3. 로컬 머신에서 VM으로 파일을 복사합니다.

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> 이 명령은 클라이언트에서 Windows 또는 Linux를 실행하는지 여부에 따라 달라지는 홈 디렉터리의 ZDT 디렉터리에 설치 파일을 복사합니다.

## <a name="install-the-enterprise-edition"></a>Enterprise Edition 설치

1. ZDT 디렉터리로 이동하고 다음 명령을 사용하여 ZDT\_Install\_EE\_V12.0.0.1.tgz 파일의 압축을 풉니다.

    ```
    cd ZDT
    tar zxvf ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. 설치 관리자 실행:

    ```
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.x86_64
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. **1** 을 선택하여 엔터프라이즈 서버를 설치합니다.

4. **Enter** 키를 누르고 라이선스 계약을 자세히 읽습니다. 라이선스의 끝에 **예** 를 입력하여 계속합니다.

5. 새로 만든 사용자 **ibmsys1** 의 암호를 변경하라는 메시지가 표시되면 **sudo passwd ibmsys1** 명령을 사용하여 새 암호를 입력합니다.

6. 설치에 성공했는지 확인하려면 다음을 입력합니다.

    ```
    dpkg -l | grep zdtapp
    ```

7. 출력에 패키지 gas가 성공적으로 설치되었음을 나타내는 **zdtapp 12.0.0.0** 문자열이 포함되어 있는지 확인합니다.

### <a name="starting-enterprise-edition"></a>Enterprise Edition 시작

웹 서버가 시작되면 설치 프로세스 중에 만들어진 zD&T 사용자 ID로 실행됩니다.

1. 웹 서버를 시작하려면 루트 사용자 ID를 사용하여 다음 명령을 실행합니다.

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. 다음과 같은 스크립트의 URL 출력을 복사합니다.

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. URL을 웹 브라우저에 붙여넣어 zD&T 설치를 위한 관리 구성 요소를 엽니다.

## <a name="next-steps"></a>다음 단계

[IBM zD&T v1에서 ADCD(애플리케이션 개발자 컨트롤된 배포) 설정](./demo.md)
