# 无服务器架构入门实验

概览

## 前提条件
1. 本文所有实验皆基于AWS中国区北京区(cn-north-1)作示例。所有控制台链接均直接连接到北京区console。
1. 如果用海外账号做此lab，请不要用本文console链接跳转，直接在global console选择对应的产品即可。

## 内容目录
1. 创建您的第一个lambda函数
1. 创建Serverless架构的调查问卷表单
1. 安全部署Lambda函数
1. 通过Lambda发送公众号推送
>Note: 为了防止资源重名，请在创建资源名称时替代下述的“XX”。
>如果以下S3链接失效，或者没有权限请到https://github.com/cncoder/awsinit/tree/master/lambdaLabs下载。


## LAB1：创建您的第一个LAMBDA 函数
在本lab当中，您将会学习如何创建一个基于 python2.7 的 lambda 函数，如何在lambda 控制台修改示例代码，并且查看输出结果以及日志。

1. 打开[AWS中国区控制台lambda界面](https://console.amazonaws.cn/lambda/home?region=cn-north-1#/) ，并点击创建函数.
   ![](img/lab1-create-lambda.png)

1. 选择“从头开始创作”，输入您的函数名称，运行语言选择python2.7，执行角色选择使用现有角色，并在现有角色中选择lambda_basic_execution
   ![](img/lab1-lambda-details.png)
   
1. 确认无误就可以点击“创建函数”了
   ![](img/lab1-create-button.png)

1. 创建完之后会自动跳转到lambda 函数页面，默认写了一个样例代码
   ![](img/lab1-hello-world.png)

1. 编辑代码，在上述截图中的第五行插入一行```print(json.dumps(event, sort_keys=True, indent=4))```
   ![](img/lab1-json-dump.png)
   
1. **编辑完之后记得点击右上角的保存**
   ![](img/lab1-save-button.png)
   
1. 点击右上角选择“配置测试事件”
   ![](img/lab1-configure-test.png)
   
1. 通过下图配置模拟触发事件，测试刚刚创建的函数。
   ![](img/lab1-test-event.png)
   
1. 点击测试
   ![](img/lab1-test.png)
   
1. 查看运行结果
   ![](img/lab1-test-result.png)
 
1. 多尝试模拟运行几次。之后您可以打开Cloudwatch 查看更详尽的报告，还可以点击下图“查看CLoudwatch 中的警报”，查看历史控制台输出.
   ![](img/lab1-cloudwatch.png)
   
1. 历史输出
   ![](img/lab1-histroy-logs.png)
   

## LAB2: 创建SERVERLESS架构的调查问卷表单

在本lab中，您将会学习如何利用 **Lambda** , **API Gateway** 以及 **DynamoDB** 制作一个serverless架构的问卷表达。在本章节的最后，我们还将通过一个简单的示例演示lambda的冷启动以及复用效果。

1. 下载lambda 代码包    
点击下载[Survery-Survery.zip]() 以及 [SurverySubmit.zip]()

1. 创建IAM Role   
   - 打开[IAM Role 页面](https://console.amazonaws.cn/iam/home?#/roles)，选择创建角色并且选择lambda作为使用角色的服务
      ![](img/lab2-create-iam-role.png)

   - 添加Role权限    
   在下一屏幕分别添加"AmazonDynamoDBFullAccess", "CloudWatchLogsFullAccess", "AWSLambdaBasicExecutionRole" 三个策略。
   添加完之后进入第三步，直接点击下一步即可。
   
   - 审核无误后创建角色
      ![](img/lab2-review-role.png)

1. 创建DynamoDB Table. 打开[DynamoDB控制台](https://console.amazonaws.cn/dynamodb/home?region=cn-north-1#)，点击创建表。输入表名称，主键名称为``id``
   ![](img/lab2-create-ddb-table.png)

1. 创建一个名为survey-a-XX的lambda
   1. 角色设置：选择在前面步骤创建的lambdarole。
      ![](img/lab2-lambda-info.png)

   1. 上传代码Survery-survery.zip
      ![](img/lab2-upload-zip-code.png)
   
      ![](img/lab2-survey-survery.png)

   1. 修改程序主入口为```survey.lambda_handler```，然后 **保存** ，就成功部署survey-a-XX函数
   ![](img/lab2-lambda-handler.png)
   
1. 创建一个名为survey-b-XX 的lambda
   1. 角色设置
      ![](img/lab2-survey-b.png)
   1. 代码上传
      ![](img/lab2-survey-b-code.png)
   1. 修改程序主入口为```survey_submit.lambda_handler``` 并点击 **保存**，成功部署survey-b-XX函数。
      ![](img/lab2-survey-submit.png)
   1. 修改b函数的config.yaml文件。 在文件第一行添加下图字段，并输入在步骤3中创建的Dynamodb Table的名称。最后点击右上角的保存即可
      ![](img/lab2-config-yaml.png)

1. 创建API Gateway并进行配置    
   1. 创建API：点击跳转到[API Gateway控制台](https://console.amazonaws.cn/apigateway/home?region=cn-north-1#/apis)，并点击创建API，名为surveyAPI-XX  
      ![](img/lab2-create-API-gateway.png)
   1. 选择 **创建资源** ，资源名称为newsurvey
      ![](img/lab2-create-resource.png)
   1. **创建方法** 。 选择创建“ANY”，代表接受任何种类的https 请求。
      ![](img/lab2-create-method.png)
   1. **方法设置**。 集成环境选择Lambda函数，记得勾选lambda 代理集成，并选择survey-a-xx Lambda函数。
      ![](img/lab2-method-configuration.png)
   1. 同样的方法，在根路径下创建/submitsurvey资源，并创建“ANY”方法，这里选择的lambda 函数应该是survey-b-xx。
   1. 最后选择部署API,并设置**部署阶段** 
      ![](img/lab2-deploy-API.png) 
      ![](img/lab2-deploy-prod.png)

1. **测试**    
   1. 访问页面给出的URL，并在url路径后添加 **/newsurvey** 指定访问/newsurvey API。
      ![](img/lab2-API-url.png)
   1. 如下图可以看到已经成功部署了Serverless的问卷，可以填写这个表单，查看效果。
      ![](img/lab2-new-survey.png)
   1. 到Dynamodb服务中查看填写表单内容的存入。
      ![](img/lab2-ddb-content.png)
   1. 至此，一个无服务架构的表单系统已经完成。
   
1. **额外关注** : survey-a-XX 函数冷启动情况    
  - 请多次访问 survey html页面，多次触发lambda。然后打开survey lambda函数。下述代码截图显示了如何复用全局变量。
    ![](img/lab2-lambda-global-para.png)
  - 现在打开a 函数的监控，到cloudwatch 查看详细logs
    ![](img/lab2-lambda-a-cloudwatch.png)
  - 点击下图右上角的“查看cloudwatch 中的警报”
    ![](img/lab2-cloudwatch-alert.png)
  - 选择刚刚触发产生的logs
    ![](img/lab2-newest-log.png)
  - 查看详细logs，请看下图：
    ![](img/lab2-detailed-log.png)
    
## LAB3： 通过流量转移安全部署LAMBDA
在本节，您将会学习如何通过调整lambda不同版本的权重，实现安全的发布部署。

1. 打开在上一个实验中部署的survey-a-xx，并选择发布新版本
   ![](img/lab3-create-new-version.png)

1. 输入版本号：1，然后点击发布
   ![](img/lab3-version-1.png)
  
1. 发布完1版本之后，我们创建lambda 别名，并指定名称为images，设置当前别名指向到刚刚创建的1版本。最后点击创建.
   ![](img/lab3-create-alias.png)
   
   ![](img/lab3-how-to-create-alias.png)
1. 创建完images别名之后，请复制右上角的arn地址。
   ![](img/lab3-alias-arn.png)

1. 打开[API Gateway](https://console.amazonaws.cn/apigateway/home?region=cn-north-1#/apis)。选择在第二个实验中创建的surveyAPI-XX 页面，并编辑/newsurvey 下的ANY 方法。点击进入设置“集成请求”。
   ![](img/lab3-integration-request.png)

1. 点击编辑Lambda 函数的arn，粘贴上述images 的arn并保存
   ![](img/lab3-replace-arn.png)

1. 选择确定授权当前API 访问Lambda函数
   ![](img/lab3-grant-permission.png)
   
1. 对API Gateway更改设置后，不要忘记**重新部署**。在这里我们仍然部署到prod阶段。
   ![](img/lab2-deploy-API.png)
   ![](img/lab3-choose-prod.png)

1. 访问原来的API Gateway: https://XXXXX(您的路径).execute-api.cn-north-1.amazonaws.com.cn/prod/newsurvey，我们可以发现因为没有做任何改动，页面效果和原来一样。
   ![](img/lab3-visit-url.png)
   
1.现在模拟修改lambda代码，并使用别名部署新版本lambda。打开survey-a-XX lambda函数，编辑config.yml 文件。如下图，替换Image 的图片网址为[此图片网址](https://raw.githubusercontent.com/awslabs/serverless-application-model/master/aws_sam_introduction.png).
   ![](img/lab3-replace-image-url.png)

1. 改完之后如下图，然后点击右上角保存，最后点击“发布新版本”。
   ![](img/lab3-save-lambda-function.png)

1. 版本描述写2
   ![](img/lab3-version-2.png)

1. 现在打开之前创建的images 别名
   ![](img/lab3-check-alias.png)
   
1. 下拉页面，配置其他版本为2，权重设置为50%。并点击右上角保存。
   ![](img/lab3-adjust-weight.png)
   
1. 再次访问访问原来的API Gateway ：https://XXXXX(您的路径).execute-api.cn-north-1.amazonaws.com.cn/prod/newsurvey，多刷几次网页看看效果。
   ![](img/lab3-final-effect.png)
   
## LAB4： 通过LAMBDA 发送公众号推送
1. 点击[代码下载页面]()下载代码
1. 登录[微信测试号](https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login), 扫码登陆后，复制页面的“appID”和 “appsecret” （相当于开发者身份密钥）
   ![](img/lab4-wechat-ID.png)
1. 打开自己的微信，关注自己的测试公众号。并记录自己微信号（openid），复制备用
   ![](img/lab4-check-wechat-number.png)
1. 创建消息模版，这里已经为大家准备好了模版
   ```
   {{first.DATA}} 
   商品明细 
   名称: {{keyword1.DATA}} 
   价格:{{keyword2.DATA}}  
   交易时间:{{keyword3.DATA}} 
   {{remark.DATA}}
   ```
   ![](img/lab4-create-template.png)
   
1. 复制您的模版id
   ![](img/lab4-template-id.png)
   
1. 到这里为止您已经配置好了微信端，此时您应该已经有以下参数格式类似如下。请替换成您的参数，备用。
   ```
   appid = ‘wx1234cd5ds9dfg89’
   appsecret = ‘123abcdefg3b298674ce60c0ejf87ds’
   openid = ‘opDi3TmaFiF98ABCDEF_9M9nHJK’
   templateId = ‘7BCdeF9hIJ0Kl-aB6KvJ59ZM7jfj6fBIf1OL_-dDFb6’
   ```
   
1. 部署lambda, 选择Nodejs6.10
   ![](img/lab4-choose-language.png)
   
1. 上传部署lambda
   ![](img/lab4-upload-code.png)
   
1. 修改代码, 替换您的appid以及appsecret   
   ![](img/lab4-replace-appid.png)

1. 打开[SNS](https://console.amazonaws.cn/sns/v2/home?region=cn-north-1#/home)进行配置
   ![](img/lab4-create-SNS-topic.png)

1. 点击创建订阅
   ![](img/lab4-create-subscription.png)
   
1. 选择刚刚创建的lambda
   ![](img/lab4-choose-lambda-as-subscription.png)
   
1. 点击 **发布到主题**
   ![](img/lab4-publish-to-topic.png)

1. 您可以在消息处选择发送以下内容
   ```
   {
      "templateId": "7BCdeF9hIJ0Kl-aB6KvJ59ZM7jfj6fBIf1OL_-dDFb6", 
      "url": "z.cn",
      "openid":"opDi3TmaFiF98ABCDEF_9M9nHJK",
      "first":"跨境交易提醒!",
      "keyword1":"海淘...",
      "keyword2":"$100.00",
      "keyword3":"2019年03月18日",
      "remark":"点击查看详情"
   }
   ```
   ![](img/lab4-SNS-message.png)
   
1. 点击发送后您可以在微信中看到如下提醒，您可以尝试点击，会跳转到对应的超链接。其实就是在SNS中发送的 json里面定义了。
   ![](img/lab4-wechat-notification.png)
   
1. 至此，我们已经完成了

## 参考资料
[AWS官方博客：轻松使用 Serverless 架构实现微信公众号后台开发](https://aws.amazon.com/cn/blogs/china/easytouse-serverless-wechat-official-account-development/)