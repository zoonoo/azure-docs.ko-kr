---
title: "Azure의 엔터프라이즈급 WordPress | Microsoft Docs"
description: "Azure 앱 서비스에서 엔터프라이즈급 WordPress 사이트를 호스트하는 방법에 대해 알아봅니다."
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 22d68588-2511-4600-8527-c518fede8978
ms.service: app-service-web
ms.devlang: php
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 541dd7bed5a4a9e306642b2c36dd7c9d45aec690
ms.openlocfilehash: fcaff51b2897b1b0bce8b1c503adfd9425c9fbb4
ms.lasthandoff: 01/24/2017


---
# <a name="enterprise-class-wordpress-on-azure"></a>Azure의 엔터프라이즈급 WordPress
Azure App Service는 중요 업무용 대규모 [WordPress][wordpress] 사이트를 위한 안전하고 사용하기 쉬운 확장 가능 환경을 제공합니다. Microsoft는 자체적으로 [Office][officeblog] 및 [Bing][bingblog] 블로그와 같은 엔터프라이즈급 사이트를 운영하고 있습니다. 이 문서에서는 Microsoft Azure App Service의 Web Apps 기능을 사용하여 대규모 방문자를 처리할 수 있는 엔터프라이즈급 클라우드 기반 WordPress 사이트를 설정하고 유지 관리하는 방법을 보여 줍니다.

## <a name="architecture-and-planning"></a>아키텍처 및 계획
기본 WordPress 설치는 두 가지 요구 사항만 충족하면 됩니다.

