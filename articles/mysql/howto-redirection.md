---
title: 리디렉션으로 연결 - MySQL용 Azure 데이터베이스
description: 이 문서에서는 리디렉션을 사용하여 MySQL용 Azure 데이터베이스에 연결하도록 응용 프로그램을 구성하는 방법에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246338"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>리디렉션을 사용하여 MySQL용 Azure 데이터베이스에 연결

이 항목에서는 리디렉션 모드로 MySQL 서버에 대한 Azure 데이터베이스응용 프로그램을 연결하는 방법을 설명합니다. 리디렉션은 응용 프로그램이 백 엔드 서버 노드에 직접 연결할 수 있도록 하여 클라이언트 응용 프로그램과 MySQL 서버 간의 네트워크 대기 시간을 줄이는 것을 목표로 합니다.

## <a name="before-you-begin"></a>시작하기 전에
[Azure 포털에](https://portal.azure.com)로그인합니다. 엔진 버전 5.6, 5.7 또는 8.0으로 MySQL 서버에 대한 Azure 데이터베이스를 만듭니다. 자세한 내용은 [포털에서 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md) 또는 [CLI를 사용하여 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-cli.md)를 참조하세요.

리디렉션은 현재 MySQL 서버에 대한 Azure 데이터베이스에서 **SSL을 사용하도록 설정된** 경우에만 지원됩니다. SSL을 구성하는 방법에 대한 자세한 내용은 [Azure Database for MySQL에 SSL 사용](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure)을 참조하세요.

## <a name="php"></a>PHP

PHP 응용 프로그램의 리디렉션에 대한 지원은 Microsoft에서 개발한 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 확장을 통해 사용할 수 있습니다. 

mysqlnd_azure 확장은 PECL을 통해 PHP 응용 프로그램에 추가할 수 있으며 공식적으로 게시된 [PECL 패키지를](https://pecl.php.net/package/mysqlnd_azure)통해 확장을 설치하고 구성하는 것이 좋습니다.

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 확장에서 리디렉션에 대한 지원은 현재 미리 보기 상태입니다.

### <a name="redirection-logic"></a>리디렉션 논리

>[!IMPORTANT]
> 리디렉션 논리/동작 시작 버전 1.1.0이 업데이트되었으며 **버전 1.1.0+를 사용하는 것이 좋습니다.**

리디렉션 동작은 `mysqlnd_azure.enableRedirect`의 값에 의해 결정됩니다. 아래 표는 **버전 1.1.0+에서**시작하는 이 매개 변수의 값을 기반으로 리디렉션의 동작을 간략하게 설명합니다.

이전 버전의 mysqlnd_azure 확장(버전 1.0.0-1.0.3)을 사용하는 경우 리디렉션 동작은 `mysqlnd_azure.enabled`의 값에 의해 결정됩니다. 유효한 값은 `off` (아래 표에 설명된 동작과 `on` 유사하게 `preferred` 작동) 및 (아래 표와 같은 역할)입니다.  

|**mysqlnd_azure.enableRedirect 값**| **동작**|
|----------------------------------------|-------------|
|`off` 또는 `0`|리디렉션은 사용되지 않습니다. |
|`on` 또는 `1`|- MySQL 서버용 Azure 데이터베이스에서 SSL을 사용하지 않으면 연결되지 않습니다. 다음 오류가 반환됩니다: *"mysqlnd_azure.enableRedirect가 설정되어 있지만 SSL 옵션은 연결 문자열에 설정되지 않았습니다. 리디렉션은 SSL에서만 가능합니다."*<br>- MySQL 서버에서 SSL을 활성화했지만 서버에서 리디렉션이 지원되지 않는 경우 첫 번째 연결이 중단되고 *"Connection aborted because redirection is not enabled on the MySQL server or the network package doesn't meet redirection protocol."* 다음 오류가 반환됩니다.<br>- MySQL 서버가 리디렉션을 지원하지만 리디렉션된 연결이 어떤 이유로든 실패한 경우 첫 번째 프록시 연결도 중단합니다. 리디렉션된 연결의 오류를 반환합니다.|
|`preferred` 또는 `2`<br> (기본값)|- mysqlnd_azure 가능하면 리디렉션을 사용합니다.<br>- 연결이 SSL을 사용하지 않거나 서버가 리디렉션을 지원하지 않거나 프록시 연결이 여전히 유효한 동안 리디렉션된 연결이 치명적이지 않은 이유로 연결되지 않으면 첫 번째 프록시 연결로 돌아갑니다.|

문서의 후속 섹션에서는 PECL을 `mysqlnd_azure` 사용하여 확장을 설치하고 이 매개 변수의 값을 설정하는 방법을 설명합니다.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>사전 요구 사항 
- PHP 버전 7.2.15+ 및 7.3.2+
- PHP 배 
- php-mysql
- SSL을 사용하도록 설정한 MySQL 서버에 대한 Azure 데이터베이스

1. [PECL로](https://pecl.php.net/package/mysqlnd_azure) [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 설치합니다. 버전 1.1.0+를 사용하는 것이 좋습니다.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. 아래를 실행하여`extension_dir`확장 디렉토리 () 를 찾습니다.

    ```bash
    php -i | grep "extension_dir"
    ```

3. 디렉터리를 반환된 폴더로 `mysqlnd_azure.so` 변경하고 이 폴더에 있는지 확인합니다. 

4. 아래를 실행하여 .ini 파일의 폴더를 찾습니다. 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. 디렉토리를 반환된 폴더로 변경합니다. 

6. 에 대한 `mysqlnd_azure`새 .ini 파일을 만듭니다. 모듈은 ini 파일의 이름 순서에 따라 로드되므로 이름의 알파벳 순서가 mysqnld의 알파벳 순서를 따서 있는지 확인합니다. 예를 들어 `mysqlnd` .ini의 `10-mysqlnd.ini`이름이 지정된 경우 mysqlnd ini의 이름을 .로 `20-mysqlnd-azure.ini`지정합니다.

7. 새 .ini 파일 내에서 다음 줄을 추가하여 리디렉션을 활성화합니다.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>사전 요구 사항 
- PHP 버전 7.2.15+ 및 7.3.2+
- php-mysql
- SSL을 사용하도록 설정한 MySQL 서버에 대한 Azure 데이터베이스

1. 다음 명령을 실행하여 x64 또는 x86 버전의 PHP를 실행 중인지 확인합니다.

    ```cmd
    php -i | findstr "Thread"
    ```

2. PHP 버전과 일치하는 [PECL에서](https://pecl.php.net/package/mysqlnd_azure) [해당 mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL 의 해당 x64 또는 x86 버전을 다운로드합니다. 버전 1.1.0+를 사용하는 것이 좋습니다.

3. zip 파일을 추출하고 라는 `php_mysqlnd_azure.dll`DLL을 찾습니다.

4. 아래 명령을 실행하여`extension_dir`확장 디렉토리 () 를 찾습니다.

    ```cmd
    php -i | find "extension_dir"
    ```

5. 4단계에서 `php_mysqlnd_azure.dll` 반환된 디렉토리에 파일을 복사합니다. 

6. 다음 명령을 사용하여 파일이 `php.ini` 포함된 PHP 폴더를 찾습니다.

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. `php.ini` 파일을 수정하고 다음 추가 줄을 추가하여 리디렉션을 활성화합니다. 

    동적 확장 섹션 아래: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    모듈 설정 섹션에서 다음을 수행합니다.     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>리디렉션 확인

아래 샘플 PHP 코드로 리디렉션이 구성되어 있는지 확인할 수도 있습니다. 라는 `mysqlConnect.php` PHP 파일을 만들고 아래 코드를 붙여 넣습니다. 서버 이름, 사용자 이름 및 암호를 사용자 이름으로 업데이트합니다. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>다음 단계
연결 문자열에 대한 자세한 내용은 [연결 문자열을](howto-connection-string.md)참조하십시오.
