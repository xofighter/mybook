       本篇继续回答网友的问题，这个主题是如何通过邮件发送测试报告。通过邮件发送测试报告，这个很重要，也很有必要。不管你使用什么自动化测试框架，都建议添加发送测试报告的功能。先来设计我们的发送邮件的场景：假如我们测试报告是固定的名称，放在固定的路径下。

1.下载java mail api包

       浏览器打开https://mvnrepository.com/artifact/com.sun.mail/javax.mail/1.6.0，点击download jar，把下载下来的jar文件，放在当前项目的tools文件夹下，并添加到当前项目的library里。具体这里不过多描述，前面我们添加了很多次jar包。

2.在testSuite包下新建一个测试发送邮件的类。

       其实这个发送邮件的类，我们可以单独写成一个静态类，设置一个变量，邮件的附件，也就是我们测试报告文件的路径就是这个变量。今天我们暂时不考虑这么多，只是简单来利用QQ邮箱发送一个测试报告。我的测试报告是TestNG自动化生成的一个html文件。

3.如何开启QQ上的smtp服务

我们在使用QQ的smtp服务器之前，我们需要登录web qq邮箱，开启smtp，具体看下面两张图。

![img](https://img-blog.csdn.net/20170816220653102?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20170816220720832?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

上面红圈的授权码需要记录下来，放到我们接下来的代码中关于身份认证这块，替代password。如果你填写的是你QQ的密码，代码运行后应该报535错误，身份认证失败。

4. 测试发送邮件类的具体代码。
```java
package testSuite;

import java.util.Properties;

import javax.activation.DataHandler;
import javax.activation.DataSource;
import javax.activation.FileDataSource;
import javax.mail.BodyPart;
import javax.mail.Message;
import javax.mail.MessagingException;
import javax.mail.Multipart;
import javax.mail.PasswordAuthentication;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeBodyPart;
import javax.mail.internet.MimeMessage;
import javax.mail.internet.MimeMultipart;

public class SendEmail {

	public static void main(String[] args) {
		
		// 创建一个Property文件对象
		Properties props = new Properties();
	 
		// 设置邮件服务器的信息，这里设置smtp主机名称
		props.put("mail.smtp.host", "smtp.qq.com");
	 
		// 设置socket factory 的端口
		props.put("mail.smtp.socketFactory.port", "465");
	 
		// 设置socket factory
		props.put("mail.smtp.socketFactory.class","javax.net.ssl.SSLSocketFactory");
	 
		// 设置需要身份验证
		props.put("mail.smtp.auth", "true");
	 
		// 设置SMTP的端口，QQ的smtp端口是25
		props.put("mail.smtp.port", "25");
	 
		// 身份验证实现
		Session session = Session.getDefaultInstance(props, new javax.mail.Authenticator() {
	 
			protected PasswordAuthentication getPasswordAuthentication() {
	                    // 第二个参数，就是我QQ开启smtp的授权码
			return new PasswordAuthentication("570xxxx@qq.com", "kvsivuydzdbpbffd");
	 
			}
	 
		});
	 
		try {
	 
			// 创建一个MimeMessage类的实例对象
			Message message = new MimeMessage(session);
	 
			// 设置发件人邮箱地址
			message.setFrom(new InternetAddress("570xxx@qq.com"));
	 
			// 设置收件人邮箱地址
			message.setRecipients(Message.RecipientType.TO,InternetAddress.parse("2048xxxx@qq.com"));
	        
	        // 设置邮件主题
			message.setSubject("测试发送邮件");
	 
			// 创建一个MimeBodyPart的对象，以便添加内容
			BodyPart messageBodyPart1 = new MimeBodyPart();
	 
			// 设置邮件正文内容
			messageBodyPart1.setText("这个是邮件的正文部分");
	 
			// 创建另外一个MimeBodyPart对象，以便添加其他内容
			MimeBodyPart messageBodyPart2 = new MimeBodyPart();
	 
			// 设置邮件中附件文件的路径
			String filename = ".\\test-output\\emailable-report.html";
	 
			// 创建一个datasource对象，并传递文件
			DataSource source = new FileDataSource(filename);
	 
			// 设置handler
			messageBodyPart2.setDataHandler(new DataHandler(source));
	 
			// 加载文件
			messageBodyPart2.setFileName(filename);
	 
			// 创建一个MimeMultipart类的实例对象
			Multipart multipart = new MimeMultipart();
	 
			// 添加正文1内容
			multipart.addBodyPart(messageBodyPart1);
	 
			// 添加正文2内容
			multipart.addBodyPart(messageBodyPart2);
	 
			// 设置内容
			message.setContent(multipart);
	 
			// 最终发送邮件
			Transport.send(message);
	 
			System.out.println("=====邮件已经发送=====");
	 
		} catch (MessagingException e) {
	 
			   throw new RuntimeException(e);
	 
		}
	 
	}

}
```
       运行一下，我能收到邮件，邮件中附件确实是TestNG生成的测试报告文件。记得替换你自己QQ邮箱信息在上面相关的地方，关于如何通过邮件发送测试报告，就介绍到这里。至于如何在完成自动化测试脚本就第一时间通过邮件发送测试报告，有必要的话，在后面文件再介绍。
---------------------
作者：Anthony_tester 
来源：CSDN 
原文：https://blog.csdn.net/u011541946/article/details/77278837 
版权声明：本文为博主原创文章，转载请附上博文链接！