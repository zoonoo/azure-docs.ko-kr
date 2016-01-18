<properties 
	pageTitle="Azure CDN 끝점 제거" 
	description="CDN 끝점에서 캐시된 콘텐츠를 모두 제거하는 방법을 알아봅니다." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="casoper"/>
	
# Azure CDN 끝점 제거

## 개요 

Azure CDN 가장자리 노드는 자산의 TTL(Time-to-Live)이 만료될 때 자산을 캐시합니다. 자산의 TTL이 만료된 후에 클라이언트가 가장자리 노드에서 자산을 요청하는 경우 가장자리 노드는 새로 업데이트된 자산 사본을 검색하여 클라이언트 요청을 처리하고 저장소는 캐시를 새로 고칩니다.

경우에 따라 모든 가장자리 노드에서 캐시된 콘텐츠를 제거하고 이러한 콘텐츠가 모두 새로 업데이트된 자산을 검색하도록 강제하려 할 수 있습니다. 웹 응용 프로그램에 대한 업데이트 또는 잘못된 정보가 포함된 신속한 업데이트 자산 때문일 수 있습니다.

이 자습서는 끝점의 모든 가장자리 노드에서 자산을 제거하는 과정을 안내합니다.

## 연습

1. [Azure 포털](http://portal.azure.com)에서 제거하려는 끝점을 포함하는 CDN 프로필로 이동합니다.

2. CDN 프로필 블레이드에서 제거 단추를 클릭합니다.
	
	![CDN 프로필 블레이드](./media/cdn-purge-endpoint/cdn-profile-blade.png)
	
	제거 블레이드가 열립니다.
	
	![CDN 제거 블레이드](./media/cdn-purge-endpoint/cdn-purge-blade.png)
	
3. 제거 블레이드에서 URL 드롭다운 목록에서 제거하려는 서비스 주소를 선택합니다.

	![양식 제거](./media/cdn-purge-endpoint/cdn-purge-form.png)
	
	> [AZURE.NOTE]또한 CDN 끝점 블레이드에서 **제거** 단추를 클릭하여 제거 블레이드로 가져올 수 있습니다. 이 경우에 **URL** 필드는 해당 특정 끝점의 서비스 주소로 미리 채워질 수 있습니다.
	
4. 가장자리 노드에서 제거하려는 자산을 선택합니다. 모든 자산을 지우려면 **모두 제거** 확인란을 클릭합니다. 그렇지 않은 경우 제거하려는 각 자산의 전체 경로를 **경로** 텍스트 상자에 입력합니다.(예: */pictures/kitten.png*)

	> [AZURE.TIP]여러 자산 목록을 작성할 수 있도록 하는 텍스트를 입력한 후에 더 많은 **경로** 텍스트 상자가 표시됩니다. 목록에서 줄임표(...) 단추를 클릭하여 자산을 삭제할 수 있습니다.
	>
	> 경로는 상대 URL이어야 합니다. 별표(*)를 와일드 카드로 사용할 수 있습니다.
	
5. **제거** 단추를 클릭합니다.

	![제거 단추](./media/cdn-purge-endpoint/cdn-purge-button.png)
	

## 참고 항목
[Azure CDN REST API 참조 - 끝점 제거 또는 미리 로드](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0107_2016-->