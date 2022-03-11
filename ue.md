[TOC]

## 概念
Level, Class, 

### Actor
- Actor: 可以放置或生成在一个关卡中的Object的基类
- Pawn: 傀儡，棋子，卒，小兵
- Character: 人型傀儡

 - 道具、光源和摄像机
 Actor类型: 
  - 网格体 & 几何体, 
  - Gameplay Actor
      - PlayerStart, 
      - Triggers: 盒体触发器,胶囊体触发器,球体触发器
      - MatineeActor
  - 光源: 点光源, 聚光源, 定向光源
  - 特效: 粒子发射器（ParticleEmitter）, 
  - 音效: 环境音效（AmbientSound）
 
### Component
 组件（Component） 是可以添加到Actor上的一项功能。组件必须绑定在Actor身上，它们无法单独存在
 
AI 组件
音频组件
缆绳组件
摄像机组件
光源组件
移动组件
寻路组件
Paper 2D 组件
物理组件
渲染组件
形状组件
骨骼网格体组件
静态网格体组件
工具组件
控件组件


### Asset
Material）、静态网格体（Static Mesh）、纹理（Texture）、粒子系统（Particle System）、蓝图（Blueprint）

游戏模式
碰撞体




### 事件
### 蓝图

内容浏览器内：蓝图类

蓝图脚本

### C++

## 源码编译

编译ue
基于ue源码的独立应用，把ue作为库

## 素材

https://www.rrcg.cn/forum-13-1.html


https://www.mixamo.com/

https://quixel.com/bridge

## 学习资源

史上最全的Unreal Engine 4学习资料整理
https://zhuanlan.zhihu.com/p/84743322


https://github.com/terrehbyte/awesome-ue4

### UE 官方C++ 教程
#### ==------------ Actor ------------==

#### 编程快速入门

https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/ProgrammingWithCPP/CPPProgrammingQuickStart

    创建项目，向项目添加C++代码，编译代码，以及在UE4中向 Actor 添加 组件

- 空白模板, 启用 C++ 和 初学者内容包
- 创建`FloatingActor`类, 拖放到世界中
- 修改FloatinActor细节， 位置（Location）设置为（-180、0、180）

实现效果：上下漂浮，同时缓慢旋转

##### FloatingActor.h
```cpp
// 版权所有 1998-2019 Epic Games, Inc。保留所有权利。

#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "FloatingActor.generated.h"

UCLASS()
class QUICKSTART_API AFloatingActor : public AActor
{
    GENERATED_BODY()

public: 
    // 设置此Actor属性的默认值
    AFloatingActor();

    UPROPERTY(VisibleAnywhere)
    UStaticMeshComponent* VisualMesh;

protected:
    // 游戏开始时或生成时调用
    virtual void BeginPlay() override;

public: 
    // 逐帧调用
    virtual void Tick(float DeltaTime) override;

};
```

##### FloatingActor.cpp
```cpp
// 版权所有 1998-2019 Epic Games, Inc。保留所有权利。

#include "FloatingActor.h"

// 设置默认值
AFloatingActor::AFloatingActor()
{
    // 将此Actor设为逐帧调用Tick()。如无需此功能，可关闭以提高性能。
    PrimaryActorTick.bCanEverTick = true;

    VisualMesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Mesh"));
    VisualMesh->SetupAttachment(RootComponent);

    static ConstructorHelpers::FObjectFinder<UStaticMesh> CubeVisualAsset(TEXT("/Game/StarterContent/Shapes/Shape_Cube.Shape_Cube"));

    if (CubeVisualAsset.Succeeded())
    {
        VisualMesh->SetStaticMesh(CubeVisualAsset.Object);
        VisualMesh->SetRelativeLocation(FVector(0.0f, 0.0f, 0.0f));
    }
}

// 游戏开始时或生成时调用
void AFloatingActor::BeginPlay()
{
    Super::BeginPlay();

}

// 逐帧调用
void AFloatingActor::Tick(float DeltaTime)
{
    Super::Tick(DeltaTime);

    FVector NewLocation = GetActorLocation();
    FRotator NewRotation = GetActorRotation();
    float RunningTime = GetGameTimeSinceCreation();

    float DeltaHeight = (FMath::Sin(RunningTime + DeltaTime) - FMath::Sin(RunningTime));
    NewLocation.Z += DeltaHeight * 20.0f;       //Scale our height by a factor of 20

    float DeltaRotation = DeltaTime * 20.0f;    //Rotate by 20 degrees per second
    NewRotation.Yaw += DeltaRotation;

    SetActorLocationAndRotation(NewLocation, NewRotation);
}
```


