<properties
   pageTitle="웹앱에서 DevOps 환경을 효과적으로 사용하기"
   description="배포 슬롯을 사용하여 응용 프로그램에 여러 개발 환경을 설정 및 관리하는 방법을 살펴봅니다."
   services="app-service\web"
   documentationCenter=""
   authors="sunbuild"
   manager="yochayk"
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="web"
   ms.date="09/24/2015"
   ms.author="sumuth"/>

# 웹 앱에서 DevOps 환경을 효과적으로 사용하기

이 문서에서는 개발, 스테이징, QA, 프로덕션 등, 다양한 응용 프로그램 버전에 대한 웹 응용 프로그램 배포의 설정 및 관리를 보여줍니다. 응용 프로그램의 각 버전을 배포 프로세스에서 특정 요구에 대한 개발 환경으로 고려할 수 있습니다. 예를 들어, 변경 내용을 프로덕션에 푸시하기 전에 개발자 팀에서 QA 환경을 사용하여 응용 프로그램의 품질을 테스트할 수 있습니다. 여러 개발 환경을 설정은 리소스(계산, 웹앱, 데이터베이스 캐시 등)의 추적과 관리가 필요하기 때문에 까다로울 수 있습니다. 이러한 환경 전체에서 한 환경으로부터 다른 환경으로 콘텐츠를 배포합니다.

## 비 프로덕션 환경(스테이지, 개발, QA) 설정
프로덕션 웹앱을 가동 및 실행한 후 다음 단계는 비 프로덕션 환경을 만드는 것입니다. 배포 슬롯을 사용하려면 **표준** 또는 **프리미엄** 서비스 계획 모드에서 실행 중이어야 합니다. 배포 슬롯은 실제로 고유한 호스트 이름이 있는 라이브 웹 앱입니다. 웹 앱 콘텐츠 및 구성 요소는 프로덕션 슬롯을 포함하여 두 배포 슬롯 간에 교환될 수 있습니다. 응용 프로그램을 배포 슬롯에 배포하면 다음과 같은 이점이 있습니다.

