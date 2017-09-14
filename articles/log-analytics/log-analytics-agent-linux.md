---
title: "OMS(Operations Management Suite)에 Linux 컴퓨터 연결 | Microsoft Docs"
description: "이 문서에서는 Linux용 OMS 에이전트를 사용하여 Azure, 기타 클라우드 또는 온-프레미스에 호스트된 Linux 컴퓨터를 OMS에 연결하는 방법을 설명합니다."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/05/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: 17b451b1fc91cf9fdc895ad28f2c455af5d28b07
ms.contentlocale: ko-kr
ms.lasthandoff: 09/06/2017

---

# <a name="connect-your-linux-computers-to-operations-management-suite-oms"></a>OMS(Operations Management Suite)에 Linux 컴퓨터 연결 

Microsoft OMS(Operations Management Suite)를 사용하여 물리적 서버 또는 가상 컴퓨터로서 온-프레미스 데이터 센터에 상주하는 Linux 컴퓨터 및 컨테이너 솔루션(예: Docker), AWS(Amazon Web Services) 또는 Microsoft Azure와 같은 클라우드 호스티드 서비스에서 생성된 데이터를 수집하고 이러한 데이터로 작업을 수행할 수 있습니다. 변경 추적과 같이 OMS에서 사용할 수 있는 관리 솔루션을 사용하여 구성 변경으 식별하고, 업데이트 관리를 통해 소프트웨어 업데이트를 관리하여 Linux VM의 수명 주기를 사전에 관리할 수도 있습니다. 

