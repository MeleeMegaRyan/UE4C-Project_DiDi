

## 实现语言切换

> SlAi_Ryan_Internation.h(无cpp)

```c++
/**
 * 本地化,用于实现语言的切换
 */
class SLAICOURSE_API SlAi_Ryan_Internation
{
public:
	static void Register(FText Value)
	{
	return;
	}
};

#define  LOCTEXT_NAMESPACE "SlAi_Ryan_Menu"
SlAi_Ryan_Internation::Register(LOCTEXT("Menu","Menu"));
#undef LOCTEXT_NAMESPACE

/*
* 下面这段代码和上面这段效果相同，也就是NSLOCTEXT和LOCTEXT的使用区别 
*SlAi_Ryan_Internation::Register(NSLOCTEXT("SlAi_Ryan_Menu","Menu","Menu"));
*/
```

这是UE4提供的一套本地化操作，简单快捷实现多语言切换和设置

![image](https://github.com/MeleeMegaRyan/UE4C-Project_DiDi/blob/master/Day2/Image/1.jpg)

![image](https://github.com/MeleeMegaRyan/UE4C-Project_DiDi/blob/master/Day2/Image/2.jpg)

![image](https://github.com/MeleeMegaRyan/UE4C-Project_DiDi/blob/master/Day2/Image/3.jpg)

![image](https://github.com/MeleeMegaRyan/UE4C-Project_DiDi/blob/master/Day2/Image/4.jpg)

![image](https://github.com/MeleeMegaRyan/UE4C-Project_DiDi/blob/master/Day2/Image/5.jpg)

![image](https://github.com/MeleeMegaRyan/UE4C-Project_DiDi/blob/master/Day2/Image/6.jpg)

在c++内创建修改语言的handle：

> SlAi_Ryan_Types.h

```c++
UENUM()
enum class ECultureTeam : uint8
{
	EN = 0,
	ZH
};
```

> SlAi_Ryan_DataHandle.h&.cpp

```c++

#pragma once
#include "SlAi_Ryan_Types.h"
#include "CoreMinimal.h"
#include "Internationalization.h"
class SLAICOURSE_API SlAi_Ryan_DataHandle
{
public:
	SlAi_Ryan_DataHandle();
	static void Initialize();
	static TSharedPtr<SlAi_Ryan_DataHandle>Get();
	//修改中英文
	void ChangeLocationCulture(ECultureTeam culture);
public:
	ECultureTeam CurrentCluture;
private:

//创建单例
	static TSharedRef<SlAi_Ryan_DataHandle>Create();
private:
	static TSharedPtr<SlAi_Ryan_DataHandle>DataInstance;
};
```

```c++
#include "SlAi_Ryan_DataHandle.h"
TSharedPtr<SlAi_Ryan_DataHandle> SlAi_Ryan_DataHandle::DataInstance = NULL;

SlAi_Ryan_DataHandle::SlAi_Ryan_DataHandle()
{
}
void SlAi_Ryan_DataHandle::Initialize()
{
	if (!DataInstance.IsValid())
	{
		DataInstance = Create();
	}
}

TSharedPtr<SlAi_Ryan_DataHandle> SlAi_Ryan_DataHandle::Get()
{
	Initialize();
	return DataInstance;
}

TSharedRef<SlAi_Ryan_DataHandle> SlAi_Ryan_DataHandle::Create()
{
	TSharedRef<SlAi_Ryan_DataHandle>DataRef = MakeShareable(new SlAi_Ryan_DataHandle());
}

void SlAi_Ryan_DataHandle::ChangeLocationCulture(ECultureTeam culture)
{
	switch (culture)
	{
	case ECultureTeam::EN:
		FInternationalization::SetCurrentCulture(TEXT("en"));
		break;
	case ECultureTeam::ZH
		FInternationalization::SetCurrentCulture(TEXT("zh"));
		break;
	}
	CurrentCluture = culture;
}
```

