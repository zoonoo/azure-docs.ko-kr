<properties 
	pageTitle="Azure 앱 서비스에서 멀티사이트로 WordPress 변환" 
	description="Azure의 갤러리를 통해 만든 기존 WordPress 웹앱을 가져와 WordPress 다중 사이트로 변환하는 방법에 대해 알아봅니다." 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="tomfitz"/>



# Azure 앱 서비스에서 멀티사이트로 WordPress 변환

## 개요

*[벤 로바우(Ben Lobaugh)][ben-lobaugh], [Microsoft Open Technologies Inc.][ms-open-tech]*

이 자습서에서는 Azure의 갤러리를 통해 만든 기존 WordPress 웹 앱을 가져와서 WordPress 멀티 사이트 설치로 변환하는 방법을 알아봅니다. 또한 설치 내에 있는 각 하위 사이트에 사용자 지정 도메인을 할당하는 방법도 알아봅니다.

여기서는 WordPress가 이미 설치되어 있다고 가정합니다. 아직 설치하지 않은 경우 [Azure의 갤러리에서 WordPress 웹 사이트 만들기][website-from-gallery]의 지침을 따르십시오.

기존 WordPress 단일 사이트 설치를 멀티 사이트로 변환하는 작업은 일반적으로 매우 간단하며 여기서는 초기의 많은 단계를 [WordPress Codex][wordpress-codex-create-a-network](영문)의 [네트워크 만들기](http://codex.wordpress.org) 페이지에서 가져옵니다.

그럼 시작해 보겠습니다.

## 멀티 사이트 허용

먼저 `wp-config.php` 파일에서 **WP\_ALLOW\_MULTISITE** 상수를 사용하여 다중 사이트를 사용하도록 설정해야 합니다. 웹 앱 파일은 두 가지 방법으로 편집할 수 있습니다. 첫 번째는 FTP를 통하는 것이고 두 번째는 Git를 통하는 것입니다. 이러한 방법을 설정하는 데 익숙지 않다면 다음 자습서를 참조하십시오.

* [MySQL 및 FTP를 사용하는 PHP 웹 사이트][website-w-mysql-and-ftp-ftp-setup]

* [MySQL 및 Git를 사용하는 PHP 웹 사이트][website-w-mysql-and-git-git-setup]

선택한 편집기를 사용하여 `wp-config.php` 파일을 열고 `/* That's all, stop editing! Happy blogging. */` 줄 위에 다음을 추가합니다.

	/* Multisite */

	define( 'WP_ALLOW_MULTISITE', true );

파일을 저장하고 다시 서버에 업로드해야 합니다.

## 네트워크 설정

웹앱의 *wp-admin*에 로그인하면 **도구** 메뉴 아래에 **네트워크 설정**이라는 새 항목이 표시됩니다. **네트워크 설정**을 클릭하고 네트워크의 세부 정보를 채웁니다.

![네트워크 설정 화면][wordpress-network-setup]

이 자습서에서는 *Sub-directories* 사이트 스키마가 항상 적절히 작동하므로 이 스키마를 사용하며 이 자습서의 뒷부분에서 각 하위 사이트에 대한 사용자 지정 도메인을 설정합니다. 그러나 [Azure 포털](https://portal.azure.com)을 통해 도메인을 매핑하고 와일드카드 DNS를 제대로 설정한 경우 하위 도메인 설치를 설정할 수 있습니다.

하위 도메인과 하위 디렉터리 설정에 대한 자세한 내용은 WordPress Codex의 [멀티 사이트 네트워크 유형][wordpress-codex-types-of-networks] 문서를 참조하십시오.

## 네트워크 사용

이제 네트워크가 데이터베이스에 구성되어 있지만 네트워크 기능을 사용하도록 설정하는 단계가 하나 남아 있습니다. `wp-config.php` 설정을 완료하고 `web.config`에서 각 사이트를 적절히 라우팅하는지 확인하세요.


**네트워크 설정** 페이지에서 *설치* 단추를 클릭하면 WordPress에서 `wp-config.php` 및 `web.config` 파일을 업데이트합니다. 그러나 항상 파일을 검토하여 업데이트가 성공했는지 확인해야 합니다. 업데이트에 성공하지 못한 경우 이 화면은 필수 업데이트를 표시합니다. 파일을 편집하고 저장합니다.


업데이트한 후 wp-admin 대시보드에서 로그아웃했다가 다시 로그인해야 합니다.

이제 관리 표시줄에 **내 사이트**라는 레이블의 추가 메뉴가 표시됩니다. 이 메뉴를 사용하여 **네트워크 관리** 대시보드를 통해 새 네트워크를 제어할 수 있습니다.

## 사용자 지정 도메인 추가

[WordPress MU 도메인 매핑][wordpress-plugin-wordpress-mu-domain-mapping] 플러그 인을 사용하면 네트워크에 있는 임의의 사이트에 사용자 지정 도메인을 쉽게 추가할 수 있습니다. 플러그 인의 정상적인 작동을 위해서는 포털과 도메인 등록 기관에서 추가 설정을 해야 합니다.

## 웹 앱에 대한 도메인 매핑 사용

**무료** [앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 계획 모드에서는 웹앱에 사용자 지정 도메인을 추가할 수 없습니다. **공유** 또는 **표준** 모드로 전환해야 합니다. 다음을 수행합니다.

* Azure 포털에 로그인하여 웹 앱을 찾습니다. 
* 기본 콘텐츠 영역에서 **크기 조정** 탭을 클릭합니다.
* **일반**에서 *공유* 또는 *표준*을 선택합니다.
* **저장**을 클릭합니다.

변경을 확인하고 사용량 및 기타 설정된 구성에 따라 웹 앱에 비용이 청구될 수 있음을 알리는 메시지가 표시될 수 있습니다.

새 설정을 처리하는 데 몇 초 정도 걸리며, 이제 도메인을 설정할 수 있습니다.

## 도메인 확인

Azure 웹 앱에서 사이트에 도메인을 매핑하려면 먼저 도메인에 매핑할 수 있는 권한이 있는지 확인해야 합니다. 이를 위해서는 DNS 항목에 새 CNAME 레코드를 추가해야 합니다.

* 도메인의 DNS 관리자에 로그온합니다.
* 새로운 CNAME *awverify*를 만듭니다.
* *awverify*로 *awverify.YOUR\_DOMAIN.azurewebsites.net*을 가리킵니다.

DNS 변경 내용이 완전히 적용되는 데 어느 정도 시간이 걸릴 수 있으므로, 다음 단계가 즉시 진행되지 않으면 잠시 쉬었다가 나중에 다시 시도해 보십시오.

## 웹 앱에 도메인 추가

Azure 포털을 통해 웹앱에 반환하고 **설정**, **사용자 지정 도메인 및 SSL**을 클릭합니다.

*SSL 설정*이 표시되면 웹앱에 할당하려는 모든 도메인을 입력할 필드가 표시됩니다. 여기에 나열되지 않는 도메인은 도메인 DNS 설정 방법과 상관없이 WordPress에서 매핑에 사용할 수 없습니다.

![사용자 지정 도메인 관리 대화 상자][wordpress-manage-domains]

텍스트 상자에 도메인을 입력한 후 Azure에서 앞서 만든 CNAME 레코드를 확인합니다. DNS가 완전히 전파되지 않으면 빨간색 표시기가 나타납니다. 성공한 경우에는 녹색 확인 표시가 나타납니다.

대화 상자 아래쪽에 나열되는 IP 주소를 기록하십시오. 도메인에 대한 A 레코드를 설정하는 데 이 주소가 필요합니다.

## 도메인 A 레코드 설정

다른 단계가 완료되었다면 이제 DNS A 레코드를 통해 Azure 웹 앱에 도메인을 할당할 수 있습니다.

Azure 웹앱에서 CNAME과 A 레코드를 모두 허용하지만 적절한 도메인 매핑을 사용하려면 *반드시* A 레코드를 사용해야 합니다. CNAME은 YOUR\_DOMAIN.azurewebsites.net을 사용하여 자동으로 만들어지는 다른 CNAME으로 전달할 수 없습니다.

이전 단계의 IP 주소를 사용하여 DNS 관리자로 돌아와서 해당 IP를 가리키도록 A 레코드를 설정하십시오.


## 플러그 인 설치 및 설정

WordPress 멀티 사이트에는 사용자 지정 도메인에 매핑하는 기본 제공 방법이 현재 없습니다. 그러나 매핑 기능을 추가해 주는 [WordPress MU 도메인 매핑][wordpress-plugin-wordpress-mu-domain-mapping](영문)이라는 플러그 인이 있습니다. 사이트의 네트워크 관리 부분에 로그인하여 **WordPress MU 도메인 매핑** 플러그 인을 설치하십시오.

플러그 인을 설치하고 활성화한 후 **설정** > **도메인 매핑**으로 이동하여 플러그 인을 구성합니다. 첫 번째 텍스트 상자인 *서버 IP 주소*에서, 도메인의 A 레코드를 설정하는 데 사용한 IP 주소를 입력합니다. 원하는 임의의 *도메인 옵션*을 설정하고(대개 기본값을 그대로 사용하는 것이 좋음) **저장**을 클릭합니다.

## 도메인 매핑

도메인을 매핑할 사이트의 **대시보드**로 이동합니다. **도구** > **도메인 매핑**을 클릭하고 텍스트 상자에 새 도메인을 입력한 후 **추가**를 클릭합니다.

기본적으로 새 도메인은 자동 생성된 사이트 도메인에 다시 쓰입니다. 모든 트래픽을 새 도메인으로 보내려면 저장하기 전에 *Primary domain for this blog*을 선택합니다. 사이트에 도메인을 무제한으로 추가할 수 있지만 한 도메인만 주 도메인이 될 수 있습니다.

## 다시 실행

Azure 웹 앱에서는 웹 앱에 도메인을 무제한으로 추가할 수 있습니다. 다른 도메인을 추가하려면 각 도메인에 대해 **도메인 확인** 및 **도메인 A 레코드 설정** 섹션을 실행해야 합니다.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 시작 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 

<!---HONumber=AcomDC_1203_2015-->