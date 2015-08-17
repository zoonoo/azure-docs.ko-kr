

<properties 
	pageTitle="Azure AD Connect 선언적 프로비전 사용" 
	description="Azure AD Connect 선언적 프로비전을 사용하는 방법에 대해 알아봅니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Azure AD Connect 선언적 프로비전


## 선언적 프로저닝 사용 
선언적 프로비저닝은 "코드 없는" 프로비저닝으로, 동기화 규칙 편집기를 사용하여 설정 및 구성할 수 있습니다. 편집기를 사용하여 고유한 프로비저닝 규칙을 설정하고 만들 수도 있습니다.

선언적 프로비저닝의 핵심적인 부분은 특성 흐름에 사용되는 표현 언어입니다. 사용 되는 언어는 VBA(Microsoft® Visual Basic® for Applications)의 하위 집합입니다. 이 언어는 Microsoft Office에서 사용되며, VBScript 경험이 있는 사용자 또한 이 언어를 인식합니다. 선언적 프로비저닝 표현 언어는 함수만 사용하며 구조적 언어는 아닙니다. 메서드 또는 문이 없습니다. 대신, 빠른 프로그램 흐름에 함수가 중첩됩니다.

표현 언어에 대한 자세한 내용은[선언적 프로비저닝 표현식 이해](https://msdn.microsoft.com/library/azure/dn801048.aspx)를 참조하세요.

<!---HONumber=August15_HO6-->