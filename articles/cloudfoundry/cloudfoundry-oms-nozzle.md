---
title: "Cloud Foundry 모니터링용 Azure Log Analytics Nozzle 배포 | Microsoft Docs"
description: "Azure Log Analytics용 Cloud Foundry Loggregator Nozzle을 배포하고, Azure Log Analytics 및 OMS 콘솔을 구성하고, 이러한 도구를 사용하여 Cloud Foundry 시스템 상태 및 성능 메트릭을 모니터링하는 단계별 지침을 제공합니다."
services: virtual-machines-linux
documentationcenter: 
author: ningk
manager: timlt
editor: 
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f704a2638a4b6b57c014d502dd87303a55334672
ms.contentlocale: ko-kr
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>Cloud Foundry 시스템 모니터링용 Azure Log Analytics Nozzle 배포

## <a name="background"></a>백그라운드

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/)는 Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/)(OMS)의 서비스입니다. Log Analytics를 사용하면 클라우드 및 온-프레미스 환경에서 생성되는 데이터를 수집하고 분석할 수 있습니다.

Microsoft Azure Log Analytics Nozzle(Nozzle)은 [Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) Firehose에서 Azure Log Analytics로 메트릭을 전달하는 Cloud Foundry 구성 요소입니다. Nozzle을 사용하면 여러 배포에서 Cloud Foundry 시스템 상태와 성능 메트릭을 수집, 확인 및 분석할 수 있습니다.

이 문서에서는 Cloud Foundry 환경에 Nozzle을 배포한 다음 Azure Log Analytics OMS 콘솔에서 데이터에 액세스하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

### <a name="1-deploy-a-cf-or-pcf-environment-in-azure"></a>1. Azure에서 CF 또는 PCF 환경 배포

오픈 소스 CF(Cloud Foundry) 배포나 PCF(Pivotal Cloud Foundry) 배포에서 Nozzle을 사용할 수 있습니다.

