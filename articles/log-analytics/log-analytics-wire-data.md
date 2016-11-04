---
title: Log Analytics에서 실시간 데이터 솔루션 | Microsoft Docs
description: 실시간 데이터는 Operations Manager 및 Windows 연결 에이전트를 포함하여 OMS 에이전트 컴퓨터에서 통합된 네트워크 및 성능 데이터입니다. 네트워크 데이터는 데이터를 상호 연결할 수 있도록 로그 데이터와 결합됩니다.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2016
ms.author: banders

---
# <a name="wire-data-solution-in-log-analytics"></a>Log Analytics에서 실시간 데이터 솔루션
실시간 데이터는 Operations Manager 및 Windows 연결 에이전트를 포함하여 OMS 에이전트 컴퓨터에서 통합된 네트워크 및 성능 데이터입니다. 네트워크 데이터는 데이터를 상호 연결할 수 있도록 로그 데이터와 결합됩니다. IT 인프라에서 컴퓨터에 설치된 OMS 에이전트는 사용된 다양한 프로토콜 및 포트를 포함하여 [OSI 모델](https://en.wikipedia.org/wiki/OSI_model) 에서 네트워크 수준 2-3에 해당하는 컴퓨터와 주고 받는 네트워크 데이터를 모니터링합니다.

> [!NOTE]
> 실시간 데이터 솔루션은 현재 작업 영역에 추가할 수 없습니다. 이미 실시간 데이터 솔루션이 사용하도록 설정된 고객은 계속해서 실시간 데이터 솔루션을 사용할 수 있습니다.
> 
> 

기본적으로 OMS는 Windows에 기본 제공된 카운터에서 CPU, 메모리, 디스크 및 네트워크 성능 데이터에 대해 기록된 데이터를 수집합니다. 컴퓨터에 사용되는 서브넷 및 응용 프로그램 수준 프로토콜을 포함하여 네트워크 및 기타 데이터 수집이 에이전트별로 실시간으로 이루어집니다. 로그 탭의 설정 페이지에서 다른 성능 카운터를 추가할 수 있습니다.

[Cisco의 NetFlow 프로토콜](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html)과 함께 [sFlow](http://www.sflow.org/) 또는 다른 소프트웨어를 사용한 경우 실시간 데이터에서 볼 통계 및 데이터는 익숙하게 느껴집니다.

기본 제공 로그 검색 쿼리 유형 중 일부는 다음과 같습니다.

* 실시간 데이터를 제공하는 에이전트
* 실시간 데이터를 제공하는 에이전트의 IP 주소
* IP 주소에서 아웃바운드 통신
* 응용 프로그램 프로토콜에서 보낸 바이트 수
* 응용 프로그램 서비스에서 보낸 바이트 수
* 서로 다른 프로토콜에서 받은 바이트 수
* IP를 통해 보내고 받은 총 바이트 수
* 10.0.0.0/8 서브넷에서 에이전트와 통신한 IP 주소
* 안정적으로 측정된 평균 연결 대기 시간
* 네트워크 트래픽을 시작 또는 수신한 컴퓨터 프로세스
* 프로세스에 대한 네트워크 트래픽의 양

실시간 데이터를 사용하여 검색하는 경우 상위 에이전트 및 상위 프로토콜에 대한 정보를 보기 위해 데이터를 필터링 및 그룹화할 수 있습니다. 또는 특정 컴퓨터(IP 주소/MAC 주소)가 언제, 얼마나 오래 통신하며 기본적으로 얼마나 많은 데이터를 전송하는지 살펴볼 수 있으며 검색 기반의 네트워크 트래픽에 대한 메타데이터를 확인할 수 있습니다.

그러나 메타데이터를 보는 것이므로 자세한 문제 해결에 반드시 유용한 것은 아닙니다. OMS의 실시간 데이터는 네트워크 데이터를 전체 캡처한 것이 아닙니다. 따라서 심도 있는 패킷 수준 문제 해결에는 적절하지 않습니다.
다른 수집 방법에 비해 에이전트를 사용하는 장점은 어플라이언스를 설치하거나 네트워크 스위치를 다시 구성하거나 복잡한 구성을 수행할 필요가 없다는 점입니다. 실시간 데이터는 단순히 에이전트 기반이며 컴퓨터에 에이전트를 설치하고 자체 네트워크 트래픽을 모니터링합니다. 또 다른 장점은 사용자가 패브릭 계층을 소유하지 않는 클라우드 공급자, 호스팅 서비스 공급자 또는 Microsoft Azure에서 실행 중인 워크로드를 모니터링하는 경우입니다.

반면, 네트워크 인프라의 모든 컴퓨터에 에이전트를 설치하지 않는 경우 네트워크에서 발생하는 상황이 전체 표시되지 않습니다.

## <a name="installing-and-configuring-the-solution"></a>솔루션 설치 및 구성
다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

* 실시간 데이터 솔루션은 Windows Server 2012 R2, Windows 8.1 이상의 운영 체제를 실행하는 컴퓨터에서 데이터를 획득합니다.
* 실시간 데이터를 획득하려는 컴퓨터에는 Microsoft .NET Framework 4.0 이상이 필요합니다.
* [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에 설명된 프로세스를 사용하여 OMS 작업 영역에 실시간 데이터 솔루션을 추가합니다.  추가 구성은 필요 없습니다.
* 특정 솔루션에 대한 실시간 데이터를 보려면 OMS 작업 영역에 솔루션이 이미 추가되어 있어야 합니다.

## <a name="wire-data-data-collection-details"></a>실시간 데이터 데이터 수집 세부 정보
실시간 데이터 기능은 설정한 에이전트를 사용하여 네트워크 트래픽에 대한 메타데이터를 수집합니다.

다음 표에서는 데이터 수집 방법 및 실시간 데이터에 대해 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 플랫폼 | 직접 에이전트 | SCOM 에이전트 | Azure 저장소 | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows(2012 R2/8.1 이상) |![예](./media/log-analytics-wire-data/oms-bullet-green.png) |![예](./media/log-analytics-wire-data/oms-bullet-green.png) |![아니요](./media/log-analytics-wire-data/oms-bullet-red.png) |![아니요](./media/log-analytics-wire-data/oms-bullet-red.png) |![아니요](./media/log-analytics-wire-data/oms-bullet-red.png) |1분마다 |

## <a name="combining-wire-data-with-other-solution-data"></a>실시간 데이터를 다른 솔루션 데이터와 결합
위에 표시된 기본 제공 쿼리에서 반환된 데이터는 자체로 흥미로울 수 있습니다. 그러나 다른 OMS 솔루션의 정보로 결합하는 경우 실시간 데이터의 유용성이 실현됩니다. 예를 들어, 보안 및 감사 솔루션에 의해 수집된 보안 이벤트 데이터를 사용하고 이를 실시간 데이터와 결합하여 명명된 프로세스에 대한 비정상적인 네트워크 로그온 시도를 찾습니다.  이 예제에서는 검색 쿼리에서 데이터 요소를 조인하는 데 IN 및 DISTINCT 연산자를 사용합니다.

요구 사항: 다음 예제를 사용하려면 보안 및 감사 솔루션이 설치되어 있어야 합니다. 그러나 다른 솔루션의 데이터를 사용하여 실시간 데이터와 결합하고 유사한 결과를 달성할 수 있습니다.

### <a name="to-combine-wire-data-with-security-events"></a>보안 이벤트와 실시간 데이터를 결합하려면
1. 개요 페이지에서 **WireData** 타일을 클릭합니다.
2. **일반 WireData 쿼리** 목록에서 **프로세스별 네트워크 트래픽의 양(바이트)**을 클릭하여 반환된 프로세스 목록을 표시합니다.
    ![wiredata 쿼리](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. 프로세스 목록이 보기에 너무 길면 다음과 유사하게 검색 쿼리를 수정할 수 있습니다.
   
    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    아래 예제에서는 DancingPigs.exe라는 프로세스가 의심스러울 수 있습니다.
    ![wiredata 검색 결과](./media/log-analytics-wire-data/oms-wiredata-02.png)
4. 목록에 반환된 데이터를 사용하여 명명된 프로세스를 클릭합니다. 이 예제에서는 DancingPigs.exe를 클릭했습니다. 아래에 표시된 결과는 다양한 프로토콜을 통한 아웃바운드 통신과 같은 네트워크 트래픽의 유형을 설명합니다.
    ![명명된 프로세스를 보여 주는 wiredata 결과](./media/log-analytics-wire-data/oms-wiredata-03.png)
5. 보안 및 감사 솔루션이 설치되었으므로 검색 쿼리를 IN 및 DISTINCT 검색 쿼리 연산자로 수정하여 동일한 ProcessName 필드 값이 있는 보안 이벤트에 프로브할 수 있습니다. 실시간 데이터 및 기타 솔루션 로그가 동일한 형식의 값을 포함하는 경우 이 작업을 수행할 수 있습니다. 검색 쿼리를 다음과 같이 수정합니다.
   
    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    
   
    ![결합된 데이터를 보여 주는 wiredata 결과](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. 위의 결과에는 계정 정보가 표시된 것을 알 수 있습니다. 이제 다음과 같은 쿼리로 검색 쿼리를 구체화하여 프로세스에 보안 및 감사 데이터를 보여 주는 계정이 얼마나 자주 사용되었는지 파악할 수 있습니다.        
   
    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```
   
    ![계정 데이터를 보여 주는 wiredata 결과](./media/log-analytics-wire-data/oms-wiredata-05.png)

## <a name="next-steps"></a>다음 단계
* [로그를 검색](log-analytics-log-searches.md) 하여 자세한 실시간 데이터 검색 레코드를 볼 수 있습니다.
* 데이터 수집 빈도와 Operations Manager 에이전트에 대해 컬렉션 속성을 수정하는 방법에 대한 추가 정보는 Dan의 [Operations Management Suite 로그 검색에서 실시간 데이터 사용 블로그 게시물](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx) 을 참조하세요.

<!--HONumber=Oct16_HO2-->


