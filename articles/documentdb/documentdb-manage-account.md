---
title: "Azure Portal을 통해 DocumentDB 계정 관리 | Microsoft Docs"
description: "Azure 포털을 통해 DocumentDB 계정을 관리하는 방법을 알아봅니다. Azure 포털을 사용하여 계정을 보기, 복사, 삭제 및 액세스하는 방법에 대한 지침을 찾습니다."
keywords: "Azure 포털, Documentdb, Azure, Microsoft Azure"
services: documentdb
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f38b5d6127897ba2b083e0f3bb41562650214ae9


---
# <a name="how-to-manage-a-documentdb-account"></a>DocumentDB 계정을 관리하는 방법
Azure 포털에서 전역 일관성을 설정하고, 키로 작업하고, DocumentDB 계정을 삭제하는 방법에 대해 알아봅니다.

## <a name="a-idconsistencyamanage-documentdb-consistency-settings"></a><a id="consistency"></a>DocumentDB 일관성 설정 관리
올바른 일관성 수준을 선택하는 것은 응용 프로그램의 의미 체계에 따라 달라집니다. [일관성 수준을 사용하여 DocumentDB의 가용성 및 성능 최대화][consistency]의 내용을 확인하여 DocumentDB에서 사용 가능한 일관성 수준을 숙지해야 합니다. DocumentDB는 데이터베이스 계정에서 사용할 수 있는 모든 일관성 수준에서 일관성, 가용성 및 성능을 보증합니다. 일관성 수준 '강력'으로 데이터베이스 계정을 구성하려면 데이터를 단일 Azure 지역에서만 사용할 수 있도록 제한하고 전 세계적으로 제공하지 않아야 합니다. 반면, 관대한 일관성 수준(제한된 부실, 세션 또는 최종 일관성)은 데이터베이스 계정과 여러 Azure 지역을 연결할 수 있습니다. 아래의 간단한 단계는 데이터베이스 계정에서 기본 일관성 수준을 선택하는 방법을 보여줍니다. 

