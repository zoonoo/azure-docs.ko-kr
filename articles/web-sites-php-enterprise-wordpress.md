<properties title="Enterprise class WordPress on Azure Websites" pageTitle="Enterprise class WordPress on Azure Websites" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

## Azure 웹 사이트의 엔터프라이즈급 WordPress

Azure 웹 사이트는 중요 업무용 대규모 [WordPress][WordPress] 사이트를 위한 확장 가능하고 안전하고 사용하기 쉬운 환경을 제공합니다. Microsoft는 자체적으로 [Office][Office] 및 [Bing][Bing] 블로그와 같은 엔터프라이즈급 사이트를 운영하고 있습니다. 이 문서에서는 Azure 웹 사이트를 사용하여 대규모 방문자를 처리할 수 있는 엔터프라이즈급 클라우드 기반 WordPress 사이트를 설정하고 유지 관리하는 방법을 보여 줍니다.

## 이 문서에서는 다음을 수행합니다.

-   [아키텍처 및 계획][아키텍처 및 계획] - 사이트를 만들기 전에 아키텍처, 요구 사항 및 성능 고려 사항에 대해 알아보기

-   [방법][방법] - 사이트 만들기, 배포 및 구성

-   [추가 리소스][추가 리소스] - 추가 리소스 및 정보

## <span id="plan"></span></a>아키텍처 및 계획

기본 WordPress 설치는 두 가지 요구 사항만 충족하면 됩니다.

-   [Azure Store의 ClearDB][Azure Store의 ClearDB]를 통해 사용할 수 있는 **MySQL 데이터베이스**를 이용하거나 [Windows][Windows] 또는 [Linux][Linux]를 사용하여 Azure 가상 컴퓨터에서 자체 MySQL 설치를 관리할 수 있습니다.

    > [WACOM.NOTE] ClearDB는 각각 다양한 성능 특성을 갖는 여러 MySQL 구성을 제공합니다. Azure Store를 통해 제공되는 서비스에 대해서는 [Azure Store][Azure Store의 ClearDB](영문)를 참조하고, ClearDB에서 직접 제공되는 서비스에 대해서는 [ClearDB 가격 책정][ClearDB 가격 책정](영문)을 참조하세요.

-   **PHP 5.2.4 이상** - Azure 웹 사이트에서는 현재 [PHP 버전 5.3, 5.4 및 5.5][PHP 버전 5.3, 5.4 및 5.5]를 제공합니다.

    > [WACOM.NOTE] 최신 버전의 PHP에서 실행하여 최신 보안 픽스를 설치하는 것이 좋습니다.

### 기본 배포

기본적인 요구 사항만 사용하면 Azure 지역 내에서 기본 솔루션을 만들 수 있습니다.

![단일 Azure 지역에 호스트된 Azure 웹 사이트 및 MySQL 데이터베이스][단일 Azure 지역에 호스트된 Azure 웹 사이트 및 MySQL 데이터베이스]

이를 통해 웹 사이트의 여러 인스턴스를 만들어 응용 프로그램을 확장할 수 있지만 모든 항목은 특정 지리적 영역에 있는 데이터 센터 내에 호스트됩니다. 이 지역 외부의 방문자는 이 사이트를 사용할 때 느린 응답 시간을 경험할 수 있으며 이 지역의 데이터 센터가 작동 중단되면 응용 프로그램도 작동 중단됩니다.

### 다중 지역 배포

Azure [트래픽 관리자][트래픽 관리자]를 사용하여 방문자에게 단일 URL만 제공하면서 여러 지리적 지역으로 WordPress 사이트를 확장할 수 있습니다. 모든 방문자는 트래픽 관리자를 통해 들어온 다음 부하 분산 구성에 따라 지역으로 라우팅됩니다.

![Azure 웹 사이트, 다중 지역에 호스트, CDBR 고가용성 라우터를 사용하여 여러 지역의 MySQL로 라우팅][Azure 웹 사이트, 다중 지역에 호스트, CDBR 고가용성 라우터를 사용하여 여러 지역의 MySQL로 라우팅]