#### 游戏控制的摄像机

https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/ProgrammingWithCPP/CPPTutorials/AutoCamera

了解如何激活和切换不同的视角。

- 拖放第一个摄像机（Camera）到世界中
- 拖放立方体（Cube），详细信息（Details）中添加 摄像机组件, 约束高宽比（Constrain Aspect Ratio）
- 创建`CameraDirector`(Actor)类, 放置CameraDirector
- 在详细信息面板（Details Panel）， CameraOne设置为Cube（立方体）, CameraTwo设置为摄像机Actor （CameraActor）

##### CameraDirector.h
```cpp
// 版权所有 1998-2017 Epic Games, Inc。保留所有权利。

#pragma once

#include "GameFramework/Actor.h"
#include "CameraDirector.generated.h"

UCLASS()
class HOWTO_AUTOCAMERA_API ACameraDirector : public AActor
{
    GENERATED_BODY()

public: 
    // 为此Actor的属性设置默认值
    ACameraDirector();

protected:
    // 当游戏开始或生成时调用
    virtual void BeginPlay() override;

public:
    // 每一帧调用
    virtual void Tick( float DeltaSeconds ) override;

    UPROPERTY(EditAnywhere)
    AActor* CameraOne;

    UPROPERTY(EditAnywhere)
    AActor* CameraTwo;

    float TimeToNextCameraChange;
};
```

##### CameraDirector.cpp
```cpp
// Copyright 1998-2017 Epic Games, Inc. All Rights Reserved.

#include "HowTo_AutoCamera.h"
#include "CameraDirector.h"
#include "Kismet/GameplayStatics.h"

// Sets default values
ACameraDirector::ACameraDirector()
{
    // Set this actor to call Tick() every frame.  You can turn this off to improve performance if you don't need it.
    PrimaryActorTick.bCanEverTick = true;

}

// Called when the game starts or when spawned
void ACameraDirector::BeginPlay()
{
    Super::BeginPlay();

}

// Called every frame
void ACameraDirector::Tick( float DeltaTime )
{
    Super::Tick( DeltaTime );

    const float TimeBetweenCameraChanges = 2.0f;
    const float SmoothBlendTime = 0.75f;
    TimeToNextCameraChange -= DeltaTime;
    if (TimeToNextCameraChange <= 0.0f)
    {
        TimeToNextCameraChange += TimeBetweenCameraChanges;

        //Find the actor that handles control for the local player.
        APlayerController* OurPlayerController = UGameplayStatics::GetPlayerController(this, 0);
        if (OurPlayerController)
        {
            if (CameraTwo && (OurPlayerController->GetViewTarget() == CameraOne))
            {
                //Blend smoothly to camera two.
                OurPlayerController->SetViewTargetWithBlend(CameraTwo, SmoothBlendTime);
            }
            else if (CameraOne)
            {
                //Cut instantly to camera one.
                OurPlayerController->SetViewTarget(CameraOne);
            }
        }
    }
}
```

#### 变量、定时器和事件
https://docs.unrealengine.com/4.27/zh-CN/ProgrammingAndScripting/ProgrammingWithCPP/CPPTutorials/VariablesTimersEvents

向编辑器公开变量和函数，使用定时器并以蓝图覆盖C++函数。

- 创建Countdown(Actor)类
- 拖放Countdown
- 基于Countdown创建蓝图BP_Countdown

实现效果：倒数结束后应能同时看到"GO！"（来自C++编码）和爆炸（来自蓝图图表）

##### Countdown.h
```cpp
// Copyright 1998-2018 Epic Games, Inc. All Rights Reserved.

#pragma once

#include "GameFramework/Actor.h"
#include "Countdown.generated.h"

UCLASS()
class HOWTO_VTE_API ACountdown : public AActor
{
    GENERATED_BODY()

public: 
    //设置此Actor属性的默认值
    ACountdown();

protected:
    // 游戏开始或生成时调用
    virtual void BeginPlay() override;

public:
    // 逐帧调用
    virtual void Tick( float DeltaSeconds ) override;

    //倒数的运行时长（以秒计）
    UPROPERTY(EditAnywhere)
    int32 CountdownTime;

    UTextRenderComponent* CountdownText;

    void UpdateTimerDisplay();

    void AdvanceTimer();

    UFUNCTION(BlueprintNativeEvent)
    void CountdownHasFinished();
    virtual void CountdownHasFinished_Implementation();

    FTimerHandle CountdownTimerHandle;
};
```

