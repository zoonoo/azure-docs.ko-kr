---
title: Azure Stack 빠른 시작-VM 만들기 포털
description: Azure Stack 빠른 시작-포털을 사용 하 여 Linux VM 만들기
services: azure-stack
cloud: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: c364a9ba5a9606967fa86262417ccd1ac3aeffc3
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866273"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>빠른 시작: Azure Stack 포털을 사용 하 여 Linux 서버 가상 머신 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 포털을 사용 하 여 Ubuntu Server 16.04 LTS 가상 컴퓨터를 만들 수 있습니다. 만들고 가상 머신을 사용 하려면이 문서의 단계를 수행 합니다. 이 문서에서는 단계를 제공 합니다.

* 원격 클라이언트를 사용 하 여 가상 컴퓨터에 연결 합니다.
* NGINX 웹 서버를 설치 합니다.
* 리소스를 정리 합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure Stack marketplace에서 Linux 이미지**

   Azure Stack marketplace에는 기본적으로 Linux 이미지를 포함 하지 않습니다. Linux 서버 가상 컴퓨터를 만들려면 먼저 Azure Stack 연산자를 제공 하는지 확인 합니다 **Ubuntu Server 16.04 LTS** 이미지 해야 합니다. 연산자에 설명 된 단계를 사용할 수는 [Azure에서 Azure Stack marketplace 항목 다운로드](../azure-stack-download-azure-marketplace-item.md) 문서.

