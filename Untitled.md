# Jenkins 实现 GitLab 特定分支提交时自动化构建

![96](https://upload.jianshu.io/users/upload_avatars/5083227/f851f189-b09f-429f-beed-af3c83f83a9b.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/96/h/96)

 

[Liucs](https://www.jianshu.com/u/11eaf19f000f)

 

关注

2018.06.04 15:53* 字数 208 阅读 3078评论 0喜欢 1

#### 一、GitLab 配置

1. 

   

   给 GitLab 配置 Jenkins 的部署秘钥（公钥，用 root 登录 GitLab）

   

   ![img](https://upload-images.jianshu.io/upload_images/5083227-a7f9211d2cd427c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   

   ![img](https://upload-images.jianshu.io/upload_images/5083227-2749aec61604bb53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

     注意：如果配置了 Jenkins-slave ，项目在哪个 slave 上进行构建 就需要把哪个 slave 的公钥配置到 GitLab 的部署秘钥中

2. 

   

   新建的项目中启用对应的部署秘钥

   

   ![img](https://upload-images.jianshu.io/upload_images/5083227-61b2a5f665a23c8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/927/format/webp)

   

   ![img](https://upload-images.jianshu.io/upload_images/5083227-bec32ea3f308a758.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

   秘钥未启用是显示的 enable，启用后显示的是 disable

3. 

   

   配置 GitLab 项目 push 事件的 web Hook（它会把事件信息发送通过URL发送给Jenkins）

   

   ![img](https://upload-images.jianshu.io/upload_images/5083227-c125ac6d0e52fe16.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/904/format/webp)

```
# GitLab 发送给 Jenkins 的内容
{
    "object_kind": "push",
        "event_name": "push",
        "before": "07e1b053d4f699a13e1abfdddbd7c53e7df1436a",
        "after": "42b8a70696eb3357963b6f076aed49cced0d20de",
        "ref": "refs/heads/release",
        "checkout_sha": "42b8a70696eb3357963b6f076aed49cced0d20de",
        "message": null,
        "user_id": 18,
        "user_name": "xxx",
        "user_username": "liuchengsheng",
        "user_email": "liuchengsheng@xxxxxxx.com",
        "user_avatar": "https://www.gravatar.com/avatar/d640f62f6441a0d232dab26113fc1e32?s=80\u0026d=identicon",
        "project_id": 19,
        "project": {
            "id": 19,
            "name": "test",
            "description": "测试",
            "web_url": "http://code.xxxxxxx.cc/liuchengsheng/test",
            "avatar_url": null,
            "git_ssh_url": "git@code.xxxxxxx.cc:liuchengsheng/test.git",
            "git_http_url": "http://code.xxxxxxx.cc/liuchengsheng/test.git",
            "namespace": "liuchengsheng",
            "visibility_level": 0,
            "path_with_namespace": "liuchengsheng/test",
            "default_branch": "master",
            "ci_config_path": null,
            "homepage": "http://code.xxxxxxx.cc/liuchengsheng/test",
            "url": "git@code.xxxxxxx.cc:liuchengsheng/test.git",
            "ssh_url": "git@code.xxxxxxx.cc:liuchengsheng/test.git",
            "http_url": "http://code.xxxxxxx.cc/liuchengsheng/test.git"
        },
        "commits": [
        {
            "id": "42b8a70696eb3357963b6f076aed49cced0d20de",
            "message": "six\n",
            "timestamp": "2018-06-04T11:12:10+08:00",
            "url": "http://code.xxxxxxx.cc/liuchengsheng/test/commit/42b8a70696eb3357963b6f076aed49cced0d20de",
            "author": {
                "name": "imlcs",
                "email": "15224800531@163.com"
            },
            "added": [

                ],
            "modified": [
                "README.MD"
                ],
            "removed": [

                ]
        }
    ],
        "total_commits_count": 1,
        "repository": {
            "name": "test",
            "url": "git@code.xxxxxxx.cc:liuchengsheng/test.git",
            "description": "测试",
            "homepage": "http://code.xxxxxxx.cc/liuchengsheng/test",
            "git_http_url": "http://code.xxxxxxx.cc/liuchengsheng/test.git",
            "git_ssh_url": "git@code.xxxxxxx.cc:liuchengsheng/test.git",
            "visibility_level": 0
        }
}
```

#### 二、Jenkins 配置

1. 

   

   配置获取 Git 仓库的私钥（就是刚才在 GitLab 中公钥所对应的私钥）

   

   ![img](https://upload-images.jianshu.io/upload_images/5083227-865b765c7b73f198.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/919/format/webp)

2. 安装

    

   Generic Webhook Trigger Plugin

    

   插件

   

   ![img](https://upload-images.jianshu.io/upload_images/5083227-51d5f66d7a955f30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/999/format/webp)

3. 

   

   新建项目（新版本 jenkins URL 配置中 token 一定要写）

   

   ![img](https://upload-images.jianshu.io/upload_images/5083227-bcd8b182c3bcf29a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

4. 自动构建测试，GitLab 用户在指定分支上提交代码触发 Jenkins 构建