##### Countdown.cpp
```cpp
// Copyright 1998-2018 Epic Games, Inc. All Rights Reserved.

#include "HowTo_VTE.h"
#include "Components/TextRenderComponent.h"
#include "Countdown.h"

//设置默认值
ACountdown::ACountdown()
{
    //将此Actor设为逐帧调用Tick()。如无需此功能，可关闭以提高性能。
    PrimaryActorTick.bCanEverTick = false;

    CountdownText = CreateDefaultSubobject<UTextRenderComponent>(TEXT("CountdownNumber"));
    CountdownText->SetHorizontalAlignment(EHTA_Center);
    CountdownText->SetWorldSize(150.0f);
    RootComponent = CountdownText;

    CountdownTime = 3;
}

// 游戏开始或生成时调用
void ACountdown::BeginPlay()
{
    Super::BeginPlay();

    UpdateTimerDisplay();
    GetWorldTimerManager().SetTimer(CountdownTimerHandle, this, &ACountdown::AdvanceTimer, 1.0f, true);
}

// 逐帧调用
void ACountdown::Tick( float DeltaTime )
{
    Super::Tick( DeltaTime );

}

void ACountdown::UpdateTimerDisplay()
{
    CountdownText->SetText(FString::FromInt(FMath::Max(CountdownTime, 0)));
}

void ACountdown::AdvanceTimer()
{
    --CountdownTime;
    UpdateTimerDisplay();
    if (CountdownTime < 1)
    {
        //倒数完成，停止运行定时器。
        GetWorldTimerManager().ClearTimer(CountdownTimerHandle);
        //定时器结束时，执行要执行的特殊操作。
        CountdownHasFinished();
    }
}

void ACountdown::CountdownHasFinished_Implementation()
{
    //改为特殊读出
    CountdownText->SetText(TEXT("GO!"));
}
```

#### ==------------ Pawn ------------==


#### 组件和碰撞
https://docs.unrealengine.com/4.27/zh-CN/ProgrammingAndScripting/ProgrammingWithCPP/CPPTutorials/Components

学习利用组件将Pawn与物理交互、使用粒子效果等方法。

- 创建`CollidingPawn`类，
- 添加映射
  - 添加操作映射ParticleToggle(Space)
  - 添加轴映射MoveForward(W,S)，MoveRight(A,D)，Turn（MouseX）
- 创建移动组件`CollidingPawnMovementComponent`类(PawnMovementComponent)，

##### CollidingPawn.h
```cpp
// Copyright 1998-2018 Epic Games, Inc. All Rights Reserved.

#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Pawn.h"
#include "CollidingPawn.generated.h"

UCLASS()
class HOWTO_COMPONENTS_API ACollidingPawn : public APawn
{
    GENERATED_BODY()

public:
    // 设置此Pawn属性的默认值
    ACollidingPawn();

protected:
    // 游戏开始或生成时调用
    virtual void BeginPlay() override;

public:
    // 逐帧调用
    virtual void Tick( float DeltaSeconds ) override;

    // 调用以将功能与输入绑定
    virtual void SetupPlayerInputComponent(class UInputComponent* InInputComponent) override;

    UPROPERTY()
    class UParticleSystemComponent* OurParticleSystem;

    UPROPERTY()
    class UCollidingPawnMovementComponent* OurMovementComponent;

    virtual UPawnMovementComponent* GetMovementComponent() const override;

    void MoveForward(float AxisValue);
    void MoveRight(float AxisValue);
    void Turn(float AxisValue);
    void ParticleToggle();
};
```

