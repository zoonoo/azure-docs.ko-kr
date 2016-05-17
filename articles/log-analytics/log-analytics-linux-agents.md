<properties
	pageTitle="Log Analytics에 Linux 컴퓨터 연결 | Microsoft Azure"
	description="Log Analytics를 사용하여 Linux 컴퓨터에서 생성되는 데이터를 수집하고 그에 따른 조치를 취할 수 있습니다."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="banders"/>

# Log Analytics에 Linux 컴퓨터 연결

Log Analytics를 사용하여 Linux 컴퓨터에서 생성되는 데이터를 수집하고 그에 따른 조치를 취할 수 있습니다. Linux에서 수집된 데이터를 OMS에 추가하면 컴퓨터 위치에 상관없이(사실상 모든 곳에서), Linux 시스템 및 Docker 같은 컨테이너 솔루션을 관리할 수 있습니다. 그러한 데이터 원본은 물리적 서버로서 온-프레미스 데이터 센터, Amazon Web Services(AWS) 또는 Microsoft Azure 같은 클라우드 호스티드 서비스의 가상 컴퓨터, 또는 사용자 책상 위 노트북에도 상주할 수 있습니다. 뿐만 아니라 OMS는 Windows 컴퓨터에서도 유사하게 데이터를 수집하기 때문에 진정한 하이브리드 IT 환경을 지원합니다.

하나의 관리 포털을 통해 OMS의 Log Analytics에서 이 모든 소스의 데이터를 보고 관리할 수 있습니다. 이렇게 하면 많은 다양한 시스템을 사용하여 데이터를 모니터링할 필요가 줄어들고, 데이터를 사용하기가 쉬워지고, 원하는 데이터를 무엇이든 이미 보유하고 있는 비즈니스 분석 솔루션이나 시스템에 내보낼 수 있습니다.

