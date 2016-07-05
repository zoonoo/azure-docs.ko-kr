<properties
	pageTitle="Azure 대시보드에 대한 공유 및 액세스 제어 | Microsoft Azure"
	description="Azure 대시보드에 대한 공유 및 액세스 제어를 관리하는 방법에 대해 알아봅니다."
	services="azure-resource-manager,azure-portal"
    keywords="포털, 공유, 액세스"
	documentationCenter=""
	authors="adamabdelhamed"
	manager="dend"
	editor="dend"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="adamab"/>
    
# Azure 대시보드에 대한 공유 및 액세스 제어

포털에서 대부분 타일에 표시되는 정보에 대한 액세스는 Azure [역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)로 제어됩니다. 대시보드를 환경에 원활하게 통합하기 위해 게시된 모든 대시보드가 Azure 리소스로 구현됩니다. 액세스 제어 관점에서 대시보드는 가상 컴퓨터 또는 저장소 계정과 차이가 없습니다.

다음은 예제입니다. Azure 구독을 보유하고 구독의 **소유자**, **참여자** 또는 **읽기 권한자** 역할에 할당된 팀의 다양한 멤버가 있다고 가정해 보겠습니다. 소유자 또는 참여자인 사용자는 구독 내에서 대시보드를 나열, 보기, 만들기, 수정 또는 삭제할 수 있습니다. 읽기 권한자인 사용자는 대시보드를 나열 및 볼 수 있지만 수정 또는 삭제할 수 없습니다. 읽기 권한자 액세스를 보유한 사용자는 게시된 대시보드에 대한 로컬 편집을 만들 수 있지만(예: 문제 해결 시) 해당 변경 내용을 서버로 다시 게시하는 옵션은 없습니다. 대시보드의 개인 복사본을 직접 만들 수 있는 옵션을 갖게 됩니다.

대시보드의 개별 타일은 데이터를 보여주는 리소스에 따라 고유한 액세스 제어 요구 사항을 적용합니다. 즉, 개별 타일에 있는 데이터를 보호하면서 보다 광범위하게 공유할 수 있는 대시보드를 디자인할 수 있습니다.

<!---HONumber=AcomDC_0622_2016-->