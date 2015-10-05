<properties
   pageTitle="Emulator Express를 사용하여 클라우드 서비스를 로컬 컴퓨터에서 실행 및 디버그 | Microsoft Azure"
   description="Emulator Express를 사용하여 클라우드 서비스를 로컬 컴퓨터에서 실행 및 디버그"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/14/2015"
   ms.author="patshea" />


# Emulator Express를 사용하여 클라우드 서비스를 로컬 컴퓨터에서 실행 및 디버그

Emulator Express를 사용하여 관지라로 Visual Studio를 실행하지 않고 클라우드 서비스를 테스트 및 디버그할 수 있습니다. 클라우드 서비스의 요구 사항에 따라 Emulator Express 또는 전체 에뮬레이터를 사용하도록 프로젝트를 설정할 수 있습니다. 전체 에뮬레이터에 대한 자세한 내용은 [계산 에뮬레이터에서 Azure 응용 프로그램 실행](../storage/storage-use-emulator.md)을 참조하세요. Emulator Express는 Azure SDK 2.1에 처음 포함되었으며 Azure SDK 2.3에서는 기본 에뮬레이터입니다.

## Visual Studio IDE에서에서 Emulator Express 사용

Azure SDK 2.3 이상에서 새 프로젝트를 만들 때 Emulator Express가 이미 선택되어 있습니다. 이전 버전의 SDK를 사용하여 만든 기존 프로젝트의 경우 Emulator Express를 선택하려면 다음 단계를 수행합니다.

### Emulator Express를 사용하여 프로젝트 구성하기

1. Azure 프로젝트에 대한 바로 가기 메뉴에서 **속성**을 선택한 다음 **웹** 탭을 선택합니다.

1. **로컬 개발 서버** 아래에서 **IIS Express 사용 옵션** 단추를 선택합니다. Emulator Express는 IIS 웹 서버와 호환되지 않습니다.

1. **에뮬레이터** 아래에서 **Emulator Express 사용** 옵션 단추를 선택합니다.

    ![Emulator Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## 명령 프롬프트에서 Emulator Express 시작

명령 프롬프트에서 /useemulatorexpress 옵션을 사용하여 Azure 계산 에뮬레이터의 Express 버전인 csrun.exe를 시작할 수 있습니다.

## 제한 사항

Emulator Express를 사용하려면 다음과 같은 몇 가지 제한 사항을 알고 있어야 합니다.

- Emulator Express는 IIS 웹 서버와 호환되지 않습니다.

- 클라우드 서비스는 여러 역할을 포함할 수 있지만 각 역할은 하나의 인스턴스로 제한됩니다.

- 1000 아래의 포트 번호에 액세스할 수 없습니다. 예를 들어 일반적으로 1000 아래의 포트를 사용하는 인증 공급자를 사용하는 경우 1000 위의 포트 번호로 이 값을 변경해야 할 수도 있습니다.

- Azure 계산 에뮬레이터에 적용되는 모든 제한 사항은 Emulator Express에도 적용됩니다. 예를 들어 배포당 50개 이상의 역할 인스턴스를 가질 수 없습니다. [계산 에뮬레이터에서 Azure 응용 프로그램 실행](http://go.microsoft.com/fwlink/p/?LinkId=623050) 참조

## 다음 단계

[클라우드 서비스 디버깅](https://msdn.microsoft.com/library/azure/ee405479.aspx)

<!---HONumber=Sept15_HO4-->