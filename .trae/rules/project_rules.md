# TikTokDownloader 开发规范文档

## 1. 技术选型规范

### 1.1 核心技术栈
- **编程语言**: Python 3.12+（强制要求）
- **Web框架**: FastAPI（API模式）/ 自定义（终端模式）
- **异步框架**: asyncio + aiohttp（强制使用异步编程）
- **数据库**: SQLite（默认）/ MySQL 8.0（可选）
- **缓存**: 本地缓存 + Redis（可选）
- **依赖管理**: pip + requirements.txt
- **构建工具**: setuptools / Docker

### 1.2 关键依赖库
- **HTTP客户端**: httpx[aiohttp] >= 1.0.0
- **数据解析**: beautifulsoup4 >= 4.12.0
- **异步处理**: aiofiles >= 23.0.0
- **数据验证**: pydantic >= 2.0.0
- **日志管理**: loguru >= 0.7.0
- **配置管理**: pyyaml >= 6.0
- **数据库ORM**: sqlalchemy >= 2.0.0（可选）

## 2. 项目结构规范

### 2.1 模块划分标准
```
TikTokDownloader/
├── src/                          # 核心源码目录
│   ├── application/              # 应用入口模块
│   │   ├── TikTokDownloader.py # 主应用类
│   │   ├── main_server.py      # Web服务器模式
│   │   ├── main_terminal.py    # 终端交互模式
│   │   └── main_monitor.py     # 监控模式
│   ├── config/                   # 配置管理模块
│   │   ├── parameter.py        # 参数配置类
│   │   └── settings.py          # 设置管理
│   ├── custom/                   # 自定义工具模块
│   │   ├── function.py          # 通用函数
│   │   ├── internal.py         # 内部常量定义
│   │   └── static.py            # 静态配置
│   ├── downloader/               # 下载核心模块
│   │   └── download.py          # 下载器实现
│   ├── encrypt/                  # 加密算法模块
│   │   ├── aBogus.py           # ABogus加密
│   │   ├── xBogus.py           # XBogus加密
│   │   ├── msToken.py          # MS Token生成
│   │   └── device_id.py        # 设备ID生成
│   ├── extract/                  # 数据提取模块
│   │   └── extractor.py         # 数据提取器
│   ├── interface/                # 接口定义模块
│   │   ├── account.py           # 账户相关接口
│   │   ├── detail.py            # 详情接口
│   │   ├── comment.py           # 评论接口
│   │   └── template.py          # 接口模板
│   ├── manager/                  # 管理器模块
│   │   ├── cache.py             # 缓存管理
│   │   ├── database.py          # 数据库管理
│   │   └── recorder.py          # 记录管理
│   ├── models/                   # 数据模型模块
│   │   ├── base.py               # 基础模型
│   │   ├── account.py            # 账户模型
│   │   ├── detail.py             # 详情模型
│   │   └── response.py           # 响应模型
│   ├── module/                   # 功能模块
│   │   ├── cookie.py            # Cookie管理
│   │   ├── ffmpeg.py            # 视频处理
│   │   └── tiktok_unofficial.py # 非官方API
│   ├── storage/                  # 存储模块
│   │   ├── csv.py               # CSV存储
│   │   ├── mysql.py             # MySQL存储
│   │   ├── sqlite.py            # SQLite存储
│   │   └── xlsx.py              # Excel存储
│   ├── tools/                    # 工具类模块
│   │   ├── browser.py           # 浏览器工具
│   │   ├── cleaner.py           # 清理工具
│   │   ├── console.py           # 控制台工具
│   │   ├── file_folder.py       # 文件操作
│   │   └── retry.py             # 重试机制
│   └── testers/                  # 测试模块
│       ├── params.py            # 参数测试
│       └── logger.py            # 日志测试
├── static/                       # 静态资源
│   ├── images/                  # 图片资源
│   └── js/                      # JavaScript文件
├── locale/                       # 国际化资源
├── docs/                         # 文档目录
├── main.py                       # 程序入口
├── requirements.txt              # 依赖清单
├── Dockerfile                    # Docker配置
└── README.md                     # 项目说明
```

### 2.2 文件命名规范
- **类文件**: 使用驼峰命名法，如 `TikTokDownloader.py`
- **模块文件**: 使用下划线命名法，如 `main_server.py`
- **配置文件**: 使用小写+下划线，如 `requirements.txt`
- **常量定义**: 全大写+下划线，如 `DATA_HEADERS`

