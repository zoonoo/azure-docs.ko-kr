<properties 
	pageTitle="<MORAVIA_TRANSLATEBizTalk 서비스의 발급자 이름 및 발급자 키 | Azure" 
	description="BizTalk 서비스에서 서비스 버스 또는 액세스 제어(ACS)에 대한 발급자 이름 및 발급자 키를 검색하는 방법에 대해 알아봅니다. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="mandia"/>




# BizTalk 서비스: Editions 차트

Azure BizTalk 서비스는 Free, Developer, Basic, Standard 및 Premium Edition으로 제공됩니다.

**Free(Preview)**: 하이브리드 연결을 만들고 관리하는 기능이 포함되어 있습니다. 하이브리드 연결은 Azure 웹 사이트를 SQL Server와 같은 온-프레미스 시스템에 연결하는 간편한 방법입니다. 

**Developer**: 하이브리드 연결, 사용하기 쉬운 거래 업체 관리 포털을 통한 EAI 및 EDI 메시지 처리, X12와 AS2를 통한 다양한 EDI 처리 및 공통 EDI 스키마 지원 등의 기능이 포함되어 있습니다. 메시지를 읽고 쓰기 위해 HTTP/S, REST, FTP, WCF 및 SFTP 프로토콜로 클라우드에서 서비스를 연결하는 일반 EAI 시나리오를 만들 수 있습니다.  미리 만들어진 SAP, Oracle eBusiness, Oracle DB, Siebel 및 SQL Server 어댑터로 온-프레미스 LOB 시스템에 연결할 수 있습니다. 쉽게 개발하고 배포하기 위해 Visual Studio 도구를 통해 모두 개발자 중심 환경으로 이루어져 있습니다. SLA(서비스 수준 계약)가 없는 개발 및 테스트 용도로만 제한됩니다.

**Basic**: 대부분의 Developer 기능이 포함되어 있으며, 더 많은 하이브리드 연결, EAI 브리지, EDI 계약 및 BizTalk Adapter Pack 연결을 지원합니다. 또한 SLA(서비스 수준 계약)를 통해 확장할 수 있는 옵션 및 고가용성을 제공합니다.

**Standard**: 모든 Basic 기능이 포함되어 있으며, 더 많은 하이브리드 연결, EAI 브리지, EDI 계약 및 BizTalk Adapter Pack 연결을 지원합니다. 또한 SLA(서비스 수준 계약)를 통해 확장할 수 있는 옵션 및 고가용성을 제공합니다.


**Premium**: 모든 Standard 기능이 포함되어 있으며, 더 많은 하이브리드 연결, EAI 브리지, EDI 계약 및 BizTalk Adapter Pack 연결을 지원합니다. 또한 SLA(서비스 수준 계약)를 통해 확장할 수 있는 옵션, 보관 및 고가용성을 제공합니다.


다음 표에는 차이점이 나와 있습니다.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Free(Preview)</th>
        <th>Developer</th>
        <th>Basic</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>
<tr>
<td><strong>가격</strong></td>
<td><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk 서비스 가격</a>을 참조하세요.</td>
<td><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk 서비스 가격</a>을 참조하세요.</td>
<td><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk 서비스 가격</a>을 참조하세요.</td>
<td><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk 서비스 가격</a>을 참조하세요.</td>
<td><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk 서비스 가격</a>을 참조하세요.</td>
</tr>
<tr>
<td><strong>기본 최소 구성</strong></td>
<td>Free 장치 1개</td>
<td>Developer 장치 1개</td>
<td>Basic 장치 1개</td>
<td>Standard 장치 1개</td>
<td>Premium 장치 1개</td>
</tr>
<tr>
<td><strong>크기 조정</strong></td>
<td>확장 안 함</td>
<td>확장 안 함</td>
<td>1 Basic 장치씩 확장함</td>
<td>1 표준 장치씩 확장함</td>
<td>1 프리미엄 장치씩 확장함</td>
</tr>
<tr>
<td><strong>최대 허용 확장 규모</strong></td>
<td>확장 안 함</td>
<td>확장 안 함</td>
<td>최대 8개</td>
<td>최대 8개</td>
<td>최대 8개</td>
</tr>
<tr>
<td><strong>장치당 EAI 브리지</strong></td>
<td>포함되지 않음</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
TPM 계약 포함</td>
<td>포함되지 않음</td>
<td>포함됨. 장치당 계약 10개</td>
<td>포함됨. 장치당 계약 50개</td>
<td>포함됨. 장치당 계약 250개</td>
<td>포함됨. 장치당 계약 1000개</td>
</tr>
<tr>
<td><strong>장치당 하이브리드 연결 수</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>장치당 하이브리드 연결 데이터 전송(GB)</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>온-프레미스 LOB 시스템에 BizTalk 어댑터 서비스 연결</strong></td>
<td>포함되지 않음</td>
<td>1개 연결</td>
<td>2개 연결</td>
<td>5개 연결</td>
<td>25개 연결</td>
</tr>
<tr>
<td align="left"><strong>지원되는 프로토콜/시스템:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>SB(서비스 버스)</li>
<li>Azure Blob</li>
<li>REST API</li>
</ul>
</td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
</tr>
<tr>
<td><strong>고가용성</strong>
<br/><br/>
SLA(서비스 수준 계약)는 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk 서비스 가격</a>을 참조하세요.
</td>
<td>포함되지 않음</td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
</tr>
<tr>
<td><strong>백업 및 복원</strong></td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
</tr>
<tr>
<td><strong>추적</strong></td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
</tr>
<tr>
<td><strong>보관</strong><br/><br/>
추적 메시지 다운로드 및 NRR(수신 부인 방지) 포함</td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함되지 않음</td>
<td>포함되지 않음</td>
<td>포함됨</td>
</tr>
<tr>
<td><strong>사용자 지정 코드 사용</strong></td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
</tr>
<tr>
<td><strong>변환 사용(사용자 지정 XSLT 포함)</strong></td>
<td>포함되지 않음</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
<td>포함됨</td>
</tr>
</table>

