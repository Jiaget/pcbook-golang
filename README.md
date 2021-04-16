# 准备
- 下载`clang-format`可以自动格式化`proto`代码 [github地址](https://github.com/llvm/llvm-project/releases/tag/llvmorg-12.0.0)
- 设置 

# protocol message
```
syntax = "proto3";

message <MessageName> {
    <data-type> fiedl_1 = tag_ 1;
    ...
}
```
- message 首字母大写
- 字段名小写
- 数据类型
  - string, bool, bytes
  - float,double
  - int32,int64,uint32....
- 数据类型还可以是自定义的枚举型或者其他message类型
- **tags比字段名还要重要**
  - tag 可以是任意的整数
    - tag 可以是 1 到 2**(29-1)之间的数
    - 除了19000 到 19999（因为这段数字作为保留数字供网络通信使用的）
  - 1到15占1个字节
  - 16到2047占两个字节
  - 上面两条意味着，频繁使用的字段应该标记1到15，减少开支
  - tag可以不用按顺序排列，但是同一级的字段的tag必须是独一无二的
# 生成代码
写好proto文件后，下载`protoc`与相关包
```
https://github.com/protocolbuffers/protobuf/releases
go get google.golang.org/grpc/cmd/protoc-gen-go-grpc
go get google.golang.org/protobuf/cmd/protoc-gen-go

```
写下面的命令生成go代码

`protoc -I=proto proto/*.proto --go_out=pb` 

- 其中，`-I` 代表porto文件的相对路径，`-go_out`代表输出的go代码路径， `proto/*.proto` 代表proto目录下所有的proto文件。
- 注意，当前gRPC版本需要在文件中添加两条指令。旧版本可能不需要，新版本会提示加上，不然报错。
```
package jiaget.pcbook; 
option go_package ="./;pb"; 
```
- 第一行： 声明包名，避免冲突
- 第二行： 声明go代码输出路径，该路径已写在生成指令里，但这里又必须写，随便写一个当前路径。用分号隔开的第二个参数是生成的go文件的package名。

# 客制化数据类型
- 枚举
  - Enum
- Message嵌套
  - 什么时候应该嵌套
  - 两个实体关系很近，且其中一个实体离开另一个实体没有意义，这个时候使用嵌套。
  - 例如：显示屏幕 - 分辨率

`import` 问题， vscode 默认import路径在项目根目录下， `proto` 代码Import 的路径总是需要加上`proto/`。当我们使用其他语言的时候，会切换到其他目录，这会非常麻烦。
在vscode `settings.json`下添加以下json信息。
```
"protoc": {
        "path": "/usr/local/bin/protoc",
        "options": [
            "--proto_path=protos/v3",
            "--proto_path=protos/v2",
            "--java_out=gen/java"
        ]
    }
```

格式化问题：
  - 在proto文件按`shift +  alt + f` vscode 会进行格式化程序的初始化，选择一项格式化的程序，并在`settings`确定启动自动格式化，之后vscode会自动帮助格式化。

