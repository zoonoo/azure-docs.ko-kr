<properties 
   pageTitle="Azure Government 이미지 갤러리" 
   description="이 문서에서는 Azure Government 이미지 갤러리 및 포함된 이미지에 대한 개요를 제공합니다." 
   services="Azure-Government" 
   documentationCenter="" 
   authors="joharve2" 
   manager="chrisnie" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="azure-government" 
   ms.date="05/20/2015"
   ms.author="john.harvey@microsoft.com"/>


#  Microsoft Azure Government 이미지 갤러리

<p> Microsoft Azure Government 이미지 갤러리는 공용 Azure 포털과 유사한 환경입니다. 고객은 Microsoft와 파트너가 미리 작성한 이미지를 배포하거나 자신의 VHD를 업로드하도록 선택할 수 있습니다. 따라서 필요한 경우 사용자 고유의 표준화된 이미지를 배포할 수 있는 유연성이 제공됩니다.

다음은 Azure Government 이미지 갤러리 내에서 사용할 수 있는 이미지 목록입니다. 일부 미리 작성된 이미지에는 특정 소프트웨어에 대한 종량제 라이선스가 포함되어 있습니다. 자세한 지침은 <a href="http://azure.microsoft.com/pricing/details/virtual-machines/">가상 컴퓨터 가격 책정</a> 페이지를 검토하고, Azure Government와 관련된 가격은 Microsoft 계정 팀 또는 대리점에 문의하세요.


## Azure Government 이미지 목록

<table cellspacing="0" border="1">
<tr>
<th align="left" valign="middle">
게시자
</th>
<th align="left" valign="middle">
이미지 이름
</th>
<th align="left" valign="middle">
설명
</th>
<th>
OS
</tH>
<th align="left" valign="middle">
디스크 크기
</th>
</tr>

