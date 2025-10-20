<img width="1300" height="200" alt="sdk-banner(1)" src="https://github.com/user-attachments/assets/c4a7857e-10dd-420b-947a-ed2ea5825cb8" />

<h3>Bright Data JavaScript SDK，为抓取、网页搜索等场景提供简单且可扩展的方法。</h3>

## 安装 [![@latest](https://img.shields.io/npm/v/@brightdata/sdk.svg)](https://www.npmjs.com/package/@brightdata/sdk)

在终端中安装该包：

```bash
npm install @brightdata/sdk
```

## 快速开始

### 1. 在此处[注册](https://www.bright.cn/cp)并获取你的 API 密钥

### 2. 初始化客户端

创建一个名为 pizzaSearch.mjs 的文件，并写入以下内容：

```javascript
import { bdclient } from '@brightdata/sdk';

const client = new bdclient({
    apiKey: '[your_api_key_here]', // 也可通过环境变量 BRIGHTDATA_API_KEY 定义
});
```

### 3. 发起你的第一个请求

添加搜索函数：

```javascript
import { bdclient } from '@brightdata/sdk';

const client = new bdclient({
    apiKey: '[your_api_key_here]', // 也可通过环境变量 BRIGHTDATA_API_KEY 定义
});
const result = await client.search('pizza restaurants');
console.log(result);
```

然后运行：

```bash
node pizzaSearch.mjs
```

## 特性

- 网页抓取：借助反机器人检测能力与代理支持抓取各类网站
- 搜索引擎结果：支持在 Google、Bing 和 Yandex 上按查询搜索（包括批量搜索）
- 并行处理：对多个 URL 或查询进行并发处理
- 健壮的错误处理：内置全面的错误处理与重试逻辑
- Zone 管理：自动创建与管理 Zone
- 多种输出格式：HTML、JSON 与 Markdown
- 双构建：同时支持 ESM 与 CommonJS
- TypeScript：为多种输入与输出数据组合提供完整类型定义

## 用法

### 抓取网站

```javascript
// 单个 URL——默认返回 markdown 字符串
const result = await client.scrape('https://example.com');
console.log(result); // 输出：网页 HTML 内容

// 多个 URL（并行处理）
const urls = [
    'https://example1.com',
    'https://example2.com',
    'https://example3.com',
];
const results = await client.scrape(urls);
console.log(results); // 返回 HTML 字符串数组

// 不同数据格式可选
const htmlResult = await client.scrape('https://example.com', {
    dataFormat: 'html', // 返回原始 HTML（默认：'html'）
});

const screenshotResult = await client.scrape('https://example.com', {
    dataFormat: 'screenshot', // 返回 base64 截图
});

// 不同响应格式
const jsonResult = await client.scrape('https://example.com', {
    format: 'json', // 返回解析后的 JSON 对象（默认：'raw' 字符串）
});

// 组合自定义选项
const result = await client.scrape('https://example.com', {
    format: 'raw', // 'raw'（默认）或 'json'
    dataFormat: 'markdown', // 'markdown'（默认）、'raw'、'screenshot' 等
    country: 'gb', // 两位国家代码
    method: 'GET', // HTTP 方法（默认：'GET'）
});
```

### 搜索引擎结果

```javascript
// 单个搜索查询
const result = await client.search('pizza restaurants');
console.log(result);

// 多个查询（并行处理）
const queries = ['pizza', 'restaurants', 'delivery'];
const results = await client.search(queries);
console.log(results);

// 不同搜索引擎
const result = await client.search('pizza', {
    searchEngine: 'google', // 也可为 'yandex' 或 'bing'
});
console.log(result);

// 自定义选项
const results = await client.search(['pizza', 'sushi'], {
    country: 'gb',
    format: 'raw',
});
console.log(results);
```

### 保存结果

```javascript
// 下载抓取内容
const data = await client.scrape('https://example.com');
const filePath = await client.saveResults(data, {
    filename: 'results.json',
    format: 'json',
});
console.log(`内容已保存至: ${filePath}`);
```

### 触发数据集快照采集

```javascript
const res = await client.datasets.linkedin.discoverCompanyPosts([
    { url: 'https://www.linkedin.com/company/bright-data' },
]);

// 将轮询快照是否就绪，一旦就绪即下载
const filePath = await client.datasets.snapshot.download(res.snapshot_id, {
    filename: './brd_posts.jsonl',
    format: 'jsonl',
});
console.log(`内容已保存至: ${filePath}`);
```

## 配置

### API 密钥

