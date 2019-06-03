---
title: 만들기 및 Azure 공간 앵커를 사용 하 여 Objective-c에서 앵커를 찾는 방법 | Microsoft Docs
description: 자세한 설명은 만들고 Azure 공간 앵커를 사용 하 여 목표 C에서 앵커를 찾는 방법
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 58d3b3e3da518c9f6c32924ec29fbec3c810b58e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242332"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-objective-c"></a>만들기 및 Azure 공간 앵커를 사용 하 여 Objective-c에서 앵커를 찾는 방법

> [!div  class="op_single_selector"]
> * [Unity](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

Azure Spatial Anchors를 사용하면 다양한 디바이스 간에 전 세계 앵커를 공유할 수 있습니다. 여러 가지 다양한 개발 환경을 지원합니다. 이 문서에서는 Azure 공간 앵커 SDK Objective-c를 사용 하는 방법에 자세히 알아봅니다.

- 올바르게 설정 하 고 Azure 공간 앵커 세션을 관리 합니다.
- 페이지를 만들고 로컬 앵커에 속성을 설정 합니다.
- 클라우드로 업로드 합니다.
- 찾아서 클라우드 공간 앵커를 삭제 합니다.

## <a name="prerequisites"></a>필수 조건

이 가이드를 완료 하려면 했는지 확인 합니다.

- [Azure Spatial Anchors 개요](../overview.md)를 자세히 읽었습니다.
- [5분 빠른 시작](../index.yml) 중 하나를 완료했습니다.
- C. 목표에 대 한 기본 지식이
- 에 대 한 기본 지식이 <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a>합니다.

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

에 대 한 자세한 정보는 [ASACloudSpatialAnchorSession](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession) 클래스입니다.

```objc
    ASACloudSpatialAnchorSession *_cloudSession;
    // In your view handler
    _cloudSession = [[ASACloudSpatialAnchorSession alloc] init];
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

에 대 한 자세한 정보는 [ASASessionConfiguration](https://docs.microsoft.com/objectivec/api/spatial-anchors/asasessionconfiguration) 클래스입니다.

```objc
    _cloudSession.configuration.accountKey = @"MyAccountKey";
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```objc
    _cloudSession.configuration.accessToken = @"MyAccessToken";
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

에 대 한 자세한 정보는 [tokenRequired](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#tokenrequired) 메서드 프로토콜입니다.

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        args.accessToken = @"MyAccessToken";
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        ASACloudSpatialAnchorSessionDeferral *deferral = [args getDeferral];
        [myGetTokenAsync callback:^(NSString *myToken) {
            if (myToken) args.accessToken = myToken;
            [deferral complete];
        }];
    }
```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```objc
    _cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        args.authenticationToken = @"MyAuthenticationToken";
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```objc
    - (void)tokenRequired:(ASACloudSpatialAnchorSession *)cloudSession :(ASATokenRequiredEventArgs *)args {
        ASACloudSpatialAnchorSessionDeferral *deferral = [args getDeferral];
        [myGetTokenAsync callback:^(NSString *myToken) {
            if (myToken) args.authenticationToken = myToken;
            [deferral complete];
        }];
    }
```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-ios.md)]

에 대 한 자세한 정보는 [시작](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#start) 메서드.

```objc0
    _cloudSession.session = self.sceneView.session;
    _cloudSession.delegate = self;
    [_cloudSession start];
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

에 대 한 자세한 정보는 [processFrame](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#processframe) 메서드.

```objc
    [_cloudSession processFrame:_sceneView.session.currentFrame];
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

에 대 한 자세한 정보는 [sessionUpdated](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#sessionupdated) 메서드 프로토콜입니다.

```objc
    - (void)sessionUpdated:(ASACloudSpatialAnchorSession *)cloudSession :(ASASessionUpdatedEventArgs *)args {
        ASASessionStatus *status = [args status];
        if (status.userFeedback == ASASessionUserFeedbackNone) return;
        _feedback = [NSString
            stringWithFormat:@"Feedback: %@ - Recommend Create=%.0f%%",
            FeedbackToString(status.userFeedback),
            status.recommendedForCreateProgress * 100.f];
    }
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

에 대 한 자세한 정보는 [ASACloudSpatialAnchor](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor) 클래스입니다.

```objc
    // Create a local anchor, perhaps by hit-testing and creating an ARAnchor
    NSArray<ARHitTestResult *> *hits = [_sceneView.session.currentFrame hitTest:CGPointMake(0.5, 0.5) types:ARHitTestResultTypeEstimatedHorizontalPlane];
    if ([hits count] == 0) return;
    // The hitTest method sorts the resulting list by increasing distance from the camera
    // The first hit result will usually be the most relevant when responding to user input
    ARAnchor *localAnchor = [[ARAnchor alloc] initWithTransform:hits[0].worldTransform];
    [_sceneView.session addAnchor:localAnchor];

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    ASACloudSpatialAnchor *cloudAnchor = [[ASACloudSpatialAnchor alloc] init];
    cloudAnchor.localAnchor = localAnchor;
    [_cloudSession createAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Save Failed:%@", error.localizedDescription];
            return;
        }
        _feedback = [NSString stringWithFormat:@"Created a cloud anchor with ID=%@", cloudAnchor.identifier];
    }];
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

에 대 한 자세한 정보는 [getSessionStatusWithCompletionHandler](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getsessionstatus) 메서드.

```objc
    [_cloudSession getSessionStatusWithCompletionHandler:^(ASASessionStatus *value, NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Session status error:%@", error.localizedDescription];
            return;
        }
        if (value.recommendedForCreateProgress < 1.0f) return;
        // Issue the creation request ...
    }];
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

에 대 한 자세한 정보는 [appProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor#appproperties) 속성입니다.

```objc
    ASACloudSpatialAnchor *cloudAnchor = [[ASACloudSpatialAnchor alloc] init];
    cloudAnchor.localAnchor = localAnchor;
    cloudAnchor.appProperties = @{ @"model-type" : @"frame", @"label" : @"my latest picture" };
    [_cloudSession createAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        // ...
    });
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

에 대 한 자세한 정보는 [updateAnchorProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#updateanchorproperties) 메서드.

```objc
    ASACloudSpatialAnchor *anchor = /* locate your anchor */;
    [anchor.appProperties setValue:@"just now" forKey:@"last-user-access"];
    [_cloudSession updateAnchorProperties:anchor withCompletionHandler:^(NSError *error) {
        if (error) _feedback = [NSString stringWithFormat:@"Updating Properties Failed:%@", error.localizedDescription];
    }];
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

에 대 한 자세한 정보는 [getAnchorProperties](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getanchorproperties) 메서드.

```objc
    [_cloudSession getAnchorProperties:@"anchorId" withCompletionHandler:^(SCCCloudSpatialAnchor *anchor, NSError *error) {
        if (error) {
            _feedback = [NSString stringWithFormat:@"Getting Properties Failed:%@", error.localizedDescription];
            return;
        }
        if (anchor) {
            [anchor.appProperties setValue:@"just now" forKey:@"last-user-access"];
            [_cloudSession updateAnchorProperties:anchor withCompletionHandler:^(NSError *error) {
                // ...
            }];
        }
    }];
```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

에 대 한 자세한 정보는 [만료](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor#expiration) 속성입니다.

```objc
    int secondsInAWeek = 60 * 60 * 24 * 7;
    NSDate *oneWeekFromNow = [[NSDate alloc] initWithTimeIntervalSinceNow: (NSTimeInterval) secondsInAWeek];
    cloudAnchor.expiration = oneWeekFromNow;
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

에 대 한 자세한 정보는 [createWatcher](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#createwatcher) 메서드.

```objc
    ASAAnchorLocateCriteria *criteria = [ASAAnchorLocateCriteria new];
    criteria.identifiers = @[ @"id1", @"id2", @"id3" ];
    [_cloudSession createWatcher:criteria];
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

에 대 한 자세한 정보는 [anchorLocated](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#anchorlocated) 메서드 프로토콜입니다.

```objc
    - (void)anchorLocated:(ASACloudSpatialAnchorSession *)cloudSession :(ASAAnchorLocatedEventArgs *)args {
        ASALocateAnchorStatus status = [args status];
        switch (status) {
        case ASALocateAnchorStatusLocated: {
            ASACloudSpatialAnchor *foundAnchor = [args anchor];
            // Go add your anchor to the scene...
        }
            break;
        case ASALocateAnchorStatusAlreadyTracked:
            // This anchor has already been reported and is being tracked
            break;
        case ASALocateAnchorStatusNotLocatedAnchorDoesNotExist:
            // The anchor was deleted or never existed in the first place
            // Drop it, or show UI to ask user to anchor the content anew
            break;
        case ASALocateAnchorStatusNotLocated:
            // The anchor hasn't been found given the location data
            // The user might in the wrong location, or maybe more data will help
            // Show UI to tell user to keep looking around
            break;
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

에 대 한 자세한 정보는 [deleteAnchor](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#deleteanchor) 메서드.

```objc
    [_cloudSession deleteAnchor:cloudAnchor withCompletionHandler:^(NSError *error) {
        // Perform any processing you may want when delete finishes
    }];
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

에 대 한 자세한 정보는 [중지](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#stop) 메서드.

```objc
    [_cloudSession stop];
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

에 대 한 자세한 정보는 [재설정](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#reset) 메서드.

```objc
    [_cloudSession reset];
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-others.md)]

```objc
    _cloudSession = NULL;
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
