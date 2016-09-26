### MAC下配置使用Nexus3.0
1. 简介
    
	Nexus 是一个可以在自己服务器上搭建 maven 仓库的工具，目前版本是3.0.1（2016.09.07），具体信息参见[nexus官网](https://www.sonatype.com/download-oss-sonatype)。
版本3和版本2差异比较大，版本2不是很了解，我是从3开始使用，版本3直接有mac版本，直接下载安装，没太多技术问题。
2. 配置
	1. 登录
安装完成之后，启动 nexus，访问http://localhost:8081  登录在右上角，默认登录账户和密码是 admin 和 admin123，登录上去之后首先把密码改了。
	1. 新增仓库
登录成功之后，在标题栏选择设置，再到`Repository`下选择`Create repository`之后再选择`maven2(hosted)`之后就到了仓库的设置页面。![设置仓库](https://github.com/Dramers/android/raw/master/nexus/nexus20160908-0.png)
名字先随便取，下面使用的时候会用到，我们这里填写为`android-snap`。
关于`type of artifacts`的选择，如果你最终想要上传的aar或者包是SNAPSHOT版本的话，则可以勾选`Snapshot`，否则可以勾选`Release`. 我们这里选择`Snapshot`
Deployment policy选择`Allow Redeploy`，之后创建仓库。![建立仓库](https://github.com/Dramers/android/raw/master/nexus/nexus20160908-1.png)
	1. 配置规则
其实通过以上2步就可以使用 nexus 了，但是有些情况下不想给所有人都是 admin 账户权限，这时候就需要新建账户了。
一般新建账户我们都希望自己制定权限范围，这时候就需要 Roles 了。
在 Security-->Roles中选择`Create role`，选择`Nexus role`，之后填写配置内容，关键点在于配置 Privileges, 其中带有\*的表示通配符，包括所有的`add browser view`等权限，一般选择你创建仓库名称下的所有权限。可以直接通过 filter 快速过滤想要的权限。我们这里选择刚创建的仓库`android`下的所有权限。![配置规则](https://github.com/Dramers/android/raw/master/nexus/nexus20160908-2.png)
	1. 角色
创建角色就相对简单一些了，在Security-->Users下选择`Create user`只需要在 Roles 一栏中选择上一步配置好的 Role 就可以。 这里创建用户名和密码分别为 android android 的账户。![配置角色](https://github.com/Dramers/android/raw/master/nexus/nexus20160908-3.png)

1. 上传项目包

	在项目的 gradle.properties 中添加配置：
	
		 nexusUrl=http://localhost:8081/repository/android #需要android这个仓库
		 nexusSnapUrl=http://localhost:8081/repository/android-snapshot
   		 nexusUsername=android
   		 nexusPassword=android
	来到需要上传到 nexus 的 module 的build.gradle中，添加以下配置，注释后面都有参数说明：
	
    		apply plugin: 'maven'//添加maven plugin
			group = 'com.zxc.support'//上传的group name 一般设置为包名
			version = '1.0-SNAPSHOT'//版本号 -SNAPSHOT代表上传到Snapshot库并且会更新Snapshot中的版本号
		    uploadArchives {
	      	repositories {
	      	  mavenDeployer {
	       	     repository(url: "${nexusUrl}") {
	       	         authentication(userName: nexusUsername, password: nexusPassword)
	        	    }
	        	    snapshotRepository(url: "${nexusSnapUrl}") {
	         	       authentication(userName: nexusUsername, password: nexusPassword)
	          	  }
	      		}
	   		}
			}
然后在 Android Studio 右侧选择`Gradle projects`视图，找到 module名-->upload-->uploadArchives,双击 uploadArchives 运行 gradle，没有问题的话 module 中打出的 aar 就会上传到 nexus，上传成功后在 nexus 的 andoid-snap 中会看到刚才上传 module。
1. #####使用 nexus 中包
使用的话想对简单一些，首先在 Project 级别的 build.gradle 中配置自己的仓库地址

		allprojects {
   		 repositories {
     	   jcenter()
    	    maven{url 'http://localhost:8081/repository/android-snapshot/'}
   		 }
		}
之后在想要使用的 module 中
     	dependencies {
  		  compile 'com.raventech.support:support:1.0-SNAPSHOT'
          compile 'group名字:module名字:版本号'
		}
1. #####
如果想要在项目中每次都更新 snapshot 最新版本，需要在 module 中配置更改 gradle 默认缓存刷新时间：

		apply plugin: 'java' //so that there are some configurations

		configurations.all {
		  resolutionStrategy {
		    // cache dynamic versions for 10 minutes
		    cacheDynamicVersionsFor 10*60, 'seconds'
		    // don't cache changing modules at all
		    cacheChangingModulesFor 0, 'seconds'
		  }
		}

1. 总结
	
	总体来说还是挺简单，一旦成功一次后面就轻车熟路了，首次学习成本比较高。
	[官方文档](http://books.sonatype.com/nexus-book/index.html)