각 지역 내에서 WordPress 사이트는 여러 웹 사이트 인스턴스로 확장되지만 이러한 확장은 지역별로 이루어집니다. 즉, 높은 트래픽 지역과 낮은 트래픽 지역이 다르게 확장될 수 있습니다.

여러 MySQL 데이터베이스로의 복제 및 라우팅은 ClearDB의 [CDBR 고가용성 라우터][CDBR 고가용성 라우터](왼쪽에 표시) 또는 [MySQL 클러스터 CGE][MySQL 클러스터 CGE]를 사용하여 수행될 수 있습니다.

### 미디어 저장소 및 캐싱을 사용한 다중 지역 배포

사이트가 업로드를 허용하거나 미디어 파일을 호스트하는 경우에는 Azure Blob 저장소를 사용합니다. 캐싱이 필요한 경우에는 [Redis 캐시][Redis 캐시], [Memcache 클라우드][Memcache 클라우드], [MemCachier][MemCachier] 또는 [Azure Store][Azure Store]에 제공되는 기타 캐싱 서비스 중 하나를 고려하세요.

![Azure 웹 사이트, 다중 지역에 호스트, MySQL용 CDBR 고가용성 라우터 사용, 관리되는 캐시, Blob 저장소 및 CDN 포함][Azure 웹 사이트, 다중 지역에 호스트, MySQL용 CDBR 고가용성 라우터 사용, 관리되는 캐시, Blob 저장소 및 CDN 포함]

Blob 저장소는 기본적으로 여러 지역에 지리적으로 분산되어 있으므로 모든 사이트에 파일을 복제하는 문제를 걱정할 필요가 없습니다. Blob 저장소에 대해 Azure [CDN(Content Distribution Network)][CDN(Content Distribution Network)]을 사용하도록 설정할 수도 있습니다. 그러면 여기서 파일을 방문자에게 가까운 끝 노드로 분산합니다.

### 계획

#### 추가 요구 사항

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">원하는 작업..</th>
<th align="left">사용 기능...</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>대규모 파일 업로드 또는 저장</strong></td>
<td align="left"><a href="https://wordpress.org/plugins/windows-azure-storage/">Blob 저장소 사용을 위한 WordPress 플러그 인</a></td>
</tr>
<tr class="even">
<td align="left"><strong>전자 메일 보내기</strong></td>
<td align="left"><a href="http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/">SendGrid</a> 및 <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">SendGrid 사용을 위한 WordPress 플러그 인</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>사용자 지정 도메인 이름</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-custom-domain-name/">Azure 웹 사이트를 포함하는 사용자 지정 도메인 이름</a></td>
</tr>
<tr class="even">
<td align="left"><strong>HTTPS</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-configure-ssl-certificate/">Azure 웹 사이트의 HTTPS 지원</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>사전 프로덕션 유효성 검사</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-staged-publishing/">Azure 웹 사이트에 대한 스테이징된 게시 지원</a>
<p>스테이징에서 프로덕션으로 사이트를 전환하면 WordPress 구성도 이동됩니다. 스테이징된 사이트를 프로덕션 사이트로 전환하기 전에 프로덕션 사이트에 필요한 수준으로 모든 설정을 업데이트해야 합니다.</p></td>
</tr>
<tr class="even">
<td align="left"><strong>모니터링 및 문제 해결</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-enable-diagnostic-log/">Azure 웹 사이트의 진단 로깅</a> 및 <a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-monitor/">Azure 웹 사이트 모니터링</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>사이트 배포</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-deploy/">Azure 웹 사이트 배포</a></td>
</tr>
</tbody>
</table>

#### 가용성 및 재해 복구

| 원하는 작업..                                        | 사용 기능...                                         |
|------------------------------------------------------|------------------------------------------------------|
| **사이트 부하 분산** 또는 **사이트 지리적으로 분산** | [Azure 트래픽 관리자로 트래픽 라우팅][트래픽 관리자] |
| **백업 및 복원**                                     | [Azure 웹 사이트 백업][Azure 웹 사이트 백업] 및 [Azure 웹 사이트 복원][Azure 웹 사이트 복원] |

