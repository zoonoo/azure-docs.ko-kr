---
title: "Azure Site Recovery를 사용하여 다중 계층 IIS 기반 웹 응용 프로그램 복제 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 IIS 웹 팜 가상 컴퓨터를 복제하는 방법에 대해 설명합니다."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: b23624fc7e82af1cb593a1aedd138ae0d6637ae7
ms.lasthandoff: 03/29/2017


---
# <a name="replicate-a-multi-tier-iis-based-web-application-using-azure-site-recovery"></a>Azure Site Recovery를 사용하여 다중 계층 IIS 기반 웹 응용 프로그램 복제

## <a name="overview"></a>개요


응용 프로그램 소프트웨어는 조직에서 비즈니스 생산성의 엔진입니다. 조직에서 다양한 웹 응용 프로그램이 서로 다른 용도로 사용될 수 있습니다. 급여 처리, 금융 응용 프로그램 및 고객 연결 웹 사이트와 같은 응용 프로그램 중 일부는 조직에서 가장 중요할 수 있습니다. 조직에서는 생산성의 손실, 더 나아가 조직의 브랜드 이미지 손상을 방지하기 위해 조직을 항상 운영하는 것이 중요합니다.

중요한 웹 응용 프로그램은 일반적으로 웹, 데이터베이스 및 응용 프로그램이 서로 다른 계층에 있는 다중 계층 응용 프로그램으로 설정됩니다. 여러 계층으로 분산하는 것 외에도 각 계층에 여러 서버를 사용하여 트래픽 부하를 분산할 수도 있습니다. 또한 다양한 계층 간 그리고 웹 서버 상의 매핑은 고정 IP 주소를 기반으로 할 수 있습니다. 장애 조치에서, 특히 웹 서버에 구성된 웹 사이트가 여러 개 있는 경우 이러한 매핑 중 일부를 업데이트해야 합니다. SSL을 사용하는 웹 응용 프로그램의 경우 인증서 바인딩을 업데이트해야 합니다.

기존의 비복제 기반 복구 방법에는 다양한 구성 파일, 레지스트리 설정, 바인딩, 사용자 지정 구성 요소(COM 또는 .NET), 콘텐츠 및 인증서를 백업하고, 일단의 수동 단계를 통해 파일을 복구하는 작업이 필연적으로 수반됩니다. 이러한 기술은 분명히 번거롭고, 오류가 발생하기 쉽고, 확장할 수 없습니다. 예를 들어 인증서를 백업하는 것을 쉽게 잊어버릴 수 있으며, 장애 조치 후에 서버에 대한 새 인증서를 구입할 수 밖에 없습니다.

훌륭한 재해 복구 솔루션에서는 위의 복잡한 응용 프로그램 아키텍처에 기반하여 복구 계획을 모델링할 수 있어야 하며, 다양한 계층 간의 응용 프로그램 매핑을 처리하기 위한 사용자 지정 단계를 추가할 수 있어야 합니다. 이에 따라 재해 발생시 한 번의 클릭으로 RTO(복구 시간 목표)를 낮추는 확실한 솔루션을 제공할 수 있습니다.


이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 IIS 기반 웹 응용 프로그램을 보호하는 방법에 대해 설명합니다. 그리고 3계층 IIS 기반 웹 응용 프로그램을 Azure로 복제하는 방법, 재해 복구 연습을 수행하는 방법 및 응용 프로그램을 Azure로 장애 조치하는 방법에 대한 모범 사례를 설명합니다. 

 
## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 항목을 이해해야 합니다.

1. [Azure에 가상 컴퓨터 복제](site-recovery-vmware-to-azure.md)
1. 방법: [복구 네트워크 디자인](site-recovery-network-design.md)
1. [Azure로 테스트 장애 조치 수행](./site-recovery-test-failover-to-azure.md)
1. [Azure로 장애 조치 수행](site-recovery-failover.md)
1. 방법: [도메인 컨트롤러 복제](site-recovery-active-directory.md)
1. 방법: [SQL Server 복제](site-recovery-sql.md)

