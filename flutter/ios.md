## IOS 编译错误

- **The 'Pods-Runner' target has transitive dependencies that include statically linked binaries:..**

  方案一：

  在报错的插件xxx_plugin中的iOS目录下，找到xxx_plugin.podspec文件并添加以下代码：

  ```text
  s.static_framework = true
  ```

  方案二（不推荐）：

  不确定是否会有其他坑，有的能解决，有的不能，建议使用方案一；

  在iOS 目录 Podfile 中增加：

  ```objective-c
  pre_install do |installer|
    Pod::Installer::Xcode::TargetValidator.send(:define_method, :verify_no_static_framework_transitive_dependencies) {}
  end
  ```

​       参照：https://zhuanlan.zhihu.com/p/363551822

- **Cannot find "xcodebuild". Xcode 11.0.0 or greater is required to develop for iOS**

  ~~~bash
  sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer/
  ~~~

- [!] Unable to find a specification for `Bugly` depended upon by `flutter_bugly`

  You have either:
   * out-of-date source repos which you can update with `pod repo update` or with `pod install --repo-update`.
   * mistyped the name or version.
   * not added the source repo that hosts the Podspec to your Podfile.

  ~~~
  
  ~~~

  