## 3. 编码规范

### 3.1 Python代码规范
- **代码风格**: 遵循PEP 8规范，使用Ruff进行格式化
- **行长度**: 每行不超过88个字符（Black格式化标准）
- **导入顺序**: 标准库 → 第三方库 → 本地模块
- **类型注解**: 强制使用类型注解，特别是公共API
- **文档字符串**: 所有公共函数和类必须有docstring

### 3.2 异步编程规范
```python
# 正确的异步函数定义
async def fetch_data(url: str) -> dict:
    """异步获取数据"""
    async with httpx.AsyncClient() as client:
        response = await client.get(url)
        return response.json()

# 正确的异步上下文管理器
async with aiofiles.open('file.txt', 'r') as f:
    content = await f.read()
```

### 3.3 错误处理规范
```python
from loguru import logger

async def safe_operation():
    """安全的异步操作"""
    try:
        # 业务逻辑
        result = await risky_operation()
        return result
    except NetworkError as e:
        logger.error(f"网络错误: {e}")
        raise
    except Exception as e:
        logger.exception(f"未知错误: {e}")
        raise
```

## 4. 开发流程规范

### 4.1 功能开发步骤
1. **需求分析**: 明确功能需求和业务逻辑
2. **接口设计**: 定义数据模型和接口规范
3. **模块划分**: 确定模块职责和依赖关系
4. **编码实现**: 按以下顺序开发：
   - 数据模型 → 工具函数 → 业务逻辑 → 接口实现
5. **单元测试**: 编写测试用例，确保功能正确
6. **集成测试**: 验证模块间的协作
7. **文档更新**: 更新相关文档和注释

### 4.2 分层调用规范
```
应用层 (application/)
    ↓
接口层 (interface/)
    ↓
服务层 (module/)
    ↓
数据层 (storage/)
    ↓
工具层 (tools/)
```

**禁止跨层调用**，每层只能调用直接下层的服务。

### 4.3 Git提交规范
- **提交格式**: `<类型>: <简短描述>`
- **提交类型**:
  - `feat`: 新功能
  - `fix`: 修复bug
  - `docs`: 文档更新
  - `style`: 代码格式调整
  - `refactor`: 代码重构
  - `test`: 测试相关
  - `chore`: 构建过程或辅助工具的变动

**示例**:
```
feat: 添加TikTok视频批量下载功能
fix: 修复Cookie过期导致的下载失败问题
docs: 更新API文档和使用说明
```

## 5. 配置管理规范

### 5.1 配置文件结构
```yaml
# config/settings.yaml
app:
  name: "TikTokDownloader"
  version: "1.0.0"
  debug: false

download:
  max_workers: 8
  chunk_size: 8192
  timeout: 30
  retry_count: 3

storage:
  type: "sqlite"  # sqlite, mysql, csv
  path: "./data"
  
proxy:
  enabled: false
  http: "http://127.0.0.1:8080"
  https: "https://127.0.0.1:8080"
```

### 5.2 环境变量配置
```python
# src/config/settings.py
import os
from pathlib import Path

class Settings:
    """配置管理类"""
    
    def __init__(self):
        self.debug = os.getenv('DEBUG', 'false').lower() == 'true'
        self.max_workers = int(os.getenv('MAX_WORKERS', '8'))
        self.download_path = Path(os.getenv('DOWNLOAD_PATH', './downloads'))
        
    @classmethod
    def load_from_file(cls, config_path: str) -> 'Settings':
        """从配置文件加载设置"""
        # 实现配置加载逻辑
        pass
```

## 6. 日志规范

### 6.1 日志配置
```python
# src/custom/internal.py
from loguru import logger
import sys

# 日志格式
LOG_FORMAT = (
    "<green>{time:YYYY-MM-DD HH:mm:ss}</green> | "
    "<level>{level: <8}</level> | "
    "<cyan>{name}</cyan>:<cyan>{function}</cyan>:<cyan>{line}</cyan> - "
    "<level>{message}</level>"
)

# 配置日志
logger.remove()
logger.add(
    sys.stdout,
    format=LOG_FORMAT,
    level="INFO",
    colorize=True
)
logger.add(
    "logs/app_{time:YYYY-MM-DD}.log",
    format=LOG_FORMAT,
    level="DEBUG",
    rotation="00:00",
    retention="7 days",
    compression="zip"
)
```