##### CollidingPawn.cpp
```cpp
// Copyright 1998-2018 Epic Games, Inc. All Rights Reserved.

#include "CollidingPawn.h"
#include "CollidingPawnMovementComponent.h"
#include "UObject/ConstructorHelpers.h"
#include "Particles/ParticleSystemComponent.h"
#include "Components/SphereComponent.h"
#include "Camera/CameraComponent.h"
#include "GameFramework/SpringArmComponent.h"

// 设置默认值
ACollidingPawn::ACollidingPawn()
{
    // 设置该Pawn以逐帧调用Tick()。如无需此功能，可关闭以提高性能。
    PrimaryActorTick.bCanEverTick = true;

    // 根组件将成为对物理反应的球体
    USphereComponent* SphereComponent = CreateDefaultSubobject<USphereComponent>(TEXT("RootComponent"));
    RootComponent = SphereComponent;
    SphereComponent->InitSphereRadius(40.0f);
    SphereComponent->SetCollisionProfileName(TEXT("Pawn"));

    // 创建并放置网格体组件，以便查看球体位置
    UStaticMeshComponent* SphereVisual = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("VisualRepresentation"));
    SphereVisual->SetupAttachment(RootComponent);
    static ConstructorHelpers::FObjectFinder<UStaticMesh> SphereVisualAsset(TEXT("/Game/StarterContent/Shapes/Shape_Sphere.Shape_Sphere"));
    if (SphereVisualAsset.Succeeded())
    {
        SphereVisual->SetStaticMesh(SphereVisualAsset.Object);
        SphereVisual->SetRelativeLocation(FVector(0.0f, 0.0f, -40.0f));
        SphereVisual->SetWorldScale3D(FVector(0.8f));
    }

    // 创建可激活或停止的粒子系统
    OurParticleSystem = CreateDefaultSubobject<UParticleSystemComponent>(TEXT("MovementParticles"));
    OurParticleSystem->SetupAttachment(SphereVisual);
    OurParticleSystem->bAutoActivate = false;
    OurParticleSystem->SetRelativeLocation(FVector(-20.0f, 0.0f, 20.0f));
    static ConstructorHelpers::FObjectFinder<UParticleSystem> ParticleAsset(TEXT("/Game/StarterContent/Particles/P_Fire.P_Fire"));
    if (ParticleAsset.Succeeded())
    {
        OurParticleSystem->SetTemplate(ParticleAsset.Object);
    }

    // 使用弹簧臂给予摄像机平滑自然的运动感。
    USpringArmComponent* SpringArm = CreateDefaultSubobject<USpringArmComponent>(TEXT("CameraAttachmentArm"));
    SpringArm->SetupAttachment(RootComponent);
    SpringArm->SetRelativeRotation(FRotator(-45.f, 0.f, 0.f));
    SpringArm->TargetArmLength = 400.0f;
    SpringArm->bEnableCameraLag = true;
    SpringArm->CameraLagSpeed = 3.0f;

    // 创建摄像机并附加到弹簧臂
    UCameraComponent* Camera = CreateDefaultSubobject<UCameraComponent>(TEXT("ActualCamera"));
    Camera->SetupAttachment(SpringArm, USpringArmComponent::SocketName);

    // 控制默认玩家
    AutoPossessPlayer = EAutoReceiveInput::Player0;

    // 创建移动组件的实例，并要求其更新根组件。
    OurMovementComponent = CreateDefaultSubobject<UCollidingPawnMovementComponent>(TEXT("CustomMovementComponent"));
    OurMovementComponent->UpdatedComponent = RootComponent;
}

// 游戏开始或生成时调用
void ACollidingPawn::BeginPlay()
{
    Super::BeginPlay();

}

// 逐帧调用
void ACollidingPawn::Tick( float DeltaTime )
{
    Super::Tick( DeltaTime );

}

// 调用以将功能与输入绑定
void ACollidingPawn::SetupPlayerInputComponent(class UInputComponent* InInputComponent)
{
    Super::SetupPlayerInputComponent(InInputComponent);

    InInputComponent->BindAction("ParticleToggle", IE_Pressed, this, &ACollidingPawn::ParticleToggle);

    InInputComponent->BindAxis("MoveForward", this, &ACollidingPawn::MoveForward);
    InInputComponent->BindAxis("MoveRight", this, &ACollidingPawn::MoveRight);
    InInputComponent->BindAxis("Turn", this, &ACollidingPawn::Turn);
}

UPawnMovementComponent* ACollidingPawn::GetMovementComponent() const
{
    return OurMovementComponent;
}

void ACollidingPawn::MoveForward(float AxisValue)
{
    if (OurMovementComponent && (OurMovementComponent->UpdatedComponent == RootComponent))
    {
        OurMovementComponent->AddInputVector(GetActorForwardVector() * AxisValue);
    }
}

void ACollidingPawn::MoveRight(float AxisValue)
{
    if (OurMovementComponent && (OurMovementComponent->UpdatedComponent == RootComponent))
    {
        OurMovementComponent->AddInputVector(GetActorRightVector() * AxisValue);
    }
}

void ACollidingPawn::Turn(float AxisValue)
{
    FRotator NewRotation = GetActorRotation();
    NewRotation.Yaw += AxisValue;
    SetActorRotation(NewRotation);
}

void ACollidingPawn::ParticleToggle()
{
    if (OurParticleSystem && OurParticleSystem->Template)
    {
        OurParticleSystem->ToggleActive();
    }
}
```

