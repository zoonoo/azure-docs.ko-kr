---
title: "Azure의 Linux 가상 컴퓨터에 Node.js 응용 프로그램 배포"
description: "Azure의 Linux 가상 컴퓨터에 Node.js 응용 프로그램을 배포하는 방법을 알아봅니다."
services: 
documentationcenter: nodejs
author: stepro
manager: dmitryr
editor: 
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: /azure
ms.assetid: 857a812d-c73e-4af7-a985-2d0baf8b6f71
ms.service: multiple
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2016
ms.author: stephpr
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: 0de0314902805a2bdb37ce3c6f79ec221f3aed31
ms.lasthandoff: 12/06/2016


---
# <a name="deploy-a-nodejs-application-to-linux-virtual-machines-in-azure"></a>Azure의 Linux 가상 컴퓨터에 Node.js 응용 프로그램 배포
이 자습서에서는 Node.js 응용 프로그램을 사용하고 Azure에서 실행되는 Linux 가상 컴퓨터에 배포하는 방법을 보여줍니다. 이 자습서의 지침은 Node.js를 실행할 수 있는 모든 운영 체제에 적용될 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

* 간단한 TODO 응용 프로그램을 포함하는 GitHub 리포지토리 분기 및 복제.
* Azure에서 Linux 가상 컴퓨터 두 대를 만들고 구성하여 응용 프로그램 실행.
* 웹 프런트 엔드 가상 컴퓨터에 업데이트를 푸시하여 응용 프로그램 반복.

