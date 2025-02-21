# 使用 Android Preference

创建于 2021/10/19；编辑于 2024/04/15

---

## 在 XML 中构建 Preference

1. 在 `/res/xml` 中添加一个 xml 文件，名称任意，向其中添加以下代码以构建 `PreferenceScreen`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
</PreferenceScreen>
```

1. 向 `PreferenceScreen` 中添加具体的 `Preferences` 对象，详细信息可以参照 [Android 官方文档](https://developer.android.com/reference/androidx/preference/CheckBoxPreference)，添加完成后即可在设置界面找到已添加的设置项。

## 在 Java 中引用

可以在项目的任意位置中引用 `Preference`（存疑）。

**例：EditTextPreference**

```xml
<EditTextPreference
        app:key="signature"
        app:title="Your signature"/>
```

对于以上 XML 定义的 `Preference` 对象，按照如下代码获取 `Preference` 的值：

```Java
SharedPreferences sharedPreferences =
        PreferenceManager.getDefaultSharedPreferences(this /* Activity context */);
String name = sharedPreferences.getString("signature", "");
```


**需要注意的是**，并非所有的 `Preference` 对象都可用 `SharedPreferences.getString(String, String)` 获取值，比如对于 `SeekBarPreference` 需要使用 `SharedPreferences.getInt(String, Int)`。

详细信息可见 [SharedPreference 的文档](https://developer.android.google.cn/reference/android/content/SharedPreferences.html)，里面介绍了可以用来获取 `Preference` 对象的值的方法。