#### 성능

클라우드의 성능은 주로 캐싱 및 확장을 통해 구현되지만 웹 사이트 호스팅의 메모리, 대역폭 및 기타 특성도 고려해야 합니다.

| 원하는 작업..                    | 사용 기능...                                                                                                                                                                                  |
|----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **웹 사이트 인스턴스 기능 이해** | [웹 사이트 규모 및 모드 용량을 비롯한 가격 책정 세부 정보][웹 사이트 규모 및 모드 용량을 비롯한 가격 책정 세부 정보]                                                                                                                                  |
| **리소스 캐시**                  | [Redis 캐시][Redis 캐시], [Memcache 클라우드][Memcache 클라우드], [MemCachier][MemCachier] 또는 [Azure Store][Azure Store]에 제공되는 기타 캐싱 서비스 중 하나                                                                                |
| **응용 프로그램 확장**           | [Azure 웹 사이트 확장][Azure 웹 사이트 확장] 및 [ClearDB 고가용성 라우팅][CDBR 고가용성 라우터]. 자체 MySQL 설치를 호스트하고 관리하도록 선택한 경우 확장을 위해 [MySQL 클러스터 CGE][MySQL 클러스터 CGE]를 고려하는 것이 좋습니다. |

#### 마이그레이션

기존 WordPress 사이트를 Azure 웹 사이트로 마이그레이션하는 방법에는 두 가지가 있습니다.

-   **[WordPress 내보내기][WordPress 내보내기]** - 이 기능은 블로그 콘텐츠를 내보낸 다음 [WordPress 가져오기 도구 플러그 인][WordPress 가져오기 도구 플러그 인]을 사용하여 Azure에서 새 WordPress 사이트로 가져올 수 있습니다.

    > [WACOM.NOTE] 이 프로세스를 통해 콘텐츠를 마이그레이션할 수 있지만 플러그 인, 테마 또는 다른 사용자 지정 내용은 마이그레이션되지 않습니다. 이러한 항목은 수동으로 다시 설치해야 합니다.

-   **수동 마이그레이션** - [사이트][사이트] 및 [데이터베이스를 백업][데이터베이스를 백업]한 다음 Azure 웹 사이트 및 관련 MySQL 데이터베이스에 수동으로 복원하여 고가용성 사이트를 마이그레이션하고, 플러그 인, 테마 및 사용자 지정 내용을 수동으로 설치하는 번거로움을 피할 수 있습니다.

## 방법

### <span id="create"></span></a>새 WordPress 사이트 만들기

1.  [Azure Store][Azure Store의 ClearDB]를 사용하여 사이트를 호스트할 지역에서 MySQL 데이터베이스([아키텍처 및 계획][아키텍처 및 계획] 섹션에서 확인한 크기로)를 만듭니다.

2.  [Azure의 갤러리에서 WordPress 웹 사이트 만들기][Azure의 갤러리에서 WordPress 웹 사이트 만들기]의 단계에 따라 새 WordPress 사이트를 만듭니다. 사이트를 만들 때 **기존 MySQL 데이터베이스 사용**을 선택하고 1단계에서 만든 데이터베이스를 선택합니다.

기존 WordPress 사이트를 마이그레이션하는 경우 새 사이트를 만든 후에 [기존 WordPress 사이트 마이그레이션][기존 WordPress 사이트 마이그레이션]을 참조하세요.

### <span id="migrate"></span></a>기존 WordPress 사이트를 Azure로 마이그레이션

[아키텍처 및 계획][아키텍처 및 계획] 섹션에 설명된 것처럼 WordPress 웹 사이트를 마이그레이션하는 방법에는 두 가지가 있습니다.

-   **내보내기 및 가져오기** - 많은 사용자 지정이 수행되지 않은 사이트 또는 콘텐츠만 이동하려는 경우

-   **백업 및 복원** - 사용자 지정 내용이 많아서 모든 항목을 이동하려는 사이트

다음 섹션 중 하나를 사용하여 사이트를 마이그레이션합니다.