Linux용 OMS 에이전트는 TCP 포트 443을 통해 OMS 서비스와 아웃바운드 통신을 수행하고, 컴퓨터가 인터넷을 통해 통신하기 위해 방화벽 또는 프록시 서버에 연결하는 경우 [HTTP 프록시 서버 또는 OMS 게이트웨이에서 사용하도록 에이전트 구성](#configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway)을 검토하여 적용해야 하는 구성 변경 내용을 이해하세요.  System Center 2016 - Operations Manager 또는 Operations Manager 2012 R2를 사용하여 컴퓨터를 모니터링하는 경우 OMS 서비스와 멀티홈으로 구성되어 데이터를 수집하고 서비스로 전달할 수 있고 Operations Manager에서 계속 모니터링될 수도 있습니다.  OMS와 통합된 Operations Manager 관리 그룹에서 모니터링되는 Linux 컴퓨터는 데이터 원본에 대한 구성을 수신하거나 관리 그룹을 통해 수집된 데이터를 전달하지 않습니다.  OMS 에이전트는 둘 이상의 작업 영역에 보고하도록 구성할 수 없습니다.  

IT 보안 정책이 네트워크의 컴퓨터가 인터넷에 연결하도록 허용하지 않을 경우 OMS 게이트웨이에 연결하여 구성 정보를 받고 사용하도록 설정한 솔루션에 따라 수집된 데이터를 보내도록 에이전트를 구성할 수 있습니다. OMS Linux 에이전트가 OMS 게이트웨이를 통해 OMS 서비스와 통신할 수 있도록 구성하는 방법에 대한 자세한 내용 및 단계에 대해서는 [OMS 게이트웨이 사용하여 OMS에 컴퓨터 연결](log-analytics-oms-gateway.md)을 참조하세요.  

다음 다이어그램은 방향 및 포트를 포함하여 에이전트 관리 Linux 컴퓨터와 OMS 간 연결을 보여 줍니다.

![OMS와 에이전트의 직접 통신 다이어그램](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## <a name="system-requirements"></a>시스템 요구 사항
시작하기 전에 다음 세부 정보를 검토하여 필수 구성 요소를 충족하는지 확인합니다.

### <a name="supported-linux-operating-systems"></a>지원되는 Linux 운영 체제
다음 Linux 배포판이 공식적으로 지원됩니다.  하지만, Linux용 OMS 에이전트는 나열되지 않은 그 밖의 배포에서 실행이 가능할 수 있습니다.

* Amazon Linux 2012.09 ~ 2015.09(x86/x64)
* CentOS Linux 5, 6 및 7(x86/x64)
* Oracle Linux 5, 6 및 7(x86/x64)
* Red Hat Enterprise Linux Server 5, 6 및 7(x86/x64)
* Debian GNU/Linux 6, 7, 8(x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS(x86/x64)
* SUSE Linux Enterprise Server 11 및 12(x86/x64)

### <a name="network"></a>네트워크
아래 정보는 Linux 에이전트가 OMS와 통신하는 데 필요한 프록시 및 방화벽 구성 정보를 나열합니다. 트래픽은 네트워크에서 OMS 서비스로 아웃바운드됩니다. 

|에이전트 리소스| 포트 |  
|------|---------|  
|*.ods.opinsights.azure.com | 포트 443|   
|*.oms.opinsights.azure.com | 포트 443|   
|*.blob.core.windows.net/ | 포트 443|   
|*.azure-automation.net | 포트 443|  

### <a name="package-requirements"></a>패키지 요구 사항

 **필수 패키지**   | **설명**   | **최소 버전**
--------------------- | --------------------- | -------------------
Glibc | GNU C 라이브러리   | 2.5-12 
Openssl | OpenSSL 라이브러리 | 0.9.8e 또는 1.0
Curl | cURL 웹 클라이언트 | 7.15.5
Python-ctypes | | 
PAM | 플러그형 인증 모듈 | 

> [!NOTE]
>  Syslog 메시지를 수집하려면 rsyslog 또는 syslog-ng가 필요합니다. Red Hat Enterprise Linux 버전 5, CentOS 및 Oracle Linux 버전(sysklog)에서는 syslog 이벤트 수집을 위한 기본 syslog 디먼이 지원되지 않습니다. 이 배포의 해당 버전에서 syslog 데이터를 수집하려면 rsyslog 디먼을 설치하고 sysklog를 대체하도록 구성해야 합니다. 

에이전트는 여러 패키지를 포함합니다. 다음 패키지를 포함하는 릴리스 파일은 `--extract`와 함께 shell 번들을 실행하면 사용할 수 있습니다.

**패키지** | **버전** | **설명**
----------- | ----------- | --------------
omsagent | 1.4.0 | Linux용 Operations Management Suite 에이전트
omsconfig | 1.1.1 | OMS 에이전트용 구성 에이전트
omi | 1.2.0 | Open Management Infrastructure(OMI) -- 경량 CIM 서버
scx | 1.6.3 | 운영 체제 성능 메트릭용 OMI CIM 공급자
apache-cimprov | 1.0.1 | OMI용 Apache HTTP 서버 성능 모니터링 공급자. Apache HTTP 서버가 감지되면 설치됨.
mysql-cimprov | 1.0.1 | OMI용 MySQL 서버 성능 모니터링 공급자. MySQL/MariaDB 서버가 감지되면 설치됨.
docker-cimprov | 1.0.0 | OMI용 Docker 공급자. Docker가 감지되면 설치됨.

### <a name="compatibility-with-system-center-operations-manager"></a>System Center Operations Manager와의 호환성
Linux 용 OMS 에이전트는 System Center Operations Manager 에이전트와 에이전트 바이너리를 공유합니다. 현재 Operations Manager로 관리되는 시스템에 Linux용 OMS 에이전트를 설치하면, 컴퓨터의 OMI 및 SCX 패키지가 새 버전으로 업그레이드됩니다. 이 릴리스에서 OMS 및 Linux용 System Center 2016 - Linux 용 Operations Manager/Operations Manager 2012 R2 에이전트는 호환됩니다. 

> [!NOTE]
> 하지만 System Center 2012 SP1 이전 버전은 현재 OMS Agent for Linux와 호환되거나 지원되지 않습니다.<br>
> Operations Manager로 모니터링되지 않는 컴퓨터에 OMS Agent for Linux를 설치한 다음, Operations Manager로 컴퓨터를 모니터링하려는 경우 컴퓨터를 검색하기 전에 [OMI 구성](#enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager)을 반드시 수정해야 합니다. **Operations Manager 에이전트가 OMS Agent for Linux보다 먼저 설치되면 이 단계가 필요하지 *않습니다*.**

### <a name="system-configuration-changes"></a>시스템 구성 변경 내용
OMS Agent for Linux 패키지를 설치한 후에는 다음과 같은 시스템 수준의 구성 변경이 추가로 적용됩니다. 이러한 아티팩트는 omsagent 패키지가 제거되면 제거됩니다.

* `omsagent` 라는 이름의 권한 없는 사용자가 생성됩니다. 이 계정으로 omsagent 디먼이 실행됩니다.
* sudoers "include" 파일은 /etc/sudoers.d/omsagent에 생성됩니다. 그러면 omsagent에서 syslog 및 omsagent 데몬을 다시 시작할 수 있도록 허가됩니다. 설치된 sudo 버전에서 sudo “include” 지시문이 지원되지 않으면, 이 항목은 /etc/sudoers에 기록됩니다.
* syslog 구성은 이벤트 하위 집합을 에이전트에 전달하도록 수정됩니다. 자세한 내용은 아래의 **데이터 수집 구성** 섹션을 참조하세요.

### <a name="upgrade-from-a-previous-release"></a>이전 릴리스에서 업그레이드
이 릴리스에서는 1.0.0-47보다 이전 버전에서 업그레이드하도록 지원됩니다. `--upgrade` 명령을 사용하여 설치를 수행하면 에이전트의 모든 구성 요소가 최신 버전으로 업그레이드됩니다.

## <a name="installing-the-agent"></a>에이전트 설치

이 섹션에서는 각 에이전트 구성 요소에 대한 Debian 및 RPM 패키지를 포함하는 번들을 사용하여 Linux용 OMS 에이전트를 설치하는 방법을 설명합니다.  직접 설치하거나 개별 패키지를 검색하도록 추출될 수 있습니다.  

[OMS 클래식 포털](https://mms.microsoft.com)로 전환하면 찾을 수 있는 OMS 작업 영역 ID 및 키가 필요합니다.  **개요** 페이지의 맨 위 메뉴에서 **설정**을 선택한 다음, **Connected Sources\Linux Servers**로 이동합니다.  **작업 영역 ID** 및 **기본 키** 오른쪽에 값이 표시됩니다.  두 항목을 복사하여 선호하는 편집기에 붙여넣습니다.    

1. GitHub에서 최신 [Linux용 OMS 에이전트(x64)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x64.sh) 또는 [Linux x86용 OMS 에이전트](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x86.sh)를 다운로드합니다.  
2. scp/sftp를 사용하여 Linux 컴퓨터로 해당 번들(x86 또는 x64)을 전송합니다.
3. `--install` 또는 `--upgrade` 인수를 사용하여 번들을 설치합니다. 

    > [!NOTE]
    > Linux용 System Center Operations Manager 에이전트가 이미 설치된 경우처럼 기존 패키지가 설치된 경우에는 `--upgrade` 인수를 사용합니다. 설치 중에 Operations Management Suite에 연결하려면 `-w <WorkspaceID>` 및 `-s <Shared Key>` 매개 변수를 제공합니다.


#### <a name="to-install-and-onboard-directly"></a>직접 설치하고 직접 등록하려면
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### <a name="to-upgrade-the-agent-package"></a>에이전트 패키지를 업그레이드하려면
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade
```

#### <a name="to-install-and-onboard-to-a-workspace-in-us-government-cloud"></a>미국 정부 클라우드의 작업 영역에 설치하고 등록하려면
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

## <a name="configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway"></a>HTTP 프록시 서버 또는 OMS 게이트웨이에서 사용하도록 에이전트 구성
OMS Agent for Linux는 HTTP 또는 HTTPS 프록시 서버 또는 OMS 게이트웨이를 통해 OMS 서비스와 통신하도록 지원합니다.  익명 및 기본 인증(사용자 이름/암호) 둘 다 지원됩니다.  

### <a name="proxy-configuration"></a>프록시 구성
프록시 구성 값은 다음 구문을 갖습니다.

`[protocol://][user:password@]proxyhost[:port]`

속성|설명
-|-
프로토콜|HTTP 또는 HTTPS
사용자|프록시 인증을 위한 선택적 사용자 이름
password|프록시 인증을 위한 선택적 암호
proxyhost|프록시 서버/OMS 게이트웨이의 주소 또는 FQDN
포트|프록시 서버/OMS 게이트웨이 대한 선택적 포트 번호

예: `http://user01:password@proxy01.contoso.com:8080`

설치 중에 또는 설치 후에 proxy.conf 구성 파일을 수정하여 프록시 서버를 지정할 수 있습니다.   

### <a name="specify-proxy-configuration-during-installation"></a>설치 중 프록시 구성 지정
omsagent 설치 번들에 대한 `-p` 또는 `--proxy` 인수는 사용할 프록시 구성을 지정합니다. 

```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -p http://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### <a name="define-the-proxy-configuration-in-a-file"></a>파일에 프록시 구성 정의
프록시 구성은 파일 `/etc/opt/microsoft/omsagent/proxy.conf` 및 `/etc/opt/microsoft/omsagent/conf/proxy.conf `에서 설정할 수 있습니다. 이 파일을 직접 만들거나 편집할 수 있지만 파일에 omiuser 그룹 읽기 권한을 부여하도록 사용 권한을 업데이트해야 합니다. 예:
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:8080"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 600 /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf  
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### <a name="removing-the-proxy-configuration"></a>프록시 구성 제거
이전에 정의된 프록시 구성을 제거하고 직접 연결로 되돌리려면 proxy.conf 파일을 제거합니다.
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="onboarding-with-operations-management-suite"></a>Operations Management Suite에 등록
번들 설치 동안 작업 영역 ID 및 키가 제공되지 않았으므로 나중에 에이전트를 Operations Management Suite에 등록해야 합니다.

### <a name="onboarding-using-the-command-line"></a>명령줄을 사용하여 등록
작업 영역에 대한 작업 영역 ID 및 키를 제공하여 omsadmin.sh 명령을 실행합니다. 이 명령은 루트 권한(sudo 권한 상승)으로 실행해야 합니다.
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="onboarding-using-a-file"></a>파일을 사용하는 등록
1.  파일 `/etc/omsagent-onboard.conf`를 만듭니다. 이 파일은 루트에 대해 읽을 수 있고 쓸 수 있어야 합니다.
`sudo vi /etc/omsagent-onboard.conf`
2.  작업 영역 ID 및 공유 키를 사용하여 파일에 다음 줄을 삽입합니다.

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  다음 명령을 실행하여 OMS에 등록합니다. `sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
4.  이 파일은 등록이 성공적으로 수행되면 삭제됩니다.

## <a name="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager"></a>OMS Agent for Linux에서 System Center Operations Manager에 보고하도록 설정
OMS Agent for Linux에서 System Center Operations Manager 관리 그룹에 보고하도록 구성하려면 다음 단계를 수행합니다.  

1. 파일 `/etc/opt/omi/conf/omiserver.conf`
2. **httpsport=** 로 시작되는 줄이 1270 포트를 지정하도록 합니다. 예: `httpsport=1270`
3. OMI 서버를 다시 시작합니다. `sudo /opt/omi/bin/service_control restart`

## <a name="agent-logs"></a>에이전트 로그
OMS Agent for Linux용 로그는 `/var/opt/microsoft/omsagent/<workspace id>/log/`에서 찾을 수 있습니다. omsconfig(에이전트 구성) 프로그램용 로그는 `/var/opt/microsoft/omsconfig/log/`에서 찾을 수 있습니다. OMI 및 SCX 구성 요소(성능 메트릭 데이터 제공)용 로그는 `/var/opt/omi/log/ and /var/opt/microsoft/scx/log`에서 찾을 수 있습니다.

### <a name="log-rotation-configuration"></a>로그 순환 구성##
omsagent에 대한 로그 순환 구성은 `/etc/logrotate.d/omsagent-<workspace id>`에서 찾을 수 있습니다.

기본 설정은 다음과 같습니다. 
```
/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log {
    rotate 5
    missingok
    notifempty
    compress
    size 50k
    copytruncate
}
```

## <a name="uninstalling-the-oms-agent-for-linux"></a>OMS Agent for Linux 제거
`--purge` 인수로 번들.sh 파일을 실행하여 에이전트 패키지를 제거할 수 있습니다. 그러면 컴퓨터에서 에이전트와 해당 구성이 완전히 제거됩니다.   

```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

## <a name="troubleshooting"></a>문제 해결

### <a name="issue-unable-to-connect-through-proxy-to-oms"></a>문제: 프록시를 통해 OMS에 연결할 수 없음

#### <a name="probable-causes"></a>가능한 원인
* 등록하는 동안 지정된 프록시가 올바르지 않습니다.
* OMS 서비스 끝점이 데이터 센터의 허용 목록에 없습니다. 

#### <a name="resolutions"></a>해결 방법
1. 다음 명령과 `-v` 옵션을 사용하여 OMS Agent for Linux가 있는 OMS 서비스에 다시 등록합니다. OMS 서비스에 대한 프록시를 통해 연결되는 에이전트의 자세한 정보를 출력할 수 있습니다. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. [HTTP 프록시 서버에서 사용하기 위해 에이전트 구성(#configuring the-agent-for-use-with-a-http-proxy-server)] 섹션을 검토하여 프록시 서버를 통해 통신하도록 에이전트를 적절히 구성했는지 확인합니다.    
* 다음 OMS 서비스 끝점이 허용 목록에 있는지 한 번 더 확인합니다.

    |에이전트 리소스| 포트 |  
    |------|---------|  
    |*.ods.opinsights.azure.com | 포트 443|   
    |*.oms.opinsights.azure.com | 포트 443|   
    |ods.systemcenteradvisor.com | 포트 443|   
    |*.blob.core.windows.net/ | 포트 443|   

### <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>문제: 등록하는 동안 403 오류 발생

#### <a name="probable-causes"></a>가능한 원인
* Linux 서버의 날짜 및 시간이 올바르지 않습니다 
* 사용된 작업 영역 ID 및 작업 영역 키가 올바르지 않습니다.

#### <a name="resolution"></a>해결 방법

1. 명령 시간을 사용하여 Linux 서버에서 시간을 확인합니다. 시간이 현재 시간에서 +/-15분인 경우 등록이 실패합니다. 이 문제를 해결하려면 Linux 서버의 날짜 및/또는 시간대를 업데이트합니다. 
2. Linux용 OMS 에이전트 최신 버전이 설치되어 있는지 확인합니다.  최신 버전은 시간 차이로 인해 등록 실패가 발생하는지 여부를 알려줍니다.
3. 이 항목 앞부분의 설치 지침에 따라 올바른 작업 영역 ID 및 작업 영역 키를 사용하여 다시 등록합니다.

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>문제: 등록 직후에 로그 파일에 500 및 404 오류가 표시됨
이 문제는 알려진 문제이며 Linux 데이터를 OMS 작업 영역으로 처음 업로드할 때 발생합니다. 이 문제는 전송되는 데이터 또는 서비스 환경에 영향을 미치지 않습니다.

### <a name="issue-you-are-not-seeing-any-data-in-the-oms-portal"></a>문제: OMS 포털에서 데이터가 보이지 않음

#### <a name="probable-causes"></a>가능한 원인

- OMS 서비스 등록이 실패했습니다.
- OMS 서비스에 대한 연결이 차단되었습니다.
- Linux용 OMS 에이전트가 백업되었습니다.

#### <a name="resolutions"></a>해결 방법
1. `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` 파일이 있는지 확인하여 OMS 서비스 등록에 성공했는지 알아봅니다.
2. `omsadmin.sh` 명령줄 명령을 사용하여 다시 등록합니다.
3. 프록시를 사용하는 경우 앞서 제공된 프록시 문제 해결 단계를 참조하세요.
4. OMS Agent for Linux가 OMS 서비스와 통신할 수 없는 경우 에이전트의 데이터가 최대 버퍼 크기인 50MB로 대기될 수 있습니다. 명령 `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`를 실행하여 Linux용 OMS 에이전트를 다시 시작해야 합니다. 

    >[!NOTE]
    >이 문제는 에이전트 버전 1.1.0-28 및 이상에서 해결되었습니다.


