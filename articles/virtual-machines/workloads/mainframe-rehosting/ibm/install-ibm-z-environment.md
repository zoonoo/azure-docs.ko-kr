---
title: Azure에 IBM zD&T 개발/테스트 환경 설치 | 마이크로 소프트 문서
description: Azure 가상 머신(VM) 인프라에 IBM Z 개발 및 테스트 환경(zD&T)을 서비스(IaaS)로 배포합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025946"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Azure에 IBM zD&T 개발/테스트 환경 설치

IBM Z Systems에서 메인프레임 워크로드에 대한 개발/테스트 환경을 만들려면 Azure VM(VM) 인프라에 IBM Z 개발 및 테스트 환경(zd&T)을 서비스(IaaS)로 배포할 수 있습니다.

zD&T를 사용하면 덜 중요한 개발 및 테스트 환경을 위해 x86 플랫폼의 비용 절감효과를 활용한 다음 업데이트를 Z 시스템 프로덕션 환경으로 다시 푸시할 수 있습니다. 자세한 내용은 IBM [ZD&T 설치 지침을](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)참조하십시오.

Azure 및 Azure 스택은 다음 버전을 지원합니다.

- zD&T 퍼스널 에디션
- zD&T 병렬 Sysplex
- zD&T 엔터프라이즈 에디션

zD&T의 모든 버전은 윈도우 서버가 아닌 x86 리눅스 시스템에서만 실행됩니다. 엔터프라이즈 에디션은 레드 햇 엔터프라이즈 리눅스 (RHEL) 또는 우분투 / 데비안중 하나에서 지원됩니다. RHEL 및 데비안 VM 이미지는 Azure에서 모두 사용할 수 있습니다.

이 문서에서는 zD&T 엔터프라이즈 에디션을 Azure에서 설정하는 방법을 보여 주므로 zD&T 엔터프라이즈 에디션 웹 서버를 사용하여 환경을 만들고 관리합니다. zD&T를 설치해도 환경은 설치되지 않습니다. 이러한 패키지를 설치 패키지로 별도로 만들어야 합니다. 예를 들어 응용 프로그램 개발자 제어 배포(ADCD)는 테스트 환경의 볼륨 이미지입니다. IBM에서 사용할 수 있는 미디어 배포의 zip 이미지에 포함되어 있습니다. Azure 에서 [ADCD 환경을 설정하는](demo.md)방법을 알아봅

자세한 내용은 IBM 지식 센터의 [zD&T 개요를](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) 참조하십시오.

이 문서에서는 Azure에서 Z 개발 및 테스트 환경(zD&T) 엔터프라이즈 에디션을 설정하는 방법을 보여 주며 있습니다. 그런 다음 zD&T 엔터프라이즈 에디션 웹 서버를 사용하여 Azure에서 Z 기반 환경을 만들고 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

> [!NOTE]
> IBM은 zD&T 엔터프라이즈 에디션을 프로덕션 환경이*아닌* 개발/테스트 환경에만 설치할 수 있도록 허용합니다.

- Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- IBM 고객 및 파트너만 사용할 수 있는 미디어에 액세스해야 합니다. 자세한 내용은 IBM 담당자에게 문의하거나 [zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) 웹 사이트의 연락처 정보를 참조하십시오.

- [라이센스 서버](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). 이는 환경에 액세스하는 데 필요합니다. 이를 만드는 방법은 IBM에서 소프트웨어에 라이선스를 부여하는 방법에 따라 다릅니다.

     - **하드웨어 기반 라이선싱 서버에는** 소프트웨어의 모든 부분에 액세스하는 데 필요한 합리적인 토큰이 포함된 USB 하드웨어 장치가 필요합니다. IBM에서 이 정보를 얻어야 합니다.

     - **소프트웨어 기반 라이선싱 서버에서는** 라이선싱 키 관리를 위해 중앙 집중식 서버를 설정해야 합니다. 이 방법을 사용하는 것이 바람직하며 관리 서버에서 IBM에서 받은 키를 설정해야 합니다.

