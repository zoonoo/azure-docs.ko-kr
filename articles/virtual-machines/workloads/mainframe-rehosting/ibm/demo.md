---
title: IBM zD&T v1에서 응용 프로그램 개발자 제어 배포(ADCD) 설정 | 마이크로 소프트 문서
description: Azure 가상 시스템(VM)에서 IBM Z 개발 및 테스트 환경(zD&T) 환경을 실행합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68841380"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>IBM zD&T v1에서 응용 프로그램 개발자 제어 배포(ADCD) 설정

Azure 가상 시스템(VM)에서 IBM Z 개발 및 테스트 환경(zD&T) 환경을 실행할 수 있습니다. 이 환경은 IBM Z 시리즈 아키텍처를 에뮬레이트합니다. 다양한 Z 시리즈 운영 체제 또는 설치(Z 인스턴스 또는 패키지라고도 함)를 호스팅할 수 있으며, 이는 IBM 응용 프로그램 개발자 제어 배포(ADC)라고 하는 사용자 지정된 번들을 통해 사용할 수 있습니다.

이 문서에서는 Azure의 zD&T 환경에서 ADCD 인스턴스를 설정하는 방법을 보여 주며 있습니다. ADCD는 zD&T에서 실행되는 개발 및 테스트 환경을 위한 완전한 Z 시리즈 운영 체제 구현을 생성합니다.