##### CollidingPawnMovementComponent.h
```cpp
// Copyright 1998-2018 Epic Games, Inc. All Rights Reserved.

#pragma once

#include "CoreMinimal.h"
#include "GameFramework/PawnMovementComponent.h"
#include "CollidingPawnMovementComponent.generated.h"

/**
    * 
    */
UCLASS()
class HOWTO_COMPONENTS_API UCollidingPawnMovementComponent : public UPawnMovementComponent
{
    GENERATED_BODY()

public:
    virtual void TickComponent(float DeltaTime, enum ELevelTick TickType, FActorComponentTickFunction *ThisTickFunction) override;  
};
```

##### CollidingPawnMovementComponent.cpp
```cpp
// Copyright 1998-2018 Epic Games, Inc. All Rights Reserved.

#include "CollidingPawnMovementComponent.h"

void UCollidingPawnMovementComponent::TickComponent(float DeltaTime, enum ELevelTick TickType, FActorComponentTickFunction *ThisTickFunction)
{
    Super::TickComponent(DeltaTime, TickType, ThisTickFunction);

    // 确保所有事物持续有效，以便进行移动。
    if (!PawnOwner || !UpdatedComponent || ShouldSkipUpdate(DeltaTime))
    {
        return;
    }

    // 获取（然后清除）ACollidingPawn::Tick中设置的移动向量
    FVector DesiredMovementThisFrame = ConsumeInputVector().GetClampedToMaxSize(1.0f) * DeltaTime * 150.0f;
    if (!DesiredMovementThisFrame.IsNearlyZero())
    {
        FHitResult Hit;
        SafeMoveUpdatedComponent(DesiredMovementThisFrame, UpdatedComponent->GetComponentRotation(), true, Hit);

        // 若发生碰撞，尝试滑过去
        if (Hit.IsValidBlockingHit())
        {
            SlideAlongSurface(DesiredMovementThisFrame, 1.f - Hit.Time, Hit.Normal, Hit);
        }
    }
};
```

#### 玩家输入和Pawn
https://docs.unrealengine.com/4.27/zh-CN/ProgrammingAndScripting/ProgrammingWithCPP/CPPTutorials/PlayerInput

对Pawn类进行扩展，以便响应玩家的输入操作。

- 创建MyPawn类
- 放置MyPawn, Detail中设置StaticMesh
- 配置映射
  - 操作映射, Grow(Space)
  - 轴映射, MoveX(W, S), MoveY(A, D)
    

##### MyPawn.h
```cpp
// Copyright 1998-2018 Epic Games, Inc. All Rights Reserved.

#pragma once

#include "GameFramework/Pawn.h"
#include "MyPawn.generated.h"

UCLASS()
class HOWTO_PLAYERINPUT_API AMyPawn : public APawn
{
    GENERATED_BODY()

public:
    // 设置默认值
    AMyPawn();

protected:
    // 游戏开始或生成时调用
    virtual void BeginPlay() override;

public:
    // 逐帧调用
    virtual void Tick(float DeltaSeconds) override;

    // 调用以将功能绑定至输入
    virtual void SetupPlayerInputComponent(class UInputComponent* InputComponent) override;

    UPROPERTY(EditAnywhere)
    USceneComponent* OurVisibleComponent;

    //输入函数
    void Move_XAxis(float AxisValue);
    void Move_YAxis(float AxisValue);
    void StartGrowing();
    void StopGrowing();

    //输入变量
    FVector CurrentVelocity;
    bool bGrowing;
};
```

