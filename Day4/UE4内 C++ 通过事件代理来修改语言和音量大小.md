## UE4内 C++ 通过事件代理来修改语言和音量大小

> 首先先声明两个代理

```c++
//修改中英文的单播代理
DECLARE_DELEGATE_OneParam(FChangeCulture, const ECultureTeam)
//修改音量和音效的代理
DECLARE_DELEGATE_TwoParams(FChangeVolume, const float, const float)
```

>然后在.h类中创建两个事件和委托变量

```c++
class SLAICOURSE_API SSlAi_Ryan_GameOptionWidget : public SCompoundWidget
{
public:
	SLATE_BEGIN_ARGS(SSlAi_Ryan_GameOptionWidget)
	{}

	SLATE_EVENT(FChangeCulture, ChangeCulture)
	SLATE_EVENT(FChangeVolume, ChangeVolume)

	SLATE_END_ARGS()
 
private：
    	//委托变量
	FChangeCulture ChangeCulture;
	FChangeVolume ChangeVolume;
}
```

> 在.cpp中获取代理

```c++
void SSlAi_Ryan_GameOptionWidget::Construct(const FArguments& InArgs)
{
	
	//获取委托，方法绑定到事件
	ChangeCulture = InArgs._ChangeCulture;
	ChangeVolume = InArgs._ChangeVolume;
}
```

>这时就可以在函数中绑定代理（委托）

```c++
//修改语言
void SSlAi_Ryan_GameOptionWidget::ENCheckBoxStateChanged(ECheckBoxState NewState)
{
	/*告诉数据控制类转换成英文
	* 方法由调用改为委托,
	*/
	ChangeCulture.ExecuteIfBound(ECultureTeam::EN);
}
//修改音量
void SSlAi_Ryan_GameOptionWidget::SoundSliderChanged(float value)
{
	//用代理的方式，Event
	ChangeVolume.ExecuteIfBound(-1.0f, value);
}
```

> 实现的方法

```c++
 //声明
//修改语言
void ChangeCulture(ECultureTeam Culture);
//修改音量
void ChangeVolume(const float MusicVol,const float SoundVol);

//实现：
void SSlAi_Ryan_MenuWidget::ChangeCulture(ECultureTeam Culture)
{
	switch (culture)
	{
	case ECultureTeam::EN:
		FInternationalization::Get().SetCurrentCulture(TEXT("en"));
		break;
	case ECultureTeam::ZH:
		FInternationalization::Get().SetCurrentCulture(TEXT("zh"));
		break;
	}
	CurrentCluture = culture;
}

void SSlAi_Ryan_MenuWidget::ChangeVolume(const float MusicVol, const float SoundVol)
{
    if (MusicVal > 0)
	{
		MusicVolume = MusicVol;
	}
	if (SoundVol>0)
	{
		SoundVolume = SoundVol;
	}
}

```