* **MySQL 데이터베이스**:이 요구 사항은 [Azure Marketplace의 ClearDB][cdbnstore]를 통해 사용할 수 있습니다. 또는 [Windows][mysqlwindows] 또는 [Linux][mysqllinux] 중 하나를 사용하여 Azure Virtual Machines에서 고유한 MySQL 설치를 관리할 수 있습니다.

  > [!NOTE]
  > ClearDB에서는 여러 MySQL 구성을 제공합니다. 각 구성에는 다양한 성능 특징이 있습니다. Azure Store를 통해 제공되거나 [ClearDB 웹 사이트](http://www.cleardb.com/pricing.view)에 표시된 대로 직접 제공되는 제품에 대한 자세한 내용은 [Azure Store][cdbnstore]를 참조하세요.
  >
  >
* **PHP 5.2.4 이상**: Azure App Service에서는 현재 [PHP 버전 5.4, 5.5 및 5.6][phpwebsite]을 제공합니다.

  > [!NOTE]
  > 최신 버전의 PHP를 실행하여 최신 보안 픽스를 설치하는 것이 좋습니다.
  >
  >

### <a name="basic-deployment"></a>기본 배포
기본적인 요구 사항만 사용하면 Azure 지역 내에서 기본 솔루션을 만들 수 있습니다.

![단일 Azure 지역에 호스트된 Azure 웹앱 및 MySQL 데이터베이스][basic-diagram]

이를 통해 사이트의 여러 Web Apps 인스턴스를 만들어 응용 프로그램을 확장할 수 있지만 모든 항목은 특정 지리적 영역에 있는 데이터 센터 내에서 호스트됩니다. 이 지역 외부의 방문자가 사이트를 사용하는 경우 응답 시간이 느릴 수 있습니다. 이 지역의 데이터 센터의 작동이 중지되는 경우 응용 프로그램도 중지됩니다.

### <a name="multi-region-deployment"></a>다중 지역 배포
Azure [Traffic Manager][trafficmanager]를 사용하여 여러 지리적 영역으로 WordPress 사이트를 확장하고 방문자에게 동일한 URL를 제공할 수 있습니다. 모든 방문자는 Traffic Manager를 통해 들어온 다음 부하 분산 구성에 따라 지역으로 라우팅됩니다.

![다중 지역에 호스트된 Azure 웹앱은 여러 지역의 MySQL로 라우팅하는 CDBR 고가용성 라우터를 사용합니다.][multi-region-diagram]

각 지역 내에서 WordPress 사이트는 여러 웹앱 인스턴스로 확장되지만 이러한 확장은 지역에 따라 다릅니다. 높은 트래픽 지역과 낮은 트래픽 지역이 다르게 확장될 수 있습니다.

트래픽을 복제하고 여러 MySQL 데이터베이스로 라우팅하려면 [ClearDB 고가용성 라우터(CDBR)][cleardbscale](왼쪽에 표시) 또는 [MySQL 클러스터 캐리어 등급 버전(CGE)][cge]을 사용할 수 있습니다.

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>미디어 저장소 및 캐싱을 사용한 다중 지역 배포
사이트가 업로드를 허용하거나 미디어 파일을 호스트하는 경우에는 Azure Blob Storage를 사용합니다. 캐싱이 필요한 경우에는 [Redis Cache][rediscache], [Memcache 클라우드](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) 또는 [Azure Store](http://azure.microsoft.com/gallery/store/)에 제공되는 기타 캐싱 서비스 중 하나를 고려하세요.

![다중 지역에 호스트된 Azure 웹앱은 관리되는 캐시, Blob Storage 및 Content Delivery Network와 함께 MySQL용 CDBR 고가용성 라우터를 사용합니다.][performance-diagram]

Blob 저장소는 기본적으로 여러 지역에 지리적으로 분산되어 있으므로 모든 사이트에 파일을 복제하는 문제를 걱정할 필요가 없습니다. Blob Storage에 대해 Azure [Content Delivery Network][cdn]를 사용하도록 설정할 수도 있습니다. 그러면 파일이 방문자에게 가까운 끝 노드에 배포됩니다.

### <a name="planning"></a>계획
#### <a name="additional-requirements"></a>추가 요구 사항
| 원하는 작업.. | 사용 기능... |
| --- | --- |
| **대규모 파일 업로드 또는 저장** |[Blob Storage 사용을 위한 WordPress 플러그 인][storageplugin] |
| **메일 보내기** |[SendGrid][storesendgrid] 및 [SendGrid 사용을 위한 WordPress 플러그 인][sendgridplugin] |
| **사용자 지정 도메인 이름** |[Azure App Service에서 사용자 지정 도메인 이름 구성][customdomain] |
| **HTTPS** |[Azure App Service에서 웹앱에 대한 HTTPS를 사용하도록 설정][httpscustomdomain] |
| **사전 프로덕션 유효성 검사** |[Azure App Service에서 웹앱에 대한 스테이징 환경 설정][staging] <p>스테이징에서 프로덕션으로 웹앱을 이동하면 WordPress 구성도 이동됩니다. 스테이징된 앱을 프로덕션으로 이동하기 전에 프로덕션 앱의 요구 사항으로 모든 설정을 업데이트해야 합니다.</p> |
| **모니터링 및 문제 해결** |[Azure App Service에서 웹앱에 대한 진단 로깅 사용하도록 설정][log] 및 [Azure App Service에서 Web Apps 모니터링][monitor] |
| **사이트 배포** |[Azure App Service에서 웹앱 배포][deploy] |

#### <a name="availability-and-disaster-recovery"></a>가용성 및 재해 복구
| 원하는 작업.. | 사용 기능... |
| --- | --- |
| **사이트 부하 분산** 또는 **사이트 지리적으로 분산** |[Azure Traffic Manager로 트래픽 라우팅][trafficmanager] |
| **백업 및 복원** |[Azure App Service에서 웹앱 백업][backup] 및 [Azure App Service에서 웹앱 복원][restore] |

#### <a name="performance"></a>성능
클라우드의 성능은 주로 캐싱 및 확장을 통해 달성됩니다. 그러나 메모리, 대역폭 및 Web Apps 호스팅의 다른 특성을 고려해야 합니다.

| 원하는 작업.. | 사용 기능... |
| --- | --- |
| **앱 서비스 인스턴스 기능 이해** |[App Service 계층의 기능을 비롯한 가격 책정 세부 정보][websitepricing] |
| **리소스 캐시** |[Redis cache][rediscache], [Memcache 클라우드](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/) 또는 [Azure Store](/gallery/store/)에 제공되는 기타 캐싱 서비스 중 하나 |
| **응용 프로그램 확장** |[Azure App Service에서 웹앱 크기 조정][websitescale] 및 [ClearDB 고가용성 라우팅][cleardbscale] 자체 MySQL 설치를 호스트하고 관리하도록 선택한 경우 확장을 위해 [MySQL 클러스터 CGE][cge]를 고려하는 것이 좋습니다. |

#### <a name="migration"></a>마이그레이션
기존 WordPress 사이트를 Azure App Service로 마이그레이션하는 방법에는 두 가지가 있습니다.

* **[WordPress 내보내기][export]**:이 메서드는 블로그의 콘텐츠를 내보냅니다. [WordPress 가져오기 도구 플러그 인][import]을 사용하여 Azure App Service의 새 WordPress 사이트로 이 콘텐츠를 가져올 수 있습니다.

  > [!NOTE]
  > 이 프로세스를 통해 콘텐츠를 마이그레이션할 수 있지만 플러그 인, 테마 또는 다른 사용자 지정 내용은 마이그레이션되지 않습니다. 이러한 구성 요소를 수동으로 다시 설치해야 합니다.
  >
  >
* **수동 마이그레이션**: [데이터베이스][wordpressdbbackup] 및 [사이트를 백업][wordpressbackup]한 다음 Azure App Service의 웹앱 및 관련 MySQL 데이터베이스에 수동으로 복원합니다. 이 메서드는 플러그 인, 테마 및 사용자 지정 내용을 수동으로 설치하는 번거로움을 방지하기 때문에 고도로 사용자 지정된 사이트를 마이그레이션할 때 유용합니다.

## <a name="step-by-step-instructions"></a>단계별 지침
### <a name="create-a-wordpress-site"></a>WordPress 사이트 만들기
1. [Azure Marketplace][cdbnstore]를 사용하여 사이트를 호스트할 지역에서 [아키텍처 및 계획](#planning) 섹션에서 확인한 크기로 MySQL 데이터베이스를 만듭니다.
2. [Azure App Service에서 WordPress 웹앱 만들기][createwordpress]의 단계를 따라 WordPress 웹앱을 만듭니다. 웹앱을 만들 때 **기존 MySQL 데이터베이스 사용**을 선택한 다음 1단계에서 만든 데이터베이스를 선택합니다.

기존 WordPress 사이트를 마이그레이션하는 경우 새 웹앱을 만든 후에 [Azure에 기존 WordPress 사이트 마이그레이션](#Migrate-an-existing-WordPress-site-to-Azure)을 참조하세요.

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Azure에 기존 WordPress 사이트 마이그레이션
[아키텍처 및 계획](#planning) 섹션에 설명된 것처럼 WordPress 사이트를 마이그레이션하는 방법에는 두 가지가 있습니다.

* **내보내기 및 가져오기 사용**은 많은 사용자 지정이 수행되지 않은 사이트 또는 콘텐츠만 이동하려는 경우입니다.
* **백업 및 복원 사용**은 사용자 지정 내용이 많아서 모든 항목을 이동하려는 사이트의 경우입니다.

다음 섹션 중 하나를 사용하여 사이트를 마이그레이션합니다.

#### <a name="the-export-and-import-method"></a>내보내기 및 가져오기 방법
1. [WordPress 내보내기][export]를 사용하여 기존 사이트를 내보냅니다.
2. [WordPress 사이트 만들기](#Create-a-new-WordPress-site) 섹션의 단계를 사용하여 웹앱을 만듭니다.
3. [Azure Portal][mgmtportal]에서 WordPress 사이트에 로그인한 다음 **플러그 인** > **새로 추가**를 클릭합니다. **WordPress 가져오기 도구** 플러그 인을 검색하고 설치합니다.
4. WordPress 가져오기 도구 플러그 인이 설치되면 **도구** > **가져오기**를 클릭한 다음 **WordPress**를 클릭하여 WordPress 가져오기 도구 플러그 인을 사용합니다.
5. **WordPress 가져오기** 페이지에서 **파일 선택**을 클릭합니다. 기존 WordPress 사이트에서 내보낸 WXR 파일을 찾은 다음 **파일 업로드 및 가져오기**를 클릭합니다.
6. **Submit**를 클릭합니다. 가져오기가 성공적으로 수행되었다는 메시지가 표시됩니다.
7. 모든 단계를 완료하면, [Azure Portal][mgmtportal]의 해당 **App Services** 블레이드에서 사이트를 다시 시작합니다.

사이트를 가져온 후에 다음 단계를 수행하여 가져오기 파일에 들어 있지 않은 설정을 사용하도록 구성해야 합니다.

| 기존 설정... | 방법 |
| --- | --- |
| **Permalinks** |새 사이트의 WordPress 대시보드에서 **설정** > **Permalinks**를 클릭한 다음 Permalinks 구조를 업데이트합니다. |
| **이미지/미디어 링크** |새 위치에 대한 링크를 업데이트하려면 검색 및 바꾸기 도구인 [Velvet Blues Update URL 플러그 인][velvet]을 사용하거나 데이터베이스에서 링크를 수동으로 업데이트합니다. |
| **테마** |**모양** > **테마**로 이동한 다음 필요에 따라 사이트 테마를 업데이트합니다. |
| **메뉴** |테마가 메뉴를 지원하는 경우 홈 페이지에 대한 링크에 이전 하위 디렉터리가 포함되어 있을 수 있습니다. **모양** > **메뉴**로 이동한 다음 업데이트합니다. |

#### <a name="the-backup-and-restore-method"></a>백업 및 복원 방법
1. [WordPress 백업][wordpressbackup]의 정보를 사용하여 기존 WordPress 사이트를 백업합니다.
2. [데이터베이스 백업][wordpressdbbackup]의 정보를 사용하여 기존 데이터베이스를 백업합니다.
3. 데이터베이스를 만들고 백업을 복원합니다.

   1. [Azure Marketplace][cdbnstore]에서 새 데이터베이스를 구입하거나 [Windows][mysqlwindows] 또는 [Linux][mysqllinux] 가상 컴퓨터에서 MySQL 데이터베이스를 설정합니다.
   2. [MySQL 워크벤치][workbench]와 같은 MySQL 클라이언트를 사용하여 새 데이터베이스에 연결하고 WordPress 데이터베이스를 가져옵니다.
   3. 데이터베이스를 업데이트하여 도메인 항목을 새 Azure App Service 도메인(예: mywordpress.azurewebsites.net)으로 변경합니다. [WordPress 데이터베이스 스크립트 검색 및 바꾸기][searchandreplace]를 사용하여 모든 인스턴스를 안전하게 변경합니다.
4. Azure Portal에서 웹앱을 만들고 WordPress 백업을 게시합니다.

   1. 데이터베이스가 있는 웹앱을 만들려면 [Azure Portal][mgmtportal]에서 **새로 만들기** > **웹 + 모바일** > **Azure Marketplace** > **Web Apps** > **웹앱 + SQL**(또는 **웹앱 + MySQL**) > **만들기**를 클릭합니다. 필요한 모든 설정을 구성하여 빈 웹 앱을 만듭니다.
   2. WordPress 백업에서 **wp-config.php** 파일을 찾아 편집기에서 엽니다. 다음 항목을 새 MySQL 데이터베이스에 대한 정보로 바꿉니다.

      * **DB_NAME**: 데이터베이스의 사용자 이름입니다.
      * **DB_USER**: 데이터베이스에 액세스하는 데 사용되는 사용자 이름입니다.
      * **DB_PASSWORD**: 사용자 암호입니다.

        이러한 항목을 변경한 후에는 **wp-config.php** 파일을 저장한 후 닫습니다.
   3. [Azure App Service에서 웹앱 배포][deploy] 정보를 사용하여 사용할 배포 방식을 설정한 다음 Azure App Service에서 웹앱으로 WordPress 백업을 배포합니다.
5. WordPress 사이트가 배포되면 사이트에 대한 *.azurewebsite.net URL을 사용하여 새 사이트(App Service 웹앱)에 액세스할 수 있어야 합니다.

### <a name="configure-your-site"></a>사이트 구성
WordPress 사이트가 만들어지거나 마이그레이션되면 다음 정보를 사용하여 성능을 향상시키거나 추가 기능을 사용하도록 설정합니다.

| 원하는 작업.. | 사용 기능... |
| --- | --- |
| **앱 서비스 계획 모드, 크기 및 사용 크기 조정 설정** |[Azure App Service에서 웹앱 크기 조정][websitescale] |
| **영구 데이터베이스 연결 사용** |기본적으로 WordPress는 영구 데이터베이스 연결을 사용하지 않으므로 여러 번 연결된 후에는 데이터베이스에 대한 연결이 제한될 수 있습니다. 영구 연결을 사용하려면 [영구 연결 어댑터 플러그 인](https://wordpress.org/plugins/persistent-database-connection-updater/installation/)을 설치합니다. |
| **성능 향상** |<ul><li><p><a href="https://azure.microsoft.com/en-us/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/">ARR 쿠키 사용 안 함</a>은 여러 Web Apps 인스턴스에서 WordPress를 실행할 때 성능을 향상시킬 수 있습니다.</p></li><li><p>캐싱을 사용하도록 설정합니다. <a href="https://wordpress.org/plugins/redis-object-cache/">Redis 개체 캐시 WordPress 플러그 인</a>과 함께 <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis Cache</a>(미리 보기)를 사용하거나 <a href="/gallery/store/">Azure Store</a>의 기타 캐싱 제품 중 하나를 사용할 수 있습니다.</p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Wincache로 WordPress 더 빠르게 만들기</a> Wincache는 기본적으로 웹앱에 대해 사용되도록 설정되어 있습니다.</p></li><li><p>[Azure App Service에서 웹앱 크기를 조정][websitescale]하고 <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB 고가용성 라우팅</a> 또는 <a href="http://www.mysql.com/products/cluster/">MySQL 클러스터 CGE</a>를 사용합니다.</p></li></ul> |
| **저장소에 Blob 사용** |<ol><li><p>[Azure Storage 계정 만들기](../storage/storage-create-storage-account.md)</p></li><li><p>[콘텐츠 배포 네트워크를 사용](../cdn/cdn-create-new-endpoint.md)하여 Blob에 저장된 데이터를 지리적으로 분산하는 방법을 알아봅니다.</p></li><li><p><a href="https://wordpress.org/plugins/windows-azure-storage/">WordPress용 Azure Storage 플러그 인</a>을 설치하고 구성합니다.</p><p>이 플러그 인의 자세한 설치 및 구성 정보는 <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">사용자 가이드</a>(영문)를 참조하세요.</p> </li></ol> |
| **전자 메일 사용** |Azure Store를 사용하여 <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a>를 사용합니다. WordPress용 <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">SendGrid 플러그 인</a> 설치 |
| **사용자 지정 도메인 이름 구성** |[Azure App Service에서 사용자 지정 도메인 이름 구성][customdomain] |
| **사용자 지정 도메인 이름에 HTTPS 사용** |[Azure App Service에서 웹앱에 대한 HTTPS를 사용하도록 설정][httpscustomdomain]. |
| **사이트 부하 분산 또는 지리적으로 분산** |[Azure Traffic Manager로 트래픽 라우팅][trafficmanager] 사용자 지정 도메인을 사용하는 경우 사용자 지정 도메인 이름으로 Traffic Manager를 사용하는 방법에 대한 자세한 내용은 [Azure App Service에서 사용자 지정 도메인 이름 구성][customdomain]을 참조하세요. |
| **자동 백업 사용** |[Azure App Service에서 웹앱 백업][backup] |
| **진단 로깅 사용** |[Azure App Service에서 웹앱에 대한 진단 로깅 사용하도록 설정][log] |

## <a name="next-steps"></a>다음 단계
* [WordPress 최적화](http://codex.wordpress.org/WordPress_Optimization)
* [Azure App Service에서 멀티사이트로 WordPress 변환](web-sites-php-convert-wordpress-multisite.md)
* [Azure용 ClearDB 업그레이드 마법사](http://www.cleardb.com/store/azure/upgrade)
* [Azure 앱 서비스에서 웹앱의 하위 폴더에 WordPress 호스팅](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)
* [단계별: Azure를 사용하여 WordPress 사이트 만들기](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)
* [Azure에 기존 WordPress 블로그 호스트](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)
* [WordPress에서 깔끔한 고정 링크 사용](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)
* [Azure 앱 서비스에서 WordPress 블로그를 마이그레이션 및 실행하는 방법](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)
* [Azure 앱 서비스에서 무료로 WordPress를 실행하는 방법](http://architects.dzone.com/articles/how-run-wordpress-azure)
* [2분 이내의 Azure WordPress](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)
* [WordPress 블로그를 Azure로 이동 - 1부: Azure에서 WordPress 블로그 만들기](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)
* [WordPress 블로그를 Azure로 이동 - 2부: 콘텐츠 전송](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)
* [WordPress 블로그를 Azure로 이동 - 3부: 사용자 지정 도메인 설정](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)
* [WordPress 블로그를 Azure로 이동 - 4부: 깔끔한 고정 링크 및 URL 다시 쓰기 규칙](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)
* [WordPress 블로그를 Azure로 이동 - 5부: 하위 폴더에서 루트로 이동](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)
* [Azure 계정에서 WordPress 웹앱을 설정하는 방법](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)
* [Azure에서 WordPress 지원](http://www.johnpapa.net/wordpress-on-azure/)
* [Azure의 WordPress 관련 팁](http://www.johnpapa.net/azurecleardbmysql/)

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 평가](https://azure.microsoft.com/try/app-service/)로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정은 없습니다.
>
>

## <a name="whats-changed"></a>변경된 내용
웹 사이트에서 App Service로의 변경에 대한 지침은 [Azure App Service 및 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]:../virtual-machines/windows/classic/mysql-2008r2.md
[mysqllinux]:../virtual-machines/linux/classic/mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: /powershell/azureps-cmdlets-docs
[Azure CLI]:../cli-install-nodejs.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md

