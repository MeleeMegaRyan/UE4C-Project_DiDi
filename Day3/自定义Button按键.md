## 自定义Button按键

```c++
SLATE_ATTRIBUTE(FText,ItemText)
SLATE_ATTRIBUTE(EMenuItem::Type,ItemType)
```

宏，前面是Name，后面是 自定义的类型名



### 绑定按钮点击事件

> 1、先声明一个代理

```c++
DECLARE_DELEGATE_OneParam(FItemClicked,const EMenuItem::Type)
```

> 2、建立一个事件

```c++
SLATE_EVENT(FItemClicked,OnClick)
```

> 3、创建这个按下事件的代理

```c++
FItemClicked OnClicked;
```

> 4、通过.cpp中的InArgs可以调用.h中的所有

```c++
OnClicked = InArgs._OnClick;
```



这种方法可将在Widget内写的创建控件时得到的值传入自己定义的变量中，如下

```c++
ContentBox->AddSlot()
		[
			SNew(SSlAi_Ryan_MenuItemWidget)
			.ItemText(NSLOCTEXT("SSlAi_Ryan_Menu","StartGame","StartGame"))
			.ItemType(EMenuItem::StartGame)
			.event_OnClick(this,&SSlAi_Ryan_MenuWidget::MenuItemOnClicked)
		];
```

