## C++项目  UI实现：

> SlAi_Ryan_Style.h&.cpp

```c++
#include "CoreMinimal.h"
#include "SlateBasics.h"

/**
 * 
 */
class SLAICOURSE_API SlAi_Ryan_Style
{
public:
    
    static void Initialize();
    static FName GetStyleSetName();
    static void ShutDown();
    static const ISlateStyle& Get();

private:
    
    static TSharedRef<class FSlateStyleSet> Create();
    static TSharedPtr<FSlateStyleSet> SlAiStyleInstance;

};
```

```c++
#include "SlAi_Ryan_Style.h"
#include "SlateGameResources.h"



TSharedPtr<FSlateStyleSet> SlAi_Ryan_Style::SlAiStyleInstance = NULL;

//初始化样式
void SlAi_Ryan_Style::Initialize()
{
  if (!SlAiStyleInstance.IsValid())
  {
    SlAiStyleInstance = Create();
    FSlateStyleRegistry::RegisterSlateStyle(*SlAiStyleInstance);
  }
}

//获取设置的样式的名字
FName SlAi_Ryan_Style::GetStyleSetName()
{
    static FName StyleSetName(TEXT("SlAiStyle"));
    return StyleSetName;
}
//关闭游戏的时候取消注册
void SlAi_Ryan_Style::ShutDown()
{
     FSlateStyleRegistry::UnRegisterSlateStyle(*SlAiStyleInstance);
     ensure(SlAiStyleInstance.IsUnique());
     SlAiStyleInstance.Reset();
}

const ISlateStyle& SlAi_Ryan_Style::Get()
{
    return *SlAiStyleInstance;
}

//创建样式，从给定的地址内获取样式并返回
TSharedRef<class FSlateStyleSet> SlAi_Ryan_Style::Create()
{
    TSharedRef<FSlateStyleSet>StyleRef = FSlateGameResources::New(SlAi_Ryan_Style::GetStyleSetName(),"/Game/UI/Style", "/Game/UI/Style");
    StyleRef->Set("MenuItemFont",FSlateFontInfo("Roboto-Regularr.ttf",50));
    return StyleRef;
}
```

> SlAi_Ryan_MenuWidgetStyle.h&.cpp

```c++
#pragma once

#include "CoreMinimal.h"
#include "Styling/SlateWidgetStyle.h"
#include "SlateWidgetStyleContainerBase.h"
#include "SlateBrush.h"

#include "SlAi_Ryan_MenuWidgetStyle.generated.h"

/**
 * 
 */
USTRUCT()
struct SLAICOURSE_API FSlAi_Ryan_MenuStyle : public FSlateWidgetStyle
{
	GENERATED_USTRUCT_BODY()

	FSlAi_Ryan_MenuStyle();
	virtual ~FSlAi_Ryan_MenuStyle();

	// FSlateWidgetStyle
	virtual void GetResources(TArray<const FSlateBrush*>& OutBrushes) const override;
	static const FName TypeName;
	virtual const FName GetTypeName() const override { return TypeName; };
	static const FSlAi_Ryan_MenuStyle& GetDefault();

	UPROPERTY(EditAnywhere,Category = MenuHUD)
		FSlateBrush MenuHUDBackgroundBrush;
	//指定Menu的背景图片
	UPROPERTY(EditAnywhere, Category = MenuHUD)
		FSlateBrush MenuBackgroundBrush;
	//Menu左和右的笔刷
	UPROPERTY(EditAnywhere,Category = Menu)
		FSlateBrush LeftIconBrush;
	UPROPERTY(EditAnywhere,Category = Menu)
		FSlateBrush RightIconBrush;
	//Menu标题border的Brush
	UPROPERTY(EditAnywhere,Category = Menu)
		FSlateBrush TitleBorderBrush;

};
```

无.cpp，此文件被引用至HUDWidget中，注：MeanStyle就是MenuWidgetStyle，Widget由系统所添加，用于区分

> SSlAi_Ryan_HUDWidget.h&.cpp

```c++
#pragma once
#include "CoreMinimal.h"
#include "Widgets/SCompoundWidget.h"
#include "SButton.h"
#include "SImage.h"
/**
 * 
 */
class SLAICOURSE_API SSlAi_Ryan_HUDWidget : public SCompoundWidget
{
public:
	SLATE_BEGIN_ARGS(SSlAi_Ryan_HUDWidget)
	{}
	SLATE_END_ARGS()
	/** Constructs this widget with InArgs */
		void Construct(const FArguments& InArgs);
private:
    //绑定UIScaler的函数
	float GetUIScaler() const;
    //获取屏幕尺寸
	FVector2D GetViewportSize() const;
	//定义一个buttom的按下函数
	FReply OnClick();
private:
    //获取Menu样式
	const struct FSlAi_Ryan_MenuStyle* MenuStyle;
	//DPI缩放系数
	TAttribute<float> UIScaler;
	//获取Image的Slot
	//SOverlay::FOverlaySlot*ImageSlot;
	// 菜单指针
	TSharedPtr<class SSlAi_Ryan_MenuWidget>SP_MenuWidget;
	//保存标题
	
};
```

