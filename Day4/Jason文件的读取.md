## UE4  Jason 文件的读取

```c++
    //解析存档方法
	void RecordDataJsonRead(FString& culture,float& MusicVolume,float& SoundVolume,TArray<FString>&RecordDataList);

	//读取Json文件到字符串
	bool LoadStringFromFile(const FString& FileName,const FString& RelaPath,FString& ResultString);
```

```c++
void RecordDataJsonRead(FString& culture, float& MusicVolume, float& SoundVolume, TArray<FString>& RecordDataList)
{
	FString JsonValue;
	LoadStringFromFile(RecordDataFileName,RelativePath,JsonValue);

	TArray<TSharedPtr<FJsonValue>> JsonParsed;
	TSharedRef<TJsonReader<TCHAR>> JsonReader = 		            TJsonReaderFactory<TCHAR>::Create(JsonValue);
	if (FJsonSerializer::Deserialize(JsonReader,JsonParsed))
	{
		//将JsonReader解析的数据获取到JsonParsed
		culture = JsonParsed[0]->AsObject()->GetStringField(FString("Culture"));
		MusicVolume = JsonParsed[1]->AsObject()->GetNumberField(FString("MusicVolume"));
		SoundVolume = JsonParsed[2]->AsObject()->GetNumberField(FString("SoundVolume"));
		//获取存档数据
		TArray<TSharedPtr<FJsonValue>> RecordDataArray = JsonParsed[3]->AsObject()->GetArrayField(FString("RecordData"));
		for (int i = 0;i<RecordDataArray.Num();++i)
		{
			FString RecordDataName = RecordDataArray[i]->AsObject()->GetStringField(FString::FromInt(i));
			RecordDataList.Add(RecordDataName);
		}
	}
	else
	{
		SlAi_Ryan_Helper::Debug(FString("Deserialize Failed"));
	}
}

bool LoadStringFromFile(const FString& FileName, const FString& RelaPath, FString& ResultString)
{
	if (!FileName.IsEmpty())
	{
		//获取绝对路径
		FString AbsoPath = FPaths::GameSourceDir()+RelaPath+FileName;
		if (FPaths::FileExists(AbsoPath))
		{
			if (FFileHelper::LoadFileToString(ResultString,*AbsoPath))
			{
				return true;
			}
			else
			{
			//加载不成功
				SlAi_Ryan_Helper::Debug(FString("Load Error")+AbsoPath);
			}
		}
		else//输出文件不存在
		{
			SlAi_Ryan_Helper::Debug(FString("File Not Exist")+AbsoPath);
		}
	}
	return false;
}
```