> [!NOTE]
> 이 자습서를 완료하려면 GitHub 계정 및 Microsoft Azure 계정과 개발 컴퓨터에서 Git를 사용하는 기능이 필요합니다.
> 
> 현재 GitHub 계정이 없는 경우 [여기서](https://github.com/join)등록할 수 있습니다.
> 
> [Microsoft Azure](https://azure.microsoft.com/) 계정이 없으면 [여기서](https://azure.microsoft.com/pricing/free-trial/) 무료 평가판에 등록할 수 있습니다. 이렇게 하면 계정이 없는 경우 [Microsoft 계정](http://account.microsoft.com) 에 대한 등록 프로세스를 안내합니다. 또는 Visual Studio 구독자인 경우 [MSDN 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.
> 
> 개발 컴퓨터에 Git가 없으며 Macintosh 또는 Windows 컴퓨터를 사용하는 경우 Git를 [여기](http://www.git-scm.com)에서 설치합니다. Linux를 사용하는 경우 `sudo apt-get install git`와 같은 가장 적합한 메커니즘을 사용하여 Git를 설치합니다.
> 
> 

## <a name="forking-and-cloning-the-todo-application"></a>TODO 응용 프로그램 분기 및 복제
이 자습서에서 사용된 TODO 응용 프로그램은 TODO 목록을 추적하는 MongoDB 인스턴스를 통해 간단한 웹 프런트 엔드를 구현합니다. GitHub에 로그인한 후에 [여기](https://github.com/stepro/node-todo) 로 이동하여 응용 프로그램을 찾고 오른쪽 위에 있는 링크를 사용하여 분기합니다. *accountname*/node-todo로 명명된 계정에 리포지토리를 만들어야 합니다.

이제 개발 컴퓨터에서 이 리포지토리를 복제합니다.

    git clone https://github.com/accountname/node-todo.git

원본 코드를 변경하는 경우 나중에 리포지토리의 로컬 복제본을 사용합니다.

## <a name="creating-and-configuring-the-linux-virtual-machines"></a>Linux 가상 컴퓨터 만들기 및 구성
Azure는 Linux 가상 컴퓨터를 사용하는 원시 계산을 훌륭히 지원합니다. 자습서의 이 부분에서는 두 대의 Linux 가상 컴퓨터를 스핀업하고 TODO 응용 프로그램을 배포하는 방법을 보여주어 하나의 Linux 가상 컴퓨터에 웹 프런트 엔드를 실행하고 다른 Linux 가상 컴퓨터에 MongoDB 인스턴스를 실행합니다.

### <a name="creating-virtual-machines"></a>가상 컴퓨터 만들기
Azure에서 새 가상 컴퓨터를 만드는 가장 쉬운 방법은 Azure 포털을 사용하는 것입니다. [여기](https://portal.azure.com) 를 클릭하여 로그인하고 웹 브라우저에서 Azure 포털을 시작합니다. Azure 포털이 로드되면 다음 단계를 완료합니다.

* "+ 새로 만들기" 링크를 클릭합니다.
* "계산" 범주를 선택하고 "Ubuntu Server 14.04 LTS"를 선택합니다.
* "리소스 관리자" 배포 모델을 선택하고 "만들기"를 클릭합니다.
* 다음 지침에 따라 기본 사항을 입력합니다.
  * 나중에 쉽게 식별할 수 있는 이름을 지정합니다.
  * 이 자습서에서는 암호 인증을 선택합니다.
  * 식별할 수 있는 이름으로 새 리소스 그룹을 만듭니다.
* 이 자습서에서는 가상 컴퓨터 크기에 "A1 표준"을 선택하는 것이 좋습니다.
* 추가 설정에 디스크 형식이 "표준"인지 확인하고 나머지는 모두 기본값을 적용합니다.
* 요약 페이지에서 만들기를 시작합니다.

위의 프로세스를 두 번 수행하여 웹 프런트 엔드 및 MongoDB 인스턴스에 각각 하나씩, 두 대의 Linux 가상 컴퓨터를 만듭니다. 가상 컴퓨터 만들기는 5-10분 정도 걸립니다.

### <a name="assigning-a-dns-entry-for-virtual-machines"></a>가상 컴퓨터에 DNS 항목 할당
Azure에서 만든 가상 컴퓨터는 기본적으로 1.2.3.4와 같은 공용 IP 주소를 통해 액세스할 수 있습니다. DNS 항목을 지정하여 컴퓨터를 보다 쉽게 식별할 수 있도록 해보겠습니다.

포털이 가상 컴퓨터가 만들어졌다고 알리면 왼쪽 탐색 모음에서 "가상 컴퓨터" 링크를 클릭하고 컴퓨터를 찾습니다. 각 컴퓨터에 다음을 수행합니다.

* 필수 탭을 찾아서 공용 IP 주소를 클릭합니다.
* 공용 IP 주소 구성에서 DNS 이름 레이블을 할당하고 저장합니다.

포털은 지정한 이름을 사용할 수 있는지 확인합니다. 구성을 저장한 후에 가상 컴퓨터는 `machinename.region.cloudapp.azure.com`과 유사한 호스트 이름을 갖습니다.

### <a name="connecting-to-the-virtual-machines"></a>가상 컴퓨터에 연결
가상 컴퓨터를 프로비전한 경우 SSH에 대해 원격 연결을 허용하도록 미리 구성되었습니다. 가상 컴퓨터를 구성하는 데 사용할 메커니즘입니다. 개발을 위해 Windows를 사용하는 경우 SSH 클라이언트가 없다면 하나를 가져와야 합니다. 여기서 일반적인 선택은 PuTTY이며 [여기](http://www.chiark.greenend.org.uk/~sgtatham/putty/)에서 다운로드할 수 있습니다. Macintosh 및 Linux OS는 미리 설치된 SSH 버전으로 제공됩니다.

### <a name="configuring-the-web-frontend-virtual-machine"></a>웹 프런트 엔드 가상 컴퓨터 구성
PuTTY, SSH 명령줄 또는 다른 즐겨찾는 SSH 도구를 사용하여 만든 웹 프런트 엔드 컴퓨터에 대한 SSH입니다. 명령 프롬프트에서 다음 환영 메시지가 표시되어야 합니다.

우선 Git 및 노드가 모두 설치되었는지 확인해보겠습니다.

    sudo apt-get install -y git
    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs

응용 프로그램의 웹 프런트 엔드가 일부 네이티브 Node.js 모듈을 사용하기 때문에 필수적인 빌드 도구 집합을 설치해야 합니다.

    sudo apt-get install -y build-essential

마지막으로 *영원히*라는 Node.js 응용 프로그램을 설치하며 이는 Node.js 서버 응용 프로그램을 실행하는 데 도움이 됩니다.

    sudo npm install -g forever

이상은 가상 컴퓨터가 응용 프로그램의 웹 프런트 엔드를 실행하는 데 필요한 종속성입니다. 이제 실행해보겠습니다. 이렇게 하려면 먼저 이전에 분기된 GitHub 리포지토리의 기본 복제본을 만들어서 쉽게 가상 컴퓨터에 게시(이 업데이트는 나중에 다룹니다)한 다음 기본 복제본을 복제하여 실제로 실행될 수 있는 버전의 리포지토리를 제공합니다.

홈 (~) 디렉터리를 시작하고 다음 명령을 실행합니다( *accountname* 을 GitHub 사용자 계정 이름으로 대체).

    git clone --bare https://github.com/accountname/node-todo.git
    git clone node-todo.git

이제 노드 todo 디렉터리를 입력하고이 명령을 실행합니다.

    npm install
    forever start server.js

응용 프로그램의 웹 프런트 엔드가 이제 실행되지만 웹 브라우저에서 응용 프로그램에 액세스할 수 있기 전에 한 단계가 더 있습니다. 만든 가상 컴퓨터는 *네트워크 보안 그룹*이라는 Azure 리소스에서 보호되며 이는 가상 컴퓨터를 프로비전할 때 만들어졌습니다. 현재 이 리소스를 사용하면 가상 컴퓨터를 라우트할 수 있도록 포트22에 대한 외부 요청을 허용하며 이는 SSH가 오로지 컴퓨터와 통신할 수 있도록 합니다. 따라서 포트8080에서 실행하도록 구성된 TODO 응용 프로그램을 보기 위해 이 포트가 열려야 합니다.

Azure 포털로 돌아가 다음 단계를 완료합니다.

* 왼쪽 탐색 모음에서 "리소스 그룹"을 클릭합니다.
* 가상 컴퓨터를 포함하는 리소스 그룹을 선택합니다.
* 리소스의 결과 목록에서 네트워크 보안 그룹(방패 아이콘이 있는 그룹)을 선택합니다.
* 속성에서 "인바운드 보안 규칙"을 선택합니다.
* 도구 모음에서 "추가"를 클릭합니다.
* "default-allow-todo"와 같은 이름을 제공합니다.
* 프로토콜을 "TCP"로 설정합니다.
* 대상 포트 범위를 "8080"으로 설정합니다.
* 확인을 클릭하고 보안 규칙이 만들어지기를 기다립니다.

이 보안 규칙을 만든 후에 TODO 응용 프로그램은 인터넷에서 공개적으로 표시되고 예를 들어 URL을 사용하여 찾아볼 수 있습니다.

    http://machinename.region.cloudapp.azure.com:8080

MongoDB 가상 컴퓨터를 아직 구성하지 않았더라도 TODO 응용 프로그램은 상당히 잘 작동한다는 점을 기억합니다. 원본 리포지토리가 미리 배포된 MongoDB 인스턴스를 사용하도록 하드 코드되기 때문입니다. MongoDB 가상 컴퓨터를 구성한 후에 다시 돌아가고 원본 코드를 변경하여 개인 MongoDB 인스턴스를 대신 활용합니다.

### <a name="configuring-the-mongodb-virtual-machine"></a>MongoDB 가상 컴퓨터 구성
PuTTY, SSH 명령줄 또는 다른 즐겨찾는 SSH 도구를 사용하여 만든 두 번째 컴퓨터에 대한 SSH입니다. 시작 메시지 및 명령 프롬프트를 확인한 후에 MongoDB를 설치합니다.(이 지침은 [여기](https://docs.mongodb.org/master/tutorial/install-mongodb-on-ubuntu/)에서 가져왔음)

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
    echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

기본적으로 MongoDB는 로컬로 액세스할 수 있도록 구성됩니다. 이 자습서에서는 응용 프로그램의 가상 컴퓨터에서 액세스할 수 있도록 MongoDB을 구성합니다. sudo 컨텍스트에서 /etc/mongod.conf 파일을 열고 `# network interfaces` 섹션을 찾습니다. `net.bindIp` 구성 값을 `0.0.0.0`으로 변경합니다.

> [!NOTE]
> 이 자습서에서만 이렇게 구성합니다. 권장되는 보안 사례가 **아니고** 프로덕션 환경에서는 사용되면 안됩니다.
> 
> 

이제 MongoDB 서비스가 시작되었는지 확인합니다.

    sudo service mongod restart

MongoDB는 기본적으로 포트 27017에 대해 작동합니다. 따라서 웹 프런트 엔드 가상 컴퓨터에서 포트 8080을 여는 데 필요한 것과 동일한 방법으로 MongoDB 가상 컴퓨터에서 포트 27017을 열어야 합니다.

Azure 포털로 돌아가 다음 단계를 완료합니다.

* 왼쪽 탐색 모음에서 "리소스 그룹"을 클릭합니다.
* MongoDB 가상 컴퓨터를 포함하는 리소스 그룹을 선택합니다.
* 리소스의 결과 목록에서 MongoDB 가상 컴퓨터에 지정한 동일한 이름인 네트워크 보안 그룹(방패 아이콘이 있는 그룹)을 선택합니다.
* 속성에서 "인바운드 보안 규칙"을 선택합니다.
* 도구 모음에서 "추가"를 클릭합니다.
* "default-allow-mongo"와 같은 이름을 제공합니다.
* 프로토콜을 "TCP"로 설정합니다.
* 대상 포트 범위를 "27017"으로 설정합니다.
* 확인을 클릭하고 보안 규칙이 만들어지기를 기다립니다.

## <a name="iterating-on-the-todo-application"></a>TODO 응용 프로그램에 반복
지금까지 각각 응용 프로그램의 웹 프런트 엔드를 실행하고 MongoDB 인스턴스를 실행하는 두 대의 Linux 가상 컴퓨터를 프로비전했습니다. 하지만 웹 프런트 엔드가 프로비전된 MongoDB 인스턴스를 아직 실제로 사용하지 않는다는 문제가 있습니다. 웹 프런트 엔드 코드를 업데이트하여 하드 코드된 인스턴스 대신 환경 변수를 사용하도록 수정해 보겠습니다.

### <a name="changing-the-todo-application"></a>TODO 응용 프로그램 변경
먼저 node-todo 리포지토리를 복제한 개발 컴퓨터의 좋아하는 편집기에서 `node-todo/config/database.js` 파일을 열고 `mongodb://...`와 같은 하드 코드된 값에서 `process.env.MONGODB`으로 URL 값을 변경합니다.

변경 내용을 커밋하고 GitHub 마스터로 푸시합니다.

    git commit -am "Get MongoDB instance from env"
    git push origin master

아쉽게도 웹 프런트 엔드 가상 컴퓨터에 변경 내용을 게시하지 않습니다. 라이브 환경에서 변경의 효과를 신속하게 확인할 수 있도록 해당 가상 컴퓨터에 몇 가지 사항을 더 변경하여 업데이트를 게시하는 데 간단하지만 효과적인 메커니즘을 사용하겠습니다.

### <a name="configuring-the-web-frontend-virtual-machine"></a>웹 프런트 엔드 가상 컴퓨터 구성
웹 프런트 엔드 가상 컴퓨터에서 이전에 만든 node-todo 리포지토리의 기본 복제본을 회수합니다. 이 작업은 변경 내용을 푸시할 수 있는 새 Git 원격을 만들었습니다. 그러나 이 원격을 단순히 푸시하는 작업은 개발자가 코드에서 작업할 경우 찾고 있는 신속한 반복 모델을 제공하지 않습니다.

실행하려는 작업은 가상 컴퓨터에서 원격 리포지토리에 푸시가 발생할 때 실행 중인 TODO 응용 프로그램이 자동으로 업데이트되는 것입니다. 다행히 Git를 사용하여 쉽게 달성할 수 있습니다.

Git는 특정 시간에 호출되는 많은 후크를 노출하여 리포지토리에서 수행된 작업에 반응합니다. 이 리포지토리의 `hooks` 폴더에서 셸 스크립트를 사용하여 지정됩니다. 자동 업데이트 시나리오에 가장 적합한 후크는 `post-update` 이벤트입니다.

웹 프런트 엔드 가상 컴퓨터에 대한 SSH 세션에서 `~/node-todo.git/hooks` 디렉터리로 변경하고 `post-update`라는 파일에 다음 콘텐츠를 추가합니다(`machinename` 및 `region`를 MongoDB 가상 컴퓨터 정보로 대체).

    #!/bin/bash

    forever stopall
    unset 'GIT_DIR'
    export MONGODB="mongodb://machinename.region.cloudapp.azure.com:27017/tododb"
    cd ~/node-todo && git fetch origin && git pull origin master && npm install && forever start ~/node-todo/server.js
    exec git update-server-info

다음 명령을 실행하여 이 파일을 실행할 수 있는지 확인합니다.

    chmod 755 post-update

이 스크립트는 현재 서버 응용 프로그램을 중지하고 복제된 리포지토리의 코드를 최신으로 업데이트하며 업데이트된 종속성을 충족하고 서버를 다시 시작하는지 확인합니다. 또한 환경은 첫 번째 응용 프로그램 업데이트를 수신하기 위해 준비되도록 구성되어 환경 변수에서 MongoDB 인스턴스를 가져오는지 확인합니다.

### <a name="configuring-your-development-machine"></a>개발 컴퓨터 구성
이제 웹 프런트 엔드 가상 컴퓨터에 연결된 개발 컴퓨터를 가져와 보겠습니다. 이는 가상 컴퓨터에 리포지토리를 원격으로 추가하는 것 만큼 간단합니다. 이렇게 하려면 다음 명령을 실행합니다(*사용자*를 웹 프런트 엔드 가상 컴퓨터 로그인 이름 및 *machinename*과 *지역*으로 적절하게 대체).

    git remote add azure user@machinename.region.cloudapp.azure.com:node-todo.git

웹 프런트 엔드 가상 컴퓨터에 대한 푸시, 효과적인 게시, 변경을 사용하는 데 필요한 모든 것입니다.

### <a name="publishing-updates"></a>업데이트 게시
응용 프로그램이 고유의 MongoDB 인스턴스를 사용하도록 지금까지 변경된 내용을 게시하겠습니다.

    git push azure master

다음과 유사한 결과가 표시됩니다.

    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (4/4), 406 bytes | 0 bytes/s, done.
    Total 4 (delta 1), reused 0 (delta 0)
    remote: info:    Forever stopped processes:
    remote: data:        uid  command         script    forever pid  id logfile                         uptime
    remote: data:    [0] 0Lyh /usr/bin/nodejs server.js 9064    9301    /home/username/.forever/0Lyh.log 0:0:3:17.487
    remote: From /home/username/node-todo
    remote:    5f31fd7..5bc7be5  master     -> origin/master
    remote: From /home/username/node-todo
    remote:  * branch            master     -> FETCH_HEAD
    remote: Updating 5f31fd7..5bc7be5
    remote: Fast-forward
    remote:  config/database.js | 2 +-
    remote:  1 file changed, 1 insertion(+), 1 deletion(-)
    remote: npm WARN package.json node-todo@0.0.0 No repository field.
    remote: npm WARN package.json node-todo@0.0.0 No license field.
    remote: warn:    --minUptime not set. Defaulting to: 1000ms
    remote: warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
    remote: info:    Forever processing file: /home/username/node-todo/server.js
    To username@machinename.region.cloudapp.azure.com:node-todo.git
    5f31fd7..5bc7be5  master -> master

이 명령이 완료된 후에 웹 브라우저에서 응용 프로그램을 새로 고쳐 봅니다. 여기에 제시된 TODO 목록이 비어 있고 더 이상 공유 배포 MongoDB 인스턴스에 연결되지 않는다는 것을 알 수 있어야 합니다.

자습서를 완료하려면 다른 가시적인 사항을 변경하겠습니다. 개발 컴퓨터에서 즐겨찾는 편집기를 사용하여 node-todo/public/index.html 파일을 엽니다. jumbotron 헤더를 찾아서 제목을 "I am Todo-aholic"에서 "I'm a Todo-aholic on Azure!"로 변경합니다.

이제 커밋하겠습니다.

    git commit -am "Azurify the title"

이번에는 변경 사항을 GitHub 리포지토리에 게시하기 전에 Azure에 게시하겠습니다.

    git push azure master

이 명령이 완료되면 웹 페이지를 새로 고치고 변경 내용을 확인합니다. 보기 좋기 때문에 변경 내용을 다시 원래 원격으로 푸시합니다. 

    git push origin master

## <a name="next-steps"></a>다음 단계
이 문서는 Node.js 응용 프로그램을 사용하고 Azure에서 실행되는 Linux 가상 컴퓨터에 배포하는 방법을 보여주었습니다. Azure의 Linux 가상 컴퓨터에 대해 자세히 알아보려면 [Azure의 Linux 소개](/documentation/articles/virtual-machines-linux-introduction/)를 참조하세요.

Azure에서 Node.js 응용 프로그램을 개발하는 방법에 대한 자세한 내용은 [Node.js 개발자 센터](/develop/nodejs/)를 참조하세요.


