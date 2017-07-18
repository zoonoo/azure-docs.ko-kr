---
title: "MySQL용 Azure Database에 안전하게 연결하기 위한 SSL 연결 구성 | Microsoft Docs"
description: "SSL 연결을 올바르게 사용하기 위해 MySQL용 Azure Database 및 연결된 응용 프로그램을 올바르게 구성하는 방법에 대한 지침"
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 06/01/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 1303e6cf6f6fdd10a7310d770127828276d5056e
ms.contentlocale: ko-kr
ms.lasthandoff: 06/30/2017

---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>MySQL용 Azure Database에 안전하게 연결하기 위한 사용자 응용 프로그램의 SSL 연결 구성
MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 응용 프로그램 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간 SSL 연결을 적용하면 서버와 응용 프로그램 간 데이터 스트림을 암호화함으로써 “메시지 가로채기(man in the middle)” 공격으로부터 보호할 수 있습니다.

기본적으로 데이터베이스 서비스는 MySQL용 Azure Database 서버에 연결할 때 SSL 연결이 필요하도록 구성되어야 합니다.  가능하면 SSL 옵션을 해제하지 않는 것이 좋습니다.

## <a name="enforcing-ssl-connections"></a>SSL 연결 적용
Azure Portal 또는 CLI를 통해 새로운 MySQL용 Azure Database 서버를 프로비전할 때 SSL 연결을 적용은 기본적으로 활성화됩니다.

마찬가지로, Azure Portal의 해당 서버에 있는 “연결 문자열” 설정에서 미리 정의된 연결 문자열에는 SSL이 필요한 일반적인 언어에 대한 필수 매개 변수가 포함되어 있습니다. SSL 매개 변수는 “ssl=true” 또는 “sslmode=require” 또는 “sslmode=required” 및 다른 변형과 같은 커넥터에 따라 달라집니다.

## <a name="configure-enforcement-of-ssl"></a>SSL 적용 구성
SSL 적용을 사용하지 않거나 사용하도록 설정할 수 있습니다. Microsoft Azure는 항상 향상된 보안을 위해 SSL 연결 적용을 사용하는 것을 권장합니다.

### <a name="using-azure-portal"></a>Azure 포털 사용
Azure Portal을 사용하여 MySQL용 Azure Database 서버를 방문하여 **연결 보안**을 클릭합니다. 설정/해제 단추를 사용하여 **SSL 연결 적용** 설정을 사용하거나 사용하지 않도록 설정합니다. 그런 다음 **Save**를 클릭합니다. Microsoft는 향상된 보안을 위해 항상 **SSL 연결 적용**을 활성화하는 것을 권장합니다.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

**SSL 적용 상태** 표시기를 확인할 수 있는 **개요** 페이지에서 설정을 확인할 수 있습니다.

