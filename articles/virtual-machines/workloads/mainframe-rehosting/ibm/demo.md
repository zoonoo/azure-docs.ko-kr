---
title: IBM zD&T v1에서 ADCD(Application Developers Controlled Distribution) 설치 | Microsoft Docs
description: Azure VM(Virtual Machines)에서 IBM zD&T(Development and Test Environment) 환경을 실행합니다.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: b1391f81c571879534258c289436d2d086b84bef
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045946"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>IBM zD&T v1에서 ADCD(Application Developers Controlled Distribution) 설치

Azure VM(Virtual Machines)에서 IBM zD&T(Development and Test Environment) 환경을 실행할 수 있습니다. 이 환경은 IBM Z 시리즈 아키텍처를 에뮬레이트합니다. 이 도구는 IBM ADCD(Application Developers Controlled Distribution)라는 사용자 지정된 번들을 통해 제공되는 다양한 Z 시리즈 운영 체제 또는 설치(Z 인스턴스 또는 패키지 라고도 함)를 호스트할 수 있습니다.

이 문서에서는 Azure의 zD&T 환경에 ADCD 인스턴스를 설치하는 방법을 보여 줍니다. ADCD는 zD&T에서 실행되는 개발 및 테스트 환경을 위한 전체 Z 시리즈 운영 체제 구현을 만듭니다.

