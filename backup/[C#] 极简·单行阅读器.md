> [!NOTE]
> 软件名称: 极简·单行阅读器
> 软件官网: [Read133](http://read.home133.com/)

# 离线版

> [!TIP]
> 强制开启离线版

1. 搜索"登录"
![image](https://github.com/user-attachments/assets/a45a423d-895e-48ed-b98d-605f469f0054)
2. 找到方法"BindLoginResult"
```C#
private void BindLoginResult(LoginResult result)
{
	if (result.LoginStatus == LoginStatus.UnLogin)
	{
		this.lblInfo.Text = "欢迎使用极简单行阅读器，请点击右键菜单注册或登录";
		...
	}
	this.lblInfo.Text = "欢迎使用极简单行阅读器，请点击右键菜单打开文本或者网页";
	...
	if (result.LoginStatus == LoginStatus.Login)
	{
		...
	}
	if (result.LoginStatus == LoginStatus.Offline || result.LoginStatus == LoginStatus.Error)
	{
		this.lblInfo.Text = "欢迎使用极简单行阅读器[离线]，请点击右键菜单打开文本";
		...
	}
	...
}
```
> 由此可见，通过"result.LoginStatus"来识别当前登录状态
4. 修改 `result.LoginStatus` 为 `LoginStatus.Offline`
5. 完成修改

![image](https://github.com/user-attachments/assets/94489e21-a94e-49c1-bffe-fd6dfa32e332)

> 缺少在线功能

# 在线版

## 模拟登录

> [!TIP]
> 目的: 绕过手动登录，模拟用户已经登陆过了，即触发`AutoLogin`方法

### `AutoLogin`方法

```c#
public static void AutoLogin()
{
    if (string.IsNullOrEmpty(Bis.UID))
    {
        ...
    }
    try
    {
        ApiResult apiResult2 = Util.GetApiResult(Constants.API_AutoLogin, 10);
        if (apiResult2.Status == 200)
        {
            Bis.LoRes.LoginStatus = LoginStatus.Login;
            Bis.LoRes.UpdateDataFromJson(apiResult2.Data);
            ...
        }
        ...
    }
    catch (Exception ex2)
    {
       ...
    }
}
```

### 1. 修改`UID`

> 这样才能开启"打开网页-热门站点"\“系统-会员”等多个页面

1. 找到获取UID的方法
![image](https://github.com/user-attachments/assets/859c8666-f2a9-4af6-950a-de406c7560f5)
4. 反编译方法 & 修改代码 & 编译
![image](https://github.com/user-attachments/assets/8970e133-fdf5-4a9a-b03a-b206bfcac67c)
![image](https://github.com/user-attachments/assets/4adf2493-dbf6-4af2-ab7d-c5b0914c0b90)
```diff
    public static string UID
    {
        get
        {
-           if (Bis.uid == null)
-           {
-               Bis.uid = DataProcess.GetKey("Sys", "UID");
-           }
-           return Bis.uid;
+          return "52钓鱼崽";
        }
        set
        {
        }
    }
```
### 2. 创建虚假登录res

1. 创建虚拟登录状态
设置 `apiResult2.Status` 为 200
2. 修改`apiResult2.Data`, 即修改登录结果
> 不过修改`apiResult2.Data`过于麻烦，所以还是直接修改`UpdateDataFromJson`函数

```c#
public void UpdateDataFromJson(JToken data)
{
	this.UserName = data["user_name"].ToString();
	...
}
```
> 都是通过传入的内容解析获取的
> 构建一个虚假登录内容用于解析

```c#
JObject jobject = new JObject();
jobject["user_name"] = "52钓鱼崽";
jobject["user_create_time"] = DateTime.Now.ToString();
jobject["phone"] = "1234567890";
jobject["nick_name"] = "52钓鱼崽";
jobject["nick_flag"] = 0;
jobject["sex"] = 0;
jobject["head_img"] = "http://example.com/image.jpg";
jobject["status"] = 1;
jobject["active_id"] = "A123";
jobject["last_version"] = "123";
jobject["update_version"] = "456";
jobject["expire_date"] = DateTime.Now.AddDays(30.0).ToString();
jobject["qq_group"] = "123456";
jobject["channel"] = "Web";
jobject["chapter_ai_url"] = "http://example.com/ai";
jobject["member_share_cnt"] = 5;
jobject["point_day"] = 50;
// 这里是下拉框更多
string text3 = "append";
JArray jarray = new JArray();
JObject jobject2 = new JObject();
jobject2["url"] = "";
jobject2["title"] = "52钓鱼崽 破解";
jarray.Add(jobject2);
jobject[text3] = jarray;
// 好像是和 nick_name 字段有关
string text4 = "site";
JArray jarray2 = new JArray();
JObject jobject3 = new JObject();
jobject3["url"] = "";
jobject3["title"] = "";
jobject3["plug_uid"] = "";
jarray2.Add(jobject3);
jobject[text4] = jarray2;
// 购买了哪些APP
// 这里的参数都是 和 应用中心 的应用信息一一对应
string text5 = "app";
JArray jarray3 = new JArray();
JObject jobject4 = new JObject();
jobject4["id"] = Guid.NewGuid().ToString();
jobject4["name"] = "52钓鱼崽";
jobject4["status"] = "1";
jobject4["enabled"] = "1";
jobject4["type"] = "1";
jobject4["path"] = "52钓鱼崽";
jobject4["imgUrl"] = "https://static.52pojie.cn/static/image/common/logo.png";
jobject4["remark"] = "52钓鱼崽";
jobject4["helpUrl"] = "https://www.52pojie.cn/";
jobject4["downloadUrl"] = "https://www.52pojie.cn/";
jobject4["updateUrl"] = "https://www.52pojie.cn/";
jobject4["className"] = "52钓鱼崽";
jobject4["version"] = text;
jobject4["price"] = "999";
jobject4["freeDay"] = "1";
jobject4["size"] = "520";
jarray3.Add(jobject4);
jobject[text5] = jarray3;
// 公告
// url 存在，软件运行，打开网页
// msg 存在，软件运行，打开弹窗
// close 等于 true，软件运行后自动关闭
// cap 不清楚，忘了
string text6 = "msg";
JObject jobject5 = new JObject();
jobject5["msg"] = "";
jobject5["cap"] = "";
jobject5["url"] = "";
jobject5["close"] = false;
jobject[text6] = jobject5;
data = jobject;
```
3. 保持软件更新(不过这里没有测试过...)
> 把`AutoLogin`函数里的`ApiResult apiResult = Util.GetApiResult(Constants.API_PreRegInfo, 10);`复制一份
> 将获取到的内容通过Data传递下去
```diff
  ApiResult apiResult2 = Util.GetApiResult(Constants.API_PreRegInfo, 10);
+ ApiResult apiResult3 = Util.GetApiResult(Constants.API_AutoLogin, 10);
+ apiResult3.Data = apiResult2.Data;
+ apiResult3.Status = 200;
  if (apiResult3.Status == 200)
```
> 在`UpdateDataFromJson`里面提取出`last_version`和`update_version`

```c#
string text = data["last_version"].ToString();
string text2 = data["update_version"].ToString();
```

```diff
jobject["last_version"] = "123";
jobject["update_version"] = "456";
jobject["last_version"] = text;
jobject["update_version"] = text2;
```

### 3. 应用免费下载

1. 找到`AddAppBox`这个函数
```c#
private void AddAppBox(ReApp app)
{
	try
	{
		Border border = this.CreateBorder();
		Grid grid = this.CreateGridBox(app);
		StackPanel stackPanel = this.CreateImgPannel(app.ImageUrl);
		Label label = this.CreateTitleLabel(10, "应用名：");
		Label label2 = this.CreateTitleLabel(35, "积分：");
		Label label3 = this.CreateTitleLabel(60, "演示：");
		Label label4 = this.CreateTitleLabel(85, "描述：");
		Label label5 = this.CreateLabel(10);
		label5.Content = app.Name;
		if (!string.IsNullOrEmpty(app.Size))
		{
			Label label6 = label5;
			label6.Content = string.Concat(new object[] { label6.Content, " [", app.Size, "]" });
		}
		Label label7 = this.CreateLabel(35);
		this.BindPrice(label7, app);
		...
	}
	catch (Exception ex)
	{
		Util.AddLog("AddAppBox Error:" + ex.Message);
	}
}
```
2. 修改app的积分价格
```diff
+ app.Price = 0.0;
  this.BindPrice(label7, app);
```
3. 允许打开应用
> 找到`AppController`类
> 找到`AppController`下的`CheckAppEnable`方法

```diff
public ApiResult CheckAppEnable()
{
-    return Util.GetApiResult(string.Concat(new string[]
+    ApiResult apiResult = Util.GetApiResult(string.Concat(new string[]
    {
        Constants.API_CheckAppEnabled,
        "?appId=",
        this.app.AppID,
        "&appVer=",
        this.app.Version
     )), 3);
+    apiResult.Status = 200;
+    return apiResult;
}
```

### 4. 修改会员状态为永久会员

> 在修改登录结果时，将"status"设置为1即可

![image](https://github.com/user-attachments/assets/bac5b871-f336-44ea-91a4-ed0115139a81)

```c#
jobject4["status"] = "1";
```

### 5. 插件免费下载(待完成)


# 相关资源

C#反编译工具: https://github.com/dnSpyEx/dnSpy
[![dnSpyEx/dnSpy - GitHub](https://gh-card.dev/repos/dnSpyEx/dnSpy.svg)](https://github.com/dnSpyEx/dnSpy)

[Read133_Offline.zip](https://github.com/user-attachments/files/16489657/Read133_Offline.zip)