<tr><td>Barracuda Networks, Inc.</td><td>Barracuda Web Application Firewall (WAF) 7.8 PatchLevel 4</td><td>참고: 이 어플라이언스는 웹 UI를 통해 관리되며, 이를 위해 TCP/8000을 전달해야 합니다. 자세한 내용은 배포 README(https://cloudvm.cudasvc.com/azure/deployment-readme-waf.html)를 참조하세요.</td><td>Linux</td><td>50GB</td></tr>
<tr><td>Barracuda Networks, Inc.</td><td>Barracuda NG Firewall 5.4.3-182 PatchLevel 4</td><td>참고: 이 어플라이언스는 클라이언트 응용 프로그램을 통해 관리되며, 이를 위해 TCP/807을 전달해야 합니다. 자세한 내용은 배포 README(https://cloudvm.cudasvc.com/azure/deployment-readme-ng.html)를 참조하세요.</td><td>Linux</td><td>80GB</td></tr>
<tr><td>Bitnami</td><td>Drupal 7</td><td>Bitnami에서 제공하는 Drupal은 Microsoft Azure에서 Drupal을 실행하는 데 필요한 이미지를 실행할 수 있도록 미리 구성되어 있습니다. Drupal은 시중에서 가장 다양한 기능을 갖춘 오픈 소스 콘텐츠 관리 시스템 중 하나입니다. ...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>Bitnami</td><td>Drupal 6</td><td>Bitnami에서 제공하는 Drupal은 Microsoft Azure에서 Drupal을 실행하는 데 필요한 이미지를 실행할 수 있도록 미리 구성되어 있습니다. Drupal은 시중에서 가장 다양한 기능을 갖춘 오픈 소스 콘텐츠 관리 시스템 중 하나입니다. ...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>Bitnami</td><td>Ruby Stack 2.0</td><td>Bitnami RubyStack은 Ruby on Rails 응용 프로그램의 개발 및 배포를 크게 간소화합니다. Ruby on Rails는 데이터베이스 기반 웹 응용 프로그램을 위한 전체 스택 MVC 프레임워크로서...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>Bitnami</td><td>Nginx Stack 1.6</td><td>Bitnami Nginx Stack은 완전히 통합되고 즉시 실행 가능한 완벽한 PHP, MySQL 및 Nginx 개발 환경을 제공합니다. 또한 phpMyAdmin, SQLite, ImageMagick, FastCGI, Memcache, GD,...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>Bitnami</td><td>Drupal 8</td><td>Bitnami에서 제공하는 Drupal은 Microsoft Azure에서 Drupal을 실행하는 데 필요한 이미지를 실행할 수 있도록 미리 구성되어 있습니다. Drupal은 시중에서 가장 다양한 기능을 갖춘 오픈 소스 콘텐츠 관리 시스템 중 하나입니다. ...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>Bitnami</td><td>Ruby Stack 2.1</td><td>Bitnami RubyStack은 Ruby on Rails 응용 프로그램의 개발 및 배포를 크게 간소화합니다. Ruby on Rails는 데이터베이스 기반 웹 응용 프로그램을 위한 전체 스택 MVC 프레임워크로서...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>Bitnami</td><td>LAMP Stack 5.6</td><td>Bitnami LAMPStack은 PHP 응용 프로그램의 개발 및 배포를 크게 간소화합니다. 여기에는 즉시 실행 가능한 버전의 Apache, MySQL, PHP 및 phpMyAdmin이 포함되어 있으며, 다른 모든 소프트웨어...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>Bitnami</td><td>LAMP Stack 5.4</td><td>Bitnami LAMPStack은 PHP 응용 프로그램의 개발 및 배포를 크게 간소화합니다. 여기에는 즉시 실행 가능한 버전의 Apache, MySQL, PHP 및 phpMyAdmin이 포함되어 있으며, 다른 모든 소프트웨어...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>CloudLink</td><td>CloudLink SecureVM</td><td>CloudLink® SecureVM은 VM 이미지 무결성 유효성 검사와 함께 VM 볼륨 암호화 및 사전 부팅 권한 부여를 제공하여 VM 부팅 시기 및 위치를 제어할 수 있게 해줍니다. SecureVM은 네이티브 Windows B...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>Oracle Database 12c Standard Edition on Windows Server 2012</td><td>Oracle Database(http://www.oracle.com/database) 12c Standard Edition(12.1.0.1.0)은 중간 규모 기업에 이상적인 완전한 기능의 경제적인 데이터 관리 솔루션입니다. 최소...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>JDK 7 on Windows Server 2012</td><td>Java Platform(http://www.oracle.com/java) Standard Edition 7(업데이트 71)은 휴대할 수 있는 안전한 고성능 응용 프로그램 개발을 지원하며, JDK(Java Development Kit), Java...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>JDK 8 on Windows Server 2012 R2</td><td>Java Platform(http://www.oracle.com/java) Standard Edition 8(업데이트 25)은 휴대할 수 있는 안전한 고성능 응용 프로그램 개발을 지원하며, JDK(Java Development Kit), Java...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>Oracle Database 11g R2 Enterprise Edition on Windows Server 2008 R2</td><td>Oracle Database(http://www.oracle.com/database) 11g R2 Enterprise Edition(11.2.0.4.0)은 포괄적인 기능을 제공하여 가장 까다로운 트랜잭션 처리, 비즈니스...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>Oracle WebLogic Server 11g Standard Edition on Windows Server 2008 R2</td><td>Oracle WebLogic Server(http://www.oracle.com/weblogicserver) 11g Standard Edition(10.3.6)은 모든 규모의 기업에 적합한 업계 최고의 Java 응용 프로그램 서버로서, 개발자에게...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>Oracle WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2</td><td>Oracle WebLogic Server(http://www.oracle.com/weblogicserver) 11g Enterprise Edition(10.3.6)은 최신 데이터 센터에 적합한 업계 최고의 Java 응용 프로그램 서버입니다. 최신...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>Oracle WebLogic Server 12c Standard Edition on Windows Server 2012</td><td>Oracle WebLogic Server(http://www.oracle.com/weblogicserver) 12c Standard Edition(12.1.2.0)은 업계 최고의 Java EE 응용 프로그램 서버로서, 차세대 응용 프로그램...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>Oracle WebLogic Server 12c Enterprise Edition on Windows Server 2012</td><td>Oracle WebLogic Server(http://www.oracle.com/weblogicserver) 12c Enterprise Edition(12.1.2.0)은 선도적인 Java EE 응용 프로그램 서버로서, 차세대 응용 프로그램...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012</td><td>Oracle Database(http://www.oracle.com/database) 12c Standard Edition(12.1.0.1.0)은 중간 규모 기업에 이상적인 완전한 기능의 경제적인 데이터 관리 솔루션입니다. Oracle WebLogic...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012</td><td>Oracle Database(http://www.oracle.com/database) 12c Enterprise Edition(12.1.0.1.0)은 클라우드용으로 설계된 차세대 데이터베이스로, 새로운 다중 테넌트 아키텍처...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>JDK 6 on Windows Server 2012</td><td>Java Platform(http://www.oracle.com/java) Standard Edition 6(업데이트 85)은 휴대할 수 있는 안전한 고성능 응용 프로그램 개발을 지원하며, JDK(Java Development Kit), Java...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>Oracle Database 12c Enterprise Edition on Windows Server 2012</td><td>Oracle Database(http://www.oracle.com/database) 12c Enterprise Edition(12.1.0.1.0)은 클라우드용으로 설계된 차세대 데이터베이스로, 새로운 다중 테넌트 아키텍처...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>Oracle Database 11g R2 and WebLogic Server 11g Standard Edition on Windows Server 2008 R2</td><td>Oracle Database(http://www.oracle.com/database) 11g R2 Standard Edition(11.2.0.4.0)은 중간 규모 기업에 이상적인 완전한 기능의 경제적인 관리 솔루션입니다. Oracle WebLo...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>Oracle Database 11g R2 and WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2</td><td>Oracle Database(http://www.oracle.com/database) 11g R2 Enterprise Edition(11.2.0.4.0)은 포괄적인 기능을 제공하여 가장 까다로운 트랜잭션 처리, 비즈니스...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Open Technologies, Inc.</td><td>Oracle Database 11g R2 Standard Edition on Windows Server 2008 R2</td><td>Oracle Database(http://www.oracle.com/database) 11g R2 Standard Edition(11.2.0.4.0)은 중간 규모 기업에 이상적인 완전한 기능의 경제적인 관리 솔루션입니다. 최소...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft SQL Server 그룹</td><td>SQL Server 2014 RTM Enterprise on Windows Server 2012 R2</td><td>이 이미지에는 정식 버전의 SQL Server가 포함되어 있습니다. A3 이상의 가상 컴퓨터를 사용하는 것이 좋습니다. 이 이미지는 CEIP whic을 포함하여 Azure용으로 미리 구성되었으며...</td><td>Windows</td><td>127GB</td></tr>
<tr><td>Microsoft SQL Server 그룹</td><td>SQL Server 2012 SP2 Enterprise on Windows Server 2012</td><td>이 이미지에는 정식 버전의 SQL Server가 포함되어 있습니다. 일부 SQL Server 구성 요소는 사용하기 전에 추가 설치 및 구성이 필요합니다. A3 이상의 가상 컴퓨터 크기를 사용하는 것이 좋습니다.</td><td>Windows</td><td>127GB</td></tr>
<tr><td>Microsoft SQL Server 그룹</td><td>SQL Server 2014 RTM Enterprise on Windows Server 2012 R2</td><td>이 이미지에는 정식 버전의 SQL Server가 포함되어 있습니다. A3 이상의 가상 컴퓨터를 사용하는 것이 좋습니다. 이 이미지는 CEIP whic을 포함하여 Azure용으로 미리 구성되었으며...</td><td>Windows</td><td>127GB</td></tr>
<tr><td>Microsoft SQL Server 그룹</td><td>SQL Server 2014 RTM Standard on Windows Server 2012 R2</td><td>이 이미지에는 정식 버전의 SQL Server가 포함되어 있습니다. A2 이상의 가상 컴퓨터를 사용하는 것이 좋습니다. 이 이미지는 CEIP whic을 포함하여 Azure용으로 미리 구성되었으며...</td><td>Windows</td><td>127GB</td></tr>
<tr><td>Microsoft SQL Server 그룹</td><td>SQL Server 2012 SP2 Standard on Windows Server 2012</td><td>일부 SQL Server 구성 요소는 사용하기 전에 추가 설치 및 구성이 필요합니다. A2 이상의 가상 컴퓨터를 사용하는 것이 좋습니다. 이 이미지는 Azure용으로 미리 구성되었으며...</td><td>Windows</td><td>127GB</td></tr>
<tr><td>Microsoft SQL Server 그룹</td><td>SQL Server 2014 RTM Standard on Windows Server 2012 R2</td><td>이 이미지에는 정식 버전의 SQL Server가 포함되어 있습니다. A2 이상의 가상 컴퓨터를 사용하는 것이 좋습니다. 이 이미지는 CEIP whic을 포함하여 Azure용으로 미리 구성되었으며...</td><td>Windows</td><td>127GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2012 R2 Datacenter, June 2014</td><td>Microsoft 클라우드 OS 비전의 핵심인 Windows Server 2012 R2는 고객의 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 경험을 선사합니다. 엔터프라이즈급 성능을 제공하며...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2012 Datacenter, June 2014</td><td>Windows Server 2012는 공용 클라우드 구축 및 운영에 대한 Microsoft의 경험을 바탕으로 항상 사용 가능한 동적 서버 플랫폼을 제공합니다. 확장 가능한 동적 다중 테넌트...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2008 R2 SP1, June 2014</td><td>Windows Server 2008 R2는 서버 또는 사설 클라우드 인프라의 안정성 및 유연성을 개선하여 시간을 절약하고 비용을 절감할 수 있도록 도와주는 다용도 서버입니다. 이 서버는...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2008 R2 SP1, September 2014</td><td>Windows Server 2008 R2는 서버 또는 사설 클라우드 인프라의 안정성 및 유연성을 개선하여 시간을 절약하고 비용을 절감할 수 있도록 도와주는 다용도 서버입니다. 이 서버는...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2008 R2 SP1, October 2014</td><td>Windows Server 2008 R2는 서버 또는 사설 클라우드 인프라의 안정성 및 유연성을 개선하여 시간을 절약하고 비용을 절감할 수 있도록 도와주는 다용도 서버입니다. 이 서버는...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2008 R2 SP1, November 2014</td><td>Windows Server 2008 R2는 서버 또는 사설 클라우드 인프라의 안정성 및 유연성을 개선하여 시간을 절약하고 비용을 절감할 수 있도록 도와주는 다용도 서버입니다. 이 서버는...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2008 R2 SP1, April 2015</td><td>Windows Server 2008 R2는 서버 또는 사설 클라우드 인프라의 안정성 및 유연성을 개선하여 시간을 절약하고 비용을 절감할 수 있도록 도와주는 다용도 서버입니다. 이 서버는...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2012 Datacenter, September 2014</td><td>Windows Server 2012는 공용 클라우드 구축 및 운영에 대한 Microsoft의 경험을 바탕으로 항상 사용 가능한 동적 서버 플랫폼을 제공합니다. 확장 가능한 동적 다중 테넌트...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2012 Datacenter, November 2014</td><td>Windows Server 2012는 공용 클라우드 구축 및 운영에 대한 Microsoft의 경험을 바탕으로 항상 사용 가능한 동적 서버 플랫폼을 제공합니다. 확장 가능한 동적 다중 테넌트...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2012 Datacenter, October 2014</td><td>Windows Server 2012는 공용 클라우드 구축 및 운영에 대한 Microsoft의 경험을 바탕으로 항상 사용 가능한 동적 서버 플랫폼을 제공합니다. 확장 가능한 동적 다중 테넌트...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2012 R2 Datacenter, September 2014</td><td>Microsoft 클라우드 OS 비전의 핵심인 Windows Server 2012 R2는 고객의 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 경험을 선사합니다. 엔터프라이즈급 성능을 제공하며...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2012 R2 Datacenter, October 2014</td><td>Microsoft 클라우드 OS 비전의 핵심인 Windows Server 2012 R2는 고객의 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 경험을 선사합니다. 엔터프라이즈급 성능을 제공하며...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2012 R2 Datacenter, November 2014</td><td>Microsoft 클라우드 OS 비전의 핵심인 Windows Server 2012 R2는 고객의 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 경험을 선사합니다. 엔터프라이즈급 성능을 제공하며...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2012 R2 Datacenter, April 2015</td><td>Microsoft 클라우드 OS 비전의 핵심인 Windows Server 2012 R2는 고객의 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 경험을 선사합니다. 엔터프라이즈급 성능을 제공하며...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server Technical Preview</td><td>Microsoft 클라우드 플랫폼의 핵심인 Windows Server는 고객의 데이터 센터 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 수준 높고 풍부한 경험을 선사합니다. Windows S...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Microsoft Windows Server 그룹</td><td>Windows Server 2012 Datacenter, April 2015</td><td>Windows Server 2012는 공용 클라우드 구축 및 운영에 대한 Microsoft의 경험을 바탕으로 항상 사용 가능한 동적 서버 플랫폼을 제공합니다. 확장 가능한 동적 다중 테넌트...</td><td>Windows</td><td>128GB</td></tr>
<tr><td>Oracle</td><td>Oracle WebLogic Server 12.1.2 on Oracle Linux 6.4.0.0.0</td><td>Oracle WebLogic Server 12c Enterprise Edition은 선도적인 Java EE 응용 프로그램 서버로서, 중요 업무용 클라우드 플랫폼에서 네이티브 클라우드 관리를 사용하여 차세대 응용 프로그램...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>Oracle</td><td>Oracle Linux 7.0.0.0.0</td><td>Oracle Linux 7.0.0.0은 엔터프라이즈 응용 프로그램 및 시스템에 뛰어난 성능, 고급 확장성 및 안정성을 제공합니다. 엔터프라이즈 작업 부하에 최적화된 Oracle Linux는...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>Oracle</td><td>Oracle Database 12.1.0.1 Enterprise Edition on Oracle Linux 6.4.0.0.0</td><td>Oracle Database 12c Enterprise Edition은 클라우드용으로 설계된 차세대 데이터베이스로, 빠르고 확장 가능하며 안정적인 보안 데이터베이스 플랫폼을 기반으로 하는 새로운 다중 테넌트 아키텍처...</td><td>Linux</td><td>40GB</td></tr>
<tr><td>Oracle</td><td>Oracle Database 12.1.0.1 Standard Edition on Oracle Linux 6.4.0.0.0</td><td>Oracle Database 12c Standard Edition은 중간 규모 기업에 이상적인 완전한 기능의 경제적인 관리 솔루션입니다. 자세한 내용은 http://www.oracle.com/database에서 확인할 수 있습니다. </td><td>Linux</td><td>40GB</td></tr>
<tr><td>SUSE</td><td>SUSE Linux Enterprise Server 11 SP3</td><td>Microsoft Azure의 SUSE Linux Enterprise Server에서 프로덕션 작업 부하를 안심하고 실행하세요. 서비스 수준이 보장되며 SUSE 및 Microsoft 엔지니어의 도움을...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>SUSE</td><td>SUSE Linux Enterprise Server 11 SP3(Premium 이미지)</td><td>Microsoft Azure의 SUSE Linux Enterprise Server에서 프로덕션 작업 부하를 안심하고 실행하세요. 서비스 수준이 보장되며 SUSE 및 Microsoft 엔지니어의 도움을...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>SUSE</td><td>SUSE Linux Enterprise Server 12(Premium 이미지)</td><td>Microsoft Azure의 SUSE Linux Enterprise Server에서 프로덕션 작업 부하를 안심하고 실행하세요. 서비스 수준이 보장되며 SUSE 및 Microsoft 엔지니어의 도움을...</td><td>Linux</td><td>30GB</td></tr>
<tr><td>SUSE</td><td>SUSE Linux Enterprise Server 12</td><td>Microsoft Azure의 SUSE Linux Enterprise Server에서 프로덕션 작업 부하를 안심하고 실행하세요. 서비스 수준이 보장되며 SUSE 및 Microsoft 엔지니어의 도움을...</td><td>Linux</td><td>30GB</td></tr>
</table>


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>다음 단계

다음은 이미지 갤러리에서 배포하거나 사용자 고유의 VHD를 만드는 방법에 대한 자세한 정보를 제공하는 리소스입니다. Azure Government로 작업할 때 끝점 간의 프로그래밍 차이점은 <a href="../azure-government-developer-guide">Azure Government 개발자 가이드</a>를 참조하세요.

###기타 리소스: 

- <a href="virtual-machines/virtual-machines-windows-tutorial.md">Windows 가상 컴퓨터 배포</a>

- <a href="../virtual-machines-linux-tutorial/">Linux 가상 컴퓨터 배포</a>

- <a href="http://msdn.microsoft.com/library/azure/dn683781.aspx">가상 컴퓨터 FAQ</A>

- <a href="../virtual-machines-create-upload-vhd-windows-server/">사용자 고유의 VHD 만들기 및 업로드</a>

- <a href="../virtual-machines-linux-capture-image/">Linux VM 이미지 캡처</a>

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines/virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=July15_HO3-->