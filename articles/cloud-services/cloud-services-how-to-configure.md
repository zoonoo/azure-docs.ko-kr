<properties 
	pageTitle="클라우드 서비스를 구성하는 방법 | Microsoft Azure" 
	description="Azure에서 클라우드 서비스를 구성하는 방법에 대해 알아봅니다. 또한 클라우드 서비스 구성을 업데이트하고 역할 인스턴스에 대한 원격 액세스를 구성하는 방법도 알아봅니다." 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/15/2016"
	ms.author="adegeo"/>




# 클라우드 서비스를 구성하는 방법

> [AZURE.SELECTOR]
- [Azure portal](cloud-services-how-to-configure-portal.md)
- [Azure classic portal](cloud-services-how-to-configure.md)

Azure 클래식 포털에서 클라우드 서비스에 가장 일반적으로 사용되는 설정을 구성할 수 있습니다. 또는 구성 파일을 직접 업데이트하려는 경우 업데이트할 서비스 구성 파일을 다운로드한 후 업데이트된 파일을 업로드하고 구성 변경 내용으로 클라우드 서비스를 업데이트합니다. 어느 방법이든 모든 역할 인스턴스에 구성 업데이트를 적용합니다.

Azure 클래식 포털을 통해 [Azure 클라우드 서비스의 역할에 대해 원격 데스크톱 연결을 사용](cloud-services-role-enable-remote-desktop.md)하도록 설정할 수도 있습니다.

Azure는 각 역할에 둘 이상의 역할 인스턴스가 있는 경우에만 구성 업데이트 중 99.95%의 서비스 가용성을 보장할 수 있습니다. 이에 따라, 가상 컴퓨터 하나는 클라이언트 요청을 처리하고 다른 하나는 업데이트를 진행할 수 있습니다. 자세한 내용은 [서비스 수준 계약](http://azure.microsoft.com/support/legal/sla/)을 참조하세요.

## 클라우드 서비스 변경하기

1. [Azure 클래식 포털](http://manage.windowsazure.com/)에서 **클라우드 서비스**를 클릭한 다음 클라우드 서비스의 이름을 클릭하고 **구성**을 클릭합니다.

    ![구성 페이지](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    **구성** 페이지에서 모니터링을 구성하고, 역할 설정을 업데이트하고, 게스트 운영 체제 및 역할 인스턴스 패밀리를 선택할 수 있습니다.

2. **모니터링**에서 모니터링 수준을 Verbose 또는 Minimal로 설정하고 자세한 모니터링에 필요한 진단 연결 문자열을 구성합니다.

3. 역할을 기준으로 그룹화된 서비스 역할의 경우 다음 설정을 업데이트할 수 있습니다.
    
    >**설정** 서비스 구성 파일(.cscfg)의 ConfigurationSettings 요소에 지정된 기타 구성 설정의 값을 수정합니다.
    >
    >**인증서** 역할의 SSL 암호화에 사용 중인 인증서 지문을 변경합니다. 인증서를 변경하려면 먼저 새 인증서를 업로드해야 합니다(**인증서** 페이지). 그런 다음, 역할 설정에 표시되는 인증서 문자열에서 인증서 지문을 업데이트합니다.

4. **운영 체제**에서 역할 인스턴스의 버전이나 운영 체제 제품군을 변경하거나 **자동**을 선택하여 현재 운영 체제 버전의 자동 업데이트를 사용하도록 설정할 수 있습니다. 운영 체제 설정은 웹 역할 및 작업자 역할에는 적용되지만 가상 컴퓨터에는 영향을 주지 않습니다.

    배포하는 동안 모든 역할 인스턴스에 최신 운영 체제 버전이 설치되며 운영 체제는 기본적으로 자동 업데이트됩니다.
    
    코드의 호환성 요구 사항으로 인해 다른 운영 체제 버전에서 클라우드 서비스를 실행하기 위해 필요한 경우 운영 체제 제품군 및 버전을 선택할 수 있습니다. 특정 운영 체제 버전을 선택하면 클라우드 서비스를 위한 자동 운영 체제 업데이트가 일시 중단됩니다. 운영 체제가 업데이트를 받도록 해야 합니다.
    
    앱과 최신 운영 체제 버전 사이의 호환성 문제를 모두 해결하면 해당 운영 체제 버전을 **자동**으로 설정하여 자동 운영 체제 업데이트를 사용하도록 설정할 수 있습니다.
    
    ![OS 설정](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. 구성 설정을 저장하려면 역할 인스턴스에 적용하고 **저장**을 클릭합니다. 변경 내용을 취소하려면 **Discard**를 클릭합니다. **저장** 및 **취소**는 설정 변경 후 명령 모음에 추가됩니다.

## 클라우드 서비스 구성 파일 업데이트

1. 현재 구성과 함께 클라우드 서비스 구성 파일(.cscfg)을 다운로드합니다. 클라우드 서비스의 **구성** 페이지에서 **다운로드**를 클릭합니다. **저장** 또는 **다른 이름으로 저장**을 클릭하여 파일을 저장합니다.

2. 서비스 구성 파일을 업데이트한 후 구성 업데이트를 업로드하고 적용합니다.

    1. **구성** 페이지에서 **업로드**를 클릭합니다.
    
        ![구성 업로드](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. **구성 파일**에서 **찾아보기**를 사용하여 업데이트된 .cscfg 파일을 선택합니다.
    
    3. 클라우드 서비스에 인스턴스가 하나뿐인 역할이 포함된 경우 **Apply configuration even if one or more roles contain a single instance** 확인란을 선택하여 역할의 구성 업데이트를 진행하도록 설정합니다.
    
        모든 역할에 대해 두 개 이상의 인스턴스를 정의하지 않는 경우 Azure는 서비스 구성 업데이트 과정에서 최소 99.95%의 클라우드 서비스 가용성을 보장할 수 없습니다. 자세한 내용은 [서비스 수준 계약](http://azure.microsoft.com/support/legal/sla/)을 참조하세요.
    
    4. **확인**(확인 표시)을 클릭합니다.


## 다음 단계

* [클라우드 서비스를 배포](cloud-services-how-to-create-deploy.md)하는 방법을 알아봅니다.
* [사용자 지정 도메인 이름](cloud-services-custom-domain-name.md)을 구성합니다.
* [클라우드 서비스를 관리합니다](cloud-services-how-to-manage.md).
* [Azure 클라우드 서비스의 역할에 대해 원격 데스크톱 연결 사용](cloud-services-role-enable-remote-desktop.md)
* [SSL 인증서](cloud-services-configure-ssl-certificate.md)를 구성합니다.

<!---HONumber=AcomDC_0121_2016-->