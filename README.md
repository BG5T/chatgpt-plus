# ChatGPT-Plus

·增加了反代设置（可选关闭不影响之前存储的数据），无需存储图片到本地OSS。
·增加保存图片到OSS设置（在图片的左上角有个收藏按钮）
go.mod
//替换discordgo依赖地址 github.com/bg5t/mydiscordgo 0.28.1 版本号随便设置的，主要是为了把反代地址塞进去，所以不能用原来作者的。

api\service\mj\bot.go
// 导入包 discordgo "github.com/bg5t/mydiscordgo"
// 在func Run里把配置文件中的 gateway 和cdn和wss反代地址传给discordgo

api\service\mj\client.go
// 在func Imagine和Upscale和Variation里增加了反代地址

api\service\mj\service.go
// 在func Service里的Notify增加了反代地址

api\core\types\config.go
// 在type MidJourneyConfig里增加了下面4个字段
// Discord        string // https://discord.com 反代地址
// Discordcdn     string // https://cdn.discordapp.com 反代地址
// Cdn            bool   // 是否启用反代
// Discordgateway string // wss://gateway.discord.gg wws反代地址

api\handler\mj_handler.go

// 增加func Save接口，可以自己手动选择需要存储的图片到OSS，避免丢失。

web\src\views\ImageMj.vue ImageSD.vue
web\src\assets\css\image-mj.css image-sd.css
web\src\assets\css\task-list.styl
//在图片上增加收藏按钮，点击会从远端地址存储到OSS
//修改img_url的后缀参数兼容阿里云图片压缩和discord的webp压缩

# https://discord.com 反代地址 Nginx配置文件
  location ^~ /discord/ {
    proxy_pass https://discord.com/; 
    proxy_http_version 1.1; 
    proxy_set_header Upgrade $http_upgrade; 
    proxy_set_header Connection "Upgrade"; 
    add_header Cache-Control no-cache; 
    proxy_set_header Host discord.com; 
}

# https://cdn.discordapp.com 反代地址 Nginx配置文件
location ^~ /cdn {
    proxy_pass https://media.discordapp.net/; 
    proxy_set_header Host media.discordapp.net; 
    proxy_set_header X-Real-IP $remote_addr; 
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
    proxy_set_header REMOTE-HOST $remote_addr; 
    proxy_set_header Upgrade $http_upgrade; 
    proxy_set_header Connection "upgrade"; 
    proxy_set_header X-Forwarded-Proto $scheme; 
    proxy_http_version 1.1; 
    proxy_ignore_headers Set-Cookie Cache-Control expires; 
    proxy_cache proxy_cache_panel; 
    proxy_cache_key $host$uri$is_args$args; 
    proxy_cache_valid 200 304 301 302 10m; 
    if ( $uri ~* "\.(gif|png|jpg|css|js|woff|woff2|webp)$" ) {
        expires 1d; 
    }
}

# wss://gateway.discord.gg wws反代地址 Nginx配置文件
location ^~ /wss {
    proxy_pass https://gateway.discord.gg; 
    proxy_set_header Host gateway.discord.gg; 
    proxy_set_header Upgrade $http_upgrade; 
    proxy_set_header Connection "upgrade"; 
    proxy_http_version 1.1; 
    add_header Cache-Control no-cache; 
}


**ChatGPT-PLUS** 基于 AI 大语言模型 API 实现的 AI 助手全套开源解决方案，自带运营管理后台，开箱即用。集成了 OpenAI, Azure,
ChatGLM,讯飞星火，文心一言等多个平台的大语言模型。集成了 MidJourney 和 Stable Diffusion AI绘画功能。主要有如下特性：

* 完整的开源系统，前端应用和后台管理系统皆可开箱即用。
* 基于 Websocket 实现，完美的打字机体验。
* 内置了各种预训练好的角色应用，比如小红书写手，英语翻译大师，苏格拉底，孔子，乔布斯，周报助手等。轻松满足你的各种聊天和应用需求。
* 支持 OPenAI，Azure，文心一言，讯飞星火，清华 ChatGLM等多个大语言模型。
* 支持 MidJourney / Stable Diffusion AI 绘画集成，开箱即用。
* 支持使用个人微信二维码作为充值收费的支付渠道，无需企业支付通道。
* 已集成支付宝支付功能，支持多种会员套餐和点卡购买功能。
* 集成插件 API 功能，可结合大语言模型的 function 功能开发各种强大的插件，已内置实现了微博热搜，今日头条，今日早报和 AI
  绘画函数插件。