1. 프로덕션 슬롯과 교환하기 전에 준비 배포 슬롯에서 웹 앱 변경 사항의 유효성을 검사할 수 있습니다.
2. 먼저 슬롯으로 웹 앱을 배포하고 프로덕션으로 교환하기 때문에 프로덕션으로 교환되기 전에 슬롯에 있는 모든 인스턴스가 준비되어 있는 상태입니다. 따라서 웹 앱을 배포할 때 가동 중지가 발생하지 않습니다. 트래픽 리디렉션은 중단 없이 원활하게 수행되며 교환 작업으로 인해 삭제되는 요청은 없습니다. 사전 교환 유효성 검사가 필요하지 않은 경우 [자동 교환](web-sites-staged-publishing/#configure-auto-swap-for-your-web-app)을 구성하여 이 전체 워크플로를 자동화할 수 있습니다.
3. 교환 후에는 이전의 준비된 웹 앱이 들어 있던 슬롯 안에 이전의 프로덕션 웹 앱이 들어갑니다. 프로덕션 슬롯과 교환한 변경 내용이 예상과 다른 경우 같은 교환 작업을 즉시 수행하여 "마지막 양호 웹앱"으로 돌아갈 수 있습니다.

스테이징 배포 슬롯을 설정하려면 [Azure 앱 서비스에서 웹 앱에 대한 스테이징 환경 설정](web-sites-staged-publishing)을 참조하세요. 모든 환경에는 자체 리소스 세트가 포함되어야 합니다. 예를 들어, 데이터베이스를 사용하는 웹앱이라면 프로덕션과 스테이징 웹앱이 다른 데이터베이스를 사용해야 합니다. 스테이징 개발 환경 설정을 위해 데이터베이스, 저장소 또는 캐시 등의 스테이징 개발 환경 리소스를 추가합니다.

## 여러 개발 환경을 사용하는 예

모든 프로젝트는 최소한 두 환경, 개발 및 프로덕션 환경이 있는 소스 코드 관리를 따라야 합니다. 그러나 콘텐츠 관리 시스템, 응용 프로그램 프레임워크 등을 사용할 경우 응용 프로그램이 기본적으로 이 시나리오를 지원하지 않는 문제가 발생할 수 있습니다. 아래에서 논의할 일부 인기 프레임워크에서 그렇습니다. CMS/프레임워크 작업에서는 다음과 같은 여러 질문이 떠오르게 됩니다.

1. 다른 환경으로 분리하는 방법
2. 변경 가능하고 프레임워크 버전 업데이트에 영향이 없는 파일
3. 환경별 구성의 관리 방법
4. 모듈/플러그인 버전 업데이트, 핵심 프레임워크 버전 업데이트 관리 방법

여러 가지 방법으로 프로젝트에 대 해 여러 환경을 설정할 수 있으며 아래의 예에서는 그 방법 중 하나인 관련 응용 프로그램을 설명합니다.

### WordPress
이 섹션에서는 WordPress에 슬롯을 사용하여 배포 워크플로를 설정하는 방법을 학습합니다. 대부분의 CMS 솔루션처럼 WordPress도 여러 개발 환경 작업을 기본적으로 지원하지 않습니다. 앱 서비스 웹앱에는 코드 외부에서 구성 설정을 더 쉽게 저장할 수 있는 몇 가지 기능이 있습니다.

스테이징 슬롯을 만들기 전에 여러 환경을 지원하도록 응용 프로그램 코드를 설정합니다. WordPress에서 여러 환경을 지원 하도록 로컬 개발 웹 앱에서 `wp-config.php`를 편집하여 파일 시작 부분에 다음 코드를 추가합니다. 그러면 응용 프로그램이 선택된 환경을 기초로 정확한 구성을 선택할 수 있습니다.

```
// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
    // local development
    $config_file = 'config/wp-config.local.php';
}
elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
      //single file for all azure development environments
      $config_file = 'config/wp-config.azure.php';
}
$path = dirname(__FILE__) . '/';
if (file_exists($path . $config_file)) {
// include the config file if it exists, otherwise WP is going to fail
require_once $path . $config_file;
```

웹앱 루트에 이름이 `config`인 폴더를 만들고 각각 Azure와 로컬 환경을 나타내는 `wp-config.azure.php` 및 `wp-config.local.php` 등의 두 파일을 추가합니다.

`wp-config.local.php`에 다음을 복사합니다.

```
<?php
// MySQL settings
/** The name of the database for WordPress */

define('DB_NAME', 'yourdatabasename');

/** MySQL database username */
define('DB_USER', 'yourdbuser');

/** MySQL database password */
define('DB_PASSWORD', 'yourpassword');

/** MySQL hostname */
define('DB_HOST', 'localhost');
/**
 * For developers: WordPress debugging mode.
 * * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 */
define('WP_DEBUG', true);

//Security key settings
define('AUTH_KEY',         'put your unique phrase here');
define('SECURE_AUTH_KEY',  'put your unique phrase here');
define('LOGGED_IN_KEY',    'put your unique phrase here');
define('NONCE_KEY',        'put your unique phrase here');
define('AUTH_SALT',        'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT',   'put your unique phrase here');
define('NONCE_SALT',       'put your unique phrase here');

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix  = 'wp_';
```

위의 보안 키를 설정하면 웹앱 해킹을 차단하는 데 도움이 되므로 고유한 값을 사용합니다. 위에서 언급한 보안 키에 대한 문자열을 생성해야 할 경우 이 [링크](https://api.wordpress.org/secret-key/1.1/salt)를 통해 자동 생성기로 이동하여 새 키/값을 만들 수 있습니다.

`wp-config.azure.php`에서 다음 코드로 바꿉니다.


```
<?php
// MySQL settings
/** The name of the database for WordPress */

define('DB_NAME', getenv('DB_NAME'));

/** MySQL database username */
define('DB_USER', getenv('DB_USER'));

/** MySQL database password */
define('DB_PASSWORD', getenv('DB_PASSWORD'));

/** MySQL hostname */
define('DB_HOST', getenv('DB_HOST'));

/**
* For developers: WordPress debugging mode.
*
* Change this to true to enable the display of notices during development.
* It is strongly recommended that plugin and theme developers use WP_DEBUG
* in their development environments.
* Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
* do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
* with WP_DEBUG_LOG so that errors are not displayed on the page */

*/
define('WP_DEBUG', getenv('WP_DEBUG'));
define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
define('WP_DEBUG_DISPLAY',false);

//Security key settings
/** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
define('AUTH_KEY' ,getenv('DB_AUTH_KEY'));
define('SECURE_AUTH_KEY',  getenv('DB_SECURE_AUTH_KEY'));
define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
define('NONCE_KEY', getenv('DB_NONCE_KEY'));
define('AUTH_SALT',  getenv('DB_AUTH_SALT'));
define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
define('LOGGED_IN_SALT',   getenv('DB_LOGGED_IN_SALT'));
define('NONCE_SALT',   getenv('DB_NONCE_SALT'));

/**
* WordPress Database Table prefix.
*
* You can have multiple installations in one database if you give each a unique
* prefix. Only numbers, letters, and underscores please!
*/
$table_prefix  = getenv('DB_PREFIX');
```

#### 상대 경로 사용
마지막으로 WordPress 앱이 상대 경로를 사용하도록 허용합니다. WordPress는 URL 정보를 데이터베이스에 저장합니다. 이렇게 하면 로컬에서 스테이지 또는 스테이지에서 프로덕션 환경으로 이동할 때마다 데이터를 업데이트해야 하기 때문에, 한 환경의 콘텐츠를 다른 환경으로 이동하는 것이 더 어려워집니다. 한 환경에서 다른 환경으로 배포할 때마다 매번 데이터베이스를 배포하는 데 따른 문제의 위험을 줄이기 위해, WordPress 관리자 대시보드를 사용하거나 [여기](https://downloads.wordpress.org/plugin/root-relative-urls.zip)에서 직접 다운로드할 수 있는 [상대 루트 링크 플러그인](https://wordpress.org/plugins/root-relative-urls/)을 사용합니다.

`wp-config.php` 파일에서 `That's all, stop editing!` 주석 앞에 다음 항목을 추가합니다.

```
    define('WP_HOME', 'http://' . $_SERVER ['HTTP_HOST']);
    define('WP_SITEURL', 'http://' . $_SERVER ['HTTP_HOST']);
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);
```
WordPress 관리자 대시보드에서 `Plugins` 메뉴를 통해 플러그인을 활성화합니다. WordPress 앱에 대한 고정 링크 설정을 저장합니다.

#### 최종 `wp-config.php` 파일
WordPress 핵심 업데이트는 `wp-config.php` , `wp-config.azure.php` 및 `wp-config.local.php` 파일에 영향이 없습니다. 결과적으로 `wp-config.php` 파일은 다음과 유사하게 됩니다.

```
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 *
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web web app, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
    $config_file = 'config/wp-config.local.php';
}
elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
    $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__) . '/';
if (file_exists($path . $config_file)) {
    // include the config file if it exists, otherwise WP is going to fail
    require_once $path . $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://' . $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://' . $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
	define('ABSPATH', dirname(__FILE__) . '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH . 'wp-settings.php');
```

#### 스테이징 환경 설정
Azure 웹에서 이미 WordPress 웹앱을 실행 중이라고 가정하고, [Azure 포털](http://portal.azure.com)에 로그인한 다음 WordPress 웹앱으로 이동합니다. 앱이 없으면 마켓플레이스에서 만들 수 있습니다. 자세히 알아보려면 [여기](web-sites-php-web-site-gallery)를 클릭하세요. 설정 -> 배포 슬롯 -> 추가를 클릭하여 이름 스테이지로 배포 슬롯을 만듭니다. 배포 슬롯은 위에서 만든 기본 웹앱과 동일한 리소스를 공유하는 다른 웹 응용 프로그램입니다.

![스테이지 배포 슬롯 만들기](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

다른 MySQL 데이터베이스, `wordpress-stage-db`를 리소스 그룹 `wordpressapp-group`에 추가합니다.

 ![리소스 그룹에 MySQL 데이터베이스 추가](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

스테이지 배포 슬롯에 대한 연결 문자열을 새로 만든 데이터베이스 `wordpress-stage-db`를 가리키도록 업데이트합니다. 프로덕션 웹앱 `wordpressapp-group`과 스테이징 웹앱 `wordpressprodapp-stage`는 서로 다른 데이터베이스를 가리켜야 합니다.

#### 환경 관련 앱 설정 구성
개발자는 앱 설정이라고 하는 웹앱과 연결된 구성 정보의 일부로 Azure에 키-값 문자열 쌍을 저장할 수 있습니다. 런타임에서 앱 서비스 웹앱이 사용자에 대해 자동으로 이 값을 검색하고 웹앱을 실행하는 코드에서 사용할 수 있게 합니다. 보안 관점에서 보면, 데이터베이스 연결 문자열 및 암호와 같은 중요 정보가 `wp-config.php` 등의 파일에 일반 텍스트로 표시되지 않기 때문에 긍정적인 부수 효과가 있습니다.

아래에서 정의한 이 프로세스는 WordPress 앱에 대해 파일 변경 내용과 데이터베이스 변경 내용이 모두 포함된 작업일 때 유용합니다.

- WordPress 버전 업그레이드
- 플러그인 새로 추가, 편집 또는 업그레이드
- 테마 새로 추가, 편집 또는 업그레이드

다음에 대한 앱 설정 구성

- 데이터베이스 정보
- WordPress 로깅 설정/해제
- WordPress 보안 설정

![Wordpress 웹앱에 대한 앱 설정](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

프로덕션 웹앱 및 스테이지 슬롯에 대해 다음 설정을 추가했는지 확인합니다. 프로덕션 웹앱과 스테이징 웹앱은 서로 다른 데이터베이스를 사용해야 합니다. WP\_ENV를 제외한 모든 설정 매개 변수에 대해 **슬롯 설정** 확인란을 선택 취소합니다. 그러면 웹앱에 대한 구성과, 파일 내욜 및 데이터베이스가 교체됩니다. **슬롯 설정**을 **선택**한 경우 교환 작업을 수행할 때 웹앱의 앱 설정, 연결 문자열 구성이 환경 간을 이동하지 않으므로, 남아 있는 데이터베이스 변경 내용이 프로덕션 웹앱을 중단할 때 보이지 않게 됩니다.

WebMatrix나, FTP , Git, PhpMyAdmin 등과 같은 사용자 선택 도구를 사용하여 웹앱 및 데이터베이스를 스테이징하는 개발 환경 웹앱을 배포합니다.

![WordPress 웹앱에 Web Matrix 게시 대화 상자](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

스테이징 웹앱을 찾아 테스트합니다. 웹앱 테마를 업데이트하는 시나리오의 스테이징 웹앱을 가정해 보겠습니다.

![슬롯 교환 전에 스테이징 웹앱 찾아보기](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 모든 값이 올바르면 스테이징 웹앱에서 **교환** 버튼을 클릭하여 콘텐츠를 프로덕션 환경으로 이동합니다. 이 경우 모든 **스왑** 작업 중에 환경 간에 웹앱과 데이터베이스를 교환합니다.

![Wordpress에 대한 변경 내용 미리 보기 교환](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

 >[AZURE.NOTE]> 파일 푸시만 필요한 시나리오의 경우(데이터베이스 업데이트 없음), 교환을 수행하기 전에 Azure 포털 내의 웹앱 블레이드에서 모든 데이터베이스 관련 *앱 설정* 및 *연결 문자열 설정*에 대해 **슬롯 설정**을 **선택**합니다. 이 경우 **교환**을 수행할 때DB\_NAME, DB\_HOST, DB\_PASSWORD, DB\_USER, 기본 연결 문자열 설정이 변경 내용 미리 보기에 표시되지 않아야 합니다. 이 시점에서 **교환** 작업을 완료하면 WordPress 웹앱에 업데이트 파일**만** 있습니다.

교환 수행 전 프로덕션 WordPress 웹앱 ![슬롯 교환 전 프로덕션 웹앱](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

교환 작업 후 프로덕션 웹앱의 테마가 업데이트되었습니다.

![슬롯 교환 후 프로덕션 웹앱](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

**롤백**이 필요한 상황에서 프로덕션 웹앱 설정으로 이동하여 **교환** 버튼을 클릭하면 웹앱과 데이터베이스가 프로덕션에서 스테이징 슬롯으로 교환됩니다. 특정 시점에 **교환** 작업에 데이터베이스 변경이 포함된 경우 다음 번에 스테이징 웹앱을 재배포할 때 이전 프로덕션이나 스테이지 데이터베이스일 수 있는 스테이징 웹앱에서 가리키는 현재 데이터베이스에 데이터베이스 변경 내용을 배포해야 한다는 점을 기억해야 합니다.

#### 요약
데이터베이스가 있는 모든 응용 프로그램에 대해 프로세스를 일반화하려면

1. 로컬 환경에 응용 프로그램 설치
2. 환경 특정 구성(로컬 및 Azure 웹앱) 포함
3. 앱 서비스 웹앱에서 환경 설정 - 스테이징, 프로덕션
4. Azure에서 이미 실행되는 프로덕션 응용 프로그램이 있는 경우 프로덕션 컨텐츠(파일/코드 + 데이터베이스)를 로컬 및 스테이징 환경에 동기화합니다.
5. 로컬 환경에서 응용 프로그램 개발
6. 프로덕션 웹앱을 유지 관리나 잠김 모드에 배치하고 프로덕션의 데이터베이스 콘텐츠를 스테이징 및 개발 환경과 동기화
7. 스테이징 환경에 배포 및 테스트
8. 프로덕션 환경에 배포
9. 4-6단계 반복

### Umbraco
이 섹션에서는 Umbraco CMS에서 사용자 지정 모듈을 사용하여 여러 DevOps 환경으로부터 배포하는 방법을 살펴봅니다. 이 예에서는 여러 개발 환경을 관리하는 서로 다른 접근 방법을 제시합니다.

[Umbraco CMS](http://umbraco.com/)는 많은 개발자들이 사용하는 인기 .NET CMS 솔루션 중 하나로, 개발에서 스테이징, 프로덕션 환경으로 배포하는 [Courier2](http://umbraco.com/products/more-add-ons/courier-2) 모듈을 제공합니다. Visual Studio나 WebMatrix를 사용 하 여 Umbraco CMS 웹앱용 로컬 개발 환경을 쉽게 만들 수 있습니다.

1. Visual Studio를 통해 Umbraco 웹앱을 만들려면 [여기](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget)를 클릭합니다.
2. WebMatrix를 통해 Umbraco 웹앱을 만들려면 [여기](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix)를 클릭합니다.

응용 프로그램의 `install` 폴더는 항상 제거하고, 스테이지 또는 프로덕션 웹앱에 업로드하지 않아야 합니다. 이 자습서에서는 WebMatrix를 사용합니다.

#### 스테이징 환경 설정
Umbraco CMS 웹앱에 대해 위에서 언급한 배포 슬롯을 만듭니다. 이미 Umbraco CMS 웹앱을 가동 및 실행 중이라고 가정합니다. 없으면 마켓플레이스에서 만들 수 있습니다.

새로 만든 데이터베이스 **umbraco-stage-db**를 가리키도록 스테이지 배포 슬롯에 대한 연결 문자열을 업데이트합니다. 프로덕션 웹앱(umbraositecms-1)과 스테이징 웹앱(umbracositecms-1-stage)은 다른 데이터베이스를 가리켜야 **합니다**.

![새 스테이징 데이터베이스를 통해 스테이징 웹앱의 연결 문자열 업데이트](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

배포 슬롯 **스테이지**에 대해 **게시 설정 가져오기**를 클릭합니다. 그러면 Visual Studio나 Web Matrix가 로컬 개발 웹앱의 응용 프로그램을 Azure 웹앱에 게시하는 데 필요한 모든 정보가 담긴 게시 설정 파일을 다운로드합니다.

 ![스테이징 웹앱의 게시 설정 가져오기](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- **WebMatrix** 또는 **Visual Studio**에서 로컬 개발 웹앱을 엽니다. 이 자습서에서는 Web Matrix를 사용하며 먼저 스테이징 웹앱에 대한 게시 설정 파일을 가져와야 합니다.

![Web Matrix를 사용하여 Umbraco 게시 설정 가져오기](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- 대화 상자에서 변경 내용을 검토하고 로컬 웹앱을 Azure 웹앱 *umbracositecms-1-stage*에 배포합니다. 파일을 스테이징 웹앱에 직접 배포할 경우 `~/app_data/TEMP/` 폴더의 모든 파일을 생략합니다. 이러한 파일은 스테이지 웹앱을 처음 시작할 때 다시 생성되기 때문입니다. `~/app_data/umbraco.config` 파일도 다시 생성되므로 생략해야 합니다.

![Web Matrix에서 게시 설정 검토](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- Umbraco 로컬 웹앱을 성공적으로 게시한 후 스테이징 웹앱을 탐색하고 몇 가지 테스트를 실행하여 문제를 제거합니다.

#### Courier2 배포 모듈 설정
[Courier2](http://umbraco.com/products/more-add-ons/courier-2) 모듈을 사용하면 콘텐츠, 스타일시트, 개발 모듈 및 기타 항목을 스테이징 웹앱에서 마우스 오른쪽 단추를 클릭하여 간단하게 프로덕션 웹앱에 푸시할 수 있으므로, 업데이트를 배포할 때 배포 부담이 줄고 프로덕션 웹앱의 손상 위험을 낮출 수 있습니다. 도메인 `*.azurewebsites.net` 및 사용자 지정 도메인(http://abc.com이라 가정)에 대해 Courier2 라이선스를 구매합니다. 라이선스를 구매한 후에는 다운로드한 라이선스(.LIC 파일)를 `bin` 폴더에 저장합니다.

![라이선스 파일을 bin 폴더에 넣기](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

Courier2 패키지를 [여기](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/)에서 다운로드합니다. 스테이지 웹앱 http://umbracocms-site-stage.azurewebsites.net/umbraco에 로그온하여 **개발자** 메뉴를 클릭하고 **패키지**를 선택합니다. 로컬 패키지 **설치**를 클릭합니다.

![Umbraco 패키지 설치 관리자](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

설치 관리자를 사용하여 courier2 패키지를 업로드합니다.

![Courier 모듈에 대한 패키지 업로드](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

구성하려면 웹앱의 **Config** 폴더 아래에 있는 courier.config 파일을 업데이트해야 합니다.

```xml
<!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1.azurewebsites.net</url>
            <user>0</user>
            <!--<login>user@email.com</login> -->
            <!-- <password>user_password</password>-->
           <!-- <passwordEncoding>Clear</passwordEncoding>-->
           </repository>
  </repositories>
 ```

`<repositories>`에서 프로덕션 사이트 URL 및 사용자 정보를 입력하세요. 기본 Umbraco 멤버 자격 공급자를 사용 중인 경우 <user> 섹션에서 관리 사용자의 ID를 추가하세요. 사용자 정의 Umbraco 멤버 자격 공급자를 사용 중인 경우 `<login>`,`<password>`을(를) Courier2 모듈에 사용하세요.  자세한 정보는 Courier 모듈 [설명서](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation) 를 확인하세요.

마찬가지로, 여기에 표시된 대로 프로덕션 사이트에 Courier 모듈을 설치하고 각 courier.config 파일에서 스테이지 웹 앱에 연결하도록 구성하세요.

```xml
  <!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1-stage.azurewebsites.net</url>
            <user>0</user>
           </repository>
  </repositories>
```

Umbraco CMS 웹앱 대시보드에서 Courier2 탭을 클릭하고 위치를 선택합니다. `courier.config`에서 설명한 대로 리포지토리 이름이 표시됩니다. 프로덕션과 스테이징 웹앱 모두에 대해 이 작업을 수행합니다.

![대상 웹앱 리포지토리 보기](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

이제 스테이징 사이트의 콘텐츠를 프로덕션 사이트로 배포합니다. 콘텐츠 위치로 이동하여 기존 페이지를 선택하거나 새 페이지를 만듭니다. 페이지 제목이 **Getting Started - new**로 바뀐 내 웹앱에서 기존 페이지를 선택합니다. 그런 다음 **저장 및 게시**를 클릭합니다.

![페이지 제목 변경 및 게시](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

이제 수정된 페이지를 선택하고 *마우스 오른쪽 단추로 클릭*하여 모든 옵션을 확인합니다. **Courier**를 클릭하고 배포 대화 상자를 봅니다. **배포**를 클릭하여 배포를 시작합니다.

![Courier 모듈 배포 대화 상자](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

변경 내용을 검토하고 계속을 클릭합니다.

![Courier 모듈 배포 대화 상자 변경 내용 검토](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

배포 로그에 배포 성공 여부가 표시됩니다.

 ![Courier 모듈에서 배포 로그 보기](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

프로덕션 웹앱을 탐색하여 변경 내용이 반영되었는지 확인합니다.

 ![프로덕션 웹앱 찾아보기](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Courier 사용 방법에 대한 자세한 내용은 설명서를 검토합니다.

#### Umbraco CMS 버전을 업그레이드 하는 방법

Courier는 Umbraco CMS의 한 버전을 다른 버전으로 업그레이드하는 배포에는 도움이 되지 않습니다. Umbraco CMS 버전을 업그레이드할 때는 사용자 지정 모듈 또는 타사 모듈과 Umbraco 코어 라이브러리와의 비호환성을 확인해야 합니다. 모범 사례

1. 업그레이드 전에는 항상 웹앱과 데이터베이스를 백업합니다. Azure 웹앱에서는 백업 기능을 사용하여 웹 사이트 자동 백업을 설정하고 필요한 경우 복원 기능을 통해 사이트를 복원할 수 있습니다. 자세한 내용은 [웹앱 백업 방법](web-sites-backup) 및 [웹앱 복원 방법](web-sites-restore)을 참조하세요.

2. 사용 중인 타사 패키지가 업그레이드하려는 버전과 호환되는지 확인합니다. 패키지 다운로드 페이지에서 Umbraco CMS 버전과의 프로젝트 호환성을 검토합니다.

로컬로 웹앱을 업그레이드하는 방법에 대한 자세한 내용은 [여기](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general)서 설명하는 지침에 따릅니다.

로컬 개발 사이트를 업그레이드한 후에는 변경 내용을 스테이징 웹앱에 게시합니다. 응용 프로그램을 테스트하고 모든 내용이 양호하면 **교환** 단추를 사용하여 스테이징 사이트를 프로덕션 웹앱으로 **교환**합니다. **교환** 작업을 수행하면 웹앱 구성에 영향을 미칠 변경 내용을 확인할 수 있습니다. 이 **교환** 작업을 통해 웹앱과 데이터베이스를 교환합니다. 즉 교환한 후에는 프로덕션 웹앱이 umbraco-stage-db 데이터베이스를, 스테이징 웹앱이 umbraco-prod-db 데이터베이스를 가리키게 됩니다.

![Umbraco CMS 배포를 위한 교환 미리보기](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

교환은 웹앱과 데이터베이스에 모두 혜택이 있습니다. 1. 응용 프로그램에 문제가 있는 경우 다른 **교환**을 통해 웹앱의 이전 버전으로 롤백할 수 있습니다. 2. 업그레이드하려면 스테이징 웹앱의 파일과 데이터베이스를 프로덕션 웹앱과 데이터베이스에 배포해야 합니다. 파일 및 데이터베이스를 배포할 때는 여러 가지가 잘못될 수 있습니다. 슬롯 **교환** 기능을 사용하면 업그레이드 중의 중단 시간을 줄이고 변경 내용 배포 시 발생할 수 있는 실패 위험을 낮출 수 있습니다. 3. [프로덕션에서 테스트](http://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/) 기능을 사용하여 **A/B 테스트**를 수행할 수 있습니다.

이 예에서는 전체 환경에서 배포를 관리하기 위해 Umbraco Courier 모듈과 유사한 사용자 지정 모듈을 구축할 수 있는 플랫폼 유연성을 보여줍니다.

## 참조
[Azure 앱 서비스를 사용하여 Agile 소프트웨어 개발](app-service-agile-software-development)

[Azure 앱 서비스에서 웹 앱에 대한 스테이징 환경 설정](web-sites-staged-publishing)

[비 프로덕션 배포 슬롯에 대한 웹 액세스를 차단하는 방법](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

<!---HONumber=AcomDC_1203_2015-->