### 6.2 日志级别使用
- **DEBUG**: 详细的调试信息，仅在开发环境使用
- **INFO**: 重要的业务流程信息
- **WARNING**: 警告信息，不影响程序运行
- **ERROR**: 错误信息，需要处理但不影响程序继续运行
- **CRITICAL**: 严重错误，程序无法继续运行

## 7. 性能优化规范

### 7.1 异步并发控制
```python
# 使用信号量控制并发数
import asyncio
from typing import List

async def limited_download(urls: List[str], max_concurrent: int = 5):
    """限制并发数的下载器"""
    semaphore = asyncio.Semaphore(max_concurrent)
    
    async def download_one(url: str):
        async with semaphore:
            return await download_file(url)
    
    tasks = [download_one(url) for url in urls]
    return await asyncio.gather(*tasks)
```

### 7.2 内存管理
- **大文件处理**: 使用流式处理，避免一次性加载到内存
- **对象池**: 复用频繁创建的对象
- **缓存策略**: 合理使用缓存，避免内存泄漏

## 8. 安全规范

### 8.1 敏感信息处理
```python
# 敏感信息加密存储
from cryptography.fernet import Fernet

class SecureStorage:
    """安全存储类"""
    
    def __init__(self, key: bytes):
        self.cipher = Fernet(key)
    
    def encrypt_data(self, data: str) -> str:
        """加密数据"""
        return self.cipher.encrypt(data.encode()).decode()
    
    def decrypt_data(self, encrypted_data: str) -> str:
        """解密数据"""
        return self.cipher.decrypt(encrypted_data.encode()).decode()
```

### 8.2 输入验证
- **URL验证**: 验证输入的URL格式和安全性
- **文件路径**: 防止路径遍历攻击
- **用户输入**: 对所有用户输入进行验证和清理

## 9. 测试规范

### 9.1 单元测试
```python
# tests/test_downloader.py
import pytest
import asyncio
from src.downloader.download import Downloader

@pytest.mark.asyncio
async def test_download_video():
    """测试视频下载功能"""
    downloader = Downloader()
    url = "https://example.com/video.mp4"
    
    result = await downloader.download(url)
    
    assert result.success is True
    assert result.file_path.exists()
    assert result.file_size > 0
```

### 9.2 集成测试
- **API测试**: 测试各个接口的正确性
- **性能测试**: 测试并发处理能力和响应时间
- **异常测试**: 测试错误处理和恢复机制

## 10. 部署规范

### 10.1 Docker部署
```dockerfile
# 使用多阶段构建优化镜像大小
FROM python:3.12-slim as builder

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir --prefix="/install" -r requirements.txt

FROM python:3.12-slim
WORKDIR /app
COPY --from=builder /install /usr/local
COPY . .

EXPOSE 5555
VOLUME ["/app/downloads"]
CMD ["python", "main.py"]
```

### 10.2 环境配置
- **开发环境**: 启用调试模式，详细日志
- **测试环境**: 模拟生产环境，完整测试
- **生产环境**: 关闭调试，优化性能，监控告警

## 11. 文档规范

### 11.1 代码文档
```python
class TikTokDownloader:
    """
    TikTok视频下载器主类
    
    提供视频下载、数据处理、文件管理等功能
    
    Attributes:
        config: 配置对象
        logger: 日志记录器
        session: HTTP会话
        
    Examples:
        >>> downloader = TikTokDownloader()
        >>> await downloader.download_video("https://example.com/video")
    """
```

### 11.2 API文档
- **接口说明**: 详细描述每个接口的功能和参数
- **返回格式**: 明确定义返回数据的结构和类型
- **错误码**: 定义统一的错误码和错误信息

## 12. 版本管理规范

### 12.1 版本号规范
采用语义化版本控制（Semantic Versioning）：
- **主版本号**: 不兼容的API修改
- **次版本号**: 向下兼容的功能性新增
- **修订号**: 向下兼容的问题修正

格式：`MAJOR.MINOR.PATCH`（如：1.2.3）

### 12.2 发布流程
1. **功能开发**: 在develop分支开发新功能
2. **版本测试**: 在release分支进行版本测试
3. **发布准备**: 更新版本号和CHANGELOG
4. **正式发布**: 合并到main分支并打标签
5. **文档更新**: 更新相关文档和说明