#### 내보내기 및 가져오기 방법

1.  [WordPress 내보내기][WordPress 내보내기]를 사용하여 기존 사이트를 내보냅니다.

2.  [새 WordPress 사이트 만들기][새 WordPress 사이트 만들기] 섹션의 단계를 사용하여 새 웹 사이트를 만듭니다.

3.  Azure 웹 사이트에서 WordPress 사이트에 로그인한 후 **플러그 인** -\> **새로 추가**를 클릭합니다. **WordPress 가져오기 도구** 플러그 인을 검색하고 설치합니다.

4.  가져오기 도구 플러그 인이 설치되면 **도구** -\> **가져오기**를 클릭하고 **WordPress**를 선택하여 WordPress 가져오기 도구 플러그 인을 사용합니다.

5.  **WordPress 가져오기** 페이지에서 **파일 선택**을 클릭합니다. 기존 WordPress 사이트에서 내보낸 WXR 파일로 이동한 후 **파일 업로드 및 가져오기**를 선택합니다.

6.  **Submit**를 클릭합니다. 가져오기가 성공적으로 수행되었다는 메시지가 표시됩니다.

7.  이러한 모든 단계를 완료한 경우 [Azure 관리 포털][Azure 관리 포털]에서 웹 사이트의 대시보드에서 웹 사이트를 다시 시작합니다.

사이트를 가져온 후에 다음 단계를 수행하여 가져오기 파일에 들어 있지 않은 설정을 사용하도록 구성해야 할 수 있습니다.

| 기존 설정...           | 방법                                                                                                                                             |
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| **Permalinks**         | 새 사이트의 WordPress 대시보드에서 **설정** -\> **Permalinks**를 클릭하고 Permalinks 구조 업데이트                                               |
| **이미지/미디어 링크** | 새 위치에 대한 링크를 업데이트하려면 검색 및 바꾸기 도구인 [Velvet Blues Update URL 플러그 인][Velvet Blues Update URL 플러그 인]을 사용하거나 데이터베이스에서 수동으로 업데이트 |
| **테마**               | **모양** -\> **테마**로 이동한 후 필요에 따라 웹 사이트 테마 업데이트                                                                            |
| **메뉴**               | 테마가 메뉴를 지원하는 경우 홈 페이지에 대한 링크에 이전 하위 디렉터리가 포함되어 있을 수 있습니다. **모양** -\> **메뉴**로 이동한 후 업데이트   |

#### 백업 및 복원 방법

1.  [WordPress 백업][사이트](영문)의 정보를 사용하여 기존 WordPress 사이트를 백업합니다.

2.  [데이터베이스 백업][데이터베이스를 백업](영문)의 정보를 사용하여 기존 데이터베이스를 백업합니다.

3.  새 데이터베이스를 만들고 백업을 복원합니다.

    1.  [Azure Store][Azure Store의 ClearDB]에서 새 데이터베이스를 구입하거나 [Windows][Windows] 또는 [Linux][Linux] VM에서 MySQL 데이터베이스를 설정합니다.

    2.  [MySQL 워크벤치][MySQL 워크벤치]와 같은 MySQL 클라이언트를 사용하여 새 데이터베이스에 연결하고 WordPress 데이터베이스를 가져옵니다.

    3.  데이터베이스를 업데이트하여 도메인 항목을 새 Azure 웹 사이트 도메인으로 변경합니다. mywordpress.azurewebsites.net을 예로 들 수 있습니다. [WordPress 데이터베이스 스크립트 검색 및 바꾸기][WordPress 데이터베이스 스크립트 검색 및 바꾸기]를 사용하여 모든 인스턴스를 안전하게 변경합니다.

