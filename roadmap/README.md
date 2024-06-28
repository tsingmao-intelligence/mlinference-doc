# 🎇 快速开始

## 0x01 安装与认证

### 认证文件部署

请联系发行方获得相应的安装包，以及认证文件。将认证文件cert放入\~/.mi文件夹下。

### 主体包安装

将下载下来的安装包解压至任意位置，并设置好相应的路径

```bash
export PATH=/path/to/package/bin:$PATH
export LD_LIBRARY_PATH=/path/to/package/lib:$LD_LIBRARY_PATH
```

激活主机中部署的python环境，执行如下命令来安装相应的whl包

```sh
# cd to the unzipped package
pip install python/*.whl
```

## 0x02 快速构建大模型服务

### 权重转换

MLInference提供了一键启动大模型服务的入口。首先需要准备好模型权重文件（HF格式），之后，通过如下命令进行权重文件的格式转换：

```bash
mkdir -p path/to/out
python -m mi.convert --path /path/to/model --out /path/to/out --tp 1/2/4/8
```

其中--tp指tensor并行的维度，按照需求来进行设置。

### 服务启动

MLInference采用了前后端分离的架构设计方式，需要单独启动前端服务（对外提供上层访问接口，如chat、generation等等方法）以及后端服务（对内的微服务架构）。通过如下方式启动后端服务：

```bash
python -m mi.backend.launch --model /path/to/out --device all --port 12222
```

其中，--device指定了启动的加速器数量，all为所有加速器可用，也可以换成数量指定，需要注意：加速器数量需要为tensor并行维度的整数倍，比如tp=2，则加速器数量需要为2的整数倍。

当后端启动成功后，会显示service start at localhost:12222，之后启动前端：

```bash
python -m mi.frontend.launch --backend 12222 --server-port 13000
```

此时会在localhost:13000暴露最终的大模型服务。用户可通过如下语句进行服务访问：

```bash
curl https://api.openai.com/v1/completions \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Say this is a test",
    "max_tokens": 7,
    "temperature": 0
  }'
```

大模型服务API的具体参数和访问方式参考章节 [qian-duan-fu-wu-api-jiao-hu-jie-kou-biao-zhun.md](api-documentation/qian-duan-fu-wu-api-jiao-hu-jie-kou-biao-zhun.md "mention")

## 0x03 模型服务SDK嵌入式调用

> 此处产品策略为尽可能复用或者链接开源社区的成果，比如openai自己的sdk。

## 0x04 大模型推理加速组件SDK使用

> 这个功能是为了贴近比如tensorrt-llm或者vllm的使用方式，需要判断要不要进行这个层级的暴露。
>
> tensorrt-llm提供了用户可自组装的layer组件，可由用户来进行构图，对模型进行精细的控制，主要应对场景是用户有自己结构的模型时。
>
> vllm所有源码均为开源状态，方便用户进行各方面的自我调试。
>
> 目前而言，建议：如果用户真的有需求再进行这一接口的暴露，至少不是前几个版本的重点。
>
> 目前建议暴露的SDK接口为：model层面的一些行为控制，比如generate接口等等，用于用户来控制精细化的启停节奏，对齐vllm或者tensorrt-llm中的服务化部分的精细控制。

除了启动前端服务之外，用户还可以直接使用python SDK进行模型推理任务，用于进行业务代码编程，或进行一些精细化控制。

以下代码案例给出了一个最简的模型推理案例（注意：所有以下案例均需要首先启动后端服务）

```python
from mi import Model

model = Model(endpoint='http://localhost:12222')

inputs = [
    "hello, my name is",
    "Welcome to"
]

outputs = model.generate(inputs, do_sample=False, max_new_tokens=200)

# Print the outputs.
for output in outputs:
    prompt = model.tokenizer.decode(output.prompt_ids)
    complete_text = model.tokenizer.decode(output.ids)
    print(f"Prompt: {prompt!r}, complete text: {complete_text!r}")
```