**참고**
<br/>하드웨어 오류에 대한 복원력을 위해 고가용성은 단일 BizTalk 장치 내에 반드시 VM을 여러 개 가집니다.

## FAQ
#### BizTalk 장치란 무엇입니까?
"장치"는 원자 수준의 Azure BizTalk 서비스 배포입니다. 각 버전에는 계산 용량 및 메모리가 다른 장치가 제공됩니다. 예를 들어 Basic 장치는 Developer 장치보다 계산 용량이 더 크고, Standard 장치는 Basic 장치보다 계산 용량이 더 크며 등등합니다. BizTalk 서비스를 확장할 때 장치로 확장합니다.

#### BizTalk 서비스와 Azure BizTalk VM 간의 차이점은 무엇입니까?
BizTalk 서비스는 클라우드에서 통합 솔루션을 구축하는 데 진정한 PaaS(Platform-as-a-Service) 아키텍처를 제공합니다. PaaS 모델을 사용하여 응용 프로그램 논리에 완전히 초점을 맞추며 다음을 포함한 모든 인프라 관리는 Microsoft에 맡깁니다.

- 가상 컴퓨터를 관리하거나 패치할 필요 없음
- Microsoft에서 가용성을 보증함
- Azure 관리 포털을 통해 용량을 더 많거나 더 적게 간단히 요청하여 주문형 확장을 제어함

Azure 가상 컴퓨터의 BizTalk Server는 IaaS(Infrastructure-as-a-Service) 아키텍처를 제공합니다. 가상 컴퓨터를 만들고 온-프레미스 환경과 똑같이 구성하여 코드를 변경하지 않고도 클라우드에서 기존 응용 프로그램을 더 쉽게 실행할 수 있습니다. IaaS를 사용하여 가상 컴퓨터를 구성하고 관리하고(예: 소프트웨어 설치 및 OS 패치)   응용 프로그램의 고가용성을 구성해야 합니다. 

인프라 관리 노력을 최소화하는 새로운 통합 솔루션을 구축하려면 BizTalk 서비스를 사용하세요. 기존 BizTalk 솔루션을 마이그레이션하거나 BizTalk Server 응용 프로그램을 개발하고 테스트하기 위해 주문형 환경을 찾으려면 Azure 가상 컴퓨터에서 BizTalk Server를 사용하세요.

#### BizTalk 어댑터 서비스와 하이브리드 연결 간의 차이점은 무엇입니까?
BizTalk 어댑터 서비스는 Azure BizTalk 서비스에서 사용됩니다. BizTalk 어댑터 서비스에서는 BizTalk Adapter Pack을 사용하여 온-프레미스 LOB(기간 업무) 시스템에 연결합니다. 하이브리드 연결은 웹 사이트 및 모바일 서비스와 같은 Azure 응용 프로그램을 온-프레미스 리소스에 연결할 수 있는 간단하고 편리한 방법을 제공합니다. 

#### BizTalk 서비스에서 계약을 생성할 때 왜 브리지 수가 하나가 아닌 두 개씩 증가합니까? 

각 계약은 두 개의 다른 브리지(송신 측 통신 브리지와 수신 측 통신 브리지)로 구성되어 있습니다.

####  브리지 또는 계약 개수가 할당량 한도를 초과하면 어떻게 됩니까? 

새 브리지를 배포하거나 새 계약을 생성할 수 없습니다. 더 많이 배포하려면 BizTalk 서비스의 장치를 좀 더 확장하거나 더 높은 버전으로 업그레이드해야 합니다.

#### BizTalk 서비스의 한 단계에서 어떻게 다른 단계로 마이그레이션합니까? 

백업을 사용하고 한 단계에서 다른 단계로의 마이그레이션 흐름을 복원합니다. 일부 마이그레이션 경로만 지원됩니다. 지원되는 마이그레이션 경로에 대한 자세한 내용은 [BizTalk 서비스: 백업 및 복원](http://go.microsoft.com/fwlink/p/?LinkID=329873)을 참조하세요.

#### 이 서비스에 BizTalk 어댑터 서비스도 포함되어 있습니까? 소프트웨어를 어떻게 받습니까?

네. BizTalk 어댑터 서비스는 BizTalk Adapter Pack과 함께 Azure BizTalk 서비스 SDK [다운로드](http://www.microsoft.com/download/details.aspx?id=39087)에 포함되어 있습니다.

## 다음

Azure 관리 포털에서 Azure BizTalk 서비스를 프로비전하려면 [BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)을 참조하세요. 응용 프로그램을 만들려면 [Azure BizTalk 서비스](http://go.microsoft.com/fwlink/p/?LinkID=235197)로 이동하세요.

## 참고 항목
- [BizTalk 서비스: Azure 관리 포털을 사용하여 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk 서비스: 상태 차트 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk 서비스: 백업 및 복원](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk 서비스: 제한](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk 서비스: 발급자 이름 및 발급자 키](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Azure BizTalk 서비스 SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

<!--HONumber=46--> 