4.  새 웹 사이트를 만들고 WordPress 백업을 게시합니다.

    1.  **새로 만들기** -\> **웹 사이트** -\> **사용자 지정 만들기**를 사용하여 [Azure 관리 포털][Azure 관리 포털]에서 데이터베이스로 새 웹 사이트를 만듭니다. 이렇게 하면 빈 사이트가 만들어집니다.

    2.  WordPress 백업에서 **wp-config.php** 파일을 찾아 편집기에서 엽니다. 다음 항목을 새 MySQL 데이터베이스에 대한 정보로 바꿉니다.

        -   **DB\_NAME** - 데이터베이스의 사용자 이름

        -   **DB\_USER** - 데이터베이스에 액세스하는 데 사용되는 사용자 이름

        -   **DB\_PASSWORD** - 사용자 암호

        이러한 항목을 변경한 후에는 **wp-config.php** 파일을 저장한 후 닫습니다.

    3.  [Azure 웹 사이트를 배포하는 방법][Azure 웹 사이트 배포] 정보를 사용하여 사용하려는 배포 방법을 사용하도록 설정한 다음 Azure 웹 사이트에 WordPress 백업을 배포합니다.

5.  WordPress 사이트가 배포되면 사이트에 대한 \*.azurewebsite.net URL을 사용하여 새 사이트에 액세스할 수 있어야 합니다.

### 사이트 구성

WordPress 사이트가 만들어지거나 마이그레이션되면 다음 정보를 사용하여 성능을 향상시키거나 추가 기능을 사용하도록 설정합니다.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">원하는 작업..</th>
<th align="left">사용 기능...</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>웹 사이트 모드, 크기 설정 및 확장 사용</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-scale/">웹 사이트를 확장하는 방법</a></td>
</tr>
<tr class="even">
<td align="left"><strong>영구 데이터베이스 연결 사용</strong>
<p>기본적으로 WordPress는 영구 데이터베이스 연결을 사용하지 않으므로 여러 번 연결된 후에 데이터베이스에 대한 연결이 조절될 수 있습니다.</p></td>
<td align="left"><ol>
<li><p><strong>wp-includes/wp-db.php</strong> 파일을 편집합니다.</p></li>
<li><p>다음 줄을 찾습니다.</p>
<p><code data-inline="1">$this-&gt;dbh = mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags );</code></p></li>
<li><p>앞의 줄을 다음 줄로 바꿉니다.</p>
<p><code>$this-&gt;dbh = mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); if ( false !== $error_reporting ) { /br/&gt;  error_reporting( $error_reporting ); } </code></p></li>
<li><p>다음 줄을 찾습니다.</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags ); </code></p></li>
<li><p>위의 줄을 다음 줄로 바꿉니다.</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); </code></p></li>
<li><p><strong>wp-includes/wp-db.php</strong> 파일을 저장하고 사이트를 다시 배포합니다.</p></li>
</ol>
<div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>참고:</h5><p>이러한 변경 내용은 WordPress가 업데이트될 때 덮어쓰여질 수 있습니다.</p><p>WordPress는 기본적으로 자동 업데이트되며, 이러한 자동 업데이트는 <strong>wp-config.php</strong> 파일을 편집하고 <code data-inline="1">define ( 'WP_AUTO_UPDATE_CORE', false );</code>를 추가하여 사용되지 않도록 설정할 수 있습니다.</p><p>업데이트를 처리하는 또 다른 방법은 <strong>wp-db.php</strong> 파일을 모니터링하고 파일이 업데이트될 때마다 위 수정 작업을 수행하는 WebJob을 사용하는 것입니다. 자세한 내용은 <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">WebJob 소개</a>(영문)를 참조하세요.</p></div></td>
</tr>
<tr class="odd">
<td align="left"><strong>성능 향상</strong></td>
<td align="left"><ul>
<li><p><a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">ARR 쿠키 사용 안 함</a> - 여러 웹 사이트 인스턴스에서 WordPress를 실행할 때 성능을 향상시킬 수 있습니다.</p></li>
<li><p>캐싱을 사용하도록 설정합니다. <a href="http://msdn.microsoft.com/en-us/library/azure/dn690470.aspx">Redis 캐시</a>(미리 보기)를 <a href="https://wordpress.org/plugins/redis-object-cache/">Redis 개체 캐시 WordPress 플러그 인</a>과 함께 사용하거나 <a href="http://azure.microsoft.com/en-us/gallery/store/">Azure Store</a>의 기타 캐싱 서비스 중 하나를 사용합니다.</p></li>
<li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Wincache로 WordPress를 더 빠르게 만드는 방법</a> - Wincache는 웹 사이트에 대해 기본적으로 사용되도록 설정되어 있습니다.</p></li>
<li><p><a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-scale/">Azure 웹 사이트를 확장</a>하고 <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB 고가용성 라우팅</a> 또는 <a href="http://www.mysql.com/products/cluster/">MySQL 클러스터 CGE</a> 사용</p></li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><strong>저장소에 Blob 사용</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/ko-kr/documentation/articles/storage-create-storage-account/">Azure 저장소 계정 만들기</a></p></li>
<li><p><a href="http://azure.microsoft.com/ko-kr/documentation/articles/cdn-how-to-use/">CDN(Content Distribution Network)를 사용</a>하여 Blob에 저장된 데이터를 지리적으로 분산하는 방법을 알아봅니다.</p></li>
<li><p><a href="https://wordpress.org/plugins/windows-azure-storage/">WordPress용 Azure 저장소 플러그 인</a>을 설치하고 구성합니다.</p>
<p>이 플러그 인의 자세한 설치 및 구성 정보에 대해서는 <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">사용자 가이드</a>(영문)를 참조하세요.</p></li>
</ol></td>
</tr>
<tr class="odd">
<td align="left"><strong>전자 메일 사용</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/">Azure Store를 사용하여 SendGrid 사용</a></p></li>
<li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">WordPress용 SendGrid 플러그 인 설치</a></p></li>
</ol></td>
</tr>
<tr class="even">
<td align="left"><strong>사용자 지정 도메인 이름 구성</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-custom-domain-name/">Azure 웹 사이트에서 사용자 지정 도메인 이름 사용</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>사용자 지정 도메인 이름에 HTTPS 사용</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-configure-ssl-certificate/">Azure 웹 사이트에 HTTPS 사용</a></td>
</tr>
<tr class="even">
<td align="left"><strong>사이트 부하 분산 또는 지리적으로 분산</strong></td>
<td align="left"><a href="http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/">Azure 트래픽 관리자로 트래픽 라우팅</a>(영문) 사용자 지정 도메인을 사용하는 경우 <a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-custom-domain-name/">Azure 웹 사이트에서 사용자 지정 도메인 이름 사용</a>에서 사용자 지정 도메인 이름에 트래픽 관리자를 사용하는 방법을 참조하세요.</td>
</tr>
<tr class="odd">
<td align="left"><strong>자동 웹 사이트 백업 사용</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-backup/">Azure 웹 사이트 백업</a></td>
</tr>
<tr class="even">
<td align="left"><strong>진단 로깅 사용</strong></td>
<td align="left"><a href="http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-enable-diagnostic-log/">Azure 웹 사이트에 진단 로그 사용</a></td>
</tr>
</tbody>
</table>