##### MyPawn.cpp
```cpp
// Copyright 1998-2018 Epic Games, Inc. All Rights Reserved.

#include "HowTo_PlayerInput.h"
#include "MyPawn.h"

// 设置默认值
AMyPawn::AMyPawn()
{
    // 设置该Pawn以逐帧调用Tick()。如无需要，可关闭此项以提高性能。
    PrimaryActorTick.bCanEverTick = true;

    // 将该pawn设为由最小编号玩家控制
    AutoPossessPlayer = EAutoReceiveInput::Player0;

    // 创建可附加内容的虚拟根组件。
    RootComponent = CreateDefaultSubobject<USceneComponent>(TEXT("RootComponent"));
    // 创建相机和可见对象
    UCameraComponent* OurCamera = CreateDefaultSubobject<UCameraComponent>(TEXT("OurCamera"));
    OurVisibleComponent = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("OurVisibleComponent"));
    // 将相机和可见对象附加到根组件。偏移并旋转相机。
    OurCamera->SetupAttachment(RootComponent);
    OurCamera->SetRelativeLocation(FVector(-250.0f, 0.0f, 250.0f));
    OurCamera->SetRelativeRotation(FRotator(-45.0f, 0.0f, 0.0f));
    OurVisibleComponent->SetupAttachment(RootComponent);
}

// 游戏开始或生成时调用
void AMyPawn::BeginPlay()
{
    Super::BeginPlay();

}

// 逐帧调用
void AMyPawn::Tick(float DeltaTime)
{
    Super::Tick(DeltaTime);

    // 根据"Grow"操作处理增长和缩减
    {
        float CurrentScale = OurVisibleComponent->GetComponentScale().X;
        if (bGrowing)
        {
            // 一秒内增长到两倍大小
            CurrentScale += DeltaTime;
        }
        else
        {
            // 以增长速度缩减一半
            CurrentScale -= (DeltaTime * 0.5f);
        }
        // 确保不会降至初始大小以下，或者增至两倍大小以上。
        CurrentScale = FMath::Clamp(CurrentScale, 1.0f, 2.0f);
        OurVisibleComponent->SetWorldScale3D(FVector(CurrentScale));
    }

    // 根据"MoveX"和"MoveY"轴处理移动
    {
        if (!CurrentVelocity.IsZero())
        {
            FVector NewLocation = GetActorLocation() + (CurrentVelocity * DeltaTime);
            SetActorLocation(NewLocation);
        }
    }
}

// 调用以将功能绑定至输入
void AMyPawn::SetupPlayerInputComponent(class UInputComponent* InputComponent)
{
    Super::SetupPlayerInputComponent(InputComponent);

    // 在按下或松开"Grow"键时做出响应。
    InputComponent->BindAction("Grow", IE_Pressed, this, &AMyPawn::StartGrowing);
    InputComponent->BindAction("Grow", IE_Released, this, &AMyPawn::StopGrowing);

    // 对两个移动轴"MoveX"和"MoveY"的值逐帧反应。
    InputComponent->BindAxis("MoveX", this, &AMyPawn::Move_XAxis);
    InputComponent->BindAxis("MoveY", this, &AMyPawn::Move_YAxis);
}

void AMyPawn::Move_XAxis(float AxisValue)
{
    // 以100单位/秒的速度向前或向后移动
    CurrentVelocity.X = FMath::Clamp(AxisValue, -1.0f, 1.0f) * 100.0f;
}

void AMyPawn::Move_YAxis(float AxisValue)
{
    // 以100单位/秒的速度向右或向左移动
    CurrentVelocity.Y = FMath::Clamp(AxisValue, -1.0f, 1.0f) * 100.0f;
}

void AMyPawn::StartGrowing()
{
    bGrowing = true;
}

void AMyPawn::StopGrowing()
{
    bGrowing = false;
}
```


#### ==---------- FPS ------------==
https://docs.unrealengine.com/4.27/zh-CN/ProgrammingAndScripting/ProgrammingWithCPP/CPPTutorials/FirstPersonShooter

#### 建立项目

- 创建项目，游戏（Games）-> 空白模板（Blank template）, 
- C++（而非 蓝图（Blueprint）），无初学者内容（No Starter Content）
- 项目命名为"FPSProject"
- 在 内容（Content） 文件夹下创建一个 地图（Maps） 文件夹
- 将新地图命名为"FPSMap"
- 项目（Project）标题下，点击地图和模式（Maps & Modes）
- `启动地图`（Editor Startup Map） 下拉菜单，选择 FPSMap
- 将你的C++游戏模式类扩展到蓝图
- 使用 BP_FPSProjectGameModeBase 作为`默认游戏模式`

