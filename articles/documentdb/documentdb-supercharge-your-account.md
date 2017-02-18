---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-performance-levels
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 94ae24e605f99e23766702a3954db567bb64bb51
ms.openlocfilehash: 1c055187405b9c858676b2b80e4bc5c4157ef580


---
# <a name="migrate-your-documentdb-s1-s2-or-s3-account"></a>DocumentDB S1, S2 또는 S3 계정 마이그레이션
다음 단계를 수행하여 표준 가격 책정 계층으로 이동하여 Azure DocumentDB 계정에 대한 향상된 처리량을 활용합니다. 추가 비용 없이 250 [RU/s](documentdb-request-units.md) 에서 400RUs/ 이상으로 기존 S1 계정의 처리량을 늘릴 수 있습니다! 모든 표준 계정은 응용 프로그램의 요구를 충족하기 위해 처리량 규모를 조정할 수 있는 이점을 누릴 수 있습니다. 더 이상 미리 정의된 처리량 옵션에서 선택할 필요가 없으며, 필요할 때마다 규모를 조정하여 응용 프로그램에서 요구하는 처리량을 얻을 수 있습니다. 

## <a name="change-to-user-defined-performance-in-the-azure-portal"></a>Azure 포털에서 사용자 정의 성능 변경
1. 브라우저에서 [**Azure Portal**](https://portal.azure.com)로 이동합니다. 
2. 왼쪽 메뉴에서 **NoSQL(DocumentDB)**을 클릭하거나, **더 많은 서비스**를 클릭하여 **데이터베이스**로 이동한 다음 **NoSQL(DocumentDB)**을 선택합니다.   
3. **NoSQL(DocumentDB)** 블레이드에서 수정할 계정을 선택합니다.
4. 새 블레이드의**컬렉션** 메뉴에서 **규모**를 클릭합니다. 

      ![DocumentDB 설정의 스크린샷 및 가격 책정 계층 블레이드 선택](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)
5. 위의 스크린샷에 표시된 것처럼 다음을 수행합니다. 

 - 새 블레이드에서 드롭다운 메뉴를 사용하여 S1, S2 또는 S3 가격 책정 계층의 컬렉션을 선택합니다. 
 - **가격 책정 계층 S1**, **S2** 또는 **S3**을 클릭합니다.
 - **가격 책정 계층 선택** 블레이드에서 **표준**을 클릭한 다음 **선택**을 클릭하여 변경 내용을 저장합니다.
   
6. **규모** 블레이드로 돌아가면 **처리량(RU/s)** 상자에 기본값인 400이 표시되며 **가격 책정 계층**은 **표준**으로 변경되어 있습니다.  응용 프로그램에서 필요한 값으로 처리량을 변경할 수 있습니다. [DocumentDB Capacity Planner](https://www.documentdb.com/capacityplanner)를 사용하여 응용 프로그램에서 필요한 초당 [요청 단위](documentdb-request-units.md)(RU/s) 수를 결정하는 것이 좋습니다. 월별 예상 비용을 제공하기 위해 페이지 아래쪽의 **월별 예상 비용** 영역이 자동으로 업데이트됩니다. **저장**을 클릭하여 변경 내용을 저장합니다. 
      
    ![처리량 값을 변경하는 위치를 보여 주는 설정 블레이드의 스크린 샷](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)
7. 컬렉션은 새 요구 사항을 충족하도록 규모가 조정되며 성공 메시지가 표시됩니다.  
   
    ![수정된 컬렉션과 데이터베이스 블레이드의 스크린샷](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

사용자 정의 처리량 및 미리 정의된 처리량과 관련된 변경 내용에 대한 자세한 내용은 블로그 게시물 [DocumentDB: 새 가격 책정 옵션 사용에 대해 알아야 하는 모든 항목](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure DocumentDB의 분할 및 크기 조정](documentdb-partition-data.md)에서 DocumentDB를 사용하여 데이터를 파티션하고 전역 규모를 달성하는 방법에 대해 자세히 알아 보세요.



<!--HONumber=Feb17_HO2-->


