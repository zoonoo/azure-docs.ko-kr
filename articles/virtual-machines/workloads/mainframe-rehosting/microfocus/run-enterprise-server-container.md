---
title: Azure 가상 컴퓨터에서 Docker 컨테이너에서 마이크로 포커스 엔터프라이즈 서버 4.0 실행
description: Azure 가상 머신의 Docker 컨테이너에서 마이크로 포커스 엔터프라이즈 서버를 실행하여 IBM z/OS 메인프레임 워크로드를 다시 호스팅합니다.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "61488461"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Azure의 Docker 컨테이너에서 마이크로 포커스 엔터프라이즈 서버 4.0 실행

Azure의 Docker 컨테이너에서 마이크로 포커스 엔터프라이즈 서버 4.0을 실행할 수 있습니다. 이 튜토리얼은 방법을 보여줍니다. 엔터프라이즈 서버에 대 한 Windows CICS (고객 정보 제어 시스템) acctdemo 데모를 사용 합니다.

Docker는 응용 프로그램에 이식성과 격리를 추가합니다. 예를 들어 한 Windows VM에서 Docker 이미지를 내보내 다른 Windows VM에서 실행하거나 리포지토리에서 Docker가 있는 Windows 서버로 내보낼 수 있습니다. Docker 이미지는 엔터프라이즈 서버를 설치하지 않고도 동일한 구성으로 새 위치에서 실행됩니다. 그것은 이미지의 일부입니다. 라이선스 고려 사항은 여전히 적용됩니다.

이 자습서에서는 Azure 마켓플레이스에서 컨테이너 가상 시스템(VM)이 **있는 Windows 2016 데이터 센터를** 설치합니다. 이 VM에는 **Docker 18.09.0.** 아래 단계는 컨테이너를 배포하고 실행한 다음 3270 에뮬레이터로 연결하는 방법을 보여 주며 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 필수 구성 조건을 확인하십시오.

- Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- 마이크로 포커스 소프트웨어 및 유효한 라이센스 (또는 평가판 라이센스). 기존 마이크로 포커스 고객인 경우 Micro Focus 담당자에게 문의하십시오. 그렇지 않으면 [평가판을 요청합니다.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)

     > [!NOTE]
     > Docker 데모 파일은 엔터프라이즈 서버 4.0에 포함되어 있습니다. 이 튜토리얼은\_\_ent\_서버 dockerfiles 4.0\_windows.zip을 사용합니다. 엔터프라이즈 서버 설치 파일에 액세스한 동일한 위치에서 액세스하거나 *마이크로 포커스로* 이동하여 시작하십시오.

- [엔터프라이즈 서버 및 엔터프라이즈 개발자에](https://www.microfocus.com/documentation/enterprise-developer/#")대한 설명서입니다.

## <a name="create-a-vm"></a>VM 만들기

1. ent\_서버\_dockerfiles\_4.0\_windows.zip 파일에서 미디어를 보호합니다. 도커 이미지를 빌드하는 데 필요한 ES-Docker-Prod-XXXXXXXX.mflic 라이선싱 파일을 보호합니다.

2. VM을 만듭니다. 이렇게 하려면 Azure 포털을 열고 왼쪽 상단에서 **리소스 만들기를** 선택하고 *Windows 서버별로*필터링합니다. 결과에서 컨테이너를 사용하여 **Windows Server 2016 데이터 센터를**선택합니다.

     ![Azure 포털 검색 결과](media/01-portal.png)

3. VM에 대한 속성을 구성하려면 인스턴스 세부 정보를 선택합니다.

    1. VM 크기를 선택합니다. 이 자습서에서는 2vCPU와 7GB메모리의 **표준\_DS2 v2** VM을 사용하는 것이 좋습니다.

    2. 배포할 **지역** 및 **리소스 그룹을** 선택합니다.

    3. **가용성 옵션의**경우 기본 설정을 사용합니다.

    4. **사용자 이름의**경우 사용하려는 관리자 계정과 암호를 입력합니다.

    5. 포트 **3389 RDP가** 열려 있는지 확인합니다. VM에 로그인할 수 있도록 이 포트만 공개적으로 노출해야 합니다. 그런 다음 모든 기본값을 수락하고 **Review+만들기를 클릭합니다.**

     ![가상 컴퓨터 창 만들기](media/container-02.png)

4. 배포가 완료될 때까지 기다립니다(몇 분). VM이 만들어졌다는 메시지가 표시됩니다.

5. **리소스로 이동을** 클릭하여 VM의 **개요** 블레이드로 이동합니다.

6. 오른쪽에서 **연결** 버튼을 클릭합니다. **가상 시스템에 연결** 옵션이 오른쪽에 나타납니다.

7. **RDP 파일 다운로드** 버튼을 클릭하여 VM에 연결할 수 있는 RDP 파일을 다운로드합니다.

8. 파일 다운로드가 완료되면 파일을 열고 VM용으로 만든 사용자 이름과 암호를 입력합니다.

     > [!NOTE]
     > 회사 자격 증명을 사용하여 로그인하지 마십시오. RDP 클라이언트는 이러한 것을 사용할 수 있다고 가정합니다. 당신은하지 않습니다.)

