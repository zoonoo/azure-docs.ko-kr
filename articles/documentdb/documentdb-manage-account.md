<properties
	pageTitle="Azure 포털을 통해 DocumentDB 계정 관리 | Microsoft Azure"
	description="Azure 포털을 통해 DocumentDB 계정을 관리하는 방법을 알아봅니다. Azure 포털을 사용하여 계정을 보기, 복사, 삭제 및 액세스하는 방법에 대한 지침을 찾습니다."
	keywords="Azure 포털, Documentdb, Azure, Microsoft Azure"
	services="documentdb"
	documentationCenter=""
	authors="AndrewHoh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="anhoh"/>

# DocumentDB 계정을 관리하는 방법

데이터의 글로벌 가용성에 대해 글로벌 일관성을 설정하고 여러 지역을 관리하는 방법을 알아봅니다. 또한, 키를 적용하고 Azure 포털에서 계정을 삭제하는 방법도 알아봅니다.

## <a id="consistency"></a>DocumentDB 일관성 설정 관리

올바른 일관성 수준을 선택하는 것은 응용 프로그램의 의미 체계에 따라 달라집니다. 여러분은 DocumentDB의 이용 가능한 일관성 수준에 친숙해져야 합니다. [일관성 수준을 사용하여 DocumentDB의 가용성과 성능을 최대화][consistency]합니다. DocumentDB는 데이터베이스에서 사용할 수 있는 모든 일관성 수준에서 일관성, 가용성, 성능 보증을 제공합니다. 강력한 일관성 수준으로 데이터베이스 계정을 구성하려면 데이터를 단일 Azure 지역에 국한하고 전 세계적으로 제공하지 말아야 합니다. 반면, 관대한 일관성 수준(제한된 부실, 세션 또는 최종 일관성)은 데이터베이스 계정과 여러 Azure 지역을 연결할 수 있습니다. 아래의 간단한 단계는 데이터베이스 계정에서 기본 일관성 수준을 선택하는 방법을 보여줍니다.

### DocumentDB 계정의 기본 일관성을 지정하려면

