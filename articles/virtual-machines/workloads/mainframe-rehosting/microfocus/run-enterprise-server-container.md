---
title: Azure의 Docker 컨테이너에서 마이크로 포커스 엔터프라이즈 서버 5.0 실행 Microsoft Docs
description: 이 문서에서는 Microsoft Azure의 Docker 컨테이너에서 마이크로 포커스 Enterprise Server 5.0를 실행 하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 3c4afc6c0adb8d499e38abf3d709a951774dda4e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90974061"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>Azure의 Docker 컨테이너에서 마이크로 포커스 엔터프라이즈 서버 5.0 실행

Azure의 Docker 컨테이너에서 마이크로 포커스 엔터프라이즈 서버 5.0을 실행할 수 있습니다. 이 자습서에서는 방법을 보여 줍니다. 엔터프라이즈 서버용 Windows CICS (고객 정보 컨트롤 시스템) acctdemo 데모를 사용 합니다.

Docker는 응용 프로그램에 이식성 및 격리를 추가 합니다. 예를 들어, 다른 Windows VM (가상 머신)에서 실행 하거나 Docker를 사용 하는 Windows server에 대 한 저장소에서 Docker 이미지를 내보낼 수 있습니다. Docker 이미지는 엔터프라이즈 서버를 설치할 필요 없이 동일한 구성으로 새 위치에서 실행 됩니다. 이미지의 일부입니다. 라이선스 고려 사항은 여전히 적용 됩니다.

이 자습서에서는 Azure Marketplace의 **컨테이너 VM을 사용 하 여 Windows 2016 Datacenter** 를 설치 합니다. 이 VM은 **Docker 18.09.0**를 포함 합니다. 다음 단계는 컨테이너를 배포 하 고 실행 한 다음 3270 에뮬레이터를 사용 하 여 연결 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

시작 하기 전에 다음 필수 구성 요소를 확인 하세요.

-   Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

-   마이크로 포커스 소프트웨어 및 유효한 라이선스 (또는 평가판 라이선스) 기존 마이크로 포커스 고객 인 경우 마이크로 포커스 담당자에 게 문의 하세요. 그렇지 않으면 [평가판을 요청](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)합니다.

    > [!Note] 
    > Docker 시연 파일은 Enterprise Server 5.0에 포함 되어 있습니다. 이 자습서에서는 ent \_ 서버 \_ dockerfile \_ 5.0 \_windows.zip를 사용 합니다. 엔터프라이즈 서버 설치 파일에 액세스 한 것과 동일한 위치에서 액세스 하거나 *마이크로 포커스* 로 이동 하 여 시작 합니다.

