<properties 
    pageTitle="Linux VM에 Apache Qpid Proton-c 설치 하는 방법 | Microsoft Azure"
    description="Azure 가상 컴퓨터를 사용하여 CentOS Linux VM을 만드는 방법 및 Apache Qpid Proton-C 라이브러리를 빌드 및 설치하는 방법입니다."
    services="service-bus-messaging"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus-messaging"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/06/2016"
    ms.author="sethm" />

# Azure Linux VM에 Apache Qpid Proton-C 설치

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

이 섹션에서는 Azure 가상 컴퓨터를 사용하여 CentOS Linux VM을 만드는 방법 및 Python 및 PHP 언어 바인딩과 함께 Apache Qpid Proton-C 라이브러리를 다운로드, 빌드 및 설치하는 방법을 보여 줍니다. 이 단계를 완료한 후 이 가이드에 포함된 Python 및 PHP 샘플을 실행할 수 있습니다.

첫 번째 단계는 [Azure 클래식 포털][]을 사용하여 수행됩니다. 다음 스크린샷은 “scott-centos”라는 CentOS VM을 만드는 방법을 보여 줍니다.

![Azure Linux VM의 Proton][0]

프로비전한 후 포털에 다음이 표시됩니다.

![Azure Linux VM의 Proton][1]

컴퓨터에 로그온하려면 SSH에 대한 끝점 포트를 알고 있어야 합니다. 새로 만든 VM을 선택하고 **끝점** 탭을 클릭하여 [Azure 클래식 포털][]에서 이 값을 가져올 수 있습니다. 다음 스크린샷은 이 컴퓨터의 공용 SSH 포트가 57146임을 보여 줍니다.

![Azure Linux VM의 Proton][2]

이제 SSH를 사용하여 컴퓨터에 연결할 수 있습니다. 이 예제에서는 다음 스크린샷과 같이 PuTTY 도구를 사용합니다.

![Azure Linux VM의 Proton][3]

Python 및 PHP 응용 프로그램의 경우 이 예제에서는 Apache의 Proton 클라이언트 라이브러리를 사용합니다. 이러한 라이브러리는 [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html)에서 다운로드하여 사용 가능합니다. 배포 패키지의 Readme 파일은 종속성을 설치하고 Proton을 빌드하는데 필요한 단계를 설명합니다. 단계 요약은 다음과 같습니다.

1.  yum 구성 파일(/etc/yum.conf) 편집 및 커널 헤더(#exclude=kernel*)로 업데이트에 대한 제외를 주석으로 처리. gcc 컴파일러를 설치해야 합니다.

2.  필수 구성 요소 패키지를 설치합니다.

	```
	# required dependencies 
	yum install gcc cmake libuuid-devel
	
	# dependencies needed for ssl support
	yum install openssl-devel
	
	# dependencies needed for bindings
	yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
	
	# dependencies needed for python docs
	yum install epydoc
	```

1.  Proton 라이브러리를 다운로드합니다.

	```
	[azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
	--2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
	Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
	Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
	HTTP request sent, awaiting response... 200 OK
	Length: 868226 (848K) [application/x-gzip]
	Saving to: ‘qpid-proton-0.9.tar.gz’
	
	qpid-proton-0.9.tar.gz                               
	
	100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
	
	2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
	```

1.  배포 보관에서 Proton 코드를 추출합니다.

	```
	tar xvfz qpid-proton-0.9.tar.gz
	```

1.  다음 단계를 사용하여 Readme 파일에서 가져온 코드를 빌드하고 설치합니다.

	```
	From the directory where you found this README file:	
	
	mkdir build cd build
			
	# Set the install prefix. You may need to adjust depending on your		
	# system.		
	cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
			
	# Omit the docs target if you do not wish to build or install		
	# documentation.		
	make all docs
			
	# Note that this step will require root privileges.		
	make install
	```

이러한 단계를 수행한 후 Proton이 컴퓨터에 설치되어 사용할 준비가 됩니다.

## 다음 단계

자세히 알아볼 준비가 되셨습니까? 다음 링크를 방문하세요.

- [서비스 버스 AMQP 개요][]

[서비스 버스 AMQP 개요]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Azure 클래식 포털]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0928_2016-->