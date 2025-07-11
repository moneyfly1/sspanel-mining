# 越权网站搜集 应用到getnodelist

[![GitHub Actions](https://github.com/RobAI-Lab/sspanel-mining/workflows/SSPanel%20Mining/badge.svg)](https://github.com/RobAI-Lab/sspanel-mining/actions)

> 基于 Google 搜索引擎的 SSPanel 站点采集器

## ✨ 特性

- 🔍 **智能采集**: 基于 Google 搜索引擎的智能站点发现
- 🛡️ **反检测机制**: 内置重试机制和随机延迟，减少被 Google 拦截的可能性
- 📊 **数据分类**: 自动对采集到的站点进行分类和过滤
- 🚀 **高性能**: 支持多线程并发处理
- 🔄 **自动重试**: 当遇到 Google 拦截时自动重试，提高成功率

## 🚀 快速开始

### 环境要求

- Python 3.9+
- Chrome 浏览器
- ChromeDriver

### 安装

```bash
# 克隆仓库
git clone https://github.com/RobAI-Lab/sspanel-mining.git
cd sspanel-mining

# 安装依赖
pip install -r requirements.txt

# 安装配置
python src/main.py install
```

### 使用

```bash
# 运行采集器（推荐）
python src/main.py mining --collector --classifier

# 仅运行采集器
python src/main.py mining --collector

# 仅运行分类器
python src/main.py mining --classifier

# 生产环境运行
python src/main.py mining --env=production --collector --classifier
```

## 🔧 配置

### 环境变量

- `CHROME_DRIVER_PATH`: ChromeDriver 路径（可选）
- `MAX_RETRIES`: 最大重试次数（默认: 3）
- `RETRY_DELAY`: 重试延迟时间（默认: 30秒）

### 参数说明

- `--env`: 运行环境 (`development` | `production`)
- `--silence`: 静默模式（默认: `True`）
- `--collector`: 启用采集器
- `--classifier`: 启用分类器
- `--source`: 数据源 (`local` | `remote`)
- `--power`: 分类器功率（默认: 16）

## 🛡️ 反检测机制

### 重试机制

当遇到 `CollectorSwitchError`（Google 拦截）时，系统会自动重试：

1. **递增延迟**: 每次重试的等待时间递增（30秒、60秒、90秒）
2. **随机行为**: 添加随机滚动和延迟，模拟人类浏览行为
3. **智能检测**: 自动检测 Google 拦截页面并触发重试
4. **优雅降级**: 如果重试失败，提供详细的错误信息和解决建议

### 行为模拟

- 随机页面滚动（PAGE_DOWN、END、PAGE_UP、HOME）
- 随机延迟（1-5秒）
- 模拟人类点击行为
- 智能页面切换
- 随机用户代理
- 随机搜索查询

### 代理支持

为了避免被Google拦截，建议使用代理：

```bash
# 设置代理环境变量
export HTTP_PROXY="http://your-proxy:port"
export HTTPS_PROXY="http://your-proxy:port"

# 或者使用代理配置脚本
python setup_proxy.py
```

### 代理类型支持

- **HTTP代理**: `http://ip:port`
- **SOCKS5代理**: `socks5://ip:port`
- **认证代理**: `http://user:pass@ip:port`

### 推荐代理服务

- **免费代理**: 可能不稳定，建议用于测试
- **付费代理**: 更稳定，推荐用于生产环境
- **住宅代理**: 最不容易被检测，但价格较高

## 📊 数据分类

采集到的站点会被自动分类为：

- **Normal**: 无验证站点
- **Google reCAPTCHA**: Google reCAPTCHA 人机验证
- **GeeTest Validation**: 极验滑块验证
- **Email Validation**: 邮箱验证
- **SMS**: 手机短信验证
- **CloudflareDefenseV2**: 高防服务器

## 🌐 代理搜集功能

### 自动代理搜集

系统集成了自动代理搜集功能，可以从多个免费代理网站搜集代理：

```bash
# 搜集和测试代理
python collect_proxies.py

# 或者直接运行采集器（会自动搜集代理）
python src/main.py mining --collector --classifier
```

### 代理源

系统支持从以下代理源搜集代理：

- **FreeProxyList**: https://free-proxy-list.net/
- **ProxyNova**: https://www.proxynova.com/
- **ProxyList**: https://www.proxy-list.download/
- **SpysOne**: http://spys.one/

### 代理管理

- **自动测试**: 所有搜集的代理都会进行可用性测试
- **速度排序**: 按响应速度对代理进行排序
- **自动轮换**: 采集器会自动轮换使用不同代理
- **失败处理**: 代理失败时会自动切换到下一个代理
- **智能刷新**: 当所有代理都失效时，自动搜集新代理

### 代理文件

可用代理会保存到 `working_proxies.txt` 文件中，格式如下：

```
1.2.3.4:8080
5.6.7.8:3128
9.10.11.12:80
```

### 代理状态监控

```bash
# 查看代理状态
python -c "
from src.services.utils.proxy_manager import ProxyManager
manager = ProxyManager()
status = manager.get_proxy_status()
print(f'总计: {status[\"total\"]}, 可用: {status[\"available\"]}, 失败: {status[\"failed\"]}')
"
```

## 🔍 故障排除

### ChromeDriver 问题

如果遇到 ChromeDriver 兼容性问题，请参考 [ChromeDriver 修复指南](CHROME_DRIVER_FIX.md)。

### Google 拦截

如果频繁遇到 Google 拦截：

1. **增加延迟**: 调整 `RETRY_DELAY` 环境变量
2. **使用代理**: 配置代理服务器
3. **减少频率**: 降低采集频率
4. **手动处理**: 在 Windows 环境下可以手动处理验证码

### 常见错误

- `CollectorSwitchError`: Google 拦截，系统会自动重试
- `CollectorNoTouchElementError`: 元素定位失败
- `ManuallyCloseTheCollectorError`: 手动关闭采集器

## 📁 项目结构

```
sspanel-mining/
├── src/
│   ├── apis/              # API 接口
│   ├── database/          # 数据存储
│   ├── services/          # 核心服务
│   └── main.py           # 主程序
├── docs/                 # 文档
├── examples/             # 示例
└── requirements.txt      # 依赖
```

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 📄 许可证

本项目采用 MIT 许可证。详见 [LICENSE](LICENSE) 文件。

## ⚠️ 免责声明

本项目仅供学习和研究使用。请遵守相关法律法规和网站使用条款。


