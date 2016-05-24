<properties
   pageTitle="서버 탐색기에서 Azure 가상 컴퓨터 액세스 | Microsoft Azure"
   description="Visure Studio에서 Azure 서버 탐색기 (VM) 만들기 및 관리하기를 보는 방법에 대한 개요를 가져옵니다."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="05/08/2016"
   ms.author="tarcher" />

# 서버 탐색기에서 Azure 가상 컴퓨터 액세스(영문)

Visure Studio에서 서버 탐색기를 사용하여 Azure에서 호스팅되는 가상 컴퓨터에 대한 정보를 표시할 수 있습니다.

## 서버 탐색기의 Azure 가상 컴퓨터 액세스

Azure에서 호스팅되는 가상 컴퓨터가 있는 경우 서버 탐색기에서 액세스할 수 있습니다. 처음에 Azure 구독에 로그인해야 모바일 서비스를 볼 수 있습니다. 로그인하려면 서버 탐색기에서 Azure 노드에 대한 바로 가기 메뉴를 열고 **Microsoft Azure에 연결**을 선택할 수 있습니다.

### 가상 컴퓨터에 대한 정보를 가져오려면

1. 서버 탐색기에서 가상 컴퓨터를 선택한 다음 F4 키를 선택하여 속성 창을 표시합니다.

    다음의 테이블은 사용 가능하지만 모두 읽기 전용인 속성을 보여줍니다. 변경하려면 [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)을 사용합니다.

  	|속성|설명|
  	|---|---|
  	|DNS 이름|가상 컴퓨터의 인터넷 주소 URL입니다.|
  	|Environment|가상 컴퓨터에 대한 이 속성의 값은 항상 프로덕션입니다.|
  	|이름|가상 컴퓨터의 이름입니다.|
  	|크기|사용 가능한 디스크 공간과 메모리의 양이 반영된 가상 컴퓨터의 크기입니다. 자세한 내용은 가상 컴퓨터 구성 방법을 참조하세요.|
  	|상태|값은 시작, 시작됨, 중지, 중지됨, 및 상태 검색을 포함합니다. 상태 검색이 나타나면 현재 상태는 알 수 없습니다. 이 속성의 값은 [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)에서 사용된 값과 다릅니다.|
  	|구독 ID|Azure 계정에 대한 구독 ID 구독 속성을 확인하여 [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)에 대한 정보를 표시할 수 있습니다.|

1. 끝점 노드를 선택한 다음 **속성** 창을 확인합니다.

1. 다음 테이블은 사용 가능하지만 읽기 전용인 끝점 속성을 설명합니다. 가상 컴퓨터에 대해 끝점을 추가 또는 편집하려면 [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=213885)을 사용합니다.

  	|속성|설명|
  	|---|---|
  	|이름|끝점에 대한 식별자입니다.|
  	|개인 포트|응용 프로그램의 내부 네트워크 액세스 용 포트입니다.|
  	|프로토콜|이 끝점에 대한 전송 계층이 사용하는 프로토콜로, TCP 또는 UDP입니다.|
  	|공용 포트|응용 프로그램에 대한 공용 액세스에 사용되는 포트입니다.|

## 다음 단계

Visual Studio에서 Azure 역할을 사용하는 방법에 대한 자세한 내용은 [Azure 역할로 원격 데스크톱 사용](vs-azure-tools-remote-desktop-roles.md)을 참조하세요.

<!---HONumber=AcomDC_0511_2016-->