## 关于部署镜像申明

由于目前部署人数越来越多，本人的阿里云镜像仓库流量不够支撑大家使用了。所以从 v3.2.0 版本开始，一键部署脚本和部署镜像将只提供给 **[付费技术交流群]** 内用户使用。
代码依旧是全部开源的，大家可自行编译打包镜像。

## 功能截图

### PC 端聊天界面

![ChatGPT Chat Page](/docs/imgs/gpt.gif)

### AI 对话界面

![ChatGPT new Chat Page](/docs/imgs/chat-new.png)

### MidJourney 专业绘画界面

![mid-journey](/docs/imgs/mj_image.jpg)

### Stable-Diffusion 专业绘画页面

![Stable-Diffusion](/docs/imgs/sd_image.jpg)
![Stable-Diffusion](/docs/imgs/sd_image_detail.jpg)

### 绘图作品展

![ChatGPT image_list](/docs/imgs/image-list.png)

### AI应用列表

![ChatGPT-app-list](/docs/imgs/app-list.jpg)

### 会员充值

![会员充值](/docs/imgs/member.png)

### 自动调用函数插件

![ChatGPT function plugin](/docs/imgs/plugin.png)
![ChatGPT function plugin](/docs/imgs/mj.jpg)

### 管理后台

![ChatGPT admin](/docs/imgs/admin_dashboard.png)
![ChatGPT admin](/docs/imgs/admin_config.jpg)
![ChatGPT admin](/docs/imgs/admin_models.jpg)
![ChatGPT admin](/docs/imgs/admin_user.png)

### 移动端 Web 页面

![Mobile chat list](/docs/imgs/mobile_chat_list.png)
![Mobile chat session](/docs/imgs/mobile_chat_session.png)
![Mobile chat setting](/docs/imgs/mobile_user_profile.png)
![Mobile chat setting](/docs/imgs/mobile_pay.png)

### 7. 体验地址

> 免费体验地址：[https://ai.r9it.com/chat](https://ai.r9it.com/chat) <br/>
> **注意：请合法使用，禁止输出任何敏感、不友好或违规的内容！！！**

## 使用须知

1. 本项目基于 MIT 协议，免费开放全部源代码，可以作为个人学习使用或者商用。
2. 如需商用必须保留版权信息，请自觉遵守。确保合法合规使用，在运营过程中产生的一切任何后果自负，与作者无关。


## 项目地址

* Github 地址：https://github.com/yangjian102621/chatgpt-plus
* 码云地址：https://gitee.com/blackfox/chatgpt-plus

## 客户端下载

目前已经支持 Win/Linux/Mac/Android 客户端，下载地址为：https://github.com/yangjian102621/chatgpt-plus/releases/tag/v3.1.2

## TODOLIST
* [ ] 支持基于知识库的 AI 问答
* [ ] 会员邀请注册推广功能
* [ ] 微信支付功能

## 项目文档

详细的部署和开发文档请参考 [**ChatGPT-Plus 文档**](https://ai.r9it.com/docs/)。

加微信进入微信讨论群可获取 **一键部署脚本（添加好友时请注明来自Github!!!）。**

![微信名片](docs/imgs/wx.png)

## 参与贡献

个人的力量始终有限，任何形式的贡献都是欢迎的，包括但不限于贡献代码，优化文档，提交 issue 和 PR 等。

#### 特此声明：由于个人时间有限，不接受在微信或者微信群给开发者提 Bug，有问题或者优化建议请提交 Issue 和 PR。非常感谢您的配合！

### Commit 类型

* feat: 新特性或功能
* fix: 缺陷修复
* docs: 文档更新
* style: 代码风格或者组件样式更新
* refactor: 代码重构，不引入新功能和缺陷修复
* opt: 性能优化
* chore: 一些不涉及到功能变动的小提交，比如修改文字表述，修改注释等

## 打赏

如果你觉得这个项目对你有帮助，并且情况允许的话，可以请作者喝杯咖啡，非常感谢你的支持～

![打赏](docs/imgs/donate.png)

![Star History Chart](https://api.star-history.com/svg?repos=yangjian102621/chatgpt-plus&type=Date)



