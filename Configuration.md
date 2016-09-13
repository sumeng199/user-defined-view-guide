# Configuration
----
> * 用来描述设备的配置信息。如用户的配置信息：locale和scaling等等
> * 设备的相关信息：输入模式，屏幕大小，屏幕方向等等

#### 我们经常采用如下方式获取需要的相关信息
```python
Configuration configuration = getResources().getConfiguration();
// 获取国家码
int countryCode = configuration.mcc;
// 获取网络码
int networkCode = configuration.mnc;
// 判断横竖屏
if (configuration.orientation == Configuration.ORIENTATION_PORTRAIT) {

}
```
