## 在UE中, C++中使用template遇到无法解析的问题
---

### 案例代码

```c++
// ActorObjectPool.h

#pragma once

#include "CoreMinimal.h"
#include "Subsystems/GameInstanceSubsystem.h"

template <class Object = AActor>
class TESTPROJECT_API TActorObjectPool final : public UGameInstanceSubsystem
{

	const int32 MaxSize = 10;

	TArray<Object*> ActorPool;

public:

	TActorObjectPool();

	Object* Pop();

	void Push(Object* ActorObject);

	void Clear();

	bool IsEmpty();
};
```
```c++
// ActorObjectPool.cpp

#include "Test/ActorObjectPool.h"

// Other implement methods

template <class Object>
bool TActorObjectPool<Object>::IsEmpty()
{
	// ...
}
```

在TestActor的BeginPlay中调用带模板的类函数：
```c++
// TestActor.cpp
// ...
// Called when the game starts or when spawned
void ATestActor::BeginPlay()
{
	Super::BeginPlay();
	TActorObjectPool<AActor>* Pool = GetGameInstance()->GetSubsystem<TActorObjectPool<AActor>>();
	Pool->IsEmpty();
}
//...
```
编译时出现如下错误：
```
LNK2019: unresolved external symbol "public: bool __cdecl TActorObjectPool<class AActor>::IsEmpty(void)" (?IsEmpty@?$TActorObjectPool@VAActor@@@@QEAA_NXZ) referenced in function "protected: virtual void __cdecl ATestActor::BeginPlay(void)" (?BeginPlay@ATestActor@@MEAAXXZ)
LNK1120: 1 unresolved externals
```
### 原因：
`
编译器针对类模板不支持分离编译
`

### 解决方案：
```c++
1.将函数申明和实现全部写在.h文件中；
2.如果非要.h文件与.cpp文件分开，则可在使用类模板的文件中，同时包含类模板的.h文件和.cpp文件;

// TestActor.cpp

#include "Test/TestActor.h"
#include "Test/ActorObjectPool.h"
#include "Test/ActorObjectPool.cpp"
```
此时便可通过编译。
