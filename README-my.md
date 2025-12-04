pnpm run dev

# 多语言支持

从项目的配置和代码来看，中文的翻译文件并非缺失，而是通过以下方式进行管理和处理的：

1. **国际化架构设计**：
   - 项目采用了多语言架构，`src/lang/en` 目录仅作为基准语言（英文）的源文件存放地
   - 中文及其他语言的翻译文件通过 **Crowdin** 平台进行管理（见 `crowdin.yml` 配置）
   - 翻译流程：英文源文件 → Crowdin 翻译平台 → 多语言文件生成

2. **构建流程集成**：
   - 在 `build.sh` 脚本中可以看到，构建过程会从 GitHub 下载 `i18n.tar.gz`（包含所有语言翻译）
   - 解压后会将翻译文件放置到 `src/lang` 目录下，包括中文在内的其他语言文件
   - 最终构建产物会包含完整的多语言支持

3. **.gitignore 配置说明**：
   - `.gitignore` 中忽略了 `src/lang/*` 但保留了 `src/lang/en/`
   - 这是为了避免将自动生成的翻译文件提交到代码库，仅保留基准英文文件进行版本控制

4. **中文支持验证**：
   - 项目的国际化逻辑（`src/app/i18n.ts`）支持自动检测浏览器语言
   - 切换语言组件（`SwitchLanguage.tsx`）可选择包括中文在内的所有支持语言
   - 构建脚本明确处理了多语言包的下载和集成

简单来说，中文翻译文件并非在开发阶段直接存放在代码库中，而是通过翻译平台管理并在构建过程中动态引入，这是大型项目多语言管理的常见实践。

## crowdin

https://crowdin.com/

# 以下是 `package.json` 中各命令的详细解释：

```bash
   pnpm run crowdin:upload

   pnpm run crowdin:download

   pnpm run crowdin
```

### 国际化相关命令

1. **`crowdin:upload`**:

   ```bash
   crowdin upload sources --auto-update
   ```

   使用 Crowdin CLI 将英文源文件（`src/lang/en/*.json`）上传到 Crowdin 平台，并自动更新源文件内容（当本地文件有修改时）。

2. **`crowdin:download`**:

   ```bash
   crowdin download --verbose
   ```

   从 Crowdin 平台下载所有语言的翻译文件到本地（输出到 `src/lang/%locale%/` 目录，如 `src/lang/zh-CN/`），`--verbose` 会显示详细下载日志。

3. **`crowdin`**:

   ```bash
   pnpm crowdin:upload && pnpm crowdin:download
   ```

   组合命令，先上传最新的英文源文件，再下载所有语言的最新翻译，同步本地与 Crowdin 平台的翻译资源。

4. **`i18n:build`**:

   ```bash
   pnpm crowdin && node ./scripts/i18n.mjs
   ```

   先执行 `crowdin` 命令同步翻译，再运行 `i18n.mjs` 脚本（可能用于处理翻译文件格式、生成最终可用的国际化资源等）。

5. **`i18n:release`**:
   ```bash
   pnpm run crowdin:download && node ./scripts/i18n.mjs
   ```
   与 `i18n:build` 类似，但仅下载翻译文件（不重新上传源文件），适合发布阶段使用，确保基于最新翻译构建。

### 开发与构建命令

6. **`start`**:

   ```bash
   vite
   ```

   启动 Vite 开发服务器，默认使用开发模式运行项目，支持热更新。

7. **`dev`**:

   ```bash
   vite --force
   ```

   强制启动 Vite 开发服务器（`--force` 会强制重新构建依赖预构建缓存，解决依赖相关的缓存问题）。

8. **`build`**:

   ```bash
   vite build
   ```

   使用 Vite 构建生产环境版本的项目，输出到 `dist` 目录。

9. **`build:lite`**:

   ```bash
   cross-env VITE_LITE=true vite build
   ```

   构建轻量版（Lite）项目，通过 `cross-env` 设置环境变量 `VITE_LITE=true`，可能用于移除非核心功能以减小包体积。

10. **`serve`**:
    ```bash
    vite preview
    ```
    启动 Vite 预览服务器，用于本地查看 `build` 或 `build:lite` 生成的生产环境构建产物。

### 工具与配置命令

11. **`prepare`**:

    ```bash
    husky
    ```

    项目安装依赖后自动执行，用于初始化 Husky（Git 钩子工具），通常用于配置代码提交前的校验（如格式化、 lint 等）。

12. **`format`**:
    ```bash
    prettier --ignore-path .gitignore -w "src/**/*.{js,ts,json,css,tsx,jsx}"
    ```
    使用 Prettier 格式化代码，`--ignore-path .gitignore` 忽略 `.gitignore` 中指定的文件，`-w` 表示直接修改文件，格式化 `src` 目录下所有指定后缀的文件（JS、TS、JSON 等）。

# 命令解释

构建生产环境完整版
pnpm build

构建生产环境轻量版
pnpm build:lite

预览打包结果
pnpm serve

手动格式化代码
pnpm format

# 部署cloudflare

pnpm build 后 得到 dist文件夹

针对mainfest.json 需要移动到外部dist文件夹下

12
