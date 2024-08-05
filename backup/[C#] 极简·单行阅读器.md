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
JObject jsonObject = new JObject
{
    ["user_name"] = "52钓鱼崽",
    ["user_create_time"] = DateTime.Now.ToString(),
    ["phone"] = "1234567890",
    ["nick_name"] = "52钓鱼崽",
    ["nick_flag"] = 0,
    ["sex"] = 0,
    ["head_img"] = "http://read.home133.com/img/icon/picture.svg",
    ["status"] = 1,
    ["active_id"] = "A123",
    ["last_version"] = "123",
    ["update_version"] = "456",
    ["expire_date"] = DateTime.Now.AddDays(30.0).ToString(),
    ["qq_group"] = "123456",
    ["channel"] = "Web",
    ["chapter_ai_url"] = "https://acg-q.github.io/new_blog/post/12.html",
    ["member_share_cnt"] = 5,
    ["point_day"] = 50,
    // 更多 选项
    ["append"] = new JArray
    {
        new JObject
        {
            ["url"] = "",
            ["title"] = "52钓鱼崽 破解"
        }
    },
    // 这里应该是插件
    ["site"] = new JArray
    {
        new JObject
        {
            ["url"] = "",
            ["title"] = "",
            ["plug_uid"] = ""
        }
    },
    ["site"] = new JArray
    {
        new JObject
        {
            ["url"] = "",
            ["title"] = "",
            ["plug_uid"] = ""
        }
    },
    // 购买了哪些APP
    // 这里的参数都是 和 应用中心 的应用信息一一对应
    ["app"] = new JArray
    {
        new JObject
        {
            ["id"] = "",
            ["name"] = "",
            ["status"] = "1", // 这里必须存在且是数字
            ["enabled"] = "1", // 这里必须存在且是数字
            ["type"] = "1", // 这里必须存在且是数字
            ["path"] = "",
            ["imgUrl"] = "",
            ["remark"] = "",
            ["helpUrl"] = "",
            ["downloadUrl"] = "",
            ["updateUrl"] = "",
            ["className"] = "",
            ["version"] = "",
            ["price"] = "999", // 这里必须存在且是数字
            ["freeDay"] = "1", // 这里必须存在且是数字
            ["size"] = ""
        }
    },
    // 公告
    // url 存在，软件运行，打开网页
    // msg 存在，软件运行，打开弹窗
    // close 等于 true，软件运行后自动关闭
    // cap 不清楚，忘了
    ["msg"] = new JObject
    {
        ["msg"] = "",
        ["cap"] = "",
        ["url"] = "",
        ["close"] = false
    }
};

data = jsonObject;
```
3. 保持软件更新(不过这里没有测试过...)
> 把`AutoLogin`函数里的`ApiResult apiResult = Util.GetApiResult(Constants.API_PreRegInfo, 10);`复制一份
> 将获取到的内容通过Data传递下去
```diff
  ApiResult apiResult2 = Util.GetApiResult(Constants.API_AutoLogin, 10);
+ ApiResult apiResult = Util.GetApiResult(Constants.API_PreRegInfo, 10);
+ apiResult2.Data = apiResult.Data;
+ apiResult2.Status = 200;
  if (apiResult2.Status == 200)
```
> 在`UpdateDataFromJson`里面提取出`last_version`和`update_version`

```c#
string text = data["last_version"].ToString();
string text2 = data["update_version"].ToString();
```

```diff
- ["last_version"] = "123",
- ["update_version"] = "456",
+ ["last_version"] = text,
+ ["update_version"]  = text2,
```

<details>
<summary>完整的代码</summary>

```c#
string text = data["last_version"].ToString();
string text2 = data["update_version"].ToString();

JObject jsonObject = new JObject
{
    ["user_name"] = "52钓鱼崽",
    ["user_create_time"] = DateTime.Now.ToString(),
    ["phone"] = "1234567890",
    ["nick_name"] = "52钓鱼崽",
    ["nick_flag"] = 0,
    ["sex"] = 0,
    ["head_img"] = "http://read.home133.com/img/icon/picture.svg",
    ["status"] = 1,
    ["active_id"] = "A123",
    ["last_version"] = text,
    ["update_version"]  = text2,
    ["expire_date"] = DateTime.Now.AddDays(30.0).ToString(),
    ["qq_group"] = "123456",
    ["channel"] = "Web",
    ["chapter_ai_url"] = "https://acg-q.github.io/new_blog/post/12.html",
    ["member_share_cnt"] = 5,
    ["point_day"] = 50,
    // 更多 选项
    ["append"] = new JArray
    {
        new JObject
        {
            ["url"] = "https://acg-q.github.io/new_blog/post/12.html",
            ["title"] = "52钓鱼崽 破解"
        }
    },
    // 这里应该是插件
    ["site"] = new JArray
    {
        new JObject
        {
            ["url"] = "",
            ["title"] = "",
            ["plug_uid"] = ""
        }
    },
    // 购买了哪些APP
    // 这里的参数都是 和 应用中心 的应用信息一一对应
    ["app"] = new JArray
    {
        new JObject
        {
            ["id"] = "",
            ["name"] = "",
            ["status"] = "1", // 这里必须存在且是数字
            ["enabled"] = "1", // 这里必须存在且是数字
            ["type"] = "1", // 这里必须存在且是数字
            ["path"] = "",
            ["imgUrl"] = "",
            ["remark"] = "",
            ["helpUrl"] = "",
            ["downloadUrl"] = "",
            ["updateUrl"] = "",
            ["className"] = "",
            ["version"] = "",
            ["price"] = "999", // 这里必须存在且是数字
            ["freeDay"] = "1", // 这里必须存在且是数字
            ["size"] = ""
        }
    },
    // 公告
    // url 存在，软件运行，打开网页
    // msg 存在，软件运行，打开弹窗
    // close 等于 true，软件运行后自动关闭
    // cap 不清楚，忘了
    ["msg"] = new JObject
    {
        ["msg"] = "",
        ["cap"] = "",
        ["url"] = "",
        ["close"] = false
    }
};

data = jsonObject;
```
</details>

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

### 4. 修改会员信息

1.  找到`RegResult`类

![image](https://github.com/user-attachments/assets/e5b96599-7ffb-49ff-88bc-9b7c6f441ae3)

2. 修改`UpdateDataFromJson`方法

```diff
  public void UpdateDataFromJson(JToken data)
  {
	// 剩余可用积分
- 	this.SharePoint = (int)data["share"];
+   this.SharePoint = 9999;
	this.ExpireDate = data["expire_date"].ToString();
	// 积分总数
- 	this.TotalSharePoint = (int)data["total_share"];
+   this.TotalSharePoint = 9999;
	// 分享数量
- 	this.ShareUserCnt = (int)data["share_ucnt"];
+   this.ShareUserCnt = 456;
	// 不清楚
- 	this.PayPoint = ((data["pay_point"] == null) ? 5 : ((int)data["pay_point"]));
+   this.PayPoint = 999;
	// 用户状态
- 	this.Status = (UserStatus)(int)data["status"];
+	this.Status = UserStatus.Member;
  }
```

![image](https://github.com/user-attachments/assets/d37554a2-e0d7-4273-bf99-7f56ba65872e)


### 5. 插件免费下载(待完成)


# 相关资源

C#反编译工具: https://github.com/dnSpyEx/dnSpy
[![dnSpyEx/dnSpy - GitHub](https://gh-card.dev/repos/dnSpyEx/dnSpy.svg)](https://github.com/dnSpyEx/dnSpy)

[Read133_Offline.zip](https://github.com/user-attachments/files/16489657/Read133_Offline.zip)