## [][]추가 리소스

-   [WordPress 최적화(영문)][WordPress 최적화(영문)]

-   [WordPress 사이트를 멀티 사이트로 변환(영문)][WordPress 사이트를 멀티 사이트로 변환(영문)]

-   [Azure용 ClearDB 업그레이드 마법사(영문)][Azure용 ClearDB 업그레이드 마법사(영문)]

-   [Azure 웹 사이트의 하위 폴더에 WordPress 호스트(영문)][Azure 웹 사이트의 하위 폴더에 WordPress 호스트(영문)]

-   [단계별 작업: Azure를 사용하여 WordPress 사이트 만들기][단계별 작업: Azure를 사용하여 WordPress 사이트 만들기](영문)

-   [Azure에 기존 WordPress 블로그 호스트(영문)][Azure에 기존 WordPress 블로그 호스트(영문)]

-   [WordPress에서 멋진 permalinks 사용(영문)][WordPress에서 멋진 permalinks 사용(영문)]

-   [Azure 웹 사이트에서 WordPress 블로그를 마이그레이션 및 실행하는 방법(영문)][Azure 웹 사이트에서 WordPress 블로그를 마이그레이션 및 실행하는 방법(영문)]

-   [Azure 웹 사이트에서 무료로 WordPress를 실행하는 방법(영문)][Azure 웹 사이트에서 무료로 WordPress를 실행하는 방법(영문)]

