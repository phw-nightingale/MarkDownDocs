## 软指针与硬指针

### TSoftObjectPtr&TSoftClassPtr的一些API

```c++
// 1.判断资源是否指向一个真实的UObject对象:TSoftObjectPtr::IsNull
FORCEINLINE bool IsNull() const
{
	return !ObjectID.IsValid();
}


// 2.判断资源是否未被加载：TSoftObjectPtr::IsPending，当返回true时，资源未被加载，但是指向的是一个真实的Uobject；否则资源被加载或者不是指向的一个真实UObject
FORCEINLINE bool IsPending() const
{
	return Get() == nullptr && ObjectID.IsValid();
}


// 3.判断指向的资源被加载：TSoftObjectPtr::IsValid，当返回true时，表示资源已加载，可以通过Get()的方式使用资源
FORCEINLINE bool IsValid() const
{
	return !!Get();
}
```
