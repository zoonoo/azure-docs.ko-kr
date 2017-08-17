---
title: "Azure의 Linux(Ubuntu) VM에서 첫 번째 Jenkins 마스터 만들기"
description: "솔루션 템플릿을 활용하여 Jenkins를 배포합니다."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: multiple
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 80fd9ee9b9de5c7547b9f840ac78a60d52153a5a
ms.openlocfilehash: 06d6d305eb9711768dc62a04726359e6280d1b69
ms.contentlocale: ko-kr
ms.lasthandoff: 08/14/2017

---

# <a name="create-your-first-jenkins-master-on-a-linux-ubuntu-vm-on-azure"></a>Azure의 Linux(Ubuntu) VM에서 첫 번째 Jenkins 마스터 만들기

이 빠른 시작은 Linux(Ubuntu 14.04 LTS) VM에 Azure와 함께 작동하도록 구성된 도구 및 플러그 인과 함께 안정적인 최신 버전인 Jenkins를 설치하는 방법을 보여 줍니다. 도구로는 다음이 있습니다.
<ul>
<li>소스 제어를 위한 Git</li>
<li>안전하게 연결하기 위한 Azure 자격 증명 플러그 인</li>
<li>탄력적인 빌드, 테스트 및 연속 통합을 위한 Azure VM 에이전트 플러그 인</li>
<li>아티팩트 저장을 위한 Azure Storage 플러그 인</li>
<li>스크립트를 사용하여 앱을 배포하기 위한 Azure CLI</li>
</ul>

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 평가판 Azure 계정을 만듭니다.
> * 솔루션 템플릿을 사용하여 Azure VM에 Jenkins 마스터를 만듭니다. 
> * Jenkins에 대한 초기 구성을 수행합니다.
> * 제안된 플러그 인을 설치합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-the-vm-in-azure-by-deploying-the-solution-template-for-jenkins"></a>Jenkins에 대한 솔루션 템플릿을 배포하여 Azure에서 VM 만들기

