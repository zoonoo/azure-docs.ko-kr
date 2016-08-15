<properties 
	pageTitle="클라우드 서비스를 구성하는 방법(포털) | Microsoft Azure" 
	description="Azure에서 클라우드 서비스를 구성하는 방법에 대해 알아봅니다. 또한 클라우드 서비스 구성을 업데이트하고 역할 인스턴스에 대한 원격 액세스를 구성하는 방법도 알아봅니다. 이 예제는 Azure 포털을 사용합니다." 
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
	ms.date="07/27/2016"
	ms.author="adegeo"/>

# 클라우드 서비스를 구성하는 방법

> [AZURE.SELECTOR]
- [Azure 포털](cloud-services-how-to-configure-portal.md)
- [Azure 클래식 포털](cloud-services-how-to-configure.md)

Azure 포털에서 클라우드 서비스에 가장 일반적으로 사용되는 설정을 구성할 수 있습니다. 또는 구성 파일을 직접 업데이트하려는 경우 업데이트할 서비스 구성 파일을 다운로드한 후 업데이트된 파일을 업로드하고 구성 변경 내용으로 클라우드 서비스를 업데이트합니다. 어느 방법이든 모든 역할 인스턴스에 구성 업데이트를 적용합니다.

클라우드 서비스 역할의 인스턴스 또는 이에 대한 원격 데스크톱을 관리할 수도 있습니다.

Azure는 각 역할에 둘 이상의 역할 인스턴스가 있는 경우에만 구성 업데이트 중 99.95%의 서비스 가용성을 보장할 수 있습니다. 이에 따라, 가상 컴퓨터 하나는 클라이언트 요청을 처리하고 다른 하나는 업데이트를 진행할 수 있습니다. 자세한 내용은 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)을 참조하세요.

## 클라우드 서비스 변경하기

[Azure 포털](https://portal.azure.com/)을 연 후 클라우드 서비스로 이동합니다. 여기에서 여러 항목을 관리할 수 있습니다.

![설정 페이지](./media/cloud-services-how-to-configure-portal/cloud-service.png)

**설정** 또는 **모든 설정** 링크를 클릭하면 **설정** 블레이드가 열리며 여기에서 **속성** 및 **구성**을 변경하고 **인증서**를 관리하며 **경고 규칙**을 설정하고 이 클라우드 서비스에 액세스하는 **사용자**를 관리할 수 있습니다.

![Azure 클라우드 서비스 설정 블레이드](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

>[AZURE.NOTE]
클라우드 서비스에 사용되는 운영 체제는 **Azure 포털**을 사용하여 변경할 수 없으며, [Azure 클래식 포털](http://manage.windowsazure.com/)을 통해서만 이 설정을 변경할 수 있습니다. 이 내용은 [여기](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file)에 자세히 설명되어 있습니다.

## 모니터링

클라우드 서비스에 경고를 추가할 수 있습니다. **설정** > **경고 규칙** > **경고 추가**를 클릭합니다.

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

여기에서 경고를 설정할 수 있습니다. **메트릭** 드롭다운 상자에서 다음 데이터 형식에 대한 경고를 설정할 수 있습니다.

- 디스크 읽기
- 디스크 쓰기
- 네트워크 입력
- 네트워크 출력
- CPU 비율

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### 메트릭 타일에서 모니터링 구성

**설정** > **경고 규칙**을 사용하는 대신 **클라우드 서비스** 블레이드의 **모니터링** 섹션에서 메트릭 타일 중 하나를 클릭할 수 있습니다.

![클라우드 서비스 모니터링](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

여기에서 타일에 사용되는 차트를 사용자 지정하거나 경고 규칙을 추가할 수 있습니다.


## 다시 부팅, 이미지로 다시 설치 또는 원격 데스크톱

지금은 **Azure 포털**을 사용하여 원격 데스크톱을 구성할 수 없습니다. 그러나 [Azure 클래식 포털](cloud-services-role-enable-remote-desktop.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md) 또는 [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)를 통해 설정할 수 있습니다.

먼저 클라우드 서비스 인스턴스를 클릭합니다.

![클라우드 서비스 인스턴스](./media/cloud-services-how-to-configure-portal/cs-instance.png)

열리는 블레이드에서 원격 데스크톱 연결을 시작하고 인스턴스를 원격으로 다시 부팅하거나 인스턴스를 원격으로 이미지로 다시 설치(새 이미지로 시작)할 수 있습니다.

![클라우드 서비스 인스턴스 단추](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)



## .cscfg 다시 구성

[서비스 구성(cscfg)](cloud-services-model-and-package.md#cscfg) 파일을 통해 클라우드 서비스를 다시 구성해야 할 수 있습니다. 먼저 .cscfg 파일을 다운로드하고 수정한 후 업로드해야 합니다.

1. **설정** 아이콘 또는 **모든 설정**링크를 클릭하여 **설정**블레이드를 엽니다.

    ![설정 페이지](./media/cloud-services-how-to-configure-portal/cloud-service.png)

2. **구성** 항목을 클릭합니다.

    ![구성 블레이드](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)

3. **다운로드** 단추를 클릭합니다.

    ![다운로드](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

4. 서비스 구성 파일을 업데이트한 후 구성 업데이트를 업로드하고 적용합니다.

    ![업로드](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
    
5. .Cscfg 파일을 선택하고 **확인**을 클릭합니다.

			
## 다음 단계

* [클라우드 서비스를 배포](cloud-services-how-to-create-deploy-portal.md)하는 방법을 알아봅니다.
* [사용자 지정 도메인 이름](cloud-services-custom-domain-name-portal.md)을 구성합니다.
* [클라우드 서비스를 관리합니다](cloud-services-how-to-manage-portal.md).
* [SSL 인증서](cloud-services-configure-ssl-certificate-portal.md) 구성

<!---HONumber=AcomDC_0803_2016-->