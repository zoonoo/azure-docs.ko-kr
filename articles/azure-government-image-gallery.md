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
   ms.date="06/01/2016"
   ms.author="brendalee"/>


#  Microsoft Azure Government 이미지 갤러리

<p> Microsoft Azure Government 이미지 갤러리는 공용 Azure 포털과 유사한 환경입니다. 고객은 Microsoft와 파트너가 미리 작성한 이미지를 배포하거나 자신의 VHD를 업로드하도록 선택할 수 있습니다. 따라서 필요한 경우 사용자 고유의 표준화된 이미지를 배포할 수 있는 유연성이 제공됩니다.

다음은 Azure Government 이미지 갤러리 내에서 사용할 수 있는 이미지 목록입니다. 일부 미리 작성된 이미지에는 특정 소프트웨어에 대한 종량제 라이선스가 포함되어 있습니다. 자세한 지침은 <a href="http://azure.microsoft.com/pricing/details/virtual-machines/">가상 컴퓨터 가격 책정</a> 페이지를 검토하고, Azure Government와 관련된 가격은 Microsoft 계정 팀 또는 대리점에 문의하세요.


## Azure Government 이미지 목록

게시자|이미지 이름|설명|OS|디스크 크기
---|---|---|---|---
Barracuda Networks, Inc.|Barracuda NextGen Firewall F-Series 6.2.1-057|참고: 이 어플라이언스는 클라이언트 응용 프로그램을 통해 관리되며, 이를 위해 TCP/807을 전달해야 합니다. 배포 추가 정보 https://techlib.barracuda.com/NG61/DeployAzure...|Linux|80GB를 참조하세요.
Barracuda Networks, Inc.|Barracuda Web Application Firewall 8.0.1.008-20160405|참고: 이 어플라이언스는 웹 UI를 통해 관리되며, 이를 위해 TCP/8000을 전달해야 합니다. 자세한 내용은 배포 추가 정보 https://techlib.barracuda.com/WAF/Azure를 참조하세요. Barracuda 웹...|Linux|50GB
Bitnami|Nginx Stack 1.6|Bitnami Nginx Stack은 완전히 통합되고 즉시 실행 가능한 완벽한 PHP, MySQL 및 Nginx 개발 환경을 제공합니다. 또한 phpMyAdmin, SQLite, ImageMagick, FastCGI, Memcache, GD,...|Linux|30GB
Bitnami|Ruby Stack 2.0|Bitnami RubyStack은 Ruby on Rails 응용 프로그램의 개발 및 배포를 크게 간소화합니다. Ruby on Rails는 데이터베이스 기반 웹 응용 프로그램을 위한 전체 스택 MVC 프레임워크로서...|Linux|30GB
Bitnami|LAMP Stack 5.4|Bitnami LAMPStack은 PHP 응용 프로그램의 개발 및 배포를 크게 간소화합니다. 여기에는 즉시 실행 가능한 버전의 Apache, MySQL, PHP 및 phpMyAdmin이 포함되어 있으며, 다른 모든 소프트웨어...|Linux|30GB
Bitnami|Drupal 8|Bitnami에서 제공하는 Drupal은 Microsoft Azure에서 Drupal을 실행하는 데 필요한 이미지를 실행할 수 있도록 미리 구성되어 있습니다. Drupal은 시중에서 가장 다양한 기능을 갖춘 오픈 소스 콘텐츠 관리 시스템 중 하나입니다. ...|Linux|30GB
Bitnami|Drupal 6|Bitnami에서 제공하는 Drupal은 Microsoft Azure에서 Drupal을 실행하는 데 필요한 이미지를 실행할 수 있도록 미리 구성되어 있습니다. Drupal은 시중에서 가장 다양한 기능을 갖춘 오픈 소스 콘텐츠 관리 시스템 중 하나입니다. ...|Linux|30GB
Bitnami|LAMP Stack 5.6|Bitnami LAMPStack은 PHP 응용 프로그램의 개발 및 배포를 크게 간소화합니다. 여기에는 즉시 실행 가능한 버전의 Apache, MySQL, PHP 및 phpMyAdmin이 포함되어 있으며, 다른 모든 소프트웨어...|Linux|30GB
Bitnami|Ruby Stack 2.1|Bitnami RubyStack은 Ruby on Rails 응용 프로그램의 개발 및 배포를 크게 간소화합니다. Ruby on Rails는 데이터베이스 기반 웹 응용 프로그램을 위한 전체 스택 MVC 프레임워크로서...|Linux|30GB
Bitnami|Drupal 7|Bitnami에서 제공하는 Drupal은 Microsoft Azure에서 Drupal을 실행하는 데 필요한 이미지를 실행할 수 있도록 미리 구성되어 있습니다. Drupal은 시중에서 가장 다양한 기능을 갖춘 오픈 소스 콘텐츠 관리 시스템 중 하나입니다. ...|Linux|30GB
Canonical|Ubuntu Server 12.04.5-LTS|Microsoft Azure용 Ubuntu Server 12.04.5-LTS(amd64 20160315). Ubuntu Server는 클라우드 환경에 세계적으로 가장 많이 사용되는 Linux입니다. Ubuntu 12.04.5-LTS에 대한 업데이트 및 패치는...|Linux|30GB
Canonical|Ubuntu Server 16.04 LTS|Microsoft Azure용 Ubuntu Server 16.04 LTS(amd64 20160420.3). Ubuntu Server는 클라우드 환경에 세계적으로 가장 많이 사용되는 Linux입니다. Ubuntu 16.04 LTS에 대한 업데이트 및 패치는...|Linux|30GB
Canonical|Ubuntu Server 14.04.4-LTS|Microsoft Azure용 Ubuntu Server 14.04.4-LTS(amd64 20160314). Ubuntu Server는 클라우드 환경에 세계적으로 가장 많이 사용되는 Linux입니다. Ubuntu 14.04.4-LTS에 대한 업데이트 및 패치는...|Linux|30GB
Check Point|Check Point vSec, R77.30-015052|Azure용 Check Point vSEC는 차세대 병화벽 및 보안 게이트웨이를 제공합니다. |Linux|50GB
CloudLink|CloudLink SecureVM 5.0 BYOL|간편하고 손쉽게 VM을 제어, 모니터링, 암호화합니다. EMC의 CloudLink SecureVM은 정책 기반 키 관리를 제공하여 네이티브 Windows BitLocker 및 Linux OS 암호화 기능의 잠금을 해제합니다.|Linux|30GB
CloudLink|CloudLink SecureVM|CloudLink® SecureVM은 VM 이미지 무결성 유효성 검사와 함께 VM 볼륨 암호화 및 사전 부팅 권한 부여를 제공하여 VM 부팅 시기 및 위치를 제어할 수 있게 해줍니다. SecureVM은 네이티브 Windows B...|Linux|30GB
coreos|CoreOS Stable|프로덕션 클러스터에 안정적인 채널이 사용되어야 합니다. CoreOS의 버전은 승격 전에 알파 및 베타 채널 내에서 입증됩니다. |Linux|31GB
credativ|Debian 7 Wheezy|Microsoft Azure용 Debian 7 Wheezy(20160405.0). Debian GNU/Linux는 가장 많이 사용되는 Linux 배포 중 하나입니다. |Linux|30GB
credativ|Debian 8 Jessie|Microsoft Azure용 Debian 8 Jessie(20160405.0). Debian GNU/Linux는 가장 많이 사용되는 Linux 배포 중 하나입니다. |Linux|30GB
Esri|ArcGIS 10.3.1 for Server (Windows)|ArcGIS for Server는 세계 최고의 다용도 GIS 서버이며, 조직의 지리 공간적 자산 및 데이터를 완벽히 제어할 수 있도록 해 줍니다. |Windows|128GB
Microsoft 하이브리드 클라우드 저장소 그룹|StorSimple 가상 어플라이언스 1100, 2015년 10월|Microsoft StorSimple 가상 어플라이언스 1100 |Windows|32GB
Microsoft 하이브리드 클라우드 저장소 그룹|StorSimple 가상 어플라이언스 1100, 2015년 5월|Microsoft StorSimple 가상 어플라이언스 1100 |Windows|32GB
Microsoft 하이브리드 클라우드 저장소 그룹|StorSimple 가상 어플라이언스 1100, 2015년 7월|Microsoft StorSimple 가상 어플라이언스 1100 |Windows|32GB
Microsoft 하이브리드 클라우드 저장소 그룹|StorSimple 가상 어플라이언스 GU2, 2016년 2월|Microsoft StorSimple 가상 어플라이언스 GU2 |Windows|32GB
Microsoft Open Technologies, Inc.|Oracle Database 11g R2 Standard Edition on Windows Server 2008 R2|Oracle Database http://www.oracle.com/database 11g R2 Standard Edition(11.2.0.4.0)은 중간 규모 기업에 이상적인 완전한 기능의 경제적인 관리 솔루션입니다. 최소...|Windows|128GB
Microsoft Open Technologies, Inc.|Oracle Database 11g R2 and WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2|Oracle Database http://www.oracle.com/database 11g R2 Enterprise Edition(11.2.0.4.0)은 포괄적인 기능을 제공하여 가장 까다로운 트랜잭션 처리, 비즈니스...|Windows|128GB
Microsoft Open Technologies, Inc.|Oracle Database 12c Enterprise Edition on Windows Server 2012|Oracle Database http://www.oracle.com/database 12c Enterprise Edition(12.1.0.1.0)은 클라우드용으로 설계된 차세대 데이터베이스로, 새로운 다중 테넌트 아키텍처...|Windows|128GB
Microsoft Open Technologies, Inc.|Oracle Database 11g R2 and WebLogic Server 11g Standard Edition on Windows Server 2008 R2|Oracle Database http://www.oracle.com/database 11g R2 Standard Edition(11.2.0.4.0)은 중간 규모 기업에 이상적인 완전한 기능의 경제적인 관리 솔루션입니다. Oracle WebLo...|Windows|128GB
Microsoft Open Technologies, Inc.|JDK 8 on Windows Server 2012 R2|Java Platform http://www.oracle.com/java, Standard Edition 8(업데이트 25)은 휴대할 수 있는 안전한 고성능 응용 프로그램 개발을 지원하며, JDK(Java Development Kit), Java...|Windows|128GB
Microsoft Open Technologies, Inc.|Oracle Database 11g R2 Enterprise Edition on Windows Server 2008 R2|Oracle Database http://www.oracle.com/database 11g R2 Enterprise Edition(11.2.0.4.0)은 포괄적인 기능을 제공하여 가장 까다로운 트랜잭션 처리, 비즈니스...|Windows|128GB
Microsoft Open Technologies, Inc.|JDK 6 on Windows Server 2012|Java Platform http://www.oracle.com/java, Standard Edition 6(업데이트 85)은 휴대할 수 있는 안전한 고성능 응용 프로그램 개발을 지원하며, JDK(Java Development Kit), Java...|Windows|128GB
Microsoft Open Technologies, Inc.|JDK 7 on Windows Server 2012|Java Platform http://www.oracle.com/java, Standard Edition 7(업데이트 71)은 휴대할 수 있는 안전한 고성능 응용 프로그램 개발을 지원하며, JDK(Java Development Kit), Java...|Windows|128GB
Microsoft Open Technologies, Inc.|Oracle Database 12c Standard Edition on Windows Server 2012|Oracle Database http://www.oracle.com/database 12c Standard Edition(12.1.0.1.0)은 중간 규모 기업에 이상적인 완전한 기능의 경제적인 관리 솔루션입니다. 최소...|Windows|128GB
Microsoft Open Technologies, Inc.|Oracle WebLogic Server 12c Standard Edition on Windows Server 2012|Oracle WebLogic Server http://www.oracle.com/weblogicserver 12c Standard Edition(12.1.2.0)은 업계 최고의 Java EE 응용 프로그램 서버로서, 차세대 응용 프로그램...|Windows|128GB
Microsoft Open Technologies, Inc.|Oracle WebLogic Server 12c Enterprise Edition on Windows Server 2012|Oracle WebLogic Server http://www.oracle.com/weblogicserver 12c Enterprise Edition(12.1.2.0)은 선도적인 Java EE 응용 프로그램 서버로서, 차세대 응용 프로그램...|Windows|128GB
Microsoft Open Technologies, Inc.|Oracle WebLogic Server 11g Standard Edition on Windows Server 2008 R2|Oracle WebLogic Server http://www.oracle.com/weblogicserver 11g Standard Edition(10.3.6)은 모든 규모의 기업에 적합한 업계 최고의 Java 응용 프로그램 서버로서, 개발자에게...|Windows|128GB
Microsoft Open Technologies, Inc.|Oracle WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2|Oracle WebLogic Server http://www.oracle.com/weblogicserver 11g Enterprise Edition(10.3.6)은 최신 데이터 센터에 적합한 업계 최고의 Java 응용 프로그램 서버입니다. 최신...|Windows|128GB
Microsoft Open Technologies, Inc.|Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012|Oracle Database http://www.oracle.com/database 12c Standard Edition(12.1.0.1.0)은 중간 규모 기업에 이상적인 완전한 기능의 경제적인 관리 솔루션입니다. Oracle WebLogic...|Windows|128GB
Microsoft Open Technologies, Inc.|Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012|Oracle Database http://www.oracle.com/database 12c Enterprise Edition(12.1.0.1.0)은 클라우드용으로 설계된 차세대 데이터베이스로, 새로운 다중 테넌트 아키텍처...|Windows|128GB
Microsoft SQL Server 그룹|SQL Server 2012 SP2 Enterprise on Windows Server 2012|이 이미지에는 정식 버전의 SQL Server가 포함되어 있습니다. 일부 SQL Server 구성 요소는 사용하기 전에 추가 설치 및 구성이 필요합니다. A3 이상의 가상 컴퓨터 크기를 사용하는 것이 좋습니다.|Windows|127GB
Microsoft SQL Server 그룹|SQL Server 2014 RTM Enterprise on Windows Server 2012 R2|이 이미지에는 정식 버전의 SQL Server가 포함되어 있습니다. A3 이상의 가상 컴퓨터를 사용하는 것이 좋습니다. 이 이미지는 Microsoft Azure용으로 미리 구성되었으며...|Windows|127GB
Microsoft SQL Server 그룹|SQL Server 2012 SP2 Standard on Windows Server 2012|일부 SQL Server 구성 요소는 사용하기 전에 추가 설치 및 구성이 필요합니다. A2 이상의 가상 컴퓨터를 사용하는 것이 좋습니다. 이 이미지는 Microsoft Azure용으로 미리 구성되었으며...|Windows|127GB
Microsoft SQL Server 그룹|SQL Server 2014 RTM Standard on Windows Server 2012 R2|이 이미지에는 정식 버전의 SQL Server가 포함되어 있습니다. A2 이상의 가상 컴퓨터를 사용하는 것이 좋습니다. 이 이미지는 Microsoft Azure용으로 미리 구성되었으며...|Windows|127GB
Microsoft SQL Server 그룹|SQL Server 2014 RTM Enterprise on Windows Server 2012 R2|이 이미지에는 정식 버전의 SQL Server가 포함되어 있습니다. A3 이상의 가상 컴퓨터를 사용하는 것이 좋습니다. 이 이미지는 Microsoft Azure용으로 미리 구성되었으며...|Windows|127GB
Microsoft SQL Server 그룹|SQL Server 2014 RTM Standard on Windows Server 2012 R2|이 이미지에는 정식 버전의 SQL Server가 포함되어 있습니다. A2 이상의 가상 컴퓨터를 사용하는 것이 좋습니다. 이 이미지는 Microsoft Azure용으로 미리 구성되었으며...|Windows|127GB
Microsoft Windows Server 그룹|Windows Server 2016 Technical Preview 4|Microsoft 클라우드 OS 비전의 핵심인 Windows Server는 고객의 데이터 센터 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 수준 높고 풍부한 경험을 선사합니다. Windows...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2016 Core with Containers Tech Preview 4|서버는 세계적인 규모의 클라우드 서비스를 제공하는 Microsoft의 폭넓고 다양한 경험을 고객의 데이터 센터 인프라에 선사합니다. Windows Server 2016 Core with Containers Technical Preview 4는...|Windows|127GB
Microsoft Windows Server 그룹|Windows Server 2012 Datacenter, 2015년 12월|Windows Server 2012는 공용 클라우드 구축 및 운영에 대한 Microsoft의 경험을 바탕으로 항상 사용 가능한 동적 서버 플랫폼을 제공합니다. 확장 가능한 동적 다중 테넌트...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2012 Datacenter, 2016년 1월|Windows Server 2012는 공용 클라우드 구축 및 운영에 대한 Microsoft의 경험을 바탕으로 항상 사용 가능한 동적 서버 플랫폼을 제공합니다. 확장 가능한 동적 다중 테넌트...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2012 Datacenter, 2015년 11월|Windows Server 2012는 공용 클라우드 구축 및 운영에 대한 Microsoft의 경험을 바탕으로 항상 사용 가능한 동적 서버 플랫폼을 제공합니다. 확장 가능한 동적 다중 테넌트...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2008 R2 SP1, 2016년 4월|Windows Server 2008 R2는 서버 또는 사설 클라우드 인프라의 안정성 및 유연성을 개선하여 시간을 절약하고 비용을 절감할 수 있도록 도와주는 다용도 서버입니다. 이 서버는...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2012 R2 Datacenter, 2015년 11월|Microsoft 클라우드 OS 비전의 핵심인 Windows Server 2012 R2는 고객의 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 경험을 선사합니다. 엔터프라이즈급 성능을 제공하며...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2012 Datacenter, 2016년 4월|Windows Server 2012는 공용 클라우드 구축 및 운영에 대한 Microsoft의 경험을 바탕으로 항상 사용 가능한 동적 서버 플랫폼을 제공합니다. 확장 가능한 동적 다중 테넌트...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2012 Datacenter, 2016년 2월|Windows Server 2012는 공용 클라우드 구축 및 운영에 대한 Microsoft의 경험을 바탕으로 항상 사용 가능한 동적 서버 플랫폼을 제공합니다. 확장 가능한 동적 다중 테넌트...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2008 R2 SP1, 2015년 11월|Windows Server 2008 R2는 서버 또는 사설 클라우드 인프라의 안정성 및 유연성을 개선하여 시간을 절약하고 비용을 절감할 수 있도록 도와주는 다용도 서버입니다. 이 서버는...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server Nano 2016 Tech Preview 4|Nano Server는 Windows Server의 새로운 헤드리스 배포 옵션으로, 전체 운영 체제 크기의 소량으로 실행됩니다. 이 Technical Preview를 통해 Nano Server를 시도해 보고...|Windows|8GB
Microsoft Windows Server 그룹|Windows Server 2016 Technical Preview 4|Microsoft 클라우드 OS 비전의 핵심인 Windows Server는 고객의 데이터 센터 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 수준 높고 풍부한 경험을 선사합니다. Windows...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2008 R2 SP1, 2016년 2월|Windows Server 2008 R2는 서버 또는 사설 클라우드 인프라의 안정성 및 유연성을 개선하여 시간을 절약하고 비용을 절감할 수 있도록 도와주는 다용도 서버입니다. 이 서버는...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2008 R2 SP1, 2016년 1월|Windows Server 2008 R2는 서버 또는 사설 클라우드 인프라의 안정성 및 유연성을 개선하여 시간을 절약하고 비용을 절감할 수 있도록 도와주는 다용도 서버입니다. 이 서버는...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2008 R2 SP1, 2015년 12월|Windows Server 2008 R2는 서버 또는 사설 클라우드 인프라의 안정성 및 유연성을 개선하여 시간을 절약하고 비용을 절감할 수 있도록 도와주는 다용도 서버입니다. 이 서버는...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2012 R2 Datacenter, 2016년 2월|Microsoft 클라우드 OS 비전의 핵심인 Windows Server 2012 R2는 고객의 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 경험을 선사합니다. 엔터프라이즈급 성능을 제공하며...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2012 R2 Datacenter, 2016년 1월|Microsoft 클라우드 OS 비전의 핵심인 Windows Server 2012 R2는 고객의 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 경험을 선사합니다. 엔터프라이즈급 성능을 제공하며...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2012 R2 Datacenter, 2015년 12월|Microsoft 클라우드 OS 비전의 핵심인 Windows Server 2012 R2는 고객의 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 경험을 선사합니다. 엔터프라이즈급 성능을 제공하며...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2012 R2 Datacenter, 2016년 4월|Microsoft 클라우드 OS 비전의 핵심인 Windows Server 2012 R2는 고객의 인프라에 세계적인 규모의 클라우드 서비스 제공에 대한 Microsoft의 경험을 선사합니다. 엔터프라이즈급 성능을 제공하며...|Windows|128GB
Microsoft Windows Server 그룹|Windows Server 2016 Technical Preview 5 - Nano Server|Nano Server는 Windows Server의 새로운 헤드리스 배포 옵션으로, 전체 운영 체제 크기의 소량으로 실행됩니다. 이 Technical Preview를 통해 Nano Server를 시도해 보고...|Windows|8GB
OpenLogic|OpenLogic 7.2|Linux의 이 배포판은 CentOS 버전 7.2를 기반으로 하며, OpenLogic에 의해 제공됩니다. 기본 서버 설치 패키지를 포함합니다. |Linux|30GB
OpenLogic|OpenLogic 6.7|Linux의 이 배포판은 CentOS 버전 6.7을 기반으로 하며, OpenLogic에 의해 제공됩니다. 기본 서버 설치 패키지를 포함합니다. |Linux|30GB
Oracle|Oracle Linux 7.0.0.0.0|Oracle Linux 7.0.0.0은 엔터프라이즈 응용 프로그램 및 시스템에 뛰어난 성능, 고급 확장성 및 안정성을 제공합니다. 엔터프라이즈 작업 부하에 최적화된 Oracle Linux는...|Linux|30GB
Oracle|Oracle Database 12.1.0.1 Enterprise Edition on Oracle Linux 6.4.0.0.0|Oracle Database 12c Enterprise Edition은 클라우드용으로 설계된 차세대 데이터베이스로, 빠르고 확장 가능하며 안정적인 보안 데이터베이스 플랫폼을 기반으로 하는 새로운 다중 테넌트 아키텍처...|Linux|40GB
Oracle|Oracle WebLogic Server 12.1.2 on Oracle Linux 6.4.0.0.0|Oracle WebLogic Server 12c Enterprise Edition은 선도적인 Java EE 응용 프로그램 서버로서, 중요 업무용 클라우드 플랫폼에서 네이티브 클라우드 관리를 사용하여 차세대 응용 프로그램...|Linux|30GB
Oracle|Oracle Database 12.1.0.1 Standard Edition on Oracle Linux 6.4.0.0.0|Oracle Database 12c Standard Edition은 중간 규모 기업에 이상적인 완전한 기능의 경제적인 관리 솔루션입니다. 자세한 내용은 http://www.oracle.com/database을(를) 참조하세요. |Linux|40GB
원격 데스크톱 호스트|Microsoft Office 365 ProPlus가 있는 Windows 서버 원격 데스크톱 세션 호스트|이 이미지에는 Microsoft Office 365 ProPlus와 함께 RD 세션 호스트(원격 데스크톱 세션 호스트) 역할이 설치된 Windows Server 2012 R2 운영 체제가 포함되어 있습니다. 여기에는 ...|Windows|127GB
원격 데스크톱 호스트|Windows Server 2012 R2의 Windows Server 원격 데스크톱 세션 호스트|이 이미지에는 RD 세션 호스트(원격 데스크톱 세션 호스트) 역할이 설치된 Windows Server 2012 R2 운영 체제가 포함되어 있습니다. 이 이미지는 Microsoft Azure용으로 미리 구성되었습니다. RD 세션...|Windows|127GB
SharePoint|SharePoint Server 2016 평가판|Windows Server 2012 R2 Datacenter의 Microsoft SharePoint Server 2016 평가판입니다. 이 평가판 이미지를 사용하여 만든 가상 컴퓨터는 2016년 9월 19일에 만료됩니다. 이 이미지에는 전체 설치...|Windows|128GB
SUSE|SUSE Linux Enterprise Server 12(Premium 이미지)|Microsoft Azure의 SUSE Linux Enterprise Server에서 프로덕션 작업 부하를 안심하고 실행하세요. 서비스 수준이 보장되며 SUSE 및 Microsoft 엔지니어의 도움을...|Linux|30GB
SUSE|SUSE Linux Enterprise Server 12 SP1|Microsoft Azure의 SUSE Linux Enterprise Server에서 프로덕션 작업 부하를 안심하고 실행하세요. 서비스 수준이 보장되며 SUSE 및 Microsoft 엔지니어의 도움을...|Linux|30GB
SUSE|SUSE Linux Enterprise Server 12 SP1|Microsoft Azure의 SUSE Linux Enterprise Server에서 프로덕션 작업 부하를 안심하고 실행하세요. 서비스 수준이 보장되며 SUSE 및 Microsoft 엔지니어의 도움을...|Linux|30GB
SUSE|SUSE Linux Enterprise Server 11 SP4|Microsoft Azure의 SUSE Linux Enterprise Server에서 프로덕션 작업 부하를 안심하고 실행하세요. 서비스 수준이 보장되며 SUSE 및 Microsoft 엔지니어의 도움을...|Linux|30GB
SUSE|SUSE Linux Enterprise Server 11 SP4|Microsoft Azure의 SUSE Linux Enterprise Server에서 프로덕션 작업 부하를 안심하고 실행하세요. 서비스 수준이 보장되며 SUSE 및 Microsoft 엔지니어의 도움을...|Linux|30GB
SUSE|SUSE Linux Enterprise Server 12|Microsoft Azure의 SUSE Linux Enterprise Server에서 프로덕션 작업 부하를 안심하고 실행하세요. 서비스 수준이 보장되며 SUSE 및 Microsoft 엔지니어의 도움을...|Linux|30GB
Visual Studio|Windows Server 2012 R2의 Azure SDK 2.9가 있는 Visual Studio Enterprise 2015 업데이트 2|Visual Studio Enterprise 2015 업데이트 2를 사용하면 일관된 개발 환경의 단일 솔루션을 사용하여 여러 장치 및 서비스에서 응용 프로그램을 만들 수 있습니다. 도구를 사용하여...|Windows|128GB



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>다음 단계

다음은 이미지 갤러리에서 배포하거나 사용자 고유의 VHD를 만드는 방법에 대한 자세한 정보를 제공하는 리소스입니다. Azure Government로 작업할 때 끝점 간의 프로그래밍 차이점은 <a href="../azure-government-developer-guide">Azure Government 개발자 가이드</a>를 참조하세요.

###기타 리소스: 

- [Windows 가상 컴퓨터 배포](virtual-machines/virtual-machines-windows-hero-tutorial.md)

- [가상 컴퓨터 FAQ](http://msdn.microsoft.com/library/azure/dn683781.aspx)

- [Linux VM 이미지 캡처](virtual-machines/virtual-machines-linux-classic-capture-image.md)

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines/virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=AcomDC_0608_2016-->