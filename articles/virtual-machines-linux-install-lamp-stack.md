<properties linkid="manage-linux-common-tasks-lampstack" urlDisplayName="Install LAMP stack" pageTitle="Install the LAMP stack on a Linux virtual machine" metaKeywords="" description="Learn how to install the LAMP stack on a Linux virtual machine (VM) in Azure. You can install on Ubuntu or CentOS." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install the LAMP Stack on a Linux virtual machine in Azure" authors="" solutions="" manager="" editor="" />

Azure에서 Linux 가상 컴퓨터에 LAMP 스택 설치
============================================

LAMP 스택은 다음과 같은 여러 요소로 구성됩니다.

-   **L**inux - 운영 체제
-   **A**pache - 웹 서버
-   **M**ySQL - 데이터베이스 서버
-   **P**HP - 프로그래밍 언어

Ubuntu에 설치
-------------

다음 패키지를 설치해야 합니다.

-   `apache2`
-   `mysql-server`
-   `php5`
-   `php5-mysql`
-   `libapache2-mod-auth-mysql`
-   `libapache2-mod-php5`
-   `php5-xsl`
-   `php5-gd`
-   `php-pear`

이 스택 설치를 다음과 같은 단일 `apt-get install` 명령으로 실행할 수 있습니다.

    apt-get install apache2 mysql-server php5 php5-mysql libapache2-mod-auth-mysql libapache2-mod-php5 php5-xsl php5-gd php-pear

CentOS에 설치
-------------

다음 패키지를 설치해야 합니다.

-   `httpd`
-   `mysql`
-   `mysql-server`
-   `php`
-   `php-mysql`

이 스택 설치를 다음과 같은 단일 `yum install` 명령으로 실행할 수 있습니다.

    yum install httpd mysql mysql-server php-php-mysql

설치
----

1.  **Apache**를 설치합니다.

    1.  Apache 웹 서버를 다시 시작해야 합니다. 다음 명령을 실행합니다.

             sudo /etc/init.d/apache2 restart

    2.  설치가 실행되고 있는지 확인합니다. 브라우저에서 <http://localhost>로 이동합니다. "It works!"라는 메시지가 표시됩니다.

2.  **MySQL**을 설치합니다.
    1.  다음 명령을 실행하여 mysql의 루트 암호를 설정합니다.

             mysqladmin -u root -p password yourpassword

    2.  `mysql` 또는 다양한 MySQL 클라이언트를 사용하여 콘솔에 로그인합니다.

3.  **PHP**를 설치합니다.

    1.  다음 명령을 실행하여 Apache PHP 모듈을 사용하도록 설정합니다.

             sudo a2enmod php5

    2.  다음 명령을 실행하여 Apache를 다시 시작합니다.

             sudo service apache2 restart

추가 참고 자료
--------------

Ubuntu에 LAMP 스택을 설치하는 데 도움이 되는 여러 리소스가 있습니다.

-   <https://help.ubuntu.com/community/ApacheMySQLPHP>
-   <http://fedorasolved.org/server-solutions/lamp-stack>

