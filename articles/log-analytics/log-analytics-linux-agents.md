---
title: "Azure Log Analytics에 Linux 컴퓨터 연결 | Microsoft Docs"
description: "Log Analytics를 사용하여 Linux 컴퓨터에서 생성되는 데이터를 수집하고 그에 따른 조치를 취할 수 있습니다."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ab5b76d8-9ab5-406e-8768-76fb0632d830
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: b01b0d3d61168c1eec52f3fd040b829e0c51a878
ms.lasthandoff: 03/30/2017


---
# <a name="connect-your-linux-computers-to-log-analytics"></a>Log Analytics에 Linux 컴퓨터 연결
Log Analytics를 사용하여 Linux 컴퓨터에서 생성되는 데이터를 수집하고 그에 따른 조치를 취할 수 있습니다. Linux에서 수집된 데이터를 OMS에 추가하면 컴퓨터 위치에 상관없이(사실상 모든 곳에서), Linux 시스템 및 Docker 같은 컨테이너 솔루션을 관리할 수 있습니다. 데이터 원본은 물리적 서버로서 온-프레미스 데이터 센터, Amazon Web Services(AWS) 또는 Microsoft Azure 같은 클라우드 호스티드 서비스의 가상 컴퓨터나 사용자 책상 위 노트북에도 상주할 수 있습니다. 뿐만 아니라 OMS는 Windows 컴퓨터에서도 유사하게 데이터를 수집하기 때문에 진정한 하이브리드 IT 환경을 지원합니다.

하나의 관리 포털을 통해 OMS의 Log Analytics에서 이 모든 소스의 데이터를 보고 관리할 수 있습니다. 이렇게 하면 많은 다양한 시스템을 사용하여 데이터를 모니터링할 필요가 줄어들고, 데이터를 사용하기가 쉬워지고, 원하는 데이터를 무엇이든 이미 보유하고 있는 비즈니스 분석 솔루션이나 시스템에 내보낼 수 있습니다.

