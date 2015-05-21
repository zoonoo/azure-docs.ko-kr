다음 단계에 따라 CentOS Linux에서 실행 중인 가상 컴퓨터에 MongoDB를 설치하고 실행하세요.

<div class="dev-callout">
<b>경고</b>
<p>인증 및 IP 주소 바인딩과 같은 MongoDB 보안 기능은 기본적으로 사용하도록 설정되어 있지 않습니다. MongoDB를 프로덕션 환경에 배포하기 전에 보안 기능을 사용하도록 설정해야 합니다.  자세한 내용은 <a href="http://www.mongodb.org/display/DOCS/Security+and+Authentication">보안 및 인증</a> 을 참조하세요.</p>
</div>

1. MongoDB를 설치할 수 있도록 패키지 관리 시스템(YUM)을 구성합니다. */etc/yum.repos.d/10gen.repo* 파일을 작성하여 저장소 정보를 저장하고 다음 내용을 추가합니다.

		[10gen]
		name=10gen Repository
		baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
		gpgcheck=0
		enabled=1

2. repo 파일을 저장한 후 다음 명령을 실행하여 로컬 패키지 데이터베이스를 업데이트합니다.

		$ sudo yum update

3. 패키지를 설치하려면 다음 명령을 실행하여 안정적인 최신 버전의 MongoDB와 관련 도구를 설치합니다.

		$ sudo yum install mongo-10gen mongo-10gen-server

	MongoDB가 다운로드되어 설치될 때까지 기다립니다.

4. 데이터 디렉터리를 만듭니다. 기본적으로 MongoDB는 */data/db* 디렉터리에 데이터를 보관하지만 디렉터리는 사용자가 만들어야 합니다. 디렉터리를 만들려면 다음을 실행하세요.

		$ sudo mkdir -p /srv/datadrive/data
		$ sudo chown `id -u` /srv/datadrive/data

	Linux에 MongoDB를 설치하는 방법에 대한 자세한 정보는 [Unix 퀵 스타트][QuickstartUnix](영문)을 참조하세요.

5. 데이터베이스를 시작하려면 다음을 실행합니다.

		$ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

	MongoDB 서버가 시작되고 저널 파일이 사전 할당되면 모든 로그 메시지가 */srv/datadrive/data/mongod.log* 파일로 보내집니다. MongoDB가 저널 파일을 사전 할당하고 연결이 수신될 때까지 몇 분이 걸릴 수 있습니다.

6. MongoDB 관리 셸을 시작하려면 별도 SSH 또는 PuTTY 창을 열어 다음을 실행합니다.

		$ mongo
		> db.foo.save ( { a:1 } )
		> db.foo.find()
		{ _id : ..., a : 1 }
		> show dbs  
		...
		> show collections  
		...  
		> help  

	이렇게 하면 데이터베이스가 만들어집니다.

7. MongoDB가 설치되면 MongoDB에 원격으로 액세스할 수 있도록 끝점을 구성해야 합니다. 관리 포털에서 **가상 컴퓨터**를 클릭한 후 새로운 가상 컴퓨터의 이름과 **끝점**을 차례로 클릭합니다.
	
	![Endpoints][Image7]

8. 페이지 맨 아래에 있는 **끝점 추가**를 클릭합니다.
	
	![Endpoints][Image8]

9. 다음 설정을 사용하여 끝점을 추가합니다.

 - **이름**: Mongo
 - **프로토콜**: TCP
 - **공용 포트**: 27017
 - **개인 포트**: 27017
 
 이렇게 하면 MongoDB에 원격으로 액세스할 수 있습니다.



[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png

<!--HONumber=35.1-->