实现效果："Hello World, this is FPSGameMode!"这句话应在视口左上角以黄色文本显示五秒钟

#### 实现你的角色
https://docs.unrealengine.com/4.27/Attachments/ProgrammingAndScripting/ProgrammingWithCPP/CPPTutorials/FirstPersonShooter/2/6/GenericMale.zip
https://docs.unrealengine.com/4.27/Attachments/ProgrammingAndScripting/ProgrammingWithCPP/CPPTutorials/FirstPersonShooter/2/8/HeroFPP.zip
https://docs.unrealengine.com/4.27/Attachments/ProgrammingAndScripting/ProgrammingWithCPP/CPPTutorials/FirstPersonShooter/3/2/Sphere.zip
https://docs.unrealengine.com/Attachments/Programming/Tutorials/FirstPersonShooter/3/5/Crosshair_fps_tutorial.zip
https://docs.unrealengine.com/Attachments/Programming/Tutorials/FirstPersonShooter/4/1/FPP_Animations.zip

- 创建新角色
  - 新建FPSCharacter类
  - 基于FPSCharacter创建蓝图类BP_FPSCharacter
  - 在 默认Pawn类（Default Pawn Class） 下拉菜单中选择 BP_FPSCharacter
  - 效果：红色文本"We are using FPSCharacter."
- 设置轴映射
  - MoveForward(W, S), MoveRight(D, A)

- 实现角色移动函数
- 实现鼠标摄像机控制
  - Turn(Mouse X)
  - LookUp(Mouse Y)
  - Jump(Space Bar)
- 实现角色跳跃
- 将网格体添加到角色
  - 导入资产（Import Asset）, GenericMale.fbx 网格体文件
  - 编辑BP_FPSCharacter, 设置 细节（Details） 中的网格体（Mesh）段为GenericMale 骨骼网格体
  - 细节（Details）中的变换（Transform）段，将Z轴位置设置为 "-88.0"
- 更改摄像机视角
- 将第一人称网格体添加到角色
  - 导入HeroFPP.fbx, 保将 骨架（Skeleton） 设置为 无（None）
  - 编辑BP_FPSCharacter，FPSMesh 组件，选择 HeroFPP 骨骼网格体
  - 修改新增网格体, 位置（Location） 设置为{220, 0, 35}，将其 旋转（Rotation） 设置为{180, 50, 180}

#### 实现发射物

- 将发射物添加到游戏
  - 鼠标左键（Left Mouse Button）
  - 新建FPSProjectile(Actor)类
- 实现射击
  - 导入发射物网格体,Sphere.fbx
  - 新材质命名为"SphereMaterial", 
  - 打开BP_FPSCharacter, 在发射物类（Projectile Class）旁边的下拉列表中，选择FPSProjectile
- 设置发射物碰撞和生命周期
  - 在引擎（Engine） - 碰撞（Collision）中，展开预设（Preset）
  - 在对象通道（Object Channels）中，选择 新建对象通道...（New Object Channel...）, 命名为"Projectile", 默认响应（Default Response）设置为阻止（Block）
  -  在预设（Preset）中选择 新建...（New...），将新配置文件命名为"Projectile"。参考以下图片来设置你的碰撞预设
- 使发射物与世界交互
- 将十字准星添加到视口
  - 导入crosshair.TGA
  - 新建FPSHUD(HUD)类
  - 基于FPSHUD创建蓝图类
  - 地图和模式（Maps & Modes）中，设置默认HUD（Default HUD）为BP_FPSHUD
  - 编辑BP_FPSHUD,  FPSHUD 分段的下拉菜单，选择十字准星纹理

#### 添加角色动画

- 为角色添加动画
  - 在"Animations"文件夹中导入，选择骨架（Select Skeleton） 标题下的 HeroFPP_Skeleton
    - FPP_Idle.FBX
    - FPP_JumpEnd.FBX
    - FPP_JumpLoop.FBX
    - FPP_JumpStart.FBX
    - FPP_Run.FBX
  - 右键点击动画文件夹。创建动画蓝图（Animation Blueprint）
  - 选择 AnimInstance 作为父类，并选择 HeroFPP_Skeleton 作为目标骨架
  - 将新动画蓝图命名为"`Arms_AnimBP`"
  - 编辑Arms_AnimBP, 新增（Add New） ，变量（Variable）, IsRunning, IsFalling
 