-   [2분 이내의 Azure WordPress(영문)][2분 이내의 Azure WordPress(영문)]

-   [WordPress 블로그를 Azure로 이동 - 1부: Azure에 WordPress 블로그 만들기][WordPress 블로그를 Azure로 이동 - 1부: Azure에 WordPress 블로그 만들기](영문)

-   [WordPress 블로그를 Azure로 이동 - 2부: 콘텐츠 전송][WordPress 블로그를 Azure로 이동 - 2부: 콘텐츠 전송](영문)

-   [WordPress 블로그를 Azure로 이동 - 3부: 사용자 지정 도메인 설정][WordPress 블로그를 Azure로 이동 - 3부: 사용자 지정 도메인 설정](영문)

-   [WordPress 블로그를 Azure로 이동 - 4부: 멋진 permalinks 및 URL 다시 쓰기 규칙][WordPress 블로그를 Azure로 이동 - 4부: 멋진 permalinks 및 URL 다시 쓰기 규칙](영문)

-   [WordPress 블로그를 Azure로 이동 - 5부: 하위 폴더에서 루트로 이동][WordPress 블로그를 Azure로 이동 - 5부: 하위 폴더에서 루트로 이동](영문)

-   [Azure 계정에서 WordPress 웹 사이트를 설정하는 방법(영문)][Azure 계정에서 WordPress 웹 사이트를 설정하는 방법(영문)]

-   [Azure에서 WordPress 지원(영문)][Azure에서 WordPress 지원(영문)]

