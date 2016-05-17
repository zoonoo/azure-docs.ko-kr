<properties
  pageTitle="Azure IoT Suite FAQ | Microsoft Azure"
  description="IoT Suite에 대한 질문과 대답"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="05/09/2016"
  ms.author="araguila"/>
   
# IoT Suite에 대한 질문과 대답

### Azure 포털에서 리소스 그룹을 삭제하는 것과 azureiotsuite.com의 미리 구성된 솔루션에서 삭제를 클릭하는 것의 차이는 무엇인가요?

- [azureiotsuite.com][lnk-azureiotsuite]에서 미리 구성된 솔루션을 삭제하면, 미리 구성된 솔루션을 만들 때 프로비전된 모든 리소스가 삭제됩니다. 리소스 그룹에 리소스를 추가하면, 이들 역시 삭제됩니다. 

- [Azure 포털][lnk-azure-portal]에서 리소스 그룹을 삭제하면 해당 리소스 그룹에 포함된 리소스만 삭제됩니다. 따라서 [Azure 클래식 포털][lnk-classic-portal]의 미리 구성된 솔루션과 연결된 Azure Active Directory 응용 프로그램도 삭제해야 합니다.

### 하나의 구독에 프로비전할 수 있는 DocumentDB 인스턴스는 몇 개인가요?

5개입니다. [Azure 지원 티켓][link-azuresupportticket]을 만들어서 이 한도를 높일 수 있지만, 기본적으로 구독 당 DocumentDB 인스턴스를 5개만 프로비전할 수 있습니다. 결과적으로, 지정된 구독에 원격 모니터링 미리 구성된 솔루션을 5개까지만 프로비전할 수 있습니다.

### 하나의 구독에 프로비전할 수 있는 무료 Bing 지도 API는 몇 개인가요?

2개입니다. 구독에 무료 Bing 지도 API는 2개만 만들 수 있습니다. 원격 모니터링 솔루션이 무료 Bing 지도 API와 함께 기본으로 프로비전됩니다. 결과적으로 구독에 원격 모니터링 솔루션을 가감 없이 2개까지만 프로비전할 수 있습니다.

### 정적 맵이 있는 원격 모니터링 솔루션 배포에서는 대화형 Bing 맵을 어떻게 추가하나요? 
1. [Azure 포털][lnk-azure-portal]에서 엔터프라이즈용 Bing 맵 API QueryKey 키 가져오기: 
 1. [Azure 포털][lnk-azure-portal]에서 엔터프라이즈용 Bing 맵 API가 있는 리소스 그룹으로 이동합니다.
 2. 모든 설정을 클릭한 다음 키 관리를 클릭합니다. 
 3. MasterKey와 QueryKey 등, 두 개의 키가 보일 것입니다. QueryKey에 대한 값을 복사합니다.

     > [AZURE.NOTE] 엔터프라이즈용 Bing 맵 API 계정이 없는 경우 [Azure 포털][lnk-azure-portal]에서 + 새로 만들기를 클릭하고 엔터프라이즈용 Bing 맵 API를 검색한 다음 지침에 따라 새로 만듭니다.

2. [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github]에서 최신 코드를 풀다운합니다.

3. 저장소의 /docs/ 폴더에서 명령줄 배포 지침에 따라 로컬 또는 클라우드 배포를 실행합니다.

4. 로컬 또는 클라우드 배포를 실행한 후에는 루트 폴더에서 배포 중에 *.user.config 파일이 만들어졌는지 확인합니다. 텍스트 편집기에서 이 파일을 엽니다.

5. QueryKey에서 복사한 값을 포함하도록 다음 줄을 변경합니다.
   
  `<setting name="MapApiQueryKey" value="" />`

### DreamSpark용 Microsoft Azure가 있는 경우 사전 구성된 솔루션을 만들 수 있나요?
현재는 [DreamSpark용 Microsoft Azure][lnk-dreamspark] 계정을 사용하여 사전 구성된 솔루션을 만들 수 없습니다. 하지만 몇 분 이내에 [Azure용 무료 평가판 계정][lnk-30daytrial]을 만들면 사전 구성된 솔루션을 만들 수 있습니다.

### AAD 테넌트를 어떻게 삭제하나요?

Eric Golpe의 블로그 게시물 [Azure AD 테넌트 삭제 연습(영문)][lnk-delete-aad-tennant]을 참조하세요.

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0511_2016-->