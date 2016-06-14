<properties
	pageTitle="저장소 탐색기(미리 보기) 시작 | Microsoft Azure"
	description="저장소 탐색기(미리 보기)를 사용하여 Azure 저장소 리소스 관리"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="visual-studio-online"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="06/05/2016"
	ms.author="tarcher" />

# 저장소 탐색기(미리 보기) 시작

## 개요 

Microsoft Azure 저장소 탐색기 프리뷰는 장소에 상관 없이 Windows, OSX 및 Linux에서 Azure 저장소 데이터로 손쉽게 작업할 수 있도록 해주는 독립 실행형 앱입니다. 이 문서에서 Azure 저장소 계정에 연결하고 관리하는 다양한 방법을 알아봅니다.

## 필수 조건

- [저장소 탐색기(미리 보기) 다운로드 및 설치](http://go.microsoft.com/fwlink/?LinkId=708343)

## 저장소 계정 또는 서비스에 연결

저장소 탐색기(미리 보기)는 저장소 계정에 연결하는 다양한 방법을 제공합니다. Azure 구독에 연결된 저장소 계정에 연결, 다른 Azure 구독에서 공유 되는 저장소 계정 및 서비스에 연결, Azure 저장소 에뮬레이터를 사용하여 로컬 저장소에 연결 및 관리를 포함합니다.

- [Azure 구독에 연결](#connect-to-an-azure-subscription) - Azure 구독에 속한 저장소 리소스를 관리합니다.
- [로컬 저장소에 연결](#connect-to-local-storage) - Azure 저장소 에뮬레이터를 사용하여 로컬 저장소를 관리합니다. 
- [외부 저장소에 연결](#attach-or-detach-an-external-storage-account) - 저장소 계정의 계정 이름 및 키를 사용하여 다른 Azure 구독에 속하는 저장소 리소스를 관리합니다.
- [SAS를 사용하여 계정 연결](#attach-account-using-sas) - SAS를 사용하여 다른 Azure 구독에 속하는 저장소 리소스를 관리합니다.
- [SAS를 사용하여 서비스 연결](#attach-service-using-sas) - SAS를 사용하여 다른 Azure 구독에 속하는 특정 저장소 서비스(Blob 컨테이너, 큐 또는 테이블)를 관리합니다.

## Azure 구독에 연결

> [AZURE.NOTE] Azure 계정이 없는 경우 [무료 평가판을 등록](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.

1. 저장소 탐색기(미리 보기)를 시작합니다. 

1. 처음으로 저장소 탐색기(미리 보기)를 실행하거나 이전에 저장소 탐색기(미리 보기)를 실행했지만 Azure 계정에 연결하지 않은 경우 Azure 계정에 연결할 수 있는 정보 표시줄이 표시됩니다.

	![][0]
	
1. **Microsoft Azure에 연결**을 선택하고 대화 상자를 따라 하나 이상의 활성 Azure 구독에 연결된 Microsoft 계정으로 로그인합니다.

Microsoft 계정으로 성공적으로 로그인하면 저장소 탐색기(미리 보기)의 왼쪽 창에 Microsoft 계정과 연결된 모든 Azure 구독과 연결된 모든 저장소 계정으로 채워집니다.

### Azure 구독 필터링

저장소 탐색기(미리 보기)를 사용하면 왼쪽 창에 나열된 해당 저장소 계정을 갖는 로그인 Microsoft 계정으로 연결된 Azure 구독을 필터링할 수 있습니다.

1. **설정**(기어) 아이콘을 선택합니다.

	![][1]

1. 	왼쪽 창 맨 위에 로그인한 모든 Microsoft 계정을 포함하는 드롭다운 목록이 표시됩니다.

	![][3]
	 
1.	드롭다운 목록 옆의 아래쪽 화살표를 선택하여 모든 로그인 Microsoft 계정뿐만 아니라 추가 Microsoft 계정 추가(로그인)를 위한 링크를 표시합니다.

	![][4]

1.	드롭다운 목록에서 원하는 Microsoft 계정을 선택합니다.

1. 	왼쪽 창은 선택한 Microsoft 계정과 연결된 모든 Azure 구독을 표시합니다. 각 Azure 구독의 왼쪽에 있는 확인란을 사용하면 해당 Azure 구독과 연결된 모든 저장소 계정을 나열하는 저장소 탐색기(미리 보기)를 사용할지 여부를 지정할 수 있습니다. 나열된 Azure 구독을 모두 선택하거나 하나도 선택하지 않는 **모든 구독** 토글을 선택/선택 취소합니다.

	![][2]

1.	관리하려는 Azure 구독 선택을 완료하면 **적용**을 선택합니다. 왼쪽 창은 현재 Microsoft 계정에 대해 선택한 각 Azure 구독에 대한 모든 저장소 계정을 나열하도록 업데이트됩니다.

### 추가 Microsoft 계정 추가

다음 단계는 각 계정의 Azure 구독 및 저장소 계정을 보는 추가 Microsoft 계정에 연결을 안내합니다.

1.	**설정**(기어) 아이콘을 선택합니다.

	![][1]

1. 	왼쪽 창 맨 위에 현재 연결된 모든 Microsoft 계정을 포함하는 드롭다운 목록이 표시됩니다.

	![][3]
	 
1.	드롭다운 목록 옆의 아래쪽 화살표를 선택하여 모든 로그인 Microsoft 계정뿐만 아니라 추가 Microsoft 계정 추가(로그인)를 위한 링크를 표시합니다.

	![][4]

1.	**계정 추가**를 선택하고 대화 상자를 따라 하나 이상의 활성 Azure 구독에 연결된 계정에 로그인합니다.

1.	검색하려는 Azure 구독에 대한 확인란을 선택합니다.

	![][2]

1.	**적용**을 선택합니다.

### Microsoft 계정 간 전환

여러 Microsoft 계정에 연결할 수 있지만 왼쪽 창은 단일(현재) Microsoft 계정에 대한 구독과 연결된 저장소 계정만 표시합니다. 여러 Microsoft 계정에 연결하는 경우 다음 단계를 수행하여 계정 간에 전환할 수 있습니다.

1.	**설정**(기어) 아이콘을 선택합니다.

	![][1]

1. 	왼쪽 창 맨 위에 현재 연결된 모든 Microsoft 계정을 포함하는 드롭다운 목록이 표시됩니다.

	![][3]
	 
1.	드롭다운 목록 옆의 아래쪽 화살표를 선택하여 모든 로그인 Microsoft 계정뿐만 아니라 추가 Microsoft 계정 추가(로그인)를 위한 링크를 표시합니다.

	![][4]

1.	원하는 Microsoft 계정을 선택합니다.

1.	검색하려는 Azure 구독에 대한 확인란을 선택합니다.

	![][2]

1.	**적용**을 선택합니다.
  
## 로컬 저장소에 연결

저장소 탐색기(미리 보기)를 사용하면 Azure 저장소 에뮬레이터를 사용하여 로컬 저장소에서 작동할 수 있습니다. Azure에 배포된 저장소 계정 없이 저장소에 대한 코드를 작성하고 테스트할 수 있습니다(저장소 계정은 Azure 저장소 에뮬레이터에서 에뮬레이트되므로).

>[AZURE.NOTE] Azure 저장소 에뮬레이터는 현재 Windows에 대해서만 지원됩니다.

1. 저장소 탐색기(미리 보기)를 시작합니다. 

1. 왼쪽 창에서 **(개발)** 노드를 확장합니다.

	![][21]

1. Azure 저장소 에뮬레이터를 아직 설치 하지 않은 경우 정보 표시줄을 통해 작업을 수행하라는 메시지가 표시됩니다. 정보 표시줄이 표시되면 **최신 버전 다운로드**를 선택하고 에뮬레이터를 설치합니다.

	![][22]

1. 에뮬레이터가 설치되면 로컬 Blob, 큐 및 테이블을 만들고 사용할 수 있습니다. 각 저장소 계정 유형으로 작업하는 방법을 알아보려면 아래의 해당 링크를 선택합니다.

	- [Azure Blob 저장소 리소스 관리](./vs-azure-tools-storage-explorer-blobs.md)
	- Azure 큐 저장소 리소스 관리 - *서비스 예정*
	- Azure 테이블 저장소 리소스 관리 - *서비스 예정*

## 외부 저장소 계정 연결 또는 분리

저장소 탐색기(미리 보기)는 저장소 계정을 쉽게 공유할 수 있도록 외부 저장소 계정에 연결하는 기능을 제공합니다. 이 섹션은 외부 저장소 계정에 연결(및 분리)하는 방법을 설명합니다.

### 저장소 계정 자격 증명 가져오기

외부 저장소 계정을 공유하기 위해 해당 계정의 소유자는 먼저 계정에 대한 자격 증명(계정 이름 및 키)을 가져온 다음 해당 정보를 해당(외부) 계정에 연결하려는 사용자와 공유해야 합니다. 다음 단계를 수행하여 Azure 포털을 통해 저장소 계정 자격 증명을 가져올 수 있습니다.

1.	[Azure 포털](https://portal.azure.com)에 로그인합니다.
1.	**찾아보기**를 선택합니다.
1.	**저장소 계정**을 선택합니다.
1.	**저장소 계정** 블레이드에서 원하는 저장소 계정을 선택합니다.
1.	선택한 저장소 계정에 대한 **설정** 블레이드에서 **액세스 키**를 선택합니다.

	![][5]
	
1.	**액세스 키** 블레이드에서 저장소 계정에 연결할 때 사용할 **저장소 계정 이름** 및 **키 1** 값을 복사합니다.

	![][6]

### 외부 저장소 계정에 연결

1.	저장소 탐색기(미리 보기)의 상황에 맞는 메뉴에서 **저장소 계정**을 마우스 오른쪽 단추로 클릭하고 **외부 저장소 연결**을 선택합니다.

	![][7]
	
1.	*저장소 계정 자격 증명 가져오기* 섹션은 저장소 계정 이름 및 키 1 값을 가져오는 방법을 설명합니다. 해당 값은 이 단계에서 사용됩니다. **외부 저장소 연결** 대화 상자에서 **계정 이름** 상자에 저장소 계정 이름을 **계정 키** 상자에 키 1 값을 입력합니다. 완료되면 **확인**을 선택합니다.

	![][8]

	연결되면 외부 저장소 계정이 저장소 계정 이름에 추가된 텍스트 **(외부)**와 함께 표시됩니다.

	![][9]

### 외부 저장소 계정에서 분리

1. 	상황에 맞는 메뉴에서 분리하려는 외부 저장소 계정을 마우스 오른쪽 단추로 클릭하고 **분리**를 선택합니다.

	![][10]

1.	확인 메시지 상자가 나타나면 **예**를 선택하여 외부 저장소 계정에서 분리를 확인합니다.

	![][12]

## SAS를 사용하여 계정 연결

SAS(공유 액세스 서명)는 Azure 구독 관리자에게 해당 Azure 구독 자격 증명을 제공할 필요 없이 임시 기반으로 저장소 계정에 대한 액세스를 부여할 수 있도록 합니다.

이를 설명하기 위해 사용자 A가 Azure 구독의 관리자이고 사용자 A가 특정 권한으로 제한된 시간에 저장소 계정에 액세스하도록 사용자 B를 허용하려 한다고 가정해 보겠습니다.

1. 사용자 A는 특정 기간 동안 원하는 사용 권한으로 SAS(저장소 계정에 대한 연결 문자열로 구성)를 생성합니다.
1. 사용자 A는 저장소 계정에 액세스하려는 사용자(이 예에서 사용자 B)와 SAS를 공유합니다.  
1. 사용자 B는 저장소 탐색기(미리 보기)를 사용하여 제공된 SAS를 사용하여 사용자 A에 속한 계정에 연결합니다. 

### 공유하려는 계정에 대한 SAS 가져오기

1.	저장소 탐색기(미리 보기)를 엽니다.
1.	왼쪽 창의 상황에 맞는 메뉴에서 공유하려는 저장소 계정을 마우스 오른쪽 단추로 클릭하고 **공유 액세스 서명 가져오기**를 선택합니다.

	![][13]

1. **공유 액세스 서명** 대화 상자에서 계정에 대해 원하는 시간 프레임 및 권한을 지정하고 **만들기**를 선택합니다.

	![][14]
 
1. 두 번째 **공유 액세스 서명** 대화 상자가 SAS 표시를 나타냅니다. **연결 문자열** 옆의 **복사**를 선택하여 클립보드에 복사합니다. **닫기**를 선택하여 대화 상자를 닫습니다.

### SAS를 사용하여 공유 계정에 연결

1.	저장소 탐색기(미리 보기)를 엽니다.
1.	왼쪽 창의 상황에 맞는 메뉴에서 **저장소 계정**을 마우스 오른쪽 단추로 클릭하고 **SAS를 사용하여 계정 연결**을 선택합니다. ![][15]

1. **SAS를 사용하여 계정 연결** 대화 상자에서:

	- **계정 이름** - 이 계정과 연결하려는 이름을 입력합니다. **참고:** 계정 이름은 SAS가 생성된 원래 저장소 계정 이름과 일치하지 않아도 됩니다. 
 	- **연결 문자열** - 앞에서 복사한 연결 문자열을 붙여 넣습니다.
 	- 완료되면 **확인**을 선택합니다.
	
	![][16]

연결되면 저장소 계정이 제공한 계정 이름에 추가된 텍스트(SAS)와 함께 표시됩니다.

![][17]

## SAS를 사용하여 서비스 연결

섹션 [SAS를 사용하여 계정 연결](#attach-account-using-sas)은 Azure 구독 관리자가 저장소 계정에 대한 SAS를 생성(및 공유)하여 저장소 계정에 대한 임시 액세스를 부여할 수 있는 방법을 보여 줍니다. 마찬가지로, SAS는 저장소 계정 내에서 특정 서비스(Blob 컨테이너, 큐 또는 테이블)에 대해 생성될 수 있습니다.

### 공유하려는 서비스에 대한 SAS 생성

이 컨텍스트에서 서비스는 Blob 컨테이너, 큐 또는 테이블일 수 있습니다. 다음 섹션은 나열된 서비스에 대한 SAS를 생성하는 방법을 설명합니다.

- [Blob 컨테이너에 대한 SAS 가져오기](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- 큐에 대한 SAS 가져오기 - *서비스 예정*
- 테이블에 대한 SAS 가져오기 - *서비스 예정*

### SAS를 사용하여 공유 계정 서비스에 연결

1.	저장소 탐색기(미리 보기)를 엽니다.
1.	왼쪽 창의 상황에 맞는 메뉴에서 **저장소 계정**을 마우스 오른쪽 단추로 클릭하고 **SAS를 사용하여 서비스 연결**을 선택합니다. ![][18]

1. **SAS를 사용하여 계정 연결** 대화 상자에서 이전에 복사한 SAS URI에 붙여 넣고 **확인**을 선택합니다.

	![][19]

연결되면 새로 연결된 서비스가 **(서비스 SAS)** 노드 아래에 표시됩니다.

![][20]

## 저장소 계정 검색

저장소 계정 목록이 긴 경우 특정 저장소 계정을 찾는 빠른 방법은 왼쪽 창 맨 위에 있는 검색 상자를 사용하는 것입니다.

검색 상자에 내용을 입력하면 왼쪽 창에 해당 시점에 입력한 검색 값과 일치하는 저장소 계정만 표시됩니다. 다음 스크린샷은 저장소 계정 이름이 "tarcher" 텍스트를 포함하는 모든 저장소 계정에 대해 검색한 위치의 예를 보여 줍니다.

![][11]
	
검색을 지우려면 검색 상자의 **x** 단추를 선택합니다.

## 다음 단계
- [저장소 탐색기(미리 보기)를 사용하여 Azure Blob 저장소 리소스 관리](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-gear.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-subscriptions.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-accounts.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/accounts-drop-down.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-context-menu.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-dlg.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-context-menu.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-dlg.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png

<!---HONumber=AcomDC_0608_2016-->