1. [Azure 포털](https://portal.azure.com/)에서 DocumentDB 계정에 액세스합니다.
2. 계정 블레이드에서 **설정** 블레이드가 아직 열려 있지 않은 경우 **모든 설정**을 클릭합니다.![기본 일관성 세션][5]

3. **모든 설정** 블레이드의 **기능**에서 **기본 일관성** 항목을 클릭합니다.![기본 일관성 세션][6]

4. **기본 일관성** 블레이드에서 새 일관성 수준을 선택하고 **저장**을 클릭합니다.
5. Azure 포털 알림 허브를 통해 작업 진행률을 모니터링할 수 있습니다.

> [AZURE.NOTE] 기본 일관성 설정이 DocumentDB 계정에 적용되기 전에는 몇 분 정도 걸릴 수 있습니다.

## <a id="addregion"></a>지역 추가

DocumentDB는 대부분 [Azure 지역][azureregions]에서 사용할 수 있습니다. 데이터베이스 계정에서 기본 일관성 수준을 선택한 후에는 (선택한 기본 일관성 수준 및 글로벌 배포 수요에 따라) 하나 이상의 지역을 연결합니다.

> [AZURE.NOTE] 이때, 2016년 6월 13일 이후에 생성된 새로운 DocumentDB 계정에 새 지역을 추가할 수 있습니다. 다중 지역 계정을 만들려면 마켓플레이스에서 "Azure DocumentDB - 다중 지역 데이터베이스 계정"을 선택합니다. 6월 13일 이전에 만든 계정은 가까운 미래에 글로벌 가용성이 제공될 예정입니다.

1. [Azure 포털](https://portal.azure.com/)의 이동 표시줄에서 **DocumentDB 계정**을 클릭합니다.
2. **DocumentDB 계정** 블레이드에서 수정할 데이터베이스 계정을 선택합니다.
3. 계정 블레이드에서 **모든 설정** 블레이드가 아직 열려 있지 않은 경우 **모든 설정**을 클릭합니다.
4. **모든 설정** 블레이드에서 **지역 추가/삭제**를 클릭합니다. ![DocumentDB 계정 > 설정 > 지역 추가/삭제에서 지역 추가][1]
5. **지역 추가/삭제** 블레이드에서 추가/삭제할 지역을 선택하고 **확인**을 클릭합니다. 지역을 추가하는 데는 비용이 부과됩니다. 자세한 정보는 가격 책정 페이지를 참조하세요.

    ![지도에서 지역을 클릭하여 추가/삭제][2]

### 지역 선택

두 개 이상의 지역을 구성할 때는 [비즈니스 연속성 및 재해 복구(BCDR): Azure 쌍을 이루는 지역][bcdr] 문서에서 설명된 지역 쌍에 기초하여 지역을 선택하는 것이 좋습니다.

특히, 여러 지역을 구성할 때는 각 쌍을 이루는 지역 열에서 같은 수의 지역(짝수/홀수 +/-1)을 선택해야 합니다. 예를 들어 미국 지역 4개에 배포하고자 할 경우 왼쪽 열에서 2개 미국 지역을 선택하고 오른쪽 열에서 2개 미국 지역을 선택합니다. 그러므로 미국 서부, 미국 동부, 미국 중북부, 미국 중남부가 적절한 세트가 될 것입니다.

재해 복구 시나리오에서 2개 지역만 구성할 때는 이 지침을 따르는 것이 중요합니다. 2개 이상의 지역을 선택할 경우, 지침을 따르면 좋지만 선택한 지역 중 일부만 이 짝 지침을 따르면 크게 중요하지 않습니다.

## <a id="selectwriteregion"></a>쓰기 지역 선택

DocumentDB 데이터베이스 계정과 연결된 모든 지역은 읽기(단일 항목 및 다중 항목 페이지가 매겨진 읽기)와 쿼리를 서비스할 수 있지만, 한 지역만 쓰기(삽입, 업로드 및 삽입, 교체, 삭제) 요청을 능동적으로 수신합니다. 활성 쓰기 지역을 설정하려면 다음을 수행합니다.


1. **DocumentDB 계정** 블레이드에서 수정할 데이터베이스 계정을 선택합니다.
2. 계정 블레이드에서 **모든 설정** 블레이드가 아직 열려 있지 않은 경우 **모든 설정**을 클릭합니다.
3. **모든 설정** 블레이드에서 **쓰기 지역 우선순위**를 클릭합니다. ![DocumentDB 계정 > 설정 > 지역 추가/삭제에서 쓰기 지역 변경][3]
4. 지역을 클릭하고 드래그하여 지역 목록을 정렬합니다. 지역 목록에서 첫 번째 지역이 활성 쓰기 지역입니다. ![DocumentDB 계정 > 설정 > 쓰기 지역 변경에서 지역 목록을 다시 정렬하여 쓰기 지역 변경][4]

## <a id="keys"></a>선택키 보기, 복사 및 다시 생성
DocumentDB 계정을 만들면 해당 서비스에서 DocumentDB 계정에 액세스할 때 인증에 사용할 수 있는 2개의 마스터 액세스 키가 생성됩니다. DocumentDB에서는 2개의 액세스 키를 제공해서 사용자가 DocumentDB 계정에 대한 중단 없이 키를 다시 생성할 수 있습니다.

[Microsoft Azure 포털](https://portal.azure.com/)에서 **DocumentDB 계정** 블레이드의 **필수 패키지** 모음에서 **키** 블레이드를 액세스하여 DocumentDB 계정에 액세스하는 데 사용되는 액세스 키를 표시, 복사 및 다시 생성합니다.

![Azure 포털 스크린샷, 키 블레이드](./media/documentdb-manage-account/keys.png)

또 다른 옵션은 **모든 설정** 블레이드에서 **키** 항목에 액세스하는 것입니다.

![모든 설정, 키 블레이드](./media/documentdb-manage-account/allsettingskeys.png)

> [AZURE.NOTE] **키** 블레이드에는 [데이터 마이그레이션 도구](documentdb-import-data.md)에서 계정에 연결하는 데 사용할 수 있는 기본 및 보조 연결 문자열도 포함되어 있습니다.

사용자에게 DocumentDB에 대한 읽기 전용 액세스로 제공되는 읽기 전용 키도 포함되어 있습니다. 읽기 및 쿼리는 읽기 전용 작업이며 만들기, 삭제 및 바꾸기는 읽기 전용 작업이 아닙니다.

### Azure 포털에서 선택키 보기 및 복사

1. [Azure 포털](https://portal.azure.com/)에서 DocumentDB 계정에 액세스합니다. 
2. **DocumentDB 계정** 블레이드의 **필수** 모음에서 **키**를 클릭합니다.
3. **키** 블레이드에서 복사할 키 오른쪽의 **복사** 단추를 클릭합니다.![Azure 포털에서 선택키 보기 및 복사, 키 블레이드](./media/documentdb-manage-account/copykeys.png)

### 액세스 키 다시 생성

저장소 연결을 더욱 안전하게 유지할 수 있도록 정기적으로 DocumentDB 계정의 액세스 키를 변경해야 합니다. 두 개의 액세스 키가 할당되므로 액세스 키 하나를 다시 생성하는 동안 다른 액세스 키를 사용하여 DocumentDB 계정에 대한 연결을 유지할 수 있습니다.

> [AZURE.WARNING] 액세스 키를 다시 생성하면 현재 키에 종속된 모든 응용 프로그램에 영향을 줍니다. 액세스 키를 사용하여 DocumentDB 계정에 액세스하는 모든 클라이언트가 새 키를 사용하도록 업데이트되어야 합니다.

DocumentDB 계정을 사용하는 웹 응용 프로그램이나 클라우드 서비스가 있는 경우 키를 롤링하지 않고 다시 생성하면 연결이 끊어집니다. 다음 단계에서는 키 롤링에 관련된 프로세스를 간략하게 설명합니다.

1. DocumentDB 계정의 보조 액세스 키를 참조하도록 응용 프로그램 코드의 액세스 키를 업데이트합니다.
2. DocumentDB 계정의 기본 액세스 키를 다시 생성합니다. [Azure 포털](https://portal.azure.com/)에서 DocumentDB 계정에 액세스합니다.
3. **DocumentDB 계정** 블레이드의 **필수** 모음에서 **키**를 클릭합니다.
4. **키** 블레이드에서 **기본 키 다시 생성** 명령을 클릭하고 **확인**을 클릭하여 새 키를 생성할지 확인합니다.
5. 키를 다시 생성하고 약 5분 후에 새 키를 사용할 수 있는지 확인한 후 응용 프로그램 코드에서 새 기본 액세스 키를 참조하도록 액세스 키를 업데이트합니다.
6. 보조 액세스 키를 다시 생성합니다.

> [AZURE.NOTE] 새로 생성된 키를 사용하여 DocumentDB 계정에 액세스할 수 있을 때까지 몇 분 정도 걸릴 수 있습니다.

## <a id="delete"></a> DocumentDB 계정 삭제
Azure 포털에서 더 이상 사용하지 않는 DocumentDB 계정을 제거하려면 **DocumentDB 계정** 블레이드에서 **삭제** 명령을 사용합니다.

![Azure 포털에서 DocumentDB 계정을 삭제하는 방법](./media/documentdb-manage-account/deleteaccountconfirmation.png)


1. [Azure 포털](https://portal.azure.com/)에서 삭제할 DocumentDB 계정에 액세스합니다.
2. **DocumentDB 계정** 블레이드에서 **계정 삭제**를 클릭합니다.
3. 그러면 표시되는 확인 블레이드에서 DocumentDB 계정 이름을 입력하여 계정을 삭제할 것임을 확인합니다.
4. 확인 블레이드에서 **삭제** 단추를 클릭합니다.

## <a id="next"></a>다음 단계

[DocumentDB 계정을 사용하기 시작](http://go.microsoft.com/fwlink/p/?LinkId=402364)하는 방법을 알아봅니다.

DocumentDB에 대한 자세한 내용은 [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409)에서 Azure DocumentDB 설명서를 참조하세요.


<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/ko-KR/regions/#services
[offers]: https://azure.microsoft.com/ko-KR/pricing/details/documentdb/

<!---HONumber=AcomDC_0615_2016-->