Azure 빠른 시작 템플릿을 사용하여 Azure에 복잡한 기술을 빠르고 안정적으로 배포할 수 있습니다.  Azure Resource Manager를 사용하면 [선언적 템플릿](https://azure.microsoft.com/en-us/resources/templates/?term=jenkins)을 통해 응용 프로그램을 프로비전할 수 있습니다. 단일 템플릿에서 여러 서비스를 해당 종속성과 함께 배포할 수 있습니다. 동일한 템플릿을 사용하여 응용 프로그램 수명 주기의 각 단계 중에 응용 프로그램을 반복해서 배포합니다.

이 템플릿에 대한 [계획 및 가격](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview) 정보를 보고 비용 옵션을 파악합니다.

[Jenkins에 대한 마켓플레이스 이미지](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview)로 이동하여 **지금 가져오기**를 클릭합니다.  

Azure Portal에서 **만들기**를 클릭합니다.  이 템플릿에서는 템플릿 모델 드롭다운을 사용하지 않도록 리소스 관리자를 사용해야 합니다.
   
![Azure Portal 대화 상자](./media/install-jenkins-solution-template/ap-create.png)

**기본 설정 구성** 탭에서:

![기본 설정 구성](./media/install-jenkins-solution-template/ap-basic.png)

* Jenkins 인스턴스에 이름을 제공합니다.
* VM 디스크 유형을 선택합니다.  프로덕션 워크로드의 경우 성능 향상을 위해 더 큰 VM과 SSD를 선택합니다.  Azure 디스크 유형에 대한 자세한 내용은 [여기](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage)서 확인할 수 있습니다.
* 사용자 이름: 길이 요구 사항을 충족해야 하며 예약어나 지원되지 않는 문자는 포함하지 않아야 합니다. "admin"과 같은 이름은 허용되지 않습니다.  사용자 이름 및 암호 요구 사항에 대한 자세한 내용은 [여기](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq)를 참조하세요.
* 인증 유형: 암호 또는 [SSH 공개 키](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows)로 보호되는 인스턴스를 만듭니다. 암호를 사용하는 경우 1개의 소문자, 1개의 대문자, 1개의 대문자, 1개의 숫자 및 1개의 특수 문자 요구 사항 중 세 가지를 충족해야 합니다.
* Jenkins 릴리스 형식을 **LTS**로 유지합니다.
* 구독을 선택합니다.
* 리소스 그룹을 만들거나 비어 있는 기존 리소스 그룹을 사용합니다. 
* 위치를 선택합니다.

**추가 옵션 구성** 탭에서:

![추가 옵션 설정](./media/install-jenkins-solution-template/ap-addtional.png)

* Jenkins 마스터를 고유하게 식별하는 도메인 이름 레이블을 제공합니다.

**확인**을 클릭하고 다음 단계로 이동합니다. 

유효성 검사를 통과했으면 **확인**을 클릭하여 템플릿 및 매개 변수를 다운로드합니다. 

다음으로, **구매**를 선택하여 모든 리소스를 프로비전합니다.

## <a name="setup-ssh-port-forwarding"></a>SSH 포트 전달 설정

기본적으로 Jenkins 인스턴스는 http 프로토콜을 사용하고 포트 8080에서 수신 대기합니다. 사용자는 보안되지 않는 프로토콜을 통해 인증을 수행하지 않아야 합니다.
    
로컬 컴퓨터에서 Jenkins UI를 보기 위해 포트 전달을 설정합니다.

### <a name="if-you-are-using-windows"></a>Windows를 사용하는 경우:

PuTTY를 설치하고 Jenkins를 보호하기 위해 암호를 사용하는 경우 이 명령을 실행합니다.
```
putty.exe -ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* 로그인하려면 암호를 입력합니다.

![로그인하려면 암호를 입력합니다.](./media/install-jenkins-solution-template/jenkins-pwd.png)

SSH를 사용하는 경우 이 명령을 실행합니다.
```
putty -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

### <a name="if-you-are-using-linux-or-mac"></a>Linux 또는 Mac을 사용 중인 경우:

Jenkins 마스터를 보호하기 위해 암호를 사용하는 경우 이 명령을 실행합니다.
```
ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* 로그인하려면 암호를 입력합니다.

SSH를 사용하는 경우 이 명령을 실행합니다.
```
ssh -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

## <a name="connect-to-jenkins"></a>Jenkins에 연결
터널을 시작한 후 로컬 컴퓨터에서 http://localhost:8080/으로 이동합니다.

초기 관리자 암호로 Jenkins 대시보드의 잠금을 처음으로 해제합니다.

![Jenkins 잠금 해제](./media/install-jenkins-solution-template/jenkins-unlock.png)

토큰을 가져오려면 VM으로 SSH를 수행하고 `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`를 실행합니다.

![Jenkins 잠금 해제](./media/install-jenkins-solution-template/jenkins-ssh.png)

제안된 플러그 인을 설치하라는 메시지가 표시됩니다.

다음으로 Jenkins 마스터의 관리 사용자를 만듭니다.

Jenkins 인스턴스를 사용할 준비가 되었습니다. http://\<방금 만든 인스턴스의 공용 DNS 이름\>으로 이동하여 읽기 전용 보기에 액세스할 수 있습니다.

![Jenkins가 준비되었습니다.](./media/install-jenkins-solution-template/jenkins-welcome.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * 솔루션 템플릿으로 Jenkins 마스터를 만들었습니다.
> * Jenkins의 초기 구성을 수행했습니다.
> * 플러그 인을 설치했습니다.

이 링크를 따라 Jenkins와 연속 통합을 위해 Azure VM 에이전트를 사용하는 방법을 확인합니다.

> [!div class="nextstepaction"]
> [Jenkins 에이전트로 Azure VM](jenkins-azure-vm-agents.md)

