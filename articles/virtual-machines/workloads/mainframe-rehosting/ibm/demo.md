---
title: IBM zD&T v1에서 응용 프로그램 개발자 제어 배포 (ADCD)를 설정 합니다. Microsoft Docs
description: Azure Virtual Machines (Vm)에서 IBM Z (zD&T) 환경을 실행 합니다.
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 66f80c79219090c27da37dfc1d9149df5604961f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "68841380"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>IBM zD&T v1에서 응용 프로그램 개발자 제어 배포 (ADCD)를 설정 합니다.

Azure Virtual Machines (Vm)에서 IBM Z (zD&T) 환경을 실행할 수 있습니다. 이 환경은 IBM Z 시리즈 아키텍처를 에뮬레이트합니다. 이 도구는 다양 한 Z 시리즈 운영 체제 또는 설치 (Z 인스턴스 또는 패키지 라고도 함)를 호스트할 수 있습니다 .이는 IBM 응용 프로그램 개발자 제어 배포 (ADCDs) 라는 사용자 지정 된 번들을 통해 제공 됩니다.

이 문서에서는 Azure에서 zD&T 환경에 ADCD 인스턴스를 설정 하는 방법을 보여 줍니다. ADCDs는 zD&T에서 실행 되는 개발 및 테스트 환경을 위한 전체 Z 시리즈 운영 체제 구현을 만듭니다.

