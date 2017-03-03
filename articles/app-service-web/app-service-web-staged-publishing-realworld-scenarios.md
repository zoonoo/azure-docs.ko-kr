---
title: "웹앱에서 효과적으로 DevOps 환경 사용 | Microsoft Docs"
description: "배포 슬롯을 사용하여 응용 프로그램에 여러 개발 환경을 설정 및 관리하는 방법을 살펴봅니다."
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 16a594dc-61f5-4984-b5ca-9d5abc39fb1e
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 385eb87ec32f5f605b28cc8c76b1c89c7e90bfec
ms.openlocfilehash: 5284022ea473db893800b0f64b5bf4f811d994aa
ms.lasthandoff: 12/20/2016


---
# <a name="use-devops-environments-effectively-for-your-web-apps"></a>웹 앱에서 DevOps 환경을 효과적으로 사용하기
이 문서에서는 여러 버전의 응용 프로그램이 개발, 준비, QA 및 프로덕션과 같이 다양한 환경에 있을 때 웹 응용 프로그램 배포를 설정하고 관리하는 방법에 대해 설명합니다. 응용 프로그램의 각 버전은 배포 프로세스의 특정 목적을 위한 개발 환경으로 간주될 수 있습니다. 예를 들어 개발자는 QA 환경을 사용하여 변경 내용을 프로덕션에 적용하기 전에 응용 프로그램의 품질을 테스트할 수 있습니다.
코드를 추적하고 리소스(계산, 웹앱, 데이터베이스, 캐시 등)를 관리하고 여러 환경에 코드를 배포해야 하기 때문에 여러 개발 환경이 힘든 과제일 수 있습니다.

## <a name="set-up-a-non-production-environment-stage-dev-qa"></a>비프로덕션 환경(스테이지, 개발, QA) 설정
프로덕션 웹앱을 작동 및 실행한 후 다음 단계는 비프로덕션 환경을 만드는 것입니다. 배포 슬롯을 사용하려면 Standard 또는 Premium Azure App Service 계획 모드에서 실행해야 합니다. 배포 슬롯은 자체 호스트 이름을 갖춘 라이브 웹앱입니다. 웹앱 콘텐츠 및 구성 요소는 프로덕션 슬롯을 포함하여 두 배포 슬롯 간에 교환될 수 있습니다. 응용 프로그램을 배포 슬롯에 배포하면 다음과 같은 이점이 있습니다.