zD&T와 같이 ADCD는 IBM 고객 및 파트너만 사용할 수 있으며 개발 및 테스트 목적으로만 사용할 수 있습니다. 프로덕션 환경에는 사용되지 않습니다. 수많은 IBM 설치 패키지는 [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) 또는 [IBM PartnerWorld](https://www.ibm.com/partnerworld/public)에서 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- 이전에 Azure에서 설치된 [zD&T 환경][ibm-install-z]. 이 문서에서는 이전에 만든 것과 동일한 Ubuntu 16.04 VM 이미지를 사용하고 있다고 가정합니다.

- IBM PartnerWorld 또는 Passport Advantage를 통해 ADCD 미디어에 액세스할 수 있습니다.

- [라이선스 서버](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). IBM zD&T를 실행하는 데 필요합니다. 만드는 방법은 IBM에서 소프트웨어의 라이선스를 사용하는 방법에 따라 달라집니다.

  - **하드웨어 기반 라이선스 서버** 에는 소프트웨어의 모든 부분에 액세스하는 데 필요한 Rational Token이 포함된 USB 하드웨어 디바이스가 필요합니다. IBM에서 받아야 합니다.

  - **소프트웨어 기반 라이선스 서버** 를 사용하려면 라이선스 키를 관리하기 위해 중앙 집중식 서버를 설정해야 합니다. 이 방법이 선호되며 관리 서버에서 IBM을 통해 받은 키를 설정해야 합니다.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Passport Advantage에서 설치 패키지 다운로드

ADCD 미디어에 대한 액세스가 필요합니다. 아래 단계에서는 사용자가 IBM 고객이며 Passport Advantage를 사용할 수 있다고 가정합니다. IBM 파트너는 [IBM PartnerWorld](https://www.ibm.com/partnerworld/public)를 사용할 수 있습니다.

> [!NOTE]
> 이 문서에서는 Windows PC에서 Azure Portal에 액세스하고 IBM 미디어를 다운로드한다고 가정합니다. Mac 또는 Ubuntu 데스크톱을 사용하는 경우 IBM 미디어를 가져오기 위한 명령과 프로세스가 약간 다를 수 있습니다.

1. [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)에 로그온합니다.

2. **Software Downloads(소프트웨어 다운로드)** 및 **Media Access(미디어 액세스)** 를 선택합니다.

3. **Program offering and agreement number(프로그램 제품 및 계약 번호)** 를 선택하고 **Continue(계속)** 를 클릭합니다.

4. 파트 설명 또는 파트 번호를 입력하고 **Finder(찾기)** 를 클릭합니다.

5. 필요에 따라 사전순 목록을 클릭하여 제품을 이름별로 표시하고 볼 수 있습니다.

6. **Operating system field(운영 체제 필드)** 에서 **All Operating Systems모든 운영 체제)** 를 선택하고 **Languages field(언어 필드)** 에서 **All Languages(모든 언어)** 를 선택합니다. 그런 후 **Go(계속)** 를 클릭합니다.

7. **Select individual files(개별 파일 선택)** 을 클릭하여 목록을 확장하고 다운로드할 개별 미디어를 표시합니다.

8. 다운로드하려는 패키지를 확인하고 **Download(다운로드)** 를 선택한 후 원하는 디렉터리에 파일을 다운로드합니다.

## <a name="upload-the-adcd-packages"></a>ADCD 패키지 업로드

이제 패키지가 있으므로 Azure에서 VM에 업로드해야 합니다.

1. Azure Portal에서 만든 Ubuntu VM을 사용하여 **ssh** 세션을 시작합니다. VM으로 이동하여 **개요** 블레이드를 선택하고 **연결** 을 선택합니다.

2. **SSH** 탭을 선택한 다음, ssh 명령을 클립보드에 복사합니다.

3. 선택한 자격 증명 및 [SSH 클라이언트](../../../linux/use-remote-desktop.md)를 사용하여 VM에 로그온합니다. 이 데모에서는 Windows 명령 프롬프트에 bash 셸을 추가하는 Windows 10용 Linux 확장을 사용합니다. PuTTY는 마찬가지로 작동합니다.

4. 로그온하면 IBM 패키지를 업로드할 디렉터리를 만듭니다. Linux에서 대/소문자를 구분한다는 점에 유의하세요. 예를 들어 이 데모에서는 패키지를 다음에 업로드했다고 가정합니다.

    `/home/MyUserID/ZDT/adcd/nov2017/volumes`

5. [WinSCP](https://winscp.net/eng/index.php)와 같은 SSH 클라이언트를 사용하여 파일을 업로드합니다. SCP는 SSH의 일부이므로 SSH에서 사용하는 포트 22를 사용합니다. 로컬 컴퓨터가 Windows가 아닌 경우 SSH 세션에서 [scp 명령](http://man7.org/linux/man-pages/man1/scp.1.html)을 입력할 수 있습니다.

6. 만든 Azure VM 디렉터리로의 업로드를 시작합니다. 이 디렉터리는 zD&T용 이미지 스토리지가 됩니다.

    > [!NOTE]
    > **Home/MyUserID/ZDT/adcd/nov2017** 디렉터리로의 업로드에 **ADCDTOOLS.XML** 이 포함되어 있는지 확인합니다. 이 시간은 나중에 필요합니다.

7. 파일이 업로드될 때까지 기다립니다. Azure에 대한 연결에 따라 다소 시간이 걸릴 수 있습니다.

8. 업로드가 완료되면 볼륨 디렉터리로 이동하고 모든 **gz** 볼륨의 압축을 해제합니다.

    ```console
    gunzip \*.gz
    ```
    
![압축을 푼 gz 볼륨을 보여 주는 파일 탐색기](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>이미지 스토리지 구성

다음 단계는 업로드된 패키지를 사용하도록 zD&T를 구성하는 것입니다. zD&T 내의 이미지 스토리지 프로세스를 사용하여 이미지를 탑재하고 사용할 수 있습니다. SSH 또는 FTP를 사용할 수 있습니다.

1. **zDTServer** 를 시작합니다. 이렇게 하려면 루트 수준에 있어야 합니다. 다음 두 명령을 순서대로 입력합니다.
    ```console
    sudo su -
    /opt/ibm/zDT/bin/startServer
    ```
2. 명령별 URL 출력을 확인하고 이 URL을 사용하여 웹 서버에 액세스합니다. 출력은 다음과 비슷합니다.
     > https://(사용자의 VM 이름 또는 IP 주소):9443/ZDTMC/index.html
     >
     > 웹 액세스에서는 포트 9443을 사용합니다. 이 포트를 사용하여 웹 서버에 로그온합니다. zD&T의 사용자 ID는 **zdtadmin** 이고 암호는 **password** 입니다.

    ![IBM zD&T Enterprise Edition Welcome(시작) 화면](media/02-welcome.png)

3. **Quick Start(빠른 시작)** 페이지의 **Configure(구성)** 에서 **Image Storage(이미지 스토리지)** 를 선택합니다.

     ![IBM zD&T Enterprise Edition Quick Start(빠른 시작) 화면](media/03-quickstart.png)

4. **Configure image storage(이미지 스토리지 구성)** 페이지에서 **SSH File Transfer Protocol(SSH 파일 전송 프로토콜)** 을 선택합니다.

5. **Host name(호스트 이름)** 에 **Localhost** 를 입력하고 이미지를 업로드한 디렉터리 경로를 입력합니다. 예: /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. VM에 대한 **사용자 ID** 와 **암호** 를 입력합니다. zD&T 사용자 ID와 암호는 사용하지 마세요.

7. 연결을 테스트하여 액세스 권한이 있는지 확인하고 **Save(저장)** 를 선택하여 구성을 저장합니다.

## <a name="configure-the-target-environments"></a>대상 환경 구성

다음 단계는 zD&T 대상 환경을 구성하는 것입니다. 이 에뮬레이트된 호스티드 환경은 이미지를 실행하는 위치입니다.

1. **Quick Start(빠른 시작)** 페이지의 **Configure(구성)** 에서 **Target environments(대상 환경)** 를 선택합니다.

2. **Configure target environments(대상 환경 구성)** 페이지에서 **Add Target(대상 추가)** 을 선택합니다.

3. **Linux** 를 선택합니다. IBM은 두 가지 유형의 환경인 Linux 및 클라우드(OpenStack)를 지원하지만 이 데모는 Linux에서 실행됩니다.

4. **Add target environment(대상 환경 추가)** 페이지에서 **Host name(호스트 이름)** 에 **localhost** 를 입력합니다. **SSH port(SSH 포트)** 를 **22** 로 설정합니다.

5. **Target Environment label(대상 환경 레이블)** 상자에 **MyCICS** 와 같은 레이블을 입력합니다.

     ![Add target environment(대상 환경 추가) 화면](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>ADCD 구성 및 배포

이전 구성 단계를 완료한 후 패키지와 대상 환경을 사용하도록 zD&T를 구성해야 합니다. 다시 말하지만 zD&T의 이미지 스토리지 프로세스를 사용하여 이미지를 탑재하고 사용할 수 있습니다. SSH 또는 FTP를 사용할 수 있습니다.

1. **Quick Start(빠른 시작)** 페이지의 **Configure(구성)** 에서 **ADCD** 를 선택합니다. ADCD 패키지를 탑재하기 전에 완료해야 하는 단계를 알려 주는 일련의 지침이 표시됩니다. 여기서는 이전에 수행한 방법으로 대상 디렉터리의 이름을 지정한 이유를 설명합니다.

2. 모든 이미지를 올바른 디렉터리에 업로드했다고 가정하고 오른쪽 아래에 표시된 **IMAGE from ADCD(ADCD의 이미지)** 링크를 클릭합니다(다음 스크린샷의 7단계에 표시).

     ![IBM zD&T Enterprise Edition - Configure ADCD(ADCD 구성) 화면](media/05-adcd.png)

## <a name="create-the-image"></a>이미지 만들기

이전 구성 단계가 완료되면 **Create an image using ADCD Components(ADCD 구성 요소를 사용하여 이미지 만들기)** 페이지가 나타납니다.

1. 볼륨(이 경우 2017년 11월)을 선택하여 해당 볼륨에 있는 다른 패키지를 표시합니다.

2. 이 데모에서는 **Customer Information Control System (CICS) - 5.3** 을 선택합니다.

3. **이미지 이름** 상자에 **MyCICS Image(MyCICS 이미지)** 와 같은 이미지의 이름을 입력합니다.

4. 오른쪽 아래에서 **Create Image(이미지 만들기)** 단추를 선택합니다.

     ![IBM zD&T Enterprise Edition - Create an image using ADCD Components(ADCD 구성 요소를 사용하여 이미지 만들기) 화면](media/06-adcd.png)

5. 표시되는 창에서 이미지가 성공적으로 배포되었다고 표시되면 **Deploy images(이미지 배포)** 를 선택합니다.

6. **Deploy an image to a target environment(대상 환경에 이미지 배포)** 페이지에서 이전 페이지에서 만든 이미지(**MyCICS Image(Mycics 이미지)** ) 및 이전에 만든 대상 환경(**MyCICS**)을 선택합니다.

7. 다음 화면에서 VM에 대한 자격 증명을 제공합니다(ztadmin 자격 증명 아님).

8. Properties(속성) 창에서 실행 중인 이미지의 **CP(중앙 프로세서)** 수, **시스템 메모리 양(GB)** 및 **배포 디렉터리** 를 입력합니다. 이것은 데모이므로 작게 유지합니다.

9. **Automatically issue IPL command to z/OS after deploy(배포 후 자동으로 z/OS에 IPL 명령 실행)** 에 대한 확인란을 선택합니다.

     ![Properties(속성) 화면](media/07-properties.png)

10. **Complete(완료)** 를 선택합니다.

11. **Deploy an image to a target environment(대상 환경에 이미지 배포)** 페이지에서 **Deploy Image(이미지 배포)** 를 선택합니다.

이제 이미지를 배포할 수 있으며 3270 터미널 에뮬레이터에서 탑재할 준비가 되었습니다.

> [!NOTE]
> 디스크 공간이 부족하다는 오류 메시지가 표시되면 지역에 151Gb가 필요합니다.

축하합니다! 이제 Azure에서 IBM 메인프레임 환경이 실행됩니다.

## <a name="learn-more"></a>자세한 정보

- [메인프레임 마이그레이션: 허구 및 팩트](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure의 IBM DB2 pureScale](ibm-db2-purescale-azure.md)
- [문제 해결](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [메인프레임에서 Azure로의 마이그레이션 개요](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md