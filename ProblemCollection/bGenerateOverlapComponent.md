## bGenerateOverlapComponent

![image-20200911103818305](C:\Users\Young\AppData\Roaming\Typora\typora-user-images\image-20200911103818305.png)

```c++
class UBoxComponent* AffectCollision;
AffectCollision -> bGenerateOverlapEvents = true;
```

以上代码会报错，提示bGenerateOverlapEvents无法调用

经过查询，可以使用以下方法

```c++
AffectCollision->SetGenerateOverlapEvents(true);
```