-   [엔터프라이즈 서버 및 엔터프라이즈 개발자](https://www.microfocus.com/documentation/enterprise-developer/#%22)를 위한 설명서입니다.

## <a name="create-a-vm"></a>VM 만들기

1.  Ent \_ 서버 \_ dockerfile \_ 5.0 \_windows.zip 파일에서 미디어를 보호 합니다. Docker 이미지를 작성 하는 데 필요한, mflic-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 라이선스 파일을 보호 합니다.

2.  VM을 만듭니다. 이렇게 하려면 Azure Portal을 열고 왼쪽 상단 메뉴에서 **리소스 만들기** 를 선택 하 고 *windows server 운영 체제*를 기준으로 필터링 합니다. 결과에서 **Windows Server를 선택 합니다.** 다음 화면에서 **컨테이너를 사용 하 여 Windows Server 2016 Datacenter –** 를 선택 합니다.

    ![Azure Portal 검색 결과의 스크린샷](./media/run-image-1.png)

3.  VM에 대 한 속성을 구성 하려면 인스턴스 정보를 선택 합니다.

    1.  VM 크기를 선택합니다. 이 자습서에서는 2 개 vCPUs와 16gb의 메모리로 **표준 d s 2 \_ v3** VM을 사용 하는 것이 좋습니다.

    2.  배포 하려는 **지역** 및 **리소스 그룹** 을 선택 합니다.

    3.  **가용성 옵션**에 대해서는 기본 설정을 사용 합니다.

    4.  **사용자 이름**에 사용 하려는 관리자 계정과 암호를 입력 합니다.

    5.  **포트 3389 RDP** 가 열려 있는지 확인 합니다. 이 포트만 공개적으로 노출 해야 하므로 VM에 로그인 할 수 있습니다. 그런 다음 모든 기본값을 그대로 적용 하 고 **검토 + 만들기**를 클릭 합니다.

    ![가상 컴퓨터 창 만들기의 스크린샷](./media/run-image-2.png)

4.  배포가 완료 될 때까지 기다립니다 (몇 분). VM이 생성 되었음을 알리는 메시지가 있습니다.

5.  **리소스로 이동** 을 선택 하 여 VM의 **개요** 블레이드로 이동 합니다.

6.  오른쪽에서 **연결**을 선택 합니다. **가상 컴퓨터에 연결** 옵션이 오른쪽에 나타납니다.

7.  **Rdp 파일 다운로드** 단추를 선택 하 여 VM에 연결할 수 있는 rdp (원격 데스크톱 프로토콜) 파일을 다운로드 합니다.

8.  파일 다운로드가 완료 되 면 파일을 열고 VM에 대해 만든 사용자 이름 및 암호를 입력 합니다.

    > [!Note]    
    > 회사 자격 증명을 사용 하 여 로그인 하지 마세요. (RDP 클라이언트에서 사용 하려는 것으로 가정 합니다. 그렇지 않습니다.)

9.  **더 많은 옵션**을 선택한 다음, VM 자격 증명을 선택 합니다.

이 시점에서 VM은 RDP를 통해 실행 되 고 연결 됩니다. 사용자가 로그인 하 고 다음 단계를 진행할 준비가 되었습니다.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>샌드박스 디렉터리를 만들고 zip 파일을 업로드 합니다.

1.  데모 및 라이선스 파일을 업로드할 수 있는 VM에 디렉터리를 만듭니다. 예를 들어 **C: \\ Sandbox**입니다.

2.  만든 디렉터리에 **ent \_ 서버 \_ dockerfile \_ 5.0 \_windows.zip** 및 **ES-Docker-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx** 파일을 업로드 합니다.

3.  Zip 파일의 내용을 추출 프로세스에서 만든 **ent \_ 서버 \_ dockerfile \_ 5.0 \_ windows** 디렉터리에 추출 합니다. 이 디렉터리에는 추가 정보 파일 (.html 및 .txt 파일) 및 두 개의 하위 디렉터리 **Enterpriseserver** 와 **예제가**포함 되어 있습니다.

4.  **ES-Docker-Prod-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx** 를 C: \\ sandbox \\ ent \_ 서버 \_ dockerfile \_ 5.0 \_ windows \\ enterpriseserver 및 C: \\ sandbox \\ ent \_ 서버 \_ dockerfile \_ 5.0 \_ windows \\ 예제 \\ cics 디렉터리에 복사 합니다.  
      
    > [!Note]
    > 라이선스 파일을 두 디렉터리에 모두 복사 해야 합니다. 이미지가 올바르게 사용이 허가 되었는지 확인 하기 위해 Docker 빌드 단계에 필요 합니다.

## <a name="check-docker-version-and-create-base-image"></a>Docker 버전 확인 및 기본 이미지 만들기

> [!Important]  
> 적절 한 Docker 이미지를 만드는 과정은 2 단계 프로세스입니다. 먼저 Enterprise Server 5.0 기본 이미지를 만듭니다. 그런 다음 x64 플랫폼용으로 다른 이미지를 만듭니다. X86 (32 비트) 이미지를 만들 수 있지만 64 비트 이미지를 사용 합니다.

1.  명령 프롬프트를 엽니다.

2.  Docker가 설치 되어 있는지 확인 합니다. 명령 프롬프트에서 **docker 버전** 을 입력 합니다.  
    예를 들어이를 쓸 때 버전은 18.09.0입니다.

3.  디렉터리를 변경 하려면 다음을 입력 합니다.  
    **cd \\ Sandbox \\ \_ 서버 \_ dockerfile \_ 5.0 \_ windows \\ enterpriseserver**

4.  **bld.bat IacceptEULA** 를 입력 하 여 초기 기본 이미지에 대 한 빌드 프로세스를 시작 합니다. 이 프로세스를 실행 하는 데 몇 분 정도 기다립니다. 결과에서 생성 된 두 이미지 (x 64 용 및 x 86 용)를 확인 합니다.

    ![이미지를 표시 하는 명령 창](./media/run-image-3.png)

5.  Cics 데모에 대 한 최종 이미지를 만들려면 **cd \\ 샌드박스 \\ ent \_ 서버 \_ dockerfile \_ 5.0 \_ windows \\ 예제 \\ cics**를 입력 하 여 cics 디렉터리로 전환 합니다.

6.  이미지를 만들려면 **bld.bat x 64**를 입력 합니다. 프로세스가 실행 될 때까지 몇 분 정도 기다린 후 이미지가 생성 되었다는 메시지가 나타나는 경우

7.  **Docker 이미지** 를 입력 하 여 VM에 설치 된 모든 docker 이미지 목록을 표시 합니다. **마이크로 포커스/es-acctdemo** 가 그 중 하나 인지 확인 합니다.

    ![Es-acctdemo 이미지를 표시 하는 명령 창](./media/run-image-4.png)

## <a name="run-the-image"></a>이미지 실행

1.  엔터프라이즈 서버 5.0 및 acctdemo 응용 프로그램을 시작 하려면 명령 프롬프트에서 다음을 입력 합니다.

    ~~~
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ~~~

1.  [X3270](http://x3270.bgp.nu/) 와 같은 3270 터미널 에뮬레이터를 설치 하 고 포트 9040를 통해를 실행 하는 이미지에 연결 하는 데 사용 합니다.

2.  Docker가 관리 하는 컨테이너에 대 한 DHCP (Dynamic Host Configuration Protocol) 서버 역할을 할 수 있도록 acctdemo 컨테이너의 IP 주소를 가져옵니다.

    1.  실행 중인 컨테이너의 ID를 가져옵니다. 명령 프롬프트에서 **Docker ps** 를 입력 하 고 ID (이 예제에서는**22a0fe3159d0** )를 확인 합니다. 다음 단계를 위해 저장 합니다.

    2.  Acctdemo 컨테이너의 IP 주소를 가져오려면 다음과 같이 이전 단계의 컨테이너 ID를 사용 합니다.

    ~~~
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ~~~

    다음은 그 예입니다. 

    ~~~
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ~~~

4. Acctdemo 이미지에 대 한 IP 주소를 적어둡니다. 예를 들어 다음 출력의 주소는 172.19.202.52입니다.

    ![IP 주소를 보여 주는 명령 창 스크린샷](./media/run-image-5.png)

5. 에뮬레이터를 사용 하 여 이미지를 탑재 합니다. Acctdemo 이미지 및 포트 9040의 주소를 사용 하도록 에뮬레이터를 구성 합니다. **172.19.202.52:9040**입니다. 이는 유사 합니다. **CICS에 로그온** 화면이 열립니다.

    ![CICS에 대 한 Signon 스크린샷](./media/run-image-6.png)

6. **USERID** 에 대해 **sysad** 를 입력 하 고 **암호**에 **sysad** 를 입력 하 여 cics 지역에 로그인 합니다.

7. 에뮬레이터의 keymap을 사용 하 여 화면을 지웁니다. X3270의 경우 **Keymap** 메뉴 옵션을 선택 합니다.

8. Acctdemo 응용 프로그램을 시작 하려면 **계정**을 입력 합니다. 응용 프로그램의 초기 화면이 표시 됩니다.

     ![계정 데모 스크린샷](./media/run-image-7.png)

9. 표시 계정 유형을 실험 합니다. 예를 들어 요청에 대해 **D** 를 입력 하 고 **계정**에 **11111** 을 입력 합니다. 시도할 다른 계정 번호는 22222, 33333 등입니다.

    ![계정 데모 스크린샷](./media/run-image-8.png)

10. 엔터프라이즈 서버 관리 콘솔을 표시 하려면 명령 프롬프트로 이동 하 고 **start http: 172.19.202.52:86**을 입력 합니다.

    ![엔터프라이즈 서버 관리 콘솔](media/run-image-9.png)

이것으로 끝입니다. 이제 Docker 컨테이너에서 CICS 응용 프로그램을 실행 하 고 관리 하 고 있습니다.

## <a name="next-steps"></a>다음 단계

-   [Azure에서 마이크로 포커스 엔터프라이즈 서버 5.0 및 Enterprise Developer 5.0 설치](./set-up-micro-focus-azure.md)

-   [메인프레임 애플리케이션 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
