---
title: Azure에서 IBM zD&T 개발/테스트 환경 설치 | Microsoft Docs
description: Azure 가상 머신 (VM) infrastructure as a service (IaaS)에서 IBM Z 개발 및 테스트 환경 (zD&T)을 배포 합니다.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 67af4eae03b773fad9cf38964152c1fb9d623bd5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "72025946"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Azure에서 IBM zD&T 개발/테스트 환경 설치

IBM Z 시스템에서 메인프레임 워크 로드에 대 한 개발/테스트 환경을 만들려면 Azure VM (가상 머신) IaaS (infrastructure as a service)에서 IBM Z (zD&T)를 배포할 수 있습니다.

ZD&T를 사용 하면 더 중요 한 개발 및 테스트 환경에 대 한 x86 플랫폼의 비용 절감을 활용할 수 있으며, 업데이트를 Z 시스템 프로덕션 환경으로 다시 푸시할 수 있습니다. 자세한 내용은 [IBM ZD&T 설치 지침](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)을 참조 하세요.

Azure 및 Azure Stack 지원 되는 버전은 다음과 같습니다.

- zD&T Personal Edition
- zD&T 병렬 Sysplex
- zD&T Enterprise Edition

ZD&T의 모든 버전은 Windows Server가 아닌 x86 Linux 시스템 에서만 실행 됩니다. Enterprise Edition은 Red Hat Enterprise Linux (RHEL) 또는 Ubuntu/Debian에서 지원 됩니다. RHEL 및 Debian VM 이미지를 모두 Azure에서 사용할 수 있습니다.

이 문서에서는 zD&T Enterprise Edition 웹 서버를 사용 하 여 환경 만들기 및 관리를 수행할 수 있도록 Azure에서 zD&T Enterprise Edition을 설정 하는 방법을 보여 줍니다. ZD&T를 설치 해도 환경이 설치 되지 않습니다. 이러한 사용자는 설치 패키지로 따로 만들어야 합니다. 예를 들어 ADCD (응용 프로그램 개발자 제어 배포)는 테스트 환경의 볼륨 이미지입니다. 이러한 이미지는 IBM에서 사용할 수 있는 미디어 배포의 zip 이미지에 포함 되어 있습니다. [Azure에서 ADCD 환경을 설정](demo.md)하는 방법을 참조 하세요.

자세한 내용은 IBM 지식 센터에서 [Zd&T 개요](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) 를 참조 하세요.

이 문서에서는 Azure에서 Z 개발 및 테스트 환경 (zD&T) Enterprise Edition을 설정 하는 방법을 보여 줍니다. 그런 다음 zD&T Enterprise Edition 웹 서버를 사용 하 여 Azure에서 Z 기반 환경을 만들고 관리할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

> [!NOTE]
> IBM에서는 zD&T Enterprise Edition을 개발/테스트 환경에만 설치할 수 있으며 프로덕션 환경에서는 설치할 수*없습니다* .

- Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- IBM 고객 및 파트너만 사용할 수 있는 미디어에 액세스할 수 있어야 합니다. 자세한 내용은 IBM 담당자에 게 문의 하거나 [Zd&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) 웹 사이트의 연락처 정보를 참조 하세요.

- [라이선스 서버](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). 이는 환경에 액세스 하는 데 필요 합니다. 이를 만드는 방법은 IBM에서 소프트웨어의 라이선스를 사용 하는 방법에 따라 달라 집니다.

     - **하드웨어 기반 라이선스 서버** 에는 소프트웨어의 모든 부분에 액세스 하는 데 필요한 유리 토큰이 포함 된 USB 하드웨어 장치가 필요 합니다. IBM에서 구해야 합니다.

     - **소프트웨어 기반 라이선스 서버** 를 사용 하려면 라이선스 키를 관리 하기 위해 중앙 집중식 서버를 설정 해야 합니다. 이 방법을 사용 하는 것이 좋습니다. 관리 서버의 IBM에서 받는 키를 설정 해야 합니다.

