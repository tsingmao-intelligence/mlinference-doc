# 模型部署

## 一般模型部署

## 量化模型部署

MLInference同样支持量化模型的部署。MLInference兼容[mi-optimize](https://github.com/TsingmaoAI/MI-optimize)格式的模型权重。请先用mi-optimize处理模型权重，之后使用如下脚本进行模型格式转换：

```bash
mkdir -p path/to/out
python -m mi.convert --path /path/to/model --out /path/to/out --tp 1/2/4/8 
```
