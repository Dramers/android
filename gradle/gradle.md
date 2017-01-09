# Gradle学习笔记
* 动态更改manifest中值：
    * Manifestfest中声明placehoder: ${holder} 
    * manifestPlaceholders = [holder:"xxxx"]
* 动态增加编译值：
    * buildConfigField "boolean", "LOG_DEBUG", "true"
* gradle读取属性文件
    *     Properties properties = new Properties();    properties.load(project.rootProject.file('local.properties').newDataInputStream());    properties.getProperty("key");
* 配置string资源
	*     resValue "string", "app_name", "test"
* 更改applicationId
	*     applicationIdSuffix '.debug'
          versionNameSuffix '-DEBUG'


    
## 完整实例
     buildTypes {
        release {
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            buildConfigField "boolean", "LOG_DEBUG", "true"                    manifestPlaceholders = [UMENG_APPKEY:""]
             if (properties.getProperty("UMENG_APPKEY") != null) {
                manifestPlaceholders = [UMENG_APPKEY: properties.getProperty("UMENG_APPKEY"),
                                        AMAP_KEY    : properties.getProperty("AMAP_KEY")]
            }
        }
        debug {
        if (properties.getProperty("distinguish_version", "false").equals("true")){
                applicationIdSuffix '.debug'
                versionNameSuffix '-DEBUG'
                resValue "string", "app_name", "Test Debug"
            }else{
                resValue "string", "app_name", "Test"
            }
            buildConfigField "boolean", "LOG_DEBUG", "true"
            manifestPlaceholders = [UMENG_APPKEY:""]
        }
    }