## <a name="create-the-base-image-and-connect"></a>기본 이미지 만들기 및 연결

1. Azure Portal에서 원하는 운영 체제 구성을 사용 하 여 [VM을 만듭니다](/azure/virtual-machines/linux/quick-create-portal) . 이 문서에서는 Ubuntu 16.04를 실행 하는 B4ms VM (4 개의 vCPUs와 16gb의 메모리)을 가정 합니다.

2. VM을 만든 후 SSH의 경우 인바운드 포트 22, FTP의 경우 21, 웹 서버의 경우 9443을 엽니다.

3. **연결** 단추를 통해 VM의 **개요** 블레이드에 표시 된 SSH 자격 증명을 가져옵니다. **Ssh** 탭을 선택 하 고 ssh logon 명령을 클립보드에 복사 합니다.

4. 로컬 PC에서 [Bash 셸에](/azure/cloud-shell/quickstart) 로그온 하 여 명령을 붙여넣습니다. 이 파일은 **ssh\<사용자 id\>\@\<IP\>주소**형식입니다. 자격 증명을 입력 하 라는 메시지가 표시 되 면 해당 자격 증명을 입력 하 여 홈 디렉터리에 대 한 연결을 설정 합니다.

## <a name="copy-the-installation-file-to-the-server"></a>서버에 설치 파일을 복사 합니다.

웹 서버의 설치 파일은 **Zdt\_Install\_EE\_v 12.0.0.1. tgz**입니다. IBM에서 제공 하는 미디어에 포함 되어 있습니다. Ubuntu VM에이 파일을 업로드 해야 합니다.

1. 명령줄에서 다음 명령을 입력 하 여 새로 만든 이미지에서 모든 항목이 최신 상태 인지 확인 합니다.

    ```
    sudo apt-get update
    ```

2. 설치할 디렉터리를 만듭니다.

    ```
    mkdir ZDT
    ```

3. 로컬 컴퓨터에서 VM으로 파일을 복사 합니다.

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> 이 명령은 클라이언트에서 Windows 또는 Linux를 실행 하는지 여부에 따라 달라 지는 홈 디렉터리의 ZDT 디렉터리에 설치 파일을 복사 합니다.

## <a name="install-the-enterprise-edition"></a>Enterprise Edition 설치

1. 다음 명령을 사용 하 여 ZDT 디렉터리로 이동 하 고\_\_zdt\_Install EE 12.0.0.1. tgz 파일의 압축을 해제 합니다.

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. 설치 관리자를 실행 합니다.

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. **1** 을 선택 하 여 엔터프라이즈 서버를 설치 합니다.

4. **Enter** 키를 누르고 사용권 계약을 자세히 읽습니다. 라이선스의 끝에 **예** 를 입력 하 여 계속 합니다.

5. 새로 만든 사용자 **ibmsys1**에 대 한 암호를 변경 하 라는 메시지가 표시 되 면 **sudo passwd ibmsys1** 명령을 사용 하 여 새 암호를 입력 합니다.

6. 설치에 성공 했는지 확인 하려면 다음을 입력 합니다.

    ```
    dpkg -l | grep zdtapp
    ```

7. 출력에 **zdtapp 12.0.0.0**문자열이 포함 되어 있는지 확인 합니다 .이는 패키지 가스가 성공적으로 설치 되었음을 나타냅니다.

### <a name="starting-enterprise-edition"></a>Enterprise Edition 시작

웹 서버가 시작 되 면 설치 프로세스 중에 만들어진 zD&T 사용자 ID에서 실행 됩니다.

1. 웹 서버를 시작 하려면 루트 사용자 ID를 사용 하 여 다음 명령을 실행 합니다.

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. 스크립트를 통해 URL 출력을 복사 합니다 .이는 다음과 같습니다.

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. URL을 웹 브라우저에 붙여넣어 zD&T 설치용 관리 구성 요소를 엽니다.

## <a name="next-steps"></a>다음 단계

[IBM zD&T v1에서 응용 프로그램 개발자 제어 배포 (ADCD)를 설정 합니다.](./demo.md)
