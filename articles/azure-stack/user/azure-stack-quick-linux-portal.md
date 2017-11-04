---
title: "Azure 빠른 시작 스택-포털 VM 만들기"
description: "Azure 스택 빠른 시작-포털을 사용 하 여 Linux VM 만들기"
services: azure-stack
cloud: azure-stack
author: vhorne
manager: byronr
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 5f815bafdcc7c05ec7f4149fb7c9df178f0f80e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-stack-portal"></a>Azure 스택 포털과 Linux 가상 컴퓨터 만들기

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 포털을 통해 azure 스택 가상 컴퓨터를 만들 수 있습니다. 이 메서드를 만들고 가상 컴퓨터 구성 브라우저 기반 사용자 인터페이스를 제공 하 고 모든 관련 리소스입니다. 이 퀵 스타트의 신속 하 게 Linux 가상 컴퓨터를 만들고 웹 서버에 설치 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* **Azure 스택 마켓플레이스에서 Linux 이미지**

   Azure 스택 마켓플레이스 기본적으로 Linux 이미지를 포함 하지 않습니다. 따라서 Linux 가상 컴퓨터를 만들기 전에 확인 Azure 스택 연산자가 다운로드는 **Ubuntu Server 16.04 LT** 에 설명 된 단계를 사용 하 여 이미지는 [Azure에서 Azure 마켓플레이스 항목을 다운로드 스택](../azure-stack-download-azure-marketplace-item.md) 항목입니다.