### <a name="to-specify-the-default-consistency-for-a-documentdb-account"></a>DocumentDB 계정의 기본 일관성을 지정하려면
1. [Azure 포털](https://portal.azure.com/)에서 DocumentDB 계정에 액세스합니다.
2. 계정 블레이드에서 **기본 일관성**을 클릭합니다.
3. **기본 일관성** 블레이드에서 새 일관성 수준을 선택하고 **저장**을 클릭합니다.
    ![기본 일관성 세션][5]

## <a name="a-idkeysaview-copy-and-regenerate-access-keys"></a><a id="keys"></a>선택키 보기, 복사 및 다시 생성
DocumentDB 계정을 만들면 해당 서비스에서 DocumentDB 계정에 액세스할 때 인증에 사용할 수 있는 2개의 마스터 액세스 키가 생성됩니다. DocumentDB에서는 2개의 액세스 키를 제공해서 사용자가 DocumentDB 계정에 대한 중단 없이 키를 다시 생성할 수 있습니다. 

[Azure Portal](https://portal.azure.com/)에서 **DocumentDB 계정** 블레이드의 리소스 메뉴에 있는 **키** 블레이드에 액세스하여 DocumentDB 계정에 액세스하는 데 사용되는 선택키를 표시, 복사 및 다시 생성합니다.

![Azure 포털 스크린샷, 키 블레이드](./media/documentdb-manage-account/keys.png)

> [!NOTE]
> **키** 블레이드에는 [데이터 마이그레이션 도구](documentdb-import-data.md)에서 계정에 연결하는 데 사용할 수 있는 기본 및 보조 연결 문자열도 포함되어 있습니다.
> 
> 

읽기 전용 키도 이 블레이드에서 사용할 수 있습니다. 읽기 및 쿼리는 읽기 전용 작업이며 만들기, 삭제 및 바꾸기는 읽기 전용 작업이 아닙니다.

### <a name="copy-an-access-key-in-the-azure-portal"></a>Azure 포털에서 선택키 복사
**키** 블레이드에서 복사할 키 오른쪽의 **복사** 단추를 클릭합니다.

![Azure 포털에서 선택키 보기 및 복사, 키 블레이드](./media/documentdb-manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>액세스 키 다시 생성
저장소 연결을 더욱 안전하게 유지할 수 있도록 정기적으로 DocumentDB 계정의 액세스 키를 변경해야 합니다. 두 개의 액세스 키가 할당되므로 액세스 키 하나를 다시 생성하는 동안 다른 액세스 키를 사용하여 DocumentDB 계정에 대한 연결을 유지할 수 있습니다.

> [!WARNING]
> 액세스 키를 다시 생성하면 현재 키에 종속된 모든 응용 프로그램에 영향을 줍니다. 액세스 키를 사용하여 DocumentDB 계정에 액세스하는 모든 클라이언트가 새 키를 사용하도록 업데이트되어야 합니다.
> 
> 

DocumentDB 계정을 사용하는 웹 응용 프로그램이나 클라우드 서비스가 있는 경우 키를 롤링하지 않고 다시 생성하면 연결이 끊어집니다. 다음 단계에서는 키 롤링에 관련된 프로세스를 간략하게 설명합니다.

1. DocumentDB 계정의 보조 액세스 키를 참조하도록 응용 프로그램 코드의 액세스 키를 업데이트합니다.
2. DocumentDB 계정의 기본 액세스 키를 다시 생성합니다. [Azure 포털](https://portal.azure.com/)에서 DocumentDB 계정에 액세스합니다.
3. **DocumentDB 계정** 블레이드에서 **키**를 클릭합니다.
4. **키** 블레이드에서 다시 생성 단추를 클릭한 다음 **확인**을 클릭하여 새 키를 생성할 것임을 확인합니다.
    ![액세스 키 다시 생성](./media/documentdb-manage-account/regenerate-keys.png)
5. 키를 다시 생성하고 약 5분 후에 새 키를 사용할 수 있는지 확인한 후 응용 프로그램 코드에서 새 기본 액세스 키를 참조하도록 액세스 키를 업데이트합니다.
6. 보조 액세스 키를 다시 생성합니다.
   
    ![액세스 키 다시 생성](./media/documentdb-manage-account/regenerate-secondary-key.png)

> [!NOTE]
> 새로 생성된 키를 사용하여 DocumentDB 계정에 액세스할 수 있을 때까지 몇 분 정도 걸릴 수 있습니다.
> 
> 

## <a name="get-the-connection-string"></a>연결 문자열 가져오기
연결 문자열을 검색하려면 다음을 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에서 DocumentDB 계정에 액세스합니다.
2. 리소스 메뉴에서 **키**를 클릭합니다.
3. **기본 연결 문자열** 또는 **보조 연결 문자열** 상자 옆의 **복사** 단추를 클릭합니다. 

[DocumentDB 데이터베이스 마이그레이션 도구](documentdb-import-data.md)에서 연결 문자열을 사용하는 경우 연결 문자열 끝에 데이터베이스 이름을 추가합니다. `AccountEndpoint=< >;AccountKey=< >;Database=< >`에 설정해야 합니다에 설정해야 합니다.

## <a name="a-iddeletea-delete-a-documentdb-account"></a><a id="delete"></a> DocumentDB 계정 삭제
Azure Portal에서 더 이상 사용하지 않는 DocumentDB 계정을 제거하려면 **DocumentDB 계정** 블레이드에서 **계정 삭제** 명령을 사용합니다.

![Azure 포털에서 DocumentDB 계정을 삭제하는 방법](./media/documentdb-manage-account/deleteaccount.png)

1. [Azure 포털](https://portal.azure.com/)에서 삭제할 DocumentDB 계정에 액세스합니다.
2. **DocumentDB 계정** 블레이드에서 **더 보기**, **계정 삭제**를 차례로 클릭합니다. 또는 데이터베이스의 이름을 마우스 오른쪽 단추로 클릭한 다음 **계정 삭제**를 클릭합니다.
3. 그러면 표시되는 확인 블레이드에서 DocumentDB 계정 이름을 입력하여 계정을 삭제할 것임을 확인합니다.
4. **삭제** 단추를 클릭합니다.

![Azure 포털에서 DocumentDB 계정을 삭제하는 방법](./media/documentdb-manage-account/delete-account-confirm.png)

## <a name="a-idnextanext-steps"></a><a id="next"></a>다음 단계
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
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/



<!--HONumber=Nov16_HO3-->


