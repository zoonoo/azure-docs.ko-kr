---
title: "Azure에서 Jenkins 서버 만들기"
description: "Jenkins 솔루션 템플릿의 Azure Linux 가상 컴퓨터에 Jenkins를 설치하고 샘플 Java 응용 프로그램을 빌드합니다."
author: mlearned
manager: douge
ms.service: multiple
ms.workload: web
ms.devlang: java
ms.topic: hero-article
ms.date: 08/21/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 7bb74f297d52fb25171817175cce64187b397c38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Azure Portal의 Azure Linux VM에서 Jenkins 서버 만들기

이 빠른 시작은 Azure에서 사용하기 위해 구성된 도구와 플러그 인을 사용하여 Ubuntu Linux VM에 [Jenkins](https://jenkins.io)를 설치하는 방법을 보여줍니다. 작업을 완료하면 Azure에서 실행되는 Jenkins 서버가 [GitHub](https://github.com)에서 샘플 Java 앱을 작성합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독
* 컴퓨터의 명령줄(Bash 셸 또는 [PuTTY](http://www.putty.org/) 등)에서 SSH에 액세스

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>솔루션 템플릿에서 Jenkins VM 만들기

웹 브라우저에서 [Jenkins의 마켓플레이스 이미지](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)를 열고 페이지의 왼쪽에서 **지금 가져오기**를 선택합니다. 가격 책정 세부 정보를 검토하고 **계속**을 선택한 후 **만들기**를 선택하여 Azure Portal에서 Jenkins 서버를 구성합니다. 
   
![Azure Portal 대화 상자](./media/install-jenkins-solution-template/ap-create.png)

**기본 설정 구성** 탭에서 다음 필드를 입력합니다.

![기본 설정 구성](./media/install-jenkins-solution-template/ap-basic.png)

* **이름**에 **Jenkins**를 사용합니다.
* **사용자 이름**을 입력합니다. 사용자 이름은 [특정 요구 사항](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm)을 충족해야 합니다.
* **암호**를 **인증 유형**으로 선택하고 암호를 입력합니다. 암호에는 대문자, 숫자 및 특수 문자가 포함되어야 합니다.
* **리소스 그룹**에 **myJenkinsResourceGroup**을 사용합니다.
* **위치** 드롭 다운에서 **미국 동부** [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다.

**확인**을 선택하여 **추가 옵션 구성** 탭으로 진행합니다. 고유한 도메인 이름을 입력하여 Jenkins 서버를 식별하고 **확인**을 선택합니다.

![추가 옵션 설정](./media/install-jenkins-solution-template/ap-addtional.png)  

 유효성 검사에 통과하면 **요약** 탭에서 다시 **확인**을 선택합니다. 마지막으로 **구매**를 선택하여 Jenkins VM을 만듭니다. 서버가 준비되면 Azure Portal에서 알림을 받게 됩니다.   

![Jenkins는 준비 알림임](./media/install-jenkins-solution-template/jenkins-deploy-notification-ready.png)

## <a name="connect-to-jenkins"></a>Jenkins에 연결

웹 브라우저에서 가상 컴퓨터로 이동합니다(예: http://jenkins2517454.eastus.cloudapp.azure.com/). Jenkins 콘솔은 보안되지 않은 HTTP를 통해 액세스할 수 없으므로 SSH 터널을 사용하여 컴퓨터에서 안전하게 Jenkins 콘솔에 액세스하는 지침이 페이지에서 제공됩니다.

![Jenkins 잠금 해제](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

명령줄 페이지에서 `ssh` 명령을 사용하여 터널을 설정하고, 솔루션 템플릿에서 가상 컴퓨터를 설정하는 경우 이전에 선택한 가상 컴퓨터 관리 사용자의 이름으로 `username`을 바꿉니다.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

터널을 시작한 후에 로컬 컴퓨터에서 http://localhost:8080/으로 이동합니다. 

Jenkins VM에 SSH를 통해 연결되어 있는 동안 명령줄에서 다음 명령을 실행하여 초기 암호를 가져옵니다.

```bash
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
```

이 초기 암호를 사용하여 Jenkins 대시보드의 잠금을 처음으로 해제합니다.

![Jenkins 잠금 해제](./media/install-jenkins-solution-template/jenkins-unlock.png)

다음 페이지에서 **제안된 플러그 인 설치**를 선택하고 Jenkins 대시보드에 액세스하는 데 사용하는 Jenkins 관리 사용자를 만듭니다.

![Jenkins가 준비되었습니다.](./media/install-jenkins-solution-template/jenkins-welcome.png)

이제 Jenkins 서버는 코드를 빌드할 준비가 되었습니다.

## <a name="create-your-first-job"></a>첫 번째 작업 만들기

Jenkins 콘솔에서 **새 작업 만들기**를 선택하고 이름을 **mySampleApp**으로 지정하고 **프리스타일 프로젝트**를 선택한 후 **확인**을 선택합니다.

![새 작업 만들기](./media/install-jenkins-solution-template/jenkins-new-job.png) 

**소스 코드 관리** 탭을 선택하고, **Git**를 사용 하도록 설정하고, **리포지토리 URL** 필드에 다음 URL을 입력합니다. `https://github.com/spring-guides/gs-spring-boot.git`

![Git 리포지토리 정의](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

**빌드** 탭을 선택한 후 **빌드 단계 추가**, **Gradle 스크립트 호출**을 선택합니다. **Gradle 래퍼 사용**을 선택한 후 **래퍼 위치**에 `complete`를 입력하고 **작업**에 `build`를 입력합니다.

![Gradle 래퍼를 사용하여 빌드](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

**고급..**을 선택하고 **루트 빌드 스크립트** 필드에 `complete`를 입력합니다. **저장**을 선택합니다.

![Gradle 래퍼 빌드 단계에서 고급 설정을 설정](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>코드 빌드

**지금 빌드**를 선택하여 코드를 컴파일하고 샘플 앱을 패키징합니다. 빌드가 완료되면 프로젝트에 대한 **작업 영역** 링크를 선택합니다.

![작업 영역으로 이동하여 빌드에서 JAR 파일 가져오기](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

`complete/build/libs`로 이동하고 `gs-spring-boot-0.1.0.jar`에서 빌드가 성공했는지 확인합니다. 이제 Jenkins 서버는 Azure에서 고유한 프로젝트를 빌드할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Jenkins 에이전트로 Azure VM 추가](jenkins-azure-vm-agents.md)