* **SSH 클라이언트에 대 한 액세스**

   Azure 스택 개발 키트 (ASDK)를 사용 하는 경우 없을 수 있습니다 SSH 클라이언트에 대 한 액세스 사용자 환경에서 합니다. 이 경우 SSH 클라이언트를 포함 하는 여러 패키지 간에 선택할 수 있습니다. 예를 들어 SSH 클라이언트 및 SSH 키 생성기 (puttygen.exe)를 포함 하는 PuTTY를 설치할 수 있습니다. 가능한 옵션에 대 한 자세한 내용은 다음 관련 Azure 문서 참조: [windows Azure에서 사용 하 여 SSH 키 하는 방법](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients)합니다.

   이 퀵 스타트의 SSH 키를 생성 하 고 Linux 가상 컴퓨터에 연결 하려면 PuTTY를 사용 합니다. 을 다운로드 및 설치 PuTTY 하려면로 이동 [http://www.putty.org/](http://www.putty.org)합니다.

## <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기

SSH 키 쌍이 빠른이 시작을 완료 해야 합니다. 기존 SSH 키 쌍을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

1. PuTTY 설치 폴더로 이동 (기본 위치는 ```C:\Program Files\PuTTY```) 및 실행 ```puttygen.exe```합니다.
2. PuTTY 키 생성기 창에서 확인 하십시오.는 **생성 하는 키의 유형** 로 설정 된 **RSA**, 및 **생성 된 키의 비트 수** 로 설정 된 **2048**. 준비 되 면 **생성**합니다.

   ![puttygen.exe](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. 키 생성 프로세스를 완료 하려면 PuTTY 키 생성기 창 내에서 마우스 커서를 이동 합니다.
4. 키 생성 완료 되 면 클릭 **저장 공개 키** 및 **개인 키 저장** 파일에 공개 및 개인 키를 저장할 수 있습니다.

   ![PuTTY 키](media/azure-stack-quick-linux-portal/Putty02.PNG)



## <a name="sign-in-to-the-azure-stack-portal"></a>Azure 스택 포털에 로그인

Azure 스택 포털에 로그인 합니다. Azure 스택 포털의 주소에 따라 Azure 스택 제품에 연결 하는 다릅니다.

* Azure 스택 개발 키트 (ASDK)에 대 한 이동: https://portal.local.azurestack.external 합니다.
* Azure 스택 통합 하는 시스템에 대 한 Azure 스택 연산자를 제공 하는 URL로 이동 합니다.

## <a name="create-the-virtual-machine"></a>가상 컴퓨터 만들기

1. 클릭는 **새로** 스택 Azure 포털의 왼쪽 위 모서리에서 단추를 찾을 수 있습니다.

2. **Compute**를 선택한 후 **Ubuntu Server 16.04 LTS**를 선택합니다.
3. **만들기**를 클릭합니다.

4. 가상 컴퓨터 정보를 입력 합니다. **인증 유형**으로 **SSH 공용 키**를 선택합니다. 선행 또는 후행 공백을 모두 제거 하 여 SSH 공개 키 (파일에 이전에 저장)에 붙여 넣을 때 주의 합니다. 완료되면 **확인**을 클릭합니다.

   ![가상 컴퓨터 기본 사항](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. 선택 **D1_V2** 가상 컴퓨터에 대 한 합니다.

   ![컴퓨터 크기](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. 에 **설정** 페이지에서 기본값을 유지 하 고 클릭 **확인**합니다.

7. 에 **요약** 페이지 **확인** 가상 컴퓨터 배포를 시작 합니다.


## <a name="connect-to-the-virtual-machine"></a>가상 컴퓨터에 연결

1. 클릭 **연결** 가상 컴퓨터 페이지. 이 가상 컴퓨터에 연결 하는 데 사용할 수 있는 SSH 연결 문자열을 표시 합니다.

   ![가상 컴퓨터에 연결](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. PuTTY를 엽니다.
3. 에 **PuTTY 구성** 화면의 **범주**를 확장 하 고 **SSH** 클릭 하 고 **Auth**합니다. 클릭 **찾아보기** 하 고 이전에 저장 하는 개인 키 파일을 선택 합니다.

   ![PuTTY 개인 키](media/azure-stack-quick-linux-portal/Putty03.PNG)
4. 아래 **범주**, 위로 스크롤 및 클릭 **세션**합니다.
5. 에 **호스트 이름 (또는 IP 주소)** 상자에서 이전에 본 스택 Azure 포털에서 연결 문자열을 붙여 넣습니다. 이 예제에서는 문자열은 ```asadmin@192.168.102.34```합니다.
 
   ![PuTTY 세션](media/azure-stack-quick-linux-portal/Putty04.PNG)
6. 클릭 **열고** 가상 컴퓨터에 세션을 엽니다.

   ![Linus 세션](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-nginx"></a>NGINX 설치

패키지 소스를 업데이트 하 고 가상 컴퓨터에 최신 NGINX 패키지를 설치 하려면 다음 bash 스크립트를 사용 합니다. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

을 완료 한 후 SSH 세션을 종료 하 고 Azure 스택 포털에서 가상 컴퓨터 개요 페이지를 반환 합니다.


## <a name="open-port-80-for-web-traffic"></a>웹 트래픽에 대해 포트 80 열기 

NSG(네트워크 보안 그룹)는 인바운드 및 아웃바운드 트래픽의 보안을 유지합니다. Azure 스택 포털에서 가상 컴퓨터를 만들면는 인바운드 규칙 SSH 연결에 대 한 포트 22에 만들어집니다. 이 가상 컴퓨터는 웹 서버를 호스트 하기 때문에 포트 80에 대해 생성 될 NSG 규칙이 필요 합니다.

1. 가상 컴퓨터에서 **개요** 페이지의 이름을 클릭 하 여 **리소스 그룹**합니다.
2. 선택 된 **네트워크 보안 그룹** 가상 컴퓨터에 대 한 합니다. NSG는 **형식** 열을 사용하여 식별할 수 있습니다. 
3. 왼쪽 메뉴에서 **설정**, 클릭 **인바운드 보안 규칙**합니다.
4. **추가**를 클릭합니다.
5. **이름**에서 **http**를 입력합니다. **포트 범위**를 80으로 설정하고 **작업**을 **허용**으로 설정해야 합니다. 
6. **확인**을 클릭합니다.


## <a name="view-the-nginx-welcome-page"></a>NGINX 시작 페이지 보기

웹 서버 설치 NGINX 및 포트 80 가상 컴퓨터에서 열려 가상 컴퓨터의 공용 IP 주소에 액세스할 수 이제 수 있습니다. Azure 스택 포털에서 가상 컴퓨터의 개요 페이지에서 공용 IP 주소를 찾을 수 있습니다.

웹 브라우저를 열고 ```http://<public IP address>```합니다.

![NGINX 기본 사이트](media/azure-stack-quick-linux-portal/linux-04.PNG)


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않을 때 리소스 그룹, 가상 컴퓨터 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 가상 컴퓨터 페이지에서 리소스 그룹을 선택 하 고 클릭 **삭제**합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 간단한 Linux 가상 컴퓨터를 네트워크 보안 그룹 규칙을 배포 하 고 웹 서버를 설치 합니다. Azure 스택 가상 컴퓨터에 대 한 자세한 내용은 계속 [스택 Azure의에서 가상 컴퓨터에 대 한 고려 사항](azure-stack-vm-considerations.md)합니다.

