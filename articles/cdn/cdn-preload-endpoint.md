<properties 
	pageTitle="Azure CDN 끝점에 자산 미리 로드" 
	description="CDN 끝점에 캐시된 콘텐츠를 미리 로드하는 방법을 알아봅니다." 
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
	ms.date="01/20/2016" 
	ms.author="casoper"/>
	
# Azure CDN 끝점에 자산 미리 로드

이 자습서는 모든 Azure CDN 에지 노드에 캐시된 콘텐츠 미리 로드에 대해 안내합니다.

## 연습

1. [Azure 포털](http://portal.azure.com)에서 미리 로드하려는 끝점을 포함하는 CDN 프로필로 이동합니다. 프로필 블레이드가 열립니다.

2. 목록에서 끝점을 클릭합니다. 끝점 블레이드가 열립니다.

3. CDN 끝점 블레이드에서 로드 단추를 클릭합니다.
	
	![CDN 끝점 블레이드](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
	
	로드 블레이드가 열립니다.
	
	![CDN 로드 블레이드](./media/cdn-preload-endpoint/cdn-load-blade.png)
	
4. 로드하려는 각 자산의 전체 경로를 **경로** 텍스트 상자에 입력합니다(예: /pictures/kitten.png).

	> [AZURE.TIP]여러 자산 목록을 작성할 수 있도록 하는 텍스트를 입력한 후에 더 많은 **경로** 텍스트 상자가 표시됩니다. 목록에서 줄임표(...) 단추를 클릭하여 자산을 삭제할 수 있습니다.
	>
	> 경로는 상대 URL이어야 합니다. 별표(*)를 와일드 카드로 사용할 수 있습니다.
	
    ![로드 단추](./media/cdn-preload-endpoint/cdn-load-paths.png)
    
5. **로드** 단추를 클릭합니다.

	![로드 단추](./media/cdn-preload-endpoint/cdn-load-button.png)
	

## 참고 항목
- [Azure CDN 끝점 제거](cdn-purge-endpoint.md)
- [Azure CDN REST API 참조 - 끝점 제거 또는 미리 로드](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0121_2016-->