zD&T와 마찬가지로 ADCD는 IBM 고객 및 파트너만 사용할 수 있으며 개발 및 테스트 목적으로만 사용할 수 있습니다. 프로덕션 환경에는 사용할 수 없습니다. 수많은 IBM 설치 패키지는 [여권 어드밴티지](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) 또는 [IBM 파트너 월드를](https://www.ibm.com/partnerworld/public)통해 다운로드 할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- [zD&T 환경은][ibm-install-z] 이전에 Azure에서 설정했습니다. 이 문서에서는 이전에 만든 동일한 우분투 16.04 VM 이미지를 사용 하는 가정.

- IBM 파트너월드 또는 패스포트 어드밴티지를 통해 ADCD 미디어에 액세스할 수 있습니다.

- [라이센스 서버](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). 이는 IBM zD&T를 실행하는 데 필요합니다. 이를 만드는 방법은 IBM에서 소프트웨어에 라이선스를 부여하는 방법에 따라 다릅니다.

  - **하드웨어 기반 라이선싱 서버에는** 소프트웨어의 모든 부분에 액세스하는 데 필요한 합리적인 토큰이 포함된 USB 하드웨어 장치가 필요합니다. IBM에서 이 정보를 얻어야 합니다.

  - **소프트웨어 기반 라이선싱 서버에서는** 라이선싱 키 관리를 위해 중앙 집중식 서버를 설정해야 합니다. 이 방법을 사용하는 것이 바람직하며 관리 서버에서 IBM에서 받은 키를 설정해야 합니다.

## <a name="download-the-installation-packages-from-passport-advantage"></a>여권 어드밴티지에서 설치 패키지 다운로드

ADCD 미디어에 액세스해야 합니다. 아래 단계는 IBM 고객이며 Passport Advantage를 사용할 수 있다고 가정합니다. IBM 파트너는 [IBM 파트너월드를](https://www.ibm.com/partnerworld/public)사용할 수 있습니다.

> [!NOTE]
> 이 문서에서는 Windows PC가 Azure 포털에 액세스하고 IBM 미디어를 다운로드하는 데 사용된다고 가정합니다. Mac 또는 우분투 데스크톱을 사용 하는 경우, IBM 미디어를 얻기 위한 명령 및 프로세스 약간 다를 수 있습니다.

1. [여권 어드밴티지에](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)로그온.

2. 소프트웨어 다운로드 및 **미디어 액세스**를 **선택합니다.**

3. 프로그램 제공 및 계약 번호를 선택하고 **계속을** **클릭합니다.**

4. 부품 설명 또는 부품 번호를 입력하고 **파인더**를 클릭합니다.

5. 선택적으로 알파벳 순서 목록을 클릭하여 제품 이름을 표시하고 봅니다.

6. **운영 체제 필드의**모든 운영 **체제** 및 언어 필드의 **모든 언어를** **선택합니다.** 그런 다음 **이동을**클릭합니다.

7. **개별 파일 선택을** 클릭하여 목록을 확장하고 다운로드할 개별 미디어를 표시합니다.

8. 다운로드할 패키지를 확인한 다음 **다운로드를**선택한 다음 파일을 원하는 디렉터리로 다운로드합니다.

## <a name="upload-the-adcd-packages"></a>ADCD 패키지 업로드

이제 패키지가 있으므로 Azure에서 VM에 업로드해야 합니다.

1. Azure 포털에서 만든 우분투 VM으로 **ssh** 세션을 시작합니다. VM으로 이동하여 **개요** 블레이드를 선택한 다음 **연결을**선택합니다.

2. **SSH** 탭을 선택한 다음 ssh 명령을 클립보드에 복사합니다.

3. 자격 증명 및 선택한 [SSH 클라이언트를](/azure/virtual-machines/linux/use-remote-desktop) 사용하여 VM에 로그온합니다. 이 데모는 Windows 용 Linux 확장을 사용 하 여 10, Windows 명령 프롬프트에 배쉬 쉘을 추가 하는. PuTTY도 마찬가지입니다.

4. 로그온하면 IBM 패키지를 업로드할 디렉터리를 만듭니다. 명심 리눅스는 대소문자에 민감한. 예를 들어 이 데모에서는 패키지가 다음으로 업로드된다고 가정합니다.

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. WinSCP 와 같은 SSH 클라이언트를 사용하여 파일을[업로드합니다.](https://winscp.net/eng/index.php) SCP는 SSH의 일부이므로 SSH가 사용하는 포트 22를 사용합니다. 로컬 컴퓨터가 Windows가 아닌 경우 SSH 세션에서 [scp 명령을](http://man7.org/linux/man-pages/man1/scp.1.html) 입력할 수 있습니다.

6. 생성한 Azure VM 디렉터리로 업로드를 시작하여 zD&T의 이미지 저장소가 됩니다.

    > [!NOTE]
    > **ADCDTOOLS가 있는지 확인하십시오. XML은** **홈 / MyUserID / ZDT / adcd / nov2017** 디렉토리에 업로드에 포함되어 있습니다. 이 시간은 나중에 필요합니다.

7. Azure에 대한 연결에 따라 다소 시간이 걸릴 수 있는 파일이 업로드될 때까지 기다립니다.

8. 업로드가 완료되면 볼륨 디렉터리로 이동하여 모든 **gz** 볼륨의 압축을 풀수 있습니다.

    ```
        gunzip \*.gz
    ```
    
![압축 해제된 gz 볼륨을 표시하는 파일 탐색기](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>이미지 저장 구성

다음 단계는 업로드된 패키지를 사용하도록 zD&T를 구성하는 것입니다. zD&T 내의 이미지 저장 프로세스를 통해 이미지를 마운트하고 사용할 수 있습니다. SSH 또는 FTP를 사용할 수 있습니다.

1. **zDTServer를**시작합니다. 이렇게 하려면 루트 수준에 있어야 합니다. 다음 두 명령을 순서대로 입력합니다.
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. 명령으로 URL 출력을 기록하고 이 URL을 사용하여 웹 서버에 액세스합니다. 다음과 유사합니다.
     > https://(VM 이름 또는 IP 주소): 9443/ZDTMC/index.html
     >
     > 웹 액세스는 포트 9443을 사용합니다. 웹 서버에 로그온하려면 이 방법을 사용합니다. ZD&T의 사용자 ID는 **zdtadmin이고** 암호는 **암호입니다.**

    ![IBM zD&T 엔터프라이즈 에디션 환영 화면](media/02-welcome.png)

3. 빠른 **시작** 페이지에서 **구성**에서 **이미지 저장소를**선택합니다.

     ![IBM zD&T 엔터프라이즈 에디션 빠른 시작 화면](media/03-quickstart.png)

4. 이미지 **저장 구성** 페이지에서 **SSH 파일 전송 프로토콜을**선택합니다.

5. **호스트 이름의**경우 **Localhost를** 입력하고 이미지를 업로드한 위치에 대한 디렉터리 경로를 입력합니다. 예를 들어, /home/MyUserID/ZDT/adcd/nov2017/볼륨입니다.

6. VM의 **사용자 ID와** **암호를** 입력합니다. ZD&T 사용자 ID 및 암호를 사용하지 마십시오.

7. 연결을 테스트하여 액세스 권한이 있는지 확인한 다음 **저장을** 선택하여 구성을 저장합니다.

## <a name="configure-the-target-environments"></a>대상 환경 구성

다음 단계는 zD&T 대상 환경을 구성하는 것입니다. 이 에뮬레이트된 호스팅 환경은 이미지가 실행되는 곳입니다.

1. 빠른 **시작** 페이지에서 **구성**에서 **대상 환경을**선택합니다.

2. 대상 **환경 구성** 페이지에서 **대상 추가를**선택합니다.

3. **리눅스를**선택합니다. IBM은 리눅스와 클라우드(OpenStack)의 두 가지 유형의 환경을 지원하지만 이 데모는 Linux에서 실행됩니다.

4. 대상 **환경 추가** 페이지에서 **호스트 이름에**대해 **localhost를**입력합니다. **SSH 포트를** **22로**설정합니다.

5. 대상 **환경 레이블** 상자에 **MyCICS와 같은 레이블을 입력합니다.**

     ![대상 환경 화면 추가](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>ADCD 구성 및 배포

이전 구성 단계를 완료한 후 패키지 및 대상 환경을 사용하도록 zD&T를 구성해야 합니다. 다시 말하지만, zD&T에서 이미지 저장 프로세스를 사용하여 이미지를 마운트하고 사용할 수 있습니다. SSH 또는 FTP를 사용할 수 있습니다.

1. 빠른 **시작** 페이지에서 **구성**에서 **ADCD를**선택합니다. ADCD 패키지를 탑재하기 전에 완료해야 하는 단계를 알려주는 일련의 지침이 나타납니다. 이것은 우리가 이전과 같은 대상 디렉토리의 이름을 지정하는 이유를 설명합니다.

2. 모든 이미지가 올바른 디렉토리에 업로드되었다고 가정하면 오른쪽 하단에 표시된 **ADCD 링크의 이미지를** 클릭합니다(다음 스크린샷의 7단계에 표시됨).

     ![IBM zD&T 엔터프라이즈 에디션 - ADCD 화면 구성](media/05-adcd.png)

## <a name="create-the-image"></a>이미지 만들기

이전 구성 단계가 완료되면 **ADCD 구성 요소를 사용하여 이미지 만들기** 페이지가 나타납니다.

1. 볼륨(이 경우 2017년 11월)을 선택하여 해당 볼륨에 있는 다른 패키지를 표시합니다.

2. 이 데모에서는 **고객 정보 제어 시스템(CICS)을 선택합니다.**

3. 이미지 **이름** 상자에 **MyCICS 이미지와**같은 이미지의 이름을 입력합니다.

4. 오른쪽 하단에서 **이미지 만들기** 단추를 선택합니다.

     ![IBM zD&T 엔터프라이즈 에디션 - ADCD 구성 요소 화면을 사용하여 이미지 만들기](media/06-adcd.png)

5. 표시되는 창에서 이미지가 성공적으로 배포되었다는 것을 알리는 경우 **이미지 배포를**선택합니다.

6. 대상 **환경** 페이지에 이미지 배포 페이지에서 이전 페이지(MyCICS**이미지)에서**만든 이미지와 이전에 만든 대상**환경(MyCICS)을**선택합니다.

7. 다음 화면에서 VM에 대한 자격 증명(즉, ztadmin 자격 증명이 아님)을 제공합니다.

8. 속성 창에서 **중앙 프로세서(AP)** 수, **시스템 메모리 양(GB)** 및 실행 중인 이미지의 **배포 디렉터리를** 입력합니다. 데모이므로 작게 유지하십시오.

9. **배포 후 자동으로 IPL 명령을 z/OS에 발급할 때**상자가 선택되어 있는지 확인합니다.

     ![속성 화면](media/07-properties.png)

10. **완료를 선택합니다.**

11. **이미지 배포페이지에서 대상 환경** 으로 이미지 **배포를** 선택합니다.

이제 이미지를 배포할 수 있으며 3270 터미널 에뮬레이터로 탑재할 수 있습니다.

> [!NOTE]
> 디스크 공간이 충분하지 않다는 오류가 발생하면 해당 지역에 151Gb가 필요합니다.

축하합니다! 이제 Azure에서 IBM 메인프레임 환경을 실행중입니다.

## <a name="learn-more"></a>자세한 정보

- [메인프레임 마이그레이션: 신화와 사실](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [AZURE의 IBM DB2 순수 스케일](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [문제 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [메인프레임을 Azure 마이그레이션으로 신비화](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
