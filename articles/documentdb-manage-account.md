<properties 
	pageTitle="DocumentDB 계정 관리 | Azure" 
	description="DocumentDB 계정을 관리하는 방법을 알아봅니다." 
	services="documentdb" 
	documentationCenter="" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="stbaro"/>

#DocumentDB 계정을 관리하는 방법

키, 일관성 설정, 용량 설정을 사용하는 방법을 배우고 계정을 삭제하는 방법을 알아봅니다.

## <a id="keys"></a>액세스 키 보기, 복사 및 다시 생성
DocumentDB 계정을 만들면 해당 서비스에서 DocumentDB 계정에 액세스할 때 인증에 사용할 수 있는 2개의 마스터 액세스 키가 생성됩니다. DocumentDB에서는 2개의 액세스 키를 제공해서 사용자가 DocumentDB 계정에 대한 중단 없이 키를 다시 생성할 수 있습니다.

[Azure Preview 포털](https://portal.azure.com/)에서 DocumentDB 계정에 액세스하는 데 사용되는 액세스 키를 표시, 복사 및 다시 생성하려면 **DocumentDB 계정** 블레이드에서 **키** 부분에 액세스합니다.

![](../includes/media/documentdb-keys/keys.png)

### 액세스 키 보기 및 복사

1.      [Azure Preview 포털](https://portal.azure.com/)에서 DocumentDB 계정에 액세스합니다.

2.      **요약** 렌즈에서 **키**를 클릭합니다.

3.      **키** 블레이드에서 복사할 키 오른쪽의 **복사** 단추를 클릭합니다.

  ![](./media/documentdb-manage-account/image004.jpg)

### 액세스 키 다시 생성

저장소 연결을 더욱 안전하게 유지할 수 있도록 정기적으로 DocumentDB 계정의 액세스 키를 변경해야 합니다. 두 개의 액세스 키가 할당되므로 액세스 키 하나를 다시 생성하는 동안 다른 액세스 키를 사용하여 DocumentDB 계정에 대한 연결을 유지할 수 있습니다.

> [AZURE.WARNING] 액세스 키를 다시 생성하면 현재 키에 종속된 모든 응용 프로그램에 영향을 줍니다. 액세스 키를 사용하여 DocumentDB 계정에 액세스하는 모든 클라이언트가 새 키를 사용하도록 업데이트되어야 합니다.

DocumentDB 계정을 사용하는 웹 응용 프로그램이나 클라우드 서비스가 있는 경우 키를 롤링하지 않고 다시 생성하면 연결이 끊어집니다. 다음 단계에서는 키 롤링에 관련된 프로세스를 간략하게 설명합니다.

1.      DocumentDB 계정의 보조 액세스 키를 참조하도록 응용 프로그램 코드의 액세스 키를 업데이트합니다.

2.      DocumentDB 계정의 기본 액세스 키를 다시 생성합니다. [Azure Preview 포털](https://portal.azure.com/)에서 DocumentDB 계정에 액세스합니다.

3.      요약 렌즈에서 **키**를 클릭합니다.

4.      **키** 블레이드에서 **기본 키 다시 생성** 명령을 클릭하고 **확인**을 클릭하여 새 키를 생성할지 확인합니다.

5.      키를 다시 생성하고 약 5분 후에 새 키를 사용할 수 있는지 확인한 후 응용 프로그램 코드에서 새 기본 액세스 키를 참조하도록 액세스 키를 업데이트합니다.

6.      보조 액세스 키를 다시 생성합니다.

*새로 생성된 키를 사용하여 DocumentDB 계정에 액세스할 수 있을 때까지 몇 분 정도 걸릴 수 있습니다.*

## <a id="consistency"></a>DocumentDB 일관성 설정 관리
DocumentDB는 개발자가 일관성, 가용성 및 대기 시간을 예측 가능하게 절충할 수 있도록 효율적으로 정의된 네 가지 사용자 구성 가능 데이터 일관성 수준을 지원합니다.

- **강력**: 이 일관성 수준에서는 읽기 작업이 항상 마지막으로 기록된 값을 반환하도록 보장합니다.

- **제한된 부실**: 이 일관성 수준에서는 읽기 작업이 너무 오래된 상태가 아님을 보장합니다. 구체적으로 설명하자면, 읽기가 마지막으로 기록된 버전에서 K개 버전 이내임을 보장합니다. 

- **세션**: 이 일관성 수준에서는 일정한 읽기(이전 데이터를 읽고 새 데이터를 읽은 후에 이전 데이터를 다시 읽지 않음)와 일정한 쓰기(순서에 따라 쓰기가 수행됨)를 보장하며 단일 클라이언트의 관점에서 최신 쓰기 내용을 읽을 수 있음을 보장합니다.

- **최종**: 이 일관성 수준에서는 읽기 작업이 항상 유효한 쓰기 하위 집합을 읽으며 최종적으로는 수렴되도록 보장합니다.

*기본적으로 DocumentDB 계정은 세션 수준 일관성을 사용해서 프로비전됩니다. DocumentDB 일관성 설정에 대한 추가 정보는 [일관성 수준](http://go.microsoft.com/fwlink/p/?LinkId=402365) 섹션을 참조하세요.*

### DocumentDB 계정의 기본 일관성을 지정하려면

1.      [Azure Preview 포털](https://portal.azure.com/)에서 DocumentDB 계정에 액세스합니다.

2.      **구성** 렌즈에서 l**기본 일관성**을 클릭합니다.

3.      **기본 일관성** 블레이드에서 DocumentDB 계정에 적용할 기본 일관성 수준을 선택합니다.

![](./media/documentdb-manage-account/image005.png)

![](./media/documentdb-manage-account/image006.png)

4.      **저장**을 클릭합니다.

5.      Azure Preview 포털 알림 허브를 통해 작업 진행률을 모니터링할 수 있습니다.

*기본 일관성 설정 변경 내용이 DocumentDB 계정에 적용될 때까지 몇 분 정도 걸릴 수 있습니다.*

## <a id="capacity"></a>DocumentDB 용량 설정 관리
Microsoft Azure DocumentDB를 사용하면 전체 수명 주기 동안 응용 프로그램의 수요 변경에 맞춰 탄력적으로 크기를 조정할 수 있습니다. DocumentDB의 크기를 조정할 때는 Azure Preview 포털을 통해 DocumentDB 데이터베이스 계정의 용량을 늘립니다.
데이터베이스 계정을 만들면 데이터베이스 저장소 및 예약된 처리량을 사용하여 계정이 프로비전됩니다. 언제든지 Azure Preview 포털을 통해 용량 단위를 추가하거나 제거하여 계정에 대해 프로비전된 데이터베이스 저장소 및 처리량을 변경할 수 있습니다.

### 용량 단위를 추가하거나 제거하려면

1.      [Azure Preview 포털](https://portal.azure.com/)에서 DocumentDB 계정에 액세스합니다.

2.      **사용량** 렌즈에서 **크기 조정**을 클릭합니다.

3.      **크기 조정** 블레이드에서 DocumentDB 계정에 대해 사용할 용량 단위를 지정합니다.


![](./media/documentdb-manage-account/image007.png)

4.      **저장**을 클릭합니다. 크기 조정 작업이 완료되려면 몇 분 정도 걸릴 수 있습니다. Azure Preview 포털 알림 허브를 통해 진행률을 모니터링할 수 있습니다.

 *DocumentDB 미리 보기에서는 DocumentDB 계정당 용량 단위를 5개까지
지원합니다.*
 

## <a id="delete"></a> 방법: DocumentDB 계정 삭제
더 이상 사용하지 않는 DocumentDB 계정을 제거하려면 **DocumentDB 계정** 블레이드에서** 삭제** 명령을 사용합니다.

> [AZURE.WARNING] 미리 보기 릴리스에서는 삭제된 DocumentDB 계정에서 콘텐츠를 복원할 수 있는 방법이 없습니다. DocumentDB 계정을 삭제하면 데이터베이스, 컬렉션, 문서 및 첨부 파일을 포함해서 해당 계정의 모든 리소스가 삭제됩니다.

![](./media/documentdb-manage-account/image009.png)

1.      [Azure Preview 포털](https://portal.azure.com/)에서 삭제할 DocumentDB 계정에 액세스합니다.

2.      **DocumentDB 계정** 블레이드에서 **삭제** 명령을 클릭합니다.

3.      그러면 표시되는 확인 블레이드에서 DocumentDB 계정 이름을 입력하여 계정을 삭제할 것임을 확인합니다.

4.      확인 블레이드에서 **삭제** 단추를 클릭합니다.

## <a id="next"></a>다음 단계

[DocumentDB 계정 사용을 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkId=402364)을 알아봅니다.

DocumentDB에 대한 자세한 내용은

[azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409)에서 Azure DocumentDB 설명서를 참조하세요.

 

<!--HONumber=49-->