* [Azure에서 Cloud Foundry 배포](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [Azure에서 Pivotal Cloud Foundry 배포](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2. Nozzle 배포를 위해 CF 명령줄 도구 설치

Nozzle은 CF 환경에서 응용 프로그램으로 실행됩니다. 이 응용 프로그램을 배포하려면 CF CLI가 필요합니다. 또한 Nozzle에는 Loggregator Firehose 및 Cloud Controller 액세스 권한도 필요합니다. 이러한 액세스 권한이 있는 사용자를 만들고 구성하려면 UAA 명령줄 클라이언트가 필요합니다.

* [Cloud Foundry CLI 설치](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [Cloud Foundry UAA 명령줄 클라이언트 설치](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

UAA 명령줄 클라이언트를 설치하기 전에 Rubygems가 설치되어 있는지 확인하세요.

### <a name="3-create-an-oms-workspace-in-azure"></a>3. Azure에서 OMS 작업 영역 만들기

#### <a name="create-the-oms-workspace-manually"></a>수동으로 OMS 작업 영역 만들기

OMS 작업 영역을 수동으로 만든 다음 Nozzle 배포를 완료한 후에 미리 구성된 OMS 보기와 경고를 로드할 수 있습니다.

1. Azure Portal의 Marketplace 서비스 목록에서 Log Analytics를 검색하여 Log Analytics를 선택합니다.
2. 만들기를 클릭하고 다음 항목에 대한 옵션을 선택합니다.

* OMS 작업 영역: 작업 영역의 이름을 입력합니다.
* 구독: 구독이 여러 개이면 CF 배포가 포함된 구독과 동일한 구독을 선택합니다.
* 리소스 그룹: 새 리소스 그룹을 만들 수도 있고 CF 배포가 포함된 그룹과 같은 그룹을 사용할 수도 있습니다.
* 위치
* 가격 책정 계층: 확인을 클릭하여 완료합니다.
> 자세한 내용은 [Log Analytics 시작](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)을 참조하세요.

#### <a name="create-the-oms-workspace-through-the-oms-template"></a>OMS 템플릿을 통해 OMS 작업 영역 만들기

[Cloud Foundry용 Azure OMS Log Analytics 솔루션](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution)에서 OMS 작업 영역을 만들고 미리 구성된 OMS 보기와 경고를 로드할 수 있습니다.

## <a name="deploy-the-nozzle"></a>Nozzle 배포

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>PCF Ops Manager 타일로 Nozzle 배포

Ops Manager를 통해 PCF를 배포한 경우 [PCF용 Nozzle 설치 및 구성](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html)의 단계를 진행하면 Nozzle이 Ops Manager가 포함된 타일로 설치됩니다.

### <a name="deploy-the-nozzle-as-an-application-to-cloud-foundry"></a>Cloud Foundry에 응용 프로그램으로 Nozzle 배포

PCF Ops Manager를 사용하지 않는 경우 Nozzle을 응용 프로그램으로 푸시해야 합니다.

#### <a name="log-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>CF CLI를 통해 관리자로 CF 배포 로그인

Dev box에서 다음 명령을 실행합니다.
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

> "SYSTEM_DOMAIN"은 CF 도메인 이름입니다. CF 배포 매니페스트 파일에서 "SYSTEM_DOMAIN"을 검색하면 이 이름을 검색할 수 있습니다. 
> "CF_User"는 CF 관리자 이름입니다. CF 배포 매니페스트 파일에서 "scim" 섹션을 검색하고 "cf_admin_password"의 이름을 찾으면 이름과 암호를 검색할 수 있습니다.

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>CF 사용자를 만들고 필요한 권한 부여

Dev box에서 다음 명령을 실행합니다.
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

> "SYSTEM_DOMAIN"은 CF 도메인 이름입니다. CF 배포 매니페스트 파일에서 "SYSTEM_DOMAIN"을 검색하면 이 이름을 검색할 수 있습니다.

#### <a name="download-the-latest-azure-log-analytics-nozzle-release"></a>최신 Azure Log Analytics Nozzle 릴리스 다운로드

Dev box에서 다음 명령을 실행합니다.
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables-in-manifestyml-in-your-current-directory"></a>현재 디렉터리에서 "manifest.yml"에 환경 변수 설정

이 파일은 Nozzle의 앱 매니페스트입니다. 포함된 값은 구체적인 OMS 작업 영역 정보로 바꿔야 합니다.

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics, default is 10s.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics, default is 10s.
OMS_MAX_MSG_NUM_PER_BATCH : The max number of messages in a batch to OMS Log Analytics, default is 1000.
API_ADDR                  : The api URL of the CF environment, refer to "Push the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
DOPPLER_ADDR              : Loggregator's traffic controller URL, refer to "Deploy the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
FIREHOSE_USER             : CF user you created in "Push the Nozzle as an App to Cloud Foundry" section, who has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma separated list, valid event types are METRIC,LOG,HTTP
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the Trafficcontroller
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments
IDLE_TIMEOUT              : Keep Alive duration for the firehose consumer, default is 60s.
LOG_LEVEL                 : Logging level of the nozzle, valid levels: DEBUG, INFO, ERROR
LOG_EVENT_COUNT           : If true, the total count of events that the nozzle has received and sent will be logged to OMS Log Analytics as CounterEvents
LOG_EVENT_COUNT_INTERVAL  : The time interval of logging event count to OMS Log Analytics, default is 60s.
```

### <a name="push-the-application-from-your-dev-box"></a>Dev box에서 응용 프로그램 푸시

현재 위치가 "oms-log-analytics-firehose-nozzle" 폴더인지 확인한 후에 다음 명령을 실행합니다.
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>Nozzle 설치 유효성 검사

### <a name="from-apps-manager-for-pcf"></a>Apps Manager에서(PCF의 경우)

1. Ops Manager에 로그인하여 설치 대시보드에 타일이 표시되는지 확인합니다.
2. Apps Manager에 로그인하여 Nozzle용으로 만든 영역이 사용 현황 보고서에 나열되며 상태가 정상인지 확인합니다.

### <a name="from-dev-box"></a>Dev box에서

Dev box CF CLI 창에서 다음 명령을 입력합니다.
```
cf apps
```
OMS Nozzle 응용 프로그램이 실행되고 있는지 확인합니다.

## <a name="view-the-data-in-oms-portal"></a>OMS 포털에서 데이터 보기

### <a name="1-import-oms-view"></a>1. OMS 뷰 가져오기

OMS 포털에서 **뷰 디자이너** -> **가져오기** -> **찾아보기**로 이동하여 *Cloud Foundry.omsview*와 같은 omsview 파일 중 하나를 선택한 후 뷰를 저장합니다. 이제 **타일**이 주 OMS 개요 페이지에 표시됩니다. **타일**을 클릭하면 시각화된 메트릭이 표시됩니다.

운영자는 **뷰 디자이너**를 통해 새 뷰를 만들거나 이러한 뷰를 사용자 지정할 수 있습니다.

*"Cloud Foundry.omsview"*는 Cloud Foundry OMS 뷰 템플릿의 미리 보기 버전이며 완전히 구성된 기본 템플릿은 현재 제작되는 중입니다. 제안 사항과 의견은 [문제 섹션](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)으로 보내 주시기 바랍니다.

### <a name="2-create-alert-rules"></a>2. 경고 규칙 만들기

운영자는 [경고를 작성](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)하고 필요에 따라 쿼리와 임계값을 사용자 지정할 수 있습니다. 권장 경고 집합은 다음과 같습니다.

| 검색 쿼리                                                                  | 경고 생성 조건 | 설명                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | 결과 수 < 1   | **bbs.Domain.cf-apps**는 cf-apps 도메인이 최신 상태인지, 즉 Cloud Controller로부터의 CF App 요청이 실행을 위해 bbs.LRPsDesired(Diego에 적합한 AI)로 동기화되는지 여부를 나타냅니다. 수신되는 데이터가 없으면 지정한 기간에 cf-apps 도메인이 최신 상태가 아닌 것입니다. |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | 결과 수 > 0   | Diego 셀의 경우 값이 0이면 정상 상태이고 1이면 비정상 상태입니다. 지정한 기간에 **비정상 Diego 셀**이 여러 개 검색되면 경고를 설정합니다. |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | 결과 수 > 0 | 값이 1이면 시스템이 정상 상태인 것이고 0이면 시스템이 정상 상태가 아닌 것입니다. |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | 결과 수 > 0   | Consul은 상태를 주기적으로 내보냅니다. 값이 0이면 시스템이 정상 상태인 것이고 1이면 경로 내보내기에서 **Consul이 다운**되었음을 검색한 것입니다. |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | 결과 수 > 0 | 백 프레셔로 인해 Doppler가 의도적으로 **삭제**한 메시지의 델타 번호입니다. |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | 결과 수 > 0   | Loggregator는 로깅 프로세스의 문제(예: 로그 메시지 출력이 너무 높음)를 나타내기 위해 **LGR**을 내보냅니다. |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | 결과 수 > 0   | Nozzle은 Loggregator에서 slow consumer 경고를 수신하면 OMS에 **slowConsumerAlert** ValueMetric을 보냅니다. |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | 결과 수 > 0   | **손실된 이벤트**의 델타 번호가 임계값에 도달하는 경우 Nozzle 실행과 관련하여 문제가 있는 것일 수 있습니다. |

## <a name="scale"></a>확장

### <a name="scale-the-nozzle"></a>Nozzle 확장

운영자는 첫 배포 시 Nozzle 인스턴스를 두 개 이상 포함하는 것이 좋습니다. Firehose는 모든 Nozzle 인스턴스에 작업을 배포합니다.
Nozzle이 Firehose의 데이터 트래픽을 처리할 수 있도록 하려면 운영자는 "경고 규칙 만들기" 섹션에 나와 있는 **slowConsumerAlert** 경고를 설정해야 합니다. 그리고 경고가 수신되면 [Nozzle 속도 저하 관련 지침](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz)에 따라 확장이 필요한지를 결정합니다.
Nozzle을 강화하려면 Apps Manager 또는 CF CLI를 사용하여 Nozzle용 인스턴스 수 또는 메모리/디스크 리소스를 늘립니다.

### <a name="scale-the-loggregator"></a>Loggregator 확장

Loggregator는 로깅 프로세스의 문제를 나타내기 위해 **LGR**에 로그 메시지를 보냅니다. 운영자는 경고를 모니터링하여 Loggregator를 강화해야 하는지 여부를 결정할 수 있습니다.
Loggregator를 강화하려는 경우 운영자는 Doppler 버퍼 크기를 늘리거나 CF 매니페스트에서 Doppler 서버 인스턴스를 더 추가할 수 있습니다. 자세한 내용은 [Loggregator 확장 지침](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling)을 참조하세요.

## <a name="update"></a>업데이트

Nozzle을 최신 버전으로 업데이트하려면 새 Nozzle 릴리스를 다운로드한 다음 "배포" 섹션의 단계에 따라 응용 프로그램을 다시 푸시합니다.

Nozzle을 제거하려면 다음 단계를 수행합니다.

### <a name="from-the-ops-manager"></a>Ops Manager에서

1. Ops Manager에 로그인합니다.
2. "PCF용 Microsoft Azure Log Analytics Nozzle" 타일을 찾습니다.
3. 휴지통 아이콘을 클릭하고 삭제 작업을 확인합니다.

### <a name="from-the-dev-box"></a>Dev box에서

CF CLI 창에서 다음 명령을 입력합니다.
```
cf delete <App Name> -r
```

Nozzle을 제거해도 OMS 포털의 데이터는 자동으로 제거되지 않으며 OMS Log Analytics 보존 설정에 따라 만료됩니다.

## <a name="support-and-feedback"></a>지원 및 피드백

Azure Log Analytics Nozzle은 오픈 소스 응용 프로그램입니다. 문의 사항과 의견은 [github 섹션](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)으로 보내 주시기 바랍니다. Azure 지원 요청을 시작하려면 서비스 범주로 "Cloud Foundry를 실행하는 가상 컴퓨터"를 사용하세요. 

## <a name="next-step"></a>다음 단계

Nozzle에서 제공하는 Cloud Foundry 메트릭을 확인할 수 있을 뿐 아니라 OMS 에이전트를 사용하여 VM 수준 작동 데이터(Syslog, 성능, 경고, 인벤토리)에 대한 정보도 파악할 수 있습니다. OMS 에이전트는 Bosh 추가 기능으로 CF VM에 설치됩니다.
> 자세한 내용은 [Cloud Foundry 배포에 OMS 에이전트 배포](https://github.com/Azure/oms-agent-for-linux-boshrelease)를 참조하세요.