ZD&T와 같이 ADCDs는 IBM 고객 및 파트너만 사용할 수 있으며 개발 및 테스트 목적 으로만 사용할 수 있습니다. 프로덕션 환경에는 사용 되지 않습니다. 수많은 IBM 설치 패키지는 [Passport 혜택](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) 또는 [IBM 지역](https://www.ibm.com/partnerworld/public)에서 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- 이전에 Azure에서 설정 된 [Zd&T 환경][ibm-install-z] 이 문서에서는 이전에 만든 것과 동일한 Ubuntu 16.04 VM 이미지를 사용 하 고 있다고 가정 합니다.

- IBM 세계 또는 Passport 혜택을 통해 ADCD 미디어에 액세스할 수 있습니다.

- [라이선스 서버](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). IBM zD&T를 실행 하는 데 필요 합니다. 이를 만드는 방법은 IBM에서 소프트웨어의 라이선스를 사용 하는 방법에 따라 달라 집니다.

  - **하드웨어 기반 라이선스 서버** 에는 소프트웨어의 모든 부분에 액세스 하는 데 필요한 유리 토큰이 포함 된 USB 하드웨어 장치가 필요 합니다. IBM에서 구해야 합니다.

  - **소프트웨어 기반 라이선스 서버** 를 사용 하려면 라이선스 키를 관리 하기 위해 중앙 집중식 서버를 설정 해야 합니다. 이 방법을 사용 하는 것이 좋습니다. 관리 서버의 IBM에서 받는 키를 설정 해야 합니다.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Passport 혜택에서 설치 패키지 다운로드

ADCD 미디어에 대 한 액세스가 필요 합니다. 아래 단계에서는 IBM 고객이 고 Passport 혜택을 사용할 수 있다고 가정 합니다. Ibm 파트너는 [Ibm 세계](https://www.ibm.com/partnerworld/public)를 사용할 수 있습니다.

> [!NOTE]
> 이 문서에서는 Windows PC가 Azure Portal 액세스 하 고 IBM 미디어를 다운로드 하는 데 사용 된다고 가정 합니다. Mac 또는 Ubuntu 데스크톱을 사용 하는 경우 IBM 미디어를 얻기 위한 명령과 프로세스가 약간 다를 수 있습니다.

1. [Passport 이점](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)에 로그온 합니다.

2. **소프트웨어 다운로드** 및 **미디어 액세스**를 선택 합니다.

3. **프로그램 제공 및 계약 번호**를 선택 하 고 **계속**을 클릭 합니다.

4. 파트 설명 또는 부품 번호를 입력 하 고 **Finder**를 클릭 합니다.

5. 필요에 따라 사전순 목록을 클릭 하 여 이름별로 제품을 표시 하 고 볼 수 있습니다.

6. **운영 체제 필드**에서 **모든 운영** 체제를 선택 하 고 **언어 필드**에 **모든 언어** 를 선택 합니다. 그런 다음 **이동**을 클릭 합니다.

7. **개별 파일 선택** 을 클릭 하 여 목록을 확장 하 고 다운로드할 개별 미디어를 표시 합니다.

8. 다운로드 하려는 패키지를 확인 하 고 **다운로드**를 선택한 후 원하는 디렉터리에 파일을 다운로드 합니다.

## <a name="upload-the-adcd-packages"></a>ADCD 패키지 업로드

이제 패키지가 있으므로 Azure에서 VM에 업로드 해야 합니다.

1. Azure Portal에서 만든 Ubuntu VM을 사용 하 여 **ssh** 세션을 시작 합니다. VM으로 이동 하 여 **개요** 블레이드를 선택 하 고 **연결**을 선택 합니다.

2. **Ssh** 탭을 선택한 다음 ssh 명령을 클립보드에 복사 합니다.

3. 선택한 자격 증명 및 [SSH 클라이언트](/azure/virtual-machines/linux/use-remote-desktop) 를 사용 하 여 VM에 로그온 합니다. 이 데모에서는 windows 명령 프롬프트에 bash 셸을 추가 하는 Windows 10 용 Linux 확장을 사용 합니다. PuTTY 마찬가지로 작동 합니다.

4. 로그온 하면 IBM 패키지를 업로드할 디렉터리를 만듭니다. Linux에서 대/소문자를 구분 한다는 점에 유의 하세요. 예를 들어이 데모에서는 패키지를에 업로드 했다고 가정 합니다.

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. [Winscp](https://winscp.net/eng/index.php)와 같은 SSH 클라이언트를 사용 하 여 파일을 업로드 합니다. SCP는 SSH의 일부 이므로 SSH에서 사용 하는 포트 22를 사용 합니다. 로컬 컴퓨터가 Windows가 아닌 경우 SSH 세션에서 [scp 명령을](http://man7.org/linux/man-pages/man1/scp.1.html) 입력할 수 있습니다.

6. 만든 Azure VM 디렉터리에 대 한 업로드를 시작 합니다 .이 디렉터리는 zD&T의 이미지 저장소가 됩니다.

    > [!NOTE]
    > **Adcdtools를 확인 합니다. XML** 은 **Home/myuserid/zdt/adcd/nov2017** 디렉터리에 업로드 하는 데 포함 됩니다. 이 시간은 나중에 필요합니다.

7. 파일이 업로드 될 때까지 기다렸다가 Azure에 대 한 연결에 따라 다소 시간이 걸릴 수 있습니다.

8. 업로드가 완료 되 면 볼륨 디렉터리로 이동 하 여 모든 **release.tar.gz** 볼륨의 압축을 해제 합니다.

    ```
        gunzip \*.gz
    ```
    
![압축을 푼 release.tar.gz 볼륨을 보여 주는 파일 탐색기](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>이미지 저장소 구성

다음 단계는 업로드 된 패키지를 사용 하도록 zD&T를 구성 하는 것입니다. ZD&T 내의 이미지 저장소 프로세스를 사용 하 여 이미지를 탑재 하 고 사용할 수 있습니다. SSH 또는 FTP를 사용할 수 있습니다.

1. **Zdtserver**를 시작 합니다. 이렇게 하려면 루트 수준에 있어야 합니다. 다음 두 명령을 순서 대로 입력 합니다.
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. 명령에의 한 URL 출력을 확인 하 고이 URL을 사용 하 여 웹 서버에 액세스 합니다. 다음과 유사 하 게 표시 됩니다.
     > https://(사용자의 VM 이름 또는 IP 주소): 9443/ZDTMC/index .html
     >
     > 웹 액세스에서는 포트 9443을 사용 합니다. 이를 사용 하 여 웹 서버에 로그온 합니다. ZD&T의 사용자 ID는 **zdtadmin** 이 고 암호는 **password**입니다.

    ![IBM zD&T Enterprise Edition 시작 화면](media/02-welcome.png)

3. **빠른 시작** 페이지의 **구성**에서 **이미지 저장소**를 선택 합니다.

     ![IBM zD&T Enterprise Edition 빠른 시작 화면](media/03-quickstart.png)

4. **이미지 저장소 구성** 페이지에서 **SSH 파일 전송 프로토콜**를 선택 합니다.

5. **호스트 이름**에 **Localhost** 를 입력 하 고 이미지를 업로드 한 디렉터리 경로를 입력 합니다. 예:/home/MyUserID/ZDT/adcd/nov2017/volumes.

6. VM에 대 한 **사용자 ID** 와 **암호** 를 입력 합니다. ZD&T 사용자 ID와 암호를 사용 하지 마세요.

7. 연결을 테스트 하 여 액세스 권한이 있는지 확인 하 고 **저장** 을 선택 하 여 구성을 저장 합니다.

## <a name="configure-the-target-environments"></a>대상 환경 구성

다음 단계는 zD&T 대상 환경을 구성 하는 것입니다. 이 에뮬레이트된 호스팅된 환경은 이미지를 실행 하는 위치입니다.

1. **빠른 시작** 페이지의 **구성**에서 **대상 환경**을 선택 합니다.

2. **대상 환경 구성** 페이지에서 **대상 추가**를 선택 합니다.

3. **Linux**를 선택 합니다. IBM은 두 가지 유형의 환경, Linux 및 클라우드 (OpenStack)를 지원 하지만이 데모는 Linux에서 실행 됩니다.

4. **대상 환경 추가** 페이지에서 **호스트 이름**에 **localhost**를 입력 합니다. **SSH 포트** 를 **22**로 설정 합니다.

5. **대상 환경 레이블** 상자에 **mycics** 와 같은 레이블을 입력 합니다.

     ![대상 환경 추가 화면](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>ADCD 및 배포 구성

이전 구성 단계를 완료 한 후 패키지와 대상 환경을 사용 하도록 zD&T를 구성 해야 합니다. 다시, 이미지를 탑재 하 고 사용할 수 있도록 하는 zD&T에서 이미지 저장소 프로세스를 사용 합니다. SSH 또는 FTP를 사용할 수 있습니다.

1. **빠른 시작** 페이지의 **구성**에서 **adcd**를 선택 합니다. ADCD 패키지를 탑재 하기 전에 완료 해야 하는 단계를 알려 주는 일련의 지침이 표시 됩니다. 이는 이전에 수행한 방법으로 대상 디렉터리의 이름을 지정 하는 이유를 설명 합니다.

2. 모든 이미지를 올바른 디렉터리에 업로드 했다고 가정 하 고 오른쪽 아래에 표시 된 **ADCD 링크의 이미지** 를 클릭 합니다 (다음 스크린샷에서 7 단계에 표시 됨).

     ![IBM zD&T Enterprise Edition-ADCD 구성 화면](media/05-adcd.png)

## <a name="create-the-image"></a>이미지 만들기

이전 구성 단계가 완료 되 면 **ADCD 구성 요소를 사용 하 여 이미지 만들기** 페이지가 나타납니다.

1. 볼륨 (이 경우 11 월 2017)을 선택 하 여 해당 볼륨에 있는 다른 패키지를 표시 합니다.

2. 이 데모에서는 **CICS (Customer Information Control System)-5.3**을 선택 합니다.

3. **이미지 이름** 상자에 **mycics 이미지**와 같은 이미지의 이름을 입력 합니다.

4. 오른쪽 아래에서 **이미지 만들기** 단추를 선택 합니다.

     ![IBM zD&T Enterprise Edition-ADCD 구성 요소를 사용 하 여 이미지 만들기 화면](media/06-adcd.png)

5. 표시 되는 창에서 이미지가 성공적으로 배포 되었음을 알려주는 **이미지 배포**를 선택 합니다.

6. **대상 환경에 이미지 배포** 페이지에서 이전 페이지 (**Mycics 이미지**) 및 이전에 만든 대상 환경 (**mycics**)에서 만든 이미지를 선택 합니다.

7. 다음 화면에서 VM에 대 한 자격 증명을 제공 합니다 (즉, ztadmin 자격 증명이 아님).

8. 속성 창에서 실행 중인 이미지에 대 한 **CPs (중앙 프로세서)** 수, **시스템 메모리 양 (GB)** 및 **배포 디렉터리** 를 입력 합니다. 이는 데모 이므로 작게 유지 합니다.

9. **배포 후 자동으로 IPL 명령을 z/OS에 자동으로 발급**하도록 확인란을 선택 했는지 확인 합니다.

     ![속성 화면](media/07-properties.png)

10. **완료**를 선택 합니다.

11. **대상 환경에 이미지 배포** 페이지에서 **이미지 배포** 를 선택 합니다.

이제 이미지를 배포할 수 있으며 3270 터미널 에뮬레이터에서 탑재할 준비가 되었습니다.

> [!NOTE]
> 디스크 공간이 부족 하다는 오류 메시지가 표시 되 면 지역에 151 Gb가 필요 합니다.

지금까지 이제 Azure에서 IBM 메인프레임 환경을 실행 하 고 있습니다.

## <a name="learn-more"></a>자세한 정보

- [메인프레임 마이그레이션: myths 및 팩트](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure에서 IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [문제 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [전문가가 제공 자세히 메인프레임에서 Azure로 마이그레이션](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
