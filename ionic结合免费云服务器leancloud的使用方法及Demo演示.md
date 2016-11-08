#LeadCloud云服务器介绍LeanCloud 是国内的移动应用一站式云服务。LeanCloud提供了数据存储、实时消息、统计分析以及多种扩展组件，全面涵盖移动应用开发的需求，支持 iOS、Android、Web 等多平台。它帮助开发者摆脱后端开发负担以专注于产品创新，同时缩短开发周期、节省开发投入、快速进入市场。

比较知名的客户有“知乎”、“暴走漫画”、“丁香客”等，而我自己之前在做原生app开发的时候在服务器端也采用了LeanCloud，原因很简单，操作方便，功能齐全，其中封装了很多的方法，而我们只需要使用它提供的api就可以实现功能。

而现在我们主要使用ionic前端框架来开发WebApp，恰好leadCloud支持javaScript，所以我考虑能不能将ionic结合leancloud来使用，这里写了一个登陆的Demo，有兴趣的可以看下。#Demo界面展示

##登陆界面：
<img src="http://static.oschina.net/uploads/space/2016/1025/123841_HEgt_2493500.png" width=300 style="position:relative;left:50%;margin-left:-150px"/>

##注册界面：

<img src="http://static.oschina.net/uploads/space/2016/1025/123658_keAG_2493500.png" width=300 style="position:relative;left:50%;margin-left:-150px"/>

##手机号登录界面：<img src="http://static.oschina.net/uploads/space/2016/1025/123912_HC50_2493500.png" width=300 style="position:relative;left:50%;margin-left:-150px"/>

##个人信息界面：<img src="http://static.oschina.net/uploads/space/2016/1025/123822_nO6i_2493500.png" width=300 style="position:relative;left:50%;margin-left:-150px"/>

#已实现功能

* <font color=red>登录 :</font>若登录失败，则返回错误信息，如：用户名不存在，密码错误等。* <font color=red> 注册 :</font>如果必填信息没有填写，则Toast提醒，其中，手机号和邮箱做了规范性处理。* <font color=red> 唯一性 :</font>用户名与邮箱做了唯一性处理，即同一个用户名和邮箱只能注册一次* <font color=red> 手机号登陆 :</font>输入你的手机号，如果该手机号在数据库中存在，则系统将给此手机号发送一条短信，输入短信中的验证码，核对完成之后即可实现登录。* <font color=red> 记住密码 :</font>登录时默认选中记住密码，下次登录时默认自动填写用户名和密码，否则只保存当前用户名。* <font color=red> 重置密码 :</font>系统将发送一封邮件至当前登录用户的邮箱，在邮件中点开重置密码的链接即可修改密码，立即生效。#使用方法
1. 登录 [https://leancloud.cn/](https://leancloud.cn/) ，创建一个项目，在个人应用列表中可以看到项目。
2. 在index页面中引入：

	```javascript
	<script src="https://cdn1.lncld.net/static/js/av-core-mini-0.6.3.js"></	script>
	```3. 创建应用后，可以在控制台-应用设置中找到应用对应的id和key。
4. 进行代码初始化，加入以下的代码，就可以进行相应的初始化操作。	```javascript
	// 初始化 param1：应用 id、param2：应用 key
	AV.initialize('aCRbjvo2hjCETdDIABfWdze1', '597U1ilGI8m4sDKrqL9RIiYb');
	```
5. 	需要做什么直接查询官网提供的api即可.

#开发细节

##登录
```javascript
AV.User.logIn($scope.loginData.username, $scope.loginData.password, { //将用户名和密码post至云服务器
                success: function(user) {
                    // 成功了，现在可以做其他事情了.
                    Toast.show('登录成功', 'short');
                    console.log('登录成功', 'short')
                    var ischecked = document.getElementById('checkboxId'); //判断是否记住密码
                    if (ischecked.checked == true) {
                        window.localStorage.test_username = $scope.loginData.username;
                        window.localStorage.test_password = $scope.loginData.password;
                    } else {
                        window.localStorage.name = $scope.loginData.username;
                        window.localStorage.password = '';
                    }
                    $state.go('personInfo') //登录成功，进入个人详情界面
                },
                error: function(user, error) {
                    // 失败了.
                    console.log("Error: " + error.code + " " + error.message);
                    if (error.code == 211) {
                        $timeout(function() {
                            Toast.show('用户名不存在');
                            return
                        }, 10)
                    }
                    if (error.code == 210) {
                        $timeout(function() {
                            Toast.show('密码错误');
                            return
                        }, 10)
                    }
                }
            });
```

##注册

```javascript
var user = new AV.User(); //初始化用户
            user.set("username", $scope.registerData.username); //用户名
            user.set("password", $scope.registerData.password); //密码
            user.set("nickname", $scope.registerData.nickname); //昵称
            user.set("email", $scope.registerData.email); //邮箱
            user.set("mobilePhoneNumber", $scope.registerData.tel); //电话
            user.set("qq", $scope.registerData.qq); //qq
            user.set("sex", $scope.registerData.sex); //性别
            user.signUp(null, {
                success: function(user) {
                    Toast.show('注册成功');
                    window.localStorage.test_username = $scope.registerData.username;
                    window.localStorage.test_password = $scope.registerData.password;
                    $scope.goBack(); //回到主界面
                },
                error: function(user, error) {
                    // 失败了
                    if (error.code == 214) {
                        $timeout(function() {
                            Toast.show('电话已存在'); //同一个电话数据库中只运行存在一个
                            return
                        }, 10)
                    }
                    if (error.code == 202) {
                        $timeout(function() {
                            Toast.show('用户名已存在'); //同一个用户数据库中只运行存在一个
                            return
                        }, 10)
                    }
                    if (error.code == 203) {
                        $timeout(function() {
                            Toast.show('邮箱已存在'); //同一个邮箱数据库中只运行存在一个
                            return
                        }, 10)
                    }
                    Toast.show("Error: " + error.code + " " + error.message);
                    console.log("Error: " + error.code + " " + error.message)
                }
            });
```

##发送短信

```javascript
//在应用选项开启 "启用帐号无关短信验证服务（针对 requestSmsCode 和 verifySmsCode 接口）"
            AV.Cloud.requestSmsCode($scope.telLoginData.tel).then(function() {
                发送成功
                console.log('发送成功')
                $timeout(function() {
                    Toast.show('发送成功');
                    return
                }, 10)
            }, function(err) {
                //发送失败
                console.log('发送失败')
            });
```

##获取个人信息

```javascript
$scope.personInfo = AV.User.current()._serverData;
```
这里只需要使用AV.User.current()就可以获取当前登录用户的所有信息，包括登录时间等等很多详细信息，其中_serverData保存的是用户的基本信息。

###特别说明

在个人信息界面，圆形头像的定位：

1.	这里，我使用的是一个方形头像，然后使用border-radius：50%变成圆形2.	由于悬浮在div之上，需要设置postion:absolute，然后动态定位3.	在controller加载的时候，使用
	```javascript
	$('#headPicId').css('top', $('#photoId').height() - 30);
	```来动态定位圆形头像的位置，不过使用calc来计算也是可以的。



云服务器总体来说使用起来还是比较方便的，希望对大家有帮助。