```c++
#include "SSlAi_Ryan_HUDWidget.h"
#include "SlateOptMacros.h"

#include "SlAi_Ryan_Style.h"
#include "SlAi_Ryan_MenuWidgetStyle.h"
#include "SDPIScaler.h"
#include "SSlAi_Ryan_MenuWidget.h"

BEGIN_SLATE_FUNCTION_BUILD_OPTIMIZATION
void SSlAi_Ryan_HUDWidget::Construct(const FArguments& InArgs)
{
	//获取编辑器的MenuStyle
	MenuStyle = &SlAi_Ryan_Style::Get().GetWidgetStyle<FSlAi_Ryan_MenuStyle>("BPSlAi_Ryan_MenuStyle");
	//绑定缩放规则方法
	UIScaler.Bind(this,&SSlAi_Ryan_HUDWidget::GetUIScaler);
	ChildSlot
	[
		SNew(SDPIScaler)
	 	.DPIScale(UIScaler)
	 	[
		//插槽添加一个overlay，再加一个image
	  	SNew(SOverlay)
	  	+SOverlay::Slot()
	  	.HAlign(HAlign_Fill)
      	.VAlign(VAlign_Fill)
	  	.Padding(FMargin(30.0f))
	 		 [
		 	 //先NEW一个image，再个这个Image添加一个Image组件，并载入已创建好的笔刷
		 	 SNew(SImage).Image(&MenuStyle->MenuHUDBackgroundBrush)
	  		 ]
      	+SOverlay::Slot()
      	.HAlign(HAlign_Center)
      	.VAlign(VAlign_Center)
      		[
	    	SAssignNew(SP_MenuWidget,SSlAi_Ryan_MenuWidget)
      		]
	 	]	
	];
}
END_SLATE_FUNCTION_BUILD_OPTIMIZATION

float SSlAi_Ryan_HUDWidget::GetUIScaler() const
{
    return GetViewportSize().Y/1080.f;
}
FVector2D SSlAi_Ryan_HUDWidget::GetViewportSize() const
{
   FVector2D Result(1920,1080);
   if (GEngine && GEngine->GameViewport)
   {
	   GEngine->GameViewport->GetViewportSize(Result);
   }
   return Result;
}
```

在HUDWidget中，实现对屏幕自定义的DPI缩放

> SSlAi_Ryan_MenuWidget.h&.cpp

```c++
#pragma once
#include "CoreMinimal.h"
#include "Widgets/SCompoundWidget.h"
class SBox;
class STextBlock;
/**
 * 
 */
class SLAICOURSE_API SSlAi_Ryan_MenuWidget : public SCompoundWidget
{
public:
	SLATE_BEGIN_ARGS(SSlAi_Ryan_MenuWidget)
	{}
	SLATE_END_ARGS()

	/** Constructs this widget with InArgs */
	void Construct(const FArguments& InArgs);
private:

	//保存根节点的RootSizeBox
	TSharedPtr<SBox>RootSizeBox;
	const struct FSlAi_Ryan_MenuStyle *MenuStyle;
	TSharedPtr<class STextBlock> TitleText;
};
```

```c++
#include "SSlAi_Ryan_MenuWidget.h"
#include "SlateOptMacros.h"
#include "SlAi_Ryan_Style.h"
#include "SlAi_Ryan_MenuWidgetStyle.h"
#include "SBox.h"
#include "STextBlock.h"

BEGIN_SLATE_FUNCTION_BUILD_OPTIMIZATION
void SSlAi_Ryan_MenuWidget::Construct(const FArguments& InArgs)
{
	//获取编辑器的MenuStyle
	MenuStyle = &SlAi_Ryan_Style::Get().GetWidgetStyle<FSlAi_Ryan_MenuStyle>("BPSlAi_Ryan_MenuStyle");
	ChildSlot
	[
		SAssignNew(RootSizeBox,SBox)
		[
			SNew(SOverlay)
			+SOverlay::Slot()
			.HAlign(HAlign_Fill).VAlign(VAlign_Fill)
			.Padding(FMargin(0.f,50.f,0.f,0.f))
			[
				SNew(SImage)
				.Image(&MenuStyle->MenuBackgroundBrush)
			]
			+SOverlay::Slot()
			.HAlign(HAlign_Left).VAlign(VAlign_Center)
			.Padding(FMargin(0.f,25.f,0.f,0.f))
			[
				SNew(SImage).Image(&MenuStyle->LeftIconBrush)
			]
			+SOverlay::Slot()
			.HAlign(HAlign_Right).VAlign(VAlign_Center)
			.Padding(FMargin(0.f, 25.f, 0.f, 0.f))
			[
				SNew(SImage).Image(&MenuStyle->RightIconBrush)
			]
			//标题
			+SOverlay::Slot()
			.HAlign(HAlign_Center).VAlign(VAlign_Top)
			[
				SNew(SBox).WidthOverride(400.f).HeightOverride(100.f)
				[
				//此处用Border而不用image的原因是，Border下放可放子控件，而Image下不可
					SNew(SBorder)
					.BorderImage(&MenuStyle->TitleBorderBrush)
					.HAlign(HAlign_Center).VAlign(VAlign_Center)
					[
					SAssignNew(TitleText,STextBlock)
					.Font(SlAi_Ryan_Style::Get().GetFontStyle("MenuItemFont"))
					.Text(FText::FromString("I am Ryan"))
					]
				]
			]
		]
	];
	RootSizeBox->SetWidthOverride(600.f);
	RootSizeBox->SetHeightOverride(510.f);
}
END_SLATE_FUNCTION_BUILD_OPTIMIZATION
```