이 문서는 Linux용 OMS 에이전트를 사용하여 Linux 컴퓨터에 대한 데이터를 수집하고 관리하도록 돕는 빠른 시작 가이드입니다. 기술적으로 자세한 내용은 Github에서 [OMS Agent for Linux overview](https://github.com/Microsoft/OMS-Agent-for-Linux)(Linux용 OMS 에이전트 개요) 및 [OMS Agent for Linux full documentation](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)(Linux용 OMS 에이전트 전체 설명서) 참조하세요.


현재는 Linux 컴퓨터에서 다음과 같은 유형의 데이터를 수집할 수 있습니다.

- 성능 메트릭
- Syslog 이벤트
- Nagios 및 Zabbix의 경고
- Docker 컨테이너 성능 메트릭, 인벤토리 및 로그

## 지원되는 Linux 버전

x86 및 x64 버전 모두 다양한 Linux 배포에 대해 공식적으로 지원됩니다. 하지만, Linux용 OMS 에이전트는 나열되지 않은 그 밖의 배포에서 실행이 가능할 수 있습니다.

- Amazon Linux 2012.09~2015.09
- CentOS Linux 5,6,7
- Oracle Linux 5,6,7
- Red Hat Enterprise Linux Server 5,6,7
- Debian GNU/Linux 6, 7, 8
- Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10
- SUSE Linux Enterprise Server 11 및 12

## Linux 용 OMS 에이전트
Linux용 Operations Management Suite 에이전트는 여러 패키지로 구성됩니다. 다음 패키지를 포함하는 릴리스 파일은 `--extract`와 함께 shell 번들을 실행하면 사용할 수 있습니다.

**패키지** | **버전** | **설명**
----------- | ----------- | --------------
omsagent | 1\.1.0 | Linux용 Operations Management Suite 에이전트
omsconfig | 1\.1.1 | OMS 에이전트용 구성 에이전트
omi | 1\.0.8.3 | Open Management Infrastructure(OMI) -- 경량 CIM 서버
scx | 1\.6.2 | 운영 체제 성능 메트릭용 OMI CIM 공급자
apache-cimprov | 1\.0.0 | OMI용 Apache HTTP 서버 성능 모니터링 공급자. Apache HTTP 서버가 감지되는 경우에만 설치됨.
mysql-cimprov | 1\.0.0 | OMI용 MySQL 서버 성능 모니터링 공급자. MySQL/MariaDB 서버가 감지되는 경우에만 설치됨.
docker-cimprov | 0\.1.0 | OMI용 Docker 공급자. Docker가 감지되는 경우에만 설치됨.

### 추가적인 설치 아티팩트
Linux용 OMS 에이전트 패키지를 설치한 후에는 다음과 같은 시스템 수준의 구성 변경이 추가로 적용됩니다. 이러한 아티팩트는 omsagent 패키지가 제거되면 제거됩니다.
- `omsagent`라는 이름의 권한 없는 사용자가 생성됩니다. 이 계정으로 omsagent 디먼이 실행됩니다.
- sudoer의 “include” 파일은 /etc/sudoers.d/omsagent에 생성됩니다 syslog 및 omsagent 디먼을 다시 시작하도록 omsagent에게 권한을 부여합니다. 설치된 sudo 버전에서 sudo “include” 지시문이 지원되지 않으면, 이 항목은 /etc/sudoers에 기록됩니다.
- syslog 구성은 이벤트 하위 집합을 에이전트에 전달하도록 수정됩니다. 자세한 내용은 아래의 **데이터 수집 구성** 섹션을 참조하세요.

### Linux 데이터 수집 세부 정보

다음 테이블은 데이터 수집 방법 및 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 원본 | 직접 에이전트 | SCOM 에이전트 | Azure 저장소 | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
|---|---|---|---|---|---|---|
|Zabbix|![예](./media/log-analytics-linux-agents/oms-bullet-green.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|1분|
|Nagios|![예](./media/log-analytics-linux-agents/oms-bullet-green.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|도착 시|
|syslog|![예](./media/log-analytics-linux-agents/oms-bullet-green.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|Azure 저장소: 10분, 에이전트: 도착 시|
|Linux 성능 카운터|![예](./media/log-analytics-linux-agents/oms-bullet-green.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|예약된 대로, 최소 10초|
|변경 내용 추적|![예](./media/log-analytics-linux-agents/oms-bullet-green.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|![아니요](./media/log-analytics-linux-agents/oms-bullet-red.png)|매시간|



### 패키지 요구 사항
| **필수 패키지** | **설명** | **최소 버전**|
|--------------------- | --------------------- | -------------------|
|Glibc |	GNU C 라이브러리 | 2\.5-12|
|Openssl | OpenSSL 라이브러리 | 0\.9.8e 또는 1.0|
Curl | cURL 웹 클라이언트 | 7\.15.5
|Python-ctypes |함수 라이브러리 | 해당 없음|
|PAM | 플러그형 인증 모듈 |해당 없음 |

>[AZURE.NOTE] Syslog 메시지를 수집하려면 rsyslog 또는 syslog-ng가 필요합니다. Red Hat Enterprise Linux 버전 5, CentOS, 및 Oracle Linux 버전(sysklog)에서는 syslog 이벤트 수집을 위한 기본 syslog 디먼이 지원되지 않습니다. 이 배포의 해당 버전에서 syslog 데이터를 수집하려면 rsyslog 디먼을 설치하고 sysklog를 대체하도록 구성해야 합니다.

## 빠른 설치

다음 명령을 실행하여 omsagent를 다운로드하고, 체크섬의 유효성을 검사한 다음 에이전트를 설치하고 등록합니다. 명령은 64비트용입니다. 작업 영역 ID 및 기본 Key는 OMS 포털에서 **연결된 원본** 탭의 **설정**에 있습니다.

![작업 영역 정보](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.1.0-28/omsagent-1.1.0-28.universal.x64.sh
sha256sum ./omsagent-1.1.0-28.universal.x64.sh
sudo sh ./omsagent-1.1.0-28.universal.x64.sh --upgrade -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

에이전트를 설치하고 업그레이드하는 방법은 매우 다양합니다. 자세한 내용은 [Steps to install the OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux)(Linux용 OMS 에이전트를 설치하는 단계)를 참조하세요.


## Linux 데이터 수집 방법 선택

수집할 데이터 유형을 선택하는 방식은 OMS 포털을 사용할지 또는 자신의 Linux 클라이언트에서 다양한 구성 파일을 직접 편집할지에 따라 달라집니다. 포털을 사용하기로 선택하면, 구성은 사용자의 모든 Linux 클라이언트에 자동으로 보내집니다. 여러 Linux 클라이언트에 다른 구성이 필요하면, 클라이언트 파일을 개별적으로 편집하거나 PowerShell DSC, Chef, 또는 Puppet 같은 대안을 사용해야 합니다.

Linux 컴퓨터의 구성 파일을 사용하여 수집할 성능 카운터와 syslog 이벤트를 지정할 수 있습니다. *에이전트 구성 파일을 편집하여 데이터 수집을 구성하기로 선택하면, 중앙 집중식 구성을 비활성화해야 합니다.* 에이전트 구성 파일에서 데이터 수집을 구성하는 것은 물론 Linux용 OMS 에이전트 또는 개별 컴퓨터에 대해 중앙 집중식 구성을 비활성화하는 지침이 아래에 제공됩니다.

### 개별 Linux 컴퓨터에 대한 OMS 관리 비활성화

구성 데이터에 대한 중앙 집중식 데이터 수집은 OMS\_MetaConfigHelper.py 스크립트를 통해 개별 Linux 컴퓨터에 대해 비활성화됩니다. 이것은 컴퓨터 하위 집합에 특별한 구성이 필요한 경우에 유용합니다.

중앙 집중식 구성을 비활성화하려면:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

중앙 집중식 구성을 다시 활성화하려면:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## Linux 성능 카운터

Linux 성능 카운터는 Windows 성능 카운터와 유사합니다. 둘 다 비슷하게 작동합니다. 다음 절차를 사용하여 추가하고 구성할 수 있습니다. OMS에 추가되면, 30초마다 데이터가 수집됩니다.

### OMS에서 Linux 성능 카운터를 추가하려면:

1. OMS 포털을 사용하여 Linux용 OMS 에이전트를 구성하려면, 설정 페이지에서 Linux 성능 카운터를 추가하고 **데이터**를 클릭합니다. ![데이터](./media/log-analytics-linux-agents/oms-settings-data01.png)
2. **데이터**의 **설정** 페이지에서, **Linux 성능 카운터**를 클릭한 다음 더하기 아이콘 왼쪽에 추가할 카운터의 이름을 입력합니다. ![Linux 성능 카운터](./media/log-analytics-linux-agents/oms-linuxperfcounter01.png)
3. 카운터의 전체 이름을 모르는 경우, 이름의 일부를 입력하기 시작하면 사용 가능한 카운터 목록이 표시됩니다. 추가할 카운터를 발견하면, 목록에서 이름을 클릭한 다음 더하기 아이콘을 클릭하여 카운터를 추가합니다.
4. 카운터를 추가하면, 카운터 목록에 컬러 막대로 강조 표시됩니다.
5. **Apply below configuration to my machines**(내 컴퓨터에 아래 구성 적용) 옵션은 기본적으로 선택되어 있습니다. 구성 데이터 보내기를 비활성화하려면, 이 선택을 해제합니다.
6. 성능 카운터 수정을 완료하면, 페이지 맨 아래에서 **저장**을 클릭하여 변경을 마무리합니다. 그러면 변경한 구성 내용이 OMS에 등록된 모든 Linux용 OMS 에이전트로 보내집니다(일반적으로 5분 이내).

### OMS에서 Linux 성능 카운터 구성

Windows 성능 카운터의 경우, 각 성능 카운터에 대해 특정 인스턴스를 선택할 수 있습니다. 하지만 Linux 성능 카운터의 경우, 어떤 카운터 인스턴스를 선택하든, 부모 카운터의 모든 자식 카운터에 적용됩니다. 다음 테이블은 Linux와 Windows 성능 카운터 모두에서 사용할 수 있는 공통 인스턴스를 보여줍니다.

| **인스턴스 이름** | **의미** |
| --- | --- |
| \_Total | 모든 인스턴스의 총계 |
| * | 모든 인스턴스 |
| (/|/var) | / 또는 /var로 명명된 인스턴트와 일치 |


마찬가지로, 부모 카운터에 대해 선택한 샘플 간격은 모든 자식 카운터에 적용됩니다. 즉, 모든 자식 카운터 샘플 간격 및 인스턴스는 함께 연결되어 있습니다.

### Linux에서 성능 메트릭 추가 및 구성

수집할 성능 메트릭은 /etc/opt/microsoft/omsagent/conf/omsagent.conf에 포함된 구성에 의해 제어됩니다. Linux 용 OMS 에이전트에 사용할 수 있는 클래스 및 메트릭은 [Available performance metrics](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics)(사용 가능한 성능 메트릭)을 참조하세요.

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

- **Object\_name**: 컬렉션에 대한 개체 이름입니다.
- **Instance\_regex**: 수집할 인스턴스를 정의하는 *정규식*입니다. `.*` 값은 모든 인스턴스를 지정합니다. \_Total 인스턴스에 대해서만 프로세서 메트릭을 수집하려면 `_Total`을 지정합니다. crond 또는 sshd 인스턴스에 대해서만 프로세서 메트릭을 수집하려면 `(crond|sshd)`를 지정합니다.
- **Counter\_name\_regex**: 수집할 카운터(개체에 대한)를 정의하는 *정규식*입니다. 개체에 대한 모든 카운터를 수집하려면 `.*`를 지정합니다. 메모리 개체에 대한 스왑 공간 카운터만 수집하려면 `.+Swap.+`을 지정합니다.
- **Interval:**: 개체의 카운터가 수집되는 빈도입니다.

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

### Linux 명령을 사용하여 MySQL 성능 카운터 활성화

omsagent 번들이 설치된 경우 컴퓨터에서 MySQL 서버나 MariaDB 서버가 감지되면, MySQL 서버용 성능 모니터링 공급자가 자동으로 설치됩니다. 이 공급자는 성능 통계를 공개하기 위해 로컬 MySQL/MariaDB 서버에 연결합니다. 공급자가 MySQL 서버에 액세스할 수 있도록 MySQL 사용자 자격 증명을 구성해야 합니다.

localhost에서 MySQL 서버의 기본 사용자 계정을 정의하려면 다음 명령 예제를 사용합니다.

>[AZURE.NOTE] 자격 증명 파일은 omsagent 계정이 읽을 수 있어야 합니다. omsgent로 mycimprovauth 명령을 실행하는 것이 좋습니다.


```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'

sudo service omiserverd restart
```


또는 /var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth 파일을 생성하여, 필요한 MySQL 자격 증명을 파일 내에 지정할 수 있습니다. mysql-auth 파일을 통해 모니터링을 위한 MySQL 자격 증명을 관리하는 방법에 대한 자세한 내용은 [Manage MySQL monitoring credentials in the authentication file](#manage-mysql-monitoring-credentials-in-the-authentication-file)(인증 파일에서 MySQL 모니터링 자격 증명 관리)을 참조하세요.

MySQL 서버 성능 데이터를 수집하기 위해 MySQL 사용자에게 필요한 개체 권한에 대한 자세한 내용은 [Database permissions required for MySQL performance counters](#database-permissions-required-for-mysql-performance-counters)(MySQL 성능 카운터에 필요한 데이터베이스 권한)를 참조하세요.

### Linux 명령을 사용하여 Apache HTTP 서버 성능 카운터 활성화

omsagent 번들이 설치된 경우 컴퓨터에서 Apache HTTP 서버가 감지되면, Apache HTTP 서버용 성능 모니터링 공급자가 자동으로 설치됩니다. 이 공급자는 성능 데이터에 액세스하기 위해 Apache HTTP 서버에 로드되어야 하는 Apache "모듈"에 의존합니다.

모듈은 다음 명령을 사용하여 로드할 수 있습니다.

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Apache 모니터링 모듈을 언로드하려면, 다음 명령을 실행합니다.

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### Log Analytics에서 성능 데이터를 보려면

1. Operations Management Suite 포털에서 로그 검색 타일을 클릭합니다.
2. 모든 성능 카운터를 보려면, 검색 창에 `* (Type=Perf)`를 입력합니다.


OMS는 Windows 성능 카운터 데이터도 수집하기 때문에 Linux에 해당하는 데이터로 검색 범위를 좁혀야 합니다. 다음 예제는 Chorizo21이라는 이름의 예제 Linux 서버에 해당하는 성능 데이터를 보여줍니다.

```
Type=Perf Computer=chorizo*
```

![검색 결과에 표시된 예제 서버](./media/log-analytics-linux-agents/oms-perfsearch01.png)

결과에서 **메트릭**을 클릭하면 데이터가 수집된 카운터를 볼 수 있습니다. 각 카운터에 대한 실시간 데이터가 그래프로 표시됩니다.

![메트릭](./media/log-analytics-linux-agents/oms-perfmetrics01.png)


## Syslog

Syslog는 Windows 이벤트 로그와 유사한 이벤트 기록 프로토콜입니다. OMS에 표시되면 둘 다 유사하게 작동합니다.

### OMS에서 새 Linux syslog 기능을 추가하려면

1. **데이터**의 **설정** 페이지에서, **Syslog**를 클릭한 다음 더하기 아이콘 왼쪽에 추가할 syslog 기능의 이름을 입력합니다. ![Linux syslog](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2.	기능의 전체 이름을 모르는 경우, 이름의 일부를 입력하기 시작하면 사용 가능한 syslog 기능 목록이 표시됩니다. 추가할 syslog 기능을 발견하면, 목록에서 이름을 클릭한 다음 더하기 아이콘을 클릭하여 syslog 기능을 추가합니다.
3.	기능을 추가하면, 컬러 막대로 강조 표시된 목록에 표시됩니다. 다음으로 수집할 심각도(syslog 기능 범주 정보)를 선택합니다.
4.	페이지 맨 아래에서 **저장**을 클릭하여 변경을 마무리합니다. 그러면 변경한 구성 내용이 OMS에 등록된 모든 Linux용 OMS 에이전트로 보내집니다(일반적으로 5분 이내).


### Linux에서 Linux syslog 기능 구성

Syslog 이벤트는 Syslog 디먼(예 rsyslog 또는 syslog-ng)에서 에이전트가 수신 대기 중인 로컬 포트로 보내집니다. 기본 포트는 25224입니다. 에이전트가 설치될 때 기본 syslog 구성이 적용됩니다. 위치는 다음과 같습니다.


Rsyslog: /etc/rsyslog.d/rsyslog-oms.conf

Syslog-ng: /etc/syslog-ng/syslog-ng.conf


기본 OMS 에이전트 syslog 구성은 심각도가 경고 이상인 모든 기능의 syslog 이벤트를 업로드합니다.

>[AZURE.NOTE] Syslog 구성을 편집하는 경우, 변경 내용을 적용하려면 syslog 디먼을 다시 시작해야 합니다.

OMS의 Linux 용 OMS 에이전트에 대한 기본 syslog 구성은 다음과 같습니다.

#### Rsyslog

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

#### Syslog-ng

```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### Log Analytics에서 모든 Syslog 이벤트를 보려면

1. Operations Management Suite 포털에서 **로그 검색** 타일을 클릭합니다.
2. **로그 관리** 그룹화에서 미리 정의된 syslog 검색을 선택한 다음 실행할 항목을 선택합니다.

이 예제는 모든 Syslog 이벤트를 보여줍니다.

![로그 검색에 표시된 Syslog 이벤트](./media/log-analytics-linux-agents/oms-linux-syslog.png)

이제 검색 결과를 세부적으로 들여다 볼 수 있습니다.

## Linux 경고

Nagios 또는 Zabbix를 사용하여 Linux 컴퓨터를 관리하는 경우라면, OMS는 이런 도구에서 생성되는 경고를 수신할 수 있습니다. 하지만, OMS 포털을 사용하여 들어오는 경고 데이터를 구성하는 방법이 현재는 없습니다. 대신, OMS에 경고 보내기를 시작하도록 구성 파일을 편집해야 합니다.



### Nagios의 경고 수집

Nagios 서버의 경고를 수집하려면, 다음 구성을 변경해야 합니다.

1. **omsagent** 사용자에게 Nagios 로그 파일(예: /var/log/nagios/nagios.log/var/log/nagios/nagios.log)에 대한 읽기 권한을 부여합니다. nagios.log 파일이 **nagios** 그룹의 소유라는 가정 하에, **omsagent** 사용자를 **nagios** 그룹에 추가합니다.

    ```
    sudo usermod –a -G nagios omsagent
    ```

2. omsagent.conf 구성 파일 (/etc/opt/microsoft/omsagent/conf/omsagent.conf)을 수정합니다. 다음 항목이 포함되고 주석 처리되지 않도록 합니다.

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
    sudo service omsagent restart
    ```

### Zabbix의 경고 수집

Zabbix 서버의 경고를 수집하려면, 위의 Nagios와 유사한 단계를 수행합니다. 다만 사용자 및 암호를 *일반 텍스트*로 지정해야 합니다. 바람직하지는 않지만 곧 변경될 것입니다. 이 문제에 대처하려면, 사용자를 생성하고 모니터링 권한만 부여하는 것이 좋습니다.

Zabbix용 omsagent.conf 구성 파일(/etc/opt/microsoft/omsagent/conf/omsagent.conf)의 예제 섹션은 다음과 비슷해야 합니다.

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

### Log Analytics 검색에서 경고 보기

OMS에 경고를 보내도록 Linux 컴퓨터를 구성한 후에, 몇 가지 간단한 로그 검색 쿼리를 사용하여 경고를 볼 수 있습니다. 다음 검색 쿼리 예제는 생성되어 기록된 모든 경고를 반환합니다. 예를 들어, IT 인프라 내에서 어떤 문제가 발생하면, 다음 예제 쿼리의 결과는 문제가 발생했을 만한 위치를 나타냅니다. 그러면, 원본 시스템의 경고를 손쉽게 세부적으로 들여다 보고 조사 범위를 좁힐 수 있습니다. 장점은 처음부터 다양한 관리 시스템으로 이동할 필요가 없다는 점입니다. 경고가 OMS로 보내지기만 한다면, OMS에서 작업을 시작할 수 있습니다.

```
Type=Alert
```

#### Log Analytics에서 모든 Nagios 경고를 보려면
1. Operations Management Suite 포털에서 **로그 검색** 타일을 클릭합니다.
2. 쿼리 표시줄에 다음 검색 쿼리를 입력합니다.

    ```
    Type=Alert SourceSystem=Nagios
    ```
![로그 검색에 표시된 Nagios 경고](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

검색 결과를 본 다음, *AlertState* 같은 추가적인 세부 정보를 들여다 볼 수 있습니다.

### Log Analytics에서 모든 Zabbix 경고를 보려면
1. Operations Management Suite 포털에서 **로그 검색** 타일을 클릭합니다.
2. 쿼리 표시줄에 다음 검색 쿼리를 입력합니다.

    ```
    Type=Alert SourceSystem=Zabbix
    ```
![로그 검색에 표시된 Zabbix 경고](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

검색 결과를 본 다음, *AlertName* 같은 추가적인 세부 정보를 들여다 볼 수 있습니다.


## System Center Operations Manager와의 호환성

Linux 용 OMS 에이전트는 System Center Operations Manager 에이전트와 에이전트 바이너리를 공유합니다. Operations Manager로 관리되는 시스템에 Linux용 OMS 에이전트를 설치하면, 컴퓨터의 OMI 및 SCX 패키지가 새 버전으로 업그레이드됩니다. Linux용 OMS 에이전트와 System Center 2012 R2는 호환됩니다. 하지만, **System Center 2012 SP1 이전 버전은 현재 Linux용 OMS 에이전트와 호환되거나 지원되지 않습니다.**

>[AZURE.NOTE] Operations Manager로 관리되지 않는 컴퓨터에 Linux용 OMS 에이전트를 설치한 다음, 나중에 Operations Manager로 컴퓨터를 관리하려는 경우에는, 컴퓨터를 검색하기 전에 OMI 구성을 반드시 수정해야 합니다. **Operations Manager 에이전트가 Linux용 OMS 에이전트보다 먼저 설치되면 이 단계가 필요하지 않습니다.**

### Linux용 OMS 에이전트가 Operations Manager와 통신하도록 설정하려면

1. /etc/opt/omi/conf/omiserver.conf 파일을 편집합니다.
2. **httpsport=**로 시작되는 줄이 1270 포트를 지정하도록 합니다. 예: `httpsport=1270`
3. OMI 서버를 다시 시작합니다.

    ```
    service omiserver restart or systemctl restart omiserver
    ```




## MySQL 성능 카운터에 필요한 데이터베이스 권한

MySQL 모니터링 사용자에게 권한을 부여하려면, 권한을 부여하는 사용자에게 'GRANT option' 권한은 물론 부여되는 권한에 대한 권한이 있어야 합니다.

MySQL 사용자가 성능 데이터를 반환하려면 해당 사용자는 다음 쿼리에 대한 액세스가 있어야 합니다.

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

이러한 쿼리 외에, MySQL 사용자는 다음과 같은 기본 테이블에 대해 SELECT 액세스가 필요합니다.

- information\_schema
- mysql

이러한 권한은 다음과 같은 권한 부여 명령을 실행하여 부여될 수 있습니다.

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## 인증 파일에서 MySQL 모니터링 자격 증명 관리

다음 섹션은 MySQL 자격 증명을 관리하는 데 도움이 됩니다.

### MySQL OMI 공급자 구성

MySQL OMI 공급자가 MySQL 인스턴스의 성능/상태 정보를 쿼리하려면, 미리 정의된 MySQL 사용자와 설치되어 있는 MySQL 클라이언트 라이브러리가 필요합니다.

### MySQL OMI 인증 파일

MySQL OMI 공급자는 인증 파일을 사용하여 MySQL 인스턴스가 수신 대기할 바인딩 주소와 포트를 결정하고 메트릭 수집에 사용할 자격 증명을 결정합니다. 설치하는 동안 MySQL OMI 공급자는 MySQL my.cnf 구성 파일(기본 위치)에서 바인딩 주소와 포트를 검색하고, MySQL OMI 인증 파일을 부분적으로 설정합니다.

MySQL 서버 인스턴스에 대한 모니터링을 완료하려면, 미리 생성된 MySQL OMI 인증 파일을 올바른 디렉터리에 추가합니다.

### 인증 파일 형식

MySQL OMI 인증 파일은 다음 항목에 대한 정보를 포함하는 텍스트 파일입니다.

- 포트
- 바인딩 주소
- MySQL 사용자 이름
- Base64로 인코딩된 암호

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
| 포트 | 포트는 MySQL 인스턴스가 수신 대기 중인 현재 포트를 나타냅니다. 포트 0은 뒤에 나오는 속성이 기본 인스턴스에 사용된다는 것을 의미합니다. |
| 바인딩 주소 | 바인딩 주소는 현재 MySQL 바인딩 주소입니다. |
| username | MySQL 서버 인스턴스를 모니터링하는 데 사용할 MySQL 사용자의 사용자 이름입니다. |
| Base64로 인코딩된 암호 | Base64로 인코딩된 MySQL 모니터링 사용자의 암호입니다. |
| AutoUpdate | MySQL OMI 공급자가 업그레이드되면 공급자는 my.cnf 파일에서 변경 내용을 검색하고 MySQL OMI 인증 파일을 덮어씁니다. MySQL OMI 인증 파일에 필요한 업데이트에 따라 이 플래그를 true 또는 false로 설정합니다. |

#### 인증 파일 위치

MySQL OMI 인증 파일은 다음 경로에 위치해야 하며 이름을 "mysql-auth"라고 지정해야 합니다.

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

파일(및 auth/omsagent 디렉터리)는 omsagent 사용자가 소유해야 합니다.

## 에이전트 로그

Linux 용 OMS 에이전트 로그의 위치:

/var/opt/microsoft/omsagent/log/

omsconfig(에이전트 구성) 프로그램에 대한 Linux 용 OMS 에이전트 로그의 위치:

/var/opt/microsoft/omsconfig/log/

OMI 및 SCX 구성 요소(성능 메트릭 데이터를 제공하는) 로그의 위치:

/var/opt/omi/log/ and /var/opt/microsoft/scx/log

## 알려진 제한 사항
Linux 용 OMS 에이전트의 현재 제한 사항에 대해 알아보려면 다음 섹션을 검토합니다.

### Azure 진단

Azure에서 실행되는 Linux 가상 컴퓨터의 경우, Azure 진단 및 Operations Management Suite의 데이터 수집을 허용하려면 추가적인 단계가 필요합니다. Linux 용 OMS 에이전트와의 호환을 위해 Linux용 진단 확장 **버전 2.2**가 필요합니다.

Linux용 진단 확장 설치 및 구성에 대한 자세한 내용은 [Azure CLI 명령을 사용하여 Linux 진단 확장을 사용하도록 설정](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension/#use-the-azure-cli-command-to-enable-linux-diagnostic-extension)을 참조하세요.

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

### Sysklog가 지원되지 않습니다.

Syslog 메시지를 수집하려면 rsyslog 또는 syslog-ng가 필요합니다. Red Hat Enterprise Linux 버전 5, CentOS, 및 Oracle Linux 버전(sysklog)에서는 syslog 이벤트 수집을 위한 기본 syslog 디먼이 지원되지 않습니다. 이 배포의 해당 버전에서 syslog 데이터를 수집하려면 rsyslog 디먼을 설치하고 sysklog를 대체하도록 구성해야 합니다. sysklog를 rsyslog로 대체하는 방법에 대한 자세한 내용은 [Install the newly built rsyslog RPM](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM)(새로 작성한 rsyslog RPM 설치)을 참조하세요.

## 다음 단계

- [솔루션 갤러리에서 Log Analytics 솔루션을 추가](log-analytics-add-solutions.md)하여 기능을 추가하고 데이터를 수집합니다.
- [로그 검색](log-analytics-log-searches.md)을 숙지하여 솔루션에서 수집한 자세한 정보를 확인합니다.
- [대시보드](log-analytics-dashboards.md)를 사용하여 자신만의 사용자 지정 검색을 저장하고 표시합니다.

<!---HONumber=AcomDC_0504_2016-->