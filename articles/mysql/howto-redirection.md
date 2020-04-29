---
title: 리디렉션을 사용 하 여 연결-Azure Database for MySQL
description: 이 문서에서는 리디렉션을 사용 하 여 Azure Database for MySQL에 연결 하도록 응용 프로그램을 구성 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246338"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>리디렉션을 사용 하 여 Azure Database for MySQL에 연결

이 항목에서는 리디렉션 모드를 사용 하 여 Azure Database for MySQL 서버에 응용 프로그램을 연결 하는 방법을 설명 합니다. 리디렉션은 응용 프로그램이 백 엔드 서버 노드에 직접 연결할 수 있도록 하 여 클라이언트 응용 프로그램과 MySQL 서버 간의 네트워크 대기 시간을 줄이는 것을 목표로 합니다.

## <a name="before-you-begin"></a>시작하기 전에
[Azure Portal](https://portal.azure.com)에 로그인합니다. 엔진 버전 5.6, 5.7 또는 8.0을 사용 하 여 Azure Database for MySQL 서버를 만듭니다. 자세한 내용은 [포털에서 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md) 또는 [CLI를 사용하여 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-cli.md)를 참조하세요.

현재 Azure Database for MySQL 서버에서 **SSL을 사용 하도록 설정한** 경우에만 리디렉션이 지원 됩니다. SSL을 구성하는 방법에 대한 자세한 내용은 [Azure Database for MySQL에 SSL 사용](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure)을 참조하세요.

## <a name="php"></a>PHP

PHP 응용 프로그램의 리디렉션 지원은 Microsoft에서 개발한 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 확장을 통해 제공 됩니다. 

Mysqlnd_azure 확장은 PECL를 통해 PHP 응용 프로그램에 추가할 수 있으며 공식적으로 게시 된 [PECL 패키지](https://pecl.php.net/package/mysqlnd_azure)를 통해 확장을 설치 하 고 구성 하는 것이 좋습니다.

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 확장의 리디렉션에 대 한 지원은 현재 미리 보기 상태입니다.

### <a name="redirection-logic"></a>리디렉션 논리

>[!IMPORTANT]
> 1.1.0 버전을 시작 하는 리디렉션 논리/동작이 업데이트 되었으며 **버전 1.1.0 +를 사용 하**는 것이 좋습니다.

리디렉션 동작은의 `mysqlnd_azure.enableRedirect`값에 따라 결정 됩니다. 아래 표에서는이 매개 변수 값을 기반으로 하는 **1.1.0 + 버전**부터 리디렉션의 동작을 간략하게 설명 합니다.

이전 버전의 mysqlnd_azure 확장 (버전 1.0.0-1.0.3)을 사용 하는 경우 리디렉션 동작은의 `mysqlnd_azure.enabled`값에 따라 결정 됩니다. 유효한 값은 `off` (아래 표에 설명 된 동작과 유사 하 게 작동) 및 `on` (아래 표에서와 같이 `preferred` 작동)입니다.  

|**mysqlnd_azure enableRedirect 값**| **동작**|
|----------------------------------------|-------------|
|`off` 또는 `0`|리디렉션이 사용 되지 않습니다. |
|`on` 또는 `1`|-Azure Database for MySQL 서버에서 SSL을 사용 하지 않는 경우 연결 되지 않습니다. 다음 오류가 반환 됩니다. *"mysqlnd_azure. enableRedirect는 on 이지만 SSL 옵션은 연결 문자열에 설정 되어 있지 않습니다. 리디렉션은 SSL로만 가능 합니다. "*<br>-MySQL 서버에서 SSL을 사용 하지만 서버에서 리디렉션이 지원 되지 않으면 첫 번째 연결이 중단 되 고 *"리디렉션이 MySQL 서버에서 사용 하도록 설정 되어 있지 않거나 네트워크 패키지가 리디렉션 프로토콜을 충족 하지 않아 연결이 중단 되었습니다." 라는* 오류가 반환 됩니다.<br>-MySQL 서버에서 리디렉션을 지원 하지만 어떤 이유로 든 리디렉션된 연결에 실패 한 경우 첫 번째 프록시 연결만 중단 됩니다. 리디렉션된 연결의 오류를 반환 합니다.|
|`preferred` 또는 `2`<br> (기본값)|-가능 하면 mysqlnd_azure에서 리디렉션을 사용 합니다.<br>-연결에서 SSL을 사용 하지 않거나, 서버에서 리디렉션을 지원 하지 않거나, 프록시 연결이 유효한 경우에는 잘못 된 이유로 인해 리디렉션되는 연결에 실패 하 고, 첫 번째 프록시 연결로 대체 됩니다.|

문서의 다음 섹션에서는 PECL를 사용 하 여 `mysqlnd_azure` 확장을 설치 하 고이 매개 변수의 값을 설정 하는 방법을 간략하게 설명 합니다.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>사전 요구 사항 
- PHP 버전 7.2.15 + and 7.3.2 +
- PHP 배 
- php-mysql
- SSL을 사용 하는 Azure Database for MySQL 서버

1. [PECL](https://pecl.php.net/package/mysqlnd_azure)를 사용 하 여 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 를 설치 합니다. 버전 1.1.0 +를 사용 하는 것이 좋습니다.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. 아래를 실행 하 여`extension_dir`확장 디렉터리 ()를 찾습니다.

    ```bash
    php -i | grep "extension_dir"
    ```

3. 반환 된 폴더로 디렉터리를 변경 하 고 `mysqlnd_azure.so` 이 폴더에 있는지 확인 합니다. 

4. 다음을 실행 하 여 .ini 파일의 폴더를 찾습니다. 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. 이 반환 된 폴더로 디렉터리를 변경 합니다. 

6. 에 대 한 `mysqlnd_azure`새 .ini 파일을 만듭니다. 모듈이 ini 파일의 이름 순서에 따라 로드 되므로 이름의 사전순은 mysqnld의 뒤에 있어야 합니다. 예를 들어 .ini `mysqlnd` 의 이름을로 지정 `10-mysqlnd.ini`하는 경우 mysqlnd ini의 `20-mysqlnd-azure.ini`이름을로 지정 합니다.

7. 새 .ini 파일 내에서 리디렉션을 사용 하도록 설정 하려면 다음 줄을 추가 합니다.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>사전 요구 사항 
- PHP 버전 7.2.15 + and 7.3.2 +
- php-mysql
- SSL을 사용 하는 Azure Database for MySQL 서버

1. 다음 명령을 실행 하 여 x64 또는 x86 버전의 PHP를 실행 하 고 있는지 확인 합니다.

    ```cmd
    php -i | findstr "Thread"
    ```

2. 해당 버전의 PHP와 일치 하는 [PECL](https://pecl.php.net/package/mysqlnd_azure) 에서 해당 하는 x64 또는 x86 버전의 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL을 다운로드 합니다. 버전 1.1.0 +를 사용 하는 것이 좋습니다.

3. Zip 파일을 추출 하 고 라는 `php_mysqlnd_azure.dll`DLL을 찾습니다.

4. 아래 명령을 실행 하 여`extension_dir`확장 디렉터리 ()를 찾습니다.

    ```cmd
    php -i | find "extension_dir"
    ```

5. 4 단계 `php_mysqlnd_azure.dll` 에서 반환 된 디렉터리에 파일을 복사 합니다. 

6. 다음 명령을 사용 하 여 파일 `php.ini` 을 포함 하는 PHP 폴더를 찾습니다.

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. `php.ini` 파일을 수정 하 고 다음 추가 줄을 추가 하 여 리디렉션을 사용 하도록 설정 합니다. 

    동적 확장 섹션에서 다음을 수행 합니다. 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    모듈 설정 섹션에서 다음을 수행 합니다.     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>리디렉션 확인

다음 샘플 PHP 코드를 사용 하 여 리디렉션이 구성 되었는지 확인할 수도 있습니다. 이라는 `mysqlConnect.php` PHP 파일을 만들고 아래 코드를 붙여넣습니다. 서버 이름, 사용자 이름 및 암호를 사용자 고유의 이름으로 업데이트 합니다. 
 
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
연결 문자열에 대 한 자세한 내용은 [연결 문자열](howto-connection-string.md)을 참조 하세요.
