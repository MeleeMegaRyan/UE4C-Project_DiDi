## Json文件的写入，用于存档

```c++
//把JsonObject转换成JsonStr格式的字符串
	bool GetFStringInJsonData(const TSharedPtr<FJsonObject>& JsonObj,FString& JsonStr);
//修改存档
	void UpdateRecordData(FString Culture,float MusicVolume,float SoundVolume,TArray<FString>* RecordDataList);
//保存字符串到文件
	bool WriteFileWhtiJsonData(const FString& JsonStr,const FString& RelaPath,const FString& FileName);
```

每次修改游戏内的值，都会更新到存档里，方式是：新修改的值---》创建成JsonObject格式---》将JsonObject转换成JsonStr格式---》再将JsonStr保存到文件中，也就是覆写到存档的Json文件里。

下面为次3个函数的实现

```c++

bool SlAi_Ryan_JsonHandle::GetFStringInJsonData(const TSharedPtr<FJsonObject>& JsonObj, FString& JsonStr)
{
	if (JsonObj.IsValid() && JsonObj->Values.Num() > 0)
	{
		TSharedRef<TJsonWriter<TCHAR>>JsonWriter = TJsonWriterFactory<TCHAR>::Create(&JsonStr);
		FJsonSerializer::Serialize(JsonObj.ToSharedRef(),JsonWriter);
		return true; 
	}
	return false;
```

```c++
bool SlAi_Ryan_JsonHandle::WriteFileWhtiJsonData(const FString& JsonStr, const FString& RelaPath, const FString& FileName)
{
	if (!JsonStr.IsEmpty())
	{
		if (!FileName.IsEmpty())
		{
			FString AbsoPath = FPaths::GameSourceDir()+RelaPath+FileName;
			//保存
			if (FFileHelper::SaveStringToFile(JsonStr,*FileName));
			{
				SlAi_Ryan_Helper::Debug(FString("Save Successfully!"));
				return true;
			}
			else
			{
				SlAi_Ryan_Helper::Debug(FString("Save")+AbsoPath+FString("--->Failed"),10.f);
				
			}
		}
	}
	else
	{
	return false;
	}
}
```

```c++\
void SlAi_Ryan_JsonHandle::UpdateRecordData(FString Culture, float MusicVolume, float SoundVolume, TArray<FString>* RecordDataList)
{
	TSharedPtr<FJsonObject>JsonObject = MakeShareable(new FJsonObject);
	TArray<TSharedPtr<FJsonValue>>BaseDataArray;

	//将数据写入Json文件，按照Json文件的格式
	TSharedPtr<FJsonObject>CultureObject = MakeShareable(new FJsonObject);
	CultureObject->SetStringField("Culture",Culture);
	TSharedPtr<FJsonValueObject>CultureValue = MakeShareable(new FJsonValueObject(CultureObject));

	TSharedPtr<FJsonObject>MusicVolumeObject = MakeShareable(new FJsonObject);
	MusicVolumeObject->SetNumberField("MusicVolume", MusicVolume);
	TSharedPtr<FJsonValueObject>MusicVolumeValue = MakeShareable(new FJsonValueObject(MusicVolumeObject));

	TSharedPtr<FJsonObject>SoundVolumeObject = MakeShareable(new FJsonObject);
	SoundVolumeObject->SetNumberField("SoundVolume", SoundVolume);
	TSharedPtr<FJsonValueObject>SoundVolumeValue = MakeShareable(new FJsonValueObject(SoundVolumeObject));

	TArray<TSharedPtr<FJsonValue>>RecordDataArray;
	for (int i = 0;i<RecordDataList->Num();++i)
	{
		TSharedPtr<FJsonObject>RecordItem = MakeShareable(new FJsonObject);
		RecordItem->SetStringField(FString::FromInt(i),(*RecordDataList)[i]);
		TSharedPtr<FJsonValueObject>RecordDataValue = MakeShareable(new FJsonValueObject(RecordItem));
		RecordDataArray.Add(RecordDataValue);
	}
	//给Json文件的数组部分添加一个key
	TSharedPtr<FJsonObject>RecordDataObject = MakeShareable(new FJsonObject);
	RecordDataObject->SetArrayField("RecordData",RecordDataArray);
	TSharedPtr<FJsonValueObject>RecordDataArrayValue = MakeShareable(new FJsonValueObject(RecordDataObject));

	//将上方所以数据写入最外层的数组（Json文件）
	BaseDataArray.Add(CultureValue);
	BaseDataArray.Add(MusicVolumeValue);
	BaseDataArray.Add(SoundVolumeValue);
	BaseDataArray.Add(RecordDataArrayValue);

	JsonObject->SetArrayField("T",BaseDataArray);
	
	
	FString JsonStr;
	GetFStringInJsonData(JsonObject,JsonStr);
	SlAi_Ryan_Helper::Debug(FString ("Origin Str:"+JsonStr),60.f);

	//将多余的部分剔除，保存为Json格式
	JsonStr.RemoveAt(0,8);
	JsonStr.RemoveFromEnd(FString("}"));
	SlAi_Ryan_Helper::Debug(FString("Final str:"+JsonStr),60.f);

	//写入文件
	WriteFileWhtiJsonData(JsonStr,RelativePath,RecordDataFileName);
}
```