이 문서는 Linux용 OMS 에이전트를 사용하여 Linux 컴퓨터에 대한 데이터를 수집하고 관리하도록 돕는 빠른 시작 가이드입니다. 프록시 서버 구성, CollectD 메트릭에 대한 정보, 사용자 지정 JSON 데이터 원본과 같은 자세한 기술 정보는 GitHub의 [Linux용 OMS 에이전트 개요](https://github.com/Microsoft/OMS-Agent-for-Linux)(영문) 및 [Linux용 OMS 에이전트 전체 설명서](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)(영문)를 참조하세요.

현재는 Linux 컴퓨터에서 다음과 같은 유형의 데이터를 수집할 수 있습니다.

* 성능 메트릭
* Syslog 이벤트
* Nagios 및 Zabbix의 경고
* Docker 컨테이너 성능 메트릭, 인벤토리 및 로그

## <a name="supported-linux-versions"></a>지원되는 Linux 버전
x86 및 x64 버전 모두 다양한 Linux 배포에 대해 공식적으로 지원됩니다. 하지만, Linux용 OMS 에이전트는 나열되지 않은 그 밖의 배포에서 실행이 가능할 수 있습니다.

* Amazon Linux 2012.09~2015.09
* CentOS Linux 5, 6, 7
* Oracle Linux 5, 6, 7
* Red Hat Enterprise Linux Server 5, 6, 7
* Debian GNU/Linux 6, 7, 8
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10
* SUSE Linux Enterprise Server 11 및 12

## <a name="oms-agent-for-linux"></a>Linux 용 OMS 에이전트
Linux용 Operations Management Suite 에이전트는 여러 패키지로 구성됩니다. 다음 패키지를 포함하는 릴리스 파일은 `--extract`와 함께 shell 번들을 실행하면 사용할 수 있습니다.

| **패키지** | **버전** | **설명** |
| --- | --- | --- |
| omsagent |1.1.0 |Linux용 Operations Management Suite 에이전트 |
| omsconfig |1.1.1 |OMS 에이전트용 구성 에이전트 |
| omi |1.0.8.3 |Open Management Infrastructure(OMI) -- 경량 CIM 서버 |
| scx |1.6.2 |운영 체제 성능 메트릭용 OMI CIM 공급자 |
| apache-cimprov |1.0.0 |OMI용 Apache HTTP 서버 성능 모니터링 공급자. Apache HTTP 서버가 감지되는 경우에만 설치됨. |
| mysql-cimprov |1.0.0 |OMI용 MySQL 서버 성능 모니터링 공급자. MySQL/MariaDB 서버가 감지되는 경우에만 설치됨. |
| docker-cimprov |0.1.0 |OMI용 Docker 공급자. Docker가 감지되는 경우에만 설치됨. |

### <a name="additional-installation-artifacts"></a>추가적인 설치 아티팩트
Linux용 OMS 에이전트 패키지를 설치한 후에는 다음과 같은 시스템 수준의 구성 변경이 추가로 적용됩니다. 이러한 아티팩트는 omsagent 패키지가 제거되면 제거됩니다.

* `omsagent` 라는 이름의 권한 없는 사용자가 생성됩니다. 이 계정으로 omsagent 디먼이 실행됩니다.
* sudoer의 “include” 파일은 /etc/sudoers.d/omsagent에 생성됩니다 syslog 및 omsagent 디먼을 다시 시작하도록 omsagent에게 권한을 부여합니다. 설치된 sudo 버전에서 sudo “include” 지시문이 지원되지 않으면, 이 항목은 /etc/sudoers에 기록됩니다.
* syslog 구성은 이벤트 하위 집합을 에이전트에 전달하도록 수정됩니다. 자세한 내용은 아래의 **데이터 수집 구성** 섹션을 참조하세요.

### <a name="linux-data-collection-details"></a>Linux 데이터 수집 세부 정보
다음 테이블은 데이터 수집 방법 및 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 원본 | 직접 에이전트 | SCOM 에이전트 | Azure 저장소 | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- |
| Zabbix |![예](./media/log-analytics-linux-agents/oms-bullet-green.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |1분 |
| Nagios |![예](./media/log-analytics-linux-agents/oms-bullet-green.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |도착 시 |
| syslog |![예](./media/log-analytics-linux-agents/oms-bullet-green.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |Azure 저장소: 10분, 에이전트: 도착 시 |
| Linux 성능 카운터 |![예](./media/log-analytics-linux-agents/oms-bullet-green.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |예약된 대로, 최소 10초 |
| 변경 내용 추적 |![예](./media/log-analytics-linux-agents/oms-bullet-green.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png) |매시간 |

### <a name="package-requirements"></a>패키지 요구 사항
| **필수 패키지** | **설명** | **최소 버전** |
| --- | --- | --- |
| Glibc |GNU C 라이브러리 |2.5-12 |
| Openssl |OpenSSL 라이브러리 |0.9.8e 또는 1.0 |
| Curl |cURL 웹 클라이언트 |7.15.5 |
| Python-ctypes |함수 라이브러리 |해당 없음 |
| PAM |플러그형 인증 모듈 |해당 없음 |

> [!NOTE]
> Syslog 메시지를 수집하려면 rsyslog 또는 syslog-ng가 필요합니다. Red Hat Enterprise Linux 버전 5, CentOS 및 Oracle Linux 버전(sysklog)에서는 syslog 이벤트 수집을 위한 기본 syslog 디먼이 지원되지 않습니다. 이 배포의 해당 버전에서 syslog 데이터를 수집하려면 rsyslog 디먼을 설치하고 sysklog를 대체하도록 구성해야 합니다.
>
>

## <a name="quick-install"></a>빠른 설치
다음 명령을 실행하여 omsagent를 다운로드하고, 체크섬의 유효성을 검사한 다음 에이전트를 설치하고 등록합니다. 명령은 64비트용입니다. 작업 영역 ID 및 기본 키는 OMS 포털에서 **연결된 원본** 탭의 **설정**에 있습니다.

![작업 영역 정보](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

에이전트를 설치하고 업그레이드하는 방법은 매우 다양합니다. 자세한 내용은 [Steps to install the OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux)(Linux용 OMS 에이전트를 설치하는 단계)를 참조하세요.

[Azure 연습 동영상](https://www.youtube.com/watch?v=mF1wtHPEzT0)을 볼 수도 있습니다.

## <a name="choose-your-linux-data-collection-method"></a>Linux 데이터 수집 방법 선택
수집할 데이터 유형을 선택하는 방식은 OMS 포털을 사용할지 또는 자신의 Linux 클라이언트에서 다양한 구성 파일을 직접 편집할지에 따라 달라집니다. 포털을 사용하기로 선택하면, 구성은 사용자의 모든 Linux 클라이언트에 자동으로 보내집니다. 여러 Linux 클라이언트에 다른 구성이 필요하면, 클라이언트 파일을 개별적으로 편집하거나 PowerShell DSC, Chef, 또는 Puppet 같은 대안을 사용해야 합니다.

Linux 컴퓨터의 구성 파일을 사용하여 수집할 성능 카운터와 syslog 이벤트를 지정할 수 있습니다. *에이전트 구성 파일을 편집하여 데이터 수집을 구성하기로 선택하면, 중앙 집중식 구성을 비활성화해야 합니다.*  에이전트 구성 파일에서 데이터 수집을 구성하는 것은 물론 Linux용 OMS 에이전트 또는 개별 컴퓨터에 대해 중앙 집중식 구성을 비활성화하는 지침이 아래에 제공됩니다.

### <a name="disable-oms-management-for-an-individual-linux-computer"></a>개별 Linux 컴퓨터에 대한 OMS 관리 비활성화
구성 데이터에 대한 중앙 집중식 데이터 수집은 OMS_MetaConfigHelper.py 스크립트를 통해 개별 Linux 컴퓨터에 대해 비활성화됩니다. 이것은 컴퓨터 하위 집합에 특별한 구성이 필요한 경우에 유용합니다.

중앙 집중식 구성을 비활성화하려면:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

중앙 집중식 구성을 다시 활성화하려면:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## <a name="linux-performance-counters"></a>Linux 성능 카운터
Linux 성능 카운터는 Windows 성능 카운터와 유사합니다. 둘 다 비슷하게 작동합니다. 다음 절차를 사용하여 추가하고 구성할 수 있습니다. OMS에 추가되면, 30초마다 데이터가 수집됩니다.

### <a name="to-add-a-linux-performance-counter-in-oms"></a>OMS에서 Linux 성능 카운터를 추가하려면:
1. OMS 포털을 사용하여 Linux용 OMS 에이전트를 구성하려면, 설정 페이지에서 Linux 성능 카운터를 추가하고 **데이터**를 클릭합니다.  
2. **데이터**의 **설정** 페이지에서, **Linux 성능 카운터**를 클릭한 다음 추가하려는 카운터의 이름을 선택하거나 입력합니다.  
    ![데이터](./media/log-analytics-linux-agents/oms-settings-data01.png)
3. 카운터의 전체 이름을 모르는 경우, 이름의 일부를 입력하기 시작하면 사용 가능한 카운터 목록이 표시됩니다. 추가할 카운터를 발견하면, 목록에서 이름을 클릭한 다음 더하기 아이콘을 클릭하여 카운터를 추가합니다.
4. 카운터를 추가하면, 카운터 목록에 컬러 막대로 강조 표시됩니다.
5. **Apply below configuration to my machines** (내 컴퓨터에 아래 구성 적용) 옵션은 기본적으로 선택되어 있습니다. 구성 데이터 보내기를 비활성화하려면, 이 선택을 해제합니다.
6. 성능 카운터 수정을 완료하면, 페이지 맨 아래에서 **저장** 을 클릭하여 변경을 마무리합니다. 그러면 변경한 구성 내용이 OMS에 등록된 모든 Linux용 OMS 에이전트로 보내집니다(일반적으로 5분 이내).

### <a name="configure-linux-performance-counters-in-oms"></a>OMS에서 Linux 성능 카운터 구성
Windows 성능 카운터의 경우, 각 성능 카운터에 대해 특정 인스턴스를 선택할 수 있습니다. 하지만 Linux 성능 카운터의 경우, 어떤 카운터 인스턴스를 선택하든, 부모 카운터의 모든 자식 카운터에 적용됩니다. 다음 테이블은 Linux와 Windows 성능 카운터 모두에서 사용할 수 있는 공통 인스턴스를 보여줍니다.

| **인스턴스 이름** | **의미** |
| --- | --- |
| \_합계 |모든 인스턴스의 총계 |
| \* |모든 인스턴스 |
| (/&#124;/var) |/ 또는 /var로 명명된 인스턴트와 일치 |

마찬가지로, 부모 카운터에 대해 선택한 샘플 간격은 모든 자식 카운터에 적용됩니다. 즉, 모든 자식 카운터 샘플 간격 및 인스턴스는 함께 연결되어 있습니다.

### <a name="add-and-configure-performance-metrics-with-linux"></a>Linux에서 성능 메트릭 추가 및 구성
수집할 성능 메트릭은 /etc/opt/microsoft/omsagent/&lt;workspace id&gt;/conf/omsagent.conf의 구성으로 제어됩니다. Linux 용 OMS 에이전트에 사용할 수 있는 클래스 및 메트릭은 [Available performance metrics](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics) (사용 가능한 성능 메트릭)을 참조하세요.

수집할 성능 메트릭의 각 개체나 범주는 구성 파일 내에 단일 `<source>` 요소로 정의되어야 합니다. 구문은 아래와 같은 패턴을 따릅니다.

```
<source>
  type oms_omi  
  object_name "Processor"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

이 요소에서 구성할 수 있는 매개 변수는 다음과 같습니다.

* **Object\_name**: 수집하는 개체의 이름입니다.
* **Instance\_regex**: 수집할 인스턴스를 정의하는 *정규식*입니다. `.*` 값은 모든 인스턴스를 지정합니다. \_Total 인스턴스에 대해서만 프로세서 메트릭을 수집하려면 `_Total`을 지정합니다. crond 또는 sshd 인스턴스에 대해서만 프로세서 메트릭을 수집하려면 `(crond|sshd)`를 지정합니다.
* **Counter\_name\_regex**: 수집할 (개체에 대한) 카운터를 정의하는 *정규식*입니다. 개체에 대한 모든 카운터를 수집하려면 `.*`를 지정합니다. 메모리 개체에 대한 스왑 공간 카운터만 수집하려면 `.+Swap.+`
* **Interval:**: 개체의 카운터가 수집되는 빈도입니다.

성능 메트릭에 대한 기본 구성은 다음과 같습니다.

```
<source>
  type oms_omi
  object_name "Physical Disk"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Logical Disk"
  instance_regex ".*
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Processor"
  instance_regex ".*
  counter_name_regex ".*"
  interval 30s
</source>

<source>
  type oms_omi
  object_name "Memory"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

### <a name="enable-mysql-performance-counters-using-linux-commands"></a>Linux 명령을 사용하여 MySQL 성능 카운터 활성화
omsagent 번들이 설치된 경우 컴퓨터에서 MySQL 서버나 MariaDB 서버가 감지되면, MySQL 서버용 성능 모니터링 공급자가 자동으로 설치됩니다. 이 공급자는 성능 통계를 공개하기 위해 로컬 MySQL/MariaDB 서버에 연결합니다. 공급자가 MySQL 서버에 액세스할 수 있도록 MySQL 사용자 자격 증명을 구성해야 합니다.

localhost에서 MySQL 서버의 기본 사용자 계정을 정의하려면 다음 명령 예제를 사용합니다.

> [!NOTE]
> 자격 증명 파일은 omsagent 계정이 읽을 수 있어야 합니다. omsgent로 mycimprovauth 명령을 실행하는 것이 좋습니다.
>
>

```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>

sudo /opt/omi/bin/service_control restart
```


또는 /var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth 파일을 생성하여, 필요한 MySQL 자격 증명을 파일 내에 지정할 수 있습니다. mysql-auth 파일을 통해 모니터링을 위한 MySQL 자격 증명을 관리하는 방법에 대한 자세한 내용은 [Manage MySQL monitoring credentials in the authentication file](#manage-mysql-monitoring-credentials-in-the-authentication-file)(인증 파일에서 MySQL 모니터링 자격 증명 관리)을 참조하세요.

MySQL 서버 성능 데이터를 수집하기 위해 MySQL 사용자에게 필요한 개체 권한에 대한 자세한 내용은 [Database permissions required for MySQL performance counters](#database-permissions-required-for-mysql-performance-counters) (MySQL 성능 카운터에 필요한 데이터베이스 권한)를 참조하세요.

### <a name="enable-apache-http-server-performance-counters-using-linux-commands"></a>Linux 명령을 사용하여 Apache HTTP 서버 성능 카운터 활성화
omsagent 번들이 설치된 경우 컴퓨터에서 Apache HTTP 서버가 감지되면, Apache HTTP 서버용 성능 모니터링 공급자가 자동으로 설치됩니다. 이 공급자는 성능 데이터에 액세스하기 위해 Apache HTTP 서버에 로드되어야 하는 Apache "모듈"에 의존합니다.

모듈은 다음 명령을 사용하여 로드할 수 있습니다.

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Apache 모니터링 모듈을 언로드하려면, 다음 명령을 실행합니다.

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### <a name="to-view-performance-data-with-log-analytics"></a>Log Analytics에서 성능 데이터를 보려면
1. Operations Management Suite 포털에서 로그 검색 타일을 클릭합니다.
2. 모든 성능 카운터를 보려면, 검색 창에 `* (Type=Perf)` 를 입력합니다.

OMS는 Windows 성능 카운터 데이터도 수집하기 때문에 Linux에 해당하는 데이터로 검색 범위를 좁혀야 합니다. 다음 예제는 Chorizo21이라는 이름의 예제 Linux 서버에 해당하는 성능 데이터를 보여줍니다.

```
Type=Perf Computer=chorizo*
```

![검색 결과에 표시된 예제 서버](./media/log-analytics-linux-agents/oms-perfsearch01.png)

결과에서 **메트릭** 을 클릭하면 데이터가 수집된 카운터를 볼 수 있습니다. 각 카운터에 대한 실시간 데이터가 그래프로 표시됩니다.

![메트릭](./media/log-analytics-linux-agents/oms-perfmetrics01.png)

## <a name="syslog"></a>syslog
Syslog는 Windows 이벤트 로그와 유사한 이벤트 기록 프로토콜입니다. OMS에 표시되면 둘 다 유사하게 작동합니다.

### <a name="to-add-a-new-linux-syslog-facility-in-oms"></a>OMS에서 새 Linux syslog 기능을 추가하려면
1. **데이터**의 **설정** 페이지에서, **Syslog**를 클릭한 다음 더하기 아이콘 왼쪽에 추가할 syslog 기능의 이름을 입력합니다.
    ![Linux syslog](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2. 기능의 전체 이름을 모르는 경우, 이름의 일부를 입력하기 시작하면 사용 가능한 syslog 기능 목록이 표시됩니다. 추가할 syslog 기능을 발견하면, 목록에서 이름을 클릭한 다음 더하기 아이콘을 클릭하여 syslog 기능을 추가합니다.
3. 기능을 추가하면, 컬러 막대로 강조 표시된 목록에 표시됩니다. 다음으로 수집할 심각도(syslog 기능 범주 정보)를 선택합니다.
4. 페이지 맨 아래에서 **저장** 을 클릭하여 변경을 마무리합니다. 그러면 변경한 구성 내용이 OMS에 등록된 모든 Linux용 OMS 에이전트로 보내집니다(일반적으로 5분 이내).

### <a name="configure-linux-syslog-facilities-in-linux"></a>Linux에서 Linux syslog 기능 구성
Syslog 이벤트는 Syslog 디먼(예 rsyslog 또는 syslog-ng)에서 에이전트가 수신 대기 중인 로컬 포트로 보내집니다. 기본 포트는 25224입니다. 에이전트가 설치될 때 기본 syslog 구성이 적용됩니다. 위치는 다음과 같습니다.

Rsyslog: /etc/rsyslog.d/rsyslog-oms.conf

Syslog-ng: /etc/syslog-ng/syslog-ng.conf

기본 OMS 에이전트 syslog 구성은 심각도가 경고 이상인 모든 기능의 syslog 이벤트를 업로드합니다.

> [!NOTE]
> Syslog 구성을 편집하는 경우, 변경 내용을 적용하려면 syslog 디먼을 다시 시작해야 합니다.
>
>

OMS의 Linux 용 OMS 에이전트에 대한 기본 syslog 구성은 다음과 같습니다.

#### <a name="rsyslog"></a>Rsyslog
```
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>Syslog-ng
```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### <a name="to-view-all-syslog-events-with-log-analytics"></a>Log Analytics에서 모든 Syslog 이벤트를 보려면
1. Operations Management Suite 포털에서 **로그 검색** 타일을 클릭합니다.
2. **로그 관리** 그룹화에서 미리 정의된 syslog 검색을 선택한 다음 실행할 항목을 선택합니다.

이 예제는 모든 Syslog 이벤트를 보여줍니다.

![로그 검색에 표시된 Syslog 이벤트](./media/log-analytics-linux-agents/oms-linux-syslog.png)

이제 검색 결과를 세부적으로 들여다 볼 수 있습니다.

## <a name="linux-alerts"></a>Linux 경고
Nagios 또는 Zabbix를 사용하여 Linux 컴퓨터를 관리하는 경우라면, OMS는 이런 도구에서 생성되는 경고를 수신할 수 있습니다. 하지만, OMS 포털을 사용하여 들어오는 경고 데이터를 구성하는 방법이 현재는 없습니다. 대신, OMS에 경고 보내기를 시작하도록 구성 파일을 편집해야 합니다.

### <a name="collect-alerts-from-nagios"></a>Nagios의 경고 수집
Nagios 서버의 경고를 수집하려면, 다음 구성을 변경해야 합니다.

1. **omsagent** 사용자에게 Nagios 로그 파일(예: /var/log/nagios/nagios.log)에 대한 읽기 권한을 부여합니다. nagios.log 파일이 **nagios** 그룹의 소유라는 가정 하에, **omsagent** 사용자를 **nagios** 그룹에 추가합니다.

    ```
    sudo usermod –a -G nagios omsagent
    ```
2. omsagent.conf 구성 파일(/etc/opt/microsoft/omsagent/&lt;workspace id&gt;/conf/omsagent.conf)을 수정합니다. 다음 항목이 포함되고 주석 처리되지 않도록 합니다.

    ```
    <source>
    type tail
    #Update path to point to your nagios.log
    path /var/log/nagios/nagios.log
    format none
    tag oms.nagios
    </source>

    <filter oms.nagios>
    type filter_nagios_log
    </filter>
    ```
3. omsagent 디먼을 다시 시작합니다.

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="collect-alerts-from-zabbix"></a>Zabbix의 경고 수집
Zabbix 서버의 경고를 수집하려면 위의 Nagios와 유사한 단계를 수행합니다. 단, 사용자 및 암호를 *일반 텍스트*로 지정해야 합니다. 바람직하지는 않지만 곧 변경될 것입니다. 이 문제에 대처하려면, 사용자를 생성하고 모니터링 권한만 부여하는 것이 좋습니다.

Zabbix용 omsagent.conf 구성 파일(/etc/opt/microsoft/omsagent/&lt;workspace id&gt;/conf/omsagent.conf)의 예제 섹션은 다음과 비슷해야 합니다.

```
<source>
  type zabbix_alerts
  run_interval 1m
  tag oms.zabbix
  zabbix_url http://localhost/zabbix/api_jsonrpc.php
  zabbix_username Admin
  zabbix_password zabbix
</source>

```

### <a name="view-alerts-in-log-analytics-search"></a>Log Analytics 검색에서 경고 보기
OMS에 경고를 보내도록 Linux 컴퓨터를 구성한 후에, 몇 가지 간단한 로그 검색 쿼리를 사용하여 경고를 볼 수 있습니다. 다음 검색 쿼리 예제는 생성되어 기록된 모든 경고를 반환합니다. 예를 들어, IT 인프라 내에서 어떤 문제가 발생하면, 다음 예제 쿼리의 결과는 문제가 발생했을 만한 위치를 나타냅니다. 그러면, 원본 시스템의 경고를 손쉽게 세부적으로 들여다 보고 조사 범위를 좁힐 수 있습니다. 장점은 처음부터 다양한 관리 시스템으로 이동할 필요가 없다는 점입니다. 경고가 OMS로 보내지기만 한다면, OMS에서 작업을 시작할 수 있습니다.

```
Type=Alert
```

#### <a name="to-view-all-nagios-alerts-with-log-analytics"></a>Log Analytics에서 모든 Nagios 경고를 보려면
1. Operations Management Suite 포털에서 **로그 검색** 타일을 클릭합니다.
2. 쿼리 표시줄에 다음 검색 쿼리를 입력합니다.

    ```
    Type=Alert SourceSystem=Nagios
    ```
   ![로그 검색에 표시된 Nagios 경고](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

검색 결과를 본 다음, *AlertState*같은 추가적인 세부 정보를 들여다 볼 수 있습니다.

### <a name="to-view-all-zabbix-alerts-with-log-analytics"></a>Log Analytics에서 모든 Zabbix 경고를 보려면
1. Operations Management Suite 포털에서 **로그 검색** 타일을 클릭합니다.
2. 쿼리 표시줄에 다음 검색 쿼리를 입력합니다.

    ```
    Type=Alert SourceSystem=Zabbix
    ```
   ![로그 검색에 표시된 Zabbix 경고](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

검색 결과를 본 다음, *AlertName*같은 추가적인 세부 정보를 들여다 볼 수 있습니다.

## <a name="compatibility-with-system-center-operations-manager"></a>System Center Operations Manager와의 호환성
Linux 용 OMS 에이전트는 System Center Operations Manager 에이전트와 에이전트 바이너리를 공유합니다. Operations Manager로 관리되는 시스템에 Linux용 OMS 에이전트를 설치하면, 컴퓨터의 OMI 및 SCX 패키지가 새 버전으로 업그레이드됩니다. Linux용 OMS 에이전트와 System Center 2012 R2는 호환됩니다. 하지만, **System Center 2012 SP1 이전 버전은 현재 Linux용 OMS 에이전트와 호환되거나 지원되지 않습니다.**

> [!NOTE]
> Operations Manager로 관리되지 않는 컴퓨터에 Linux용 OMS 에이전트를 설치한 다음, 나중에 Operations Manager로 컴퓨터를 관리하려는 경우에는, 컴퓨터를 검색하기 전에 OMI 구성을 반드시 수정해야 합니다. **Operations Manager 에이전트가 Linux용 OMS 에이전트보다 먼저 설치되면 이 단계가 필요하지 않습니다.**
>
>

### <a name="to-enable-the-oms-agent-for-linux-to-communicate-with-operations-manager"></a>Linux용 OMS 에이전트가 Operations Manager와 통신하도록 설정하려면
1. /etc/opt/omi/conf/omiserver.conf 파일을 편집합니다.
2. **httpsport=** 로 시작되는 줄이 1270 포트를 지정하도록 합니다. 예: `httpsport=1270`
3. OMI 서버를 다시 시작합니다.

    ```
    sudo /opt/omi/bin/service_control restart
    ```

## <a name="database-permissions-required-for-mysql-performance-counters"></a>Database permissions required for MySQL performance counters
MySQL 모니터링 사용자에게 권한을 부여하려면, 권한을 부여하는 사용자에게 'GRANT option' 권한은 물론 부여되는 권한에 대한 권한이 있어야 합니다.

MySQL 사용자가 성능 데이터를 반환하려면 해당 사용자는 다음 쿼리에 대한 액세스가 있어야 합니다.

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

이러한 쿼리 외에, MySQL 사용자는 다음과 같은 기본 테이블에 대해 SELECT 액세스가 필요합니다.

* information_schema
* mysql

이러한 권한은 다음과 같은 권한 부여 명령을 실행하여 부여될 수 있습니다.

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## <a name="manage-mysql-monitoring-credentials-in-the-authentication-file"></a>Manage MySQL monitoring credentials in the authentication file
다음 섹션은 MySQL 자격 증명을 관리하는 데 도움이 됩니다.

### <a name="configure-the-mysql-omi-provider"></a>MySQL OMI 공급자 구성
MySQL OMI 공급자가 MySQL 인스턴스의 성능/상태 정보를 쿼리하려면, 미리 정의된 MySQL 사용자와 설치되어 있는 MySQL 클라이언트 라이브러리가 필요합니다.

### <a name="mysql-omi-authentication-file"></a>MySQL OMI 인증 파일
MySQL OMI 공급자는 인증 파일을 사용하여 MySQL 인스턴스가 수신 대기할 바인딩 주소와 포트를 결정하고 메트릭 수집에 사용할 자격 증명을 결정합니다. 설치하는 동안 MySQL OMI 공급자는 MySQL my.cnf 구성 파일(기본 위치)에서 바인딩 주소와 포트를 검색하고, MySQL OMI 인증 파일을 부분적으로 설정합니다.

MySQL 서버 인스턴스에 대한 모니터링을 완료하려면, 미리 생성된 MySQL OMI 인증 파일을 올바른 디렉터리에 추가합니다.

### <a name="authentication-file-format"></a>인증 파일 형식
MySQL OMI 인증 파일은 다음 항목에 대한 정보를 포함하는 텍스트 파일입니다.

* 포트
* 바인딩 주소
* MySQL 사용자 이름
* Base64로 인코딩된 암호

MySQL OMI 인증 파일은 파일을 생성한 Linux 사용자에게만 읽기/쓰기 권한을 부여합니다.

```
[Port]=[Bind-Address], [username], [Base64 encoded Password]
(Port)=(Bind-Address), (username), (Base64 encoded Password)
(Port)=(Bind-Address), (username), (Base64 encoded Password)
AutoUpdate=[true|false]
```

기본 MySQL OMI 인증 파일은 MySQL 구성 파일에서 찾은 정보 중 사용이 가능하고 구문 분석된 정보에 따라 기본 인스턴스 및 포트 번호를 포함합니다.

기본 인스턴스는 하나의 Linux 호스트에서 여러 개의 MySQL 인스턴스를 쉽게 관리하도록 만드는 수단이며, 포트가 0인 인스턴스로 표시됩니다. 추가된 모든 인스턴스는 기본 인스턴스로부터 속성 집합을 상속합니다. 예를 들어, '3308' 포드에서 수신 대기 중인 MySQL 인스턴스가 추가되면, 3308 포트에서 수신 대기 중인 인스턴스를 시도하고 모니터링하기 위해, 기본 인스턴스의 바인딩 주소, 사용자 이름, Base64로 인코딩된 암호가 사용됩니다. 3308 포트에서 수신 대기 중인 인스턴스가 다른 주소로 바인딩되고 동일한 MySQL 사용자 이름과 암호 쌍이 사용되면, 바인딩 주소만 다시 지정하면 되고 다른 속성은 상속됩니다.

인증 파일의 예제는 다음과 유사합니다.

기본 인스턴스 및 포트 3308 인스턴스:

```
0=127.0.0.1, myuser, cnBwdA==3308=, ,AutoUpdate=true
```

기본 인스턴스 및 포트 3308 및 Base64로 인코딩된 암호가 다른 인스턴스:

```
0=127.0.0.1, myuser, cnBwdA==3308=127.0.1.1, , AutoUpdate=true
```


| **속성** | **설명** |
| --- | --- |
| 포트 |포트는 MySQL 인스턴스가 수신 대기 중인 현재 포트를 나타냅니다.  포트 0은 뒤에 나오는 속성이 기본 인스턴스에 사용된다는 것을 의미합니다. |
| 바인딩 주소 |바인딩 주소는 현재 MySQL 바인딩 주소입니다. |
| username |MySQL 서버 인스턴스를 모니터링하는 데 사용할 MySQL 사용자의 사용자 이름입니다. |
| Base64로 인코딩된 암호 |Base64로 인코딩된 MySQL 모니터링 사용자의 암호입니다. |
| AutoUpdate |MySQL OMI 공급자가 업그레이드되면 공급자는 my.cnf 파일에서 변경 내용을 검색하고 MySQL OMI 인증 파일을 덮어씁니다. MySQL OMI 인증 파일에 필요한 업데이트에 따라 이 플래그를 true 또는 false로 설정합니다. |

#### <a name="authentication-file-location"></a>인증 파일 위치
MySQL OMI 인증 파일은 다음 경로에 위치해야 하며 이름을 "mysql-auth"라고 지정해야 합니다.

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

파일(및 auth/omsagent 디렉터리)는 omsagent 사용자가 소유해야 합니다.

## <a name="agent-logs"></a>에이전트 로그
Linux 용 OMS 에이전트 로그의 위치:

/var/opt/microsoft/omsagent/&lt;workspace id&gt;/log/

omsconfig(에이전트 구성) 프로그램에 대한 Linux 용 OMS 에이전트 로그의 위치:

/var/opt/microsoft/omsconfig/log/

OMI 및 SCX 구성 요소(성능 메트릭 데이터를 제공하는) 로그의 위치:

/var/opt/omi/log/ and /var/opt/microsoft/scx/log

## <a name="troubleshooting-the-oms-agent-for-linux"></a>Linux 용 OMS 에이전트 문제 해결
다음 정보를 사용하여 일반적 문제를 진단 및 해결합니다.

이 섹션의 문제 해결 정보로 해결되지 않는 경우 다음 리소스를 사용하여 문제를 해결해 볼 수 있습니다.

* 프리미어 지원이 적용되는 고객의 경우 [프리미어](https://premier.microsoft.com/)를 통해 지원 사례를 기록할 수 있습니다.
* Azure 지원 계약이 있는 고객은 [Azure Portal](https://manage.windowsazure.com/?getsupport=true)에서 지원 사례를 기록할 수 있습니다.
* [GitHub 문제](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) 제출
* 아이디어를 얻고 버그 보고서를 작성할 수 있는 피드백 포럼 [http://aka.ms/opinsightsfeedback](http://aka.ms/opinsightsfeedback)

### <a name="important-log-locations"></a>중요 로그 위치
| 파일 | Path |
| --- | --- |
| Linux 용 OMS 에이전트 로그 파일 |`/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log ` |
| OMS 에이전트 구성 로그 파일 |`/var/opt/microsoft/omsconfig/omsconfig.log` |

### <a name="important-configuration-files"></a>중요 구성 파일
| 범주 | 파일 위치 |
| --- | --- |
| syslog |`/etc/syslog-ng/syslog-ng.conf` 또는 `/etc/rsyslog.conf` 또는 `/etc/rsyslog.d/95-omsagent.conf` |
| 성능, Nagios, Zabbix, OMS 출력 및 일반 에이전트 |`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` |
| 추가 구성 |`/etc/opt/microsoft/omsagent/<workspace id>/omsagent.d/*.conf` |

> [!NOTE]
> OMS 포털 구성을 사용하도록 설정한 경우 시스템에서 성능 카운터 및 syslog의 편집 구성 파일을 덮어씁니다. OMS 포털에서 구성을 비활성화하거나(전체 노드) 단일 노드의 경우 다음을 실행합니다.
>
>

```
sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```


### <a name="enable-debug-logging"></a>디버그 로깅 활성화
디버그 로깅을 활성화하려면 OMS 출력 플러그인 및 자세한 정보 출력을 사용할 수 있습니다.

#### <a name="oms-output-plugin"></a>OMS 출력 플러그인
FluentD를 사용하면 플러그인에서 입력 및 출력의 다양한 로그 수준에 대해 로깅 수준을 지정할 수 있습니다. OMS 출력에 대해 다른 로그 수준을 지정하려면 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 파일에서 일반 에이전트 구성을 편집합니다.

구성 파일 아래쪽에서 `log_level` 속성을 `info`에서 `debug`로 변경합니다.

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

디버그 로깅을 사용하면 OMS 서비스에 대한 배치 업로드를 형식, 데이터 항목 수, 전송 소요 시간으로 구분하여 확인할 수 있습니다.

*디버그 사용 로그 예:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

#### <a name="verbose-output"></a>자세한 정보 출력
OMS 출력 플러그인을 사용하는 대신 데이터 항목을 `stdout`으로 직접 출력할 수 있으며, 이 데이터 항목은 Linux용 OMS 에이전트 로그 파일에서 확인할 수 있습니다.

`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`의 OMS 일반 에이전트 구성 파일에서 각 라인 앞에 `#` 추가하여 OMS 출력 플러그인을 주석 처리합니다.

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

출력 플러그인 아래에서 각 라인의 맨 앞에 있는 `#` 기호를 제거하여 다음 섹션의 주석을 제거합니다.

```
<match **>
  type stdout
</match>
```

### <a name="forwarded-syslog-messages-do-not-appear-in-the-log"></a>전달된 Syslog 메시지가 로그에 나타나지 않음
#### <a name="probable-causes"></a>가능한 원인
* Linux 서버에 적용된 구성에서는 전송된 시설 및/또는 로그 수준을 수집할 수 없습니다.
* Syslog가 Linux 서버로 올바르게 전달되지 않고 있습니다.
* 초당 전달되는 메시지 수가 너무 많아 Linux용 OMS 에이전트의 기본 구성에서 처리할 수 없습니다.

#### <a name="resolutions"></a>해결 방법
* Syslog용 OMS 포털의 구성에 모든 설비 및 올바른 로그 수준이 있는지 확인합니다.
  * **OMS 포털 > 설정 > 데이터 > Syslog**
* 네이티브 syslog 메시징 디먼(`rsyslog`, `syslog-ng`)이 전달된 메시지를 수신할 수 있는지 확인합니다.
* Syslog 서버에서 방화벽 설정을 확인하여 메시지가 차단되지 않는지 확인합니다.
* `logger` 명령을 사용하여 OMS에 대한 Syslog 메시지를 시뮬레이션합니다. 예:
  * `logger -p local0.err "This is my test message"`

### <a name="problems-connecting-to-oms-when-using-a-proxy"></a>프록시를 사용할 때 OMS에 연결되지 않음
#### <a name="probable-causes"></a>가능한 원인
* 에이전트를 설치 및 구성할 때 지정한 프록시가 잘못되었습니다.
* OMS 서비스 끝점이 데이터 센터의 허용 목록에 없습니다.

#### <a name="resolutions"></a>해결 방법
* 다음 명령과 `-v` 옵션을 사용하여 Linux용 OMS 에이전트를 다시 설치합니다. OMS 서비스에 대한 프록시를 통해 연결되는 에이전트의 자세한 정보를 출력할 수 있습니다.
  * `/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`
  * [HTTP 프록시 서버에 사용할 에이전트 구성](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#configuring-the-agent-for-use-with-an-http-proxy-server)에서 OMS 프록시에 대한 문서를 검토합니다.
* 다음 OMS 서비스 끝점이 허용 목록에 있는지 확인합니다.

| 에이전트 리소스 | 포트 |
| --- | --- |
| &#42;.ods.opinsights.azure.com |포트 443 |
| &#42;.oms.opinsights.azure.com |포트 443 |
| ods.systemcenteradvisor.com |포트 443 |
| &#42;.blob.core.windows.net/ |포트 443 |

### <a name="a-403-error-is-displayed-when-onboarding"></a>등록 시 403 오류가 표시됨
#### <a name="probable-causes"></a>가능한 원인
* Linux 서버의 날짜 및 시간이 올바르지 않습니다
* 사용된 작업 영역 ID 및 작업 영역 키가 올바르지 않습니다.

#### <a name="resolution"></a>해결 방법
* `date` 명령을 사용하여 Linux 서버에서 시간을 확인합니다. 데이터가 현재 시간보다 15분 넘게 빠르거나 느릴 경우 등록이 실패합니다. 이 문제를 해결하려면 Linux 서버의 날짜 및/또는 시간대를 업데이트합니다.
* 최신 버전의 Linux용 OMS 에이전트는 시간 차이로 인해 등록 실패가 발생하는지 여부를 알려줍니다
* 올바른 작업 영역 ID 및 작업 영역 키를 사용하여 다시 등록하세요. 자세한 내용은 [명령줄 도구를 사용하여 등록](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)을 참조하세요.

### <a name="a-500-error-or-404-error-appears-in-the-log-file-after-onboarding"></a>등록 후 로그 파일에 500 오류 또는 404 오류가 나타남
이 문제는 알려진 문제이며 Linux 데이터를 OMS 작업 영역으로 처음 업로드할 때 발생합니다. 이 문제는 전송되는 데이터 또는 다른 문제에 영향을 미치지 않습니다. 처음 등록할 때에는 이 오류를 무시해도 됩니다.

### <a name="nagios-data-does-not-appear-in-the-oms-portal"></a>OMS 포털에 Nagios 데이터가 나타나지 않음
#### <a name="probable-causes"></a>가능한 원인
* omsagent 사용자는 Nagios 로그 파일에서 읽을 수 있는 권한이 없습니다.
* Nagios 원본 및 필터 섹션이 omsagent.conf 파일에서 아직 주석 처리되어 있습니다.

#### <a name="resolutions"></a>해결 방법
* Nagios 파일에서 읽을 수 있도록 omsagent 사용자를 추가합니다. 자세한 내용은 [Nagios 경고](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)를 참조하세요.
* `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`의 Linux용 OMS 에이전트 일반 구성 파일에서 다음과 같이 Nagios 원본 및 필터 섹션의 주석이 **모두** 제거되었는지 확인합니다.

```
<source>
  type tail
  path /var/log/nagios/nagios.log
  format none
  tag oms.nagios
</source>

<filter oms.nagios>
  type filter_nagios_log
</filter>
```


### <a name="linux-data-doesnt-appear-in-the-oms-portal"></a>OMS 포털에 Linux 데이터가 나타나지 않음
#### <a name="probable-causes"></a>가능한 원인
* OMS 서비스 등록이 실패했습니다.
* OMS 서비스에 대한 연결이 차단되었습니다.
* Linux용 OMS 에이전트가 백업되었습니다.

#### <a name="resolutions"></a>해결 방법
* `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`가 있는지 확인하여 OMS 서비스에 성공적으로 등록되었는지 확인합니다.
* omsadmin.sh 명령줄을 사용하여 다시 등록합니다. 자세한 내용은 [명령줄 도구를 사용하여 등록](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)을 참조하세요.
* 프록시를 사용할 경우 위의 프록시 문제 해결 단계를 사용합니다
* Linux용 OMS 에이전트가 OMS 서비스와 통신할 수 없는 경우 에이전트의 데이터가 최대 버퍼 크기인 50MB로 백업될 수 있습니다. `/opt/microsoft/omsagent/bin/service_control restart` 명령을 실행하여 Linux용 OMS 에이전트를 다시 시작합니다.
  >[AZURE.NOTE] 이 문제는 에이전트 버전 1.1.0-28 및 이상에서 해결되었습니다.

### <a name="syslog-linux-performance-counter-configuration-is-not-applied-in-the-oms-portal"></a>OMS 포털에서 Syslog Linux 성능 카운터 구성이 적용되지 않음
#### <a name="probable-causes"></a>가능한 원인
* Linux용 OMS 에이전트의 구성 에이전트가 OSM 포털에서 최신 구성을 검색하지 않았습니다.
* 포털에서 수정한 설정이 적용되지 않았습니다

#### <a name="resolutions"></a>해결 방법
`omsconfig`는 Linux용 OMS 에이전트에서 OMS 포털 구성 변경 사항을 5분마다 검색하는 구성 에이전트입니다. 그런 다음 이 구성은 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`에 있는 Linux용 OMS 에이전트 구성 파일에 적용됩니다.

* Linux용 OMS 에이전트 구성 에이전트가 포털 구성 서비스와 통신할 수 없어 최신 구성이 적용되지 않는 경우가 있습니다.
* `omsconfig` 에이전트에 다음이 적용되어 설치되었는지 확인합니다.

  * `dpkg --list omsconfig` 또는 `rpm -qi omsconfig`
  * Linux용 OMS 에이전트 최신 버전이 설치되지 않은 경우 다시 설치합니다.
* `omsconfig` 에이전트가 OMS 서비스와 통신할 수 있는지 확인합니다.

  * `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` 명령을 실행합니다.
    * 이 명령은 Syslog 설정, Linux 성능 카운터, 사용자 지정 로그 등 에이전트가 포털에서 검색하는 구성을 반환합니다.
    * 위 명령이 실패할 경우 `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` 명령을 실행합니다. 이 명령에서는 omsconfig 에이전트가 OMS 서비스와 통신하여 최신 구성을 검색합니다.

### <a name="custom-linux-log-data-does-not-appear-in-the-oms-portal"></a>OMS 포털에서 사용자 지정 Linux 로그 데이터가 나타나지 않음
#### <a name="probable-causes"></a>가능한 원인
* OMS 서비스 등록이 실패했습니다
* **다음 구성을 Linux 서버에 적용** 설정을 선택하지 않았습니다
* omsconfig가 포털의 최신 사용자 지정 로그를 적용하지 않았습니다
* `omsagent` 사용 시 권한 문제 또는 `omsagent`가 검색되지 않아 사용자 지정 로그에 액세스할 수 없습니다. 이 경우 다음 출력이 표시됩니다.
  * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
  * `[DATETIME] [error]: file not accessible by omsagent.`
* 이 문제는 경합 상태의 알려진 문제이며 Linux용 OMS Agent 버전 1.1.0-217에서 해결되었습니다

#### <a name="resolutions"></a>해결 방법
* `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` 파일이 있는지 확인하여 성공적으로 등록되었는지 확인합니다.
  * 필요할 경우 omsadmin.sh 명령줄을 사용하여 다시 등록합니다. 자세한 내용은 [명령줄 도구를 사용하여 등록](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)을 참조하세요.
* OMS 포털의 **데이터** 탭의 **설정**에서 **내 Linux 서버에 다음 구성 적용 설정**이 선택되었는지 확인합니다.  
  ![구성 적용](./media/log-analytics-linux-agents/customloglinuxenabled.png)
* `omsconfig` 에이전트가 OMS 서비스와 통신할 수 있는지 확인합니다.

  * `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` 명령을 실행합니다.
  * 이 명령은 Syslog 설정, Linux 성능 카운터, 사용자 지정 로그 등 에이전트가 포털에서 검색하는 구성을 반환합니다.
  * 위 명령이 실패할 경우 `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` 명령을 실행합니다. 이 명령에서는 omsconfig 에이전트가 OMS 서비스와 통신하여 최신 구성을 검색합니다.

Linux용 OMS Agent 사용자는 권한 있는 사용자 `root`로 실행되지 않고 `omsagent` 사용자로 실행됩니다. 대부분의 경우 사용자가 특정 파일을 읽으려면 명시적 권한을 부여해야 합니다.

`omsagent` 사용자에게 권한을 부여하려면 다음 명령을 실행합니다.

1. `omsagent`를 사용하여 특정 그룹에 `sudo usermod -a -G <GROUPNAME> <USERNAME>` 사용자를 추가합니다
2. `sudo chmod -R ugo+rw <FILE DIRECTORY>`를 사용하여 필수 파일에 공용 읽기 액세스 권한을 부여합니다.

이 문제는 경합 상태의 알려진 문제이며 Linux용 OMS Agent 버전 1.1.0-217에서 해결되었습니다. 최신 에이전트로 업데이트한 후 다음 명령을 사용하여 최신 버전의 출력 플러그인을 가져오십시오.

```
sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf
```

## <a name="known-limitations"></a>알려진 제한 사항
Linux 용 OMS 에이전트의 현재 제한 사항에 대해 알아보려면 다음 섹션을 검토합니다.

### <a name="azure-diagnostics"></a>Azure 진단
Azure에서 실행되는 Linux 가상 컴퓨터의 경우, Azure 진단 및 Operations Management Suite의 데이터 수집을 허용하려면 추가적인 단계가 필요합니다. **버전 2.2** 가 필요합니다.

Linux용 진단 확장 설치 및 구성에 대한 자세한 내용은 [Azure CLI 명령을 사용하여 Linux 진단 확장을 사용하도록 설정](../virtual-machines/linux/classic/diagnostic-extension.md#use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension)을 참조하세요.

**Linux 진단 확장을 2.0에서 2.2로 업그레이드 Azure CLI ASM:**

```
azure vm extension set -u <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.0
azure vm extension set <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

**ARM**

```
azure vm extension set -u <resource-group> <vm-name> Microsoft.Insights.VMDiagnosticsSettings Microsoft.OSTCExtensions 2.0
azure vm extension set <resource-group> <vm-name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

이 코드 예제는 PrivateConfig.json이라는 이름의 파일을 참조합니다. 그 파일의 형식은 다음 샘플과 유사합니다.

```
    {
    "storageAccountName":"the storage account to receive data",
    "storageAccountKey":"the key of the account"
    }
```

### <a name="sysklog-is-not-supported"></a>Sysklog가 지원되지 않습니다.
Syslog 메시지를 수집하려면 rsyslog 또는 syslog-ng가 필요합니다. Red Hat Enterprise Linux 버전 5, CentOS 및 Oracle Linux 버전(sysklog)에서는 syslog 이벤트 수집을 위한 기본 syslog 디먼이 지원되지 않습니다. 이 배포의 해당 버전에서 syslog 데이터를 수집하려면 rsyslog 디먼을 설치하고 sysklog를 대체하도록 구성해야 합니다. sysklog를 rsyslog로 대체하는 방법에 대한 자세한 내용은 [Install the newly built rsyslog RPM](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM)(새로 작성한 rsyslog RPM 설치)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [솔루션 갤러리에서 Log Analytics 솔루션을 추가](log-analytics-add-solutions.md) 하여 기능을 추가하고 데이터를 수집합니다.
* [로그 검색](log-analytics-log-searches.md) 을 숙지하여 솔루션에서 수집한 자세한 정보를 확인합니다.
* [대시보드](log-analytics-dashboards.md) 를 사용하여 자신만의 사용자 지정 검색을 저장하고 표시합니다.

