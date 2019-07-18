---
title: Avere vFXT 제어판에 액세스 - Azure
description: vFXT 클러스터 및 브라우저 기반 Avere 제어판에 연결하여 Avere vFXT를 구성하는 방법입니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: v-erkell
ms.openlocfilehash: 830be92d37f304598cca05c3ac80973158c38a59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439982"
---
# <a name="access-the-vfxt-cluster"></a>vFXT 클러스터에 액세스

설정을 변경하고 Avere vFXT 클러스터를 모니터링하려면 Avere 제어판을 사용합니다. Avere 제어판은 클러스터에 대한 브라우저 기반의 그래픽 인터페이스입니다.

vFXT 클러스터는 프라이빗 가상 네트워크 내에 있으므로 SSH 터널을 만들거나 다른 방법을 사용하여 클러스터의 관리 IP 주소에 연결해야 합니다. 다음과 같은 두 가지 기본 단계가 있습니다. 

1. 워크스테이션과 프라이빗 vnet 간의 연결 만들기 
1. 웹 브라우저에서 클러스터의 제어판 로드 

> [!NOTE] 
> 이 문서에서는 클러스터 컨트롤러 또는 클러스터의 가상 네트워크 내부에 있는 다른 VM에 공용 IP 주소를 설정했다고 가정합니다. 이 문서에서는 호스트로 해당 VM을 사용하여 클러스터에 액세스하는 방법을 설명합니다. VPN 또는 ExpressRoute를 사용하여 vnet에 액세스하는 경우 [Avere 제어판에 연결](#connect-to-the-avere-control-panel-in-a-browser)로 건너뜁니다.

연결하기 전에 클러스터 컨트롤러를 만들 때 사용한 SSH 공개/개인 키 쌍이 로컬 머신에 설치되어 있는지 확인합니다. 도움이 필요한 경우 [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) 또는 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)용 SSH 키 설명서를 참조하세요. 공개 키 대신 암호를 사용한 경우에는 연결 시 암호를 입력하라는 메시지가 표시됩니다. 

## <a name="create-an-ssh-tunnel"></a>SSH 터널 만들기 

Linux 기반의 명령줄에서 SSH 터널 또는 Windows 10 클라이언트 시스템을 만들 수 있습니다. 

SSH 터널링이 폼을 사용 하 여 명령을 사용 합니다. 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

이 명령은 클러스터 컨트롤러의 IP 주소를 통해 클러스터의 관리 IP 주소에 연결합니다.

예제:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

SSH 공개 키를 사용하여 클러스터를 만들고 일치하는 키가 클라이언트 시스템에 설치된 경우 인증이 자동으로 수행됩니다. 암호를 사용한 경우에는 시스템에 암호를 입력하라는 메시지가 표시됩니다.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>브라우저에서 Avere 제어판에 연결

이 단계에서는 웹 브라우저를 사용하여 vFXT 클러스터에서 실행되는 구성 유틸리티에 연결합니다.

* SSH 터널 연결의 경우 웹 브라우저를 열고 `https://127.0.0.1:8443` 으로 이동합니다. 

  터널을 만들었을 때 클러스터 IP 주소에 연결했으므로 브라우저에서 localhost IP 주소를 사용해야 합니다. 8443 이외의 로컬 포트를 사용한 경우 포트 번호를 대신 사용합니다.

* VPN 또는 ExpressRoute를 사용하여 클러스터에 도달하는 경우 브라우저에서 클러스터 관리 IP 주소로 이동합니다. 예제: ``https://203.0.113.51``

브라우저에 따라 **고급**을 클릭하고 페이지로 계속 진행하는 것이 안전한지 확인해야 할 수 있습니다.

클러스터를 만들 때 입력한 `admin` 사용자 이름과 관리 암호를 입력합니다.

!['admin' 사용자 이름과 암호로 채워진 Avere 로그인 페이지의 스크린샷](media/avere-vfxt-gui-login.png)

**로그인**을 클릭하거나 키보드에서 Enter 키를 누릅니다.

## <a name="next-steps"></a>다음 단계

이제 클러스터에 액세스할 수 있으므로 [지원](avere-vfxt-enable-support.md)을 사용하도록 설정합니다.
