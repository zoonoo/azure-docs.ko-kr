---
title: "Azure 스택 사용할 수 있는 azure 마켓플레이스 항목 | Microsoft Docs"
description: "Azure 스택에 이러한 Azure 마켓플레이스 항목을 사용할 수 있습니다."
services: azure-stack
documentationcenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2018
ms.author: JeffGoldner
ms.openlocfilehash: a23b5ff4dc8b0cf18e529d9cdc9925ea7fcedba0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-marketplace-items-available-for-azure-stack"></a>Azure 스택 사용할 수 있는 azure 마켓플레이스 항목

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*


## <a name="virtual-machine-extensions"></a>가상 머신 확장
필요한 VM 확장에 대 한 업데이트가 있을 때마다 다운로드 하는 것이 좋습니다. 제품에서 제공 하는 확장 프로그램 일반 패치 및 업데이트 프로세스;에서 업데이트 되지 않습니다. 하십시오 업데이트에 대 한 자주 확인 합니다. 다른 확장 에게만 마켓플레이스 관리를 통해 제공 됩니다.

|  | 항목 이름 | 설명 | 게시자 | OS 종류 |
| --- | --- | --- | --- | --- |
|![](media/azure-stack-marketplace-azure-items/cse.png) | [SQL IaaS 확장](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)| <b>모든 SQL Server에서 Windows 마켓플레이스 항목을 배포 하려면이 확장을 다운로드-이것이 필요 합니다.</b> | Microsoft | Windows |
|![](media/azure-stack-marketplace-azure-items/cse.png) | [사용자 지정 스크립트 확장](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-customscript)| <b>기본 버전의 Windows 사용자 지정 스크립트 확장이이 업데이트를 다운로드 합니다.</b> | Microsoft | Windows |
|![](media/azure-stack-marketplace-azure-items/dsc.png) | [PowerShell DSC 확장](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-dsc-overview)| <b>PowerShell DSC 확장의 기본 버전으로이 업데이트를 다운로드 합니다.</b> | Microsoft | Windows |
| ![](media/azure-stack-marketplace-azure-items/cse.png) | [Microsoft 맬웨어 방지 확장](https://docs.microsoft.com/en-us/azure/security/azure-security-antimalware)| Azure를 위한 Microsoft 맬웨어 방지는 응용 프로그램 및 테넌트 환경에 대한 단일 에이전트 솔루션이며 사용자의 개입 없이 백그라운드에서 실행되도록 설계됩니다. | Microsoft | Windows |
| ![](media/azure-stack-marketplace-azure-items/dockerextension.png) | [Docker](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.docker-arm) | Linux 가상 컴퓨터에 docker 확장 합니다. | Microsoft | Linux |
| ![](media/azure-stack-marketplace-azure-items/cse.png) | [Linux 용 VM 액세스](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/)| <b>Linux 확장용는 VMAccess의 기본 버전으로이 업데이트를 다운로드 합니다. Debian Linux Vm을 사용 하려는 경우에 유용 합니다.</b> | Microsoft | Linux |
| ![](media/azure-stack-marketplace-azure-items/acronis.png) | [Linux 용 Acronis 백업 확장](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-lin-arm) | Microsoft Azure에 대 한 Acronis 백업 확장에는 데이터 보호 제품의 Acronis 백업 제품군의 일부입니다. | Acronis 국제 GmbH 합니다. | Linux |
| ![](media/azure-stack-marketplace-azure-items/acronis.png) | [Windows 용 Acronis 백업 확장](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-win-arm) | Microsoft Azure에 대 한 Acronis 백업 확장에는 데이터 보호 제품의 Acronis 백업 제품군의 일부입니다. | Acronis 국제 GmbH 합니다. | Windows |

## <a name="microsoft-virtual-machine-images-and-solution-templates"></a>Microsoft 가상 컴퓨터 이미지 및 솔루션 템플릿

Microsoft Azure 스택 솔루션 템플릿과 다음 Azure 마켓플레이스 가상 컴퓨터를 지원합니다. 모든 종속 관계를 명시 된 대로 별도로 다운로드 하십시오. SQL Server 컴퓨터 학습 서버 등 응용 프로그램 무료 또는 평가판으로 표시 하는 경우를 제외 하 고 적절 한 라이선스 필요 합니다.

|  | 항목 이름 | 설명 | 게시자 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/windowsserver.png) | [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) | 엔터프라이즈 수준의 솔루션을 배포 하기 쉽지만, 비용 효율적인, 응용 프로그램 중심 및 사용자 중심 있습니다. 이러한 이미지는 최신 패치도 정기적으로 업데이트 됩니다. <b>중요 한 정보: 이미지를 다운로드 한 후에 2018 년 1 월 18, 삭제 되 고 최신 버전으로 대체 해야 합니다.</b> | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/remotedesktopservicesdeployment.png) | [원격 데스크톱 서비스 (RDS) 배포](https://azuremarketplace.microsoft.com/marketplace/apps/rds.remote-desktop-services-basic-deployment) | 기본 서비스 RDS (원격 데스크톱) 배포를 만듭니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2013 평가판](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2013Trial) | Microsoft SharePoint Server 2013 평가판에서 Windows Server 2012 Datacenter 및 Visual Studio 2017 community edition. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2016 평가판](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2016Trial) | Microsoft SharePoint Server 2016 평가판 Windows Server 2016 데이터 센터에 있습니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2012 r 2에서 SQL Server 2014 SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2014SP2-WS2012R2) | SQL Server 2014 서비스 팩 2입니다.  **필요한 다운로드:** SQL IaaS 확장 합니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016에서 SQL Server 2016 SP1 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | 지능적이 고 중요 한 응용 프로그램에 대 한 데이터베이스 플랫폼입니다. **필요한 다운로드:** SQL IaaS 확장 합니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016에서 SQL Server 2016 SP1 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeLicenseSQLServer2016SP1DeveloperWindowsServer2016) | 무료 개발자 버전 SQL Server 2016 s p 1의 트랜잭션, 데이터 웨어하우징, 비즈니스 인텔리전스 및 분석 워크 로드 합니다.  **필요한 다운로드:** SQL IaaS 확장 합니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016의 SQL Server 2016 SP1 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeLicenseSQLServer2016SP1ExpressWindowsServer2016) | Express 버전을 SQL Server 2016 s p 1의 무료입니다. **필요한 다운로드:** SQL IaaS 확장 합니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016의 SQL Server 2016 SP1 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | 지능적이 고 중요 한 응용 프로그램에 대 한 데이터베이스 플랫폼입니다.  **필요한 다운로드:** SQL IaaS 확장 합니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016에서 SQL Server 2016 SP1 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | 지능적이 고 중요 한 응용 프로그램에 대 한 데이터베이스 플랫폼입니다.  **필요한 다운로드:** SQL IaaS 확장 합니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016에서 SQL Server 2017 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017StandardonWindowsServer2016) | 지능적이 고 중요 한 응용 프로그램에 대 한 데이터베이스 플랫폼입니다. **필요한 다운로드:** SQL IaaS 확장 합니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016에서 SQL Server 2017 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) | 무료 개발자 버전 SQL Server 2016 s p 1의 트랜잭션, 데이터 웨어하우징, 비즈니스 인텔리전스 및 분석 워크 로드 합니다.  **필요한 다운로드:** SQL IaaS 확장 합니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016의 SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) | Express 버전을 SQL Server 2016 s p 1의 무료입니다. **필요한 다운로드:** SQL IaaS 확장 합니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016에서 SQL Server 2017 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017EnterpriseWindowsServer2016) | 지능적이 고 중요 한 응용 프로그램에 대 한 데이터베이스 플랫폼입니다.  **필요한 다운로드:** SQL IaaS 확장 합니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016에서 SQL Server 2017 웹](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017WebonWindowsServer2016) | 지능적이 고 중요 한 응용 프로그램에 대 한 데이터베이스 플랫폼입니다.  **필요한 다운로드:** SQL IaaS 확장 합니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS에 SQL Server 2017 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS) | 지능적이 고 중요 한 응용 프로그램에 대 한 데이터베이스 플랫폼입니다. | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS에 SQL Server 2017 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) | 무료 개발자 버전 SQL Server 2016 s p 1의 트랜잭션, 데이터 웨어하우징, 비즈니스 인텔리전스 및 분석 워크 로드 합니다. | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS의 SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) | Express 버전을 SQL Server 2016 s p 1의 무료입니다. | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS SQL Server 2017 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017EnterpriseUbuntuServer1604LTS) | 지능적이 고 중요 한 응용 프로그램에 대 한 데이터베이스 플랫폼입니다. | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS에 SQL Server 2017 웹](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017WebonUbuntuServer1604LTS) | 지능적이 고 중요 한 응용 프로그램에 대 한 데이터베이스 플랫폼입니다. | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server 12 (SLES)에서 SQL Server 2017 Standard SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017StandardonSLES12SP2) | 지능적이 고 중요 한 응용 프로그램에 대 한 데이터베이스 플랫폼입니다. | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server 12 (SLES)에서 SQL Server 2017 Developer SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP) | 무료 개발자 버전 SQL Server 2016 s p 1의 트랜잭션, 데이터 웨어하우징, 비즈니스 인텔리전스 및 분석 워크 로드 합니다. | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server 12 (SLES)의 SQL Server 2017 Express s p 2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) | Express 버전을 SQL Server 2016 s p 1의 무료입니다. | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server 12 (SLES)에서 SQL Server 2017 Enterprise SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017EnterpriseonSLES12SP2) | 지능적이 고 중요 한 응용 프로그램에 대 한 데이터베이스 플랫폼입니다. | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 2017 웹 SUSE Linux Enterprise Server (SLES)를에 12 SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017WebonSLES12SP2) | 지능적이 고 중요 한 응용 프로그램에 대 한 데이터베이스 플랫폼입니다. | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Windows Server 2016에서 9.2.1 서버 학습 Microsoft 컴퓨터](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer921onWindowsServer2016) | Microsoft 기계 9.2.1 Windows Server 2016에서 서버를 학습 합니다. | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Microsoft 기계 16.04 ubuntu Server 9.2.1 학습](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer921onUbuntu1604) | Microsoft 기계 16.04 ubuntu Server 9.2.1를 학습 합니다. | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Microsoft 기계 CentOS linux 7.2 9.2.1 서버 학습](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer921onCentOSLinux72) | Microsoft 기계 CentOS linux 7.2 9.2.1 서버를 학습 합니다. | Microsoft + Rogue Wave |


## <a name="linux-distributions"></a>Linux 배포
|  | 항목 이름 | 설명 | 게시자 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/coreos.png) | [CoreOS 하 여 Linux 컨테이너](https://azuremarketplace.microsoft.com/marketplace/apps/CoreOS.CoreOS) | Linux 컨테이너는 컨테이너를 실행 하 고 클러스터를 관리할 서버를 원활 하 게 업데이트 하는 쉬운 방법을 제공 현대적이 고 최소한의 Linux 배포판-웨어하우스 확장을 사용할 수 있는 모든 구성 요소를 계산 합니다. | CoreOS |
| ![](media/azure-stack-marketplace-azure-items/ubuntu.png) | [Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer) | Ubuntu Server는 클라우드 환경에 세계적으로 가장 많이 사용되는 Linux입니다. | Canonical |
| ![](media/azure-stack-marketplace-azure-items/debian8.png) | [Debian 8 "제시"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian) | Debian GNU/Linux는 가장 많이 사용되는 Linux 배포 중 하나입니다. | credativ |
| ![](media/azure-stack-marketplace-azure-items/debian8.png) | [Debian 9 "Stretch"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian9) | Debian GNU/Linux는 가장 많이 사용되는 Linux 배포 중 하나입니다. | credativ |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [CentOS 기반 6.9](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased69) | 이 배포의 Linux CentOS을 기반으로 하 고 Rogue 웨이브 소프트웨어에 의해 제공 됩니다. | 불량 웨이브 소프트웨어 (이전의 OpenLogic)  |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [7.4 centOS 기반](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74) | 이 배포의 Linux CentOS을 기반으로 하 고 Rogue 웨이브 소프트웨어에 의해 제공 됩니다. | 불량 웨이브 소프트웨어 (이전의 OpenLogic) |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [7.4 LVM centOS 기반](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74LVM) | 이 배포의 Linux CentOS을 기반으로 하 고 Rogue 웨이브 소프트웨어에 의해 제공 됩니다. | 불량 웨이브 소프트웨어 (이전의 OpenLogic) |
| ![](media/azure-stack-marketplace-azure-items/suse.png) | [SLES 12 SP3 (BYOS)](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SUSELinuxEnterpriseServer12SP3BringYourOwnSubscription) | SUSE Linux Enterprise Server 12 SP3. | SUSE |

## <a name="third-party-byol-free-and-trial-images-and-solution-templates"></a>제 3 자 BYOL, Free 및 평가판 이미지 및 솔루션 템플릿

|  | 항목 이름 | 설명 | 게시자 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/abantecart.png) | [AbanteCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.abantecart) | 소스 전자 상거래를 쇼핑 카트를 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/activemq.png) | [ActiveMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.activemq) | Java에서 소스 메시지 브로커를 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/akeneo.png) | [Akeneo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.akeneo) | 강력한 PIM 관리 프로세스를 간소화 하도록 설계 되었습니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/alfrescocommunity.png) | [Alfresco Community](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.alfrescocommunity) | 문서를 손쉽게 관리를 위한 ECM 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/apachesolr.png) | [Apache Solr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.apachesolr) | 신뢰할 수 있는 오픈 소스 엔터프라이즈 검색 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/canvaslms.png) | [Canvas LMS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.canvaslms) | 오픈 소스 교육 관리 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/cassandra.png) | [Cassandra](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cassandra) | 항상 사용 가능한 확장 가능한 오픈 소스 데이터베이스입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/civicrm.png) | [CiviCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.civicrm) | 간단한 웹 기반 관계 관리 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/cmsmadesimple.png) | [간단 하 게 CMS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cmsmadesimple) | 만들기 및 관리 웹 사이트를 빠르고 쉬운 방법입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/codedx.png) | [코드 Dx](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.codedx) | 코드 분석 및 취약점 관리 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/codiad.png) | [Codiad](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.codiad) | 최소 사용 공간 소스 클라우드 IDE를 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/concrete5.png) | [Concrete5](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.concrete5) | 쉽게 배포할 웹 응용 프로그램, 웹 사이트, 저장소 및 포럼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/coppermine.png) | [Coppermine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.coppermine) | 다용도, 모든 기능을 갖춘 웹 갤러리입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/couchdb.png) | [CouchDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.couchdb) | 사용 하기 쉬운 오픈 소스 데이터베이스 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/diaspora.png) | [Diaspora](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.diaspora) | 개인 인기 있는 웹 서버입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/discourse.png) | [Discourse](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.discourse) | 고해상도 오픈 소스 토론 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/django.png) | [Django](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.djangostack) | 고급 Python 웹 프레임 워크입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dolibarr.png) | [Dolibarr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dolibarr) | 무료 오픈 소스 소프트웨어 패키지입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dokuwiki.png) | [DokuWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dokuwiki) | 다양 한 오픈 소스 wiki 소프트웨어입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dreamfactory.png) | [DreamFactory](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dreamfactory) | SQL, NoSQL, 같은 서비스와 소스 REST API를 열고 BLOB 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/elasticsearch.png) | [Elasticsearch](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elastic-search) | 유연 하 고 강력한 오픈 소스 분석 엔진입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/elk.png) | [ELK](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elk) | Elasticsearch, Kibana, 및 Logstash의 빅 데이터 모음입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/erpnext.png) | [ERPNext](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.erpnext) | 소스 계획 ERP (엔터프라이즈 리소스) 플랫폼을 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/espocrm.png) | [EspoCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.espocrm) | 고객 관계를 관리 하는 데 도움이 되는 간단한 CRM 시스템. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/exoplatform.png) | [eXo 플랫폼](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.exoplatform) | 오픈 소스, 기업 소셜 소프트웨어. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/fatfreecrm.png) | [Fat 무료 CRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.fatfreecrm) | 레일 기반 CRM에서 오픈 소스 Ruby 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/bitnami.png) | [GitLab Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.gitlab) | Git 관리 소프트웨어를 신속 하 고 보호 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/ghost.png) | [Ghost](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.ghost) | 게시에만 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hadoop.png) | [Hadoop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hadoop) | 신뢰할 수 있는 하 고 확장 가능한 분산 컴퓨팅을 위한 프레임 워크. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hhvm.png) | [HHVM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hhvmstack) | 완전 하 게 통합 되 고 실행 방식 개발 환경입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hordegroupwarewebmail.png) | [무리 그룹웨어 Webmail](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hordegroupwarewebmail) | 무료 이며 엔터프라이즈 준비, 브라우저 기반 통신 모음입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jenkins.png) | [Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jenkins) | SCM 도구를 지 원하는 통합 서버: CVS, Subversion, 및 Git 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jfrogartifactory.png) | [JFrog Artifactory Bitnami에 의해 인증](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.artifactory) | 선행 게시자에서 이진 리포지토리 소프트웨어입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/joomla.png) | [Joomla!](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.joomla) | 쉽게 웹 사이트에 대 한 알기 쉬운 CMS를 작성합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jruby.png) | [JRuby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jrubystack) | Java로 구현 Ruby의 성능이 우수 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/kafka.png) | [Kafka](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.kafka) | Distributed 강력한 메시징 시스템 게시-구독. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/lamp.png) | [LAMP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lampstack) | 완전 하 게 통합 되 고 개발 환경을 실행 하도록 준비 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/lapp.png) | [LAPP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lappstack) | 전체 PHP, PostgreSQL, 및 Apache 개발 환경입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/letschat.png) | [채팅 보겠습니다](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.letschat) | 오픈 소스 영구 메시징 응용 프로그램입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/limesurvey.png) | [LimeSurvey](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.limesurvey) | 질문 및 답변이 폴링 관리 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/livehelperchat.png) | [실시간 채팅 도우미](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.livehelperchat) | 소스 실시간 채팅 지원을 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mahara.png) | [Mahara](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mahara) | 인기 있는 오픈 소스 ePortfolio 및 소셜 네트워킹 웹 앱입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/magento.png) | [Magento](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.magento) | 인기 있는 전자 상거래 소프트웨어 및 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mantis.png) | [사마귀](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mantis) | 고급 버그 추적 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mattermostteamedition.png) | [Mattermost Team Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mattermost) | 소스 작업 공간 메시징 솔루션을 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mautic.png) | [Mautic](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mautic) | 오픈 소스, 엔터프라이즈 마케팅 자동화 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mean.png) | [MEAN](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mean) | Node.js 및 mongoDB에 대 한 일반적인 개발 환경입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mediawiki.png) | [MediaWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mediawiki) | 강력 하 고 확장 가능한 wiki 구현입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/memcached.png) | [Memcached](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.memcached) | 시스템 캐싱 고성능, 분산 된 메모리 개체입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/modx.png) | [MODX](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.modx) | 직관적인 웹 CMS입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mongodb.png) | [MongoDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mongodb) | 성능 우선 열기 소스 NoSQL 데이터베이스 c + +로 작성 된입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/moodle.png) | [Moodle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.moodle) | 효과적인 CMS 온라인 학습 커뮤니티를 위한 것입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/multicraft.png) | [Multicraft](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.multicraft) | 솔루션 및 제어 패널 호스팅 Minecraft 서버입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mybb.png) | [MyBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mybb) | 무료 오픈 소스 포럼 소프트웨어입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mysql.png) | [MySQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mysql) | 가장 인기 있는 데이터베이스 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/neos.png) | [Neos](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.neos) | 다양 한 오픈 소스 콘텐츠 관리 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/nginx.png) | [Nginx](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nginxstack) | 전체 PHP, MySQL 및 Nginx 개발 환경입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/noalyss.png) | [Noalyss](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.noalyss) | 강력한 이중 항목 회계 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/nodejs.png) | [node.js](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nodejs) | 쉽게 작성에 대 한 Javascript로 작성 된 소스 환경을 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/odoo.png) | [Odoo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.odoo) | 비즈니스 프로세스를 효과적으로 연결 하는 CRM 및 ERP 시스템. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openatrium.png) | [열기 중앙 홀의 형태](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openatrium) | 유연 하 고 다채로운 인트라넷 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/opencart.png) | [OpenCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.opencart) | 온라인 판매 업체에 대 한 가능한 전자 상거래 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openedx.png) | [열기 edX Bitnami 하 여 인증](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openedx) | 선행 게시자에서 eLearning 소프트웨어입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openfire.png) | [OpenFire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openfire) | XMPP으로 소스 실시간 공동 작업 서버를 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openproject.png) | [OpenProject](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openproject) | 인기 있는 오픈 소스 프로젝트 관리 소프트웨어입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/orangehrm.png) | [OrangeHRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orangehrm) | 모듈의 풍부한 HR 관리 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/orocrm.png) | [OroCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orocrm) | 유연한 오픈 소스 CRM 응용 프로그램입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/osclass.png) | [Osclass](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.osclass) | 만들고 기술적인 전문 지식 없이 무료 광고를 관리 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/owncloud.png) | [ownCloud](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.owncloud) | 가장 많이 소스 파일 동기화를 열고 솔루션을 공유 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/oxideshop.png) | [OXID eShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.oxid-eshop) | 오픈 소스 전자 상거래 시스템이 신뢰할 수 있습니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/phpbb.png) | [phpBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phpbb) | 사용자 지정 가능한 게시판 솔루션입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/phplist.png) | [phpList](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phplist) | 단방향 전자 메일 알림 배달 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/pimcore.png) | [Pimcore](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pimcore) | 강력한 engagement 관리 플랫폼 (CEM/CXM). | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/piwik.png) | [Piwik](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.piwik) | 실시간 웹 분석 소프트웨어 프로그램입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/plone.png) | [Plone](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.plone) | 무료 오픈 소스 가상 어플라이언스 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/pootle.png) | [Pootle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pootle) | 번역 프로젝트에 대 한 사용 하기 쉬운 웹 포털입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/postgresql.png) | [PostgreSQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.postgresql) | 높은 고급 오픈 소스 데이터베이스입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/prestashop.png) | [PrestaShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.prestashop) | 소스 전자 상거래 웹 사이트 작성기를 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processmaker.png) | [프로세스 Maker Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakeropensourceedition) | 비즈니스 프로세스 관리 및 워크플로 자동화 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processmaker.png) | [프로세스 Maker Enterprise Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakerenterprise) | 소스 워크플로 및 비즈니스 프로세스 관리 소프트웨어를 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processwire.png) | [ProcessWire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processwire) | PHP5 인기 있는 오픈 소스 CMS입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/publify.png) | [Publify](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.publify) | Ruby 레일 기반 블로깅 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/rabbitmq.png) | [RabbitMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rabbitmq) | 효율적인 메시징 브로커 공통 플랫폼을 제공 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redash.png) | [Re: 대시](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redash) | 오픈 소스 데이터 시각화 및 공동 작업 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redis.png) | [Redis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redis) | 강력한 오픈 소스 키-값 저장소입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redmine.png) | [Redmine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmine) | 강력한 프로젝트 관리 웹 응용 프로그램 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redmineagile.png) | [Redmine+Agile](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmineplusagile) | 프로젝트 관리 응용 프로그램 Agile 플러그 인으로 미리 구성 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/reportserver.png) | [ReportServer 커뮤니티](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserver) | 소스 비즈니스 인텔리전스 플랫폼을 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/reportserverenterprise.png) | [ReportServer Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserverenterprise) | 엔터프라이즈 비즈니스 인텔리전스 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/resourcespace.png) | [ResourceSpace](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.resourcespace) | 향상 된 공동 작업을 위한 디지털 자산 관리 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/roundcube.png) | [Roundcube](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.roundcube) | MIME 지원과 같은 기능을 사용 하 여 IMAP 클라이언트 브라우저 기반 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/ruby.png) | [Ruby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rubystack) | 레일에 Ruby에 대 한 사용 하기 쉬운 개발 환경입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/seopanel.png) | [SEO 패널](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.seopanel) | 여러 웹 사이트를 추적 하기 위한 소스 SEO 관리 응용 프로그램을 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/shopware.png) | [Shopware](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.shopware) | 소스 전자 상거래 플랫폼을 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/simplemachinesforum.png) | [간단한 컴퓨터 포럼](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.simplemachinesforum) | 사용자 고유의 온라인 커뮤니티를 만드는 간단한 포럼 소프트웨어입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/spree.png) | [마구](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.spree) | 사용 하기 쉬운 전자 상거래 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/subversion.png) | [Subversion](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.subversion) | 소스 버전 제어 시스템을 엽니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/sugarcrm.png) | [SugarCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.sugarcrm) | 유연한 고객 관계 관리 솔루션입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/suitecrm.png) | [SuiteCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.suitecrm) | 인기 있는 엔터프라이즈 수준의 CRM 응용 프로그램입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/testlink.png) | [TestLink](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.testlink) | 품질 보증을 촉진 하는 관리 소프트웨어를 테스트 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tikiwikicmsgroupware.png) | [Tiki Wiki CMS Groupware](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tikiwikicmsgroupware) | 모든 기능 갖춘된 wiki 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tinytinyrss.png) | [아주 작은 아주 작은 RSS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tinytinyrss) | 유연한 Spen 소스 웹 기반 뉴스 피드 및 집계 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tomcat.png) | [Tomcat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tom-cat) | Java 커뮤니티에서 사양을 구현 하는 인기 있는 플랫폼입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/trac.png) | [Trac](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.trac) | 향상 된 wiki 및 문제 추적 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/typo3.png) | [Typo3](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.typo3) | 완벽 하 게 유연한 CMS입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/weblate.png) | [Weblate](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.weblate) | 웹 기반 번역 관리 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/webmailprophp.png) | [WebMail Pro PHP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.webmailpro) | 엔터프라이즈 기능을 통해 웹 메일 시스템입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/wildfly.png) | [WildFly](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wildfly) | Apache, WildFly, MySQL 및 Java를 포함 하는 응용 프로그램 서버. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/wordpress.png) | [WordPress](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wordpress) | 가장 널리 사용 되 고 준비 휴대용 CMS입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/x2enginesalescrm.png) | [X2Engine 판매 CRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.x2enginesalescrm) | Compact 마케팅, 영업 및 고객 서비스 응용 프로그램입니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/xoops.png) | [Xoops](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.xoops) | CMS 이자 동적 웹 사이트를 만드는 웹 포털 프로그램. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/zurmo.png) | [Zurmo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.zurmo) | 소스 CRM 시스템을 엽니다: 모바일 앱, 사회, 및 Gamified 합니다. | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/checkpoint.png) | [검사점 vSEC 보안 관리](https://azuremarketplace.microsoft.com/marketplace/apps/checkpoint.vsec) | 검사점 vSEC 자산을 보호 하기 고객이 Azure에서 맬웨어 및 정교한 위협 으로부터 고급, 다중 계층 위협 방지를 제공 합니다. <b>두 체크 포인트 vSEC 마켓플레이스 항목을 다운로드 합니다.</b> | Check Point |
| ![](media/azure-stack-marketplace-azure-items/chefautomate.png) | [Chef 자동화](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) | 빌드, 배포 및 사용 하 여 Chef 자동화 연속 자동화 플랫폼 관리 합니다. <b>Chef 마켓플레이스 항목을 모두 다운로드</b> | Chef Software, Inc |
| ![](media/azure-stack-marketplace-azure-items/hortonworks.png) | [Hortonworks Data Platform (HDP) 샌드박스](https://azuremarketplace.microsoft.com/marketplace/apps/hortonworks.hortonworks-sandbox) | HDP 기반의 2.5 100 %Hadoop, Spark, 스톰, HBase, Kafka, Hive, Ambari 소스 플랫폼을 엽니다. | Hortonworks |
| ![](media/azure-stack-marketplace-azure-items/kemp.png) | [LoadMaster 부하 분산 장치 ADC 콘텐츠 스위치](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure) | 계층 4-7 응용 프로그램 배달 컨트롤러 (ADC) 부하 분산 장치, 콘텐츠 스위치 및 트래픽 관리자. | KEMP Technologies Inc. |
| ![](media/azure-stack-marketplace-azure-items/noobaa.png) | [NooBaa 하이브리드 AWS S3 compatibe-Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/noobaa.noobaa-hybrid-s3-archive-05) | S3 호환 저장소 서비스 공개에 걸쳐 있고 온-프레미스 리소스 용량입니다. | NooBaa |
| ![](media/azure-stack-marketplace-azure-items/puppet.png) | [Puppet Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2) | Puppet Enterprise를 사용 하면 Azure 스택 인프라의 전체 수명 주기를 자동화할 수 있습니다. <b>Puppet 마켓플레이스 항목을 모두 다운로드 합니다.</b> | Puppet |
| ![](media/azure-stack-marketplace-azure-items/suse.png) | [SUSE Manager 3.0 프록시 (BYOS)](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SUSEManager30ProxyBringYourOwnSubscription) | 클래스의 가장 소스 인프라 관리를 엽니다. | SUSE |
| ![](media/azure-stack-marketplace-azure-items/zerodown.png) | [ZeroDown 소프트웨어 도구](https://azuremarketplace.microsoft.com/marketplace/apps/zerodown_software.zerodown-software-tool-7_1) | ZeroDown 소프트웨어 도구 v7.1-마이그레이션할 + HA 합니다. | ZeroDown 소프트웨어 |