### <a name="using-azure-cli"></a>Azure CLI 사용
Azure CLI에서 Enabled 또는 Disabled 값을 각각 사용하여 **ssl-enforcement** 매개 변수를 사용하거나 사용하지 않도록 설정할 수 있습니다.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```
## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>응용 프로그램 또는 프레임워크가 SSL 연결을 지원하는지 확인합니다.
Wordpress, Drupal 및 Magento와 같은 데이터베이스 서비스용 MySQL을 사용하는 많은 일반적인 응용 프로그램은 기본적으로 설치하는 동안 SSL을 사용하도록 설정하지 마십시오. 설치 후 또는 응용 프로그램과 관련이 있는 CLI 명령을 통해 해당 응용 프로그램에서 SSL 연결을 사용하도록 설정합니다. MySQL 서버에 SSL 연결이 적용되어 있고 연결된 응용 프로그램이 올바르게 구성되어 있지 않은 경우 데이터베이스 서버에 연결하지 못할 수 있습니다. 응용 프로그램의 설명서를 참조하여 SSL 연결을 설정하는 방법을 알아보세요.

## <a name="applications-that-require-a-local-certificate-for-ssl-connectivity"></a>SSL 연결을 위해 로컬 인증서가 필요한 응용 프로그램
경우에 따라 안전한 연결을 위해 응용 프로그램에 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일(.pem)이 필요합니다.  다음 단계를 참조하여 .cer 파일을 얻고, 로컬 .pem 파일을 생성하고, 응용 프로그램에 바인딩합니다.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>CA(인증 기관)에서 인증서 파일 다운로드
MySQL용 Azure Database 서버와 함께 SSL을 통해 통신하는 데 필요한 인증서는 [여기](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)에 있습니다.  인증서 파일을 로컬 드라이브에 다운로드합니다(이 자습서에서는 **c:\ssl** 사용).

### <a name="download-and-install-openssl-on-your-pc"></a>PC에 OpenSSL 다운로드 및 설치
응용 프로그램을 데이터베이스 서버에 안전하게 연결하기 위해 필요한 로컬 **.pem** 파일을 생성하려면 OpenSSL을 로컬 컴퓨터에 설치해야 합니다.  

다음 섹션에서는 사용할 수는 방법에 설명합니다. 선호하는 OS에 따라 Linux 또는 Windows PC 중 하나를 사용할 수 있습니다. 한 가지 방법을 수행하기만 하면 됩니다.

### <a name="linux-users---download-and-install-openssl-using-a-linux-pc"></a>Linux 사용자 - Linux PC를 사용하여 OpenSSL 다운로드 및 설치
OpenSSL 라이브러리는 [OpenSSL Software Foundation](http://www.openssl.org)에서 직접 소스 코드로 제공됩니다.  다음 지침에서는 Linux PC에 OpenSSL을 설치하는 데 필요한 단계를 안내합니다.  이 가이드에서는 Ubuntu 12.04 이상용 명령에 대해 살펴봅니다.

터미널 세션을 열고 OpenSSL을 설치합니다.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
```  
다운로드 패키지에서 파일 압축을 풉니다.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
파일 압축을 푼 디렉터리를 입력합니다.  기본적으로 다음과 같아야 합니다.

```bash
cd openssl-1.1.0e
```
다음 명령을 실행하여 OpenSSL을 구성합니다. /usr/local/openssl이 아닌 다른 폴더의 파일을 원하면 다음 명령을 적절하게 변경합니다.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
이제 OpenSSL이 올바르게 구성되었으므로 인증서를 변환하기 위해 컴파일해야 합니다.  컴파일하려면 다음 명령을 실행합니다.

```bash
make
```
컴파일이 완료되면 다음 명령을 실행하여 OpenSSL을 실행 가능하도록 설치할 준비가 되었습니다.
```bash
make install
```
시스템에 OpenSSL이 성공적으로 설치되었는지 확인하려면 다음 명령을 실행합니다. 동일한 출력이 나타나는지 확인합니다.

```bash
/usr/local/openssl/bin/openssl version
```
성공적이라면 다음과 같은 메시지가 표시되어야 합니다.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

### <a name="windows-pc-users---download-and-install-openssl-using-a-windows-pc"></a>Windows PC 사용자 - Windows PC를 사용하여 OpenSSL 다운로드 및 설치
응용 프로그램을 데이터베이스 서버에 안전하게 연결하기 위해 필요한 로컬 **.pem** 파일을 생성하려면 OpenSSL을 로컬 컴퓨터에 설치해야 합니다.

