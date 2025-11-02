# Codex

------------------------------------------------------------------------

## 一、安装

### 1. 安装node.js

``` 
    https://nodejs.org/en/download
```

### 2. 安装codex

``` bash
    sudo npm i -g @openai/codex
```

### 3. free to use

#### 3.1 AI Router

``` 
    https://modelscope.cn/models/ZhipuAI/GLM-4.6
```

#### 3.2 修改Codex模型模型配置

```
    /Users/user_name/.codex/config.toml
        
    model  = "ZhipuAI/GLM-4.6"
    model_provider = "modelscope"
    
    [model_providers.modelscope]
    name ="Modelscope" 
    base_url = "https://api-inference.modelscope.cn/v1"
    env_key = "MODELSCOPE_API_KEY"
```

#### 3.3 设置环境变量

``` bash
    open ~/.zshrc
    touch ~/.zshrc
    export MODELSCOPE_API_KEY='api_key'
    source ~/.zshrc
```

### 3. 常用命令