## <a name="deployment-patterns"></a>배포 패턴
IIS 기반 웹 응용 프로그램은 일반적으로 다음 배포 패턴 중 하나를 따릅니다.

**배포 패턴 1** ARR(응용 프로그램 요청 라우팅), IIS 서버 및 Microsoft SQL Server를 사용하는 IIS 기반 웹 팜 

![배포 패턴](./media/site-recovery-iis/deployment-pattern1.png)

**배포 패턴 2** ARR(응용 프로그램 요청 라우팅), IIS 서버, 응용 프로그램 서버 및 Microsoft SQL Server를 사용하는 IIS 기반 웹 팜 


![배포 패턴](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery 지원

이 문서를 작성하기 위해 Windows Server 2012 R2 Enterprise에 IIS 서버 버전 7.5가 있는 VMware 가상 컴퓨터가 사용되었습니다. 사이트 복구 복제는 응용 프로그램을 제한하지 않으므로 여기서 제시하는 권장 사항은 다음 시나리오뿐만 아니라 다른 버전의 IIS에서도 유지됩니다.

### <a name="source-and-target"></a>원본 및 대상

**시나리오** | **보조 사이트로** | **Azure로**
--- | --- | ---
**Hyper-V** | 예 | 예
**VMware** | 예 | 예
**물리적 서버** | 아니요 | 예

## <a name="replicate-virtual-machines"></a>가상 컴퓨터 복제

[이 지침](site-recovery-vmware-to-azure.md)에 따라 모든 IIS 웹 팜 가상 컴퓨터를 Azure로 복제합니다. 

고정 IP를 사용하는 경우 계산 및 네트워크 설정의 [**대상 IP**](./site-recovery-replicate-vmware-to-azure.md#view-and-manage-vm-properties) 설정에 가상 컴퓨터에서 사용할 IP를 지정합니다. 

![대상 IP](./media/site-recovery-active-directory/dns-target-ip.png)


## <a name="creating-a-recovery-plan"></a>복구 계획 만들기

복구 계획을 사용하면 다중 계층 응용 프로그램에서 다양한 계층의 장애 조치를 시퀀싱하여 응용 프로그램의 일관성을 유지할 수 있습니다. 다중 계층 웹 응용 프로그램에 대한 복구 계획을 만드는 동안 아래 단계를 수행합니다.  [복구 계획 만들기에 대한 자세한 정보](./site-recovery-create-recovery-plans.md)

### <a name="adding-virtual-machines-to-failover-groups"></a>장애 조치 그룹에 가상 컴퓨터 추가
일반적인 다중 계층 IIS 웹 응용 프로그램은 SQL 가상 컴퓨터가 있는 데이터베이스 계층 및 IIS 서버와 응용 프로그램 계층으로 구성된 웹 계층으로 구성됩니다. 아래와 같이 계층을 기반으로 하여 서로 다른 그룹에 이러한 모든 가상 컴퓨터를 추가합니다. [복구 계획 사용자 지정에 대한 자세한 정보](site-recovery-runbook-automation.md#customize-the-recovery-plan)

1. 복구 계획을 만듭니다. 맨 마지막으로 종료하고 맨 먼저 작동하도록 그룹 1에 데이터베이스 계층 가상 컴퓨터를 추가합니다. 

1. 데이터베이스 계층을 작동한 후에 작동하도록 그룹 2에 응용 프로그램 계층 가상 컴퓨터를 추가합니다.

1. 응용 프로그램 계층을 작동한 후에 작동하도록 그룹 3에 웹 계층 가상 컴퓨터를 추가합니다.

1. 웹 계층을 작동한 후에 작동하도록 그룹 4에 부하 분산 가상 컴퓨터를 추가합니다.


### <a name="adding-scripts-to-the-recovery-plan"></a>복구 계획에 스크립트 추가
장애 조치/테스트 장애 조치 후에 IIS 웹 팜 기능이 올바르게 작동하도록 Azure 가상 컴퓨터에서 일부 작업을 수행해야 할 수 있습니다. 아래와 같이 복구 계획에 해당 스크립트를 추가하여 DNS 항목 업데이트, 사이트 바인딩 변경, 연결 문자열 변경 등과 같은 사후 장애 조치 작업을 자동화할 수 있습니다. [복구 계획에 스크립트 추가에 대한 자세한 정보](./site-recovery-create-recovery-plans.md#add-scripts)

#### <a name="dns-update"></a>DNS 업데이트
DNS가 동적 DNS 업데이트를 위해 구성된 경우 일반적으로 가상 컴퓨터를 시작할 때 새 IP로 DNS를 업데이트합니다. 가상 컴퓨터의 새 IP로 DNS를 업데이트하는 명시적인 단계를 추가하려면 복구 계획 그룹의 사후 작업으로 [DNS의 IP를 업데이트하는 스크립트](https://aka.ms/asr-dns-update)를 추가합니다.  

#### <a name="connection-string-in-an-applications-webconfig"></a>응용 프로그램의 web.config에 있는 연결 문자열
연결 문자열은 웹 사이트에서 통신하는 데이터베이스를 지정합니다. 

연결 문자열에 데이터베이스 가상 컴퓨터의 이름이 있으면 장애 조치 후의 추가 단계가 필요하지 않고 응용 프로그램에서 DB와 자동으로 통신할 수 있습니다. 또한 데이터베이스 가상 컴퓨터의 IP 주소가 유지되면 연결 문자열을 업데이트할 필요가 없습니다. 연결 문자열에서 IP 주소를 사용하는 데이터베이스 가상 컴퓨터를 참조하는 경우 장애 조치 후 해당 IP를 업데이트해야 합니다. 예: 아래 연결 문자열은 127.0.1.2 IP를 사용하는 DB를 가리킵니다. 

        <?xml version="1.0" encoding="utf-8"?> 
        <configuration> 
        <connectionStrings> 
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" /> 
        </connectionStrings> 
        </configuration>

복구 계획에서 그룹 3 뒤에 [IIS 연결 업데이트 스크립트](https://aka.ms/asr-update-webtier-script-classic)를 추가하여 웹 계층의 연결 문자열을 업데이트할 수 있습니다.

#### <a name="site-bindings-for-the-application"></a>응용 프로그램에 대한 사이트 바인딩
모든 사이트는 바인딩 형식, IIS 서버에서 사이트 요청을 수신 대기하는 IP 주소, 포트 번호 및 사이트의 호스트 이름을 포함하는 바인딩 정보로 구성됩니다. 장애 조치 시 연결되는 IP 주소가 변경된 경우 이러한 바인딩을 업데이트해야 할 수 있습니다. 

> [!NOTE]
> 
> 아래 예제와 같이 사이트 바인딩에 대해 '모두 할당되지 않음'으로 표시했으면 장애 조치 후에 이 바인딩을 업데이트할 필요가 없습니다. 또한 사이트와 연결된 IP 주소가 장애 조치 후에 변경되지 않으면 사이트 바인딩을 업데이트할 필요가 없습니다. (IP 주소의 보존은 기본사이트와 복구 사이트에 할당된 네트워크 아키텍처 및 서브넷에 따라 달라지므로 조직에 적합하거나 적합하지 않을 수 있습니다.) 

![SSL 바인딩](./media/site-recovery-iis/sslbinding.png)

IP 주소를 사이트와 연결한 경우 모든 사이트 바인딩을 새 IP 주소로 업데이트해야 합니다. 복구 계획에서 그룹 3 뒤에 [IIS 웹 계층 업데이트 스크립트](https://aka.ms/asr-web-tier-update-runbook-classic)를 추가하여 사이트 바인딩을 변경할 수 있습니다. 


#### <a name="update-load-balancer-ip-address"></a>부하 분산 장치 IP 주소 업데이트
ARR(응용 프로그램 요청 라우팅) 가상 컴퓨터가 있는 경우 그룹 4 뒤에 [IIS ARR 장애 조치 스크립트](https://aka.ms/asr-iis-arrtier-failover-script-classic)를 추가하여 IP 주소를 업데이트합니다.

#### <a name="the-ssl-cert-binding-for-an-https-connection"></a>https 연결을 위한 SSL 인증서 바인딩
웹 사이트에는 웹 서버와 사용자의 브라우저 사이의 보안 통신을 보장하는 데 도움이 되는 연결된 SSL 인증서가 있을 수 있습니다. 웹 사이트에 SSL 인증서 바인딩과 함께 IIS 서버의 IP 주소에 대한 https 연결 및 연결된 https 사이트 바인딩이 있는 경우 장애 조치 후에 IIS 가상 컴퓨터의 IP를 사용하여 인증서에 대한 새 사이트 바인딩을 추가해야 합니다. 

SSL 인증서는 다음 항목에 대해 발급될 수 있습니다. 

a) 웹 사이트의 정규화된 도메인 이름<br>
b) 서버의 이름<br>
c) 도메인 이름에 대한 와일드카드 인증서<br>
d) IP 주소 - SSL 서버 인증서가 IIS 서버의 IP에 대해 발급되면, Azure 사이트에 있는 IIS 서버의 IP 주소에 대해 다른 SSL 인증서를 발급해야 하며 이 인증서에 대한 추가 SSL 바인딩을 만들어야 합니다. 따라서 IP에 대해 발급된 SSL 인증서는 사용하지 않는 것이 좋습니다. 이는 널리 사용되지 않는 옵션이며, CA/브라우저 포럼의 새로운 변경 내용에 따라 곧 사용되지 않을 예정입니다.

#### <a name="update-the-dependency-between-the-web-and-the-application-tier"></a>웹과 응용 프로그램 계층 간 종속성 업데이트
가상 컴퓨터의 IP 주소를 기반으로 하는 응용 프로그램 특정 종속성이 있는 경우 장애 조치 후에 이 종속성을 업데이트해야 합니다.

## <a name="doing-a-test-failover"></a>테스트 장애 조치 수행
[이 지침](site-recovery-test-failover-to-azure.md)에 따라 테스트 장애 조치를 수행합니다.

1.    Azure Portal로 이동하여 복구 서비스 자격 증명 모음을 선택합니다.
1.    IIS 웹 팜에 대해 만든 복구 계획을 클릭합니다.
1.    '테스트 장애 조치'를 클릭합니다.
1.    복구 지점 및 Azure 가상 네트워크를 선택하여 테스트 장애 조치 프로세스를 시작합니다.
1.    보조 환경이 가동 중이면 유효성 검사를 수행할 수 있습니다.
1.    유효성 검사가 완료되면 '유효성 검사 완료'를 선택할 수 있으며 테스트 장애 조치 환경이 정리됩니다.

## <a name="doing-a-failover"></a>장애 조치 수행
장애 조치를 수행할 때 [이 지침](site-recovery-failover.md)을 따릅니다.

1.    Azure Portal로 이동하여 복구 서비스 자격 증명 모음을 선택합니다.
1.    IIS 웹 팜에 대해 만든 복구 계획을 클릭합니다.
1.    '장애 조치'를 클릭합니다.
1.    복구 지점을 선택하여 장애 조치 프로세스를 시작합니다.

## <a name="next-steps"></a>다음 단계
Site Recovery를 사용하여 [다른 응용 프로그램을 복제하는 방법](site-recovery-workload.md)에 대해 자세히 알아볼 수 있습니다. 