OpenSSL 라이브러리는 [OpenSSL Software Foundation](http://www.openssl.org)에서 직접 소스 코드로 제공됩니다. 다음 지침에서는 Linux PC에 OpenSSL을 설치하는 데 필요한 단계를 안내합니다.

다음 방법을 수행하면 Windows PC에서 OpenSSL을 설치할 수 있습니다.

1. **(권장)** Window 10 이상에서 기본 제공되는 Windows용 Bash 기능을 사용하면 OpenSSL이 기본적으로 설치됩니다.  Windows 10의 Windows용 Bash 기능을 사용하도록 설정하는 방법에 대한 지침은 [여기](https://msdn.microsoft.com/commandline/wsl/install_guide)에 나와 있습니다.

2. 커뮤니티에서 제공되는 Win32/64 응용 프로그램을 다운로드하여, OpenSSL Software Foundation에서 특정 Windows 설치 관리자를 제공하거나 보증하지는 않지만 사용 가능한 설치 관리자의 목록은 [여기](https://wiki.openssl.org/index.php/Binaries)에서 확인할 수 있습니다.

### <a name="convert-your-cer-certificate-to-a-local-pem"></a>.cer 인증서를 로컬.pem으로 변환
다운로드한 루트 CA 파일은 **.crt** 형식입니다. OpenSSL을 사용하여 인증서 파일을 **.pem**으로 변환합니다.  이렇게 하려면 openssl.exe 명령줄 도구를 실행하고 다음 명령을 실행합니다.

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.crt -out MyServerCACert.pem
```
이제 인증서 파일(MyServerCACert.pem)을 성공적으로 만들었으므로 SSL을 통해 데이터베이스 서버에 안전하게 연결할 수 있습니다.

다음 예제에서는 **MyServerCACert.pem** 파일을 사용하여 MySQL 명령줄 인터페이스 및 MySQL 워크벤치를 통해 MySQL 서버에 연결하는 방법을 설명합니다.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>SSL로 MySQL CLI를 사용하는 서버에 연결
MySQL 명령줄 인터페이스를 사용하여 다음 명령을 실행합니다.

```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -uUsername@mysqlserver4demo -pYourPassword --ssl-ca=c:\ssl\MyServerCACert.pem
```
mysql **status** 명령을 실행하여 SSL로 MySQL 서버에 연결되어 있는지 확인합니다.

```dos
mysql> status
--------------
mysql.exe  Ver 14.14 Distrib 5.7.18, for Win64 (x86_64)

Connection id:          65535
Current database:
Current user:           jason@66.235.55.141
SSL:                    Cipher in use is AES256-SHA
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             janderserver.mysql.sqltest-eg1.mscds.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    cp850
Conn.  characterset:    cp850
TCP port:               3306
Uptime:                 1 day 19 hours 9 min 43 sec

Threads: 4  Questions: 26082  Slow queries: 0  Opens: 112  Flush tables: 1  Open tables: 105  Queries per second avg: 0.167
--------------
```

> [!NOTE]
> 현재 서비스에 대한 mysql.exe 연결에서 “--ssl-mode=VERIFY_IDENTITY” 옵션을 사용하는 경우 알려진 문제가 있습니다. 연결은 다음 오류를 표시하며 실패합니다. _오류 2026(HY000): SSL 연결 오류: SSL 인증서 유효성 검사 오류_ “--ssl-mode=VERIFY_CA” 이하 [SSL 모드](https://dev.mysql.com/doc/refman/5.7/en/secure-connection-options.html#option_general_ssl-mode)로 다운그레이드하세요. “--ssl-mode=VERIFY_IDENTITY”를 사용해야 하는 경우 이 문제가 해결될 때까지 지역별 서버 이름을 대신 사용합니다. 지역 서버를 확인하려면 서버 이름을 ping하고(예: `westeurope1-a.control.database.windows.net`), 해당 지역별 서버 이름을 연결에 사용합니다. 나중에 이 제한을 제거할 예정입니다.

### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>SSL로 MySQL 워크벤치를 사용하는 서버에 연결
SSL을 통해 안전하게 연결하도록 MySQL Workbench를 구성합니다. 새 연결 설정 대화 상자에서 MySQL Workbench의 **SSL** 탭으로 이동합니다. **SSL CA 파일:** 필드에 있는 **MyServerCACert.pem**의 파일 위치를 입력합니다.
![사용자 지정된 타일 저장](./media/howto-configure-ssl/mysql-workbench-ssl.png)

## <a name="next-steps"></a>다음 단계
[MySQL용 Azure Database에 대한 연결 라이브러리](concepts-connection-libraries.md)에 이어지는 다양한 응용 프로그램 연결 옵션 검토

