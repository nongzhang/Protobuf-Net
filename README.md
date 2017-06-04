# Protobuf-Net

轻便高效的结构化数据存储

Unity3D中数据的序列化和反序列化实例
详细步骤：
从http://code.google.com/p/protobuf/ 下载最新的protobuf-net
protobuf-net下载后的目录如下图
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/1.png)

进入ProtoGen 中建一个记事本叫做Role.proto的文件，记住把文件后缀名改成.proto

Role.proto中的内容如下

1.// 技能信息  

2.message VO_Skill  

3.{  

4.    required int32 skillGroupId = 1; // 技能id 

5.    required int32 level = 2; // 技能等级  

6.}  

7.  
8.// 玩家信息消息  
9.  
10.message CS_RoleInfo 

11.{ 

12.    required int32 playerId = 1; // 角色id

13.    repeated VO_Skill skills = 2; // 技能组  

} 

其中字段message 你可以理解成c#中的类class ,VO_Skill 和 CS_RoleInfo均为类名。字段 required标示该字段是必须的，即实例化该类时必须对该字段赋值

字段 repeated标示该字段是可以重复的，repeated VO_Skill skills 你可以理解成 List<VO_Skill>VO_Skill skills.

下一步是把Role.proto文件转化成.cs文件

打开cmd命令行 进入 ProtoGen这个目录下我的是放在桌面了，所以如下图
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/2.png)

然后按下面的格式输入内容
protogen -i:Test1.proto -i:Test2.proto -i:Test3.proto -o:Output.cs -ns:com.fbmly.model
-i 是输入文件,可以有多个
-o 输出的cs文件, 只能有一个..如果-i有多个 会将所有的代码生成到这一个cs文件当中
-ns 命名空间   最好使用，如果不使用每次生成的默认命名空间是proto的文件名。
比如 我输入的是

protogen -i:Role.proto -o:Output.cs -ns:com.yyq.model
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/3.png)

按下回车键 我们就发现在ProtoGen这个目录下多了一个Output.cs的文件
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/4.png)

下面就是把Output.cs文件生成dll
打开Unity3D自带的编译器MonoDevelop，创建c#的 Library工程 如下图
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/5.png)

删除系统生成的Class1.cs文件，把刚才生成的Output.cs文件加载进来
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/6.png)

下面引入protobuf-net.dll库,在References右键 选择 Edit References如下图
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/7.png)

选择.Net Assembly 找到CpreOnly/iOS 目录 会看到 有一个 protobuf-net.dll库 ，双击它 在右面看到有这个就可以了，然后点击Ok
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/8.png)

然后选择Build-->Build All ，会看到窗口中显示Build successful 就是成功了.
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/9.png)

因为我的工程叫做Protobuf-NetTest 所以这时会在工程下看到有一个Protobuf-NetTest.dll
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/10.png)

这个库中有咱们的数据结构 既 VO_Skill 和 CS_RoleInfo类

下面就是把这个Protobuf-NetTest.dll 生成专门序列化的库,预编译要使用到的库为precompile.exe 在Precompile目录下,因为我的Protobuf-NetTest.dll是生成在 bin\Release目录下的，所以在刚才的工程的bin\Release目录下建造一个批处理文件project.bat,project.bat中的内容如下

1.C:\Users\jk\Desktop\proto_net\Precompile\precompile Protobuf-NetTest.dll -o:ProtobufSerializer.dll -t:com.yyq.model.ProtoSerializer 

其中 Protobuf-NetTest.dll 是上一步生成的dll的名字，ProtobufSerializer.dll是将要生成的序列化的库名字，com.yyq.model.ProtobufSerializer是命名空间(注意，这里如果从网上下载解压之后的文件夹名字像protobuf-net 668这样的，必须把空格去掉)

双击project.bat即可看到多了一个ProtobufSerializer.dll库文件，该库就是序列化的库

准备工作完成，在unity3d中使用
我们在unity的Assets下建个名字叫ProtoBuf的文件夹，把上一步生成的Protobuf-NetTest.dll ，ProtobufSerializer.dl 和 protobuf-net.dll复制到该文件夹下，我们在unity的Assets下建个名字叫Scripts的文件夹，建一个叫做ProtoTest.cs的文件

在unity场景中放两个按钮，名字分别叫做 Serialize 和 Deserialize，其中按钮Serialize的点击事件 调用函数 Serialize()，按钮Deserialize的点击事件调用函数 Deserialize().
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/11.png)

先点击Serialize 按钮，再点击Deserialize 按钮，则在控制台会看到输出
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/12.png)

注意：如果编译的dll文件放到unity中报错，在MonoDevelop中修改如下地方
![Image text](https://raw.github.com/nongzhang/Protobuf-Net/master/processingguide/13.png)
