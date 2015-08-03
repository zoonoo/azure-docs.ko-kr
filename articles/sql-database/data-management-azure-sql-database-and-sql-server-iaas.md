<properties 
	pageTitle="Azure SQL 데이터베이스 및 Azure VM의 SQL Server 이해" 
	description="Azure 가상 컴퓨터의 Azure SQL 데이터베이스 및 SQL Server를 이해합니다. 일반적인 비즈니스 동기 부여 요소를 검토하여 응용 프로그램에 가장 적합한 SQL 기술을 확인합니다." 
	services="sql-database, virtual-machines" 
	documentationCenter="" 
	authors="Selcin" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="selcint"/>

# Azure SQL 데이터베이스 및 Azure VM의 SQL Server 이해

Microsoft Azure는 SQL Server를 호스팅하는 것에 대한 두 가지 옵션을 제공합니다: **Azure SQL 데이터베이스** 및 **Azure 가상 컴퓨터의 SQL Server**. 이 문서에서는 먼저 Microsoft 데이터 플랫폼의 전체 환경에서 각 옵션이 얼마나 적합한지 살펴본 다음 계속해서 선택의 동기가 되는 비즈니스 요구 사항을 바탕으로 더 깊이 있게 논의합니다. 비용 절감이 우선이든 관리 최소화가 무엇보다 중요하든 이 문서에서는 가장 관심 있는 비즈니스 요구 사항에 대해 각 옵션이 어떤 결과를 내놓는지를 기준으로 적합한 접근 방식을 결정하도록 도울 수 있습니다.

