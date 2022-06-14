 ---
  title: This is the title
  author: D. Author
  date: 14-02-2013
  ...

## 如何使用DataStream插件

### 1.引入DataStream插件

首先，将DataStream插件复制到项目Plugins文件夹中，之后在Project.Build.cs文件中添加依赖：

```csharp

// ZTZB_SJ_426.Build.cs
public ZTZB_SJ_426(ReadOnlyTargetRules Target) : base(Target)
	{
		PCHUsage = PCHUsageMode.UseExplicitOrSharedPCHs;

		PublicDependencyModuleNames.AddRange(new string[] { "Core", "CoreUObject", "Engine", "InputCore" });

    // 添加"DataStreamFramework", "Json", "JsonUtilities"三个依赖
		PrivateDependencyModuleNames.AddRange(new string[] { "DataStreamFramework", "Json", "JsonUtilities" });

		// Uncomment if you are using Slate UI
		// PrivateDependencyModuleNames.AddRange(new string[] { "Slate", "SlateCore" });

		// Uncomment if you are using online features
		// PrivateDependencyModuleNames.Add("OnlineSubsystem");

		// To include OnlineSubsystemSteam, add it to the plugins section in your uproject file with the Enabled attribute set to true
	}
```

### 2.新建DataModel类

DataModel是DataStream插件中用于解析一个模块中的接口的数据。
模块的划分根据项目具体情况，可以将多个接口或图表组合成一个模块，写到一个DataModel中

```c++

/**
 * SCGL_DataModel.h
 * 生产管理模块的DataModel
 *
 * 根据接口中的数据，组织我们想要的数据字段成为一个结构体
 *
 */
USTRUCT(BlueprintType)
struct FFactoryDeviceInfo
{
	GENERATED_BODY()

	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SCGL_DataModel")
	int32 Id;

  // Other properties...
};

USTRUCT(BlueprintType)
struct FDeviceAlertAmountInfo
{
	GENERATED_BODY()

	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SCGL_DataModel")
	int32 Year;

  // Other properties...
};

// Other data structres
// ......

/**
 * DataModel类，继承UBaseDataModel基类
 */
UCLASS()
class ZTZB_SJ_426_API USCGL_DataModel : public UBaseDataModel
{
	GENERATED_BODY()

  // 需要解析的数据1
	TArray<FFactoryDeviceInfo> DeviceListTemp;

  // 需要解析的数据2
	TArray<FDeviceAlertAmountInfo> DeviceAlertAmountListTemp;

public:

  // 在构造函数中绑定如何解析数据的函数，详见CPP文件
	USCGL_DataModel();

  // 暴露在蓝图中的解析结果1
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SCGL_DataModel")
	TArray<FFactoryDeviceInfo> DeviceList;

  // 暴露在蓝图中的解析结果2
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SCGL_DataModel")
	TArray<FDeviceAlertAmountInfo> DeviceAlertAmountList;

  // 定义解析数据1
	void Parse_DeviceList(TSharedPtr<FJsonObject> Obj);

  // 定义解析数据2
	void Parse_DeviceAlertAmountList(TSharedPtr<FJsonObject> Obj);

protected:

  // 定义当发送数据前会被执行
	virtual void PreDataUpdate() override;

  // 定义当发送数据时会被执行
	virtual void PostDataUpdate() override;

};
```

```c++
// SCGL_DataModel.cpp

// 在构造函数中绑定数据解析函数
USCGL_DataModel::USCGL_DataModel()
{
	SourceParserMap.Emplace(TEXT("FactoryDevInfo"),
	                        [=](TSharedPtr<FJsonObject> Obj) { Parse_DeviceList(Obj); });
	SourceParserMap.Emplace(TEXT("DevAlertAmountInfo"),
	                        [=](TSharedPtr<FJsonObject> Obj) { Parse_DeviceAlertAmountList(Obj); });
}

// 解析数据1
void USCGL_DataModel::Parse_DeviceList(TSharedPtr<FJsonObject> Obj)
{
	if (!Obj.IsValid()) return;

	FString Code = Obj->GetStringField("code");
	if (!Code.Equals("0")) return;

  // Other parse code...
}

// 解析数据2
void USCGL_DataModel::Parse_DeviceAlertAmountList(TSharedPtr<FJsonObject> Obj)
{
	if (!Obj.IsValid()) return;

	FString Code = Obj->GetStringField("code");
	if (!Code.Equals("0")) return;

  // Other parse code...
}

// 定义当发送数据前会被执行
void USCGL_DataModel::PreDataUpdate()
{
}

// 定义当发送数据时会被执行
void USCGL_DataModel::PostDataUpdate()
{
}
```

### 3.配置DataStream配置文件

写好DataModel之后，我们需要在DataStream的配置文件中对接口进行配置：

### 4.在Widget蓝图中解析数据