- 你可以在[这里](https://www.bright.cn/cp/setting/users?=)获取 API 密钥

### 环境变量

设置以下环境变量（也可在客户端构造函数中配置）：

```env
BRIGHTDATA_API_KEY=your_bright_data_api_key
BRIGHTDATA_WEB_UNLOCKER_ZONE=your_web_unlocker_zone  # 可选，如有特定 zone
BRIGHTDATA_SERP_ZONE=your_serp_zone                  # 可选，如有特定 zone
```

### 管理 Zones

```javascript
const zones = await client.listZones();
console.log(`Found ${zones.length} zones`);
```

### 常量

| 常量                    | 默认值  | 说明                    |
| ----------------------- | ------- | ----------------------- |
| `DEFAULT_CONCURRENCY`   | `10`    | 最大并行任务数          |
| `DEFAULT_TIMEOUT`       | `30000` | 请求超时（毫秒）        |
| `MAX_RETRIES`           | `3`     | 失败后的重试次数        |
| `RETRY_BACKOFF_FACTOR`  | `1.5`   | 指数退避倍数            |

## API 参考

### bdclient 类

```javascript
const client = new bdclient({
    apiKey: 'string', // 你的 API 密钥
    autoCreateZones: true, // 若不存在则自动创建 zones
    webUnlockerZone: 'string', // 自定义 web unlocker zone 名称
    serpZone: 'string', // 自定义 SERP zone 名称
    logLevel: 'INFO', // 日志级别
    structuredLogging: true, // 使用结构化 JSON 日志
    verbose: false, // 启用详细日志
});
```

### 关键方法

### scrape(url, options)

使用 Web Unlocker 抓取单个 URL 或 URL 数组。

**参数：**

| 名称                  | 类型                                               | 说明                                       | 默认值    |
| --------------------- | -------------------------------------------------- | ------------------------------------------ | --------- |
| `url`                 | `string` &#124; `string[]`                         | 单个 URL 字符串或 URL 数组                 | —         |
| `options.zone`        | `string`                                           | Zone 标识（若为 `null` 将自动配置）        | —         |
| `options.format`      | `"json"` &#124; `"raw"`                            | 响应格式                                   | `"raw"`   |
| `options.method`      | `string`                                           | HTTP 方法                                  | `"GET"`   |
| `options.country`     | `string`                                           | 两位国家代码                               | `""`      |
| `options.dataFormat`  | `"markdown"` &#124; `"screenshot"` &#124; `"html"` | 返回内容格式                               | `"html"`  |
| `options.concurrency` | `number`                                           | 最大并行工作数                             | `10`      |
| `options.timeout`     | `number` (ms)                                      | 请求超时                                   | `30000`   |

### search(query, options)

使用 SERP API 执行搜索。

**参数：**

| 名称                   | 类型                                               | 说明                                       | 默认值     |
| ---------------------- | -------------------------------------------------- | ------------------------------------------ | ---------- |
| `query`                | `string` &#124; `string[]`                         | 搜索查询字符串或查询数组                   | —          |
| `options.searchEngine` | `"google"` &#124; `"bing"` &#124; `"yandex"`       | 搜索引擎                                   | `"google"` |
| `options.zone`         | `string`                                           | Zone 标识（若为 `null` 将自动配置）        | —          |
| `options.format`       | `"json"` &#124; `"raw"`                            | 响应格式                                   | `"raw"`    |
| `options.method`       | `string`                                           | HTTP 方法                                  | `"GET"`    |
| `options.country`      | `string`                                           | 两位国家代码                               | `""`       |
| `options.dataFormat`   | `"markdown"` &#124; `"screenshot"` &#124; `"html"` | 返回内容格式                               | `"html"`   |
| `options.concurrency`  | `number`                                           | 最大并行工作数                             | `10`       |
| `options.timeout`      | `number` (ms)                                      | 请求超时                                   | `30000`    |

### saveResults(content, options)

将内容保存到本地文件。

**参数：**

| 名称               | 类型                                  | 说明                                   | 默认值  |
| ------------------ | ------------------------------------- | -------------------------------------- | ------- |
| `content`          | `any`                                 | 要保存的内容                           | —       |
| `options.filename` | `string`                              | 输出文件名（若为 `null` 将自动生成）   | —       |
| `options.format`   | `string` (`"json"`, `"csv"`, `"txt"`) | 文件格式                               | `"json"` |

### listZones()

列出你 Bright Data 账户中的所有活跃 Zone。

**返回：** Promise<Array<ZoneInfo>>

## 错误处理

SDK 内置输入校验与重试逻辑：

```javascript
try {
    const result = await client.scrape('https://example.com');
    console.log(result);
} catch (error) {
    if (error.name === 'ValidationError') {
        console.error('Invalid input:', error.message);
    } else {
        console.error('API error:', error.message);
    }
}
```

## 开发

开发环境安装：

```bash
git clone https://github.com/bright-cn/bright-data-sdk-js.git
cd bright-data-sdk-js
npm install
npm run build:dev
```

## 提交规范与发布

我们使用 [Semantic Release](https://github.com/semantic-release/semantic-release) 进行自动化发布与仓库维护。为便于其工作，请遵循以下轻量提交信息规范：
- 使用 `fix:` 前缀表示修复问题（触发 PATCH 版本发布 `0.5.0` => `0.5.1`）
- 使用 `feat:` 前缀表示新功能（触发 MINOR 版本发布 `0.5.0` => `0.6.0`）
- 文档更新使用 `docs:` 前缀（如 README 变更）
- 常规更改使用 `chore:` 或不加前缀
- 如需发布 MAJOR 版本，请在提交底部使用 `BREAKING CHANGE:`（`0.5.0` => `1.0.0`）

  示例：`fix: correct floating numbers bug`，`docs: fixed typo`

## 支持

如遇任何问题，请联系 [Bright Data 支持](https://www.bright.cn/contact)，或在本仓库提交 issue。

## 许可

本项目基于 [MIT License](https://opensource.org/licenses/MIT) 授权。
