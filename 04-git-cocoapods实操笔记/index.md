# 一. cocoapods 远程私有库的实现步骤



1. 创建远程私有索引库

   > - 就是在自己的 git 服务器上创建一个git仓库, 专门用来存储自己的私有库的 `xxx.podspec`  描述文件的
   >
   > - 创建远程私有索引库和创建其它的git远程仓库是一样的.一般的远程git仓库是用来存储我们编写的代码的, 而远程私有索引仓库仅仅是用来存储我们私有库的描述文件 `xxx.podspec` 的
   > - 简单的说, 远程私有索引库, 就是我们在远程的git仓库上创建的一个git仓库, 只是这个仓库只存储 `xxx.podspec` 描述文件

2. 在本地电脑添加 本地私有索引库

   ```
   // YRBase 是远程私有索引库的名字
   // url 是远程私有索引库YRBase对应的url 地址
   pod repo add YRBase url 
   ```

   > 添加的远程私有索引库, 需要和当前电脑使用 ssh 连接

   

3. 在本地创建 私有代码库的模板库

   ```
   // pod lib create 是固定写法
   // YRBaseToolLib 是私有库的名字, 执行命令后会根据这个名字创建一个xcode工程 以及 git本地仓库, 以及帮助我们自动生生 podfile , YRBaseToolLib.podspec 等配置文件
   pod lib create YRBaseToolLib
   ```



4. 将核心代码拖入到 `YRBaseToolLib` 下的Classes目录下即可
5. pod intall 安装 YRBaseToolLib库中的classes 中的代码到模板库测试工程中, 进行测试即可

6. 编写描述文件基本信息

   - 如果不会写可以在cocospods 上找一个 `xxx.podspec`  文件, 参看修改即可
   - 编写`xxx.podspec` 描述文件的重点是, source 和版本信息 等

7. 将私有代码模板库全部提交到git仓库,并打tag

   ```
   git add . 
   git commit -m '提交信息'
   git remote 
   git remote add origin url
   git push origin master
   git tag
   git tag -a '1.0.0' -m '初始化xxx 库'
   git push --tags 
   ```

8. 检查本地的 spec 文件编写否合法

   ```
   pod lib lint
   ```

9. 检查远程私有索引库的 spec 编写是否合法

   ```
   pod spec lint
   ```

10. 向私有索引库提交新编写的 远程私有库的spec描述文件

    ```
    // pod repo push 是固定写法
    // YRBase 私有索引库的名字
    // xxx.podspec 要向私有索引库YRBase 提交的 spec 描述文件
    pod repo push YRBase xxx.podspec
    ```

    > 注意:
    >
    > 在执行此步骤时, 需要在 远程私有代码模板仓库目录

11. 在编写`podfile` 时需要注意:

    - 如果需要使用**cocoapods** 安装带有, 私有索引库中的库文件时, 编写的**podfile** 文件需要在前面明确的指明, 如下示例:

    ```
    source '官方索引库路径'
    source '私有索引库路径'
    
    use_frameworks!
    platform :ios, '8.0'
    
    target 'abcLib_Example' do
      pod 'abcLib'
     
    end
    ```

    > 如果不知道当前的官方索引库和私有索引库的地址, 可以使用命令 `pod repo` 在终端上查到.





# 二. 根据podfile安装指定的第三方库



- `pod install` : 根据 `podfile.lock` 文件 中的描述版本信息安装 第三方库

  > 在以前的旧版本中使用 `pod install ` 命令是会自动更新本地的版本索引库
  >
  > 如果在使用 `pod install` 时不希望更新版本索引库可以在候命使用`--no-repo-update`
  >
  > 新版的cocoapods 好像改了, 如果不清楚可以使用 命令 `pod --help` 或者`pod install --help` 查看对应的命令帮助.
  >
  > 新版的cocoapods 好像使用`pod install` 不会更新本地的索引库了, 使用`pod install --repo-update` 才会更新.

- `pod update` :  根据`podfile` 文件中的描述版本信息安装第三方库, 并更新 `podfile.lock` 文件的内容

  > 以前是使用 `pod install` 时会自动更新本地索引库, 现在是使用`pod update` 时会自动更行本地索引, 如果使用 `pod update ` 是不需要自动更新, 需要使用新的命名`pod update --no-repo-update` 

  