9.  **더 많은 선택 을**선택한 다음 VM 자격 증명을 선택합니다.

이 시점에서 VM이 RDP를 통해 실행되고 연결됩니다. 로그인하여 다음 단계를 수행할 준비가 되었습니다.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>샌드박스 디렉토리를 만들고 zip 파일을 업로드합니다.

1.  데모 및 라이센스 파일을 업로드할 수 있는 VM에서 디렉터리를 만듭니다. 예를 들어 **C:\\샌드박스.**

2.  당신이 만든 디렉토리에 **인트\_서버\_dockerfiles\_4.0\_windows.zip** 및 **ES-도커 프로드 - XXXXXXXXXXXXXX.mflic** 파일을 업로드합니다.

3.  추출 프로세스에서 만든 **4.0\_\_윈도우\_디렉토리를 ent 서버 dockerfiles에\_** zip 파일의 내용을 추출합니다. 이 디렉터리에는 readme 파일(.html 및 .txt 파일)과 두 개의 하위 디렉토리인 **EnterpriseServer** 및 **예제가 포함됩니다.**

4.  **C에 ES-도커 프로드-XXXXXXXX.mflic** \\복사:\\샌드박스 ent\_서버\_dockerfiles\_4.0\_윈도우\\엔터프라이즈\_\_서버\_및\_C:\\\\샌드박스\\ent 서버 dockerfiles 4.0 창 예제\\CICS 디렉토리.

> [!NOTE]
> 라이선스 파일을 두 디렉터리에 복사해야 합니다. 이미지가 제대로 라이선스가 있는지 확인하기 위해 Docker 빌드 단계에 필요합니다.

## <a name="check-docker-version-and-create-base-image"></a>Docker 버전을 확인하고 기본 이미지 만들기

> [!IMPORTANT]
> 적절한 Docker 이미지를 만드는 것은 두 단계로 진행됩니다. 먼저 엔터프라이즈 서버 4.0 기본 이미지를 만듭니다.그런 다음 x64 플랫폼에 대한 다른 이미지를 만듭니다. x86(32비트) 이미지를 만들 수 있지만 64비트 이미지를 사용합니다.

1. 명령 프롬프트를 엽니다.

2. Docker가 설치되어 있는지 확인합니다. 명령 프롬프트에 다음을 입력합니다.

    ```
        docker version
    ```

     예를 들어 이 버전이 작성되었을 때 버전은 18.09.0이었습니다.

3. 디렉토리를 변경하려면 **cd \sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**를 입력합니다.

