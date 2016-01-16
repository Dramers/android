# Gradle学习笔记
* 动态更改manifest中值：
    * Manifestfest中声明placehoder: ${holder} 
    * manifestPlaceholders = [holder:"567127d4e0f55a36b10004a7"]
* 动态增加编译值：
    * buildConfigField "boolean", "LOG_DEBUG", "true"
* gradle读取属性文件
    *     Properties properties = new Properties();    properties.load(project.rootProject.file('local.properties').newDataInputStream());    properties.getProperty("key");
    
## 完整实例
     buildTypes {
        release {
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            buildConfigField "boolean", "LOG_DEBUG", "true"                    manifestPlaceholders = [UMENG_APPKEY:"567127d4e0f55a36b10004a7"]
             if (properties.getProperty("UMENG_APPKEY") != null) {
                manifestPlaceholders = [UMENG_APPKEY: properties.getProperty("UMENG_APPKEY"),
                                        AMAP_KEY    : properties.getProperty("AMAP_KEY")]
            }
        }
        debug {
            buildConfigField "boolean", "LOG_DEBUG", "true"
            manifestPlaceholders = [UMENG_APPKEY:"55f1672367e58eac62000ff6"]
        }
    }