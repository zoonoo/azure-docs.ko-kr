### 작성기를 통해 설치

1.  [Git를 설치][1]합니다.
<div class="dev-callout">

**참고**
Windows에서는 PATH 환경 변수에도 Git 실행 파일을 추가해야 합니다.
</div>


2.  프로젝트 루트에 **composer.json**이라는 파일을 만들고 다음 코드를 추가합니다.
    
         {
             "require": {
                 "microsoft/windowsazure": "*"
             },			
             "repositories": [
                 {
                     "type": "pear",
                     "url": "http://pear.php.net"
                 }
             ],
             "minimum-stability": "dev"
         }

3.  프로젝트 루트에 **[composer.phar][2]**을 다운로드합니다.

4.  명령 프롬프트를 열고 프로젝트 루트에서 이 파일을 실행합니다.
    
         php composer.phar install
### 수동으로 설치

Azure용 PHP 클라이언트 라이브러리를 수동으로 다운로드하고 설치하려면 다음 단계를 따르십시오.

1.  [GitHub][3]에서 라이브러리가 포함된 .zip 보관 파일을 다운로드합니다. 또는 리포지토리를 분기하여 로컬 컴퓨터에
    복제합니다. 두 번째 옵션을 사용하려면 GitHub 계정이 필요하며 Git가 로컬에 설치되어 있어야 합니다.
		<div class="dev-callout">

	**참고**

	Azure용 PHP 클라이언트 라이브러리는 [HTTP\_Request2][4],
	[Mail\_mime][5] 및 [Mail\_mimeDecode][6] PEAR 패키지에 종속됩니다. 이 종속성을 해결하는 권장 방법은 [PEAR 패키지
	관리자][7](영문)를 사용하여 이러한 패키지를 설치하는 것입니다.
		</div>


2.  다운로드한 보관 파일의 `WindowsAzure` 디렉터리를 응용 프로그램 디렉터리 구조에 복사합니다.

Azure용 PHP 클라이언트 라이브러리 설치에 대한 자세한 내용(PEAR 패키지로 설치에 대한 정보 포함)은 [PHP용
Azure SDK 다운로드](../php-download-sdk/)(영문)를 참조하십시오.



[1]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[2]: http://getcomposer.org/composer.phar
[3]: http://go.microsoft.com/fwlink/?LinkId=252719
[4]: http://pear.php.net/package/HTTP_Request2
[5]: http://pear.php.net/package/Mail_mime
[6]: http://pear.php.net/package/Mail_mimeDecode
[7]: http://pear.php.net/manual/en/installation.php
