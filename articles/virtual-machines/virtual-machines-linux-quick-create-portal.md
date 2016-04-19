<properties
    pageTitle="Azure 포털을 사용하여 Linux VM 만들기 | Microsoft Azure"
    description="Azure 포털을 사용하여 Linux VM을 만듭니다."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="04/05/2016"
    ms.author="v-livech"
/>

# Azure 포털을 사용하여 Linux VM 만들기

이 문서에서는 아무것도 설치하지 않고 [Azure 포털](https://portal.azure.com/)을 사용하여 Linux VM을 즉시 만드는 방법을 보여 줍니다. 유일한 요구 사항은 [Azure 계정](https://azure.microsoft.com/pricing/free-trial/) 및 [SSH 공용 및 개인 키 파일](virtual-machines-linux-mac-create-ssh-keys.md)입니다.


1. Azure 계정 ID를 사용하여 Azure 포털에 로그인하고 왼쪽 위에서 **+ 새로 만들기**를 클릭합니다.

    ![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. **마켓플레이스**에서 **가상 컴퓨터**를 클릭한 후 **추천 앱** 이미지 목록에서 **Ubuntu Server 14.04 LTS**를 클릭하면 다음 화면이 표시됩니다.

    ![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. 맨 아래에서 배포 모델이 `Resource Manager`인지 확인하고 **만들기**를 클릭합니다.

4. **기본** 페이지에서 다음을 입력합니다.
    - VM의 이름
    - 관리 사용자의 사용자 이름
    - 인증 유형이 **SSH 공개 키**로 설정됨
    - 문자열로 SSH 공개 키(기본적으로 `~/.ssh/` 디렉터리에서)
    - 리소스 그룹 이름(새 배포 그룹 만들기) 또는 기존 그룹 선택

    계속하려면 **확인**을 클릭하고 VM 크기를 선택합니다. 다음과 같이 표시됩니다.

    ![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

5. Premium SSD에 Ubuntu를 설치하는 **DS1** 크기를 선택하고 **선택**을 클릭하여 설정을 구성합니다.

    ![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

6. **설정**에서 저장소 및 네트워크에 대한 기본값을 그대로 두고 **확인**을 클릭하여 요약을 봅니다.

    ![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

7. 새로운 Ubuntu VM에 대한 설정을 확인하고 **확인**을 클릭합니다.

    ![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

8. 포털 대시보드를 열고 **네트워크 인터페이스**에서 NIC를 선택합니다.

    ![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

9. NIC 설정 아래에서 공용 IP 주소 메뉴를 엽니다.

    ![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

10. SSH 공개 키를 사용하여 공용 IP로 SSH합니다.

```
user@slackware$ ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## 다음 단계

원하는 경우 계속해서 [디스크를 추가](virtual-machines-linux-add-disk.md)합니다.

<!---HONumber=AcomDC_0413_2016-->