-   [Azure의 WordPress 관련 팁(영문)][Azure의 WordPress 관련 팁(영문)]

  [WordPress]: http://www.microsoft.com/web/wordpress
  [Office]: http://blogs.office.com/
  [Bing]: http://blogs.bing.com/
  [아키텍처 및 계획]: #planning
  [방법]: #howto
  [추가 리소스]: #resources
  [Azure Store의 ClearDB]: http://www.cleardb.com/store/azure
  [Windows]: http://azure.microsoft.com/ko-kr/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
  [Linux]: http://azure.microsoft.com/ko-kr/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
  [ClearDB 가격 책정]: http://www.cleardb.com/pricing.view
  [PHP 버전 5.3, 5.4 및 5.5]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-php-configure/
  [단일 Azure 지역에 호스트된 Azure 웹 사이트 및 MySQL 데이터베이스]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
  [트래픽 관리자]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
  [Azure 웹 사이트, 다중 지역에 호스트, CDBR 고가용성 라우터를 사용하여 여러 지역의 MySQL로 라우팅]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
  [CDBR 고가용성 라우터]: http://www.cleardb.com/developers/cdbr/introduction
  [MySQL 클러스터 CGE]: http://www.mysql.com/products/cluster/
  [Redis 캐시]: http://msdn.microsoft.com/en-us/library/azure/dn690470.aspx
  [Memcache 클라우드]: http://azure.microsoft.com/en-us/gallery/store/garantiadata/memcached/
  [MemCachier]: http://azure.microsoft.com/en-us/gallery/store/memcachier/memcachier/
  [Azure Store]: http://azure.microsoft.com/en-us/gallery/store/
  [Azure 웹 사이트, 다중 지역에 호스트, MySQL용 CDBR 고가용성 라우터 사용, 관리되는 캐시, Blob 저장소 및 CDN 포함]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
  [CDN(Content Distribution Network)]: http://azure.microsoft.com/ko-kr/documentation/articles/cdn-how-to-use/
  [Blob 저장소 사용을 위한 WordPress 플러그 인]: https://wordpress.org/plugins/windows-azure-storage/
  [SendGrid]: http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/
  [SendGrid 사용을 위한 WordPress 플러그 인]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
  [Azure 웹 사이트를 포함하는 사용자 지정 도메인 이름]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-custom-domain-name/
  [Azure 웹 사이트의 HTTPS 지원]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-configure-ssl-certificate/
  [Azure 웹 사이트에 대한 스테이징된 게시 지원]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-staged-publishing/
  [Azure 웹 사이트의 진단 로깅]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-enable-diagnostic-log/
  [Azure 웹 사이트 모니터링]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-monitor/
  [Azure 웹 사이트 배포]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-deploy/
  [Azure 웹 사이트 백업]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-backup/
  [Azure 웹 사이트 복원]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-restore/
  [웹 사이트 규모 및 모드 용량을 비롯한 가격 책정 세부 정보]: https://azure.microsoft.com/en-us/pricing/details/web-sites/
  [Azure 웹 사이트 확장]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-scale/
  [WordPress 내보내기]: http://en.support.wordpress.com/export/
  [WordPress 가져오기 도구 플러그 인]: http://wordpress.org/plugins/wordpress-importer/
  [사이트]: http://codex.wordpress.org/WordPress_Backups
  [데이터베이스를 백업]: http://codex.wordpress.org/Backing_Up_Your_Database
  [Azure의 갤러리에서 WordPress 웹 사이트 만들기]:  "http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-php-web-site-gallery/"
  [기존 WordPress 사이트 마이그레이션]: #migrate
  [새 WordPress 사이트 만들기]: #create
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [Velvet Blues Update URL 플러그 인]: https://wordpress.org/plugins/velvet-blues-update-urls/
  [MySQL 워크벤치]: http://www.mysql.com/products/workbench/
  [WordPress 데이터베이스 스크립트 검색 및 바꾸기]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
  [WebJob 소개]: http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
  [ARR 쿠키 사용 안 함]: http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx
  [Redis 개체 캐시 WordPress 플러그 인]: https://wordpress.org/plugins/redis-object-cache/
  [Wincache로 WordPress를 더 빠르게 만드는 방법]: http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/
  [Azure 저장소 계정 만들기]: http://azure.microsoft.com/ko-kr/documentation/articles/storage-create-storage-account/
  [사용자 가이드]: http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx
  []: resources
  [WordPress 최적화(영문)]: http://codex.wordpress.org/WordPress_Optimization
  [WordPress 사이트를 멀티 사이트로 변환(영문)]: http://azure.microsoft.com/ko-kr/documentation/articles/web-sites-php-convert-wordpress-multisite/
  [Azure용 ClearDB 업그레이드 마법사(영문)]: http://www.cleardb.com/store/azure/upgrade
  [Azure 웹 사이트의 하위 폴더에 WordPress 호스트(영문)]: http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx
  [단계별 작업: Azure를 사용하여 WordPress 사이트 만들기]: http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx
  [Azure에 기존 WordPress 블로그 호스트(영문)]: http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx
  [WordPress에서 멋진 permalinks 사용(영문)]: http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress
  [Azure 웹 사이트에서 WordPress 블로그를 마이그레이션 및 실행하는 방법(영문)]: http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/
  [Azure 웹 사이트에서 무료로 WordPress를 실행하는 방법(영문)]: http://architects.dzone.com/articles/how-run-wordpress-azure
  [2분 이내의 Azure WordPress(영문)]: http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/
  [WordPress 블로그를 Azure로 이동 - 1부: Azure에 WordPress 블로그 만들기]: http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1
  [WordPress 블로그를 Azure로 이동 - 2부: 콘텐츠 전송]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content
  [WordPress 블로그를 Azure로 이동 - 3부: 사용자 지정 도메인 설정]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain
  [WordPress 블로그를 Azure로 이동 - 4부: 멋진 permalinks 및 URL 다시 쓰기 규칙]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules
  [WordPress 블로그를 Azure로 이동 - 5부: 하위 폴더에서 루트로 이동]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root
  [Azure 계정에서 WordPress 웹 사이트를 설정하는 방법(영문)]: http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/
  [Azure에서 WordPress 지원(영문)]: http://www.johnpapa.net/wordpress-on-azure/
  [Azure의 WordPress 관련 팁(영문)]: http://www.johnpapa.net/azurecleardbmysql/