- 设置事件图表
  - 更新状态变量
    - 编辑Arms_AnimBP, 打开打开事件图表（EventGraph），
    - 右键, 搜索字段中输入"Update", 点击 Event Blueprint Update Animation 以添加该节点
    - 添加Try Get Pawn Owner 节点
    - 将 Event Blueprint Update Animation 节点上的输出执行引脚关联到 Cast to Character 节点上的输入执行引脚
    - 从 As Character 输出引脚连出引线，并选择 Get Character Movement（可能需要禁用上下文相关（Context Sensitivity）以方便找到此节点）
    - 从 Character Movement 输出引脚连出引线，并选择 Get Movement Mode
  - 查询角色移动
    - 从 Movement Mode 输出引脚连出引线，并选择 Equal (Enum)
    - ...
- 添加动画状态机
  - 打开该图表(AnimGraph)
  - 右键，选择状态机（State Machines）>添加新状态机（Add New State Machine...）
  - 命名为"Arms State Machine"
  - 将"Arms State Machine"节点上的输出执行引脚连接到 Final Animation Pose 节点上的 Result 输入执行引脚
- 为动画添加过渡状态
  - 添加五个状态
    - 空闲（Idle）
    - 奔跑（Run）
    - 跳跃开始（JumpStart）
    - 跳跃结束（JumpEnd）
    - 跳跃循环（JumpLoop）
  - ...
- 关联动画蓝图与角色蓝图

##### FPSProjectGameModeBase.h
```cpp
    // Epic Games, Inc版权所有。保留所有权利。

    #pragma once

    #include "CoreMinimal.h"
    #include "GameFramework/GameModeBase.h"
    #include "FPSProjectGameModeBase.generated.h"

    /**
     * 
     */
    UCLASS()
    class FPSPROJECT_API AFPSProjectGameModeBase : public AGameModeBase
    {
        GENERATED_BODY()

        virtual void StartPlay() override;

    };
```

##### FPSProjectGameModeBase.cpp
```cpp
    // Epic Games, Inc版权所有。保留所有权利。

    #include "FPSProjectGameMode.h"

    void AFPSProjectGameMode::StartPlay()
    {
        Super::StartPlay();

        checkGEngine != nullptr);

          // 显示调试消息五秒。 
          // -1"键"值参数防止该消息被更新或刷新。
          GEngine->AddOnScreenDebugMessage(-1, 5.0f, FColor::Yellow, TEXT("Hello World, this is FPSGameModeBase!"));

    }
```



### Unreal入门第一季 - 虚幻C++基础训练
http://www.sikiedu.com/course/518/task/40735/show

 新项目：空模板,首次等待vs2019建索引

####  设置：

##### 编辑器

transform: move, rotate, scale

Modes
* BSP(Binary Space Partitioning) Geometry Brushes 
* Light

##### 项目

游戏模式: GameBase/new GameMode -> new Controller(蓝图类): begin event
地图: new GameLevel

Blueprints/
Actor: 
Character: new Player(蓝图类:角色), new Player_BPI(蓝图接口)

##### 世界



#### C++

.net sdk Dev Pack

Package -> World -> Actor -> Compoment

New Class: base of UObject
蓝图类，蓝图脚本，关卡蓝图

UCLASS(Blueprintable)

UPROPERTY(BlueprintReadWrite, Category="My var")



UFUNCTION()
HelloFunc::()
{

}

### 【虚幻5】用C++来进行基于UE5的游戏开发（无蓝图）

#### Character

- Content中加入Mannequin
- 创建Chareacter类
    - StaticMesh
    - SkeletalMesh
    - Camera, SpringArm
    - PlayerInput
    - PlayerAnimation


### 建模

一品威客，猪八戒，解放号

krita

world machine


### 游戏类型
- FPS
- 平台，第三人称
- 格斗
- 竞速
- TTS
- RTS
- MMOG
- 体育
- RPG
- 上帝模拟
- 环境、社会模拟
- 解谜
- 棋牌


### UE C++ 游戏开发进阶指南

> 输出是最好的学习

文档和视频同步，游戏化的技能进阶教程

入门：ue基础，蓝图，c++，官方c++指南，
进阶：技能点亮，定制游戏模版，骨骼动画，材质，灯光，地编...
高级：成品游戏，rpg,fps