4. 초기 기본 이미지에 대한 빌드 프로세스를 시작하려면 **bld.bat IacceptEULA를** 입력합니다. 이 프로세스가 실행될 때까지 몇 분 정도 기다립니다. 결과에서 생성된 두 이미지(x64용 및 x86용 이미지)를 확인합니다.

     ![이미지를 표시하는 명령 창](media/container-04.png)

5. CICS 데모에 대한 최종 이미지를 만들려면 **CD\\\\샌드박스 ent\_서버\_dockerfiles\_4.0\_창\\예제\\CICS를**입력하여 CICS 디렉토리로 전환합니다.

6. 이미지를 만들려면 **bld.bat x64를**입력합니다. 프로세스가 실행되고 이미지가 생성되었다는 메시지가 표시됩니다.

7. **도커 이미지를** 입력하여 VM에 설치된 모든 Docker 이미지 목록을 표시합니다. 마이크로 **포커스 / es-acctdemo가** 그 중 하나인지 확인하십시오.

     ![es-acctdemo 이미지를 표시하는 명령 창](media/container-05.png)

## <a name="run-the-image"></a>이미지 실행 

1.  엔터프라이즈 서버 4.0 및 acctdemo 응용 프로그램을 명령 프롬프트 유형에서 시작하려면 다음을 수행하십시오.

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  [x3270과](http://x3270.bgp.nu/) 같은 3270 터미널 에뮬레이터를 설치하고 포트 9040을 통해 실행 중인 이미지에 연결하는 데 사용합니다.

3.  Acctdemo 컨테이너의 IP 주소를 가져옵니다.

    1.  실행 중인 컨테이너의 ID를 가져옵니다. 명령 프롬프트에서 **Docker ps를** 입력하고 ID(이 예제에서는**22a0fe3159d0)를** 기록합니다. 다음 단계를 위해 저장합니다.

    2.  acctdemo 컨테이너의 IP 주소를 얻으려면 이전 단계의 컨테이너 ID를 다음과 같이 사용하십시오.

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       예를 들어:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. acctdemo 이미지의 IP 주소를 기록합니다. 예를 들어 다음 출력의 주소는 172.19.202.52입니다.

     ![IP 주소를 표시하는 명령 창](media/container-06.png)

5. 에뮬레이터를 사용하여 이미지를 마운트합니다. acctdemo 이미지 및 포트 9040의 주소를 사용하도록 에뮬레이터를 구성합니다. 여기, 그것은 **172.19.202.52:9040입니다.** 당신은 비슷할 것입니다. **CICS에 서명 화면이** 열립니다.

    ![CICS 화면으로 서명](media/container-07.png)

6. **USERID**및 **SYSAD에** 대한 **암호를** 위해 **SYSAD를** 입력하여 CICS 지역에 로그인합니다.

7. 에뮬레이터의 키맵을 사용하여 화면을 지웁습니다. x3270의 경우 **키맵** 메뉴 옵션을 선택합니다.

8. acctdemo 응용 프로그램을 실행하려면 **ACCT를**입력합니다. 응용 프로그램의 초기 화면이 표시됩니다.

     ![계정 데모 화면](media/container-08.png)

9. 표시 계정 유형을 실험해 보입니다. 예를 들어 요청에 대한 **D를** 입력하고 **계정에**대해 **11111을** 입력합니다. 시도할 다른 계정 번호는 22222, 33333 등입니다.

     ![계정 데모 화면](media/container-09.png)

10. 엔터프라이즈 서버 관리 콘솔을 표시하려면 명령 프롬프트로 이동하여 **시작 http:172.19.202.52:86을** 입력합니다.

     ![엔터프라이즈 서버 관리 콘솔](media/container-010.png)

이것으로 끝입니다. 이제 Docker 컨테이너에서 CICS 응용 프로그램을 실행하고 관리하고 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure에 마이크로 포커스 엔터프라이즈 서버 4.0 및 엔터프라이즈 개발자 4.0 설치](./set-up-micro-focus-azure.md)
- [메인프레임 애플리케이션 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