* **SSH 클라이언트에 대 한 액세스**

   Azure Stack 개발 키트 (ASDK)를 사용 하는 경우 SSH 클라이언트에 대 한 액세스가 없을 수 있습니다. 클라이언트에 필요한 경우에 SSH 클라이언트를 포함 하는 여러 개의 패키지가 있습니다. 예를 들어, PuTTY SSH 클라이언트 및 SSH 키 생성기 (puttygen.exe)를 포함 합니다. 사용 가능한 패키지에 대 한 자세한 내용은 다음 Azure 문서를 참조 하세요. [Azure에서 Windows 사용 하 여 SSH 키를 사용 하는 방법을](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients)합니다.

   이 빠른 시작에서는 SSH 키를 생성 하 고 Linux server 가상 머신에 연결할 PuTTY를 사용 합니다. PuTTY 다운로드 및 설치로 이동 [ http://www.putty.org/ ](http://www.putty.org)합니다.

## <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기

SSH 키 쌍이 문서의 모든 단계를 완료 해야 합니다. 기존 SSH 키 쌍에 있는 경우이 단계를 건너뛸 수 있습니다.

1. PuTTY 설치 폴더로 이동 (기본 위치는 ```C:\Program Files\PuTTY```) 및 실행 ```puttygen.exe```합니다.
2. PuTTY 키 생성기 창에서 확인 합니다 **생성할 키의 형식** 로 설정 된 **RSA**, 및 **생성 된 키의 비트 수** 로 설정 된 **2048**. 준비 되 면, 클릭 **생성**합니다.

   ![PuTTY 키 생성기 구성](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. 키를 생성 하려면 마우스 커서를 PuTTY 키 생성기 창에서 임의로 이동 합니다.
4. 키 생성이 완료 되 면 클릭 **공개 키 저장** 클릭 하 고 **개인 키 저장** 키 파일에 저장 합니다.

   ![PuTTY 키 생성기 결과](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Azure Stack 포털에 로그인

Azure Stack 포털에 로그인 합니다. Azure Stack 포털의 주소는 Azure Stack 제품에 연결 하는 달라 집니다.

* 에 대 한 Azure Stack 개발 키트 ASDK ()로 이동: https://portal.local.azurestack.external합니다.
* Azure Stack 통합 시스템, 귀하가 Azure Stack 운영자가 제공한 URL로 이동 합니다.

## <a name="create-the-virtual-machine"></a>가상 머신 만들기

1. 클릭 **리소스 만들기** Azure Stack 포털의 왼쪽 위 모서리에 있습니다.

2. **Compute**를 선택한 후 **Ubuntu Server 16.04 LTS**를 선택합니다.
3. **만들기**를 클릭합니다.

4. 가상 머신 정보를 입력 합니다. **인증 유형**으로 **SSH 공용 키**를 선택합니다. 클릭 하 여 저장 하는 SSH 공개 키를 붙여 넣습니다 **확인**합니다.

   >[!NOTE]
 키에 대 한 모든 선행 또는 후행 공백을 제거 해야 합니다.

   ![기본 패널-가상 컴퓨터를 구성 합니다.](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. 선택 **D1_V2** 가상 머신에 대 한 합니다.

   ![패널 크기-가상 머신 크기 선택](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. 에 **설정을** 페이지에서 기본값을 그대로 유지 하 고 클릭 **확인**합니다.

7. 에 **요약** 페이지에서 클릭 **확인** 가상 머신 배포를 시작 합니다.

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

1. 클릭 **Connect** 가상 컴퓨터 페이지입니다. 이 가상 머신에 연결 해야 하는 SSH 연결 문자열을 표시 합니다.

   ![가상 컴퓨터 연결](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. PuTTY를 엽니다.
3. 에 **PuTTY 구성** 사용 하 여 화면을 **범주** 위로 또는 아래로 스크롤해야 할 합니다. 아래로 스크롤하여 **SSH**, 확장 **SSH**를 클릭 하 고 **Auth**합니다. 클릭 **찾아보기** 저장 된 개인 키 파일을 선택 합니다.

   ![PuTTY 개인 키를 선택 합니다.](media/azure-stack-quick-linux-portal/Putty03.PNG)

4. 위로 스크롤 합니다 **범주** 창에서 마우스 클릭 **세션**합니다.
5. 에 **호스트 이름 (또는 IP 주소)** 상자에 Azure Stack 포털에 표시 되는 연결 문자열을 붙여넣습니다. 이 예제에서는 문자열은 ```asadmin@192.168.102.34```합니다.

   ![PuTTY 구성 연결 문자열](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. 클릭 **엽니다** 가상 머신에 대 한 세션을 엽니다.

   ![Linux 세션](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>NGINX 웹 서버 설치

다음 bash 명령을 사용 하 여 패키지 소스를 업데이트 하 고 가상 머신에서 최신 NGINX 패키지를 설치 합니다.

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

NGINX를 설치를 완료 하면 SSH 세션을 닫고 Azure Stack 포털에 가상 컴퓨터 개요 페이지를 엽니다.

## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기

NSG(네트워크 보안 그룹)는 인바운드 및 아웃바운드 트래픽의 보안을 유지합니다. Azure Stack 포털에 가상 컴퓨터를 만들면 SSH 연결에 대 한 포트 22에 인바운드 규칙이 만들어집니다. 이 가상 머신에 웹 서버를 호스트 하기 때문에 포트 80에서 웹 트래픽을 허용 하기 위해 NSG 규칙을 만들어야 합니다.

1. 가상 머신에서 **개요** 페이지에서 이름을 클릭 합니다 **리소스 그룹**합니다.
2. 선택 된 **네트워크 보안 그룹** 가상 머신에 대 한 합니다. NSG는 **형식** 열을 사용하여 식별할 수 있습니다.
3. 왼쪽 메뉴에서 아래 **설정을**, 클릭 **인바운드 보안 규칙**합니다.
4. **추가**를 클릭합니다.
5. **이름**에서 **http**를 입력합니다. **포트 범위**를 80으로 설정하고 **작업**을 **허용**으로 설정해야 합니다.
6. **확인**

## <a name="view-the-nginx-welcome-page"></a>NGINX 시작 페이지 보기

NGINX를 설치 및 포트 80 열어서 가상 머신에서 사용 하 여 가상 머신의 공용 IP 주소를 사용 하 여 웹 서버를 액세스할 수 있습니다. (공용 IP 주소는 가상 머신의 개요 페이지에 표시 됩니다.)

웹 브라우저를 열고 ```http://<public IP address>```합니다.

![NGINX 웹 서버에 대 한 시작 페이지](media/azure-stack-quick-linux-portal/linux-04.PNG)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않은 리소스를 정리 합니다. 가상 컴퓨터 및 해당 리소스를 삭제 하려면 가상 컴퓨터 페이지의 리소스 그룹을 선택 하 고 클릭 **삭제**합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 웹 서버를 사용 하 여 기본 Linux server 가상 머신을 배포 합니다. Azure Stack virtual machines에 대 한 자세한 내용은 계속 [Azure Stack에서 Virtual Machines에 대 한 고려 사항](azure-stack-vm-considerations.md)합니다.
