---
title: Unity Roll a Ball 자습서
description: 모든 Mobile Engagement Unity의 필수 조건인 고전 Unity Roll a Ball 게임을 만드는 단계를 설명합니다.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0afd0eca-f74a-43aa-bba8-436a0265c312
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 52d5962645b1408fdba61ec1bf4e4f682b80905b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a id="unity-roll-a-ball"></a>Unity Roll a Ball 게임 만들기
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 자습서에서는 약간 수정된 [Unity Roll a Ball 자습서](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial)의 주요 단계를 다룹니다. 이 샘플 게임은 앱 사용자에 의해 제어되는 구 모양의 '플레이어' 개체로 구성되며, 플레이어 개체를 수집 가능한 개체와 충돌하여 수집 가능한 개체를 '수집'하는 것이 이 게임의 목표입니다. 이 자습서는 여러분이 Unity 편집기 환경에 대한 기본 지식을 갖춘 것으로 가정하고 진행됩니다. 문제가 발생하면 전체 자습서를 참조하셔야 합니다. 

### <a name="setting-up-the-game"></a>게임 설정
아래 단계는 [Unity 자습서](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. **Unity 편집기**를 열고 **새로 만들기**를 클릭합니다. 
   
    ![][51] 
2. **프로젝트 이름** & **위치**를 입력하고, **3D**를 선택한 다음 **프로젝트 만들기**를 클릭합니다.
   
    ![][52]
3. 방금 위에서 새 프로젝트의 일부로 만든 기본 장면을 **Assets** 폴더의 새로운 **\_Scenes** 폴더 안에 **MiniGame**이라는 이름으로 저장합니다.
   
    ![][53]
4. 플레이 필드로 **3D 개체 -> 평면**을 만들고 이 평면 개체의 이름을 **Ground**로 변경합니다.
   
    ![][1]
5. 이 **Ground** 개체가 원점에 위치하도록 변환 구성 요소를 다시 설정합니다. 
   
    ![][3]
6. **Ground** 개체의 **Gizmos 메뉴**에서 **눈금 표시**의 선택을 취소합니다.
   
    ![][4]
7. **Ground** 개체의 **규모** 구성 요소를 [X = 2,Y = 1, Z = 2]로 업데이트합니다. 
   
    ![][5]
8. 프로젝트에 새로운 **3D 개체 -> 구**를 추가하고 이 구 개체의 이름을 **Player**로 변경합니다. 
   
    ![][6]
9. **Player** 개체를 선택하고 평면 개체와 마찬가지로 **변형 다시 설정**을 클릭합니다. 
10. Player Y의 **변형 -> 위치 -> Y 좌표** 구성 요소를 0.5로 업데이트합니다.  
    
    ![][7]
11. 프로젝트에서 **Materials** 라는 새 폴더를 만들고 여기서 플레이어에게 색을 입힐 재질을 만들겠습니다. 
12. 이 폴더에 **백그라운드**라고 하는 새 **재질**을 만듭니다. 
    
    ![][8]
13. 재질의 **Albedo** 속성을 업데이트하여 재질 색상을 업데이트합니다. RGB 값으로 [0,32,64]를 선택할 수 있습니다. 
    
    ![][9]
14. 이 재질을 장면 보기로 끌어서 **Ground** 개체에 색상을 적용합니다. 
    
    ![][10]
15. 마지막으로 선명도를 높이기 위해 [Directional Light] 개체에서 **변형 -> 회전 -> Y**를 60으로 업데이트합니다. 
    
    ![][12]

### <a name="moving-the-player"></a>플레이어 업데이트
아래 단계는 [Unity 자습서](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. **Player** 개체에 **RigidBody** 구성 요소를 추가합니다. 
   
    ![][13]
2. 프로젝트에서 **Scripts** 라고 하는 새 폴더를 만듭니다. 
3. **구성 요소 추가 -> 새 스크립트 -> C# 스크립트**를 클릭합니다. 이름을 **PlayerController**로 지정하고 **만들기 및 추가**를 클릭합니다. 그러면 스크립트가 생성되어 플레이어 개체에 연결될 것입니다.  
   
    ![][14]
4. 이 스크립트를 프로젝트의 **Scripts** 폴더로 이동합니다. 
5. 스크립트를 편집하기 위해 선호하는 스크립트 편집기에서 스크립트를 열고, 다음 코드를 사용하여 스크립트 코드를 업데이트하고 저장합니다. 
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
6. 위의 스크립트는 **Scripts** 속성을 사용합니다. Unity 편집기에서 Scripts 속성을 10으로 업데이트합니다.  
   
    ![][15]
7. Unity 편집기에서 **Play** 를 누릅니다. 이제 키보드를 사용하여 공을 회전하고 주변을 이동하면서 제어할 수 있을 것입니다. 

### <a name="moving-the-camera"></a>카메라 이동
아래 단계는 [Unity 자습서](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141)에서 가져온 것이며 **주 카메라**를 **Player** 개체에 연결할 것입니다. 

1. **Transform.Position**을 X = 0, Y = 10.5, Z=-10으로 업데이트합니다.  
2. **Transform.Rotation** 을 X = 45, Y = 0, Z = 0으로 업데이트합니다.  
   
    ![][16]
3. **MainCamera**에 **CameraController**라고 하는 새 스크립트를 추가하고 해당 스크립트를 [Scripts] 폴더로 이동합니다. 
   
    ![][17]
4. 편집을 위해 해당 스크립트를 열고 다음 코드를 추가합니다.
   
        using UnityEngine;
        using System.Collections;
   
        public class CameraController : MonoBehaviour {
   
            public GameObject player;
   
            private Vector3 offset;
   
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
   
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
5. Unity 환경에서 플레이어 변수를 주 카메라 개체의 플레이어 슬롯으로 끌어다 놓아 서로 연결합니다. 
   
    ![][18]
6. 이제 Unity 편집기에서 Play를 누르고 플레이어 볼 개체를 회전하면 환경에서 카메라가 플레이어 볼 개체를 따라다니는 것을 볼 수 있습니다.  

### <a name="setting-up-the-play-area"></a>플레이 영역 설정
아래 단계는 [Unity 자습서](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141)에서 가져온 것입니다. 플레이어 볼 개체가 이동하다가 플레이 영역에서 떨어지지 않도록 그라운드를 둘러싸는 벽을 만들겠습니다. 

1. **만들기 -> 빈 항목 만들기 -> Game 개체**를 클릭하고 이름을 **벽**으로 지정합니다.
   
    ![][19]
2. 이 벽 개체에서 새 **3D 개체 -> 큐브**를 만들고 이름을 "서쪽 벽"으로 지정합니다. 
   
    ![][20]
3. 이 서쪽 벽 개체의 **변형 -> 위치** 및 **변형 -> 규모**를 업데이트합니다. 
   
    ![][21]
4. 서쪽 벽을 복제하고 변환 위치 및 배율을 업데이트하여 **동쪽 벽** 을 만듭니다. 
   
    ![][22]
5. 동쪽 벽을 복제하고 변환 위치 및 배율을 업데이트하여 **북쪽 벽**을 만듭니다. 
   
    ![][23]
6. 북쪽 벽을 복제하고 변환 위치 및 배율을 업데이트하여 **남쪽 벽**을 만듭니다. 
   
    ![][24]

### <a name="creating-collectible-objects"></a>수집 가능한 개체 만들기
아래 단계는 [Unity 자습서](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141)에서 가져온 것입니다. 수집 가능한 개체 집합을 형성하는 멋진 개체를 만들어 보겠습니다. 플레이어 볼 개체는 수집 가능한 개체와 충돌하여 '수집'할 수 있습니다. 

1. 새 **3D 큐브 개체** 를 만들고 이름을 Pickup으로 지정합니다. 
2. Pickup 개체의 **변형 -> 회전** & **변형 -> 규모**를 조정합니다. 
   
    ![][25]
3. **Rotator**라고 하는 **새 C# 스크립트**를 만들어서 Pickup 개체에 연결합니다. 이 스크립트를 Scripts 폴더에 배치합니다. 
   
    ![][26]
4. 편집을 위해 이 스크립트를 열고 다음과 같이 업데이트합니다. 
   
        using UnityEngine;
        using System.Collections;
   
        public class Rotator : MonoBehaviour {
   
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }
5. 이제 Unity 편집기에서 Play를 누르면 Pickup 개체가 축에서 회전할 것입니다.
6. 프로젝트에서 **Prefabs** 
   
    ![][27]
7. **Pickup** 개체를 Prefabs 폴더에 끌어다 놓습니다.
   
    ![][28]
8. **Pickups**라고 하는 **빈 게임 개체**를 새로 만듭니다. 이 게임 개체의 위치를 원점으로 다시 설정한 다음 Pickup 개체를 이 게임 개체 아래로 끌어다 놓습니다.  
   
    ![][29]
9. **Pickup** 개체를 복제하고 **Transform.Position의 X 및 Z** 값을 적절하게 업데이트하여 **Player** 개체 주변의 **Ground** 개체에 분산합니다. 
   
    ![][30]
10. **Pickup**이라고 하는 **새 재질**을 만들고 Ground 개체를 업데이트했던 것과 비슷한 방법으로 **Albedo 속성**을 빨간색으로 업데이트합니다. 
    
    ![][31]
11. Pickup 개체 4개 모두에 재질을 적용합니다.
    
    ![][32]

### <a name="collecting-the-pickup-objects"></a>Pickup 개체 수집
아래 단계는 [Unity 자습서](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141)에서 가져온 것입니다. 플레이어가 Pickup 개체와 충돌하는 방법으로 Pickup 개체를 '수집'할 수 있도록 플레이어를 업데이트하겠습니다. 

1. 편집을 위해 Player 개체에 연결된 **PlayerController** 스크립트를 열고 다음과 같이 업데이트합니다.  
   
        using UnityEngine;
        using System.Collections;
   
        public class PlayerController : MonoBehaviour {
   
            public float speed;
   
            private Rigidbody rb;
   
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
   
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
   
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
   
                rb.AddForce (movement * speed);
            }
   
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }
2. **Pick Up**이라고 하는 새 **태그**를 만듭니다(스크립트에 있는 것과 일치해야 함).  
   
    ![][33]
   
    ![][34]
3. 이 **태그** 를 Prefab Pickup 개체에 적용합니다. 
   
    ![][35]
4. Prefab 개체의 **IsTrigger** 확인란을 활성화합니다.
   
    ![][36]
5. Pickup Prefab 개체에 Rigid 바디를 추가합니다. 성능 최적화를 위해 동적 충돌체에 사용된 정적 충돌체를 업데이트하겠습니다. 
   
    ![][37]
6. 마지막으로 Prefab 개체의 **IsKinematic** 속성을 확인합니다. 
   
    ![][38]
7. Unity 편집기에서 **플레이**를 누르면 키보드의 방향 키로 Player 개체를 이동하여 이 **Roll a Ball** 게임을 플레이할 수 있습니다. 

### <a name="updating-the-game-for-mobile-play"></a>모바일 플레이가 가능하도록 게임 업데이트
Unity의 기본 자습서는 위의 섹션에서 끝입니다. 이제 모바일 장치에서 쉽게 플레이할 수 있도록 게임을 수정하겠습니다. 지금까지는 게임 테스트에서 키보드 입력을 사용했습니다. 이제는 스마트폰 동작을 사용하여(예: 가속도계를 입력으로 사용) 플레이어를 제어할 수 있도록 게임을 수정하겠습니다. 

편집을 위해 **PlayerController** 스크립트를 열고, 가속도계의 동작을 사용하여 Player 개체를 이동하도록 **FixedUpdate** 메서드를 업데이트합니다. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

이 자습서는 Unity를 사용하여 기본적인 게임을 만드는 방법을 설명하며, 만든 게임을 원하는 장치에 배포하여 플레이할 수 있습니다. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png    
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png    
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png













