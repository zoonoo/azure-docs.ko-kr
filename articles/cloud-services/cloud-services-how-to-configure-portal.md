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
	ms.date="06/29/2015"
	ms.author="adegeo"/>




# 클라우드 서비스를 구성하는 방법

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-configure.md)
- [Azure Preview Portal](cloud-services-how-to-configure-portal.md)

Azure 관리 포털에서 클라우드 서비스에 가장 일반적으로 사용되는 설정을 구성할 수 있습니다. 또는 구성 파일을 직접 업데이트하려는 경우 업데이트할 서비스 구성 파일을 다운로드한 후 업데이트된 파일을 업로드하고 구성 변경 내용으로 클라우드 서비스를 업데이트합니다. 어느 방법이든 모든 역할 인스턴스에 구성 업데이트를 적용합니다.

클라우드 서비스에서 실행 중인 하나 또는 모든 역할에 원격 데스크톱 연결을 사용할 수도 있습니다. 원격 데스크톱을 사용하면 응용 프로그램이 실행 중일 때 해당 데스크톱에 액세스하여 문제를 진단하고 해결할 수 있습니다. 응용 프로그램 개발 과정에서 원격 데스크톱의 서비스 정의 파일(.csdef)을 구성하지 않았더라도 역할에 원격 데스크톱 연결을 사용할 수 있습니다. 원격 데스크톱 연결을 사용하기 위해 응용 프로그램을 다시 배포할 필요가 없습니다.

Azure는 각 역할에 둘 이상의 역할 인스턴스가 있는 경우에만 구성 업데이트 중 99.95%의 서비스 가용성을 보장할 수 있습니다. 이에 따라, 가상 컴퓨터 하나는 클라이언트 요청을 처리하고 다른 하나는 업데이트를 진행할 수 있습니다. 자세한 내용은 [서비스 수준 계약](http://azure.microsoft.com/support/legal/sla/)을 참조하세요.

## 클라우드 서비스 변경하기

1. [Azure Preview 포털](http://portal.azure.com/)에서 클라우드 서비스로 이동합니다.

2. **설정**아이콘 또는 **Essentials/모든 설정**링크를 클릭하여 **설정**블레이드 링크를 엽니다.

    ![설정 페이지](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    여기에서 **속성**을 보고 **구성**을 변경하고 **인증서**를 관리하고 이 클라우드 서비스에 대한 액세스를 가진 **사용자**를 관리할 수 있습니다.

2. **모니터링** 섹션에서 원하는 타일을 클릭하여 경고를 구성할 수 있습니다.

    ![클라우드 서비스 모니터링](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)
    
3. **역할 및 인스턴스** 섹션에서 원하는 클라우드 서비스 역할을 클릭하여 인스턴스를 관리할 수 있습니다.

    ![클라우드 서비스 인스턴스](./media/cloud-services-how-to-configure-portal/cs-instance.png)
    
    여기에서 클라우드 서비스를 원격으로 연결하거나 다시 부팅하거나 이미지로 다시 설치할 수 있습니다.
    
    ![클라우드 서비스 인스턴스 단추](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

>[AZURE.NOTE]클라우드 서비스에 사용되는 운영 체제는 **Azure Preview 포털**을 사용하여 변경할 수 없으며 [미리 보기를 할 수 없는 포털](http://manage.windowsazure.com/)을 통해서만 이 설정을 변경할 수 있습니다. 이 내용은 [여기](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file)에 자세히 설명되어 있습니다.

## 클라우드 서비스 구성 파일 업데이트

1. 먼저, 기존 클라우드 서비스 구성 파일 (.cscfg)을 다운로드 합니다.

    1. [Azure Preview 포털](http://portal.azure.com/)에서 클라우드 서비스로 이동합니다.

    2. **설정**아이콘 또는 **Essentials/모든 설정**링크를 클릭하여 **설정**블레이드 링크를 엽니다.

        ![설정 페이지](./media/cloud-services-how-to-configure-portal/cloud-service.png)
    
    3. **구성** 항목을 클릭합니다.

        ![구성 블레이드](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
    
    4. **다운로드** 단추를 클릭합니다.

        ![다운로드](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

2. 서비스 구성 파일을 업데이트한 후 구성 업데이트를 업로드하고 적용합니다.

    1. 위의 처음 3 개 단계에 따라 클라우드 서비스에 대한 **구성** 블레이드를 엽니다.
    
    2. **업로드** 단추를 클릭합니다.

        ![업로드](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
    
    3. .Cscfg 파일을 선택하고 **확인**을 클릭합니다.

## 역할 인스턴스에 대한 원격 액세스 구성

원격 액세스는 **Azure Preview 포털**을 사용하여 구성할 수 없으며 [미리 보기를 할 수 없는 포털](http://manage.windowsazure.com/)을 통해서만 이 설정을 변경할 수 있습니다. 이 내용은 [여기](cloud-services-role-enable-remote-desktop.md)에 자세히 설명되어 있습니다.
			
 

<!---HONumber=September15_HO1-->