- [Microsoft의 데이터 플랫폼](#platform)
- [Azure SQL 데이터베이스 및 Azure VM의 SQL Server에 대해 자세히 살펴보기](#close)	
- [Azure SQL 데이터베이스 또는 Azure VM의 SQL Server를 선택할 때 비즈니스 동기](#business)	
	- [비용](#cost)
		- [청구 및 라이선스 기본 사항](#billing)	
		- [총 응용 프로그램 비용 계산](#appcost)	
	- [관리](#admin)	
	- [SLA(서비스 수준 계약)](#sla)	
	- [출시 시간](#market)	
- [요약](#summary)	
- [승인](#ack)	
- [추가 리소스](#resources)	


##<a name="platform"></a>Microsoft의 데이터 플랫폼

Azure와 온-프레미스 SQL Server 데이터베이스를 비교 논의할 때 우선 이 둘을 모두 사용할 수 있다는 점을 이해해야 합니다. Microsoft의 데이터 플랫폼에서는 SQL Server 기술을 활용하며 물리적 온-프레미스 컴퓨터, 사설 클라우드 환경, 타사 호스팅 사설 클라우드 환경 및 공용 클라우드 전반에서 이 기술을 사용할 수 있습니다. 따라서 온-프레미스 배포와 클라우드에 호스트된 배포를 조합하여 고유하고 다양한 비즈니스 요구 사항을 충족하고, 이러한 환경 전반에서 일련의 서버 제품, 개발 도구 및 전문 지식을 동일하게 사용할 수 있습니다.

   ![][1]

다이어그램에 나온 대로 각 제품의 특징을 인프라에 대한 관리 수준(X축)과 데이터베이스 수준 통합 및 자동화를 통해 얻는 비용 효율성의 정도(Y축)를 기준으로 설명할 수 있습니다.

응용 프로그램을 설계할 때 다음 네 가지 기본 옵션을 사용하여 응용 프로그램의 SQL Server 부분을 호스트할 수 있습니다.

- 가상화되지 않은 물리적 컴퓨터의 SQL Server 
- 온-프레미스 가상화 컴퓨터의 SQL Server(사설 클라우드)
- Azure 가상 컴퓨터의 SQL Server(공용 클라우드)
- Azure SQL 데이터베이스(공용 클라우드)

다음 섹션에서 우리는 마지막 두 가지를 배웁니다: Azure SQL 데이터베이스 및 Azure VMs에서 SQL Server 또한 응용 프로그램에 가장 적합한 옵션을 결정하는 데 영향을 미치는 일반적인 비즈니스 동인도 살펴봅니다.

##<a name="close"></a>Azure SQL 데이터베이스 및 Azure VM의 SQL Server에서 자세히 보기

**Microsoft Azure SQL 데이터베이스 (Azure SQL 데이터베이스)** 는 관계형 DaaS(Database-as-a-Service)로, *PaaS(Platform as a Service)* 산업 부분으로 분류됩니다. Azure SQL 데이터베이스는 Microsoft에서 소유하고 호스트하고 유지 관리하는 표준화된 하드웨어 및 소프트웨어를 기반으로 구축됩니다. SQL 데이터베이스에서는 기본 제공 기능을 사용하여 서비스를 직접 개발할 수 있습니다. SQL 데이터베이스를 사용할 경우 필요한 추가 성능에 맞게 강화 및 규모 확장하는 옵션으로 사용량에 따라 지불합니다.

**Azure VM(가상 컴퓨터)의 SQL Server**는 업계 범주에 속하는 *IaaS(Infrastructure as a service)* 산업 부분으로 분류되며 클라우드의 가상 컴퓨터 내에서 SQL Server를 실행할 수 있도록 지원합니다. Azure SQL 데이터베이스와 마찬가지로 Microsoft에서 소유하고 호스트하고 유지 관리하는 표준화된 하드웨어를 기반으로 구축됩니다. VM의 SQL Server를 사용할 경우 고유한 SQL Server 라이선스를 Azure로 가져오거나 Azure 포털에 미리 구성된 SQL Server 이미지 중 하나를 사용할 수 있습니다.

일반적으로 이러한 두 SQL 옵션은 다음과 같이 최적의 용도가 서로 다릅니다.

- **Azure SQL 데이터베이스**는 여러 데이터베이스를 프로비전 및 관리하는 전체 비용을 최소한으로 줄이는 데에 최적화되어 있습니다. 가상 컴퓨터, 운영 체제 또는 데이터베이스 소프트웨어의 업그레이드, 고가용성, 백업 등을 관리할 필요가 없으므로 지속적인 관리 비용을 최소화합니다. 일반적으로 SQL 데이터베이스는 단일 IT 또는 개발 리소스로 관리되는 데이터베이스 수를 크게 늘립니다.
- **Azure VM에서 실행되는 SQL Server**는 하이브리드 시나리오에서 기존 온-프레미스 SQL Server 응용 프로그램을 Azure로 확장하거나 마이그레이션 시나리오 또는 개발/테스트 시나리오에서 기존 응용 프로그램을 Azure에 배포하는 데 가장 적합합니다. 하이브리드 시나리오의 예로 [Azure 가상 네트워크](http://msdn.microsoft.com/library/azure/jj156007.aspx)를 통해 보조 데이터베이스 복제본을 Azure에 유지하는 경우를 들 수 있습니다. Azure VM의 SQL Server를 사용할 경우 전용 SQL Server 인스턴스와 클라우드 기반 VM에 대한 모든 관리 권한을 갖습니다. 이 옵션은 가상 컴퓨터를 유지 관리하는 IT 리소스를 이미 갖춘 조직에게 가장 적합합니다. VM의 SQL Server를 사용하면 응용 프로그램 관련 성능 및 가용성 요구 사항을 해결하는 고도로 사용자 지정된 시스템을 구축할 수 있습니다.

다음 표에는 Azure SQL 데이터베이스와 Azure VM의 SQL Server의 주요 특징이 요약되어 있습니다.

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">Azure SQL 데이터베이스</th>
   <th align="left" valign="middle">Azure VM의 SQL Server</th>
   
</tr>
<tr>
   <td valign="middle"><p><b>최적 용도</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>개발 및 마케팅에 시간 제약이 따르는 클라우드용으로 설계된 새 응용 프로그램 
          <li type=round>고가용성, 재해 복구 솔루션 및 업그레이드 메커니즘을 자동으로 기본 제공해야 하는 응용 프로그램
          <li type=round>데이터베이스 수는 수백 또는 수천이 넘지만 기본 운영 체제, 하드웨어 및 구성 설정을 관리하지 않으려는 경우 
         <li type=round>규모 확장 패턴을 사용하는 응용 프로그램
         <li type=round>크기가 최대 500GB인 데이터베이스
         <li type=round>Software-as-a-Service 응용 프로그램 빌드
         
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>기존 응용 프로그램을 변경은 최소로 하고 클라우드로 빠르게 마이그레이션해야 하는 경우
      <li type=round>SQL Server 응용 프로그램이 Azure에서 보안 터널을 통해 Active Directory와 같은 온-프레미스 리소스에 액세스해야 하는 경우 
      <li type=round>모든 관리 권한이 있는 사용자 지정된 IT 환경이 필요한 경우
      <li type=round>온-프레미스 개발 및 테스팅 SQL Server 하드웨어를 구입하지 않는 빠른 개발 및 테스트 시나리오
      <li type=round><a href="http://msdn.microsoft.com/library/jj919148.aspx">Azure 저장소의 백업</a> 또는<a href="http://msdn.microsoft.com/library/azure/jj870962.aspx">Azure VMs의 AlwaysOn 복제본</a>을 사용한 온-프레미스 SQL Server 응용 프로그램에 대한 재해 복구
      <li type=round>크기가 1TB 이상인 대형 데이터베이스
      </ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>리소스</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>기본 인프라의 지원 및 유지 관리에 IT 리소스를 사용하고 싶지 않습니다.
       <li type=round>응용 프로그램 계층에 집중하고 싶습니다.
       </ul></td>
   <td valign="middle"><ul><li type=round>지원 및 유지 관리용 IT 리소스가 있습니다.</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>총 소유 비용</b></p></td>
   <td valign="middle"><ul><li type=round>하드웨어 비용을 제거합니다. 관리 비용을 줄입니다.</ul></td>
   <td valign="middle"><ul><li type=round>하드웨어 비용을 제거합니다. </ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>비즈니스 연속성</b></p></td>
   <td valign="middle"><ul><li type=round>Azure SQL 데이터베이스는 내결함성 인프라 기능을 기본적으로 제공할 뿐 아니라 지정 시간 복원, 지역 복원 및 지역에서 복제 등의 다양한 기능을 제공하여 비즈니스 연속성을 향상합니다. 자세한 내용은 <a href="http://msdn.microsoft.com/library/azure/hh852669.aspx">Azure SQL 데이터베이스 비즈니스 연속성</a>을 참조하세요.</ul></td>
   <td valign="middle"><ul><li type=round>Azure VM의 SQL Server를 사용하면 데이터베이스의 특정 요구에 맞게 고가용성 및 재해 복구 솔루션을 설정할 수 있습니다. 따라서 시스템을 응용 프로그램에 최적화할 수 있습니다. 필요한 경우 장애 조치(Failover)를 직접 테스트하고 실행할 수 있습니다. 자세한 내용은 <a href="http://msdn.microsoft.com/library/azure/jj870962.aspx">Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해복구</a>를 참조하세요.</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>하이브리드 클라우드</b></p></td>
   <td valign="middle"><ul><li type=round>온-프레미스 응용 프로그램은 Azure SQL 데이터베이스의 데이터에 액세스할 수 있습니다.</ul></td>
   <td valign="middle"><ul>
      <li type=round>Azure VM의 SQL Server를 사용하면 응용 프로그램을 일부는 클라우드에서, 일부는 온-프레미스에서 실행할 수 있습니다. 예를 들어 온-프레미스 네트워크 및 도메인의 Active Directory를 <a href="http://msdn.microsoft.com/library/azure/gg433091.aspx">Azure 네트워크 서비스</a>를 통해 클라우드로 확장할 수 있습니다. 또한<a href="http://msdn.microsoft.com/library/dn385720.aspx">Azure의 SQL Server 데이터 파일기능</a>을 사용하여 온-프레미스 데이터 파일을 Azure 저장소에 저장할 수도 있습니다. 자세한 내용은 <a href="http://msdn.microsoft.com/library/dn606154.aspx">SQL Server 2014 하이브리드 클라우드 소개</a>를 참조하세요.
      <li type=round><a href="http://msdn.microsoft.com/library/jj919148.aspx">Azure 저장소의 백업</a> 또는 <a href="http://msdn.microsoft.com/library/azure/jj870962.aspx">Azure VMs의 AlwaysOn 복제본</a>을 사용한 온-프레미스 SQL Server 응용 프로그램에 대한 재해 복구를 지원합니다.
      </ul></td>
   
</tr>
</table>

##<a name="business"></a>Azure SQL 데이터 베이스 또는 Azure VM의 SQL Server를 선택할 때 비즈니스 동기

###<a name="cost"></a>비용

현금이 쪼들리는 신생 기업이든 자리를 잡은 회사에서 빠듯한 예산으로 운영되는 팀이든 데이터베이스를 호스트하는 방법을 결정할 때 제한된 자금이 주된 동인으로 작용하는 경우가 많습니다. 이 섹션에서는 먼저 Azure의 청구 및 라이선스 기본 사항을 위의 두 가지 관계형 데이터베이스 옵션에 대해 알아봅니다: Azure SQL Database와 Azure VM에서 SQL Server 그런 다음 총 응용 프로그램 비용을 계산하는 방법을 살펴봅니다.

####<a name="billing"></a>청구 및 라이선스 기본 사항

**Azure SQL 데이터베이스**는 고객에게 라이선스가 아니라 서비스로 판매되는 반면 Azure VM의 SQL Server는 기존 SQL Server 라이선스가 필요합니다.

현재 **Azure SQL 데이터베이스**는 여러 서비스 계층으로 제공됩니다. Basic, Standard 및 Premium 서비스 계층의 경우 선택하는 서비스 계층과 성능 수준을 기준으로 고정 요금이 시간당 청구됩니다. Basic, Standard 및 Premium 서비스 계층은 응용 프로그램의 최대 요구 사항에 맞게 여러 성능 수준의 예측 가능한 성능을 제공하기 위해 설계되었습니다. 응용 프로그램의 다양한 처리량 요구에 맞게 서비스 계층과 성능 수준을 변경할 수 있습니다. 현재 지원되는 서비스 계층에 대한 최신 정보는 [Azure SQL 데이터베이스 서비스 계층(버전)](http://msdn.microsoft.com/library/azure/dn741340.aspx)을 참조하세요.

**Azure SQL 데이터베이스**에서는 데이터베이스 소프트웨어의 구성, 패치 적용 및 업그레이드가 전 세계 데이터 센터의 Microsoft Azure를 통해 자동으로 수행됩니다. 따라서 관리 비용이 줄어듭니다. 또한 [기본 제공 백업](http://msdn.microsoft.com/library/azure/jj650016.aspx) 기능을 사용하여 비용을 크게 절감할 수 있으며, 특히 데이터베이스 수가 많을 경우 그 효과가 큽니다. Azure SQL 데이터베이스를 사용할 경우 Azure SQL 데이터베이스에 대해 실행하는 개별 쿼리나 수신/발신 인터넷 트래픽에 대해 요금이 청구되지 않습니다. 데이터베이스에 트랜잭션 볼륨이 많고 지원해야 하는 동시 사용자가 많은 경우 Basic 또는 Standard 서비스 계층보다는 Premium을 사용하는 것이 좋습니다.

**Azure VM의 SQL Server**에서는 기존 SQL Server 라이선스를 사용합니다. 플랫폼에서 제공하는 SQL Server 이미지를 사용하거나 자신의 SQL Server 라이선스를 Azure로 가져올 수 있습니다. SQL Server 플랫폼에서 제공하는 이미지를 사용할 경우 VM 크기 및 선택하는 SQL Server 버전에 따라 비용이 달라집니다. 기본적으로 SQL Server에 대한 분당 라이선스 비용, Windows Server에 대한 분당 라이선스 비용 및 Azure 저장소 비용을 지불합니다. 분당 청구 옵션을 사용하면 전체 SQL Server 라이선스를 구입하지 않고 필요한 시간 동안만 SQL Server를 사용할 수 있습니다. 고유한 SQL Server 라이선스를 Azure에 가져오는 경우 Azure 계산 및 저장소 비용만 청구됩니다. 자세한 내용은 [Azure에서 Software Assurance를 통한 라이선스 이동](http://azure.microsoft.com/pricing/license-mobility/)을 참조하세요.

####<a name="appcost"></a>총 응용 프로그램 비용 계산

클라우드 플랫폼 사용을 시작하면 응용 프로그램 실행 비용에는 기본적으로 개발 및 관리 비용이 포함되고 공용 클라우드 플랫폼에 필요한 서비스 비용도 포함됩니다.

다음은 Azure SQL 데이터베이스 및 Azure VM의 SQL Server에서 실행 중인 응용 프로그램에 대한 자세한 비용 계산입니다.

**Azure SQL 데이터베이스를 사용하는 경우:**

*총 응용 프로그램 비용 = 최소 관리 비용 + 소프트웨어 개발 비용 + Azure SQL 데이터베이스 서비스 비용*

**Azure VM의 SQL Server를 사용하는 경우:**

*총 응용 프로그램 비용 = 최소 소프트웨어 개발/수정 비용 + 관리 비용 + SQL Server 및 Windows Server 라이선스 비용 + Azure 저장소 비용*

**중요 참고 사항:**현재 Azure SQL 데이터베이스는 SQL Server의 일부 기능을 지원하지 않습니다. 자세한 비교 정보는[Azure SQL 데이터베이스 지침 및 제한 사항](http://msdn.microsoft.com/library/azure/ff394102.aspx)을 참조하세요. 따라서 기존 데이터베이스를 Azure SQL 데이터베이스로 이동하려는 경우 데이터베이스 재설계에 따른 추가 예산이 필요할 수 있습니다. Azure SQL 데이터베이스는 Microsoft의 PasS(Platform-as-a-Service) 제품입니다. 기존 온-프레미스 SQL Server 응용 프로그램을 Azure SQL 데이터베이스로 마이그레이션하는 경우에는 PasS(Platform-as-a-Service) 제품을 최대한 활용하기 위해 응용 프로그램을 업데이트하는 것이 좋습니다. 예를 들어 응용 프로그램 계층에서 [Azure 웹 사이트](http://azure.microsoft.com/documentation/services/websites/)나 [Azure 클라우드 서비스](http://azure.microsoft.com/services/cloud-services/)를 사용하면 비용 혜택이 증가합니다. 또한 서로 다른 Azure SQL 데이터베이스 서비스 계층에 대해 응용 프로그램의 유효성을 검사하고 응용 프로그램 요구에 가장 적합한 계층을 확인하세요. 이러한 과정을 통해 성능 결과를 더 향상하고 비용을 최소화할 수 있습니다. 자세한 정보는 [Azure SQL 데이터베이스 서비스 계층 및 성능 수준](http://msdn.microsoft.com/library/azure/dn741336.aspx)을 참조하세요.

예상 비용을 자세히 알아보려면 [Azure 비용 계산기](http://azure.microsoft.com/pricing/calculator/)를 사용하세요.

가격에 대한 자세한 내용은 다음 리소스를 참조하세요.

- [Azure SQL 데이터베이스 가격 정보](http://azure.microsoft.com/pricing/details/sql-database/) 
- [가상 컴퓨터 가격 정보](http://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure VM의 SQL Server - 가격 정보](http://azure.microsoft.com/pricing/details/virtual-machines/#sql-server)
- [Azure VM의 Windows Server - 가격 정보](http://azure.microsoft.com/pricing/details/virtual-machines/#windows) 

###<a name="admin"></a>관리

이미 여러 작업으로 분주하다면 서버 및 데이터베이스 관리를 맡고 싶지 않을 것입니다. 많은 기업이 클라우드를 선택하는 이유는 전적으로 관리의 복잡성을 줄일 수 있기 때문입니다. **Azure SQL 데이터베이스**에서는 Microsoft가 하드 드라이브, 서버 및 저장소와 같은 물리적 하드웨어를 관리하고, 모든 데이터를 자동으로 복제하여 고가용성을 제공하고, 데이터베이스 소프트웨어를 구성 및 업그레이드하고, 부하 분산을 관리하고, 서버 오류가 발생하는 경우 장애 조치(Failover)를 투명하게 수행합니다. 사용자가 Azure SQL 데이터베이스 인스턴스를 계속 관리할 수 있지만 기본 SQL Server 인스턴스 및 Azure 플랫폼의 물리적 리소스를 제어하지 않아도 됩니다. 예를 들어 데이터베이스 및 로그인을 관리하고, 인덱스 조정을 수행하고, 쿼리를 최적화할 수는 있지만 시스템 테이블 및 파일 그룹 관리는 수행할 수 없습니다. 자세한 내용은 [Azure SQL 데이터베이스 지침 및 제한 사항](http://msdn.microsoft.com/library/ff394102.aspx)을 참조하세요.

반면에 사내 전문성을 갖추고 데이터베이스 위치에서 컴퓨터 자체에 이르기까지 제어할 수 있는 기능을 유지하고 싶을 수도 있습니다. **Azure VM에서 실행되는 SQL Server**에서는 운영 체제와 SQL Server 인스턴스 구성을 완벽히 제어할 수 있습니다. VM에서는 운영 체제 및 데이터베이스 소프트웨어를 업데이트/업그레이드할 시기 및 바이러스 백신, 백업 도구 등의 추가 소프트웨어를 설치할 시기를 사용자가 결정합니다. 또한 VM 크기, 디스크 수 및 해당 저장소 구성도 사용자가 제어할 수 있습니다. 예를 들어 Azure를 사용하여 실행 중인 VM 크기를 필요에 맞게 변경할 수 있습니다. 자세한 내용은 [Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](http://msdn.microsoft.com/library/azure/dn197896.aspx)를 참조하세요.

###<a name="sla"></a>SLA(서비스 수준 계약)

일부에게는 SLA(서비스 수준 계약)의 작동 시간 의무를 충족하는 일이 가장 우선합니다. 이 섹션에서는 각 데이터베이스 호스팅 옵션이 SLA에 어떤 의미가 있는지 살펴봅니다.

**Azure SQL 데이터베이스**의 경우 Basic, Standard 및 Premium 서비스 계정에 대해 Microsoft는 99.99%의 가용성 SLA를 제공합니다. 가용성 SLA란 데이터베이스에 연결할 수 있는 기능을 나타냅니다. 즉, 데이터베이스 수준 SLA입니다. SLA에 대한 최신 정보는 [서비스 수준 계약](http://azure.microsoft.com/support/legal/sla/)을 참조하세요. Azure SQL 데이터베이스 서비스 계층(버전) 및 지원되는 비즈니스 연속성 계획에 대한 최신 정보는 [Azure SQL 데이터베이스 서비스 계층](http://msdn.microsoft.com/library/dn741340.aspx)을 참조하세요.

**Azure에 호스트된 가상 컴퓨터**의 경우 Microsoft는 99.95%의 가용성 SLA를 제공하며, 이는 VM 내에서 실행되는 프로세스(SQL Server 등)에 대한 가용성이 아니라 VM에 대한 가용성입니다. [VM SLA](http://www.microsoft.com/download/details.aspx?id=38427)를 이용하려면 두 개 이상의 VM을 가용성 집합으로 호스트해야 합니다. 이러한 구성을 사용하면 Azure에서는 VM 중 하나 이상을 99.95%의 시간 동안 사용할 수 있음을 보장합니다. VM 내에서 데이터베이스 고가용성(HA)을 이용하려면 SQL Server에서 지원되는 고가용성 옵션 중 하나(예: AlwaysOn 가용성 그룹)를 구성해야 합니다. Azure에서 AlwaysOn을 설정하려면 일부 수동 구성과 관리가 필요하며 작동하는 각 보조 항목에 대해 추가로 지불해야 합니다.


###<a name="market"></a>출시 시간

**Azure SQL 데이터베이스**는 개발자 생산성과 빠른 출시 시간이 중요한 경우 클라우드용으로 설계된 응용 프로그램에 적합한 솔루션입니다. 프로그래밍 방식 DBA와 비슷한 기능을 사용하여 기본 운영 체제 및 데이터베이스를 관리할 필요성을 낮추므로 클라우드 설계자와 개발자에게 이상적인 솔루션입니다. 개발자가 데이터베이스 관련 작업을 파악하고 구성하는 데 도움을 줍니다. 예를 들어 [REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) 및 [PowerShell cmdlet](http://msdn.microsoft.com/library/azure/dn546726.aspx)을 사용하여 수천 개 데이터베이스에 대한 관리 작업을 자동화할 수 있습니다. 클라우드의 [탄력적인 규모 조정](sql-database-elastic-pool.md)을 통해 응용 프로그램 계층에 집중하기 쉽고 응용 프로그램을 더 빨리 출시할 수 있습니다.

**Azure VM에서 실행되는 SQL Server**는 기존 응용 프로그램과 새 응용 프로그램이 SQL Server 인스턴스의 모든 기능을 액세스하고 제어해야 하는 경우와 기존 온-프레미스 응용 프로그램 및 데이터베이스를 클라우드로 그대로 마이그레이션하려는 경우에 이상적입니다. 프레젠테이션, 응용 프로그램 및 데이터 계층을 변경할 필요가 없으므로 기존 솔루션 재설계에 따른 비용과 예산이 절감됩니다. 대신 모든 솔루션 패키지를 VM으로 마이그레이션하고 Azure 플랫폼에 필요한 일부 성능 최적화를 수행하는 데 집중할 수 있습니다. 자세한 내용은 [Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례](http://msdn.microsoft.com/library/azure/dn133149.aspx)를 참조하세요.

##<a name="summary"></a>요약

이 문서에서는Azure SQL 데이터베이스 및 Azure VM의 SQL Server에 대해 알아봅니다. 또한 어떤 제품을 선택할지 결정하는 데 영향을 줄 수 있는 일반적인 비즈니스 동인도 설명했습니다.

다음은 둘 중 하나를 선택할 때 고려할 제안 사항을 요약한 내용입니다.

**Azure SQL 데이터베이스**를 선택하는 경우:

- 새로운 클라우드 기반 응용 프로그램을 빌드하는 경우 또는 기존 SQL Server 데이터베이스를 Azure로 마이그레이션하려고 하는데 데이터베이스에서 Azure SQL 데이터베이스에서 지원되지 않는 기능은 사용하지 않는 경우. 자세한 내용은 [Azure SQL 데이터베이스 Transact-SQL 참조](http://msdn.microsoft.com/library/azure/ee336281.aspx)를 참조하세요. 이 접근 방식을 사용하면 클라우드 서비스를 완전히 관리할 수 있고 빠른 출시 시간이 보장됩니다.

- 데이터베이스의 일반 관리 작업을 Microsoft로 하여금 수행하게 하고 데이터베이스에 대해 높은 가용성 SLA가 필요한 경우. 이 접근 방식은 관리 비용을 최소화하는 동시에 데이터베이스에 대한 가용성을 보장합니다.

**Azure VM의 SQL Server**를 선택하는 경우:

- 기존 온-프레미스 응용 프로그램이 있는데 자체적인 하드웨어 유지 관리를 중지하고 싶거나 하이브리드 솔루션을 고려하는 경우. 이 접근 방식을 사용하면 고용량 데이터베이스에 빠르게 액세스할 수 있고 온-프레미스 응용 프로그램을 보안 터널을 통해 클라우드에 연결할 수 있습니다.

- 기존 IT 리소스가 있고, SQL Server에 대한 모든 관리 권한이 필요하고, 온-프레미스 SQL Server와의 완벽한 호환성이 필요한 경우(예: 일부 기능이 Azure SQL 데이터베이스에 없는 경우). 이 접근 방식을 사용하면 대부분의 응용 프로그램을 유연하게 실행할 수 있는 기능을 통해 기존 응용 프로그램의 개발 또는 수정 비용을 최소화할 수 있습니다. 또한 VM, 운영 체제 및 데이터베이스 구성을 완벽히 제어할 수 있습니다.

> [AZURE.NOTE]- SQL Server 2016 CTP2를 사용해보시겠습니까? Microsoft Azure에 등록한 다음 [여기](http://aka.ms/sql2016vm "여기")로 이동하여 SQL Server 2016 CTP2가 이미 설치된 가상 컴퓨터를 스핀업합니다.


##<a name="ack"></a>감사의 말

이 문서는 Microsoft 클라우드 및 엔터프라이즈 콘텐츠 서비스 그룹이 많은 Microsoft 커뮤니티 사람들의 도움을 받아 제작했습니다.

**작성자:** Selcin Turkarslan

**기술 참여자:** Conor Cunningham

**기술 검토자:** Joanne Marone (Hodgins), Karthika Raman, Lindsey Allen, Lori Clark, Luis Carlos Vargas Herring, Nosheen Syed Wajahatulla Hussain, Pravin Mittal, Shawn Bice, Silvano Coriani, Tony Petrossian, Tracy Daugherty.

**편집 검토자:** Heidi Steen, Maggie Sparkman.

이 문서가 나올 수 있도록 도움을 주신 모든 분께 감사드립니다.

##<a name="resources"></a>추가 리소스 

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">리소스</th>
   <th align="left" valign="middle">설명</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/ee336279.aspx">MSDN: Azure SQL Database</a></p>
<p><a href="http://msdn.microsoft.com/library/azure/jj823132.aspx">MSDN: SQL Server in Azure Virtual Machines</a></p>

<p><a href="http://azure.microsoft.com/services/sql-database/">Azure.com: Azure SQL Database</a></p></td>
   <td valign="middle">라이브러리 문서에 대한 링크입니다.</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/dn574746.aspx">Azure 가상 컴퓨터의 SQL Server를 위한 응용 프로그램 패턴 및 개발 전략</p></td>
   <td valign="middle">이 문서에서는 Azure VM의 SQL Server에 적용되는 가장 일반적인 응용 프로그램 패턴에 대해 설명하고 Azure SQL 데이터베이스를 비롯한 하이브리드 시나리오도 다룹니다. </td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/hh680934(v=PandP.50).aspx">Microsoft Enterprise Library 일시적인 오류 처리 응용 프로그램 블록</p></td>
   <td valign="middle">이 라이브러리에서는 개발자가 강력한 일시적인 오류 처리 논리를 추가하여 더욱 복원력이 뛰어난 Azure SQL 데이터베이스에서 자신의 응용 프로그램을 실행해 볼 수 있습니다. 일시적인 오류는 네트워크 연결 문제 또는 서비스 사용 불가 등의 일시적인 조건으로 인해 발생하는 오류입니다. Azure SQL 데이터베이스는 다중 테넌트 서비스이므로 응용 프로그램 가동 중지 시간을 최소화하기 위해 이러한 오류를 처리해야 합니다. </td>   
</tr>
</table>

<!--Image references-->
[1]: ./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png
 

<!---HONumber=July15_HO4-->