- 프로덕션 슬롯으로 앱을 교환하기 전에 스테이징 배포 슬롯에서 웹앱 변경 내용의 유효성을 검사할 수 있습니다.
- 먼저 웹앱을 슬롯에 배포한 다음 프로덕션으로 교환하면 프로덕션 환경으로 교환되기 전에 슬롯의 모든 인스턴스가 준비됩니다. 이 프로세스는 웹앱을 배포할 때 가동 중지가 발생하지 않습니다. 트래픽 리디렉션은 중단 없이 원활하게 수행되며 교환 작업으로 인해 삭제되는 요청은 없습니다. 사전 교환 유효성 검사가 필요하지 않을 때 [자동 교환](web-sites-staged-publishing.md#configure-auto-swap)을 구성하면 이 워크플로 전체를 자동화할 수 있습니다.
- 교환 후에는 이전에 준비된 웹앱이 있던 슬롯에 이전의 프로덕션 웹앱이 들어갑니다. 프로덕션 슬롯으로 교환된 변경 내용이 예상한 대로 변경되지 않은 경우 동일한 교환을 즉시 수행하여 "마지막으로 알려진 양호한" 웹앱을 다시 가져올 수 있습니다.

스테이징 배포 슬롯을 설정하려면 [Azure App Service에서 웹앱에 대한 스테이징 환경 설정](web-sites-staged-publishing.md)을 참조하세요. 모든 환경에는 고유한 리소스 집합이 있어야 합니다. 예를 들어 웹앱에서 데이터베이스를 사용하는 경우 프로덕션 웹앱과 스테이징 웹앱은 서로 다른 데이터베이스를 사용해야 합니다. 스테이징 개발 환경을 설정하려면 데이터베이스, 저장소 또는 캐시와 같은 스테이징 개발 환경 리소스를 추가합니다.

## <a name="examples-of-using-multiple-development-environments"></a>여러 개발 환경을 사용하는 예
모든 프로젝트는 최소한 두 가지 환경(개발 및 생산)에서 소스 코드 관리를 수행해야 합니다. CMS(콘텐츠 관리 시스템), 응용 프로그램 프레임워크 등을 사용하는 경우 응용 프로그램에서 이 시나리오를 지원하려면 반드시 사용자 지정해야 합니다. 이 사건은 다음 섹션에서 설명하는 인기 있는 프레임워크 중 일부에 해당합니다. CMS/프레임워크로 작업할 때는 다음과 같은 많은 질문에 대해 생각하게 됩니다.

- 콘텐츠를 다른 환경으로 어떻게 나눕니까?
- 프레임워크 버전 업데이트에 영향을 주지 않고 변경할 수 있는 파일은 무엇입니까?
- 환경별 구성은 어떻게 관리합니까?
- 모듈, 플러그 인 및 코어 프레임워크의 버전 업데이트는 어떻게 관리합니까?

프로젝트에 여러 환경을 설정하는 방법은 여러 가지 있습니다. 다음 예제에서는 각각의 응용 프로그램에 대한 한 가지 방법을 보여 줍니다.

### <a name="wordpress"></a>WordPress
이 섹션에서는 WordPress용 슬롯을 사용하여 배포 워크플로를 설정하는 방법에 대해 알아봅니다. 대부분의 CMS 솔루션처럼 WordPress도 여러 개발 환경을 지원하려면 사용자 지정해야 합니다. Azure App Service의 Web Apps 기능에는 구성 설정을 코드 외부에 쉽게 저장할 수 있는 몇 가지 기능이 있습니다.

1. 스테이징 슬롯을 만들기 전에 여러 환경을 지원하도록 응용 프로그램 코드를 설정합니다. WordPress의 여러 환경을 지원하려면 로컬 개발 웹앱에서 `wp-config.php`를 편집하고 파일의 시작 부분에 다음 코드를 추가해야 합니다. 이 프로세스를 통해 응용 프로그램에서 선택한 환경을 기반으로 하여 올바른 구성을 선택할 수 있습니다.

    ```
    // Support multiple environments
    // set the config file based on current environment
    if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
    // local development
     $config_file = 'config/wp-config.local.php';
    }
    elseif ((strpos(getenv('WP_ENV'),'stage') !== false) || (strpos(getenv('WP_ENV'),'prod' )!== false ))
    //single file for all azure development environments
     $config_file = 'config/wp-config.azure.php';
    }
    $path = dirname(__FILE__). '/';
    if (file_exists($path. $config_file)) {
    // include the config file if it exists, otherwise WP is going to fail
    require_once $path. $config_file;
    ```

2. `config`라는 웹앱 루트 아래에 폴더를 만들고 각각 Azure 환경과 로컬 환경을 나타내는 `wp-config.azure.php` 및 `wp-config.local.php` 파일을 추가합니다.

3. `wp-config.local.php`에 다음을 복사합니다.

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
    define('AUTH_KEY', 'put your unique phrase here');
    define('SECURE_AUTH_KEY','put your unique phrase here');
    define('LOGGED_IN_KEY','put your unique phrase here');
    define('NONCE_KEY', 'put your unique phrase here');
    define('AUTH_SALT', 'put your unique phrase here');
    define('SECURE_AUTH_SALT', 'put your unique phrase here');
    define('LOGGED_IN_SALT', 'put your unique phrase here');
    define('NONCE_SALT', 'put your unique phrase here');

    /**
     * WordPress Database Table prefix.
     *
     * You can have multiple installations in one database if you give each a unique
     * prefix. Only numbers, letters, and underscores please!
     */
    $table_prefix = 'wp_';
    ```

    이전 코드에서 설명한 대로 보안 키를 설정하면 웹앱을 해킹하지 못하도록 방지할 수 있으므로 고유한 값을 사용합니다. 코드에서 언급한 보안 키의 문자열을 생성해야 하는 경우 [자동 생성기로 이동](https://api.wordpress.org/secret-key/1.1/salt)하여 새 키/값 쌍을 만들 수 있습니다.

4. `wp-config.azure.php`에서 다음 코드로 바꿉니다.

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
    define('AUTH_KEY',getenv('DB_AUTH_KEY'));
    define('SECURE_AUTH_KEY', getenv('DB_SECURE_AUTH_KEY'));
    define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
    define('NONCE_KEY', getenv('DB_NONCE_KEY'));
    define('AUTH_SALT', getenv('DB_AUTH_SALT'));
    define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
    define('LOGGED_IN_SALT',  getenv('DB_LOGGED_IN_SALT'));
    define('NONCE_SALT',  getenv('DB_NONCE_SALT'));

    /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each a unique
    * prefix. Only numbers, letters, and underscores please!
    */
    $table_prefix = getenv('DB_PREFIX');
```

#### <a name="use-relative-paths"></a>상대 경로 사용
WordPress 앱에서 구성하는 마지막 한 가지는 상대 경로입니다. WordPress는 URL 정보를 데이터베이스에 저장합니다. 이 저장소는 콘텐츠를 한 환경에서 다른 환경으로 이동하는 것을 더욱 어렵게 만듭니다. 로컬에서 스테이지로 또는 스테이지에서 프로덕션 환경으로 이동할 때마다 데이터베이스를 업데이트해야 합니다. 한 환경에서 다른 환경으로 데이터베이스를 배포할 때마다 이 배포로 인해 발생할 수 있는 문제의 위험을 줄이려면 WordPress 관리자 대시보드를 사용하여 설치할 수 있는 [상대 루트 링크 플러그 인](https://wordpress.org/plugins/root-relative-urls/)을 사용합니다.

`wp-config.php` 파일에서 `That's all, stop editing!` 주석 앞에 다음 항목을 추가합니다.

```

  define('WP_HOME', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_SITEURL', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

WordPress 관리자 대시보드에서 `Plugins` 메뉴를 통해 플러그 인을 활성화합니다. WordPress 앱에 대한 고정 링크 설정을 저장합니다.

#### <a name="the-final-wp-configphp-file"></a>최종 `wp-config.php` 파일
WordPress 코어 업데이트는 `wp-config.php`, `wp-config.azure.php` 및 `wp-config.local.php` 파일에 영향을 주지 없습니다. `wp-config.php` 파일의 최종 버전은 다음과 같습니다.

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
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) ||(strpos(getenv('WP_ENV'),'prod' )!== false )){
  $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
  // include the config file if it exists, otherwise WP is going to fail
  require_once $path. $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__). '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH. 'wp-settings.php');
```

#### <a name="set-up-a-staging-environment"></a>스테이징 환경 설정
1. Azure 구독에서 이미 실행 중인 WordPress 웹앱을 사용하는 경우 [Azure Portal](http://portal.azure.com)에 로그인한 다음 WordPress 웹앱으로 이동합니다. WordPress 웹앱이 없으면 Azure Marketplace에서 만들 수 있습니다. 자세한 내용은 [Azure App Service에서 WordPress 웹앱 만들기](web-sites-php-web-site-gallery.md)를 참조하세요.
**설정** > **배포 슬롯** > **추가**를 차례로 클릭하여 *stage* 이름의 배포 슬롯을 만듭니다. 배포 슬롯은 이전에 만든 기본 웹앱과 동일한 리소스를 공유하는 다른 웹 응용 프로그램입니다.

    ![스테이지 배포 슬롯 만들기](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

2. `wordpress-stage-db`라는 다른 MySQL 데이터베이스를 `wordpressapp-group` 리소스 그룹에 추가합니다.

    ![리소스 그룹에 MySQL 데이터베이스 추가](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

3. 스테이지 배포 슬롯의 연결 문자열을 새로운 `wordpress-stage-db` 데이터베이스를 가리키도록 업데이트합니다. `wordpressprodapp` 프로덕션 웹앱과 `wordpressprodapp-stage` 스테이징 웹앱은 서로 다른 데이터베이스를 가리켜야 합니다.

#### <a name="configure-environment-specific-app-settings"></a>환경 관련 앱 설정 구성
개발자는 Azure에서 웹앱과 관련된 **앱 설정**이라는 구성 정보의 일부로 키/값 문자열 쌍을 저장할 수 있습니다. 런타임에서 웹앱은 이러한 값을 자동으로 검색하여 웹앱에서 실행 중인 코드에서 사용할 수 있도록 합니다. 보안 측면에서 암호가 포함된 데이터베이스 연결 문자열과 같은 중요한 정보는 `wp-config.php`와 같은 파일에 일반 텍스트로 표시되지 않기 때문에 긍정적인 부수 효과가 있습니다.

다음 단락에서 설명하는 프로세스는 WordPress 앱의 파일 변경과 데이터베이스 변경을 모두 포함하기 때문에 유용합니다.

* WordPress 버전 업그레이드
* 플러그 인 새로 추가, 편집 또는 업그레이드
* 테마 새로 추가, 편집 또는 업그레이드

다음에 대한 앱 설정 구성

* 데이터베이스 정보
* WordPress 로깅 설정/해제
* WordPress 보안 설정

![Wordpress 웹앱에 대한 앱 설정](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

프로덕션 웹앱과 스테이지 슬롯에 대해 다음 앱 설정을 추가해야 합니다. 프로덕션 웹앱과 스테이징 웹앱은 서로 다른 데이터베이스를 사용합니다.

1. WP_ENV를 제외한 모든 설정 매개 변수에 대해 **슬롯 설정** 확인란의 선택을 취소합니다. 이 프로세스는 웹앱, 파일 내용 및 데이터베이스의 구성을 교환합니다. **슬롯 설정**을 선택하면 **교환** 작업을 수행할 때 웹앱의 앱 설정 및 연결 문자열 구성이 환경 간에 이동하지 *않습니다*. 데이터베이스 변경 내용이 있더라도 프로덕션 웹앱은 손상되지 않습니다.

2. WebMatrix 또는 선택한 도구(예: FTP, Git 또는 PhpMyAdmin)를 사용하여 스테이지 웹앱과 데이터베이스에 로컬 개발 환경 웹앱을 배포합니다.

    ![WordPress 웹앱에 Web Matrix 게시 대화 상자](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

3. 스테이징 웹앱을 찾아 테스트합니다. 웹앱 테마를 업데이트하는 시나리오의 준비 웹앱을 가정해 보겠습니다.

    ![슬롯 교환 전에 스테이징 웹앱 찾아보기](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)

4. 모든 항목이 양호하면 스테이징 웹앱에서 **교환** 단추를 클릭하여 콘텐츠를 프로덕션 환경으로 이동합니다. 이 경우 모든 **교환** 작업 중에 환경 간에 웹앱과 데이터베이스를 교환합니다.

    ![WordPress에 대한 변경 내용 미리 보기 교환](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

    > [!NOTE]
    > 시나리오에서 파일만 푸시해야 하는 경우(데이터베이스 업데이트 없음) **교환**을 수행하기 전에 Azure Portal의 **웹앱 설정** 블레이드에서 모든 데이터베이스 관련 *앱 설정* 및 *연결 문자열 설정*에 대한 **슬롯 설정**을 확인합니다. 이 경우 **교환**을 수행할 때 DB_NAME, DB_HOST, DB_PASSWORD, DB_USER 및 기본 연결 문자열 설정이 변경 내용 미리 보기에 표시되지 않아야 합니다. 이 시점에서 **교환** 작업을 완료하면 WordPress 웹앱에 업데이트 파일만 있습니다.
    >
    >

    **교환**을 수행하기 전의 WordPress 프로덕션 웹앱은 다음과 같습니다.
    ![슬롯 교환 전 프로덕션 웹앱](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

    **교환** 작업 후에는 프로덕션 웹앱의 테마가 업데이트됩니다.

    ![슬롯 교환 후 프로덕션 웹앱](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

5. 롤백해야 하는 경우 프로덕션 웹 **앱 설정**으로 이동하고 **교환** 단추를 클릭하여 웹앱과 데이터베이스를 프로덕션에서 스테이징 슬롯으로 교환할 수 있습니다. 데이터베이스 변경 내용이 **교환** 작업에 포함된 경우 다음 번에 스테이징 웹앱에 배포할 때 해당 데이터베이스 변경 내용을 스테이징 데이터베이스 웹앱의 현재 데이터베이스에 배포해야 합니다. 현재 데이터베이스는 이전 프로덕션 데이터베이스 또는 스테이지 데이터베이스일 수 있습니다.

#### <a name="summary"></a>요약
다음은 데이터베이스가 있는 모든 응용 프로그램에 일반화된 프로세스입니다.

1. 로컬 환경에 응용 프로그램 설치
2. 환경별 구성(로컬 및 Azure Web Apps) 포함
3. Web Apps의 스테이징 환경 및 프로덕션 환경 설정
4. Azure에서 프로덕션 응용 프로그램을 이미 실행하고 있는 경우 로컬 및 스테이징 환경으로 프로덕션 콘텐츠(파일/코드 및 데이터베이스) 동기화
5. 로컬 환경에서 응용 프로그램 개발
6. 프로덕션 웹앱을 유지 관리나 잠김 모드로 전환 및 프로덕션 환경에서 스테이징 및 개발 환경으로 데이터베이스 콘텐츠 동기화
7. 스테이징 환경에 배포 및 테스트
8. 프로덕션 환경에 배포
9. 4-6단계 반복

### <a name="umbraco"></a>Umbraco
이 섹션에서는 Umbraco CMS에서 사용자 지정 모듈을 사용하여 여러 DevOps 환경에 배포하는 방법에 대해 알아봅니다. 이 예제에서는 여러 개발 환경을 관리하는 다른 방법을 제공합니다.

[Umbraco CMS](http://umbraco.com/)는 많은 개발자가 사용하는 인기 있는 .NET CMS 솔루션이며, 개발, 스테이징, 프로덕션 환경 순으로 배포하는 [Courier2](http://umbraco.com/products/more-add-ons/courier-2) 모듈을 제공합니다. Visual Studio 또는 WebMatrix를 사용하여 Umbraco CMS 웹앱용 로컬 개발 환경을 쉽게 만들 수 있습니다.

- [Visual Studio를 사용하여 Umbraco 웹앱 만들기](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget)
- [WebMatrix를 사용하여 Umbraco 웹앱 만들기](http://umbraco.tv/videos/umbraco-v7/implementor/fundamentals/installation/creating-umbraco-site-from-webmatrix-web-gallery/)

반드시 응용 프로그램에서 `install` 폴더를 제거하고, 이 폴더를 스테이지 또는 프로덕션 웹앱에 업로드하면 안 됩니다. 이 자습서에서는 WebMatrix를 사용합니다.

#### <a name="set-up-a-staging-environment"></a>스테이징 환경 설정
1. Umbraco CMS 웹앱을 이미 작동 및 실행하고 있다고 가정하고 Umbraco CMS 웹앱에 대해 앞에서 언급한 대로 배포 슬롯을 만듭니다. 그렇지 않으면 마켓플레이스에서 만들 수 있습니다.
2. 새 **umbraco-stage-db** 데이터베이스를 가리키도록 스테이지 배포 슬롯의 연결 문자열을 업데이트합니다. 프로덕션 웹앱(umbraositecms-1)과 스테이징 웹앱(umbracositecms-1-stage)은 서로 다른 데이터베이스를 *가리켜야 합니다*.

    ![새 스테이징 데이터베이스를 통해 스테이징 웹앱의 연결 문자열 업데이트](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

3. **스테이지** 배포 슬롯에 대해 **게시 설정 가져오기**를 클릭합니다. 이 프로세스는 응용 프로그램을 로컬 개발 웹앱에서 Azure 웹앱으로 게시하는 데 필요한 모든 정보를 Visual Studio 또는 WebMatrix에서 저장하는 게시 설정 파일을 다운로드합니다.

    ![스테이징 웹앱의 게시 설정 가져오기](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)
4. WebMatrix 또는 Visual Studio에서 로컬 개발 웹앱을 엽니다. 이 자습서에서는 WebMatrix를 사용합니다. 먼저 스테이징 웹앱의 게시 설정 파일을 가져와야 합니다.

    ![Web Matrix를 사용하여 Umbraco 게시 설정 가져오기](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

5. 대화 상자에서 변경 내용을 검토하고 로컬 웹앱을 *umbracositecms-1-stage* Azure 웹앱에 배포합니다. 파일을 스테이징 웹앱에 직접 배포할 경우 `~/app_data/TEMP/` 폴더에 있는 모든 파일은 생략합니다. 이러한 파일은 스테이지 웹앱을 처음 시작할 때 다시 생성되기 때문입니다. 또한 재생성될 `~/app_data/umbraco.config` 파일도 생략해야 합니다.

    ![Web Matrix에서 게시 설정 검토](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

6. 스테이징 웹앱에 Umbraco 로컬 웹앱을 성공적으로 게시한 후 스테이징 웹앱을 탐색하고 몇 가지 테스트를 실행하여 문제를 제거합니다.

#### <a name="set-up-the-courier2-deployment-module"></a>Courier2 배포 모듈 설정
[Courier2](http://umbraco.com/products/more-add-ons/courier-2) 모듈을 사용하면 콘텐츠, 스타일시트 및 개발 모듈을 마우스 오른쪽 단추로 클릭하여 스테이징 웹앱에서 프로덕션 웹앱으로 간단하게 푸시할 수 있습니다. 이 프로세스는 업데이트를 배포할 때 프로덕션 웹앱이 손상될 위험을 줄입니다.
`*.azurewebsites.net` 도메인 및 사용자 지정 도메인 (예:  http://abc.com ) 에 대한 Courier2 라이선스를 구입합니다. 라이선스를 구입한 후 다운로드한 라이선스(.LIC 파일)를 `bin` 폴더에 저장합니다.

![라이선스 파일을 bin 폴더에 넣기](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

1. [Courier2 패키지를 다운로드합니다](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). 스테이지 웹앱(http://umbracocms-site-stage.azurewebsites.net/umbraco)에 로그인하고 **개발자** 메뉴를 클릭한 다음 **패키지** > **로컬 패키지 설치**를 차례로 클릭합니다.

    ![Umbraco 패키지 설치 관리자](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

2. 설치 관리자를 사용하여 Courier2 패키지를 업로드합니다.

    ![Courier 모듈에 대한 패키지 업로드](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

3. 패키지를 구성하려면 웹앱의 **Config** 폴더 아래에 있는 courier.config 파일을 업데이트해야 합니다.

    ```xml
    <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1.azurewebsites.net</url>
          <user>0</user>
          <!--<login>user@email.com</login> -->
          <!-- <password>user_password</password>-->
          <!-- <passwordEncoding>Clear</passwordEncoding>-->
          </repository>
     </repositories>
     ```

4. `<repositories>`에서 프로덕션 사이트 URL 및 사용자 정보를 입력하세요.
    기본 Umbraco 멤버 자격 공급자를 사용하는 경우 &lt;user&gt; 섹션에서 관리 사용자의 ID를 추가합니다.
    사용자 지정 Umbraco 멤버 자격 공급자를 사용하는 경우 Courier2 모듈에서 `<login>`,`<password>`를 사용하여 프로덕션 사이트에 연결합니다.
    자세한 내용은 [Courier2 모듈 설명서](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation)(영문)를 검토하세요.

5. 마찬가지로 프로덕션 사이트에 Courier2 모듈을 설치하고, 각 courier.config 파일에서 여기서 표시한 대로 스테이지 웹앱을 가리키도록 구성합니다.

    ```xml
     <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1-stage.azurewebsites.net</url>
          <user>0</user>
          </repository>
     </repositories>
    ```

6. Umbraco CMS 웹앱 대시보드에서 **Courier2** 탭을 클릭한 다음 **위치**를 클릭합니다. `courier.config`에 설명된 대로 리포지토리 이름이 표시됩니다. 프로덕션 웹앱 및 스테이징 웹앱 모두에서 이 프로세스를 수행합니다.

    ![대상 웹앱 리포지토리 보기](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

7. 콘텐츠를 스테이징 사이트에서 프로덕션 사이트로 배포하려면 **콘텐츠**로 이동하여 기존 페이지를 선택하거나 새 페이지를 만듭니다. 웹앱에서 **시작 – 새로 만들기** 페이지 제목의 기존 페이지를 선택한 다음 **저장 및 게시**를 클릭합니다.

    ![페이지 제목 변경 및 게시](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

8. 수정된 페이지를 마우스 오른쪽 단추로 클릭하여 모든 옵션을 확인합니다. **Courier**를 클릭하여 **배포** 대화 상자를 엽니다. **배포**를 클릭하여 배포를 시작합니다.

    ![Courier 모듈 배포 대화 상자](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

9. 변경 내용을 검토한 다음 **계속**을 클릭합니다.

    ![Courier 모듈 배포 대화 상자 변경 내용 검토](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

    배포 로그에서 배포 성공 여부를 표시합니다.

     ![Courier 모듈에서 배포 로그 보기](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

10. 프로덕션 웹앱을 탐색하여 변경 내용이 반영되었는지 확인합니다.

     ![프로덕션 웹앱 찾아보기](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Courier 사용 방법에 대한 자세한 내용은 설명서를 검토합니다.

#### <a name="how-to-upgrade-the-umbraco-cms-version"></a>Umbraco CMS 버전을 업그레이드하는 방법
Courier는 한 버전의 Umbraco CMS에서 다른 버전으로 업그레이드하는 데 도움이 되지 않습니다. Umbraco CMS 버전을 업그레이드할 때는 사용자 지정 모듈 또는 파트너 및 Umbraco 코어 라이브러리 모듈과의 비호환성을 확인해야 합니다. 모범 사례는 다음과 같습니다.

* 업그레이드하기 전에 항상 웹앱과 데이터베이스를 백업합니다. Azure의 웹앱에서 백업 기능을 사용하여 웹 사이트의 자동 백업을 설정하고, 필요할 경우 복원 기능을 사용하여 사이트를 복원할 수 있습니다. 자세한 내용은 [웹앱 백업 방법](web-sites-backup.md) 및 [웹앱 복원 방법](web-sites-restore.md)을 참조하세요.
* 파트너의 패키지가 업그레이드하려는 버전과 호환되는지 확인합니다. 패키지의 다운로드 페이지에서 Umbraco CMS 버전과의 프로젝트 호환성을 검토합니다.

웹앱을 로컬로 업그레이드하는 방법에 대한 자세한 내용은 [일반 업그레이드 지침](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general)을 참조하세요.

로컬 개발 사이트를 업그레이드한 후에 변경 내용을 스테이징 웹앱에 게시합니다. 응용 프로그램을 테스트합니다. 모든 항목이 양호하면 **교환** 단추를 사용하여 스테이징 사이트를 프로덕션 웹앱으로 교환합니다. **교환** 작업을 수행하면 웹앱 구성에 영향을 미칠 변경 내용을 확인할 수 있습니다. 이 **교환** 작업은 웹앱과 데이터베이스를 교환합니다. **교환** 후에는 프로덕션 웹앱은 umbraco-stage-db 데이터베이스를 가리키며, 스테이징 웹앱은 umbraco-prod-db 데이터베이스를 가리키게 됩니다.

![Umbraco CMS 배포를 위한 교환 미리보기](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

웹앱과 데이터베이스를 교환하는 이점은 다음과 같습니다.

* 응용 프로그램에 문제가 있는 경우 다른 **교환**을 사용하여 웹앱의 이전 버전으로 롤백할 수 있습니다.
* 업그레이드하는 경우 스테이징 웹앱의 파일과 데이터베이스를 프로덕션 웹앱과 데이터베이스에 배포해야 합니다. 파일과 데이터베이스를 배포할 때 많은 문제가 발생할 수 있습니다. 슬롯의 **교환** 기능을 사용하면 업그레이드 동안의 가동 중지 시간을 줄이고 변경 내용을 배포할 때 발생할 수 있는 오류 위험을 낮출 수 있습니다.
* [프로덕션에서 테스트](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/) 기능을 사용하여 **A/B 테스트**를 수행할 수 있습니다.

이 예에서는 전체 환경에서 배포를 관리하기 위해 Umbraco Courier 모듈과 유사한 사용자 지정 모듈을 구축할 수 있는 플랫폼 유연성을 보여줍니다.

## <a name="references"></a>참조
[Azure 앱 서비스를 사용하여 Agile 소프트웨어 개발](app-service-agile-software-development.md)

[Azure 앱 서비스에서 웹 앱에 대한 스테이징 환경 설정](web-sites-staged-publishing.md)

[비 프로덕션 배포 슬롯에 대한 웹 액세스를 차단하는 방법](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