## <a name="create-the-base-image-and-connect"></a>기본 이미지 만들기 및 연결

1. Azure 포털에서 원하는 운영 체제 구성으로 [VM을 만듭니다.](/azure/virtual-machines/linux/quick-create-portal) 이 문서에서는 우분투 16.04를 실행하는 B4ms VM(vCPU 4개 및 메모리 16GB)을 가정합니다.

2. VM을 만든 후 SSH용 인바운드 포트 22, FTP용 21개, 웹 서버의 경우 9443을 엽니다.

3. **연결** 버튼을 통해 VM의 **개요** 블레이드에 표시된 SSH 자격 증명을 가져옵니다. **SSH** 탭을 선택하고 SSH 로그온 명령을 클립보드에 복사합니다.

4. 로컬 PC에서 [Bash 셸에](/azure/cloud-shell/quickstart) 로그온하고 명령을 붙여넣습니다. 그것은 형태 **ssh\<사용자 ID\>\@\<IP\>주소에있을**것입니다 . 자격 증명을 입력하면 홈 디렉터리에 대한 연결을 설정합니다.

## <a name="copy-the-installation-file-to-the-server"></a>설치 파일을 서버에 복사합니다.

웹 서버의 설치 파일은 **ZDT\_\_설치 EE\_V12.0.0.1.tgz입니다.** IBM에서 제공하는 미디어에 포함되어 있습니다. 이 파일을 우분투 VM에 업로드해야 합니다.

1. 명령줄에서 다음 명령을 입력하여 새로 만든 이미지에서 모든 것이 최신 상태인지 확인합니다.

    ```
    sudo apt-get update
    ```

2. 설치할 디렉토리를 만듭니다.

    ```
    mkdir ZDT
    ```

3. 로컬 컴퓨터에서 VM으로 파일을 복사합니다.

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> 이 명령은 클라이언트가 Windows 또는 Linux를 실행하는지 여부에 따라 달라지는 홈 디렉터리에서 설치 파일을 ZDT 디렉터리로 복사합니다.

## <a name="install-the-enterprise-edition"></a>엔터프라이즈 에디션 설치

1. ZDT 디렉토리로 이동하여 다음 명령을\_\_사용하여\_ZDT 설치 EE V12.0.0.1.tgz 파일의 압축을 풀수 있습니다.

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. 설치 관리자를 실행합니다.

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. 엔터프라이즈 서버를 설치하려면 **1을** 선택합니다.

4. **입력을** 누르고 사용권 계약을 주의 깊게 읽으십시오. 라이센스가 끝나면 **예를** 입력하여 계속진행합니다.

5. 새로 만든 사용자에 대 한 암호를 변경 하라는 메시지가 표시 되 면 **ibmsys1,** 명령 **sudo passwd ibmsys1를** 사용 하 고 새 암호를 입력 합니다.

6. 설치가 성공적으로 입력되었는지 확인하려면

    ```
    dpkg -l | grep zdtapp
    ```

7. 패키지 가스가 성공적으로 설치되었음을 나타내는 출력에 문자열 **zdtapp 12.0.0이**포함되어 있는지 확인합니다.

### <a name="starting-enterprise-edition"></a>엔터프라이즈 에디션 시작

웹 서버가 시작되면 설치 프로세스 중에 생성된 zD&T 사용자 ID로 실행됩니다.

1. 웹 서버를 시작하려면 루트 사용자 ID를 사용하여 다음 명령을 실행합니다.

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. 다음과 같은 스크립트로 URL 출력을 복사합니다.

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. URL을 웹 브라우저에 붙여 넣은 후 zD&T 설치에 대한 관리 구성 요소를 엽니다.

## <a name="next-steps"></a>다음 단계

[IBM zD&T v1에서 응용 프로그램 개발자 제어 배포(ADCD) 설정](./demo.md)
